---
title: "Adatok betöltése az SQL Serverről az Azure SQL Data Warehouse-ba (PolyBase) | Microsoft Docs"
description: "A bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, az AzCopy segítségével importál adatokat az Azure Blob Storage-ban, és a PolyBase használatával viszi be az adatokat az SQL Data Warehouse-ba."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4d42786a-fb28-43c9-9c3b-72d19c0ecc11
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 21b4cc704e271ac220fd606305f8f97c9b2593bb
ms.lasthandoff: 03/21/2017



---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-azcopy"></a>Adatok betöltése az SQL Serverről az Azure SQL Data Warehouse-ba (AZCopy)
Adatok betöltése a bcp és az AZCopy parancssori segédprogram használatával az SQL Serverről az Azure Blob Storage-be. Ezután az adatok betöltése a PolyBase vagy az Azure Data Factory használatával az Azure SQL Data Warehouse-ba. 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse-adatbázis
* Telepített bcp parancssori segédprogram
* Telepített SQLCMD parancssori segédprogram

> [!NOTE]
> A bcp és az sqlcmd parancssori segédprogramot a [Microsoft letöltőközpontból][Microsoft Download Center] töltheti le.
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Adatok importálása az SQL Data Warehouse-ba
Ebben az oktatóanyagban létrehoz egy táblát az Azure SQL Data Warehouse-ban, és adatokat importál a táblába.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>1. lépés: Tábla létrehozása az Azure SQL Data Warehouse-ban
Egy parancssorban az sqlcmd paranccsal futtassa a következő lekérdezést. Ezzel egy táblát hoz létre a példányán:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> További információk a táblázatok létrehozásáról az SQL Data Warehouse-ban, illetve a WITH záradékkal használható lehetőségekről: [Táblák áttekintése][Table Overview] vagy [CREATE TABLE szintaxis][CREATE TABLE syntax].
> 
> 

### <a name="step-2-create-a-source-data-file"></a>2. lépés: Forrásadatfájlok létrehozása
Nyissa meg a Jegyzettömböt, és másolja az alábbi adatsorokat egy új szöveges fájlba, majd mentse ezt a fájlt a helyi ideiglenes könyvtárba (C:\Temp\DimDate2.txt).

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Fontos észben tartani, hogy a bcp.exe nem támogatja az UTF-8 kódolást. A bcp.exe használatakor használjon ASCII fájlokat vagy UTF-16 kódolást.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>3. lépés: Csatlakozás és az adatok importálása
A bcp és az alábbi parancs segítségével csatlakozhat és importálhatja az adatokat. Az értékeket szükség szerint cserélje le:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Az adatok betöltésének ellenőrzéséhez futtassa az alábbi lekérdezést az sqlcmd segítségével:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Az alábbi eredményeket fogja kapni:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>4. lépés: Statisztikák létrehozása az újonnan betöltött adatokról
Az Azure SQL Data Warehouse még nem támogatja a statisztikák automatikus létrehozását és frissítését. A legjobb lekérdezési teljesítmény eléréséhez fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően. A statisztika részletes ismertetését a Fejlesztés témakörcsoport [Statisztika][Statistics] témakörében találja. Alább egy gyors példát láthat a példában betöltött táblák statisztikáinak létrehozására

Hajtsa végre az alábbi CREATE STATISTICS utasításokat egy sqlcmd parancssorból:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Adatok exportálása az SQL Data Warehouse-ból
Ebben az oktatóanyagban létrehoz egy adatfájlt egy SQL Data Warehouse-táblából. A fenti létrehozott adatokat exportáljuk egy új, DimDate2_export.txt adatfájlba.

### <a name="step-1-export-the-data"></a>1. lépés: Az adatok exportálása
A bcp segédprogram és az alábbi parancs segítségével csatlakozhat és exportálhatja az adatokat. Az értékeket szükség szerint cserélje le:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Az adatok helyes exportálását az új fájl megnyitásával ellenőrizheti. A fájl adatainak az alábbi szöveggel kell egyezniük:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Az elosztott rendszerek jellemzői miatt előfordulhat, hogy az adatok sorrendje nem egyforma a különböző SQL Data Warehouse-adatbázisokban. Másik lehetőségként használhatja a bcp **queryout** függvényét, amellyel a teljes táblázat helyett egy lekérdezéssel kinyert tartalom írható.
> 
> 

## <a name="next-steps"></a>Következő lépések
A betöltés áttekintése: [Adatok betöltése az SQL Data Warehouse-ba][Load data into SQL Data Warehouse].
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

