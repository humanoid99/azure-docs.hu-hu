---
title: "ASP.NET-alkalmazás üzembe helyezése az Azure-ba a Visual Studio használatával | Microsoft Docs"
description: "ASP.NET webes projekt telepítése új webalkalmazásba az Azure App Service szolgáltatásban a Visual Studio használatával."
services: app-service\web
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: 69759e3c-384c-4afb-9278-db6724f6cb74
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 93107d19e1814b30d69fc12c92b544d9760bda6a
ms.lasthandoff: 04/04/2017


---
# <a name="deploy-an-aspnet-web-app-to-azure-app-service-using-visual-studio"></a>ASP.NET webalkalmazás telepítése az Azure App Service szolgáltatásba a Visual Studio használatával
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan telepíthető ASP.NET webalkalmazás az [Azure App Service szolgáltatásban lévő webalkalmazásba](app-service-web-overview.md) a Visual Studio 2015 használatával.

Az oktatóanyag azt feltételezi, hogy Ön egy ASP.NET fejlesztő, akinek nincs előzetes tapasztalata az Azure használatával. Az oktatóanyag végrehajtásával egy egyszerű, működő webalkalmazása lesz a felhőben.

Az oktatóanyagból a következőket sajátíthatja el:

* Új App Service webalkalmazás létrehozása a Visual Studio programban új webes projekt létrehozása során.
* Webes projekt telepítése egy App Service webalkalmazásba a Visual Studio használatával.

Az ábra az oktatóanyag lépéseit mutatja be.

![A Visual Studio létrehozási és telepítési ábrája](./media/web-sites-dotnet-get-started/Create_App.png)

Az oktatóanyag végén a [Hibaelhárítás](#troubleshooting) szakaszban találhat javaslatokat azzal kapcsolatban, hogy mi a teendő, ha valami nem működik, a [További lépések](#next-steps) szakaszban pedig más oktatóanyagokra mutató hivatkozásokat találhat, amelyek részletesen bemutatják az Azure App Service használatát.

Mivel ez egy első lépéseket ismertető oktatóanyag, a bemutatott telepítendő webes projekt egyszerű – nem használ adatbázist és nem végez hitelesítést vagy engedélyezést. Összetettebb telepítési témakörökre mutató hivatkozásokat itt találhat: [How to deploy an Azure web app](web-sites-deploy.md) (Azure webalkalmazás telepítése).

Az Azure SDK for .NET telepítéséhez szükséges időn kívül az oktatóanyag elvégzése kb. 10–15 percet vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek
* Az oktatóanyag azt feltételezi, hogy Ön már használta az ASP.NET MVC-t és a Visual Studiót. A bevezetésért lásd: [Getting Started with ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) (ASP.NET MVC 5 – Első lépések).
* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 
  
    Ha nem szeretne regisztrálni Azure-fiókot az Azure App Service megismerése előtt, menjen [Az App Service kipróbálása](https://azure.microsoft.com/try/app-service/) oldalra. ahol létrehozhat egy rövid élettartamú alapszintű alkalmazást az App Service-ben. Ehhez nincs szükség bankkártyára, és nem jár semmilyen kötelezettséggel.

## <a name="setupdevenv"></a>A fejlesztési környezet kialakítása
Az oktatóanyag a Visual Studio 2015-höz, valamint az [Azure SDK for .NET](../dotnet-sdk.md) 2.9-es vagy újabb verziójához készült. 

* [Töltse le a legfrissebb Azure SDK-t a Visual Studio 2015-höz](http://go.microsoft.com/fwlink/?linkid=518003). Ha a Visual Studio 2015 még nincs telepítve, az SDK telepíti azt.
  
  > [!NOTE]
  > Attól függően, hogy mennyi SDK-függőség van telepítve a számítógépen, az SDK telepítése hosszú időt is igénybe vehet – néhány perctől akár több mint fél óráig is tarthat.
  > 
  > 

Ha a számítógépén a Visual Studio 2013 van telepítve, és inkább ezt szeretné használni, [töltse le a legfrissebb Azure SDK-t a Visual Studio 2013-hoz](http://go.microsoft.com/fwlink/?LinkID=324322). Egyes képernyők eltérhetnek az ábrákon láthatótól.

## <a name="create-a-web-application"></a>Webalkalmazás létrehozása
A következő lépés egy webalkalmazás-projekt létrehozása a Visual Studióban, valamint egy webalkalmazás létrehozása az Azure App Service-ben. Az oktatóanyag ezek szakaszában konfigurálja az új webes projektet. 

1. Nyissa meg a Visual Studio 2015-öt.
2. Kattintson a **File > New > Project** (Fájl > Új > Projekt) elemre.
3. A **New Project** (Új projekt) párbeszédpanelen válassza a **Visual C# > Web > ASP.NET Web Application** (ASP.NET webalkalmazás) elemet.
4. Győződjön meg arról, hogy a **.NET Framework 4.5.2** (.NET-keretrendszer 4.5.2) van kiválasztva célkeretrendszerként.
5. Az [Azure Application Insights](../application-insights/app-insights-overview.md) figyeli a webalkalmazása rendelkezésre állását, teljesítményét és használatát. Az **Add Application Insights to Project** (Az Application Insights hozzáadása a projekthez) jelölőnégyzet automatikusan be van jelölve, amikor először hoz létre webes projektet a Visual Studio telepítése után. Ha a jelölőnégyzet be van jelölve, de nem szeretné kipróbálni az Application Insights programot, törölje a jelölést.
6. Nevezze el az alkalmazást **MyExample** néven, majd kattintson az **OK** gombra.
   
    ![A New Project (Új projekt) párbeszédpanel](./media/web-sites-dotnet-get-started/GS13newprojdb.png)
7. A **New ASP.NET Project** (Új ASP.NET-projekt) párbeszédpanelban válassza az **MVC** sablont, majd kattintson a **Change Authentication** (Hitelesítés módosítása) lehetőségre.
   
    Ebben az oktatóanyagban egy ASP.NET MVC webes projektet telepít. Ha ASP.NET webes API-projektek telepítését szeretné elsajátítani, tekintse meg a [További lépések](#next-steps) szakaszt. 
   
    ![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](./media/web-sites-dotnet-get-started/GS13changeauth.png)
8. A **Change Authentication** (Hitelesítés módosítása) párbeszédpanelen kattintson a **No Authentication** (Nincs hitelesítés) elemre, majd az **OK** gombra.
   
    ![Nincs hitelesítés](./media/web-sites-dotnet-get-started/GS13noauth.png)
   
    Ebben az első lépéseket ismertető oktatóanyagban egy egyszerű alkalmazást telepít, amely nem használ felhasználói bejelentkezést.
9. A **New ASP.NET Project** (Új ASP.NET-projekt) párbeszédpanel **Microsoft Azure** szakaszában győződjön meg arról hogy a **Host in the cloud** (Üzemeltetés a felhőben) lehetőség be van jelölve, és a legördülő listából ki van választva az **App Service** lehetőség.
   
    ![A New ASP.NET Project (Új ASP.NET-projekt) párbeszédpanel](./media/web-sites-dotnet-get-started/GS13newaspnetprojdb.png)
   
    Ezek a beállítások utasítják a Visual Studiót egy Azure webalkalmazás létrehozására a webes projekthez.
10. Kattintson az **OK** gombra

## <a name="create-the-azure-resources"></a>Azure-erőforrások létrehozása
Most pedig utasítani fogja a Visual Studiót a kívánt Azure-erőforrások létrehozására.

1. A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson az **Add an account** (Fiók hozzáadása) gombra, majd jelentkezzen be az Azure-ba az Azure-előfizetése kezeléséhez használt fiók azonosítójával és jelszavával.
   
    ![Bejelentkezés az Azure-ba](./media/web-sites-dotnet-get-started/configuresitesettings.png)
   
    Ha már bejelentkezett korábban ugyanezen a számítógépen, előfordulhat, hogy nem jelenik meg az **Add an account** (Fiók hozzáadása) gomb. Ebben az esetben kihagyhatja ezt a lépést, vagy előfordulhat, hogy újra meg kell adnia a hitelesítő adatait.
2. Adjon meg egy **webalkalmazás-nevet**, amely egyedi az *azurewebsites.net* tartományban. Adhatja például a webalkalmazásnak a MyExample nevet számjegyekkel, hogy egyedi legyen, például: MyExample810. Ha a rendszer létrehoz egy alapértelmezett webes nevet, az egyedi lesz, így használhatja.
   
    Ha valaki már használta az Ön által megadott nevet, a zöld pipa helyett egy piros felkiáltójel jelenik meg a jobb oldalon. Ekkor meg kell adnia egy másik nevet.
   
    Az alkalmazás URL-címe a megadott név lesz a *.azurewebsites.net* utótaggal. Ha például a név `MyExample810`, az URL-cím `myexample810.azurewebsites.net`.
   
    Az Azure-webalkalmazásokhoz egyéni tartományt is használtat. További információk: [Configure a custom domain name in Azure App Service](web-sites-custom-domain-name.md) (Egyéni tartománynév beállítása az Azure App Service-ben).
3. Kattintson a **New** (Új) gombra a **Resource Group** (Erőforráscsoport) mező mellett, majd írja be a „MyExample” nevet, vagy egy másik kívánt nevet. 
   
    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/web-sites-dotnet-get-started/rgcreate.png)
   
    Az erőforráscsoport olyan Azure-erőforrások gyűjteménye, mint a webalkalmazások, adatbázisok és virtuális gépek. Az oktatóanyagok esetén a legjobb megoldás új erőforráscsoport létrehozása, mert így egyszerűbb az oktatóanyag végrehajtása során létrehozott Azure-erőforrások eltávolítása egy lépésben. További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).
4. Kattintson a **New** (Új) gombra az **App Service Plan** (App Service-csomag) legördülő menü mellett.
   
    ![A Create App Service (App Service létrehozása) párbeszédpanel](./media/web-sites-dotnet-get-started/createasplan.png)
   
    Megjelenik a **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédpanel.
   
    ![A Configure App Service (App Service konfigurálása) párbeszédpanel](./media/web-sites-dotnet-get-started/configasp.png)
   
    A következő lépésekben konfigurál egy App Service-csomagot az új erőforráscsoporthoz. Az App Service-csomag azokat a számítási erőforrásokat határozza meg, amelyeken a webalkalmazás fut. Ha például az ingyenes szintet választja, az API-alkalmazása megosztott virtuális gépeken fut, míg egyes fizetett szinteken dedikált virtuális gépeken. További információk: [App Service plans overview](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) (Az App Service-csomagok áttekintése).
5. A **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédablakban adja meg a „MyExamplePlan” nevet vagy egy másik kívánt nevet.
6. A **Location** (Hely) legördülő menüben válassza ki az Önhöz legközelebbi helyet.
   
    Ez a beállítás azt határozza meg, hogy melyik Azure-adatközpontban fog futni az alkalmazás. Ebben az oktatóanyagban nincs különbség bármelyik régiót is választja. Azonban éles alkalmazások esetén az azt elérő ügyfelekhez lehető legközelebbi kiszolgálót célszerű választani a [késés](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) minimalizálása érdekében.
7. A **Size** (Méret) legördülő menüben kattintson a **Free** (Ingyenes) elemre.
   
    Ehhez az oktatóanyaghoz az ingyenes tarifacsomag elegendő teljesítményt biztosít.
8. A **Configure App Service Plan** (App Service-csomag konfigurálása) párbeszédpanelen kattintson az **OK** gombra.
9. A **Create App Service** (App Service létrehozása) párbeszédpanelen kattintson a **Create** (Létrehozás) gombra.

## <a name="inspect-the-azure-resources-in-visual-studio"></a>Azure-erőforrások megvizsgálása a Visual Studióban
A Visual Studio rövid időn belül – általában kevesebb mint egy percen belül – létrehozza a webes projektet és a webalkalmazást.  

A **Megoldáskezelő** ablakban megtekinthetők az új projekt fájljai és mappái.

![Megoldáskezelő](./media/web-sites-dotnet-get-started/solutionexplorer.png)

Az **Azure App Service Activity** (Azure App Service-tevékenység) ablak mutatja, hogy az App Service-erőforrások létrejöttek az Azure-ban. Ha erre a hivatkozásra kattint, azonnal elkezdheti közzétenni új projektjét. Az oktatóanyag később azonban bemutatja, hogyan teheti közzé fájljait bármikor.

![A létrehozott webalkalmazás az Azure App Service Activity (Azure App Service-tevékenység) ablakban](./media/web-sites-dotnet-get-started/GS13sitecreated1.png)

A **Cloud Explorer** (Felhőkezelő) ablakban megtekintheti és kezelheti az Azure-erőforrásokat, beleértve a most létrehozott új webalkalmazást.

![A létrehozott webalkalmazás a Cloud Explorer (Felhőkezelő) ablakban](./media/web-sites-dotnet-get-started/siteinse.png)

## <a name="deploy-the-web-project-to-azure"></a>A webes projekt telepítése az Azure-ban
Ebben a szakaszban a webes projektet telepíti az Azure App Service-ben létrehozott webalkalmazás-erőforrásban.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.
   
    ![A Visual Studio menüjében válassza a Publish (Közzététel) lehetőséget](./media/web-sites-dotnet-get-started/choosepublish.png)
   
    Néhány másodpercen belül megjelenik a **Publish Web** (Webes közzététel) varázsló. A varázsló egy *közzétételi profillal* nyílik meg, amelyben beállítások szerepelnek a webes projekt telepítéséhez az új webalkalmazásban.
   
    > [!TIP] 
    > A közzétételi profil része a telepítéshez szükséges felhasználónév és jelszó.  Ezeket a hitelesítő adatokat a rendszer hozta létre, és nem kell őket megadnia. A jelszó egy rejtett, felhasználóspecifikus fájlban van titkosítva, amely a `Properties\PublishProfiles` mappában található.
    >
    >
2. A **Publish Web** (Webes közzététel) varázsló **Connection** (Kapcsolat) lapján kattintson a **Next** (Tovább) gombra.
   
    ![Kattintson a Next (Tovább) gombra a Publish Web (Webes közzététel) varázsló Connection (Kapcsolat) lapján](./media/web-sites-dotnet-get-started/GS13ValidateConnection.png)
   
    A következő a **Settings** (Beállítások) lap. Itt módosíthatja a build konfigurációját úgy, hogy hibakeresési buildet telepítsen a [távoli hibakereséshez](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). Ezen a lapon több[fájlközzétételi beállítás](https://msdn.microsoft.com/library/dd465337.aspx#Anchor_2) is elérhető.
3. A **Settings** (Beállítások) lapon kattintson a **Next** (Tovább) gombra.
   
   ![A Publish Web (Webes közzététel) varázsló Settings (Beállítások) lapja](./media/web-sites-dotnet-get-started/GS13SettingsTab.png)
   
   A következő a **Preview** (Előnézet) lap. Itt megtekintheti, melyik fájlok lesznek a projektből az API-alkalmazásba másolva. Amikor telepít egy projektet egy API-alkalmazásban, amelyen már korábban telepítést végzett, csak a módosított fájlok lesznek másolva. A másolandó elemek listájának megjelenítéséhez kattintson a **Start Preview** (Előnézet indítása) gombra.
4. A **Preview** (Előnézet) lapon kattintson a **Publish** (Közzététel) lehetőségre.
   
   ![A Publish Web (Webes közzététel) varázsló Preview (Előnézet) lapja](./media/web-sites-dotnet-get-started/GS13previewoutput.png)
   
   Amikor a **Publish** (Közzététel) gombra kattint, a Visual Studio megkezdi a fájlok másolását az Azure-kiszolgálóra. Ez egy-két percet vesz igénybe.
   
   Az **Output** (Kimenet) és az **Azure App Service Activity** (Azure App Service-tevékenység) ablakban megtekinthető, milyen telepítési műveletek lettek elvégezve, és itt jelenik meg a sikeres telepítésről szóló jelentés is.
   
   ![A Visual Studio Output (Kimenet) ablaka a sikeres telepítést jelzi](./media/web-sites-dotnet-get-started/PublishOutput.png)
   
   A sikeres telepítés után az alapértelmezett böngésző automatikusan megnyitja a telepített webalkalmazás URL-címét, és a létrehozott alkalmazás innentől kezdve fut a felhőben. A böngésző címsorában megjelenő URL-cím mutatja, hogy a webalkalmazás be lett töltve az internetről.
   
   ![Az Azure-ban futó webalkalmazás](./media/web-sites-dotnet-get-started/GS13deployedsite.png)
   
   > [!TIP]
   > A gyors telepítéshez engedélyezheti a **Web One Click Publish** (Egykattintásos webes közzététel) eszköztárat. Kattintson a **View (Nézet) > Toolbars (Eszköztárak)** elemre, majd válassza a **Web One Click Publish** (Egykattintásos webes közzététel) lehetőséget. Az eszköztárban kiválaszthat egy profilt, és egy gombra kattintva elvégezheti a közzétételt, vagy egy másikra kattintva megnyithatja a **Publish Web** (Webes közzététel) varázslót.
   > ![A Web One Click Publish (Egykattintásos webes közzététel) eszköztár](./media/web-sites-dotnet-get-started/weboneclickpublish.png)
   > 
   > 

## <a name="troubleshooting"></a>Hibaelhárítás
Ha problémába ütközik az oktatóanyag lépéseinek követése közben, győződjön meg arról, hogy az Azure SDK for .NET legújabb verzióját használja. Ennek legegyszerűbb módja [az Azure SDK letöltése a Visual Studio 2015-höz](http://go.microsoft.com/fwlink/?linkid=518003). Ha a legfrissebb verzió van telepítve, a Webplatform-telepítő értesíti Önt arról, hogy nincs szükség a telepítésre.

Most, hogy sikeresen telepítette ASP.NET webalkalmazását az Azure App Service-ben, további információkat tudhat meg a Visual Studio hibaelhárítást megkönnyítő funkcióiról. A naplózással, a távoli hibakereséssel és egyebekkel kapcsolatos információkért lásd: [Troubleshooting Azure web apps in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md) (Azure-webalkalmazások hibaelhárítása a Visual Studióban).

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudhatta, hogyan lehet létrehozni egy egyszerű webalkalmazást és telepíteni azt egy Azure-webalkalmazásban. Íme, néhány kapcsolódó témakör és forrás, amely az Azure App Service-szel kapcsolatos további tudnivalókat biztosít:

* Megfigyelheti és kezelheti webalkalmazását az [Azure portálon](https://portal.azure.com/). 
  
    További információk: [Az Azure portál áttekintése](/services/management-portal/) és [Configure web apps in Azure App Service](web-sites-configure.md) (Webalkalmazások konfigurálása az Azure App Service szolgáltatásban).
* Meglévő webes projektet telepíthet egy új webalkalmazásba a Visual Studio használatával
  
    Kattintson a jobb gombbal a projektre a **Megoldáskezelőben**, és kattintson a **Publish** (Közzétesz) lehetőségre. Közzétételi célként válassza a **Microsoft Azure App Service** lehetőséget, majd kattintson a **New** (Új) gombra. Az ekkor megjelenő párbeszédpanelek megegyeznek az ebben az oktatóanyagban láthatókkal.
* Webes projektet telepíthet a forráskezelőből
  
    Tudnivalók a [telepítés automatizálásáról](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) [forráskezelő rendszerből](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control): [Ismerkedés a webalkalmazásokkal az Azure App Service-ben](app-service-web-get-started.md) és [Az Azure-webalkalmazások telepítése](web-sites-deploy.md).
* ASP.NET webes API-t telepíthet egy API-alkalmazásba az Azure App Service szolgáltatásban
  
    Most láthatta, hogyan hozható létre az Azure App Service egy példánya, amely főként egy webhely üzemeltetéséhez használható. Az App Service emellett a webes API-k üzemeltetéséhez szükséges funkciókat is biztosít. Ilyen például a CORS-támogatás és az API-metaadatok támogatása az ügyfélkódok létrehozásához. A webalkalmazásokban használhat API-funkciókat, de ha főként API-t szeretne üzemeltetni egy App Service-példányban, az **API-alkalmazás** jobb választás. További információkért lásd: [Get started with API Apps and ASP.NET in Azure App Service](../app-service-api/app-service-api-dotnet-get-started.md) (API-alkalmazások és ASP.NET az Azure App Service szolgáltatásban – Első lépések). 
* Egyéni tartománynév és SSL hozzáadása
  
    Az SSL és a saját tartomány (például www.contoso.com a contoso.azurewebsites.net helyett) használatával kapcsolatban lásd a következő forrásokat:
  
  * [Egyéni tartománynév konfigurálása az Azure App Service-ben](web-sites-custom-domain-name.md)
  * [HTTPS engedélyezése az Azure-webhelyeken)](web-sites-configure-ssl-certificate.md)
* Ha már nem használja őket, törölje a webalkalmazást tartalmazó erőforráscsoportot és a kapcsolódó Azure-erőforrásokat.
  
    További információ az erőforráscsoportoknak az Azure Portalon való használatával kapcsolatban: [Deploy resources with Resource Manager templates and Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md) (Erőforrások üzembe helyezése Resource Manager-sablonok és az Azure Portal segítségével).   
* További példák egy ASP.NET-webalkalmazás létrehozására az App Service szolgáltatásban: [Create and deploy an ASP.NET web app in Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) (ASP.NET-webalkalmazás létrehozása és üzembe helyezése az Azure App Service szolgáltatásban) és [Create and deploy a mobile app in Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-a-mobile-app-in-Azure-App-Service) (Mobilalkalmazás létrehozása és üzembe helyezése az Azure App Service szolgáltatásban) a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [bemutatóból](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). A HealthClinic.biz bemutató további gyors útmutatóit lásd: [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Azure fejlesztői eszközök – gyors útmutatók).


