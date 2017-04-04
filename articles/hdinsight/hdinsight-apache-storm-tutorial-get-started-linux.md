---
title: "Az Apache Storm használatának első lépései az Azure HDInsightban | Microsoft Docs"
description: "Ismerkedjen meg a big data elemzésre szolgáló Apache Storm alkalmazással, valamint a Linux-alapú HDInsight Storm Starter-mintáival. Ismerkedjen meg a Storm valós idejű adatfeldolgozásra történő használatával."
keywords: "apache storm,apache storm-oktatóanyag,big data-elemzés,storm starter"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: d710dcac-35d1-4c27-a8d6-acaf8146b485
ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/17/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 5d74f2c130eeddb1022acf9673c6a2006af2db58
ms.lasthandoff: 03/25/2017

---
#<a name="get-started-with-the-storm-starter-samples-for-big-data-analytics-on-linux-based-hdinsight"></a>Ismerkedés a Storm Starter-mintákkal Linux-alapú HDInsight platformon történő big data-elemzéshez

Az Apache Storm egy skálázható, hibatűrő, elosztott, valós idejű számítási rendszer az adatstreamek feldolgozására. A Storm on Azure HDInsight segítségével olyan felhőalapú Storm-fürtöket hozhat létre, amelyek valós időben végeznek big data elemzést.

> [!IMPORTANT]
> A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További információ: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **SSH- és SCP-ismeretek**. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-storm-cluster"></a>Storm-fürt létrehozása

HDInsight alatt futó Storm-fürt létrehozásához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **+ ÚJ**, **Intelligencia és analitika**, majd a **HDInsight** elemet.
   
    ![HDInsight-fürt létrehozása](./media/hdinsight-apache-storm-tutorial-get-started-linux/create-hdinsight.png)

2. Az **Alapvető beállítások** panelen adja meg a következőket:

    * **Fürt neve**: A HDInsight-fürt neve.
    * **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    * **Fürt bejelentkezési felhasználóneve** és **Fürt bejelentkezési jelszava**: A fürt HTTPS-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Ezekkel a hitelesítő adatokkal érheti el az olyan szolgáltatásokat, mint az Ambari webes felület vagy a REST API.
    * **SSH-felhasználónév**: A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával.
    * **Erőforráscsoport**: Az az erőforráscsoport, amelyben a fürt létre lesz hozva.
    * **Hely**: Az az Azure-régió, amelyben a fürt létre lesz hozva.
   
    ![Előfizetés kiválasztása](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-basic-configuration.png)

3. Válassza ki a **Fürt típusát**, majd állítsa be a következő értékeket a **Fürtkonfiguráció** panelen:
   
    * **Fürt típusa**: Storm

    * **Operációs rendszer**: Linux

    * **Verzió**: Storm 1.0.1 (HDI 3.5)

    * **Fürt szintje**: Standard
     
    Végül mentse a beállításokat a **Kiválasztás** gomb használatával.
     
    ![Fürttípus kiválasztása](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-cluster-type.png)

4. A fürt típusának kijelölése után erősítse meg a beállítást a __Kiválasztás__ gombbal. Ezután kattintson a __Tovább__ gombra az alapszintű konfiguráció befejezéséhez.

5. A **Tárolás** panelen válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépések esetében a panel többi mezőjét hagyja az alapértelmezett értékeken. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez.

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/hdinsight-apache-storm-tutorial-get-started-linux/set-hdinsight-storage-account.png)

6. Az **Összegzés** lapon tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül kattintson a__Létrehozás__ gombra a fürt létrehozásához.
   
    ![A fürtkonfiguráció összegzése](./media/hdinsight-apache-storm-tutorial-get-started-linux/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > A fürt létrehozása 20 percig is eltarthat.

## <a name="run-a-storm-starter-sample-on-hdinsight"></a>Storm Starter-minta futtatása HDInsight platformon

1. Csatlakozzon SSH-val a HDInsight-fürthöz:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Ha az SSH-felhasználói fiókhoz jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használatára van szükség a megfelelő titkos kulcs megadásához. Például: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Használja az alábbi parancsot példatopológia indításához:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
   
    > [!NOTE]
    > A HDInsight előző verzióiban a topológia osztályneve `org.apache.storm.starter.WordCountTopology` helyett `storm.starter.WordCountTopology`.
   
    Ez a parancs a „wordcount” rövid néven elindítja a fürtön a WordCount-példatopológiát. Ez véletlenszerűen állít elő mondatokat, majd az egyes szavak előfordulását számolja meg a mondatokban.
   
    > [!NOTE]
    > A saját topológiák a fürtre történő elküldésekor a fürtöket tartalmazó jar-fájlt a `storm` parancs használata előtt kell másolnia. A fájl másolásához használja az `scp` parancsot. Például: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    > 
    > A WordCount-példa és egyéb Storm Starter-példák megtalálhatóak a fürtön a következő helyen: `/usr/hdp/current/storm-client/contrib/storm-starter/`.

Ha szeretné megtekinteni a Storm Starter-példák forráskódját, a [https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter](https://github.com/apache/storm/tree/1.0.x-branch/examples/storm-starter) helyen találja azokat. A hivatkozás a Storm 1.0.x-es verzió példáira mutat, amely a HDInsight 3.5-ben érhető el. A Storm egyéb verziói esetében használja a __Branch__ (Ág) gombot a lap tetején egy másik Storm-verzió kiválasztásához.

## <a name="monitor-the-topology"></a>A topológia figyelése

A HDInsight-fürtön elérhető Storm webes felhasználói felületet biztosít a futó topológiákkal való munkavégzéshez.

Kövesse az alábbi lépéseket a topológia a Storm felhasználói felületével történő figyeléséhez:

1. A Storm felhasználói felületének megjelenítéséhez egy webböngészőben nyissa meg a következőt: https://CLUSTERNAME.azurehdinsight.net/stormui. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
    
    > [!NOTE]
    > Ha a rendszer felkéri a felhasználónév és a jelszó megadására, a fürt létrehozásakor használt fürtrendszergazda (rendszergazda) nevét és jelszavát adja meg.

2. A **Topology summary** (Topológia összegzése) területen válassza ki a **wordcount** bejegyzést a **Name** (Név) oszlopban. Megjelennek a topológiával kapcsolatos információk.
    
    ![A Storm irányítópultja a Storm Starter WordCount-topológiával kapcsolatos információkkal.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
    
    Ezen a lapon az alábbi információk találhatóak meg:
    
    * **Topológiastatisztikák** – Alapszintű információkat tartalmaz a topológia teljesítményével kapcsolatban, időtartományokba rendezve.
     
        > [!NOTE]
        > Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.

    * **Spoutok** – Alapszintű információkat tartalmaz a spoutokkal kapcsolatban, beleértve az egyes spoutok által visszaadott legutóbbi hibaüzenetet is.
    
    * **Boltok** – Alapszintű információkat tartalmaz a boltokkal kapcsolatban.
    
    * **Topológiakonfiguráció** – Részletes információkat tartalmaz a topológia konfigurációjával kapcsolatban.
     
    Ezen a lapon a következő topológiaműveletek szerepelnek:
   
    * **Aktiválás** – Folytatja az inaktivált topológia feldolgozását.
    
    * **Inaktiválás** – Megszakítja a futó topológiát.
    
    * **Visszaegyensúlyozás** – Beállítja a topológia párhuzamosságát. A fürtben található csomópontok számának megváltoztatását követően újra ki kell egyensúlyozni a futó topológiákat. Az újraegyensúlyozás beállítja a párhuzamosságot a fürtben található csomópontok számának növekedése/csökkenése kiegyensúlyozása érdekében. További információ: [Understanding the parallelism of a Storm topology](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) (A Storm-topológia párhuzamosságának ismertetése).
    
    * **Törlés** – A Storm-topológia leállítása bizonyos időtúllépést követően.

3. Válassza ki a jelen lapon található **Spoutok** vagy **Boltok** szakaszok bejegyzéseinek egyikét. Ekkor információk jelennek meg a kiválasztott összetevővel kapcsolatban.
   
    ![A Storm irányítópultja a kiválasztott összetevőkkel kapcsolatos információkkal.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    Ezen a lapon az alábbi információk találhatóak meg:
   
    * **Spout-/Bolt-statisztikák** – Alapszintű információkat tartalmaz az egyes összetevők teljesítményével kapcsolatban, időtartományokba rendezve.
     
        > [!NOTE]
        > Egy adott időtartományt kiválasztva a lap más szakaszaiban található információk időtartománya megváltozik.
     
    * **Beviteli statisztikák** (csak boltok esetében) – Információkat tartalmaz a bolt által feldolgozott adatokat előállító összetevőkről.
    
    * **Kimeneti statisztikák** – Információkat tartalmaz a bolt által kibocsátott adatokról.
    
    * **Végrehajtók** – Információkat tartalmaz a jelen összetevő példányairól.
    
    * **Hibák** – A jelen összetevő által visszaadott hibaüzenetek.

4. Egy adott spout vagy bolt részletes adatainak megtekintésekor az összetevők adott példánya részletes adatainak megtekintéséhez jelöljön ki egy bejegyzést a **Port** oszlopból az **Executors** (Végrehajtók) szakaszban.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    Ebben a példában a **seven** szó 1 493 957 alkalommal fordul elő. A szó előfordulásainak számlálása a topológia indításától kezdve történik.

## <a name="stop-the-topology"></a>A topológia leállítása

Lépjen vissza a **Topology summary** (Topológia összegzése) lapra a word-count topológiához, majd válassza a **Kill** (Törlés) gombot a **Topology actions** (Topológiaműveletek) szakaszban. Amikor a rendszer kéri, adjon meg 10 másodperces értéket a topológia leállítása előtti várakozási időként. Az időtúllépés lejáratát követően az adott topológia már nem jelenik meg az irányítópult **Storm felhasználói felülete** szakaszában.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a id="next"></a>Következő lépések

Ebből az Apache Storm-oktatóanyagból megismerhette a Storm HDInsightban való használatának alapjait. A következő szakaszban a [Java-alapú topológiák fejlesztését ismertetjük Maven használatával](hdinsight-storm-develop-java-topology.md).

Ha már ismeri a Java-alapú topológiák fejlesztését, és egy meglévő topológiát kíván üzembe helyezni a HDInsighton, tekintse meg a következő cikket: [HDInsighton futó Apache Storm-topológiák üzembe helyezése és kezelése](hdinsight-storm-deploy-monitor-topology-linux.md).

A.NET-fejlesztők C#- vagy hibrid C#/Java-topológiákat hozhatnak létre a Visual Studio használatával. További információk: [C#-topológiák fejlesztése HDInsight alatt futó Apache Stormra a Visual Studio Hadoop-eszközeinek használatával](hdinsight-storm-develop-csharp-visual-studio-topology.md).

A HDInsight alatt futó Stormmal használható példatopológiákat az alábbiakban talál:

* [HDInsight alatt futó Storm példatopológiái](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

