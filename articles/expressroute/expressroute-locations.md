---
title: "Kapcsolatszolgáltatók és helyek: Azure ExpressRoute | Microsoft Docs"
description: "A cikk részletes áttekintést nyújt a szolgáltatási helyekről és az Azure-régiókhoz való csatlakozásról. Kapcsolatszolgáltatók szerint rendezve."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 70955113bbd2e008be855e591d04651d09438e81
ms.lasthandoff: 03/24/2017


---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute-partnerek és társviszony-létesítési helyszínek.

> [!div class="op_single_selector"]
> * [Helyek szolgáltató alapján](expressroute-locations.md)
> * [Szolgáltatók hely alapján](expressroute-locations-providers.md)


A cikkben található táblázatok információkat nyújtanak az ExpressRoute-kapcsolatszolgáltatókkal, az ExpressRoute földrajzi lefedettségével, az ExpressRoute-on támogatott Microsoft-felhőszolgáltatásokkal és az ExpressRoute-rendszerintegrátorokkal (SI) kapcsolatban.

## <a name="partners"></a>ExpressRoute-kapcsolatszolgáltatók
Az ExpressRoute az összes Azure-régióban és -helyen támogatott. A következő térkép az Azure-régiók és ExpressRoute-helyek listáját tartalmazza. Az ExpressRoute-helyek azokra a helyekre utalnak, ahol a Microsoft több szolgáltatóval is társul.

![Helyek térképe][0]

Ha legalább egy ExpressRoute-helyhez csatlakozott egy geopolitikai régióban, az adott geopolitikai régióban lévő összes régióban hozzáférhet az Azure-szolgáltatásokhoz.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek.
A következő tábla az egyes geopolitikai régiókban lévő Azure-régiók és ExpressRoute-helyek térképét mutatja.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- |
| **Észak-Amerika** |USA keleti régiója, USA nyugati régiója, USA 2. keleti régiója, USA 2. nyugati régiója, USA középső régiója, USA déli középső régiója, USA északi középső régiója, USA középnyugati régiója, Közép-Kanada, Kelet-Kanada |Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Szilícium-völgy, Washington, D.C., Montréal, Québec város, Toronto |
| **Dél-Amerika** |Dél-Brazília |Sao Paulo |
| **Európa** |Észak-Európa, Nyugat-Európa, az Egyesült Királyság nyugati része, az Egyesült Királyság déli része |Amszterdam, Dublin, London, Newport (Wales), Párizs |
| **Ázsia** |Kelet-Ázsia, Délkelet-Ázsia |Hongkong, Szingapúr |
| **Japán** |Nyugat-Japán, Kelet-Japán |Oszaka, Tokió |
| **Ausztrália** |Délkelet-Ausztrália, Kelet-Ausztrália |Melbourne, Sydney |
| **India** |Nyugat-India, Közép-India, Dél-India |Csennai, Mumbai |
| **Dél-Korea** |Korea középső régiója, Korea déli régiója |Busan, Szöul |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>A régiók és az országos felhők geopolitikai határai
Az alábbi tábla a régiók és az országos felhők geopolitikai határainak információit tartalmazza.

| **Geopolitikai régió** | **Azure-régiók** | **ExpressRoute-helyek** |
| --- | --- | --- | --- |
| **Az Egyesült Államok kormányának felhője** |USA-beli államigazgatás – Iowa, USA-beli államigazgatás – Virginia, USA Védelmi Minisztériuma – Középső régió, USA Védelmi Minisztériuma – Keleti régió  |Chicago, Dallas, New York, Szilícium-völgy, Washington, D.C. |
| **Kína** |Észak-Kína, Kelet-Kína |Peking, Sanghaj |
| **Németország** |Közép-Németország, Kelet-Németország |Berlin, Frankfurt |

A geopolitikai régiókon átívelő kapcsolódás nem támogatott a standard ExpressRoute-termékváltozatokban. A globális kapcsolatok támogatásához engedélyeznie kell az ExpressRoute prémium bővítményt. Az országos felhőkörnyezetekhez való kapcsolódás nem támogatott. Igény esetén tájékozódjon kapcsolatszolgáltatójánál a lehetőségekről.

## <a name="locations"></a>Kapcsolatszolgáltatói helyek

Az alábbi táblázat a szolgáltatók szerint jeleníti meg a helyeket. Ha az elérhető szolgáltatókat hely szerint kívánja megtekinteni, tekintse meg a következőt: [Szolgáltatók hely szerint](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Termelési Azure
| **Szolgáltató** | **Microsoft Azure** | **Office 365 és CRM Online** | **Helyek** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Támogatott |Támogatott |Melbourne, Sydney |
| **Airtel** | Hamarosan | Hamarosan | Csennai, Mumbai |
| **[Aryaka Networks](http://www.aryaka.com/)** |Támogatott |Támogatott |Amszterdam, Dallas, Szilícium-völgy, Szingapúr, Tokió, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, London, Szilícium-völgy, Szingapúr, Sydney, Washington, D.C. |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Támogatott |Támogatott |Montréal, Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Támogatott |Támogatott |Amszterdam, Hongkong, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Hamarosan elérhető |Hamarosan elérhető |Szilícium-völgy |
| **China Telecom Global** |Támogatott |Nem támogatott |Hongkong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Támogatott |Támogatott |Dallas, Montréal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Támogatott |Amszterdam, Dublin, London, Tokió |
| **Comcast** |Támogatott |Támogatott |Chicago, Szilícium-völgy, Washington, D.C. |
| **Console**| Támogatott | Támogatott |Szilícium-völgy, Toronto |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Támogatott |Támogatott |Los Angeles, New York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Amszterdam, Atlanta, Chicago, Dallas, Hongkong, London, Los Angeles, Melbourne, New York, Oszaka, Párizs+, Sao Paulo, Seattle, Szilícium-völgy, Szingapúr, Sydney, Tokió, Toronto, Washington, D.C. |
| **euNetworks** |Támogatott |Támogatott |Amszterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Támogatott| Támogatott | Csennai |
| **GÉANT** |Támogatott |Támogatott |Amszterdam |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[InterCloud](https://www.intercloud.com/)** |Támogatott |Támogatott |Amszterdam, London, Szingapúr, Washington, D.C. |
| **Internet Solutions - Cloud Connect** |Támogatott |Támogatott |Amszterdam, London |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Támogatott |Támogatott |Amszterdam, London, Párizs |
| **Jisc** |Támogatott |Támogatott |London |
| **KINX** |Támogatott |Támogatott |Szöul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Támogatott | Támogatott | Amszterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Las Vegas+, London, Seattle, Szilícium-völgy, Szingapúr, Washington, D.C. |
| **LG CNS** |Hamarosan |Hamarosan |Busan+ |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott |Támogatott |Dallas, Hongkong, Las Vegas, Los Angeles, Melbourne, New York, Québec város, Seattle, Szingapúr, Sydney, Toronto, Washington D.C. |
| **MTN** |Támogatott |Támogatott |London |
| **[Következő generációs adatok](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Támogatott |Támogatott |Newport (Wales) |
| **NEXTDC** |Támogatott |Támogatott |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Támogatott |Támogatott |London, Los Angeles, Oszaka, Szingapúr, Tokió, Washington DC |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Támogatott |Támogatott |Amszterdam, Hongkong, London, Szilícium-völgy, Szingapúr, Sydney, Washington, D.C. |
| **PCCW Global Limited** |Támogatott |Támogatott |Hongkong |
| **Sejong Telecom** |Támogatott |Támogatott |Szöul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Támogatott |Támogatott |Csennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Támogatott |Támogatott |Szingapúr |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Támogatott |Támogatott |Oszaka, Tokió |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Támogatott |Támogatott |Amszterdam, Csennai, Hongkong, London, Mumbai, Szilícium-völgy, Szingapúr, Washington, D.C. |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Támogatott |Támogatott |Amszterdam, Dublin, London |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Támogatott |Támogatott |Amsterdam+, Sao Paulo |
| **[Telehouse – KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Támogatott |Támogatott |London |
| **Telenor** |Támogatott |Támogatott |Amszterdam, London |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Támogatott |Támogatott |Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Támogatott |Támogatott |Amszterdam, Chicago, Dallas, Hongkong, London, Szilícium-völgy, Szingapúr, Sydney, Tokió, Washington, D.C. |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Támogatott |Nem támogatott |London |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Támogatott |Támogatott |Chicago, Dallas+, London+, Los Angeles, New York, Szilícium-völgy, Toronto, Washington DC |

 **+** = hamarosan elérhető

### <a name="national-cloud-environment"></a>Országos felhőkörnyezet

### <a name="us-government-cloud"></a>Az Egyesült Államok kormányának felhője
| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Támogatott |Támogatott |Chicago, Washington, D.C. |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Seattle+, Szilícium-völgy, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Támogatott |Támogatott |Chicago, New York+, Washington, D.C. |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott | Támogatott | Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Támogatott |Támogatott |Chicago, Dallas, New York, Washington, D.C. |

### <a name="china"></a>Kína
| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **China Telecom** |Támogatott |Nem támogatott |Peking, Sanghaj |

További tudnivalókért lásd: [Az ExpressRoute Kínában](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Németország
| **Szolgáltató** | **Microsoft Azure** | **Office 365** | **Helyek** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Támogatott |Nem támogatott |Berlin+, Frankfurt |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Támogatott |Nem támogatott |Frankfurt |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Támogatott |Nem támogatott |Berlin |
| **Interxion** |Támogatott |Nem támogatott |Frankfurt |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Támogatott  | Nem támogatott | Berlin |

## <a name="c1partners"></a>Kapcsolódás a listában nem szereplő szolgáltatókon keresztül
Ha a kapcsolatszolgáltató nincs felsorolva az előző szakaszokban, akkor is létesíthet kapcsolatot.

* Ellenőrizze a kapcsolatszolgáltatójánál, hogy kapcsolódik-e a fenti táblában felsorolt adatcsere-szolgáltatások bármelyikéhez. A következő hivatkozásokon további információkat találhat az adatcsere-szolgáltatók által kínált szolgáltatásokkal kapcsolatban. Több kapcsolatszolgáltató már eleve kapcsolódik Ethernet-adatcserélőkhöz.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Kérje meg a kapcsolatszolgáltatót, hogy terjessze ki a hálózatot a választott társviszony-létesítési helyszínre.
  * Győződjön meg róla, hogy a kapcsolatszolgáltató magas rendelkezésre állással terjesztette ki a kapcsolatot, tehát nincsenek kritikus hibapontok a rendszeren belül.
* Rendeljen meg egy ExpressRoute-kapcsolatcsoportot, ahol az adatcserélő a Microsofthoz kapcsolódó kapcsolatszolgáltató.
  * Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) című témakör lépéseit a kapcsolat beállításához.

| **Kapcsolatszolgáltató** | **Exchange** | **Helyek** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Szingapúr |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokió |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | London |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montréal, Toronto |
| **[C3ntro](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Szingapúr |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amszterdam |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | London |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amszterdam |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | London, Slough |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |New York, Washington, D.C. |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion London |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Frankfurt |  
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | London
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Frankfurt |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |Chicago, Szilícium-völgy, Washington, D.C. |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| 3-as szint | Madrid |


## <a name="expressroute-system-integrators"></a>ExpressRoute-rendszerintegrátorok
A privát kapcsolatok igény szerinti beállítása nehéz feladat lehet, a hálózat méretétől függően. A következő táblában felsorolt rendszerintegrátorok bármelyike segítségére lehet az ExpressRoute üzembe helyezésében.

| **Rendszerintegrátor** | **Kontinens** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Európa |
| **[Avanade Inc.](http://www.avanade.com/)** | Ázsia, Európa, Észak-Amerika, Dél-Amerika |
| **Bright Skies GmbH** | Európa
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | Európa |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Észak-Amerika |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Ausztrália |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Európa (Németország) |
| **[Nelite](http://nelite.com/)** | Európa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Ázsia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Európa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Észak-Amerika |
| **[Project Leadership](http://www.projectleadership.net/azure)** | Észak-Amerika |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Európa |


## <a name="next-steps"></a>Következő lépések
* További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).
* Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Helyek térképe"

