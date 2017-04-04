---
title: "Webalkalmazás létrehozása a Redis Cache használatával | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre webalkalmazást a Redis Cache használatával"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 02e30f7fcbe0782528460b542a75f1d11c7286a1
ms.lasthandoff: 03/28/2017


---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Webalkalmazás létrehozása a Redis Cache használatával
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan hozhat létre és helyezhet üzembe egy ASP.NET-webalkalmazást az Azure App Service szolgáltatásban lévő webalkalmazásba a Visual Studio 2017 használatával. Ez a mintaalkalmazás a csoportstatisztikák adatbázisból származó listáját jeleníti meg, illetve az Azure Redis Cache használatának különböző módjait mutatja be a gyorsítótár adatainak tárolására és beolvasására. Az oktatóanyag befejezését követően egy olyan futó webalkalmazással fog rendelkezni, amely adatokat olvas be és ír egy adatbázisba, az Azure Redis Cache használatával lett optimalizálva, és az Azure-ban van üzemeltetve.

Az oktatóanyagból a következőket sajátíthatja el:

* ASP.NET MVC 5 webalkalmazás létrehozása a Visual Studio használatával.
* Adatbázisadatok elérése az Entity Framework használatával.
* Az adatteljesítmény növelésének és az adatbázis-terhelés csökkentése az Azure Redis Cache használatával történő adattárolás és -beolvasás révén.
* Egy rendezett Redis-készlet használata az 5 legjobb csoport lekérdezéséhez.
* Azure-erőforrások kiépítése egy Resource Manager-sablont használó alkalmazás számára.
* Alkalmazás közzététele az Azure-ban a Visual Studio használatával.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez az alábbi előfeltételekkel kell rendelkeznie.

* [Azure-fiók](#azure-account)
* [Visual Studio 2017 Azure SDK for .NET csomaggal](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure-fiók
Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-fiókra. A következőket teheti:

* [Nyisson egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Jóváírásokat kap, amelyeket fizetős Azure-szolgáltatások kipróbálására használhat fel. Még ha a keretét el is használta, továbbra is megtarthatja a fiókot, és használhatja az ingyenes szolgáltatásokat és lehetőségeket.
* [Aktiválja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Az MSDN-előfizetés minden hónapban biztosít Önnek krediteket, amelyekkel fizetős Azure-szolgáltatásokat használhat.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 Azure SDK for .NET csomaggal
Az oktatóanyag a Visual Studio 2017-hez, valamint az [Azure SDK for .NET-hez](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools) készült. Az Azure SDK 2.9.5 a Visual Studio telepítőjének részét képezi.

Ha a gépén a Visual Studio 2015 van telepítve, kövesse az [Azure SDK for .NET](../dotnet-sdk.md) 2.8.2 vagy újabb verzió oktatóanyagát. [Innen letöltheti a legfrissebb Azure SDK-t a Visual Studio 2015-höz](http://go.microsoft.com/fwlink/?linkid=518003). Ha a Visual Studio még nincs telepítve, a rendszer automatikusan telepíti azt, az SDK-val együtt. Egyes képernyők eltérhetnek a jelen oktatóanyag ábráin láthatóaktól.

Ha a számítógépén a Visual Studio 2013 van telepítve, [töltse le a legfrissebb Azure SDK for Visual Studio 2013 alkalmazást](http://go.microsoft.com/fwlink/?LinkID=324322). Egyes képernyők eltérhetnek a jelen oktatóanyag ábráin láthatóaktól.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása
1. Nyissa meg a Visual Studio alkalmazást, majd kattintson a **File** (File), **New** (Új), **Project** (Projekt) lehetőségre.
2. Bontsa ki a **Visual C#** csomópontot a **Templates** (Sablonok) listában, válassza a **Cloud** (Felhő) lehetőséget, majd kattintson az **ASP.NET Web Application** (ASP.NET-webalkalmazás) elemre. Győződjön meg arról, hogy a **.NET Framework 4.5.2** vagy újabb keretrendszer van kiválasztva.  Írja be a **ContosoTeamStats** szöveget a **Name** (Név) szövegmezőbe, majd kattintson az **OK** gombra.
   
    ![Projekt létrehozása][cache-create-project]
3. A projekt típusaként válassza az **MVC** lehetőséget. 

    Ellenőrizze, hogy az **Authentication** (Hitelesítés) beállításai között a **No Authentication** (Nincs hitelesítés) van megadva. A Visual Studio verziójától függően az alapértelmezett beállítás más lehet. A beállítás módosításához kattintson a **Change Authentication** (Hitelesítés módosítása) gombra, és válassza a **No Authentication** (Nincs hitelesítés) értéket.

    Ha a Visual Studio 2015-öt használja, törölje a **Host in the cloud** (Üzemeltetés a felhőben) jelölőnégyzet jelölését. Az oktatóanyag következő lépéseiben megismerkedhet az [Azure-erőforrások kiépítésével](#provision-the-azure-resources) és az [alkalmazások közzétételével az Azure-ban](#publish-the-application-to-azure). A **Host in the cloud** (Üzemeltetés a felhőben) jelölőnégyzet bejelölésével a Visual Studio felületéről egy App Service-webalkalmazás létrehozására itt láthat példát: [Ismerkedés a webalkalmazásokkal az Azure App Service-ben, az ASP.NET és a Visual Studio használatával](../app-service-web/web-sites-dotnet-get-started.md).
   
    ![Projektsablon kiválasztása][cache-select-template]
4. A projekt létrehozásához kattintson az **OK** gombra.

## <a name="create-the-aspnet-mvc-application"></a>Az ASP.NET MVC alkalmazás létrehozása
Az oktatóanyag ezen szakaszában egy olyan alapszintű alkalmazást fog létrehozni, amely adatbázisból olvas be és jelenít meg csoportstatisztikákat.

* [Az Entity Framework NuGet-csomag hozzáadása](#add-the-entity-framework-nuget-package)
* [Modell hozzáadása](#add-the-model)
* [Vezérlő hozzáadása](#add-the-controller)
* [A nézetek konfigurálása](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Az Entity Framework NuGet-csomag hozzáadása

1. Kattintson a **Tools** (Eszközök) menü **NuGet Package Manager** (NuGet-csomagkezelő), **Package Manager Console** (Csomagkezelő konzol) elemére.
2. Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    
    ```
    Install-Package EntityFramework
    ```

A csomaggal kapcsolatos további információt az [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet-oldalon talál.

### <a name="add-the-model"></a>Modell hozzáadása
1. Kattintson a jobb gombbal a **Models** (Modellek) elemre a **Solution Explorer** (Megoldáskezelő) területén, és válassza az **Add** (Hozzáadás), **Class** (Osztály) lehetőségeket. 
   
    ![Modell hozzáadása][cache-model-add-class]
2. Az osztály neveként adja meg a `Team` nevet, majd kattintson az **Add** (Hozzáadás) gombra.
   
    ![Modellosztály hozzáadása][cache-model-add-class-dialog]
3. A `Team.cs` fájl elején cserélje le a `using` utasításokat az alábbi `using` utasításokra.

    ```c#
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Cserélje le a `Team` osztály definícióját az alábbi kódrészlettel, amely a `Team` osztály frissített definícióját, valamint néhány további Entity Framework-súgóosztályt tartalmaz. További információk a jelen oktatóanyagban használt, Code First nevű Entity Framework-megközelítésról: [Code First alkalmazása egy új adatbázisra](https://msdn.microsoft.com/data/jj193542).

    ```c#
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán a **web.config** fájlra annak megnyitásához.
   
    ![Web.config][cache-web-config]
2. Adja hozzá a következő `connectionStrings` szakaszt. A kapcsolati karakterlánc nevének meg kell egyeznie az Entity Framework-adatbáziskörnyezet osztályának nevével, amely a következő: `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Hozzáadhatja az új `connectionStrings` szakaszt a `configSections` után, ahogyan az az alábbi példában látható.

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

### <a name="add-the-controller"></a>Vezérlő hozzáadása
1. A projekt létrehozásához nyomja le az **F6** billentyűt. 
2. A **Solution Explorerben** (Megoldáskezelőben) kattintson a jobb gombbal a **Controllers** (Vezérlők) mappára, majd válassza az **Add** (Hozzáadás), **Controller** (Vezérlő) lehetőségeket.
   
    ![Vezérlő hozzáadása][cache-add-controller]
3. Válassza az **MVC 5 Controller with views, using Entity Framework** (MVC 5 vezérlő nézetekkel, az Entity Framework használatával) lehetőséget, majd kattintson az **Add** (Hozzáadás) lehetőségre. Ha az **Add** (Hozzáadás) gombra kattintva a rendszer hibaüzenetet küld, győződjön meg arról, hogy a projekt létrehozása előzetesen megtörtént.
   
    ![Vezérlőosztály hozzáadása][cache-add-controller-class]
4. Válassza ki a **Team (ContosoTeamStats.Models)** elemet a **Model class** (Modellosztály) legördülő listából. Válassza ki a **TeamContext (ContosoTeamStats.Models)** elemet a **Adatkörnyezet osztálya** (Adatkörnyezet osztálya) legördülő listából. Írja be a `TeamsController` szöveget a **Controller** (Vezérlő) névmezőbe (ha az nincs automatikusan kitöltve). Kattintson az **Add** (Hozzáadás) gombra a vezérlőosztály létrehozásához és az alapértelmezett nézetek hozzáadásához.
   
    ![Vezérlő konfigurálása][cache-configure-controller]
5. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki a **Global.asax** elemet, majd kattintson duplán a **Global.asax.cs** fájlra annak megnyitásához.
   
    ![Global.asax.cs][cache-global-asax]
6. Adja hozzá a következő két `using` utasítást a fájl elejéhez, a többi `using` utasítás alá.

    ```c#
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. Illessze az alábbi kódsort az `Application_Start` módszer végére.

    ```c#
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki az `App_Start` elemet, majd kattintson duplán a `RouteConfig.cs` elemre.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Cserélje le a `controller = "Home"` elemet a `RegisterRoutes` módszer alábbi kódjában a `controller = "Teams"` szövegre, a következő példán látható módon.

    ```c#
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>A nézetek konfigurálása
1. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki a **Views**(Nézetek), majd a **Shared** (Közös) mappát, és kattintson duplán a **_Layout.cshtml** fájlra. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. Az alábbi példában látható módon módosítsa a `title` elem tartalmát, majd cserélje le a `My ASP.NET Application` szöveget a `Contoso Team Stats` szövegre.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. A `body` szakaszban frissítse az első `Html.ActionLink` utasítást, és cserélje le az `Application name` szöveget a `Contoso Team Stats` szövegre, majd a `Home` szöveget a `Teams` szövegre.
   
   * Előtte: `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Utána: `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Kódmódosítások][cache-layout-cshtml-code]
2. Az alkalmazás fordításához és futtatásához nyomja le a **Ctrl+F5** billentyűkombinációt. Az alkalmazás ezen verziója az eredményeket közvetlenül az adatbázisból olvassa ki. Figyelje meg, hogy az **Új létrehozása**, a **Szerkesztés**, a **Részletek**és a **Törlés** parancsok az **MVC 5 Controller with views, using Entity Framework** (MVC 5 vezérlő nézetekkel, az Entity Framework használatával) szerkezettel automatikusan bekerültek az alkalmazásba. Az oktatóanyag következő szakaszában az adatelérés optimalizálása és további alkalmazásszolgáltatások biztosítása érdekében el fogja végezni a Redis Cache hozzáadását.

![Kezdő szintű alkalmazás][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Az alkalmazás konfigurálása a Redis Cache használatára
Az oktatóanyag jelen szakaszában el fogja végezni a mintaalkalmazás konfigurálását az Azure Redis Cache-példányból származó Contoso-csoportstatisztikák tárolására és beolvasására a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) gyorsítótárügyfél használatával.

* [Az alkalmazás konfigurálása a StackExchange.Redis használatára](#configure-the-application-to-use-stackexchangeredis)
* [A TeamsController osztály frissítése a gyorsítótárból vagy az adatbázisból eredmények visszaadásához](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [A Létrehozás, Szerkesztés és Törlés módszerek frissítése a gyorsítótárral való együttműködéshez](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [A Teams Index nézet frissítése a gyorsítótárral való együttműködéshez](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Az alkalmazás konfigurálása a StackExchange.Redis használatára
1. Ha egy ügyfélalkalmazást a StackExchange.Redis NuGet-csomaggal szeretne konfigurálni a Visual Studióban, kattintson a **Tools** (Eszközök) menü **NuGet Package Manager** (NuGet-csomagkezelő), **Package Manager Console** (Csomagkezelő konzol) elemére.
2. Futtassa az alábbi parancsot a `Package Manager Console` ablakából.
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    A NuGet-csomag letölti és hozzáadja az ügyfélalkalmazás számára szükséges szerelvényhivatkozásokat az Azure Redis Cache a StackExchange.Redis gyorsítótárügyféllel történő eléréséhez. Ha inkább a `StackExchange.Redis` ügyfélkönyvtár erős elnevezésű verzióját kívánja használni, telepítse a `StackExchange.Redis.StrongName` csomagot.
3. A **Solution Explorerben** (Megoldáskezelőben) bontsa ki a **Controllers** (Vezérlők) mappát, majd kattintson duplán a **TeamsController.cs** fájlra annak megnyitásához.
   
    ![Csoportvezérlő][cache-teamscontroller]
4. Adja hozzá az alábbi két `using` utasítást a **TeamsController.cs** fájlhoz.

    ```c#   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Adja hozzá az alábbi két tulajdonságot a `TeamsController` osztályhoz.

    ```c#   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. Hozzon létre egy `WebAppPlusCacheAppSecrets.config` nevű fájlt a számítógépen, majd mentse azt egy olyan helyre, amelyet a mintaalkalmazás forráskódja nem fog ellenőrizni, amennyiben úgy dönt, hogy valahol ellenőrizni kívánja azt. Jelen példában az `AppSettingsSecrets.config` fájl elérési útja: `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Módosítsa a `WebAppPlusCacheAppSecrets.config` fájlt, és adja hozzá az alábbi tartalmakat. Az alkalmazás helyi futtatásakor ezen információk az Azure Redis Cache-példányhoz történő kapcsolódáshoz lesznek felhasználva. Az oktatóanyag későbbi szakaszában egy Azure Redis Cache-példány létrehozását, valamint a gyorsítótár nevének és jelszavának módosítását fogja elvégezni. Ha nem tervezi az alkalmazás helyi futtatását, kihagyhatja ennek a fájlnak a létrehozását, illetve a fájlra hivatkozó következő lépéseket, mivel az Azure-on történő telepítéskor az alkalmazás a gyorsítótár csatlakoztatási információit a webalkalmazás beállításaiból kéri le, nem pedig ebből a fájlból. Mivel a `WebAppPlusCacheAppSecrets.config` nem települ az Azure-on az alkalmazással együtt, csak abban az esetben van rá szüksége, ha az alkalmazást helyileg kívánja futtatni.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. A **Solution Explorerben** (Megoldáskezelőben) kattintson duplán a **web.config** fájlra annak megnyitásához.
   
    ![Web.config][cache-web-config]
2. Adja hozzá az alábbi `file` attribútumot az `appSettings` elemhez. Ha más fájlnevet vagy helyet használ, helyettesítse azokat a példában látható értékekkel.
   
   * Előtte: `<appSettings>`
   * Utána: ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
     
   Az ASP.NET futtatási környezet a külső fájl tartalmát egyesíti az `<appSettings>` elem kódjával. Ha a megadott fájl nem található, a futtatási környezet figyelmen kívül hagyja a fájlattribútumot. A titkos kulcsok (a gyorsítótárhoz tartozó kapcsolati karakterláncok) nem képezik részét az alkalmazás forráskódjának. A webalkalmazás Azure-on történő üzembe helyezésekor a `WebAppPlusCacheAppSecrests.config` fájl nem lesz telepítve (ez megfelel a szándékainknak). A titkos kulcsok megadására számos mód létezik az Azure-ban, ezek pedig ennek az oktatóanyagnak a későbbi lépéseiben automatikusan konfigurálva lesznek az [Azure-erőforrások kiépítésekor](#provision-the-azure-resources). További információk a titkos kulcsok használatáról az Azure-ban: [Ajánlott eljárások a jelszavak és egyéb érzékeny adatok telepítéséhez az ASP.NET és az Azure App Service szolgáltatásokban](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>A TeamsController osztály frissítése a gyorsítótárból vagy az adatbázisból eredmények visszaadásához
Jelen példában a csapatstatisztikák az adatbázisból vagy a gyorsítótárból is lekérdezhetők. A csapatstatisztikák a gyorsítótárban szerializált `List<Team>`, illetve (Redis adattípusok használatával) rendezett készlet formájában vannak tárolva. Rendezett készletből történő lekérdezéskor egyes, az összes vagy bizonyos feltételnek megfelelő elemek lekérésére van lehetőség. Jelen példában lekérdezünk egy rendezett készletet a győzelmek száma szerint rangsorolt 5 legjobb csapatra.

> [!NOTE]
> Az Azure Redis Cache használatához nem szükséges a csapatstatisztikák többféle formátumban történő elmentése a gyorsítótárban. Ez az oktatóanyag többféle formátumot használ az adatok gyorsítótárazásához használható különböző módszerek és adattípusok példáinak bemutatására.
> 
> 

1. Adja hozzá az alábbi `using` utasításokat a `TeamsController.cs` fájl elejéhez, a többi `using` utasítással együtt.

    ```c#   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Az aktuális `public ActionResult Index()` metódusmegvalósítást cserélje le az alábbi megvalósításra.

    ```c#
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. Vegye fel az alábbi három módszert a `TeamsController` osztályba azon `playGames`, `clearCache`, és `rebuildDB` művelettípusok megvalósításához, amelyek az előző kódrészletben hozzáadott „switch” utasításból származnak.
   
    Egy játékszezon szimulálásával a `PlayGames` módszer frissíti a csapatstatisztikákat, az eredményeket elmenti az adatbázisba, majd törli a gyorsítótárból a már elavult adatokat.

    ```c#
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    A `RebuildDB` módszer újrainicializálja az adatbázist az alapértelmezett csapatokkal, statisztikákat állít elő számukra, és törli a gyorsítótárból a már elavult adatokat.

    ```c#
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    A `ClearCachedTeams` módszer eltávolítja a gyorsítótárazott csapatstatisztikákat a gyorsítótárból.

    ```c#
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. Vegye fel az alábbi négy módszert a `TeamsController` osztályba a csapatstatisztikák gyorsítótárból és adatbázisból különböző módszerekkel történő lekérdezéséhez. Ezen módszerek mindegyike egy, a nézetben megjelenített `List<Team>` választ ad vissza.
   
    A `GetFromDB` módszer beolvassa a csapatstatisztikákat a gyorsítótárból.
   
    ```c#
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    A `GetFromList` módszer szerializált `List<Team>` formájában olvassa be a csapatstatisztikákat a gyorsítótárból. Gyorsítótár-tévesztés esetén a rendszer az adatbázisból olvassa be a statisztikákat, és azokat a gyorsítótárba menti a következő alkalomra. Jelen mintában a JSON.NET szerializálást alkalmazzuk a .NET-objektumok gyorsítótárba és gyorsítótárból történő szerializálására. További információk: [.NET-objektumokkal való munka az Azure Redis Cache-ben](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```c#
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    A `GetFromSortedSet` módszer beolvassa a csapatstatisztikákat egy gyorsítótárazott rendezett készletből. Gyorsítótár-tévesztés esetén a rendszer az adatbázisból olvassa be a statisztikákat, és azokat a gyorsítótárba menti, rendezett készletként.

    ```c#
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    A `GetFromSortedSetTop5` módszer beolvassa az 5 legjobb csapatot a gyorsítótárazott rendezett készletből. Első lépésben a `teamsSortedSet` kulcsot keresi meg a gyorsítótárban. Ha a kulcs nem található, a rendszer a `GetFromSortedSet` módszert hívja meg a csapatstatisztikák beolvasásához és azoknak a gyorsítótárban történő tárolásához. Ezt a gyorsítótárazott rendezett készlet lekérdezése követi, amely az 5 legjobb csapatot adja vissza.

    ```c#
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>A Létrehozás, Szerkesztés és Törlés módszerek frissítése a gyorsítótárral való együttműködéshez
A szerkezeti kódot a rendszer ezen minta részeként állítja elő a csapatok hozzáadásához, szerkesztéséhez és törléséhez. Egy csapat hozzáadását, szerkesztését vagy eltávolítását követően a gyorsítótárban található adatok elavulttá válnak. Jelen szakaszban ezen három módszer módosítását fogja elvégezni a gyorsítótárazott csapatok törlése érdekében, így a gyorsítótár szinkronizálva lesz az adatbázissal.

1. Keresse meg a `Create(Team team)` módszert a `TeamsController` osztályban. Adjon hozzá hívást a `ClearCachedTeams` módszerhez, ahogy az az alábbi példában is látható.

    ```c#
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. Keresse meg a `Edit(Team team)` módszert a `TeamsController` osztályban. Adjon hozzá hívást a `ClearCachedTeams` módszerhez, ahogy az az alábbi példában is látható.

    ```c#
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. Keresse meg a `DeleteConfirmed(int id)` módszert a `TeamsController` osztályban. Adjon hozzá hívást a `ClearCachedTeams` módszerhez, ahogy az az alábbi példában is látható.

    ```c#
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>A Teams Index nézet frissítése a gyorsítótárral való együttműködéshez
1. A **Solution Explorer** (Megoldáskezelőben) bontsa ki a **Views** (Nézetek), majd a **Teams** (Csapatok) mappát, és kattintson duplán az **Index.cshtml** fájlra.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. A fájl elején keresse meg az alábbi bekezdéselemet.
   
    ![Művelettábla][cache-teams-index-table]
   
    Itt található az új csapat létrehozására szolgáló hivatkozás. Cserélje le a bekezdéselemet az alábbi táblával. Ez a tábla műveleti hivatkozásokkal rendelkezik egy új csapat létrehozása, egy új játékszezon lejátszása, a gyorsítótár kiürítése, a csapatok gyorsítótárból, több formátumban történő lekérdezése, a csapatok adatbázisból történő lekérdezése, valamint az adatbázis friss mintaadatokkal való újraépítése céljából.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. Görgessen lefelé az **Index.cshtml** fájl aljához, és vegye fel az alábbi `tr` elemet, így ez lesz a fájl utolsó táblájának utolsó sora.
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    Ez a sor a `ViewBag.Msg` értékét jeleníti meg, amely az aktuális művelet állapotjelentését tartalmazza. A `ViewBag.Msg` beállítása az előző lépés egyik műveleti hivatkozására kattintva történhet.   
   
    ![Állapotüzenet][cache-status-message]
2. A projekt létrehozásához nyomja le az **F6** billentyűt.

## <a name="provision-the-azure-resources"></a>Azure-erőforrások kiépítése
Az alkalmazásnak az Azure-on történő üzemeltetéséhez először is létre kell hoznia az alkalmazás számára szükséges Azure-szolgáltatásokat. A jelen oktatóanyagban szereplő mintaalkalmazás az alábbi Azure-szolgáltatásokat használja.

* Azure Redis Cache
* App Service webalkalmazás
* SQL Database

Ezen szolgáltatások új vagy létező, szabadon választott erőforráscsoporton történő üzembe helyezéséhez kattintson az alábbi **Deploy to Azure** (Üzembe helyezés az Azure-ban) gombra.

[![Deploy to Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Ez az **Deploy to Azure** (Üzembe helyezés az Azure-ban) gomb a [Webalkalmazás, Redis Cache és SQL Database egyidejű létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure gyors üzembe helyezés](https://github.com/Azure/azure-quickstart-templates) sablonját használja ezen szolgáltatások kiépítéséhez, illetve az SQL Database kapcsolati karakterláncának megadásához és az Azure Redis Cache-kapcsolatikarakterlánc alkalmazás-beállításához.

> [!NOTE]
> Ha nincs Azure-fiókja, néhány perc alatt [létrehozhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero).
> 
> 

Az **Üzembe helyezés az Azure-ban** gombra kattintva megnyílik az Azure portál, majd elindul a sablonban megadott erőforrások létrehozásának folyamata.

![Üzembe helyezés az Azure-ban][cache-deploy-to-azure-step-1]

1. Az **Alapok** szekcióban válassza ki a használni kívánt Azure-előfizetést, jelöljön ki egy meglévő erőforráscsoportot vagy hozzon létre egy újat, majd adja meg az erőforráscsoport helyét.
2. A **Beállítások** részben adja meg az **rendszergazdai bejelentkezési nevet** (ne használja az **admin** kifejezést), a **rendszergazdai bejelentkezési jelszót** és az **adatbázisnevet**. A többi paraméter egy ingyenes App Service futtatási csomaghoz van konfigurálva, valamint alacsonyabb költségszint érhető el az SQL Database és az Azure Redis Cache esetében, amelyek nem részei az ingyenes szintnek.

    ![Üzembe helyezés az Azure-ban][cache-deploy-to-azure-step-2]

3. A kívánt beállítások konfigurálása után görgessen az oldal végére, olvassa el a feltételeket és kikötéseket, és pipálja ki az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet.
4. Az erőforrások kiépítésének megkezdéséhez kattintson a **Vásárlás** gombra.

Az üzembe helyezési folyamat előrehaladásának megtekintéséhez kattintson az értesítési ikonra, majd a **Központi telepítés elindítva** gombra.

![Központi telepítés elindítva][cache-deployment-started]

A központi telepítés állapotát a **Microsoft.Template** panelen tekintheti meg.

![Üzembe helyezés az Azure-ban][cache-deploy-to-azure-step-3]

A kiépítés után a Visual Studio felületéről közzéteheti alkalmazását az Azure-on.

> [!NOTE]
> A kiépítési folyamat során esetlegesen jelentkező hibák a **Microsoft.Template** panelen jelennek meg. Gyakori hiba például az előfizetésenkénti túl sok SQL Server-példány és a túl sok Ingyenes App Service-futtatási csomag. A hibák elhárítása és a folyamat újraindítása a **Microsoft.Template** panel **Ismételt üzembe helyezés** elemére, vagy a jelen oktatóanyag **Üzembe helyezés az Azure-ban** gombjára kattintva végezhető el.
> 
> 

## <a name="publish-the-application-to-azure"></a>Az alkalmazás közzététele az Azure-ban
Az oktatóanyag ezen lépésben közzéteszi alkalmazását az Azure-ban, majd futtatja azt a felhőben

1. Kattintson a jobb gombbal a Visual Studio **ContosoTeamStats** projektjére, majd válassza a **Publish** (Közzététel) lehetőséget
   
    ![Közzététel][cache-publish-app]
2. Kattintson a **Microsoft Azure App Service** lehetőségre, válassza a **Meglévő kiválasztása** elemet, majd kattintson a **Közzététel** gombra.
   
    ![Közzététel][cache-publish-to-app-service]
3. Válassza ki az Azure-erőforrások létrehozásakor használt előfizetést, bontsa ki az erőforrásokat tartalmazó erőforráscsoportot, és válassza ki a kívánt webappot. Ha az **Üzembe helyezés az Azure-ban** gombot használta, a webalkalmazás neve a **webSite** kifejezéssel kezdődik, amit néhány további karakter követ.
   
    ![Webalkalmazás kiválasztása][cache-select-web-app]
4. A közzétételi folyamat elindításához kattintson az **OK** gombra. A közzétételi folyamat néhány pillanat múlva befejeződik, és a elindul böngésző a futó mintaalkalmazással együtt. Ha érvényesítés vagy közzététel közben a rendszer DNS-hibát ad vissza, az alkalmazáshoz tartozó Azure-erőforrások kiépítési folyamata pedig csak az imént fejeződött be, várjon egy kicsit, és próbálkozzon újra.
   
    ![Gyorsítótár hozzáadva][cache-added-to-application]

A mintaalkalmazás egyes műveleti hivatkozásait a következő táblázat ismerteti.

| Műveletek | Leírás |
| --- | --- |
| Új létrehozása |Létrehoz egy új csapatot. |
| Szezon végigjátszása |Végigjátszik egy szezont, frissíti a csapatstatisztikákat, és törli a gyorsítótárból az elavult adatokat. |
| Gyorsítótár ürítése |Törli a csapatstatisztikákat a gyorsítótárból. |
| Lista a gyorsítótárból |Lekérdezi a csapatstatisztikákat a gyorsítótárból. Gyorsítótár-tévesztés esetén az adatbázisból tölti be a statisztikákat, és menti azt a gyorsítótárba a következő alkalomra. |
| Rendezett készlet a gyorsítótárból |Lekérdezi a csapatstatisztikákat a gyorsítótárból egy rendezett készlet használatával. Gyorsítótár-tévesztés esetén az adatbázisból tölti be a statisztikákat, és menti azt a gyorsítótárba egy rendezett készlet használatával. |
| Az 5 legjobb csapat a gyorsítótárból |Lekérdezi az 5 legjobb csapatot a gyorsítótárból egy rendezett készlet használatával. Gyorsítótár-tévesztés esetén az adatbázisból tölti be a statisztikákat, és menti azt a gyorsítótárba egy rendezett készlet használatával. |
| Betöltés adatbázisból |Lekérdezi a csapatstatisztikákat az adatbázisból. |
| Adatbázis újraépítése |Újraépíti az adatbázist, és ismét feltölti azt minta-csapatadatokkal. |
| Szerkesztés / Részletek / Törlés |Szerkeszthet egy csapatot, megtekintheti annak részletes adatait, törölhet egy csapatot. |

Kattintson néhány műveletre, és kísérletezzen az adatok különböző forrásokból történő lekérdezésével. Figyelje meg az adatbázisból és a gyorsítótárból történő adatlekérdezés különböző módjainak végrehajtásához szükséges időbeli eltéréseket.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Az erőforrások törlése az alkalmazás bezárását követően
Ha befejezte az oktatóanyag mintaalkalmazásának használatát, a költség- és erőforrás-takarékosság érdekében törölheti az ott használt Azure-erőforrásokat. Ha a [Azure-erőforrások kiépítése](#provision-the-azure-resources) szakasz **Üzembe helyezés az Azure-ban** gombját használja, és valamennyi erőforrás azonos erőforráscsoportban található, az erőforráscsoport törlésével egy művelettel, együttesen is törölheti azokat.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.
2. Írja be az erőforráscsoport nevét az **Elemek szűrése...** szövegmezőbe.
3. Kattintson az erőforráscsoporttól jobbra lévő **...** elemre.
4. Kattintson a **Törlés** gombra.
   
    ![Törlés][cache-delete-resource-group]
5. Írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.
   
    ![Törlés megerősítése][cache-delete-confirm]

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

> [!IMPORTANT]
> Ügyeljen arra, hogy az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő erőforráscsoportban hozta létre, az erőforrásokat külön-külön törölheti a megfelelő panelekről.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Mintaalkalmazás futtatása helyi gépen
Az alkalmazás helyi számítógépen történő futtatásához egy olyan Azure Redis Cache-példányra van szükség, amelyen az adatok gyorsítótárazása elvégezhető. 

* Ha az alkalmazás Azure-on történő közzétételét az előző szakaszban leírt módon hajtotta végre, használhatja az abban a lépésben üzembe helyezett Azure Redis Cache-példányt.
* Ha rendelkezik egy másik meglévő Azure Redis Cache-példánnyal, használhatja azt ezen minta helyi futtatásához.
* Amennyiben létre kell hoznia egy Azure Redis Cache-példányt, ennek műveleti lépéseit megtalálja a [Gyorsítótár létrehozása](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) részben.

A használni kívánt gyorsítótár kiválasztása vagy létrehozása után keresse meg azt az Azure portálon, majd kérje le a hozzá tartozó [állomásnév](cache-configure.md#properties) és [hívóbetű](cache-configure.md#access-keys) paramétereket. Útmutatásért lásd: [A Redis Cache-gyorsítótár beállításai](cache-configure.md#configure-redis-cache-settings).

1. A kívánt szerkesztővel nyissa meg a jelem oktatóanyag [Az alkalmazás konfigurálása a Redis Cache használatára](#configure-the-application-to-use-redis-cache) lépésében létrehozott `WebAppPlusCacheAppSecrets.config` fájlt.
2. Módosítsa a `value` attribútumot, és cserélje le a `MyCache.redis.cache.windows.net` elemet a gyorsítótár [állomásnevével](cache-configure.md#properties), majd jelszóként adja meg a gyorsítótár [elsődleges vagy másodlagos kulcsát](cache-configure.md#access-keys).

    ```xml
    <appSettings>
      <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
    </appSettings>
    ```


1. Az alkalmazás futtatásához nyomja le a **Ctrl+F5** billentyűkombinációt.

> [!NOTE]
> Vegye figyelembe, hogy mivel az alkalmazás (beleértve az adatbázist is) futtatása helyileg történik, a Redis Cache üzemeltetését pedig az Azure végzi, a gyorsítótár teljesítménye az adatbázisénál kisebbnek tűnhet. A legjobb teljesítmény érdekében az ügyfélalkalmazásnak és az Azure Redis Cache-példánynak azonos helyen kell lennie. 
> 
> 

## <a name="next-steps"></a>Következő lépések
* Az [ASP.NET MVC 5 – Első lépések](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) elvégzéséről további információkat az [ASP.NET](http://asp.net/) webhelyén talál.
* További példák egy ASP.NET-webalkalmazás létrehozására az App Service szolgáltatásban: [Create and deploy an ASP.NET web app in Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) (ASP.NET-webalkalmazás létrehozása és üzembe helyezése az Azure App Service szolgáltatásban) a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [bemutatóból](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
  * A HealthClinic.biz bemutató további gyors útmutatóit lásd: [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Azure fejlesztői eszközök – gyors útmutatók).
* Itt további információkat talál a jelen oktatóanyagban használt, [Code first to a new database](https://msdn.microsoft.com/data/jj193542) (Code First alkalmazása egy új adatbázisra) nevű Entity Framework-megközelítésról.
* További információ [az Azure App Service webalkalmazásairól](../app-service-web/app-service-web-overview.md).
* Tudnivalók a gyorsítótár [figyeléséről](cache-how-to-monitor.md) az Azure portálon.
* Az Azure Redis Cache prémium funkcióinak megismerése
  
  * [Adatmegőrzés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-persistence.md)
  * [Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-clustering.md)
  * [Virtuális hálózat támogatásának konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-vnet.md)
  * További részletes információk a prémium gyorsítótárak méretével, teljesítményével és a sávszélességével kapcsolatban: [Azure Redis Cache – Gyakori kérdések](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png


