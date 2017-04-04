---
title: "A platformszolgáltatásos SQL Database és A SQL Server futtatása felhőben, virtuális gépeken (Iaas) | Microsoft Docs"
description: "Cikkünkből megtudhatja, melyik felhőalapú SQL Server-verzió illik a legjobban az alkalmazásához: az Azure SQL Database (platformszolgáltatásos) adatbázis vagy az Azure virtuális gépeken futó felhőalapú SQL Server?"
services: sql-database, virtual-machines
keywords: "SQL Server-felhő, SQL Server a felhőben, PaaS-adatbázis, felhőalapú SQL Server, DBaaS"
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: cjgronlund
ms.assetid: 7467f422-b77d-4b60-9cb5-0f1ec17ec565
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 98cb1d7e5fd464421ae97f2ace33852e71a18a21
ms.lasthandoff: 03/25/2017


---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Felhőalapú SQL Server-verzió választása: Azure SQL Database (PaaS) adatbázis vagy az Azure virtuális gépeken futó SQL Server (IaaS)
Az Azure két lehetőséget biztosít SQL Server számítási feladatok a Microsoft Azure-ban történő üzemeltetésére:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): natív módon felhőalapú, szoftverszolgáltatás (SaaS) típusú alkalmazások fejlesztésére optimalizált SQL-adatbázis, amely platformszolgáltatási adatbázisként vagy adatbázis-szolgáltatásként is ismert. Ez a megoldás az SQL Server legtöbb funkciójával kompatibilis. További információ a PaaS-ről: [Mi az a PaaS?](https://azure.microsoft.com/overview/what-is-paas/)
* [Azure virtuális gépeken futó SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/): az SQL Servert ebben a megoldásban a felhőben, az Azure-platformon futó Windows Server virtuális gépekre telepítik, illetve ezek futtatják a programot. Ez a módszer IaaS néven is ismert.
  Az Azure virtuális gépeken futó SQL Server a létező SQL Server-alkalmazások áttelepítésére lett optimalizálva. Az SQL Server minden verziója és kiadása elérhető. 100%-os kompatibilitást nyújt az SQL Serverrel, így Ön annyi adatbázist futtathat, amennyire szüksége van, és adatbázisok közti tranzakciókat hajthat végre. Teljes körű vezérlést biztosít az SQL Serveren és Windows rendszeren.

Ebből a cikkből megtudhatja, hogyan illeszkednek ezek a verziók a Microsoft adatplatformjának rendszerébe, és rátalálhat az üzleti igényeinek leginkább megfelelő lehetőségre. Akár az adminisztrációs terhek csökkentését, akár a költségek csökkentését helyezi előtérbe, ez a cikk segít eldönteni, hogy melyik megközelítés képes teljesíteni az Ön számára legfontosabb üzleti igényekhez kötődő elvárásokat.

## <a name="microsofts-data-platform"></a>A Microsoft adatplatformja
Az Azure-platformon futó és a helyszíni SQL Server-adatbázisok közötti összehasonlítás első lépése, hogy leszögezzük: semmiről nem kell lemondania. A Microsoft adatplatformja különböző fizikai, helyszíni gépeken, magánfelhőt alkalmazó környezetekben, külső fejlesztőtől származó magánfelhő-környezetekben és nyilvános felhőkben egyaránt lehetővé teszi az SQL Server-technológia felhasználását. Az Azure-beli virtuális gépeken futó SQL Server helyszíni és felhőben futó üzemelő példányok együttes használatával segít megfelelni az egyedi és sokszínű üzleti igényeiknek, ráadásul Ön az összes környezetben ugyanazokat a kiszolgálótermékeket, fejlesztői eszközöket és ismereteket alkalmazhatja.

   ![Felhőalapú SQL Server-verziók: SQL Server infrastruktúra-szolgáltatásban vagy SaaS SQL-adatbázis a felhőben.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Ahogy az az ábrán is látható, az ajánlatokat az infrastruktúra feletti felügyelet mértéke (X-tengely) és az adatbázisszintű konszolidáció és automatizálás által elérhető költségmegtakarítás mértéke (Y-tengely) különbözteti meg egymástól.

Az alkalmazások fejlesztése során négy alapvető lehetőség áll rendelkezésre az alkalmazás SQL Server részének futtatására:

* SQL Server nem virtuális, fizikai gépeken
* SQL Server helyszíni virtuális gépeken (magánfelhő)
* SQL Server Azure virtuális gépeken (Microsoft nyilvános felhő)
* Azure SQL Database (Microsoft nyilvános felhő)

A következő szakaszokban megismerheti a Microsoft nyilvános felhőben futó SQL Servert: Azure SQL Database és Azure virtuális gépeken futó SQL Server. Ezenfelül áttekintünk néhány általános üzleti igényt, amelyek segítségével megállapíthatja, hogy melyik verzió a legjobb az Ön alkalmazása számára.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Az Azure SQL Database és az Azure virtuális gépeken futó SQL Server részletes bemutatása
Az **Azure SQL Database** az Azure-felhőben üzemeltetett relációs adatbázis-szolgáltatás, amely a *szoftverszolgáltatás (SaaS)* és a *platformszolgáltatás (PaaS)* ipari kategóriákba tartozik. Az [SQL Database](sql-database-technical-overview.md) a Microsoft tulajdonában álló, illetve általa futtatott és fenntartott szabványos hardvereken és szoftvereken fut. Az SQL Database beépített szolgáltatásai és funkciói segítségével közvetlenül a szolgáltatásban végezheti el a fejlesztést. Az SQL Database használatalapú fizetéssel működik, emellett a teljesítményigény növekedése esetére lehetőség van vertikális vagy horizontális felskálázásra is.

Az **Azure virtuális gépeken futó SQL Server** az *infrastruktúra-szolgáltatás (IaaS)* kategóriába esik. Ebben a verzióban az SQL Server egy felhőalapú virtuális gépen fut. Az SQL Database-hez hasonlóan ez is a Microsoft tulajdonában álló, illetve általa üzemeltetett és fenntartott szabványos hardvereken fut. Az SQL Server virtuális gépen történő futtatásakor választhat, hogy használat alapján fizet egy SQL Server-rendszerképben található SQL Server-licencért, vagy egy már meglévő licencet használ. A virtuális gépet szükség szerint könnyedén méretezheti, illetve szüneteltetheti/folytathatja.

Általánosságban elmondható, hogy ezeket az SQL-verziókat különböző célok végrehajtására optimalizálták:

* Az **Azure SQL Database** segítségével nagyszámú adatbázis üzembe helyezése és kezelése esetében a lehető legalacsonyabbra csökkenthetők a költségek. Ez a megoldás csökkenti az adminisztrációra fordítandó összeget, mivel használata esetén nincs szükség virtuális gépek, operációs rendszerek vagy adatbázisszoftverek kezelésére. Nem kell foglalkoznia a frissítések, a magas rendelkezésre állás és a [biztonsági mentések](sql-database-automated-backups.md) kezelésével sem. Összefoglalva elmondható, hogy az Azure SQL Database segítségével egyetlen számítástechnikai vagy fejlesztői erőforrás jóval nagyobb számú adatbázis kezelésére válik alkalmassá.
* Az **Azure virtuális gépeken futó SQL Server** segítségével az Azure-ba helyezhet meglévő alkalmazásokat, vagy hibrid megoldásként terjeszthet ki a felhőbe meglévő helyszíni alkalmazásokat. Emellett a virtuális gépen futó SQL Servert használhatja hagyományos SQL Server-alkalmazások fejlesztésére és tesztelésére is. Az Azure virtuális gépeken futó SQL Server esetében teljes körű rendszergazdai jogokkal rendelkezik egy dedikált SQL Server-példányban és egy felhőalapú virtuális gépen. Ez tökéletes választás, ha a vállalatnál adva vannak a virtuális gépek fenntartásához szükséges számítástechnikai erőforrások. E lehetőségeknek köszönhetően a legapróbb részletekig testre szabhatja a rendszert, úgy, hogy az a leginkább megfeleljen az alkalmazás teljesítmény- és rendelkezésre állási követelményeinek.

Az alábbi táblázatban az SQL Database és az Azure virtuális gépeken futó SQL Server főbb jellemzőit foglaljuk össze:

| **A következőkre alkalmas:** | **Azure SQL Database** | **SQL Server Azure virtuális gépen** |
| --- | --- | --- |
|  |Új felhőalapú alkalmazásokhoz, amelyek fejlesztésére és népszerűsítésére szűkösebb időkeret áll rendelkezésre. |Gyors felhőbeli áttelepítést igénylő alkalmazásokhoz, amelyekben legfeljebb minimális változtatásokra van szükség. Ha gyorsan kell elvégezni a fejlesztést és a tesztelést, és nem szeretne helyszíni nem üzemi SQL Server-hardvert vásárolni. |
|  | Olyan csoportok számára, amelyeknek beépített magas rendelkezésre állási, vészhelyreállítási és frissítési mechanizmusokkal rendelkező adatbázisokra van szüksége. |Olyan csoportok számára, amelyek konfigurálni és kezelni tudják az SQL Server magas rendelkezésre állását, a vészhelyreállítását és frissítéseit. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. | |
|  | Olyan csoportok számára, amelyek nem szeretnének az alkalmazás mögötti operációs rendszer beállításával és egyéb konfigurációs feladatokkal foglalkozni. |Ha testre szabható környezetre van szüksége, amelyhez teljes körű rendszergazdai jogokkal rendelkezik. | |
|  | Legfeljebb 1 TB méretű adatbázisokhoz, vagy egy kibővítési mintával [vízszintesen vagy függőlegesen particionálható](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) nagyobb adatbázisokhoz. |Akár 64 TB tárterülettel rendelkező SQL Server-példányokhoz. A példányok annyi adatbázist képesek támogatni, amennyire szükség van. | |
|  | [Szoftverszolgáltatás (SaaS) típusú alkalmazásokhoz](sql-database-design-patterns-multi-tenancy-saas-applications.md). |Vállalati és hibrid alkalmazások áthelyezéséhez és létrehozásához. | |
|  | | |
| **Erőforrások:** |Ha nem szeretne az alapul szolgáló infrastruktúra konfigurálására és felügyeletére informatikai erőforrásokat fordítani, hanem az alkalmazásrétegre kíván összpontosítani. |Ha rendelkezik bizonyos informatikai erőforrásokkal a konfigurációhoz és felügyelethez. Mindezeket egyes beépített automatizált funkciók jelentősen leegyszerűsítik. |
| **Tulajdonosi költségek:** |Nincsenek hardverköltségek és alacsonyabbak a felügyeleti költségek. |Nincsenek hardverköltségek. |
| **Az üzletmenet folytonossága** |Az infrastruktúra beépített hibatűrési képességei mellett az Azure SQL Database olyan szolgáltatásokat kínál az üzletmenet folytonosságának javítására, mint az [automatikus biztonsági mentések](sql-database-automated-backups.md), az [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore), a [geo-visszaállítás](sql-database-recovery-using-backups.md#geo-restore) és az [aktív georeplikáció](sql-database-geo-replication-overview.md). További információk: [SQL Database business continuity overview](sql-database-business-continuity.md) (Az SQL Database üzletmenet-folytonossági funkcióinak áttekintése). |Az Azure virtuális gépeken futó SQL Server lehetőséget kínál az adatbázis konkrét igényeinek megfelelő magas rendelkezésre állási és vészhelyreállítási megoldás kialakítására. Így az adott alkalmazásra optimalizálhatja a rendszert. Szükség esetén önállóan is tesztelheti a feladatátvételt. További információk: [High Availability and Disaster Recovery for SQL Server in Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) (Magas rendelkezésre állás és vészhelyreállítás Azure virtuális gépeken futó SQL Serveren). |
| **Hibrid felhő:** |A helyszíni alkalmazások képesek az Azure SQL Database-ben tárolt adatok elérésére. |Az Azure virtuális gépeken futó SQL Server esetében részben a felhőben, részben helyszínen futó alkalmazásokat is használhat. Például az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) segítségével kiterjesztheti helyszíni hálózatát és Active Directory-tartományát a felhőre. Ezenfelül az [Azure SQL Server-adatfájlok](http://msdn.microsoft.com/library/dn385720.aspx) funkciójával elhelyezheti a helyszíni adatfájlokat az Azure Storage tárhelyen. További információk: [Introduction to SQL Server 2014 Hybrid Cloud](http://msdn.microsoft.com/library/dn606154.aspx) (Bevezetés az SQL Server 2014 által biztosított hibrid felhő használatába). |
|  | Előfizetőként támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx) az adatok replikálása céljából. |Teljes körűen támogatja az [SQL Server tranzakciós replikációját](https://msdn.microsoft.com/library/mt589530.aspx), az [AlwaysOn rendelkezésre állási csoportokat](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), az integrációs szolgáltatásokat és a naplóküldést az adatok replikálása céljából. Emellett a hagyományos SQL Server-biztonságimásolatok is teljes körűen támogatottak. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Az Azure SQL Database vagy az Azure virtuális gépeken futó SQL Server használatát támogató üzleti indokok
### <a name="cost"></a>Költségek
Az adatbázisok üzemeltetési módjának kiválasztását általában a rendelkezésre álló források határozzák meg, és ez egyformán igaz, akár szűkös költségvetésű startupról, akár egy nagyvállalat szigorú költségkeretek közé szorított osztályáról van szó. Ebben a részben először az Azure-ben elérhető két relációsadatbázis-változatra (az SQL Database-re és az Azure virtuális gépeken futó SQL Serverre) vonatkozó számlázási és licencelési alapokkal ismerkedhet meg. Megismerheti az alkalmazás összköltségének kiszámításának módját is.

#### <a name="billing-and-licensing-basics"></a>A számlázás és a licencelés alapjai
Az **SQL Database** értékesítése szolgáltatásként történik az ügyfelek számára, nem pedig licenccel.  Az [Azure virtuális gépeken futó SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) mellé jár egy percalapon fizetendő licenc is. Ha már rendelkezik egy licenccel, azt is használhatja.  

Az **SQL Database** jelenleg többféle szolgáltatáscsomagban kapható, amelyek esetében a választott szolgáltatás- és teljesítményszinten alapuló rögzített óradíjat kell fizetni. Emellett a kimenő internetes forgalom számlázása a normál [adatátviteli díjszabások](https://azure.microsoft.com/pricing/details/data-transfers/) szerint történik. Az Alapszintű, a Standard és a Prémium csomag és a hozzá választható különböző teljesítményszintek az alkalmazás maximális terhelése esetén is megbízhatóan képesek működni. Az alkalmazás változó sebességigényét követve akár válthat is a szolgáltatás- és teljesítményszintek között. Amennyiben az adatbázisban sok tranzakció zajlik, illetve nagyszámú egyidejű felhasználót kell kiszolgálnia, javasoljuk, hogy válassza a Prémium szolgáltatásszintet. Az aktuálisan választható szolgáltatásszintekkel kapcsolatos további információkért lásd: [Azure SQL Database Service Tiers](sql-database-service-tiers.md) (Az Azure SQL Database szolgáltatáscsomagjai). Létrehozhat [rugalmas készleteket](sql-database-elastic-pool.md) a nagy teljesítményű erőforrások adatbázispéldányok közötti megosztásához is.

Az **SQL Database** használata esetén az adatbázis automatikus konfigurálásáért, javításáért és frissítéséért a Microsoft felel, így Önnek kevesebbet kell adminisztrációra költenie. Ezenfelül a [beépített biztonsági mentési](sql-database-automated-backups.md) funkciókkal is jelentős költségmegtakarítást érhet el, különösen, ha nagyszámú adatbázist használ.

Az **Azure virtuális gépeken futó SQL Server** segítségével használhatja a platform által biztosított bármelyik SQL Server-rendszerképet (amely tartalmaz licencet) vagy saját SQL Server-licencét. A támogatott SQL Server-verziók (2008R2, 2012, 2014, 2016) és -kiadások (Developer, Express, Web, Standard, Enterprise) mindegyike elérhető. Emellett a rendszerképek saját licenccel használható (BYOL) verziói is elérhetők. Az Azure által biztosított rendszerképek használata esetén az üzemeltetési költségek a virtuális gép méretétől, valamint a használni kívánt SQL Server-verziótól függnek. A virtuális gép méretétől és az SQL Server verziójától függetlenül meg kell azonban fizetnie az SQL Server és a Windows Server percalapú licencelési költségeit, valamint a virtuális gép lemezeivel kapcsolatban felmerülő Azure Storage-költségeket. A percalapú számlázásnak köszönhetően igényeinek megfelelő ideig használhatja az SQL Servert, anélkül, hogy további SQL Server-licenceket kellene vásárolnia. Ha saját SQL Server-licencét használja az Azure-ban, csak a Windows Server és a tárhely költségeit kell kifizetnie. A saját licenc használatával kapcsolatos további információkért lásd: [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/) (Licenchordozás az Azure Frissítési garancia programja keretében).

#### <a name="calculating-the-total-application-cost"></a>Az alkalmazás összköltségének kiszámítása
A felhőplatform használatának kezdetén az alkalmazás futtatásának díját a fejlesztés és a felügyelet költségei, valamint a nyilvános felhőplatform használatának szolgáltatási költségei teszik ki.

Az alábbiakban az alkalmazás SQL Database-zel és az Azure virtuális gépeken futó SQL Serverrel történő futtatásának részletes költségszámításait láthatja:

**Az Azure SQL Database használata esetében:**

*Alkalmazás összköltsége = a lehető legalacsonyabbra csökkentett adminisztrációs költségek + szoftverfejlesztési költségek + SQL Database szolgáltatási költségei*

**Az Azure virtuális gépeken futó SQL Server esetében:**

*Alkalmazás összköltsége = a szoftver fejlesztésének lehető legalacsonyabbra csökkentett költségei + adminisztrációs költségek + az SQL Server és a Windows Server licencköltségei + az Azure Storage költségei*

Az árakkal kapcsolatos további információkért lásd a következő erőforrásokat:

* [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
* [Virtuális gépek – Díjszabás ](https://azure.microsoft.com/pricing/details/virtual-machines/) ([SQL-hez](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) és [Windowshoz](https://azure.microsoft.com/pricing/details/virtual-machines/#windows))
* [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> Az SQL Server funkcióinak egy szűk köre nem használható az SQL Database-ben. További információért lásd az [SQL Database funkcióit](sql-database-features.md) és az [SQL Database Transact-SQL információit](sql-database-transact-sql-information.md) ismertető részeket. Ha meglévő SQL Server-megoldást szeretne áttelepíteni a felhőbe, olvassa el a következő témakört: [Migrating a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md) (SQL Server-adatbázis áttelepítése az Azure SQL Database alá). Amikor meglévő helyszíni SQL Server-alkalmazást telepít át az SQL Database alá, fontolja meg az alkalmazás frissítését, hogy az kihasználhassa a felhőszolgáltatások által kínált előnyöket. Az alkalmazás üzemeltetésére használhatja például az [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) vagy az [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) szolgáltatást, így további költségelőnyökhöz juthat.
> 
> 

### <a name="administration"></a>Adminisztráció
Számos vállalat számára a felhőszolgáltatásokra történő átállást az összetett adminisztrációs feladatok kiszervezése ösztönzi, és nem csupán a költségszempontok. Az **SQL Database** használata esetén a Microsoft felel az alkalmazás mögött álló hardverek felügyeletéért. A Microsoft minden adatot automatikusan replikál a magas rendelkezésre állás érdekében, konfigurálja és frissíti az adatbázisszoftvert, kezeli a terheléselosztást, valamint kiszolgálóhiba esetén gondoskodik a transzparens feladatátvételről. Az adatbázis kezelését továbbra is Ön végzi, de az adatbázismotor, valamint a kiszolgáló-operációsrendszer és -hardver felügyeletével többé nem kell foglalkoznia.  Ön felelős például a következő területekért: adatbázisok és bejelentkezési adatok, indexek és lekérdezések hangolása, naplózás és biztonság.

Az **Azure virtuális gépeken futó SQL Server** használata esetén teljes mértékben Ön végezheti el az operációs rendszer és az SQL Server-példány konfigurálását. A virtuális gépen Ön dönti el, hogy mikor frissíti az operációs rendszert és az adatbázisszoftvert, illetve, hogy telepít-e kiegészítő programokat, például víruskereső vagy biztonsági mentési eszközöket. Egyes automatizált szolgáltatások jelentősen leegyszerűsítik a frissítést, a biztonsági mentések készítését és a magas rendelkezésre állást. Ezenfelül meghatározhatja a virtuális gép méretét, a lemezek számát, valamint a tárhely konfigurációját is. Az Azure-ban igény szerint módosítható a virtuális gépek mérete. További információk: [Virtual Machine and Cloud Service Sizes for Azure](../virtual-machines/virtual-machines-windows-sizes.md) (Virtuális gépek és felhőszolgáltatások mérete az Azure-ban). 

### <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)
Számos számítástechnikai osztály számára elsődleges prioritást jelent a szolgáltatói szerződésben (SLA) vállalt üzemidő biztosítása. Ebben a részben áttekintjük, hogy milyen  SLA vonatkozik az egyes adatbázis-üzemeltetési megoldásokra.

Az **SQL Database** Alapszintű, Standard és Prémium szolgáltatáscsomagja esetében a Microsoft 99,99%-os SLA-elérhetőséget garantál. További információk: [SLA a következőhöz: SQL-adatbázis](https://azure.microsoft.com/support/legal/sla/sql-database/). Az SQL Database szolgáltatáscsomagjaival és az általuk támogatott üzletmenet-folytonossági csomagokkal kapcsolatban lásd: [Service Tiers](sql-database-service-tiers.md) (Szolgáltatáscsomagok).

Az **Azure virtuális gépeken futó SQL Server** esetében a Microsoft 99,95%-os SLA-elérhetőséget garantál, amely kizárólag a virtuális gépre vonatkozik. Ez az SLA nem érvényes a virtuális gépen futó folyamatokra (például az SQL Serverre), ezenfelül legalább két, rendelkezésre állási csoportba rendezett virtuálisgép-példány futtatása is szükséges hozzá. A legfrissebb információkért lásd: [SLA a következőhöz: Virtuális gépek](https://azure.microsoft.com/support/legal/sla/virtual-machines/). A virtuális gépeken üzemeltetett adatbázisok magas rendelkezésre állása érdekében használja az SQL Server által biztosított magas rendelkezésre állási funkciók egyikét, például az [AlwaysOn rendelkezésre állási csoportokat](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). A támogatott magas rendelkezésre állási lehetőségek valamelyikének használata nem biztosít további SLA-t, de lehetővé teszi az adatbázis 99,99% fölötti rendelkezésre állásának elérését.

### <a name="market"></a>Piacra jutás
Az **SQL Database** ideális megoldás az olyan felhőalapú alkalmazásokhoz, amelyek esetében rendkívül fontos szempont a gyors fejlesztés és a rövid piacra jutási idő. A DBA-hoz hasonló programozási funkciói révén tökéletes választás a felhőben dolgozó tervezők és fejlesztők számára, mivel csökkenti az alkalmazás mögötti operációs rendszer és adatbázis felügyeletére fordítandó időt. A [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) és a [PowerShell-parancsmagok](http://msdn.microsoft.com/library/mt740629.aspx) segítségével például akár több ezer adatbázis felügyeleti műveleteit automatizálhatja és felügyelheti. A [rugalmas készletnek](sql-database-elastic-pool.md) és más hasonló funkcióknak köszönhetően Ön az alkalmazásrétegre összpontosíthat, így gyorsabb lehet a megoldások piaci bevezetése.

Az **Azure virtuális gépeken futó SQL Server** tökéletes megoldás, ha meglévő vagy új alkalmazásainak nagy méretű vagy egymáshoz kapcsolódó adatbázisokra van szüksége, vagy a Windows vagy az SQL Server összes funkciójához hozzá kell férnie. Akkor is érdemes ezt a verziót választani, ha meglévő helyszíni alkalmazásokat és adatbázisokat szeretne változtatás nélkül az Azure-ra áttelepíteni. Mivel így nincs szükség a prezentációs, alkalmazási és adatréteg módosítására, időt és pénzt takarít meg, amit nem kell meglévő termékeinek egy új architektúrára való áthelyezésére fordítania. Ehelyett termékeinek az Azure-ra történő áttelepítésére, illetve az Azure-platformon való működéshez esetleg szükséges teljesítményoptimalizálásra összpontosíthat. További információk: [Performance Best Practices for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md) (Teljesítményre vonatkozó ajánlott eljárások az Azure Virtual Machines szolgáltatásban futtatott SQL Server esetében).

## <a name="summary"></a>Összefoglalás
Ebben a cikkben bemutattuk az SQL Database-t és az Azure virtuális gépeken futó SQL Servert, és szót ejtettünk azokról az üzleti szempontokról, amelyek hatással lehetnek választására. Az alábbiakban a megfontolásra érdemes tényeket foglaljuk össze:

Válassza az **Azure SQL Database-t**, ha:

* Új felhőalapú alkalmazásokat fejleszt, hogy igénybe vehesse a felhőszolgáltatások által biztosított költségmegtakarításokat és optimalizált teljesítményt. Ezzel a verzióval a teljes körűen felügyelt felhőszolgáltatások minden előnyét élvezheti, ezenfelül csökkentheti a piacra jutási időt, illetve hosszú távon is optimalizálhatja kiadásait.
* E megoldás választása esetén érdemes a Microsoftra bízni az adatbázisok általános felügyeleti műveleteinek elvégzését, és erősebb rendelkezésre állási SLA-kat igényelni az adatbázisokhoz.

Válassza az **Azure virtuális gépeken futó SQL Servert**, ha:

* Olyan meglévő helyszíni alkalmazásokkal rendelkezik, amelyeket a felhőbe kíván áthelyezni vagy kiterjeszteni, vagy ha 1 TB-nál nagyobb méretű vállalati alkalmazásokat kíván létrehozni. Ez a megoldás 100%-os SQL-kompatibilitást, nagy adatbázis-kapacitást, az SQL Server és a Windows teljes körű irányítását, és biztonságos helyszíni bújtatást biztosít. Ez a verzió a lehető legalacsonyabbra csökkenti a meglévő alkalmazások fejlesztési és átalakítási költségeit.
* Meglévő számítógépes erőforrások használatával Ön irányíthatja a frissítéseket, a biztonsági mentéseket és az adatbázisok magas rendelkezésre állását. Megjegyzendő, hogy egyes automatizált funkciók jelentősen leegyszerűsítik ezeket a műveleteket. 

## <a name="next-steps"></a>Következő lépések
* Tekintse meg [az első Azure SQL Database-adatbázissal](sql-database-get-started.md) foglalkozó témakört, hogy megismerkedhessen az SQL Database használatának első lépéseivel.
* [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
* Az Azure virtuális gépeken futó SQL Server használatának megkezdésével kapcsolatban lásd: [Provision a SQL Server virtual machine in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) (SQL Servert futtató virtuális gép létrehozása az Azure-ban).


