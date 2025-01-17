---
title: "Kiadások előrejelzése az Azure Cost Management szolgáltatással | Microsoft Docs"
description: "Előre jelezheti költségeit a korábbi használati és költségadatok használatával."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/30/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 03624efc419efe46aef472007b438442ce22eb9c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="forecast-future-spending"></a>Jövőbeli kiadások előrejelzése

Az Azure Cost Management by Cloudyn szolgáltatás segít előre jelezni a jövőbeli kiadásokat a korábbi használati és költségadatok alapján. Cloudyn-jelentések használatával minden költség-előrejelzési adat megtekinthető. Az ebben az oktatóanyagban szereplő példák végigvezetik a költség-előrejelzések áttekintésén a jelentések használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Jövőbeli kiadások előrejelzése

## <a name="forecast-future-spending"></a>Jövőbeli kiadások előrejelzése

A Cloudyn költség-előrejelzési jelentéseinek segítségével felmérheti kiadásait a használat alapján. A jelentések elsődleges célja, hogy segítsenek kiadási trendjeit vállalata elvárásainak szintjén tartani. A használt jelentések: aktuális havi előre jelzett költség és éves előre jelzett költség. Mindkettő a jövőbeli kiadások előrejelzését mutatja, ha a használata viszonylag konzisztens az elmúlt 30 napnyi használattal.

Az aktuális havi előre jelzett költségről szóló jelentés a szolgáltatások költségeit mutatja. A hónap eleji és az előző havi költségeket felhasználva jeleníti meg az előre jelzett költséget. Kattintson a portál tetején található jelentések menüben a **Cost (Költség)** > **Projection and Budget (Előrejelzés és költségvetés)** > **Current Month Projected Cost (Aktuális havi előre jelzett költség)** elemre. Az alábbi képen egy példa látható.

![aktuális havi előre jelzett költség](./media/tutorial-forecast-spending/project-month01.png)

A példában látható, melyik szolgáltatások költöttek a legtöbbet. Az Azure-költségek alacsonyabbak voltak, mint az AWS-költségek. Az Azure-beli virtuális gépek költség-előrejelzési részleteinek megtekintéséhez a **Filter (Szűrő)** listában válassza ki az **Azure/VM** elemet.

![Azure-beli virtuális gépek aktuális havi előre jelzett költsége](./media/tutorial-forecast-spending/project-month02.png)

Kövesse az előző alapvető lépéseket bármely más szolgáltatás havi költség-előrejelzéseinek megtekintéséhez.

Az éves előre jelzett költségről szóló jelentés a szolgáltatások extrapolált költségét mutatja a következő 12 hónapon keresztül.

Kattintson a portál tetején található jelentések menüben a **Cost (Költség)** > **Projection and Budget (Előrejelzés és költségvetés)** > **Annual Projected Cost (Éves előre jelzett költség)** elemre. Az alábbi képen egy példa látható.

![jelentés éves előre jelzett költségről](./media/tutorial-forecast-spending/project-annual01.png)

A példában látható, melyik szolgáltatások költöttek a legtöbbet. A havi példához hasonlóan az Azure-költségek alacsonyabbak voltak, mint az AWS-költségek. Az Azure-beli virtuális gépek költség-előrejelzési részleteinek megtekintéséhez a **Filter (Szűrő)** listában válassza ki az **Azure/VM** elemet.

![virtuális gépek éves előre jelzett költsége](./media/tutorial-forecast-spending/project-annual02.png)

A fenti képen az Azure-beli virtuális gépek éves előre jelzett költsége 28 374 dollár.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jövőbeli kiadások előrejelzése


A következő oktatóanyagra továbblépve megtanulhatja, hogyan kezelje költségeit a költséglefoglalási és költséghelyi visszacsatolási jelentésekkel.

> [!div class="nextstepaction"]
> [Költségek kezelése költséglefoglalási és költséghelyi visszacsatolási jelentésekkel](tutorial-manage-costs.md)
