---
title: "StorSimple virtuális eszköz – 2. frissítés | Microsoft Docs"
description: "Megtudhatja, hogyan hozható létre, helyezhető üzembe és kezelhető egy StorSimple virtuális eszköz egy Microsoft Azure virtuális hálózaton. (A StorSimple 2. frissítésére vonatkozik)."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 48d9d8ae97eb763932dd6a59a7df01ae92c92eff
ms.lasthandoff: 03/24/2017


---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>A StorSimple virtuális eszköz üzembe helyezése és kezelése az Azure-ban
## <a name="overview"></a>Áttekintés
A StorSimple 8000 sorozatú virtuális eszköz egy további lehetőség a Microsoft Azure StorSimple megoldáshoz. A StorSimple virtuális eszköz egy virtuális gépen fut egy Microsoft Azure virtuális hálózatban, és arra szolgál, hogy az állomásokon tárolt adatokról biztonsági másolatokat készítsen, illetve klónozza őket. Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe és kezelhet egy virtuális eszközt az Azure-ban (a 2. vagy annál korábbi frissítési szoftververziót futtató összes virtuális eszközre érvényes).

#### <a name="virtual-device-model-comparison"></a>Virtuáliseszköz-modellek összehasonlítása
A StorSimple virtuális eszköz két modellben érhető el: a hagyományos 8010-es (előző nevén 1100-as) és a prémium 8020-as változatban (amely a 2. frissítésben jelent meg először). A két modell összehasonlítása az alábbi táblázatban látható.

| Eszközmodell | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximális kapacitás** |30 TB |64 TB |
| **Azure virtuális gép** |Standard_A3 (4 mag, 7 GB memória) |Standard_DS3 (4 mag, 14 GB memória) |
| **Verziók kompatibilitása** |A 2. frissítés előzetes verzióját vagy újabb verziókat futtató verziók |A 2. frissítést vagy újabb verziókat futtató verziók |
| **Régiónkénti elérhetőség** |Minden Azure-régió |A Premium Storage-ot támogató összes Azure-régió<br></br> A prémium szintű Storage-régiók olyan régiók, amelyek a *Lemezes tárolás* sornak felelnek meg az [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/en-us/regions/services) című lapon található listában. |
| **Tárolás típusa** |A helyi lemezeken Azure Standard szintű tárolást használ<br></br> További információ a [Standard szintű tárfiók létrehozásáról](../storage/storage-create-storage-account.md) |A helyi lemezeken Azure Premium szintű tárolást használ<sup>2</sup> <br></br>További információ a [Premium Storage-fiók létrehozásáról](../storage/storage-premium-storage.md) |
| **Útmutató a számítási feladatokhoz** |A fájlok elemszintű lekérése a biztonsági másolatokból |Felhőalapú fejlesztési és tesztelési forgatókönyvek, kis késés, nagyobb teljesítményű számítási feladatok <br></br>Másodlagos vészhelyreállítási eszköz |

<sup>1</sup> *Korábbi nevén az 1100-as*.

<sup>2</sup> *A 8010-es és a 8020-as modellek egyaránt Azure Standard szintű tárolást használnak a felhő szintjén. Csak az eszközön belül, a helyi rétegen van különbség*.

Ez a cikk lépésenként mutatja be a StorSimple virtuális eszköz telepítésének folyamatát az Azure-ban. A cikk elolvasása után:

* Megtudhatja, hogy miben különbözik a virtuális eszköz a fizikai eszköztől.
* Képes lesz létrehozni és konfigurálni a virtuális eszközt.
* Csatlakozhat a virtuális eszközhöz.
* Megismerheti a virtuális eszköz használatának módját.

Ez az oktatóprogram a 2. frissítést és újabb verziókat futtató összes StorSimple virtuális eszközre érvényes.

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Miben különbözik a virtuális eszköz a fizikai eszköztől?
A StorSimple virtuális eszköz egy csak szoftveres StorSimple-verzió, amely a Microsoft Azure Virtuális gép egyetlen csomópontján fut. A virtuális gép támogatja azokat a vészhelyreállítási forgatókönyveket, amelyekben a fizikai eszköz nem érhető el, és alkalmas a biztonsági mentésekből történő elemszintű előhívásokhoz, helyszíni vészhelyreállításhoz, valamint felhőalapú fejlesztési és tesztelési forgatókönyvekhez.

#### <a name="differences-from-the-physical-device"></a>Különbségek a fizikai eszközhöz képest
Az alábbi táblázat a StorSimple virtuális eszköz és a StorSimple fizikai eszköz közötti fontosabb különbségeket mutatja be.

|  | Fizikai eszköz | Virtuális eszköz |
| --- | --- | --- |
| **Hely** |Az adatközpontban található. |Az Azure-ban fut. |
| **Hálózati illesztők** |Hat hálózati adapterrel rendelkezik: DATA 0-tól DATA 5 számozásig. |Csak egy hálózati adapterrel rendelkezik: DATA 0. |
| **Regisztráció** |A rendszer a konfigurációs lépés során regisztrálja. |A regisztráció egy külön feladat. |
| **Szolgáltatásadat-titkosítási kulcs** |Újra létrejön a fizikai eszközön, majd az új kulccsal frissíti a virtuális eszközt. |A virtuális eszközről nem tud újra létrejönni. |

## <a name="prerequisites-for-the-virtual-device"></a>A virtuális eszköz előfeltételei
Az alábbi szakaszok ismertetik a StorSimple virtuális eszköz előfeltételeit. A virtuális eszközök üzembe helyezése előtt tekintse meg [a virtuális eszközök használatának biztonsági szempontjait](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Azure-követelmények
A virtuális eszköz kiépítése előtt a következő előkészületekre lesz szükség az Azure-környezetben:

* A virtuális eszközhöz [konfigurálnia kell egy virtuális hálózatot az Azure-on](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Premium szintű Storage használata esetén a Premium szintű Storage-ot támogató Azure-régióban kell létrehoznia egy virtuális hálózatot. A prémium szintű Storage-régiók olyan régiók, amelyek a *Lemezes tárolás* sornak felelnek meg az [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/en-us/regions/services) című lapon található listában.
* Saját DNS-kiszolgálónév megadása helyett az Azure által megadott alapértelmezett DNS-kiszolgáló használata javasolt. Ha megadott DNS-kiszolgálónév nem érvényes, vagy ha a DNS-kiszolgáló nem tudja megfelelően feloldani az IP-címeket, akkor a virtuális eszköz létrehozása sikertelen lesz.
* A végpont és telephely közötti, valamint a telephely és telephely közötti lehetőségek választhatóak, de nem kötelezőek. Igény szerint ezeket a lehetőségeket speciális forgatókönyvekhez is konfigurálhatja.
* Az [Azure virtuális gépeket](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (gazdakiszolgálókat) létrehozhatja abban a virtuális hálózatban, amely a virtuális eszközön elérhető köteteket használja. Ezeknek a kiszolgálóknak az alábbi követelményeknek kell megfelelniük:                             

  * Windows vagy Linux rendszerű virtuális gépnek kell lenniük, amelyen telepítve van az iSCSI-kezdeményező szoftver.
  * Ugyanazon a virtuális hálózaton kell futniuk, mint a virtuális gépnek.
  * Tudniuk kell csatlakozni a virtuális eszköz iSCSI-tárolójához a virtuális eszköz belső IP-címén keresztül.
* Ellenőrizze, hogy ugyanazon a virtuális hálózaton konfigurálta-e az iSCSI és a felhőalapú forgalom támogatását.

#### <a name="storsimple-requirements"></a>A StorSimple követelményei
Hajtsa végre a következő frissítéseket az Azure StorSimple eszközön a virtuális eszköz létrehozása előtt:

* Vegyen fel [hozzáférés-vezérlési rekordokat](storsimple-manage-acrs.md) azokon a gépekhez, amelyek a virtuális eszköz gazdakiszolgálói lesznek.
* A virtuális eszközével megegyező régióban lévő [tárfiókot](storsimple-manage-storage-accounts.md#add-a-storage-account) használjon. Különböző régiókban lévő tárfiókok használata esetén a teljesítmény gyenge lehet. A virtuális eszközzel Standard vagy Prémium szintű Storage-fiókot is használhat. További információ a [Standard Storage-fiók](../storage/storage-create-storage-account.md), illetve a [Premium Storage-fiók ](../storage/storage-premium-storage.md) létrehozásáról
* A virtuális eszköz létrehozásához ne használja ugyanazt a tárfiókot, amelyet az adataihoz is használ. Ugyanazon tárfiók használata esetén a teljesítmény gyenge lehet.

Mielőtt hozzákezd, ellenőrizze az alábbi információk meglétét:

* A klasszikus Azure portálbeli fiókja és a hozzáférési hitelesítő adatai.
* A szolgáltatásadat-titkosítási kulcs másolata a fizikai eszközről.

## <a name="create-and-configure-the-virtual-device"></a>A virtuális eszköz létrehozása és konfigurálása
Az eljárások végrehajtása előtt ellenőrizze, hogy teljesülnek-e [A virtuális eszköz előfeltételei](#prerequisites-for-the-virtual-device).

Miután létrehozott egy virtuális hálózatot, konfigurálta a StorSimple Manager szolgáltatást és regisztrálta a fizikai StorSimple eszközt a szolgáltatásban, az alábbi lépések segítségével létrehozhat és konfigurálhat egy StorSimple virtuális eszközt.

### <a name="step-1-create-a-virtual-device"></a>1. lépés: Virtuális eszköz létrehozása
A StorSimple virtuális eszköz létrehozásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Ha a virtuális eszköz létrehozása ebben a lépésben meghiúsul, lehet, hogy nem rendelkezik internetkapcsolattal. Virtuális eszköz létrehozásakor olvassa el az [internetkapcsolat hibáinak elhárításával](#troubleshoot-internet-connectivity-errors) foglalkozó szakaszt a további információk megtekintéséhez.

### <a name="step-2-configure-and-register-the-virtual-device"></a>2. lépés: A virtuális eszköz konfigurálása és regisztrálása
Mielőtt hozzákezdene ehhez az eljáráshoz, ellenőrizze, hogy rendelkezik-e a szolgáltatásadat-titkosítási kulcs másolatával. A szolgáltatásadat-titkosítási kulcs az első StorSimple eszköz konfigurálásakor jött létre, és ezután biztonságos helyre kellett mentenie. Ha nem rendelkezik a szolgáltatásadat-titkosítási kulcs másolatával, akkor a Microsoft támogatási szolgálat segítségét kell kérnie.

A StorSimple virtuális eszköz konfigurálásához és regisztrálásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>3. lépés: (opcionális) Az eszköz konfigurációs beállításainak módosítása
Az alábbi szakasz azokat az eszközkonfigurációs beállításokat ismerteti, amelyek a StorSimple virtuális eszköz számára szükségesek, ha a CHAP protokollt vagy a StorSimple Snapshot Managert kívánja használni, vagy módosítani szeretné az eszköz rendszergazdai jelszavát.

#### <a name="configure-the-chap-initiator"></a>A CHAP-kezdeményező konfigurálása
Ez a paraméter azokat a hitelesítő adatokat tartalmazza, amelyeket a virtuális eszköz (tároló) a kötetek elérését megkísérlő kezdeményezőktől (kiszolgálóktól) vár. A hitelesítés során a kezdeményezők egy CHAP-felhasználónév és egy CHAP-jelszó segítségével azonosítják magukat az eszköz előtt. A részletes lépéseket lásd: [A CHAP konfigurálása az eszközhöz](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>A CHAP céljának konfigurálása
Ez a paraméter tartalmazza azokat a hitelesítő adatokat, amelyeket az eszköz akkor használ, ha egy CHAP-támogatással rendelkező kezdeményező kölcsönös vagy kétirányú hitelesítést kér. A virtuális eszköze egy fordított CHAP-felhasználónév és egy fordított CHAP-jelszó segítségével azonosítja magát a kezdeményező előtt a hitelesítési folyamat során. Vegye figyelembe, hogy a CHAP-cél beállításai globálisak. Ha alkalmazza ezeket a beállításokat, a virtuális tárolóeszközhöz csatlakoztatott összes kötet CHAP-hitelesítést fog használni. A részletes lépéseket lásd: [A CHAP konfigurálása az eszközhöz](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager jelszavának konfigurálása
A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

> [!NOTE]
> A virtuális eszköze számára a Windows-állomás egy Azure virtuális gép.
>
>

Amikor konfigurál egy eszközt a StorSimple Snapshot Managerben, a rendszer a tárolóeszköz hitelesítése érdekében a StorSimple eszköz IP-címének és jelszavának megadására kéri. A részletes lépésekről lásd: [A StorSimple Snapshot Manager jelszavának konfigurálása](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
Amikor a Windows PowerShell felületét a virtuális eszköz elérésére használja, előfordulhat, hogy a rendszer egy eszközadminisztrátori jelszó megadására kéri. Az adatok biztonsága érdekében ezt a jelszót a virtuális eszköz használata előtt módosítania kell. A részletes lépésekről lásd: [Az eszközadminisztrátori jelszó konfigurálása](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Távoli csatlakozás a virtuális eszközhöz
A virtuális eszköz a Windows PowerShell felületéről való elérése alapértelmezés szerint nincs engedélyezve. Először engedélyeznie kell a távfelügyeletet a virtuális eszközön, majd engedélyeznie kell a virtuális eszköz eléréséhez használni kívánt ügyfélen.

A távoli csatlakozás két lépésből álló folyamatának részleteit az alábbiakban láthatja.

### <a name="step-1-configure-remote-management"></a>1. lépés: A távfelügyelet konfigurálása
A StorSimple virtuális eszköz távfelügyeletének konfigurálásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>2. lépés: A virtuális eszköz távoli elérése
Miután engedélyezte távfelügyeletet a StorSimple eszköz konfigurációs lapján, a Windows PowerShell távoli eljáráshívás segítségével egy ugyanazon a virtuális hálózatban lévő másik virtuális gépről is csatlakozhat a virtuális eszközhöz. Ez lehet például az a virtuális gazdagép, amelyet az iSCSI csatlakoztatásához használt és konfigurált. A legtöbb üzemelő példányban ekkor már megnyitott egy nyilvános végpontot a virtuális gazdagépe elérésére, amelyet a virtuális eszköz eléréséhez használhat.

> [!WARNING]
> **A biztonság növelése érdekében a végpontokhoz való csatlakozáshoz nyomatékosan ajánlott a HTTPS használata, majd a távoli PowerShell-munkamenet befejezése után a végpontok törlése.**
>
>

Ha szeretne távoli eljáráshívást beállítani a virtuális eszközhöz, kövesse a [Connecting remotely to your StorSimple device](storsimple-remote-connect.md) (Távoli csatlakozás a StorSimple eszközhöz) című rész eljárásait.

## <a name="connect-directly-to-the-virtual-device"></a>Közvetlen csatlakozás a virtuális eszközhöz
A virtuális eszközhöz közvetlenül is csatlakozhat. Ha a virtuális eszközhöz közvetlenül szeretne csatlakozni egy, a virtuális hálózaton vagy a Microsoft Azure-környezeten kívüli számítógépről, akkor további végpontokat kell megadnia az alábbi eljárásban meghatározottak szerint.

Az alábbi lépések végrehajtásával hozhat létre nyilvános végpontokat a virtuális eszközön.

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Javasolt ugyanannak a virtuális hálózatnak egy másik virtuális gépéről csatlakozni, mivel így csökkenthető a nyilvános végpontok száma a virtuális hálózaton. Ha ezt a módszert használja, egyszerűen egy távoli asztali munkameneten keresztül csatlakozhat a virtuális géphez, majd a virtuális gépet ugyanúgy konfigurálhatja a használatra, ahogy bármely más Windows-ügyfél esetében tenné egy helyi hálózaton. A nyilvános port számát nem kell hozzáfűznie, mivel a port már ismert lesz.

## <a name="work-with-the-storsimple-virtual-device"></a>A StorSimple virtuális eszköz használata
Most, hogy létrehozta és konfigurálta a StorSimple virtuális eszközt, elkezdhet dolgozni vele. Egy virtuális eszközön ugyanúgy használhatja a kötettárolókat, köteteket és biztonsági mentési házirendeket, mint egy fizikai StorSimple eszközön. Az egyetlen különbség, hogy meg kell győződnie arról, hogy kiválasztotta a virtuális eszközt a listából. A virtuális eszköz különböző felügyeleti feladatainak részletes eljárásait lásd: [A virtuális eszköz felügyelete a StorSimple Manager szolgáltatással](storsimple-manager-service-administration.md).

Az alábbi szakaszok a virtuális eszközök használatának egyes különbségeit ismertetik.

### <a name="maintain-a-storsimple-virtual-device"></a>A StorSimple virtuális eszköz karbantartása
Mivel a virtuális eszköz egy csak szoftveres eszköz, a karbantartása minimális egy fizikai eszköz karbantartásához képest. A következő lehetőségek közül választhat:

* **Szoftverfrissítések** – Megtekintheti a szoftver legutolsó frissítésének dátumát, és az összes frissítési állapotüzenetet. A lap alján található **Scan updates** (Frissítések keresése) gombbal manuális keresést hajthat végre, ha ellenőrizni szeretné, hogy vannak-e új frissítések. Ha vannak elérhető frissítések, kattintson az **Install Updates** (Frissítések telepítése) gombra a telepítésükhöz. Mivel a virtuális eszköz csak egyetlen felülettel rendelkezik, a frissítések alkalmazása kismértékben zavarni fogja a szolgáltatást. A virtuális eszköz (szükség esetén) leáll és újraindul, hogy alkalmazza a kiadott frissítéseket. Az eljárás pontos lépéseit [az eszköz frissítését](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal) ismertető szakaszban találja.
* **Támogatási csomag** – Egy támogatási csomag létrehozásával és feltöltésével segíthet a Microsoft támogatási csoportjának a virtuális eszközével kapcsolatos problémák megoldásában. Az eljárás pontos lépéseit a [támogatási csomag létrehozását és kezelését](storsimple-create-manage-support-package.md) ismertető szakaszban találja.

### <a name="storage-accounts-for-a-virtual-device"></a>Tárfiókok létrehozása egy virtuális eszközhöz
A tárfiókok a StorSimple Manager szolgáltatás, a virtuális eszköz és a fizikai eszköz általi használatra jönnek létre. A tárfiókok létrehozásakor javasolt egy régióazonosító használata a felhasználóbarát névben, amely segít biztosítani a régió egységességét az összes rendszerösszetevőn. A virtuális eszköz esetében fontos, hogy az összes összetevője ugyanabban a régióban legyen, így elkerülhetővé válnak a teljesítménybeli problémák.

Az eljárás pontos lépéseit a [tárfiók felvételét](storsimple-manage-storage-accounts.md#add-a-storage-account) ismertető szakaszban találja.

### <a name="deactivate-a-storsimple-virtual-device"></a>A StorSimple virtuális eszköz inaktiválása
A virtuális eszköz inaktiválása törli a virtuális gépet és a kiépítésekor létrejött erőforrásokat. A virtuális eszközt az inaktiválás után már nem lehet a korábbi állapotára visszaállítani. A virtuális eszköz inaktiválása előtt győződjön meg arról, hogy leállította vagy törölte a tőle függő ügyfeleket és állomásokat.

A virtuális eszköz inaktiválása az alábbi következményekkel jár:

* A virtuális eszköz el lesz távolítva.
* A virtuális rendszerhez létrehozott operációsrendszer-lemez és adatlemezek el lesznek távolítva.
* A kiépítés során létrehozott üzemeltetett szolgáltatás és virtuális hálózat megmarad. Ha ezeket nem használja, akkor manuálisan kell törölnie őket.
* A virtuális rendszerhez létrehozott felhőalapú pillanatfelvételek megmaradnak.

Az eljárás pontos lépéseit [a StorSimple eszköz inaktiválását és törlését](storsimple-deactivate-and-delete-device.md) ismertető részben találja.

Amint a virtuális eszköz inaktiváltként jelenik meg a StorSimple Manager szolgáltatás lapján, törölheti a virtuális eszközt a **Devices** (Eszközök) lap eszközeinek listájából.

### <a name="start-stop-and-restart-a-virtual-device"></a>A virtuális eszköz indítása, leállítása és újraindítása
A StorSimple fizikai eszközzel ellentétben a StorSimple virtuális eszköz nem rendelkezik egy megnyomható be- vagy kikapcsoló gombbal. Azonban lehetnek olyan alkalmak, amikor le kell állítania és újra kell indítania a virtuális eszközt. Egyes frissítések például a virtuális gép újraindítását kérhetik a frissítési folyamat befejezéséhez. A virtuális eszköz indítása, leállítása és újraindítása a Virtual Machines Management Console használatával a legegyszerűbb.

Ha megnézi a Management Console-t, a virtuális eszköz állapota **Running** (Fut) lesz, mivel a létrehozása után alapértelmezés szerint elindul. A virtuális eszközt bármikor elindíthatja, leállíthatja és újraindíthatja.

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Gyári beállítások visszaállítása
Ha úgy dönt, hogy szeretné elölről kezdeni a virtuális eszköz létrehozását, egyszerűen inaktiválja és törölje, majd hozzon létre egy újat. A fizikai eszköz alaphelyzetbe állításához hasonlóan, az új virtuális eszközön sem lesznek telepített frissítések, ezért a használata előtt ne felejtsen el frissítéseket keresni hozzá.

## <a name="fail-over-to-the-virtual-device"></a>Feladatátvétel a virtuális eszközre
A vészhelyreállítás (DR) azon kulcsfontosságú forgatókönyvek egyike, amelyre a StorSimple virtuális eszközt tervezték. Ebben az esetben előfordulhat, hogy nem érhető el a fizikai StorSimple eszköz vagy az egész adatközpont. Szerencsére a virtuális eszköz segítségével egy másik helyen helyreállíthatja a műveleteket. A vészhelyreállítás során a forráseszközről származó kötettárolók tulajdonjogai módosulnak, és átkerülnek a virtuális eszközre. A vészhelyreállítás előfeltétele az eszköz létrehozása és konfigurálása, a kötettároló összes kötetének offline állapotba állítása és egy felhőalapú pillanatfelvétel társítása a kötettárolóhoz.

> [!NOTE]
> * Ha virtuális eszközt használ a vészhelyreállítás másodlagos eszközeként, vegye figyelembe, hogy a 8010-es modell 30 TB Standard szintű tárterülettel, a 8020-as modell pedig 64 TB Prémium szintű tárterülettel rendelkezik. A nagyobb kapacitású 8020-as virtuális gép alkalmasabb lehet egy vészhelyreállítási forgatókönyvhöz.
> * Egy 2. frissítést futtató eszközről egy 1. frissítés előtti szoftvert futtató eszközre nem klónozhat és nem hajthat végre feladatátvételt. Egy 2. frissítést futtató eszközről egy 1. frissítést (1.1-es vagy 1.2-es verziót) futtató eszközre azonban végrehajthat feladatátvételt
>
>

Az eljárás pontos lépéseit a [virtuális eszközre történő feladatátvételt](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device) ismertető szakaszban találja.

## <a name="shut-down-or-delete-the-virtual-device"></a>A virtuális eszköz leállítása vagy törlése
Ha nem szeretné, hogy egy korábban konfigurált és használt StorSimple virtuális eszköz használatáért további költségeket számoljanak fel Önnek, akkor leállíthatja a virtuális eszközt. A virtuális eszköz leállításával nem törlődik sem az operációs rendszer, sem a tároló adatlemezei. Az előfizetésén keletkező költségek valóban nem nőnek tovább, az operációs rendszer és az adatlemezek tárolása azonban továbbra is költségekkel jár majd.

Ha törli vagy leállítja a virtuális eszközt, az **Offline** állapottal fog megjelenni a StorSimple Manager szolgáltatás Eszközök lapján. Ha a virtuális eszköz által létrehozott biztonsági mentéseket is törölni szeretné, akkor az eszköz inaktiválása vagy törlése mellett is dönthet. További információkért lásd [a StorSimple eszköz inaktiválását vagy törlését](storsimple-deactivate-and-delete-device.md) ismertető szakaszt.

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Az internetkapcsolat hibáinak elhárítása
Ha nincs internetkapcsolat a virtuális eszköz létrehozása közben, a létrehozási lépés sikertelen lesz. Az internetkapcsolat miatti hiba elhárításához hajtsa végre a következő lépéseket a klasszikus Azure portálon:

1. Hozzon létre egy Windows Server 2012 virtuális gépet az Azure-ban. A virtuális gépnek ugyanazt a tárfiókot, virtuális hálózatot és alhálózatot kell használnia, mint a virtuális eszköznek. Ha már rendelkezik meglévő Windows Server-gazdagéppel az Azure-ban, amely ugyanazt a tárfiókot, virtuális hálózatot és alhálózatot használja, azt is használhatja az internetkapcsolat hibáinak elhárítására.
2. Jelentkezzen be távolról az előző lépésben létrehozott virtuális gépbe.
3. Nyisson meg egy parancsablakot a virtuális gépen (Win+R, majd írja be a `cmd` kifejezést).
4. Futtassa az alábbi parancsot a parancssorban.

    `nslookup windows.net`
5. Ha az `nslookup` sikertelen, akkor az internetkapcsolat hibája megakadályozza, hogy a virtuális eszköz regisztráljon a StorSimple Manager szolgáltatásban.
6. Végezze el a virtuális hálózat szükséges módosításait, hogy a virtuális eszköz elérhesse az Azure-helyeket, például a „windows.net” helyet.

## <a name="next-steps"></a>Következő lépések
* Tekintse át, hogyan végezhető el [a virtuális eszköz felügyelete a StorSimple Manager szolgáltatással](storsimple-manager-service-administration.md).
* Ismerje meg, hogyan hajtható végre egy [StorSimple-kötet visszaállítása egy biztonságimentés-készletből](storsimple-restore-from-backup-set.md).

