---
title: "IP-cím-típusok az Azure-ban | Microsoft Docs"
description: "Információk a nyilvános és privát IP-címekről az Azure-ban."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0506de98119576aae8f55cc6067f0bee95334c87
ms.lasthandoff: 03/31/2017


---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>IP-cím-típusok és lefoglalási módszerek az Azure-ban
IP-címeket rendelhet az Azure-erőforrásokhoz a többi Azure-erőforrással, a helyszíni hálózattal és az internettel való kommunikáció céljából. Az Azure-ban két típusú IP-címet használhat:

* **Nyilvános IP-címek**: Az internettel való kommunikációhoz használatos, beleértve az Azure nyilvánosan elérhető szolgáltatásait
* **Magánhálózati IP-címek**: Egy Azure virtuális hálózaton (VNet) belüli és a helyszíni hálózaton belüli kommunikációra használatos, ha VPN-átjárót vagy ExpressRoute-kapcsolatcsoportot használ a hálózat kiterjesztésére az Azure-ban.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md).  Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti, amelyet a Microsoft a legtöbb új telepítéshez a [klasszikus üzemi modell](virtual-network-ip-addresses-overview-classic.md) helyett javasol.
> 

Ha a klasszikus üzemi modellt ismeri, tekintse meg a következőt: [Az IP-címkezelés különbségei a klasszikus és a Resource Manager modellek között](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Nyilvános IP-címek
A nyilvános IP-címek lehetővé teszik az Azure-erőforrások számára, hogy kommunikáljanak az internettel és az Azure más nyilvános szolgáltatásaival, például az [Azure Redis Cache](https://azure.microsoft.com/services/cache/) és az [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) szolgáltatásokkal, valamint az [SQL-adatbázisokkal](../sql-database/sql-database-technical-overview.md) és az [Azure-tárolóval](../storage/storage-introduction.md).

Az Azure Resource Manager szolgáltatásban a [nyilvános IP-cím](resource-groups-networking.md#public-ip-address) olyan erőforrás, amely saját tulajdonságokkal rendelkezik. A nyilvános IP-cím-erőforrást a következő erőforrások bármelyikéhez hozzárendelheti:

* Virtuális gépek (VM)
* Internetkapcsolattal rendelkező terheléselosztók
* VPN-átjárók
* Alkalmazásátjárók

### <a name="allocation-method"></a>Lefoglalási módszer
Két módszer van, amellyel az IP-címek lefoglalhatók egy *nyilvános* IP-erőforráshoz: *dinamikus* vagy *statikus*. Az alapértelmezett lefoglalási módszer a *dinamikus*, amelyben az IP-cím **nincs** lefoglalva a létrehozás idején. Ehelyett a nyilvános IP-címet akkor foglalja le a rendszer, amikor elindítja (vagy létrehozza) a hozzárendelt erőforrást (pl. virtuális gépet vagy terheléselosztót). Az IP-cím akkor szabadul fel, ha leállítja (vagy törli) az erőforrást. Ezáltal az IP-cím változhat, ha leállítja majd újraindítja az erőforrást.

A lefoglalási módszert állíthatja *statikusra* is, hogy a kapcsolódó erőforráshoz tartozó IP-címek változatlanok maradjanak. Ebben az esetben a rendszer azonnal hozzárendel egy IP-címet. Az IP-cím csak akkor szabadul fel, ha törli az erőforrást, vagy *dinamikusra* változtatja a kiosztási módszert.

> [!NOTE]
> Akkor sem adhatja meg a *nyilvános IP-erőforráshoz* rendelt konkrét IP-címet, ha a kiosztási módszert *statikusra* állítja. Ehelyett a rendszer az elérhető IP-címek készletéből foglal le egyet azon az Azure-helyen, ahol az erőforrást létrehozták.
>

Statikus nyilvános IP-címeket általában a következő esetekben szoktak használni:

* A végfelhasználóknak frissíteniük kell a tűzfalszabályokat, hogy kommunikálhassanak az Ön Azure-erőforrásaival.
* DNS-névfeloldás, ahol az IP-cím változása miatt frissíteni kellene az A-rekordokat.
* Az Ön Azure-erőforrásai más alkalmazásokkal és szolgáltatásokkal is kommunikálnak, amelyek IP-címalapú biztonsági modellt használnak.
* Ön IP-címhez kötött SSL-tanúsítványokat használ.

> [!NOTE]
> Az IP-tartományok listája, amelyből a rendszer a nyilvános (dinamikus/statikus) IP-címeket lefoglalja, a következő helyen található: [Az Azure adatközpont IP-tartományai](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>DNS-állomásnév feloldása
Megadhat egy DNS-tartománynév címkét a nyilvános IP-cím erőforráshoz, amely hozzárendelést hoz létre a *domainnamelabel*.*location*.cloudapp.azure.com és a nyilvános IP-cím között az Azure által felügyelt DNS-kiszolgálókon. Ha például nyilvános IP-cím erőforrást hoz létre a **contoso** *domainnamelabel* használatával a **West US** *Azure-helyen*, akkor a rendszer feloldja a **contoso.westus.cloudapp.azure.com** teljes tartománynevet (FQDN) az erőforrás nyilvános IP-címéhez. Ezt a teljes tartománynevet használhatja az Azure-ban lévő nyilvános IP-címre mutató egyéni tartományi CNAME-rekord létrehozásához.

> [!IMPORTANT]
> Minden egyes létrehozott tartománynév-címkének egyedinek kell lennie az Azure-helyen.  
>

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
A nyilvános IP-címet társíthatja egy [Windows](../virtual-machines/windows/about.md) vagy [Linux](../virtual-machines/virtual-machines-linux-about.md) virtuális géppel, ha hozzárendeli a **hálózati adapteréhez**. Több hálózati adapterrel rendelkező virtuális gépek esetén csak az *elsődleges* hálózati adapterhez végezhet hozzárendelést. Hozzárendelhet egy dinamikus vagy egy statikus méretű nyilvános IP-címet az egyes virtuális gépekhez.

### <a name="internet-facing-load-balancers"></a>Internetkapcsolattal rendelkező terheléselosztók
A nyilvános IP-címet társíthatja egy [Azure Load Balancerrel](../load-balancer/load-balancer-overview.md), ha hozzárendeli a terheléselosztó **előtér**-konfigurációjához. Ez a nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd. Hozzárendelhet egy dinamikus vagy egy statikus nyilvános IP-címet a terheléselosztó előtérrendszerhez. Több nyilvános IP-címet is hozzárendelhet egy terheléselosztó előtérrendszerhez, ami olyan [több virtuális IP-címes](../load-balancer/load-balancer-multivip.md) forgatókönyveket tesz lehetővé, mint az SSL-alapú webhelyek több-bérlős környezetei.

### <a name="vpn-gateways"></a>VPN-átjárók
Az [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) használatával egy Azure virtuális hálózat (VNet) összekapcsolható más Azure VNet-hálózatokkal vagy egy helyszíni hálózattal. Hozzá kell rendelnie egy nyilvános IP-címet az **IP-konfigurációhoz**, hogy képes legyen kommunikálni a távoli hálózattal. Jelenleg csak *dinamikus* nyilvános IP-címeket rendelhet a VPN-átjárókhoz.

### <a name="application-gateways"></a>Alkalmazásátjárók
A nyilvános IP-címet társíthatja egy [Azure Application Gateway átjáróval](../application-gateway/application-gateway-introduction.md), ha hozzárendeli az átjáró **előtér**-konfigurációjához. Ez a nyilvános IP-cím terheléselosztásos virtuális IP-címként szolgál majd. Jelenleg csak *dinamikus* nyilvános IP-címeket rendelhet az alkalmazásátjárók előtér-konfigurációjához.

### <a name="at-a-glance"></a>Egy pillantásra
Az alábbi táblázat bemutatja azokat a specifikus tulajdonságokat, amelyeken keresztül a nyilvános IP-címek a legfelsőbb szintű erőforrással társíthatók, továbbá a lehetséges használható lefoglalási módszereket (dinamikus vagy statikus).

| Legfelső szintű erőforrás | IP-cím társítása | Dinamikus | Statikus |
| --- | --- | --- | --- |
| Virtuális gép |Hálózati illesztő |Igen |Igen |
| Terheléselosztó |Előtér-konfiguráció |Igen |Igen |
| VPN-átjáró |Átjáró IP-konfigurációja |Igen |Nem |
| Alkalmazásátjáró |Előtér-konfiguráció |Igen |Nem |

## <a name="private-ip-addresses"></a>Magánhálózati IP-címek
A magánhálózati IP-címek segítségével az Azure-erőforrások képesek a helyszíni vagy a [virtuális hálózaton](virtual-networks-overview.md) lévő erőforrásokkal VPN-átjárón vagy ExpressRoute-kapcsolatcsoporton keresztül kommunikálni az internetről elérhető IP-címek használata nélkül is.

Az Azure Resource Manager-alapú üzemi modellben a magánhálózati IP-címek az alábbi Azure-erőforrástípusokkal vannak társítva:

* Virtuális gépek
* Belső terheléselosztók (ILB-k)
* Alkalmazásátjárók

### <a name="allocation-method"></a>Lefoglalási módszer
A magánhálózati IP-cím annak az alhálózatnak a címtartományából van lefoglalva, amelyhez az erőforrás csatlakozik. Az alhálózat címtartománya maga a VNet címtartományának része.

Két módszer van, amellyel a magánhálózati IP-címek lefoglalhatók: *dinamikus* vagy *statikus*. Az alapértelmezett lefoglalási módszer a *dinamikus*, amelyben az IP-cím lefoglalása automatikusan történik az erőforrás alhálózatából (a DHCP használatával). Az IP-cím változhat, ha leállítja, majd újraindítja az erőforrást.

A lefoglalási módszert állíthatja *statikusra* is, hogy az IP-címek változatlanok maradjanak. Ebben az esetben meg kell adnia egy olyan érvényes IP-címet, amely az erőforrás alhálózatának részét képezi.

Statikus magánhálózati IP-címeket általában a következő esetekben szoktak használni:

* Tartományvezérlőként vagy DNS-kiszolgálóként működő virtuális gépek esetén.
* IP-címeket használó tűzfalszabályokat igénylő erőforrások esetén.
* Más alkalmazások/erőforrások által IP-címen keresztül elért erőforrások esetén.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
A magánhálózati IP-cím egy [Windows-](../virtual-machines/windows/about.md) vagy [Linux-](../virtual-machines/virtual-machines-linux-about.md)virtuális gép **hálózati adapteréhez** van rendelve. Több hálózati adapterrel rendelkező virtuális gép esetében mindegyik adapterhez külön lesz egy magánhálózati IP-cím rendelve. A lefoglalási módszert hálózati adapter esetében meghatározhatja dinamikusként vagy statikusként is.

#### <a name="internal-dns-hostname-resolution-for-vms"></a>Belső DNS-állomásnév feloldása (virtuális gép esetén)
Minden Azure VM alapértelmezés szerint az [Azure által felügyelt DNS-kiszolgálókkal](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) van konfigurálva, ha nem konfigurál kifejezetten egyéni DNS-kiszolgálókat. Ezek a DNS-kiszolgálók belső névfeloldást biztosítanak az egyazon VNeten található virtuális gépek számára.

Amikor létrehoz egy virtuális gépet, az állomásnév a magánhálózati IP-címére való leképezése fel lesz véve az Azure által felügyelt DNS-kiszolgálókra. Több hálózati adapterrel rendelkező virtuális gép esetében az állomásnév az elsődleges hálózati adapter magánhálózati IP-címére lesz leképezve.

Az Azure által felügyelt DNS-kiszolgálókkal konfigurált virtuális gépek képesek lesznek a VNet hálózatukban az összes virtuális gép állomásnevét feloldani azok magánhálózati IP-címeire.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Belső terheléselosztók (ILB) és alkalmazásátjárók
Hozzárendelhet egy magánhálózati IP-címet az [Azure Internal Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) terheléselosztók vagy [Azure Application Gateway](../application-gateway/application-gateway-introduction.md) alkalmazásátjárók **előtér**-konfigurációjához. Ez a magánhálózati IP-cím belső végpontként szolgál, amelyet csak a virtuális hálózatán (VNet) és a VNethez csatlakozó távoli hálózatokon lévő erőforrások érnek el. Hozzárendelhet egy dinamikus vagy egy statikus méretű magánhálózati IP-címet az előtér-konfigurációhoz.

### <a name="at-a-glance"></a>Egy pillantásra
Az alábbi táblázat bemutatja azokat a specifikus tulajdonságokat, amelyeken keresztül a magánhálózati IP-címek a legfelsőbb szintű erőforrással társíthatók, továbbá a lehetséges használható lefoglalási módszereket (dinamikus vagy statikus).

| Legfelső szintű erőforrás | IP-cím társítása | Dinamikus | Statikus |
| --- | --- | --- | --- |
| Virtuális gép |Hálózati illesztő |Igen |Igen |
| Terheléselosztó |Előtér-konfiguráció |Igen |Igen |
| Alkalmazásátjáró |Előtér-konfiguráció |Igen |Igen |

## <a name="limits"></a>Korlátok
Az IP-címkezelésre vonatkozó korlátokat a [hálózati korlátok](../azure-subscription-service-limits.md#networking-limits) teljes csomagjában jelezzük az Azure-ban. A korlátok régiónként és előfizetésenként értendők. [Lépjen kapcsolatba a támogatással](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), ha növelni szeretné az alapértelmezett korlátot üzleti igényei szerint – akár a maximális korlátig.

## <a name="pricing"></a>Díjszabás
A nyilvános IP-címek kapcsán névleges díjak merülhetnek fel. Ha többet szeretne megtudni az Azure-ban használt IP-címek díjszabásáról, tekintse át az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt.

## <a name="next-steps"></a>Következő lépések
* [Statikus nyilvános IP-címmel rendelkező virtuális gép telepítése az Azure Portal használatával](virtual-network-deploy-static-pip-arm-portal.md)
* [Statikus nyilvános IP-címmel rendelkező virtuális gép telepítése sablon használatával](virtual-network-deploy-static-pip-arm-template.md)
* [Statikus magánhálózati IP-címmel rendelkező virtuális gép telepítése az Azure Portal használatával](virtual-networks-static-private-ip-arm-pportal.md)

