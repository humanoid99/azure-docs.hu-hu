---
title: "SQL Data Warehouse létrehozása PowerShell használatával | Microsoft Docs"
description: "SQL Data Warehouse létrehozása PowerShell használatával"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 97434863-7938-4129-8949-5a119f5949e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 58ba34f8f99b7cd2b6a9a199bc70d79431405100
ms.lasthandoff: 03/10/2017


---
# <a name="create-sql-data-warehouse-using-powershell"></a>SQL Data Warehouse létrehozása PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Ebből a cikkből megtudhatja, hogyan hozható létre az SQL Data Warehouse a PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következők szükségesek:

* **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][Azure Free Trial] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][MSDN Azure Credits].
* **Azure SQL Server**: Az [Azure SQL Database-adatbázis létrehozása az Azure Portal használatával][Create an Azure SQL database in the Azure Portal] vagy az [Azure SQL Database-adatbázis létrehozása a PowerShell használatával][Create an Azure SQL database with PowerShell] című cikkekben talál további információt.
* **Erőforráscsoport**: Használja ugyanazt az erőforráscsoportot, mint az Azure SQL-kiszolgáló, vagy tekintse át az [erőforráscsoportok létrehozásával foglalkozó](../azure-resource-manager/resource-group-portal.md) cikket.
* **PowerShell 1.0.3-as vagy újabb verzió**: A verziószámot a **Get-Module -ListAvailable -Name Azure** futtatásával ellenőrizheti.  A legújabb verziót a [Microsoft Webplatform-telepítőből][Microsoft Web Platform Installer] telepítheti.  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ a díjszabásról: [Az SQL Data Warehouse díjszabása][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse létrehozása
1. Nyissa meg a Windows PowerShellt.
2. A parancsmag futtatásával jelentkezzen be az Azure Resource Managerbe.

    ```Powershell
    Login-AzureRmAccount
    ```
3. Válassza ki a jelenlegi munkamenethez használni kívánt előfizetést.

    ```Powershell
    Get-AzureRmSubscription    -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```
4. Hozza létre az adatbázist. Ebben a példában egy adatbázist hozunk létre „mynewsqldw” néven, „DW400” szintű szolgáltatási céllal az „sqldwserver1” elnevezésű kiszolgálóra, amely a „mywesteuroperesgp1” nevű erőforráscsoportban található.

   ```Powershell
   New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
   ```

A szükséges paraméterek a következők:

* **RequestedServiceObjectiveName**: A kért [DWU][DWU]-mennyiség.  A támogatott értékek a következők: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 és DW6000.
* **DatabaseName**: A létrehozandó SQL Data Warehouse neve.
* **ServerName**: A létrehozás során használt kiszolgáló neve (V12-nek kell lennie).
* **ResourceGroupName**: A használt erőforráscsoport.  Az előfizetésben elérhető erőforráscsoportok kereséséhez használja a Get-AzureResource parancsot.
* **Edition**: Egy SQL Data Warehouse létrehozásához a „DataWarehouse” értéket kell megadni.

A választható paraméterek a következők:

* **CollationName**: Ha nincs megadva, az alapértelmezett rendezés: SQL_Latin1_General_CP1_CI_AS.  Az adatbázisok rendezése nem módosítható.
* **MaxSizeBytes**: Alapértelmezés szerint az adatbázisok maximális mérete 10 GB.

A paraméterbeállításokkal kapcsolatos további információk: [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase] és [Create Database (Azure SQL Data Warehouse)][Create Database (Azure SQL Data Warehouse)] (Adatbázis létrehozása (Azure SQL Data Warehouse)).

## <a name="next-steps"></a>Következő lépések
Miután az SQL Data Warehouse kiépítése befejeződött, [mintaadatokat tölthet be][loading sample data], vagy további részleteket tudhat meg a [fejlesztés][develop], [betöltés][load] vagy [áttelepítés][migrate] mikéntjéről.

Ha érdekli az SQL Data Warehouse programozott módon való kezelése, tekintse meg a [PowerShell-parancsmagok és a REST API-k][PowerShell cmdlets and REST APIs] használatával kapcsolatos cikkünket.

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[migrate]: ./sql-data-warehouse-overview-migrate.md
[develop]: ./sql-data-warehouse-overview-develop.md
[load]: ./sql-data-warehouse-load-with-bcp.md
[loading sample data]: ./sql-data-warehouse-load-sample-databases.md
[PowerShell cmdlets and REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Create an Azure SQL database in the Azure Portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-get-started-powershell.md
[how to create a resource group]: ../azure-resource-manager/resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references-->
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

