---
title: "Frissítéskezelési megoldás az OMS-ben | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja ezt a megoldást Windows és Linux rendszerű számítógépek frissítéseinek kezelésére."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 18aa17f6af7fe492f3875e1af7cb06b613f171af
ms.lasthandoff: 04/03/2017


---
# <a name="update-management-solution-in-oms"></a>Frissítéskezelési megoldás az OMS-ben
Az OMS-beli Frissítéskezelési megoldással Windows és Linux rendszerű számítógépek frissítéseit kezelheti.  Az elérhető frissítések állapota minden ügynökszámítógépen egyszerűen felmérhető, és elindítható a kiszolgálók szükséges frissítéseinek telepítése is. 

## <a name="prerequisites"></a>Előfeltételek
* A megoldás a frissítések állapotának felmérését csak Windows Server 2008 vagy újabb, a környezetek frissítését pedig csak Windows Server 2012 vagy újabb rendszerek esetében támogatja.  A Server Core és a Nano Server telepítési lehetőségek nem támogatottak.
* Az ügyféloldali Windows operációs rendszerek nem támogatottak.  
* A Windows rendszerű ügynökszámítógépeket vagy a Windows Server Update Services (WSUS) szolgáltatással való kommunikációhoz kell konfigurálni, vagy a Microsoft Update szolgáltatáshoz kell hozzáféréssel rendelkezniük.  
  
  > [!NOTE]
  > A Windows-ügynök ezzel egyidejűleg nem felügyelhető a System Center Configuration Manager használatával.  
  > 
  > 
* A Linux-ügynököknek hozzáféréssel kell rendelkezniük valamely frissítési tárházhoz.  A Linuxhoz készült OMS-ügynök letölthető a [GitHubról](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Konfiguráció
A Frissítéskezelési megoldás OMS-munkaterülethez való hozzáadásához és Linux-ügynökök felvételéhez hajtsa végre az alábbi lépéseket. Windows-ügynökök hozzáadása automatikusan történik, esetükben nincs szükség további konfigurációs beállításokra.

> [!NOTE]
> Ha engedélyezi ezt a megoldást, az OMS-munkaterülethez kapcsolódó Windows-számítógépeket a rendszer automatikusan hibrid runbook-feldolgozóként konfigurálja a megoldásban lévő runbookok támogatása érdekében.  A számítógépeket azonban a rendszer nem regisztrálja az Automation-fiókban esetleg már létrehozott hibridfeldolgozó-csoportok egyikében sem.  Viszont hozzáadhatók hibrid runbook-feldolgozó csoporthoz az Automation-fiókban az Automation-runbookok támogatása érdekében, ha ugyanazon fiókot használja a megoldáshoz és a hibrid runbook-feldolgozó csoporttagsághoz is.  Ez a funkció a hibrid runbook-feldolgozó 7.2.12024.0-s verziójától érhető el.   

1. Az OMS-munkaterülethez adja hozzá a Frissítéskezelési megoldást. Ehhez kövesse a megoldástárban található [OMS-megoldás hozzáadása](../log-analytics/log-analytics-add-solutions.md) című témakörben leírt eljárást.  
2. Az OMS-portálon válassza a **Beállítások**, majd az **Összekapcsolt források** elemet.  Jegyezze fel a **munkaterület azonosítóját** és vagy az **elsődleges kulcsot**, vagy a **másodlagos kulcsot**.
3. Minden Linux-számítógép esetében végezze el az alábbi lépéseket.
   
   a.    A linuxos OMS-ügynök legújabb verziójának telepítéséhez futtassa az alábbi parancsot.  A <Workspace ID> helyére írja be a munkaterület azonosítóját, a <Key> helyére pedig vagy az elsődleges kulcsot, vagy a másodlagos kulcsot.
   
        cd ~
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID>  -s <PrimaryKey> -d opinsights.azure.com 

   b. Az ügynök eltávolításához kövesse [az OMS ügynök Linux rendszerről történő eltávolításával kapcsolatos](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#uninstalling-the-oms-agent-for-linux) szakasz lépéseit.  

## <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportja össze van kapcsolva az OMS-munkaterülettel, ennek a megoldásnak a hozzáadásakor az alábbi felügyeleti csomagok is telepítve lesznek az Operations Managerben. Ezek a felügyeleti csomagok nem igényelnek további konfigurációs vagy karbantartási feladatokat. 

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Igen |A megoldás információt szerez be Windows-ügynököktől a rendszerfrissítésekről, és kezdeményezi a szükséges frissítések telepítését. |
| Linux-ügynökök |Igen |A megoldás információt szerez be a Linux-ügynököktől a rendszerfrissítésekről. |
| Az Operations Manager felügyeleti csoportja |Igen |A megoldás információt szerez be a csatlakoztatott felügyeleti csoportban lévő ügynököktől a rendszerfrissítésekről.<br>Ehhez nem szükséges, hogy közvetlen kapcsolat legyen az Operations Manager-ügynök és a Log Analytics között. Az adatok a felügyeleti csoportból az OMS-tárházba lesznek továbbítva. |
| Azure Storage-fiók |Nem |Az Azure Storage nem tartalmaz rendszerfrissítésekkel kapcsolatos információt. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága
Minden felügyelt Windows-számítógép esetében naponta kétszer történik vizsgálat.  Az információk a telepítést követő 15 percen belül frissülnek.  

Linux-számítógépek esetében a vizsgálat három óránként történik.  

## <a name="using-the-solution"></a>A megoldás használata
A Frissítéskezelési megoldás hozzáadásakor az OMS-munkaterületen a **Frissítéskezelés** csempe felkerül az OMS-irányítópultra. Ez a csempe a környezetben jelenleg elérhető, frissítést igénylő számítógépek számát és grafikus ábrázolását jeleníti meg.<br><br>
![Frissítéskezelés – áttekintő csempe](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>A frissítési felmérések áttekintése
A **Frissítéskezelés** irányítópultjának megnyitásához kattintson a **Frissítéskezelés** csempére. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlopban legfeljebb tíz olyan elem jelenik meg, amely megfelel a megadott hatóköri és időtartományi kritériumoknak. Az oszlop alján található **Az összes megtekintése** elemre vagy az oszlop fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

| Oszlop | Leírás |
| --- | --- |
| **Frissítést igénylő számítógépek** | |
| Kritikus vagy biztonsági frissítések |Az első tíz, frissítést igénylő számítógépet listázza a hiányzó frissítések száma alapján sorba rendezve. A számítógép nevére kattintva naplókeresést indíthat el, amellyel megjelenítheti az adott számítógépre vonatkozó összes frissítési bejegyzést. |
| 30 napnál régebbi kritikus vagy biztonsági frissítések |A kritikus vagy biztonsági frissítéseket igénylő számítógépeket azonosítja a frissítés közzététele óta eltelt idő alapján sorba rendezve. A bejegyzés nevére kattintva keresést indíthat a naplóban, amellyel megjeleníti az összes hiányzó és kritikus frissítést. |
| **Hiányzó szükséges frissítések** | |
| Kritikus vagy biztonsági frissítések |A számítógépekről hiányzó frissítéseket kategorizálva listázza aszerint sorba rendezve, hogy az adott kategórián belüli frissítések hány számítógépről hiányoznak. Az egyes kategóriák nevére kattintva naplókeresést indíthat el, amely megjeleníti az adott kategória összes frissítési bejegyzését. |
| **Frissítéstelepítések** | |
| Frissítéstelepítések |A jelenleg beütemezett frissítéstelepítések számát, valamint a következő beütemezett telepítésig hátralévő időt jeleníti meg.  A csempére kattintva megjelenítheti az ütemezéseket, a folyamatban lévő és a befejezett frissítéseket, továbbá új telepítés ütemezését végezheti el. |

<br>  
![Frissítéskezelés – áttekintő irányítópult](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Frissítéskezelés irányítópultja – számítógép nézet](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Frissítéskezelés irányítópultja – csomag nézet](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Frissítések telepítése
A környezetben szereplő összes Windows-számítógép frissítési felmérését követően a szükséges frissítések telepítését *frissítéstelepítés* létrehozásával végezheti el.  A frissítéstelepítés egy vagy több Windows-számítógép szükséges frissítéseinek ütemezett telepítése.  Ehhez meg kell adnia a központi telepítés dátumát és idejét, valamint meg kell határoznia a telepítésben érintett számítógépet vagy számítógépcsoportot.  

A telepítést az Azure Automation runbookjai végzik.  A runbookok nem tekinthetők meg, és nem kívánnak semmilyen konfigurálást.  Frissítéstelepítés létrehozásakor egy ütemezés jön létre, amely a megadott időben a megadott számítógépekre irányuló frissítési mester runbookot indít el.  A mester runbook minden Windows-ügynökön egy gyermek runbookot indít, amely elvégzi a szükséges frissítések telepítését.  

Az Azure Marketplace-en elérhető, igény szerinti Red Hat Enterprise Linux- (RHEL-) rendszerképekből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat Update Infrastructure (RHUI)](../virtual-machines/linux/update-infrastructure-redhat.md) eléréséhez.  Minden más Linux-változatot a disztribúciók online fájltárolójából kell frissíteni, a támogatott eljárásokat követve.  

### <a name="viewing-update-deployments"></a>Frissítéstelepítések megtekintése
Az elérhető frissítéstelepítések listáját a **Frissítéstelepítés** csempére kattintva tekintheti meg.  A lista a frissítések állapota szerinti csoportosításban jelenik meg: **Ütemezett**, **Futó** és **Befejezett**.<br><br> ![Frissítéstelepítési ütemezési lap](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

A frissítéstelepítések tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
| --- | --- |
| Név |A frissítéstelepítés neve. |
| Ütemezés |Az ütemezés típusa.  Jelenleg az *OneTime* (Egyszeri) az egyetlen lehetséges érték. |
| Kezdési idő |A frissítéstelepítés ütemezett kezdetének dátuma és időpontja. |
| Időtartam |A frissítéstelepítés engedélyezett futási idejének hossza percekben megadva.  Ha ez alatt az idő alatt nem fejeződik be minden frissítés telepítése, akkor a hátramaradó frissítések csak a következő ütemezett frissítéstelepítés során kerülnek telepítésre. |
| Kiszolgálók |A frissítéstelepítés által érintett számítógépek száma. |
| status |A frissítéstelepítés aktuális állapota.<br><br>Lehetséges értékek:<br>– Nincs elindítva<br>– Fut<br>– Befejeződött |

Az egyes frissítéstelepítésekre kattintva megjeleníthető a részletes nézetű képernyő, amely az alábbi táblázatban felsorolt oszlopokat tartalmazza.  Ha a frissítéstelepítés még nem indult el, az oszlopok nem tartalmaznak adatot.<br>

| Oszlop | Leírás |
| --- | --- |
| **Számítógépekre vonatkozó adatok** | |
| Sikeresen befejezve |A frissítéstelepítéssel érintett számítógépek számát jelzi ki az állapot szerint rendezve.  Az állapot nevére kattintva naplókeresést indíthat el, amely megjelenítheti az adott állapotú összes frissítési bejegyzést. |
| Számítógép telepítési állapota |A frissítéstelepítéssel érintett számítógépeket listázza a sikeres telepítések százalékarányával. A bejegyzés nevére kattintva keresést indíthat a naplóban, amellyel megjeleníti az összes hiányzó és kritikus frissítést. |
| **Frissítési példányok adatai** | |
| Példányok telepítésének állapota |A számítógépekről hiányzó frissítéseket kategorizálva listázza aszerint sorba rendezve, hogy az adott kategórián belüli frissítések hány számítógépről hiányoznak. A számítógép nevére kattintva naplókeresést indíthat el, amely megjelenítheti az adott számítógépre vonatkozó összes frissítési bejegyzést. |

<br><br> ![Frissítéstelepítési eredmények áttekintése](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Frissítéstelepítés létrehozása
Új frissítéstelepítés létrehozásához a képernyő felső részében kattintson a **Hozzáadás** gombra az **Új telepítésfrissítés** lap megnyitásához.  Az alábbi táblázatban szereplő tulajdonságok értékeit kell megadnia.

| Tulajdonság | Leírás |
| --- | --- |
| Név |A frissítéstelepítést beazonosító egyedi név. |
| Időzóna |A kezdési időpontnál használandó időzóna. |
| Kezdési idő |A frissítéstelepítés indításának dátuma és időpontja. |
| Időtartam |A frissítéstelepítés engedélyezett futási idejének hossza percekben megadva.  Ha ez alatt az idő alatt nem fejeződik be minden frissítés telepítése, akkor a hátramaradó frissítések csak a következő ütemezett frissítéstelepítés során kerülnek telepítésre. |
| Számítógépek |A frissítéstelepítés által érintett számítógépek vagy számítógépcsoportok nevei.  A legördülő listából válasszon ki egy vagy több elemet. |

<br><br> ![Új frissítéstelepítés lapja](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Időtartomány
A Frissítéskezelési szolgáltatás adatelemzése alapértelmezés szerint a legutóbbi egy nap során felderített csatlakoztatott felügyeleti csoportokra terjed ki. 

Az adatokra vonatkozó időtartomány módosításához válassza ki az irányítópult tetején látható **Az adatok időtartománya** elemet. Itt meghatározhatja, hogy milyen időtartományban létrehozott vagy módosított adatok legyenek használva: legutóbbi 7 nap, 1 nap vagy 6 óra. Az **Egyéni** lehetőség választásával egyéni dátumtartományt is megadhat.<br><br> ![Egyéni időtartomány beállítása](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Frissítéskezelési megoldás két rekordtípust hoz létre az OMS-adattárban.

### <a name="update-records"></a>Update típusú rekordok
Az egyes számítógépekhez szükséges vagy telepített minden egyes frissítéshez egy**Update** (frissítés) típusú rekord készül. Az Update típusú rekordok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
| --- | --- |
| Típus |*Update* |
| SourceSystem |A frissítés telepítését jóváhagyó forrás.<br>Lehetséges értékek:<br>– Microsoft Update (Microsoft-frissítés)<br>– Windows Update (Windows-frissítés)<br>– SCCM<br>– Linux Servers (Linux-kiszolgálók – a csomagkezelőkből lekért információ alapján) |
| Approved |Azt jelzi, hogy a frissítés jóvá lett-e hagyva telepítésre.<br> Linux-kiszolgálók esetén ez nem kötelező, mivel a javításokat nem az OMS kezeli. |
| Kategóriák Windows esetén |A frissítés kategóriája.<br>Lehetséges értékek:<br>– Applications (Alkalmazások)<br>– Critical Updates (Kritikus frissítések)<br>– Definition Updates (Definíciófrissítés)<br>– Feature Packs (Funkciócsomag)<br>– Security Updates (Biztonsági frissítések)<br>– Service Packs (Szervizcsomagok)<br>– Update Rollups (Kumulatív frissítések)<br>– Updates (Frissítések) |
| Kategóriák Linux esetén |A frissítés kategóriája.<br>Lehetséges értékek:<br>– Critical Updates (Kritikus frissítések)<br>– Security Updates (Biztonsági frissítések)<br>– Other Updates (Másféle frissítések) |
| Computer |A számítógép neve. |
| InstallTimeAvailable |Azt jelzi, hogy elérhető-e a telepítés ideje az ugyanezt a frissítést telepítő más ügynököktől. |
| InstallTimePredictionSeconds |Az ugyanezt a frissítést telepítő más ügynököktől származó információ alapján számított várható telepítési idő. |
| KBID |A frissítést ismertető tudásbáziscikk azonosítója. |
| ManagementGroupName |A felügyeleti csoport neve SCOM-ügynökök esetén.  Más ügynökök esetén ez AOI-<workspace ID>. |
| MSRCBulletinID |A frissítést ismertető Microsoft biztonsági közlemény azonosítója. |
| MSRCSeverity |A Microsoft biztonsági közlemény fontossága.<br>Lehetséges értékek:<br>– Critical (Kritikus)<br>– Important (Fontos)<br>– Moderate (Közepes) |
| Optional |Azt jelzi, hogy a frissítés választható-e. |
| Product |A terméke neve, amelyhez a frissítés tartozik.  A **Megtekintés** lehetőségre kattintva a cikket megnyithatja egy böngészőben. |
| PackageSeverity |A frissítés által javított biztonsági rés  súlyossága a Linux-disztribúció szállítója által közölt adatok alapján. |
| PublishDate |A frissítés telepítésének dátuma és időpontja. |
| RebootBehavior |Azt jelzi, hogy a frissítés telepítéséhez szükséges-e újraindítás.<br>Lehetséges értékek:<br>- canrequestreboot (újraindítást igényelhet)<br>- neverreboots (nem igényel újraindítást) |
| RevisionNumber |A frissítés változatszáma. |
| SourceComputerId |A számítógép azonosításához használt globálisan egyedi azonosító (GUID). |
| TimeGenerated |A rekord utolsó frissítésének dátuma és időpontja. |
| Cím |A frissítés címe. |
| UpdateID |A frissítés azonosításához használt globálisan egyedi azonosító (GUID). |
| UpdateState |Azt jelzi, hogy a frissítés telepítve van-e ezen a számítógépen.<br>Lehetséges értékek:<br>– Installed – A frissítés telepítve van ezen a számítógépen.<br>– Needed – A frissítés szükséges, de nincs telepítve ezen a számítógépen. |

<br>
Ha olyan naplókeresést végez, amely **Update** típusú rekordokat ad vissza, a **Frissítések** nézet kiválasztásával megjeleníthet olyan csempéket, amelyek a keresés eredményeként visszaadott frissítések áttekintését tartalmazzák. A **Hiányzó és alkalmazott frissítések** és a **Szükséges és választható frissítések** csempék bejegyzéseire kattintva az adott típusú frissítésekre korlátozhatja a megjelenítést. Az eredeti rekordokhoz a **Lista** vagy a **Táblázat** nézetre kattintva térhet vissza.<br> 

![Naplókeresés, frissítés nézet, Update típusú rekorddal](./media/oms-solution-update-management/update-la-view-updates.png)  

A **Táblázat** nézetben bármely rekord **KBID** tulajdonságára kattintva a vonatkozó tudásbáziscikket megnyithatja egy böngészőben. Ez lehetővé teszi, hogy az egyes frissítésekről egyszerűen jusson további információkhoz.<br> 

![Naplókeresés, táblázat nézet csempékkel, frissítések típusú rekorddal](./media/oms-solution-update-management/update-la-view-table.png)

**Lista** nézetben a tudásbáziscikk megnyitásához a KBID tulajdonság melletti **Megtekintés** hivatkozásra kell kattintani.<br>

![Naplókeresés, lista nézet csempékkel, frissítések típusú rekorddal](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>UpdateSummary típusú rekordok
Minden Windows-ügynök esetében egy **UpdateSummary** típusú rekord készül. Ez a rekord minden olyan alkalommal frissül, amikor a számítógépen frissítéskeresés történik. Az **UpdateSummary** típusú rekordok tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
| --- | --- |
| Típus |UpdateSummary |
| SourceSystem |OpsManager |
| Computer |A számítógép neve. |
| CriticalUpdatesMissing |A számítógépről hiányzó kritikus frissítések száma. |
| ManagementGroupName |A felügyeleti csoport neve SCOM-ügynökök esetén. Más ügynökök esetén ez AOI-<workspace ID>. |
| NETRuntimeVersion |A számítógépre telepített .NET-futtatórendszer verziója. |
| OldestMissingSecurityUpdateBucket |A számítógépen a leghosszabb ideje hiányzó biztonsági frissítés kiadása óta eltelt időt kategorizáló gyűjtő.<br>Lehetséges értékek:<br>– Older (Régebbi)<br>– 180 days ago (180 napja)<br>– 150 days ago (150 napja)<br>– 120 days ago (120 napja)<br>– 90 days ago (90 napja)<br>– 60 days ago (60 napja)<br>– 30 days ago (30 napja)<br>– Recent (Friss) |
| OldestMissingSecurityUpdateInDays |A számítógépen a leghosszabb ideje hiányzó biztonsági frissítés kiadása óta eltelt napok száma. |
| OsVersion |A számítógépre telepített operációs rendszer verziószáma. |
| OtherUpdatesMissing |A számítógépről hiányzó további frissítések száma. |
| SecurityUpdatesMissing |A számítógépről hiányzó biztonsági frissítések száma. |
| SourceComputerId |A számítógép azonosításához használt globálisan egyedi azonosító (GUID). |
| TimeGenerated |A rekord utolsó frissítésének dátuma és időpontja. |
| TotalUpdatesMissing |A számítógépről hiányzó összes frissítés száma. |
| WindowsUpdateAgentVersion |A számítógépen található Windows Update Agent verziószáma. |
| WindowsUpdateSetting |A fontos frissítések telepítésének módját meghatározó beállítás a számítógépen.<br>Lehetséges értékek:<br>– Disabled (Letiltva)<br>– Notify before installation (Értesítés telepítés előtt)<br>– Scheduled installation (Ütemezett telepítése) |
| WSUSServer |A WSUS-kiszolgálóra mutató URL, ha a számítógép konfigurálásában meg van adva ennek használata. |

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött frissítési rekordokkal kapcsolatos naplókeresési mintákat tartalmazza. 

| Lekérdezés | Leírás |
| --- | --- |
| Minden számítógép, amelyről hiányzik frissítés |Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| A "COMPUTER01.contoso.com" számítógépről hiányzó frissítések (írja felül a nevet a saját gép nevére) |Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |
| Minden számítógép, amelyről kritikus vagy biztonsági frissítés hiányzik |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") |
| Hiányzó kritikus vagy biztonsági frissítések olyan számítógépeken, amelyeken kézi frissítés van beállítva |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |
| Olyan gépek hibaeseményei, amelyeknél kritikus vagy biztonsági szükséges frissítések hiányoznak |Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false &#124; Distinct Computer} |
| Minden olyan számítógép, amelynél kumulatív frissítések hiányoznak |Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| Egyedi frissítések minden számítógépnél |Type=Update UpdateState=Needed Optional=false &#124; Egyedi név |
| WSUS-t használó számítógépek |Type=UpdateSummary &#124; measure count() by WSUSServer |
| Automatikus frissítési beállítások |Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |
| Azok a számítógépek, amelyeken az automatikus frissítés le van tiltva |Type=UpdateSummary WindowsUpdateSetting=Manual |
| Minden olyan Linux-számítógép, amelyhez csomagfrissítés érhető el |Type=Update and OSType=Linux and UpdateState!="Not needed" &#124; measure count() by Computer |
| Minden olyan Linux-számítógép, amelyhez kritikus vagy biztonsági rést javító csomagfrissítés érhető el |Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") &#124; measure count() by Computer |
| Minden olyan csomag, amelyhez frissítés érhető el |Type=Update and OSType=Linux and UpdateState!="Not needed" |
| Minden olyan csomag, amelyhez kritikus vagy biztonsági rést javító frissítés érhető el |Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") |
| Minden olyan Ubuntu-gép, amelyhez bármilyen frissítés érhető el |Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |

## <a name="next-steps"></a>Következő lépések
* A részletes frissítési adatokat a [Log Analytics](../log-analytics/log-analytics-log-searches.md) Naplókeresés funkciójával is megtekintheti.
* [Saját irányítópult létrehozásával](../log-analytics/log-analytics-dashboards.md) megjelenítheti a felügyelt számítógépek frissítési megfelelőségét.
* [Létrehozhat riasztásokat](../log-analytics/log-analytics-alerts.md) a számítógépekről hiányzó kritikus frissítések jelzésére vagy arra az estre, ha egy számítógép automatikus frissítése letiltott állapotba kerül.  


