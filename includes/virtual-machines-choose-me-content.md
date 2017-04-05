<a name="tellmevm"></a>

## <a name="tell-me-about-virtual-machines"></a>Tudnivalók a virtuális gépekről
Az Azure Virtual Machines modullal virtuális gépeket hozhat létre és használhat a felhőben. A *Szolgáltatásként kínált infrastruktúra (IaaS)* néven ismert koncepciónak köszönhetően a virtuális gépek technológiája számos különböző módon használható. Néhány példa:

* **Fejlesztés és tesztelés virtuális gépek használatával.** A fejlesztői csoportok gyakran használnak virtuális gépeket, mert gyors és egyszerű módot biztosítanak egy alkalmazás programozásához és teszteléséhez szükséges adott konfigurációjú számítógépek létrehozásához. Az Azure Virtual Machines célirányos és gazdaságos módot biztosít a virtuális gépek létrehozásához, használatához, majd, ha már nincs szükség rájuk, a törlésükhöz.
* **Alkalmazások futtatása a felhőben.** Gazdaságossági megfontolásból érdemes lehet néhány alkalmazást a nyilvános felhőben futtatni. Igaz lehet ez egy olyan alkalmazás esetében, amely időnként kiugróan magas teljesítményt igényel. Ugyan felszerelheti a saját adatközpontját a magas teljesítményigényeknek megfelelő hardverekkel, azonban azok az idő legnagyobb részében kihasználatlanok maradnának. Ezzel az alkalmazással az Azure lehetővé teszi, hogy csak akkor fizessen az extra virtuális gépekért, amikor szüksége van rájuk, és leállítsa őket, amikor nincs. Vagy tegyük fel, hogy egy olyan start-up vállalkozást vezet, amelynek igény szerinti számítási erőforrásra van szüksége gyorsan, elkötelezettség nélkül. Ebben az esetben is az Azure lehet a megfelelő választás.
* **A saját adatközpont kiterjesztése a nyilvános felhőbe.** Az Azure Virtual Network használatakor a szervezete létrehozhat egy virtuális hálózatot (VNET), amely a saját helyszíni hálózat kiterjesztése, és hozzáadhat virtuális gépeket a virtuális hálózathoz. Ez lehetővé teszi, hogy olyan alkalmazásokat futtasson egy Azure virtuális gépen, mint például a [SharePoint](../articles/virtual-machines/windows/sharepoint-farm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), az [SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) és egyebek. Ez a megközelítés könnyebben üzembe helyezhető vagy kevésbé költséges lehet, mint ha a saját adatközpontjában lévő virtuális gépeken futtatná őket.   
* **Vészhelyreállítás** Ahelyett, hogy folyamatosan fizetne egy biztonsági mentési adatközpontért, amelyet ritkán használ, az IaaS-alapú vészhelyreállítással csak akkor kell fizetnie a számítási erőforrásokért, amikor tényleg szüksége van rájuk.  Ha például az elsődleges adatközpont leáll, létrehozhat az Azure-on futó virtuális gépeket a létfontosságú alkalmazások futtatásához, majd leállíthatja őket, amikor már nincs rájuk szüksége.

Az egyéb virtuális gépekhez hasonlóan egy Azure-ban futó virtuális gépen is van operációs rendszer, tárhely és hálózati képességek, valamint számos különböző alkalmazás futtatására alkalmas. Használhat az Azure vagy egy partnere által biztosított rendszerképet, vagy használhatja a sajátját. A példák között számos különböző verziót, kiadást és konfigurációt talál:

* Linux kiszolgálók, mint például a Suse, az Ubuntu és a CentOS
* Windows Server 
* SQL Server
* BizTalk Server 
* SharePoint Server

A virtuális gépek virtuális merevlemezeken (VHD-k) tárolják az operációs rendszereket és az adatokat. A VHD-ken találhatók az operációs rendszer telepítéséhez kiválasztható rendszerképek is. Az alábbi ábra ezt mutatja be, valamint két eszközt a virtuális gépek létrehozásához és felügyeletéhez.

<a name="fig_createvms"></a>
![vm_diagram](./media/virtual-machines-choose-me-content/diagram.png)

**Ábra: Az Azure Virtual Machines szolgáltatásként kínált infrastruktúrát (IaaS) biztosít.**

A virtuális gépek felügyelhetők egy böngészőalapú portállal, szkriptelést támogató parancssori eszközökkel, vagy közvetlenül a REST API-n keresztül. A Microsoft bizonyos partnerei, például a RightScale és a ScaleXtreme REST API-n alapuló felügyeleti szolgáltatásokat is biztosítanak. 

Az operációs rendszer mellett a virtuális gépeken elérhető egyéb konfigurációs lehetőségek:

* A méret, amely meghatároz olyan tényezőket, mint a csatlakoztatható lemezek számát és a feldolgozási kapacitást. Az Azure számos különböző méretet kínál különféle felhasználási módokhoz. Részletek: [Virtuális gépek méretei](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  
* Az az Azure-régió, ahol az új virtuális gép üzemelni fog, például az Egyesült Államok, Európa vagy Ázsia. 
* Virtuálisgép-bővítmények, amelyek segítségével a virtuális gép további képességeket kaphat, például képes lesz vírusirtók futtatására vagy a Windows PowerShell célállapot-konfiguráció szolgáltatásának használatára.

A virtuális gépek által biztosított egyéb előnyök:

**Használatalapú fizetés** – Az Azure óradíjat számol fel a virtuális gép mérete és az operációs rendszer alapján. A megkezdett órák esetében az Azure percalapú elszámolást biztosít. A tárhely árazása és felszámítása külön történik. Részletek: [A Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/).

**Rugalmasság** – Az Azure megfigyeli az egyes virtuális gépeket futtató fizikai hardvert. Ha egy virtuális gépet futtató fizikai kiszolgáló leáll, az Azure észleli ezt, és a virtuális gépet új hardverre költözteti, majd újraindítja. Ezt a folyamatot nevezik néha szolgáltatásjavításnak. Az Azure védi a virtuális gép adatait is, ennek érdekében redundáns másolatokat tart a virtuális merevlemezről a Blob Storage-ban. 

