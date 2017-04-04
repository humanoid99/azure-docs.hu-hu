---
title: "Azure-beli futtató fiók konfigurálása | Microsoft Docs"
description: "Ez az oktatóanyag végigvezeti az egyszerű biztonsági hitelesítés létrehozásán, tesztelésén és használatán az Azure Automationben."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "egyszerű szolgáltatásnév, setspn, azure-hitelesítés"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/28/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Runbookok hitelesítése Azure-beli futtató fiókkal
Ebből a cikkből megtudhatja, hogyan konfigurálhat Azure Automation-fiókot az Azure Portalon. Ehhez a Futtató fiók funkció segítségével hitelesítse az Azure Resource Managerben vagy az Azure Service Managerben erőforrásokat kezelő runbookokat.

Amikor egy Automation-fiókot hoz létre az Azure Portalon, automatikusan két fiók jön létre:

* Egy futtató fiók. Ez a fiók létrehoz egy egyszerű szolgáltatást az Azure Active Directory-ban (Azure AD), valamint egy tanúsítványt. Emellett kiosztja a Közreműködő szerepköralapú hozzáférés-vezérlést (RBAC), amely runbookok használatával kezeli a Resource Manager-erőforrásokat.
* Egy klasszikus futtató fiókot. Ez a fiók feltölt egy felügyeleti tanúsítványt, amelynek használatával a Szolgáltatásfelügyelet erőforrásai vagy a klasszikus erőforrások kezelhetők runbookokkal.

Az Automation-fiók létrehozása leegyszerűsíti a folyamatot, valamint segít a tervezés gyors megkezdésében és a runbookok üzembe helyezésében az automatizálási szükségletek támogatására.

A futtató és klasszikus futtató fiókok segítségével a következőket teheti meg:

* Szabványos módot biztosíthat az Azure-hitelesítésre abban az esetben, ha runbookokból származó Resource Manager- vagy Azure Service Management-erőforrásokat felügyel az Azure Portal webhelyen.
* Automatizálhatja az Azure Alerts szolgáltatásban konfigurálható globális runbookok használatát.

> [!NOTE]
> Az Automation globális runbookokhoz készült [Azure Alert integrációs funkció](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) használatához szükség van egy futtató és klasszikus futtató fiókokkal konfigurált Automation-fiókra. Kiválaszthat egy olyan Automation-fiókot, amelyhez már tartozik futtató és klasszikus futtató fiók, vagy létrehozhat egy új Automation-fiókot.
>  

A cikkben bemutatjuk, hogyan hozhat létre Automation-fiókot az Azure Portalról, hogyan frissítheti Automation-fiókját az Azure PowerShell-lel, hogyan kezelheti a fiók konfigurációját, illetve hogyan végezhet hitelesítést a runbookokban.

Az Automation-fiók létrehozása előtt érdemes áttekintenie és megfontolnia a következőket:

* Az Automation-fiók létrehozása nincs hatással a korábban a klasszikus vagy a Resource Manager-alapú üzemi modell keretében esetleg már létrehozott Automation-fiókokra.
* Ez a folyamat kizárólag olyan Automation-fiókok esetében működik, amelyeket az Azure Portalon hozott létre. Ha a klasszikus Azure portálról hoz létre fiókot, a rendszer nem replikálja a futtató fiók konfigurációját.
* Ha jelenleg rendelkezik olyan runbookokkal és objektumokkal (például ütemezésekkel vagy változókkal), amelyeket klasszikus erőforrások felügyeletére hozott létre, és szeretné, hogy ezek a runbookok hitelesítést végezzenek az új klasszikus futtató fiókkal, a következő lehetőségek közül választhat:

  * Klasszikus futtató fiók létrehozásához kövesse a „Saját futtató fiók kezelése” szakaszban megadott utasításokat. 
  * Meglévő fiók frissítéséhez használja „Az Automation-fiók frissítése a PowerShell használatával” szakaszban megadott PowerShell-szkriptet.
* Ha az új futtató fiókkal és a klasszikus futtató Automation-fiókkal szeretne hitelesítést végezni, a [Hitelesítésikód-példák](#authentication-code-examples) szakaszban található példakód segítségével módosítania kell meglévő runbookjait.

    >[!NOTE]
    >A futtató fiók Resource Manager-erőforrásokon, tanúsítványalapú szolgáltatásnévvel történő hitelesítésre szolgál. A klasszikus futtató fiók Service Manager-erőforrásokon, felügyeleti tanúsítvánnyal történő hitelesítésére szolgál.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Egy Automation-fiók létrehozása az Azure Portalról
Ebben a szakaszban olyan Azure Automation-fiókot hoz létre az Azure Portalról, amely egy futtató fiókot és egy klasszikus futtató fiókot is létrehoz.

>[!NOTE]
>Automation-fiók létrehozásához a Szolgáltatás-adminisztrátorok szerepkör tagjának, vagy azon előfizetés társadminisztrátorának kell lennie, amely hozzáférést biztosít az előfizetéshez. Ezenfelül felhasználóként hozzá kell legyen rendelve az előfizetés alapértelmezett Active Directory-példányához. A fiókhoz nem szükséges kiemelt szerepkört rendelni.
>
>Ha nem tagja az előfizetéshez tartozó Active Directory-példánynak, mielőtt hozzáadják az előfizetés társadminisztrátori szerepköréhez, vendégként lesz hozzáadva az Active Directoryhoz. Ez esetben „Nincs engedélye létrehozni…” figyelmeztető üzenetet kap az **Automation-fiók hozzáadása** panelen.
>
>Az először a társadminisztrátori szerepkörhöz hozzáadott felhasználók eltávolíthatók az előfizetéshez tartozó Active Directory-példányból, majd újra hozzáadhatók, így teljes jogú felhasználók lehetnek az Active Directoryban. Ez a helyzet úgy ellenőrizhető, ha az Azure Portal **Azure Active Directory** panelén a **Felhasználók és csoportok** és a **Minden felhasználó** elemre kattint, majd a konkrét felhasználó kiválasztása után a **Profil** elemet választja. A felhasználók profilja alatti **Felhasználó típusa** attribútum értéke ne legyen **Guest** (vendég).
>

1. Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, és emellett az előfizetés társadminisztrátorának is számít.

2. Válassza az **Automation-fiókok** elemet.

3. Az **Automation-fiókok** panelen kattintson a **Hozzáadás** elemre.
Ekkor megnyílik az **Automation-fiók hozzáadása** panel.

 ![Az „Automation-fiók hozzáadása” panel](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Ha a fiók nem tagja az Előfizetés-adminisztrátorok szerepkörnek, illetve nem az előfizetés társadminisztrátora, az **Automation-fiók hozzáadása** panelen a következő figyelmeztetés jelenik meg:
   >
   >![Az Automation-fiókhoz kapcsolódó figyelmeztetés hozzáadása](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. Az **Automation-fiók hozzáadása** panel **Név** mezőjében adjon meg egy nevet az új Automation-fióknak.

5. Ha több előfizetéssel rendelkezik, tegye a következőket:

    a. Az **Előfizetés** területen adjon meg egy előfizetést az új fiókhoz.

    b. Az **Erőforráscsoport** területen kattintson az **Új létrehozása** vagy a **Meglévő használata** elemre.

    c. A **Hely** területen adjon meg egy Azure-adatközpontot.

6. Az **Azure-beli futtató fiók létrehozása** területen válassza az **Igen** lehetőséget, majd kattintson a **Létrehozás** elemre.

   > [!NOTE]
   > Ha a **Nem** beállítás kiválasztásával úgy dönt, hogy nem hozza létre a futtató fiókot, egy figyelmeztető üzenet jelenik meg az **Automation-fiók hozzáadása** panelen. A fiók az Azure Portalon jön létre, azonban nem kap megfelelő hitelesítési identitást a klasszikus vagy Resource Manager-alapú előfizetés címtárszolgáltatásában. Következésképpen a fiók az előfizetéshez tartozó erőforrásokhoz sem fér hozzá. Ez megakadályozza, hogy az erre a fiókra hivatkozó runbookok hitelesítést végezhessenek, illetve feladatokat futtathassanak az ezekben az üzemi modellekben található erőforrások alapján.
   >
   > ![Figyelmeztető üzenet az „Automation-fiók hozzáadása” panelen](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Továbbá, mivel nem hozott létre szolgáltatásnevet, a rendszer nem osztja ki a Közreműködő szerepkört.
   >

7. Amíg az Azure létrehozza az Automation-fiókot, a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="resources"></a>Erőforrások
Ha befejeződött az Automation-fiók létrehozása, számos erőforrás automatikusan létrejön. Az erőforrásokat az alábbi két táblázat foglalja össze:

#### <a name="run-as-account-resources"></a>Futtató fiók erőforrásai

| Erőforrás | Leírás |
| --- | --- |
| AzureAutomationTutorial forgatókönyv | Grafikus mintarunbook, amely bemutatja, hogyan kell a futtató fiókkal hitelesítést végezni, és megjeleníti az összes Resource Manager-alapú erőforrást. |
| AzureAutomationTutorialScript forgatókönyv | PowerShell-mintarunbook, amely bemutatja, hogyan kell a futtató fiókkal hitelesítést végezni, és megjeleníti az összes Resource Manager-alapú erőforrást. |
| AzureRunAsCertificate | Tanúsítványobjektum, amely automatikusan létrejön, amikor Automation-fiókot hoz létre, vagy futtatja egy meglévő fiókon az alábbi PowerShell-szkriptet. A tanúsítvány lehetővé teszi az Azure-hitelesítést, így a runbookokból is kezelheti az Azure Resource Manager-erőforrásokat. A tanúsítvány élettartama egy év. |
| AzureRunAsConnection | Kapcsolatobjektum, amely automatikusan létrejön, amikor Automation-fiókot hoz létre, vagy futtatja egy meglévő fiókon a PowerShell-szkriptet. |

#### <a name="classic-run-as-account-resources"></a>Klasszikus futtató fiók erőforrásai

| Erőforrás | Leírás |
| --- | --- |
| AzureClassicAutomationTutorial forgatókönyv | Grafikus mintarunbook, amely a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetéshez tartozó, a klasszikus üzemi modellel létrehozott virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicAutomationTutorial parancsprogram-forgatókönyv | PowerShell-mintarunbook, amely a klasszikus futtató fiók (tanúsítvány) segítségével lekéri az előfizetéshez tartozó klasszikus virtuális gépeket, majd megjeleníti a virtuális gépek nevét és állapotát. |
| AzureClassicRunAsCertificate | Automatikusan létrejövő tanúsítványobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a runbookokból. A tanúsítvány élettartama egy év. |
| AzureClassicRunAsConnection | Automatikusan létrejövő kapcsolatobjektum, amely Azure-hitelesítésre használható, így a klasszikus Azure-erőforrások is kezelhetők a runbookokból. |

## <a name="verify-run-as-authentication"></a>A futtató fiókos hitelesítés ellenőrzése
Hajtson végre egy rövid tesztet, amellyel megállapítja, hogy képes-e sikeres hitelesítést végezni az új futtató fiókkal.

1. Az Azure Portalon nyissa meg a korábban létrehozott Automation-fiókot.

2. Kattintson a **Runbookok** csempére a forgatókönyvek listájának megnyitásához.

3. Válassza ki az **AzureAutomationTutorialScript** runbookot, majd a runbook elindításához kattintson az **Indítás** gombra. Az alábbi események történnek:
 * Létrejön egy [runbookfeladat](automation-runbook-execution.md), megjelenik a **Feladat** panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.
 * A feladat állapota kezdetben **Várólistán**, ami jelzi, hogy egy felhőbeli runbookfeldolgozó elérhetővé válására vár.
 * Az állapot akkor változik **Indítás** értékre, ha egy feldolgozó elvállalja a feladatot.
 * Amikor a runbook elkezd futni, az állapota **Fut** értékre változik.
 * Ha befejeződik a runbookfeladat, a **Befejezve** állapot jelenik meg.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Ha szeretné megtekinteni a runbook részletes eredményeit, kattintson a **Kimenet** csempére.  
Az ekkor megjelenő **Kimenet** panelen látható, hogy a runbook sikeresen elvégezte a hitelesítést, és visszaadta az erőforráscsoportban elérhető összes erőforrás listáját.

5. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.

6. Zárja be a **Feladat összegzése** panelt és a megfelelő **AzureAutomationTutorialScript** runbook paneljét.

## <a name="verify-classic-run-as-authentication"></a>A klasszikus futtató fiókos hitelesítés ellenőrzése
Hajtson végre egy hasonló rövid tesztet annak ellenőrzéséhez, hogy képes-e sikeres hitelesítést végezni az új klasszikus futtató fiókkal.

1. Az Azure Portalon nyissa meg a korábban létrehozott Automation-fiókot.

2. Kattintson a **Runbookok** csempére a forgatókönyvek listájának megnyitásához.

3. Válassza ki az **AzureClassicAutomationTutorialScript** runbookot, majd a runbook elindításához kattintson az **Indítás** gombra. Az alábbi események történnek:

 * Létrejön egy [runbookfeladat](automation-runbook-execution.md), megjelenik a **Feladat** panel, a feladat állapota pedig megjelenik a **Feladat összegzése** csempén.
 * A feladat állapota kezdetben **Várólistán**, ami jelzi, hogy egy felhőbeli runbookfeldolgozó elérhetővé válására vár.
 * Az állapot akkor változik **Indítás** értékre, ha egy feldolgozó elvállalja a feladatot.
 * Amikor a runbook elkezd futni, az állapota **Fut** értékre változik.
 * Ha befejeződik a runbookfeladat, a **Befejezve** állapot jelenik meg.

    ![Rendszerbiztonsági tag forgatókönyvtesztje](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Ha szeretné megtekinteni a runbook részletes eredményeit, kattintson a **Kimenet** csempére.  
Az ekkor megjelenő **Kimenet** panelen látható, hogy a runbook sikeresen elvégezte a hitelesítést, és visszaadta az előfizetésben elérhető összes klasszikus virtuális gép listáját.

5. Zárja be a **Kimenet** panelt, és lépjen vissza a **Feladat összegzése** panelre.

6. Zárja be a **Feladat összegzése** panelt és a megfelelő **AzureAutomationTutorialScript** runbook paneljét.

## <a name="managing-your-run-as-account"></a>Futtató fiók kezelése
Az Automation-fiók lejárata előtt meg kell újítania a tanúsítványt. Ha úgy véli, hogy a futtató fiók biztonsága sérült, akkor törölheti, majd újra létrehozhatja a fiókot. Ebben a részben ezeknek a műveleteknek a végrehajtását ismertetjük.

### <a name="self-signed-certificate-renewal"></a>Önaláírt tanúsítvány megújítása
A futtató fiókhoz létrehozott önaláírt tanúsítvány a létrehozás dátumától számítva egy év múlva jár le. A tanúsítványt bármikor meg lehet újítani a lejárata előtt. A megújításkor a jelenleg érvényes tanúsítványt megőrzi a rendszer, hogy a művelet ne érintse hátrányosan a várólistán lévő vagy aktívan futó runbookokat, amelyek hitelesítést végeznek a futtató fiókkal. A tanúsítvány a lejárati dátumáig érvényes marad.

> [!NOTE]
> Ha úgy konfigurálta az Automation futtató fiókot, hogy a vállalati hitelesítésszolgáltató által kibocsátott tanúsítványt használja, és ezt a lehetőséget alkalmazza, a vállalati tanúsítvány egy önaláírt tanúsítványra lesz lecserélve.

A tanúsítvány megújításához tegye a következőket:

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. Az **Automation-fiók** panelen, a **Fióktulajdonságok** ablaktábla **Fiókbeállítások** területén válassza a **Futtató fiókok** lehetőséget.

    ![Az Automation-fiók tulajdonságpanelje](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. A **Futtató fiókok** tulajdonságpaneljén válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelynek a tanúsítványát meg kívánja újítani.

4. A kiválasztott fiók **Tulajdonságok** paneljén kattintson a **Tanúsítvány megújítása** elemre.

    ![Futtató fiók tanúsítványának megújítása](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. A tanúsítvány megújítása során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Futtató fiók vagy klasszikus futtató fiók törlése
Ez a témakör ismerteti, hogyan törölhet és hozhat újra létre futtató fiókot vagy klasszikus futtató fiókot. A művelet során a rendszer megőrzi az Automation-fiókot. A törölt futtató fiókot vagy klasszikus futtató fiókot újra létrehozhatja az Azure Portalon.

1. Az Azure Portalon nyissa meg az Automation-fiókot.

2. Az **Automation-fiók** panelen, a fiók tulajdonságait tartalmazó ablaktáblán válassza a **Futtató fiókok** lehetőséget.

3. A **Futtató fiókok** tulajdonságpaneljén válassza ki azt a futtató fiókot vagy klasszikus futtató fiókot, amelyet törölni kíván. Ezután a kiválasztott fiók **Tulajdonságok** paneljén kattintson a **Törlés** elemre.

 ![Futtató fiók törlése](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. A fiók törlése során a menü **Értesítések** részén nyomon követheti a folyamat állapotát.

5. A törlés után újra létrehozhatja a fiókot a **Futtató fiókok** tulajdonságpaneljén az **Azure-alapú futtató fiók** lehetőség kiválasztásával.

 ![Automation futtató fiók újbóli létrehozása](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Hibás konfiguráció
Előfordulhat, hogy a futtató fiók vagy klasszikus futtató fiók megfelelő működéséhez szükséges valamelyik konfigurációs elem törlődött, illetve nem megfelelően hozták létre az első beállításkor. Ilyen elemek például a következők:

* Tanúsítványobjektum
* Kapcsolatobjektum
* A futtató fiók el lett távolítva a közreműködői szerepkörből
* Egyszerű szolgáltatás vagy alkalmazás az Azure AD-ben

Az előző és más hibás konfigurációk esetében az Automation-fiók észleli a módosításokat, és *Hiányos* állapotot jelez a fiók **Futtató fiókok** tulajdonságpaneljén.

![Hiányos futtatófiók-konfigurációs állapot](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

Ha kiválasztja a futtató fiókot, a fiók **Tulajdonságok** paneljén a következő hibaüzenet jelenik meg:

![Hiányos futtatófiók-konfigurációra figyelmeztető üzenet](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

A futtató fiókkal kapcsolatos hasonló problémákat gyorsan elháríthatja a fiók törlésével és ismételt létrehozásával.

## <a name="update-your-automation-account-by-using-powershell"></a>Az Automation-fiók frissítése a PowerShell használatával
A PowerShell segítségével frissítheti meglévő Automation-fiókját. Erre a következő esetekben lehet szükség:

* Létrehozott egy Automation-fiókot, de futtató fiókot nem.
* Már használ egy, a Resource Manager-erőforrások felügyeletére szolgáló Automation-fiókot, de szeretné frissíteni, hogy runbookos hitelesítést lehetővé tevő futtató fiókot is tartalmazzon.
* Már használ egy, a klasszikus erőforrások felügyeletére szolgáló Automation-fiókot, de szeretné azt frissíteni, és klasszikus futtató fiókként használni, hogy megtakarítsa az új fiók létrehozásához és a runbookok és objektumok áthelyezéséhez szükséges időt.   
* Egy futtató és egy klasszikus futtató fiókot szeretne létrehozni a vállalati hitelesítésszolgáltató által kibocsátott tanúsítvánnyal.

A szkriptre a következő előfeltételek vonatkoznak:

* Ez a szkript kizárólag Windows 10 és Azure Resource Manager 2.01-es vagy újabb modulokkal rendelkező Windows Server 2016 rendszeren futtatható. A korábbi Windows-verziók esetében nem támogatott.
* Az Azure PowerShell 1.0-s és újabb verziói. Információk a PowerShell 1.0-s kiadásáról: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
* Olyan Automation-fiók, amelyre az alábbi PowerShell-szkript az *–AutomationAccountName* és az *-ApplicationDisplayName* paraméterek értékeként hivatkozik.

A szkript végrehajtásához feltétlenül szükséges *SubscriptionID*, *ResourceGroup* és *AutomationAccountName* paraméterek értékének lekéréséhez tegye a következőket:
1. Az Azure Portalon válassza ki Automation-fiókját az **Automation-fiók** panelen, majd válassza a **Minden beállítás** elemet. 
2. A **Minden beállítás** panel **Fiókbeállítások** részénél válassza a **Tulajdonságok** lehetőséget. 
3. Jegyezze fel a **Tulajdonságok** panelen megjelenő értékeket.

 ![Az Automation-fiók „Tulajdonságok” panelje](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Futtató fiókhoz használható PowerShell-szkript létrehozása
Ez a PowerShell-szkript a következő konfigurációk támogatását tartalmazza:

* Futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával.
* Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával.

A szkript az alábbi elemeket hozza létre a kiválasztott konfigurációs beállításoktól függően.

**Futtató fiókok esetén:**

* Létrehoz egy Azure AD-alkalmazást, amelynek az exportálása az önaláírt vagy vállalati tanúsítvány nyilvános kulcsával történik, továbbá létrehoz egy egyszerű szolgáltatásfiókot az alkalmazáshoz az Azure AD-ben, és hozzárendeli a Közreműködő szerepkört ehhez a fiókhoz a jelenlegi előfizetésében. Ezt a beállítást bármikor módosíthatja Tulajdonos értékre vagy bármely egyéb szerepkörre. További információk: [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md).
* Létrehoz egy *AzureRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza az Azure AD-alkalmazás által használt titkos tanúsítványkulcsot.
* Létrehoz egy *AzureRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum magában foglalja az alkalmazásazonosítót, a bérlőazonosítót, az előfizetés-azonosítót és a tanúsítvány ujjlenyomatát.

**Klasszikus futtatófiókok esetében:**

* Létrehoz egy *AzureClassicRunAsCertificate* nevű Automation-tanúsítványobjektumot a megadott Automation-fiókban. Ez a tanúsítványobjektum tartalmazza a felügyeleti tanúsítvány által használt titkos tanúsítványkulcsot.
* Létrehoz egy *AzureClassicRunAsConnection* nevű Automation-kapcsolatobjektumot a megadott Automation-fiókban. Ez a kapcsolatobjektum tartalmazza az előfizetés nevét, a subscriptionId paramétert, valamint a tanúsítványobjektum nevét.

>[!NOTE]
> Ha klasszikus futtató fiók létrehozását választja, a szkript végrehajtása után töltse fel a nyilvános tanúsítványt (.cer fájlnévkiterjesztéssel) annak az előfizetésnek a felügyeleti tárába, amelyben az Automation-fiókot létrehozták.
> 

A szkript végrehajtásához és a tanúsítvány feltöltéséhez tegye a következőket:

1. Mentse el a következő parancsprogramot a számítógépén. Ebben a példában mentse a következő fájlnéven: *New-RunAsAccount.ps1*.

        #Requires -RunAsAdministrator
         Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
           Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
           return
        }

        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create a Run As account by using a service principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"

        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
          $CertificateName = $AutomationAccountName+$CertifcateAssetName
          $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
          $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
          $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}


        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
            $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
            $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
            $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
            $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#"

             if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
             $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
             $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
             $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        } else {
             $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
             $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
             $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
             $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
             $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. A számítógépén kattintson a **Start** gombra, majd indítsa el a **Windows PowerShellt** emelt szintű felhasználói jogokkal.

3. Az emelt szintű PowerShell parancssori felületből lépjen abba a mappába, amely az 1. lépésben létrehozott szkriptet tartalmazza.

4. Futtassa a szkriptet a kívánt konfigurációhoz szükséges paraméterértékekkel.

    **Futtató fiók létrehozása önaláírt tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Futtató fiók és klasszikus futtató fiók létrehozása önaláírt tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Futtató fiók és klasszikus futtató fiók létrehozása vállalati tanúsítvány használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Futtató fiók és klasszikus futtató fiók létrehozása az Azure Government Cloud egyik önaláírt tanúsítványának használatával**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > A rendszer az Azure-ral történő hitelesítést fogja kérni a szkript futtatása után. Jelentkezzen be egy olyan fiókkal, amely tagja az Előfizetés-adminisztrátorok szerepkörnek, és emellett az előfizetés társadminisztrátorának is számít.
    >
    >

A szkript sikeres futtatása után jegyezze fel a következőket:
* Ha önaláírt nyilvános tanúsítvánnyal (.cer fájl) ellátott klasszikus futtató fiókot hoz létre, a szkript létrehozza és menti a tanúsítványt a számítógépen a PowerShell-munkamenet végrehajtásához használt *%USERPROFILE%\AppData\Local\Temp* felhasználói profilhoz tartozó ideiglenes mappába.
* Ha egy (.cer formátumú) vállalati tanúsítvánnyal rendelkező klasszikus futtató fiókot hozott létre, használja ezt a tanúsítványt. Kövesse az utasításokat, amelyek bemutatják [a felügyeleti API-tanúsítványok klasszikus Azure portálra való feltöltését](../azure-api-management-certs.md), majd ellenőrizze a hitelesítő adatok konfigurációját a Service Management-erőforrásokkal. Ehhez használja a [Service Management-erőforrásokkal](#sample-code-to-authenticate-with-service-management-resources) való hitelesítéshez használt mintakódot. 
* Ha *nem* klasszikus futtató fiókot hozott létre, állítsa be a hitelesítést a Resource Manager-erőforrásokkal, valamint ellenőrizze a hitelesítő adatok konfigurációját. Ehhez használja a [Service Management-erőforrásokkal való hitelesítéshez használt mintakódot.](#sample-code-to-authenticate-with-resource-manager-resources)

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Mintakód a Resource Manager-erőforrásokkal történő hitelesítéshez
A Resource Manager-erőforrások runbookkal való kezeléséhez használhatja az alábbi, az *AzureAutomationTutorialScript* mintaforgatókönyvből származó frissített mintakódot a futtató fiók használatával történő hitelesítéshez.

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }

A szkript tartalmaz két további kódsort az előfizetési környezet hivatkozásának támogatásához, így könnyedén tud több előfizetéssel is dolgozni. Egy *SubscriptionId* nevű változó objektum tartalmazza az előfizetés azonosítóját. Az `Add-AzureRmAccount` parancsmag-utasítás után a [`Set-AzureRmContext`](https://msdn.microsoft.com/library/mt619263.aspx) parancsmag a *-SubscriptionId* paraméterkészlettel lesz kiadva. Ha a változó neve túl általános, módosíthatja, hogy tartalmazzon egy előtagot vagy igazodjon más elnevezési konvenciókhoz, és könnyebbé tegye az azonosítását. Alternatív megoldásként a *-SubscriptionId* helyett használhatja a *-SubscriptionName* paraméterkészletet egy megfelelő változóobjektummal.

A runbookban használt hitelesítési parancsmag (`Add-AzureRmAccount`) a *ServicePrincipalCertificate* paraméterkészletet használja. Ez a megoldás a szolgáltatástanúsítvány segítségével, és nem a felhasználói hitelesítő adatokkal végzi el a hitelesítést.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Mintakód a Service Manager-erőforrásokkal történő hitelesítéshez
Ha a klasszikus futtató fiókkal szeretne hitelesítést végezni a klasszikus erőforrások forgatókönyvekkel való felügyeletéhez, használhatja az alábbi, az *AzureClassicAutomationTutorialScript* mintarunbookból származó frissített mintakódot is.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>Következő lépések
* [Alkalmazás- és egyszerű szolgáltatási objektumok Azure AD-ben](../active-directory/active-directory-application-objects.md)
* [Szerepköralapú hozzáférés-vezérlés az Azure Automationben](automation-role-based-access-control.md)
* [Azure Cloud Services – tanúsítványok áttekintése](../cloud-services/cloud-services-certs-create.md)

