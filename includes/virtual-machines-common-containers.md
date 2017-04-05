Az Azure felhőmegoldásai virtuális gépekre (fizikai számítógép hardverelemeinek emulációira) épülnek, ami lehetőséget teremt a szoftvertelepítések gyors csomagolására, illetve a fizikai hardvereknél jobb erőforrás-konszolidációra. A [Docker](https://www.docker.com)-tárolók és -ökoszisztéma révén lényegesen szélesebb körben lehet képes az elosztott szoftverek fejlesztésére, kiadására és kezelésére. A tárolók alkalmazáskódjai el vannak különítve a virtuális gazdagéptől, illetve az ugyanazon virtuális gépen található más tárolóktól. Ez az elkülönítés gyorsabb fejlesztést és üzembe helyezést tesz lehetővé.

Az Azure a következő, a Dockerre jellemző előnyöket biztosítja:

* [Számos](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) [különböző](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), az adott helyzetnek megfelelő lehetőség tárolók Docker-gazdagépeinek létrehozására.
* Az [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/) vezénylők alkalmazásával olyan tárológazdagép-fürtöket hoz létre, mint például a **Marathon** és a **Swarm**.
* Az [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) és [erőforráscsoport-sablonok](../articles/resource-group-authoring-templates.md) segítségével a legösszetettebb elosztott alkalmazások üzembe helyezése és frissítése is leegyszerűsíthető.
* Integráció jogvédett és nyílt forráskódú konfigurációkezelési eszközök széles választékával.

Mivel az Azure-on programozás útján hozhatók létre virtuális gépek és Linux-tárolók, így virtuálisgép- és tároló*vezénylési* eszközökkel virtuálisgép-csoportokat is létrehozhat, valamint alkalmazásokat helyezhet üzembe Linux-tárolókban, illetve mostantól [Windows-tárolókban](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) is.

A jelen cikk nem csupán ezeket a fogalmakat tárgyalja magas szinten, de számos olyan, további információkra, oktatóanyagra és termékre mutató hivatkozást is magában foglal, amelyek az Azure-on történő tároló- és fürthasználathoz kapcsolódnak. Ha már tisztában van a cikkben ismertetett információkkal, és csak a hivatkozásokra van szüksége, azokat [a tárolókkal folytatott munkavégzéshez kapcsolódó eszközöket](#tools-for-working-with-azure-vms-and-containers) felsoroló résznél találja.

## <a name="the-difference-between-virtual-machines-and-containers"></a>Mi a különbség a virtuális gépek és a tárolók között?
A virtuális gépek egy [hipervizor](http://en.wikipedia.org/wiki/Hypervisor) által biztosított, elkülönített hardvervirtualizálási környezetben futnak. Az Azure-on a kezelésüket Ön helyett a [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/) szolgáltatás végzi – a virtuális gépek létrehozásához Ön kiválasztja és konfigurálja az operációs rendszert, &mdash;vagy feltölt egy egyéni virtuálisgép-rendszerképet. A Virtual Machines egy olyan, folyamatosan tesztelt és fejlesztett technológiát alkalmazó szolgáltatás, amely számos eszközt biztosít az operációs rendszerek és az azokon futó alkalmazások kezeléséhez.  A virtuális gépeken futó alkalmazások a gazdagép operációs rendszere elől rejtve vannak. A virtuális gépen futó alkalmazás vagy felhasználó szempontjából a virtuális gép autonóm fizikai számítógépként jelenik meg.

A [Linux-tárolók](http://en.wikipedia.org/wiki/LXC) és a Docker-eszközökkel létrehozott és üzemeltetett tárolók az elkülönítéshez nem használnak hipervizort. A tárolók esetében a tárológazdagép a Linux-kernel fájlrendszer-elkülönítési funkcióit és -folyamatát úgy alkalmazza, hogy a tároló a saját alkalmazásai számára a kernel bizonyos funkcióit és a saját, elkülönített fájlrendszerét teszi elérhetővé. A tárolóban futtatott alkalmazások szempontjából a tároló egyedi operációsrendszer-példányként jelenik meg. A tárolóban található alkalmazások nem láthatják a tárolón kívüli folyamatokat vagy erőforrásokat.

A Docker-tárolók sokkal kevesebb erőforrást használnak, mint a virtuális gépek. A Docker-tárolók egy olyan alkalmazáselkülönítési és -végrehajtási modellt alkalmaznak, amely nem osztozik a Docker-gazdagép kernelén. A tároló sokkal kevesebb helyet foglal el a lemezen, mivel nem tartalmazza a teljes operációs rendszert. Az indítási idő és az elfoglalt lemezterület sokkal kevesebb, mint a virtuális gépek esetében.
A Windows-tárolók ugyanazokat az előnyöket biztosítják, mint a Windows rendszeren futtatott alkalmazások Linux-tárolói. A Windows-tárolók támogatják a Docker rendszerképformátumát és API-ját, de ezeket továbbra is lehet a PowerShell-lel is kezelni. Windows-tárolók, Windows Server-tárolók és Hyper-V-tárolók esetében kétféle tároló-futtatókörnyezet érhető el. A Hyper-V-tárolók egy további elkülönítési réteget biztosítanak azáltal, hogy az egyes tárolókat nagymértékben optimalizált virtuális gépeken üzemeltetik. A Windows-tárolókra vonatkozó további információkat a [Windows-tárolókat ismertető](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) cikk tartalmaz. Ha el szeretné kezdeni az Azure Windows-tárolóinak használatát, lásd az [Azure Container Service-fürt üzembe helyezését](/articles/container-service/container-service-deployment.md) ismertető cikket.

## <a name="what-are-containers-good-for"></a>Mire jók a tárolók?

A tárolók képesek javítani:

* Az alkalmazáskódok fejlesztésének és széles körben történő megosztásának sebességét
* Az alkalmazások tesztelésének sebességét és magabiztosságát
* Az alkalmazások üzembe helyezésének sebességét és magabiztosságát

A tárolók operációs rendszert&mdash;üzemeltető tárolón futnak, ami pedig az Azure-ban egy Azure-alapú virtuális gépet jelent. Még ha vonzónak is találja a tárolók használatának gondolatát, az üzemeltetésükhöz virtuálisgép-infrastruktúrára is szüksége lesz. Könnyebbséget jelent azonban, hogy a tárolók bármely virtuális gépen futtathatók (bár azt például figyelembe kell venni, hogy az adott tároló Linux- vagy Windows-alapú végrehajtási környezetet igényel-e).


## <a name="what-are-containers-good-for"></a>Mire jók a tárolók?
Számos dologra, a tárolók ugyanakkor&mdash; – az [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) és az [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md)&mdash; platformokhoz hasonlóan – olyan egyetlen szolgáltatást kezelő, mikroszolgáltatás-orientált elosztott alkalmazások létrehozására adnak módot, amelyekben az alkalmazások tervezése több kicsi, egymáshoz illeszthető részegységen, és nem pedig nagyobb, szoros kapcsolatban lévő összetevőn alapul.

Ez különösen az Azure-hoz hasonló, nyilvános felhőkörnyezetekben igaz, ahol szükség szerinti időpontban és helyen kölcsönözhet virtuális gépeket. Így nem csupán elkülönítési lehetőséghez, illetve gyors üzembe helyezési és vezénylési eszközökhöz juthat, de hatékonyabb döntéseket is hozhat az alkalmazás-infrastruktúrával kapcsolatban.

Az aktuális környezet egy magas rendelkezésre állású elosztott alkalmazás esetében például állhat 9 nagyméretű Azure virtuális gépből. Ha ennek az alkalmazásnak az összetevői tárolókban telepíthetők, akkor megoldható, hogy a redundancia és a terheléselosztás optimalizálása érdekében csupán 4 virtuális gépet használjon, és az alkalmazás összetevőit 20 tárolóba telepítse.

Ez persze csak egy példa, de az adott helyzetben a használati igényekhez több Azure virtuális gép helyett több tároló alkalmazásával igazodhat, a fennmaradó teljes CPU-terhelést pedig sokkal hatékonyabban használhatja ki, mint bármikor korábban.

Számos olyan alkalmazási helyzet is létezik, amely nem követi a mikroszolgáltatási megközelítést; leginkább Ön tudja eldönteni, mikor érdemes mikroszolgáltatásokat és a tárolókat használnia.

### <a name="container-benefits-for-developers"></a>A tárolók használatából származó előnyök fejlesztők számára
Általánosságban nézve könnyű belátni, hogy a tárolókon alapuló technológia a jövő útja, ugyanakkor számos, ennél specifikusabb előnnyel is bírnak. Vegyük például a Docker-tárolókat. Ez a témakör nem tárgyalja részletesen a Dockert (erre vonatkozóan lásd a [What is Docker?](https://www.docker.com/whatisdocker/) (Mi az a Docker?) weboldalt vagy a [Wikipédiát](http://wikipedia.org/wiki/Docker_%28software%29)), elmondható ugyanakkor, hogy a Docker és ökoszisztémájának használata számtalan előnnyel jár a fejlesztők és az informatikai szakemberek számára.

A fejlesztők általában szeretnek a Docker-tárolókkal dolgozni, mivel azok mindenekelőtt leegyszerűsítik a Linux- és Windows-tárolók használatát:

* A könnyen üzembe helyezhető, rögzített rendszerképek létrehozásához egyszerű, egymásra épülő parancsokat használhatnak, valamint Docker-fájlokkal automatizálhatják ezen rendszerképek létrehozását.
* Egyszerű, [git](https://git-scm.com/) stílusú leküldési és lekérési parancsok használatával könnyedén meg is oszthatják a rendszerképeket [nyilvános](https://registry.hub.docker.com/) vagy [magán Docker-beállításjegyzékekben](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Számítógépek helyett elkülönített alkalmazás-összetevőkben gondolkodhatnak.
* Számos olyan eszközt használhatnak, amelyek támogatják a Docker-tárolókat és az alapul szolgáló különféle rendszerképeket.

### <a name="container-benefits-for-operations-and-it-professionals"></a>A tárolók használatából származó előnyök üzemeltetők és informatikai szakemberek számára
A tárolók és a virtuális gépek együttes használata az informatikai és üzemeltetési szakemberek számára is számos előnnyel jár.

* A tartalmazott szolgáltatások elkülönülnek a virtuálisgép-gazda végrehajtási környezetétől.
* A tartalmazott kód azonossága igazolható.
* A tartalmazott szolgáltatások elindíthatók, leállíthatók, valamint gyorsan áthelyezhetők az egyes fejlesztési, tesztelési és éles környezetek között.

Az ilyen&mdash;és ehhez&mdash;hasonló szolgáltatások rendkívüli előnnyel járnak olyan vállalkozások számára, amelyeknek a professzionális informatikai technológiát alkalmazó részlegei igyekeznek az erőforrásokat&mdash;(beleértve a feldolgozási teljesítményt is) az üzletmenet fenntartásához,&mdash;valamint az ügyfelek elégedettségének fokozásához és új ügyfelek szerzéséhez szükséges feladatok igényeihez szabni. A kisvállalkozások, a független szoftvergyártók és az induló cégek, még ha más köntösbe bújtatva is, de ugyanezekkel a követelményekkel találkoznak.

## <a name="what-are-virtual-machines-good-for"></a>Mire jók a virtuális gépek?
A felhőalapú számítógép-használat hátterét mindig is a virtuális gépek adták, és ez manapság is így van. Még ha a virtuális gépek indítása hosszabb ideig is tart, nagyobb lemezterületet foglalnak, és nem képezhetők le közvetlenül mikroszolgáltatási architektúrára, akkor is lényeges előnyökkel bírnak:

1. Alapesetben sokkal nagyobb teljesítményű alapértelmezett védelmet biztosítanak a gazdagépek számára.
2. Az összes fő operációsrendszer- és alkalmazás-konfigurációt támogatják.
3. Bevált eszköz-ökoszisztémákkal rendelkeznek a parancsokhoz és a vezérléshez.
4. Végrehajtási környezetet biztosítanak a tárolók üzemeltetéséhez.

Az utóbbi jellemző különösen fontos, mivel a tartalmazott alkalmazásokhoz továbbra is adott, az általuk végrehajtott hívásoktól függő operációs rendszerre és processzortípusra van szükség. Fontos megjegyezni, hogy azért telepítünk tárolókat a virtuális gépeken, mert ezek tartalmazzák az üzembe helyezni kívánt alkalmazásokat; a tárolók nem helyettesítik a virtuális gépeket vagy az operációs rendszereket.

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>A virtuális gépek és a tárolók magas szintű szolgáltatásainak összehasonlítása
Az alábbi táblázat a virtuális gépek és a Linux-tárolók közötti, különösebb pluszmunka&mdash;nélkül megvalósítható szolgáltatás-különbségek&mdash;természetét részletezi. Vegye figyelembe, hogy az alkalmazás szükségleteitől függően egyes szolgáltatások jobban vagy kevésbé kívánatosak lehetnek, és, ahogy bármely egyéb szoftver esetében, a pluszmunka fokozott szolgáltatástámogatást eredményez, különösen a biztonság területén.

| Szolgáltatás | Virtuális gépek | Tárolók |
|:--- | --- | --- |
| „Alapértelmezett” biztonsági támogatás |magasabb szintű |valamelyest kisebb szintű |
| Szükséges lemezmemória mennyisége |Teljes OS plusz az alkalmazások |Csak alkalmazáskövetelmények |
| Rendszerindításhoz szükséges idő |Jelenősen hosszabb: az operációs rendszer indítása és az alkalmazások betöltése |Jelentősen rövidebb: mivel a kernel már fut, csak az alkalmazásokat kell elindítani |
| Hordozhatóság |A hordozhatóság megfelelő előkészítés mellett biztosított |A hordozhatóság az adott képformátumon belül biztosított; jellemzően kisebb mértékű |
| Rendszerkép-automatizálás |Az operációs rendszertől és az alkalmazásoktól függően nagyban változó |[Docker-beállításjegyzék](https://registry.hub.docker.com/); egyebek |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>Virtuálisgép- és tárolócsoportok létrehozása és kezelése
Ezen a ponton a tervezők, a fejlesztők vagy az informatikai üzemeltetési szakemberek azt gondolhatják: „Ezt az EGÉSZET automatizálhatom; ez TÉNYLEG egy szolgáltatásként biztosított adatközpont!”.

Igazuk is van, tényleg megtehetik. Számos olyan rendszer van (ezek közül sokkal akár találkoztak is), amelyek képesek Azure virtuális gépek csoportjainak kezelésére és egyéni kódok beszúrására szkriptekkel, leggyakrabban a [CustomScriptingExtension bővítmény (Windows rendszeren)](https://msdn.microsoft.com/library/azure/dn781373.aspx) vagy a [CustomScriptingExtension bővítmény (Linux rendszeren)](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) használatával. A&mdash;PowerShell-lel vagy az Azure CLI-szkriptekkel&mdash;elvégezheti, és talán már el is végezte az Azure-környezetek automatizálását.

Ezek a képességek aztán olyan, a virtuális gépek tömeges létrehozásának és konfigurálásának automatizálására szolgáló eszközökbe telepíthetők át, mint például a [Puppet](https://puppetlabs.com/) és a [Chef](https://www.chef.io/). (Néhány hivatkozás [ezen eszközök használatával kapcsolatban az Azure-ban](#tools-for-working-with-containers).)

### <a name="azure-resource-group-templates"></a>Azure erőforráscsoport-sablonok
Az Azure mostanában adta ki az [Azure erőforrás-kezelés](../articles/resource-manager-deployment-model.md) REST API-ját, illetve az egyszerűbb használat érdekében frissítette a PowerShellt és az Azure CLI-t. Teljes alkalmazástopológiákat helyezhet üzembe, módosíthat vagy telepíthet újra az [Azure Resource Manager-sablonokkal](../articles/resource-group-authoring-templates.md) és az Azure erőforrás-kezelés API-jával, illetve a következők segítségével:

* a [sablonokat használó Azure Portal](https://github.com/Azure/azure-quickstart-templates)&mdash; – tipp: használja a „DeployToAzure” gombot;
* az [Azure CLI](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json);
* az [Azure PowerShell-modulok](../articles/virtual-machines/linux/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>Azure virtuális gépek és tárolók teljes csoportjainak üzembe helyezése és kezelése
Számos népszerű rendszeren helyezhet üzembe teljes virtuálisgép-csoportokat és telepítheti a Dockert (vagy egyéb, Linux-tárolókat üzemeltető rendszert) automatizálható csoportként. Közvetlen hivatkozásokat az alábbi, [tárolókkal és eszközökkel](#containers-and-vm-technologies) foglalkozó szakaszban talál. Kisebb vagy nagyobb mértékben számos további rendszer is képes kezelni az ilyen üzembe helyezéseket, a lista tehát nem tekinthető teljesnek. A lista egyes elemeinek hasznossága a felhasználó készségeitől és az adott alkalmazási helyzettől függ.

A Docker saját virtuálisgép-létrehozási ([docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)), valamint a Docker és a tároló közötti terheléselosztást vezérlő fürtkezelő eszközökkel ([Swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) is rendelkezik. Ezen kívül az [Azure Docker VM-bővítmény](https://github.com/Azure/azure-docker-extension/blob/master/README.md) alapértelmezés szerint támogatja a [`docker-compose`](https://docs.docker.com/compose/) parancsot, amellyel a konfigurált alkalmazástárolók több tárolón is üzembe helyezhetők.

Mindezek mellett kipróbálhatja a [Mesosphere Data Center Operating System (DCOS)](http://docs.mesosphere.com/install/azurecluster/) rendszerét is. A DCOS alapjául a nyílt forráskódú [Mesos](http://mesos.apache.org/) „elosztottrendszer-kernel” szolgál, amelynek segítségével adatközpontját egyetlen címezhető szolgáltatásként kezelheti. A DCOS több fontos rendszerhez (többek között a [Sparkhoz](http://spark.apache.org/) és a [Kafkához](http://kafka.apache.org/)) való beépített csomagokkal, valamint beépített szolgáltatásokkal (többek között a [Marathon](https://mesosphere.github.io/marathon/) tárolóvezérlő rendszerrel és a [Chronos](https://mesos.github.io/chronos/) elosztott ütemezővel) rendelkezik. A Mesost a Twitter, az AirBnb és egyéb webes vállalkozások tapasztalatai alapján fejlesztették ki. A **Swarm** vezénylési motorként is használható.

A [Kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) szintén egy nyílt forráskódú virtuálisgép- és tárolócsoport-kezelési rendszer, amely a Google tapasztalatai alapján lett összeállítva. [A megfelelő hálózati támogatás biztosításához a Kubernetes és a Weave együtt](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave) is használható.

A [Deis](http://deis.io/overview/) egy olyan nyílt forráskódú „szolgáltatásként nyújtott platform” (PaaS), amelynek segítségével leegyszerűsíthető az alkalmazások üzembe helyezése és kezelése a saját kiszolgálóin. Egy egyszerűsített, Heroku típusú munkafolyamattal rendelkező PaaS biztosításához a Deis a Docker és a CoreOS platformokra épül. Így egyszerűen [létrehozhat egy 3 csomópontot tartalmazó Azure virtuálisgép-csoportot és telepítheti a Deist](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) az Azure-on, majd [telepíthet egy Hello World Go alkalmazást](../articles/virtual-machines/linux/deis-cluster.md#deploy-and-scale-a-hello-world-application).

A [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html), egy optimalizált tárhelyszükségletű, Docker-támogatással és saját, [rkt](https://github.com/coreos/rkt) nevű tárolórendszerrel ellátott Linux-disztribúció is rendelkezik egy [Fleet](https://coreos.com/using-coreos/clustering/) nevű tárolócsoport-kezelési eszközzel.

Az Ubuntu (egy másik nagyon népszerű Linux-disztribúció) széles körű támogatást biztosít a Dockernek, ugyanakkor a [Linux- (LXC stílusú) fürtöket](https://help.ubuntu.com/lts/serverguide/lxc.html) is támogatja.

## <a name="tools-for-working-with-azure-vms-and-containers"></a>Az Azure virtuális gépek és tárolók használatához szükséges eszközök
A tárolók és az Azure virtuális gépek használatához különböző eszközökre van szükség. Ez a szakasz csupán a leghasznosabb vagy legfontosabb fogalmakat és eszközöket ismerteti a tárolókkal, csoportokkal és a velük együtt használt nagyobb konfigurációs és vezénylési eszközökkel kapcsolatban.

> [!NOTE]
> Ez a terület rendkívüli iramban fejlődik, és bár mindent megteszünk a témakör és a hivatkozások naprakészen tartásáért, ez néha lehetetlen feladatnak tűnik. Rendszeresen nézzen körül az Önt érdeklő tartalmak között, hogy tudása naprakész maradhasson!
>
>

### <a name="containers-and-vm-technologies"></a>Tárolókkal és virtuális gépekkel kapcsolatos technológiák
Példák Linux-tárolókhoz kapcsolódó technológiákra:

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS és rkt](https://github.com/coreos/rkt)
* [Open Container projekt](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Hivatkozások Windows-tárolókhoz:

* [Windows-tárolók](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Hivatkozások a Visual Studio Dockerhez:

* [Visual Studio 2015 RC Tools for Docker – Előzetes verzió](https://visualstudiogallery.msdn.microsoft.com/6f638067-027d-4817-bcc7-aa94163338f0)

Docker-eszközök:

* [Docker-démon](https://docs.docker.com/installation/#installation)
* Docker-ügyfelek
  * [Windows Docker-ügyfél a Chocolatey-n](https://chocolatey.org/packages/docker)
  * [Docker – Telepítési utasítások](https://docs.docker.com/installation/#installation)

A Docker a Microsoft Azure-on:

* [Docker VM-bővítmény Linuxhoz az Azure-on](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Docker VM-bővítmény – Használati útmutató](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [A Docker VM-bővítmény használata az Azure parancssori felületével (Azure CLI)](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Az Azure Portal Docker VM-bővítményének használata](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [A docker-machine használata az Azure-on](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Swarm szolgáltatással kiegészülő Docker használata az Azure-on](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Bevezetés a Docker és a Docker Compose használatába az Azure-on](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Docker-gazdagép gyors létrehozása Azure erőforráscsoport-sablonnal az Azure-on](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [Beépített `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys)-támogatás a tartalmazott alkalmazásokhoz
* [Magán Docker-beállításjegyzék létrehozása az Azure-on](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Linux-disztribúciók és példák az Azure-on:

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

Konfigurálás, fürtfelügyelet és tárolóvezénylés:

* [CoreOS-flotta](https://coreos.com/using-coreos/clustering/)
* Deis

  * [3 csomópontot tartalmazó Azure virtuálisgép-csoport létrehozása, a Deis telepítése és a Hello World Go alkalmazás elindítása](../articles/virtual-machines/linux/deis-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Kubernetes

  * [Teljes útmutató az automatizált Kubernetes-fürtök üzembe helyezéséhez a CoreOS és a Weave használatával](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes megjelenítő](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Mesosphere Data Center Operating System (DCOS)](http://beta-docs.mesosphere.com/install/azurecluster/)
* [Jenkins](https://jenkins-ci.org/) és [Hudson](http://hudson-ci.org/)

  * [Blog: Jenkins alárendelt beépülő modul Azure-hoz](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
  * [GitHub-tár: Jenkins tároló beépülő modul Azure-hoz](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [Külső gyártó: Hudson alárendelt beépülő modul Azure-hoz](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [Külső gyártó: Hudson tároló beépülő modul Azure-hoz](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Azure Automation](https://azure.microsoft.com/services/automation/)

  * [Videó: Az Azure Automation használata Linux rendszerű virtuális gépekkel](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* PowerShell DSC Linux-hoz

  * [Blog: PowerShell DSC végrehajtása Linuxhoz](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub: DSC Docker-ügyfelekhez](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>Következő lépések
Tekintse meg a [Docker](https://www.docker.com) webhelyét és a [Windows-tárolókkal](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview) foglalkozó témakört.

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
