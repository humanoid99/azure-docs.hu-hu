---
title: "Azure Service Fabric beépülő modul az Eclipse-hez | Microsoft Docs"
description: "Bevezetés az Eclipse Service Fabric beépülő moduljának használatába."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0407eab7e70649999ba07730425366b7b62e4e7a
ms.lasthandoff: 03/22/2017


---

# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Az Eclipse Service Fabric beépülő moduljának Java alkalmazásfejlesztése
Az Eclipse a Java-fejlesztők által leggyakrabban használt integrált fejlesztőkörnyezetek (IDE-k) közé tartozik. Ebben a cikkben azt ismertetjük, hogyan állíthatja be az Eclipse fejlesztői környezetet az Azure Service Fabrickel való használathoz. Megtudhatja, hogyan telepítheti a Service Fabric beépülő modult, hogyan hozhat létre Service Fabric-alkalmazást, és hogyan helyezhet üzembe Service Fabric-alkalmazásokat helyi vagy távoli Service Fabric-fürtön az Eclipse Neonon.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>A Service Fabric beépülő modul telepítése vagy frissítése az Eclipse Neonon
Telepíthet egy Service Fabric beépülő modult az Eclipse-en. A beépülő modul segíthet leegyszerűsíteni a Java-szolgáltatások létrehozásának és üzembe helyezésének folyamatát.

1.  Gondoskodjon róla, hogy az Eclipse Neon és a Buildship legújabb verziója (1.0.17-es vagy újabb) legyen telepítve:
    -   A telepített összetevők verziójának ellenőrzéséhez az Eclipse Neonban lépjen a **Help** > **Installation Details** (Súgó, Telepítés részletei) területre.
    -   A Buildship frissítéséért lásd: [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse beépülő modulok a Gradle-hez).
    -   Az Eclipse Neon frissítéseinek kereséséhez és telepítéséhez lépjen a **Help** > **Check for Updates** (Súgó, Frissítések keresése) területre.

2.  A Service Fabric beépülő modul telepítéséhez az Eclipse Neonban lépjen a **Help** > **Install New Software** (Súgó, Új szoftver telepítése) területre.
  1.    A **Work with** (Használat) mezőbe írja be a **http://dl.windowsazure.com/eclipse/servicefabric** címet.
  2.    Kattintson az **Add** (Hozzáadás) parancsra.
    ![Az Eclipse Neon Service Fabric beépülő modulja][sf-eclipse-plugin-install]
  3.    Válassza ki a Service Fabric beépülő modult, majd kattintson a **Next** (Tovább) gombra.
  4.    Végezze el a telepítés lépéseit, majd fogadja el a Microsoft szoftverlicenc-szerződését.

Ha a Service Fabric beépülő modul már telepítve van, győződjön meg arról, hogy a legújabb verzióval rendelkezik. Az elérhető frissítések kereséséhez lépjen a **Help** > **Installation Details** (Súgó, Telepítés részletei) területre. A telepített beépülő modulok listájában válassza ki a Service Fabric elemet, majd kattintson az **Update** (Frissítés) parancsra. A rendszer telepíti az elérhető frissítéseket.

> [!NOTE]
> Ha a Service Fabric beépülő modul telepítése vagy frissítése túl lassú, azt az Eclipse valamelyik beállítása okozhatja. Az Eclipse metaadatokat gyűjt az Eclipse-példányhoz regisztrált frissítési helyek összes módosításáról. Ahhoz, hogy fel tudja gyorsítani a Service Fabric beépülő modul frissítéseinek keresési és telepítési folyamatát, lépjen az **Available Software Sites** (Elérhető szoftverhelyek) területre. Törölje az összes hely jelölőnégyzetét a Service Fabric beépülő modul helyére (http://dl.windowsazure.com/eclipse/servicefabric) mutató jelölőnégyzet kivételével.

## <a name="create-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás létrehozása az Eclipse-ben

1.  Az Eclipse Neonban lépjen a **File** > **New** > **Other** (Fájl, Új, Egyéb) lehetőségre. Válassza a **Service Fabric Project** (Service Fabric-projekt) lehetőséget, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 1. oldal][create-application/p1]

2.  Adja meg a projekt nevét, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 2. oldal][create-application/p2]

3.  A sablonok listájában válassza a **Service Template** (Szolgáltatássablon) elemet. Válassza ki a szolgáltatássablon típusát (Actor, Stateless, Container, Guest Binary – aktor, állapot nélküli, tároló, vendég bináris), majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Új projekt, 3. oldal][create-application/p3]

4.  Adja meg a szolgáltatás nevét és a szolgáltatás részleteit, majd kattintson a **Finish** (Befejezés) gombra.

    ![Service Fabric – Új projekt, 4. oldal][create-application/p4]

5. Az első Service Fabric-projekt létrehozásakor az **Open Associated Perspective** (Társított perspektíva megnyitása) párbeszédpanelen kattintson a **Yes** (Igen) gombra.

    ![Service Fabric – Új projekt, 5. oldal][create-application/p5]

6.  Így néz ki az új projekt:

    ![Service Fabric – Új projekt, 6. oldal][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Service Fabric-alkalmazás felépítése és üzembe helyezése az Eclipse-ben

1.  Kattintson a jobb gombbal az új Service Fabric-alkalmazásra, majd válassza a **Service Fabric** lehetőséget.

    ![Service Fabric – helyi menü][publish/RightClick]

2. Az almenüben válassza ki a kívánt beállítást:
    -   Az alkalmazás tisztítás nélküli kiépítéséhez kattintson a **Build Application** (Alkalmazás buildelése) parancsra.
    -   Az alkalmazás tiszta buildjének kiépítéséhez kattintson a **Rebuild Application** (Alkalmazás újrabuildelése) parancsra.
    -   A kiépített összetevők törléséhez az alkalmazásból kattintson a **Clean Application** (Alkalmazás tisztítása) parancsra.

3.  Ebből a menüből üzembe helyezheti vagy közzéteheti az alkalmazását, illetve visszavonhatja annak üzembe helyezését:
    -   A helyi fürtre való üzembe helyezéshez kattintson a **Deploy Application** (Alkalmazás üzembe helyezése) parancsra.
    -   A **Publish Application** (Alkalmazás közzététele) párbeszédpanelen válasszon egy közzétételi profilt:
        -  **Local.json**
        -  **Cloud.json**

     Ezek a JavaScript Object Notation (JSON-) fájlok a helyi vagy a felhőbeli (Azure-) fürthöz való csatlakozáshoz szükséges információk (például csatlakozási végpontok és biztonsági információk) tárolására szolgálnak.

  ![A Service Fabric közzétételi menüje][publish/Publish]

A Service Fabric-alkalmazások üzembe helyezésének másik módszere, ha Eclipse futtatási konfigurációkat használ.

  1.    Lépjen a **Run** > **Run Configurations** (Futtatás, Konfigurációk futtatása) területre.
  2.    A **Gradle Project** (Gradle-projekt) területen válassza a **ServiceFabricDeployer** futtatási konfigurációt.
  3.    A jobb oldali ablaktáblán, az **Arguments** (Argumentumok) lapon a **publishProfile** értékeként válassza a **local** (helyi) vagy a **cloud** (felhő) értéket.  Az alapértelmezett érték a **local** (helyi). Távoli vagy felhőalapú fürtbe végzett üzembe helyezéshez válassza a **cloud** (felhő) értéket.
  4.    Ahhoz, hogy a megfelelő információk legyenek betöltve a közzétételi profilokba, szükség szerint szerkessze a **Local.json** vagy a **Cloud.json** fájlt. Hozzáadhat vagy frissíthet végpontrészleteket és biztonsági hitelesítő adatokat.
  5.    Győződjön meg arról, hogy a **Working Directory** (Munkakönyvtár) az üzembe helyezni kívánt alkalmazásra mutat. Az alkalmazás módosításához kattintson a **Workspace** (Munkaterület) gombra, és válassza ki a kívánt alkalmazást.
  6.    Kattintson az **Apply** (Alkalmaz), majd a **Run** (Futtatás) gombra.

Néhány másodpercen belül megtörténik az alkalmazás felépítése és üzembe helyezése. Az üzembe helyezés állapotát a Service Fabric Explorerben követheti nyomon.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric-szolgáltatás hozzáadása a Service Fabric-alkalmazáshoz

Ha Service Fabric-szolgáltatást szeretne hozzáadni egy meglévő Service Fabric-alkalmazáshoz, végezze el a következő lépéseket:

1.  Kattintson a jobb gombbal a projektre, amelyhez hozzá szeretne adni egy szolgáltatást, majd kattintson a **Service Fabric** elemre.

    ![Service Fabric – Szolgáltatás hozzáadása, 1. oldal][add-service/p1]

2.  Kattintson az **Add Service Fabric Service** (Service Fabric-szolgáltatás hozzáadása) parancsra, és végezze el a szolgáltatás hozzáadásának lépéseit.
3.  Válassza ki a projekthez hozzáadni kívánt szolgáltatássablont, majd kattintson a **Next** (Tovább) gombra.

    ![Service Fabric – Szolgáltatás hozzáadása, 2. oldal][add-service/p2]

4.  Adja meg a szolgáltatás nevét (és a többi szükséges adatot), majd kattintson az **Add Service** (Szolgáltatás hozzáadása) gombra.  

    ![Service Fabric – Szolgáltatás hozzáadása, 3. oldal][add-service/p3]

5.  A szolgáltatás hozzáadása után a teljes projektstruktúra a következőhöz hasonlóan néz ki:

    ![Service Fabric – Szolgáltatás hozzáadása, 4. oldal][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>A Service Fabric Java-alkalmazásának frissítése

Frissítési forgatókönyv esetén tegyük fel, hogy az **App1** projektet hozta létre a Service Fabric beépülő modullal az Eclipse-ben. A projektet a beépülő modullal helyezte üzembe a **fabric:/App1Application** nevű alkalmazás létrehozásához. Az alkalmazás típusa **App1AppicationType**, a verziója pedig 1.0. A rendelkezésre állás megszakítása nélkül szeretné frissíteni az alkalmazást.

Először végezzen módosítást az alkalmazáson, majd építse újra a módosított szolgáltatást. Frissítse a módosított szolgáltatás jegyzékfájlját (ServiceManifest.xml) a szolgáltatás frissített verzióival (és a megfelelő Code, Config vagy Data értékkel). Módosítsa az alkalmazás jegyzékfájlját is (ApplicationManifest.xml) az alkalmazás frissített verziószámával és a módosított szolgáltatással.  

Ha az Eclipse Neonnal szeretné frissíteni az alkalmazást, létrehozhat egy duplikált futtatási konfigurációs profilt, amelyet aztán szükség szerint az alkalmazás frissítésére használhat.

1.  Lépjen a **Run** > **Run Configurations** (Futtatás, Konfigurációk futtatása) területre. A bal oldali ablaktáblában kattintson a **Gradle Project** (Gradle-projekt) bal oldalán található kis nyílra.
2.  Kattintson a jobb gombbal a **ServiceFabricDeployer** elemre, majd válassza a **Duplicate** (Megkettőzés) parancsot. Adjon egy új nevet a konfigurációnak, például **ServiceFabricUpgrader**.
3.  A jobb oldali ablaktáblán, az **Arguments** (Argumentumok) lapon módosítsa a **-Pconfig='deploy'** értéket **-Pconfig='upgrade'** értékre, majd kattintson az **Apply** (Alkalmaz) gombra.

Ez a folyamat olyan futtatási konfigurációs profilt hoz létre és ment el, amellyel bármikor frissítheti az alkalmazást. Az alkalmazás jegyzékfájljából a legújabb frissített alkalmazástípus-verziót is lekéri.

Az alkalmazás frissítése eltarthat néhány percig. Az alkalmazás frissítésének folyamatát a Service Fabric Explorerben követheti nyomon.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

