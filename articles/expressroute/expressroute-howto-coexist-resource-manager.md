---
title: "Párhuzamosan fennálló ExpressRoute- és helyek közötti VPN-kapcsolatok konfigurálása: Resource Manager: Azure | Microsoft Docs"
description: "A cikk bemutatja az ExpressRoute- és egy helyek közötti VPN-kapcsolat konfigurálását, amelyek párhuzamosan használhatók a Resource Manager-alapú üzemi modellben."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c7717b14-3da3-4a6d-b78e-a5020766bc2c
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 17f179d9bd614b5ea05bef8c4ca4173eecc6b9be
ms.lasthandoff: 03/24/2017


---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections"></a>Párhuzamos ExpressRoute- és párhuzamos telephelyközi kapcsolatok konfigurálása
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – Klasszikus](expressroute-howto-coexist-classic.md)
> 
> 

A helyek közötti VPN és az ExpressRoute konfigurálásának lehetősége több előnnyel jár. A helyek közötti VPN-t konfigurálhatja biztonságos feladatátvételi útvonalként az ExpressRoute számára, vagy használhat helyek közötti VPN-eket is a nem ExpressRoute-on keresztül kapcsolódó helyekhez való csatlakozáshoz. A cikkben mindkét forgatókönyv lépéseit ismertetjük. Ez a cikk a Resource Manager-alapú üzemi modell vonatkozik. Ez a konfiguráció az Azure Portalon nem érhető el.

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportokat előre konfigurálni kell, mielőtt végrehajtaná az alábbi utasításokat. Mielőtt folytatná az alábbi lépésekkel, az útmutatásoknak megfelelően [hozzon létre egy ExpressRoute-kapcsolatcsoportot](expressroute-howto-circuit-arm.md) és [konfigurálja az útválasztást](expressroute-howto-routing-arm.md).
> 
> 

## <a name="limits-and-limitations"></a>Korlátok és korlátozások
* **A tranzit útválasztás nem támogatott.** Nem hajthat végre útválasztást (az Azure-on keresztül) a helyek közötti VPN használatával csatlakoztatott helyi hálózat és az ExpressRoute használatával csatlakoztatott helyi hálózat között.
* **A Basic SKU-átjáró nem támogatott.** Nem Basic SKU-átjárót kell használnia [ExpressRoute-](expressroute-about-virtual-network-gateways.md) és [VPN-átjáróként](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* **Kizárólag az útvonalalapú VPN-átjáró támogatott.** Útvonalalapú [VPN-átjárót](../vpn-gateway/vpn-gateway-about-vpngateways.md) kell használnia.
* **A VPN-átjáróhoz statikus útvonalat kell konfigurálni.** Ha a helyi hálózat az ExpressRoute-hoz és a helyek közötti VPN-hez is csatlakozik, konfigurálnia kell egy statikus útvonalat a helyi hálózaton a helyek közötti VPN-kapcsolat a nyilvános internetre történő átirányításához.
* **Elsőként az ExpressRoute-átjárót kell konfigurálnia.** Először az ExpressRoute-átjárót kell létrehoznia, mielőtt felvenné a helyek közötti VPN-átjárót.

## <a name="configuration-designs"></a>Konfigurációs tervek
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Helyek közötti VPN konfigurálása feladatátvételi útvonalként az ExpressRoute számára
Konfigurálhat egy helyek közötti VPN-kapcsolatot tartalékként az ExpressRoute számára. Ez csak az Azure privát társviszony-létesítési útvonalhoz társított virtuális hálózatokra vonatkozik. Az Azure nyilvános és a Microsoft társviszony-létesítésekhez nem létezik VPN-alapú feladatátvételi megoldás. Minden esetben az ExpressRoute-kapcsolatcsoport az elsődleges kapcsolat. Az adatok csak akkor lesznek a helyek közötti VPN-útvonalon továbbítva, ha az ExpressRoute-kapcsolatcsoport meghibásodik.

> [!NOTE]
> Bár a rendszer az ExpressRoute-kapcsolatcsoportot részesíti előnyben a helyek közötti VPN helyett, ha az útvonalak megegyeznek, az Azure a leghosszabb előtag-megfeleltetéssel választja ki a célcsomag útvonalát.
> 
> 

![Egyidejű jelenlét](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Helyek közötti VPN konfigurálása az ExpressRoute használatával nem csatlakozó helyekhez
A hálózatát konfigurálhatja úgy is, hogy egyes helyek közvetlenül az Azure-hoz kapcsolódnak helyek közötti VPN-en keresztül, míg más helyek az ExpressRoute használatával kapcsolódnak. 

![Egyidejű jelenlét](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Virtuális hálózatot nem konfigurálhat tranzit útválasztóként.
> 
> 

## <a name="selecting-the-steps-to-use"></a>A használni kívánt lépések kiválasztása
Két különböző eljáráscsoport közül választhat az egyidejűleg használható kapcsolatok konfigurálásához. A konfigurálás választott módja attól függ, hogy rendelkezik-e meglévő virtuális hálózattal, amelyhez csatlakozni szeretne, vagy egy új virtuális hálózatot szeretne létrehozni.

* Nem rendelkezem VNettel, és létre kell hoznom egyet.
  
    Ha még nem rendelkezik virtuális hálózattal, ez az eljárás lépésről lépésre végigvezeti az új virtuális hálózat létrehozásának folyamatán a Resource Manager-alapú üzemi modellnek megfelelően, valamint az új ExpressRoute- és helyek közötti VPN-kapcsolatok létrehozásának folyamatán. A konfiguráláshoz kövesse a cikk az [Új virtuális hálózat és egyidejű kapcsolatok létrehozása](#new) szakaszában foglalt lépéseket.
* Már rendelkezem egy Resource Manager-alapú üzemi modell szerinti VNettel.
  
    Előfordulhat, hogy már rendelkezik üzemelő virtuális hálózattal egy létező helyek közötti VPN- vagy ExpressRoute-kapcsolattal. Az [Egyidejű kapcsolatok konfigurálása meglévő VNet számára](#add) szakasz végigvezeti az átjáró törlésének, majd az új ExpressRoute- és helyek között VPN-kapcsolatok létrehozásának folyamatán. Ügyeljen arra, hogy az új kapcsolatok létrehozásakor a lépéseket szigorú sorrendben kell végrehajtani. Az átjárók és kapcsolatok létrehozására ne használja más cikkek utasításait.
  
    Ebben az eljárásban az egyidejű kapcsolatok létrehozásához törölnie kell az átjárót, majd új átjárókat kell konfigurálnia. Ez azt jelenti, hogy a helyszínek közötti kapcsolatok esetében állásidővel kell számolnia, amíg törli és újra létrehozza az átjárót és a kapcsolatokat, azonban a virtuális gépeket és a szolgáltatásokat nem kell áttelepítenie egy új virtuális hálózatra. Ha megfelelően vannak konfigurálva, a virtuális gépek és a szolgáltatások továbbra is képesek lesznek kommunikálni a terheléselosztón keresztül az átjáró konfigurálása közben.

## <a name="new"></a>Új virtuális hálózat és egyidejű kapcsolatok létrehozása
Az eljárás a VNetek, valamint az egyidejűleg jelenlévő helyek közötti és ExpressRoute-kapcsolatok létrehozásának módját ismerteti.

1. Az Azure PowerShell-parancsmagok legújabb verzióit kell telepítenie. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs). Vegye figyelembe, hogy az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja.
2. Jelentkezzen be a fiókjába, és állítsa be a környezetet.
   
        login-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
        $location = "Central US"
        $resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location
3. Hozzon létre egy virtuális hálózatot az átjáró-alhálózattal együtt. A virtuális hálózat konfigurálásával kapcsolatos információkért lásd: [Azure Virtual Network konfigurálása](../virtual-network/virtual-networks-create-vnet-arm-ps.md).
   
   > [!IMPORTANT]
   > Az átjáró-alhálózat /27 vagy egy rövidebb előtag kell legyen (például /26 vagy /25).
   > 
   > 
   
    Hozzon létre egy új VNetet.
   
        $vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 
   
    Adjon hozzá alhálózatokat.
   
        Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Mentse a VNet konfigurációját.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
4. <a name="gw"></a>Hozzon létre egy ExpressRoute-átjárót. Az ExpressRoute-átjáró konfigurálásával kapcsolatos további információkért lásd: [ExpressRoute-átjáró konfigurálása](expressroute-howto-add-gateway-resource-manager.md). A GatewaySKU paraméterben a *Standard*, *HighPerformance* vagy *UltraPerformance* értéket kell megadni.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        $gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 
5. Csatlakoztassa az ExpressRoute-átjárót az ExpressRoute-kapcsolatcsoporthoz. A lépés végrehajtásával létrejön a kapcsolat a helyszíni hálózat és az Azure között az ExpressRoute-on keresztül. A csatolási művelettel kapcsolatos további információkért lásd: [VNetek csatolása az ExpressRoute-hoz](expressroute-howto-linkvnet-arm.md).
   
        $ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
        New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
6. <a name="vpngw"></a>Ezután hozza létre a helyek közötti VPN-átjárót. A VPN-átjáró konfigurálásával kapcsolatos további információkért lásd: [VNet létrehozása helyek közötti kapcsolattal](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). A GatewaySKU paraméterben a *Standard*, *HighPerformance* vagy *UltraPerformance* értéket kell megadni. A VpnType paramétert *RouteBased* értékre kell állítania.
   
        $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
        $gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
        New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"
   
    Az Azure-alapú VPN-átjáró támogatja a BGP-t. Az alábbi parancsban megadhatja az -EnableBgp paramétert.
   
        $azureVpn = New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard" -EnableBgp $true
   
    A következőkben megtalálhatja a BGP-társviszony-létesítés IP-címét és AS-számát, amelyeket az Azure használ a VPN-átjáróhoz: $azureVpn.BgpSettings.BgpPeeringAddress és $azureVpn.BgpSettings.Asn. További információk: [A BGP konfigurálása](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) az Azure-alapú VPN-átjáróhoz.
7. Hozzon létre egy helyi VPN-átjáró entitást. Ez a parancs nem konfigurálja a helyszíni VPN-átjárót. Ehelyett a helyi átjáró beállításai, például a nyilvános IP-cím és a helyszíni címtér megadására szolgál, hogy az Azure VPN-átjáró kapcsolódhasson hozzá.
   
    Ha a helyi VPN-eszköz csak a statikus útválasztást támogatja, a következő módon konfigurálhatja a statikus útvonalakat.
   
        $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   
    Ha a helyi VPN-eszköz támogatja a BGP-t, és Ön szeretné engedélyezni a dinamikus útválasztást, ismernie kell a helyi VPN-eszköz által használt BGP-társviszony-létesítés IP-címét és AS-számát.
   
        $localVPNPublicIP = "<Public IP>"
        $localBGPPeeringIP = "<Private IP for the BGP session>"
        $localBGPASN = "<ASN>"
        $localAddressPrefix = $localBGPPeeringIP + "/32"
        $localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
8. Konfigurálja helyi VPN-eszközét, hogy az új Azure VPN-átjáróhoz csatlakozzon. A VPN-eszköz konfigurálásával kapcsolatos további információkért lásd: [VPN-eszköz konfigurálása](../vpn-gateway/vpn-gateway-about-vpn-devices.md).
9. Csatlakoztassa az Azure-on a helyek közötti VPN-átjárót a helyi átjáróhoz.
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>

## <a name="add"></a>Egyidejű kapcsolatok konfigurálása meglévő VNet számára
Ha már rendelkezik meglévő virtuális hálózattal, ellenőrizze az átjáró-alhálózat méretét. Ha az átjáró-alhálózat /28 vagy /29, először törölnie kell a virtuális hálózati átjárót, és növelnie kell az átjáró-alhálózat méretét. A jelen szakaszban ismertetett lépések bemutatják, mindez hogyan valósítható meg.

Ha az átjáró-alhálózat /27 vagy nagyobb, és a virtuális hálózat ExpressRoute-on keresztül csatlakozik, kihagyhatja az alábbi lépéseket, és továbbléphet a [„6. lépés – Helyek közötti VPN-átjáró létrehozása”](#vpngw) lépésre az előző szakaszban. 

> [!NOTE]
> Amikor törli a meglévő átjárót, megszakad a helyi helyszínek kapcsolata a virtuális hálózattal, amíg ezen a konfiguráción dolgozik. 
> 
> 

1. Az Azure PowerShell-parancsmagok legújabb verzióit kell telepítenie. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs). Vegye figyelembe, hogy az ehhez a konfigurációhoz használt parancsmagok eltérőek lehetnek az Ön által már ismertektől. Ügyeljen arra, hogy az ebben az útmutatóban meghatározott parancsmagokat használja. 
2. Törölje a meglévő ExpressRoute- vagy helyek közötti VPN-átjárót. 
   
        Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
3. Törölje az átjáró-alhálózatot.
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
        Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
4. Adjon hozzá egy átjáró-alhálózatot, amely legfeljebb /27.
   
   > [!NOTE]
   > Ha már nincs elegendő IP-cím a virtuális hálózaton az átjáró-alhálózat méretének növeléséhez, további IP-címtereket kell hozzáadnia.
   > 
   > 
   
        $vnet = Get-AzureRmVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
        Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   
    Mentse a VNet konfigurációját.
   
        $vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
5. Ezen a ponton egy átjáró nélküli VNettel rendelkezik. Új átjárók létrehozásához és a kapcsolatok véglegesítéséhez folytathatja az előző lépéssorban foglalt [4. lépés – ExpressRoute-átjáró létrehozása](#gw) lépéssel.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>Pont-hely konfiguráció hozzáadása a VPN-átjáróhoz
Az alábbi lépések végrehajtásával pont–hely konfigurációt adhat hozzá a VPN-átjáróhoz egyidejű jelenléttel rendelkező beállításokban.

1. Adja hozzá a VPN-ügyfél címterét. 
   
        $azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
        Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
2. Töltse fel a VPN-főtanúsítványt az Azure-ba a VPN-átjáró számára. Ebben a példában feltételezzük, hogy a főtanúsítványt a helyi gépen tárolja, ahol az alábbi PowerShell-parancsmagok futnak. 
   
        $p2sCertFullName = "RootErVpnCoexP2S.cer"
        $p2sCertMatchName = "RootErVpnCoexP2S"
        $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
        if ($p2sCertToUpload.count -eq 1){
            write-host "cert found"
        } else {
            write-host "cert not found"
            exit
        } 
        $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

A pont-hely VPN-ekkel kapcsolatos további információkért lásd: [Pont-hely kapcsolat konfigurálása](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Következő lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).


