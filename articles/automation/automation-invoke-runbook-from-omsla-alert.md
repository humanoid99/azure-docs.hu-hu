---
title: "Azure Automation-runbook hívása Log Analytics-riasztásból | Microsoft Docs"
description: "Ez a cikk az Automation-runbookok Microsoft OMS Log Analytics-riasztásokból való meghívásának áttekintését tartalmazza."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/31/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 81cf490eae7f283c0180875cb3a2ed2ffe6333c8
ms.lasthandoff: 03/29/2017

---

# <a name="calling-an-azure-automation-runbook-from-an-oms-log-analytics-alert"></a>Azure Automation-runbook hívása OMS Log Analytics-riasztásból

Ha egy riasztás úgy van konfigurálva a Log Analyticsben, hogy riasztásbejegyzést hozzon létre, ha az eredmények megfelelnek egy adott feltételnek (például ha hosszabb ideig megnövekedett processzorhasználat tapasztalható, vagy egy adott üzleti alkalmazás működése szempontjából kritikus valamely alkalmazásfolyamat meghiúsul, és egy megfelelő eseményt ír a Windows eseménynaplójába), a riasztás képes automatikusan lefuttatni egy Automation-runbookot a hiba automatikus javításának megkísérlésére.  

A riasztás konfigurálásakor a runbookok meghívásának két alternatívája közül választhat.  Ezek:

1. Egy webhook használata.
   * Ha az OMS-munkaterület nincs Automation-fiókhoz társítva, csak ez a lehetőség áll rendelkezésre.
   * Azonban akkor is elérhető, ha már csatlakoztatta az OMS-munkaterületet egy Automation-fiókhoz.  

2. A runbook közvetlen kiválasztása.
   * Ez a lehetőség csak akkor áll rendelkezésre, ha az OMS-munkaterület csatlakoztatva van egy Automation-fiókhoz.  

## <a name="calling-a-runbook-using-a-webhook"></a>Runbook meghívása webhook használatával

A webhookok használatával egyetlen HTTP-kérés kiadásával indíthat adott runbookokat az Azure Automationben.  Mielőtt beállíthatná, hogy a [Log Analytics-riasztás](../log-analytics/log-analytics-alerts.md#alert-rules) a runbookot egy webhook használatával hívja meg a riasztási művelet keretében, előbb létre kell hoznia egy webhookot az ezzel a módszerrel meghívni kívánt runbookhoz.  Tekintse át és kövesse a [webhookok létrehozását](automation-webhooks.md#creating-a-webhook) tárgyaló cikk lépéseit, és jegyezze fel a webhook URL-címét, hogy a riasztási szabály konfigurálása során hivatkozhasson rá.   

## <a name="calling-a-runbook-directly"></a>Runbookok közvetlen meghívása

Ha az OMS-munkaterületen telepítve és konfigurálva van az Automatizálás és vezérlés ajánlat, a riasztás Runbook-műveletek beállításának konfigurálásakor a **Runbook kiválasztása** legördülő menüben láthatja az összes runbookot, és kiválaszthatja közülük azt, amelyiket a riasztásra válaszként futtatni kíván.  A kiválasztott runbook futtatható az Azure felhőben lévő munkaterületeken vagy egy hibrid runbook-feldolgozón.  Ha a riasztást a runbook beállítás használatával hozza létre, a rendszer létrehoz egy webhookot a runbookhoz.  A webhookot az Automation-fiókban a kiválasztott runbook webhook paneljére lépve tekintheti meg.  A riasztás törlésével a webhook nem törlődik automatikusan, azonban a felhasználó manuálisan törölheti azt.  Nem probléma, ha a webhook nincs törölve, csupán egy olyan árva elemmé válik, amelyet idővel érdemes törölni, hogy az Automation-fiók rendezett maradjon.  

## <a name="characteristics-of-a-runbook-for-both-options"></a>A runbookok jellemzői (mindkét lehetőség esetében)

A runbookoknak a Log Analytics-riasztásokból való meghívására szolgáló két módszer különbözően működik, amivel érdemes megismerkednie, mielőtt konfigurálná a riasztásokat.  

* Rendelkeznie kell egy **WebhookData** elnevezésű és **Objektum** típusú bemeneti runbookparaméterrel.  Ez lehet kötelező vagy nem kötelező.  A riasztás a keresési eredményeket a runbooknak ezen a bemeneti paraméteren keresztül adja át.

        param  
         (  
          [Parameter (Mandatory=$true)]  
          [object] $WebhookData  
         )

*  A WebhookData PowerShell-objektummá való átalakításához kód szükséges.

    `$SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value`

    A *$SearchResults* egy objektumtömb, amelyben mindegyik objektum egy keresési eredmény értékeit tároló mezőket tartalmaz

### <a name="webhookdata-inconsistencies-between-the-webhook-option-and-runbook-option"></a>A WebhookData eltérései a webhookot használó és a runbookot használó alternatívák közt

* Amikor egy riasztást úgy állít be, hogy egy webhookot hívjon meg, adja meg a runbookhoz létrehozott webhook URL-címét, majd kattintson a **Webhook tesztelése** gombra.  Az így létrejövő és a runbooknak küldött WebhookData nem tartalmazza a *.SearchResult* vagy a *.SearchResults* tömböt.

*  Ha menti ezt a riasztást, amikor az az aktiválásakor meghívja a webhookot, a runbooknak küldött WebhookData tartalmazza a *.SearchResult* tömböt.
* Ha létrehoz egy riasztást, és úgy állítja be, hogy meghívjon egy runbookot (ami szintén létrehoz egy webhookot), a riasztás által az aktiválásakor a runbooknak küldött WebhookData paraméter tartalmazza a *.SearchResults* tömböt.

Így a fenti példa kódban a *.SearchResult* tömböt kell szerepeltetni, ha a riasztás egy webhookot hív meg, és a *.SearchResults* tömböt, ha a riasztás közvetlenül a runbookot hívja meg.

## <a name="example-walkthrough"></a>Forgatókönyv – példa

Most a következő, valamilyen Windows-szolgáltatást indító grafikus runbook segítségével bemutatjuk, hogyan is működik mindez.<br><br> ![Windows-szolgáltatás grafikus runbookjának indítása](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice.png)<br>

A runbook rendelkezik egy **Objektum** típusú, **WebhookData** elnevezésű bemeneti paraméterrel, amely tartalmazza a riasztás által továbbított webhookadatokat a *.SearchResults* tömbbel.<br><br> ![Runbook bemeneti paraméterei](media/automation-invoke-runbook-from-omsla-alert/automation-runbook-restartservice-inputparameter.png)<br>

Esetünkben most létrehoztunk két egyéni mezőt a Log Analyticsben *SvcDisplayName_CF* és *SvcState_CF* néven, amelyek a szolgáltatás megjelenített nevét és állapotát (fut vagy leállítva) nyerik ki a rendszer-eseménynaplóba írt eseményből.  Ezután létrehozunk egy riasztási szabályt a `Type=Event SvcDisplayName_CF="Print Spooler" SvcState_CF="stopped"` keresési lekérdezéssel, hogy észlelni tudjuk, amikor a Nyomtatásisor-kezelő szolgáltatás leáll a Windows-rendszerben.  Ez lehet bármely számunkra fontos szolgáltatás, de ebben a példában egy olyan már meglévő szolgáltatást választottunk, amely része a Windows operációs rendszernek.  A riasztási művelet a konfigurációja szerint végrehajtja a példában használt runbookot a hibrid runbook-feldolgozón futva, amelyek engedélyezve vannak a célrendszereken.   

A runbookkódban lévő **Szolgáltatásnév lekérése a Log Analyticsből** tevékenység a JSON-formátumú karakterláncot objektumtípussá alakítja, és a *SvcDisplayName_CF* elemre szűrve kinyeri a Windows-szolgáltatás megjelenített nevét, majd továbbítja azt a következő tevékenységnek, amely ellenőrzi, hogy a szolgáltatás le van-e állítva, mielőtt megkísérelné újraindítani.  Az *SvcDisplayName_CF* [egyéni mezőt](../log-analytics/log-analytics-custom-fields.md) a példa szemléltetéséhez hoztuk létre a Log Analyticsben.

    $SearchResults = (ConvertFrom-Json $WebhookData.RequestBody).SearchResults.value
    $SearchResults.SvcDisplayName_CF  

Ha a szolgáltatás leáll, a Log Analyticsben lévő riasztási szabály egyezést észlel, majd aktiválja a runbookot, és továbbítja neki a riasztás környezetét. A runbook munkához lát, és ellenőrzi, hogy a szolgáltatás valóban leállt-e, és amennyiben igen, megkísérli újraindítani azt, majd ellenőrzi, hogy megfelelően elindult-e, és elküldi az eredményeket a kimenetre.     

Ha az Automation-fiók nincs az OMS-munkaterülethez társítva, alternatív megoldásként a riasztási szabályt a webhook művelettel úgy kell beállítania, hogy a runbookot aktiválja, továbbá konfigurálnia kell a runbookot, hogy az konvertálja a JSON-formátumú karakterláncot, és a *.SearchResult* tömbre szűrjön a fenti útmutatásoknak megfelelően.    

## <a name="next-steps"></a>Következő lépések

* A Log Analytics-riasztásokkal és létrehozásukkal kapcsolatos további információkért lásd: [Riasztások a Log Analyticsben](../log-analytics/log-analytics-alerts.md).

* A runbookok webhookkal való aktiválásával kapcsolatos további információkért lásd: [Azure Automation-webhookok](automation-webhooks.md).

