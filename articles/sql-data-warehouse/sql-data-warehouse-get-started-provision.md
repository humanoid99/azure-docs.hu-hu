---
title: "SQL Data Warehouse létrehozása az Azure Portalon | Microsoft Docs"
description: "Tudnivalók Azure SQL Data Warehouse létrehozásáról az Azure Portalon"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 552e496e-d560-419c-9996-6bbc80c521cb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: create
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 75f4792ff2aa2b73ebc3ff976887a74ce09988f3
ms.lasthandoff: 03/10/2017


---
# <a name="create-an-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse létrehozása
> [!div class="op_single_selector"]
> * [Azure Portal](sql-data-warehouse-get-started-provision.md)
> * [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
> * [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
>
>

Ebben az oktatóanyagban az Azure Portalon létrehoz egy SQL Data Warehouse-t, amely egy AdventureWorksDW mintaadatbázist tartalmaz.

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez a következők szükségesek:

* **Azure-fiók**: A fiók létrehozásával kapcsolatban lásd: [Ingyenes Azure-fiók létrehozása][Azure Free Trial] vagy [Havi Azure-kredit a Visual Studio-előfizetőknek][MSDN Azure Credits].
* **Azure SQL-kiszolgáló**: További információ: [Azure SQL-adatbázis létrehozása az Azure Portalon][Create an Azure SQL database in the Azure portal].

> [!NOTE]
> Egy SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása][SQL Data Warehouse pricing].
>
>

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse létrehozása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Kattintson a **+ Új** > **Adatbázisok** > **SQL Data Warehouse** lehetőségre.

    ![Létrehozás](./media/sql-data-warehouse-get-started-provision/create-sample.gif)
3. Az **SQL Data Warehouse** panelen adja meg a szükséges információkat, majd kattintson a „Létrehozás” gombra az adatraktár létrehozásához.

    ![Adatbázis létrehozása](./media/sql-data-warehouse-get-started-provision/create-database.png)

   * **Kiszolgáló**: Javasoljuk, hogy először válassza ki a kiszolgálót.  
   * **Adatbázis neve**: Az SQL Data Warehouse-ra való hivatkozáshoz használt név.  Ennek a kiszolgálón egyedinek kell lennie.
   * **Teljesítmény**: Javasoljuk, hogy kiindulásként 400 [DWU][DWU]-t adjon meg. A csúszka jobbra-balra mozgatásával állíthatja az adatraktár teljesítményét, vagy skálázhatja azt vertikálisan le vagy fel a létrehozást követően.  A DWU egységekkel kapcsolatos további információkért tekintse meg [skálázással](sql-data-warehouse-manage-compute-overview.md) kapcsolatos dokumentációnkat vagy [díjszabási oldalunkat][SQL Data Warehouse pricing].
   * **Előfizetés**: Válassza ki az [előfizetést], amelyikre az adott SQL Data Warehouse díjai számlázva lesznek.
   * **Erőforráscsoport**: Az [erőforráscsoportok][Resource group] olyan tárolók, amelyekkel Azure-erőforrások gyűjteményét kezelheti. További információk az [erőforráscsoportokról](../azure-resource-manager/resource-group-overview.md).
   * **Forrás kiválasztása**: Kattintson a **Forrás kiválasztása** > **Minta** lehetőségre. Az Azure automatikusan feltölti a **Minta kiválasztása** beállítást az AdventureWorksDW elemmel.

   > [!NOTE]
   > Az SQL Data Warehouse alapértelmezett rendezése az SQL_Latin1_General_CP1_CI_AS. Ha másik rendezésre van szüksége, a [T-SQL][T-SQL] használatával hozhatja létre az adatbázist másik rendezéssel.
   >
   >

1. Kattintson a **Létrehozás** gombra az SQL Data Warehouse létrehozásához.
2. Várjon néhány percet. Amikor az adatraktár elkészült, a rendszer visszalépteti az [Azure Portal](https://portal.azure.com). Az SQL Data Warehouse az irányítópulton található meg az SQL Database adatbázisok listájában vagy a létrehozásához használt erőforráscsoportban.

    ![portál nézet](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Következő lépések
Miután létrehozta az SQL Data Warehouse-t, készen áll a [Csatlakozásra](sql-data-warehouse-connect-overview.md) és lekérdezések indítására.

Adatok az SQL Data Warehouse-ba való betöltésével kapcsolatban lásd a [betöltést áttekintő](sql-data-warehouse-overview-load.md) cikket.

Ha egy meglévő adatbázist szeretne áttelepíteni az SQL Data Warehouse-ba, tekintse meg az [áttelepítés áttekintését](sql-data-warehouse-overview-migrate.md), vagy használja az [áttelepítő segédprogramot](sql-data-warehouse-migrate-migration-utility.md).

Tűzfalszabályok is a Transact-SQL segítségével konfigurálhatók. További információ: [sp_set_firewall_rule][sp_set_firewall_rule] és [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Érdemes megtekinteni az [Ajánlott eljárásokat][Best practices] is.

<!--Article references-->
[Create an Azure SQL database in the Azure portal]: ../sql-database/sql-database-get-started.md
[Create an Azure SQL database with PowerShell]: ../sql-database/sql-database-create-and-configure-database-powershell
[resource groups]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[Best practices]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md
[előfizetést]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md

<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[SQL Data Warehouse pricing]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure Free Trial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure Credits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

