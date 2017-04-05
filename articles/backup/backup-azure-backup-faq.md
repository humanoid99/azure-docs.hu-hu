
---
title: "Azure Backup – gyakori kérdések | Microsoft Docs"
description: "Válaszok a gyakori kérdésekre a következőkkel kapcsolatban: a szolgáltatás működése, az Azure Backup-ügynök, a Recovery Services-tároló, valamint a biztonsági mentési és megőrzési korlátok."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "biztonsági mentés és vészhelyreállítás; biztonsági mentési szolgáltatás"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/10/2017
ms.author: markgal;giridham;arunak;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 87d500d886feb2dbd61d15d5a980ab2c4018c2f2
ms.lasthandoff: 04/03/2017


---
# <a name="questions-about-the-azure-backup-service"></a>Kérdések az Azure Backup szolgáltatással kapcsolatban
A cikk gyakori kérdéseket (és válaszokat) tartalmazó szakaszaiból gyorsan áttekinthető az Azure Backup összetevőinek működése. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Ha kérdést szeretne feltenni az Azure Backup szolgáltatással kapcsolatban, kattintson a jobb oldalon található **Megjegyzések** gombra. A megjegyzések a cikk alján jelennek meg. Megjegyzések írásához Livefyre-fiók szükséges. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

A cikk szakaszainak gyors áttekintéséhez használja **A cikk tartalma** terület hivatkozásait a jobb oldalon.


## <a name="recovery-services-vault"></a>Recovery Services-tároló

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Az egyes Azure-előfizetésekben létrehozható tárolók száma korlátozott? <br/>
Igen. 2016 szeptemberétől kezdve előfizetésenként 25 Recovery Services-szolgáltatás vagy biztonsági mentési tár hozható létre. Előfizetésenként az Azure Backup szolgáltatás minden támogatott régiójához legfeljebb 25 Recovery Services-tároló hozható létre. Ha több tárolóra van szüksége, hozzon létre egy további előfizetést.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Az egyes tárolókhoz regisztrálható kiszolgálók/gépek száma korlátozott? <br/>
Igen, tárolónként 50 gépet regisztrálhat. Az Azure IaaS virtuális gépei esetén a korlát 200 virtuális gép tárolónként. Ha több gépet szeretne regisztrálni, hozzon létre egy másik tárolót.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Hogyan regisztrálhatom a kiszolgálómat egy másik adatközpontra?<br/>
A biztonsági mentési adatokat a rendszer annak a tárolónak az adatközpontjába küldi, amelyikre regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása, újratelepítése, majd a kívánt adatközponthoz tartozó új tárolóra regisztrálása.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Ha a szervezetem egy tárolóval rendelkezik, hogyan tudom az egyik kiszolgáló adatait elszigetelni egy másik kiszolgálóétól az adatok visszaállításakor?<br/>
Minden kiszolgáló, amely ugyanahhoz a tárolóhoz van regisztrálva, képes helyreállítani más, *ugyanazt a hozzáférési kódot használó* kiszolgálók adatainak biztonsági másolatait. Ha olyan kiszolgálókkal rendelkezik, amelyek adatainak biztonsági másolatait szeretné elszigetelni a szervezetében található más kiszolgálóétól, használjon egy erre kijelölt hozzáférési kódot ezekhez a kiszolgálókhoz. Például a humánerőforrás-kiszolgálók használhatnának egy titkosító hozzáférési kódot, a könyvelési kiszolgálók egy másikat és a tároló kiszolgálók egy harmadikat.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Mi a gyorsítótármappa minimális méretkövetelménye? <br/>
A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít. A gyorsítótár mappájának az adatok tárolásához szükséges terület 5%-ának kell lennie.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Át lehet telepíteni a biztonsági mentési adatokat vagy tárolót az előfizetéseim között? <br/>
Nem. A tároló előfizetési szinten jön létre, és a létrehozása után nem lehet másik előfizetéshez hozzárendelni.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>A helyreállítási tárak a Resource Manageren alapulnak. A biztonsági mentési tárak (klasszikus módban) továbbra is támogatottak? <br/>
A [klasszikus portálon](https://manage.windowsazure.com) lévő biztonsági mentési tárak továbbra is támogatottak. A klasszikus portál azonban nem használható új biztonsági mentési tárak üzembe helyezésére. A Microsoft javasolja, hogy Recovery Services-tárolókat használjon az összes üzemelő példányhoz, mivel a jövőbeli fejlesztések csak a Recovery Services-tárolókra lesznek érvényesek. Ha a klasszikus portálon próbál létrehozni egy biztonsági mentési tárolót, a rendszer át fogja irányítani az [Azure Portalra](https://portal.azure.com).

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Áttelepíthetek egy Backup tárolót egy Recovery Services-tárolóra? <br/>
Sajnos nem, a Backup-tároló tartalma nem telepíthető át egy Recovery Services-tárolóra. Jelenleg is dolgozunk ezen funkción, azonban most még nem elérhető.

### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>A Recovery Services-tárolók támogatják a klasszikus vagy a Resource Manager alapú virtuális gépeket? <br/>
A Recovery Services-tárolók mindkét modellt támogatják.  Mind a klasszikus (azaz a klasszikus portálon létrehozott), mind a Resource Manager-alapú (azaz az Azure Portalon létrehozott) virtuális gépekről készíthet biztonsági mentést egy Recovery Services-tárolóba.

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Biztonsági másolatot készítettem a klasszikus virtuális gépemről egy biztonsági mentési tárban. Áttelepíthetem a virtuális gépeimet a klasszikus módból Resource Manager módba, hogy egy Recovery Services-tárolóban védjem őket?
A klasszikus virtuális gépek a biztonsági mentési tárolókban lévő helyreállítási pontjai nem lesznek automatikusan áttelepítve a Recovery Services-tárolóba, amikor a virtuális gépet klasszikus módból Resource Manager módba telepíti. Hajtsa végre ezeket a lépéseket a virtuális gép biztonsági mentéseinek áttelepítéséhez:

1. A Backup-tárolóban lépjen a **Protected Items** (Védett elemek) lapra, és válassza ki a virtuális gépet. Kattintson a [Védelem kikapcsolása](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines) parancsra. Hagyja a *Delete associated backup data* (Társított biztonsági mentési adatok törlése) beállítást **bejelöletlenül**.
2. Telepítse át a virtuális gépet a klasszikus módból a Resource Manager módba. A virtuális gépnek megfelelő tároló és hálózat adatait is mindenképpen telepítse át Resource Manager módba.
3. Hozzon létre egy Recovery Services-tárolót, és konfigurálja a biztonsági mentést az áttelepített virtuális gépen a tároló irányítópultjának tetejénél található **Backup** (Biztonsági mentés) művelettel. A virtuális gépek Recovery Services-tárolóba való biztonsági mentésével kapcsolatos részletes információkért lásd az [Azure virtuális gépek védelme Recovery Services-tárolóval](backup-azure-vms-first-look-arm.md) című cikket.



## <a name="azure-backup-agent"></a>Az Azure Backup ügynöke

### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Honnan tölthetem le a legújabb Azure Backup ügynököt? <br/>
[Innen](http://aka.ms/azurebackup_agent) töltheti le a legújabb ügynököt a Windows Server, System Center DPM vagy Windows-ügyfél biztonsági mentése számára. Ha egy virtuális gépről szeretne biztonsági mentést készíteni, használja a virtuálisgép-ügynököt (amely automatikusan telepíti a megfelelő bővítményt). Az Azure katalógusból létrehozott virtuális gépeken a virtuálisgép-ügynök már jelen van.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Az Azure Backup ügynök telepítése közben a rendszer a tároló hitelesítő adatainak megadását kéri. A tároló hitelesítő adatai lejárnak?
Igen, a tároló hitelesítő adatai 48 óra után lejárnak. Ha a fájl lejár, jelentkezzen be az Azure Portalra, és töltse le a tároló hitelesítő adatfájljait a tárolójából.

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Mi történik, ha átnevezek egy Windows-kiszolgálót, amelyről biztonsági adatmentés történik az Azure rendszerbe?<br/>
Ha átnevez egy kiszolgálót, minden aktuálisan konfigurált biztonsági mentés leáll.
Regisztrálja a kiszolgáló új nevét a Backup-tárolóban. Ha regisztrálta az új nevet a tárolóval, az első biztonsági mentési művelet egy *teljes* biztonsági mentés lesz. Ha helyre kell állítania a régi kiszolgálónévvel a tárolóba mentett adatokat, használja az **Adatok helyreállítása** varázsló [**Másik kiszolgáló**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) beállítását.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>Milyen típusú meghajtókon lévő fájlokról és mappákról tudok biztonsági másolatot készíteni? <br/>
A következő meghajtókról/kötetekről nem készíthető biztonsági másolat:

* Cserélhető adathordozók: a biztonsági mentés minden forráselemének rögzített állapotban kell lennie.
* Csak olvasható kötetek: A kötetnek a működéshez írhatónak kell lennie a kötet árnyékmásolat (VSS) számára.
* Offline kötetek: A kötetnek a működéshez online-nak kell lennie a VSS számára.
* Hálózati megosztás: A kötetnek helyinek kell lennie a kiszolgáló számára, hogy az online biztonsági mentés használatával biztonsági másolatot lehessen róla készíteni.
* BitLocker által védett kötetek: A kötetet fel kell oldani a biztonsági mentés megkezdése előtt.
* Fájlrendszer azonosítása: Csak az NTFS fájlrendszer támogatott.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>A kiszolgálón lévő milyen fájlokról és mappákról készíthetek biztonsági másolatot?<br/>
A következő típusok támogatottak:

* Titkosított
* Tömörített
* Ritka
* Tömörített + ritka
* Rögzített hivatkozások: Nem támogatott, átugorva
* Újraelemzési pont: Nem támogatott, átugorva
* Titkosított + ritka: Nem támogatott, átugorva
* Tömörített stream: Nem támogatott, átugorva
* Ritka stream: Nem támogatott, átugorva

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Mi a fájl elérési útjának maximális hossza, amely megadható az Azure Backup-ügynök használatával a Backup szabályzatában? <br/>
Az Azure Backup ügynök az NTFS-re hagyatkozik. A [fájl elérési útvonalának hossz-specifikációját a Windows API korlátozza](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Ha a védeni kívánt fájlok hosszabb elérési úttal rendelkeznek, mint a Windows API által engedélyezett korlát, a biztonsági mentést a szülőmappáról vagy a lemezmeghajtóról készítse.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Mely karakterek engedélyezettek az Azure Backup ügynököt használó Azure Backup házirend elérési útjában? <br>
 Az Azure Backup ügynök az NTFS-re hagyatkozik. Ez engedélyezi az [NTFS által támogatott karakterek](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) használatát a fájl meghatározásának részeként.  

### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Hogyan változtathatom meg az Azure Backup ügynök számára meghatározott gyorsítótár helyét?<br/>
Használja az alábbi listát a gyorsítótár helyének módosításához.

* Állítsa le a Backup motort úgy, hogy végrehajtja a következő parancsot egy rendszergazdai jogú parancssorban:

```PS C:\> Net stop obengine```
* Ne helyezze át a fájlokat. Ehelyett másolja a gyorsítótár mappáját egy másik, elegendő tárhellyel rendelkező meghajtóra. Miután ellenőrizte, hogy a biztonsági másolatok működnek az új gyorsítótárhelyen, az eredeti gyorsítótárhelyet eltávolíthatja.
* Frissítse a következő beállításjegyzékbeli bejegyzéseket az új gyorsítótárhely mappájának elérési útjával.<br/>

| Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

* Indítsa újra a Backup motort úgy, hogy végrehajtja a következő parancsot egy rendszergazdai jogú parancssorban:

```PS C:\> Net start obengine```

Miután a biztonsági mentés létrehozása sikeresen befejeződött az új gyorsítótárhelyen, eltávolíthatja az eredeti gyorsítótármappát.

### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Hova helyezhetem el az Azure Backup ügynök gyorsítótármappáját, hogy megfelelően működjön?<br/>
A gyorsítótár-mappa számára a következő helyek nem javasoltak:

* Hálózati megosztás vagy cserélhető adathordozó: A gyorsítótármappának helyinek kell lennie az online biztonsági mentés használatával mentést igénylő kiszolgáló számára. A hálózati helyek és a cserélhető adathordozók, például az USB-meghajtók nem támogatottak.
* Offline kötetek: A gyorsítótár-mappának online kell lennie az Azure Backup ügynök használatával készített várható biztonsági mentés számára.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Vannak a gyorsítótármappának olyan attribútumai, amelyek nem támogatottak?<br/>
A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

* Titkosított
* Deduplikált
* Tömörített
* Ritka
* Újraelemzési pont

Sem a gyorsítótármappa, sem a metaadatok virtuális merevlemez-meghajtója nem rendelkezik a szükséges attribútumokkal az Azure Backup-ügynökhöz.


## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Telepíthetem az Azure Backup ügynököt egy olyan Azure virtuális gépre, amelyet az Azure Backup szolgáltatás már támogat a virtuális gép bővítmény használatával? <br/>
Abszolút. Az Azure Backup virtuálisgép-szintű biztonsági mentést nyújt az Azure virtuális gépeknek az erre szolgáló bővítmény használatával. A vendég Windows operációs rendszeren található fájlok és mappák védelme érdekében telepítse az Azure Backup ügynököt a vendég Windows operációs rendszeren.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Telepíthetem az Azure Backup ügynököt egy Azure virtuális gépre, hogy biztonsági mentést készítsek egy, az Azure virtuális gép által biztosított ideiglenes tárolón lévő fájlokról és mappákról? <br/>
Igen. Telepítse az Azure Backup ügynököt a vendég Windows operációs rendszeren, és készítsen biztonsági mentést a fájlokról és mappákról egy ideiglenes tárolóra. A biztonsági mentési feladatok sikertelenek lesznek, ha törli az ideiglenes tároló adatait. Emellett ha az ideiglenes tároló adatait törölték, csak állandó tárolóba tud visszaállítani.


## <a name="azure-backup-server-and-data-protection-manager"></a>Azure Backup Server és Data Protection Manager

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón? <br/>
Igen.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>A System Center – Data Protection Manager melyik verziója támogatott? <br/>
Javasoljuk, hogy telepítse a [legújabb](http://aka.ms/azurebackup_agent) Azure Backup-ügynököt a System Center – Data Protection Manager legújabb kumulatív frissítésén. 2016 augusztusában a legújabb frissítés a 11. kumulatív frissítés.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>Telepítettem az Azure Backup ügynököt a fájljaim és mappáim megvédéséhez. Telepíthetem a System Center DPM-et, hogy az Azure Backup-ügynökkel együttműködve az Azure-ban védje a helyszíni alkalmazásokat vagy a virtuális gépek számítási feladatait? <br/>
Az Azure Backup és a System Center Data Protection Manager (DPM) együttes használatához először a DPM-et telepítse, majd az Azure Backup ügynököt. Az Azure Backup ügynök és a DPM együttműködését úgy biztosíthatja, ha az Azure Backup-összetevőket ebben a sorrendben telepíti. Nem javasolt és nem támogatott az Azure Backup ügynök telepítése a DPM telepítése előtt.


## <a name="how-azure-backup-works"></a>Az Azure Backup működése

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Az Azure Backup ügynök működik egy Windows Server 2012 deduplikációt használó kiszolgálón? <br/>
Igen. Az ügynökszolgáltatás átalakítja a deduplikált adatokat normális adatokká, amikor előkészíti a biztonsági mentési műveletet. Ezután optimalizálja az adatokat a biztonsági mentéshez, titkosítja őket, majd elküldi a titkosított adagokat az online biztonsági mentési szolgáltatásba.

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Ha megszakítok egy már elindult biztonsági mentési feladatot, az átküldött adatok biztonsági másolata törlődik? <br/>
Nem. A biztonsági mentési feladat megszakításának pillanata előtt a tárolóba már átvitt minden adat a tárolóban marad. Az Azure Backup egy ellenőrzőpont-alapú mechanizmust használ, így a biztonsági mentés közben esetenként ellenőrzőpontokat ad az adatok biztonsági másolatához. Mivel ellenőrzőpontok vannak az adatok biztonsági másolatában, a következő biztonsági mentési folyamat ellenőrizni tudja a fájlok épségét. A következő biztonsági mentési feladat a korábban már mentett adatokra épülő növekményes mentés lesz. A növekményes mentések csak az új vagy módosított adatok átvitelét jelentik, így a sávszélesség-használat optimalizálható.

Ha megszakítja egy Azure virtuális gép valamely biztonsági mentését, a rendszer a már átvitt adatokat figyelmen kívül hagyja. A következő biztonsági mentési feladat az utolsó sikeres biztonsági mentéshez képest végzi el az adatok növekményes mentését.

### <a name="if-a-backup-job-fails-can-i-configure-the-backup-service-to-send-e-mail-br"></a>Konfigurálhatom úgy a Backup szolgáltatást, hogy e-mailt küldjön, ha egy biztonsági mentési feladat sikertelen volt? <br/>
Igen, a Backup szolgáltatás számos eseményalapú értesítéssel rendelkezik, amelyek egy PowerShell-parancsprogrammal használhatók. A teljes leírást itt találja: [Értesítések konfigurálása](backup-azure-monitor-vms.md#configure-notifications)

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Korlátozva van, hogy mikorra vagy hányszor ütemezhető egy biztonsági mentési feladat?<br/>
Igen. Windows Server- vagy Windows-munkaállomásokon a biztonsági mentési feladatok naponta legfeljebb háromszor futtathatók. System Center DPM esetén a biztonsági mentési feladatok naponta legfeljebb kétszer futtathatók. Az infrastruktúra-szolgáltatás virtuális gépei esetén a biztonsági mentési feladat naponta legfeljebb egyszer futtatható. A napi vagy heti ütemezéseket a Windows Server- vagy Windows-munkaállomás ütemezési szabályzatával adhatja meg. A System Center DPM-mel napi, heti, havi és évi ütemezéseket határozhat meg.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Miért kisebb a Recovery Services-tárolóba átvitt adatok mérete a biztonsági mentéskor létrehozott adatméretnél?<br/>
 Minden, az Azure Backup ügynökétől, az SCDPM-ből vagy az Azure Backup Serverről származó adatot, amelyről biztonsági mentés készül, a rendszer tömörít és titkosít az átvitel előtt. A tömörítés és a titkosítás alkalmazása után a biztonsági mentési tárolóban lévő adatok 30–40 százalékkal kisebbek.

 ### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Van mód a Backup szolgáltatás által használt sávszélesség nagyságának beállítására?<br/>
  Igen, használhatja a Backup ügynök **Tulajdonságok módosítása** beállítását a sávszélesség módosításához. Beállíthatja a sávszélesség méretét, továbbá az időpontokat, amikor használja ezt a sávszélességet. A részletes útmutatást lásd: **[Hálózatszabályozás engedélyezése](backup-configure-vault.md#enable-network-throttling)**.



## <a name="what-can-i-back-up"></a>Miről tudok biztonsági mentést készíteni?

### <a name="which-operating-systems-do-azure-backup-support-br"></a>Mely operációs rendszereket támogatja az Azure Backup? <br/>
Az Azure Backup a következő operációs rendszerek biztonsági mentését támogatja: az Azure Backup Server és a System Center Data Protection Manager (DPM) használatával védett fájlok és mappák, valamint számítási feladatokat végző alkalmazások.

| Operációs rendszer | Platform | SKU |
|:--- | --- |:--- |
| Windows 8 és a legújabb szervizcsomagok |64 bit |Enterprise, Pro |
| Windows 7 és a legújabb szervizcsomagok |64 bit |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 és a legújabb szervizcsomagok |64 bit |Enterprise, Pro |
| Windows 10 |64 bit |Enterprise, Pro, Home |
| Windows Server 2016 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 és a legújabb szervizcsomagok |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 és a legújabb szervizcsomagok |64 bit |Standard, Workgroup |
| Windows Server 2012 R2 és a legújabb szervizcsomagok |64 bit |Essential |
| Windows Server 2008 R2 SP1 |64 bit |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bit |Standard, Enterprise, Datacenter, Foundation |

**Azure VM Backup esetében:**

* **Linux**: Az Azure Backup az [Azure által támogatott disztribúciókat](../virtual-machines/linux/endorsed-distros.md) támogatja, a Core OS Linux kivételével.  Egyéb saját Linux-disztribúciók is működtethetők, ha a virtuális gép ügynöke elérhető a virtuális gépen, és a rendszer támogatja a Pythont.
* **Windows Server**: A Windows Server 2008 R2-nél régebbi verziók nem támogatottak.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Létezik méretkorlátozás a biztonsági mentésre kijelölt adatforrásokra vonatkozóan? <br/>
Azon adatok mennyisége, amelyekről egy adott tárolóban biztonsági másolat készíthető, nem korlátozott. Az Azure Backup ugyan korlátozza az adatforrások maximális méretét, ez a korlát azonban elég magas. A 2015. augusztusi állapot szerint a támogatott operációs rendszerek esetén az adatforrás maximális mérete a következő:

| Sorszám | Operációs rendszer | Adatforrás maximális mérete |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 vagy újabb |54 400 GB |
| 2 |Windows 8 vagy újabb |54 400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

Az alábbi táblázat megmagyarázza, hogy az egyes adatforrásméretek hogyan lettek meghatározva.

| Adatforrás | Részletek |
|:---:|:--- |
| Kötet |Egy kiszolgáló vagy ügyfélgép egyetlen kötetéről biztonsági mentés alatt álló adatok mennyisége |
| Hyper-V virtuális gép |A biztonsági mentés alatt álló virtuális gép összes virtuális merevlemezének összes adata |
| Microsoft SQL Server-adatbázis |A biztonsági mentés alatt álló egyetlen SQL-adatbázis mérete |
| Microsoft SharePoint |Egy biztonsági mentés alatt álló SharePoint-farm tartalmának és konfigurációs adatbázisainak összessége |
| Microsoft Exchange |Egy biztonsági mentés alatt álló Exchange-kiszolgáló összes Exchange-adatbázisa |
| BMR/Rendszerállapot |A biztonsági mentés alatt álló gép BMR-ének vagy rendszerállapotának minden egyes másolata |



## <a name="retention-policy-and-recovery-points"></a>Adatmegőrzési szabály és helyreállítási pontok

### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Van különbség a DPM és a Windows Server vagy Windows-ügyfél (pl. Windows Server kiszolgálón DPM nélkül) esetén a megtartási házirendben?<br/>
Nem, a DPM és a Windows Server vagy Windows-ügyfél is rendelkezik napi, heti, havi és évi megtartási házirendekkel.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Beállíthatom a megtartási házirendeket szelektíven – pl. konfigurálom hetente és naponta, de nem évente és havonta?<br/>
Igen, az Azure Backup megtartási struktúrája az igényeihez igazodva teljes rugalmasságot tesz lehetővé a megtartási házirend meghatározásában.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>„Ütemezhetek egy biztonsági mentést” este 6 órára, és megadhatok „megtartási házirendeket” egy másik időpontra?<br/>
Nem. Megtartási házirendeket csak biztonsági mentési pontokon lehet alkalmazni. A következő képen a megtartási házirend 0 és 18 órakor készült biztonsági mentésekre van meghatározva. <br/>

![Biztonsági mentés és megőrzés ütemezése](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>Egy növekményes másolat át lesz küldve az ütemezett megtartási házirendekhez? <br/>
Nem, a növekményes másolat a biztonsági mentés ütemezési oldalán megjelölt időpont alapján lesz elküldve. A megtartható pontok a megtartási házirend alapján vannak meghatározva.

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Ha egy biztonsági másolat hosszú ideig van megtartva, több időbe telik egy régebbi adatpont helyreállítása? <br/>
Nem – a legrégebbi és a legújabb pont helyreállítási ideje azonos. Minden helyreállítási pont teljes pontként viselkedik.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Ha minden helyreállítási pont olyan, mint egy teljes pont, ez befolyásolja a teljes számlázandó biztonsági mentési tárolást?<br/>
A tipikus hosszú távú megtartási pontok az adatok biztonsági másolatát teljes pontokként tárolják. A teljes pontok tárolás szempontjából *nem hatékonyak*, azonban könnyebben és gyorsabban visszaállíthatók. A növekményes másolatok tárolás szempontjából *hatékonyak* de egy adatlánc visszaállítását teszik szükségessé, ami befolyásolja a helyreállítási időt. Az Azure Backup tárolási architektúrája ezen két megoldás előnyeit ötvözi az adatok optimális tárolásával a gyors visszaállítás érdekében és alacsony tárolási költségekkel. Ez az adattárolási módszer biztosítja, hogy a bemenő és kimenő sávszélesség is hatékonyan van felhasználva. Mind az adattárolási hely, mind az adatok helyreállításához szükséges idő mértéke a minimális szinten van tartva. Megtudhatja, hogy miért hatékonyak a [növekményes biztonsági mentések](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>A létrehozható helyreállítási pontok száma korlátozott?<br/>
Védett példányonként 9999 helyreállítási pontot hozhat létre. A védett példányok olyan számítógépek, kiszolgálók (fizikai vagy virtuális) vagy számítási feladatok, amelyek úgy vannak konfigurálva, hogy biztonsági mentést végezzenek az Azure-ba. Backup-tárolónként a védett példányok száma korlátlan. További információkért lásd a magyarázatokat a [biztonsági mentést és megőrzést](./backup-introduction-to-azure-backup.md#backup-and-retention), illetve a [védett példányokat](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance) ismertető szakaszban.

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Hány helyreállítást végezhetek el az Azure-ban biztonsági másolattal rendelkező adatokból?<br/>
Az Azure Backupból történő helyreállítások száma korlátlan.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Az adatok visszaállításakor fizetnem kell az Azure-ból kimenő forgalomért? <br/>
Nem. A helyreállítások ingyenesek, és nem kell fizetni a kimenő forgalomért.

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Továbbra is megjelenik „Az Azure Backup nem lett konfigurálva ezen a kiszolgálón” figyelmeztetés annak ellenére, hogy már konfiguráltam biztonsági mentési szabályzatot <br/>
Ez a figyelmeztetés akkor jelenik meg, amikor a helyi kiszolgálón tárolt ütemezett biztonsági mentési beállítások nem egyeznek meg a biztonsági mentési tárolóban tárolt beállításokkal. Ha a kiszolgáló vagy a beállítások egy korábbi hibátlan állapotra lettek visszaállítva, a biztonsági mentés ütemezése elveszítheti a szinkronizálást. Ha ilyen figyelmeztetést kap, [konfigurálja újra a biztonsági mentési házirendet](backup-azure-manage-windows-server.md), majd kattintson a **Biztonsági mentés azonnali futtatása** parancsra a helyi kiszolgálónak az Azure szolgáltatással történő újraszinkronizálásához.



## <a name="azure-backup-encryption"></a>Azure Backup-titkosítás

### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Az Azure-ba küldött adatok titkosítottak? <br/>
Igen. Az adatokat a rendszer az AES256 használatával a helyszíni kiszolgálón/ügyfélen/SCDPM-gépen titkosítja, és egy biztonságos HTTPS-kapcsolaton keresztül küldi el.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Az adatokról készült biztonsági másolat az Azure-on is titkosított?<br/>
Igen. Az Azure-ba küldött (aktívan nem használt) adatok titkosítva maradnak. A Microsoft soha nem fejt vissza adatokat. Azure virtuális gép biztonsági mentése esetén az Azure Backup a virtuális gép titkosítására támaszkodik. Ha például a virtuális gépe az Azure Disk Encryption vagy más titkosítási technológiával van titkosítva, az Azure Backup ezzel védi az adatait.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Mi az adatok biztonsági másolatának titkosításához használt titkosítási kulcs minimális hossza? <br/>
A titkosítási kulcsnak legalább 16 karakter hosszúnak kell lennie.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Mi történik, ha elveszítem a titkosítási kulcsot? Helyre tudom állítani az adatokat (vagy) a Microsoft helyre tudja állítani az adatokat? <br/>
Az adatok biztonsági másolatának titkosításához használt kulcs csak az ügyfél telephelyén van jelen. A Microsoft nem tárol másolatot az Azure-ban, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.

