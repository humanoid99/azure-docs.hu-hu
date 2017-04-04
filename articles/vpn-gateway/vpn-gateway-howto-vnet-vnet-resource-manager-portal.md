---
title: "Azure virtuális hálózat csatlakoztatása másik virtuális hálózathoz: Portal | Microsoft Docs"
description: "VPN Gateway-kapcsolatot hozhat létre virtuális hálózatok között a Resource Managerrel és az Azure Portallal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 102eab0e2e915521f8702b526dda886a2502f40b
ms.lasthandoff: 03/25/2017


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Virtuális hálózatok közötti kapcsolat konfigurálása az Azure Portallal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Klasszikus – Klasszikus portál](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózatok közötti kapcsolat a Resource Manager-alapú üzemi modellben a VPN Gateway és az Azure Portal használatával.

Amikor az Azure Portalt használja virtuális hálózatok összekapcsolásához, a virtuális hálózatoknak azonos előfizetésben kell lenniük. Ha a virtuális hálózatok különböző előfizetésekben találhatóak, akkor a [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) lépéseivel kapcsolhatja össze őket.

![v2v ábra](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Üzembe helyezési modellek és módszerek virtuális hálózatok közötti kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázat a virtuális hálózatok közötti kapcsolati konfigurációkhoz jelenleg elérhető üzembe helyezési modelleket és módszereket tartalmazza. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Virtuális hálózatok közötti társviszony**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Tudnivalók a virtuális hálózatok közötti kapcsolatokról
Egy virtuális hálózat egy másikkal való összekapcsolása (a virtuális hálózatok közötti kapcsolat) nagyon hasonlít egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus Azure VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE használatával. A csatlakoztatott virtuális hálózatok lehetnek különböző régiókban vagy különböző előfizetésekben is.

A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal egyesítik, ahogyan azt a következő diagram mutatja:

![Tudnivalók a kapcsolatokról](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Tudnivalók a kapcsolatokról")

### <a name="why-connect-virtual-networks"></a>Miért érdemes összekapcsolni a virtuális hálózatokat?
A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

* **Georedundancia és földrajzi jelenlét több régióban**
  
  * Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
  * Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.
* **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**
  
  * Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.

A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, [A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok](#faq) című részben talál.

### <a name="values"></a>Példabeállítások
Ha gyakorlatként használja ezeket a lépéseket, ezeket a konfigurációs mintaértékeket használhatja. Csupán a példa kedvéért több címteret használunk az egyes virtuális hálózatokhoz. Azonban a virtuális hálózatok közötti kapcsolat konfigurálásához nincs szükség több címtérre.

**Értékek a TestVNet1-hez:**

* Virtuális hálózat neve: TestVNet1
* Címtér: 10.11.0.0/16
  * Alhálózat neve: FrontEnd
  * Alhálózati címtartomány: 10.11.0.0/24
* Erőforráscsoport: TestRG1
* Hely: East US
* Címtér: 10.12.0.0/16
  * Alhálózat neve: BackEnd
  * Alhálózati címtartomány: 10.12.0.0/24
* Átjáróalhálózat neve: GatewaySubnet (a portálon ez a rész automatikusan ki lesz töltve)
  * Átjáróalhálózat címtartománya: 10.11.255.0/27
* DNS-kiszolgáló: Használja a saját DNS-kiszolgálója IP-címét.
* Virtuális hálózati átjáró neve: TestVNet1GW
* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú
* Termékváltozat: Válassza ki az átjáró használni kívánt termékváltozatát.
* Nyilvános IP-cím neve: TestVNet1GWIP
* Csatlakozási értékek:
  * Név: TestVNet1toTestVNet4
  * Megosztott kulcs: Saját maga létrehozhatja a megosztott kulcsot. Ebben a példában az abc123-at használjuk. Fontos, hogy az érték egyezzen, amikor létrehozza a virtuális hálózatok közötti kapcsolatot.

**Értékek a TestVNet4-hez:**

* Virtuális hálózat neve: TestVNet4
* Címtér: 10.41.0.0/16
  * Alhálózat neve: FrontEnd
  * Alhálózati címtartomány: 10.41.0.0/24
* Erőforráscsoport: TestRG1
* Hely: West US
* Címtér: 10.42.0.0/16
  * Alhálózat neve: BackEnd
  * Alhálózati címtartomány: 10.42.0.0/24
* Átjáró-alhálózat neve: GatewaySubnet (a portálon ez a rész automatikusan ki lesz töltve)
  * Átjáró-alhálózat címtartománya: 10.41.255.0/27
* DNS-kiszolgáló: Használja a saját DNS-kiszolgálója IP-címét.
* Virtuális hálózati átjáró neve: TestVNet4GW
* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú
* Termékváltozat: Válassza ki az átjáró használni kívánt termékváltozatát.
* Nyilvános IP-cím neve: TestVNet4GWIP
* Csatlakozási értékek:
  * Név: TestVNet4toTestVNet1
  * Megosztott kulcs: Saját maga létrehozhatja a megosztott kulcsot. Ebben a példában az abc123-at használjuk. Fontos, hogy az érték egyezzen, amikor létrehozza a virtuális hálózatok közötti kapcsolatot.

## <a name="CreatVNet"></a>1. A TestVNet1 létrehozása és konfigurálása
Ha már rendelkezik egy virtuális hálózattal, győződjön meg arról, hogy a beállításai kompatibilisek a VPN-átjáró kialakításával. Különösen ügyeljen az esetleges olyan alhálózatokra, amelyek átfedésbe kerülhetnek más hálózatokkal. Egymást átfedő alhálózatok esetén a kapcsolat nem fog megfelelően működni. Ha a virtuális hálózat a megfelelő beállításokkal lett konfigurálva, folytassa a [DNS-kiszolgáló megadása](#dns) szakaszban leírt lépésekkel.

### <a name="to-create-a-virtual-network"></a>Virtuális hálózat létrehozása
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. További címterek hozzáadása és alhálózatok létrehozása
Miután létrehozta a virtuális hálózatot, további címtereket adhat hozzá és alhálózatokat hozhat létre.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Átjáróalhálózat létrehozása
Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Ha lehetséges, a legjobb megoldás egy átjáróalhálózat létrehozása /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [Példabeállításokat](#values) az átjáróalhálózat létrehozásakor.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. DNS-kiszolgáló megadása (nem kötelező)
A virtuális hálózatok közötti kapcsolatokhoz nincs szükség DNS-re. Ha azonban azt szeretné, hogy a virtuális hálózatokon üzembe helyezett erőforrásokon működjön a névfeloldás, adjon meg egy DNS-kiszolgálót. Ezzel a beállítással megadhatja azt a DNS-kiszolgálót, amelyet névfeloldásra kíván használni ennél a virtuális hálózatnál. A beállítás nem hoz létre új DNS-kiszolgálót.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Virtuális hálózati átjáró létrehozása
Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Ennek végrehajtása 45 percig is eltarthat. Ha gyakorlatként hozza létre ezt a konfigurációt, használja ezeket a [Példabeállításokat](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. A TestVNet4 létrehozása és konfigurálása
Miután konfigurálta a TestVNet1-et, hozza létre a TestVNet4-et az előző lépések megismétlésével és az értékek a TestVNet4 értékeire való kicserélésével. A TestVNet4 konfigurálásához nem kell megvárnia, hogy befejeződjön a TestVNet1 virtuális hálózati átjárójának létrehozása. Ha a saját értékeit használja, győződjön meg róla, hogy a címterek nincsenek átfedésben azokkal a virtuális hálózatokkal, amelyekhez csatlakozni kíván.

## <a name="TestVNet1Connection"></a>7. A TestVNet1 kapcsolat konfigurálása
Miután mind a TestVNet1, mind a TestVNet4 virtuális hálózati átjárójának létrehozása befejeződött, létrehozhatja a virtuális hálózati átjárókapcsolatokat. Ebben a szakaszban hozza létre a kapcsolatot a VNet1 felől a VNet4 felé.

1. A **Minden erőforrás** részben navigáljon a virtuális hálózata virtuális hálózati átjárójához. Például: **TestVNet1GW**. A virtuális hálózati átjáró paneljének megnyitásához kattintson a **TestVNet1GW** elemre.
   
    ![Kapcsolatpanel](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Kapcsolatpanel")
2. Kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** panel megnyitásához.
3. A **Kapcsolat hozzáadása** panelen, a név mezőben adja meg a kapcsolat nevét. Például: **TestVNet1toTestVNet4**.
   
    ![Kapcsolat neve](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Kapcsolat neve")
4. A **Kapcsolat típusa** mezőben. válassza a **Virtuális hálózatok közötti** lehetőséget a legördülő listából.
5. Az **Első virtuális hálózati átjáró**mező értéke automatikusan ki lesz töltve, mert egy megadott virtuális hálózati átjáróból hozza létre a kapcsolatot.
6. A **Második virtuális hálózati átjáró**mezőben annak a virtuális hálózatnak a virtuális hálózati átjáróját kell megadni, amelyikhez létre szeretné hozni a kapcsolatot. Kattintson a **Másik virtuális hálózati átjáró kiválasztása** elemre a **Virtuális hálózati átjáró kiválasztása** panel megnyitásához.
   
    ![Kapcsolat hozzáadása](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Kapcsolat hozzáadása")
7. Tekintse át a panelen felsorolt virtuális hálózati átjárók listáját. Csak azok a virtuális hálózati átjárók jelennek meg, amelyek az előfizetésében szerepelnek. Ha olyan virtuális hálózati átjáróhoz szeretne kapcsolódni, amely nem szerepel az előfizetésében, olvassa el a [PowerShell-lel kapcsolatos cikket](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Kattintson arra a virtuális hálózati átjáróra, amelyhez csatlakozni kíván.
9. A **Megosztott kulcs** mezőben adja meg a kapcsolat megosztott kulcsát. A kulcsot generálhatja, vagy saját maga is létrehozhatja. Egy helyek közötti kapcsolat esetében a helyszíni eszközhöz és a virtuális hálózati átjáró kapcsolatához használt kulcs ugyanaz lenne. A fogalom itt is hasonló, azonban itt nem egy VPN-eszközhöz, hanem egy másik virtuális hálózati átjáróhoz való csatlakozásról van szó.
   
    ![Megosztott kulcs](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Megosztott kulcs")
10. A módosítások mentéséhez kattintson az **OK** gombra a panel alján.

## <a name="TestVNet4Connection"></a>8. A TestVNet4 kapcsolat konfigurálása
Ezután hozzon létre kapcsolatot a TestVNet4 felől a TestVNet1 felé. Ugyanazt a módszert használja, mint a TestVNet1 felől a TestVNet4 felé irányuló kapcsolat létrehozásakor. Ügyeljen arra, hogy ugyanazt a megosztott kulcsot használja.

## <a name="VerifyConnection"></a>9. A kapcsolat ellenőrzése
Ellenőrizze a kapcsolatot. Minden egyes virtuális hálózati átjáró esetében tegye a következőket:

1. Keresse meg a virtuális hálózati átjáró paneljét. Például: **TestVNet4GW**. 
2. A virtuális hálózati átjáró paneljén kattintson a **Kapcsolatok** elemre a virtuális hálózati átjáró kapcsolatpaneljének megtekintéséhez.

Tekintse át a kapcsolatokat, és ellenőrizze az állapotot. Amikor a kapcsolat létrejött, állapotértékként a **Sikeres** és a **Csatlakoztatva** szerepel.

![Sikeres](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Sikeres")

Kattintson duplán az egyes kapcsolatokra az adott kapcsolat információinak megtekintéséhez.

![Alapvető szolgáltatások](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Alapvető szolgáltatások")

## <a name="faq"></a>A virtuális hálózatok közötti kapcsolatokra vonatkozó szempontok
A virtuális hálózatok közötti kapcsolatokról további információt a gyakori kérdésekben talál.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért tekintse meg a [Virtual Machines-dokumentációt](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).

