---
title: "Az első Azure Automation PowerShell-runbookom | Microsoft Docs"
description: "Ez az oktatóanyag bemutatja egy egyszerű PowerShell-forgatókönyv létrehozását, tesztelését és közzétételét."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "azure powershell, powershell-szkript oktatóanyag, powershell automation"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2017
ms.author: magoedte;sngun
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 4b32011b72acc647d4af44bb5ccbcaab408fb4d6
ms.lasthandoff: 03/27/2017


---
# <a name="my-first-powershell-runbook"></a>Az első PowerShell-forgatókönyvem

> [!div class="op_single_selector"]
> * [Grafikus](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell-munkafolyamat](automation-first-runbook-textual.md)
> 
> 

Ez az oktatóanyag bemutatja, hogyan hozhat létre az Azure Automationben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks). Egy egyszerű runbookkal kezdünk, amelyet tesztelünk és közzé is teszünk, és bemutatjuk a runbook állapotának nyomon követését is. Ezután módosítjuk a runbookot, hogy ténylegesen kezeljen Azure-erőforrásokat, ebben az esetben elindítson egy Azure-beli virtuális gépet. Végül még robusztusabbá tesszük a runbookot, és runbook-paramétereket adunk hozzá.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve <a href="/pricing/free-account/" target="_blank">[regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
* [Automation-fiók](automation-sec-configure-azure-runas-account.md) a forgatókönyv tárolásához és az Azure erőforrásokban való hitelesítéshez.  Ennek a fióknak jogosultsággal kell rendelkeznie a virtuális gép elindításához és leállításához.
* Egy Azure virtuális gép. Ezt a gépet leállítjuk és elindítjuk, tehát ne olyan virtuális gépet használjon, amely élesben működik.

## <a name="step-1---create-new-runbook"></a>1. lépés – Új forgatókönyv létrehozása
Először egy egyszerű forgatókönyvet hozunk létre, amely a *Hello World* szöveget adja vissza.

1. Az Azure Portalon nyissa meg az Automation-fiókját.  
   Az Automation-fiók oldala gyors áttekintést nyújt a fiókban levő erőforrásokról. Valószínűleg már rendelkezik adategységekkel. Ezek nagy része az új Automation-fiókhoz automatikusan hozzáadott modul. Rendelkeznie kell az [előfeltételek](#prerequisites) között említett hitelesítő adategységgel is.
2. Kattintson a **Runbookok** csempére a forgatókönyvek listájának megnyitásához.<br><br> ![RunbooksControl](media/automation-first-runbook-textual-powershell/runbooks-control-tiles.png)  
3. Hozzon létre egy új runbookot a **Forgatókönyv hozzáadása**, majd az **Új forgatókönyv létrehozása** elemre kattintva.
4. Adja a forgatókönyvnek a *MyFirstRunbook-PowerShell* nevet.
5. Ebben az esetben egy [PowerShell-forgatókönyvet](automation-runbook-types.md#powershell-runbooks) hozunk létre, ezért a **Forgatókönyv típusának** válassza a **PowerShell** lehetőséget.<br><br> ![Runbook típusa](media/automation-first-runbook-textual-powershell/automation-runbook-type.png)  
6. A forgatókönyv létrehozásához és a szöveges szerkesztő megnyitásához kattintson a **Létrehozás** gombra.

## <a name="step-2---add-code-to-the-runbook"></a>2. lépés – Kód hozzáadása a forgatókönyvhöz
Beírhat közvetlenül a forgatókönyvbe kódot, vagy választhat parancsmagokat, forgatókönyveket és adategységeket a Könyvtár vezérlőből, majd hozzáadhatja őket a forgatókönyvhöz a kapcsolódó paraméterekkel együtt. Ebben a bemutatóban közvetlenül a runbookba írunk.

1. A forgatókönyvünk jelenleg üres, írja be a következőt: *Write-Output "Hello World."*.<br><br> ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)  
2. A **Mentés** gombra kattintva mentse el a forgatókönyvet.<br><br> ![Mentés gomb](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-save.png)  

## <a name="step-3---test-the-runbook"></a>3. lépés – A forgatókönyv tesztelése
Mielőtt közzétesszük a forgatókönyvet, hogy éles üzemben is elérhető legyen, tesztelnünk kell, hogy biztosan jól működik-e. Egy forgatókönyv tesztelésekor a **Piszkozat** verziót futtatja, és interaktív módon megtekinti a kimenetét.

1. Kattintson a **Teszt panelre** a Teszt panel megnyitásához.<br><br> ![Teszt panel](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-test.png)  
2. Kattintson az **Indítás** gombra a teszt elindításához. Elvileg ez az egyetlen engedélyezett lehetőség.
3. Létrejön egy [forgatókönyv-feladat](automation-runbook-execution.md), és megjelenik annak állapota.  
   A feladat állapota kezdetben *Várólistán*. Ez azt jelöli, hogy egy felhőben lévő runbook-feldolgozó elérhetővé válására vár. Ezután *Indítás* állapotúra változik, ha egy feldolgozó elvállalja a feladatot, majd *Fut* állapotúra, amikor a forgatókönyv elkezd futni.  
4. Amikor a forgatókönyv feladat befejeződik, megjelenik a kimenete. A mi esetünkben ez a következő: *Hello World*.<br><br> ![Teszt panel kimenete](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)  
5. A vászonra való visszatéréshez zárja be a Teszt panelt.

## <a name="step-4---publish-and-start-the-runbook"></a>4. lépés: Közzététel és a forgatókönyv indítása
A létrehozott runbook még mindig Piszkozat módban van. Az üzemi környezetben való futtatás előtt közzé kell tennünk.  Amikor elérhetővé tesz egy forgatókönyvet, felülírja a Közzétett verziót a Piszkozattal.  Az esetünkben még nincs Közzétett verzió, mivel még csak most hoztuk létre a forgatókönyvet.

1. A forgatókönyv közzétételéhez kattintson a **Közzététel** lehetőségre, és ha a rendszer kéri, kattintson az **Igen** gombra.<br><br> ![Közzététel gomb](media/automation-first-runbook-textual-powershell/automation-runbook-edit-controls-publish.png)  
2. Ha most balra görgetve megtekinti a forgatókönyvet a **Forgatókönyvek** panelen, a **Közzétett** **Szerzői állapot** jelenik meg.
3. Görgessen vissza jobbra a **MyFirstRunbook-PowerShell** panel megtekintéséhez.  
   A felül látható lehetőségekkel elindíthatjuk és megtekinthetjük a runbookot, ütemezhetjük egy későbbi időpontban való indításra, vagy létrehozhatunk egy [webhookot](automation-webhooks.md), amely segítségével elindítható a runbook egy HTTP-hívással.
4. Most csak el szeretnénk indítani a runbookot, tehát kattintson az **Indítás**, majd, amikor a Runbook indítása panel megnyílik, kattintson az **OK** gombra.<br><br> ![Indítás gomb](media/automation-first-runbook-textual-powershell/automation-runbook-controls-start.png)<br>    
5. Megnyílik egy feladatpanel a létrehozott runbook-feladathoz. Ezt a panelt bezárhatjuk, de ebben az esetben nyitva hagyjuk, hogy lássuk a feladat előrehaladását.
6. A feladat állapota a **Feladat összegzése** területen látszik, és megfelel a korábban, a forgatókönyv tesztelésekor látott állapotoknak.<br><br> ![Feladat összegzése](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)<br>  
7. Ha a forgatókönyv a *Befejezve* állapotot mutatja, kattintson a **Kimenet** lehetőségre. A Kimenet panel megnyílik, és megjelenik a *Hello World* feliratunk.<br><br> ![Feladat kimenete](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)<br> 
8. Zárja be a Kimenet panelt.
9. A forgatókönyv-feladathoz tartozó Streamek panel megnyitásához kattintson **Az összes napló** lehetőségre. A kimeneti streamben csak a *Hello World* eredményt látjuk, de ez megjeleníthet egyéb streameket is egy forgatókönyv feladatból, mint a Részletes vagy a Hiba, ha a forgatókönyv ezekbe ír.<br><br> ![Minden napló](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)<br>   
10. A MyFirstRunbook-PowerShell panelre való visszatéréshez zárja be a Streamek és a Feladat panelt.
11. A forgatókönyv Feladatok paneljének megnyitásához kattintson a **Feladatok** lehetőségre. Ez felsorolja az összes, a forgatókönyv által létrehozott feladatot. Egy feladat csak egyszer szerepel a listán, mert csak egyszer futtattuk a feladatot.<br><br> ![Feladatlista](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)  
12. Erre a feladatra kattintva megnyithatja ugyanazt a Feladat panelt, amelyet már láttunk a runbook elindításakor. Ez lehetővé teszi, hogy az időben visszamenve megtekintse egy adott forgatókönyvhöz létrehozott összes feladat részleteit.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5. lépés – Hitelesítés hozzáadása az Azure-erőforrások kezeléséhez
Most már teszteltük és közzétettük a forgatókönyvet, de még nem csinál semmi hasznosat. Azt szeretnénk, hogy Azure-erőforrásokat kezeljen. Erre nem lesz képes egészen addig, amíg nem végez hitelesítést az [előfeltételek](#prerequisites) között említett hitelesítő adatokkal. Ezt az **Add-AzureRMAccount** parancsmaggal hajtjuk végre.

1. Kattintson a MyFirstRunbook-PowerShell panel **Szerkesztés** gombjára szöveges szerkesztő megnyitásához.<br><br> ![Runbook szerkesztése](media/automation-first-runbook-textual-powershell/automation-runbook-controls-edit.png)<br>   
2. A **Write-Output** sorra már nincs szükségünk, így ezt kitörölheti.
3. Írja be, vagy másolja és illessze be a következő kódot, amely kezeli a hitelesítést az Automation futtató fiókjával:
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   ```
   <br>
4. Kattintson a **Teszt panel** elemre, hogy tesztelhessük forgatókönyvet.
5. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, a kimenetnek a fiókja alapvető adatait kell megjelenítenie, a következőhöz hasonló módon. Ez ellenőrzi, hogy érvényes-e a hitelesítő adat.<br><br> ![Hitelesítés](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6. lépés: Kód hozzáadása a virtuális gép indításához
Most, hogy a forgatókönyv hitelesítést végez az Azure-előfizetésünkön, kezelhetjük az erőforrásokat. Hozzáadunk egy parancsot a virtuális gép elindítására. Kiválaszthatja az Azure-előfizetésében lévő bármelyik virtuális gépet, és most szoftveresen rögzítjük ezt a nevet a runbookban.

1. Az *Add-AzureRmAccount* után írja be a következőt: *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'*. Ezzel megadja az elindítani kívánt virtuális gép nevét, valamint az erőforráscsoportjának nevét.  
   
   ```
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```
   <br>
2. Mentse a forgatókönyvet, majd kattintson a **Teszt panel** elemre, hogy tesztelhessük.
3. Kattintson az **Indítás** gombra a teszt elindításához. Ha kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7. lépés – Bemeneti paraméter hozzáadása a forgatókönyvhöz
A runbookunk jelenleg elindítja a runbookban szoftveresen rögzített virtuális gépet, de hasznosabb lenne, ha meg tudnánk adni a virtuális gépet a runbook elindulásakor. Most hozzáadunk bemeneti paramétereket a forgatókönyvhöz, amelyek biztosítják ezt a működést.

1. Adja hozzá a *VMName* és a *ResourceGroupName* paramétereket a forgatókönyvhöz, és használja ezeket a változókat a **Start-AzureRmVM** parancsmaggal, amint az az alábbi példában látható.  
   
   ```
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```
   <br>
2. Mentse a forgatókönyvet, és nyissa meg a Teszt panelt. Most már megadhat értékeket a teszt során használt két bemeneti változóhoz.
3. Zárja be a Teszt panelt.
4. A forgatókönyv új verziójának közzétételéhez kattintson a **Közzététel** lehetőségre.
5. Állítsa le az előző lépésben elindított virtuális gépet.
6. Kattintson az **Indítás** gombra a forgatókönyv elindításához. Írja be a **VMName** és a **ResourceGroupName** értéket az elindítani kívánt virtuális géphez.<br><br> ![Paraméter átadása](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>  
7. Ha a forgatókönyv kész, ellenőrizze, hogy a virtuális gép elindult-e.

## <a name="differences-from-powershell-workflow"></a>Eltérések a PowerShell-munkafolyamattól
A PowerShell-runbookok életciklusa, képességei és felügyelete megegyezik a PowerShell-alapú munkafolyamat-runbookokéval, de van néhány eltérés és korlátozás:

1. A PowerShell-forgatókönyvek a munkafolyamat-forgatókönyvekhez képest gyorsabban futnak, mert nem szerepel bennük a fordítási lépés.
2. A PowerShell-alapú munkafolyamat-forgatókönyvek támogatják az ellenőrzőpontokat, és az ellenőrzőpontok segítségével a PowerShell-alapú munkafolyamat-forgatókönyvek a forgatókönyv bármely pontjától folytathatók, ezzel szemben egy PowerShell-forgatókönyv csak az elejéről indítható újra.
3. A PowerShell-alapú munkafolyamat-forgatókönyvek támogatják a párhuzamos és soros végrehajtást is, míg a PowerShell-forgatókönyvek csak sorosan képesek végrehajtani a parancsokat.
4. Egy PowerShell-alapú munkafolyamat-runbookban egy tevékenység-, parancs- vagy parancsfájlblokk rendelkezhet saját futási térrel, míg a PowerShell-runbookokban minden parancsfájl egyetlen futási térben fut. Van néhány [szintaktikai eltérés](https://technet.microsoft.com/magazine/dn151046.aspx) is a natív PowerShell-forgatókönyvek és a PowerShell-alapú munkafolyamat-forgatókönyvek között.

## <a name="next-steps"></a>Következő lépések
* A grafikus forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első grafikus forgatókönyvem](automation-first-runbook-graphical.md).
* A PowerShell-alapú munkafolyamat-forgatókönyvekkel való ismerkedéshez tekintse meg a következőt: [Az első PowerShell-alapú munkafolyamat-forgatókönyvem](automation-first-runbook-textual.md)
* További információk a forgatókönyvek típusairól, az előnyeikről és a korlátaikról: [Az Azure Automation forgatókönyveinek típusai](automation-runbook-types.md)
* További információk a PowerShell-parancsprogramok támogatásáról: [PowerShell-parancsprogramok natív támogatása az Azure Automationben](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)


