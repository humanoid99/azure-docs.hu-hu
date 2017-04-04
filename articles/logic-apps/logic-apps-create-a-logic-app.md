---
title: "Munkafolyamatok létrehozása az első Azure Logic App-alkalmazással | Microsoft Docs"
description: "Alkalmazások és SaaS szolgáltatások csatlakoztatása az első Logic App-alkalmazással – első lépések"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>Új, SaaS szolgáltatásokhoz csatlakozó logikai alkalmazás létrehozása
Ez a témakör bemutatja, hogyan kezdhet neki mindössze néhány perc alatt az [Azure Logic Apps](logic-apps-what-are-logic-apps.md) használatának. Bemutatunk egy egyszerű munkafolyamatot, amely érdekes tweetek elküldését teszi lehetővé az e-mail-címére.

Az itt részletezett forgatókönyvhöz a következőkre lesz szüksége:

* Azure-előfizetés
* Twitter-fiók
* Outlook.com- vagy Office 365 Outlook-fiók

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Olyan új logikai alkalmazás létrehozása, amely Twitter-üzeneteket küld Önnek e-mailben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

2. A bal oldali menüből válassza az **Új** > **Vállalati integráció** > **Logikai alkalmazás** elemet.

    Vagy válassza az **Új** elemet, írja be a keresőmezőbe a `logic app` kifejezést, majd nyomja le az Enter billentyűt. Válassza a **Logikai alkalmazás** > **Létrehozás** elemet.

3. Adja meg a logikai alkalmazás nevét, válassza ki az Azure-előfizetését, hozzon létre vagy válasszon ki egy Azure-erőforráscsoportot, válasszon egy helyet, majd kattintson a **Létrehozás** gombra.

    Ha a **Rögzítés az irányítópulton** lehetőséget választja, a logikai alkalmazás automatikusan megnyílik az üzembe helyezés után.

4. A logikai alkalmazás első megnyitásakor a kezdéshez egy sablonból választhat.
Jelen esetben az alapoktól való felépítéshez kattintson az **Üres Logic App** lehetőségre. 

5. Az első elem, amelyet létre kell hoznia, az eseményindító. Ez az esemény indítja el a logikai alkalmazást. A keresőmezőben keressen rá a **twitter** kifejezésre, majd válassza az **Új tweet közzétételekor** elemet. Jelentkezzen be Twitter-fiókja felhasználónevével és jelszavával.

6. Most pedig írjon be egy keresési kifejezést a logikai alkalmazás elindításához.

   ![Keresés a Twitteren](media/logic-apps-create-a-logic-app/twittersearch.png)

    A **Gyakoriság** és az **Időköz** határozza meg, hogy a logikai alkalmazás milyen gyakran keres új tweeteket és ad vissza minden tweetet az adott időtartományon belül.

7. Válassza az **Új lépés**, majd a **Művelet hozzáadása** vagy a **Feltétel hozzáadása** elemet.

    Ha a **Művelet hozzáadása** lehetőséget választja, kereshet az [elérhető összekötők](../connectors/apis-list.md) között a művelet kiválasztásához. 

8. A keresőmezőben keressen az **outlook** kifejezésre, majd válassza az **E-mail küldése** lehetőséget e-mail küldéséhez az Outlook-fiókjából bármely megadott e-mail-címre.

   ![Műveletek](media/logic-apps-create-a-logic-app/actions.png)

9. Ezután ki kell töltenie a kívánt e-mailre vonatkozó paramétereket:

   ![Paraméterek](media/logic-apps-create-a-logic-app/parameters.png)

10. Végül a **Mentés** lehetőséget választva élesítheti a logikai alkalmazást.

## <a name="manage-your-logic-app-after-creation"></a>Létrehozott logikai alkalmazás kezelése

A logikai alkalmazás a fentiek elvégzésével működésbe lépett. Időközönként tweeteket fog keresni a beírt keresési kifejezés alapján. Ha egyező tweetet talál, egy e-mailt küld. Végül tekintsük át, hogyan lehet letiltani az alkalmazást, illetve ellenőrizni működését.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. A bal oldali menüben kattintson a **További szolgáltatások** elemre. A **Vállalati integráció** résznél válassza a **Logikai alkalmazások** elemet. Válassza ki a logikai alkalmazást.

    *    Az alkalmazás állapotának, végrehajtási előzményeinek és általános információnak megtekintéséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjén. Ha nem jelennek meg a várt adatok, válassza a **Frissítés** elemet a parancssávon.

    *    Az alkalmazás szerkesztéséhez válassza a **Logikaialkalmazás-tervező** lehetőséget a logikai alkalmazás menüjében.

    *    Az alkalmazás ideiglenes kikapcsolásához a logikai alkalmazás menüjében válassza az **Áttekintés** elemet. A parancssávon válassza a **Letiltás** elemet.

    *    Az alkalmazás törléséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjében. 
    A parancssávon válassza a **Törlés** elemet. Adja meg a logikai alkalmazás nevét, majd válassza a **Törlés** elemet.

Az elmúlt kevesebb mint 5 percben létrehozott és beállított egy egyszerű, a felhőben futó logikai alkalmazást. A Logic Apps funkcióinak használatával kapcsolatban további információkat talál a [Use logic app features (Logikai alkalmazások funkcióinak használata)] című cikkben. Ha magukról a logikai alkalmazás definícióiról szeretne többet megtudni, olvassa el a [logikaialkalmazás-definíciók készítését](../logic-apps/logic-apps-author-definitions.md) bemutató cikket.

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Use logic app features (Logikai alkalmazások funkcióinak használata)]: logic-apps-create-a-logic-app.md
