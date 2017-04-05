---
title: "Azure-alapú virtuális hálózat csatlakoztatása másik VNethez: PowerShell | Microsoft Docs"
description: "Ez a cikk lépésről lépésre bemutatja, hogyan csatlakoztathatók a virtuális hálózatok egymáshoz az Azure Resource Manager és a PowerShell használatával."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a4cbc4cd1c48da1120c643892b19692ac583d4c3
ms.lasthandoff: 03/31/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-powershell"></a>Virtuális hálózatok közötti kapcsolat konfigurálása a PowerShell használatával
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Klasszikus – Klasszikus portál](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
>

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózatok közötti kapcsolat a Resource Manager-alapú üzemi modellben a VPN Gateway használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, illetve azonos vagy eltérő előfizetésekben.

![v2v ábra](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Üzembe helyezési modellek és módszerek virtuális hálózatok közötti kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázat a virtuális hálózatok közötti kapcsolati konfigurációkhoz jelenleg elérhető üzembe helyezési modelleket és módszereket tartalmazza. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban. 

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Virtuális hálózatok közötti társviszony**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról
Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus Azure VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE használatával. Az összekapcsolt virtuális hálózatok lehetnek eltérő régiókban vagy eltérő előfizetésekben is. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal egyesítik, ahogyan azt a következő diagram mutatja:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?
A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**
  
  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**
  
  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.

### <a name="vnet-to-vnet-considerations"></a>A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="which-set-of-steps-should-i-use"></a>Melyik eljárást használjam?
Ebben a cikkben kétféle lépéssorozatot láthat. Az egyik lépéssorozat az [azonos előfizetésben található virtuális hálózatokra](#samesub), a másik az [eltérő előfizetésekben található virtuális hálózatokra](#difsub) vonatkozik. A két sorozat közötti fő különbség az, hogy lehetséges-e egyetlen PowerShell-munkameneten belül konfigurálni az összes virtuális hálózati és átjáró-erőforrást.

A cikkben szereplő lépések az egyes szakaszok elején deklarált változókat használják. Ha már meglévő virtuális hálózatokat használ, módosítsa úgy a változókat, hogy azok a saját környezetének beállításait tükrözzék. 

![Mindkét kapcsolat](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

## <a name="samesub"></a>Azonos előfizetésben található virtuális hálózatok összekapcsolása
![v2v ábra](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Előkészületek
Mielőtt elkezdi, telepítenie kell az Azure Resource Manager PowerShell-parancsmagjait. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

### <a name="Step1"></a>1. lépés – Az IP-címtartományok megtervezése
A következő lépések során két virtuális hálózatot fogunk létrehozni az azokhoz tartozó átjáróalhálózatokkal és konfigurációkkal. Ezután létrehozunk egy VPN-kapcsolatot a két virtuális hálózat között. Fontos, hogy megtervezze a hálózati konfiguráció IP-címtartományát. Ügyeljen arra, hogy a virtuális hálózata tartományai és a helyi hálózata tartományai ne legyenek átfedésben egymással.

A példákban a következő értékeket használjuk:

**Értékek a TestVNet1-hez:**

* Virtuális hálózat neve: TestVNet1
* Erőforráscsoport: TestRG1
* Hely: East US
* TestVNet1: 10.11.0.0/16 és 10.12.0.0/16
* Előtér: 10.11.0.0/24
* Háttér: 10.12.0.0/24
* Átjáróalhálózat: 10.12.255.0/27
* DNS-kiszolgáló: 8.8.8.8
* Átjáró neve: VNet1GW
* Nyilvános IP-cím: VNet1GWIP
* VPN típusa: RouteBased
* Kapcsolat (1–4): VNet1toVNet4
* Kapcsolat (1–5): VNet1toVNet5
* Kapcsolat típusa: VNet2VNet

**Értékek a TestVNet4-hez:**

* Virtuális hálózat neve: TestVNet4
* TestVNet2: 10.41.0.0/16 és 10.42.0.0/16
* Előtér: 10.41.0.0/24
* Háttér: 10.42.0.0/24
* Átjáróalhálózat: 10.42.255.0/27
* Erőforráscsoport: TestRG4
* Hely: West US
* DNS-kiszolgáló: 8.8.8.8
* Átjáró neve: VNet4GW
* Nyilvános IP-cím: VNet4GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet4toVNet1
* Kapcsolat típusa: VNet2VNet

### <a name="Step2"></a>2. lépés – A TestVNet1 létrehozása és konfigurálása
1. A változók deklarálása
   
    Kezdje a változók deklarálásával. Ez a példa a gyakorlathoz használt értékekkel deklarálja a változókat. A legtöbb esetben ezeket az értékeket a saját értékeire kell lecserélnie. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Ha szükséges, módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.
   
        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"
2. Csatlakozás az előfizetéshez
   
    A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:
   
        Login-AzureRmAccount
   
    Keresse meg a fiókot az előfizetésekben.
   
        Get-AzureRmSubscription 
   
    Válassza ki a használni kívánt előfizetést.
   
        Select-AzureRmSubscription -SubscriptionName $Sub1
3. Új erőforráscsoport létrehozása
   
        New-AzureRmResourceGroup -Name $RG1 -Location $Location1
4. A TestVNet1 alhálózatkonfigurációinak létrehozása
   
    Ez a példa létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul. 
   
    A következő példa a korábban beállított változókat használja. A példában az átjáróalhálózat /27-es alhálózatot használ. Bár lehetséges akár /29-es átjáróalhálózatot is létrehozni, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: legalább /28-asat vagy /27-eset. Ez elegendő címet biztosít ahhoz, hogy az esetleges további konfigurációkat is elbírják. 
   
        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
5. A TestVNet1 létrehozása
   
        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
6. Nyilvános IP-cím kérése
   
    Kérje egy nyilvános IP-cím kiosztását a virtuális hálózat számára létrehozni kívánt átjáróhoz. Vegye figyelembe, hogy az AllocationMethod értéke Dynamic. A használni kívánt IP-címet nem adhatja meg. Ennek kiosztása az átjáró számára dinamikusan történik. 
   
        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic
7. Az átjáró konfigurációjának létrehozása
   
    Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A példa használatával hozza létre az átjáró konfigurációját. 
   
        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1
8. A TestVNet1 átjárójának létrehozása
   
    Ebben a lépésben a TestVNet1 virtuális hálózati átjáróját fogja létrehozni. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet.
   
        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>3. lépés – A TestVNet4 létrehozása és konfigurálása
A TestVNet1 konfigurálása után a hozza létre a TestVNet4 virtuális hálózatot. Kövesse az alábbi lépéseket, az értékeket a saját értékeire cserélve. Ez a lépés elvégezhető ugyanebben a PowerShell-munkamenetben, mert a virtuális hálózat azonos előfizetésben található.

1. A változók deklarálása
   
    Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.
   
        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"
   
    Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez.
2. Új erőforráscsoport létrehozása
   
        New-AzureRmResourceGroup -Name $RG4 -Location $Location4
3. A TestVNet4 alhálózatkonfigurációinak létrehozása
   
        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4
4. A TestVNet4 létrehozása
   
        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
5. Nyilvános IP-cím kérése
   
        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic
6. Az átjáró konfigurációjának létrehozása
   
        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
7. A TestVNet4 átjárójának létrehozása
   
        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>4. lépés – Az átjárók csatlakoztatása
1. A virtuális hálózati átjárók létrehozása
   
    Ebben a példában a lépés elvégezhető ugyanebben a PowerShell-munkamenetben, mert az átjárók azonos előfizetésben találhatóak.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
2. A TestVNet1–TestVNet4 kapcsolat létrehozása
   
    Ebben a lépésben a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot hozza létre. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
3. A TestVNet4–TestVNet1 kapcsolat létrehozása
   
    Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   
    A kapcsolat néhány perc alatt létrejön.
4. Ellenőrizze a kapcsolatot. Tekintse meg [A kapcsolat ellenőrzése](#verify) című szakaszt.

## <a name="difsub"></a>Különböző előfizetésben található virtuális hálózatok összekapcsolása
![v2v ábra](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

Ebben a forgatókönyvben csatlakoztatjuk a TestVNet1 és a TestVNet5 virtuális hálózatot. A TestVNet1 és a TestVNet5 eltérő előfizetésekben található. A konfiguráláshoz használt lépések egy további, virtuális hálózatok közötti kapcsolatot hoznak létre a TestVNet1 és a TestVNet5 összekapcsolásához. 

A különbség itt az, hogy a konfigurációs lépések egy részét külön PowerShell-munkamenetben kell elvégezni a második előfizetés környezetében. Ez különösen akkor van így, ha a két előfizetés különböző szervezetekhez tartozik. 

Az útmutató a fent leírt lépések folytatása. Az [1. lépés](#Step1) és a [2. lépés](#Step2) elvégzésével hozza létre és konfigurálja a TestVNet1-et, valamint a TestVNet1 VPN-átjáróját. Miután elvégezte az 1. lépést és a 2. lépést, folytassa az 5. lépéssel a TestVNet5 létrehozásához.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>5. lépés – A további IP-címtartományok ellenőrzése
Fontos ügyelni arra, hogy az új virtuális hálózat (TestVNet5) IP-címtere ne legyen átfedésben a többi virtuális hálózati vagy hálózati átjárói tartománnyal. 

Ebben a példában a virtuális hálózatok különböző szervezetekhez is tartozhatnak. A gyakorlatban a TestVNet5 hálózathoz a következő értékeket használhatja:

**Értékek a TestVNet5-höz:**

* Virtuális hálózat neve: TestVNet5
* Erőforráscsoport: TestRG5
* Hely: Japan East
* TestVNet5: 10.51.0.0/16 és 10.52.0.0/16
* Előtér: 10.51.0.0/24
* Háttér: 10.52.0.0/24
* Átjáróalhálózat: 10.52.255.0.0/27
* DNS-kiszolgáló: 8.8.8.8
* Átjáró neve: VNet5GW
* Nyilvános IP-cím: VNet5GWIP
* VPN típusa: RouteBased
* Kapcsolat: VNet5toVNet1
* Kapcsolat típusa: VNet2VNet

**További értékek a TestVNet1-hez:**

* Kapcsolat: VNet1toVNet5

### <a name="step-6---create-and-configure-testvnet5"></a>6. lépés – A TestVNet5 létrehozása és konfigurálása
Ezt a lépést az új előfizetés környezetében kell elvégezni. Ezt a részt azon másik szervezet rendszergazdájának kell elvégeznie, amely az előfizetés tulajdonosa.

1. A változók deklarálása
   
    Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.
   
        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"
2. Kapcsolódás az 5. előfizetéshez
   
    Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:
   
        Login-AzureRmAccount
   
    Keresse meg a fiókot az előfizetésekben.
   
        Get-AzureRmSubscription 
   
    Válassza ki a használni kívánt előfizetést.
   
        Select-AzureRmSubscription -SubscriptionName $Sub5
3. Új erőforráscsoport létrehozása
   
        New-AzureRmResourceGroup -Name $RG5 -Location $Location5
4. A TestVNet4 alhálózatkonfigurációinak létrehozása
   
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
5. A TestVNet5 létrehozása
   
        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
6. Nyilvános IP-cím kérése
   
        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic
7. Az átjáró konfigurációjának létrehozása
   
        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
8. A TestVNet5 átjárójának létrehozása
   
        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>7. lépés – Az átjárók csatlakoztatása
Ebben a példában, mivel az átjárók különböző előfizetésekben találhatóak, a lépést felosztottuk két PowerShell-munkamenetre, amelyek jelölése [1. előfizetés] és [5. előfizetés].

1. **[1. előfizetés]** Az 1. előfizetés virtuális hálózati átjárójának beszerzése
   
    Jelentkezzen be, és kapcsolódjon az 1. előfizetéshez.
   
        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   
    Másolja a következő elemek kimenetét, és küldje el a másolatokat az 5. előfizetés rendszergazdájának e-mailben vagy egyéb módon.
   
        $vnet1gw.Name
        $vnet1gw.Id
   
    Ez a két elem hasonló értékekkel fog rendelkezni, mint a következő kimeneti példában látható értékek:
   
        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
2. **[5. előfizetés]** Az 5. előfizetés virtuális hálózati átjárójának beszerzése
   
    Jelentkezzen be, és kapcsolódjon az 5. előfizetéshez.
   
        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   
    Másolja a következő elemek kimenetét, és küldje el a másolatokat az 1. előfizetés rendszergazdájának e-mailben vagy egyéb módon.
   
        $vnet5gw.Name
        $vnet5gw.Id
   
    Ez a két elem hasonló értékekkel fog rendelkezni, mint a következő kimeneti példában látható értékek:
   
        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
3. **[1. előfizetés]** A TestVNet1–TestVNet5 kapcsolat létrehozása
   
    Ebben a lépésben a TestVNet1 felől a TestVNet5 felé irányuló kapcsolatot hozza létre. A különbség itt az, hogy a $vnet5gw nem szerezhető be közvetlenül, mert másik előfizetésben található. Ehhez létre kell hoznia egy új PowerShell-objektumot a fenti lépésekben az 1. előfizetésből átküldött értékekkel. Használja az alábbi példát. Cserélje le a nevet, az azonosítót és a megosztott kulcsot a saját értékeire. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.
   
    Kapcsolódjon az 1. előfizetéshez. 
   
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
4. **[5. előfizetés]** A TestVNet5–TestVNet1 kapcsolat létrehozása
   
    Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet5 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Itt ugyanúgy egy PowerShell-objektumot kell létrehozni az 1. előfizetésből szerzett értékek alapján. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek ebben a lépésben.
   
    Kapcsolódjon az 5. előfizetéshez.
   
        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>A kapcsolat ellenőrzése
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Következő lépések

* Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért tekintse meg a [Virtual Machines-dokumentációt](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md). 


