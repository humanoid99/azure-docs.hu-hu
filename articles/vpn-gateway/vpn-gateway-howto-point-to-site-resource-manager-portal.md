---
title: "Számítógép csatlakoztatása Azure-beli virtuális hálózathoz pont–hely kapcsolat használatával: Portal | Microsoft Docs"
description: "A Resource Managerrel és az Azure Portallal létrehozott pont–hely VPN Gateway-kapcsolattal biztonságosan csatlakozhat az Azure Virtual Networkhöz."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: c3fd647fdd21f6978e4d631ca9b10429e963cd05
ms.lasthandoff: 03/25/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-the-azure-portal"></a>Pont–hely kapcsolat konfigurálása virtuális hálózat számára az Azure Portalon
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

A pont–hely (P2S) konfiguráció lehetővé teszi biztonságos kapcsolat létesítését a virtuális hálózattal egy különálló ügyfélszámítógépről. A pont–hely kapcsolat egy SSTP (Secure Socket Tunneling Protocol) használatával működő VPN-kapcsolat. A pont–hely kapcsolatok akkor hasznosak, ha egy távoli helyről szeretne csatlakozni a virtuális hálózathoz, például otthonról vagy egy konferenciáról, vagy akkor, ha csak néhány ügyfelet szeretne csatlakoztatni egy virtuális hálózathoz. A pont–hely kapcsolatok nem igényelnek VPN-eszközt vagy nyilvános IP-címet a működéshez. A VPN-kapcsolatot az ügyfélszámítógépről hozhatja létre.

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózat pont–hely kapcsolattal az Azure Portalon. A Pont–hely kapcsolatokról további információt a cikk végén, a [Pont–hely kapcsolatok – gyakori kérdések](#faq) című részben talál.

### <a name="deployment-models-and-methods-for-p2s-connections"></a>Üzemi modellek és módszerek a pont–hely (P2S) kapcsolatokhoz
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Az alábbi táblázatban látható a két üzemi modell és a pont–hely konfigurációkhoz elérhető üzembe helyezési módszerek. Amint elérhetővé válik a konfigurációs lépéseket ismertető cikk, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>Alapvető munkafolyamat
![Pont–hely diagram](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-connection-diagram.png)

### <a name="example"></a>Példaértékek
* **Név: VNet1**
* **Címtér: 192.168.0.0/16**<br>Ebben a példában csak egy címteret használunk. Azonban a virtuális hálózatához több címteret is használhat.
* **Alhálózat neve: FrontEnd**
* **Alhálózati címtartomány: 192.168.1.0/24**
* **Előfizetés:**Ha több előfizetése is van, ellenőrizze, hogy a megfelelőt használja-e.
* **Erőforráscsoport: TestRG**
* **Hely: East US**
* **Átjáró-alhálózat: 192.168.200.0/24**
* **Virtuális hálózati átjáró neve: VNet1GW**
* **Átjáró típusa: VPN**
* **VPN típusa: Útvonalalapú**
* **Nyilvános IP-cím: VNet1GWpip**
* **Kapcsolat típusa: pont–hely**
* **Ügyfélcímkészlet: 172.16.201.0/24**<br>Azok a VPN-ügyfelek, amelyek ezzel a pont–hely kapcsolattal csatlakoznak a virtuális hálózathoz, az ügyfélcímkészletből kapnak IP-címet.

## <a name="createvnet"></a>1. rész – Virtuális hálózat létrehozása
Mielőtt elkezdi végrehajtani a lépéseket, győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial). Ha gyakorlatként hozza létre ezt a konfigurációt, használja a [példaértékeket](#example).

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="address"></a>2. rész – Címterek és alhálózatok megadása
Miután létrehozta a virtuális hálózatot, további címtereket és alhálózatokat adhat hozzá.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. rész – Átjáróalhálózat hozzáadása

Mielőtt csatlakoztatja virtuális hálózatát egy átjáróhoz, létre kell hoznia az átjáróalhálózatot ahhoz a virtuális hálózathoz, amelyhez csatlakozni szeretne. Az átjárószolgáltatások az átjáróalhálózatban megadott IP-címeket használják. Ha lehetséges, hozzon létre egy átjáróalhálózatot /28 vagy /27 CIDR-blokk használatával annak érdekében, hogy a jövőbeli további konfigurációs követelmények számára elegendő IP-címet biztosíthasson.

Az ebben a részben szereplő képernyőképek példa referenciaként szolgálnak. Ügyeljen arra, hogy azt az átjáróalhálózati címtartományt használja, amelyik a konfigurációhoz szükséges értékeknek megfelelő.

###<a name="to-create-a-gateway-subnet"></a>Átjáróalhálózat létrehozása

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="dns"></a>4. rész – DNS-kiszolgáló megadása (nem kötelező)

A pont–hely kapcsolatokhoz nincs szükség DNS-re. Ha azonban azt szeretné, hogy a virtuális hálózatokon üzembe helyezett erőforrásokon működjön a névfeloldás, adjon meg egy DNS-kiszolgálót. Ezzel a beállítással megadhatja azt a DNS-kiszolgálót, amelyet névfeloldásra kíván használni ennél a virtuális hálózatnál. A beállítás nem hoz létre új DNS-kiszolgálót.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>5. rész – Virtuális hálózati átjáró létrehozása
A pont–hely kapcsolatokhoz a következő beállításokra van szükség:

* Átjáró típusa: VPN
* VPN típusa: Útvonalalapú

### <a name="to-create-a-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="generatecert"></a>6. rész – Tanúsítványok előállítása

A tanúsítványokat az Azure használja a VPN-ügyfelek hitelesítésére a pont–hely VPN-kapcsolatokban. A főtanúsítvány létrehozása után exportálja a nyilvános tanúsítványadatokat (ne a titkos kulcsot) egy Base-64 kódolású X.509 .cer fájlba. Ezután töltse fel a nyilvános tanúsítványadatokat a főtanúsítványból az Azure-ba.

Minden, a virtuális hálózathoz pont–hely kapcsolattal csatlakozó ügyfélszámítógépnek rendelkeznie kell telepített ügyféltanúsítvánnyal. Az ügyféltanúsítványokat a főtanúsítványból hozzák létre, majd telepítik az egyes ügyfélszámítógépekre. Ha nincs telepítve érvényes ügyféltanúsítvány, és az ügyfél megpróbál csatlakozni a virtuális hálózathoz, akkor a hitelesítés meghiúsul.

### <a name="getcer"></a>1. lépés – A .cer fájl beszerzése a főtanúsítványhoz

####<a name="enterprise-certificate"></a>Vállalati tanúsítvány
 
Nagyvállalati megoldás esetén használhatja meglévő tanúsítványláncát. Szerezze be a használni kívánt főtanúsítványhoz tartozó .cer fájlt.

####<a name="self-signed-root-certificate"></a>Önaláírt főtanúsítvány

Ha nem vállalati tanúsítványmegoldást használ, létre kell hoznia egy önaláírt főtanúsítványt. A pont–hely típusú hitelesítéshez szükséges mezőket tartalmazó önaláírt főtanúsítvány létrehozásához a PowerShellt használhatja. Az [önaláírt főtanúsítványok PowerShell használatával, pont–hely kapcsolatokhoz történő létrehozását ismertető](vpn-gateway-certificates-point-to-site.md) rész végigvezeti az önaláírt főtanúsítványok létrehozásához szükséges lépéseken.

> [!NOTE]
> Korábban a makecert volt az ajánlott módszer az önaláírt főtanúsítványok és a pont–hely kapcsolatokhoz szükséges ügyféltanúsítványok létrehozására. Most már a PowerShell használatával hozhatja létre ezeket a tanúsítványokat. A PowerShell egyik előnye az SHA-2-tanúsítványok létrehozásának lehetősége. Lásd az [önaláírt főtanúsítványok PowerShell használatával, pont–hely kapcsolatokhoz történő létrehozását ismertető](vpn-gateway-certificates-point-to-site.md) részt a szükséges értékekért.
>
>


#### <a name="to-export-the-public-key-for-a-self-signed-root-certificate"></a>Az önaláírt főtanúsítványhoz tartozó nyilvános kulcs exportálása

A pont–hely kapcsolatokhoz a nyilvános kulcsot (.cer) fel kell tölteni az Azure-ba. A következő lépések segítségével tudja feltölteni az önaláírt főtanúsítvány .cer fájlját.

1. A .cer fájl tanúsítványból történő beszerzéséhez futtassa a **certmgr.msc** parancsot. Keresse meg az önaláírt főtanúsítványt. Ezt általában a „Tanúsítványok – aktuális felhasználó\Személyes\Tanúsítványok” útvonalon érheti el. Ha megtalálta, kattintson rá a jobb egérgombbal. Kattintson a **Minden feladat**, majd az **Exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**.
2. A varázslóban kattintson a **Tovább** gombra. Válassza a **Nem, nem akarom exportálni a titkos kulcsomat** lehetőséget, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon válassza a **Base-64 kódolású X.509 (.CER)** lehetőséget, majd kattintson a **Tovább** gombra. 
4. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Next** (Tovább) gombra.
5. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához. Megjelenik **Az exportálás sikeres volt** üzenet. A varázsló bezárásához kattintson az **OK** gombra.

### <a name="generateclientcert"></a>2. lépés – Ügyféltanúsítvány létrehozása
Létrehozhat egy egyedi tanúsítványt minden, a virtuális hálózathoz csatlakozó ügyfél számára, vagy használhatja ugyanazt a tanúsítványt több ügyfél esetén. Az egyedi ügyféltanúsítványok előállításának előnye az, hogy szükség esetén visszavonhat egyetlen tanúsítványt. Ha azonban mindenki ugyanazt az ügyféltanúsítványt használja, és úgy találja, hogy egyetlen ügyféltől vissza kell vonnia a tanúsítványt, az összes olyan ügyfél számára elő kell állítania és telepítenie kell új tanúsítványokat, amelyek az adott tanúsítványt használják a hitelesítéshez.

####<a name="enterprise-certificate"></a>Vállalati tanúsítvány
- Ha vállalati tanúsítványmegoldást használ, az általános „name@yourdomain.com” formátumban hozza létre az ügyféltanúsítványokat a „tartománynév\felhasználónév” formátum helyett.
- Ellenőrizze, hogy a kiadott ügyféltanúsítvány azon a „felhasználói” tanúsítványsablonon alapul-e, amely használati listájának első helyén az „ügyfél-hitelesítés” áll, nem az intelligens kártyás bejelentkezés vagy egyebek. A tanúsítvány ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és tekintse meg a **Részletek > Kibővített kulcshasználat** részt.

####<a name="self-signed-root-certificate"></a>Önaláírt főtanúsítvány 
Ha önaláírt főtanúsítványt használ, tekintse meg a [tanúsítvány PowerShell használatával történő létrehozását](vpn-gateway-certificates-point-to-site.md#clientcert) ismertető szakaszt a pont–hely kapcsolatokkal kompatibilis ügyféltanúsítványok létrehozásának lépéseiért.


### <a name="exportclientcert"></a>3. lépés – Az ügyféltanúsítvány exportálása
Ha egy önaláírt főtanúsítványból a [PowerShellre](vpn-gateway-certificates-point-to-site.md#clientcert) vonatkozó utasítások segítségével hoz létre ügyféltanúsítványt, akkor az automatikusan a létrehozásához használt számítógépre lesz telepítve. Ha egy ügyféltanúsítványt egy másik ügyfélszámítógépre szeretne telepíteni, akkor exportálnia kell azt.

1. Az ügyféltanúsítvány exportálásához futtassa a **certmgr.msc** parancsot. Kattintson a jobb gombbal az exportálni kívánt ügyféltanúsítványra, majd a **minden feladat** és az **exportálás** elemre. Megnyílik a **Tanúsítványexportáló varázsló**.
2. A varázslóban kattintson a **Tovább** gombra, válassza az **Igen, a titkos kulcs exportálását választom** lehetőséget, majd kattintson a **Tovább** gombra.
3. Az **Exportfájlformátum** lapon hagyja bejelölve az alapértelmezett elemeket. Győződjön meg róla, hogy a **Minden tanúsítvány belefoglalása a tanúsítványláncba** jelölőnégyzet be van jelölve. Ezután kattintson a **Next** (Tovább) gombra. 
4. A **Biztonság** lapon be kell állítania a titkos kulcs védelmét. Ha jelszó használata mellett dönt, jegyezze fel vagy jegyezze meg a tanúsítványhoz beállított jelszót. Ezután kattintson a **Next** (Tovább) gombra.
5. Az **Exportálandó fájl** lapon a **Tallózás** gombra kattintva keresse meg azt a helyet, ahová exportálni szeretné a tanúsítványt. A **Fájlnév** mezőben nevezze el a tanúsítványfájlt. Ezután kattintson a **Next** (Tovább) gombra.
6. Kattintson a **Befejezés** gombra a tanúsítvány exportálásához.   

## <a name="addresspool"></a>7. rész – Az ügyfélcímkészlet hozzáadása
1. Miután létrehozta a virtuális hálózati átjárót, navigáljon a virtuális hálózati átjáró paneljének **Beállítások** részéhez. A **Beállítások** részben kattintson a **Pont–hely konfiguráció** elemre a **Konfiguráció** panel megnyitásához.
   
    ![Pont–hely panel](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configuration.png)
2. A **Címkészlet** azon IP-címek készletét jelenti, amelyek közül a csatlakozó ügyfelek IP-címet kapnak. Adja hozzá a címkészletet, majd kattintson a **Mentés** gombra.
   
    ![Ügyfélcímkészlet](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/ipaddresspool.png)

## <a name="uploadfile"></a>8. rész – A főtanúsítvány .cer fájljának feltöltése
Miután létrehozta az átjárót, feltöltheti a megbízható főtanúsítványhoz tartozó .cer fájlt az Azure-ba. Legfeljebb 20 főtanúsítványhoz tölthet fel fájlokat. A főtanúsítvány titkos kulcsát ne töltse fel az Azure-ba. Miután feltöltötte a .cer fájlt, az Azure használhatja azt azon ügyfelek hitelesítéséhez, amelyek a virtuális hálózathoz csatlakoznak.

1. A tanúsítványokat a rendszer hozzáadja a **Főtanúsítvány** szakasz **Pont–hely konfiguráció** paneléhez.  
2. Győződjön meg arról, hogy Base-64 kódolású X.509 (.cer) fájlként exportálta a főtanúsítványt. Ebben a formátumban kell exportálnia, hogy szövegszerkesztővel meg tudja nyitni a tanúsítványt.
3. Nyissa megy a tanúsítványt egy szövegszerkesztővel, például a Jegyzettömbbel. Csak a következő szakaszt másolja egy folyamatos sorként:
   
    ![Tanúsítványadatok](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/copycert.png)

    > [!NOTE]
    > A tanúsítványadatok másolásakor a szöveget egy folyamatos sorként másolja kocsivissza vagy új sor nélkül. A kocsivisszák és az új sorok megjelenítéséhez lehet, hogy módosítania kell a nézetet a szövegszerkesztőben a „Szimbólum megjelenítése/Minden karakter megjelenítése” beállításra.                                                                                                                                                                            
    >
    >

4. Illessze be a tanúsítványadatokat a **Nyilvános tanúsítványadatok** mezőbe. **Nevezze el** a tanúsítványt, majd a kattintson a **Mentés** gombra. Legfeljebb 20 megbízható főtanúsítványt adhat hozzá.
   
    ![Tanúsítvány feltöltése](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/rootcertupload.png)

## <a name="clientconfig"></a>9. rész – A VPN-ügyfél konfigurációs csomagjának letöltése és telepítése
Az Azure-hoz pont–hely kapcsolattal csatlakozó ügyfeleken az ügyféltanúsítványon kívül telepíteni kell egy VPN-ügyfélkonfigurációs csomagot is. A windowsos ügyfelekhez érhetők el VPN-ügyfélkonfigurációs csomagok. 

A VPN-ügyfélcsomag tartalmazza a Windows beépített VPN-ügyfélszoftverének konfigurálásához szükséges információkat. A konfiguráció arra a VPN-re jellemző, amelyhez csatlakozni szeretne. A csomag nem telepít további szoftvert.

Használhatja a VPN-ügyfél ugyanazon konfigurációs csomagját minden ügyfélszámítógépen, feltéve, hogy a verzió megegyezik az ügyfél architektúrájával.

### <a name="step-1---download-the-client-configuration-package"></a>1. rész: Az ügyfél-konfigurációs csomag letöltése

1. A **Pont–hely konfiguráció** panelen kattintson a **VPN-ügyfél letöltése** elemre a **VPN-ügyfél letöltése** panel megnyitásához. A csomag létrehozása egy-két percet vesz igénybe.
   
    ![VPN-ügyfél letöltése, 1](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/downloadvpnclient1.png)
2. Válassza ki az ügyfélnek megfelelő csomagot, majd kattintson a **Letöltés** gombra. Mentse a konfigurációscsomag-fájlt. Telepítse minden olyan ügyfélszámítógépen, amely csatlakozni fog a virtuális hálózathoz.

    ![VPN-ügyfél letöltése, 2](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpnclient.png)

### <a name="step-2---install-the-client-configuration-package"></a>2. rész: Az ügyfél-konfigurációs csomag telepítése

1. Másolja a konfigurációs fájlt a virtuális hálózathoz csatlakoztatni kívánt számítógépre. 
2. Kattintson duplán az .exe fájlra, hogy a csomagot az ügyfélszámítógépre telepítse. Mivel a konfigurációs csomagot Ön hozta létre, nincs aláírva. Emiatt esetleg egy figyelmeztetés fog megjelenni. Ha egy Windows SmartScreen előugró ablak jelenik meg, kattintson a **További információ** (a bal oldalon), majd a **Futtatás mindenképpen** elemre a csomag telepítéséhez.
3. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következőre hasonlít:
3. Telepítse a csomagot az ügyfélszámítógépen. Ha egy Windows SmartScreen előugró ablak jelenik meg, kattintson a **További információ** (a bal oldalon), majd a **Futtatás mindenképpen** elemre a csomag telepítéséhez.
4. Nyissa meg az ügyfélszámítógépen a **Hálózati beállítások** eszközt, és kattintson a **VPN** elemre. A listán láthatja a kapcsolatot. Annak a virtuális hálózatnak a nevét mutatja, amellyel kapcsolatot létesít, és következő példára hasonlít: 
   
    ![VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/vpn.png)


## <a name="installclientcert"></a>10. rész – Exportált ügyféltanúsítvány telepítése

Ha a tanúsítvány létrehozásához használttól eltérő ügyfélszámítógépről szeretne pont–hely kapcsolatot létesíteni, akkor telepítenie kell egy ügyféltanúsítványt. Az ügyféltanúsítvány telepítésekor szükség lesz az ügyféltanúsítvány exportálásakor létrehozott jelszóra. 

1. Keresse meg, és másolja a *.pfx* fájlt az ügyfélszámítógépre. Az ügyfélszámítógépen kattintson duplán a *.pfx* fájlra annak telepítéséhez. Hagyja a **Tárolás helyét** **Aktuális felhasználó** értéken, majd kattintson a **Tovább** gombra.
2. A **Fájl** importálása lapon nem kell semmit módosítania. Kattintson a **Tovább** gombra.
3. A **Titkos kulcs védelme** lapon adja meg a tanúsítvány jelszavát, ha használ olyat, vagy ellenőrizze, hogy a tanúsítványt telepítő rendszerbiztonsági tag megfelelő-e, majd kattintson a **Tovább** gombra.
4. A **Tanúsítványtároló** lapon ne módosítsa az alapértelmezett helyet, majd kattintson a **Tovább** gombra.
5. Kattintson a **Finish** (Befejezés) gombra. A tanúsítványtelepítés **Biztonsági figyelmeztetés** párbeszédpanelén kattintson az **Igen** gombra. Nyugodtan rákattinthat az Igenre, mivel már létrehozta a tanúsítványt. A rendszer ezután sikeresen importálja a tanúsítványt.

## <a name="connect"></a>11. rész – Csatlakozás az Azure-hoz
1. Csatlakozzon a virtuális hálózathoz. Ehhez navigáljon az ügyfélszámítógépen a VPN-kapcsolatokhoz, és keresse meg a létrehozott VPN-kapcsolatot. Ugyanaz a neve, mint a virtuális hálózatnak. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Ilyen esetében kattintson a **Folytatás** gombra emelt szintű jogosultságok használatához. 
2. A csatlakozás megkezdéséhez a **Kapcsolat** állapotlapon kattintson a **Csatlakozás** gombra. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, kattintson a legördülő nyílra, válassza ki a helyes tanúsítványt, majd kattintson az **OK** gombra.
   
    ![VPN-ügyfél csatlakoztatása az Azure-hoz](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)

    
3. Ekkor létre kell jönnie a kapcsolatnak.
   
    ![Az Azure-hoz csatlakoztatott VPN-ügyfél](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)
                                                                                                                                                                           

> [!NOTE]
> Ha vállalati hitelesítésszolgáltatói megoldás használatával kiadott tanúsítványt használ, és problémák merülnek fel a hitelesítés során, ellenőrizze a hitelesítési sorrendet az ügyféltanúsítványon. A hitelesítési lista sorrendjének ellenőrzéséhez kattintson duplán az ügyféltanúsítványra, és lépjen a **Részletek > Kibővített kulcshasználat** részre. Ellenőrizze, hogy a listán az „ügyfél-hitelesítés” jelenik-e meg első helyen. Ha nem, ki kell adnia egy ügyféltanúsítványt, amely az ügyfél-hitelesítést a lista első helyén tartalmazó felhasználói sablonon alapul. 
>
>

## <a name="verify"></a>12. lépés – A kapcsolat ellenőrzése
1. Annak ellenőrzéséhez, hogy a VPN-kapcsolat aktív-e, nyisson meg egy rendszergazda jogú parancssort, és futtassa az *ipconfig/all* parancsot.
2. Tekintse meg az eredményeket. Figyelje meg, hogy a kapott IP-cím azok közül a címek közül való, amelyeket a pont–hely VPN-ügyfél konfigurációjának címkészletében megadott. Az eredmények a következőhöz hasonlóak:
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="add"></a>Megbízható főtanúsítványok hozzáadása vagy eltávolítása
A megbízható főtanúsítványokat felveheti vagy el is távolíthatja az Azure-ban. Ha eltávolít egy megbízható tanúsítványt, akkor az abból a főtanúsítványból generált ügyféltanúsítványok nem tudnak többé pont–hely kapcsolattal csatlakozni az Azure-hoz. Ha azt szeretné, hogy az ügyfelek csatlakozni tudjanak, telepíteniük kell egy olyan új ügyféltanúsítvány, amelyet az Azure által megbízhatónak tartott tanúsítványból generáltak.

### <a name="to-add-a-trusted-root-certificate"></a>Megbízható főtanúsítvány hozzáadása

Az Azure-ra legfeljebb 20 megbízható főtanúsítványt tölthet fel .cer fájl formájában. Útmutatásért lásd a jelen cikk [Megbízható főtanúsítvány feltöltése](#uploadfile) című szakaszát.

### <a name="to-remove-a-trusted-root-certificate"></a>Megbízható főtanúsítvány eltávolítása

1. A megbízható főtanúsítvány eltávolításához lépjen a virtuális hálózati átjáróhoz tartozó **Pont–hely konfiguráció** panelre.
2. Keresse meg az eltávolítani kívánt tanúsítványt a panel **Főtanúsítvány** szakaszában.
3. Kattintson a tanúsítvány melletti, három pontot ábrázoló gombra, majd az „Eltávolítás” parancsra.

## <a name="revokeclient"></a>Ügyféltanúsítvány visszavonása
Az ügyféltanúsítványokat vissza lehet vonni. A visszavont tanúsítványok listájával az egyes ügyféltanúsítványok alapján, szelektíven tagadhatja meg a pont–hely kapcsolódás lehetőségét. Ez a folyamat eltér a megbízható főtanúsítvány eltávolításától. Ha töröl egy .cer formátumú megbízható főtanúsítványt az Azure-ból, azzal megvonja a hozzáférést minden olyan ügyféltanúsítványtól, amelyet a visszavont főtanúsítvánnyal hoztak létre/írtak alá. A főtanúsítvány helyett az ügyféltanúsítvány visszavonásával a főtanúsítványból létrehozott többi tanúsítvány továbbra is használható a pont–hely kapcsolat hitelesítésére.

A szokásos gyakorlat az, hogy a főtanúsítvánnyal kezelik a hozzáférést a munkacsoport vagy a szervezet szintjén, az egyes felhasználókra vonatkozó részletesebb szabályozást pedig visszavont ügyféltanúsítványokkal oldják meg.

### <a name="to-revoke-a-client-certificate"></a>Ügyféltanúsítvány visszavonása

Az ügyféltanúsítványok visszavonásához vegye fel az ujjlenyomatot a visszavont tanúsítványok listájára.

1. Kérje le az ügyféltanúsítvány ujjlenyomatát. További információkat [a tanúsítványok ujjlenyomatának lekérését ismertető útmutatóban](https://msdn.microsoft.com/library/ms734695.aspx) találhat.
2. Másolja át az adatokat egy szövegszerkesztőbe, és távolítsa el az összes szóközt, hogy egy folyamatos karakterláncot kapjon.
3. Lépjen a virtuális hálózati átjáró **Pont–hely konfiguráció** paneljére. Ez az a panel, amelyet a [megbízható főtanúsítvány feltöltéséhez](#uploadfile) használt.
4. A **Visszavont tanúsítványok** szakaszban adjon egy rövid nevet a tanúsítványnak (ennek nem kell megegyeznie a tanúsítvány köznapi nevével).
5. Másolja ki és illessze be az ujjlenyomat karakterláncát az **Ujjlenyomat** mezőbe.
6. A rendszer ellenőrzi az ujjlenyomatot, és automatikusan hozzáadja a visszavonási listához. A képernyőn megjelenik egy üzenet, mely szerint a lista frissítése folyamatban van. 
7. A frissítés befejezését követően a tanúsítvány már nem használható csatlakozáshoz. Azok az ügyfelek, akik ezzel a tanúsítvánnyal próbálnak csatlakozni, egy üzenetet kapnak majd arról, hogy a tanúsítvány már nem érvényes.

## <a name="faq"></a>Pont–hely kapcsolatok – gyakori kérdések

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>Következő lépések
Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). A hálózatok és virtuális gépek ismertetését lásd az [Azure- és Linux-alapú virtuálisgép-hálózatok áttekintésében](../virtual-machines/virtual-machines-linux-azure-vm-network-overview.md).



