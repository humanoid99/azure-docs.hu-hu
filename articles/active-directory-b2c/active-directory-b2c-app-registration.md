---
title: "Azure Active Directory B2C: Alkalmazás regisztrációja | Microsoft Docs"
description: "Hogyan regisztrálhatja alkalmazását Azure Active Directory B2C-vel?"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: e04fbd97dd4d5ecaf12edf47d80572b32d29ed00
ms.lasthandoff: 03/23/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: Az alkalmazás regisztrációja

> [!IMPORTANT]
> Az Azure Portal Azure AD B2C paneljén létrehozott alkalmazásokat ugyanarról a helyről kell kezelnie. Ha a B2C-alkalmazásokat a PowerShell vagy egy másik portál használatával szerkeszti, a rendszer nem támogatja többé az alkalmazásokat, és valószínűleg nem fognak működni az Azure AD B2C-vel.
> 
> 

## <a name="prerequisite"></a>Előfeltétel
A felhasználói regisztrációt és bejelentkezést elfogadó alkalmazás létrehozásához először regisztrálnia kell az alkalmazást egy Azure Active Directory B2C-bérlővel. Hozzon létre egy saját bérlőt az [Azure AD B2C bérlő létrehozása](active-directory-b2c-get-started.md) részben ismertetett lépések segítségével. Miután elvégezte ezeket a lépéseket, a B2C funkciók panelje rögzítésre kerül a kezdőpulton.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Lépjen a B2C funkciók paneljére
Ha a B2C funkciók panelje rögzítésre kerül a kezdőpulton, azonnal látni fogja a panelt, amint B2C-bérlő globális rendszergazdájaként bejelentkezik az [Azure Portalra](https://portal.azure.com/).

A panelre úgy is beléphet, hogy a **További szolgáltatások** elemre kattint, majd megkeresi az **Azure AD B2C** elemet az [Azure Portal](https://portal.azure.com/) bal oldali navigációs ablaktábláján.

> [!IMPORTANT]
> A B2C bérlő globális rendszergazdájának kell lennie ahhoz, hogy hozzáférhessen a B2C funkciók panelhez. Más bérlők globális rendszergazdái vagy felhasználói nem férhetnek hozzá a panelhez.  A B2C-bérlőre az Azure Portal jobb felső sarkában található bérlőváltó használatával válthat át.
> 
> 

## <a name="register-a-web-application"></a>Webalkalmazás regisztrációja
1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
2. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
3. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
4. Állítsa az **Include web app / web API** (Webappal/webes API-val együtt) kapcsolót **Yes** (Igen) állásba. A **válasz URL-címek** olyan végpontok, ahol az Azure AD B2C visszalép az alkalmazás által kért jogkivonatokhoz. Adja meg például a következőt: `https://localhost:44316/`.
5. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
6. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni. 
7. Ha a webalkalmazás az Azure AD B2C által biztonságossá tett webes API-kat is meg fog hívni, ajánlott egy **Application Secret** (Alkalmazástitok) létrehozása is a **Keys** (Kulcsok) panel **Generate Key** (Kulcs létrehozása) gombjára kattintva.

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
> 
   

## <a name="register-a-web-api"></a>Webes API regisztrálása
1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
2. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
3. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
4. Állítsa az **Include web app / web API** (Webappal/webes API-val együtt) kapcsolót **Yes** (Igen) állásba. A **válasz URL-címek** olyan végpontok, ahol az Azure AD B2C visszalép az alkalmazás által kért jogkivonatokhoz. Adja meg például a következőt: `https://localhost:44316/`.
5. Adjon meg egy **Alkalmazásazonosító URI-t**. Ez a webes API-hoz használt azonosító. Írja be például a következőt: „megjegyzések”. Ez alatta létrehozza a teljes azonosító URI-t. 
6. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
7. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.
8. Kattintson a **Published scopes** (Közzétett hatókörök) elemre. Itt határozza meg a más alkalmazásoknak megadható engedélyeket (hatóköröket).
9. Szükség szerint több hatókört is felvehet. Alapértelmezés szerint a „user_impersonation” hatókör van meghatározva. Ez lehetővé teszi az egyéb alkalmazások számára, hogy a bejelentkezett felhasználó nevében érjék el az API-t. Ha szeretné, ezt eltávolíthatja. 
10. Kattintson a **Save** (Mentés) gombra.

## <a name="register-a-mobilenative-application"></a>Mobil-/natív alkalmazás regisztrálása
1. Kattintson az Azure Portal B2C funkciók panelén az **Applications** (Alkalmazások) lehetőségre.
2. A panel tetején kattintson a **+Add** (+Hozzáadás) lehetőségre.
3. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Adja meg például a „Contoso B2C alkalmazás” nevet.
4. Állítsa az **Include native client** (Natív ügyféllel együtt) kapcsolót **Yes** (Igen) állásba.
5. Adjon meg egy **Átirányítási URI-t** egy egyéni sémával. Például: com.onmicrosoft.contoso.appnév://átirányítási/útvonal. Ellenőrizze, hogy [megfelelő átirányítási URI-t](#choosing-a-redirect-uri) választott-e.
6. Kattintson a **Save** (Mentés) gombra az alkalmazás regisztrálásához.
7. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.
8. Ha a natív alkalmazás az Azure AD B2C által biztonságossá tett webes API-kat is meg fog hívni, ajánlott egy **Application Secret** (Alkalmazástitok) létrehozása is a **Keys** (Kulcsok) panel **Generate Key** (Kulcs létrehozása) gombjára kattintva.

> [!NOTE]
> Az **Application Secret** (Alkalmazástitok) fontos biztonsági hitelesítő adat, amelynek megfelelő biztonságáról gondoskodni kell.
> 

### <a name="choosing-a-redirect-uri"></a>Átirányítási URI kiválasztása
A mobil-/natív alkalmazások átirányítási URI-jának kiválasztásakor két fontos szempontot kell megfontolnia: 
* **Egyedi**: Az átirányítási URI sémájának minden alkalmazás esetén egyedinek kell lennie. A példánkban (com.onmicrosoft.contoso.appname://redirect/path) a com.onmicrosoft.contoso.appnév a séma. Javasoljuk, hogy ezt a mintát kövesse. Ha két alkalmazás ugyanazt a sémát használja, akkor a felhasználó egy „Alkalmazás kiválasztása” párbeszédpanelt fog látni. Ha a felhasználó helytelenül választ, a bejelentkezés meghiúsul. 
* **Teljes**: Az átirányítási URI-nak sémával és elérési útvonallal kell rendelkeznie. Az útvonalnak a tartomány után legalább egy perjelet kell tartalmaznia (például a //contoso/ helyes, de a //contoso nem). 

## <a name="build-a-quick-start-application"></a>Első lépések alkalmazás készítése
Miután az Azure AD B2C-vel regisztrált egy alkalmazást, gyors üzembe helyezési oktatóanyagunk segítségével elsajátíthatja a használatát. Az alábbiakban néhány javaslatot olvashat:

[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]


