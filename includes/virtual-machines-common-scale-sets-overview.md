

A számítási erőforrások horizontális fel- és leskálázását igénylő alkalmazások esetében a méretezési műveletek implicit módon oszlanak el a tartalék- és frissítési tartományok között. A virtuálisgép-méretezési csoportokról bővebb bevezetést [ebben a friss Azure-blogbejegyzésben](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) talál.

A virtuálisgép-méretezési csoportokkal kapcsolatban további információkat tudhat meg az alábbi videókból:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)  
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

## <a name="creating-and-managing-vm-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása és felügyelete
A virtuálisgép-méretezési csoportok megadását és üzembe helyezését – az egyedi Azure Resource Manager-alapú virtuális gépekhez hasonlóan – JSON-sablonok és [REST API-k](https://msdn.microsoft.com/library/mt589023.aspx) segítségével végezheti el. Ezért lehetőség van bármilyen szabványos Azure Resource Manager-alapú üzembe helyezési módszer használatára. A sablonokról további információkat az [Authoring Azure Resource Manager templates](../articles/resource-group-authoring-templates.md) (Azure Resource Manager-sablonok készítése) című témakörben talál.

A virtuálisgép-méretezési csoportokhoz tartozó példasablonkészlet az Azure-gyorssablonok GitHub-adattárában található itt:

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) – olyan sablonokat keressen, amelyek címében szerepel a *vmss* kifejezés.

E sablonok információs lapjain talál egy, a portálon keresztüli üzembe helyezési funkcióra mutató gombot. A virtuálisgép-méretezési csoport üzembe helyezéséhez kattintson erre a gombra, majd a portálon írjon be minden szükséges paramétert. Ha nem biztos abban, hogy egy erőforrás támogatja-e a kis- és nagybetűs karaktereket, célszerűbb mindig kisbetűs paraméterértékeket használni. Itt talál továbbá egy hasznos, részletekbe menő videót a virtuálisgép-méretezési csoportsablonról:

[VM Scale Set Template Dissection](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player) (Virtuálisgép-méretezési csoportsablon részletesen)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Virtuálisgép-méretezési csoport horizontális fel- és leskálázása
Ha egy Virtuálisgép-méretezési csoportban növelni vagy csökkenteni szeretné a virtuális gépek számát, módosítsa a *capacity* tulajdonság értékét, majd alkalmazza újra a sablont. Ezzel az egyszerű módszerrel könnyűszerrel írható meg egy saját testreszabott méretezési réteg, ha olyan egyedi méretezési eseményeket szeretne meghatározni, amelyeket nem támogat az Azure-alapú automatikus skálázás.

Ha a kapacitás módosításához újraalkalmaz egy sablont, akkor lehetőség van egy sokkal kisebb, csak a termékváltozatot és a frissített kapacitást tartalmazó sablon megadására. Erre itt talál példát: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Az automatikusan méretezett méretezési csoportok létrehozásának lépéseiről bővebben az [Automatically Scale Machines in a Virtual Machine Scale Set](../articles/virtual-machines/windows/vmss-powershell-creating.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Gépek automatikus méretezése egy virtuálisgép-méretezési csoportban) című témakörben olvashat.

## <a name="monitoring-your-vm-scale-set"></a>Virtuálisgép-méretezési csoport figyelése
Jelenleg az [Azure Resource Explorer](https://resources.azure.com) használatával javasoljuk a virtuálisgép-méretezési csoportok megtekintését. A virtuálisgép-méretezési csoportok a Microsoft.Compute erőforrásai, vagyis ezen a webhelyen tekintheti meg őket az alábbi hivatkozások kibontásával:

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Virtuálisgép-méretezési csoportok használatának esetei
Ez a szakasz a virtuálisgép-méretezési csoportok használatának néhány tipikus esetét sorolja fel. Ezek az esetek néhány magasabb szintű Azure-szolgáltatás (például a Batch, a Service Fabric, az Azure Container Service) használatakor merülhetnek fel.

* **RDP/SSH a virtuálisgép-méretezési csoport példányaihoz** – A rendszer egy virtuálisgép-méretezési csoportot hoz létre egy virtuális hálózaton belül, és az egyes virtuális gépekhez nem oszt ki nyilvános IP-címeket. Ez azért is lehet hasznos, mert a számítási gridben általában véve nem szeretné viselni az egyes IP-címek minden állapot nélküli erőforráshoz való kiosztásával járó költségeket és munkaterhelést, és a virtuális hálózatában lévő más erőforrásokról (köztük azokról, amelyek nyilvános IP-címekkel rendelkeznek, például a terheléselosztókról és a különálló virtuális gépekről) könnyűszerrel kapcsolódhat ezekhez a virtuális gépekhez.
* **Csatlakozás virtuális gépekhez NAT-szabályok használatával** – Létrehozhat egy nyilvános IP-címet, hozzárendelheti egy terheléselosztóhoz, majd bejövő NAT-szabályokat adhat meg, amelyek az IP-címen található portot hozzárendelik a virtuálisgép-méretezési csoportban lévő virtuális gépen található porthoz. Például
  
   Nyilvános IP->50000-es port -> vmss\_0->Port 22  Nyilvános IP->50001-es port -> vmms\_1->22 -es port  Nyilvános IP->50002-es port-> vmss\_2->22-es port
  
   Az alábbiakban egy példát láthat egy virtuálisgép-méretezési csoport létrehozására, amely NAT-szabályokkal engedélyezi az SSH-kapcsolatot az egyetlen nyilvános IP-címet használó méretezési csoportban lévő összes virtuális géphez: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Itt pedig egy másik példa ugyanerre az RDP és a Windows esetében: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Csatlakozás virtuális gépekhez „jumpbox” segítségével** – Ha ugyanabban a virtuális hálózatban hoz létre egy virtuálisgép-méretezési csoportot és egy különálló virtuális gépet, a különálló virtuális gép és a virtuálisgép-méretezési csoport virtuális gépei kapcsolódhatnak egymáshoz a virtuális hálózat/alhálózat által megadott belső IP-címeik használatával. Ha létrehoz egy nyilvános IP-címet, majd hozzárendeli a különálló virtuális géphez, RDP és SSH segítségével kapcsolódhat a különálló virtuális géphez, majd arról a gépről a virtuálisgép-méretezési csoport példányaihoz. Bizonyára észrevette, hogy egy egyszerű virtuálisgép-méretezési csoport eleve biztonságosabb, mint alapértelmezett konfigurációjában egy nyilvános IP-címmel rendelkező egyszerű különálló virtuális gép.
  
   Erre a megközelítésre például szolgál ez a sablon, amely létrehoz egy különálló fő virtuális gépből, valamint az általa kezelt, virtuálisgép-méretezési csoportokon alapuló virtuálisgép-fürtből álló, egyszerű Mesos-fürtöt: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)
* **Ciklikus időszeleteléses terheléselosztás a virtuálisgép-méretezési csoport példányaiban** – Ha virtuális gépek számítási fürtjére a ciklikus időszeletelés módszerével szeretne munkát továbbítani, lehetőség van egy Azure-terheléselosztó megfelelő terheléselosztási szabályokkal való konfigurálására. Mintavételeket is megadhat annak ellenőrzésére, hogy az alkalmazása a portok megadott protokollal, intervallummal és kérelmi útvonallal való pingelésével fut-e.
  
   Ez a példasablon IIS-webkiszolgálón futó virtuális gépek virtuálisgép-méretezési csoportját hozza létre, és terheléselosztó segítségével osztja el az egyes virtuális gépeket érő terhelést. Használja a HTTP protokollt is egy meghatározott URL-cím pingeléséhez az egyes virtuális gépeken: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) – tekintse meg a Microsoft.Network/loadBalancers erőforrástípust, valamint a networkProfile és extensionProfile profilt a virtualMachineScaleSet méretezési csoportban.
* **Virtuálisgép-méretezési csoport üzembe helyezése számítási fürtként egy PaaS-fürtkezelőben** – A virtuálisgép-méretezési csoportokat néha következő generációs feldolgozói szerepkörnek is nevezik. Ez egy valós leírás, de fennáll esetében annak a kockázata, hogy a méretezési csoport funkcióit összekeverik a PaaS v1 feldolgozói szerepkör funkcióival. A virtuálisgép-méretezési csoportok bizonyos értelemben valódi „feldolgozói szerepkört” vagy feldolgozói erőforrást biztosítanak, amelyben platform- és futásidő-független, testreszabható és az Azure Resource Manager IaaS szolgáltatásába integrálható általános számítási erőforrást nyújtanak.
  
   Bár egy PaaS v1-alapú feldolgozói szerepkör a platform-/futásidő-támogatás tekintetében korlátozott (csak Windows platformú lemezképekkel használható), magában foglal olyan szolgáltatásokat is, mint a virtuális IP-címek felcserélése, konfigurálható frissítési beállítások, futásidő- és alkalmazástelepítés-specifikus beállítások, amelyek vagy nem érhetők *még* el a virtuálisgép-méretezési csoportokban, vagy más magasabb szintű PaaS-szolgáltatások, például Service Fabric részeként vásárolhatók meg. Mindezt szem előtt tartva a virtuálisgép-méretezési csoportok PaaS-szolgáltatást támogató infrastruktúrának is tekinthetők. Vagyis a PaaS megoldások, például a Service Fabric vagy az olyan fürtkezelők, mint a Mesos, méretezhető számítási rétegként épülhetnek rá a virtuálisgép-méretezési csoportokra.
  
   Az alábbi példa egy Mesos-fürtöt mutat be, amely egy különálló virtuális gép által is használt virtuális hálózatban helyez üzembe egy virtuálisgép-méretezési csoportot. A különálló virtuális gép a Mesos-fürt gazdájának, a virtuálisgép-méretezési csoport tagjai pedig az alcsomópontoknak felelnek meg: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). A virtuálisgép-méretezési csoportokra építve az [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) későbbi verziói e forgatókönyv összetettebb és jobban megerősített verzióit fogják alkalmazni.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>A virtuálisgép-méretezési csoportok teljesítményével és skálázásával kapcsolatos útmutató
* A nyilvános előzetes verzió során több virtuálisgép-méretezési csoportban ne hozzon létre egyszerre 500 virtuális gépnél többet.
* Tárfiókonként legfeljebb 40 virtuális géppel tervezzen.
* Minél tágabb tartományból válassza ki a tárfiókok nevének kezdőbetűit.  Az [Azure Quickstart templates](https://github.com/Azure/azure-quickstart-templates/) (Azure gyorsindítási sablonok) webhelyen található, virtuálisgép-méretezési csoportokkal kapcsolatos példasablonok számos példát szolgáltatnak e művelet elvégzésnek módjára.
* Ha egyéni virtuális gépeket használ, egyetlen tárfiókban legfeljebb csak 40 virtuális gépet tervezzen virtuálisgép-méretezési csoportokként.  Mielőtt elkezdhetné a virtuálisgép-méretezési csoport üzembe helyezését, a tárfiókba kell másolnia a lemezképet. További információkat a gyakori kérdések között találhat.
* Virtuális hálózatonként legfeljebb csak 2048 virtuális gépet tervezzen.  A későbbiekben ez a korlátozás enyhébb lesz.
* A létrehozható virtuális gépek számának bármelyik régióban a számítási magkvóta szab határt. Előfordulhat, hogy kapcsolatba kell lépnie az ügyfélszolgálattal a számítási kvótahatár további növelése érdekében, még akkor is, ha a felhőszolgáltatásokhoz vagy az IaaS v1-szolgáltatáshoz használt magok esetében magas a határérték. A kvóta lekérdezéséhez futtathatja az *azure vm list-usage* Azure CLI-parancsot, vagy a *Get-AzureRmVMUsage* PowerShell-parancsot (ha a PowerShell 1.0-nál korábbi verziót használ, használja a *Get-AzureVMUsage* parancsot).

## <a name="vm-scale-set-frequently-asked-questions"></a>A virtuálisgép-méretezési csoportokkal kapcsolatos gyakori kérdések
**K.** Hány virtuális gépet tartalmazhat egy virtuálisgép-méretezési csoport?

**V.** 100-at, ha több tárfiók között elosztható platformlemezképeket használ. Ha egyéni lemezképeket használ, legfeljebb 40-et vegyen igénybe, mivel az egyéni lemezképek az előzetes verzió ideje alatt egyetlen tárfiókra korlátozódnak.

**K.** Milyen egyéb erőforráskorlátok vonatkoznak a virtuálisgép-méretezési csoportokra?

**V.** Az előzetes verzió időszaka alatt régiónként legfeljebb 500 virtuális gépet hozhat létre több méretezési csoportban. A mindenkori [Azure-előfizetési szolgáltatási korlátok/](../articles/azure-subscription-service-limits.md) irányadók.

**K.** Támogatott az adatlemezek használata a virtuálisgép-méretezési csoportokon belül?

**V.** Az eredeti kiadásban nem. A következő adattárolási lehetőségek állnak rendelkezésre:

* Azure Files (SMB-megosztásos meghajtók)
* Operációs rendszer meghajtója
* Ideiglenes meghajtó (helyi, nem Azure Storage-alapú)
* Külső adatszolgáltatás (például távoli adatbázis, Azure-táblák, Azure-blobok)

**K.** Mely Azure-régiók támogatják a virtuálisgép-méretezési csoportokat?

**V.** A virtuálisgép-méretezési csoportokat minden olyan régió támogatja, amely támogatja az Azure Resource Managert.

**K.** Hogyan lehet egyéni lemezképekből virtuálisgép-méretezési csoportokat létrehozni?

**V.** Hagyja üresen a vhdContainers tulajdonságot, például:

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**K.** Ha a virtuálisgép-méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

**V.** A rendszer egyenlő arányban távolítja el a virtuális gépeket a méretezési csoportból a frissítési és tartalék tartományok egészében, a rendelkezésre állás maximalizálása érdekében.

**K.** Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

**V.** Ha 18-ra növeli a kapacitást, akkor létrejönnek a 15-ös, 16-os és 17-es indexekkel rendelkező virtuális gépek. A virtuális gépek mindkét esetben a tartalék és frissítési tartományok között oszlanak el.

**K.** Ha több bővítményt használok egy virtuálisgép-méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

**V.** Közvetlenül nem, bár a customScript bővítmény esetében a szkript megvárhatja egy másik bővítmény futtatásának befejezését (például a bővítménynapló figyelésével – lásd: [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**K.** Használhatók virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

**V.** Igen. A virtuálisgép-méretezési csoport egy implicit rendelkezésre állási csoport 3 tartalék és 5 frissítési tartománnyal. A virtualMachineProfile alatt nincs szükség további konfigurálásra. A jövőbeli kiadásokban valószínűsíthető, hogy a virtuálisgép-méretezési csoportok több bérlőre terjednek majd ki, de ez idő szerint egy méretezési csoport csak egyetlen rendelkezésre állási csoportot jelent.

