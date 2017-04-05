---
title: Mi az az Azure Backup? | Microsoft Docs
description: "Az Azure Backup használatával biztonsági mentést végezhet, és visszaállíthatja az adatokat Windows Serverekről, Windows-munkaállomásokról, System Center DPM-kiszolgálókról, valamint az Azure-beli virtuális gépekről."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "biztonsági mentés és visszaállítás; recovery services; biztonsági mentési megoldások"
ms.assetid: 0d2a7f08-8ade-443a-93af-440cbf7c36c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/13/2017
ms.author: markgal;trinadhk; anuragm
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: a2452b95c9355957b10477a6e457000f0eba6a9c
ms.lasthandoff: 04/04/2017


---
# <a name="overview-of-the-features-in-azure-backup"></a>Az Azure Backup szolgáltatásainak áttekintése
Az Azure Backup olyan Azure-alapú szolgáltatás, amellyel biztonsági másolatot készíthet adatairól (vagy megvédheti adatait), és visszaállíthatja őket a Microsoft-felhőből. Az Azure Backup megbízható, biztonságos és költséghatékony felhőalapú megoldással váltja fel a meglévő helyszíni vagy külső helyszínen lévő biztonsági mentési megoldást. Az Azure Backup több összetevőjét letöltheti és telepítheti a megfelelő számítógépre, kiszolgálóra vagy a felhőbe. A telepítendő összetevő vagy ügynök attól függ, hogy mit szeretne megvédeni. Minden Azure Backup-összetevővel (függetlenül attól, hogy helyszíni vagy a felhőben tárolt adatokat kíván védeni) készíthetők biztonsági másolatok az Azure mentési tárolójába. Az [Azure Backup-összetevők táblázatában](backup-introduction-to-azure-backup.md#which-azure-backup-components-should-i-use) (a cikk későbbi részében) azzal kapcsolatban talál információkat, hogy mely összetevőt kell használnia adott adatok, alkalmazások és számítási feladatok védelmére.

[Áttekintő videó megtekintése az Azure Backupról](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## <a name="why-use-azure-backup"></a>Miért érdemes az Azure Backupot használni?
A hagyományos biztonsági mentési megoldások úgy fejlődtek, hogy a felhőt a lemezekhez és szalagokhoz hasonló végpontként vagy statikus tár célhelyként kezeljék. Bár ez a megközelítés egyszerű, de korlátozott, és nem használja ki maradéktalanul a mögöttes felhőplatformot, ezáltal költséges, nem hatékony megoldást eredményez. Más megoldások drágák lehetnek, mert Ön végül a nem megfelelő típusú tárhelyért vagy szükségtelen tárhelyért fizet. Más megoldások gyakran nem hatékonyak, mert nem szerepel a lehetőségeik között az a tárhelytípus vagy tárhelyméret, amelyre Önnek szüksége van, vagy a felügyeleti feladatok elvégzése túl sok időt vesz igénybe. Ezzel szemben az Azure Backup legfontosabb előnyei a következők:

**Automatikus tárolófelügyelet** – A hibrid környezetek gyakran megkövetelik a heterogén tárhely alkalmazását – valamennyi tárhely szükséges a helyszínen, valamennyi a felhőben. Az Azure Backup szolgáltatással nem kell költenie helyszíni tárolóeszközökre. Az Azure Backup automatikusan foglalja le és kezeli a Backup-tárolót, és használatalapú modellt alkalmaz. A használatalapú elszámolás azt jelenti, hogy csak a felhasznált tárhelyért kell fizetnie. További információkat az [Azure díjszabására vonatkozó cikkben](https://azure.microsoft.com/pricing/details/backup) talál.

**Korlátlan méretezés** – Az Azure Backup az Azure-felhő mögöttes teljesítményét és korlátlan méretezhetőségét használja fel a magas rendelkezésre állás biztosításához – a karbantartás és a figyelés többletterhe nélkül. Beállíthat riasztásokat az egyes eseményekre vonatkozó információszolgáltatás céljából, de nem kell aggódnia a felhőben tárolt adatainak magas rendelkezésre állása miatt.

**Többféle tárolási lehetőség** – A magas rendelkezésre állás egyik megoldása a tárolóreplikáció. Az Azure Backup két replikációtípust nyújt: [helyileg redundáns tárolást](../storage/storage-redundancy.md#locally-redundant-storage) és [georedundáns tárolást](../storage/storage-redundancy.md#geo-redundant-storage). A biztonságimásolat-tárolási lehetőségek közül válassza az Önnek megfelelőt:

* A helyileg redundáns tárolás (LRS) háromszor replikálja az adatokat (az adatok három másolatát hozza létre) egy párosított adatközpontban, ugyanabban a régióban. Az LRS egy alacsony költségű megoldás az adatok védelmére a helyi hardveres hibák esetén.

* A georedundáns tárolás (GRS) az adatait egy másodlagos régióba replikálja (több száz kilométerre a forrásadatok elsődleges helyétől). A GRS módszer költségesebb, mint az LRS, de adatainak megőrzését magasabb szinten biztosítja, még regionális szolgáltatáskiesés esetére is.

**Korlátlan adatátvitel** – Az Azure Backup nem korlátozza a bejövő vagy kimenő adatok mennyiségét. Az Azure Backup nem számol fel díjat sem az átvitt adatokért. Azonban, ha nagy mennyiségű adat importálására az Azure Import/Export szolgáltatást használja, a bejövő adatoknak van bizonyos költségvonzatuk. Ennek költségére vonatkozóan [az offline biztonsági mentésnek az Azure Backup szolgáltatásban alkalmazott munkafolyamatát](backup-azure-backup-import-export.md) ismertető cikkben talál bővebb információt. Kimenő adatokon a Backup-tárolóból a visszaállítási művelet során átvitt adatok értendők.

**Adattitkosítás** – Az adattitkosítás adatainak biztonságos átvitelét és tárolását teszi lehetővé a nyilvános felhőben. A titkosítás jelszavát helyileg tárolja, és soha nem kerül az Azure-ba, illetve nincs ott tárolva. Ha vissza kell állítani valamilyen adatot, csak Ön rendelkezik a titkosítási jelszóval vagy a kulccsal.

**Alkalmazáskonzisztens biztonsági mentés** – Akár egy fájlkiszolgáló, akár egy virtuális gép vagy SQL Database-adatbázis biztonsági mentéséről van szó, tudnia kell, hogy a helyreállítási pont rendelkezik-e az összes szükséges adattal a biztonsági másolat visszaállításához. Az Azure Backup olyan alkalmazáskonzisztens biztonsági mentést nyújt, amely garantálja, hogy az adatok visszaállításakor ne legyen szükség további javításokra. Az alkalmazáskonzisztens adatok visszaállítása rövidebb idő alatt végrehajtható, így gyorsan visszatérhet egy működőképes állapotba.

**Hosszú távú megőrzés** – Ahelyett, hogy biztonsági másolatait lemezről szalagra mentené, majd a szalagot egy külső helyre szállítaná, az Azure-t használhatja rövid és hosszú távú megőrzésre. Az Azure nem korlátozza az adatok megőrzési idejét a Backup- vagy Recovery Services-tárolókban. Tetszőleges ideig őrizheti meg az adatokat a tárolókban. Az Azure Backup védett példányonként 9999 helyreállítási pontos felső határral rendelkezik. A cikk [Biztonsági mentés és megőrzés](backup-introduction-to-azure-backup.md#backup-and-retention) című részéből megtudhatja, milyen hatással lehet ez a korlát a biztonsági mentési igényeire.  

## <a name="which-azure-backup-components-should-i-use"></a>Melyik Azure Backup-összetevőt használjam?
Ha nem tudja biztosan, hogy melyik Azure Backup-összetevő felel meg az igényeinek, az alábbi táblázatból megtudhatja, hogy az egyes összetevők minek a védelmét tudják biztosítani. Az Azure Portal tartalmaz egy beépített varázslót, amely végigvezeti az összetevő kiválasztásának, letöltésének és telepítésének folyamatán. A varázsló, amely részét képezi a helyreállítási tár létrehozási folyamatának, végigvezeti Önt a biztonsági mentési cél kiválasztásának, illetve a védendő adatok vagy alkalmazások kiválasztásának lépésein.

| Összetevő | Előnyök | Korlátok | Mi van védve? | Hol tárolja a biztonsági mentéseket? |
| --- | --- | --- | --- | --- |
| Azure Backup (MARS) ügynöke |<li>Elkészíti a fizikai vagy virtuális Windows operációs rendszereken lévő fájlok és mappák biztonsági másolatát (a virtuális gépek lehetnek helyszíniek, vagy lehetnek az Azure-ban is)<li>Nincs szükség különálló biztonsági mentési kiszolgálóra. |<li>Biztonsági mentés naponta 3-szor. <li>Nem alkalmazásfüggő; csak fájl-/mappa-/kötetszintű visszaállítás. <li>  Nincs Linux-támogatás. |<li>Fájlok <li>Mappák |Azure Backup-tároló |
| System Center DPM |<li>Alkalmazásfüggő pillanatképek (VSS)<li>Teljes rugalmasság a biztonsági mentés időpontjának kiválasztásakor<li>Helyreállítás részletessége (összes)<li>Képes használni az Azure Backup-tárolót<li>Linux-támogatás Hyper-V és VMware virtuális gépeken <li>VMware virtuális gépek biztonsági mentése és visszaállítása a DPM 2012 R2-es verziójával |Nem készíthető biztonsági mentés az Oracle számítási feladatról.|<li>Fájlok <li>Mappák<li> Kötetek <li>Virtuális gépek<li> Alkalmazások<li> Számítási feladatok |<li>Azure Backup-tároló,<li> Helyileg csatlakoztatott lemez,<li>  Szalag (csak helyszíni) |
| Azure Backup Server |<li>Alkalmazásfüggő pillanatképek (VSS)<li>Teljes rugalmasság a biztonsági mentés időpontjának kiválasztásakor<li>Helyreállítás részletessége (összes)<li>Képes használni az Azure Backup-tárolót<li>Linux-támogatás Hyper-V és VMware virtuális gépeken<li>VMware virtuális gépek biztonsági mentése és visszaállítása <li>Nincs szükség System Center-licencre |<li>Nem készíthető biztonsági mentés az Oracle számítási feladatról.<li>Mindig élő Azure-előfizetést igényel<li>A szalagos biztonsági mentés nem támogatott |<li>Fájlok <li>Mappák<li> Kötetek <li>Virtuális gépek<li> Alkalmazások<li> Számítási feladatok |<li>Azure Backup-tároló,<li> Helyileg csatlakoztatott lemez |
| Azure IaaS virtuális gép biztonsági mentése |<li>Natív biztonsági mentések Windowshoz/Linuxhoz<li>Nincs szükség speciális ügynök telepítésére<li>Hálószintű biztonsági mentés, nincs szükség biztonsági mentési infrastruktúrára |<li>Virtuális gépek napi biztonsági mentése <li>Virtuális gépek visszaállítása csak lemezszinten<li>Nem készíthető biztonsági mentés a helyszínen |<li>Virtuális gépek <li>Minden lemez (PowerShell használatával) |<p>Azure Backup-tároló</p> |

## <a name="what-are-the-deployment-scenarios-for-each-component"></a>Mik az egyes összetevők üzembe helyezési lehetőségei?
| Összetevő | Üzembe helyezhető az Azure-ban? | Üzembe helyezhető a helyszínen? | A céltároló támogatott |
| --- | --- | --- | --- |
| Azure Backup (MARS) ügynöke |<p>**Igen**</p> <p>Az Azure Backup ügynöke az Azure-ban futó bármely Windows Server rendszerű virtuális gépre telepíthető.</p> |<p>**Igen**</p> <p>Az Azure Backup ügynöke bármely Windows Server rendszerű virtuális gépre vagy fizikai gépre telepíthető.</p> |<p>Azure Backup-tároló</p> |
| System Center DPM |<p>**Igen**</p><p>További információkat tudhat meg arról, [hogyan védheti meg a számítási feladatokat az Azure-ban a System Center DPM-mel](backup-azure-dpm-introduction.md).</p> |<p>**Igen**</p> <p>További információkat tudhat meg arról, [hogyan védheti meg a számítási feladatokat és a virtuális gépeket az adatközpontban](https://technet.microsoft.com/en-us/system-center-docs/dpm/data-protection-manager).</p> |<p>Helyileg csatlakoztatott lemez,</p> <p>Azure Backup-tároló,</p> <p>szalag (csak helyszíni)</p> |
| Azure Backup Server |<p>**Igen**</p><p>További információkat tudhat meg arról, [hogyan védheti meg a számítási feladatokat az Azure-ban az Azure Backup Serverrel](backup-azure-microsoft-azure-backup.md).</p> |<p>**Igen**</p> <p>További információkat tudhat meg arról, [hogyan védheti meg a számítási feladatokat az Azure-ban az Azure Backup Serverrel](backup-azure-microsoft-azure-backup.md).</p> |<p>Helyileg csatlakoztatott lemez,</p> <p>Azure Backup-tároló</p> |
| Azure IaaS virtuális gép biztonsági mentése |<p>**Igen**</p><p>Az Azure-háló része</p><p>Az [Azure szolgáltatásként kínált infrastruktúra (IaaS) rendszerű virtuális gépek biztonsági mentéséhez](backup-azure-vms-introduction.md) készült.</p> |<p>**Nem**</p> <p>A System Center DPM-mel biztonsági mentést készíthet az adatközpontban lévő virtuális gépekről.</p> |<p>Azure Backup-tároló</p> |

## <a name="which-applications-and-workloads-can-be-backed-up"></a>Melyik alkalmazásokról és számítási feladatokról készíthető biztonsági mentés?
A következő táblázat az Azure Backup használatával védhető adatokat és számítási feladatokat tartalmazza. Az Azure Backup-megoldás oszlopban az adott megoldás telepítési dokumentációjára mutató hivatkozások szerepelnek. Minden Azure Backup-összetevő klasszikus (Service Manager-alapú telepítés) és Resource Manager-alapú üzemi modell környezetben is telepíthető.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

| Adat vagy számítási feladat | Forráskörnyezet | Azure Backup-megoldás |
| --- | --- | --- |
| Fájlok és mappák |Windows Server |<p>[Az Azure Backup ügynöke](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Fájlok és mappák |Windows rendszerű számítógép |<p>[Az Azure Backup ügynöke](backup-configure-vault.md),</p> <p>[System Center DPM](backup-azure-dpm-introduction.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Hyper-V virtuális gép (Windows) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Hyper-V virtuális gép (Linux) |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Microsoft SQL Server |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Microsoft SharePoint |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Microsoft Exchange |Windows Server |<p>[System Center DPM](backup-azure-backup-sql.md) (+ az Azure Backup ügynöke),</p> <p>[Azure Backup Server](backup-azure-microsoft-azure-backup.md) (tartalmazza az Azure Backup ügynökét)</p> |
| Azure IaaS virtuális gépek (Windows) |Azure-ban futó |[Azure Backup (virtuálisgép-bővítmény)](backup-azure-vms-introduction.md) |
| Azure IaaS virtuális gépek (Linux) |Azure-ban futó |[Azure Backup (virtuálisgép-bővítmény)](backup-azure-vms-introduction.md) |

## <a name="linux-support"></a>Linux-támogatás
Az alábbi táblázat tartalmazza a Linuxot támogató Azure Backup-összetevőket.  

| Összetevő | Linux (Azure által támogatott) támogatása |
| --- | --- |
| Azure Backup (MARS) ügynöke |Nem (csak Windows-alapú ügynök) |
| System Center DPM |Fájlkonzisztens biztonsági mentés, csak Hyper-V megoldásban<br/> (Azure-beli virtuális gépek esetén nem érhető el) |
| Azure Backup Server |Fájlkonzisztens biztonsági mentés, csak Hyper-V megoldásban<br/> (Azure-beli virtuális gépek esetén nem érhető el) |
| Azure IaaS virtuális gép biztonsági mentése |Igen (alkalmazáskonzisztens biztonsági mentés [szkript előtti és utáni keretrendszerrel](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)) |

## <a name="using-premium-storage-vms-with-azure-backup"></a>Premium Storage virtuális gépek használata az Azure Backup szolgáltatással
Az Azure Backup szolgáltatás a Premium Storage virtuális gépek védelmét is biztosítja. Az Azure Premium Storage egy SSD-alapú tárolási szolgáltatás, amely a nagy I/O-igényű számítási feladatok ellátására készült. A Premium Storage a virtuális gépek számítási feladataihoz kínál vonzó megoldást. Részletesebb áttekintés talál a [Premium Storage szolgáltatást mint az Azure virtuális gépek számítási feladataihoz használható nagy teljesítményű tárterületet](../storage/storage-premium-storage.md) ismertető cikkben.

### <a name="back-up-premium-storage-vms"></a>A Premium Storage virtuális gépek biztonsági mentése
Premium Storage virtuális gépek biztonsági mentésekor a Backup szolgáltatás átmeneti, „AzureBackup-” nevű előkészítési helyet hoz létre a Premium Storage-fiókban. Az előkészítési hely megegyezik a helyreállítási pont pillanatképének méretével. Győződjön meg róla, hogy elég szabad hely áll rendelkezésre a tárfiókban az átmeneti előkészítési hely számára. További információért tekintse meg a [Premium Storage korlátozásaival](../storage/storage-premium-storage.md#scalability-and-performance-targets) foglalkozó témakört. A biztonsági mentési feladat befejezése után a rendszer törli az előkészítési helyet. Az előkészítési helyhez használt tároló ára megfelel a [Premium Storage díjszabásnak](../storage/storage-premium-storage.md#pricing-and-billing).

> [!NOTE]
> Ne módosítsa vagy szerkessze az előkészítési helyet.
>
>

### <a name="restore-premium-storage-vms"></a>A Premium Storage virtuális gépek visszaállítása
A Premium Storage virtuális gépek Premium Storage tárolóba vagy normál tárolóba állíthatók vissza. A Premium Storage virtuális gép helyreállítási pontjának Premium Storage-ba való visszaállítása a visszaállítás tipikus folyamata. Azonban költséghatékony lehet a Premium Storage virtuális gépek helyreállítási pontjait Standard szintű tárolóba visszaállítani. Ez a visszaállítási típus használható, ha a fájlok egy részére van szüksége a virtuális gépről.

## <a name="using-managed-disk-vms-with-azure-backup"></a>Felügyelt lemezes virtuális gépek használata az Azure Backuppal
Az Azure Backup védelmet biztosít a felügyelt lemezes virtuális gépek számára. A felügyelt lemezek használatával mentesül a virtuális gépek tárfiókjainak kezelése alól, és lényegesen leegyszerűsödik a virtuális gépek üzembe helyezése.

### <a name="back-up-managed-disk-vms"></a>Felügyelt lemezes virtuális gépek biztonsági mentése
A felügyelt lemezeken található virtuális gépek biztonsági mentése megegyezik a Resource Manager-alapú virtuális gépek biztonsági mentésével. Az Azure Portalon közvetlenül a virtuálisgép-nézetből vagy a Recovery Services-tároló nézetéből konfigurálhatja a biztonsági mentési feladatot. A felügyelt lemezeken található virtuális gépek biztonsági mentését a felügyelt lemezeken kiépített visszaállításipont-gyűjteményekkel végezheti el. Az Azure Backup jelenleg nem támogatja az Azure Disk Encryption (ADE) használatával titkosított, felügyelt lemezes virtuális gépek biztonsági mentését.

### <a name="restore-managed-disk-vms"></a>Felügyelt lemezes virtuális gépek visszaállítása
Az Azure Backup lehetővé teszi teljes felügyelt lemezes virtuális gépek visszaállítását, illetve a felügyelt lemezek visszaállítását egy Resource Manager-tárfiókba. A visszaállítási folyamat során az Azure kezeli a felügyelt lemezeket. Az ügyfél (tehát Ön) kezeli a visszaállítási folyamat részeként létrehozott tárfiókot.

## <a name="what-are-the-features-of-each-backup-component"></a>Milyen funkciókat nyújtanak az egyes Backup-összetevők?
A következő szakaszok táblázatai az egyes Azure Backup-összetevők különböző funkcióinak elérhetőségét vagy támogatottságát foglalják össze. A további támogatásra vagy részletekre vonatkozó információkat a táblázatok alatt találja.

### <a name="storage"></a>Tárolás
| Szolgáltatás | Az Azure Backup ügynöke | System Center DPM | Azure Backup Server | Azure IaaS virtuális gép biztonsági mentése |
| --- | --- | --- | --- | --- |
| Azure Backup-tároló |![Igen][green] |![Igen][green] |![Igen][green] |![Igen][green] |
| Lemezes tárolás | |![Igen][green] |![Igen][green] | |
| Szalagos tárolás | |![Igen][green] | | |
| Tömörítés <br/>(a Backup-tárolóban) |![Igen][green] |![Igen][green] |![Igen][green] | |
| Növekményes biztonsági mentés |![Igen][green] |![Igen][green] |![Igen][green] |![Igen][green] |
| Lemezdeduplikáció | |![Részlegesen][yellow] |![Részlegesen][yellow] | |

![tábla kulcsa](./media/backup-introduction-to-azure-backup/table-key.png)

A Backup-tároló az előnyben részesített tárolási cél minden összetevőben. A System Center DPM és az Azure Backup Server lehetőséget nyújt helyi lemezmásolat készítésére is. Azonban csak a System Center DPM nyújt lehetőséget az adatok szalagos tárolóeszközre írására.

#### <a name="compression"></a>Tömörítés
A biztonsági másolatok a szükséges tárterület csökkentése érdekében tömörítve vannak. Csak a virtuálisgép-bővítmény összetevő nem használ tömörítést. A virtuálisgép-bővítmény minden biztonsági mentési adatot az Ön tárfiókjából az ugyanazon régióban lévő Backup-tárolóba másol. Az adatátvitel során nem használ tömörítést. A tömörítés nélküli adatátvitel némileg megnöveli a felhasznált tárterületet. Az adatok tömörítés nélküli tárolása azonban gyorsabb helyreállítást tesz lehetővé, ha szüksége van a visszaállítási pontra.


#### <a name="disk-deduplication"></a>Lemezdeduplikáció
A deduplikáció nyújtotta előnyöket a System Center DPM vagy az Azure Backup Server [Hyper-V virtuális gépeken](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx) való üzembe helyezése esetén használhatja ki. A Windows Server az adatok deduplikációját (a gazdagép szintjén) a virtuális géphez Backup-tárolóként csatlakoztatott virtuális merevlemezeken (VHD-ken) végzi el.

> [!NOTE]
> A deduplikáció az Azure-ban egyik Backup-összetevőhöz sem érhető el. Amikor System Center DPM és Backup Server van üzembe helyezve az Azure-ban, a virtuális géphez csatolt tárolólemezek nem deduplikálhatók.
>
>

### <a name="incremental-backup-explained"></a>A növekményes biztonsági mentés bemutatása
Minden Azure Backup-összetevő támogatja a növekményes biztonsági mentést a céltárolótól függetlenül (lemez, szalag, Backup-tároló). A növekményes biztonsági mentés biztosítja, hogy a biztonsági mentések hatékonyan használják a tárhelyet és az időt, mert csak az utolsó biztonsági mentés óta végzett módosításokat viszi át.

#### <a name="comparing-full-differential-and-incremental-backup"></a>A teljes, a különbségi és a növekményes biztonsági mentés összehasonlítása

A tárhelyhasználat, a helyreállítási időre vonatkozó célkitűzés (RTO), és a hálózatleterheltség eltér az egyes biztonsági mentési módszerek esetében. A biztonsági mentés teljes birtoklási költségének alacsonyan tartásához fontos tudnia, hogyan válassza ki a legjobb biztonsági mentési megoldást. A következő képen a teljes, a különbségi és a növekményes biztonsági mentés összehasonlítása látható. A képen látható A adatforrás 10 tárolási blokkból áll (A1–A10), amelyekről havonta készül biztonsági mentés. Az A2, az A3, az A4 és az A9 módosul az első hónapban, az A5 pedig a következő hónapban.

![a biztonsági mentési módszerek összehasonlítását mutató kép](./media/backup-introduction-to-azure-backup/backup-method-comparison.png)

**Teljes biztonsági mentés** esetén minden biztonsági másolat tartalmazza a teljes adatforrást. A teljes biztonsági mentés minden egyes alkalommal nagy hálózati sávszélességet és tárterületet igényel a biztonsági másolat továbbításakor.

A **különbségi biztonsági mentés** kizárólag a kezdeti biztonsági mentés óta módosított blokkokat tárolja, így kisebb a sávszélesség- és a tárhelyhasználata. A különbségi biztonsági mentések nem őrzik meg a változatlan adatok redundáns másolatait. De mivel az egymást követő biztonsági mentések között változatlanul maradó adatblokkok átvitele és tárolása is megtörténik, a különbségi biztonsági mentés nem hatékony. A második hónapban a módosított A2, A3, A4 és A9 blokkokról készül biztonsági másolat. A harmadik hónapban a módosított A5 blokkal együtt biztonsági másolat készül ugyanezekről a blokkokról. A következő teljes biztonsági mentésig minden alkalommal készül biztonsági másolat a módosított blokkokról.

A **növekményes biztonsági mentés** tárterület- és sávszélesség-kihasználása nagyon hatékony, mivel kizárólag azokat az adatblokkokat tárolja, amelyek az előző biztonsági mentés óta módosultak. A növekményes biztonsági mentés használata esetén nincs szükség rendszeres teljes biztonsági mentésekre. A példában teljes biztonsági mentés készül az első hónapban, a módosított A2, A3, A4 és A9 blokkok meg lesznek jelölve módosítottként, és továbbítva lesznek a következő hónapra. A harmadik hónapban csak az A5 módosított blokk van megjelölve és továbbítva. A kevesebb adat mozgatásával tárterület és hálózati erőforrások takaríthatóak meg, és így csökken a teljes birtoklási költség.   

### <a name="security"></a>Biztonság
| Szolgáltatás | Az Azure Backup ügynöke | System Center DPM | Azure Backup Server | Azure IaaS virtuális gép biztonsági mentése |
| --- | --- | --- | --- | --- |
| Hálózati biztonság<br/> (az Azure-hoz) |![Igen][green] |![Igen][green] |![Igen][green] |![Részlegesen][yellow] |
| Adatbiztonság<br/> (az Azure-ban) |![Igen][green] |![Igen][green] |![Igen][green] |![Részlegesen][yellow] |

![tábla kulcsa](./media/backup-introduction-to-azure-backup/table-key.png)

#### <a name="network-security"></a>Hálózati biztonság
A kiszolgálókról a Backup-tárolóba érkező minden biztonsági mentési adat az Advanced Encryption Standard 256 eljárással van titkosítva. A biztonsági mentési adatokat a rendszer egy biztonságos HTTPS-kapcsolaton keresztül küldi el. Az adatok biztonsági másolata titkosított formában megtalálhatóak a Backup-tárolóban is. Csak Ön mint Azure-ügyfél rendelkezik az adatok zárolásának feloldására szolgáló jelszóval. A Microsoft soha nem tudja visszafejteni az adatok biztonsági másolatát.

> [!WARNING]
> A Backup-tároló létrehozása után csak Önnek lesz hozzáférése a titkosítási kulcshoz. A Microsoft soha nem őriz másolatot a titkosítási kulcsról, és nem rendelkezik hozzáféréssel a kulcshoz. Ha az ügyfél elveszíti a kulcsot, a Microsoft nem tudja helyreállítani az adatok biztonsági másolatát.
>
>

#### <a name="data-security"></a>Adatbiztonság
Az Azure virtuális gépek biztonsági mentéséhez titkosítást kell beállítani a virtuális gépen *belül*. Használja a BitLockert Windows rendszerű virtuális gépeken és a **dm-crypt**-et Linux rendszerű virtuális gépeken. Az Azure Backup nem titkosítja automatikusan az ezen az elérési úton bejövő biztonsági mentési adatokat.

### <a name="network"></a>Network (Hálózat)
| Szolgáltatás | Az Azure Backup ügynöke | System Center DPM | Azure Backup Server | Azure IaaS virtuális gép biztonsági mentése |
| --- | --- | --- | --- | --- |
| Hálózati tömörítés <br/>(a **biztonsági mentési kiszolgálóhoz**) | |![Igen][green] |![Igen][green] | |
| Hálózati tömörítés <br/>(a **biztonsági mentési tárolóhoz**) |![Igen][green] |![Igen][green] |![Igen][green] | |
| Hálózati protokoll <br/>(a **biztonsági mentési kiszolgálóhoz**) | |TCP |TCP | |
| Hálózati protokoll <br/>(a **biztonsági mentési tárolóhoz**) |HTTPS |HTTPS |HTTPS |HTTPS |

![tábla kulcsa](./media/backup-introduction-to-azure-backup/table-key-2.png)

A virtuálisgép-bővítmény (az IaaS virtuális gépen) közvetlenül az Azure Storage-fiókból olvassa be az adatokat a tárolóhálózaton keresztül, ezért ezt a forgalmat nem kell tömöríteni.

Ha a System Center DPM vagy az Azure Backup Server szolgáltatásban készít biztonsági másolatot az adatokról, tömörítse az elsődleges kiszolgálóról a biztonsági mentési kiszolgálóra mentett adatokat. A DPM-ben vagy az Azure Backup Serverben való biztonsági mentés előtti adattömörítéssel kímélheti a sávszélességet.

#### <a name="network-throttling"></a>A hálózati sávszélesség szabályozása
Az Azure Backup ügynökével szabályozhatja a hálózati sávszélességet, így az adatátvitel alatt vezérelheti a hálózati sávszélesség használatát. A szabályozás akkor lehet hasznos, ha adatokról kell biztonsági másolatot készítenie a munkaidő alatt, de nem szeretné, hogy a biztonsági mentési folyamat zavarja a többi internetes forgalmat. Az adatátvitel szabályozása a biztonsági mentési és a visszaállítást tevékenységekre vonatkozik.

## <a name="backup-and-retention"></a>Biztonsági mentés és megőrzés

Az Azure Backup *védett példányonként* 9999 helyreállítási pontos felső határral rendelkezik. Ezeket a helyreállítási pontokat biztonsági másolatoknak, illetve pillanatképeknek is hívják. A védett példányok olyan számítógépek, kiszolgálók (fizikai vagy virtuális) vagy számítási feladatok, amelyek úgy vannak konfigurálva, hogy biztonsági mentést végezzenek az Azure-ba. További információkért lásd a [Mi az a védett példány?](backup-introduction-to-azure-backup.md#what-is-a-protected-instance) szakaszt. Az egyes példányok védelméhez biztonsági másolatot kell készíteni az adatokról. Az adatok biztonsági másolata maga a védelem. Ha a forrásadatok elvesznek vagy sérülnek, a biztonsági másolatból vissza lehet állítani azokat. Az alábbi táblázat az egyes összetevők maximális biztonsági mentési gyakoriságát ismerteti. A biztonsági mentési szabályzat konfigurációja határozza meg, hogy milyen gyorsan használja fel a helyreállítási pontokat. Ha például minden nap létrehoz egy helyreállítási pontot, akkor 27 év után fogynának el a helyreállítási pontjai. Ha havonta csak egy helyreállítási pontot használ fel, akkor helyreállítási pontjai 833 év után fogynának el. A Backup szolgáltatás nem társít lejárati időt a helyreállítási pontokhoz.

|  | Az Azure Backup ügynöke | System Center DPM | Azure Backup Server | Azure IaaS virtuális gép biztonsági mentése |
| --- | --- | --- | --- | --- |
| Biztonsági mentés gyakorisága<br/> (a Backup-tárolóba) |Napi három biztonsági mentés |Napi két biztonsági mentés |Napi két biztonsági mentés |Napi egy biztonsági mentés |
| Biztonsági mentés gyakorisága<br/> (lemezre) |Nem alkalmazható |<li>15 percenként az SQL Serverhez <li>Minden órában más számítási feladatokhoz |<li>15 percenként az SQL Serverhez <li>Minden órában más számítási feladatokhoz</p> |Nem alkalmazható |
| Megőrzési beállítások |Napi, heti, havi, éves |Napi, heti, havi, éves |Napi, heti, havi, éves |Napi, heti, havi, éves |
| Helyreállítási pontok maximális száma védett példányonként |9999|9999|9999|9999|
| Maximális megőrzési időtartam |A biztonsági mentés gyakoriságától függően változik |A biztonsági mentés gyakoriságától függően változik |A biztonsági mentés gyakoriságától függően változik |A biztonsági mentés gyakoriságától függően változik |
| Helyreállítási pontok a helyi lemezen |Nem alkalmazható |<li>64 fájlkiszolgálókhoz,<li>448 alkalmazáskiszolgálókhoz |<li>64 fájlkiszolgálókhoz,<li>448 alkalmazáskiszolgálókhoz |Nem alkalmazható |
| Helyreállítási pontok a szalagon |Nem alkalmazható |Korlátlan |Nem alkalmazható |Nem alkalmazható |

## <a name="what-is-a-protected-instance"></a>Mi az a védett példány?
A védett példány egy általános elnevezés az olyan Windows-számítógépekre, -kiszolgálókra (fizikai vagy virtuális) vagy SQL-adatbázisokra, amelyek úgy vannak konfigurálva, hogy biztonsági mentést végezzenek az Azure-ba. A példány onnantól tekinthető védettnek, hogy konfigurálja a számítógép, kiszolgáló vagy adatbázis biztonsági mentésére vonatkozó házirendet, és létrehozott egy biztonsági másolatot az adatokról. Az adott védett példány rákövetkező biztonsági másolatai (az úgynevezett helyreállítási pontok), növelik a foglalt tárhely méretét. Védett példányonként 9999 helyreállítási pontot hozhat létre. Ha töröl egy helyreállítási pontot a tárterületről, az nem számít az összesen 9999 helyreállítási pont közé.
Néhány gyakori példa védett példányokra: virtuális gépek, alkalmazáskiszolgálók, adatbázisok, valamint Windows operációs rendszert futtató személyi számítógépek. Példa:

* Egy Hyper-V vagy Azure IaaS hipervizorhálót futtató virtuális gép. A virtuális gép vendég operációs rendszerei lehetnek Windows Server vagy Linux rendszerek.
* Alkalmazáskiszolgáló: Az alkalmazáskiszolgáló lehet egy fizikai vagy virtuális gép, amely a Windows Servert futtatja, valamint olyan számítási feladatokat, amelyekről biztonsági másolatot kell készíteni. Gyakori számítási feladat a Microsoft SQL Server, a Microsoft Exchange Server, a Microsoft SharePoint Server, valamint a Windows Server Fájlkiszolgáló szerepköre. A számítási feladatok biztonsági mentéséhez a System Center Data Protection Manager (DPM) vagy az Azure Backup Server szükséges.
* Egy személyi számítógép, munkaállomás vagy laptop, amelyen a Windows operációs rendszer fut.


## <a name="what-is-the-vault-credential-file"></a>Mi a tároló hitelesítőadat-fájlja?
A tároló hitelesítőadat-fájlja a portál által az egyes Backup-tárolókhoz létrehozott tanúsítvány. A portál ezután feltölti a nyilvános kulcsot az Access Control Service (ACS) szolgáltatásba. A titkos kulcsot a hitelesítő adatok letöltésekor kapja meg. Ezt a védendő számítógépek regisztrációjához használhatja. A titkos kulcs teszi lehetővé a kiszolgálók és számítógépek hitelesítését a biztonsági mentési adatoknak egy adott Backup-tárolóba való küldéséhez.

A tároló hitelesítő adatait csak a kiszolgálók vagy számítógépek regisztrálásához kell használnia. Azonban a tároló hitelesítő adatait kezelje nagy körültekintéssel, mert ha elveszti őket, vagy más is hozzáfér ezekhez az adatokhoz, más gépek is regisztrálhatók velük ugyanahhoz a tárolóhoz. Mivel a biztonsági mentési adatok olyan jelszóval vannak titkosítva, amelyhez csak Ön férhet hozzá, a meglévő biztonsági mentési adatok biztonsága nem sérülhet. A tároló hitelesítő adatai 48 óra után lejárnak. Míg a Backup-tároló hitelesítő adatait annyiszor töltheti le, ahányszor csak szeretné, a regisztrációhoz csak a legújabb hitelesítő adatok használhatók.

## <a name="how-does-azure-backup-differ-from-azure-site-recovery"></a>Miben különbözik az Azure Backup az Azure Site Recoverytől?
Az Azure Backup és az Azure Site Recovery közös jellemzője, hogy mindkét szolgáltatás használható az adatok biztonsági mentésére és visszaállítására. Azonban a szolgáltatások értékajánlatai eltérőek.

Az Azure Backup biztosítja a helyszíni és a felhőben tárolt adatok védelmét. Az Azure Site Recovery koordinálja a virtuális gépek és a fizikai kiszolgálók replikálását, feladatátvételét és feladat-visszavételét. Mindkét szolgáltatás fontos, mert a vészhelyreállítási megoldásnak biztosítania kell az adatok védelmét és helyreállíthatóságát (Backup), *valamint* a számítási feladatok rendelkezésre állását (Site Recovery) leállások esetén.

A következő fogalmak segíthetnek a fontos döntések meghozatalában a biztonsági mentéssel és a vészhelyreállítással kapcsolatban.

| Fogalom | Részletek | Biztonsági mentés | Vészhelyreállítás (DR) |
| --- | --- | --- | --- |
| Helyreállítási időkorlát (RPO) |Az elfogadható adatveszteség mennyisége, ha helyreállítást kell végezni. |A biztonsági mentési megoldások elfogadható RPO-ja nagyon változó. Virtuális gépek biztonsági mentései esetén általában egy nap az RPO, míg adatbázisok biztonsági mentései esetén akár 15 perc is lehet. |A vészhelyreállítási megoldások alacsony RPO-kkal rendelkeznek. A DR másolat néhány másodperccel vagy néhány perccel késhet. |
| Helyreállítási időre vonatkozó célkitűzés (RTO) |A helyreállítás vagy visszaállítás elvégzéséhez szükséges idő. |A nagyobb RPO miatt a biztonsági mentési megoldások által feldolgozandó adatmennyiség általában sokkal nagyobb, ami hosszabb RTO-khoz vezet. Napokba telhet például az adatok szalagokról való visszaállítása attól függően, hogy mennyi ideig tart a szalag szállítása egy külső helyről. |A vészhelyreállítási megoldások RTO-i sokkal rövidebbek, mert jobban szinkronban vannak a forrással. Kevesebb módosítást kell feldolgozni. |
| Megőrzés |Az adatok tárolásának időtartama |A műveleti helyreállítást igénylő forgatókönyvekben (adatsérülés, véletlen fájltörlés, az operációs rendszer hibája) az adatok biztonsági másolatát általában legfeljebb 30 napig őrzi meg a rendszer.<br>A megfelelőség miatt lehet, hogy hónapokig vagy akár évekig kell tárolni az adatokat. Az adatok biztonsági másolata ideális az ilyen esetekben végzett archiváláshoz. |A vészhelyreállításhoz csak műveleti helyreállítási adatokra van szükség, ami általában néhány órát vagy legfeljebb egy napot vesz igénybe. A DR (vészhelyreállítási) megoldásokban használt részletes adatrögzítés miatt a DR-adatok hosszú távú megőrzése nem javasolt. |

## <a name="next-steps"></a>Következő lépések
A következő oktatóanyagok lépésről lépésre ismertetik a Windows Serveren lévő adatok vagy az Azure-beli virtuális gépek védelmét:

* [Fájlok és mappák biztonsági mentése](backup-try-azure-backup-in-10-mins.md)
* [Azure virtuális gépek biztonsági mentése](backup-azure-vms-first-look-arm.md)

Egyéb számítási feladatok védelméről az alábbi cikkekből tájékozódhat részletesebben:

* [A Windows Server biztonsági mentése](backup-configure-vault.md)
* [Alkalmazás számítási feladatainak biztonsági mentése](backup-azure-microsoft-azure-backup.md)
* [Azure IaaS virtuális gépek biztonsági mentése](backup-azure-vms-prepare.md)

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

