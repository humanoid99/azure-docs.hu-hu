---
title: "Az első Azure virtuális hálózat létrehozása | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre Azure virtuális hálózatot (VNet), hogyan csatlakoztathat két virtuális gépet (VM) a VNethez, illetve hogyan csatlakozhat a virtuális gépekhez."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: c731099cb91512f3bf0ecc2ffa5258788c90cd1b
ms.lasthandoff: 03/31/2017


---

# <a name="create-your-first-virtual-network"></a>Az első virtuális hálózat létrehozása

Megtudhatja, hogyan hozhat létre virtuális hálózatot (VNet) két alhálózattal, hogyan hozhat létre két virtuális gépet (VM), illetve hogyan csatlakoztathatja az egyes virtuális gépeket az alhálózatok egyikéhez az alábbi ábrának megfelelően:

![Virtuális hálózat diagramja](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Az Azure virtuális hálózat (VNet) a saját, felhőben található hálózatának megfelelője. Vezérelheti az Azure-hálózat beállításait, valamint DHCP-címblokkokat, DNS-beállításokat, biztonsági házirendeket és útválasztásokat adhat meg. A VNet fogalmaival kapcsolatos további tudnivalókért olvassa el [A virtuális hálózatok áttekintése](virtual-networks-overview.md) című cikket. Az ábrán látható erőforrások létrehozásához hajtsa végre a következő lépéseket:

1. [Virtuális hálózat létrehozása két alhálózattal](#create-vnet)
2. [Két virtuális gép létrehozása egy-egy hálózati adapterrel (NIC)](#create-vms), és hálózati biztonsági csoport (NSG) hozzárendelése az egyes hálózati adapterekhez
3. [Csatlakozás a virtuális gépekhez, illetve a virtuális gépekről](#connect-to-from-vms)
4. [Az összes erőforrás törlése](#delete-resources). A gyakorlat során létrehozott egyes erőforrásokkal kapcsolatban díjak merülhetnek fel, amíg azok üzemelnek. A költségek minimálisra csökkentése érdekében a gyakorlat befejezését követően hajtsa végre a jelen szakaszban szereplő lépéseket a létrehozott erőforrások törléséhez.

A cikkben szereplő lépések végrehajtása után alapszintű ismeretekkel fog rendelkezni a virtuális hálózatok használatáról. További lépéseket is talál, amelyekből megtudhatja, hogyan használhatók a virtuális hálózatok magasabb szinten.

## <a name="create-vnet"></a>Virtuális hálózat létrehozása két alhálózattal

Virtuális hálózat két alhálózattal történő létrehozásához hajtsa végre az alábbi lépéseket. A különböző alhálózatok általában az alhálózatok közötti forgalom szabályozására használhatók.

1. Jelentkezzen be az [Azure portálra](<https://portal.azure.com>). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free). 
2. A portál **Kedvencek** panelén kattintson az **Új** lehetőségre.
3. Az **Új** panelen kattintson a **Hálózat** elemre. A **Hálózat** panelen kattintson a **Virtuális hálózat** elemre, amint a következő képen látható:

    ![Virtuális hálózat diagramja](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  A **Virtuális hálózat** panelen hagyja kiválasztva a *Resource Manager* üzembehelyezési modellt, majd kattintson a **Létrehozás** lehetőségre.
5.  A megjelenő **Virtuális hálózat létrehozása** panelen adja meg a következő értékeket, majd kattintson a **Létrehozás** elemre:

    |**Beállítás**|**Érték**|**Részletek**|
    |---|---|---|
    |**Name (Név)**|*MyVNet*|A névnek egyedinek kell lennie az erőforráscsoporton belül.|
    |**Címtér**|*10.0.0.0/16*|Tetszés szerint bármilyen címteret megadhat a CIDR-jelölésrendszerben.|
    |**Alhálózat neve**|*Előtér*|Az alhálózat nevének egyedinek kell lennie a virtuális hálózaton belül.|
    |**Alhálózati címtartomány**|*10.0.0.0/24*| A megadott tartománynak léteznie kell a virtuális hálózathoz megadott címtéren belül.|
    |**Előfizetés**|*[Az Ön előfizetése]*|Válasszon ki egy előfizetést, amelyben létrehozza a virtuális hálózatot. A virtuális hálózat egyetlen előfizetésben jön létre.|
    |**Erőforráscsoport**|**Új létrehozása:** *MyRG*|Hozzon létre egy erőforráscsoportot. Az erőforráscsoport nevének egyedinek kell lennie a kiválasztott előfizetésen belül. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) áttekintése című cikket.|
    |**Hely**|*USA nyugati régiója*| Általában a fizikai helyéhez legközelebbi hely van kiválasztva.|

    A virtuális hálózat néhány másodperc alatt létrejön. A létrehozást követően megjelenik az Azure Portal irányítópultja.

6. Ha létrehozta a virtuális hálózatot, az Azure Portal **Kedvencek** panelén kattintson az **Összes erőforrás** elemre. Az **Összes erőforrás** panelen kattintson a **MyVNet** virtuális hálózatra. Ha a kiválasztott előfizetésben már több erőforrás szerepel, a virtuális hálózat egyszerű eléréséhez beírhatja a *MyVNet* nevet a **Szűrés név alapján...** mezőbe.
7. Megnyílik a **MyVNet** panel, és megjeleníti a virtuális hálózat információit a következő ábrán láthatók szerint:

    ![Virtuális hálózat diagramja](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. Az előző ábrán láthatóknak megfelelően kattintson az **Alhálózatok** elemre a virtuális hálózatban lévő alhálózatok listájának megjelenítéséhez. Az egyetlen meglévő alhálózat az 5. lépésben létrehozott alhálózat, az **Előtér**.
9. A MyVNet – Alhálózatok panelen kattintson a **+ alhálózat** elemre egy, a következő adatokkal rendelkező alhálózat létrehozásához, majd kattintson az **OK** gombra az alhálózat létrehozásához.

    |**Beállítás**|**Érték**|**Részletek**|
    |---|---|---|
    |**Name (Név)**|*Háttér*|A névnek egyedinek kell lennie a virtuális hálózaton belül.|
    |**Címtartomány**|*10.0.1.0/24*|A megadott tartománynak léteznie kell a virtuális hálózathoz megadott címtéren belül.|
    |**Hálózati biztonsági csoport** és **Útválasztási táblázat**|*Nincs* (alapértelmezett)|A hálózati biztonsági csoportok (NSG-k) leírása a cikk későbbi részében található. A felhasználó által megadott útválasztással kapcsolatos további információkért olvassa el a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md) ismertető cikket.|

10. Miután hozzáadta az új alhálózatot a virtuális hálózathoz, bezárhatja a **MyVNet – Alhálózatok** panelt, majd az **Összes erőforrás** panelt is.

## <a name="create-vms"></a>Virtuális gépek létrehozása

A létrehozott virtuális hálózattal és alhálózatokkal létrehozhatja a virtuális gépeket. Ebben a gyakorlatban mindkét virtuális gép Windows Server operációs rendszert futtat, de bármilyen, az Azure által támogatott operációs rendszert képesek futtatni, beleértve több különböző Linux-disztribúciót is.

### <a name="create-web-server-vm"></a>A webkiszolgáló virtuális gép létrehozása

A webkiszolgáló virtuális gép létrehozásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portal Kedvencek panelén kattintson az **Új**, a **Számítás** majd a **Windows Server 2016 Datacenter** elemre.
2. A **Windows Server 2016 Datacenter** panelen kattintson a **Létrehozás** lehetőségre.
3. A megjelenő **Alapvető beállítások** panelen adja meg vagy válassza ki az alábbi értékeket, majd kattintson az **OK** gombra:

    |**Beállítás**| **Érték**|**Részletek**|
    |---|---|---|
    |**Name (Név)**|*MyWebServer*|Ez a virtuális gép fog webkiszolgálóként szolgálni, amelyhez az internetes erőforrások csatlakoznak.|
    |**Virtuális merevlemez típusa**|*SSD*|
    |**Felhasználónév**|*A választása szerint*|
    |**Jelszó és Jelszó megerősítése**|*A választása szerint*|
    | **Előfizetés**|*<Your subscription>*|Az előfizetésnek ugyanannak az előfizetésnek kell lennie, amelyet a jelen cikk [Virtuális hálózat létrehozása két alhálózattal](#create-vnet) című szakaszának 5. lépésében kiválasztott. A virtuális géphez csatlakoztatott virtuális hálózatnak a virtuális géppel megegyező előfizetésen belül kell lennie.|
    |**Erőforráscsoport**|**Meglévő használata:** Válassza a *MyRG* elemet|Bár ugyanazt az erőforráscsoportot használjuk, mint a VNet esetében, az erőforrásoknak nem szükséges ugyanabban az erőforráscsoportban lenniük.|
    |**Hely**|*USA nyugati régiója*|A helynek ugyanannak a helynek kell lennie, amelyet a jelen cikk [Virtuális hálózat létrehozása két alhálózattal](#create-vnet) című szakaszának 5. lépésében meghatározott. A virtuális gépeknek és virtuális hálózatnak, amelyhez csatlakoznak, ugyanazon a helyen kell lenniük.|

4. A **Méret kiválasztása** panelen kattintson a *DS1_V2 Standard* elemre, majd a **Kiválasztás** lehetőségre. Az Azure által támogatott összes Windows rendszerű virtuális gép listáját megtalálja a [Windows rendszerű virtuális gépek méreteit](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ismertető cikkben.
5. A **Beállítások** panelen adja meg vagy válassza ki az alábbi értékeket, majd kattintson az **OK** gombra:

    |**Beállítás**|**Érték**|**Részletek**|
    |---|---|---|
    |**Tároló: Felügyelt lemezek használata**|*Igen*||
    |**Virtuális hálózat**| Válassza a *MyVNet* elemet|Bármilyen olyan VNetet választhat, amely ugyanazon a helyen található, mint a létrehozni kívánt virtuális gép. A virtuális hálózatokkal és alhálózatokkal kapcsolatos további tudnivalókért olvassa el a [Virtuális hálózat](virtual-networks-overview.md) című cikket.|
    |**Alhálózat**|Válassza ki az *Előtér* elemet|A VNetben lévő bármelyik alhálózatot kiválaszthatja.|
    |**Nyilvános IP-cím**|Fogadja el az alapértelmezett beállítást|A nyilvános IP-cím lehetővé teszi, hogy az interneten keresztül csatlakozzon a virtuális géphez. Ha többet szeretne megtudni a nyilvános IP-címekről, olvassa el az [IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) című cikket.|
    |**Hálózati biztonsági csoport (tűzfal)**|Fogadja el az alapértelmezett beállítást|A beállítások megtekintéséhez kattintson az **(új) MyWebServer-nsg** alapértelmezett NSG-re, amelyet a portál hozott létre. A megnyíló **Hálózati biztonsági csoport létrehozása** panelen figyelje meg, hogy egy olyan bejövő szabállyal rendelkezik, amely engedélyezi a TCP/3389 (RDP) forgalmat bármilyen forrás-IP-címről.|
    |**Minden egyéb érték**|Fogadja el az alapértelmezett beállításokat|A további beállításokkal kapcsolatban olvassa el a [Tudnivalók a virtuális gépekről](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket.|

    A hálózati biztonsági csoportok (NSG) lehetővé teszik, hogy bejövő/kimenő szabályokat hozzon létre a virtuális gépekről érkező, illetve az azokra irányuló forgalom típusának megfelelően. Alapértelmezés szerint a virtuális gépre irányuló minden bejövő forgalom le van tiltva. Az éles webkiszolgáló esetében további bejövő szabályokat adhat hozzá a TCP/80 (HTTP) és a TCP/443 (HTTPS) portok számára. Nincs a kimenő forgalomra vonatkozó szabály, mivel alapértelmezés szerint minden kimenő forgalom engedélyezett. A saját házirendjeinek megfelelően szabadon hozzáadhat/eltávolíthat szabályokat a forgalom szabályozására. Ha többet szeretne megtudni az NSG-kről, olvassa el a [Hálózati biztonsági csoportok](virtual-networks-nsg.md) című cikket.

6.  Az **Összegzés** panelen tekintse át a beállításokat, és kattintson az **OK** gombra a virtuális gép létrehozásához. A virtuális gép létrehozásakor egy állapotjelző csempe jelenik meg a portál irányítópultján. A virtuális gép létrehozása eltarthat néhány percig. Nem szükséges megvárnia, amíg a folyamat a végére ér. Folytathatja a következő lépéssel, miközben a rendszer létrehozza a virtuális gépet.

### <a name="create-database-server-vm"></a>Az adatbázis-kiszolgáló virtuális gép létrehozása

Az adatbázis-kiszolgáló virtuális gép létrehozásához hajtsa végre az alábbi lépéseket:

1.  A Kedvencek panelen kattintson az **Új**, a **Számítás** majd a **Windows Server 2016 Datacenter** elemre.
2.  A **Windows Server 2016 Datacenter** panelen kattintson a **Létrehozás** lehetőségre.
3.  Az **Alapvető beállítások** panelen adja meg vagy válassza ki az alábbi értékeket, majd kattintson az **OK** gombra:

    |**Beállítás**|**Érték**|**Részletek**|
    |---|---|---|
    |**Name (Név)**|*MyDBServer*|Ez a virtuális gép fog adatbázis-kiszolgálóként szolgálni, amelyhez a webkiszolgáló csatlakozik, de az internetről nem elérhető.|
    |**Virtuális merevlemez típusa**|*SSD*||
    |**Felhasználónév**|A választása szerint||
    |**Jelszó és Jelszó megerősítése**|A választása szerint||
    |**Előfizetés**|<Your subscription>|Az előfizetésnek ugyanannak az előfizetésnek kell lennie, amelyet a jelen cikk [Virtuális hálózat létrehozása két alhálózattal](#create-vnet) című szakaszának 5. lépésében kiválasztott.|
    |**Erőforráscsoport**|**Meglévő használata:** Válassza a *MyRG* elemet|Bár ugyanazt az erőforráscsoportot használjuk, mint a VNet esetében, az erőforrásoknak nem szükséges ugyanabban az erőforráscsoportban lenniük.|
    |**Hely**|*USA nyugati régiója*|A helynek ugyanannak a helynek kell lennie, amelyet a jelen cikk [Virtuális hálózat létrehozása két alhálózattal](#create-vnet) című szakaszának 5. lépésében meghatározott.|

4.  A **Méret kiválasztása** panelen kattintson a *DS1_V2 Standard* elemre, majd a **Kiválasztás** lehetőségre.
5.  A **Beállítások** panelen adja meg vagy válassza ki az alábbi értékeket, majd kattintson az **OK** gombra:

    |**Beállítás**|**Érték**|**Részletek**|
    |----|----|---|
    |**Tároló: Felügyelt lemezek használata**|*Igen*||
    |**Virtuális hálózat**|Válassza a *MyVNet* elemet|Bármilyen olyan VNetet választhat, amely ugyanazon a helyen található, mint a létrehozni kívánt virtuális gép.|
    |**Alhálózat**|A *Háttér* kiválasztásához kattintson az **Alhálózat** mezőbe, majd válassza a **Háttér** lehetőséget az **Alhálózat kiválasztása** panelen.|A VNetben lévő bármelyik alhálózatot kiválaszthatja.|
    |**Nyilvános IP-cím**|Nincs – Kattintson az alapértelmezett címre, majd a **Nincs** lehetőségre a **Nyilvános IP-cím kiválasztása** panelen.|Nyilvános IP-cím nélkül csak az ugyanazon a VNeten lévő többi virtuális gépről lehet a virtuális gépre csatlakozni. Az internetről nem lehet közvetlen kapcsolatot létesíteni vele.|
    |**Hálózati biztonsági csoport (tűzfal)**|Fogadja el az alapértelmezett beállítást| A MyWebServer virtuális géphez létrehozott NSG-hez hasonlóan ez az NSG is ugyanazzal az alapértelmezett bejövő szabállyal rendelkezik. Hozzáadhat egy további bejövő szabályt a TCP/1433-as (MS SQL) porthoz egy adatbázis-kiszolgáló esetén. Nincs a kimenő forgalomra vonatkozó szabály, mivel alapértelmezés szerint minden kimenő forgalom engedélyezett. A saját házirendjeinek megfelelően szabadon hozzáadhat/eltávolíthat szabályokat a forgalom szabályozására.|
    |**Minden egyéb érték**|Fogadja el az alapértelmezett beállításokat||

6.  Az **Összegzés** panelen tekintse át a beállításokat, és kattintson az **OK** gombra a virtuális gép létrehozásához. A virtuális gép létrehozásakor egy állapotjelző csempe jelenik meg a portál irányítópultján. A virtuális gép létrehozása eltarthat néhány percig. Nem szükséges megvárnia, amíg a folyamat a végére ér. Folytathatja a következő lépéssel, miközben a rendszer létrehozza a virtuális gépet.

## <a name="review"></a>Erőforrások áttekintése

Bár Ön egy VNetet és két virtuális gépet hozott létre, az Azure Portal több további erőforrást is létrehozott az Ön számára a MyRG erőforráscsoportban. A MyRG erőforráscsoport tartalmát az alábbi lépéseket követve tekintheti át:

1. A **Kedvencek** panelen kattintson a **További szolgáltatások** elemre.
2. A **További szolgáltatások** panelen írja be az *Erőforráscsoportok* kifejezést a *Szűrő* feliratú mezőbe. Kattintson a szűrt listában megjelenő **Erőforráscsoportok** elemre.
3. Az **Erőforráscsoportok** panelen kattintson a *MyRG* erőforráscsoportra. Ha több meglévő erőforráscsoporttal is rendelkezik az előfizetésében, beírhatja a *MyRG* kifejezést a *Szűrés név alapján…* mezőbe a MyRG erőforráscsoport gyors eléréséhez.
4.  A **MyRG** panelen láthatja, hogy az erőforráscsoport 12 erőforrást tartalmaz, amint az alábbi képen is látható:

    ![Erőforráscsoport tartalma](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

A virtuális gépekkel, lemezekkel és tárfiókokkal kapcsolatos további információkért olvassa el a [Virtuális gép](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a [Lemez](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és a [Tárfiók](../storage/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című áttekintő cikkeket. A listában látható a portál által létrehozott két alapértelmezett NSG. Láthatja továbbá, hogy a portál létrehozott két hálózati adapter (NIC) típusú erőforrást is. A virtuális gépek a hálózati adapterek segítségével tudnak más erőforrásokhoz kapcsolódni a VNeten. Ha többet szeretne megtudni a NIC-ekkről, olvassa el a [NIC](virtual-network-network-interface.md) című cikket. A portál létrehozott egy nyilvános IP-cím típusú erőforrást is. A Nyilvános IP-címek az egyik beállítás a nyilvános IP-cím típusú erőforrásokhoz. Ha többet szeretne megtudni a nyilvános IP-címekről, olvassa el az [IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) című cikket.

## <a name="connect-to-from-vms"></a>Kapcsolódás a virtuális gépekhez

Miután létrehozta a VNetet és a két virtuális gépet, az alábbi szakaszokban ismertetett lépéseket követve csatlakozhat a virtuális gépekhez:

### <a name="connect-from-internet"></a>Csatlakozás a webkiszolgáló virtuális géphez az internetről

A webkiszolgáló virtuális géphez az internetről az alábbi lépések végrehajtásával kapcsolódhat:

1. A portálon nyissa meg a MyRG erőforráscsoportot a jelen cikk [Erőforrások áttekintése](#review) című szakaszában ismertetett lépéseket követve.
2. A **MyRG** panelen kattintson a **MyWebServer** virtuális gépre.
3. A **MyWebServer** panelen kattintson a **Csatlakozás** gombra, amint az alábbi képen is látható:

    ![Csatlakozás webkiszolgáló virtuális géphez](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. Engedélyezze, hogy a böngésző letöltse a *MyWebServer.rdp* fájlt, majd nyissa meg azt.
5. Ha egy párbeszédpanel arról értesíti, hogy a távoli kapcsolat közzétevője nem azonosítható, kattintson a **Csatlakozás** gombra.
6. Hitelesítő adatai megadásakor ügyeljen arra, hogy a jelen cikk [a webkiszolgáló virtuális gép létrehozását](#create-web-server-vm) ismertető szakaszának 3. lépésében megadott felhasználónévvel és jelszóval jelentkezzen be. Ha a megjelenő **Windows rendszerbiztonság** mezőben nem a megfelelő hitelesítő adatok láthatók, a **További lehetőségek**, majd a **Másik fiók használata** elemre kattintva adhatja meg a megfelelő felhasználónevet és jelszót. Kattintson az **OK** gombra a virtuális géphez való csatlakozáshoz.
7. Ha egy **Távoli asztali kapcsolat**-üzenet arról értesíti, hogy a távoli számítógép nem azonosítható, kattintson az **Igen** gombra.
8. Ezzel csatlakozott a MyWebServer virtuális géphez az internetről. A következő szakaszban foglalt lépések végrehajtásához hagyja nyitva a távoli asztali kapcsolatot.

A távoli kapcsolat a jelen cikk [Virtuális hálózat létrehozása két alhálózattal](#create-vnet) című szakaszának 5. lépésében a portál által létrehozott nyilvános IP-címerőforráshoz rendelt nyilvános IP-címre mutat. A kapcsolódás engedélyezett, mivel a **MyWebServer-nsg** NSG-ben létrehozott alapértelmezett szabály engedélyezte a TCP/3389-es (RDP) port bejövő forgalmát a virtuális gépre bármely forrás-IP-címről. Ha bármely más porton keresztül próbál csatlakozni a virtuális géphez, a csatlakozás meghiúsul, hacsak nem vesz fel további bejövő szabályokat az NSG-be további portok engedélyezéséhez.

>[!NOTE]
>Ha további bejövő szabályokat vesz fel az NSG-be, győződjön meg róla, hogy az adott portok nyitva legyenek a Windows tűzfalon is, máskülönben a csatlakozás sikertelen lesz.
>

### <a name="connect-to-internet"></a>Csatlakozás az internethez a webkiszolgáló virtuális gépről

A webkiszolgáló virtuális gépről kifelé az internethez az alábbi lépések végrehajtásával kapcsolódhat:

1. Ha még nem rendelkezik nyitott távoli kapcsolattal a MyWebServer virtuális géphez, létesítsen egyet a jelen cikk [Csatlakozás a webkiszolgáló virtuális géphez az internetről](#connect-from-internet) című szakaszában ismertetett lépéseket követve.
2. Indítsa el az Internet Explorert a Windows asztalán. Az **Internet Explorer 11 beállítása** párbeszédpanelen kattintson a **Ne használja az ajánlott beállításokat**, majd az **OK** gombra. Éles kiszolgálók esetében javasoljuk az ajánlott beállítások elfogadását.
3. Az Internet Explorer címsorába írja be a [bing.com](http:www.bing.com) címet. Ha megjelenik egy Internet Explorer-párbeszédpanel, kattintson a **Hozzáadás** gombra, majd a **Hozzáadás** gombra a **Megbízható helyek** párbeszédpanelen, és végül a **Bezárás** gombra. Ismételje meg ezt a műveletet az összes többi Internet Explorer-párbeszédpanelen is.
4. A Bing keresőoldalán írja be a *whatsmyipaddress* kifejezést, majd kattintson a nagyítóüveget ábrázoló gombra. A Bing a virtuális gép létrehozásakor a portál által létrehozott nyilvános IP-címerőforráshoz rendelt nyilvános IP-címet adja vissza. Ha megvizsgálja a **MyWebServer-ip** erőforrás beállításait, ugyanezt az IP-címet láthatja a nyilvános IP-címerőforráshoz rendelve, amint az az alábbi ábrán is látható. A virtuális géphez rendelt IP-cím azonban ettől eltér.

    ![Csatlakozás webkiszolgáló virtuális géphez](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  A következő szakaszban foglalt lépések végrehajtásához hagyja nyitva a távoli asztali kapcsolatot.

Az internethez csatlakozhat a virtuális gépről, mivel alapértelmezés szerint minden kimenő kapcsolat engedélyezve van. A kimenő kapcsolatok korlátozásához felvehet további szabályokat a NIC-re alkalmazott NSG-be, vagy arra az alhálózatra, amelyhez a NIC csatlakozik, illetve mindkettőbe.

Ha a virtuális gépet a portál használatával leállított (felszabadított) állapotba helyezi, a nyilvános IP-cím módosulhat. Ha szükséges, hogy a nyilvános IP-cím soha ne változzon, használhatja a statikus foglalási módszert is az IP-címre a dinamikus helyett (amely utóbbi az alapértelmezett módszer). Ha többet szeretne megtudni a foglalási módszerek közti különbségekről, olvassa el az [IP-címtípusokat és foglalási módszereket](virtual-network-ip-addresses-overview-arm.md) ismertető cikket.

### <a name="webserver-to-dbserver"></a>Csatlakozás az adatbázis-kiszolgáló virtuális géphez a webkiszolgáló virtuális gépről

Az adatbázis-kiszolgáló virtuális géphez a webkiszolgáló virtuális gépről az alábbi lépések végrehajtásával kapcsolódhat:

1. Ha még nem rendelkezik nyitott távoli kapcsolattal a MyWebServer virtuális géphez, létesítsen egyet a jelen cikk [Csatlakozás a webkiszolgáló virtuális géphez az internetről](#connect-from-internet) című szakaszában ismertetett lépéseket követve.
2. Kattintson a Start gombra a Windows asztal bal alsó sarkában, majd kezdje beírni a *távoli asztal* kifejezést. Amint a Start menü listájában megjelenik a **Távoli asztali kapcsolat** elem, kattintson rá.
3. A **Távoli asztali kapcsolat** párbeszédpanelen írja be a *MyDBServer* nevet a számítógép neveként, és kattintson a **Csatlakozás** gombra.
4. Adja meg a jelen cikk [adatbázis-kiszolgáló virtuális gép létrehozását](#create-database-server-vm) ismertető szakaszának 3. lépésében megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.
5. Ha egy párbeszédpanel arról értesíti, hogy a távoli számítógép nem azonosítható, kattintson az **Igen** gombra.
6. A következő szakaszban foglalt lépések végrehajtásához hagyja nyitva a távoli asztali kapcsolatot mindkét kiszolgálóhoz.

Az adatbázis-kiszolgáló virtuális géphez a webkiszolgáló virtuális gépről az alábbi okokból kifolyólag lehetséges csatlakozni:

- A TCP/3389-es bejövő kapcsolatok bármely forrás-IP számára engedélyezettek a jelen cikk [adatbázis-kiszolgáló virtuális gép létrehozását](#create-database-server-vm) ismertető szakaszának 5. lépésében létrehozott alapértelmezett NSG-ben.
- A kapcsolódást a webkiszolgáló virtuális gépről kezdeményezte, amely ugyanahhoz a VNethez kapcsolódik, mint az adatbázis-kiszolgáló virtuális gép. Ha olyan virtuális géphez szeretne kapcsolódni, amely nem rendelkezik kiosztott nyilvános IP-címmel, egy ugyanazon a VNeten lévő virtuális gépről kell csatlakoznia, még ha a virtuális gép egy másik alhálózathoz kapcsolódik is.
- Bár a virtuális gépek eltérő alhálózatokhoz csatakoznak, az Azure létrehozza az alapértelmezett útvonalakat, amelyek révén az alhálózatok között kapcsolatot lehet létesíteni. Az alapértelmezett útvonalakat felül is bírálhatja, ha saját útvonalakat hoz létre. Ha többet szeretne megtudni az Azure-beli útválasztásról, olvassa el a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md) ismertető cikket.

Ha az internetről kísérel meg távoli kapcsolatot kezdeményezni az adatbázis-kiszolgáló virtuális géphez, ahogy tette azt a jelen cikk [Csatlakozás a webkiszolgáló virtuális géphez az internetről](#connect-from-internet) című szakaszában, azt látja, hogy a **Csatlakozás** lehetőség ki van szürkítve. A Csatlakozás azért van kiszürkítve, mivel nincs nyilvános IP-cím kiosztva a virtuális gép számára, így az internetről felé irányuló bejövő kapcsolatok nem engedélyezettek.

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>Csatlakozás az internethez az adatbázis-kiszolgáló virtuális gépről

Az adatbázis-kiszolgáló virtuális gépről kifelé az internethez az alábbi lépések végrehajtásával kapcsolódhat:

1. Ha még nem rendelkezik nyitott távoli kapcsolattal a MyDBServer virtuális géphez a MyWebServer virtuális gépről, hajtsa végre a jelen cikk [Csatlakozás az adatbázis-kiszolgáló virtuális géphez a webkiszolgáló virtuális gépről](#webserver-to-dbserver) című szakaszában ismertetett lépéseket.
2. A MyDBServer virtuális gép Windows asztaláról indítsa el az Internet Explorert, és nyugtázza a párbeszédpaneleket, ahogy tette azt a jelen cikk [Csatlakozás az internethez a webkiszolgáló virtuális gépről](#connect-to-internet) című szakaszának 2. és 3. lépésében is.
3. A címsorba írja be a [bing.com](http:www.bing.com) címet.
4. A megjelenő Internet Explorer-párbeszédpanelen kattintson a **Hozzáadás** gombra, majd megint a **Hozzáadás** gombra, és végül a **Bezárás** gombra a **Megbízható helyek** párbeszédpanelen. Hajtsa végre ezeket a lépéseket az összes többi megjelenő párbeszédpanelen is.
5. A Bing keresőoldalán írja be a *whatsmyipaddress* kifejezést, majd kattintson a nagyítóüveget ábrázoló gombra. A Bing az Azure-infrastruktúra által a virtuális gép számára aktuálisan lefoglalt nyilvános IP-címet adja vissza. 6. Zárja be a MyWebServer virtuális gépről a MyDBServer virtuális gépre nyitott távoli asztalt, majd zárja be a MyWebServer virtuális gépre mutató távoli kapcsolatot.

Az internetre mutató kimenő kapcsolat engedélyezett, mivel alapértelmezés szerint minden kimenő kapcsolat engedélyezett, még ha nincs is nyilvános IP-címerőforrás hozzárendelve a MyDBServer virtuális géphez. Alapértelmezés szerint minden virtuális gép képes kifelé csatlakozni az internetre, akár rendelkezik hozzárendelt nyilvános IP-címerőforrással, akár nem. Az internetről azonban nem lehetséges úgy kapcsolódni a nyilvános IP-címhez, ahogy a nyilvános IP-címerőforrással rendelkező MyWebServer virtuális gép esetében lehetséges volt.

## <a name="delete-resources"></a>Az összes erőforrás törlése

A jelen cikkben létrehozott összes erőforrás törléséhez hajtsa végre az alábbi lépéseket:

1. A jelen cikkben létrehozott MyRG erőforráscsoport megtekintéséhez hajtsa végre a cikk [Erőforrások áttekintése](#review) című szakaszában ismertetett 1–3. lépést. Ismét tekintse át az erőforráscsoportban lévő erőforrásokat. Ha a MyRG erőforráscsoportot a fentebb ismertetett lépésekkel hozta létre, a 4. lépésnél található ábrán látható 12 erőforrás jelenik meg.
2. A MyRG panelen kattintson a **Törlés** gombra.
3. A portál megköveteli, hogy az erőforráscsoport törlésének megerősítéséhez beírja annak nevét. Ha a jelen cikk [Erőforrások áttekintése](#review) című szakaszának 4. lépésében feltüntetett erőforrásoktól eltérő erőforrások is megjelennek, kattintson a **Mégse** gombra. Ha csak a jelen cikk keretében létrehozott 12 erőforrás jelenik meg, adja meg a *MyRG* nevet az erőforráscsoport nevénél, majd kattintson a **Törlés** gombra. Az erőforráscsoport törlésével az abban foglalt összes erőforrás törölve lesz, ezért mindenképp ellenőrizze az erőforráscsoportok tartalmát azok törlése előtt. A portál törli az erőforráscsoportban lévő összes erőforrást, majd magát az erőforráscsoportot is. Ez a folyamat több percig is eltarthat.

## <a name="next-steps"></a>Következő lépések

Ebben a példában egy VNetet és két virtuális gépet hozott létre. A virtuális gépek létrehozása során megadott néhány egyéni beállítást, valamint elfogadott több alapértelmezett beállítást is. Javasoljuk, hogy mielőtt éles környezetben helyezne üzembe VNeteket és virtuális gépeket, olvassa el az alábbi cikkeket, hogy biztosan megismerje az összes elérhető beállítást:

- [Virtuális hálózatok](virtual-networks-overview.md)
- [Nyilvános IP-címek](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Hálózati illesztők](virtual-network-network-interface.md)
- [Hálózati biztonsági csoportok](virtual-networks-nsg.md)
- [Virtuális gépek](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

