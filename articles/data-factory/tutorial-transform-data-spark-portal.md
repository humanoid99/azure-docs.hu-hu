---
title: "Adatok átalakítása a Spark segítségével az Azure Data Factory-ban | Microsoft Docs"
description: "Ez az oktatóanyag lépésenkénti utasításokat biztosít az adatok átalakításához egy Spark-tevékenység az Azure Data Factoryban való használatával."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: e3b8fe37d573f537e6524438acc385220053d681
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Adatátalakítás a felhőben egy Spark-tevékenység az Azure Data Factoryban való használatával
Ebben az oktatóanyagban egy Azure Data Factory-folyamatot hoz létre az Azure Portal használatával. Ez a folyamat egy Spark-tevékenységgel és egy igény szerinti Azure HDInsight társított szolgáltatással alakítja át az adatokat. 

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Hozzon létre egy Spark-tevékenységet használó folyamatot.
> * Folyamat futtatásának aktiválása
> * A folyamat futásának monitorozása.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, tekintse meg [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
* **Egy Azure Storage-fiók**. Létrehoz egy Python-szkriptet és egy bemeneti fájlt, és feltölti őket az Azure Storage-be. A Spark-program kimenetét ebben a tárfiókban tárolja a rendszer. Az igény szerinti Spark-fürt ugyanezt a tárfiókot használja elsődleges tárterületként.  
* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat.


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Python-szkript feltöltése a Blob Storage-fiókba
1. Hozzon létre egy **WordCount_Spark.py** nevű Python-fájlt az alábbi tartalommal: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Cserélje a *&lt;storageAccountName&gt;* kifejezést az Azure Storage-fiókja nevére. Ezután mentse a fájlt. 
3. Az Azure Blob Storage-ban hozzon létre egy **adftutorial** nevű tárolót, ha még nem létezik. 
4. Hozzon létre egy **spark** mappát.
5. Hozzon létre egy **script** almappát a **spark** mappában. 
6. Töltse fel a **WordCount_Spark.py** fájlt a **szkript** almappába. 


### <a name="upload-the-input-file"></a>A bemeneti fájl feltöltése
1. Hozzon létre egy **minecraftstory.txt** nevű fájlt némi szöveges tartalommal. A Spark-program megszámolja a szavak számát ebben a szövegben. 
2. Hozzon létre egy **inputfiles** nevű almappát a **spark** mappában. 
3. Töltse fel a **minecraftstory.txt** fájlt az **inputfiles** almappába. 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson az **Új** elemre a bal oldali menüben, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
   ![„Új adat-előállító” lap](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a következő hibát látja, módosítsa az adat-előállító nevét. (Például: **&lt;sajátneve&gt;ADFTutorialDataFactory**). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet, ha egy név nem érhető el](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
   A rövid útmutató egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
5. **Verzió:** válassza a **V2 (előzetes verzió)** értéket.
6. **Hely:** válassza ki az adat-előállító helyét. 

   A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. A Data Factory által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
7. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
8. Kattintson a **Létrehozás** gombra.
9. Az irányítópulton megjelenő csempén az **Adat-előállító üzembe helyezése** állapotleírás látható: 

   ![„Adat-előállító üzembe helyezése” csempe](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
10. A létrehozás befejezése után megjelenik az **Adat-előállító** lap. A Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.

    ![Az adat-előállító kezdőlapja a „Létrehozás és monitorozás” csempével](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a szakaszban két társított szolgáltatást hoz létre: 
    
- Egy **Azure Storage-beli társított szolgáltatást**, amely egy Azure Storage-fiókot társít az adat-előállítóhoz. Ezt a tárterületet csak az igény szerinti HDInsight-fürt használja. Ez tartalmazza a futtatni kívánt Spark-szkriptet is. 
- Egy **igény szerinti HDInsight társított szolgáltatást**. Az Azure Data Factory automatikusan létrehoz egy HDInsight-fürtöt, és futtatja a Spark programot. Ezt követően pedig törli a HDInsight-fürtöt, miután a fürt egy előre meghatározott ideig tétlen volt. 

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. Az **Első lépések** lapon váltson a **Szerkesztés** lapra a bal oldali ablaktáblán. 

   ![„Első lépések” lap](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Kattintson az ablak alján látható **Kapcsolatok**, majd az **+ Új** elemre. 

   ![Új kapcsolat létrehozására szolgáló gombok](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. Az **Új társított szolgáltatás** ablakban válassza az **Adattár** > **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** elemre. 

   ![Az „Azure Blob Storage” csempe kiválasztása](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. A **Storage-fiók neve** mezőben válassza ki a nevet a listából, majd kattintson a **Mentés** gombra. 

   ![A tárfiók nevének megadására szolgáló mező](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Igény szerinti HDInsight társított szolgáltatás létrehozása

1. Kattintson ismét az **+ Új** gombra egy további társított szolgáltatás létrehozásához. 
2. Az **Új társított szolgáltatás** ablakban válassza a **Compute** > **Azure HDInsight** lehetőséget, majd kattintson a **Folytatás** gombra. 

   ![Az „Azure HDInsight” csempe kiválasztása](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

   a. A **Név** mezőben adja meg a következőt: **AzureHDInsightLinkedService**.
   
   b. Győződjön meg arról, hogy az **On-demand HDInsight** (Igény szerinti HDInsight) van kiválasztva a **Típus** elemnél.
   
   c. Az **Azure Storage társított szolgáltatás** mezőben válassza az **AzureStorage1** lehetőséget. Ezt a társított szolgáltatást korábban hozta létre. Ha másik nevet használt, adja meg a megfelelő nevet. 
   
   d. Válasza a **spark** **fürttípust**.
   
   e. Adja meg annak az **egyszerű szolgáltatásnak az azonosítóját**, amely rendelkezik a HDInsight-fürt létrehozásához szükséges engedéllyel. 
   
      A szolgáltatásnévnek az előfizetés vagy a létrejövő fürtnek helyet adó erőforráscsoport Közreműködő szerepkörének tagjának kell lennie. További információk: [Egy Azure Active Directory-alkalmazás és egyszerű szolgáltatás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md).
   
   f. A **Szolgáltatásnév kulcsa** mezőben adja meg a kulcsot. 
   
   g. Az **Erőforráscsoport** beállításnál válassza ki az adat-előállító létrehozásához használt erőforráscsoportot. Ekkor a Spark-fürt létrejön ebben az erőforráscsoportban. 
   
   h. Bontsa ki az **OS type** (Operációs rendszer típusa) elemet.
   
   i. Adjon egy nevet a fürt felhasználójának. 
   
   j. Adja meg a felhasználónévhez tartozó jelszót. 
   
   k. Kattintson a **Mentés** gombra. 

   ![HDInsight társított szolgáltatás beállításai](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Az Azure HDInsightban korlátozott azon magok száma, amelyek az egyes támogatott Azure-régiókban felhasználhatók. Igény szerinti HDInsight társított szolgáltatás esetében a HDInsight-fürt ugyanazon a helyen jön létre, mint amit az Azure Storage elsődleges tárterületként használ. Ellenőrizze, hogy a magkvótája elegendő-e a fürt sikeres létrehozásához. További információk: [Fürtök beállítása a HDInsightban Hadoop, Spark, Kafka stb. használatával](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Folyamat létrehozása

1. Kattintson a **+** (plusz) gombra, majd a menüben válassza a **Folyamat** elemet.

   ![Új folyamat létrehozására szolgáló gombok](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
2. A **Tevékenységek** eszközkészletben bontsa ki a **HDInsight** elemet. Húzza a **Spark** tevékenységet a **Tevékenységek** eszközkészletből a folyamat tervezőfelületére. 

   ![A Spark-tevékenység áthúzása](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
3. A **Spark**-tevékenység tulajdonságok ablakában végezze el az alábbi lépéseket: 

   a. Váltson a **HDI-fürt** lapra.
   
   b. Válassza ki az előző lépésben létrehozott **AzureHDInsightLinkedService** elemet. 
        
   ![Az HDInsight kapcsolódó szolgáltatás megadása](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
4. Váltson a **Szkript/Jar** lapra, és végezze el az alábbi lépéseket: 

   a. A **Feladathoz társított szolgáltatás** mezőben válassza az **AzureStorage1** lehetőséget.
   
   b. Kattintson a **Tallózás a tárolóban** lehetőségre.

   ![A Spark-szkript megadása a „Szkript/Jar” lapon](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Keresse meg az **adftutorial/spark/script mappát**, válassza ki a **WordCount_Spark.py** fájlt, majd kattintson a **Befejezés** gombra.      

5. A folyamat érvényesítéséhez kattintson az **Érvényesítés** gombra az eszköztáron. Az érvényesítési ablak bezárásához kattintson a **>>** (jobbra mutató nyíl) gombra. 
    
   ![„Érvényesítés” gomb](./media/tutorial-transform-data-spark-portal/validate-button.png)
6. Kattintson **Az összes közzététele** gombra. A Data Factory felhasználói felülete entitásokat (társított szolgáltatásokat és folyamatot) tesz közzé az Azure Data Factory szolgáltatásban. 
    
   ![„Az összes közzététele” gomb](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
Kattintson az **Aktiválás** gombra az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. 

![Az „Aktiválás” és az „Aktiválás most” gomb](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a **Monitorozás** lapra. Ellenőrizze, hogy megjelenik-e a futó folyamat. Egy Spark-fürt létrehozása nagyjából 20 percet vesz igénybe. 
   
2. Rendszeres időközönként kattintson a **Frissítés** gombra a folyamat futási állapotának ellenőrzéséhez. 

   ![Folyamatok monitorozására szolgáló lap, „Frissítés” gombbal](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

3. A folyamat futásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **Tevékenységfuttatások megtekintése** elemre a **Műveletek** oszlopban.

   ![Folyamat futási állapota](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   A fenti **Folyamatok** elemre kattintva visszaválthat a folyamatfuttatások nézetre.

   ![„Tevékenységfuttatások” nézet](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Kimenet ellenőrzése
Ellenőrizze, hogy a kimeneti fájl a spark/outputfiles/wordcount mappában jött-e létre. 

![A kimeneti fájl helye](./media/tutorial-transform-data-spark-portal/verity-output.png)

A fájlban a bemeneti szövegfájl összes szavának szerepelnie kell, valamint annak is, hogy az adott szó hányszor szerepel a fájlban. Például: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>További lépések
A mintában szereplő folyamat egy Spark-tevékenységgel és egy igény szerinti HDInsight társított szolgáltatással alakítja át az adatokat. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Hozzon létre egy Spark-tevékenységet használó folyamatot.
> * Folyamat futtatásának aktiválása
> * A folyamat futásának monitorozása.

A következő oktatóanyagra lépve megtudhatja, hogyan alakíthat át adatokat egy Hive-szkript a virtuális hálózatban lévő Azure HDInsight-fürtön való futtatásával. 

> [!div class="nextstepaction"]
> [Oktatóanyag: adatok átalakítása a Hive használatával egy Azure virtuális hálózatban](tutorial-transform-data-hive-virtual-network-portal.md).





