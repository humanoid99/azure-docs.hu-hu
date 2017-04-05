---
title: "Számítógép csatlakoztatása Azure-beli virtuális hálózathoz pont–hely kapcsolat használatával: klasszikus Azure Portal | Microsoft Docs"
description: "Az Azure Portal használatával létrehozott pont–hely VPN Gateway-kapcsolattal biztonságosan csatlakozhat a klasszikus Azure Virtual Networkhöz."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: bc542cfbca3a27aec98b75e2b7ed05472419c3a7
ms.lasthandoff: 04/03/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal-classic"></a>Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>
>

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok akkor hasznosak, ha egy távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról, vagy akkor, ha csak néhány ügyfelet szeretne csatlakoztatni egy virtuális hálózathoz. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre.

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózat pont–hely kapcsolattal a klasszikus üzemi modellben az Azure Portal használatával. A Pont–hely kapcsolatokról további információt a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című részben talál.


### <a name="deployment-models-and-methods-for-p2s-connections"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázatban látható a két üzemi modell és a pont–hely konfigurációkhoz elérhető üzembe helyezési módszerek. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Alapvető munkafolyamat
![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

A következő szakaszok végigvezetik virtuális hálózat felé irányuló pont–hely kapcsolat kialakításán.

1. Virtuális hálózat és VPN Gateway létrehozása
2. Tanúsítványok előállítása
3. A .cer fájl feltöltése
4. A VPN-ügyfél konfigurációs csomagjának létrehozása
5. Az ügyfélszámítógép konfigurálása
6. Csatlakozás az Azure szolgáltatáshoz

### <a name="example-settings"></a>Példabeállítások
Használhatja a következő példabeállításokat:

* **Név: VNet1**
* **Címtér: 192.168.0.0/16**<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve: FrontEnd**
* **Alhálózati címtartomány: 192.168.1.0/24**
* **Előfizetés:**Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: East US**
* **Kapcsolat típusa: pont–hely**
* **Ügyfélcímtér: 172.16.201.0/24**. Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, a megadott címkészletből kapnak IP-címet.
* **Átjáró-alhálózat: 192.168.200.0/24**. Az átjáró-alhálózat neve „GatewaySubnet” kell legyen.
* **Méret:** Válassza ki az átjáró használni kívánt termékváltozatát.
* **Útválasztási típus: dinamikus**



## <a name="vnetvpn"></a>1. szakasz: Virtuális hálózat és VPN Gateway létrehozása

Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).
### <a name="createvnet"></a>1. rész: Virtuális hálózat létrehozása
Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. Az alábbi lépésekkel hozhat létre virtuális hálózatokat az Azure portállal:

1. Egy böngészőből keresse fel az [Azure portált](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Új** lehetőségre. A **Piactér keresése** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és rákattintva nyissa meg a **Virtuális hálózat** panelt.

    ![Virtuális hálózat keresése panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvnetportal700.png)
3. A Virtuális hálózat panel alján, az **Üzemi modell kiválasztása** listában válassza ki a **Klasszikus** elemet, majd kattintson a **Létrehozás** elemre.

    ![Üzemi modell kiválasztása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. A **Virtuális hálózat létrehozása** panelen konfigurálja a virtuális hálózat beállításait. Ezen a panelen adhatja majd hozzá az első címterét és egy önálló alhálózati címtartományt. A virtuális hálózat létrehozása után visszaléphet, és további alhálózatokat és címtereket vehet fel.

    ![Virtuális hálózat létrehozása panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg az **Előfizetés** résznél. Az előfizetéseket a legördülő menüben módosíthatja.
6. Kattintson az **Erőforráscsoport** elemre, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../azure-resource-manager/resource-group-overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
7. Ezután válassza ki a virtuális hálózatához tartozó **Hely** beállításokat. Ez a hely határozza meg a virtuális hálózaton üzembe helyezett erőforrások helyét.
8. Ha szeretné könnyen megtalálni virtuális hálózatát az irányítópulton, akkor válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.

    ![Rögzítés az irányítópulton](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. A Létrehozás gombra kattintva létrejön egy csempe az irányítópulton, amely a virtuális hálózat állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

    ![Virtuális hálózat csempéjének létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Miután létrehozta a virtuális hálózatot, hozzáadhatja a DNS-kiszolgáló IP-címét a névfeloldás kezelésének érdekében. Nyissa meg a virtuális hálózat beállításait, kattintson a DNS-kiszolgálókra, majd adja hozzá a használni kívánt DNS-kiszolgáló IP-címét. A beállítás nem hoz létre új DNS-kiszolgálót. Ügyeljen arra, hogy olyan DNS-kiszolgálókat adjon hozzá, amelyekkel az erőforrások kommunikálni tudnak.

Miután létrejött a virtuális hálózata, megjelenik a **Létrehozva** elem a klasszikus Azure-portál Hálózatok lapján az **Állapot** listában.

### <a name="gateway"></a>2. rész: Átjáróalhálózat és dinamikus útválasztású átjáró létrehozása
Ebben a lépésben létrehoz egy átjáró-alhálózatot és egy dinamikus útválasztású átjárót. A klasszikus üzemi modell Azure Portalján az átjáró-alhálózat és a dinamikus útválasztású átjáró ugyanazokkal a konfigurációs panelekkel létrehozható.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy átjárót.
2. A virtuális hálózat paneljén, az **Overview** (Áttekintés) panel VPN-kapcsolatok szakaszában kattintson a **Gateway** (Átjáró) elemre.

    ![Kattintson egy új átjáró létrehozásához](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. A **New VPN Connection** (Új VPN-kapcsolat) panelen válassza a **Point-to-site** (Pont–hely) beállítást.

    ![Pont–hely kapcsolat típusa](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. A **Client Address Space** (Ügyfélcímtér) területen adja hozzá az IP-címtartományt. Ez a tartomány, ahonnan a VPN-ügyfelek csatlakozáskor IP-címet kapnak. Törölje az automatikusan kitöltött tartományt, és adja meg a sajátját.

    ![Ügyfélcímtér](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Jelölje be a **Create gateway immediately** (Átjáró azonnali létrehozása) jelölőnégyzetet.

    ![Átjáró azonnali létrehozása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/creategwimm.png)
6. Kattintson az **Optional gateway configuration** (Átjáró opcionális konfigurálása) lehetőségre a **Gateway configuration** (Átjáró konfigurációja) panel megnyitásához.

    ![Kattintson az Optional gateway configuration (Átjáró opcionális konfigurálása) lehetőségre](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
7. Kattintson a **Subnet Configure required settings** (Alhálózat konfigurálásához szükséges beállítások) elemre az **átjáró-alhálózat** hozzáadásához. Ugyan létrehozhat kicsi, akár /29-es méretű átjáró-alhálózatot is, a /28-as vagy /27-es lehetőség választásával ajánlott nagyobb alhálózatot létrehozni, amely több címet tartalmaz. Ez elegendő címet biztosít az esetleges későbbi konfigurációkhoz.

   > [!IMPORTANT]
   > Amikor átjáró-alhálózatokkal dolgozik, kerülje a hálózati biztonsági csoportok (NSG) társítását az átjáró-alhálózathoz. A hálózati biztonsági csoportok és az alhálózat társítása esetén előfordulhat, hogy a VPN Gateway nem fog a várt módon működni. A hálózati biztonsági csoportokkal kapcsolatos további információért tekintse meg a [Mi az a hálózati biztonsági csoport?](../virtual-network/virtual-networks-nsg.md) című cikket.
   >
   >

    ![Átjáró-alhálózat hozzáadása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
8. Válassza ki az átjáró **méretét**. Ez a virtuális hálózati átjáró létrehozásához használt Gateway termékváltozat. A portálon az alapértelmezett termékváltozat a **Basic** (Alapszintű). További információk a Gateway termékváltozatokról: [Tudnivalók a VPN Gateway beállításairól](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

    ![az átjáró mérete](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
9. Válassza ki az átjáró **útválasztási típusát**. A pont–hely konfigurációhoz a **Dynamic** (Dinamikus) útválasztási típusra van szükség. Miután befejezte a panel konfigurálását, kattintson az **OK** gombra.

    ![Az útválasztási típus konfigurálása](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
10. A **New VPN Connection** (Új VPN-kapcsolat) panel alján kattintson az **OK** gombra a virtuális hálózati átjáró létrehozásához. Ennek végrehajtása 45 percig is eltarthat.

## <a name="generatecerts"></a>2. szakasz – Tanúsítványok létrehozása
A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A főtanúsítvány létrehozása után exportálja a nyilvános tanúsítványadatokat (ne a titkos kulcsot) egy Base-64 kódolású X.509 .cer fájlba. Ezután töltse fel a nyilvános tanúsítványadatokat a főtanúsítványból az Azure-ba.

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a főtanúsítványból hozzák létre, majd telepítik az egyes ügyfélszámítógépekre. Ha nincs telepítve érvényes ügyféltanúsítvány, és az ügyfél megpróbál csatlakozni a virtuális hálózathoz, akkor a hitelesítés meghiúsul.

### <a name="cer"></a>1. rész: A nyilvános kulcs (.cer) beszerzése a főtanúsítványhoz

####<a name="enterprise-certificate"></a>Vállalati tanúsítvány
 
Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt.

####<a name="self-signed-root-certificate"></a>Önaláírt főtanúsítvány

Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. A pont–hely típusú hitelesítéshez szükséges mezőket tartalmazó önaláírt tanúsítvány létrehozásához a PowerShellt használhatja. Az [önaláírt tanúsítványok PowerShell használatával, pont–hely kapcsolatokhoz történő létrehozását ismertető](vpn-gateway-certificates-point-to-site.md) rész végigvezeti az önaláírt főtanúsítványok létrehozásához szükséges lépéseken.

> [!NOTE]
> Korábban a makecert volt az ajánlott módszer az önaláírt főtanúsítványok és a pont–hely kapcsolatokhoz szükséges ügyféltanúsítványok létrehozására. Most már a PowerShell használatával hozhatja létre ezeket a tanúsítványokat. A PowerShell egyik előnye az SHA-2-tanúsítványok létrehozásának lehetősége. Lásd az [önaláírt tanúsítványok PowerShell használatával, pont–hely kapcsolatokhoz történő létrehozását ismertető](vpn-gateway-certificates-point-to-site.md) részt a szükséges értékekért.
>
>

#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Az önaláírt főtanúsítványhoz tartozó nyilvános kulcs exportálása

A pont–hely kapcsolatokhoz a nyilvános kulcsot (.cer) fel kell tölteni az Azure-ba. A következő lépések segítségével tudja feltölteni az önaláírt főtanúsítvány .cer fájlját.

1. A .cer fájl tanúsítványból történő beszerzéséhez futtassa a **certmgr.msc** parancsot. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**.
2. A varázslóban kattintson a **Tovább** gombra. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra. 
4. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Next** (Tovább) gombra.
5. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához. Megjelenik **Az exportálás sikeres volt** üzenet. A varázsló bezárásához kattintson az **OK** gombra.

### <a name="genclientcert"></a>2. rész: Ügyféltanúsítvány létrehozása

Létrehozhat egy egyedi tanúsítványt minden csatlakozó ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy szükség esetén visszavonhat egyetlen tanúsítványt. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és úgy találja, hogy egyetlen ügyféltől vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

####<a name="enterprise-certificate"></a>Vállalati tanúsítvány
- Ha vállalati tanúsítványmegoldást használ, az általános „name@yourdomain.com” formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátum helyett.
- Ellenőrizze, hogy a kiadott ügyféltanúsítvány azon a „felhasználói” tanúsítványsablonon alapul-e, amely használati listájának első helyén az „ügyfél-hitelesítés” áll, nem az intelligens kártyás bejelentkezés vagy egyebek. A tanúsítvány ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és tekintse meg a **Részletek > Kibővített kulcshasználat** részt.

####<a name="self-signed-root-certificate"></a>Önaláírt főtanúsítvány 
Ha önaláírt főtanúsítványt használ, tekintse meg a [tanúsítvány PowerShell használatával történő létrehozását](vpn-gateway-certificates-point-to-site.md#clientcert) ismertető szakaszt a pont–hely kapcsolatokkal kompatibilis ügyféltanúsítványok létrehozásának lépéseiért.

### <a name="exportclientcert"></a>3. rész: Az ügyféltanúsítvány exportálása
Ha egy önaláírt főtanúsítványból a [PowerShellre](vpn-gateway-certificates-point-to-site.md#clientcert) vonatkozó utasítások segítségével hoz létre ügyféltanúsítványt, akkor az automatikusan a létrehozásához használt számítógépre lesz telepítve. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, akkor exportálnia kell azt.

1. Az ügyféltanúsítvány exportálásához futtassa a **certmgr.msc** parancsot. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**.
2. A varázslóban kattintson a **Tovább** gombra, válassza az **Igen, a titkos kulcs exportálását választom** lehetőséget, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve. Ezután kattintson a **Next** (Tovább) gombra.
4. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **Next** (Tovább) gombra.
5. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Next** (Tovább) gombra.
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.

## <a name="upload"></a>3. szakasz – A főtanúsítvány .cer fájljának feltöltése
Miután létrehozta az átjárót, feltöltheti a megbízható főtanúsítványhoz tartozó .cer fájlt az Azure-ba. Legfeljebb 20 főtanúsítványhoz tölthet fel fájlokat. A főtanúsítvány titkos kulcsát ne töltse fel az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure használhatja azt azon ügyfelek hitelesítéséhez, amelyek a virtuális hálózathoz csatlakoznak.

1. A virtuális hálózat paneljének **VPN connections** (VPN-kapcsolatok) szakaszában kattintson a **clients** (ügyfelek) ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.

    ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panelen kattintson a **Manage certificates** (Tanúsítványok kezelése) lehetőségre a **Certificates** (Tanúsítványok) panel megnyitásához.<br>

    ![Tanúsítványok panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. A **Certificates** (Tanúsítványok) panelen kattintson az **Upload** (Feltöltés) lehetőségre az **Upload certificate** (Tanúsítvány feltöltése) panel megnyitásához.<br>

    ![Upload certificates (Tanúsítványok feltöltése) panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Kattintson a mappa ábrára a .cer fájl kereséséhez. Válassza ki a fájlt majd kattintson az **OK** gombra. Frissítse az oldalt, hogy a feltöltött tanúsítvány megjelenjen a **Certificates** (Tanúsítványok) panelen.

    ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. szakasz – A VPN-ügyfél konfigurációs csomagjának létrehozása
A virtuális hálózathoz való csatlakozáshoz emellett konfigurálnia kell a VPN-ügyfelet is. Az ügyfélszámítógép csatlakozásához szükséges egy ügyféltanúsítvány és a VPN-ügyfél megfelelő konfigurációs csomagja.

A VPN-ügyfélcsomag tartalmazza a Windows beépített VPN-ügyfélszoftverének konfigurálásához szükséges konfigurációs adatokat. A csomag nem telepít további szoftvert. A beállítások arra a virtuális hálózatra vonatkoznak, amelyhez csatlakozni szeretne. A támogatott ügyfél operációs rendszerek listáját a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című szakaszban tekintheti meg.

### <a name="to-generate-the-vpn-client-configuration-package"></a>A VPN-ügyfél konfigurációs csomagjának létrehozása
1. Az Azure Portalon a virtuális hálózat **Overview** (Áttekintés) paneljének **VPN connections** (VPN-kapcsolatok) részén kattintson az ügyfél ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel felső részén kattintson arra a letöltési csomagra, amely ahhoz az ügyféloldali operációs rendszerhez tartozik, amelyen telepítve lesz:

   * 64 bites ügyfelek esetén válassza a **VPN Client (64-bit)** (VPN-ügyfél (64 bit)) lehetőséget.
   * 32 bites ügyfelek esetén válassza a **VPN Client (32-bit)** (VPN-ügyfél (32 bit)) lehetőséget.

     ![A VPN-ügyfél konfigurációs csomagjának letöltése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Megjelenik egy üzenet, amely arról tájékoztatja, hogy az Azure létrehozza a VPN-ügyfél konfigurációs csomagját a virtuális hálózathoz. Néhány perc elteltével a csomag létrehozása befejeződik, és megjelenik egy üzenet a helyi számítógépen, amely arról tájékoztatja, hogy a csomag letöltődött. Mentse a konfigurációscsomag-fájlt. Telepítse minden olyan ügyfélszámítógépen, amely a pont–hely kapcsolattal csatlakozni fog a virtuális hálózathoz.

## <a name="clientconfiguration"></a>5. szakasz – Az ügyfélszámítógép konfigurálása
### <a name="part-1-install-an-exported-client-certificate"></a>1. rész: Exportált ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra.

1. Keresse meg, és másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Hagyja a **Tárolás helyét** **Aktuális felhasználó** értéken, majd kattintson a **Tovább** gombra.
2. A **Fájl** importálása lapon nem kell semmit módosítania. Kattintson a **Tovább** gombra.
3. A **Titkos kulcs védelme** lapon adja meg a tanúsítvány jelszavát, ha használ olyat, vagy ellenőrizze, hogy a tanúsítványt telepítő rendszerbiztonsági tag megfelelő-e, majd kattintson a **Tovább** gombra.
4. A **Tanúsítványtároló** lapon ne módosítsa az alapértelmezett helyet, majd kattintson a **Tovább** gombra.
5. Kattintson a **Finish** (Befejezés) gombra. A tanúsítványtelepítés **Biztonsági figyelmeztetés** párbeszédpanelén kattintson az **Igen** gombra. Nyugodtan rákattinthat az Igenre, mivel már létrehozta a tanúsítványt. A rendszer ezután sikeresen importálja a tanúsítványt.

### <a name="part-2-install-the-vpn-client-configuration-package"></a>2. rész: A VPN-ügyfél konfigurációs csomagjának telepítése
Használhatja a VPN-ügyfél ugyanazon konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megegyezik az ügyfél architektúrájával.

1. Másolja a konfigurációs fájlt a virtuális hálózathoz csatlakoztatni kívánt számítógépre. 
2. Kattintson duplán az .exe fájlra, hogy a csomagot az ügyfélszámítógépre telepítse. Mivel a konfigurációs csomagot Ön hozta létre, az nincs aláírva. Emiatt esetleg egy figyelmeztetés fog megjelenni. Ha egy Windows SmartScreen előugró ablak jelenik meg, kattintson a **További információ** (a bal oldalon), majd a **Futtatás mindenképpen** elemre a csomag telepítéséhez.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következőre hasonlít:

    ![VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vpn.png)

## <a name="connect"></a>6. szakasz – Csatlakozás az Azure-hoz
### <a name="connect-to-your-vnet"></a>Csatlakozás a virtuális hálózathoz
1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához.
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.

    ![VPN-ügyfélkapcsolat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Ekkor létre kell jönnie a kapcsolatnak.

    ![Létrehozott kapcsolat](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

> [!NOTE]
> Ha vállalati hitelesítésszolgáltatói megoldás használatával kiadott tanúsítványt használ, és problémák merülnek fel a hitelesítés során, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és lépjen a **Részletek > Kibővített kulcshasználat** részre. Ellenőrizze, hogy a listán az „ügyfél-hitelesítés” jelenik-e meg első helyen. Ha nem, ki kell adnia egy ügyféltanúsítványt, amely az ügyfél-hitelesítést a lista első helyén tartalmazó felhasználói sablonon alapul. 
>
>

### <a name="verify-the-vpn-connection"></a>A VPN-kapcsolat ellenőrzése
1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. A kapott IP-cím a virtuális hálózat létrehozásakor megadott pont–hely kapcsolati címtartományba tartozó valamelyik cím. Az eredmények a következőhöz hasonlóak:

Példa:

    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása

A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Ha eltávolít egy megbízható tanúsítványt, akkor az abból a főtanúsítványból generált ügyféltanúsítványok nem tudnak többé pont–hely kapcsolattal csatlakozni az Azure-hoz. Ha azt szeretné, hogy az ügyfelek csatlakozni tudjanak, telepíteniük kell egy olyan új ügyféltanúsítvány, amelyet az Azure által megbízhatónak tartott tanúsítványból generáltak.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd [a főtanúsítvány .cer fájljának feltöltését ismertető 3. szakaszt](#upload).

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása


1. A virtuális hálózat paneljének **VPN connections** (VPN-kapcsolatok) szakaszában kattintson a **clients** (ügyfelek) ábrára a **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panel megnyitásához.

    ![Ügyfelek](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. A **Point-to-site VPN connection** (Pont–hely típusú VPN-kapcsolat) panelen kattintson a **Manage certificates** (Tanúsítványok kezelése) lehetőségre a **Certificates** (Tanúsítványok) panel megnyitásához.<br>

    ![Tanúsítványok panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. A **Tanúsítványok** panelen kattintson az eltávolítani kívánt tanúsítvány melletti, három pontot ábrázoló gombra, majd a **Törlés** parancsra.

     ![Főtanúsítvány törlése](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>


## <a name="revokeclient"></a>Ügyféltanúsítvány visszavonása
Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos karakterláncot kapjon.
3. Lépjen a **„klasszikus virtuális hálózat neve” > Pont–hely kapcsolat > Tanúsítványok** panelre, majd kattintson a **Visszavont tanúsítványok listája** elemre a Visszavont tanúsítványok listája panel megnyitásához. 
4. A **Visszavont tanúsítványok listája** panelen kattintson a **+Tanúsítvány hozzáadása** parancsra a **Tanúsítvány felvétele a visszavont tanúsítványok listájára** panel megnyitásához.
5. A **Tanúsítvány felvétele a visszavont tanúsítványok listájára** panelen illessze be a tanúsítvány ujjlenyomatát összefüggő, szóközök nélküli szövegláncként. Kattintson az **OK** gombra a panel alján.
6. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.


## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/linux/azure-vm-network-overview.md).

