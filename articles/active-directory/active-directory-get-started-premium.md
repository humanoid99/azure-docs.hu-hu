---
title: "Bevezetés a Prémium szintű Azure Active Directory használatába"
description: "Ez a témakör ismerteti, hogyan regisztrálhat a Prémium szintű Active Directory-kiadásra a Mennyiségi licencelés webhelyén keresztül."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73cbbce9ae3d727632b8cac2a29c2b1b92332ae7
ms.lasthandoff: 12/07/2016


---
# <a name="getting-started-with-azure-active-directory-premium"></a>Bevezetés a Prémium szintű Azure Active Directory használatába
A Prémium szintű Active Directory-kiadásra történő regisztrálásra több lehetősége van: 

**Azure vagy Office 365** – Azure- vagy Office 365-előfizetőként az interneten vásárolhatja meg a Prémium szintű Active Directory-kiadást. A részletes lépésekért lásd: [A Prémium szintű Active Directory megvásárlása – meglévő ügyfelek](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) vagy [A Prémium szintű Active Directory megvásárlása – új ügyfelek](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Nagyvállalati mobilitás és biztonság** – A Nagyvállalati mobilitás és biztonság (korábbi nevén Nagyvállalati mobilitási csomag) révén a szervezetek költséghatékony módon használhatják a következő szolgáltatásokat együttesen, egy licenccsomagban: Prémium szintű Active Directory, Azure Rights Management, Microsoft Intune. További információt a [Nagyvállalati mobilitás és biztonság](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx) webhelyén talál. 30 napos ingyenes próbaverzióért kattintson [ide](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).

**Microsoft mennyiségi licencelés** – A Prémium szintű Active Directory a [Microsoft Nagyvállalati szerződés](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 vagy több licenc esetén) vagy a [Open mennyiségi licencelés](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5–250 licenc esetén) keretén belül érhető el.

Ez a témakör ismerteti a Mennyiségi licencelési programon keresztül vásárolt Prémium szintű Active Directory használatának első lépéseit. Ha még nem ismeri az Azure Active Directory különböző kiadásait, olvassa el a következő cikket: [Azure Active Directory-kiadások](active-directory-editions.md).  

> [!NOTE]
> Az Azure Active Directory Prémium és Alapszintű kiadásai az Azure Active Directory világszerte elérhető példányával érhetők el a kínai ügyfelek számára. Az Azure Active Directory Prémium és Alapszintű kiadásai jelenleg nem támogatottak Kínában a 21Vianet által működtetett Microsoft Azure szolgáltatásban. További információkért lépjen velünk kapcsolatba az [Azure Active Directory fórumán](https://feedback.azure.com/forums/169401-azure-active-directory/).
> 
> 

## <a name="step-1-sign-up-for-active-directory-premium"></a>1. lépés: A Prémium szintű Active Directory-kiadás regisztrálása
A regisztrációhoz lásd: [Vásárlás mennyiségi licencelésen keresztül](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).

## <a name="step-2-activate-your-license-plan"></a>2. lépés: A licenccsomag aktiválása
Ez az első licenccsomag, amelyet a Microsofttól vásárol a Vállalati mennyiségi licencelési programon keresztül?
Ebben az esetben egy visszaigazoló e-mailt kap a vásárlása végén.
Az első licenccsomag aktiválásához szüksége lesz erre az e-mailre.

Az ezen címtárhoz történő minden további vásárláskor a rendszer automatikusan aktiválja a licenceket ugyanebben a címtárban.

**A licenccsomag aktiválásához hajtsa végre az alábbi lépések egyikét:**

1. Az aktiválás elindításához kattintson a **Bejelentkezés** vagy a **Regisztráció** gombra.
   
    ![Bejelentkezés][1]

    - Ha rendelkezik meglévő bérlővel, kattintson a **Bejelentkezés** gombra, hogy bejelentkezzen a meglévő rendszergazdai fiókjával. Azon címtárból származó globális rendszergazdai hitelesítő adatokkal kell bejelentkeznie, ahol a licenceket aktiválni szeretné.

    - Ha új Azure Active Directory-bérlőt szeretne aktiválni a licenccsomaghoz, kattintson a **Regisztráció** gombra a **Fiókprofil létrehozása** párbeszédpanel megnyitásához.

        ![Fiókprofil létrehozása][2]

Amikor végzett, megjelenik a következő párbeszédpanel a bérlő licenccsomagja aktiválásának megerősítéseként.

![Megerősítés][3]

## <a name="step-3-activate-your-azure-active-directory-access"></a>3. lépés: Az Azure Active Directory-hozzáférés aktiválása
Ha korábban már használta a Microsoft Azure-t, folytathatja a [4. lépéssel](#step-4-assign-license-to-user-accounts). 

Amikor a licencek érvénybe lépnek a címtárban, egy **üdvözlő e-mailt** kap. Az e-mail megerősíti, hogy elkezdheti a Prémium szintű Active Directory vagy a Nagyvállalati mobilitási csomag licenceinek és funkcióinak kezelését. 

Ha megpróbálja aktiválni az Azure Active Directory-hozzáférését az üdvözlő e-mail fogadása előtt, a következő hibaüzenetet kapja. 

![A hozzáférés nem érhető el][9]

Próbálkozzon újra néhány perc múlva, miután megkapta az e-mailt.

Az előfizetésének új rendszergazdái is aktiválhatják a hozzáférésüket a klasszikus Azure portálra ezen a hivatkozáson keresztül.

**Az Azure Active Directory-hozzáférésének aktiválásához végezze el a következő lépéseket:**

1. Az **üdvözlő e-mailben** kattintson a **Bejelentkezés** hivatkozásra. 
   
    ![Üdvözlő e-mail][4]
2. Amikor sikeresen bejelentkezett, el kell végeznie egy kéttényezős hitelesítést mobileszközös ellenőrzés formájában:
   
    ![Mobileszközös ellenőrzés][5]

Az aktiválás eltarthat pár percig. Ha a hozzáférése aktív, a barna színű sáv eltűnik, és kattinthatóvá válik a **Portál** gomb.

![Várjon, amíg elkészülünk a beállítással][6]

Ebben az esetben az Azure-hozzáférése az Azure Active Directoryra van korlátozva.

![Azure-képességek][7]

Lehet, hogy korábbról már van Azure-hozzáférése. Ezenkívül az Azure Active Directory-hozzáférését a teljes Azure-hozzáférésre frissítheti további Azure-előfizetések aktiválásával. Ezekben az esetekben a klasszikus Azure portálon több képesség is használható.

![Azure-képességek][8]

## <a name="step-4-assign-license-to-user-accounts"></a>4. lépés: Licenc hozzárendelése felhasználói fiókokhoz
A megvásárolt csomag használatának elkezdése előtt manuálisan kell licenceket rendelnie a szervezeten belüli felhasználói fiókokhoz, hogy használhassák a Prémium kiadásban szereplő funkciók gazdag választékát. A következő lépésekkel rendelhet licenceket a felhasználókhoz, hogy használhassák a Prémium szintű Active Directory-funkciókat.

**Ha licenceket szeretne a felhasználókhoz rendelni, végezze el a következő lépéseket:**

1. Jelentkezzen be a klasszikus Azure portálra a testreszabni kívánt címtár globális rendszergazdájaként.
2. Kattintson az **Active Directory** lehetőségre, majd válassza ki azt a címtárat, ahol licenceket szeretne hozzárendelni felhasználókhoz.
3. Válassza a **Licencek** lapot, válassza a **Prémium szintű Active Directory** vagy a **Nagyvállalati mobilitási csomag** lehetőséget, majd kattintson a **Hozzárendelés** gombra.
   
    ![Licenccsomagok][10]
4. A párbeszédpanelen válassza ki azokat a felhasználókat, akikhez licenceket szeretne rendelni, majd kattintson a pipa ikonra a módosítások mentéséhez.
   
    ![Licencek hozzárendelése][11]

### <a name="license-restrictions"></a>Licenckorlátozások
Néhány licenccsomag más licenccsomagok alkészletének vagy kibővítésének számít. Általában a felhasználókhoz nem rendelhető olyan licenccsomag, amely már hozzájuk lett rendelve. Ha kibővített licenccsomagot szeretne valakihez hozzárendelni, először el kell távolítania az alkészletet alkotó licenccsomagot.

### <a name="license-requirements"></a>Licenckövetelmények
Amikor licencet rendel egy felhasználóhoz, megadhat egy elsődleges felhasználási helyet a felhasználó fiókjának tulajdonságaiban. Ha a felhasználási hely nincs meghatározva, a bérlő helye lesz automatikusan hozzárendelve a felhasználóhoz.

![Felhasználó helye][12]

A Microsoft felhőalapú szolgáltatásainak és funkcióinak rendelkezésre állása ország vagy régió szerint változik. A szolgáltatások (például a Voice over Internet Protocol (VoIP)) elérhetőek lehetnek egy országban vagy régióban, míg egy másikban pedig nem. A szolgáltatásokon belüli funkciók bizonyos országokban vagy régiókban jogi okokból korlátozottak lehetnek. Annak megtekintéséhez, hogy egy szolgáltatás vagy funkció korlátozásokkal érhető-e el vagy azok nélkül, keresse meg a szolgáltatás licenckorlátozásait tartalmazó webhelyen az országát vagy régióját.

## <a name="whats-next"></a>A következő lépések
* [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési panel oldalon](active-directory-add-company-branding.md)
* [A hozzáférési és használati jelentések megtekintése](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png

