---
title: Mi az Azure SQL Data Warehouse? | Microsoft Docs
description: "Vállalati szintű elosztott adatbázis, amely petabájtnyi mennyiségű relációs és nem relációs adatot képes feldolgozni. Ez az iparág első felhőalapú adatraktára, amely másodpercek alatt szüneteltethető, illetve növelhető vagy csökkenthető a mérete."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: bjhubbard
editor: 
ms.assetid: 4006c201-ec71-4982-b8ba-24bba879d7bb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 2/28/2017
ms.author: jrj;mausher;kevin;barbkess;elbutter
translationtype: Human Translation
ms.sourcegitcommit: bf73ad830226626ddf41cc4ae80e714abf8bcfc2
ms.openlocfilehash: 19e87c61493bd4620120b39a533e9e4b64517538
ms.lasthandoff: 03/01/2017


---
# <a name="what-is-azure-sql-data-warehouse"></a>Mi az Azure SQL Data Warehouse?
Az Azure SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) kialakítású, felhőalapú, horizontálisan felskálázható relációs adatbázis, amely nagy mennyiségű adatot képes feldolgozni. 

SQL Data Warehouse:

* Egyesíti az SQL Server relációs adatbázist és az Azure-felhő horizontális felskálázhatóságát. 
* Elválasztja a tárterületet a számítási műveletektől.
* Lehetővé teszi a számítási műveletek növelését, csökkentését, szüneteltetését vagy folytatását. 
* Integrációt biztosít az Azure platform különböző elemei között.
* Az SQL Server Transact-SQL (T-SQL) nyelvét és eszközeit használja.
* Megfelel különböző jogi és vállalati biztonsági követelményeknek (például SOC és ISO).

A cikk az SQL Data Warehouse főbb szolgáltatásait ismerteti.

## <a name="massively-parallel-processing-architecture"></a>Nagymértékben párhuzamos feldolgozási architektúra
Az SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) elosztott adatbázisrendszerre épül. Az SQL Data Warehouse a háttérben számos olyan tárolóban és feldolgozási egységen osztja el az adatokat, amelyek nem osztanak meg semmit. A rendszer az adatokat a helyileg redundáns Premium tárolórétegben tárolja, amelyen a dinamikusan csatolt számítási csomópontok lekérdezéseket hajtanak végre. Az SQL Data Warehouse az „oszd meg és uralkodj” elv alapján futtatja a műveleteket és az összetett lekérdezéseket. A vezérlő csomópont fogadja a kérelmeket, optimalizálja őket az elosztáshoz, majd átadja őket a számítási csomópontoknak a feladatok párhuzamos végrehajtása érdekében.

Az SQL Data Warehouse a tárterület és a számítási műveletek elkülönítésével a következőkre képes:

* Növelheti és csökkentheti a tárterület a számítási kapacitástól függetlenül.
* Növelheti vagy csökkentheti a számítási kapacitást az adatok áthelyezése nélkül.
* Szüneteltetheti a számítási kapacitást az adatok sérülése nélkül, így csak a tárterületért kell fizetnie.
* A működési időn belül folytatni tudja a számítási kapacitást.

Az architektúrát a következő ábra szemlélteti.

![Az SQL Data Warehouse architektúrája][1]

**Vezérlő csomópont:** a vezérlő csomópont kezeli és optimalizálja a lekérdezéseket. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az SQL Data Warehouse szolgáltatásban a vezérlő csomópontot az SQL Database működteti, és ugyanúgy lehet hozzá csatlakozni, mint eddig. A felszín alatt a vezérlő csomópont koordinálja az összes adatáthelyezést és a számítást, ami a párhuzamos lekérdezések elosztott adatokon történő futtatásához szükséges. Amikor T-SQL-lekérdezést küld az SQL Data Warehouse-nak, a vezérlő csomópont külön lekérdezésekké alakítja át, amelyek párhuzamosan fognak futni minden számítási csomóponton.

**Számítási csomópontok:** a számítási csomópontok állnak az SQL Data Warehouse teljesítménye mögött. Ezek olyan SQL-adatbázisok, amelyek tárolják az adatokat, és feldolgozzák a lekérdezést. Adatok hozzáadásakor az SQL Data Warehouse a számítási csomópontokra osztja el a sorokat. A párhuzamos lekérdezéseket is a számítási csomópontok futtatják az adatokon. A feldolgozást követően ezek a csomópontok visszaküldik az eredményeket a vezérlő csomópontnak. A lekérdezés befejezéséhez a vezérlő csomópont aggregálja az eredményeket, majd visszaadja a végeredményt.

**Tárolás:** a rendszer az adatokat az Azure Blob szolgáltatásban tárolja. Amikor a számítási csomópontok kommunikálnak az adatokkal, közvetlenül a blobtárolóra írnak és arról olvasnak. Mivel az Azure-tárolót transzparens módon és korlátlanul bővítheti, az SQL Data Warehouse is képes ugyanerre. Mivel a számítás és a tárolás nem függ egymástól, az SQL Data Warehouse automatikusan át tudja méretezni a tárolást, külön, a számítás méretezése nélkül, és fordítva. Az Azure Blob tároló emellett teljesen hibatűrő, és megkönnyíti a biztonsági mentési és helyreállítási folyamatokat.

**Adatátviteli szolgáltatás:** az adatátviteli szolgáltatás (DMS) az adatok csomópontok közötti áthelyezését végzi. A DMS hozzáférést biztosít a számítási csomópontoknak az összekapcsolásokhoz és az aggregációhoz szükséges adatokhoz. A DMS nem Azure-szolgáltatás. Ez egy Windows-szolgáltatás, amely minden csomóponton együtt fut az SQL Database szolgáltatással. A DMS egy háttérfolyamat, amellyel nem lehet közvetlenül kapcsolatba lépni. Azonban megtekintheti a lekérdezésterveket annak megtekintéséhez, hogy mikor történnek a DMS-műveletek, mivel a lekérdezések párhuzamos futtatásához szükség van az adatmozgatásra.

## <a name="optimized-for-data-warehouse-workloads"></a>Az adatraktár munkaterhelésére optimalizálva
Az MPP megközelítést számos adattárházra vonatkozó teljesítményoptimalizálás segíti, beleértve a következőket:

* Egy elosztott lekérdezésoptimalizáló és az összes adatra vonatkozó összetett statisztikák készlete. Az adatok méretével és a terjesztésével kapcsolatos információk felhasználásával a szolgáltatás képes optimalizálni a lekérdezéseket, mivel felméri az elosztott lekérdezési műveletek költségét.
* Az adatátviteli folyamatba integrált speciális algoritmusok és technikák hatékonyan mozgatják a lekérdezés végrehajtásához szükséges adatokat a számítási erőforrások között. Ezek az adatátviteli műveletek beépítettek, és minden adatátviteli szolgáltatást érintő optimalizálás automatikusan történik.
* Alapértelmezés szerint fürtözött **oszlopcentrikus** indexek. Az oszlopalapú tárolás használatával az SQL Data Warehouse akár 5-ször nagyobb szintű tömörítésre is képes, mint a hagyományos, soralapú tárolással, és akár 10-szeres vagy nagyobb lekérdezési teljesítménynövekedést érhet el. A sok sort vizsgáló elemzési lekérdezések jobban működnek az oszlopcentrikus indexekkel.


## <a name="predictable-and-scalable-performance-with-data-warehouse-units"></a>Kiszámítható és méretezhető teljesítmény adattárházegységekkel
Az SQL Data Warehouse ugyanolyan technológiával készült, mint az SQL Database, így a felhasználók egységes és kiszámítható teljesítményt várhatnak el az elemzési lekérdezéseknél. A felhasználóknak számítaniuk kell a teljesítmény lineáris skálázására a számítási csomópontok hozzáadásával vagy eltávolításával. Az erőforrások SQL Data Warehouse szolgáltatásnak történő kiosztása az adattárházegységekkel (DWU) mérhető. A DWU-val az SQL Data Warehouse számára kiosztott háttérerőforrások, például a processzor, a memória és az IOPS mérhető. A DWU-k számának növelése növeli az erőforrásokat és a teljesítményt. A DWU-k a következőket biztosítják:

* Az adattárházak skálázhatók anélkül, hogy aggódni kellene a mögöttes hardver vagy szoftver miatt.
* A DWU-szintek teljesítményjavulása is előrejelezhető az adattárház számítási kapacitásának módosítása előtt.
* Meg lehet változtatni vagy át lehet helyezni a példány mögöttes hardverét és szoftverét anélkül, hogy az hatással lenne a számítási feladat teljesítményére.
* A Microsoft fejlesztheti a szolgáltatás mögöttes architektúráját anélkül, hogy az hatással lenne a számítási feladatok teljesítményére.
* A Microsoft gyors ütemben fejleszti az SQL Data Warehouse teljesítményét, és gondoskodik a szolgáltatás további méretezhetőségéről, valamint arról, hogy a fejlődés a teljes rendszeren mindenhol tetten érhető legyen.

Az adattárházegységek három mérőszámból álló mértéknek tekinthetők, amelyek szorosan összefüggnek az adattárház számításifeladat-teljesítményével. A következő kulcsfontosságú, számítási feladatokra vonatkozó mérőszámok lineárisan skálázódnak a DWU-kkal.

**Vizsgálat/aggregáció:** Egy standard adattárház-lekérdezés, amely számos sort átvizsgál, majd komplex összesítést készít. Ez egy I/O- és processzorigényes művelet.

**Betöltés:** Az adatok szolgáltatásba történő bevitelének képessége. A betöltés az Azure Storage Blobból vagy az Azure Data Lake-ből végezhető el a legjobban a PolyBase használatával. A mérőszám úgy lett kialakítva, hogy a szolgáltatást a hálózat és a processzor szempontjából emelje ki.

**Create Table As Select (CTAS):** A CTAS a táblák másolásának képességét méri. Ebbe beletartozik az adatok beolvasása a tárfiókból, az adatok szétosztása a készülék csomópontjai között és az adatok tárfiókba történő újbóli írása. Ez egy processzor-, I/O- és hálózatigényes művelet.

## <a name="built-on-sql-server"></a>Alapja az SQL Server
Az SQL Data Warehouse az SQL Server relációs adatbázismotorján alapul, és számos, a vállalati adatraktáraktól elvárt szolgáltatást tartalmaz. Ha már ismeri a T-SQL nyelvet, könnyűszerrel hasznosíthatja ismereteit az SQL Data Warehouse szolgáltatásban. Függetlenül attól, hogy kezdő vagy haladó, a dokumentációban található példák segítenek a kezdésben. Gondoljon arra, hogyan épülnek fel az SQL Data Warehouse nyelvi elemei:

* Az SQL Data Warehouse számos művelethez a T-SQL szintaxisát használja. Ezenkívül a hagyományos SQL-szerkezetek széles körét támogatja, például a tárolt eljárásokat, a felhasználó által definiált függvényeket, a táblaparticionálást, az indexeket és a rendezéseket.
* Az SQL Data Warehouse emellett számos újabb SQL Server szolgáltatást is tartalmaz, beleérve a fürtözött **oszlopcentrikus** indexeket, a PolyBase-integrációt és az adatok naplózását (fenyegetésértékeléssel).
* Előfordulhat, hogy jelenleg nem érhetők el a T-SQL egyes nyelvi elemei, amelyeket ritkán használnak az adatraktározási feladatoknál, vagy amelyek újdonságnak számítanak az SQL Serverben. További információt az [áttelepítési dokumentációban][Migration documentation] talál.

A Transact-SQL nyelv és az SQL Server, az SQL Data Warehouse, az SQL Database és az Analytics Platform System egyező szolgáltatásai segítségével olyan megoldást fejleszthet, amely megfelel az adattárolási igényeinek. A teljesítmény, a biztonság és a méretezési követelmények alapján eldöntheti, hol szeretné tárolni az adatait, majd szükség szerint továbbíthatja az adatokat a különböző rendszerek között.

## <a name="data-protection"></a>Adatvédelem
Az SQL Data Warehouse minden adatot az Azure Premium helyileg redundáns tárolóban tárol. A rendszer több szinkron másolatot tart az adatokról a helyi adatközpontban, így transzparens adatvédelmet garantál a helyi hibák ellen. Emellett az SQL Data Warehouse rendszeres időközönként automatikusan végrehajtja az aktív (nem szüneteltetett) adatbázisok biztonsági mentését az Azure Storage Snapshots használatával. A biztonsági mentés és a visszaállítás működésének részletes ismertetését [A biztonsági mentés és a visszaállítás áttekintése][Backup and restore overview] című cikkben olvashatja el.

## <a name="integrated-with-microsoft-tools"></a>Integráció a Microsoft eszközeivel
Az SQL Data Warehouse együttműködik számos olyan eszközzel, amelyet az SQL Server felhasználói jól ismernek. Ezek az eszközök a következőket foglalják magukban:

**Hagyományos SQL Server-eszközök:** az SQL Data Warehouse szolgáltatás teljesen integrálható az SQL Server Analysis Services, az Integration Services és a Reporting Services szolgáltatással.

**Felhőalapú eszközök:** az SQL Data Warehouse különböző szolgáltatásokkal integrálható az Azure-ban, beleértve az Azure Data Factory, a Stream Analytics, a Machine Learning és a Power BI szolgáltatást. A teljes listát lásd: [Az integrált eszközök áttekintése][Integrated tools overview].

**Harmadik felektől származó eszközök:** Számos külső eszközszolgáltató tanúsított módon integrálta az eszközeit az SQL Data Warehouse szolgáltatással. A teljes listát lásd: [Az SQL Data Warehouse megoldáspartnerei][SQL Data Warehouse solution partners].

## <a name="hybrid-data-sources-scenarios"></a>Hibrid adatforrások forgatókönyvei
A PolyBase lehetővé teszi a különböző forrásokból származó adatok használatát a jól ismert T-SQL parancsokkal. A PolyBase lehetővé teszi az Azure Blob tárolóban tárolt nem relációs adatok normál táblákhoz hasonló lekérdezését. A PolyBase szolgáltatást akkor használja, ha nem relációs adatokat szeretne lekérdezni vagy nem relációs adatokat szeretne importálni az SQL Data Warehouse szolgáltatásba.

* A PolyBase külső táblák segítségével fér hozzá a nem relációs adatokhoz. A tábladefiníciókat az SQL Data Warehouse tárolja, és az SQL-eszközökkel, illetve a relációs adatok elérésére használt szokásos eszközökkel érheti el őket.
* A PolyBase integrációs szempontból rendszerfüggetlen. Minden általa támogatott forrás ugyanazokat a szolgáltatásokat és funkciókat érheti el. A PolyBase által beolvasott adatok különböző formátumúak lehetnek, a tagolt fájlokat és az ORC-fájlokat is beleértve.
* A PolyBase technológiával hozzá lehet férni azokhoz a blobtárolókhoz, amelyet egy HD Insight-fürt tárolóként használ. Így ugyanazokat az adatokat relációs és nem relációs eszközökkel is elérheti.

## <a name="sla"></a>SLA
Az SQL Data Warehouse a Microsoft Online Services SLA részeként termékszintű szolgáltatói szerződést biztosít. További információ: [SQL Data Warehouse SLA][SLA for SQL Data Warehouse]. Minden más termék szolgáltatói szerződése a [Szolgáltatói szerződések] Azure-oldalon található, vagy letölthető a [Mennyiségi licencelés][Volume Licensing] oldalról. 

## <a name="next-steps"></a>Következő lépések
Most, hogy jobban megismerte az SQL Data Warehouse szolgáltatást, tudjon meg többet az [SQL Data Warehouse gyors létrehozásáról][create a SQL Data Warehouse] és a [mintaadatok betöltéséről][load sample data]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Támogatási jegy létrehozása]
* [MSDN-fórum]
* [Stack Overflow-fórum]
* [Twitter]

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Támogatási jegy létrehozása]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Ügyfelek sikertörténetei]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-fórum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stack Overflow-fórum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL Data Warehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Szolgáltatói szerződések]: https://azure.microsoft.com/en-us/support/legal/sla/

