# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek régiók szerinti csoportosítása és rendelkezésre állása
Fontos átlátni, hogy a virtuális gépek hol és hogyan működnek az Azure-ban, illetve hogy a teljesítmény, a rendelkezésre állás és a redundancia maximalizálása terén milyen lehetőségek vannak. Az Azure világszerte számos adatközpontban működik. Ezek az adatközpontok földrajzi régiók szerint vannak csoportosítva, ami kellő mozgásteret biztosít az alkalmazások létrehozási helyének megválasztásához. Ez a cikk az Azure rendelkezésre állással és redundanciával kapcsolatos szolgáltatásairól nyújt áttekintést.

## <a name="what-are-azure-regions"></a>Mik azok az Azure-régiók?
Az Azure lehetővé teszi erőforrások, így például virtuális gépek létrehozását olyan, meghatározott földrajzi régiókban, mint az „USA nyugati régiója”, „Észak-Európa” vagy „Délkelet-Ázsia”. Jelenleg világszerte 30 Azure-régió van. [A régiók és a kapcsolódó helyek listáját itt](https://azure.microsoft.com/regions/) tekintheti meg. A redundancia és a rendelkezésre állás biztosítása érdekében minden egyes régióban egyszerre több adatközpont működik. Ez nagyobb rugalmasságot biztosít az alkalmazások létrehozásához, a virtuális gépek pedig a lehető legközelebb lesznek a felhasználókhoz, ami jogi, megfelelőségi és adóügyi szempontból egyaránt előnyös.

## <a name="special-azure-regions"></a>Különleges Azure-régiók
Létezik néhány olyan különleges Azure-régió is, amelyet megfelelőségi vagy jogi okokból érdemes választani az alkalmazások létrehozásához. Ezek a különleges régiók a következők:

* **USA-beli államigazgatás – Virginia** és **USA-beli államigazgatás – Iowa**
  * Az Azure fizikailag és logikailag elszigetelt példánya az USA-beli államigazgatási szervek és partnereik számára, amelyet biztonsági szempontból átvilágított, USA-beli személyek kezelnek. Olyan további megfelelőségi tanúsítványokat is tartalmaz, mint a [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) vagy a [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Tudjon meg többet az [Azure Governmentről](https://azure.microsoft.com/features/gov/).
* **Közép-India**, **Dél-India** és **Nyugat-India**
  * Ezek a régiók egyelőre csak az Indiában helyi regisztrációval rendelkező, mennyiségi licencprogramban részt vevő ügyfelek és partnerek számára érhetők el. 2016-ban azok a felhasználók érhették el, akik közvetlen online előfizetést vásároltak.
* **Kelet-Kína** és **Észak-Kína**
  * Ezek a régiók a Microsoft és a 21Vianet közötti különleges partnerség révén érhetők el, amelyben az adatközpontok fenntartója nem közvetlenül a Microsoft. Tudjon meg többet a [Microsoft Azure kínai működéséről](http://www.windowsazure.cn/).
* **Közép-Németország** és **Északkelet-Németország**
  * Ezek a régiók jelenleg egy megbízott adatkezelői modell szerint érhetők el, amelynek keretében az ügyféladatok Németországban maradnak a T-Systems, a Deutsche Telekom egyik vállalata és egyben a németországi megbízott adatkezelő felügyelete alatt.

## <a name="region-pairs"></a>Régiópárok
Minden egyes Azure-régió párban áll egy másik, azonos földrajzi helyhez tartozó régióval (amilyen például az USA, Európa vagy Ázsia). Ez lehetővé teszi az erőforrások, így például a virtuálisgép-tárolók földrajzi hely szerinti replikálását abból a megfontolásból, hogy természeti katasztrófák, zavargások, áramkimaradások vagy a fizikai hálózat fennakadásai kisebb eséllyel jelentkeznek egyszerre mindkét régióban. A régiópárok további előnyei még a következők:

* Az Azure szélesebb körű leállása esetén minden párból az egyik régió előnyt élvez, ami segít csökkenteni az alkalmazások helyreállításához szükséges időt. 
* A tervezett Azure-frissítések egyszerre csak a régiópár egyik tagján jelennek meg, ami csökkenti az állásidőt és az alkalmazáskimaradás kockázatát.
* Az adatok a pár mindkét tagja esetében ugyanazon a földrajzi helyen maradnak (Dél-Brazília kivételével), ami adóügyi és törvényi szempontból fontos.

Néhány példa a régiópárokra:

| Elsődleges | Másodlagos |
|:--- |:--- |
| USA nyugati régiója |USA keleti régiója |
| Észak-Európa |Nyugat-Európa |
| Délkelet-Ázsia |Kelet-Ázsia |

[A régiópárok teljes listája](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions) itt érhető el.

## <a name="feature-availability"></a>Szolgáltatások rendelkezésre állása
Néhány szolgáltatás és virtuálisgép-funkció, például meghatározott méretű virtuális gépek vagy adott tárolótípusok csak bizonyos régiókban érhetők el. Emellett létezik pár olyan globális Azure-szolgáltatás is, amelyhez nem kell régiót választani. Ilyen például az [Azure Active Directory](../articles/active-directory/active-directory-whatis.md), a [Traffic Manager](../articles/traffic-manager/traffic-manager-overview.md) vagy az [Azure DNS](../articles/dns/dns-overview.md). Az alkalmazáskörnyezet megtervezéséhez javasoljuk, hogy tekintse meg [az Azure-szolgáltatások régiók szerinti rendelkezésre állását](https://azure.microsoft.com/regions/#services). 

## <a name="storage-availability"></a>Tárterület rendelkezésre állása
A rendelkezésre álló tárreplikációs lehetőségek mérlegeléséhez fontos átlátni az Azure-régiók és földrajzi helyek működését. A tárterület típusától függően eltérő replikálási lehetőségek állnak rendelkezésre.

**Azure Managed Disks**
* Helyileg redundáns tárolás (LRS)
  * A rendszer háromszor replikálja az adatokat abban a régióban, amelyben a tárfiókot létrehozták.

**Tárfiókalapú lemezek**
* Helyileg redundáns tárolás (LRS)
  * A rendszer háromszor replikálja az adatokat abban a régióban, amelyben a tárfiókot létrehozták.
* Zónaredundáns tárolás (ZRS)
  * A rendszer két vagy három intézményben háromszor replikálja az adatokat, amelyek egy vagy két régióban is lehetnek.
* Georedundáns tárolás (GRS)
  * A rendszer egy másodlagos régióba replikálja az adatokat, amely a forrásadatok elsődleges helyétől több száz kilométerre található.
* Írásvédett georedundáns tárolás (RA-GRS)
  * A rendszer egy másodlagos régióba replikálja az adatokat (csakúgy, mint a GRS esetében), és az adatokhoz a másodlagos helyszínen is írásvédett hozzáférést biztosít.

A következő táblázat a tárreplikáció típusai közötti különbségek rövid összefoglalását tartalmazza:

| Replikációs stratégia | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| A rendszer több intézményben replikálja az adatokat. |Nem |Igen |Igen |Igen |
| Az adatok a másodlagos és az elsődleges helyről is olvashatók. |Nem |Nem |Nem |Igen |
| A külön csomópontokon fenntartott adatmásolatok száma. |3 |3 |6 |6 |

[Az Azure tárreplikációs lehetőségeiről itt](../articles/storage/storage-redundancy.md) olvashat bővebben. További információ a felügyelt lemezekről: [Azure Managed Disks – áttekintés](../articles/storage/storage-managed-disks-overview.md).

### <a name="storage-costs"></a>Tárolási költségek
Az árak a választott tárolótípus és rendelkezésre állás függvényében változnak.

**Azure Managed Disks**
* A Premium Managed Disks szolgáltatás tartós állapotú meghajtókkal (SSD) üzemel, a Standard Managed Disks szolgáltatás pedig HDD-alapú. Mind a Premium, mind a Standard Managed Disks szolgáltatás díjszabása a kiépített lemezkapacitás szerint alakul.

**Nem felügyelt lemezek**
* A Prémium szintű tárterület tartós állapotú meghajtókkal (SSD) üzemel, a díjszabás pedig a lemezkapacitástól függ.
* A Standard szintű tárterület általános merevlemezekkel működik, a díjszabás pedig a kihasznált kapacitástól és a rendelkezésre álló, választott tárterülettől függ.
  * Az RA-GRS esetében egy további georeplikációs adatátviteli díj is érvényben van, az adatok másik Azure-régióba történő replikálásához használt sávszélességre vonatkozóan.

A különböző tártípusokhoz és rendelkezésre állási lehetőségekhez tartozó díjszabásról lásd: [Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="azure-images"></a>Azure-rendszerképek
Az Azure-ban a virtuális gépek rendszerképből jönnek létre. A rendszerképek forrása általában az [Azure Marketplace](https://azure.microsoft.com/marketplace/), ahol a partnerek teljes, előre konfigurált rendszerképeket kínálnak operációs rendszerekhez vagy alkalmazásokhoz.

Az Azure Marketplace rendszerképeivel létrehozott virtuális gép tulajdonképpen sablonokkal készül. Az Azure Resource Manager-sablonok deklaratív JavaScript Object Notation- (JSON-) fájlok, amelyek felhasználásával összetett alkalmazáskörnyezeteket lehet létrehozni virtuális gépek, tárterületek, virtuális hálózatok stb. bevonásával. Tudjon meg többet az [Azure Resource Manager-sablonok használatáról](../articles/azure-resource-manager/resource-group-overview.md), illetve ismerje meg, [hogyan hozhat létre saját sablont](../articles/resource-group-authoring-templates.md).

Az [Azure CLI](../articles/virtual-machines/linux/upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) és az [Azure PowerShell](../articles/virtual-machines/windows/upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) használatával saját, egyéni rendszerképeket is létrehozhat, illetve feltölthet, így gyorsan és egyszerűen hozhat létre az igényeinek megfelelő, egyedi virtuális gépeket.

## <a name="availability-sets"></a>Rendelkezésre állási csoportok
A rendelkezésre állási csoport a virtuális gépek logikus csoportosítását jelenti, ami lehetővé teszi az Azure számára az alkalmazás felépítésének megértését a redundancia és a rendelkezésre állás biztosításához. Két vagy annál több virtuális gépet már ajánlott egy rendelkezésre állási csoporton belül létrehozni, hogy az alkalmazás rendelkezésre állása a lehető legjobb legyen, és a [99,95%-os Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) teljesüljön. Ha az önálló virtuális gép [Azure Premium Storage](../articles/storage/storage-premium-storage.md) csomagot használ, az Azure SLA a nem tervezett karbantartási események során is érvényben van. A rendelkezésre állási csoport két további csoportosítást is tartalmaz, amelyek védelmet nyújtanak az esetleges hardverhibák ellen, illetve lehetővé teszik a frissítések biztonságos alkalmazását. Ez a tartalék tartományok, illetve a frissítési tartományok csoportja.

![A frissítési és tartalék tartományokat tartalmazó konfiguráció elméleti rajza](./media/virtual-machines-common-regions-and-availability/ud-fd-configuration.png)

Tudjon meg többet a [Linux-alapú virtuális gépek](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), illetve a [Windows-alapú virtuális gépek](../articles/virtual-machines/linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) rendelkezésre állásának kezeléséről.

### <a name="fault-domains"></a>Tartalék tartományok
A tartalék tartomány a mögöttes hardverelemek logikus csoportja, ahol az áramforrás és a hálózati kapcsoló közös, a helyszíni adatközpontok állványaihoz hasonlóan. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a tartalék tartományok között. Ez a módszer korlátozza a potenciális hardvermeghibásodások, hálózatkimaradások vagy a tápellátás megszakadásának hatását.

#### <a name="managed-disk-fault-domains-and-availability-sets"></a>Tartalék tartományok és rendelkezésre állási csoportok felügyelt lemezeken
Az [Azure Managed Disks](../articles/storage/storage-faq-for-disks.md) használatával létrehozott virtuális gépek felügyelt rendelkezésre állási csoportok használata esetén felügyelt lemezes tartalék tartományokra vannak elosztva. Ez a kiosztás biztosítja, hogy a virtuális géphez csatolt mindegyik felügyelt lemez ugyanabban a felügyelt lemezes tartalék tartományban legyen. Kizárólag felügyelt lemezeken futó virtuális gépek hozhatók létre felügyelt rendelkezésre állási csoportokban. A felügyelt lemezes tartalék tartományok száma régiónként eltérő – régiónként kettő vagy három felügyelt lemezes tartalék tartomány lehet.

### <a name="update-domains"></a>Frissítési tartományok
A frissítési tartomány a mögöttes hardverelemek logikus csoportja, amelyen egyszerre végezhető karbantartás vagy újraindítás. Amikor rendelkezésre állási csoporton belül hoz létre virtuális gépeket, az Azure platform automatikusan elosztja ezeket a frissítési tartományok között. Ez lehetővé teszi, hogy az alkalmazás legalább egy példánya mindig fusson akkor is, ha az Azure platformon épp rendszeres karbantartás folyik. A frissítési tartományok újraindítása nem haladhat szekvenciálisan a tervezett karbantartás során, hanem csak egyetlen frissítési tartományt lehet újraindítani egyszerre.

## <a name="next-steps"></a>Következő lépések
Mostantól a saját Azure-környezetében is használhatja ezeket a rendelkezésre állási és redundanciával kapcsolatos szolgáltatásokat. Javasoljuk, hogy tájékozódjon [az Azure rendelkezésre állásával kapcsolatos ajánlott eljárásokról](../articles/best-practices-availability-checklist.md).

