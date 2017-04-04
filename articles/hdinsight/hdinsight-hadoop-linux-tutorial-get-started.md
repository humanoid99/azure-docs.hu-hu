---
title: "Hadoop oktatóanyag: Ismerkedés a Hadoop és a Hive használatával a HDInsightban | Microsoft Docs"
description: "Ez az oktatóanyag segítséget nyújt a Hadoop használatának megkezdésében a HDInsight rendszerben. Megtudhatja, hogyan hozhat létre Linux-fürtöket, és hogyan kérdezhet le adatokat a Hive segítségével."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 24bb7e2ffd212594f50bab735e86d74ab95d8c62
ms.lasthandoff: 03/28/2017


---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Hadoop oktatóanyag: A Hadoop első lépései a HDInsightban

Megtanulhatja, hogyan hozhat létre [Hadoop](http://hadoop.apache.org/)-fürtöket, és hogyan futtathat Hive-feladatokat a HDInsightban. Az [Apache Hive](https://hive.apache.org/) a Hadoop ökoszisztémájának legnépszerűbb összetevője. A HDInsight jelenleg hat különböző fürttípussal érhető el: [Hadoop](hdinsight-hadoop-introduction.md), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md), [Storm](hdinsight-storm-overview.md), [Interaktív Hive (előzetes verzió)](hdinsight-hadoop-use-interactive-hive.md) és [R Server](hdinsight-hadoop-r-server-overview.md).  Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot mind a hat fürttípus támogatja. A HDInsightban támogatott összetevők listáját lásd: [What's new in the Hadoop cluster versions provided by HDInsight?](hdinsight-component-versioning.md) (A HDInsight által biztosított Hadoop-fürtverziók újdonságai).  

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elindításának feltétele:

* **Azure-előfizetés**: egy ingyenes egy hónapos próbafiók létrehozásához keresse fel az [azure.microsoft.com/free](https://azure.microsoft.com/free) webhelyet.

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-cluster"></a>Fürt létrehozása

A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban egy [Azure Resource Manager-sablonnal](../azure-resource-manager/resource-group-template-deploy.md). A Resource Manager-sablon teljes mértékben testreszabható, így könnyen létrehozhatók vele olyan Azure-erőforrások, mint például a HDInsight. Nem kell a Resource Manager-sablonok használatára vonatkozó tapasztalattal rendelkeznie az oktatóanyag követéséhez. Egyéb fürtlétrehozási módszerekhez és az oktatóanyagban használt tulajdonságok megértéséhez tekintse meg a [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight-fürtök létrehozása) című témakört. Az oldal tetején található választóval adja meg a fürtlétrehozási beállításokat.

Az oktatóanyagban használt Resource Manager-sablon a [GitHubon](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) található. 

1. Az alábbi képre kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablont az Azure Portalon. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Adja meg vagy válassza ki a következő értékeket:
   
    ![HDInsight Linux első lépések – Resource Manager-sablon a portálon](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).
   
    * **Előfizetés**: Válassza ki az Azure-előfizetést.
    * **Erőforráscsoport**: Hozzon létre új erőforráscsoportot, vagy válasszon ki egy meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. 
    * **Hely**: Válassza ki, melyik Azure-helyen kívánja létrehozni a fürtöt.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. 
    * **Fürt típusa**: Válassza a **hadoop** lehetőséget ehhez az oktatóanyaghoz.
    * **Fürt neve**: Adja meg a Hadoop-fürt nevét.
    * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
    * **SSH-felhasználónév és jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni. 
     
    Egyes tulajdonságok szoftveresen kötöttek a sablonban.  Ezeket az értéteket a sablonból konfigurálhatja.

    * **Hely**: A fürt és a függő tárfiók helye megegyezik az erőforráscsoport helyével.
    * **Fürt verziója**: 3.5
    * **Operációs rendszer típusa**: Linux
    * **Munkavégző csomópontok száma**: 2

     Minden egyes fürt az Azure Storage-fióktól függ. Általában ez az alapértelmezett tárfiók. A HDInsight-fürtnek és az alapértelmezett tárfióknak ugyanabban az Azure-régióban kell lennie. A fürtök törlésével a tárfiók nem törlődik. 
     
     További magyarázat ezekről a tulajdonságokról: [Hadoop-fürtök létrehozása a HDInsightban](hdinsight-hadoop-provision-linux-clusters.md).

3. Válassza az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Vásárlás** elemre. A portál irányítópultján egy új csempe jelenik meg **Sablon üzemelő példányának üzembe helyezése** címmel. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. A fürt létrejötte után a csempe felirata átvált az Ön által megadott erőforráscsoport-névre. A portál pedig automatikusan megnyitja az erőforráscsoportot egy új panelen. A panelen a fürt és az alapértelmezett tároló is megjelenik.
   
    ![A HDInsight Linux első lépései – erőforráscsoport](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png).

4. A fürt nevére kattintva a fürt megnyílik egy új panelen.

   ![HDInsight Linux első lépései – fürtbeállítások](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png)


## <a name="run-hive-queries"></a>Hive-lekérdezések futtatása
Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben az oktatóanyagban a portál Ambari Hive nézete segítségével futtat Hive-feladatokat. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

1. Az előző képernyőkép szerint kattintson a **Fürt irányítópultja**, majd a **HDInsight-fürt irányítópultja** elemre.  Keresse fel a **https://&lt;FürtNeve>.azurehdinsight.net** webhelyet, ahol a &lt;FürtNeve> az a fürt, amelyet az előző szakaszban az Ambari megnyitásához hozott létre.
2. Adja meg az előző szakaszban létrehozott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.
3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:
   
    ![Az Ambari nézetek kiválasztása](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. A lap **Query Editor** (Lekérdezésszerkesztő) szakaszában másolja be a következő HiveQL-kifejezést a munkalapra:
   
        SHOW TABLES;
   
   > [!NOTE]
   > A pontosvessző megadása a Hive-nál kötelező.       
   > 
   > 
5. Kattintson az **Execute** (Végrehajtás) parancsra. A lekérdezésszerkesztő alatt egy **Query Process Results** (Lekérdezési folyamat eredményei) című szakasznak kell megjelennie, amelyben a feladat információi láthatók. 
   
    A lekérdezés befejeztével a **Query Process Results** (Lekérdezési folyamat eredményei) szakaszban a művelet eredményei jelennek meg. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.
   
    ![HDInsight Hive-nézetek](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).
6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:
   
        SELECT * FROM hivesampletable;
   
   > [!TIP]
   > Tekintse meg az **Save results** (Eredmények mentése) legördülő menüt a **Query Process Results** (Lekérdezési folyamat eredményei) szakaszban, melynek segítségével az eredményeket letöltheti vagy CSV-fájlként mentheti a HDInsight-tárolóba.
   > 
   > 
7. A **History** (Előzmények) lehetőségre kattintva megkapja a feladatok listáját.

Egy Hive-feladat befejezése után [exportálhatja az eredményeket az Azure SQL Database adatbázisba vagy az SQL Server-adatbázisba](hdinsight-use-sqoop-mac-linux.md), valamint az [Excel segítségével meg is jelenítheti az eredményeket](hdinsight-connect-excel-power-query.md). A Hive HDInsight rendszeren belüli használatával kapcsolatos további információkért lásd: [Use Hive and HiveQL with Hadoop in HDInsight to analyze a sample Apache log4j file](hdinsight-use-hive.md) (A Hive és a HiveQL együttes használata a HDInsight Hadoop eszközével egy Apache log4j mintafájl elemzéséhez).

## <a name="clean-up-the-tutorial"></a>Az oktatóanyag tartalmának törlése
Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. 

> [!NOTE]
> Az [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) segítségével igény szerint hozhat létre HDInsight-fürtöket, és konfigurálhat egy TimeToLive-beállítást a fürtök automatikus törléséhez. 
> 
> 

**A fürt és/vagy az alapértelmezett tárfiók törlése**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A portál irányítópultján kattintson arra a csempére, amelynek a neve megegyezik a fürt létrehozásakor használt erőforráscsoport-névvel.
3. Kattintson az erőforrás panel **Törlés** gombjára a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez, vagy kattintson a fürt nevére az **Erőforrások** csempén, majd a **Törlés** gombra a fürt paneljén. Vegye figyelembe, hogy az erőforráscsoport törlése esetén a tárfiók is törlődik. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóprogramban megtanulhatta, hogyan hozhat létre Linux-alapú HDInsight-fürtöt egy Resource Manager-sablonnal, és hogyan hajthat végre alapszintű Hive-lekérdezéseket.

A HDInsighttal végzett adatelemzéssel kapcsolatos további információkért tekintse meg a következőket:

* További információ a Hive és a HDInsight együttes használatáról, például a Hive-lekérdezések Visual Studióból történő végrehajtásáról: [A Hive használata a HDInsighttal][hdinsight-use-hive].
* További információ az adatok átalakítására szolgáló Pig nyelvről: [A Pig használata a HDInsighttal][hdinsight-use-pig].
* További információ a Hadoopon adatokat feldolgozó programok írására szolgáló MapReduce módszerről: [A MapReduce használata a HDInsighttal][hdinsight-use-mapreduce].
* A HDInsight-adatok elemzésére szolgáló HDInsight Tools for Visual Studio szolgáltatással kapcsolatos további információkért lásd: [Get started using Visual Studio Hadoop tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) (A HDInsight Visual Studio Hadoop-eszközeinek használatára vonatkozó első lépések).

Ha készen áll dolgozni a saját adataival, és szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következőket:

* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba][hdinsight-upload-data].

A HDInsight-fürtök létréhozásával vagy kezelésével kapcsolatos további információkért lásd:

* Információk a Linux-alapú HDInsight-fürtök kezeléséhez: [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével).
* További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).
* Ha már ismeri a Linux és a Hadoop használatát, de szeretne konkrét részleteket megtudni a HDInsight-alapú Hadoopról, akkor tekintse meg a következő témakört: [Working with HDInsight on Linux](hdinsight-hadoop-linux-information.md) (A HDInsight használata Linux rendszeren). Ez többek között a következő információkat biztosítja:
  
  * a fürtön tárolt szolgáltatások, például az Ambari és a WebHCat URL-címét;
  * a Hadoop-fájlok és- példák helyét a helyi fájlrendszerben;
  * az Azure Storage (WASB) alapértelmezett adattárként történő használatát a HDFS helyett.

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md



