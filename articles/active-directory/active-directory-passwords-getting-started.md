---
title: "Első lépések: Azure AD-jelszókezelés | Microsoft Docs"
description: "Lehetővé teheti, hogy a felhasználók visszaállíthassák a saját jelszavukat, megismerheti a jelszó-visszaállítás előfeltételeit, és lehetővé teheti a jelszavak visszaírását a helyszíni jelszavak kezeléséhez az Active Directoryban."
services: active-directory
keywords: "Active Directory-jelszókezelés, jelszókezelés, Azure AD-jelszó alaphelyzetbe állítása"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: c2c46637ccccd01c1c3056d6a25ef605cfd68f2d
ms.lasthandoff: 03/28/2017


---
# <a name="getting-started-with-password-management"></a>A jelszókezelés első lépései
> [!IMPORTANT]
> **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

Csupán néhány egyszerű lépéssel lehetővé teheti, hogy a felhasználók kezelhessék a saját felhőalapú Azure Active Directory- vagy helyszíni Active Directory-jelszavaikat. Miután ellenőrizte, hogy megfelel-e néhány egyszerű előfeltételnek, a jelszómódosítás és -visszaállítás hamar engedélyezhetővé tehető a teljes szervezet számára. Ez a cikk a következő fogalmakat ismerteti:

* [**Kezdés előtt: az ügyfeleinktől származó legjobb tippek**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**LEGJOBB TIPP: NAVIGÁLÁS A DOKUMENTÁCIÓBAN** – Válaszok keresése a tartalomjegyzékkel és a böngésző keresés funkciójával](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**1. tipp: LICENCEK** – Győződjön meg arról, hogy megértette a licencelési követelményeket](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**2. tipp: TESZTELÉS** – Végfelhasználóval teszteljen, ne rendszergazdával, és kis felhasználócsoporttal kísérletezzen](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**3. tipp: ÜZEMBE HELYEZÉS** – Töltse ki előre az adatokat a felhasználók számára, hogy ne kelljen regisztrálniuk](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**4. tipp: ÜZEMBE HELYEZÉS** – A jelszavak visszaállításával kerülje el az ideiglenes jelszavakkal kapcsolatos kommunikáció bonyodalmait](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**5. tipp: VISSZAÍRÁS** – Tekintse meg az alkalmazás eseménynaplóját az AAD Connect gépen a jelszóvisszaírás hibáinak elhárítása érdekében](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**6. tipp: VISSZAÍRÁS** – Győződjön meg arról, hogy a megfelelő engedélyeket, tűzfalszabályokat és kapcsolati beállításokat engedélyezte a jelszóvisszaíráshoz](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**7. tipp: JELENTÉSKÉSZÍTÉS** – Tekintse meg az Azure AD SSPR auditnaplókkal, hogy ki regisztrál vagy állít vissza jelszavakat](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**8. tipp: HIBAELHÁRÍTÁS** – A hibaelhárítással kapcsolatos útmutató és a gyakori kérdések sok hiba elhárításához nyújthatnak segítséget](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**9. tipp: HIBAELHÁRÍTÁS** – Ha további segítségre van szüksége, adjon meg elég információt ahhoz, hogy valóban segíthessünk](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**Új Azure Active Directory-jelszavak létrehozásának engedélyezése a felhasználók számára**](#enable-users-to-reset-their-azure-ad-passwords)
 * [Az önkiszolgáló jelszó-visszaállítás előfeltételei](#prerequisites)
 * [1. lépés: Jelszó-visszaállítási házirend konfigurálása](#step-1-configure-password-reset-policy)
 * [2. lépés: Kapcsolati adatok megadása a tesztfelhasználónál](#step-2-add-contact-data-for-your-test-user)
 * [3. lépés: Jelszó visszaállítása felhasználóként](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Annak engedélyezése, hogy a felhasználók visszaállíthassák vagy módosíthassák a helyszíni Azure Active Directory-jelszavaikat**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [Jelszóvisszaírás előfeltételei](#writeback-prerequisites)
 * [1. lépés: Az Azure AD Connect legújabb verziójának letöltése](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben a felhasználói felületen vagy a PowerShellen keresztül, és ellenőrzés](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall)
 * [4. lépés: A megfelelő engedélyek beállítása](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [5. lépés: Az AD-jelszó visszaállítása felhasználóként, és ellenőrzés](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>Kezdés előtt: az ügyfeleinktől származó legjobb tippek
Az alábbiakban olyan tippeket sorolunk fel, amelyek hasznosak lehetnek azon ügyfelek számára, akik egy cégen vagy más jellegű szervezeten belül helyeznek üzembe jelszókezelési megoldásokat.

* [**LEGJOBB TIPP: NAVIGÁLÁS A DOKUMENTÁCIÓBAN** – Válaszok keresése a tartalomjegyzékkel és a böngésző keresés funkciójával](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**1. tipp: LICENCEK** – Győződjön meg arról, hogy megértette a licencelési követelményeket](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**2. tipp: TESZTELÉS** – Végfelhasználóval teszteljen, ne rendszergazdával, és kis felhasználócsoporttal kísérletezzen](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**3. tipp: ÜZEMBE HELYEZÉS** – Töltse ki előre az adatokat a felhasználók számára, hogy ne kelljen regisztrálniuk](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**4. tipp: ÜZEMBE HELYEZÉS** – A jelszavak visszaállításával kerülje el az ideiglenes jelszavakkal kapcsolatos kommunikáció bonyodalmait](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**5. tipp: VISSZAÍRÁS** – Tekintse meg az alkalmazás eseménynaplóját az AAD Connect gépen a jelszóvisszaírás hibáinak elhárítása érdekében](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**6. tipp: VISSZAÍRÁS** – Győződjön meg arról, hogy a megfelelő engedélyeket, tűzfalszabályokat és kapcsolati beállításokat engedélyezte a jelszóvisszaíráshoz](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**7. tipp: JELENTÉSKÉSZÍTÉS** – Tekintse meg az Azure AD SSPR auditnaplókkal, hogy ki regisztrál vagy állít vissza jelszavakat](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**8. tipp: HIBAELHÁRÍTÁS** – A hibaelhárítással kapcsolatos útmutató és a gyakori kérdések sok hiba elhárításához nyújthatnak segítséget](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**9. tipp: HIBAELHÁRÍTÁS** – Ha további segítségre van szüksége, adjon meg elég információt ahhoz, hogy valóban segíthessünk](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>LEGJOBB TIPP: NAVIGÁLÁS A DOKUMENTÁCIÓBAN – Válaszok keresése a tartalomjegyzékkel és a böngésző keresés funkciójával
Ha valamelyik dokumentációnkat használja, érdemes áttekinteni a tartalomjegyzéket, mert igyekeztünk megadni benne a rendszergazdák érdeklődésére számot tartó összes témakör gyorshivatkozását. 

Tekintse meg az alábbi tartalomjegyzéket: 
* [Azure AD jelszó-visszaállítás: A dokumentáció tartalomjegyzéke](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>1. tipp: LICENCEK – Győződjön meg arról, hogy megértette a licencelési követelményeket
Az Azure AD jelszó-visszaállítás működéséhez legalább egy licencnek a céghez vagy szervezethez rendelve kell lennie. Magához a jelszóvisszaállítási művelethez nem tesszük kötelezővé a felhasználónkénti licencelést, de ha úgy használja a funkciót, hogy nincs licenc rendelve egy felhasználóhoz, nem fog megfelelni a Microsoft licencszerződésének, és licenceket kell rendelnie a felhasználókhoz.

Az alábbi dokumentumok segíthetnek megérteni, milyen licencekre van szükség a jelszó-visszaállításhoz.
* [Általános jelszó-visszaállítási licencelési információk](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-customize#what-customization-options-are-available)
* [Funkciónkénti jelszó-visszaállítási licencelési információk](https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability)
* [A jelszóvisszaíráshoz támogatott alkalmazási helyzetek](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#scenarios-supported-for-password-writeback)

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>2. tipp: TESZTELÉS – Végfelhasználóval teszteljen, ne rendszergazdával, és kis felhasználócsoporttal kísérletezzen
Ha rendszergazdával tesztel, a rendszergazdai jelszóvisszaállítási szabályzat lesz érvényben, amelynek a lényegét az alábbiakban foglaljuk össze.  Ez azt jelenti, hogy NEM a végfelhasználókhoz konfigurált szabályzat várt eredményei fognak megjelenni.

A rendszergazdai felületen konfigurált szabályzatok CSAK a végfelhasználókra érvényesek, a rendszergazdákra nem. A Microsoft alapértelmezés szerint szigorú jelszóvisszaállítási szabályzatot ír elő a rendszergazdáknak – amely különbözhet a végfelhasználókhoz beállított szabályzatoktól –, hogy a cég vagy szervezet biztonsága ne sérüljön.

#### <a name="administrator-password-reset-policy"></a>Rendszergazdai jelszóvisszaállítási szabályzat
* **Érvényességi kör** – bármely rendszergazdai szerepkör (globális rendszergazda, ügyfélszolgálati rendszergazda, jelszórendszergazda stb.)
* **Egy kezdő szabályzat érvényes...**
 * ...a próbaverzió elindítása/létrehozása utáni első 30 napban **VAGY**
 * ...amikor nincs jelen személyes tartomány, **ÉS** az Azure AD Connect nem szinkronizál identitásokat.
 * **_A használatához szükséges_**: **egy** olyan hitelesítő e-mail-cím, másodlagos e-mail-cím, hitelesítő telefonszám, mobiltelefonszám vagy irodai telefonszám, amelynek van értéke.
* **Két kezdő szabályzat érvényesül...** 
 * ...a próbaverzió első 30 napjának eltelte után **VAGY**
 * ...amikor jelen van egy személyes tartomány **VAGY** 
 * ...ha engedélyezte, hogy az Azure AD Connect szinkronizálja az identitásokat a helyszíni környezetből.
 * _**A használatához szükséges**_: az értékkel rendelkező hitelesítő e-mail-cím, másodlagos e-mail-cím, hitelesítő telefonszám, mobiltelefonszám vagy irodai telefonszám közül kiválasztott **két** elem.

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>3. tipp: ÜZEMBE HELYEZÉS – Töltse ki előre az adatokat a felhasználók számára, hogy ne kelljen regisztrálniuk
Sokan nem tudják, hogy a felhasználóknak nem kell regisztrálniuk a jelszó-visszaállításra a funkció használatához.  Ha előre beállítja a felhasználók számára a telefonra és e-mailre vonatkozó tulajdonságokat, azonnal elérhetővé teheti a jelszó-visszaállítást a teljes cégben vagy szervezetben **anélkül, hogy a felhasználóknak bármit is tenniük kellene.**

Az alábbi dokumentáció azt ismerteti, hogyan teheti meg ezt API-k, a PowerShell vagy az Azure AD Connect használatával:
* [Jelszó-visszaállítás üzembe helyezése anélkül, hogy a felhasználóknak regisztrálniuk kellene](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [A jelszó-visszaállítás által használt adatok](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>4. tipp: ÜZEMBE HELYEZÉS – A jelszavak visszaállításával kerülje el az ideiglenes jelszavakkal kapcsolatos kommunikáció bonyodalmait
Ez a 3. tipphez kapcsolódik. Miután előre konfigurálta a felhasználókat a jelszó-visszaállításhoz, képzeljen el egy olyan esetet, ahol egy alkalmazott először csatlakozik a vállalatához. Ahelyett, hogy adna neki egy ideiglenes jelszót, átirányíthatja az [Azure AD jelszóvisszaállítási portálra](https://passwordreset.microsoftonline.com), hogy visszaállítsa a jelszavát.

Ha a felhasználó [Windows 10 Azure AD tartományhoz csatlakozó eszközt](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy) használ, a visszaállítást közvetlenül a Windows 10 kezdő bejelentkezési képernyőjén is végrehajthatja, így egy teljesen új számítógépet érhet el anélkül, hogy Önnek bármit is tennie kellene.

Az alábbi dokumentáció azt ismerteti, hogyan teheti meg ezt API-k, a PowerShell vagy az Azure AD Connect használatával. Miután előre kitölti az adatokat, csak irányítsa át a felhasználókat a jelszavuk visszaállításához, és azonnal hozzáférhetnek a fiókjukhoz:
* [Jelszó-visszaállítás üzembe helyezése anélkül, hogy a felhasználóknak regisztrálniuk kellene](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [A jelszó-visszaállítás által használt adatok](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>5. tipp: VISSZAÍRÁS – Tekintse meg az alkalmazás eseménynaplóját az AAD Connect gépen a jelszóvisszaírás hibáinak elhárítása érdekében
Az Azure AD Connect alkalmazás eseménynaplója sokféle naplózott információt tartalmaz, amelyek a jelszóvisszaíró szolgáltatás működését valós időben részletezik. A napló eléréséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az **Azure AD Connect** gépre.
2. A **Windows eseménynapló** megnyitásához kattintson a **Start** gombra, és írja be az **„Eseménynapló”** kifejezést.
3. Nyissa meg az **Alkalmazás** eseménynaplót.
4. Az esetlegesen előforduló problémákra vonatkozó további információért keressen a következő forrásokból származó eseményeket: **PasswordResetService** vagy **ADSync**.

Azon események teljes listájáért, amelyek szerepelhetnek a naplóban, valamint a jelszóvisszaíróval kapcsolatos további hibaelhárítási útmutatásért lásd:
* [A jelszóvisszaíró hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [A visszaíró eseménynaplójának hibakódjai](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [A jelszóvisszaíró csatlakoztatási problémáinak hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [A visszaíró üzembe helyezése – 3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall)
* [A visszaíró üzembe helyezése – 4. lépés: A megfelelő engedélyek beállítása](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>6. tipp: VISSZAÍRÁS – Győződjön meg arról, hogy a megfelelő engedélyeket, tűzfalszabályokat és kapcsolati beállításokat engedélyezte a jelszóvisszaíráshoz
A visszaírás megfelelő működéséhez biztosítania kell a következőket:

1. A megfelelő **Active Directory-engedélyeket** kell beállítani a jelszóvisszaírót használó felhasználókhoz, hogy jogosultak legyenek a saját jelszavuk és a fiókjuk zárolásfeloldó jelzőinek módosítására az AD-ben.
2. A megfelelő **tűzfalportoknak** kell nyitva lenniük, hogy a jelszóvisszaíró szolgáltatás biztonságosan kommunikálhasson a külvilággal egy kimenő kapcsolaton keresztül.
3. A megfelelő **tűzfalkivételeket** kell beállítani a jelszóvisszaállítási szolgáltatás főbb URL-címeihez, például a Service Bushoz.
4. A **proxy és a tűzfal nem törölheti a tétlen kimenő kapcsolatokat**: legalább 10 perces beállítást javasolunk.

A hibaelhárítási útmutatók teljes listájáért és a jelszóvisszaíró engedélyei és tűzfalszabályai konfigurálásával kapcsolatos útmutatásért lásd:
* [A jelszóvisszaíró hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [A visszaíró eseménynaplójának hibakódjai](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [A jelszóvisszaíró csatlakoztatási problémáinak hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [A visszaíró üzembe helyezése – 3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall)
* [A visszaíró üzembe helyezése – 4. lépés: A megfelelő engedélyek beállítása](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>7. tipp: JELENTÉSKÉSZÍTÉS – Tekintse meg az Azure AD SSPR auditnaplókkal, hogy ki regisztrál vagy állít vissza jelszavakat 
Ha a jelszó-visszaállítás üzembe van helyezve és működik, a következő logikus lépés működésének áttekintése és annak elemzése, hogy kinek kell még regisztrálnia, melyek a visszaállítás során a felhasználók által tapasztalt általános problémák, és a befektetés milyen mértékű megtérülésével lehet számolni.

Az Azure AD jelszó-visszaállítási auditnaplókkal ezeket és sok más műveletet is végrehajthat az Azure Portalon, a PowerBI-ban, az Azure AD Reporting Events API-val vagy a PowerShell segítségével.  A jelentéskészítési funkciók használatára vonatkozó további információért lásd:
* [Jelszókezelési jelentések áttekintése](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [A jelszókezelési jelentések megtekintése az Azure Portalon](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Önkiszolgáló jelszókezelési tevékenységtípusok az Azure Portalon](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Jelszókezelési események lekérése az Azure AD-jelentések és -események API-jából](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [Jelszóvisszaállítási regisztrációs események gyors letöltése a PowerShell használatával](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>8. tipp: HIBAELHÁRÍTÁS – A hibaelhárítással kapcsolatos útmutató és a gyakori kérdések sok hiba elhárításához nyújthatnak segítséget
Tudta, hogy a jelszó-visszaállításra rengeteg hibaelhárítási útmutató és egy gyakori kérdéseket tartalmazó szakasz is vonatkozik? Ha kérdése van, akkor nagy a valószínűsége, hogy a következő hivatkozásokon megtalálja rá a választ.

Ezenkívül az [Azure Portal](https://portal.azure.com) **„Támogatás és hibaelhárítás”** panelén is megtalálja a hibaelhárítási tartalmak gazdag választékát, amely a bal oldali navigációs ablaktáblán lévő **Azure Active Directory** -> **Felhasználók és csoportok** -> **Jelszó-visszaállítás** -> **Támogatás és hibaelhárítás** területen található jelszókezelési rendszergazdai felhasználói felületen érhető el.

A jelszóvisszaállítási útmutatókra és gyakori kérdésekre mutató hivatkozások:
* [A jelszókezelés hibaelhárítása](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [Jelszókezelés GYIK](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>9. tipp: HIBAELHÁRÍTÁS – Ha további segítségre van szüksége, adjon meg elég információt ahhoz, hogy valóban segíthessünk
Ha további segítségre van szüksége a hibaelhárításhoz, rendelkezésére állunk. Megnyithat egy támogatási esetet, vagy közvetlenül a fiókkezelő csapattal is kapcsolatba léphet. Forduljon hozzánk bizalommal.

Mielőtt azonban kapcsolatba lépne velünk, **győződjön meg arról, hogy beszerezte az alább kért összes információt**, hogy minél gyorsabban a segítségére lehessünk!
* [A segítségkéréshez mellékelendő információk](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>A jelszó-visszaállítással kapcsolatos visszajelzések
* [Szolgáltatással kapcsolatos kérések vagy hibaelhárítás – Közzététel az Azure AD MSDN fórumokon](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [Szolgáltatással kapcsolatos kérések vagy hibaelhárítás – Közzététel a StackOverflow-n](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [Szolgáltatással kapcsolatos kérések vagy hibaelhárítás – Twitter-üzenet küldése @azuread-nek](https://twitter.com/azuread)
* [Csak a szolgáltatással kapcsolatos kérések – Megjegyezés írása a UserVoice-on](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Annak engedélyezése, hogy a felhasználók visszaállíthassák az Azure AD-jelszavaikat
Ez a szakasz végigvezeti az önkiszolgáló jelszó-visszaállítás az AAD a felhőalapú címtárához történő engedélyezésén, a felhasználók önkiszolgáló jelszó-visszaállításhoz történő regisztrálásán, majd végül tesztelési célból egy önkiszolgáló jelszó-visszaállítás felhasználóként történő elvégzésén.

* [Az önkiszolgáló jelszó-visszaállítás előfeltételei](#prerequisites)
* [1. lépés: Jelszó-visszaállítási házirend konfigurálása](#step-1-configure-password-reset-policy)
* [2. lépés: Kapcsolati adatok megadása a tesztfelhasználónál](#step-2-add-contact-data-for-your-test-user)
* [3. lépés: Jelszó visszaállítása felhasználóként](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Előfeltételek
Mielőtt önkiszolgáló jelszó-visszaállítást engedélyezhetne és használhatna, eleget kell tennie a következő előfeltételeknek:

* Hozzon létre egy AAD-bérlőt. További információ: [Ismerkedés az Azure AD szolgáltatással](https://azure.microsoft.com/trial/get-started-active-directory/)
* Szerezzen be egy Azure-előfizetést. További információért lásd: [Mi az az Azure AD-bérlő?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* Társítsa az AAD-bérlőt az Azure-előfizetéshez. További információkért lásd: [How Azure subscriptions are associated with Azure AD?](https://msdn.microsoft.com/library/azure/dn629581.aspx) (Hogyan kapcsolódnak az Azure-előfizetések az Azure AD-hoz?).
* Frissítsen az Azure AD Prémium vagy Alapszintű kiadásra, vagy használjon fizetős O365-licencet. További információk: [Azure Active Directory editions](https://azure.microsoft.com/pricing/details/active-directory/) (Azure Active Directory-kiadások).

  > [!NOTE]
  > Ha engedélyezni szeretné az önkiszolgáló jelszó-visszaállítást a felhőbeli felhasználóinak, frissítenie kell az Azure AD Prémium, Azure AD Alapszintű vagy egy fizetős O365-licencre.  Az önkiszolgáló jelszó-visszaállítás helyszíni felhasználók számára történő engedélyezéséhez frissítenie kell az Azure AD Prémium kiadásra. További információk: [Azure Active Directory editions](https://azure.microsoft.com/pricing/details/active-directory/) (Azure Active Directory-kiadások). Az alábbi információkban megtalálja az Azure AD Prémium vagy Alapszintű kiadásra történő regisztráció, a licenccsomag aktiválásának és az Azure AD-hozzáférés aktiválásának, valamint a hozzáférés rendszergazdákhoz és felhasználói fiókokhoz történő hozzárendelésének részletes útmutatásait.
  >
  >
* Hozzon létre legalább egy rendszergazdai fiókot és egy felhasználói fiókot az AAD-címtárában.
* Rendeljen egy AAD Prémium, Alapszintű vagy egy fizetős O365-licencet a létrehozott rendszergazdai és felhasználói fiókhoz.

### <a name="step-1-configure-password-reset-policy"></a>1. lépés: Jelszó-visszaállítási házirend konfigurálása
Felhasználói jelszó-visszaállítási házirend konfigurálásához végezze el a következő lépéseket:

1. Nyisson meg egy böngészőt, és nyissa meg a [klasszikus Azure portált](https://manage.windowsazure.com).
2. A [klasszikus Azure portálon](https://manage.windowsazure.com) keresse meg az **Active Directory-bővítményt** a bal oldali navigációs sávon.

   ![Jelszókezelés az Azure AD-ben][001]
3. A **Címtár** lapon kattintson arra a címtárra, amelyben konfigurálni szeretné a jelszó-visszaállítási házirendet, például a Wingtip Toys címtárra.

    ![][002]
4. Kattintson a **Configure** (Konfigurálás) lapra.

   ![][003]

5. A **Konfigurálás** lapon görgessen le a **felhasználói jelszó-visszaállítási házirend** szakaszhoz.  Itt konfigurálhatja az adott címtárak jelszó-visszaállítási házirendjének minden szempontját. *Ha nem látja a Konfigurálás lapot, ellenőrizze, hogy regisztrált-e a Prémium szintű vagy Alapszintű Azure Active Directoryra, és hogy __hozzárendelt-e egy licencet__ a funkciót konfiguráló rendszergazdai fiókhoz.*  

   > [!NOTE]
   > **A beállított szabályzat csak a szervezet végfelhasználóira érvényes, a rendszergazdákra nem**. Biztonsági okokból a Microsoft kezeli a rendszergazdák jelszó-visszaállítási házirendjét. A rendszergazdák aktuális szabályzatához két lépés szükséges: mobiltelefon és e-mail-cím.

   ![][004]
6. A felhasználói jelszó-visszaállítási házirend konfigurálásához állítsa a **jelszó-visszaállításhoz engedélyezett felhasználók** kapcsolót az **igen** beállításra.  Ez több más vezérlőt is megjelenít, amelyekkel konfigurálhatja a funkció működésének módját a címtárban.  Nyugodtan szabja testre a jelszó-visszaállítást, ahogyan szeretné.  Ha további információt szeretne megtudni arról, hogy mire szolgálnak az egyes jelszó-visszaállítási házirendek, lásd: [Testreszabás: Azure AD-jelszókezelés](active-directory-passwords-customize.md).

   ![][005]
7. A bérlő felhasználói jelszó-visszaállítási házirendjének kívánt módon történő konfigurálása után kattintson a képernyő alján lévő **Mentés** gombra.

   > [!NOTE]
   > Két ellenőrző felhasználói jelszó-visszaállítási házirend ajánlott, hogy láthassa, hogyan működik a funkció a legösszetettebb esetben.
   >
   >

   ![][006]

   > [!NOTE]
   > **A beállított szabályzat csak a szervezet végfelhasználóira érvényes, a rendszergazdákra nem**. Biztonsági okokból a Microsoft kezeli a rendszergazdák jelszó-visszaállítási házirendjét. A rendszergazdák aktuális szabályzatához két lépés szükséges: mobiltelefon és e-mail-cím.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>2. lépés: Kapcsolati adatok megadása a tesztfelhasználónál
Több lehetősége van arra, hogyan adhatja meg a szervezetében lévő felhasználók adatait a jelszó-visszaállításhoz.

* A felhasználók szerkesztése a [klasszikus Azure portálon](https://manage.windowsazure.com) vagy az [Office 365 felügyeleti portálon](https://portal.microsoftonline.com)
* Az AAD Connect használata a felhasználói tulajdonságok a helyszíni Active Directory-tartományból az Azure AD-be történő szinkronizálásához
* Felhasználói tulajdonságok szerkesztése a Windows PowerShell használatával
* Ha lehetővé szeretné tenni, hogy a felhasználók regisztrálhassák a saját adataikat, irányítsa őket a [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) címen elérhető regisztrációs portálra
* Ha azt szeretné, hogy a felhasználóknak regisztrálniuk kelljen a jelszó-visszaállításhoz, amikor bejelentkeznek a Hozzáférési panelre a [http://myapps.microsoft.com](http://myapps.microsoft.com) címen, állítsa **Felhasználói regisztráció megkövetelése a hozzáférési panelen történő bejelentkezéskor** SSPR-konfigurációs beállítást **Igen** értékre.

Ha további információt szeretne megtudni arról, hogy milyen adatokat használ a jelszóvisszaállítás, valamint az adatok formázási követelményeiről, lásd: [Milyen adatokat használ a jelszóvisszaállítás?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Felhasználói kapcsolati adatok hozzáadása a Felhasználói regisztrációs portálon
1. A jelszó-visszaállítási portál használatához meg kell adnia a szervezetében lévő felhasználóknak ezen oldal hivatkozását ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)), vagy be kell kapcsolnia a felhasználók automatikus regisztrációját megkövetelő beállítást.  Amikor erre a hivatkozásra kattintanak, a rendszer megkéri őket, hogy jelentkezzenek be a szervezeti fiókjukkal.  Ezután a következő oldalt láthatják:

   ![][007]
2. Itt a felhasználók megadhatják és ellenőriztethetik a mobiltelefonjukat, másodlagos e-mail-címüket vagy biztonsági kérdéseiket.  Így néz ki egy mobiltelefon ellenőriztetése.

   ![][008]
3. Miután a felhasználó megadja ezeket az információkat, az oldal frissül, hogy jelezze az információk érvényességét (az alábbiakban ezt kitakartuk).  Ha a **Befejezés** vagy a **Mégse** gombra kattint, a felhasználó a Hozzáférési panelre kerül.

   ![][009]
4. Amikor a felhasználó ellenőriztette mindkét információt, frissül a profilja a megadott adatokkal.  Ebben a példában az **Irodai telefonszám** adatait módosították manuálisan, hogy a felhasználó ezt a kapcsolattartási módot is használhassa a jelszó visszaállítására.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>3. lépés: Az AzureAD-jelszó visszaállítása felhasználóként
Most, hogy konfigurált egy felhasználói visszaállítási házirendet, és megadta a felhasználó kapcsolattartási adatait, ez a felhasználó önkiszolgáló jelszó-visszaállítást végezhet.

#### <a name="to-perform-a-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás elvégzése
1. Ha például a [**portal.microsoftonline.com**](http://portal.microsoftonline.com) oldalra lép, az alábbihoz hasonló bejelentkezési képernyőt lát.  Kattintson a **Nem tudja elérni a fiókját?** hivatkozásra a jelszó-visszaállítási felhasználói felület teszteléséhez.

   ![][011]
2. Miután a **Nem tudja elérni a fiókját?** hivatkozásra kattint, egy új lapra kerül, amely bekéri azt a **felhasználói azonosítót**, amelyhez vissza szeretné állítani a jelszót.  Írja be ide a **felhasználói azonosítót**, teljesítse a captcha-tesztet, majd kattintson a **tovább** gombra.

   ![][012]
3. Mivel a felhasználó ebben az esetben **irodai telefonszámot**, **mobiltelefonszámot** és **másodlagos e-mail-címet** adott meg, láthatja, hogy mindezek lehetőségként megjelennek az első ellenőrzésnél.

   ![][013]
4. Ebben az esetben először válassza az **irodai telefonszám** **felhívását**.  Vegye figyelembe, hogy amikor telefonalapú módszert választ, a rendszer felkéri a felhasználókat **a telefonszámuk megerősítésére**, mielőtt visszaállíthatnák a jelszavukat.  Ez azért van, hogy a rosszindulatú támadók ne élhessenek vissza a szervezetben lévő felhasználók telefonszámaival.

   ![][014]
5. Amikor a felhasználó megerősítette a telefonszámát, a hívás gombra kattintva megjelenik egy forgó ábra, és elkezd csengeni a telefonja.  Amikor felveszi a telefont, egy üzenetet hall, és felkérik, hogy a **nyomja le a „#” billentyűt** a fiókja ellenőrzéséhez.  A billentyű lenyomásával automatikusan megerősíti, hogy a felhasználó teljesítette az első ellenőrzést, és a felhasználói felület a második ellenőrzési lépéssel folytatja.

   ![][015]
6. Miután teljesítette az első ellenőrzést, a felhasználói felület automatikusan frissül, hogy az már ne szerepeljen a lehetőségek listájában.  Ebben az esetben, mivel először az **Irodai telefonszámát** használta, csak a **Mobiltelefonszám** és a **Másodlagos e-mail-cím** marad lehetőségként a második ellenőrzési lépéshez.  Kattintson a **Másodlagos e-mail-cím** lehetőségre.  Miután ezt megtette, az e-mail gombra kattintva a rendszer küld egy e-mailt a tárolt másodlagos e-mail-címre.

   ![][016]
7. Itt láthat a felhasználók által megkapott e-mailekre egy példát – amelyen létható a bérlői arculat is:

   ![][017]
8. Amikor megérkezik az e-mail, az oldal frissül, és be tudja írni az e-mailben található ellenőrző adatokat az alábbi beviteli mezőbe.  A megfelelő kód beírása után aktívvá válik a tovább gomb, és teljesítheti a második ellenőrzési lépést.

   ![][018]
9. Ha teljesítette a szervezeti házirend követelményeit, új jelszót választhat.  A jelszó akkor fogadható el, ha megfelel az AAD „erős” jelszókövetelményeinek (lásd: [Jelszóházirend az Azure AD-ben](https://msdn.microsoft.com/library/azure/jj943764.aspx)), és megjelenik egy jelszóerősség-értékelő annak jelzéséhez, hogy a beírt jelszó megfelel-e a házirendnek.

   ![][019]
10. Miután megadta a szervezeti házirendnek megfelelő jelszavát, a jelszó alaphelyzetbe áll, és azonnal bejelentkezhet az új jelszavával.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Annak engedélyezése, hogy a felhasználók visszaállíthassák vagy módosíthassák az AD-jelszavaikat
Ez a szakasz a jelszó-visszaállítás konfigurálásán vezeti végig annak érdekében, hogy jelszavakat írhasson vissza a helyszíni Active Directoryba.

* [Jelszóvisszaírás előfeltételei](#writeback-prerequisites)
* [1. lépés: Az Azure AD Connect legújabb verziójának letöltése](#step-1-download-the-latest-version-of-azure-ad-connect)
* [2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben a felhasználói felületen vagy a PowerShellen keresztül, és ellenőrzés](#step-2-enable-password-writeback-in-azure-ad-connect)
* [3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall)
* [4. lépés: A megfelelő engedélyek beállítása](#step-4-set-up-the-appropriate-active-directory-permissions)
* [5. lépés: Az AD-jelszó visszaállítása felhasználóként, és ellenőrzés](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>A visszaírás előfeltételei
Mielőtt engedélyezhetné és használhatná a Jelszóvisszaírót, eleget kell tennie a következő előfeltételeknek:

* Rendelkezik egy Azure AD-bérlővel, és engedélyezve van az Azure AD Prémium.  További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).
* Ahhoz, hogy a jelszóvisszaíró működjön, az Azure AD Connectet csatlakoztatnia kell az elsődleges tartományvezérlő-emulátorhoz.  Ha szükséges, konfigurálhatja az Azure AD Connectet egy elsődleges tartományvezérlő használatára. Ehhez kattintson a jobb gombbal az Active Directory szinkronizálási összekötőjének **tulajdonságaira**, majd válassza a **könyvtárpartíciók konfigurálásának** lehetőségét. Ezután keresse meg a **tartományvezérlő kapcsolati beállításainak** szakaszát, és jelölje be a **csak az előnyben részesített tartományvezérlők használatának** jelölőnégyzetét.  Megjegyzés: ha az előnyben részesített tartományvezérlő nem egy PDC-emulátor, az Azure AD Connect akkor is a PDC-hez fordul jelszóvisszaírás céljából.
* A jelszó-visszaállítás konfigurálva van és engedélyezve van a bérlőn.  További információkért lásd: [Annak engedélyezése, hogy a felhasználók visszaállíthassák az Azure AD-jelszavaikat](#enable-users-to-reset-their-azure-ad-passwords)
* Van legalább egy rendszergazdai fiókja és egy tesztfelhasználói fiókja Azure AD Prémium licenccel, amellyel tesztelheti ezt a funkciót.  További információk: [Azure Active Directory editions](active-directory-editions.md) (Azure Active Directory-kiadások).

  > [!NOTE]
  > Győződjön meg róla, hogy a Jelszóvisszaíró engedélyezéséhez használt rendszergazdai fiók felhőalapú rendszergazdai fiók (amely az Azure AD-ben jött létre), nem pedig összevont fiók (amely helyszíni AD-ben jött létre, és az Azure AD-be lett szinkronizálva).
  >
  >
* Egyetlen vagy több erdőből álló helyszíni AD-környezettel rendelkezik, amelyik a Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 vagy Windows Server 2012 R2 rendszert futtatja a telepített legújabb szervizcsomagokkal.

  > [!NOTE]
  > Ha a Windows Server 2008 vagy 2008 R2 régebbi verzióját futtatja, továbbra is használhatja ezt a funkciót, de [le kell töltenie és telepítenie kell a KB 2386717 javítást](https://support.microsoft.com/kb/2386717), mielőtt érvénybe léptethetné a helyi AD-jelszóházirendet a felhőben.
  >
  >
* Telepítve van az Azure AD Connect eszköz, és előkészítette az AD-környezetet a felhőbe történő szinkronizáláshoz.  További információért lásd: [A helyszíni identitás-infrastruktúra használata a felhőben](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > A jelszóvisszaírás tesztelése előtt először végezzen teljes importálást és teljes szinkronizálást az AD-ből és az Azure AD-ből is az Azure AD Connectben.
  >
  >
* Ha az Azure AD Sync vagy az Azure AD Connect eszközt használja, akkor a kimenő **443-as TCP-portnak** (és néhány esetben a kimenő **9350–9354 TCP-portnak**) nyitva kell lennie.  További információért lásd: [3. lépés: A tűzfal konfigurálása](#step-3-configure-your-firewall). Már nem támogatott a DirSync használata ehhez a forgatókönyvhöz.  Ha még mindig a DirSync eszközt használja, frissítsen az Azure AD Connect legújabb verziójára a Jelszóvisszaíró telepítése előtt.

  > [!NOTE]
  > Különösen ajánlott, hogy az Azure AD Sync vagy a DirSync eszközöket használó minden felhasználó frissítsen az Azure AD Connect legújabb verziójára, hogy a lehető legjobb élményben legyen részük, és elérhetők legyenek számukra az új funkciók azok kiadásakor.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>1. lépés: Az Azure AD Connect legújabb verziójának letöltése
A Jelszóvisszaíró az Azure AD Connectben vagy az **1.0.0419.0911**-es vagy újabb verziójú Azure AD Sync eszközben érhető el.  A Jelszóvisszaíró automatikus fiókzárolás-feloldással az Azure AD Connectben vagy az **1.0.0485.0222**-es vagy újabb verziójú Azure AD Sync eszközben érhető el. Ha régebbi verziót futtat, frissítsen legalább erre a verzióra a folytatás előtt. [Kattintson ide az Azure AD Connect legújabb verziójának letöltéséhez](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Az Azure AD Sync verziójának ellenőrzése
1. Nyissa meg a **%ProgramFiles%\Azure Active Directory Sync\** könyvtárat.
2. Keresse meg a **ConfigWizard.exe** végrehajtható fájlt.
3. Kattintson a jobb gombbal a végrehajtható fájlra, és válassza ki a **Tulajdonságok** elemet a helyi menüből.
4. Kattintson a **Részletek** lapra.
5. Keresse meg a **Fájlverzió** mezőt.

   ![][021]

Ha ez a verziószám nagyobb vagy egyenlő, mint **1.0.0419.0911**, vagy ha az Azure AD Connectet telepíti, továbbléphet a [2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben a felhasználói felületen vagy a PowerShellen keresztül, és ellenőrzés](#step-2-enable-password-writeback-in-azure-ad-connect) című szakaszra.

> [!NOTE]
> Ha most telepíti először az Azure AD Connect eszközt, a figyelmébe ajánlunk néhány ajánlott eljárást, hogy előkészítse a környezetét a címtár-szinkronizálásra.  Az Azure AD Connect eszköz telepítése előtt aktiválnia kell a címtár-szinkronizálást az [Office 365 felügyeleti portálon](https://portal.microsoftonline.com) vagy a [klasszikus Azure portálon](https://manage.windowsazure.com).  További információért lásd: [Az Azure AD Connect kezelése](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>2. lépés: A jelszóvisszaírás engedélyezése az Azure AD Connectben
Most, hogy letöltötte az Azure AD Connect eszközt, készen áll a jelszóvisszaírás engedélyezésére.  Ezt az alábbi két módszer egyikével teheti meg.  Engedélyezheti a jelszóvisszaírást az Azure AD Connect telepítővarázsló választható funkciókat felsoroló képernyőjén, vagy engedélyezheti a Windows PowerShellen keresztül.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>A jelszóvisszaírás engedélyezése a konfigurációs varázslóban
1. A **Directory Sync számítógépén** nyissa meg az **Azure AD Connect** konfigurációs varázslóját.
2. Kattintsa végig a lépéseket, amíg el nem éri a **választható funkciók** konfigurációs képernyőt.
3. Jelölje be a **Jelszóvisszaírás** lehetőséget.

   ![][022]
4. Fejezze be a varázsló lépéseit, és az utolsó oldal tartalmazza a módosításokat és a jelszóvisszaírás konfigurációjának módosítását.

> [!NOTE]
> Bármikor letilthatja a jelszóvisszaírást a varázsló ismételt futtatásával és a funkció jelölésének törlésével, vagy a **Jelszavak visszaírása helyszíni címtárba** beállítás **Nem** értékre állításával a címtár **Konfigurálás** lapjának **Felhasználói jelszó-visszaállítási házirend** szakaszában a [klasszikus Azure portálon](https://manage.windowsazure.com).  További információ a jelszóvisszaállítási felület testreszabásáról: [Testreszabás: Azure AD-jelszókezelés](active-directory-passwords-customize.md).
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>A jelszóvisszaírás engedélyezése a Windows PowerShell használatával
1. A **Directory Sync számítógépén** nyisson meg egy új **emelt szintű Windows PowerShell-ablakot**.
2. Ha a modul még nincs betöltve, írja be az `import-module ADSync` parancsot, hogy az Azure AD Connect-parancsmagokat betöltse az aktuális munkamenetbe.
3. A rendszeren lévő Azure AD-összekötők listájának lekéréséhez futtassa a `Get-ADSyncConnector` parancsmagot, és tárolja az eredményeket a `$aadConnectorName` változóban, például `$aadConnectorName = Get-ADSyncConnector|where-object {$_.name -like "*AAD"}`
4. Az aktuális összekötő aktuális visszaírási állapotának lekéréséhez futtassa a következő parancsmagot:`Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. A jelszóvisszaírás engedélyezéséhez futtassa a következő parancsmagot:`Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Ha a rendszer bekéri a hitelesítő adatait, győződjön meg róla, hogy az AzureADCredential hitelesítő adatokhoz megadott rendszergazdai fiók egy **felhőalapú rendszergazdai fiók** legyen (amely az Azure AD-ben jött létre), nem pedig összevont fiók (amely helyszíni AD-ben jött létre, és az Azure AD-be lett szinkronizálva).
>
> [!NOTE]
> Letilthatja a jelszóvisszaírást a PowerShellen keresztül a fenti eljárást megismételve, csak adja meg a `$false` értéket, vagy állítsa a **Jelszavak visszaírása helyszíni címtárba** beállítást **Nem** értékre a címtár **Konfigurálás** lapjának **Felhasználói jelszó-visszaállítási házirend** szakaszában a [klasszikus Azure portálon](https://manage.windowsazure.com).
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Ellenőrizze, hogy sikeres volt-e a konfiguráció
Ha a konfiguráció sikeres, a jelszóvisszaállítás engedélyezett állapotáról szóló üzenetet lát a Windows PowerShell-ablakban, vagy a sikerről szóló üzenetet a konfigurációs felhasználói felületen.

A szolgáltatás megfelelő telepítését úgy is ellenőrizheti, ha megnyitja az Eseménynaplót, megkeresi az alkalmazás eseménynaplóját, és megkeresi a **31005 - OnboardingEventSuccess** eseményt a **PasswordResetService** forrásból.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>3. lépés: A tűzfal konfigurálása
Miután engedélyezte a jelszóvisszaírást, ellenőriznie kell, hogy az Azure AD Connectet futtató gép tud-e csatlakozni a Microsoft felhőszolgáltatásaihoz a jelszóvisszaírási kérelmek fogadásához. Ennek a lépésnek része a hálózati berendezések csatlakozási szabályainak frissítése (proxy-kiszolgálók, tűzfalak stb.), hogy engedélyezve legyenek a kimenő kapcsolatok bizonyos [Microsoft tulajdonú URL-ekhez és IP-címekhez](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US), megadott hálózati portokon keresztül. Ezek a módosítások eltérhetnek az Azure AD Connect eszköz verziójának függvényében. További információkat olvashat a [jelszóvisszaírás működéséről](active-directory-passwords-learn-more.md#how-password-writeback-works) és a [jelszóvisszaírás biztonsági modelljéről](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>Miért van erre szükség?

Annak érdekében, hogy a Jelszóvisszaíró megfelelően működjön, az Azure AD Connectet futtató gépnek képesnek kell lennie kommunikálni a jelszó-visszaállítási szolgáltatással és az Azure Service Busszal.

Az Azure AD Connect eszközök **1.1.443.0**-s és újabb verziói esetében:

- Az Azure AD Connect eszközök legújabb verziójának **kimenő HTTPS**-kapcsolatra van szüksége a következők eléréséhez:
    - *passwordreset.microsoftonline.com*
    - *servicebus.windows.net*

Az Azure AD Connect eszközök **1.0.8667.0**–**1.1.380.0** verziói esetében:

- **1. lehetőség:** Engedélyezze az összes kimenő HTTPS-kapcsolatot a 443-as porton keresztül (URL- vagy IP-cím segítségével).
    - Mikor érdemes ezt használni?
        - Akkor használja ezt a lehetőséget, ha a legegyszerűbb konfigurációra van szüksége, amelyet nem kell frissíteni, amikor az Azure Datacenter IP-tartományai módosulnak.
    - Szükséges lépések:
        - Engedélyezze az összes kimenő HTTPS-kapcsolatot a 443-as porton keresztül (URL- vagy IP-cím segítségével).
<br><br>
- **2. lehetősége:** Engedélyezze a kimenő HTTPS-kapcsolatokat megadott IP_tartományokra és URL-címekre
    - Mikor érdemes ezt használni?
        - Akkor használja ezt a lehetőséget, ha korlátozott hálózati környezetben van, vagy más miatt nem szeretné engedélyezni a kimenő kapcsolatokat.
        - Ha ebben a konfigurációban továbbra is használni szeretné a jelszóvisszaírást, biztosítania kell, hogy a hálózati berendezések hetente frissüljenek a Microsoft Azure Adatközpont IP-tartományainak legújabb IP-címeivel. Ezek az IP-tartományok egy minden szerdán (csendes-óceáni idő) frissülő, majd következő hétfőn (csendes-óceáni idő) hatályba lépő XML-fájlban érhetők el.
    - Szükséges lépések:
        - Az összes *.servicebus.windows.net címre mutató HTTPS-kapcsolat engedélyezése
        - Engedélyezze az összes kimenő HTTPS-kapcsolatot minden IP-cím felé a Microsoft Azure Datacenter IP-tartományok listában, és frissítse ezt a konfigurációt hetente. A lista letölthető [innen](https://www.microsoft.com/download/details.aspx?id=41653).

> [!NOTE]
> Ha a Jelszóvisszaírót a fenti útmutatást követve konfigurálta, és nem lát hibát az Azure AD Connect eseménynaplójában, de folyamatosan csatlakozási hibákat kap teszteléskor, akkor előfordulhat, hogy a környezetében lévő hálózati berendezés blokkolja a HTTPS-kapcsolatokat IP-címek felé. Előfordulhat például, hogy a kapcsolat engedélyezve van a *https://*.servicebus.windows.net* felé, a tartományon belül egy adott IP-cím felé mégis blokkolva van. Ennek megoldásához vagy úgy kell konfigurálnia a hálózati környezetet, hogy engedélyezze az összes kimenő HTTPS-kapcsolatot a 443-as porton keresztül bármely URL- vagy IP-cím felé (a fenti 1. lehetőség), vagy kérheti a hálózatkezelésért felelős csapat segítségét, hogy kifejezetten engedélyezzék a HTTPS-kapcsolatokat megadott IP-címek felé (a fenti 2. lehetőség).

**Régebbi verziók esetében:**

- Kimenő TCP-kapcsolatok engedélyezése a 443-as, 9350–9354-es és 5671-es porton keresztül
- Kimenő kapcsolatok engedélyezése a *https://ssprsbprodncu-sb.accesscontrol.windows.net/* címre

> [!NOTE]
> Ha az 1.0.8667.0-s Azure AD Connect verziónál korábbi verziót használ, a Microsoft erősen ajánlja, hogy frissítsen a [legújabb Azure AD Connect-verzióra](https://www.microsoft.com/download/details.aspx?id=47594), amely számos visszaírási hálózati fejlesztéssel teszi egyszerűbbé a konfigurálást.

Amint kész a hálózati berendezések konfigurálása, indítsa újra az Azure AD Connect eszközt futtató gépet.

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Tétlen kapcsolatok az Azure AD Connectben (1.1.443.0 és újabb)
Az Azure AD Connect eszköz rendszeren ping/életben tartási üzenetet küld a szolgáltatásbusz végpontjai számára, hogy biztosítsa a kapcsolat fenntartását. Ha az eszköz túl sok kapcsolat leállítását észleli, automatikusan növeli a végpont pingelésének gyakoriságát. A legalacsonyabb „pingelési időköz” 60 másodpercenként 1-re csökken, azonban **határozottan javasoljuk, hogy a proxyk/tűzfalak legalább 2-3 percig engedélyezzék a tétlen kapcsolatok fenntartását.** \*A korábbi verziók esetében 4 vagy több percet javasolunk.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>4. lépés: A megfelelő Active Directory-engedélyek beállítása
Minden erdő esetén, amely olyan felhasználókat tartalmaz, akiknek a jelszavait vissza fogja állítani, ha X a konfigurációs varázslóban (a kezdeti konfiguráció során) az erdőhöz meghatározott fiók, az X-hez meg kell adnia a **Jelszó alaphelyzetbe állítása**, **Jelszó módosítása**, **Írási engedélyek** kibővített jogot a `lockoutTime` alatt és az **Írási engedélyek** kibővített jogot a `pwdLastSet` alatt az erdőben lévő minden tartomány gyökérobjektumán VAGY az SSPR hatókörébe felvenni kívánt felhasználói szervezeti egység(ek)en.  Az utóbbi lehetőséget akkor használhatja, ha a visszaállítási engedélyeket csak a felhasználói objektumok adott készletére szeretné alkalmazni, nem pedig a tartomány gyökerén. A jognak minden felhasználói objektum által örököltként kell megjelölve lennie.  

Ha nem biztos abban, melyik fiókra utalnak a fentiek, nyissa meg az Azure Active Directory Connect konfigurációs felhasználói felületet, és kattintson **A megoldás áttekintése** elemre.  A fiók, amelyhez engedélyeket kell hozzáadnia, vörös színnel van aláhúzva az alábbi képernyőképen.

**<font color="red">Ellenőrizze, hogy beállította-e ezt az engedélyt a rendszeren lévő összes erdő minden tartományánál, különben a jelszóvisszaírás nem fog megfelelően működni.</font>**

  ![][032]

  Ezen engedélyek beállításakor az egyes erdők MA-szolgáltatásfiókja kezelheti a jelszavakat az erdőben lévő felhasználói fiókok nevében. Ha nem rendeli hozzá ezeket az engedélyeket, akkor bár a visszaírás konfigurációja megfelelőnek tűnik, a felhasználók hibákat tapasztalnak, amikor a helyszíni jelszavakat próbálják meg kezelni a felhőből. Itt láthatja ennek részletes lépéseit **Az Active Directory – felhasználók és számítógépek** felügyeleti beépülő modul használatával:

> [!NOTE]
> Akár egy órába is telhet, amíg ezen engedélyeket a címtár összes objektumába replikálja.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>A megfelelő engedélyek beállítása, hogy a visszaírás megtörténjen
1. Nyissa meg az **Active Directory – felhasználók és számítógépek** modult egy olyan fiókkal, amely megfelelő tartományi rendszergazdai engedélyekkel rendelkezik.
2. A **Nézet menüben** győződjön meg róla, hogy a **Speciális szolgáltatások** be van kapcsolva.
3. A bal oldali panelen kattintson a jobb gombbal a tartomány gyökerét jelző objektumra.
4. Kattintson a **Biztonság** lapra.
5. Ezután kattintson a **Speciális** elemre.

   ![][024]
6. Az **Engedélyek** lapon kattintson a **Hozzáadás** elemre.

   ![][025]
7. Válassza ki azt a fiókot, amelyhez engedélyeket szeretne hozzáadni (ez ugyanaz a fiók, amelyet az erdő szinkronizálásának beállításakor adott meg).
8. A felül lévő legördülő listából válassza a **Leszármazott felhasználó objektumai** elemet.
9. A megjelenő **Engedélybejegyzés** párbeszédpanelen jelölje be a **Jelszó alaphelyzetbe állítása**, **Jelszó módosítása**, **Írási engedélyek** jelölőnégyzetet a `lockoutTime` alatt, és az **Írási engedélyek** jelölőnégyzetet a `pwdLastSet` alatt.

   ![][026]
   ![][027]
   ![][028]
10. Ezután kattintson az **Alkalmaz/OK** gombra az összes megnyitott párbeszédpanelen.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>5. lépés: Az AD-jelszó visszaállítása felhasználóként
Most, hogy engedélyezett a jelszóvisszaírás, tesztelheti a működését, ha visszaállítja a felhőalapú bérlőben szinkronizált fiókkal rendelkező felhasználó jelszavát.

#### <a name="to-verify-password-writeback-is-working-properly"></a>A jelszóvisszaírás megfelelő működésének ellenőrzése
1. Nyissa meg a [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) oldalt, vagy valamely szervezeti azonosítót kérő bejelentkezési képernyőt, és kattintson a **Nem tudja elérni a fiókját?** hivatkozásra.

   ![][029]
2. Most egy új lapot kell látnia, amely bekéri azt a felhasználói azonosítót, amelyhez vissza szeretné állítani a jelszót. Írja be a tesztfelhasználó azonosítóját, és folytassa a jelszóvisszaállítási folyamatot.
3. A jelszó visszaállítása után egy ehhez hasonló képernyőt lát. Ez azt jelenti, hogy sikeresen visszaállította a jelszavát a helyszíni és/vagy a felhőbeli címtárakban.

   ![][030]
4. A művelet sikerének vagy a hibák diagnosztizálásának érdekében váltson a **Directory Sync számítógépére**, nyissa meg az **Eseménynaplót**, keresse meg az **alkalmazás eseménynaplóját**, és keresse meg a **31002 - PasswordResetSuccess** eseményt a tesztfelhasználó forrás **PasswordResetService** területén.

   ![][031]



## <a name="next-steps"></a>Következő lépések
Az alábbiakban láthatja az összes Azure AD-jelszóvisszaállítási dokumentációs oldal hivatkozását:

* **Azért van itt, mert problémák merültek fel a bejelentkezéssel kapcsolatban?** Ha igen, [így módosíthatja vagy állíthatja alaphelyzetbe a jelszavát](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Működés**](active-directory-passwords-how-it-works.md) – megismerheti a szolgáltatás hat különböző összetevőjét és azt, hogy ezek mire valók
* [**Testreszabás**](active-directory-passwords-customize.md) – megtudhatja, hogyan szabhatja testre a szervezet által igényelt szolgáltatás kezelőfelületét és működését
* [**Ajánlott eljárások**](active-directory-passwords-best-practices.md) – megtudhatja, hogyan helyezhet gyorsan üzembe és hogyan kezelhet hatékonyan jelszavakat a szervezetben
* [**Elemzések lekérése**](active-directory-passwords-get-insights.md) – megismerheti az integrált jelentéskészítési képességeinket
* [**Gyakori kérdések**](active-directory-passwords-faq.md) – választ kaphat a gyakori kérdésekre
* [**Hibaelhárítás**](active-directory-passwords-troubleshoot.md) – megtudhatja, hogyan háríthatja el a szolgáltatással kapcsolatos problémákat
* [**További információ**](active-directory-passwords-learn-more.md) – elmerülhet a szolgáltatás működésének műszaki részleteiben

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

