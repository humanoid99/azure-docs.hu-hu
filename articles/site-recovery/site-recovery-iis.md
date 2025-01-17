---
title: "Replikálás egy többrétegű IIS-alapú webalkalmazását az Azure Site Recovery használatával |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan replikáljon az IIS webkiszolgáló farm virtuális gépek Azure Site Recovery segítségével."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: nisoneji
ms.openlocfilehash: 00d5c1fa8c0c16daef5d928147e169553672e1f6
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Azure Site Recovery segítségével többrétegű IIS-alapú webes alkalmazás replikálása

## <a name="overview"></a>Áttekintés


Alkalmazás Ez a motor a szervezet az üzleti hatékonyságot. Különböző webalkalmazások más célt szolgálhat egy szervezet. Lehet, például a feldolgozását, pénzügyi alkalmazásokat és ügyfelek által használt webhelyek ezek némelyike a lehető legnagyobb mértékben kritikus végzi egy szervezet számára. Fontos a szervezet által, hogy azok és futó minden alkalommal a termelékenység és fontosabb az adatvesztés elkerülése érdekében megelőzése érdekében bármely a szervezet a márka lemezképhez.

Kritikus webalkalmazások általában úgy vannak beállítva, mint a webes, adatbázis és a különböző rétegeket alkalmazás Többrétegű alkalmazások. Különböző rétegek elosztva alatt, leszámítva az alkalmazások is használhatja több kiszolgáló egyes rétegekben a forgalom. A leképezések különböző fizetősökbe, és a webkiszolgálón, továbbá statikus IP-címek olyan alapul. Feladatátvétel esetén a leképezések némelyike kell frissíteni, különösen akkor, ha több webhely a webkiszolgálón beállított. Ha a webes alkalmazásokat az SSL használatához tanúsítványok kötései frissítenie kell.

Hagyományos nem replikációs alapján helyreállítási módszerek tartalmaz, amely biztonsági mentése különböző konfigurációs fájlokat, beállításjegyzék-beállítások, kötések, egyéni összetevők (COM vagy .NET), tartalmat, és is tanúsítványokat és a kézi ismertetett lépések segítségével a fájlok helyreállítása. Ezek a technológiák egyértelműen nehézkes, amelyek nagyon eséllyel fordulnak elő, és nem méretezhető hiba. Van, például könnyen lehetővé elfelejti a tanúsítványok biztonsági mentése és hagyható nem választható, de a feladatátvételt követően a kiszolgáló új tanúsítványok vásárlása.

Egy jó vész-helyreállítási megoldást helyreállítási tervek modellezési engedélyezni kell az összetett alkalmazási architektúrákban körül. Ez lehet különböző rétegek közötti alkalmazástársítások kezelése testreszabott lépések hozzáadása lehetőséget. Ha egy olyan vészhelyzet esetén, ez egy alacsonyabb RTO vezető kattintással meg arról, hogy hibaüzenetet megoldást kínál.


Ez a cikk ismerteti, hogyan védi meg egy IIS-alapú webes alkalmazást, amely [Azure Site Recovery](site-recovery-overview.md). Ez a cikk foglalkozik végez replikációt egy három réteg Azure, a vész-helyreállítási részletezési honnan és hogyan zajlik a feladatátvevő IIS-alapú webalkalmazás ajánlott eljárásai az alkalmazás az Azure-bA.


## <a name="prerequisites"></a>Előfeltételek

Megkezdése előtt győződjön meg arról, hogy a következő követelményeknek:

1. [A virtuális gépek replikálása Azure-bA](site-recovery-vmware-to-azure.md)
1. Hogyan [egy helyreállítási hálózathoz. terv](site-recovery-network-design.md)
1. [A teszt feladatátvétel az Azure-bA](./site-recovery-test-failover-to-azure.md)
1. [A feladatátvétel az Azure-bA](site-recovery-failover.md)
1. Hogyan [tartományvezérlő replikálása](site-recovery-active-directory.md)
1. Hogyan [SQL-kiszolgáló replikálása](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Központi telepítés minták
Az IIS-alapú webalkalmazás általában a következő egyike annak a következő központi telepítési:

**1. telepítési minta** az IIS webfarm alkalmazás kérelem Routing(ARR), az IIS-kiszolgáló és a Microsoft SQL Server alapú.

![Telepítési minta](./media/site-recovery-iis/deployment-pattern1.png)

**Telepítési minta 2** az IIS webfarm alkalmazás kérelem Routing(ARR), IIS-kiszolgálót, kiszolgáló és a Microsoft SQL Server alapú.


![Telepítési minta](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Webhely-helyreállítási támogatás

Történő létrehozásának ebben a cikkben, IIS 7.5-ös verziója a Windows Server 2012 R2 Enterprise kiszolgálóval VMware virtuális gépek használják. Mivel helyreállítási helyreplikálásának alkalmazás független, a ajánlás itt is és az IIS más verzióját a következő esetekben tartsa várt.

### <a name="source-and-target"></a>Forrása és célja

**Scenario** | **Egy másodlagos helyre** | **Az Azure-bA**
--- | --- | ---
**Hyper-V** | Igen | Igen
**VMware** | Igen | Igen
**Fizikai kiszolgáló** | Nem | Igen
**Azure**|NA|Igen

## <a name="replicate-virtual-machines"></a>Virtuális gépek replikálása

Hajtsa végre a [Ez az útmutató](site-recovery-vmware-to-azure.md) elindításához minden az IIS webkiszolgáló farm virtuális gépek replikálása Azure-bA.

Ha statikus IP-címet használ, majd adja meg az IP-cím azt szeretné, hogy a virtuális gépet a hálózatról a [ **cél IP-címet** ](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) a számítási és hálózati beállításainak megadása.

![Cél IP](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>Helyreállítási terv létrehozása

A helyreállítási terv lehetővé teszi, hogy a különböző rétegek egy többrétegű alkalmazást, ezért az alkalmazás konzisztencia fenntartása a feladatátvételi sorrendje. Az alábbiakban a helyreállítási terv többrétegű webalkalmazás létrehozásához szükséges lépéseket.  [További információ a helyreállítási terv létrehozása](./site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuális gépek feladatátvételi csoportok hozzáadása
Egy tipikus többrétegű IIS-webalkalmazás virtuális gépek, az IIS-kiszolgáló által létrehozott webes réteg és egy alkalmazás réteg SQL adatbázis-rétegből áll. A virtuális gépek hozzáadása a következő lépéseket a réteg alapján különböző csoporthoz. [További információ a helyreállítási terv customising](site-recovery-runbook-automation.md#customize-the-recovery-plan).

1. Helyreállítási terv létrehozása. Adja hozzá az adatbázis réteg virtuális gépein, annak érdekében, hogy azok leállítási utolsó vagy első felvet csoport 1.

1. Adja hozzá az alkalmazás réteg virtuális gépein csoport 2, úgy, hogy azok kerülnek sorra, miután az adatbázis-rétegből állapotba nem kerül.

1. Adja hozzá a webes réteg virtuális gépein csoport 3 úgy, hogy azok kerülnek sorra, miután az alkalmazás réteg leállását.

1. Load balance virtuális gépek hozzáadása csoport 4 úgy, hogy azok kerülnek sorra, miután a webes réteg leállását.


### <a name="adding-scripts-to-the-recovery-plan"></a>A helyreállítási terv parancsprogramokat hozzáadása
Szükség lehet az Azure virtuális gépek post feladatátvételi/feladatátvételi teszt megfelelően ellenőrizze az IIS webkiszolgáló farm függvény bizonyos műveletek elvégzéséhez. Automatizálható a feladás egy vagy több feladatátvételi művelet, például a DNS-bejegyzés frissítése webhelykötések módosítása, módosítsa a kapcsolati karakterláncban adja hozzá a megfelelő parancsfájlokat, az alábbi, a helyreállítási tervben. [Ismerje meg, további információk hozzáadása a parancsfájl helyreállítási terv](./site-recovery-how-to-add-vmmscript.md).

#### <a name="dns-update"></a>DNS-frissítési
Ha a DNS-ben van konfigurálva a DNS dinamikus frissítést, majd a virtuális gépek általában után elindítja az új IP-cím frissítse a DNS-ben. Ha hozzá szeretne adni egy explicit lépés DNS frissítheti az új IP-címek a virtuális gépet, majd adja hozzá ezt a [parancsprogramot a DNS-ben IP frissítéséhez](https://aka.ms/asr-dns-update) a post műveletek a helyreállítási terv csoportok.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Az alkalmazás web.config kapcsolati karakterlánc
A kapcsolati karakterláncot adja meg az adatbázis, amely a webhely kommunikál.

Ha az adatbázis-virtuálisgép neve a kapcsolati karakterláncot, további lépésekre nincs szükséges post feladatátvételi. Az alkalmazás automatikusan képes kommunikálni az DB. Is ha az IP-cím, az adatbázis-virtuális gép megőrzi, azt már nincs szükség frissíteni a kapcsolati karakterláncot. Ha a kapcsolati karakterlánc hivatkozik az adatbázis virtuális gép IP-címet, kell lennie a frissített post feladatátvételi. Például a következő kapcsolati karakterlánc az IP-127.0.1.2 db mutat

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

A kapcsolati karakterláncot a webes réteg hozzáadásával frissítheti [IIS kapcsolat frissítő parancsfájlt](https://aka.ms/asr-update-webtier-script-classic) a helyreállítási tervben 3 csoport után.

#### <a name="site-bindings-for-the-application"></a>Az alkalmazás hely kötései
Minden webhely kötési információ tartalmazza, kötés, az IP-cím, ahol az IIS-kiszolgáló figyel a kérelmekre a helyhez, a portszámot és a hely az állomásnevek áll. A feladatátvételi ilyen kötést kell frissíteni kell a hozzájuk társított IP-cím megváltozása esetén.

> [!NOTE]
>
> Ha "az összes ki nem osztott" az alábbi példában látható módon a webhelykötés van megjelölve, nem kell frissíteni a kötés post feladatátvételi. Is, ha az egy helyhez társított IP-cím nem változik a post feladatátvétel, a webhely kötés kell frissítése (az IP-cím megőrzési függ a hálózati architektúra és alhálózatok az elsődleges és a másodlagos helyekhez hozzárendelt, és emiatt előfordulhat, hogy vagy nem valósítható meg, hogy a szervezet.)

![SSL-kötés](./media/site-recovery-iis/sslbinding.png)

Ha egy helyen vannak társítva az IP-cím, szüksége lesz az összes hely kötései frissítéséhez az új IP-címmel. Hozzáadhat [IIS webes réteg frissítő parancsfájlt](https://aka.ms/asr-web-tier-update-runbook-classic) 3 csoport helyreállítási tervben a hely kötései módosítása után.


#### <a name="update-load-balancer-ip-address"></a>Load balancer IP-címének frissítése
Ha a virtuális gép alkalmazáskérelmek irányítására, vegye fel [IIS ARR feladatátvételi parancsfájl](https://aka.ms/asr-iis-arrtier-failover-script-classic) csoport 4 az IP-címének frissítése után.

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>Az SSL-tanúsítvány kötés egy https-kapcsolaton
Webhelyek rendelkezhet társított SSL-tanúsítvány, amely segít annak biztosításában a webkiszolgáló és a felhasználó böngészője közötti biztonságos kommunikációhoz. Ha a webhely https-kapcsolatot, és egy társított https hely kötése az IP-cím, az IIS-kiszolgáló SSL-tanúsítvány kötést, egy új webhelykötések hozzá kell adni az IP-címét az IIS virtuális gép post feladatátvételi a tanúsítvány.

Az SSL-tanúsítványt kibocsáthatja elleni-

a) a webhely a teljesen minősített tartományneve<br>
b) a kiszolgáló nevét<br>
c) helyettesítő tanúsítvány a tartománynév<br>
d) egy IP-cím – Ha az SSL-tanúsítványt ad ki, szemben az IP-címe az IIS-kiszolgálót, egy másik SSL-tanúsítványt kell az IP-cím, az IIS-kiszolgáló az Azure site állították ki kell, és a tanúsítvány további SSL-kötést kell létrehozni. Emiatt tanácsos IP állították ki egy SSL-tanúsítványt használ. Ez a beállítás akkor kevesebb széles körben használt, és hamarosan megszűnnek új hitelesítésszolgáltató/böngésző fórum változások szerint.

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>A webalkalmazás és az alkalmazás szint között függőség frissítése
Ha egy alkalmazás-specifikus függőség a virtuális gépek IP-címe alapján, a függőségi post feladatátvételi frissíteni szeretné.

## <a name="doing-a-test-failover"></a>A teszt feladatátvétel
Hajtsa végre a [Ez az útmutató](site-recovery-test-failover-to-azure.md) feladatátvételi teszt végrehajtásához.

1.  Nyissa meg az Azure-portálhoz, és válassza a helyreállítási szolgáltatás tárolójából.
1.  Kattintson a helyreállítási terv létrehozása az IIS webfarm.
1.  Kattintson a "Test Failover".
1.  Válassza ki a helyreállítási pont és a teszt feladatátvételi megkezdéséhez Azure virtuális hálózat.
1.  A másodlagos környezetben működik, ha az érvényesítést végezheti el.
1.  Ha az ellenőrzés befejeződött, kiválaszthatja, hogy érvényesítést végrehajtani, és a teszt feladatátvételi környezet karbantartása.

## <a name="doing-a-failover"></a>A feladatátvétel
Hajtsa végre a [Ez az útmutató](site-recovery-failover.md) Ha feladatátvételt végez.

1.  Nyissa meg az Azure-portálhoz, és válassza a helyreállítási szolgáltatás tárolójából.
1.  Kattintson a helyreállítási terv létrehozása az IIS webfarm.
1.  Kattintson a "Failover".
1.  Válassza ki a helyreállítási pontot a feladatátvételi folyamat elindításához.

## <a name="next-steps"></a>További lépések
További tudnivalók [replikálja más alkalmazások](site-recovery-workload.md) Site Recovery segítségével.
