---
title: "Azure Site Recovery Deployment Planner – VMware – Azure | Microsoft Docs"
description: "Ez az Azure Site Recovery Deployment Planner felhasználói útmutatója"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 431f73e1be45dec9aa0fe186cb22078f8d95588d
ms.lasthandoff: 03/28/2017


---
# <a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
Ez a cikk az Azure Site Recovery Deployment Planner felhasználói útmutatója a VMware–Azure éles környezetben való üzembe helyezéséhez.

## <a name="overview"></a>Áttekintés

A VMware virtuális gépek Site Recovery-vel történő védelmének megkezdése előtt elegendő sávszélességet kell kiosztania a napi szintű adatváltozások alapján, hogy elérje a kívánt helyreállítási időkorlátot (RPO). Ügyeljen arra, hogy üzembe helyezze a megfelelő számú helyszíni konfigurációs és folyamatkiszolgálót.

Létre kell hoznia a megfelelő típusú és számú cél Azure-tárfiókot is. Hozzon létre standard vagy prémium szintű tárfiókokat a fokozatosan növekvő használat során megnövekedett éles kiszolgálók miatt. A tárolási típust virtuális gépenként válassza ki, a számítási feladatok jellemzői (például olvasási és írási, bemeneti és kimeneti műveletek másodpercenként [IOPS], vagy adatváltozás) és Site Recovery-korlátok alapján.

A Site Recovery Deployment Planner nyilvános előzetes verziója egy parancssori eszköz, amely jelenleg csak a VMware–Azure forgatókönyvhöz érhető el. Az eszközzel (termelési hatás nélkül) távolról készítheti el Vmware virtuális gépeit, hogy megismerhesse a sávszélességet és az Azure Storage követelményeit a sikeres replikálás és feladatátvételi teszt érdekében. Az eszközt helyszíni Site Recovery összetevők telepítése nélkül is futtathatja. Az elért átviteli sebesség pontos meghatározása érdekében azonban ajánlott a tervező futtatása egy olyan Windows Serveren, amely teljesíti annak a Site Recovery konfigurációs kiszolgálónak a minimális követelményeit, amelyet végül telepítenie kell az éles környezetben való üzembe helyezés egyik első lépéseként.

Az eszköz a következő részleteket biztosítja:

**Kompatibilitási felmérés**

* A virtuális gép jogosultságfelmérése a lemezszám, a lemezméretek, az IOPS és a forgalom alapján
* A változásreplikáláshoz szükséges becsült hálózati sávszélesség

**A hálózatisávszélesség-igény és RPO-elemzés**

* A változásreplikáláshoz szükséges becsült hálózati sávszélesség
* Átviteli sebesség a Site Recovery számára a helyszíni rendszerek és az Azure között
* A kötegelendő virtuális gépek száma a becsült sávszélesség alapján a kezdeti replikálás adott időn belüli végrehajtásához

**Azure infrastruktúra-követelmények**

* A tárolótípus (standard vagy prémium szintű tárfiók) követelménye az egyes virtuális gépekhez
* A replikáláshoz beállítandó standard és prémium szintű tárfiókok teljes száma
* Tárfiókok elnevezési javaslatai az Azure Storage útmutatója alapján
* Az összes virtuális gép tárfiókjának elhelyezése
* A feladatátvételi teszt vagy feladatátvétel előtt beállítandó Azure magok száma az előfizetésen
* Az Azure virtuális gépek javasolt mérete az egyes helyszíni virtuális gépeknél

**Helyszíni infrastruktúra-követelmények**
* A megfelelő számú helyszíni konfigurációs és folyamatkiszolgáló üzembe helyezése

>[!IMPORTANT]
>
>Mivel a használat idővel megnövekszik, az eszközben minden számítás a munkaterhelési jellemzők 30%-os növekedési tényezőjével történik, illetve a profilkészítési mérőszámok 95%-át veszi alapul (írási/olvasási IOPS, adatforgalom stb.). Mindkét elem (a növekedési tényező és a százalékérték is) konfigurálható. További információkat a növekedési tényezőről „A növekedési tényezővel kapcsolatos szempontok” szakaszban találhat. További információkat a százalékértékről „A számításhoz használt százalékérték” szakaszban találhat.
>

## <a name="requirements"></a>Követelmények
Az eszköz két fő fázisból áll: a profil- és jelentéskészítésből. Van egy harmadik lehetőség, amely csak az átviteli sebességet számítja ki. Az alábbi táblázatban láthatók annak a kiszolgálónak a követelményei, ahonnan a profilkészítés/átviteli sebesség mérését kezdeményezi:

| Kiszolgálókövetelmények | Leírás|
|---|---|
|Profilkészítés és az átviteli sebesség mérése| <ul><li>Operációs rendszer: Microsoft Windows Server 2012 R2<br>(ideális esetben legalább a [konfigurációs kiszolgáló javasolt méretével egyezik](https://aka.ms/asr-v2a-on-prem-components))</li><li>Gépkonfiguráció: 8 vCPU, 16 GB RAM, 300 GB HDD</li><li>[Microsoft .NET-keretrendszer 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)</li><li>[A Visual Studio 2012 szoftverhez készült Microsoft Visual C++ terjeszthető változata](https://aka.ms/vcplusplus-redistributable)</li><li>Internet-hozzáférés az Azure-szolgáltatáshoz erről a kiszolgálóról</li><li>Azure Storage-fiók</li><li>Rendszergazdai hozzáférés a kiszolgálón</li><li>Minimális szabad lemezterület 100 GB (feltéve, hogy 1000 virtuális gépen átlagosan gépenként három lemezről 30 napig készít profilokat)</li><li>A VMware vCenter statisztikaszint-beállításait a 2. vagy magasabb szintre kell állítani</li></ul>|
| Jelentéskészítés | 2013-as vagy újabb Microsoft Excellel rendelkező Windows PC vagy Windows Server |
| Felhasználói engedélyek | Csak olvasási jogosultság a felhasználói fióknak a VMware vCenter/VMware vSphere ESXi kiszolgáló profilkészítés közben való hozzáféréséhez |

> [!NOTE]
>
>Az eszköz csak a VMDK- és RDM-lemezzel rendelkező virtuális gépeknek tud profilt készíteni. Nem képes az iSCSI- vagy NFS-lemezzel rendelkező virtuális gépek profilkészítésére. A Site Recovery támogatja az iSCSI- és NFS-lemezeket a VMware-kiszolgálókon, azonban az üzembe helyezési tervező nem a vendéggépen belül fut, és csak a vCenter teljesítményszámlálók használatával készít profilokat, így az eszköz számára nem láthatók ezek a lemeztípusok.
>

## <a name="download-and-extract-the-public-preview"></a>Nyilvános előzetes verzió letöltése és kibontása
1. Töltse le a [Site Recovery Deployment Planner legújabb nyilvános előzetes verzióját](https://aka.ms/asr-deployment-planner).  
Az eszköz .zip-mappába van csomagolva. Az eszköz jelenlegi verziója csak a VMware–Azure forgatókönyvet támogatja.

2. Másolja a zip-fájlt a Windows Serveren belül oda, ahonnan futtatni kívánja az eszközt.  
Az eszközt futtathatja Windows Server 2012 R2-ről, ha a kiszolgáló hálózati hozzáférésével képes csatlakozni a ahhoz a vCenter kiszolgálóhoz/vSphere ESXi-gazdagéphez, amely a profilkészítéshez használt virtuális gépeket üzemelteti. Ajánlott azonban egy olyan kiszolgálón futtatni az eszközt, amelynek a hardverkonfigurációja megfelel a [konfigurációs kiszolgáló méretezési útmutatójának](https://aka.ms/asr-v2a-on-prem-components). Ha már üzembe helyezte a Site Recovery helyszíni összetevőit, futtassa az eszközt a konfigurációs kiszolgálóról.

 A konfigurációs kiszolgálóval megegyező (beépített folyamatkiszolgálóval rendelkező) hardverkonfiguráció ajánlott azon a kiszolgálón, ahol futtatja az eszközt. Az ilyen konfiguráció biztosítja, hogy az eszköz által jelentett elért átviteli sebesség megegyezik a Site Recovery által replikáció közben elért tényleges átviteli sebességgel. Az átviteli sebesség kiszámítása a kiszolgálón elérhető hálózati sávszélességtől és a kiszolgáló hardverkonfigurációjától (processzor, tárterület stb.) függ. Ha az eszközt bármely másik kiszolgálóról futtatja, az arról a kiszolgálóról a Microsoft Azure irányába elérhető átviteli sebesség lesz kiszámítva. Mivel a kiszolgáló hardverkonfigurációja eltérhet a konfigurációs kiszolgálóétól, lehetséges, hogy az eszköz által jelentett elért átviteli sebesség nem lesz pontos.

3. Csomagolja ki a .zip mappát.  
A mappa több fájlt és almappát tartalmaz. Az ASRDeploymentPlanner.exe futtatható fájl a szülőmappában található.

    Példa:  
    Másolja a .zip fájlt az E:\ meghajtóra, és csomagolja ki.
   E:\ASR Deployment Planner-Preview_v1.1.zip

    E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>Funkciók
A parancssori eszköz (ASRDeploymentPlanner.exe) a következő három mód bármelyikében futtatható:

1. Profilkészítés  
2. Jelentéskészítés
3. Átviteli sebesség lekérdezése

Először futtassa profilkészítési módban az eszközt a virtuális gép adatváltozásának és IOPS-értékének összegyűjtéséhez. Ezután futtassa az eszközt a jelentés létrehozásához, hogy megtudhassa a hálózati sávszélességet és a tárolási követelményeket.

## <a name="profiling"></a>Profilkészítés
Profilkészítési módban a Deployment Planner eszköz a vCenter-kiszolgálóhoz vagy a vSphere ESXi-gazdagéphez csatlakozik, hogy összegyűjtse a virtuális gép teljesítményadatait.

* A profilkészítés nincs hatással az éles virtuális gépek teljesítményére, mivel nem jön létre közvetlen kapcsolat az éles virtuális gépekkel. A rendszer minden teljesítményadatot összegyűjt a vCenter-kiszolgálóról / vSphere ESXi-gazdagépről.
* Az eszköz 15 percenként lekérdezi a vCenter-kiszolgálót vagy a vSphere ESXi-gazdagépet, hogy a profilkészítés biztosan csak elhanyagolható hatással legyen a kiszolgálóra. A lekérdezési időköz azonban nem veszélyezteti a profilkészítés pontosságát, mivel az eszköz minden percben tárolja a teljesítményszámláló adatait.

### <a name="create-a-list-of-vms-to-profile"></a>A profillal ellátni kívánt virtuális gépek listájának létrehozása
Először létre kell hoznia a profillal ellátni kívánt virtuális gépek listáját. A következő eljárás VMware vSphere PowerCLI-parancsai használatával a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen található összes virtuális gép nevét elérheti. Másik lehetőségként egy fájlba listázhatja azon virtuális gépek rövid nevét vagy IP-címét, amelyeket manuálisan szeretne profillal ellátni.

1. Jelentkezzen be arra a virtuális gépre, amelyen a VMware vSphere PowerCLI telepítve van.
2. Nyissa meg a VMware vSphere PowerCLI-konzolt.
3. Ellenőrizze, hogy a végrehajtási szabályzat engedélyezve van-e a szkript esetében. Ha a szabályzat le van tiltva, indítsa el a VMware vSphere PowerCLI-konzolt rendszergazdai módban, és futtassa a következő parancsot az engedélyezéséhez:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Futtassa az alábbi két parancsot a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen található összes virtuális gép nevének lekéréséhez és a nevek egy szöveges dokumentumba (.txt) mentéséhez.
Cserélje le a &lsaquo;server name&rsaquo; (kiszolgáló neve), a &lsaquo;user name&rsaquo; (felhasználónév), a &lsaquo;password&rsaquo; (jelszó), az &lsaquo;outputfile.txt&rsaquo; (kimenetifájl.txt) paramétereket saját értékeire.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

5. Nyissa meg a kimeneti fájlt a Jegyzettömbben, és másolja egy másik fájlba (pl. ProfileVMList.txt) minden olyan virtuális gép nevét, amelyről profilt kíván készíteni. Minden virtuális gép nevét külön sorba írja. A rendszer ezt a fájlt használja majd bemenetként a parancssori eszköz *-VMListFile* paraméteréhez.

    ![Virtuálisgép-nevek listája a Deployment Planner eszközben](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>Profilkészítés indítása
Ha megvan azon virtuális gépek listája, amelyekről profilt szeretne készíteni, futtathatja az eszközt profilkészítési módban. Az alábbi lista az eszköz profilkészítési módjában használható kötelező és választható paramétereket sorolja fel.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Paraméter neve | Leírás |
|---|---|
| -Művelet | StartProfiling |
| -Kiszolgáló | Azon vCenter-kiszolgáló vagy vSphere ESXi-gazdagép teljes tartományneve vagy IP-címe, amelynek virtuális gépeiről profilt szeretne készíteni.|
| -Felhasználó | A vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez való csatlakozáshoz használt felhasználónév. A felhasználónak legalább olvasási hozzáféréssel kell rendelkeznie.|
| -VMListFile |    Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy az ESXi-gazdagépen szereplő nevekkel.<br>A „VMList.txt” fájl például az alábbi virtuális gépeket tartalmazza:<ul><li>virtual_machine_A</li><li>10.150.29.110</li><li>virtual_machine_B</li><ul> |
| -NoOfDaysToProfile | A napok száma, ameddig a profilkészítést futtatni szeretné. Javasoljuk, hogy legalább 15 napig futtassa a profilkészítést. Ez biztosítja számítási feladatok mintájának megfigyelését a környezetben a meghatározott időtartamon belül, amely alapján pontos javaslat adható |
| -Directory | (Nem kötelező) Az univerzális elnevezési konvenciónak (UNC) megfelelő elérési út vagy azon helyi könyvtár elérési útja, ahol a profilkészítés során létrehozott adatokat tárolni kívánja. Ha nem adja meg könyvtár nevét, a rendszer az aktuális elérési úton található „ProfiledData” könyvtárat használja alapértelmezett könyvtárként. |
| -Password | (Nem kötelező) A vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg most, a rendszer a parancs végrehajtásakor rákérdez.|
| -StorageAccountName | (Nem kötelező) A helyszínről az Azure-ba történő adatreplikáció során elérhető átviteli sebesség azonosításához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat az átviteli sebesség kiszámításához.|
| -StorageAccountKey | (Nem kötelező) A tárfiók eléréséhez használt tárfiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > *Tárfiók neve* > > Beállítások > Hozzáférési kulcsok > 1. kulcs (vagy klasszikus tárfiók esetén az Elsődleges elérési kulcs) elemre. |

Javasoljuk, hogy legalább 15–30 napig folytassa a virtuális gépek profiljának készítését. A profilkészítés időtartama alatt az ASRDeploymentPlanner.exe alkalmazás folyamatosan fut. A profilkészítés időtartama napokban adható meg az eszközben. Ha csak gyorsan tesztelni kívánja az eszközt, és néhány óráig vagy percig szeretné futtatni a profilkészítést, akkor a nyilvános előzetes verzióban napban kell megadnia az időtartamot. Ha például 30 percig szeretné futtatni a profilkészítést, a bemenet legyen 30/(60*24), azaz 0,021 nap. A minimális profilkészítési idő 30 perc.

A profilkészítés során lehetősége van átadni egy tárfióknevet és -kulcsot, amivel megtudhatja, hogy a Site Recovery mekkora átviteli sebességet érhet el a konfigurációs kiszolgálóról vagy a folyamatkiszolgálóról az Azure-ba történő replikáció során. Ha nem ad át tárfióknevet és -kulcsot a profilkészítés során, az eszköz nem számítja ki az elérhető átviteli sebességet.

Az eszköz több példánya is futtatható egyszerre különböző virtuálisgép-csoportokon. Győződjön meg arról, hogy a virtuális gépek neve nem ismétlődik egyik profilkészítési csoportban sem. Ha például profilt készített tíz virtuális gépről (VM1–VM10), és néhány nap elteltével további öt virtuális gépről szeretne profilt készíteni (VM11–VM15), akkor a második csoporton (VM11–VM15) egy másik parancssori konzolról futtathatja az eszközt. Győződjön meg arról, hogy a második virtuálisgép-csoport nem tartalmaz olyan virtuálisgép-nevet, amely szerepel az első profilkészítési példányban, vagy a második futtatás esetében használjon eltérő kimeneti könyvtárat. Ha az eszköz két példánya ugyanazokról a virtuális gépekről készít profilt, és ugyanazt a kimeneti könyvtárat is használják, a létrehozott jelentés helytelen lesz.

A rendszer egyszer, a profilkészítési művelet elején rögzíti virtuálisgép-konfigurációkat, és egy VMDetailList.xml nevű fájlban tárolja őket. Ezeket az adatokat azután a jelentés létrehozásakor használja. Ha a profilkészítés során a virtuális gépek konfigurációjának bármely része módosul (például nő a magok, a lemezek vagy a hálózati adapterek száma), azt a rendszer nem rögzíti. Ha a profilkészítés során módosul az egyik virtuális gép konfigurációja, amelyről profilt készített, akkor a nyilvános előzetes verzióban a következő megkerülő megoldással kérheti le a virtuális gép legfrissebb adatait a jelentés készítésekor:

* Készítsen biztonsági másolatot a VMdetailList.xml fájlról, és törölje a fájlt a jelenlegi helyéről.
* Adja meg a -User és -Password argumentumokat a jelentés létrehozásakor.

A profilkészítési parancs számos fájlt létrehoz a profilkészítési könyvtárban. Ezeket ne törölje, mert az hatással lenne a jelentésre.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>1. példa: Profilkészítés virtuális gépről 30 napon keresztül, valamint a helyszín és az Azure közötti átviteli sebesség meghatározása
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>2. példa: Profilkészítés virtuális gépről 15 napon keresztül

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>3. példa: Profilkészítés virtuális gépekről 1 órán keresztül az eszköz gyors teszteléséhez
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* Ha a kiszolgáló, amelyen az eszköz fut, újraindul vagy összeomlik, vagy ha bezárja az eszközt a Ctrl + C billentyűkombinációval, a rendszer megőrzi a profilkészítés során létrehozott adatokat. Előfordulhat azonban, hogy az utolsó 15 percnyi profilkészítés adatai elvesznek. Ebben az esetben a kiszolgáló újraindítása után ismét futtassa az eszközt profilkészítési módban.
>* Ha megadja a tárfióknevet és -kulcsot, az eszköz a profilkészítés utolsó lépéseként megméri az átviteli sebességet. Ha bezárja az eszközt, mielőtt a profilkészítés befejeződött volna, a rendszer nem számítja ki az átviteli sebességet. Az átviteli sebesség a jelentés létrehozása előtti lekérdezéséhez futtassa a GetThroughput műveletet a parancssori konzolból. Ellenkező esetben a létrehozott jelentés nem tartalmazza majd az átviteli sebességgel kapcsolatos információkat.


## <a name="generate-a-report"></a>Jelentés létrehozása
Az eszköz egy makróbarát Microsoft Excel-fájlt (XLSM-fájlt) hoz létre a jelentés kimeneteként, amely összefoglalja az üzembehelyezési javaslatokat. A jelentés neve DeploymentPlannerReport_<*egyéni numerikus azonosító*>.xlsm, és a megadott könyvtárban lesz elérhető.

A profilkészítés befejezése után futtathatja az eszközt jelentéskészítési módban. A következő táblázat a jelentéskészítési módban futtatandó kötelező és nem kötelező eszközparaméterek listáját tartalmazza.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Paraméter neve | Leírás |
|-|-|
| -Művelet | Jelentés készítése |
| -Kiszolgáló |  Annak a vCenter- vagy vSphere-kiszolgálónak a teljes tartományneve vagy IP-címe, ahol azon virtuális gépek találhatók, amelyekről profilt készített, és amelyekről most jelentést szeretne készíteni. (Használja a profilkészítéskor használt nevet vagy IP-címet.) Ügyeljen arra, hogy ha vCenter-kiszolgálót használt a profilkészítés során, akkor nem használhat vSphere-kiszolgálót a jelentéskészítéshez, és fordítva.|
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt készített, és amelyekről jelentést szeretne készíteni. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen szereplő nevekkel, és egyezniük kell a profilkészítés során használt nevekkel.|
| -Directory | (Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha a név nincs megadva, a rendszer a „ProfiledData” könyvtárat használja. |
| -GoalToCompleteIR | (Nem kötelező) Ennyi óra alatt kell befejeződnie azon virtuális gépek kezdeti replikációjának, amelyekről profilt kíván készíteni. Az elkészített jelentés tartalmazza azon virtuális gépek számát, amelyeknek a kezdeti replikációja befejezhető a megadott időtartam alatt. Az alapértelmezett érték 72 óra. |
| -Felhasználó | (Nem kötelező) A vCenter- vagy vSphere-kiszolgálóhoz való csatlakozáshoz használni kívánt felhasználónév. A rendszer a név használatával kéri le a virtuális gépek legutóbbi konfigurációs adatait a jelentéshez (pl. a lemezek, magok, hálózati adapterek száma stb.). Ha a név nincs megadva, a rendszer a profilkészítés indításakor összegyűjtött konfigurációs adatokat használja fel. |
| -Password | (Nem kötelező) A vCenter-kiszolgálóhoz vagy vSphere ESXi-gazdagéphez való csatlakozáshoz használt jelszó. Ha nem adja meg a jelszót paraméterként, a rendszer később, a parancs végrehajtásakor rákérdez. |
| -DesiredRPO | (Nem kötelező) A kívánt helyreállítási időkorlát, percben megadva. Az alapértelmezett beállítás 15 perc.|
| -Bandwidth | Sávszélesség (Mbps). A rendszer ezen paraméter alapján számítja ki az elérhető helyreállítási időkorlátot a megadott sávszélességhez. |
| -StartDate | (Nem kötelező) Kezdő dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. A *StartDate* és az *EndDate* paraméter megadása kötelező. Ha a StartDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -EndDate | (Nem kötelező) Záró dátum és idő HH-NN-ÉÉÉÉ:ÓÓ:PP (24 órás) formátumban megadva. Az *EndDate* és a *StartDate* paraméter megadása kötelező. Ha az EndDate meg van adva, a rendszer a StartDate és az EndDate paraméter közötti időszakban összegyűjtött, profilkészítéshez használt adatokról állít elő jelentést. |
| -GrowthFactor | (Nem kötelező) A növekedési tényező százalékértékként megadva. Az alapértelmezett érték 30%. |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>1. példa: Jelentés készítése az alapértelmezett értékekkel, ha a profilkészítés során létrehozott adatok a helyi meghajtón találhatóak
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>2. példa: Jelentés készítése, ha a profilkészítés során létrehozott adatok távoli kiszolgálón találhatóak
A felhasználónak olvasási/írási hozzáféréssel kell rendelkeznie a távoli könyvtárhoz.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>3. példa: Jelentés készítése megadott sávszélességgel és a kezdeti replikáció adott határidejű befejezésére vonatkozó céllal
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>4. példa: Jelentés készítése 5%-os növekedési tényezővel az alapértelmezett 30% helyett
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>5. példa: Jelentés létrehozása a profilkészítés során használt adatok egy részéből
Tegyük fel, hogy van 30 napnyi adata, amelyet a profilkészítés során hozott létre, de csak 20 napról szeretne jelentést készíteni.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>6. példa: Jelentés készítése 5 perces helyreállítási időkorláttal
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

## <a name="percentile-value-used-for-the-calculation"></a>A számításhoz használt százalékérték
**Alapértelmezés szerint a profilkészítés során összegyűjtött teljesítménymetrikák milyen százalékos arányát használja az eszköz jelentések készítésekor?**

Az eszköz alapértelmezett értéke az írási/olvasási IOPS, az írási IOPS és az adatváltozás esetén a 95. százalékérték az összes virtuális gép profiljának elkészítése során. Ez a metrika biztosítja, hogy a virtuális gépek által az ideiglenes események miatt esetlegesen észlelt 100. százalékértékes kiugrást a rendszer nem használja fel a cél tárfiók és a forrássávszélesség követelményeinek meghatározásakor. Az ideiglenes esemény lehet például egy naponta egyszer futtatott biztonsági mentési feladat, rendszeres időközönként végzett adatbázis-indexelés, elemzésijelentés-készítési tevékenység vagy bármely hasonló, rövid ideig tartó, időpontalapú esemény.

A 95. százalékérték használata valós képet ad a számítási feladatok valódi jellemzőiről, és a lehető legjobb teljesítményt biztosítja, miközben a számítási feladatok futnak az Azure-on. Ezt az értéket valószínűleg nem kell módosítania. Ha úgy dönt, hogy módosítja (például a 90. százalékértékre), akkor az alapértelmezett mappában található *ASRDeploymentPlanner.exe.config* konfigurációs fájl frissítésével és mentésével létrehozhat egy új jelentést a meglévő, profilkészítés során létrehozott adatokról.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>A növekedési tényezővel kapcsolatos szempontok
**Miért kell figyelembe vennem a növekedési tényezővel kapcsolatos szempontokat a környezetek megtervezésekor?**

Rendkívül fontos figyelembe venni a növekedést a számítási feladatok jellemzőiben, feltételezve a használat lehetséges növekedését. Ha beállítja a védelmet, és módosulnak a számítási feladatok jellemzői, nincs lehetőség arra, hogy egy másik védett tárfiókra váltson a védelem letiltása és ismételt engedélyezése nélkül.

Tegyük fel például, hogy a virtuális gép jelenleg megfelel egy standard szintű tárreplikációs fiókhoz. A következő néhány hónapban számos változás következhet be:

* Nő azoknak a felhasználóknak a száma, akik az alkalmazást a virtuális gépen futtatják.
* Az így létrejövő megnövekedett adatforgalom miatt a virtuális gépnek prémium szintű tárolóra van szüksége, hogy a Site Recovery replikációja tartani tudja a lépést.
* Ennek következtében le kell tiltania, majd újra engedélyeznie kell a védelmet egy prémium szintű tárfiókon.

Erősen ajánlott, hogy az üzembe helyezés tervezése során számoljon a növekedéssel, amelynek az alapértelmezett értéke 30 százalék. Az alkalmazása használati mintáját és növekedési előrejelzéseit Ön ismeri a legjobban, és a jelentés elkészítése közben igény szerint módosíthatja ezt a számot. Továbbá több jelentést is készíthet különböző növekedési tényezőkkel, de ugyanazokról az adatokról. Így eldöntheti, hogy melyik, a céloldali tárolóra, valamint a forrásoldali sávszélességre vonatkozó javaslatok felelnek meg leginkább Önnek.

Az elkészített Microsoft Excel-jelentés a következő információkat tartalmazza:

* [Input](site-recovery-deployment-planner.md#input) (Bemenet)
* [Javaslatok](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Recommendations-Bandwidth Input](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input) (Javaslatok – Bemeneti sávszélesség)
* [VM<->Storage Placement](site-recovery-deployment-planner.md#vm-storage-placement) (Virtuálisgép<->tároló elhelyezése)
* [Compatible VMs](site-recovery-deployment-planner.md#compatible-vms) (Kompatibilis virtuális gépek)
* [Incompatible VMs](site-recovery-deployment-planner.md#incompatible-vms) (Nem kompatibilis virtuális gépek)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>Átviteli sebesség lekérdezése

Ha meg szeretné becsülni az átviteli sebességet, amelyet a Site Recovery el tud érni helyszínről az Azure-ba történő replikáció során, futtassa az eszközt GetThroughput módban. Az eszköz arról a kiszolgálóról számítja ki az átviteli sebességet, amelyen fut. Ez a kiszolgáló optimális esetben a konfigurációs kiszolgálók méretezési útmutatóján alapul. Ha már üzembe helyezte a Site Recovery infrastruktúra-összetevőit a helyszínen, futtassa az eszközt a konfigurációs kiszolgálón.

Nyisson meg egy parancssori konzolt, és keresse meg a Site Recovery üzembehelyezés-tervező eszköz mappáját. Futtassa az ASRDeploymentPlanner.exe fájlt az alábbi paraméterekkel.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Paraméter neve | Leírás |
|-|-|
| -művelet | Átviteli sebesség lekérdezése |
| -Directory | (Nem kötelező) A profilkészítés során létrehozott adatokat tároló mappa UNC vagy helyi elérési útja. Ezekre az adatokra szükség van a jelentés létrehozásához. Ha a könyvtár neve nincs megadva, a rendszer a „ProfiledData” könyvtárat használja. |
| -StorageAccountName | A helyszínről az Azure-ba történő adatreplikáció során felhasznált sávszélesség meghatározásához szükséges tárfiók neve. Az eszköz erre a tárfiókra tölti fel a tesztadatokat a felhasznált sávszélesség megállapításához. |
| -StorageAccountKey | A tárfiók eléréséhez használt tárfiókkulcs. Nyissa meg az Azure Portalt, és kattintson a Tárfiókok > <*Tárfiók neve*> > Beállítások > Hozzáférési kulcsok > 1. kulcs (vagy klasszikus tárfiók esetén az Elsődleges elérési kulcs) elemre. |
| -VMListFile | Azon virtuális gépek listáját tartalmazó fájl, amelyekről profilt szeretne készíteni a felhasznált sávszélesség kiszámításához. A fájl elérési útja lehet abszolút vagy relatív. A fájl minden sorában egy virtuális gép nevének vagy IP-címének kell állnia. A fájlban megadott virtuálisgép-neveknek meg kell egyezniük a vCenter-kiszolgálón vagy a vSphere ESXi-gazdagépen szereplő névvel.<br>A „VMList.txt” fájl például az alábbi virtuális gépeket tartalmazza:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|

Az eszköz több 64 MB-os „asrchdfile<#>.vhd” nevű fájlt (a „#” a fájlok számát jelöli) is létrehoz a megadott könyvtárban. Az eszköz feltölti a fájlokat a tárfiókba az átviteli sebesség megállapításához. Az átviteli sebesség mérése után az eszköz törli az összes fájlt a tárfiókból és a helyi kiszolgálóról. Ha az eszköz bármilyen oknál fogva leáll az átviteli sebesség kiszámítása közben, akkor nem törli a fájlokat a tárolóból és a helyi kiszolgálóról. Ezeket manuálisan kell törölnie.

Az eszköz egy meghatározott időpontban méri az átviteli sebességet, és a kapott érték a Site Recovery által a replikáció során elérhető maximális átviteli sebesség, feltéve, hogy a többi tényező sem változik. Ha például valamelyik alkalmazás több sávszélességet kezd el felhasználni ugyanazon a hálózaton belül, a tényleges átviteli sebesség változhat a replikáció során. Ha a GetThroughput parancsot egy konfigurációs kiszolgálóról futtatja, az eszköz nem veszi figyelembe a védett virtuális gépeket és a folyamatban lévő replikációkat. Az átviteli sebesség mérésének eredménye eltérő lesz, ha a védett virtuális gépek adatváltozása magas a GetThroughput művelet végrehajtásakor. Ajánlott az eszközt különböző időpontokban futtatni a profilkészítés során, hogy átláthassa, milyen átviteli sebességi szintek érhetők el a különféle időpontokban. Az eszköz a legutóbb mért átviteli sebességet jeleníti meg a jelentésben.

### <a name="example"></a>Példa
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Olyan kiszolgálón futtassa az eszközt, amelynek a tároló- és processzorjellemzői megegyeznek a konfigurációs kiszolgálóéval
>
> A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet állítsa be. Ha a megfelelő sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:
>
>  1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná a Site Recovery átviteli sebességét.
>
>  2. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében a Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e.
>
>  3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).
>
>  4. Módosítsa a Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="recommendations-with-desired-rpo-as-input"></a>Javaslatok a kívánt helyreállítási időkorlát (RPO) bemenetként való megadásával

### <a name="profiled-data"></a>Profilkészítés során létrehozott adatok

![A profilkészítés során létrehozott adatok nézete a Deployment Planner eszközben](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Profiled data period** (Profilkészítési adatok létrehozásának időtartama): A profilkészítés futtatásának időtartama. Az eszköz alapértelmezés szerint a profilkészítés során létrehozott minden adatot számításba vesz, kivéve, ha egy adott időszakról készít jelentést, és megadja a StartDate és EndDate beállításokat a jelentéskészítés során.

**Server Name** (Kiszolgáló neve): Azon VMware vCenter vagy ESXi-gazdagép neve vagy IP-címe, amelynek a virtuális gépeiről a jelentés készül.

**Desired RPO** (Kívánt RPO): Az üzembe helyezés kívánt helyreállítási időkorlátja. Alapértelmezés szerint a szükséges hálózati sávszélesség kiszámítása 15, 30 és 60 perces RPO értékkel történik. A kiválasztástól függően az eszköz frissíti az érintett értékeket a munkalapon. Ha használta a *DesiredRPOinMin* paramétert a jelentés elkészítése közben, akkor az az érték jelenik meg a Kívánt helyreállítási időkorlát eredményei között.

### <a name="profiling-overview"></a>Profilkészítés áttekintése

![A profilkészítés eredményei a Deployment Planner eszközben](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total Profiled Virtual Machines** (Minden virtuális gép, amelyről profil készült): Azon virtuális gépek teljes száma, amelyekről profil készült. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer ezeket figyelmen kívül hagyja a jelentéskészítés során, és nem számítja bele őket a profilkészítésben részt vevő virtuális gépek teljes számába.

**Compatible Virtual Machines** (Kompatibilis virtuális gépek): Azon virtuális gépek száma, amelyek az Azure-ban védhetők a Site Recovery használatával. Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta a szükséges hálózati sávszélességet, a tárfiókok, az Azure-magok, valamint a konfigurációs és további folyamatkiszolgálók számát. Az egyes kompatibilis virtuális gépek részletei elérhetők a „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban.

**Incompatible Virtual Machines** (Nem kompatibilis virtuális gépek): Azon virtuális gépek száma, amelyekről profil készült, és amelyek nem védhetők Site Recoveryvel. Az inkompatibilitás okait a „Nem kompatibilis virtuális gépek” című szakaszban olvashatja. Ha a VMListFile olyan virtuális gépek nevét is tartalmazza, amelyekről nem készült profil, a rendszer nem számítja bele őket a nem kompatibilis virtuális gépek számába. Az ilyen virtuális gépek az „Incompatible VMs” (Nem kompatibilis virtuális gépek) szakasz végén, „Data not found” (Nem található adat) megjelöléssel láthatók.

**Desired RPO**(Kívánt RPO): A kívánt helyreállítási időkorlát, percben megadva. Az eszköz három helyreállítási időkorlát értékről készít jelentést: 15 (alapértelmezett érték), 30 és 60 perc. A jelentésben szereplő ajánlott sávszélesség attól függően változik, hogy melyik lehetőséget választja a munkalap jobb felső sarkában található Desired RPO (Kívánt RPO) legördülő listából. Ha egyéni értékű *-DesiredRPO* paraméterrel készítette el a jelentést, ez az egyéni érték jelenik meg alapértelmezett értékként a Kívánt helyreállítási időkorlát legördülő listában.

### <a name="required-network-bandwidth-mbps"></a>Szükséges hálózati sávszélesség (Mbps)

![Szükséges hálózati sávszélesség a Deployment Planner eszközben](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**To meet RPO 100 percent of the time:** (Az RPO betartása az idő 100 százalékában): Az ajánlott sávszélesség (Mbps), amelyet ki kell osztania, ha mindig garantáltan el kívánja érni a kívánt helyreállítási időkorlátot. Ekkora sávszélességet kell kijelölnie az összes kompatibilis virtuális gép stabil állapotú változásreplikációjához, ha biztosan ki kívánja zárni a helyreállítási időkorlát megszegését.

**To meet RPO 90 percent of the time** (Az RPO betartása az idő 90 százalékában): Ha a szélessávú kapcsolat díjszabása miatt vagy egyéb okból kifolyólag nincs lehetősége arra, hogy akkora sávszélességet állítson be, amellyel a kívánt helyreállítási időkorlát mindig betartható, akkor választhat egy alacsonyabb sávszélesség-beállítást, amellyel az idő 90 százalékában betarthatja a kívánt RPO-t. Az alacsonyabb sávszélesség-beállítás következményeinek áttekintéséhez a jelentés lehetőségelemzést biztosít elemzést a helyreállítási időkorlát várható megszegéseinek számáról és időtartamáról.

**Achieved Throughput:** (Elért átviteli sebesség): Az átviteli sebesség a GetThroughput parancs futtatásához használt kiszolgáló és a tárfióknak helyet adó Microsoft Azure-régió között. Ez az átvitelisebesség-érték azt a becsült szintet jelöli, amely akkor érhető el, ha a kompatibilis virtuális gépeket a Site Recoveryvel védi, feltéve, hogy a konfigurációs kiszolgáló vagy folyamatkiszolgáló tárolója és hálózati jellemzői megegyeznek azon kiszolgálóéval, amelyen az eszközt futtatta.

A replikációhoz a helyreállítási időkorlát 100 százalékos eléréséhez javasolt sávszélességet kell beállítania. Ha a sávszélesség beállítása után nem nő az eszköz által jelentett elért átviteli sebesség, tegye a következőket:

1. Ellenőrizze, hogy van-e olyan hálózati szolgáltatásminőség (QoS), amely korlátozná a Site Recovery átviteli sebességét.

2. Ellenőrizze, hogy a hálózati késés minimalizálása érdekében a Site Recovery-tároló a legközelebbi fizikai támogatott Microsoft Azure-régióban található-e.

3. Ellenőrizze a helyi tároló jellemzőit, és állapítsa meg, hogy fejleszthető-e a hardver (pl. váltás HDD-ről SSD-re).

4. Módosítsa a Site Recovery beállításait a folyamatkiszolgálón [a replikációhoz használt hálózati sávszélesség növelésével](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Ha az eszközt olyan konfigurációs kiszolgálón vagy folyamatkiszolgálón futtatja, amely már rendelkezik védett virtuális gépekkel, futtassa többször azt eszközt. Az elért átviteli sebesség eltérő lesz az aktuális adatforgalomtól függően.

A Site Recovery minden vállalati üzemelő példánya esetében az [ExpressRoute](https://aka.ms/expressroute) használata javasolt.

### <a name="required-storage-accounts"></a>Szükséges tárfiókok
A következő diagram az összes kompatibilis virtuális gép védelméhez szükséges (standard és prémium szintű) tárfiókok teljes számát mutatja. A „VM-storage placement” (Virtuálisgép-tároló elhelyezése) szakaszból megtudhatja, hogy melyik tárfiókot melyik virtuális géphez használhatja.

![Szükséges tárfiókok a Deployment Planner eszközben](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>Szükséges Azure-magok száma
Ez az eredmény az összes kompatibilis virtuális gép feladatátvétele vagy feladatátvételi tesztje előtt beállítandó magok teljes száma. Ha az előfizetés túl kevés maggal rendelkezik, a Site Recovery nem tudja létrehozni a virtuális gépeket a feladatátvételi teszt vagy a tényleges feladatátvétel alatt.

![Szükséges Azure-magok száma a Deployment Planner eszközben](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>Szükséges helyszíni infrastruktúra
Ez az adat az összes virtuális gép védelméhez szükséges konfigurálandó konfigurációs kiszolgálók és további folyamatkiszolgálók teljes száma. A [konfigurációs kiszolgáló támogatott méretére vonatkozó javaslatoktól](https://aka.ms/asr-v2a-on-prem-components) függően az eszköz további kiszolgálókat javasolhat. A javaslat a napi adatforgalmon vagy a védett virtuális gépek maximális számán alapul (virtuális gépenként átlagosan három lemezt feltételezve), ha eléri valamelyik korlátot a konfigurációs kiszolgálón vagy a további folyamatkiszolgálón. A napi összes adatforgalom és a védett lemezek teljes számának részletei az „Input” (Bemenet) szakaszban találhatók.

![Szükséges helyszíni infrastruktúra a Deployment Planner eszközben](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>Lehetőségelemzés
Ez az elemzés kiemeli, hogy hányszor szegné meg a helyreállítási időkorlátot a profilkészítés időtartama alatt, ha alacsonyabb sávszélességet állít be, amely az idő 90 százalékában biztosítaná a kívánt RPO betartását. A helyreállítási időkorlát túllépése bármelyik nap egy vagy több alkalommal is előfordulhat. Az ábra a napi RPO-csúcsértéket mutatja.
Az elemzés alapján eldöntheti, hogy az RPO-túllépések teljes száma az összes nap alatt, valamint a napi RPO-csúcsérték elfogadható-e a megadott alacsonyabb sávszélesség esetén. Ha elfogadható, akkor lefoglalhatja az alacsonyabb sávszélességet a replikációhoz. Ha nem, akkor foglalja le a javasolt magasabb sávszélességet ahhoz, hogy az idő 100 százalékában betarthassa az RPO-t.

![Lehetőségelemzés a Deployment Planner eszközben](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>A virtuálisgép-köteg ajánlott mérete a kezdeti replikációhoz
A szakaszban arra teszünk javaslatot, hogy hány virtuális gép védhető meg egyszerre úgy, hogy a kezdeti replikáció 72 órán belül befejeződjön a javasolt sávszélesség és a kívánt RPO 100 százalékos betartása mellett a beállítás során. Ezen érték konfigurálható. Módosításához használja a *GoalToCompleteIR* paramétert a jelentéskészítés során.

A diagram egy sávszélesség-tartományt és azt a virtuálisgép-köteg méretet mutatja, amellyel a kezdeti replikáció 72 órán belüli befejezhető. Ez a szám az összes kompatibilis virtuális gép és a virtuális gépek átlagos észlelt méretén alapul.

A nyilvános előzetes verzióban a jelentés nem határozza meg, hogy melyik virtuális gépeket foglalja bele egy kötegbe. A „Compatible VMs” (Kompatibilis virtuális gépek) szakaszban látható lemezméretek alapján kikeresheti az egyes virtuális gépek méretét, és kiválaszthatja az egyes kötegekhez tartozó virtuális gépeket, vagy választhat a számítási feladatok ismert jellemzői alapján is. A kezdeti replikáció befejezési ideje a virtuális gépek lemezeinek tényleges méretével, a felhasznált lemezterülettel és az elérhető hálózati átviteli sebességgel arányosan változik.

![A virtuálisgép-köteg ajánlott mérete](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>A használt növekedési tényező és százalékértékek
A munkalap alján található szakasz a profilkészítésben részt vevő virtuális gépek összes teljesítményszámlálójához használt százalékértéket (az alapértelmezett érték 95 százalék), valamint az összes számításban használt növekedési tényezőt (az alapértelmezett érték 30 százalék) mutatja.

![A használt növekedési tényező és százalékértékek](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Javaslatok az elérhető sávszélesség bemenetként való megadásával

![Javaslatok az elérhető sávszélesség bemenetként való megadásával](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Előfordulhat olyan helyzet, hogy legfeljebb x Mbps sávszélességet tud beállítani a Site Recovery replikációjára. Az eszközzel megadhatja a rendelkezésre álló sávszélességet (a jelentéskészítés közben, a -Bandwidth paraméterrel), és megkapja az elérhető RPO-t percben megadva. Az elérhető RPO-értéknek ismeretében eldöntheti, hogy további sávszélességet kell beállítania, vagy elfogadja a vészhelyreállítási megoldást ezzel az RPO-val.

![Elérhető RPO 500 Mbps sávszélességhez](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>Input (Bemenet)
Az Input (Bemenet) munkalap áttekintést nyújt arról a VMware-környezetről, amelyről profilt készített.

![Áttekintés arról a VMware-környezetről, amelyről profilt készített](./media/site-recovery-deployment-planner/Input.png)

**Start Date** és **End Date** (Kezdő dátum és záró dátum): Azon profilkészítési adatok kezdő és záró dátuma, amelyekről jelentést kíván készíteni. Alapértelmezés szerint a kezdő dátum az a dátum, amikor a profilkészítés megkezdődik, a záró dátum pedig az a dátum, amikor a profilkészítés leáll. Ezek lehetnek a StartDate és EndDate értékek, ha a jelentést ezekkel a paraméterekkel hozza létre.

**Total number of profiling days** (Profilkészítés napjainak teljes száma): A jelentéskészítés kezdő és záró dátuma között eltelt profilkészítési napok teljes száma.

**Number of compatible virtual machines** (Kompatibilis virtuális gépek száma): Ez azon kompatibilis virtuális gépek teljes számát jelöli, amelyekhez a rendszer kiszámolta szükséges hálózati sávszélességet, valamint a szükséges tárfiókok, Microsoft Azure-magok, illetve konfigurációs és további folyamatkiszolgálók számát.

**Total number of disks across all compatible virtual machines**(Lemezek teljes száma az összes kompatibilis virtuális gépen): Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.

**Average number of disks per compatible virtual machine** (Lemezek átlagos száma kompatibilis virtuális gépenként): Az összes kompatibilis virtuális gép alapján számított átlagos lemezszám.

**Average disk size (GB)** (Átlagos lemezméret (GB)): Az összes kompatibilis virtuális gép alapján számított átlagos lemezméret.

**Desired RPO (minutes)** (Kívánt RPO (perc)): A helyreállításipont-célkitűzés alapértelmezett értéke vagy a jelentéskészítéskor a „DesiredRPO” paraméterhez szükséges sávszélesség becslése érdekében megadott érték.

**Desired bandwidth (Mbps)** (Kívánt sávszélesség (Mbps)): A jelentéskészítéskor az elérhető RPO becslése érdekében a „Bandwidth” paraméterhez megadott érték.

**Observed typical data churn per day (GB)** (Megfigyelt átlagos napi adatváltozás (GB)): Az összes profilkészítési napon megfigyelt átlagos adatváltozás. Ez a szám az egyik olyan bemenet, amely alapján a rendszer meghatározza az üzemelő példányban használandó konfigurációs kiszolgálók és további folyamatkiszolgálók számát.


## <a name="vm-storage-placement"></a>Virtuálisgép-tároló elhelyezése

![Virtuálisgép-tároló elhelyezése](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Disk Storage Type** (Lemezes tárolás típusa): A **VMs to Place**(Elhelyezendő virtuális gépek) oszlopban említett összes virtuális gép replikációjához használt standard vagy prémium szintű tárfiók.

**Suggested Prefix** (Javasolt előtag): A tárfiók elnevezéséhez javasolt háromkarakteres előtag. Saját előtagot is használhat, de az eszköz által javasolt nevek követik a [tárfiókok partíció-elnevezési szabályait](https://aka.ms/storage-performance-checklist).

**Suggested Account Name** (Javasolt fióknév): A Storage-fiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Log Storage Account**(Naplótárolási fiók): Minden replikációs naplót egy standard szintű tárfiók tárol. A prémium szintű tárfiókokba replikált virtuális gépek esetében be kell állítani egy további standard szintű tárfiókot a naplók tárolására. Egyetlen standard szintű naplótárolási fiókot több prémium szintű replikációs tárfiók is használhat. A standard szintű tárfiókokba replikált virtuális gépek ugyanazt a tárfiókot használják a naplókhoz is.

**Suggested Log Account Name** (Javasolt naplótárolási fióknév): A naplókat tároló tárfiók neve, a javasolt előtag használata után. Cserélje le a csúcsos zárójelben (< és >) lévő nevet a kívánt értékre.

**Placement Summary** (Elhelyezés összegzése): A tárfiókon található virtuális gépeknek a replikáció és feladatátvételi teszt vagy tényleges feladatátvétel során mért teljes terhelésének összegzése. Tartalmazza a tárfiókhoz rendelt virtuális gépek teljes számát, az Azure Storage-fiókba elhelyezni kívánt összes virtuális gép összesített olvasási/írási IOPS-értékét, az összesített írási (replikációs) IOPS-értéket, az összes lemez teljes beállítási méretét, valamint a lemezek teljes számát.

**Virtual Machines to Place** (Elhelyezendő virtuális gépek): Az összes olyan virtuális gép listája, amelyet az optimális teljesítmény és használat érdekében az adott tárfiókon ajánlott elhelyezni.

## <a name="compatible-vms"></a>Kompatibilis virtuális gépek
![A kompatibilis virtuális gépek Excel-táblázata](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM Name** (Virtuális gép neve): Jelentés létrehozásakor a VMListFile-ban használt virtuálisgépnév vagy IP-cím. Ez az oszlop a virtuális gépekhez csatolt lemezek (VMDK-k) listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**VM Compatibility** (Virtuálisgép-kompatibilitás): Az érték **Yes** (Igen) és **Yes**\* (Igen*) lehet. A **Yes**\* (Igen*) azon példányokra vonatkozik, ahol a virtuális gép megfelel az [Azure Premium Storage-nak](https://aka.ms/premium-storage-workload). Itt a profilkészítés során megállapított magas adatváltozású vagy IOPS-értékű lemez megfelel a P20-as vagy P30-as kategóriának, de a lemez mérete miatt a rendszer P10-es vagy P20-as kategóriába sorolja be. A tárfiók a lemez mérete alapján dönti el, hogy melyik prémium szintű lemeztípushoz rendelje hozzá a lemezt. Példa:
* 128 GB alatt P10.
* 128 GB és 512 GB között P20.
* 512 GB és 1023 GB között P30.

Tehát, ha a számítási feladatok jellemzői alapján egy lemez a P20-as vagy P30-as kategóriába tartozik, de a mérete alapján egy alacsonyabb prémium szintű tárolólemez-típusba kerül, az eszköz **Yes**\* (Igen*) megjelöléssel látja el ezeket a virtuális gépeket. Az eszköz azt is javasolja, hogy módosítsa a forráslemez méretét, hogy a lemez megfeleljen az ajánlott prémium szintű tárolólemez-típusnak, vagy hogy módosítsa a céllemez típusát a feladatátvétel után.

**Storage Type** (Tároló típusa): Standard vagy Premium.

**Suggested Prefix** (Javasolt előtag): A tárfiók három karakterből álló előtagja.

**Storage Account** (Tárfiók): A javasolt tárfiókelőtagot használó név.

**R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS (növekedési tényezővel)): A lemez írási/olvasási IOPS-csúcsértéke (alapértelmezés szerint a 95. százalékos érték), beleértve a későbbi növekedési faktort is (az alapértelmezett érték: 30%). Vegye figyelembe, hogy egy virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Data Churn in Mbps (with Growth Factor)** (Adatváltozás (Mbps) (növekedési tényezővel)): A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. százalékérték), beleértve a későbbi növekedési faktort is (alapértelmezés szerint: 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Azure VM Size** (Azure-beli virtuális gép mérete): Az Azure Cloud Services-beli hozzárendelt virtuális gép ideális mérete az adott helyszíni virtuális gép esetén. A hozzárendelés a helyszíni virtuális gép memóriáján, a lemezek/magok/hálózati adapterek számán és az olvasási/írási IOPS-értéken alapul. Mindig a legalacsonyabb, a helyszíni virtuális gép összes jellemzőjének megfelelő Azure-beli virtuálisgép-méretet ajánlott használni.

**Number of Disks** (Lemezek száma): A virtuális gép lemezeinek (VMDK-inak) teljes száma.

**Disk size (GB)** (Lemez mérete (GB)): A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok): A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)): A virtuális gép fizikai memóriája (RAM).

**NICs** (Hálózati adapterek): A virtuális gép hálózati adaptereinek száma.

## <a name="incompatible-vms"></a>Nem kompatibilis virtuális gépek

![A nem kompatibilis virtuális gépek Excel-táblázata](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM Name** (Virtuális gép neve): Jelentés létrehozásakor a VMListFile-ban használt virtuálisgépnév vagy IP-cím. Ez az oszlop a virtuális gépekhez csatolt VMDK-k listáját is megjeleníti. Az ismétlődő nevű vagy IP-című vCenter-beli virtuális gépek megkülönböztetésére a nevek tartalmazzák az ESXi-gazdagépnevet is. A feltüntetett ESXi-gazdagép az a számítógép, ahol a virtuális gép megtalálható volt, amikor az eszköz először felderítette azt a profilkészítés során.

**VM Compatibility** (Virtuális gép kompatibilitása): Azt jelzi, hogy miért nem kompatibilis az adott virtuális gép a Site Recoveryvel való használattal. Az indokok a virtuális gép minden nem kompatibilis lemezénél vannak megadva, és a közzétett [tárhelykorlátok](https://aka.ms/azure-storage-scalbility-performance) alapján a következők lehetnek:

* A lemez mérete nagyobb 1023 GB-nál. Az Azure Storage jelenleg nem támogatja az 1 TB-nál nagyobb lemezméretet.

* A virtuális gép teljes mérete (replikáció + TFO) meghaladja a támogatott tárfiók méretkorlátozását (35 TB). Ez a fajta inkompatibilitás általában akkor fordul elő, ha a virtuális gép egy lemezének egyik teljesítményjellemzője meghaladja a támogatott standard szintű tárolóra vonatkozó Azure- vagy Site Recovery-korlátozásokat. Egy ilyen példány a prémium szintű tárolózónába kényszeríti a virtuális gépet. A prémium szintű tárfiókok maximális támogatott mérete azonban 35 TB, és egyetlen védett virtuális gép nem védhető több tárfiókon keresztül. Ügyeljen arra is, hogy amikor a feladatátvételi tesztet védett virtuális gépen hajtja végre, akkor az ugyanazon a tárfiókon fut, ahol a replikáció folyik. Ezen a példányon a lemez méretének kétszeresét kell kiosztani, hogy a replikáció is folytatódhasson, és ezzel párhuzamosan a feladatátvételi teszt is sikeres legyen.
* A forrás IOPS-érték meghaladja a tároló lemezenkénti 5000-es IOPS-korlátját.
* A forrás IOPS-érték meghaladja a tároló virtuális gépenkénti 80 000-es IOPS-korlátját.
* Az átlagos adatváltozás meghaladja a Site Recovery lemezenkénti átlagos I/O-mérethez megadott 10 MBps értékű adatváltozási korlátját.
* Total data churn across all disks on the VM exceeds the maximum supported Site Recovery data churn limit of 54 MBps per VM (Az összes virtuális gépre vonatkozó teljes adatváltozás meghaladja a Site Recovery virtuális gépenkénti 54 Mbps-os támogatott adatváltozási korlátját)
* Az átlagos tényleges írási IOPS-érték meghaladja a Site Recovery lemezenkénti 840-es támogatott IOPS-korlátját.
* A kiszámított pillanatkép-tároló mérete meghaladja a 10 TB-os támogatott méretet.

**R/W IOPS (with Growth Factor)** (Írási/olvasási IOPS (növekedési tényezővel)): A lemez IOPS-csúcsértéke (alapértelmezés szerint a 95. százalékos érték), beleértve a későbbi növekedési faktort is (az alapértelmezett érték: 30%). Vegye figyelembe, hogy a virtuális gép teljes írási/olvasási IOPS-értéke nem mindig egyezik meg az egyes virtuálisgép-lemezek írási/olvasási IOPS-értékeinek összegével. Ez azért van, mert a virtuális gép írási/olvasási IOPS-csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért írási/olvasási IOPS-értékek legmagasabb összege.

**Data Churn in Mbps (with Growth Factor)** (Adatváltozás (Mbps) (növekedési tényezővel)): A lemez adatváltozásának csúcsértéke (alapértelmezés szerint a 95. százalékérték), beleértve a későbbi növekedési faktort is (alapértelmezés szerint 30 százalék). Vegye figyelembe, hogy a virtuális gép teljes adatváltozása nem mindig egyezik meg az egyes lemezek adatváltozásának összegével. Ez azért van, mert a virtuális gép adatváltozásának csúcsértéke az egyes lemezeken a profilkészítési időszak alatt percenként mért adatváltozás-értékek legmagasabb összege.

**Number of Disks** (Lemezek száma): A virtuális gép VMDK-inak teljes száma.

**Disk size (GB)** (Lemez mérete (GB)): A virtuális gép összes lemezének teljes beállítási mérete. Az eszköz a virtuális gép egyes lemezeinek méretét is megjeleníti.

**Cores** (Magok): A virtuális gép processzormagjainak száma.

**Memory (MB)** (Memória (MB)): A virtuális gép fizikai memóriájának (RAM) mennyisége.

**NICs** (Hálózati adapterek): A virtuális gép hálózati adaptereinek száma.


## <a name="site-recovery-limits"></a>A Site Recovery korlátai

**Replikáció tárolási célja** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MBps | Lemezenként 168 GB
Prémium szintű P10 lemez | 8 KB    | 2 MBps | Lemezenként 168 GB
Prémium szintű P10 lemez | 16 KB | 4 MBps |    Lemezenként 336 GB
Prémium szintű P10 lemez | 32 KB vagy több | 8 MBps | Lemezenként 672 GB
Prémium szintű P20 vagy P30 lemez | 8 KB    | 5 MBps | Lemezenként 421 GB
Prémium szintű P20 vagy P30 lemez | 16 KB vagy több |10 MBps | Lemezenként 842 GB

Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek. A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján. Az előbbi számok egy általános, körülbelül ötperces várólistát feltételeznek. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

Ezek a korlátok a saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. A legjobb eredmények érdekében még az üzembe helyezés megtervezése után is ajánlott az alkalmazás alapos tesztelése feladatátvételi tesztek használatával, így valós képet kaphat a teljesítményről.

## <a name="updating-the-deployment-planner"></a>A Deployment Planner frissítése
A Deployment Planner frissítéséhez tegye a következőt:

1. Töltse le az [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) legújabb verzióját.

2. Másolja a .zip mappát arra a kiszolgálóra, amelyen futtatni szeretné.

3. Csomagolja ki a .zip mappát.

4. A következő lehetőségek közül választhat:
 * Ha a legújabb verzió nem tartalmaz profilkészítést érintő javítást, és a profilkészítés már folyamatban van a Planner aktuális verzióján, folytassa a profilkészítést.
 * Ha a legújabb verzió tartalmaz profilkészítést érintő javítást, javasoljuk, hogy állítsa le az aktuális verzión a profilkészítést, és indítsa újra a profilkészítést az új verzióval.

  >[!NOTE]
  >
  >Ha az új verzióval indítja el a profilkészítést, akkor ugyanazt a kimeneti elérési utat kell megadnia ahhoz, hogy az eszköz a már létező fájlokhoz fűzze hozzá a profiladatokat. Ekkor a profiladatok teljes körét felhasználja majd a jelentés létrehozásához. Ha eltérő kimeneti könyvtárat ad meg, akkor új fájlok jönnek létre, és a régi profiladatokat nem lehet majd felhasználni a jelentés létrehozásához.
  >
  >Minden új Deployment Planner a .zip fájl összegző frissítését jelenti. Ezért nem kell a legújabb fájlokat a korábbi mappába másolnia. Létrehozhat és használhat egy új mappát is.


## <a name="version-history"></a>Verzióelőzmények
### <a name="11"></a>1.1
Frissítve: 2017. március 9.

Az alábbi problémákat javítja:

* Az eszköz nem képes profilt készíteni a virtuális gépekről, ha a vCenter-kiszolgáló különböző ESXi-gazdagépein több azonos nevű vagy IP-című virtuális gép található.
* A Kompatibilis virtuális gépek és a Nem kompatibilis virtuális gépek munkalapján a másolás és a keresés le van tiltva.

### <a name="10"></a>1.0
Frissítve: 2017. február 23.

Az Azure Site Recovery Deployment Planner 1.0-s nyilvános előzetes verziója az alábbi ismert hibákat tartalmazza (ezeket az elkövetkező frissítések fogják elhárítani):

* Az eszköz csak a VMware – Azure forgatókönyvek esetén működik, Hyper-V – Azure irányú üzembe helyezések esetében nem. A Hyper-V – Azure forgatókönyvekhez használja a [Hyper-V Capacity Planner eszközt](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* A GetThroughput művelet nem támogatott az US Government és kínai Microsoft Azure-régiókban.
* Az eszköz nem képes profilt készíteni a virtuális gépekről, ha a vCenter-kiszolgáló különböző ESXi-gazdagépein több azonos nevű vagy IP-című virtuális gép található. Ebben a verzióban az eszköz kihagyja a profilkészítést abban az esetben, ha a VMListFile fájlban ismétlődik egy virtuális gép neve vagy IP-címe. Ez áthidalható úgy, hogy a vCenter-kiszolgáló helyett az ESXi-gazdagéppel készíti el a virtuális gépek profilját. Minden ESXi-gazdagép esetében futtatnia kell egy példányt.

