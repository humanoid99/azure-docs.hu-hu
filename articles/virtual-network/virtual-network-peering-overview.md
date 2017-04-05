---
title: "Társviszony kialakítása virtuális hálózatok között az Azure-ban | Microsoft Docs"
description: "Tudnivalók az Azure-beli virtuális hálózatok közötti társviszony-létesítésről"
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>Társviszony létesítése virtuális hálózatok között
A virtuális hálózatok (VNet) közötti társviszony lehetővé teszi, hogy az Azure gerinchálózatán keresztül összekapcsoljon két – azonos régión belüli – virtuális hálózatot. A társviszony kialakítását követően a két virtuális hálózat a csatlakozás szempontjából egyetlen hálózatnak látszik. A két virtuális hálózat kezelése továbbra is külön erőforrásként történik, de a virtuális társhálózatokba tartozó virtuális gépek közvetlenül, magánhálózati IP-címekkel kommunikálhatnak egymással.

A virtuális társhálózatokba tartozó virtuális gépek közötti forgalmat lényegében ugyanúgy továbbítja a rendszer az Azure infrastruktúráján keresztül, mint az azonos virtuális hálózatba tartozó virtuális gépek között. A virtuális társhálózatok előnyei egyebek között a következők:

* Kis késésű, nagy sávszélességű kapcsolat jön létre eltérő virtuális hálózatokba tartozó erőforrások között.
* A virtuális társhálózatokban lévő erőforrások (például hálózati berendezések és VPN-átjárók) átviteli pontként használhatók.
* Két virtuális hálózat közötti társviszony létrehozása az Azure Resource Manager-alapú üzemi modellel vagy egy, a Resource Managerrel létrehozott virtuális hálózat és egy, a klasszikus üzemi modellel létrehozott virtuális hálózat társviszonyba állítása. A két Azure üzembehelyezési modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md) című cikket.

A virtuális hálózatok közötti társviszony-létesítés követelményei és fontos szempontjai:

* A virtuális társhálózatoknak ugyanabban az Azure-régióban kell lenniük.
* A virtuális társhálózatok IP-címterei nem fedhetik egymást.
* A virtuális hálózati társviszony két virtuális hálózat között jön létre, de nem származik belőle tranzitív kapcsolat a tárviszonyok között. Ha például az A virtuális hálózat társviszonyban áll a B virtuális hálózattal, és a B hálózat társviszonyban áll a C virtuális hálózattal, az *nem* jelenti azt, hogy társviszony van az A virtuális hálózat és a C virtuális hálózat között.
* Társviszonyt létesíthet két különböző előfizetésben található virtuális hálózat között, ha a társviszony-létesítést mindkét előfizetés esetében engedélyezi egy rendszergazdai engedéllyel rendelkező felhasználó, és az előfizetések ugyanahhoz az Active Directory-bérlőhöz vannak társítva.
* Virtuális hálózatok társviszonyba állíthatók, ha mindkettő a Resource Manager-alapú üzemi modellel lett létrehozva, vagy ha az egyik a Resource Manager-alapú üzemi modellel, a másik pedig a klasszikus üzemi modellel lett létrehozva. Két, a klasszikus üzembehelyezési modellel létrehozott virtuális hálózat azonban nem hozható egymással társviszonyba. Ha különböző üzembehelyezési modellekkel létrehozott virtuális hálózatok között szeretne társviszonyt létesíteni, akkor a virtuális hálózatoknak *ugyanabban* az előfizetésben kell lenniük. A *különböző* előfizetésekhez tartozó, különböző üzembehelyezési modellel létrehozott virtuális hálózatok társviszonyba állítása **előzetes** verzióban érhető el. További részleteket a [Társviszony-létesítés virtuális hálózatok között a Powershell használatával](virtual-networks-create-vnetpeering-arm-ps.md) című részben talál.
* Bár a virtuális társhálózatokba tartozó virtuális gépek kommunikációjára nem vonatkoznak külön sávszélesség-korlátozások, a virtuális gépekre méretalapú sávszélességkorlát érvényes. Ha többet szeretne megtudni a különböző méretű virtuális gépek maximális hálózati sávszélességével kapcsolatban, olvassa el a [windowsos](../virtual-machines/windows/sizes.md) vagy a [linuxos](../virtual-machines/linux/sizes.md) virtuális gépek méretéről szóló cikkeket.

![Alapszintű virtuális társhálózati viszony](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Kapcsolatok
Ha társviszony jött létre két virtuális hálózat között, akkor a virtuális hálózatba tartozó virtuális gépek és Cloud Services-szerepkörök közvetlenül tudnak kommunikálni a virtuális társhálózatba tartozó többi erőforrással. A két virtuális hálózat között teljes körű IP-szintű kapcsolat áll fenn.

A virtuális társhálózatokban található virtuális gépek közötti körbejárási késés megegyezik az egyetlen virtuális hálózaton belüli körbejárási késéssel. A hálózat átviteli sebessége attól függ, hogy milyen, a virtuális gépekkel arányos sávszélesség van engedélyezve. A társviszonyon belül más korlátozás nem vonatkozik a sávszélességre.

A virtuális társhálózatok virtuális gépei közötti forgalom közvetlenül az Azure háttér-infrastruktúráján halad át, nem pedig átjárón.

Az egyik virtuális hálózathoz tartozó virtuális gépek elérhetik a virtuális társhálózat belső terheléselosztásos (ILB-) végpontjait. Szükség esetén bármelyik virtuális hálózatban alkalmazhatók hálózati biztonsági csoportok (NSG-k) a más virtuális hálózatok vagy alhálózatok elérésének blokkolására.

A társviszony konfigurálásakor eldöntheti, hogy megnyitja vagy lezárja a két virtuális hálózat közötti NSG-szabályokat. Ha a virtuális társhálózatok között teljes körű kapcsolatot nyit (ez az alapértelmezett beállítás), akkor az NSG-k alkalmazásával konkrét alhálózatokra vagy virtuális gépekre vonatkozóan blokkolhatja vagy megtagadhatja a megadott típusú hozzáférést. Ha többet szeretne megtudni az NSG-kről, olvassa el a [Hálózati biztonsági csoportok](virtual-networks-nsg.md) című cikket.

Az Azure által a virtuális gépekre vonatkozóan biztosított belső DNS-névfeloldás nem működik két társviszonyban álló virtuális hálózat között. A virtuális gépeknek olyan belső DNS-nevük van, amelyet csak a helyi virtuális hálózaton belül lehet feloldani. A virtuális társhálózatba tartozó virtuális gépeket azonban konfigurálhatja egy virtuális hálózat DNS-kiszolgálójaként. További részleteket a [Névfeloldás saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) című cikkben talál.

## <a name="service-chaining"></a>Szolgáltatásláncolás
Olyan, felhasználó által megadott útvonalakat (UDR) is konfigurálhat, amelyekben a társviszonyban álló virtuális hálózatba tartozó virtuális gépek vannak megadva a „következő ugrás” IP-címeként, amint az a cikk későbbi részében szereplő diagramon látható. Ez elérhetővé teszi a szolgáltatóláncolást, amely lehetővé teszi, hogy felhasználó által megadott útvonallal átirányíthassa a forgalmat az egyik virtuális hálózatból egy, a virtuális társhálózatban működő virtuális berendezésre.

Jó eredménnyel alakíthat ki csillagpontos típusú környezetet is, amelyben a csillag középponti hálózatában infrastrukturális összetevők (például virtuális hálózati berendezések) működhetnek. A csillag csúcsain lévő virtuális hálózatok ezt követően társhálózati viszonyba hozhatók a központi hálózattal, illetve a forgalom egy része átirányítható a központi virtuális hálózaton futó berendezésre. A virtuális hálózatok közötti társviszonnyal lehetőség nyílik arra, hogy a felhasználó által megadott útvonalban a virtuális társhálózat egyik virtuális gépének IP-címe legyen a következő ugrás IP-címe. A felhasználó által megadott útvonalakkal (UDR-ekkel) kapcsolatban további információt a [Felhasználó által megadott útvonalak](virtual-networks-udr-overview.md) című cikkben olvashat.

## <a name="gateways-and-on-premises-connectivity"></a>Átjárók és kapcsolat helyszíni rendszerekkel
Minden virtuális hálózatnak – függetlenül attól, hogy társviszonyban áll-e más virtuális hálózattal – lehet saját átjárója, és használhatja őket helyszíni hálózathoz való csatlakozásra. A felhasználók [két virtuális hálózat közötti kapcsolatot](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) is konfigurálhatnak átjárók segítségével, még abban az esetben is, ha a virtuális hálózatok társviszonyban állnak.

Ha a virtuális hálózatok mindkét összekapcsolási módszere konfigurálva van, a virtuális hálózatok közötti forgalom a társhálózati konfiguráción keresztül (vagyis az Azure gerinchálózatán keresztül) zajlik.

Ha két virtuális hálózat között társhálózati viszony van, a felhasználók a virtuális társhálózatban működő átjárót is konfigurálhatják átviteli pontként egy helyszíni rendszer felé. Ebben az esetben a távoli átjárót használó virtuális hálózatnak nem lehet saját átjáróra. Egy virtuális hálózatnak csak egy átjárója lehet. Az átjáró lehet helyi vagy távoli (virtuális társhálózatban működő), amint a következő képen látható:

![Átvitel virtuális társhálózat esetén](./media/virtual-networks-peering-overview/figure02.png)

Az átjáróval történő adatátvitel nem támogatott abban az esetben, ha olyan virtuális hálózatok állnak egymással társviszonyban, amelyek különböző üzembehelyezési modellel lettek létrehozva. Ahhoz, hogy az átjárón át történő átvitel működjön, a társviszonyban álló virtuális hálózatok közül mindkettőt a Resource Manager-alapú üzemi modellel kell létrehozni.

Ha két, ugyanazon az Azure ExpressRoute-kapcsolaton osztozó VNetet állítanak társviszonyba, akkor a társhálózaton (vagyis az Azure gerinchálózatán) keresztül áramlik közöttük a forgalom. Ennek ellenére használhatja az egyes virtuális hálózatok helyi átjáróit arra, hogy kapcsolatot létesítsen a helyszíni kapcsolatcsoporttal. Közös átjárót is használhat, és átvitel konfigurálásával létesíthet kapcsolatot a helyszíni rendszerrel.

## <a name="provisioning"></a>Kiépítés
A virtuális hálózatok közötti társviszony-létesítés jogosultsághoz van kötve. Külön funkció a VirtualNetworks névtérben. A felhasználók egy külön engedély megadásával jogosíthatók fel a társviszony-létesítésre. A virtuális hálózathoz írási-olvasási hozzáféréssel rendelkező felhasználó automatikusan örökli ezeket a jogokat.

Olyan felhasználó kezdeményezhet társviszony-létesítési műveletet egy másik virtuális hálózattal, aki rendszergazda, vagy fel van jogosítva a társviszony-létesítésre. Ha a másik oldalon is van egy ennek megfelelő társviszony-létesítési kérelem, és ha teljesül a többi követelmény, létrejön a társviszony.

A [Következő lépések](#next-steps) című szakaszban található cikkek részletesebb tájékoztatást nyújtanak arról, hogy miképpen lehet virtuális társhálózati viszonyba hozni egymással két virtuális hálózatot.

## <a name="limits"></a>Korlátok
Az egy virtuális hálózat esetében létesíthető társviszonyok mennyiségére korlátozás vonatkozik. Erről az [Azure hálózati korlátozásait](../azure-subscription-service-limits.md#networking-limits) ismertető cikk nyújt részletesebb tájékoztatást.

## <a name="pricing"></a>Díjszabás
Egy névleges díj vonatkozik a társhálózati viszonyt használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="next-steps"></a>Következő lépések
További információ az alábbiakkal történő virtuális társhálózati viszony létrehozásáról:

* [Azure Portal](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Azure Resource Manager-sablon](virtual-networks-create-vnetpeering-arm-template-click.md)

