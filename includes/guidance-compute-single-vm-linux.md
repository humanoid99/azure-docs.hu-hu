Ez a cikk bevált gyakorlatokat mutat be egy Linux virtuális gép (VM) Azure-on való futtatására, figyelembe véve a méretezési, a rendelkezésre állási, a felügyeleti és a biztonsági szempontokat. Az Azure támogatja különböző népszerű Linux-disztribúciók, például a CentOS, a Debian, a Red Hat Enterprise, az Ubuntu és a FreeBSD futtatását. További információk: [Az Azure és a Linux][azure-linux].

> [!NOTE]
> Az Azure-hoz két különböző üzemi modell érhető el: a [Resource Manager-alapú][resource-manager-overview] és a klasszikus. Ez a cikk a Microsoft által az új üzembe helyezésekhez javasolt Resource Managert használja.
> 
> 

Nem javasoljuk, hogy egyetlen virtuális gépet használjon kritikus fontosságú számítási feladatokhoz, mert ez egyetlen hibaérzékeny pontot hoz létre. A magasabb rendelkezésre állás érdekében helyezzen üzembe több virtuális gépet egy [rendelkezésre állási csoportban][availability-set]. További információkért tekintse meg a [több virtuális gép Azure-on való futtatását][multi-vm] ismertető szakaszt. 

## <a name="architecture-diagram"></a>Architektúradiagram
A virtuális gépek Azure-beli üzembe helyezése során nem csak magára a virtuális gépre van szükség. Figyelembe kell venni a számítási, hálózati és tárolási elemeket is.

> A [Microsoft letöltőközpontból][visio-download] letölthető egy Visio dokumentum, amely tartalmazza ezt az architektúradiagramot. A diagram a „Számítás – egy VM” oldalon található.
> 
> 

![[0]][0]

* **Erőforráscsoport.** Az [*erőforráscsoport*][resource-manager-overview] egy tároló, amely kapcsolódó erőforrásokat tárol. Hozzon létre egy erőforráscsoportot, amely a virtuális géphez kapcsolódó erőforrásokat tárolja.
* **VM**. A virtuális gépet üzembe helyezheti a közzétett rendszerképek listájáról, vagy egy, az Azure Blob Storage-ba feltöltött virtuálismerevlemez-fájlból (VHD).
* **Operációsrendszer-lemez.** Az operációsrendszer-lemez egy, az [Azure Storage-ban][azure-storage] tárolt VHD. Ez azt jelenti, hogy akkor is működik, ha a gazdagép leáll. Az operációsrendszer-lemez a `/dev/sda1`.
* **Ideiglenes lemez.** A VM egy ideiglenes lemezzel jön létre. A lemez a gazdagép egyik fizikai meghajtóján található. *Nincs* mentve az Azure Storage-ban, és előfordulhat, hogy törlődik az újraindítások és más VM-életciklusesemények során. Ez a lemez csak ideiglenes adatokat, például lapozófájlokat tárol. Az ideiglenes lemez a `/dev/sdb1`, és a csatlakoztatásának helye `/mnt/resource` vagy `/mnt`.
* **Adatlemezek.** Az [adatlemez][data-disk] egy állandó, az alkalmazásadatokhoz használt VHD. Az adatlemezeket (pl. az operációsrendszer-lemezt) az Azure Storage tárolja.
* **Virtuális hálózat (VNet) és alhálózat.** Az Azure-ban minden VM egy alhálózatokra osztott virtuális hálózatban van üzembe helyezve.
* **Nyilvános IP-cím.** Szükség van egy nyilvános IP-címre a virtuális géppel való kommunikációhoz – például SSH-n keresztül.
* **Hálózati adapter (NIC)**. A hálózati adapter teszi lehetővé a virtuális gép számára a virtuális hálózattal való kommunikációt.
* **Hálózati biztonsági csoport (NSG)**. Az [NSG][nsg] segítségével lehet engedélyezni vagy letiltani az alhálózat felé irányuló hálózati forgalmat. Egy NSG-t társíthat egy különálló hálózati adapterhez vagy egy alhálózathoz. Ha egy alhálózathoz társítja, az NSG-szabályok az alhálózat összes virtuális gépére vonatkoznak.
* **Diagnosztika.** A diagnosztikai naplózás létfontosságú a virtuális gép kezeléséhez és hibaelhárításához.

## <a name="recommendations"></a>Javaslatok

Az alábbi javaslatok a legtöbb forgatókönyvre vonatkoznak. Kövesse ezeket a javaslatokat, ha nincsenek ezeket felülíró követelményei. 

### <a name="vm-recommendations"></a>Virtuális gépekre vonatkozó javaslatok

Az Azure számos különféle virtuálisgép-méretet biztosít, de mi a DS és a GS sorozatot ajánljuk, mert ezek a gépméretek támogatják a [Premium Storage][premium-storage] tárolást. Válassza ezen gépméretek egyikét, kivéve, ha speciális számítási feladatokhoz, például nagy teljesítményű feldolgozáshoz kívánja a gépeket használni. Részletekért tekintse meg a [virtuálisgép-méretekkel kapcsolatos][virtual-machine-sizes] szakaszt.

Ha meglévő számítási feladatot helyez át az Azure-ba, kezdetnek azt a virtuálisgép-méretet válassza, amely a leginkább egyezik a helyszíni kiszolgálói méretével. Ezután mérje meg a valós számítási feladat teljesítményét a CPU, a memória és a lemez másodpercenkénti bemeneti/kimeneti műveletei (IOPS) figyelembe vételével, és módosítsa a méretet, ha szükséges. Ha több hálózati adapterre van szükség a virtuális géphez, vegye figyelembe, hogy a hálózati adapterek maximális száma a [virtuálisgép-méret][vm-size-tables] függvénye.

Amikor üzembe helyezi a virtuális gépet és az egyéb erőforrásokat, meg kell adnia egy régiót. A legjobb megoldás, ha a belső felhasználóihoz vagy ügyfeleihez legközelebb eső régiót választja. Azonban nem minden virtuálisgép-méret érhető el minden régióban. Részletek: [Szolgáltatások régiónként][services-by-region]. Az egy adott régióban elérhető virtuálisgép-méretek listájának megtekintéséhez futtassa az alábbi Azure parancssori felületi parancsot:

```
azure vm sizes --location <location>
```

Információk közzétett virtuálisgép-rendszerkép kiválasztásáról: [Linux virtuális gép rendszerképének kiválasztása az Azure CLI-vel][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>A lemezre és a tárolásra vonatkozó javaslatok

A legjobb adatátviteli teljesítmény érdekében javasoljuk a [Premium Storage][premium-storage] tárolást, amely SSD meghajtókon tárolja az adatokat. A költség az üzembe helyezett lemez méretétől függően változik. AZ IOPS és az átviteli sebesség (azaz az adatátviteli sebesség) a lemezmérettől is függ, ezért lemezek üzembe helyezésekor vegye figyelembe mindhárom tényezőt (kapacitás, IOPS és átviteli sebesség). 

Hozzon létre külön Azure Storage-fiókot minden virtuális géphez a virtuális merevlemezek (VHD-k) tárolására, hogy elkerülje az IOPS-korlátok elérését a tárfiókban. 

Adjon hozzá egy vagy több adatlemezt. A létrehozott VHD-k nincsenek formázva. A lemez formázásához jelentkezzen be a virtuális gépre. A Linux felületen az adatlemezek `/dev/sdc`, `/dev/sdd` stb. formátumban jelennek meg. Az `lsblk` futtatásával listázhatja a blokkeszközöket, beleértve a lemezeket. Adatlemez használatához hozzon létre egy partíciót és egy fájlrendszert, majd csatlakoztassa a lemezt. Példa:

```bat
# Create a partition.
sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Ha nagy számú adatlemezzel rendelkezik, vegye figyelembe a tárfiók teljes I/O-korlátját. További információkért lásd a [virtuálisgép-lemez korlátaival kapcsolatos][vm-disk-limits] szakaszt.

Adatlemez hozzáadásakor a rendszer hozzárendel egy logikaiegység-szám (LUN) azonosítót a lemezhez. Lehetősége van megadni a LUN azonosítót &mdash; – ha például lecserél egy lemezt, de meg akarja tartani a LUN azonosítót, vagy ha egy alkalmazása egy konkrét LUN azonosítót keres. Ne feledje azonban, hogy az egyes lemezek LUN azonosítóinak egyedinek kell lenniük.

Érdemes lehet módosítani az I/O-ütemezőt az SSD-k teljesítményének optimalizálására, mert a prémium szintű tárfiókkal rendelkező virtuális gépek lemezei SSD-k. Általában az NOOP-ütemezőt ajánlott használni SSD-khez, de érdemes az [iostat] vagy hasonló eszközt használni az adott számítási feladathoz tartozó lemez I/O-teljesítmény figyelésére.

A legjobb teljesítmény érdekében hozzon létre egy önálló tárfiókot a diagnosztikai naplók tárolására. Egy standard helyileg redundáns tárolási (LRS) fiók elegendő a diagnosztikai naplókhoz.

### <a name="network-recommendations"></a>Hálózatokra vonatkozó javaslatok

A nyilvános IP-cím lehet dinamikus vagy statikus. Alapértelmezés szerint dinamikus.

* Akkor foglaljon le [statikus IP-címet][static-ip], ha rögzített, nem módosuló IP-címre van szüksége – például ha egy A rekordot kell létrehoznia a DNS-ben, vagy ha hozzá kell adnia az IP-címet a biztonságos elemek listájához.&mdash;
* Létrehozhat egy teljes tartománynevet (FQDN) is az IP-címhez. Ezután a DNS-ben regisztrálhat egy, az FQDN-re mutató [CNAME rekordot][cname-record]. További információért tekintse meg a [teljes tartománynév az Azure Portalon való létrehozásával kapcsolatos][fqdn] szakaszt.

Minden NSG tartalmaz egy [alapértelmezett szabálykészletet][nsg-default-rules], amelyben szerepel egy minden bejövő internetes forgalmat blokkoló szabály. Az alapértelmezett szabályok nem törölhetők, azonban más szabályokkal felülírhatók. Az internetes forgalom engedélyezéséhez hozzon létre olyan szabályokat, amelyek adott portokon engedélyezik a bejövő forgalmat – például a HTTP-hez a 80-as porton.&mdash;  

Az SSH engedélyezéséhez adjon hozzá egy szabályt az NSG-hez, amely engedélyezi a bejövő internetes forgalmat a 22-es TCP-portra.

## <a name="scalability-considerations"></a>Méretezési szempontok

Vertikális le- vagy felskálázáshoz [módosítsa a virtuális gép méretét][vm-resize]. 

Horizontális felskálázáshoz helyezzen két vagy több virtuális gépet egy rendelkezésre állási csoportba egy terheléselosztó mögé. További információkért tekintse meg a [több virtuális gép Azure-on való futtatását][multi-vm] ismertető szakaszt.

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A magasabb rendelkezésre állás érdekében helyezzen üzembe több virtuális gépet egy rendelkezésre állási csoportban. Ez magasabb szintű [szolgáltatói szerződést][vm-sla] (SLA-t) biztosít. 

A virtuális gépre hatással lehet egy [tervezett karbantartás][planned-maintenance] vagy egy [nem tervezett karbantartás][manage-vm-availability]. Annak megállapításához, hogy a virtuális gép újraindítását egy tervezett karbantartás okozta-e, használja a [virtuális gépek újraindítási naplóit][reboot-logs].

A VHD-ket a rendszer az [Azure Storage][azure-storage]-ban tárolja, és az Azure Storage a tartósság és a rendelkezésre állás érdekében replikálva van.

A normál műveletek során történő véletlen (például hiba miatti) adatvesztés elleni védelem érdekében érdemes időponthoz kötött biztonsági mentéseket is megvalósítani [blob-pillanatképekkel][blob-snapshot] vagy más eszközzel.

## <a name="manageability-considerations"></a>Felügyeleti szempontok

**Erőforráscsoportok.** A szorosan összekapcsolt, azonos életciklusú erőforrásokat helyezze egy [erőforráscsoportba][resource-manager-overview]. Az erőforráscsoportok segítségével csoportosan helyezhet üzembe és figyelhet erőforrásokat, és a számlázási költségeket erőforráscsoportonként összegezheti. Az erőforrásokat törölheti is készletenként. Ez nagyon hasznos tesztkörnyezetek esetében. Az erőforrásoknak adjon kifejező nevet, így könnyebb megtalálni egy adott erőforrást és megérteni a szerepét. Lásd: [Az Azure-erőforrások ajánlott elnevezési konvenciói][naming conventions].

**SSH**. Linux virtuális gép létrehozása előtt hozzon létre egy 2048 bites RSA nyilvános-titkos kulcspárt. A virtuális gép létrehozásakor használja a nyilvánoskulcs-fájlt. További információ: [SSH használata Linuxon és Macen az Azure-on][ssh-linux].

**Virtuális gépek diagnosztikája.** Engedélyezze a megfigyelést és a diagnosztikát, beleértve az alapvető állapotmetrikákat, a diagnosztikai infrastruktúra naplófájljait és a [rendszerindítási diagnosztikát][boot-diagnostics]. A rendszerindítási diagnosztika segít diagnosztizálni a rendszerindítási hibát, ha a virtuális gép nem indítható állapotba kerül. További információkat [a megfigyelés és a diagnosztika engedélyezésével kapcsolatos][enable-monitoring] szakaszban találhat.  

A diagnosztika a következő parancssori felületi paranccsal engedélyezhető:

```
azure vm enable-diag <resource-group> <vm-name>
```

**Virtuális gépek leállítása.** Az Azure különbséget tesz a „leállított” és a „felszabadított” állapot között. A leállított virtuális gépek után fizetni kell, a felszabadítottak után azonban nem.

Virtuális gép felszabadításához használja az alábbi parancssori felületi parancsot:

```
azure vm deallocate <resource-group> <vm-name>
```

Az Azure Portalon a **Leállítás** gombbal szabadítható fel a virtuális gép. Ha azonban az operációs rendszerből állítja le, amikor be van jelentkezve, azzal virtuális gépet leállítja, de *nem* szabadítja fel, tehát továbbra is fizetnie kell a díját.

**Virtuális gépek törlése.** Ha töröl egy virtuális gépet, a VHD-k nem törlődnek. Ez azt jelenti, hogy biztonságosan törölheti a virtuális gépet anélkül, hogy adatot vesztene. A tárolásért azonban továbbra is díjat kell fizetnie. A VHD törléséhez törölje a fájlt a [Blob Storage-ból][blob-storage].

A véletlen törlés megelőzése érdekében használjon [erőforrászárat][resource-lock]. Ezzel zárolhat egy egész erőforráscsoportot, vagy egyes erőforrásokat, például a virtuális gépet. 

## <a name="security-considerations"></a>Biztonsági szempontok

Az operációsrendszer-frissítéseket automatizálhatja az [OSPatching] virtuálisgép-bővítménnyel. Ezt a bővítményt a virtuális gép üzembe helyezésekor telepítse. Megadhatja, milyen gyakran telepítse a rendszer a javításokat, és hogy induljon-e újra a rendszer a javítások telepítése után.

A [szerepköralapú hozzáférés-vezérléssel][rbac] (RBAC) szabályozható az üzembe helyezett Azure-erőforrásokhoz való hozzáférés. Az RBAC lehetővé teszi, hogy engedélyezési szerepköröket rendeljen a fejlesztő és üzemeltető csapata tagjaihoz. Az Olvasó szerepkör például áttekintheti az Azure-erőforrásokat, de nem hozhatja létre, nem kezelheti és nem törölheti őket. Bizonyos szerepkörök kifejezetten egy adott Azure-erőforrástípusra jellemzők. A Virtuális gép közreműködő szerepkör például újraindíthat vagy felszabadíthat egy virtuális gépet, alaphelyzetbe állíthatja a rendszergazdai jelszót, létrehozhat egy virtuális gépet stb. Ehhez a referenciaarchitektúrához hasznos lehet még a [DevTest Labs-felhasználó][rbac-devtest] és a [Hálózati közreműködő][rbac-network] [beépített RBAC-szerepkör][rbac-roles]. 

Egy felhasználóhoz több szerepkört is rendelhető, és létrehozhat egyéni szerepköröket a még részletesebb engedélyek érdekében.

d> [!NOTE]
> Az RBAC nem korlátozza a virtuális gépre bejelentkezett felhasználó által végezhető műveleteket. Azokat az engedélyeket a vendég operációs rendszeren lévő fiók típusa határozza meg.   
> 
> 

A [vizsgálati naplók][audit-logs] segítségével megtekintheti az üzembe helyezési műveleteket és más virtuálisgép-eseményeket.

Ha titkosítania kell az operációs rendszert és az adatlemezeket, érdemes megfontolnia az [Azure Disk Encryption][disk-encryption] használatát. 

## <a name="solution-deployment"></a>A megoldás üzembe helyezése
Ennek a referenciaarchitektúrának egy üzemelő példánya elérhető a [GitHubon][github-folder]. Tartalmaz egy virtuális hálózatot, egy NSG-t és egy virtuális gépet. Az architektúra üzembe helyezéséhez kövesse az alábbi lépéseket: 

1. Kattintson a jobb gombbal a lenti gombra, és válassza a „Link megnyitása új lapon” vagy a „Link megnyitása új ablakban” lehetőséget.
   [![Üzembe helyezés az Azure-ban](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)
2. Ha a hivatkozás megnyílt az Azure Portalon, meg kell adnia néhány beállítás értékét: 
   
   * Az **Erőforráscsoport** neve már meg van adva a paraméterfájlban, ezért válassza az **Új létrehozása** lehetőséget és a szövegmezőbe írja az `ra-single-vm-rg` karakterláncot.
   * Válassza ki a régiót a **Hely** legördülő listából.
   * Ne szerkessze a **Sablon gyökér szintű URI-je** vagy a **Paraméter gyökér szintű URI-je** szövegmezőt.
.   * Az **Operációs rendszer típusa** legördülő listából válassza a **linux** lehetőséget.
   * Tekintse át a használati feltételeket, majd kattintson az **Elfogadom a fenti feltételeket** lehetőségre.
   * Kattintson a **Vásárlás** gombra.
3. Várjon, amíg az üzembe helyezés befejeződik.
4. A paraméterfájlokban szerepel egy nem módosítható rendszergazdai felhasználónév és jelszó. Erősen ajánlott mindkettőt azonnal lecserélni. Kattintson az Azure Portalon az `ra-single-vm0 ` nevű virtuális gépre. Ezután kattintson a **Támogatás + hibaelhárítás** szakasz **Jelszó alaphelyzetbe állítása** elemére. A **Mód** legördülő listában válassza a **Jelszó alaphelyzetbe állítása** lehetőséget, majd adjon meg új értéket a **Felhasználónév** és a **Jelszó** mezőben. Az új felhasználó nevének és jelszavának megőrzéséhez kattintson a **Frissítés** gombra.

## <a name="next-steps"></a>Következő lépések
A magasabb rendelkezésre állás érdekében helyezzen üzembe két vagy több virtuális gépet egy terheléselosztó mögött. További információkért tekintse meg a [több virtuális gép Azure-on való futtatását][multi-vm] ismertető szakaszt.

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]:../articles/virtual-machines/windows/create-availability-set.md
[azure-cli]: /cli/azure/get-started-with-az-cli2
[azure-linux]:../articles/virtual-machines/linux/overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/storage/storage-about-disks-and-vhds-linux.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]:../articles/virtual-machines/linux/portal-create-fqdn.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]:../articles/virtual-machines/linux/manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]:../articles/virtual-machines/linux/planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-labs-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[select-vm-image]:../articles/virtual-machines/linux/cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssh-linux]:../articles/virtual-machines/linux/mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-account-limits]: ../articles/azure-subscription-service-limits.md#storage-limits
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]:../articles/virtual-machines/linux/sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]:../articles/virtual-machines/linux/change-vm-size.md
[vm-size-tables]:../articles/virtual-machines/windows/sizes.md#size-tables
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[components]: #Solution-components
[blocks]: https://github.com/mspnp/template-building-blocks
[0]: ./media/guidance-blueprints/compute-single-vm.png "Önálló Linux virtuális gép architektúra az Azure-ban"

