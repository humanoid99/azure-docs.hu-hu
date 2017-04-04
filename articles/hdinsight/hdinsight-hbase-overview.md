---
title: "Mi az a HBase a HDInsight eszközben? | Microsoft Docs"
description: "Az Apache HBase HDInsightban való használatának bevezetése, amely egy, a Hadoop programra épített NoSQL-adatbázis. Megismerheti a használati eseteket, és összehasonlíthatja a HBase programot más Hadoop-fürtökkel."
keywords: bigtable,nosql,mi az a hbase
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: d2a76d53-133a-4849-a30c-88d9c794391c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 45fb85f83570ed7d79e70dc6fb8f0fdabb7ea191
ms.lasthandoff: 03/24/2017


---
# <a name="what-is-hbase-in-hdinsight-a-nosql-database-that-provides-bigtable-like-capabilities-for-hadoop"></a>Mi az a HBase a HDInsight eszközben: Egy NoSQL-adatbázis, amely BigTable-szerű képességeket nyújt a Hadoophoz
Az Apache HBase egy nyílt forráskódú NoSQL-adatbázis, amely a Hadoopra épült, és a Google BigTable után van modellezve. A HBase véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára egy séma nélküli adatbázisban oszlopcsaládok szerint rendezve.

Az adatok a táblasorokban vannak tárolva, és a sorokon belüli adatok oszlopcsalád szerint vannak csoportosítva. A HBase egy séma nélküli adatbázis abban az értelemben, hogy az oszlopokat és a bennük tárolt adattípusokat sem kell meghatározni a használatuk előtt. A nyílt forráskód lineáris módon méreteződik át a több ezer csomópontnyi adat petabájtjainak kezelése érdekében. Az adatredundanciára, a kötegelt feldolgozásra és más olyan szolgáltatásokra támaszkodhat, amelyeket elosztott alkalmazások nyújtanak a Hadoop rendszerben.

## <a name="how-is-hbase-implemented-in-azure-hdinsight"></a>Hogyan van megvalósítva a HBase az Azure HDInsight eszközben?
A HDInsight HBase az Azure környezetbe integrált felügyelt fürtként érhető el. A fürtök úgy vannak konfigurálva, hogy közvetlenül az Azure Blob Storage tárolóban tárolják az adatokat, amely kis késést és nagyobb rugalmasságot biztosít a teljesítménnyel és költségekkel kapcsolatos választások során. Ez lehetővé teszi, hogy az ügyfelek olyan interaktív webhelyeket építsenek, amelyek nagy adatkészletekkel működnek, hogy a több millió végpontból származó érzékelői és telemetriai adatokat tároló szolgáltatásokat építhessenek, és hogy ezeket az adatokat Hadoop-feladatokkal elemezzék. A HBase és a Hadoop jó kezdőpont a big data-projektekhez az Azure rendszerben; különösképpen azt teszik lehetővé, hogy a valós idejű alkalmazások nagy adatkészletekkel dolgozhassanak.

A HDInsight-implementáció kihasználja a HBase méretezhető architektúráját, hogy a táblák automatikus árnyalását, az írások és olvasások erős következetességét és automatikus feladatátvételt nyújtson. A teljesítményt a memóriába való gyorsítótárazás növeli az olvasáshoz, és a nagy streaming-kapacitás az írásokhoz. A HBase-fürt a virtuális hálózaton belül hozható létre. További információ: [HDInsight-fürtök létrehozása az Azure Virtual Network-ön][hbase-provision-vnet].

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hogyan történik az adatok kezelése a HDInsight HBase-ben?
Az adatok a HBase-ben a(z) `create`, `get`, `put` és `scan` parancsokkal kezelhetők a HBase rendszerhéjból. Az adatok a(z) `put` paranccsal írhatók, és a(z) `get` paranccsal olvashatók az adatbázisban. A(z) `scan` paranccsal szerezhetők be adatok a táblák több sorából. Az adatok a HBase C# API-val is kezelhetők, amely egy ügyfélkönyvtárat biztosít a HBase REST API-n felül. A HBase adatbázisok a Hive használatával is lekérdezhetők. Ezen programozási modellek bevezetését lásd: [A HBase első lépései a Hadooppal a HDInsightban][hbase-get-started]. Társprocesszorok is elérhetők, amelyek lehetővé teszik az adatfeldolgozást az adatbázist szolgáltató csomópontokban.

>
> [!NOTE]
> A HBase nem támogatja a Thriftet a HDInsightban.
>

## <a name="scenarios-use-cases-for-hbase"></a>Forgatókönyvek: A HBase használati esetei
A kanonikus használati eset, amelyhez a BigTable (és így a HBase) létrejött, a webes keresés. A keresőmotorok indexeket építenek, amelyek kifejezéseket képeznek le az azokat tartalmazó weblapokra. A HBase azonban sok más használati esethez megfelelő – amelyek közül több ebben a szakaszban van felsorolva.

* Kulcs-érték tároló
  
    A HBase használható kulcs-érték tárolóként, és megfelelő az üzenetkezelési rendszerek kezeléséhez. A Facebook a HBase eszközt használja az üzenetkezelési rendszeréhez, amely ideális az internetes kommunikációk tárolásához és kezeléséhez. A WebTable a HBase eszközt használja a weblapokról kinyert táblázatok keresésére és kezelésére.
* Érzékelői adatok
  
    A HBase hasznos a növekményesen, különböző forrásokból gyűjtött adatok rögzítéséhez. Ebbe tartozik a közösségi hálók adatainak elemzése, az idősorozat, az interaktív irányítópultok naprakészen tartása trendekkel és számlálókkal, valamint a naplórendszerek kezelése. Példák lehetnek erre a Bloomberg Trader terminál és az Open Time Series Database (OpenTSDB), amelyek a kiszolgálórendszerek állapotával kapcsolatos mérőszámokat tárolnak és tesznek elérhetővé.
* Valós idejű lekérdezés
  
    A [Phoenix](http://phoenix.apache.org/) az Apache HBase SQL lekérdezési motorja. JDBC-illesztőként érhető el, és lehetővé teszi a HBase táblák SQL eszközzel végzett lekérdezését és kezelését.
* A HBase platformként
  
    Az alkalmazások a HBase felett futhatnak adattárolóként. Erre példa a Phoenix, az OpenTSDB, a Kiji és a Titan. Az alkalmazások integrálhatók is a HBase eszközzel. Erre példa a Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia és Drill.

## <a name="next-steps"></a>Következő lépések
* [A HBase első lépései a Hadooppal a HDInsightban][hbase-get-started]
* [HDInsight-fürtök létrehozása az Azure Virtual Network-ön][hbase-provision-vnet]
* [HBase-replikálás konfigurálása a HDInsightban](hdinsight-hbase-replication.md)
* [Twitter-vélemények elemzése a HBase-szel a HDInsightban][hbase-twitter-sentiment]
* [A Maven használata a HDInsight (Hadoop) eszközzel HBase-t használó Java-alkalmazások építéséhez][hbase-build-java-maven]

## <a name="see-also"></a>Lásd még:
* [Apache HBase](https://hbase.apache.org/)
* [Bigtable: Elosztott tárolórendszer strukturált adatokhoz](http://research.google.com/archive/bigtable.html)

[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/

