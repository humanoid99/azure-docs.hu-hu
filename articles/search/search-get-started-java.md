---
title: "Bevezetés az Azure Search használatába Java nyelven | Microsoft Docs"
description: "Üzemeltetett felhőalapú keresőalkalmazás felépítése az Azure rendszerben a Java programozási nyelv használatával."
services: search
documentationcenter: 
author: EvanBoyle
manager: pablocas
editor: v-lincan
ms.assetid: 8b4df3c9-3ae5-4e3a-b4bb-74b516a91c8e
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.date: 07/14/2016
ms.author: evboyle
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f6ca06a0349def97b38a1bf6d0d8f36236077e92
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-search-in-java"></a>Bevezetés az Azure Search használatába Java nyelven
> [!div class="op_single_selector"]
> * [Portál](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Ismerje meg, hogyan hozhat létre olyan egyéni Java keresőalkalmazást, amely az Azure Search szolgáltatást használja a keresésekhez. Ez az oktatóanyag az [Azure Search szolgáltatás REST API](https://msdn.microsoft.com/library/dn798935.aspx)-ját használja ebben a gyakorlatban az objektumok és műveletek összeállításához.

A minta futtatásához rendelkeznie kell egy Azure Search szolgáltatással, amelyre az [Azure portálon](https://portal.azure.com) regisztrálhat. A részletes utasításokat lásd: [Azure Search szolgáltatás létrehozása a portálon](search-create-service-portal.md).

A minta összeállításához és teszteléséhez a következő szoftvereket használtuk:

* [Eclipse IDE for Java EE Developers](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Ügyeljen arra, hogy az EE-verziót töltse le. Az ellenőrzési lépések egyikének olyan funkcióra van szüksége, amely csak ebben a kiadásban található.
* [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Tudnivalók az adatokról
A mintaalkalmazás az [Amerikai Egyesült Államok geológiai szolgáltatásainak (United States Geological Services, USGS)](http://geonames.usgs.gov/domestic/download_data.htm) adatait használja, az adatkészlet méretének csökkentése érdekében Rhode Island államra szűrve. Ezeket az adatokat fogjuk használni egy olyan keresőalkalmazás létrehozásához, amely jellegzetes épületeket, például kórházakat és iskolákat, valamint geológiai jellegzetességeket, például folyókat, tavakat és hegycsúcsokat ad vissza eredményül.

Ebben az alkalmazásban a **SearchServlet.java** program egy [indexelő](https://msdn.microsoft.com/library/azure/dn798918.aspx) szerkezet segítségével létrehozza és betölti az indexet, amelyhez egy nyilvános Azure SQL-adatbázisból kéri le a szűrt USGS-adatkészletet. Az előre meghatározott hitelesítő adatokat és az online adatforrás kapcsolódási adatait a programkód tartalmazza. Az adatelérés szempontjából nincs szükség további konfigurációra.

> [!NOTE]
> Az adatkészlethez olyan szűrőt alkalmaztunk, hogy az ingyenes tarifacsomag 10 000 dokumentumos korlátja alatt maradjunk. Ha a standard csomagot használja, arra nem vonatkozik ez a korlátozás, és módosíthatja úgy a kódot, hogy nagyobb adatkészletet használhasson. Az egyes tarifacsomagok kapacitásával kapcsolatos részletes információkat lásd: [Korlátozások és megkötések](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Tudnivalók a programfájlokról
Az alábbi lista a példához kapcsolódó fájlokat ismerteti.

* Search.jsp: a felhasználói felületet biztosítja
* SearchServlet.java: a módszereket biztosítja (az MVC-vezérlőhöz hasonló)
* SearchServiceClient.java: a HTTP-kérelmeket kezeli
* SearchServiceHelper.java: egy statikus módszereket biztosító segítőosztály
* Document.Java: az adatmodellt biztosítja
* Config.Properties: beállítja a Search szolgáltatás URL-címét és API-kulcsát
* Pom.XML: Maven-függőség

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Az Azure Search szolgáltatás szolgáltatásnevének és API-kulcsának megkeresése
Az Azure Search szolgáltatásba történő minden REST API-hívás esetében meg kell adnia a szolgáltatás URL-címét és API-kulcsát. 

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Az ugrás sávon kattintson a **Keresési szolgáltatás** elemre, hogy megjelenjen az előfizetéséhez kapcsolódó összes Azure Search szolgáltatás.
3. Válassza ki a használni kívánt szolgáltatást.
4. A szolgáltatás irányítópultján megjelennek az alapvető információkat tartalmazó csempék, valamint az adminisztrációs kulcsok eléréséhez szükséges kulcs ikon.
   
      ![][3]
5. Másolja át a szolgáltatás URL-címét és egy adminisztrációs kulcsot. Később lesz rájuk szüksége, amikor hozzáadja őket a **config.properties** fájlhoz.

## <a name="download-the-sample-files"></a>A mintafájlok letöltése
1. Lépjen a GitHubon található [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) elemre.
2. Kattintson a **Download ZIP** (ZIP-fájl letöltése) elemre, mentse a .zip-fájlt a lemezre, és bontsa ki a benne található összes fájlt. A Java-munkaterületére csomagolja ki a fájlokat, hogy később könnyebben megtalálja a projektet.
3. A mintafájlok csak olvashatók. Kattintson a jobb gombbal a mappa tulajdonságaira, és törölje a csak olvasható attribútumot.

Minden további fájlmódosítás és utasításfuttatás az ebben a mappában lévő fájlokra vonatkozóan fog történni.  

## <a name="import-project"></a>Projekt importálása
1. Az Eclipse-ben válassza ki a **File** (Fájl)  > **Import** (Importálás)  > **General** (Általános)  > **Existing Projects into Workspace** (Meglévő projekteket a munkaterületre) lehetőséget.
   
    ![][4]
2. A **Select root directory** (Gyökérkönyvtár kiválasztása) ablakban keresse meg a mintafájlokat tartalmazó mappát. Válassza ki a .project mappát tartalmazó mappát. A projektnek kiválasztott elemként meg kell jelennie a **Projects** (Projektek) listán.
   
    ![][12]
3. Kattintson a **Finish** (Befejezés) gombra.
4. A **Project Explorer** (Projektböngésző) segítségével megtekintheti és szerkesztheti a fájlokat. Ha az még nincs megnyitva, kattintson a **Window** (Ablak)  > **Show View** (Nézet megjelenítése)  > **Project Explorer** (Projektböngésző) lehetőségre, vagy nyissa meg a megfelelő parancsikonnal.

## <a name="configure-the-service-url-and-api-key"></a>A szolgáltatás URL-címének és API-kulcsának konfigurálása
1. A **Project Explorer** (Projektböngésző) nézetben kattintson duplán a **config.properties** elemre, hogy szerkeszthesse a kiszolgáló nevét és az API-kulcsot tartalmazó konfigurációs beállításokat.
2. Tekintse meg a jelen cikkben korábban ismertetett lépéseket, ahol a szolgáltatás URL-címét és API-kulcsát megtalálhatta az [Azure portálon](https://portal.azure.com), és az adott értékeket írja be **config.properties** fájlba.
3. A **config.properties** fájlban található „API-kulcsot” cserélje ki a szolgáltatásához tartozó API-kulcsra. A következő lépésben az ugyanabban a fájlban található „szolgáltatásnevet” cserélje ki a saját szolgáltatása nevére (az URL-cím első összetevője: http://servicename.search.windows.net).
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>A projekt, a build és a futtatókörnyezetek konfigurálása
1. Az Eclipse Project Explorer (Projektböngésző) nézetében kattintson a jobb gombbal a Project (Projekt) > **Properties** (Tulajdonságok)  > **Project Facets** (A projekt aspektusai) elemre.
2. Válassza ki a **Dynamic Web Module** (Dinamikus webmodul), a **Java** és a **JavaScript** elemet.
   
    ![][6]
3. Kattintson az **Apply** (Alkalmaz) gombra.
4. Válassza ki a **Window** (Ablak)  > **Preferences** (Beállítások)  > **Server** (Kiszolgáló)  > **Runtime Environments** (Futtatókörnyezetek)  > **Add..** (Hozzáadás) lehetőséget.
5. Bontsa ki az Apache elemet, és válassza ki az Apache Tomcat-kiszolgáló korábban telepített verzióját. Mi a 8-as verziót telepítettük a rendszerünkre.
   
    ![][7]
6. A következő oldalon adja meg a Tomcat telepítési könyvtárát. Windows rendszerű számítógépeken ez valószínűleg a következő lesz: C:\Program Files\Apache Software Foundation\Tomcat *verzió*.
7. Kattintson a **Finish** (Befejezés) gombra.
8. Válassza ki a **Window** (Ablak)  > **Preferences** (Beállítások)  > **Java** > **Installed JREs** (Telepített JRE-k)  > **Add** (Hozzáadás) lehetőséget.
9. Az **Add JRE** (JRE hozzáadása) panelen válassza ki a **Standard VM** elemet.
10. Kattintson a **Next** (Tovább) gombra.
11. A JRE Definition (JRE_definíció) ablakban, a JRE kezdőlapján kattintson a **Directory** (Könyvtár) elemre.
12. Navigáljon a **Program Files** (Programfájlok)  > **Java** könyvtárra, és válassza ki a korábban telepített JDK-t. Fontos, hogy a JDK-t JRE-ként válassza ki.
13. Az Installed JREs (Telepített JRE-k) panelen válassza ki a **JDK** elemet. A beállításainak az alábbi képernyőfelvételhez hasonlóan kell kinéznie.
    
    ![][9]
14. Ha az alkalmazást egy külső böngészőablakban szeretné megnyitni, válassza ki a **Window** (Ablak)  > **Web Browser** (Webböngésző)  > **Internet Explorer** lehetőséget. Külső böngészővel fokozhatja a webalkalmazás használatának élményét.
    
    ![][8]

Ezzel befejezte a konfigurálási feladatokat. A következő lépésben felépíti és futtatja a projektet.

## <a name="build-the-project"></a>A projekt felépítése
1. A projekt konfigurálásához a Project Explorer (Projektböngésző) nézetben kattintson a jobb gombbal a projekt nevére, és válassza ki a **Run As** (Futtatás másként)  > **Maven build...** elemet.
   
    ![][10]
2. Az Edit Configuration (Konfiguráció szerkesztése) panelen a Goals (Célok) mezőbe írja be a „clean install” („tiszta telepítés”) kifejezést, majd kattintson a **Run** (Futtatás) gombra.

Az állapotüzenetek kimenetként a konzolablakban jelennek meg. A BUILD SUCCESS (Sikeres felépítés) üzenetnek kell megjelennie, amely azt jelzi, hogy a projekt hibák nélkül felépült.

## <a name="run-the-app"></a>Az alkalmazás futtatása
Utolsó lépésként futtassa le az alkalmazást egy helyi kiszolgáló futtatókörnyezetében.

Ha az Eclipse keretrendszerben még nem határozta meg egy kiszolgáló futtatókörnyezetét, először azt kell elvégeznie.

1. A Project Explorer (Projektböngésző) nézetben bontsa ki a **WebContent** elemet.
2. Kattintson a jobb gombbal a **Search.jsp** fájlra, majd kattintson a  > **Run As** (Futtatás másként)  > **Run on Server** (Futtatás a kiszolgálón) elemre. Válassza ki az Apache Tomcat kiszolgálót, majd kattintson a **Run** (Futtatás) gombra.

> [!TIP]
> Ha nem alapértelmezett munkaterületen tárolja a projektet, a kiszolgálóindítási hiba elkerülése érdekében úgy kell módosítania a **Run Configuration** (Konfiguráció futtatása) beállítást, hogy a projekt helyére mutasson. A Project Explorer (Projektböngésző) nézetben kattintson a jobb gombbal a **Search.jsp** fájlra, majd kattintson a  > **Run As** (Futtatás másként)  > **Run Configurations** (Konfigurációk futtatása) elemre. Válassza ki az Apache Tomcat kiszolgálót. Kattintson az **Arguments** (Argumentumok) elemre. A projektet tartalmazó mappa beállításához kattintson a **Workspace** (Munkaterület) vagy a **File System** (Fájlrendszer) elemre.
> 
> 

Az alkalmazás futtatásakor egy keresőmezőt tartalmazó böngészőablaknak kell megjelennie, ahová beírhatja a kifejezéseket.

Várjon körülbelül egy percet, mielőtt a **Search** (Keresés) gombra kattintana, hogy a szolgáltatásnak legyen elég ideje az index létrehozására és betöltésére. Ha a HTTP 404 hibaüzenet jelenik meg, csak egy kicsit tovább kell várnia az újrapróbálkozás előtt.

## <a name="search-on-usgs-data"></a>USGS-adatok keresése
Az USGS-adatkészlet a Rhode Island államra vonatkozó rekordokat tartalmaz. Ha rákattint egy üres keresőmező **Search** (Keresés) gombjára, megjelenik az 50 legfontosabb bejegyzés; ez az alapértelmezett viselkedés.

A keresett kifejezés beírása elindítja a keresőmotort. Próbáljon meg a helyhez kötődő nevet beírni. „Roger Williams” volt Rhode Island első kormányzója. Számos parkot, épületet és iskolát neveztek el róla.

![][11]

Megpróbálhatja beírni az alábbi kifejezések bármelyikét is:

* Pawtucket
* Pembroke
* goose+cape

## <a name="next-steps"></a>Következő lépések
Ez az Azure Search első oktatóanyaga, amely Java és USGS-adatkészlet alapján készült. Idővel majd tovább bővítjük oktatóanyagunkat, és olyan kiegészítő keresési funkciókat fogunk bemutatni, amelyeket esetleg szívesen használna egyéni megoldásaiban.

Ha már rendelkezik bizonyos tapasztalattal az Azure Search használatában, ezt a mintát akár ugródeszkaként is használhatja a további kísérletezéshez, például bővítheti a [keresőoldalt](search-pagination-page-layout.md) vagy [jellemzőalapú navigációt](search-faceted-navigation.md) valósíthat meg. A keresési eredmények oldalát is tovább fejlesztheti számok és kötegelt dokumentumok hozzáadásával úgy, hogy a felhasználók lapozhassanak az eredmények között.

Mik az Azure Search újdonságai? Azt javasoljuk, próbáljon ki más oktatóanyagokat is, hogy jobban megismerhesse, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. További információkat szerezhet, ha megtekinti a [Videók és oktatóanyagok listáját](search-video-demo-tutorial-list.md).

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png

