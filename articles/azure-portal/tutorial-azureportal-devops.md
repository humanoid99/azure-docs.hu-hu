---
title: "Oktatóanyag: A DevOps és az Azure Portal | Microsoft Docs"
description: "Ismerje meg az Azure Portalon elérhető különböző DevOps-munkafolyamatokat."
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: eec7d1402bdea4e5433c473dd713eed23aa80464
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-devops-with-the-azure-portal"></a>Oktatóanyag: A DevOps és az Azure Portal
Az Azure platform számos rugalmas DevOps-munkafolyamatot tartalmaz. Az oktatóanyagból megtudhatja, hogyan használhatja az Azure Portal funkcióit alkalmazások fejlesztéséhez, teszteléséhez, üzembe helyezéséhez, hibaelhárításához, figyeléséhez és felügyeletéhez. Ez az oktatóanyag a következő témákkal foglalkozik:

1. Webalkalmazás létrehozása és a folyamatos üzembe helyezés engedélyezése
2. Alkalmazás fejlesztése és tesztelése
3. Alkalmazás figyelése és hibaelhárítása
4. Általános alkalmazásfelügyeleti feladatok

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Webalkalmazás létrehozása és a folyamatos üzembe helyezés engedélyezése
Hozzon létre egy webalkalmazást az [Azure App Service](https://azure.microsoft.com/services/app-service/) szolgáltatással. Az oktatóanyag további részeiben ezt az alkalmazást fogjuk használni. Először engedélyeznie kell a folyamatos üzembe helyezést a forráskód tárházából a futó Azure-környezetbe.

1. Jelentkezzen be az Azure Portalra.
2. Válassza az **App Services** &gt; **Hozzáadás ikon** lehetőséget, adjon meg egy nevet, válassza ki az előfizetését, és hozzon létre egy új erőforráscsoportot, amely a szolgáltatás tárolója lesz.
   
   Az erőforráscsoportok segítségével egyetlen csoportként kezelheti az [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) használatával a megoldás különböző részeit, például a számlázást, az üzemelő példányokat és a figyelést.
   
   ![image1][image1]
3. Pár pillanat múlva létrejön az alkalmazásszolgáltatás. Szánjon néhány percet arra, hogy a portálon áttekintse a szolgáltatáshoz elérhető különböző menüpontokat.
   
   ![image2][image2]    
4. Kattintson az URL-címre. Tekintse át az eszközökhöz és a tárházakhoz elérhető különböző lehetőségeket. Kiválaszthatja a használni kívánt nyelveket és keretrendszereket is, ilyen például a .NET, a Java és a Ruby.
   
   ![image3][image3]    
5. Az Azure Portal a folyamatos üzembe helyezést egyszerű, néhány rövid lépésből álló folyamattá teszi. Az Azure Portalon válassza ki a kívánt beállításokat az imént létrehozott alkalmazásszolgáltatáshoz tartozó ikonra kattintva.
   
   ![image4][image4]
   
   A jobb oldalon megnyíló panelen görgessen a közzétételi szakaszhoz.
   
   ![image5][image5]
6. Ezután adja meg a szükséges beállításokat, hogy engedélyezze az alkalmazás folyamatos üzembe helyezését. Kattintson a Központi telepítés forrása elemre, és kattintson a Forrás kiválasztása elemre. Tekintse át a különböző lehetőségeket a tárházforrásoknál.
   
   ![image6][image6]
7. Ebben a példában válassza a Githubot. Ha kívánja, választhat más tárházat is. Ekkor adja meg a szükséges hitelesítő adatokat.
   
   ![image7][image7]
8. Miután hitelesítette magát a tárházban, kiválaszthatja az üzembe helyezendő projektet és ágat. Alább több fiktív mintapéldát láthat.
   
   ![image8][image8]
9. A projekt és az ág kiválasztása után kattintson az OK gombra. Meg kell jelenniük az üzembe helyezésre vonatkozó értesítéseknek.
   
   ![image9][image9]
10. Lépjen vissza a GitHubra, és tekintse meg a webhookot, amely azért lett létrehozva, hogy integrálja a forrásvezérlő tárházat az Azure-ral. Az Azure Portal segítségével a GitHubbal való integráció néhány lépésben elvégezhető.
    
    ![image10][image10]
11. A folyamatos üzembe helyezés szemléltetéséhez gyorsan vegyen fel valamilyen tartalmat a tárházba. Például vegyen fel egy egyszerű minta szövegfájlt a GitHub-tárházba. Tetszőlegesen használhat .NET, Ruby, Python vagy más típusú alkalmazást az App Service szolgáltatással. Felvehet szövegfájlt, ASP.NET MVC, Java vagy Ruby típusú alkalmazást a kiválasztott tárházba.
    
    ![image11][image11]
12. Miután véglegesítette a tárház módosításait, a portál értesítési területén megjelenik egy új üzemelő példány jelzése. Ha a tárház változtatásainak véglegesítése után nem jelennek meg hamar a változtatások, kattintson a Szinkronizálás gombra.
    
    ![image12][image12]
13. Ha ezen a ponton megpróbálja betölteni az alkalmazásszolgáltatás oldalát, 403-as hibát kaphat. Ebben a példában ennek az az oka, hogy az oldalhoz nincs beállítva egy szokásos alapértelmezett oldal, például egy olyan fájl, mint az index.htm vagy a default.html. Ezt a helyzetet gyorsan megoldhatja az Azure Portalon elérhető eszközökkel.  Az Azure Portalon kattintson a Beállítások &gt; Alkalmazásbeállítások elemre.
    
     ![image13][image13]
14. Megjelenik egy panel az alkalmazásbeállításokkal. Írja be az oldal „SamplePage.html” nevét, és kattintson a Mentés gombra. Szánjon még pár percet a többi beállítás áttekintésére.
    
    ![image14][image14]
15. Ha meg szeretne bizonyosodni arról, hogy megjelennek a várt módosítások, frissítheti a böngésző URL-címét. Ebben a példában ekkor egyszerű szöveggel lesz feltöltve az oldal. A tárház minden további módosítása egy új automatikus üzembe helyezést eredményezne.
    
    ![image15][image15]
    
    A folyamatos üzembe helyezés engedélyezése az Azure Portallal nem túl bonyolult feladat. Létrehozhat ennél bonyolultabb kiadási folyamatokat, és sok más módszert is használhat a meglévő forrásvezérlő és folyamatos integrációjú rendszerekkel az Azure-ba való üzembe helyezéshez, például használhat automatikus rendszereket a létrehozás és a kiadás felügyeletéhez.

## <a name="develop-and-test-an-app"></a>Alkalmazás fejlesztése és tesztelése
Ezután hajtson végre néhány módosítást az alapkódban, és gyorsan helyezze üzembe ezeket a módosításokat. Ezenkívül néhány teljesítménytesztet is be fog állítani a webalkalmazáshoz.

1. Az Azure Portalon válassza az App Services elemet a navigációs ablaktáblában, és keresse meg az alkalmazásszolgáltatását.
   
   ![image16][image16]
2. Kattintson az Eszközök elemre.
   
   ![image17][image17]
3. Figyelje meg az eszközöknél megjelenő Fejlesztés kategóriát. Itt több hasznos eszközt talál, amelyekkel az Azure Portal elhagyása nélkül dolgozhat az alkalmazással. Kattintson a Konzol elemre.
   
   ![image18][image18]
4. A konzolablakban élő parancsokat adhat meg a alkalmazáshoz. Írja be a dir parancsot, és nyomja le az Enter billentyűt. Megjegyzendő, hogy az emelt szintű jogosultságokat igénylő parancsok nem működnek.
   
   ![image19][image19]
5. Lépjen vissza a Fejlesztés kategóriához, és válassza a Visual Studio Online lehetőséget. Megjegyzés: A Visual Studio Online új neve Visual Studio Team Services.
   
   ![image20][image20]
6. Váltson át a böngészőbeli szerkesztésre az alkalmazásnál.
   
   ![image21][image21]
7. Egy webes bővítmény települ az alkalmazáshoz. A bővítményekkel gyorsan és könnyen egészítheti ki további funkciókkal az alkalmazásokat az Azure-ban. Tekintse át az elérhető egyéb bővítménytípusok példáit az alábbi képernyőfelvételen.
   
   ![image22][image22]
8. A Visual Studio Online bővítmény telepítésének befejeződése után kattintson az Indítás gombra.
   
   ![image23][image23]
9. Megnyílik egy böngészőlap, ahol egy fejlesztői IDE környezet jelenik meg magában a böngészőben. Az alábbi példában a Chrome böngésző szerepel.
   
   ![image24][image24]
10. Más műveleteket is végrehajthat, például fájlokat szerkeszthet, fájlokat és mappákat vehet fel, és tartalmat tölthet le az élő webhelyről. Végezzen egy gyors szerkesztést a SamplePage.html fájlban.
    
    ![image25][image25]
11. Néhány pillanat múlva a módosítások automatikusan érvénybe lépnek. Ha visszalép az oldalra, megtekintheti a módosításokat. Ne feledje, hogy az ehhez hasonló élő szerkesztések minden valószínűség szerint nem alkalmazhatók az éles környezetben. Az eszközökkel azonban nagyon könnyen végrehajthatja a fejlesztési és tesztelési környezetek gyors módosításait.
    
    ![image26][image26]
    
    ![image27][image27]
12. Lépjen vissza az eszközök paneljére, és a Fejlesztés kategóriában kattintson a Teljesítményteszt elemre.
    
    ![image28][image28]
13. Be kell állítania egy Team Services-fiókot. További információt itt talál: [Team Services-fiók létrehozása](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services).
14. Az Új gombra kattintva hozzon létre egy új teljesítménytesztet.
    
    ![image29][image29]
    
    Konfigurálja a különböző értékeket, és kattintson a párbeszéd alján található Teszt futtatása gombra a teljesítményteszt elindításához.
    
    ![image30][image30]
    
    ![image31][image31]
15. A teszt futása közben figyelheti az állapotot.
    
    ![image32][image32]
    
    A teszt befejeződése után kattintson az eredményekre a további részletek megjelenítéséhez.
    
    ![image33][image33]
16. Ebben a példában létrehozott egy kisebb tesztfuttatást, korlátozott mennyiségű elemezhető adattal, de így is megtekintheti a különböző mérőszámokat, és újból lefuttathatja a tesztet ebben a nézetben. Az Azure Portal egyszerűvé teszi a webes teljesítménytesztek létrehozását, végrehajtását és elemzését. Az alábbi képernyőfelvételeken a teljesítményadatok láthatók.
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Alkalmazás figyelése és hibaelhárítása
Az Azure számos funkciót kínál a futó alkalmazások figyeléséhez és hibaelhárításához.

1. A webalkalmazásunkhoz tartozó Azure Portalon válassza az Eszközök lehetőséget.
   
   ![image37][image37]
2. A Hibaelhárítás kategóriában tekintse át a különböző eszközöket, amelyeket a futó alkalmazások esetleges problémáinak megoldására használhat. Lehetőség van például az élő HTTP-forgalom figyelésére, az önjavítás engedélyezésére, a naplók megtekintésére stb.
   
   ![image38][image38]
3. Válassza a Webhelymetrikák lehetőséget bizonyos HTTP-kódrészletek gyors megjelenítéséhez.
   
   ![image39][image39]
4. Válassza a Diagnosztika lehetőséget szolgáltatásként. Válassza ki az alkalmazás típusát, majd kattintson a Futtatás gombra.
   
   ![image40][image40]
   
   Elkezdődik az adatgyűjtés.
   
   ![image41][image41]
5. Kiválaszthatja a megfelelő naplót a potenciális problémák diagnosztizálásához. Engedélyeznie kell a naplózást, ha látni szeretné az összes rendelkezésre álló adatot, például a HTTP-naplókat.
   
   ![image42][image42]
   
   Az összeomlási memóriakép fájljára kattintva letöltheti és elemezheti a hibakeresés diagnosztikai elemzési naplóját, amelyben megkeresheti a lehetséges problémákat.
   
   ![image43][image43]
6. Ha még több adatot szeretne megtekinteni, engedélyeznie kell a további naplózási lehetőségeket. Az Azure Portalon lépjen a webalkalmazáshoz, és válassza a Beállítások lehetőséget.
   
   ![image44][image44]
7. Görgessen lefelé a Szolgáltatások kategóriához, és válassza a Diagnosztikai naplók elemet.
   
      ![image45][image45]
8. Tekintse át a különböző naplózási lehetőségeket. Kapcsolja be a webkiszolgálók naplózását, és kattintson a Mentés gombra.
   
   ![image46][image46]
9. Lépjen vissza az alkalmazás eszközeinek területére, és válassza a Diagnosztika lehetőséget szolgáltatásként, majd kattintson a Futtatás gombra az adatgyűjtés újbóli elindításához.
   
   ![image47][image47]
10. Ha engedélyezve van a HTTP-naplózás, láthatja a HTTP-naplókhoz gyűjtött adatokat.
    
    ![image48][image48]
11. Ha rákattint a HTML-naplófájlra, számos további böngészőalapú jelentés jelenik meg a további elemzéshez.
    
    ![image49][image49]
12. Lépjen vissza az eszközök szakaszára az alkalmazás Azure Portalján. Görgessen az Eszközök szakaszra, és válassza ki a Process Explorert.
    
    ![image50][image50]
13. A Process Explorer kiválasztásával megtekintheti a futó folyamatok adatait. Figyelje meg, hogy a folyamatokat kibonthatja, és még le is állíthatja az Azure Portalról.
    
    ![image51][image51]
    
    ![image52][image52]
14. Lépjen vissza a bal oldalon található Beállítások panelre. Kattintson az Új támogatási kérelem elemre.
    
    ![image53][image53]
15. A jobb oldali panelen beírhatja a problémával kapcsolatos adatokat, megadhatja a kapcsolattartási adatokat, és akár fel is töltheti a diagnosztikai adatokat. Az Azure Portal lehetővé teszi, hogy zökkenőmentesen együttműködjön a Microsoft támogatási szolgálatával.
    
    ![image54][image54]
    
    ![image55][image55]
    
    Az Azure Portal hatékony és megszokott eszközöket biztosít a futó alkalmazások figyeléséhez és hibaelhárításához. Emellett gyorsan végrehajthatja az olyan műveleteket, mint az újrahasznosítási folyamatok, a különböző adatgyűjtések engedélyezése és letiltása, és akár az integráció a Microsoft szakértő támogatásával.

## <a name="general-application-management"></a>Általános alkalmazásfelügyelet
Amikor alkalmazásokat felügyel, gyakran kell végrehajtania olyan tevékenységeket, mint például a biztonsági mentési stratégiák konfigurálása, az identitásszolgáltatók megvalósítása és kezelése, valamint a szerepköralapú hozzáférés-vezérlés konfigurálása. A DevOps más funkcióihoz hasonlóan az Azure platform integráltan, közvetlenül a portálon teszi elérhetővé ezeket a feladatokat.

1. A webalkalmazás adatvesztés elleni védelméhez biztonsági mentéseket kell konfigurálnia. Lépjen a webalkalmazás Beállítások területére.
   
   ![image56][image56]
2. A jobb oldali panelen görgessen le a Szolgáltatások kategóriához.
   
    ![image57][image57]
3. Válassza ki a Biztonsági másolatok lehetőséget. A jobb oldalon megnyílik egy panel.
   
   ![image58][image58]
4. Kattintson a Konfigurálás gombra, és válasszon egy tárfiókot a jobb oldali panelen.
   
   ![image59][image59]
5. Most hozzon létre egy tárolót a biztonsági mentések tárolásához. Kattintson a panel alján található Létrehozás gombra. Ezután válassza ki a tárolót.
   
   ![image60][image60]
6. Miután kiválasztotta a tárolót, beállíthatja az ütemezéseket, valamint az adatbázisok biztonsági mentését. Ebben a példában kattintson a Mentés ikonra.
   
    ![image61][image61]
7. A mentés után görgessen vissza a bal oldali panelre, a Biztonsági másolatok lehetőséghez. Kattintson a Biztonsági mentés gombra az alkalmazás biztonsági mentéséhez.
   
    ![image62][image62]
8. Rövidesen múlva megjelenik a létrehozott biztonsági másolat. A képernyőfelvételen láthatja a Visszaállítás lehetőséget.
   
    ![image63][image63]
9. Kattintson a Visszaállítás gombra, és tekintse át a jobb oldali panelen található beállításokat. Kiválaszthatja a biztonsági másolatot, és szükség esetén könnyen visszaállíthat egy korábbi állapotot. Az Azure Portal segítségével könnyen létrehoztuk az alkalmazás egyszerű vészhelyreállítási stratégiáját.
   
    ![image64][image64]
10. Lépjen vissza a bal oldali Beállítások panelre, és a Szolgáltatások elemnél válassza ki a Hitelesítés/engedélyezés lehetőséget.
    
     ![image65][image65]
11. A jobb oldali panelen válassza ki az App Service-hitelesítés lehetőséget. Tekintse át a különböző beállításokat, amelyeket megadhat a népszerű szolgáltatókhoz.
    
     ![image66][image66]
12. Válassza ki a kívánt szolgáltatót, és tekintse át a hatókör beállításait. Adja meg az alkalmazás azonosítója és az alkalmazás titkos kulcsát, és engedélyezze az alkalmazás Facebook-hitelesítését. Az Azure Portal a hitelesítést kulcsrakész megoldásként biztosítja az alkalmazásokhoz.
    
     ![image67][image67]
13. Lépjen vissza a Beállítások panelre, és válassza ki a Felhasználók elemet az Erőforrás-kezelés kategóriában.
    
     ![image68][image68]
14. A jobb oldali panelen tekintse át a szerepkörök és a felhasználók hozzáadásának különböző lehetőségeit. Az Azure Portal segítségével egyszerűen meghatározhatja az alkalmazás szerepkörön alapuló hozzáférés-vezérlését.
    
     ![image69][image69]

## <a name="summary"></a>Összefoglalás
Ez az oktatóanyag bemutatta az Azure platform néhány hatékony funkcióját: a webalkalmazások folyamatos üzembe helyezésének gyors engedélyezését, a különböző fejlesztési és tesztelési tevékenységek végrehajtását, az élő alkalmazások figyelését és hibaelhárítását, és végül a fontos stratégiák, például a vészhelyreállítás, az identitás és a szerepköralapú hozzáférés-vezérlés kezelését. Az Azure platform lehetővé teszi a felsorolt DevOps-munkafolyamatok integrált használatát, így hatékonyan dolgozhat az adott feladatnak megfelelő környezetben.

## <a name="next-steps"></a>Következő lépések
* Az Azure Resource Manager fontos szerepet tölt be a fejlesztés és az üzemeltetés lehetővé tételében az Azure platformon.  További tudnivalók: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).
* Az Azure App Service üzembe helyezésének ismertetését lásd: [Deploy your app to Azure App Service](../app-service-web/web-sites-deploy.md) (Alkalmazás telepítése az Azure App Service szolgáltatásban).

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png

