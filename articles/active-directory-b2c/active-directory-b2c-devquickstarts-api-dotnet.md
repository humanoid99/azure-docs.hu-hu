---
title: Azure AD B2C | Microsoft Docs
description: "Hogyan lehet OAuth 2.0 hitelesítési hozzáférési jogkivonat általi védelemmel és az Azure Active Directory B2C alkalmazásával .NET-alapú webes API-t készíteni?"
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 3dd207805c1a8f53c6cc74da08cc9378609581c5
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: .NET webes API készítése

Az Azure Active Directory (Azure AD) B2C segítségével védetté tehet egy webes API-t OAuth 2.0 hozzáférési jogkivonatok használatával. Ezek a jogkivonatok teszik lehetővé az ügyfélalkalmazások számára a hitelesítést az API felé. Ebből a cikkből megtudhatja, hogyan hozhat létre egy .NET MVC „feladatlista” API-t, amellyel az ügyfélalkalmazása felhasználói CRUD-feladatokat végezhetnek el. A webes API-t az Azure AD B2C látja el védelemmel, így csak a hitelesített felhasználók kezelhetik a feladatlistájukat.

## <a name="create-an-azure-ad-b2c-directory"></a>Azure AD B2C címtár létrehozása

Ahhoz, hogy használni tudja az Azure AD B2C-t, előbb létre kell hoznia egy címtárat vagy bérlőt. A címtárban tárolhatja az összes felhasználót, alkalmazást, csoportot és sok minden mást. Ha még nem tette meg, [hozzon létre most egy B2C-címtárat](active-directory-b2c-get-started.md), mielőtt továbblépne ebben az útmutatóban.

> [!NOTE]
> Az ügyfélalkalmazásnak és a webes API-nak ugyanazt az Azure AD B2C könyvtárat kell használnia.
>

## <a name="create-a-web-api"></a>Webes API létrehozása

A következő lépésben hozzon létre egy webes API-alkalmazást a B2C-címtárban. Ez biztosítja az alkalmazással történő biztonságos kommunikációhoz szükséges információkat az Azure AD számára. Az alkalmazást a következő [utasítások](active-directory-b2c-app-registration.md) alapján hozza létre. Ügyeljen arra, hogy:

* Az alkalmazás tartalmazzon egy **webalkalmazást** vagy **webes API-t**.
* A webapphoz használja a `https://localhost:44332/` **URI-átirányítást**. Ez a webalkalmazás ügyféloldalának alapértelmezett helye ehhez a kódmintához.
* Másolja az alkalmazáshoz rendelt **alkalmazásazonosítót**. Erre később még szüksége lesz.
* Adjon meg egy alkalmazásazonosítót az **Alkalmazásazonosító URI** mezőben.
* Adjon hozzá engedélyeket a **Közzétett hatókörök** menüből.

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Szabályzatok létrehozása

Az Azure AD B2C-ben a felhasználói élményeket [szabályzatok](active-directory-b2c-reference-policies.md) határozzák meg. Létre kell hoznia egy házirendet az Azure AD B2C-vel való kommunikációhoz. Javasoljuk a kombinált regisztrálási/bejelentkezési házirend használatát a [házirendek áttekintésével foglalkozó cikkben](active-directory-b2c-reference-policies.md) leírtak szerint. A házirend létrehozásakor ügyeljen arra, hogy:

* A házirendben válassza ki a **megjelenítendő nevet** és az egyéb regisztrációs attribútumokat.
* Alkalmazási jogcímnek minden házirend esetén válassza ki a **megjelenítendő nevet** és az **objektumazonosítót**. Kiválaszthat egyéb jogcímeket is.
* Az egyes házirendek létrehozása után másolja a házirend **nevét**. A szabályzat nevére később még szüksége lesz.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

A házirend sikeres létrehozása után készen áll az app elkészítésére.

## <a name="download-the-code"></a>A kód letöltése

Az oktatóanyag kódjának kezelése a [GitHubon](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi) történik. A minta klónozásához futtassa a következőt:

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

Miután letöltötte a mintakódot, nyissa meg a Visual Studio .sln fájlt a kezdéshez. A megoldásfájl két projektet tartalmaz: `TaskWebApp` és `TaskService`. A `TaskWebApp` egy MVC-webalkalmazás, amellyel a felhasználó kommunikál. A `TaskService` az alkalmazás webes API háttérszolgáltatása, amely tárolja a felhasználók feladatlistáit. Ez a cikk csak a `TaskService` alkalmazást ismerteti. Ha tudni szeretné, hogyan hozhatja létre a `TaskWebApp` alkalmazást az Azure AD B2C-vel, tekintse meg a [.NET-alapú webappokról szóló oktatóanyagunkat](active-directory-b2c-devquickstarts-web-dotnet-susi.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Az Azure AD B2C konfiguráció frissítése

A minta úgy van konfigurálva, hogy a bemutató bérlőnk házirendjeit és ügyfél-azonosítóját használja. Ha a saját bérlőjét szeretné használni, tegye a következőt:

1. Nyissa meg a `web.config` fájlt a `TaskService` projektben, és a következőképpen cserélje le az értékeket:
    * Az `ida:Tenant` helyett szerepeljen a bérlő neve
    * Az `ida:ClientId` helyett szerepeljen a webes API-alkalmazás azonosítója
    * Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve

2. Nyissa meg a `web.config` fájlt a `TaskWebApp` projektben, és a következőképpen cserélje le az értékeket:
    * Az `ida:Tenant` helyett szerepeljen a bérlő neve
    * Az `ida:ClientId` helyett szerepeljen a webapp alkalmazásazonosítója
    * Az `ida:ClientSecret` helyett szerepeljen a webapp titkos kulcsa
    * Az `ida:SignUpSignInPolicyId` helyett szerepeljen a „regisztrálási vagy bejelentkezési” házirend neve
    * Az `ida:EditProfilePolicyId` helyett szerepeljen a „profil szerkesztése” házirend neve
    * Az `ida:ResetPasswordPolicyId` helyett szerepeljen a „Jelszó alaphelyzetbe állítása” házirend neve


## <a name="secure-the-api"></a>Az API védelme

Ha egy ügyfél hívja az API-t, biztonságossá teheti az API-t (pl. `TaskService`) az OAuth 2.0 tulajdonosi jogkivonatok használatával. Ez biztosítja, hogy az API felé irányuló kérések csak akkor legyenek érvényesek, ha a kérés rendelkezik tulajdonosi jogkivonattal. Az API a Microsoft Open Web Interface .NET (OWIN) könyvtár használatával tudja elfogadni és érvényesíteni a tulajdonosi jogkivonatokat.

### <a name="install-owin"></a>Az OWIN telepítése

Első lépésként telepítse az OWIN OAuth hitelesítési folyamatot a Visual Studio csomagkezelő konzoljával.

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

Ez telepíti az OWIN közbenső szoftvert, amely fogadja és érvényesíti a tulajdonosi jogkivonatokat.

### <a name="add-an-owin-startup-class"></a>OWIN indítási osztály hozzáadása

Adjon hozzá egy OWIN indítási osztályt a `Startup.cs` nevű API-hoz.  Kattintson a jobb gombbal a projektre, válassza az **Add** (Hozzáadás), majd a **New Item** (Új elem) lehetőséget, és keresse meg az OWIN elemet. Az OWIN közbenső szoftver meghívja a `Configuration(…)` metódust az alkalmazás indulásakor.

A mintánkban módosítottuk az osztálydeklarációt `public partial class Startup` értékre, és implementáltuk az `App_Start\Startup.Auth.cs` fájlban lévő osztály másik részét. A `Configuration` metóduson belül hozzáadtunk egy hívást a `ConfigureAuth` metódusra, amely a `Startup.Auth.cs` fájlban van definiálva. A módosítás után a `Startup.cs` a következőképpen néz ki:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-oauth-20-authentication"></a>OAuth 2.0 típusú hitelesítés konfigurálása

Nyissa meg a(z) `App_Start\Startup.Auth.cs` fájlt, és hajtsa végre a(z) `ConfigureAuth(...)` módszert. A következő módon nézhet ki például:

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>Feladatvezérlő védelme

Az alkalmazás OAuth 2.0 típusú hitelesítés használatára történő konfigurálása után, a webes API-t biztonságossá teheti úgy, hogy egy `[Authorize]` címkét ad hozzá a feladatvezérlőhöz. A feladatvezérlőben történik a feladatlisták kezelése, így osztályszinten kell biztonságossá tenni az egész vezérlőt. Az egyes műveletekre vonatkozóan is hozzáadhatja a(z) `[Authorize]` címkét a részletesebb vezérlés érdekében.

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Felhasználói adatok beolvasása a jogkivonatból

A `TasksController` egy olyan adatbázisban tárolja a feladatokat, ahol minden feladathoz hozzá van rendelve egy felhasználó, aki a feladat „tulajdonosa”. A tulajdonos azonosítása a felhasználói **objektumazonosítóval** történik. (Ezért kellett az alkalmazás jogcímeként objektumazonosítót hozzáadnia minden házirendben.)

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>A jogkivonat engedélyeinek érvényesítése

Általános követelmény a webes API-khoz a jogkivonatban jelen lévő „hatókörök” érvényesítése. Ez biztosítja, hogy a felhasználó hozzájárult a feladatlista szolgáltatáshoz való hozzáféréshez szükséges engedélyekhez.

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Végezetül készítse el és futtassa a következőket: `TaskWebApp` és `TaskService`. Hozzon létre feladatokat a felhasználói feladatlistában, és figyelje meg, hogy azok megmaradnak a API-ban azután is, ha leállította, majd újraindította az adott ügyfelet.

## <a name="edit-your-policies"></a>Házirendek szerkesztése

Miután az Azure AD B2C használatával biztonságossá tett egy API-t, kísérletezhet a bejelentkezési/regisztrálási szabályzattal, és megtekintheti ennek az API-ra gyakorolt hatásait (vagy a hatás hiányát). Módosíthatja a házirendekben az alkalmazás jogcímét és a webes API-n hozzáférhető felhasználói adatokat. Minden hozzáadott jogcím elérhető lesz a .NET MVC webes API számára a(z) `ClaimsPrincipal` objektumban az ebben a cikkben leírtak szerint.

