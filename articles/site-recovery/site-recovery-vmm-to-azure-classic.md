---
title: "VMM-felhőkben futó Hyper-V virtuális gépek replikálása az Azure-ba | Microsoft Docs"
description: "Ez a cikk azt mutatja be, hogyan replikáljon System Center VMM-felhőkben működő Hyper-V gazdagépeken futó Hyper-V virtuális gépeket az Azure-ba."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 9d526a1f-0d8e-46ec-83eb-7ea762271db5
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ed97f2aeb19566b12342e5194ac8a01293f453bf
ms.lasthandoff: 04/03/2017


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>VMM-felhőkben futó Hyper-V virtuális gépek replikálása az Azure-ba
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klasszikus portál](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Klasszikus](site-recovery-deploy-with-powershell.md)
>
>

Az Azure Site Recovery szolgáltatás a virtuális gépek és a fizikai kiszolgálók replikálásával, feladatátvételével és helyreállításával segít a vállalatoknak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A gépeket az Azure-ba, vagy egy másodlagos helyszíni adatközpontba replikálhatja. A szolgáltatás gyors áttekintéséhez olvassa el az [Azure Site Recovery szolgáltatás mibenlétével foglalkozó](site-recovery-overview.md) cikket.

## <a name="overview"></a>Áttekintés
A cikkből megtudhatja, hogyan helyezze üzembe a Site Recovery szolgáltatást a VMM-magánfelhőkben működő Hyper-V gazdakiszolgálókon futó Hyper-V virtuális gépeknek az Azure-ba történő replikálása érdekében.

A cikk bemutatja a forgatókönyvhöz szükséges előfeltételeket, valamint a Site Recovery-tároló létrehozását, az Azure Site Recovery Providernek a forrás VMM-kiszolgálóra való telepítését, a kiszolgálónak a tárolóban való regisztrációját, az Azure-tárfiókok felvételét, az Azure Recovery Services Agent ügynöknek a Hyper-V gazdakiszolgálókra való telepítését,  a VMM-felhőkre érvényes védelmi beállítások megadását (amelyeket a rendszer az összes védett virtuális gépre alkalmaz), valamint a virtuális gépek védelmének bekapcsolását. Végül megismerheti a feladatátvételi teszt végrehajtásának módját, amellyel ellenőrizheti, hogy minden az elvárások szerint működik-e.

Megjegyzéseit vagy kérdéseit a cikk alján, vagy az [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti fel.

## <a name="architecture"></a>Architektúra
![Architektúra](./media/site-recovery-vmm-to-azure-classic/topology.png)

* A Site Recovery szolgáltatás üzembe helyezése során a VMM-re telepíteni kell az Azure Site Recovery Providert, a VMM-kiszolgálót pedig regisztrálni kell a Site Recovery-tárolóban. A Provider a Site Recoveryvel kommunikálva végzi el a replikálás előkészítését.
* A Site Recovery szolgáltatás üzembe helyezése során az Azure Recovery Services Agent ügynököt telepíteni kell a Hyper-V gazdakiszolgálókra. Ez az ügynök kezeli az Azure-tárterületre irányuló adatreplikációt.

## <a name="azure-prerequisites"></a>Azure-előfeltételek
A következőkre lesz szüksége az Azure-ban.

| **Előfeltétel** | **Részletek** |
| --- | --- |
| **Azure-fiók** |Szüksége lesz egy [Microsoft Azure](https://azure.microsoft.com/)-fiókra. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. [További információk](https://azure.microsoft.com/pricing/details/site-recovery/) a Site Recovery díjszabásáról. |
| **Azure Storage** |A replikált adatok tárolásához Azure-tárfiókra lesz szüksége. A replikált adatokat az Azure Storage tárolja, ha pedig feladatátvétel következik be, a rendszer Azure virtuális gépeket hoz létre. <br/><br/>Ehhez [standard georedundáns tárfiókot kell használnia](../storage/storage-redundancy.md#geo-redundant-storage). A fióknak és a Site Recovery szolgáltatásnak ugyanabban a régióban kell elhelyezkednie, és ugyanahhoz az előfizetéshez kell társulnia. Ne feledje, hogy a Prémium szintű tárfiókokba történő replikációt jelenleg nem támogatja a rendszer, ezért ne használjon ilyen típusú fiókokat.<br/><br/>[Itt részletesebben olvashat](../storage/storage-introduction.md) az Azure Storage-ről. |
| **Azure-hálózat** |Szüksége lesz egy Azure virtuális hálózatra, amelyhez az Azure virtuális gépek tudnak csatlakozni, ha a rendszer feladatátvételt hajt végre. Az Azure virtuális hálózatnak és a Site Recovery-tárolónak ugyanabban a régióban kell elhelyezkednie. |

## <a name="on-premises-prerequisites"></a>Helyszíni előfeltételek
Az alábbiakra lesz szüksége a helyszínen.

| **Előfeltétel** | **Részletek** |
| --- | --- |
| **VMM** |Szüksége lesz legalább egy önálló fizikai vagy virtuális kiszolgálóként vagy virtuális fürtként üzemelő VMM-kiszolgálóra. <br/><br/>A VMM-kiszolgálón a legújabb összesített frissítésekkel ellátott System Center 2012 R2-nek kell futnia.<br/><br/>A VMM-kiszolgálóban legalább egy felhőnek kell üzemelnie.<br/><br/>A védelemmel ellátni kívánt forrásfelhőnek legalább egy VMM-gazdagépcsoportot kell tartalmaznia.<br/><br/>A VMM-felhőkről a Keith Mayer blogjában olvasható [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) (Bemutató: magánfelhők létrehozása a System Center 2012 SP1 VMM-mel) című cikkből tudhat meg továbbiakat. |
| **Hyper-V** |A VMM-felhőben legalább egy Hyper-V gazdakiszolgálónak vagy fürtnek kell üzemelnie. A gazdakiszolgálón legalább egy virtuális gépnek kell futnia. <br/><br/>A Hyper-V-kiszolgálókon legalább a **Windows Server 2012 R2** verziónak kell futnia Hyper-V szerepkörrel vagy a **Microsoft Hyper-V Server 2012 R2** verziónak, és telepíteni kell rájuk a legújabb frissítéseket.<br/><br/>A védelemmel ellátni kívánt virtuális gépeket tartalmazó Hyper-V-kiszolgálóknak a VMM-felhőben kell futniuk.<br/><br/>Ha fürtben futtatja a Hyper-V-t, ne feledje, hogy ha statikus IP-címen alapuló fürtöt használ, a rendszer nem hozza létre automatikusan a fürtszervezőt. Ebben az esetben manuálisan kell konfigurálnia a fürtszervezőt. [További információkat](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) Aidan Finn blogbejegyzésében talál. |
| **Védett gépek** | A védelemmel ellátni kívánt gépeknek meg kell felelniük az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). |

## <a name="network-mapping-prerequisites"></a>Hálózatleképezési előfeltételek
Amikor védelemmel látja el az Azure virtuális gépeket, a hálózatleképezés kapcsolatot hoz létre a forrás VMM-kiszolgálókon futó virtuálisgép-hálózatok és a cél Azure-hálózatok között, így biztosítja a következőket:

* Az azonos hálózaton feladatátvételt végrehajtó gépek képesek csatlakozni egymáshoz, attól függetlenül, hogy melyik helyreállítási tervhez tartoznak.
* Ha a cél Azure-hálózatban hálózati átjáró működik, a virtuális gépek képesek csatlakozni a többi helyszíni virtuális géphez.
* Ha nem állítja be a hálózatleképezést, kizárólag az azonos helyreállítási tervben feladatátvételt végrehajtó virtuális gépek fognak tudni csatlakozni egymáshoz az Azure-ba történő feladatátvételt követően.

Hálózatleképezés üzembe helyezéséhez a következőkre lesz szüksége:

* A forrás VMM-kiszolgálón futó, védelemmel ellátni kívánt virtuális gépeknek csatlakozniuk kell egy virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
* Egy Azure-hálózatra, amelyhez a replikált virtuális gépek a feladatátvételt követően csatlakozni tudnak. Ezt a hálózatot a feladatátvétel idején kell kiválasztani. A hálózatnak és az Azure Site Recovery-előfizetésnek ugyanahhoz a régióhoz kell tartoznia.


Hálózatok előkészítése a VMM-ben:

   * [Állítson be logikai hálózatokat](https://technet.microsoft.com/library/jj721568.aspx).
   * [Állítson be virtuálisgép-hálózatokat](https://technet.microsoft.com/library/jj721575.aspx).


## <a name="step-1-create-a-site-recovery-vault"></a>1. lépés: Site Recovery-tároló létrehozása
1. Jelentkezzen be a regisztrálni kívánt VMM-kiszolgálón a [felügyeleti portálra](https://portal.azure.com).
2. Kattintson a **Data Services** > **Recovery Services** > **Site Recovery-tároló** lehetőségre.
3. Kattintson az **Új létrehozása** > **Gyorslétrehozás** elemre.
4. A **Név** mezőben adjon meg egy, a tárolót azonosító rövid nevet.
5. A **Régió** mezőben válassza ki a tároló földrajzi régióját. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Kattintson a **Tároló létrehozása** elemre.

    ![Új tároló](./media/site-recovery-vmm-to-azure-classic/create-vault.png)

Az állapotsorban ellenőrizheti, hogy a tároló sikeresen létrejött-e. Ezt követően a tároló **Aktív** állapottal jelenik meg a Recovery Services főoldalán.

## <a name="step-2-generate-a-vault-registration-key"></a>2. lépés: A tároló regisztrációs kulcsának létrehozása
Hozza létre a tároló regisztrációs kulcsát. Az Azure Site Recovery Provider letöltését, valamint VMM-kiszolgálóra történő telepítését követően ezzel a kulccsal regisztrálhatja a VMM-kiszolgálót a tárolóban.

1. A **Recovery Services** lapon kattintson a kívánt tárolóra az Első lépések lap megnyitásához. Az Első lépések bármikor megnyitható az ikon segítségével is.

    ![Első lépések ikon](./media/site-recovery-vmm-to-azure-classic/qs-icon.png)
2. A legördülő listából válassza a **Between an on-premises VMM site and Microsoft Azure** (Helyszíni VMM-hely és a Microsoft Azure között) lehetőséget.
3. A **Prepare VMM Servers** (VMM-kiszolgálók előkészítése) résznél kattintson a **Generate registration key file** (Regisztrációskulcs-fájl generálása) lehetőségre. A rendszer létrehozza a kulcsfájlt, amely ezt követően 5 napig érvényes. Ha az Azure portált nem a VMM-kiszolgálóról éri el, másolja ezt a fájlt a kiszolgálóra.

    ![Regisztrációs kulcs](./media/site-recovery-vmm-to-azure-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>3. lépés: Az Azure Site Recovery Provider telepítése
1. A **Quick Start (Első lépések)** > **Prepare VMM servers (VMM-kiszolgálók előkészítése)** menüpontban kattintson a **Download Microsoft Azure Site Recovery Provider for installation on VMM servers** (A Microsoft Azure Site Recovery Provider letöltése VMM-kiszolgálókon való telepítés céljából) lehetőségre a Provider telepítőfájlja legújabb verziójának letöltéséhez.
2. Futtassa ezt a fájlt a forrás VMM-kiszolgálón.

   > [!NOTE]
   > Ha a VMM fürtben üzemel, és Ön először telepíti a Providert, telepítse azt aktív csomópontra, majd fejezze be a telepítést, és regisztrálja a VMM-kiszolgálót a tárolóban. Ezt követően telepítse a Providert a többi csomópontra. A Provider frissítésekor az összes csomóponton frissítse a programot, mivel ezeknek ugyanazt a Provider-verziót kell futtatniuk.
   >
   >
3. A telepítő ellenőrzi az előfeltételeket, majd engedélyt kér a VMM szolgáltatás leállítására és a Provider telepítésének elindítására. A rendszer a telepítés befejezését követően automatikusan újraindítja a VMM szolgáltatást. Ha VMM-fürtben végzi a telepítést, a telepítő megkéri a fürtszerepkör leállítására.
4. A **Microsoft Update** lapon kérheti a frissítések telepítését. Ha bekapcsolja ezt a funkciót, a rendszer a Microsoft Update-szabályzatnak megfelelően telepíteni fogja a Provider frissítéseit.

    ![Microsoft Update](./media/site-recovery-vmm-to-azure-classic/updates.png)
5. A Provider alapértelmezett telepítési helye a következő: **<SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin**. Kattintson az **Install** (Telepítés) gombra.

   ![Telepítési hely](./media/site-recovery-vmm-to-azure-classic/install-location.png)
6. A Provider telepítését követően a kiszolgálónak a tárolóban való regisztrálásához kattintson a **Register** (Regisztrálás) elemre.

    ![Telepítés kész](./media/site-recovery-vmm-to-azure-classic/install-complete.png)
7. A **Tároló neve** résznél ellenőrizze a tároló nevét, amelyben a kiszolgálót regisztrálni fogja. Kattintson a *Tovább* gombra.

    ![Kiszolgáló regisztrációja](./media/site-recovery-vmm-to-azure-classic/vaultcred.PNG)
8. Az **Internet Connection** (Internetkapcsolat) lapon adja meg, hogy a VMM-kiszolgálón futó Provider hogyan csatlakozzon az internethez. Válassza a **Connect with existing proxy settings** (Csatlakozás a meglévő proxybeállításokkal) lehetőséget, ha a kiszolgálón konfigurált alapértelmezett internetkapcsolat-beállításokat szeretné használni.

    ![Internetbeállítások](./media/site-recovery-vmm-to-azure-classic/proxydetails.PNG)

   * Ha egyéni proxyt szeretne használni, állítsa be még a Provider telepítése előtt. Miután megadta az egyéni proxy beállításait, a rendszer egy teszt lefuttatásával ellenőrzi a proxykapcsolatot.
   * Ha egyéni proxyt használ, vagy az alapértelmezett proxy hitelesítést igényel, meg kell adnia a proxy adatait, többek között a proxy címét és portját.
   * A következő URL-címeknek elérhetőnek kell lenniük a VMM-kiszolgálóról és a Hyper-V gazdagépekről:
     * *.hypervrecoverymanager.windowsazure.com
     * *.accesscontrol.windows.net
     * *.backup.windowsazure.com
     * *.blob.core.windows.net
     * *.store.core.windows.net
   * Engedélyezze az [Azure Datacenter IP Ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653) (Azure Datacenter IP-tartományai) részben említett IP-címeket, valamint a HTTPS (443) protokollt. Egy engedélyezési listára kell tennie a használni kívánt Azure-régió és az USA nyugati régiójának IP-tartományait.
   * Ha egyéni proxyt használ, a rendszer automatikusan létrehoz egy, a megadott hitelesítő adatokat alkalmazó VMM RunAs-fiókot (DRAProxyAccount). Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A VMM RunAs-fiók beállításait a VMM-konzolban módosíthatja. Ehhez a **Beállítások** munkaterületen bontsa ki a **Biztonság** lehetőséget, kattintson a **Futtató fiókok** elemre, majd módosítsa a DRAProxyAccount jelszavát. A beállítás akkor lép érvénybe, ha újraindítja a VMM szolgáltatást.
9. A **Regisztrációs kulcs** résznél válassza ki az Azure Site Recoveryből letöltött, majd a VMM-kiszolgálóra másolt kulcsot.
10. A titkosítási beállítást csak akkor használja a rendszer, amikor a VMM-felhőkben található Hyper-V virtuális gépeket az Azure-ba replikálja. Másodlagos helyre történő replikáláskor a beállítást nem használja a rendszer.
11. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
12. A **Synchronize cloud metadata** (Felhőmetaadatok szinkronizálása) mezőben válassza ki, hogy szeretné-e a VMM-kiszolgáló összes felhőjének metaadatait szinkronizálni a tárolóval. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem szeretné az összes felhőt szinkronizálni, ne jelölje be a jelölőnégyzetet, és szinkronizálja egyenként a felhőket a VMM-konzolban, a felhők tulajdonságainál.
13. A folyamat befejezéséhez kattintson a **Next** (Tovább) gombra. A regisztrációt követően az Azure Site Recovery lekéri a metaadatokat VMM-kiszolgálóról. A kiszolgáló ezt követően megjelenik a tároló **Kiszolgálók** lapjának **VMM-kiszolgálók** lapján.

    ![Utolsó oldal](./media/site-recovery-vmm-to-azure-classic/provider13.PNG)

A regisztrációt követően az Azure Site Recovery lekéri a metaadatokat VMM-kiszolgálóról. A kiszolgáló ezt követően megjelenik a tároló **Kiszolgálók** lapjának **VMM-kiszolgálók** lapján.

### <a name="command-line-installation"></a>Parancssori telepítés
Az Azure Site Recovery Provider a következő parancssori műveletekkel is telepíthető. A Provider ezzel a módszerrel a Server Core for Windows Server 2012 R2 kiadáson is telepíthető.

1. Töltse le egy mappába a Provider telepítőfájlját és a regisztrációs kulcsot. A mappa lehet például a következő: C:\ASR.
2. Állítsa le a System Center Virtual Machine Manager szolgáltatást.
3. Rendszergazda jogú parancssor segítségével bontsa ki a Provider telepítőjét a következő parancsokkal:

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
4. Telepítse a Providert a következőkkel:

        C:\ASR> setupdr.exe /i
5. Regisztrálja a Providert a következőkkel:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

A paraméterek a következőt jelentik:

* **/Credentials**: kötelező paraméter, amely a regisztrációs kulcsfájl helyét határozza meg.  
* **/FriendlyName**: kötelező paraméter, amely a Hyper-V gazdakiszolgáló nevét tartalmazza úgy, ahogy az az Azure Site Recovery portálon megjelenik.
* **/EncryptionEnabled**: nem kötelező paraméter, amellyel megadhatja, hogy szeretne-e titkosítást használni az Azure virtuális gépeken (inaktív adatok titkosítása). A fájlnevet **.pfx** kiterjesztéssel kell ellátni.
* **/proxyAddress**: nem kötelező paraméter, amely a proxykiszolgáló címét határozza meg.
* **/proxyport**: nem kötelező paraméter, amely a proxykiszolgáló portját határozza meg.
* **/proxyUsername**: nem kötelező paraméter, amely a proxy felhasználónevét határozza meg.
* **/proxyPassword**: nem kötelező paraméter, amely a proxy jelszavát határozza meg.  

## <a name="step-4-create-an-azure-storage-account"></a>4. lépés: Azure-tárfiók létrehozása
1. Ha még nincs Azure-tárfiókja, új fiók létrehozásához kattintson az **Add an Azure Storage Account** (Azure-tárfiók hozzáadása) elemre.
2. A fióknál engedélyezze a georeplikációt. A fióknak és az Azure Site Recovery szolgáltatásnak ugyanabban a régióban kell elhelyezkednie, és ugyanahhoz az előfizetéshez kell társulnia.

    ![Tárfiók](./media/site-recovery-vmm-to-azure-classic/storage.png)

> [!NOTE]
> A Site Recovery üzembe helyezéséhez használt tárfiókok esetében a [Migration of storage accounts](../azure-resource-manager/resource-group-move-resources.md) (Tárfiókok áttelepítése) művelet az egy előfizetésen belüli erőforráscsoportok között vagy előfizetések között nem támogatott.
>
>

## <a name="step-5-install-the-azure-recovery-services-agent"></a>5. lépés: Az Azure Recovery Services Agent ügynök telepítése
Telepítse az Azure Recovery Services Agent ügynököt a VMM-felhőben futó összes Hyper-V gazdakiszolgálóra.

1. Az ügynök legfrissebb telepítőfájljának beszerzéséhez kattintson a **Quick Start (Első lépések)** > **Download Azure Site Recovery Services Agent and install on hosts (Az Azure Site Recovery Services Agent letöltése, majd telepítése a gazdagépekre)** lehetőségre.

    ![Recovery Services Agent telepítése](./media/site-recovery-vmm-to-azure-classic/install-agent.png)
2. Futtassa a telepítőfájlt az összes Hyper-V gazdakiszolgálón.
3. Az **Előfeltételek ellenőrzése** lapon kattintson a **Tovább** gombra. A rendszer automatikusan telepíti a hiányzó előfeltételeket.

    ![Recovery Services Agent, előfeltételek](./media/site-recovery-vmm-to-azure-classic/agent-prereqs.png)
4. A **Telepítési beállítások** lapon adja meg, hogy hová szeretné telepíteni az ügynököt, majd válassza ki, hogy a rendszer melyik gyorsítótárhelyre telepítse a biztonsági mentési metaadatokat. Ezt követően kattintson a **Telepítés** gombra.
5. A telepítés befejeződését követően a **Bezárás** gombra kattintva zárja be a varázslót.

    ![A MARS Agent regisztrálása](./media/site-recovery-vmm-to-azure-classic/agent-register.png)

### <a name="command-line-installation"></a>Parancssori telepítés
A Microsoft Azure Recovery Services Agent a parancssorból is telepíthető. Használja a következő parancsot:

    marsagentinstaller.exe /q /nu

## <a name="step-6-configure-cloud-protection-settings"></a>6. lépés: A felhő védelmi beállításainak konfigurálása
A VMM-kiszolgáló regisztrálását követően megadhatja a felhő védelmi beállításait. A Provider telepítésekor engedélyezte a **Synchronize cloud data with the vault** (Felhőbeli adatok szinkronizálása a tárolóval) funkciót, ezért a VMM-kiszolgálón található összes felhő meg fog jelenni a tároló <b>Protected Items</b> (Védett elemek) lapján.

![Közzétett felhő](./media/site-recovery-vmm-to-azure-classic/clouds-list.png)

1. A Quick Start (Első lépések) lapon kattintson a **Set up protection for VMM clouds** (VMM-felhők védelmének beállítása) elemre.
2. A **Protected Items** (Védett elemek) lapon kattintson a konfigurálni kívánt felhőre, majd lépjen a **Configuration** (Konfigurálás) lapra.
3. A **Target** (Cél) beállításnál válassza az **Azure** lehetőséget.
4. A **Storage Account** (Tárfiók) beállításnál válassza ki a replikáláshoz használni kívánt Azure-tárfiókot.
5. Az **Encrypt stored data** (Tárolt adatok titkosítása) beállítást állítsa **Off** (Kikapcsolás) értékre. Ez a beállítás határozza meg, hogy a rendszer titkosítsa-e az adatokat a helyszíni hely és az Azure közötti replikáció során.
6. A **Copy frequency** (Másolási gyakoriság) beállításnál hagyja meg az alapértelmezett értéket. Ez az érték határozza meg, hogy a rendszer milyen gyakran szinkronizálja az adatokat a forrás- és a célhelyek között.
7. A **Retain recovery points for** (Helyreállítási pontok megőrzésének időtartama) beállításnál hagyja meg az alapértelmezett értéket. Az alapértelmezett nulla érték használata esetén a rendszer kizárólag az elsődleges virtuális gép legutóbbi helyreállítási pontját tárolja a replika gazdakiszolgálón.
8. A **Frequency of application-consistent snapshots** (Alkalmazáskonzisztens pillanatképek gyakorisága) beállításnál hagyja meg az alapértelmezett értéket. Ez az érték határozza meg, hogy a rendszer milyen gyakran készítsen pillanatképeket. A pillanatképek a kötet árnyékmásolása szolgáltatás (VSS) segítségével garantálják, hogy az alkalmazások konzisztens állapotban legyenek a pillanatkép készítésének időpontjában.  Ha mégis módosítja az alapértelmezést, ügyeljen rá, hogy a beállított érték kisebb legyen a további beállított helyreállítási pontok számánál.
9. A **Replication start time** (Replikáció kezdési ideje) beállításnál adja meg, hogy mikor induljon el a kezdeti replikáció az Azure-ba. Az időpontra a Hyper-V gazdakiszolgáló időzónája vonatkozik. Javasoljuk, hogy olyan időpontra ütemezze a kezdeti replikációt, amikor kevesen használják a rendszert.

    ![Felhőreplikáció beállításai](./media/site-recovery-vmm-to-azure-classic/cloud-settings.png)

A beállítások mentését követően a rendszer létrehoz egy feladatot, amelyet aztán a **Jobs** (Feladatok) lapon figyelhet. Ezzel a VMM-forrásfelhőben található összes Hyper-V gazdakiszolgáló készen áll a replikációra.

A felhőbeállítások a kezdeti mentést követően a **Configure** (Konfiguráció) lapon módosíthatók. A célhely vagy a cél tárfiók módosításához törölnie kell a felhő konfigurációját, majd újra el kell végeznie a felhő konfigurálását. Vegye figyelembe, hogy a tárfiók módosítása csupán azokra a virtuális gépekre fog vonatkozni, amelyeknél a tárfiók módosítását követően ismét engedélyezi a védelmet. A meglévő virtuális gépeket a rendszer nem helyezi át az új tárfiókba.

## <a name="step-7-configure-network-mapping"></a>7. lépés: Hálózatleképezés konfigurálása
A hálózatleképezés megkezdése előtt ellenőrizze, hogy a forrás VMM-kiszolgálón működő virtuális gépek csatlakoznak-e a virtuálisgép-hálózathoz. Ezenfelül hozzon létre legalább egy Azure virtuális hálózatot. Ne feledje, hogy egyetlen Azure-hálózatra több virtuálisgép-hálózatot is le lehet képezni.

1. A Quick Start (Első lépések) lapon kattintson a **Map networks** (Hálózatok leképezése) elemre.
2. A **Networks** (Hálózatok) lapon, a **Source location** (Forrás helye) résznél válassza ki a forrás VMM-kiszolgálót. A **Target location** (Cél helye) résznél válassza az Azure lehetőséget.
3. A **Source** (Forrás) hálózatoknál a VMM-kiszolgálóhoz tartozó virtuálisgép-hálózatok listája jelenik meg. A **Target** (Cél) hálózatoknál az előfizetéshez tartozó Azure-hálózatok láthatók.
4. Válassza ki a forrás virtuálisgép-hálózatot, majd kattintson a **Map** (Leképezés) gombra.
5. A **Select a Target Network** (Célhálózat választása) lapon válassza ki a cél Azure-hálózatot.
6. A leképezési művelet végrehajtásához kattintson a pipára.

    ![Felhőreplikáció beállításai](./media/site-recovery-vmm-to-azure-classic/map-networks.png)

A beállítások mentését követően elindul egy feladat, amely a leképezési művelet előrehaladását követi nyomon. Ezt a Jobs (Feladatok) lapon ellenőrizheti. A forrás virtuálisgép-hálózatnak megfelelő meglévő replika virtuális gépeket a rendszer csatlakoztatja a cél Azure-hálózatokhoz. A forrás virtuálisgép-hálózathoz kapcsolódó új virtuális gépeket a rendszer a replikálást követően csatlakoztatja a leképezett Azure-hálózathoz. Ha módosít egy meglévő hálózatleképezést, a replika virtuális gépek az új beállításokkal fognak csatlakozni.

Ne feledje, hogy ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.

> [!NOTE]
> A Site Recovery üzembe helyezéséhez használt hálózatok esetében a [Migration of networks](../azure-resource-manager/resource-group-move-resources.md) (Hálózatok áttelepítése) művelet az egy előfizetésen belüli erőforráscsoportok között vagy előfizetések között nem támogatott.
>
>

## <a name="step-8-enable-protection-for-virtual-machines"></a>8. lépés: A virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezheti a felhőben működő virtuális gépek védelmét. Vegye figyelembe a következőket:

* A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
* A védelem engedélyezéséhez be kell állítania a virtuális gépen az operációs rendszer, és az operációsrendszer-lemez tulajdonságait. A tulajdonságokat megadhatja például, amikor virtuálisgép-sablon segítségével virtuális gépeket hoz létre a VMM-ben. A már létrehozott virtuális gépek tulajdonságait a virtuális gép tulajdonságaiban, az **Általános** és a **Hardverkonfiguráció** lapon állíthatja be. Ha a VMM-ben nem állította be ezeket a tulajdonságokat, a konfigurációt az Azure Site Recovery portálon is elvégezheti.

    ![Virtuális gép létrehozása](./media/site-recovery-vmm-to-azure-classic/enable-new.png)

    ![A virtuális gépek tulajdonságainak módosítása](./media/site-recovery-vmm-to-azure-classic/enable-existing.png)

1. A védelem engedélyezéséhez a felhőben, amelyben a virtuális gép található, lépjen a **Virtuális gépek** lapra, majd kattintson a **Védelem engedélyezése** > **Virtuális gépek hozzáadása** lehetőségre.
2. A felhőben működő virtuális gépek listájából válassza ki azt, amelyet védelemmel szeretne ellátni.

    ![A virtuális gép védelmének engedélyezése](./media/site-recovery-vmm-to-azure-classic/select-vm.png)

    A folyamatok, többek között a kezdeti replikáció előrehaladását a **Feladatok** lap **Védelem engedélyezése** műveleténél követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a virtuális gép készen áll a feladatátvételre. A védelem engedélyezését, valamint a virtuális gép replikálását követően megtekintheti őket az Azure-ban.

    ![A virtuális gép védelme feladat](./media/site-recovery-vmm-to-azure-classic/vm-jobs.png)

1. Ellenőrizze a virtuális gép tulajdonságait, és ha szükséges, módosítsa őket.

    ![Virtuális gépek ellenőrzése](./media/site-recovery-vmm-to-azure-classic/vm-properties.png)
2. A virtuális gép **Configure** (Konfiguráció) lapján a következő hálózati tulajdonságok módosíthatók.

* **Hálózati adapterek száma a cél virtuális gépen** – A hálózati adapterek számát a cél virtuális gépek mérete határozza meg. A különböző méretű virtuális gépek által támogatott adapterek számát a [virtuális gépek méreteit leíró cikkből](../virtual-machines/linux/sizes.md) ismerheti meg. Amikor módosítja a virtuális gép méretét, és menti a beállításokat, a **Konfiguráció** lap következő megnyitásakor látni fogja, hogy a hálózati adapterek száma megváltozott. A cél virtuális gépeken működő hálózati adapterek száma a forrás virtuális gépeken minimálisan használandó adapterszám és a kiválasztott virtuális gép mérete által támogatott maximális hálózatiadapter-szám lesz, azaz:

  * Ha a forrásgépen működő hálózati adapterek száma kisebb vagy egyenlő a célgép méretéhez engedélyezett adapterek számával, a célon ugyanannyi adapter fog működni, mint a forráson.
  * Ha a forrás virtuális gépek adaptereinek száma meghaladja a célmérethez engedélyezett maximumot, a rendszer a célmérethez engedélyezett maximális számot fogja használni.
  * Ha például a forrásgépen két hálózati adapter működik, és a célgép mérete négy adapter használatát teszi lehetővé, a célgépen két adapter fog működni. Ha azonban a forrásgépen két adapter működik, de a cél csupán egy adaptert támogat, akkor a célgépen is csak egy adapter fog működni.     
* **A cél virtuális gép hálózata** – A hálózatot, amelyhez a virtuális gép csatlakozik, a forrás virtuális gép hálózatleképezése határozza meg. Ha a forrás virtuális gépen egynél több hálózati adapter üzemel, és a forráshálózatokat különböző célhálózatokhoz képezték le, akkor választania kell a különböző célhálózatok közül.
* **Az egyes hálózati adapterek alhálózata** – Minden hálózati adapternél kiválaszthatja, hogy a feladatátvételt végrehajtó virtuális gép mely alhálózathoz csatlakozzon.
* **Cél IP-címe** – Ha a forrás virtuális gép hálózati adapterét statikus IP-cím használatára állították be, akkor megadhatja a cél virtuális gép IP-címét. Ezzel a funkcióval a feladatátvételt követően is megtarthatja a forrásgép IP-címét. Ha nem ad meg IP-címet, a rendszer a feladatátvétel időpontjában elérhető IP-címek közül választ egyet a hálózati adapter számára. Ha olyan cél-IP-címet ad meg, amelyet már egy másik, az Azure-ban futó virtuális gép használ, a feladatátvétel sikertelen lesz.  

    ![Hálózati tulajdonságok módosítása](./media/site-recovery-vmm-to-azure-classic/multi-nic.png)

> [!NOTE]
> A statikus IP-címet használó linuxos virtuális gépeket a rendszer nem támogatja.
>
>

## <a name="test-the-deployment"></a>Az üzemelő példány tesztelése
Az üzemelő példány teszteléséhez lefuttathatja egy különálló virtuális gép feladatátvételi tesztjét, vagy több virtuális gépet tartalmazó helyreállítási tervet is létrehozhat, és elvégezheti az egész terv feladatátvételi tesztjét.  

A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat. Vegye figyelembe:

* Ha a feladatátvételt követően távoli asztali kapcsolat segítségével szeretne csatlakozni a virtuális géphez, még a feladatátvételi teszt futtatása előtt engedélyezze a távoli asztali kapcsolat funkciót a virtuális gépen.
* A feladatátvételt követően nyilvános IP-cím és a távoli asztal funkció segítségével tud csatlakozni az Azure-ban futó virtuális géphez. Ha ezt szeretné tenni, ellenőrizze, hogy nincsenek-e érvényben tartományi szabályzatok, amelyek meggátolják, hogy nyilvános cím segítségével csatlakozzon a virtuális gépekhez.

> [!NOTE]
> Annak érdekében, hogy az Azure-ba irányuló feladatátvétel során a lehető legjobb eredményt érhesse el, győződjön meg arról, hogy a virtuális gépre telepítette az Azure Agent ügynököt. Ez felgyorsítja a rendszerindítást, és segít a hibák elhárításában. Töltse le a [Linux-ügynököt](https://github.com/Azure/WALinuxAgent) vagy a [Windows-ügynököt](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

### <a name="create-a-recovery-plan"></a>Helyreállítási terv létrehozása
1. A **Helyreállítási terv** lapon vegyen fel egy új tervet. Adja meg a kívánt nevet, a **Forrás típusa** beállításnál válassza a **VMM** lehetőséget, a **Forrás** beállításnál pedig a forrás VMM-kiszolgálót. A cél az Azure lesz.

    ![Helyreállítási terv létrehozása](./media/site-recovery-vmm-to-azure-classic/recovery-plan1.png)
2. A **Virtuális gépek kijelölése** lapon válassza ki a helyreállítási tervbe felvenni kívánt virtuális gépeket. Ezek a virtuális gépek a helyreállítási terv alapértelmezett csoportjába, az 1. csoportba kerülnek. A tesztek során egy helyreállítási tervben legfeljebb 100 virtuális gépet használtunk.

* Ha a tervbe való felvétel előtt szeretné ellenőrizni a virtuális gépek tulajdonságait, kattintson a virtuális gépet tartalmazó felhő tulajdonságok lapján a kívánt virtuális gépre. Ezenfelül a VMM-konzolban is beállíthatja a virtuális gépek tulajdonságait.
* A megjelenített virtuális gépek mindegyikén engedélyezte a védelmet. A listán egyaránt szerepelnek azok a virtuális gépek, amelyeknek már engedélyezte a védelmét, és végrehajtotta a kezdeti replikációját, illetve azok, amelyeknek ugyan már engedélyezte a védelmét, de a kezdeti replikáció még folyamatban van. Helyreállítási terv részeként csak azoknak a virtuális gépeknek a feladatátvételét lehet végrehajtani, amelyeknek már végbement a kezdeti replikációja.

    ![Helyreállítási terv létrehozása](./media/site-recovery-vmm-to-azure-classic/select-rp.png)

A helyreállítási tervek a létrehozásukat követően megjelennek a **Helyreállítási tervek** lapon. Ezenfelül [Azure Automation-forgatókönyveket](site-recovery-runbook-automation.md) is felvehet a helyreállítási tervbe, amelyek segítségével automatizálhatja a feladatátvétel során végzendő műveleteket.

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
Az Azure-ba irányuló feladatátvételi tesztet két különböző módon futtathatja.

* **Feladatátvételi teszt Azure-hálózat nélkül** – Ez a típus azt ellenőrzi, hogy a virtuális hálózat megfelelően feláll-e az Azure-ban. A virtuális gép a feladatátvételt követően nem csatlakozik Azure-hálózathoz.
* **Feladatátvételi teszt Azure-hálózattal** – Ez a típus azt ellenőrzi, hogy az egész replikációs környezet az elvárásoknak megfelelően feláll-e, illetve hogy a feladatátvételi műveletben érintett virtuális gépek csatlakoznak-e a cél Azure-hálózathoz. A feladatátvételi teszt során a tesztelésre használt virtuális gép alhálózatát a replika virtuális gép alhálózata szerint állítja be a rendszer. Ez eltér a szokásos replikációtól, amelynek keretében a replika virtuális gép alhálózata a forrás virtuális gép alhálózatán alapul.

Ha Azure-hálózat megadása nélkül szeretne az Azure-ba irányuló feladatátvételi tesztet futtatni egy védelemmel ellátott virtuális gépen, nem kell előkészületeket végeznie. Ha cél Azure-hálózat megadása mellett szeretné futtatni a feladatátvételi tesztet, hozzon létre egy, az éles Azure-hálózattól elkülönített új Azure-hálózatot (az új Azure-hálózatok létrehozásánál ez az alapértelmezés). További részletekért olvassa el [a feladatátvételi teszt futtatására vonatkozó utasításokat](site-recovery-failover.md).

Ahhoz, hogy a replikált virtuális gép az elvárásoknak megfelelően működjön, be kell állítania az infrastruktúrát is. A tartományvezérlővel és DNS-sel ellátott virtuális gépek Azure-replikációját például elvégezheti az Azure Site Recovery segítségével, illetve létrehozhatja a replikát a teszteléshez használt hálózaton a feladatátvételi teszt funkcióval. További információkért olvassa el az [Active Directoryra vonatkozó feladatátvételi szempontokat részletező cikket](site-recovery-active-directory.md#test-failover-considerations).

A feladatátvételi teszt futtatásához tegye a következőket:

1. A **Helyreállítási tervek** lapon válassza ki a kívánt tervet, majd kattintson a **Feladatátvételi teszt** lehetőségre.
2. A **Confirm Test Failover** (Feladatátvételi teszt megerősítése) lapon válassza a **None** (Nincs) lehetőséget, vagy a kívánt Azure-hálózatot.  Vegye figyelembe, hogy a None (Nincs) lehetőség kiválasztása esetén a feladatátvételi teszt csak azt ellenőrzi, hogy a virtuális gép Azure-replikálása megfelelően ment-e végbe, a replikációs hálózat konfigurációját nem.

    ![Nincs hálózat](./media/site-recovery-vmm-to-azure-classic/test-no-network.png)
3. Ha a felhőben bekapcsolták az adatok titkosítását, a **Titkosítási kulcs** résznél válassza ki azt a tanúsítványt, amelyet a Providernek a VMM-kiszolgálóra való telepítése során, a felhő adattitkosításának bekapcsolásakor kapott.
4. A **Feladatok** lapon nyomon követheti a feladatátvétel előrehaladását. A virtuális gép tesztelési replikájának meg kell jelennie az Azure portálon. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel.
5. Ha a feladatátvételi művelet elér a **Teszt befejezése** fázisba, a befejezéshez kattintson a **Teszt végrehajtása** lehetőségre. A feladatátvételi folyamat előrehaladásának és állapotának megtekintéséhez, illetve a szükséges műveletek elvégzéséhez nyissa meg a **Feladatok** lapot.
6. A feladatátvételt követően a virtuális gép tesztelési replikájának meg kell jelennie az Azure portálon. Ha beállította a virtuális gépek helyszíni hálózatról való elérését, távoli asztali kapcsolatot is kezdeményezhet a virtuális géppel. Tegye a következőket:

   1. Ellenőrizze, hogy a virtuális gépek sikeresen elindulnak-e.
   2. Ha a feladatátvételt követően távoli asztali kapcsolat segítségével szeretne csatlakozni a virtuális géphez, még a feladatátvételi teszt futtatása előtt engedélyezze a távoli asztali kapcsolat funkciót a virtuális gépen. Ezenfelül hozzá kell adnia egy RDP-végpontot a virtuális géphez. Ezt az [Azure Automation-forgatókönyveket](site-recovery-runbook-automation.md) is megteheti.
   3. Ha a feladatátvételt követően nyilvános IP-cím és a távoli asztal funkció segítségével kapcsolódik az Azure virtuális géphez, ellenőrizze, hogy nincsenek-e érvényben olyan tartományi szabályzatok, amelyek megakadályozzák a virtuális gépek nyilvános címekről való elérését.
7. A tesztelés befejezését követően tegye a következőket:

   * Kattintson **A feladatátvételi teszt befejeződött** elemre. Számolja fel a tesztkörnyezetet. lehetőségre a teszteléshez használt virtuális gép automatikus kikapcsolásához és törléséhez.
   * A feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez és mentéséhez kattintson a **Jegyzetek** elemre.


## <a name="next-steps"></a>Következő lépések
Tájékozódjon a [helyreállítási tervek beállításáról](site-recovery-create-recovery-plans.md) és a [feladatátvételről](site-recovery-failover.md).

