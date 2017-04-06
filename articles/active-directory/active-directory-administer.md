---
title: "Az Azure AD-címtár felügyelete | Microsoft Docs"
description: "Ismerteti az Azure AD-bérlő fogalmát, valamint az Azure kezelését az Azure Active Directory felületén keresztül"
services: active-directory
documentationcenter: 
author: curtand
writer: markvi
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 001ffc0f9c7465552392a9848ef1487a4c0eafce
ms.lasthandoff: 12/07/2016


---
# <a name="administer-your-azure-ad-directory"></a>Az Azure AD-címtár felügyelete
## <a name="what-is-an-azure-ad-tenant"></a>Mi az az Azure AD-bérlő?
Fizikai munkahelyek esetében a bérlő egy épületet elfoglaló csoportot vagy vállalatot takar. A szervezet például rendelkezhet saját irodával egy épületben. Lehetséges, hogy ez az épület több más szervezet épületével egy utcában van. A szervezet ilyenkor az adott épület bérlőjének tekinthető. Az épület a szervezet birtokában van, biztonságot nyújt, és garantálja a biztonságos üzletmenethez szükséges körülményeket. Az utcában található egyéb üzleti szervezetektől is elkülönül. Így biztosítható, hogy a szervezet és annak vagyontárgyai más szervezetekével ne keveredjenek.

Felhőalapú munkahelyek esetén a bérlő olyan ügyfelet vagy szervezetet jelent, amely a felhőszolgáltatás adott példányát birtokolja és kezeli. A Microsoft Azure által biztosított identitásplatformok esetében a bérlő egyszerűen az Azure Active Directory (Azure AD) olyan dedikált példányának tekinthető, amelyet a szervezet megkap és a tulajdonában áll, amikor regisztrál egy Microsoft felhőszolgáltatásra, például az Azure-ra vagy az Office 365-re.

Mindegyik Azure AD-címtár önálló, és el van választva a többi Azure AD-címtártól. Ahogy a vállalat irodaépülete is egy vállalatspecifikus biztonsági vagyontárgynak tekinthető, az Azure AD-címtár is egy, kizárólag az adott szervezet általi használatra kialakított biztonságos eszköz. Az Azure AD architektúrájával megakadályozható az ügyfél- és identitásadatok keveredése. Ez azt jelenti, hogy az adott Azure AD-címtár felhasználói és rendszergazdái véletlenül vagy kártételi szándékkal nem férhetnek hozzá más címtárak adataihoz.

![Az Azure Active-címtár kezelése][1]

## <a name="how-can-i-get-an-azure-ad-directory"></a>Hogyan juthatok Azure AD-címtárhoz?
Az Azure AD alapvető fontosságú címtár- és identitáskezelési funkciókat biztosít a Microsoft olyan felhőszolgáltatásai számára, mint például a következők:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Azure AD-címtárhoz úgy juthat, ha regisztrál bármelyik fenti Microsoft felhőszolgáltatásra. Szükség szerint több címtárat is létrehozhat. Az első címtárat fenntarthatja például éles címtárként, majd létrehozhat egy másik címtárat is tesztelési vagy előkészítési céllal.

> [!NOTE]
> Miután regisztrált az első szolgáltatásra, javasoljuk, hogy a további Microsoft felhőszolgáltatásokra történő regisztrációkor ugyanazt a szervezetéhez tartozó rendszergazdai fiókot használja.
> 
> 

Amikor első alkalommal regisztrál Microsoft felhőszolgáltatásra, a rendszer felkéri a szervezet alapadatainak és internetes tartománynév-regisztrációs adatainak megadására. Ezek az információk az adott szervezet új Azure AD-címtárpéldányának létrehozásához szükségesek. Ha több Microsoft felhőszolgáltatásra fizet elő, a bejelentkezési kísérletek hitelesítésére a rendszer ugyanazt a címtárat használja.

A további szolgáltatások teljes mértékben kihasználják a meglévő felhasználói fiókok, házirendek, beállítások vagy az Ön által konfigurált helyszíni címtár-integráció adta lehetőségeket a szervezet helyszíni identitás-infrastruktúrája és az Azure AD közötti kommunikáció hatékonyabbá tételéhez.

Ha például eredetileg egy Microsoft Intune-előfizetésre iratkozott fel, és végrehajtotta a helyszíni Active Directory és a saját Azure AD-címtárának további integrálásához szükséges lépéseket címtár-szinkronizálási és/vagy bejelentkezési kiszolgálók üzembe helyezésével, olyan további Microsoft felhőszolgáltatásokra iratkozhat fel, mint például az Office 365, amely szintén a Microsoft Intune esetében már alkalmazott címtár-integrációs lehetőségeket használja ki.

További információk a helyszíni címtár Azure AD-integrációjáról: [Directory integration](active-directory-aadconnect.md)(Címtár-integráció).

### <a name="associate-an-azure-ad-directory-with-a-new-azure-subscription"></a>Új Azure-előfizetés társítása Azure AD-címtárhoz
Új Azure-előfizetést társíthat ugyanahhoz a címtárhoz, amely egy meglévő Office 365- vagy Microsoft Intune-előfizetés bejelentkezésének hitelesítését végzi. Jelentkezzen be az Azure felügyeleti portálra munkahelyi vagy iskolai fiókjával. A felügyeleti portál által megjelenített üzenetben az szerepel, hogy az adott fiókhoz nem található előfizetés. Ahhoz, hogy a címtárat kezelni tudja a portálon, válassza a **Sign Up For Azure** (Feliratkozás az Azure szolgáltatásra) lehetőséget. További információk: [Manage the directory for your Office 365 subscription in Azure](active-directory-how-subscriptions-associated-directory.md#manage-the-directory-for-your-office-365-subscription-in-azure) (Office 365-előfizetéshez tartozó címtár kezelése az Azure-ban).

Videó az Azure AD használatával kapcsolatban gyakran felmerülő kérdésekről: [Azure Active Directory - Common Sign-up, sign-in and usage questions](http://channel9.msdn.com/Series/Windows-Azure-Active-Directory/WAADCommonSignupsigninquestions) (Azure Active Directory – Gyakori kérdések a feliratkozásról, a bejelentkezésről és a használatról).

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Azure AD-címtár létrehozása szervezeti feliratkozással egy Microsoft felhőszolgáltatásra
Ha még nem rendelkezik előfizetéssel egyetlen Microsoft felhőszolgáltatáshoz sem, a feliratkozáshoz használja az alábbi hivatkozások egyikét. Amikor először iratkozik fel egy ilyen szolgáltatásra, az Azure AD-címtár automatikusan létrejön.

* [Microsoft Azure](https://account.windowsazure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://account.manage.microsoft.com/Signup/MainSignUp.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&ali=1)

### <a name="manage-an-azure-provisioned-default-directory"></a>Azure által kiosztott alapértelmezett címtár kezelése
A címtár az Azure szolgáltatásra történő feliratkozással együtt, automatikusan létrejön, és az előfizetése ehhez a címtárhoz lesz társítva. Ha azonban még 2013 októbere előtt iratkozott fel az Azure szolgáltatásra, a címtár nem jött létre automatikusan. Ebben az esetben előfordulhat, hogy az Azure „visszatöltötte” a fiókját úgy, hogy kiosztott hozzá egy alapértelmezett címtárat. Az előfizetése innentől ehhez az alapértelmezett címtárhoz van társítva.

A címtárak visszatöltésére 2013 októberében, az Azure biztonsági modell átfogó fejlesztésének részeként került sor. Ennek segítségével az összes Azure-ügyfél számára biztosíthatók szervezetiidentitás-szolgáltatások, és arról is gondoskodik, hogy az összes Azure-erőforrás hozzáférése címtárfelhasználói szinten történjen meg. Címtár nélkül az Azure rendszer nem használható. Ezért a 2013. július 7. előtt feliratkozott, címtárral nem rendelkező felhasználóknak létre kell hozniuk egyet. Amennyiben már hozott létre címtárat, előfizetése ehhez a címtárhoz van társítva.

Az Azure AD használata ingyenes. A címtár egy ingyenes erőforrás. A Prémium szintű Azure Active Directory licencelése külön történik, és olyan további szolgáltatásokat biztosít, mint például a vállalati arculat hozzáadása és az önkiszolgáló jelszó-visszaállítás.

A címtár megjelenített nevének módosításához kattintson a portálon a címtárra, majd a **Konfigurálás** lehetőségre. Ahogy a jelen témakör későbbi részében olvasható, új címtárat is felvehet, illetve törölheti azt, amelyikre már nincs szüksége. Ha az előfizetést másik címárhoz szeretné társítani, a bal oldali navigációs ablaktáblán kattintson a **Settings** (Beállítások) elemre, majd a **Subscriptions** (Előfizetések) oldal alján kattintson az **Edit Directory** (Címtár szerkesztése) gombra. Egyéni tartományt is létrehozhat az alapértelmezett *.onmicrosoft.com tartomány helyett egy regisztrált DNS-név használatával, amely a SharePoint Online-hoz hasonló szolgáltatások esetében célravezetőbb megoldás lehet.

## <a name="how-can-i-manage-directory-data"></a>Hogyan történik a címtáradatok kezelése?
Egy vagy több Microsoft felhőszolgáltatás-előfizetés rendszergazdájaként a szervezet címtáradatainak kezeléséhez az Azure felügyeleti portált, a Microsoft Intune fiókportált vagy az Office 365 felügyeleti központját használhatja. Az Azure AD-ban tárolt adatok kezelését megkönnyíti a [Windows PowerShellhez készült Microsoft Azure Active Directory-modul](https://msdn.microsoft.com/library/azure/jj151815.aspx) parancsmagok letöltése és futtatása.

E portálok (vagy parancsmagok) a következőket teszik lehetővé:

* Felhasználói és csoportfiókok létrehozása és kezelése
* A szervezet által előfizetett, kapcsolódó felhőszolgáltatás(ok) kezelése
* Helyszíni integráció beállítása a címtárszolgáltatással

Az Azure felügyeleti portál, az Office 365 felügyeleti központ, a Microsoft Intune fiókportál és az Azure AD-parancsmagok mind egyetlen, a szervezet címtárához társított megosztott Azure AD-példányba írnak és onnan olvasnak, az alábbi ábrán látható módon. Így a portálok (vagy parancsmagok) a címtáradatok bekérésére vagy módosítására használt kezelőfelületként működnek.

![][2]

A felhasználók és csoportok kezelését ezek az Azure AD platformra épülő fiókportálok és a hozzájuk társított Azure AD PowerShell-parancsmagok végzik.

Ha valamelyik portál (vagy parancsmag) használatával módosítja a szervezeti adatokat, miközben be van jelentkezve az egyik szolgáltatásba, az adott szolgáltatásba történő legközelebbi bejelentkezés alkalmával a változás más portálokon is megjelenik, mivel adatmegosztás áll fenn az előfizetett Microsoft felhőszolgáltatások között.
Ha például egy felhasználó bejelentkezésének blokkolására az Office 365 felügyeleti központját használta, ezzel az adott felhasználó bármely másik, a szervezete által aktuálisan előfizetett szolgáltatásra történő bejelentkezését is blokkolja. Ha a Microsoft Intune fiókportálon próbálja blokkolni ugyanennek a felhasználónak a fiókját, azt fogja tapasztalni, hogy a felhasználó már blokkolva van.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Hogyan vehetek fel és kezelhetek több címtárat?
Azure AD-címtárat az Azure felügyeleti portálon vehet fel. Válassza az **Active Directory** elemet a bal oldali panelen, majd kattintson az **OK** gombra.

Az egyes címtárakat teljesen független erőforrásként kezelheti: mindegyikük társüzemű, minden funkcióval ellátott és logikailag független a többi, Ön által kezelt címtártól, nincs közöttük szülő-gyermek típusú kapcsolat. Ez a függetlenség az erőforrás, a felügyelet és a szinkronizálás függetlenségét is jelenti.

* **Erőforrás-függetlenség**. Ha erőforrást hoz létre vagy töröl egy címtárban, az nem érinti a többi címtárban található erőforrásokat, a külső felhasználókra vonatkozó részleges kivétellel (lásd lent). Ha egy címtárat egy „contoso.com” egyedi tartománnyal használja, azt egyetlen másik címtárral sem használhatja.
* **Felügyeleti függetlenség**.  Ha a „Contoso” címtár nem rendszergazda felhasználója létrehoz egy „Teszt” nevű tesztelési címtárat, majd
  
  * a címtár-szinkronizálási eszköz egyetlen AD-erdővel történő szinkronizáláshoz.
  * A „Contoso” címtár rendszergazdájának nem lesznek közvetlen rendszergazdai jogosultságai a „Teszt” címtáron, kivéve, ha annak a rendszergazdája kifejezetten megadja számára ezeket a jogokat. A „Contoso” rendszergazdái képesek a „Teszt” címtár hozzáférésének szabályozására a címtárat létrehozó felhasználói fiók felügyeleti joga révén.
    
    Ha pedig egy címtárban módosítja (hozzáadja vagy eltávolítja) a felhasználó rendszergazdai szerepkörét, ez a változtatás nem érinti az adott felhasználó más címtárakra vonatkozó rendszergazdai szerepkörét.
* **Szinkronizálási függetlenség**. Az egyes Azure AD-k egymástól függetlenül konfigurálhatók a következők egyetlen példányából származó adatok szinkronizálásához:
  
  * a címtár-szinkronizálási eszköz egyetlen AD-erdővel történő szinkronizáláshoz;
  * a Forefront Identity Manager Azure Active Directory-összekötője egy vagy több helyszíni erdővel és/vagy nem AD-alapú adatforrással történő adatszinkronizáláshoz.

Vegye figyelembe azt is, hogy a többi Azure-erőforrástól eltérően az Ön címtárai nem egy Azure-előfizetés alsóbb szintű erőforrásai. Így az Azure-előfizetés megszüntetését vagy lejártát követően továbbra is hozzáférhet a címtár adataihoz az Azure AD PowerShell, az Azure Graph API vagy egyéb felületek, például az Office 365 felügyeleti központja használatával. A címtárhoz másik előfizetést is rendelhet.

## <a name="how-can-i-delete-an-azure-ad-directory"></a>Hogyan törölhetek egy Azure AD-címtárat?
Az Azure AD-címtárakat egy globális rendszergazda törölheti a portálról. Egy címtár törlésekor az abban található összes erőforrás is törlődik, ezért a törlés előtt győződjön meg arról, hogy nincs szüksége az adott címtárra.

> [!NOTE]
> Ha a felhasználó munkahelyi vagy iskolai fiókkal jelentkezik be, kezdőcímtárát nem szabad törölnie. Ha például a felhasználó joe@contoso.onmicrosoft.com, fiókkal van bejelentkezve, nem törölheti a contoso.onmicrosoft.com-ot alapértelmezett tartományként használó címtárat.
> 
> 

### <a name="conditions-that-must-be-met-to-delete-an-azure-ad-directory"></a>Azure AD-címtár törléséhez szükséges feltételek
Egy Azure AD-címtár törléséhez bizonyos feltételeknek kell teljesülniük. Ezzel csökken annak a kockázata, hogy az adott címtár törlése kedvezőtlen hatással legyen egyes felhasználókra vagy alkalmazásokra, például a felhasználói bejelentkezésre az Office 365 felületére vagy az Azure-erőforrások hozzáférhetőségére. Ha például egy előfizetéshez tartozó címtárat akaratlanul töröltek, a felhasználók azt követően nem férhetnek hozzá az adott előfizetéshez tartozó Azure-erőforrásokhoz.

A rendszer a következő feltételek teljesülését ellenőrzi:

* Kizárólag egyetlen felhasználó, a globális rendszergazda törölheti a címtárat. A címtár törlése előtt az összes többi felhasználót törölni kell. Helyszíni felhasználó-szinkronizáláskor a művelet befejezését követően a szinkronizálást ki kell kapcsolni, a felhasználókat pedig törölni kell a felhőcímtárból a felügyeleti portál vagy Microsoft PowerShell Azure-moduljának használatával. Nincs szükség csoportok vagy névjegyek (például az Office 365 felügyeleti központjából felvett névjegyek) törlésére.
* A címtárban nem lehetnek alkalmazások. A címtár törlése előtt az összes alkalmazást törölni kell.
* Törölni kell minden, a címtárhoz társított Microsoft Online Services-előfizetést, például a Microsoft Azure-t, az Office 365-öt vagy a Prémium szintű Azure AD-t. Ha például az alapértelmezett címtár az Azure-ban lett létrehozva, nem törölheti azt mindaddig, amíg Azure-előfizetésének hitelesítése továbbra is ezen a címtáron alapul. Akkor sem törölhet egy címtárat, ha felhasználói előfizetés társul hozzá. Az előfizetés másik címtárhoz történő társításához jelentkezzen be az Azure felügyeleti portálra, majd a bal oldali navigációs ablaktáblán kattintson a **Settings**(Beállítások) lehetőségre. Ezt követően kattintson az **Subscriptions** (Előfizetések) oldal alján található **Edit Directory** (Címtár szerkesztése) elemre. További információk az Azure-előfizetésekről: [How Azure subscriptions are associated with Azure AD?](active-directory-how-subscriptions-associated-directory.md) (Hogyan kapcsolódnak az Azure-előfizetések az Azure AD-hoz?)

> [!NOTE]
> Ha a felhasználó munkahelyi vagy iskolai fiókkal jelentkezik be, kezdőcímtárát nem szabad törölnie. Ha például a felhasználó joe@contoso.onmicrosoft.com, fiókkal van bejelentkezve, nem törölheti a contoso.onmicrosoft.com-ot alapértelmezett tartományként használó címtárat.
> 
> 

* A címtárhoz nem kapcsolhatók Multi-Factor Authentication-szolgáltatók.

## <a name="additional-resources"></a>További források
* [Azure AD fórum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Azure Multi-Factor Authentication fórum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure)
* [Regisztráció az Azure-ba szervezetként](sign-up-organization.md)
* [Az Azure AD kezelése Windows PowerShell használatával](https://msdn.microsoft.com/library/azure/jj151815.aspx)
* [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles.md)

<!--Image references-->
[1]: ./media/active-directory-administer/aad_portals.png
[2]: ./media/active-directory-administer/azure_tenants.png

