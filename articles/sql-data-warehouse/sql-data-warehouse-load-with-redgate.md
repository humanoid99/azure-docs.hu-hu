---
title: "Adatok betöltése az Azure-adattárházba a Redgate használatával | Microsoft Docs"
description: "Az alábbiakból megismerheti, hogyan használható a Redgate Data Platform Studio az adattárház-forgatókönyvek esetén."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: jhubbard
editor: 
ms.assetid: 670aef98-31f7-4436-86c0-cc989a39fe7f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2548f779767635865daf790d301d86feff573a29
ms.openlocfilehash: cb0b5489ccfabb0e2a4ee412162a18930073b309
ms.lasthandoff: 01/24/2017



---
# <a name="load-data-with-redgate-data-platform-studio"></a>Adatok betöltése a Redgate Data Platform Studióval
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)
> * [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

Ebből az oktatóanyagból megtudhatja, hogy miként használható a [Redgate Data Platform Studio](http://www.red-gate.com/products/azure-development/data-platform-studio/) (DPS) az adatok helyszíni SQL Serverről Azure SQL Data Warehouse-ba történő áthelyezésére. A Data Platform Studio a legmegfelelőbb kompatibilitási javításokat és optimalizálásokat alkalmazza, így ez a leggyorsabb mód az SQL Data Warehouse használatának megkezdésére.

> [!NOTE]
> A [Redgate](http://www.red-gate.com) már régóta a Microsoft partnere, és különböző SQL Server-eszközöket tesz elérhetővé. A Data Platform Studio e funkciója ingyenesen elérhető kereskedelmi és nem kereskedelmi használatra.
> 
> 

## <a name="before-you-begin"></a>Előkészületek
### <a name="create-or-identify-resources"></a>Erőforrások létrehozása és azonosítása
Az oktatóanyag elindítása előtt a következőkkel kell rendelkeznie:

* **Helyszíni SQL Server-adatbázis:** Az SQL Data Warehouse-ba importálni kívánt adatoknak egy helyszíni SQL Serverről (2008 R2 vagy újabb verzió) kell származniuk. A Data Platform Studio nem tud közvetlenül adatokat importálni egy Azure SQL Database-ből vagy szövegfájlokból.
* **Azure-tárfiók:** A Data Platform Studio előkészíti az adatokat az Azure Blob Storage-ban azelőtt, hogy betöltené őket az SQL Data Warehouse-ba. A tárfióknak a „klasszikus” üzemi modell helyett a „Resource Manager”-alapú üzemi modellt (alapértelmezett) kell használnia. Ha még nem rendelkezik tárfiókkal, hozzon létre egyet. 
* **SQL Data Warehouse:** Az oktatóanyag az adatokat a helyi SQL Serverről az SQL Data Warehouse-ba helyezi át, ezért rendelkeznie kell egy online adattárházzal. Ha még nem rendelkezik adattárházzal, hozzon létre egyet.

> [!NOTE]
> Ha a tárfiók és az adattárház ugyanabban a régióban található, jobb teljesítményt érhet el.
> 
> 

## <a name="step-1-sign-in-to-data-platform-studio-with-your-azure-account"></a>1. lépés: Bejelentkezés a Data Platform Studióba az Azure-fiókkal
Nyissa meg a webböngészőt, majd navigáljon a [Data Platform Studio](https://www.dataplatformstudio.com/) webhelyére. Jelentkezzen be ugyanazzal az Azure-fiókkal, amelyet a tárfiók és az adattárház létrehozásához használt. Ha az e-mail-címe egy munkahelyi vagy iskolai fiókkal és egy Microsoft-fiókkal is társítva van, győződjön meg arról, hogy azt a fiókot választja, amely hozzá tud férni az erőforrásaihoz.

> [!NOTE]
> Ha most használja először a Data Platform Studiót, a rendszer megkéri, hogy engedélyezze az alkalmazás számára az Azure-erőforrások kezelését.
> 
> 

## <a name="step-2-start-the-import-wizard"></a>2. lépés: Az importálási varázsló elindítása
A DPS főképernyőjén válassza az Import to Azure SQL Data Warehouse (Importálás az Azure SQL Data Warehouse-ba) hivatkozást az importálási varázsló elindításához.

![][1]

## <a name="step-3-install-the-data-platform-studio-gateway"></a>3. lépés: A Data Platform Studio-átjáró telepítése
A helyszíni SQL Server-adatbázishoz való csatlakozáshoz telepíteni kell a DPS-átjárót. Az átjáró egy ügyfélügynök, amely hozzáférést biztosít a helyszíni környezethez, kinyeri az adatokat, és feltölti őket a tárfiókba. Az adatai soha nem haladnak keresztül a Redgate kiszolgálóin. Az átjáró telepítése:

1. Kattintson a **Create Gateway** (Átjáró létrehozása) hivatkozásra.
2. Töltse le, és telepítse az átjárót a megadott telepítővel.

![][2]

> [!NOTE]
> Az átjáró bármely olyan gépre telepíthető, amely hálózati hozzáféréssel rendelkezik az SQL Server-forrásadatbázishoz. Az SQL Server-adatbázishoz a Windows-hitelesítés használatával, az aktuális felhasználó hitelesítő adataival fér hozzá.
> 
> 

A telepítést követően az átjáró Kapcsolatban állapotúra változik, és kiválaszthatja a Next (Tovább) lehetőséget.

## <a name="step-4-identify-the-source-database"></a>4. lépés: A forrásadatbázis azonosítása
Az *Enter Server Name* (Kiszolgálónév megadása) szövegmezőben adja meg az adatbázist üzemeltető kiszolgáló nevét, majd válassza a **Next** (Tovább) lehetőséget. Ezt követően a legördülő menüből válassza ki azt az adatbázist, amelyből adatot kíván importálni.

![][3]

Majd a DPS importálható táblákat keres a kiválasztott adatbázisban. Alapértelmezés szerint a DPS az adatbázis összes tábláját importálja. Az All Tables (Összes tábla) hivatkozás kibontásával táblákat jelölhet ki, vagy törölheti a jelölésüket. A folytatáshoz válassza a Next (Tovább) gombot.

## <a name="step-5-choose-a-storage-account-to-stage-the-data"></a>5. lépés: Tárfiók kiválasztása az adatok előkészítéséhez
A DPS felkéri, hogy adjon meg egy helyet az adatok előkészítéséhez. Válasszon ki egy meglévő tárfiókot az előfizetéséből, majd válassza a **Next** (Tovább) lehetőséget.

> [!NOTE]
> A DPS létrehoz egy új blobtárolót a kiválasztott tárfiókban, és minden importáláshoz más mappát használ.
> 
> 

![][4]

## <a name="step-6-select-a-data-warehouse"></a>6. lépés: Adattárház kiválasztása
Ezután válassza ki azt az online [Azure SQL Data Warehouse](http://aka.ms/sqldw)-adatbázist, amelybe importálni szeretné az adatokat. Az adatbázis kiválasztása után adja meg a hitelesítő adatokat az adatbázishoz való csatlakozáshoz, majd válassza a **Next** (Tovább) lehetőséget.

![][5]

> [!NOTE]
> A DPS egyesíti a forrásadattáblákat az adattárházban. A DPS figyelmezteti, ha a tábla neve miatt felül kell írni az adattárház meglévő tábláit. Ha szeretné, törölheti az adattárház meglévő objektumait. Ehhez az importálás előtt jelölje be a Delete all existing objects (Összes meglévő objektum törlése) lehetőséget.
> 
> 

## <a name="step-7-import-the-data"></a>7. lépés: Az adatok importálása
A DPS visszaigazolja, hogy Ön importálni szeretné az adatokat. Az adatok importálásának megkezdéséhez egyszerűen kattintson a Start import (Importálás indítása) gombra.

![][6]

A DPS megjeleníti az adatok helyszíni SQL Serverről történő kinyerésének és feltöltésének állapotát, illetve az SQL Data Warehouse-ba való importálásuk folyamatát.

![][7]

Amint befejeződik az importálás, a DPS megjelenít egy összegzést az adatok importálásáról, és egy módosítási jelentést az elvégzett kompatibilitási javításokról.

![][8]

## <a name="next-steps"></a>Következő lépések
Ha át szeretné tekinteni az adatait az SQL Data Warehouse-ban, először olvassa el a következőket:

* [Az Azure SQL Data Warehouse lekérdezése (Visual Studio)][Query Azure SQL Data Warehouse (Visual Studio)]
* [Adatok megjelenítése Power BI használatával][Visualize data with Power BI]

További információk a Redgate Data Platform Studióról:

* [A DPS kezdőlapjának felkeresése](http://www.dataplatformstudio.com/)
* [Egy bemutató megtekintése a DPS-ről a Channel9-on](https://channel9.msdn.com/Blogs/cloud-with-a-silver-lining/Loading-data-into-Azure-SQL-Datawarehouse-with-Redgate-Data-Platform-Studio)

Az adatok áttelepítésének és SQL Data Warehouse-ba való betöltésének egyéb módjaival kapcsolatban lásd:

* [Megoldás áttelepítése az SQL Data Warehouse-ba][Migrate your solution to SQL Data Warehouse]
* [Adatok betöltése az Azure SQL Data Warehouse-ba](sql-data-warehouse-overview-load.md)

További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

<!--Image references-->
[1]: media/sql-data-warehouse-redgate/2016-10-05_15-59-56.png
[2]: media/sql-data-warehouse-redgate/2016-10-05_11-16-07.png
[3]: media/sql-data-warehouse-redgate/2016-10-05_11-17-46.png
[4]: media/sql-data-warehouse-redgate/2016-10-05_11-20-41.png
[5]: media/sql-data-warehouse-redgate/2016-10-05_11-31-24.png
[6]: media/sql-data-warehouse-redgate/2016-10-05_11-32-20.png
[7]: media/sql-data-warehouse-redgate/2016-10-05_11-49-53.png
[8]: media/sql-data-warehouse-redgate/2016-10-05_12-57-10.png

<!--Article references-->
[Query Azure SQL Data Warehouse (Visual Studio)]: ./sql-data-warehouse-query-visual-studio.md
[Visualize data with Power BI]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md
[Migrate your solution to SQL Data Warehouse]: ./sql-data-warehouse-overview-migrate.md
[Load data into Azure SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md

