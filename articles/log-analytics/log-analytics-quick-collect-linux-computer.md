---
title: "Adatok gyűjtése helyszíni linuxos számítógépekről az Azure Log Analyticsszel | Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti a Linuxhoz készült Log Analytics-ügynököket, és hogyan gyűjthet adatokat Linux rendszerről Log Analytics használatával."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/23/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 839fc3a326dca8b60c6750231b06d2369c3de2fc
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>Adatok gyűjtése saját környezetben futtatott linuxos számítógépekről
Az [Azure Log Analytics](log-analytics-overview.md) közvetlenül tud adatokat gyűjteni az Ön környezetében található fizikai vagy virtuális Linux-gépekről és egyéb erőforrásokról egy adattárba, részletes elemzés és összehasonlítás céljából.  Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a Linux rendszerű számítógépekről történő adatgyűjtést néhány egyszerű lépésben.  Azure-beli linuxos virtuális gépek esetén lásd [az Azure-beli virtuális gépekről történő adatgyűjtést](log-analytics-quick-collect-azurevm.md) ismertető cikket.  

A Linux-ügynök telepítésének hálózati és rendszerkövetelményeivel kapcsolatban lásd a [környezet adatainak az Azure Log Analytics használatával való gyűjtésével foglalkozó szakaszt](log-analytics-concept-hybrid.md#prerequisites).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása
1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Kattintson a **Létrehozás** parancsra, majd válassza ki a következő elemek beállításait:

  * Adja meg az új **OMS-munkaterület** nevét, például: *DefaultLAWorkspace*. 
  * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
  * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
  * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
  * A Log Analytics szolgáltatásban három különböző **tarifacsomag** közül választhat. A jelen rövid útmutató esetében válassza az **ingyenes** szintet.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Miután az **OMS-munkaterület** panelen megadta a szükséges adatokat, kattintson az **OK** gombra.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése
A Linuxhoz készült OMS-ügynök telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára.  Ezekre az adatokra az ügynök burkoló parancsfájljának lesz szüksége az ügynök konfigurálásához és a Log Analyticsszel való sikeres kommunikációjának biztosításához.  

1. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
2. A Log Analytics-munkaterületek listájában válassza ki a korábban létrehozott *DefaultLAWorkspace* elemet.
3. Válassza ki a **Speciális beállítások** elemet.<br><br> ![Log Analytics speciális beállításai](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki a **Csatlakoztatott források**, majd a **Linuxos kiszolgálók** elemet.   
5. A **Munkaterület-azonosító** és az **Elsődleges kulcs** jobb oldalán lévő érték. Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.   

## <a name="install-the-agent-for-linux"></a>A Linuxhoz készült ügynök telepítése
Az alábbi lépésekkel konfigurálhatóak az ügynök beállításai a Log Analytics használatához az Azure és az Azure Government felhőben.  

>[!NOTE]
>A Linuxhoz készült OMS-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen.  

1. A linuxos számítógép a Log Analyticshez való csatlakoztatásának konfigurálásához futtassa az alábbi parancsot, és adja meg a korábban kimásolt munkaterület-azonosítót és elsődleges kulcsot.  A parancs letölti az ügynököt, majd az ellenőrzőösszeg érvényesítése után telepíti azt. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. A linuxos számítógép az Azure Government felhőben futó Log Analyticshez való csatlakoztatásának konfigurálásához futtassa az alábbi parancsot, és adja meg a korábban kimásolt munkaterület-azonosítót és elsődleges kulcsot.  A parancs letölti az ügynököt, majd az ellenőrzőösszeg érvényesítése után telepíti azt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>Az ügynök konfigurálása proxykiszolgálókkal folytatott kommunikációhoz

A következő lépéseket akkor hajtsa végre, ha a linuxos számítógépeinek egy proxykiszolgálón keresztül kell kommunikálniuk a Log Analyticsszel.  A proxykonfiguráció értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`.  A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el.    

1. Szerkessze az `/etc/opt/microsoft/omsagent/proxy.conf` fájlt a következő parancsok futtatásával, és módosítsa az értékeket a saját konkrét beállításaira.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése
A Log Analytics képes az események gyűjtésére a Linux rendszernaplójából és a megadott teljesítményszámlálókból a hosszabb távú elemzések és jelentések készítéséhez, valamint műveleteket hajt végre bizonyos feltételek észlelése esetén.  A következő lépésekkel konfigurálhatja az események gyűjtését a Linux rendszernaplójából, illetve egyes gyakran használt teljesítményszámlálókból is.  

1. Válassza a **Syslog** elemet.  
2. Eseménynaplókat a nevük begépelésével adhat hozzá.  Írja be a **Syslog** kifejezést, majd kattintson a pluszjelre **+**.  
3. A táblában törölje a **Tájékoztatás**, az **Értesítés** és a **Hibakeresés** súlyossági szint jelölését. 
4. A konfiguráció mentéséhez kattintson az oldal tetején található **Mentés** parancsra.
5. Válassza a **Linuxos teljesítményadatok** elemet a teljesítményszámláló-adatok gyűjtésének egy Linux rendszerű gépen történő engedélyezéséhez. 
6. Amikor először konfigurálja egy új Log Analytics-munkaterület Linux-teljesítményszámlálóit, akkor több gyakran használt számlálót is gyorsan létrehozhat. Ezek mindegyike mellett egy jelölőnégyzet található.<br><br> ![Alapértelmezett Linux-teljesítményszámlálók kiválasztva](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br><br> Kattintson **A kijelölt teljesítményszámlálók felvétele** elemre.  A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.  
7. A konfiguráció mentéséhez kattintson az oldal tetején található **Mentés** parancsra.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése
Most, hogy engedélyezte az adatgyűjtést, futtasson le egy egyszerű naplóbeli keresést, hogy megtekinthessen néhány, a célszámtógépekről származó adatot.  

1. Az Azure Portalon lépjen a Log Analytics felületre, és válassza ki a korábban létrehozott munkaterületet.
2. Válassza a **Naplóbeli keresés** csempét, és a Naplóbeli keresés panel lekérdezési mezőjében adja meg a `Perf` típust, majd nyomja le az Enter billentyűt, vagy kattintson a lekérdezési mezőtől jobbra található Keresés gombra.<br><br> ![Példa Log Analytics-naplókeresési lekérdezésre](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> A következő képen látható lekérdezés 735 teljesítményrekordot adott vissza.<br><br> ![Log Analytics-naplókeresés eredménye](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja az ügynököt a linuxos számítógépről, és törölheti a Log Analytics-munkaterületet.  

Az ügynök eltávolításához hajtsa végre az alábbi lépéseket.

1. Töltse le a linuxos ügynök [univerzális parancsfájlját](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) a számítógépre.
2. Futtassa a csomag .sh fájlját a *--purge* argumentummal a számítógépen, ami teljesen eltávolítja az ügynököt és annak konfigurációját.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

A munkaterület törléséhez válassza ki a korábban létrehozott Log Analytics-munkaterületet, és az erőforrás oldalán kattintson a **Törlés** parancsra.<br><br> ![Log Analytics-erőforrás törlése](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>További lépések
Most, hogy már gyűjti a működési és teljesítményadatokat a linuxos számítógépéről, könnyen nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, és reagálhat a kapott eredményekre.  

Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.   

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](log-analytics-tutorial-viewdata.md)
