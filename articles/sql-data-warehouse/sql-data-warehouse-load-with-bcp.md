---
title: "Adatok betöltése az SQL Data Warehouse-ba bcp segítségével | Microsoft Docs"
description: "Megismerheti a bcp-t és az adatraktározási forgatókönyvekben való használatát."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: e368ae8b249fe3c33371794160440e472b0f35e3
ms.lasthandoff: 01/30/2017



---
# <a name="load-data-with-bcp"></a>Adatok betöltése a bcp használatával
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

A **[bcp][bcp]** egy tömeges betöltésre szolgáló parancssori eszköz, amellyel adatokat másolhat az SQL Server, az adatfájlok és az SQL Data Warehouse között. A bcp segítségével nagy mennyiségű sort importálhat az SQL Data Warehouse tábláiba, vagy adatokat exportálhat az SQL Server tábláiból adatfájlokba. Ha nem a queryout kapcsolóval használja, a bcp-hez nem szükséges a Transact-SQL ismerete.

A bcp egyszerű módot biztosít a kisebb adatkészletek áthelyezésére az SQL Data Warehouse-adatbázisokba, illetve onnan máshová. A betöltésre/kinyerésre ajánlott adatok pontos mennyisége az Azure-adatközponthoz csatlakozó hálózati kapcsolattól függ.  Általában a dimenziótáblák könnyen betölthetők és kinyerhetők, ennek ellenére a bcp nem ajánlott nagy adatmennyiségek betöltéséhez vagy kinyeréséhez.  Nagy adatkötetek betöltéséhez és kinyeréséhez a PolyBase az ajánlott eszköz, mert hatékonyabban használja az SQL Data Warehouse nagymértékben párhuzamos feldolgozási architektúráját.

A bcp-vel a következőket teheti:

* Egy egyszerű parancssori segédprogrammal adatokat tölthet be az SQL Data Warehouse-ba.
* Egy egyszerű parancssori segédprogrammal adatokat nyerhet ki az SQL Data Warehouse-ból.

Ez az oktatóanyag a következőket mutatja be:

* Adatok importálása egy táblába a bcp in paranccsal
* Adatok exportálása egy táblából a bcp out paranccsal

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

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

