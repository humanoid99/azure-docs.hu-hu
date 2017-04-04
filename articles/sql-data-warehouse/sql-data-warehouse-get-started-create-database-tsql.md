---
title: "SQL Data Warehouse létrehozása a TSQL használatával | Microsoft Docs"
description: "Tudnivalók Azure SQL Data Warehouse a TSQL használatával történő létrehozásáról"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: a4e2e68e-aa9c-4dd3-abb0-f7df997d237a
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 5d3bcc3c1434b16279778573ccf3034f9ac28a4d
ms.openlocfilehash: 836d72e32e54ecef9691b55214766a1fc3ff9701
ms.lasthandoff: 12/06/2016


---
# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>SQL Data Warehouse-adatbázis létrehozása a Transact-SQL (TSQL) használatával
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Ebből a cikkből megtudhatja, hogyan hozhat létre egy SQL Data Warehouse-adatbázist a T-SQL használatával.

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következők szükségesek:

* **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][Azure Free Trial] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][MSDN Azure Credits].
* **Azure SQL Server**: az [Azure SQL Database logikai kiszolgáló létrehozása az Azure portál használatával][Create an Azure SQL Database logical server with the Azure Portal] vagy az [Azure SQL Database logikai kiszolgáló létrehozása a PowerShell használatával][Create an Azure SQL Database logical server with PowerShell] című cikkekben talál további információt.
* **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, mint az Azure SQL Server, vagy tekintse át az [erőforráscsoportok létrehozásával foglalkozó][how to create a resource group] cikket.
* **T-SQL-végrehajtási környezet**: a T-SQL-ek végrehajtásához a [Visual Studio][Installing Visual Studio and SSDT], az [sqlcmd][sqlcmd], vagy az [SSMS][SSMS] alkalmazáskörnyezet használható.

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ a díjszabásról: [Az SQL Data Warehouse díjszabása][SQL Data Warehouse pricing].
>
>

## <a name="create-a-database-with-visual-studio"></a>Adatbázis létrehozása a Visual Studióval
Ha Ön új Visual Studio-felhasználó, tekintse meg ezt a cikket: [Az Azure SQL Data Warehouse lekérdezése (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)].  A kezdéshez nyissa meg az SQL Server Object Explorert a Visual Studióban, és csatlakozzon az SQL Data Warehouse-adatbázisnak helyet adó kiszolgálóra.  A csatlakozás után úgy hozhatja létre az SQL Data Warehouse-adatbázist, hogy a következő SQL parancsot futtatja a **fő** adatbázison.  A parancs létrehozza a MySqlDwDb adatbázist DW400 szolgáltatási céllal, és lehetővé teszi az adatbázisnak, hogy legfeljebb 10 TB nagyságúra nőjön.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Adatbázis létrehozása sqlcmd használatával
Az sqlcmd segédprogrammal is futtathatja ugyanezt a parancsot. Ehhez a következőket kell beírnia a parancssorba.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

Ha nincs megadva, az alapértelmezett rendezés: COLLATE SQL_Latin1_General_CP1_CI_AS.  A `MAXSIZE` értéke 250 GB és 240 TB között lehet.  A `SERVICE_OBJECTIVE` értéke DW100 és DW2000 [DWU][DWU] között lehet.  Az összes érvényes értéket megtekintheti az MSDN-dokumentáció [CREATE DATABASE][CREATE DATABASE] (ADATBÁZIS LÉTREHOZÁSA) című részében.  A MAXSIZE és a SERVICE_OBJECTIVE paraméter is módosítható az [ALTER DATABASE][ALTER DATABASE] T-SQL-paranccsal.  Az adott adatbázis rendezése annak létrehozását követően nem módosítható.   A SERVICE_OBJECTIVE paraméter módosításakor elővigyázatosnak kell lennie, mivel a DWU módosításakor újraindulnak a szolgáltatások, és megszakadnak a folyamatban lévő lekérdezések.  A MAXSIZE módosítása nem indítja újra a szolgáltatásokat, mivel csupán egyszerű metaadat-műveletről van szó.

## <a name="next-steps"></a>Következő lépések
Miután az SQL Data Warehouse befejezte a kiépítést, [mintaadatokat tölthet be][load sample data], vagy további részleteket tudhat meg a [fejlesztés][develop], [betöltés][load] vagy [áttelepítés][migrate] módjáról.

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Query Azure SQL Data Warehouse (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[load sample data]: sql-data-warehouse-load-sample-databases.md
[Create an Azure SQL database with the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references-->
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

