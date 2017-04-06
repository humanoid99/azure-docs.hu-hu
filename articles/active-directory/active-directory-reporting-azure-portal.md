---
title: "Jelentéskészítés az Azure Active Directory előzetes kiadásában | Microsoft Docs"
description: "Ez a dokumentum az Azure Active Directory előzetes kiadásában elérhető különféle jelentéseket sorolja fel"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: be986fd7bb1745dcf43a1066dfabc1e1c699ab4c
ms.openlocfilehash: b9cd11954a52600c1cd50155cb7ce9b7d2355cd3
ms.lasthandoff: 01/20/2017


---
# <a name="azure-active-directory-reporting---preview"></a>Jelentéskészítés az Azure Active Directory előzetes kiadásában


*Ez a dokumentáció az [Azure Active Directory Reporting-útmutató](active-directory-reporting-guide.md) része.*

Az Azure Active Directory előzetes kiadásában elérhető jelentéskészítéssel minden szükséges információhoz hozzájuthat a környezetével kapcsolatban. [Mit tartalmaz az előzetes kiadás?](active-directory-preview-explainer.md)

A jelentéskészítés két fő területe:

* **Bejelentkezési tevékenységek** – A felügyelt alkalmazások használatával és a felhasználók bejelentkezési tevékenységeivel kapcsolatos információk
* **Naplók** – Rendszertevékenység információk a felhasználó- és csoportfelügyeletre, valamint a felügyelt alkalmazásokra és a címtártevékenységekre vonatkozóan

A keresett adatok hatókörétől függően a jelentésekhez úgy férhet hozzá, ha a **Felhasználók és csoportok** vagy a **Vállalati alkalmazások** elemre kattint az [Azure Portal](https://portal.azure.com) szolgáltatáslistájában.

## <a name="sign-in-activities"></a>Bejelentkezési tevékenységek
### <a name="user-sign-in-activities"></a>Felhasználók bejelentkezési tevékenységei
A felhasználók bejelentkezési jelentésében szereplő információkból az alábbi kérdésekre kaphat választ:

* Milyen egy adott felhasználó bejelentkezési mintázata?
* Hány felhasználó jelentkezett be egy adott héten?
* Milyen állapotúak ezek a bejelentkezések?

Az adatok megtekintését a felhasználók bejelentkezési grafikonjával kezdheti, amely az **Áttekintés** szakaszban, a **Felhasználók és csoportok** területen.

 ![Jelentéskészítés](./media/active-directory-reporting-azure-portal/05.png "Jelentéskészítés")

A felhasználók bejelentkezési grafikonja az összes felhasználó bejelentkezéseinek összesítését ábrázolja egy adott időszakban. Az alapértelmezett időszak 30 nap.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/02.png "Jelentéskészítés")

A bejelentkezési grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/03.png "Jelentéskészítés")

A bejelentkezési tevékenységek listájának minden sora részletes információkat tartalmaz a kijelölt bejelentkezésről:

* Ki jelentkezett be?
* Mi volt a kapcsolódó egyszerű felhasználónév?
* Melyik alkalmazás volt a bejelentkezés célja?
* Mi a bejelentkezéshez tartozó IP-cím?
* Mi volt a bejelentkezés állapota?

### <a name="usage-of-managed-applications"></a>Felügyelt alkalmazások használati adatai
A bejelentkezési információk alkalmazás-központú nézetével az alábbi kérdésekre kaphat választ:

* Ki használja az alkalmazásaimat?
* Melyik a szervezet 3 legnépszerűbb alkalmazása?
* Nemrégiben új alkalmazást adtam ki. Mennyire sikeres?

Az adatok megtekintését a szervezet az elmúlt 30 nap alatt legnépszerűbb 3 alkalmazásáról szóló jelentéssel kezdheti az **Áttekintés** szakaszban, a **Vállalati alkalmazások** területen.

 ![Jelentéskészítés](./media/active-directory-reporting-azure-portal/06.png "Jelentéskészítés")

A 3 legnépszerűbb alkalmazásba való bejelentkezések heti összesítő grafikonja egy adott időszak során. Az alapértelmezett időszak 30 nap.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/78.png "Jelentéskészítés")

Igény esetén egy adott alkalmazást is kiemelhet.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/single_spp_usage_graph.png "Jelentéskészítés")

Az alkalmazáshasználati grafikon egyik napjára kattintva részletes listát kap a bejelentkezési tevékenységekről.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/top_app_sign_ins.png "Jelentéskészítés")

A **Bejelentkezések** lehetőség az alkalmazások összes bejelentkezési eseményének teljes körű áttekintését biztosítja.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/85.png "Jelentéskészítés")

Az oszlopválasztóval kiválaszthatja a megjelenítendő adatmezőket.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/column_chooser.png "Jelentéskészítés")

### <a name="filtering-sign-ins"></a>Bejelentkezések szűrése
A következő mezőkkel szűrheti a bejelentkezéseket a megjelenített adatok korlátozása érdekében:

* Dátum és idő 
* Felhasználó egyszerű neve
* Alkalmazásnév
* Ügyfél neve
* Bejelentkezési állapot

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/293.png "Jelentéskészítés")

A bejelentkezési tevékenységek bejegyzéseit konkrét bejegyzések keresésével is szűrheti.
Ezzel a keresési módszerrel konkrét **felhasználókra**, **csoportokra** vagy **alkalmazásokra** szűkítheti a bejelentkezések hatókörét.

![Jelentéskészítés](./media/active-directory-reporting-azure-portal/84.png "Jelentéskészítés")

## <a name="audit-logs"></a>Naplók
Az Azure Active Directory naplói a rendszertevékenységek rekordjait tartalmazzák megfelelőségi célokból.

Az Azure Portalon három fő kategóriája van a kapcsolódó tevékenységek naplózásának:

* Felhasználók és csoportok   
* Alkalmazások
* Címtár   

A naplózási jelentési tevékenységek teljes listáját [a naplózási jelentési események listájában](active-directory-reporting-audit-events.md#list-of-audit-report-events) tekintheti meg.

A naplózási adatok áttekintését a **Naplók** használatával kezdheti meg, az **Azure Active Directory** **Műveletek** szakaszában található.

![Naplózás](./media/active-directory-reporting-azure-portal/61.png "Naplózás")

Az auditnapló olyan listanézetet is tartalmaz, amely az aktorokat (ki), a tevékenységeket (mit) és a célokat is megjeleníti.

![Naplózás](./media/active-directory-reporting-azure-portal/345.png "Naplózás")

Részletes információk a listanézet elemeire kattintva érhetők el.

![Naplózás](./media/active-directory-reporting-azure-portal/873.png "Naplózás")

### <a name="users-and-groups-audit-logs"></a>Felhasználók és csoportok auditnaplói
A felhasználó- és csoportalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Milyen típusú frissítéseket telepítettek a felhasználók?
* Hány felhasználó lett módosítva?
* Hány jelszó lett módosítva?
* Mit csinált a rendszergazda egy adott címtárban?
* Mely csoportok lettek hozzáadva?
* Történt-e tagsági változás valamelyik csoportban?
* Változtak-e a csoportok tulajdonosai?
* Milyen licencek lettek hozzárendelve egy adott csoporthoz vagy felhasználóhoz?

Ha csak át szeretné tekinteni a felhasználókhoz és csoportokhoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Felhasználók és csoportok** **Tevékenység** szakaszában található.

![Naplózás](./media/active-directory-reporting-azure-portal/93.png "Naplózás")

### <a name="application-audit-logs"></a>Alkalmazások auditnaplói
Az alkalmazásalapú naplózási jelentésekkel az alábbi kérdésekre kaphat választ:

* Mely alkalmazások lettek hozzáadva vagy frissítve?
* Mely alkalmazások lettek eltávolítva?
* Megváltozott valamelyik alkalmazás egyszerű szolgáltatásneve?
* Történt változás az alkalmazások nevében?
* Ki hagyott jóvá egy adott alkalmazást?

Ha csak át szeretné tekinteni az alkalmazásokhoz kapcsolódó naplózási adatokat, megnyithat egy szűrt nézetet az **Auditnaplók** menüpontból, amely a **Vállalati alkalmazások** **Tevékenység** szakaszában található.

![Naplózás](./media/active-directory-reporting-azure-portal/134.png "Naplózás")

### <a name="filtering-audit-logs"></a>Auditnaplók szűrése
A következő mezőkkel szűrheti a bejelentkezéseket a megjelenített adatok korlátozása érdekében:

* Dátum és idő
* Szereplő felhasználói egyszerű neve
* Tevékenység típusa
* Tevékenység

![Naplózás](./media/active-directory-reporting-azure-portal/356.png "Naplózás")

A **Tevékenység típusa** lista tartalma a panel belépési pontjához van kötve.  
Ha a belépési pontja az Azure Active Directory, ez a lista tartalmazza az összes lehetséges tevékenységtípust:

* Alkalmazás 
* Csoport 
* Felhasználó
* Eszköz
* Címtár
* Szabályzat
* Egyéb

![Naplózás](./media/active-directory-reporting-azure-portal/825.png "Naplózás")

A listázott tevékenységek hatókörét a tevékenységtípus határozza meg.
Ha például a **Csoport** érték van kiválasztva a **Tevékenység típusaként**, a **Tevékenység** lista csak a csoporttal kapcsolatos tevékenységeket tartalmazza.   

![Naplózás](./media/active-directory-reporting-azure-portal/654.png "Naplózás")

Az auditnaplók bejegyzéseit konkrét bejegyzések keresésével is szűrheti.

![Naplózás](./media/active-directory-reporting-azure-portal/237.png "Naplózás")

## <a name="next-steps"></a>Következő lépések
Lásd az [Azure Active Directory Reporting-útmutatót](active-directory-reporting-guide.md).


