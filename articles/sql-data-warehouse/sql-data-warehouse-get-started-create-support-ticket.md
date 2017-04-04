---
title: "Támogatási jegy létrehozása az SQL Data Warehouse-hoz| Microsoft Docs"
description: "Támogatási jegy létrehozása az SQL Data Warehouse-hoz."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 889776269aa3d722115ea0632b7c3db2775924eb
ms.openlocfilehash: cc3bc8a0463842ba11b9cdcc996319e2d811ce8a
ms.lasthandoff: 02/11/2017


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Támogatási jegy létrehozása az SQL Data Warehouse-hoz
Ha bármilyen probléma merül fel az SQL Data Warehouses-szal kapcsolatban, hozzon létre egy támogatási jegyet, hogy mérnöki csapatunk a segítségére lehessen.

> [!NOTE] 
> 2016. december 20-tól az Azure Portal erőforrásállapot-ellenőrzése nem biztosít pontos eredményeket. Folyamatosan dolgozunk a probléma megoldásán. 


## <a name="create-a-support-ticket"></a>Támogatási jegy létrehozása
1. Nyissa meg az [Azure Portalt][Azure portal].
2. A kezdőképernyőn kattintson a **Súgó és támogatás** csempére.
   
    ![Súgó és támogatás](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. A Súgó és támogatás panelen kattintson a **Támogatási kérelem létrehozása** gombra.
   
    ![Új támogatási kérelem](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. Válassza ki a **Kérelemtípust**.
   
    ![Kérelemtípus](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > Alapértelmezés szerint minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik egy 45 000 egységnyi **DTU-kvótával**. Ez a kvóta egyszerűen egy biztonsági korlát. A kvótát egy támogatási jegy létrehozásával, és a kérelem típusaként a *Kvóta* kiválasztásával növelheti meg. A DTU-igény kiszámításához szorozza meg 7,5-tel az összes szükséges [DWU][DWU] értékét. Ha például két DW6000-et szeretne üzemeltetni egy SQL-kiszolgálón, akkor 90 000 egységnyi DTU-kvótát kell igényelnie.  Az aktuális DTU-felhasználást az SQL-kiszolgáló panelen tekintheti meg a portálon. A DTU-kvótába a szüneteltetett és a nem szüneteltetett adatbázisok is beleszámítanak. 
   > 
   > 
5. Válassza ki az **Előfizetést**, amely alatt az az adatbázis fut, amellyel kapcsolatban támogatást kér.
   
    ![Előfizetést](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Erőforrásként adja meg a **SQL Data Warehouse** szolgáltatást.
   
    ![Erőforrás](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. Jelölje ki az [Azure támogatási csomagot][Azure support plan].
   
   * A **számlázással, kvótával és az előfizetés kezelésével** kapcsolatos támogatás minden támogatási szinten elérhető.
   * A **javítás/csere** támogatás a [Fejlesztői][Developer], a [Standard][Standard], a [Közvetlen professzionális támogatás][Professional Direct] vagy a [Premier szintű támogatás][Premier] esetén érhető el. A javítás/csere típusú problémákkal az Azure használata során fellépő hibák esetén lehet a támogatáshoz fordulni, ha a hibát nagy valószínűséggel a Microsoft terméke okozta.
   * A **fejlesztői mentorálás** és a **tanácsadási szolgáltatás** [Közvetlen professzionális támogatás][Professional Direct] és [Premier szintű támogatás][Premier] esetén érhető el. 
     
     Premier szintű támogatás megléte esetén az SQL Data Warehouse-szal kapcsolatos problémákat is jelentheti a [Microsoft Premier online portálon][Microsoft Premier online portal].  Az [Azure-támogatás ügyfeleknek][Azure support plan] című témakör részletesen bemutatja a támogatási csomagokat, beleértve azok hatókörét, a válaszidőt, a díjszabást és egyéb információkat.  Az Azure-támogatással kapcsolatos gyakori kérdéseket az [Azure-támogatás – gyakori kérdések][Azure support FAQs] című témakör tekinti át.  
     
     ![Támogatási csomag](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. Válassza ki a **Problem Type** (Probléma típusa) és a **Category** (Kategória) mezőt. Ebben a példában a választott problématípus az „Eszközök”, a kategória pedig az „Ügyféleszközök”. 
   
    ![Probléma típusa és kategóriája](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Írja le a problémát, és hogy az milyen mértékben hat az üzleti működésre.
   
    ![A probléma leírása](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. A támogatási jegyen előre ki lesznek töltve a **kapcsolattartási adatok**. Ha szükséges, pontosítsa azokat.
    
    ![Kapcsolattartási adatok](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Kattintson a**Create** (Létrehozás) gombra a támogatási kérelem elküldéséhez.

## <a name="monitor-a-support-ticket"></a>Támogatási jegy nyomon követése
A támogatási kérelem elküldése után az Azure támogatási csapata kapcsolatba lép Önnel. A kérelem állapotának és részleteinek megtekintéséhez kattintson a **Támogatási kérelmek kezelése** elemre az irányítópulton.

![Állapot ellenőrzése](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Egyéb források
Azt is megteheti, hogy kapcsolatba lép az SQL Data Warehouse-közösséggel a [Stack Overflow][Stack Overflow] webhelyen vagy az [Azure SQL Data Warehouse MSDN fórumon][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/


