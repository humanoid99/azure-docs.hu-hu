---
title: "Biztonságos jelszavak az Azure AD-ban és az Intelligens jelszózárolás által blokkolt jelszavak visszaállítása | Microsoft Docs"
description: "Ismerteti az Azure AD-bérlő fogalmát, valamint az Azure kezelését az Azure Active Directory felületén keresztül"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>Biztonságos jelszavak az Azure AD-ban és az Intelligens jelszózárolás által blokkolt jelszavak visszaállítása
Ez a cikk a felhasználóként vagy rendszergazdaként követhető ajánlott eljárásokat mutatja be az Azure Active Directory (Azure AD) és a Microsoft-fiókszolgáltatás fiókjainak védelme érdekében. 

 >[!NOTE]
 >Az Azure AD-rendszergazdák a címtárnévre kattintva alaphelyzetbe állíthatják a felhasználói jelszavakat. Az [Azure felügyeleti portálon](https://manage.windowsazure.com) válassza a Felhasználók oldalt, kattintson a felhasználó nevére, majd a Jelszó alaphelyzetbe állítása elemre. 
 >

Az Azure AD a következő általános megközelítéseket tartalmazza a jelszavak védelme érdekében:
 *    Jelszóhosszra vonatkozó követelmények
 *    A jelszó „összetettségére” vonatkozó követelmények
 *    Normál és időszakos jelszólejárat 

A jelszókezelési képességekkel kapcsolatos információkért lásd: [Jelszavak kezelése az Azure Active Directoryban](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords). 

## <a name="azure-ad-password-protection"></a>Azure AD jelszóvédelem
Az Azure AD és a Microsoft-fiókrendszer az iparágban már bizonyított megközelítéseket alkalmaz a felhasználói és rendszergazdai jelszavak védelmének biztosítása érdekében. 

Ez a szakasz bemutatja, miként védi meg az Azure AD a jelszavakat a következő módszerekkel:
 *    Dinamikusan letiltott jelszavak
 *    Intelligens jelszózárolás

A jelszókezeléssel kapcsolatos, aktuális kutatásokon alapuló információkért tekintse meg a [Tájékoztató a jelszavakról](http://aka.ms/passwordguidance) című tanulmányt. 

### <a name="dynamically-banned-passwords"></a>Dinamikusan letiltott jelszavak
Az Azure AD és a Microsoft-fiókrendszer a jelszóvédelem biztosítása érdekében dinamikusan letiltja az összes gyakran használt jelszavat. Az Azure ID Identity Protection csapat rendszeresen elemzi a letiltott jelszavak listáit, amely segít meggátolni, hogy a felhasználók gyakran használt jelszavakat válasszanak. Ez a szolgáltatás az Azure AD és a Microsoft-fiókszolgáltatás ügyfeleinek érhető el. 

A jelszavak létrehozásakor fontos, hogy a rendszergazdák arra bátorítsák a felhasználókat, hogy betűk, számok és karakterek egyedi kombinációját tartalmazó, nem szokványos jelszavakat válasszanak. Ez elősegíti, hogy a felhasználói jelszavakat szinte lehetetlen legyen feltörni. 

**Illetéktelen behatolások listái**

Az Azure AD mindig azon dolgozik, hogy egy lépéssel a számítógépes bűnözők előtt járjon. Ennek egyik módja, hogy nem engedi a felhasználók számára a jelenlegi támadási listán szereplő jelszavak létrehozását.

Az Azure AD Identity Protection csapata folyamatosan elemzi a gyakran használt jelszavakat. A számítógépes bűnözők is hasonló stratégiákat használnak a támadásaikhoz, például [szivárványtáblát](https://en.wikipedia.org/wiki/Rainbow_table) hoznak létre a jelszókivonatok feltörése érdekében. 

A Microsoft folyamatosan elemzi az [adatszivárgásokat](https://www.privacyrights.org/data-breaches) a tiltott jelszavak dinamikusan frissített listájának fenntartása érdekében, így a sebezhető jelszavak még azelőtt le lesznek tiltva, hogy valódi fenyegetést jelenthetnének az Azure AD ügyfelei számára. Az aktuális biztonsági erőfeszítéseinkről további információt a [Microsoft biztonsági intelligenciáról szóló jelentésében](https://www.microsoft.com/security/sir/default.aspx) talál. 

### <a name="smart-password-lockout"></a>Intelligens jelszózárolás

Amikor az Azure AD érzékeli, hogy egy lehetséges számítógépes bűnöző megpróbál feltörni egy felhasználói jelszót, az Intelligens jelszózárolás segítségével zároljuk a felhasználói fiókot. Az Azure AD-t arra tervezték, hogy meghatározza az egyes bejelentkezési munkamenetekkel társított kockázatokat. 

A legújabb biztonsági adatok használatával zárolási szemantikát alkalmazunk a számítógépes fenyegetésekre. Így a felhasználók nem lesznek kizárva, amikor egy számítógépes bűnöző feltöri a felhasználói jelszavakat a hálózaton.

Ha egy felhasználó ki van zárva az Azure AD-ből, a képernyője az alábbihoz hasonlóan néz ki:

  ![Kizárva az Azure AD-ből](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
Egyéb Microsoft-fiókok esetén pedig a képernyő az alábbihoz hasonlóan néz ki:

  ![Kizárva egy Microsoft-fiókból](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Az Azure Active Directory jelszókezelésével kapcsolatos információért tekintse meg a [jelszókezelés működését leíró](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works) szakaszt.

  >![MEGJEGYZÉS] Ha Ön Azure AD-rendszergazda, javasolt a [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) használata, hogy a felhasználók egyáltalán ne is hozhassanak létre hagyományos jelszavakat.
  >

## <a name="next-steps"></a>Következő lépések
[Saját jelszó frissítése](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Az Azure-identitáskezelés alapjai](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[Üzemeltetési adatok beszerzése jelszókezelési jelentésekkel](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



