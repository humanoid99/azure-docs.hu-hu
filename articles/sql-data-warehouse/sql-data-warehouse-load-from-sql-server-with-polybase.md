---
title: "Adatok betöltése az SQL Serverről az Azure SQL Data Warehouse-ba (PolyBase) | Microsoft Docs"
description: "A bcp segítségével exportál adatokat az SQL Serverről egybesimított fájlokba, az AzCopy segítségével importál adatokat az Azure Blob Storage-ban, és a PolyBase használatával viszi be az adatokat az SQL Data Warehouse-ba."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 860c86e0-90f7-492c-9a84-1bdd3d1735cd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 4f6feb844774fba00e3c46438f686e61b52d03d3
ms.lasthandoff: 01/30/2017



---
# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Adatok betöltése a PolyBase-zel az SQL Data Warehouse-ba
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan lehet adatokat betölteni az SQL Data Warehouse-ba az AzCopy és a PolyBase segítségével. Az oktatóanyag végére elsajátíthatja a következőket:

* Adatok másolása az Azure Blob Storage-ba az AzCopy segítségével
* Adatbázis-objektumok létrehozása az adatok meghatározásához
* T-SQL lekérdezés futtatása az adatok betöltéséhez

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-with-PolyBase-in-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse-adatbázis.
* Egy standard helyileg redundáns tárolás (Standard-LRS), standard georedundáns tárolás (Standard-GRS) vagy standard írásvédett georedundáns tárolás (Standard-RAGRS) típusú Azure Storage-fiók.
* AzCopy parancssori segédprogram Töltse le és telepítse [az AzCopy legújabb verzióját][latest version of AzCopy], amely a Microsoft Azure Storage-eszközökkel együtt települ.
  
    ![Azure Storage-eszközök](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)

## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>1. lépés: Mintaadatok felvétele az Azure Blob Storage-ba
Az adatok betöltéséhez mintaadatokat kell helyeznünk egy Azure blobtárolóba. Ebben a lépésben feltöltünk egy Azure Storage-blobot mintaadatokkal. Később a PolyBase segítségével töltjük majd be ezeket a mintaadatokat az SQL Data Warehouse-adatbázisba.

### <a name="a-prepare-a-sample-text-file"></a>A. Minta szöveges fájl előkészítése
Minta szöveges fájl előkészítése:

1. Nyissa meg a Jegyzettömböt, és másolja az alábbi adatsorokat egy új fájlba. Mentse a fájlt a helyi átmeneti könyvtárba %temp%\DimDate2.txt néven.

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

### <a name="b-find-your-blob-service-endpoint"></a>B. A Blob-szolgáltatásvégpont megkeresése
A Blob-szolgáltatásvégpont megkeresése:

1. Az Azure Portalon válassza a **Tallózás** > **Storage-fiókok** lehetőséget.
2. Kattintson a használni kívánt tárfiókra.
3. A Storage-fiók panelen kattintson a Blobok elemre
   
    ![Kattintson a Blobok elemre](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)
4. Mentse a Blob-szolgáltatásvégpontot későbbi használatra.
   
    ![Blob-szolgáltatásvégpont](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Az Azure Storage-kulcs megkeresése
Az Azure Storage-kulcs megkeresése:

1. Az Azure Portalon válassza a **Tallózás** > **Storage-fiókok** lehetőséget.
2. Kattintson a használni kívánt tárfiókra.
3. Válassza az **Összes beállítás** > **Hívóbetűk** lehetőséget.
4. Kattintson a másolás mezőre az egyik hívóbetű vágólapra másolásához.
   
    ![Az Azure Storage-kulcs másolása](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. A mintafájl Azure Blob Storage-ba másolása
Adatok másolása az Azure Blob Storage-ba:

1. Nyisson meg egy parancssort, és módosítsa a könyvtárakat az AzCopy telepítési könyvtárára. Ez a parancs az alapértelmezett telepítési könyvtárra vált egy 64 bites Windows-ügyfélen.
   
    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```
2. A fájl feltöltéséhez futtassa az alábbi parancsot. Adja meg a(z) <blob service endpoint URL> Blob-szolgáltatásvégpont URL-jét és az <azure_storage_account_key> Azure Storage-fiók kulcsát.
   
    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Lásd még [az AzCopy parancssori segédprogram használatát ismertető][latest version of AzCopy] részt.

### <a name="e-explore-your-blob-storage-container"></a>E. A Blob Storage-tároló áttekintése
A Blob Storage-ba feltöltött fájl megtekintése:

1. Térjen vissza a Blob szolgáltatás panelre.
2. A Tárolók területen kattintson duplán a **datacontainer** elemre.
3. Az adatok elérési útjának megtekintéséhez kattintson a **datedimension** elemre, és megjelenik a **DimDate2.txt** nevű feltöltött fájl.
4. A tulajdonságok megtekintéséhez kattintson a **DimDate2.txt** elemre.
5. Ne feledje, hogy a Blob tulajdonságai panelen letöltheti és törölheti a fájlt.
   
    ![Az Azure Storage-blob megtekintése](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)

## <a name="step-2-create-an-external-table-for-the-sample-data"></a>2. lépés: Külső tábla létrehozása a mintaadatokhoz
Ebben a szakaszban létrehozunk külső táblát, amely a mintaadatokat határozza meg.

A PolyBase külső táblák segítségével fér hozzá az adatokhoz az Azure Blob Storage-ban. Mivel a rendszer nem az SQL Data Warehouse-ban az adatokat, a PolyBase adatbázis-alapú kötődő hitelesítési adatokkal végzi a külső adatok hitelesítését..

Az ebben a példában található példa a következő Transact-SQL utasításokkal hoz létre külső táblákat.

* [Create Master Key (Transact-SQL)][Create Master Key (Transact-SQL)] (Mesterkulcs létrehozása) az adatbázishoz kötődő hitelesítő adatok titkosításához.
* [Create Database Scoped Credential (Transact-SQL)][Create Database Scoped Credential (Transact-SQL)] (Adatbázishoz kötődő hitelesítő adatok létrehozása) az Azure Storage-fiókhoz tartozó hitelesítési adatok megadásához.
* [Create External Data Source (Transact-SQL)][Create External Data Source (Transact-SQL)] (Külső adatforrás létrehozása) az Azure Blob Storage helyének megadásához.
* [Create External File Format (Transact-SQL)][Create External File Format (Transact-SQL)] (Külső fájlformátum létrehozása) az adatformátum megadásához.
* [Create External Table (Transact-SQL)][Create External Table (Transact-SQL)] (Külső tábla létrehozása) a tábladefiníció és az adatok helyének megadásához.

Futtassa ezt a lekérdezést az SQL Data Warehouse-adatbázison. Létrehoz egy külső táblát DimDate2External néven a dbo sémában, amely az Azure Blob Storage DimDate2.txt mintaadataira mutat.

```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


Az SQL Server Object Explorerben a Visual Studióban megtekintheti a külső fájlformátumot, a külső adatforrást és a DimDate2External táblát.

![Külső tábla megtekintése](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>3. lépés: Adatok betöltése az SQL Data Warehouse-ba
Ha létrejött a külső tábla, betöltheti az adatokat egy új táblába, vagy beszúrhatja őket egy meglévő táblába.

* Az adatok új táblába való betöltéséhez futtassa a [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] utasítást. Az új tábla tartalmazza a lekérdezésben szereplő oszlopokat. Az oszlopok adattípusai megfelelnek a külső tábla definíciójában szereplő adattípusoknak.
* Az adatok meglévő táblába való betöltéséhez használja az [INSERT ---SELECT (Transact-SQL)][INSERT...SELECT (Transact-SQL)] utasítást.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>4. lépés: Statisztikák létrehozása az újonnan betöltött adatokról
Az SQL Data Warehouse nem tudja automatikus létrehozni és frissíteni a statisztikákat. A lekérdezési teljesítmény eléréséhez ezért fontos statisztikákat létrehozni minden tábla minden oszlopához az első betöltéskor. Fontos a statisztikák frissítése is az adatok lényeges módosításai után.

Ez a példa egyoszlopos statisztikát hoz létre az új DimDate2 táblához.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

További tudnivalók: [Statisztika][Statistics].  

## <a name="next-steps"></a>Következő lépések
A PolyBase-t használó megoldások fejlesztéséről a [PolyBase-útmutatóban][PolyBase guide] találhat további információt.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[PolyBase guide]: ./sql-data-warehouse-load-polybase-guide.md
[latest version of AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx

