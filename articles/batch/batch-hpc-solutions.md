---
title: "Batch- és HPC-megoldások a felhőben – Azure | Microsoft Docs"
description: "Megismerheti a Batch- és nagy teljesítményű feldolgozási (HPC- és Big Compute-) forgatókönyveket és megoldási lehetőségeket az Azure rendszerben"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0bab12648a31b416bd7ceca75f0f83f5da226bad
ms.lasthandoff: 03/31/2017


---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>Batch- és HPC-megoldások nagyméretű számítási feladatokhoz

Az Azure hatékony, méretezhető felhőbeli megoldásokat nyújt a Batch- és nagy teljesítményű feldolgozási (HPC), más néven *Big Compute-forgatókönyvekhez*. Ebben a részben többet tudhat meg a Big Compute számítási feladatokról és az azokat támogató Azure-szolgáltatásokról, a cikk későbbi részében pedig [megoldás-forgatókönyveket](#scenarios) találhat. Ez a cikk elsősorban a technikai döntéshozóknak, informatikai vezetőknek és független szoftverkereskedőknek szól, de más informatikai szakemberek és fejlesztők is használhatják a megoldások megismeréséhez.

A szervezeteknek nagy méretű számítási feladatokat kell megoldaniuk: műszaki tervezés és elemzés, képrenderelés, összetett modellezés, Monte Carlo-szimulációk, pénzügyi kockázatszámítások és egyebek. Az Azure a szükséges erőforrásokkal, méretezéssel és ütemezéssel segít a szervezeteknek megoldani ezeket a problémákat. Az Azure-ral a szervezetek a következőket tehetik:

* Hibrid megoldások létrehozása egy helyszíni HPC-fürt kiterjesztésével, hogy a csúcsidőszakokban a felhőbe lehessen kiszervezni a számítási feladatokat
* HPC-fürteszközök és számítási feladatok futtatása kizárólag az Azure-ban
* Felügyelt és méretezhető Azure-szolgáltatások, például a [Batch](https://azure.microsoft.com/documentation/services/batch/) használatával futtathatja a nagy teljesítményt igénylő számítási feladatokat anélkül, hogy számítási infrastruktúrát kellene telepítenie és kezelnie

Jóllehet a jelen cikk erre nem terjed ki, az Azure képességek teljes készletét, több architektúrát és fejlesztési eszközöket is nyújt a fejlesztők és partnerek számára nagyméretű, egyéni Big Compute-munkafolyamatok létrehozásához. A növekvő partneri ökoszisztéma pedig segít abban, hogy hatékonyan használhassa a Big Compute számítási feladatokat az Azure-felhőben.

## <a name="batch-and-hpc-applications"></a>Batch- és HPC-alkalmazások
A webalkalmazásokkal és számos üzletági alkalmazással ellentétben a Batch- és HPC-alkalmazásoknak meghatározott elejük és végük van, és ütemezve vagy igény szerint is futtathatók, akár órákig vagy még tovább. A legtöbbjük két fő kategória sorolható: *belsőleg párhuzamos* (néha „zavaróan párhuzamosnak” hívják, mert az általuk megoldott problémák általában párhuzamosan futnak több számítógépen vagy processzoron) és *szorosan összekapcsolt*. Ezekről az alkalmazástípusokról a következő táblázatban talál további információt. Egyes Azure-megoldások alkalmasabbak bizonyos típusú feladatokra.

> [!NOTE]
> A Batch- és HPC-megoldásokban az alkalmazások futó példányát általában *feladatnak* hívják, és minden feladat *tevékenységekre* osztható. Az alkalmazás fürtözött számítási erőforrásait gyakran *számítási csomópontoknak* nevezik.
> 
> 

| Típus | Jellemzők | Példák |
| --- | --- | --- |
| **Belsőleg párhuzamos**<br/><br/>![Intrinsically parallel][parallel] |• Az egyes számítógépek egymástól függetlenül futtatják az alkalmazáslogikát<br/><br/> • Számítógépek hozzáadásával az alkalmazás méretezhető, így csökken a számítási idő<br/><br/>• Az alkalmazás különálló végrehajtható fájlokból áll, vagy ügyfél által meghívott szolgáltatásokra oszlik (szolgáltatásorientált architektúrájú vagy SOA-alkalmazás) |• Pénzügyi kockázat modellezése<br/><br/>• Képrenderelés és -feldolgozás<br/><br/>• Médiatartalmak kódolása és átkódolása<br/><br/>• Monte Carlo-szimulációk<br/><br/>• Szoftvertesztelés |
| **Szorosan összekapcsolt**<br/><br/>![Tightly coupled][coupled] |• Az alkalmazás működéséhez a számítási csomópontoknak kommunikálniuk kell, vagy az eredmények azonnali cseréje szükséges<br/><br/>• A számítási csomópontok a Message Passing Interface (MPI) használatával kommunikálhatnak, amely a párhuzamos számítások gyakran használt kommunikációs protokollja<br/><br/>• Az alkalmazás érzékeny a hálózat késésére és a sávszélességre<br/><br/>• Az alkalmazás teljesítménye nagy sebességű hálózatkezelési technológiákkal, például az InfiniBand technológiával és távoli közvetlen memória-hozzáféréssel (RDMA) javítható |• Olaj- és gáztározók modellezése<br/><br/>• Műszaki tervezés és elemzés, például folyadékdinamikai számítások<br/><br/>• Fizikai szimulációk, például autóbalesetek és nukleáris reakciók<br/><br/>• Időjárás-előrejelzés |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>A Batch- és HPC-alkalmazások felhőben való futtatásának szempontjai
Sok, helyszíni HPC-fürtön való futtatásra tervezett alkalmazást áttelepíthet az Azure-ba vagy egy hibrid (több helyszínen működő) környezetbe. Lehetséges azonban, hogy figyelembe kell vennie néhány korlátozást vagy szempontot, például a következőket:

* **A felhőalapú erőforrások rendelkezésre állása** – A használt felhőalapú számítási erőforrások típusától függően lehet, hogy nem számíthat a gép folyamatos rendelkezésre állására a feladat futtatása közben. Az állapotkezelés és az állapot ellenőrzőpontjai gyakran használt módszerek a lehetséges átmeneti hibák kezelésére, és a felhőalapú erőforrások használatakor nagyobb szükség van rájuk.
* **Adathozzáférés** – A vállalati fürtökön általánosan elérhető adathozzáférési technikák, például az NFS, speciális konfigurációt igényelhetnek a felhőben. Az is előfordulhat, hogy más adathozzáférési gyakorlatokat és mintákat kell alkalmaznia a felhőhöz.
* **Adatáthelyezés** – A nagy mennyiségű adatot feldolgozó alkalmazások esetén stratégiákra van szükség ahhoz, hogy az adatokat felhőalapú tárolóba és számítási erőforrásokra helyezze át. Ehhez nagy sebességű, létesítmények közötti hálózatkezelés lehet szükséges, amely például az [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)-tal biztosítható. Az adatok tárolásával vagy elérésével kapcsolatos jogi, szabályozási és házirendi korlátozásokat is vegye figyelembe.
* **Licencek** – Minden kereskedelmi alkalmazás szállítójánál érdeklődjön a felhőbeli futtatásra vonatkozó licencelési vagy egyéb korlátozásokról. Nem minden szállító kínál használatalapú licencet. Lehet, hogy a megoldásához licenckiszolgálót kell használnia a felhőben, vagy helyszíni licenckiszolgálóhoz kell csatlakoznia.

### <a name="big-compute-or-big-data"></a>Big Compute vagy Big Data?
A Big Compute- és a Big Data-alkalmazások közötti határvonal nem mindig egyértelmű, és egyes alkalmazások mindkettő jellemzőivel rendelkezhetnek. Mindkettőben nagy méretű számítások futnak, általában számítógépfürtökön. A megoldások megközelítései és a támogatási eszközök azonban eltérhetnek.

• A **Big Compute** általában olyan alkalmazásokkal használatos, amelyek processzorteljesítményt és memóriát igényelnek. Ilyenek például a mérnöki szimulációk, a pénzügyi kockázatmodellezések és a digitális renderelések. A Big Compute-megoldások infrastruktúrája speciális, többmagos processzorral működő számítógépeket tartalmazhat, amelyek a nyers számítási feladatokat végzik, a számítógépeket pedig speciális, nagy sebességű hálózati hardverek köthetik össze.

• A **Big Data** olyan adatelemzési problémákat old meg, amelyek adatmennyisége nem kezelhető egyetlen számítógéppel vagy adatbázis-kezelő rendszerrel. Ilyenek például a nagy mennyiségű webnapló- vagy egyéb üzletiintelligencia-adatok. A Big Data jellemzően inkább a lemezkapacitásra és az adatátviteli teljesítményre támaszkodik, mint a processzorteljesítményre. A fürt kezelésére és az adatok particionálására olyan speciális Big Data-eszközök is elérhetők, mint az Apache Hadoop. (Az Azure HDInsighttal és más Azure Hadoop-megoldásokkal kapcsolatban további információért lásd: [Hadoop](https://azure.microsoft.com/solutions/hadoop/).)

## <a name="compute-management-and-job-scheduling"></a>Számításkezelés és feladatütemezés
A Batch- és HPC-alkalmazások futtatásában általában egy *fürtkezelő* és egy *feladatütemező* is részt vesz, hogy segítsen a fürtözött számítási erőforrások kezelésében és a feladatokat futtató alkalmazásokhoz való lefoglalásukban. Ezek a funkciók különálló eszközökkel, illetve integrált eszközökkel vagy szolgáltatásokkal is elvégezhetők.

* **Fürtkezelő** – Számítási erőforrások (vagy számítási csomópontok) kiépítésére, kibocsátására és felügyeletére szolgál. A fürtkezelők automatizálhatják az operációsrendszer-lemezképek és alkalmazások telepítését a számítási csomópontokra, igény szerint méretezhetik a számítási erőforrásokat, valamint megfigyelhetik a csomópontok teljesítményét.
* **Feladatütemező** – Meghatározza az alkalmazások által igényelt erőforrásokat (például processzorokat vagy memóriát) és a futtatásuk feltételeit. A feladatütemezők fenntartják a feladatok várólistáját, és erőforrásokat foglalnak le a hozzájuk rendelt prioritás vagy egyéb jellemzők alapján.

A Windows-alapú és Linux-alapú fürtök fürtkezelő és feladatütemező eszközei jó áttelepíthetők az Azure-ra. A [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029), a Microsoft windowsos és linuxos HPC számítási feladatokhoz használható ingyenes számítási fürtkezelő megoldása például több lehetőséget nyújt az Azure-on való futtatáshoz. Linux-fürtöket is építhet nyílt forráskódú eszközök, például a Torque és a SLURM futtatásához. Emellett olyan kereskedelmi rács-megoldásokat is hozzáadhat az Azure-hoz, mint a [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/), az [IBM Spectrum Symphony and Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/) és az [Univa Grid Engine](http://www.univa.com/products/grid-engine).

A következő szakaszokban leírtak szerint az Azure-szolgáltatások segítségével hagyományos fürtkezelő eszközök nélkül (vagy mellett) is kezelhet számítási erőforrásokat és ütemezhet feladatokat.

## <a name="scenarios"></a>Forgatókönyvek
Itt három általános forgatókönyv látható a Big Compute számítási feladatok Azure-on való futtatásáról a meglévő HPC-fürtmegoldások, az Azure-szolgáltatások vagy a kettő kombinációjának használatával. Az egyes forgatókönyvek kiválasztásának fő szempontjait is láthatja, de ezek nem tekinthetők teljesnek. A megoldásban használható elérhető Azure-szolgáltatásokról a jelen cikk későbbi részében talál további információt.

| Forgatókönyv | Kiválasztás szempontjai |
| --- | --- | --- |
| **HPC-fürt löketszerű átvitele az Azure-ba**<br/><br/>[![Cluster burst][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> További információ:<br/>• [Burst to Azure worker instances with HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Adatlöket átvitele az Azure feldolgozópéldányokba a HPC Packkel)<br/><br/>• [Set up a hybrid compute cluster with HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) (Hibrid számítási fürt beállítása HPC Packkel)<br/><br/>• [Burst to Azure Batch with HPC Pack](https://technet.microsoft.com/library/mt612877.aspx) (Adatlöket átvitele az Azure Batch-be a HPC Packkel)<br/><br/> |• A [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) vagy más helyszíni fürtök további Azure-erőforrásokkal kombinálhatók egy hibrid megoldásban.<br/><br/>• A Big Compute számítási feladatokat kibővítheti, hogy szolgáltatott platformra (PaaS) épülő virtuálisgép-példányokon fussanak (jelenleg csak a Windows Server támogatott).<br/><br/>• Helyszíni licenckiszolgáló vagy adattár elérése választható Azure virtuális hálózattal |
| **HPC-fürt létrehozása kizárólag az Azure-ban**<br/><br/>[![Cluster in IaaS][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>További információ:<br/>• [HPC-fürtmegoldások az Azure-ban](big-compute-resources.md)<br/><br/> |• Gyorsan és egységesen telepítheti az alkalmazásait és fürteszközeit szolgáltatott infrastruktúrára (IaaS) épülő, szabványos vagy egyéni Windows- és Linux- alapú virtuális gépekre.<br/><br/>• Különböző Big Compute számítási feladatokat futtathat az igényeinek megfelelő feladatütemező megoldással.<br/><br/>• További Azure-szolgáltatások, többek között a hálózatkezelés és tárolás használata teljes felhőalapú megoldások létrehozásához. |
| **Párhuzamos alkalmazás horizontális felskálázása az Azure-ra**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>További információ:<br/>• [Az Azure Batch alapjai](batch-technical-overview.md)<br/><br/>• [Ismerkedés az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md) |• Fejlesztés az [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) segítségével különböző Big Compute számítási feladatok kiterjesztéséhez, hogy Windows- vagy Linux-alapú virtuális gépek készletein fussanak.<br/><br/>• A virtuális gépek üzembe helyezésének és automatikus méretezésének, a feladatütemezés, a vészhelyreállítás, az adatmozgatás, a függőségkezelés és az alkalmazások üzembe helyezésének kezelése Azure-platformmal. |

## <a name="azure-services-for-big-compute"></a>Azure-szolgáltatások a Big Compute-feladatokhoz
Ebben a cikkben további információt talál a Big Compute-megoldások és -munkafolyamatok létrehozásához kombinálható számítási, adatkezelési, hálózatkezelési és kapcsolódó szolgáltatásokról. Az Azure-szolgáltatásokkal kapcsolatban részletesebb útmutatást az Azure-szolgáltatások [dokumentációjában](https://azure.microsoft.com/documentation/) talál. A jelen cikk korábbi részében szereplő [forgatókönyvek](#scenarios) ezen szolgáltatások használatának csak néhány módját mutatják be.

> [!NOTE]
> Az Azure rendszeresen vezet be új szolgáltatásokat, amelyek hasznosak lehetnek az Ön forgatókönyvéhez. Ha kérdése van, lépjen kapcsolatba egy [Azure-partnerrel](https://pinpoint.microsoft.com/en-US/search?keyword=azure), vagy küldjön e-mailt *bigcompute@microsoft.com* címre.
> 
> 

### <a name="compute-services"></a>Számítási szolgáltatások
Az Azure számítási szolgáltatások a Big Compute-megoldások fő elemei, és a különböző számítási szolgáltatások különböző forgatókönyvekhez nyújtanak előnyöket. Alapszinten ezek a szolgáltatások különböző módokat nyújtanak, amelyekkel az alkalmazások az Azure által a Windows Server Hyper-V technológiával biztosított virtuálisgép-alapú számítási példányokon futhatnak. Ezek a példányok szabványos és egyéni Linux és Windows operációs rendszereket és eszközöket futtathatnak. Az Azure különböző [példányméreteket](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) tesz elérhetővé a processzormagok, memória, lemezkapacitás és más jellemzők különböző konfigurációival. Az igényei alapján a példányokat több ezer magra is méretezheti, majd csökkentheti a méretüket, amikor kevesebb erőforrásra van szüksége.

> [!NOTE]
> Az Azure [számításigényes példányai, például a H-sorozat](../virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával növelheti a HPC számítási feladatainak teljesítményét és méretezhetőségét. Ezek a példányok támogatják a párhuzamos MPI-alkalmazásokat is, amelyekhez alacsony késésű és nagy átviteli kapacitású alkalmazáshálózat szükséges. Emellett elérhetőek az [N-sorozat](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/) NVIDIA GPU-kkal szerelt virtuális gépei, amelyek kiterjesztik az Azure-ban lehetséges számítási és vizualizációs forgatókönyvek tárházát.  
> 
> 

| Szolgáltatás | Leírás |
| --- | --- |
| **[Virtual machines (Virtuális gépek)](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Szolgáltatott infrastruktúra (IaaS) biztosítása Microsoft Hyper-V technológiával<br/><br/>• Lehetővé teszi, hogy rugalmasan építsen ki és kezeljen állandó felhőalapú számítógépeket az [Azure Marketplace](https://azure.microsoft.com/marketplace/)-ről származó standard Windows Server- vagy Linux-rendszerképekről, illetve az Ön által megadott rendszerképekről és adatlemezekről.<br/><br/>• [Virtuálisgép-méretezési készletekként](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) telepíthető és kezelhető nagyméretű szolgáltatások létrehozásához azonos virtuális gépekből, a kapacitás méretének automatikus növelésével vagy csökkentésével<br/><br/>• Helyszíni számításifürt-eszközök és -alkalmazások futtatása teljes mértékben a felhőn<br/><br/> |
| **[Felhőszolgáltatások](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• A Big Compute-alkalmazásokat feldolgozói szerepkörpéldányokban futtathatja, amelyek a Windows Server valamelyik verzióját futtató, teljes mértékben az Azure által kezelt virtuális gépek<br/><br/>• Méretezhető, megbízható alkalmazásokat engedélyezhet alacsony adminisztrációs terheléssel, szolgáltatott platform (PaaS) modellben<br/><br/>• További eszközöket vagy fejlesztést igényelhet a helyi-HPC fürtmegoldásokkal való integráláshoz |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Nagy méretű párhuzamos és kötegelt számítási feladatokat futtat egy teljes mértékben felügyelt szolgáltatásban<br/><br/>• Biztosítja a virtuális gépek felügyelt készletének feladatütemezését és automatikus méretezését<br/><br/>• Lehetővé teszi, hogy a fejlesztők szolgáltatásként építsék fel és futtassák az alkalmazásokat, vagy a meglévő alkalmazásokat a felhőben tegyék elérhetővé<br/> |

### <a name="storage-services"></a>Tárolási szolgáltatások
A Big Compute-megoldások általában bemeneti adatok készleteit dolgozzák fel, adatokat hoznak létre az eredményként. A Big Compute-megoldásokban például a következő Azure tárolási szolgáltatások használhatók:

* [Blob, Table és Queue Storage](https://azure.microsoft.com/documentation/services/storage/) – Nagy mennyiségű strukturálatlan adat, NoSQL-adat, valamint munkafolyamat- és kommunikációs üzenetek kezelésére szolgál. A Blob Storage például használható nagy mennyiségű műszaki adatkészletekhez, vagy az alkalmazás által feldolgozott bemeneti képekhez vagy médiafájlokhoz. A megoldásokban aszinkron kommunikációt is biztosíthat üzenetsorok használatával. Lásd: [A Microsoft Azure Storage bemutatása](../storage/storage-introduction.md).
* [Azure File Storage](https://azure.microsoft.com/services/storage/files/) – Általános fájlokat és adatokat oszt meg az Azure-ban a szabványos SMB protokollal, ami bizonyos HPC-fürtszolgáltatásokhoz szükséges.
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) – Nagy kapacitású Apache Hadoop elosztott fájlrendszert biztosít a felhőhöz, amely hasznos a kötegelt, valós idejű és interaktív elemzéshez.

### <a name="data-and-analysis-services"></a>Adat- és elemzési szolgáltatások
Egyes Big Compute-forgatókönyvekben nagy méretű adatfolyamok szerepelnek, vagy olyan adatokat hoznak létre, amelyek további feldolgozást vagy elemzést igényelnek. Az Azure több adat- és elemzési szolgáltatást nyújt, például a következőket:

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) – Adatvezérelt munkafolyamatokat (folyamatokat) hoz létre, amelyek adatokat egyesítenek, összesítenek és alakítanak át helyszíni, felhőalapú és internetes adattárakból.
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) – A Microsoft SQL Server relációsadatbázis-kezelő rendszer legfontosabb funkcióit nyújtja egy felügyelt szolgáltatásban.
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) – Windows Server- vagy Linux-alapú Apache Hadoop-fürtöket helyez üzembe és épít ki a felhőben a big data kezeléséhez, elemzéséhez, illetve a jelentéskészítéshez.
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) – A segítségével teljes mértékben felügyelt szolgáltatásban hozhatja létre, tesztelheti, működtetheti és felügyelheti a prediktív elemzési megoldásokat.

### <a name="additional-services"></a>További szolgáltatások
A Big Compute-megoldása más Azure-szolgáltatásokat is igényelhet ahhoz, hogy a helyszínen vagy más környezetekben csatlakozzon az erőforrásokhoz. Példák erre vonatkozóan:

* [Virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) – Logikailag elkülönített szakaszt hoz létre az Azure-ban, így az Azure-erőforrások egymáshoz vagy a helyszíni adatközponthoz csatlakozhatnak. A létesítmények közötti virtuális hálózattal a Big Compute-alkalmazások elérhetik a helyszíni adatokat, az Active Directory-szolgáltatásokat és a licenckiszolgálókat.
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) – Magánkapcsolatot hoz létre a Microsoft-adatközpontok és olyan infrastruktúrák között, amelyek helyszíniek vagy közös környezetben találhatók. Az ExpressRoute biztonságosabb, megbízhatóbb, gyorsabb és kevesebb késéssel jár, mint az interneten keresztüli általános kapcsolatok.
* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) – Több mechanizmust nyújt az alkalmazásoknak a kommunikációhoz vagy az adatcseréhez, akár az Azure-on, akár másik felhőalapú platformon vagy adatközpontban találhatók.

## <a name="next-steps"></a>Következő lépések
* Műszaki útmutatás a megoldás felépítéséhez: [Műszaki forrásanyagok a Batch- és HPC-eszközökhöz](big-compute-resources.md).
* Beszélje meg az Azure-lehetőségeket az olyan partnerekkel is, mint a Cycle Computing, a Rescale és az UberCloud.
* Olvasson a [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/) és [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088) által készített Azure Big Compute-megoldásokról.
* A legújabb bejelentésekért lásd: [A Microsoft HPC és Batch csapatának blogja](http://blogs.technet.com/b/windowshpc/) és [Azure-blog](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

