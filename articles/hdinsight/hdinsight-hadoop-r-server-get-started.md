---
title: "Az R Server használatának első lépései a HDInsightban | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre R Servert tartalmazó Apache Sparkot a HDInsight fürtön, majd hogyan küldhet el R-szkriptet a fürtön."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f816a6972c0e80c6a7063705917ecf18debc75f6
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-using-r-server-on-hdinsight"></a>R Server a HDInsightban – első lépések
A HDInsight olyan R Server beállítással rendelkezik, amely a HDInsight-fürtbe integrálható. Így az R-szkriptek a Spark és a MapReduce eszközökkel elosztott számításokat futtathatnak. A dokumentumban foglaltakat követve elsajátíthatja az R Server létrehozását a HDInsight-fürtökön, majd R-szkriptet futtathat, amely a Spark elosztott R számításokhoz való használatát mutatja be.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**: Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. További információ: [Ingyenes Azure-próbafiók beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Secure Shell- (SSH-) ügyfél**: Egy SSH-ügyféllel távolról csatlakozhat a HDInsight-fürthöz, és közvetlenül a fürtön futtathat parancsokat. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

  * **Secure Shell- (SSH-) kulcsok (nem kötelező)**: A fürthöz való csatlakozáshoz használt SSH-fiókjának biztonságát jelszóval vagy nyilvános kulccsal biztosíthatja. A jelszó használata könnyebb, és lehetővé teszi, hogy nyilvános/titkos kulcspár létrehozása nélkül tegye meg az első lépéseket. A kulcs használata azonban biztonságosabb.

      A dokumentum lépései azt feltételezik, hogy jelszót használ.

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-the-cluster"></a>A fürt létrehozása
> [!NOTE]
> A jelen dokumentumban lévő lépések végigvezetik azon, hogyan tud létrehozni R Servert egy HDInsight-fürtön az alapvető konfigurációs információkkal. Az egyéb fürtkonfigurációs beállításokról (például további tárfiókok hozzáadásáról, az Azure virtuális hálózat használatáról vagy a Hive metaadattárának létrehozásáról) lásd: [Linux-alapú HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az R Server Azure Resource Management-sablon használatával való létrehozásáról az [R-server HDInsight-fürt üzembe helyezésének leírásában](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) talál további információt.
>
>

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

2. Válassza az **ÚJ**, **Intelligence+ Analytics** elemet, majd a **HDInsight** lehetőséget.

    ![Új fürt létrehozásának képe](./media/hdinsight-getting-started-with-r/newcluster.png)

3. A **Gyorslétrehozás** felületen írja be a fürt nevét a **Fürt neve** mezőbe. Ha több Azure-előfizetése van, az **Előfizetés** bejegyzésben válassza ki a használni kívánt előfizetést.

    ![Fürt neve és az előfizetés kiválasztása](./media/hdinsight-getting-started-with-r/clustername.png)

4. Válassza ki a **Fürt típusát** a **Fürtkonfiguráció** panel megnyitásához. A **Fürtkonfiguráció** panelen válassza ki a következő beállításokat:

   * **Fürt típusa**: R Server
   * **Verzió**: válassza ki az R Server fürtre telepítendő verzióját. Válassza ki a legújabb verziót a legfrissebb lehetőségekhez. Szükség esetén más verziók is elérhetők a kompatibilitás érdekében. Az egyes elérhető verziók kibocsátási megjegyzései elérhetők [itt](https://msdn.microsoft.com/en-us/microsoft-r/notes/r-server-notes).
   * **R Studio community edition for R Server**: ez a böngésző alapú IDE alapértelmezés szerint telepítve van az élcsomópontra.  Ha inkább nem szeretné, hogy telepítve legyen, akkor törölje a jelölőnégyzet jelölését. Ha úgy dönt, hogy telepíti, a fürt portálalkalmazás panelén találja az R Studio kiszolgáló bejelentkezésének elérésére szolgáló URL-t, miután a fürt létrejött.

   Hagyja a többi beállítást az alapértelmezett értéken, és a **Kiválasztás** gombbal mentse a fürttípust.

   ![A Fürt típusa panel képernyőképe](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)

5. Írja be a **Fürt bejelentkezési felhasználónevét** és a **Fürt bejelentkezési jelszavát**.

   Adjon meg egy **SSH-felhasználónevet**.  Az SSH-val távolról csatlakozhat a fürthöz egy **Secure Shell- (SSH-)** ügyfél használatával. Megadhatja az SSH-felhasználót ezen a párbeszédpanelen vagy a fürt létrehozása után (a fürt Konfiguráció lapján). Az R Server úgy van konfigurálva, hogy a „remoteuser” **SSH-felhasználónevet** várja.  **Ha más felhasználónevet használ, egy további lépést kell elvégeznie a fürt létrehozása után.**

   Hagyja bejelölve a **Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzetet a **JELSZÓ** használatához hitelesítési típusként, ha nem szeretne nyilvános kulcsot használni.  Nyilvános/titkos kulcspárra van szüksége, ha a fürtön lévő R Servert egy távoli ügyfélen keresztül szeretné elérni, például RTVS-en, RStudión vagy másik asztali IDE-n keresztül. SSH-jelszót kell választania, ha az RStudio Server community edition kiadását telepíti.     

   Nyilvános/titkos kulcspár használatához törölje a **Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzet jelölését, majd válassza a **NYILVÁNOS KULCS** elemet, és folytassa a következőképpen.  Ezek az utasítások feltételezik, hogy a Cygwin with ssh-keygen vagy ennek megfelelő van telepítve.

   * Hozzon létre egy nyilvános/titkos kulcspárt a parancssorból a laptopján:

   `ssh-keygen -t rsa -b 2048`

   * Kövesse promptot a kulcsfájl elnevezéséhez, majd írjon be egy hozzáférési kódot a további biztonság érdekében. A képernyőjének a következőhöz hasonlóan kell kinéznie:

   ![SSH parancssor Windows rendszerben](./media/hdinsight-getting-started-with-r/sshcmdline.png)

   * Ez létrehoz egy titkos kulcsfájlt és egy nyilvános kulcsfájlt a <private-key-filename>.pub néven, például a furiosa és a furiosa.pub néven.

   ![SSH dir](./media/hdinsight-getting-started-with-r/dir.png)

   * Ezután adja meg a nyilvános kulcsfájlt (*.pub), amikor a HDI-fürt hitelesítő adatait rendeli hozzá, és végül erősítse meg az erőforráscsoportot és a régiót, és válassza a **Tovább** lehetőséget.

   ![Hitelesítő adatok panel](./media/hdinsight-getting-started-with-r/publickeyfile.png)  

   * Engedélyek módosítása a titkos kulcsfájlon a laptopján

   `chmod 600 <private-key-filename>`

   * A titkos kulcsfájl használata SSH-val a távoli bejelentkezéshez

   `ssh –i <private-key-filename> remoteuser@<hostname public ip>`

   vagy a Hadoop Spark számítási környezet definíciójának részeként az R Serverhez az ügyfélen (lásd: A Microsoft R Server használata Hadoop ügyfélként témakört a [Számítási környezet létrehozása a Sparkhoz](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) szakaszban, az online [A SacaleR on Apache Spark első lépései](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) dokumentumban.)

6. A gyorslétrehozás átirányítja a **Tárolás** panelre, ahol kiválaszthatja a fürt által használt HDFS-fájlrendszer elsődleges helyéhez használt tárfiók beállításait. Válasszon új vagy meglévő Azure Storage-fiókot vagy meglévő Data Lake tárfiókot.

   1. Ha Azure Storage-fiókot választ, kiválaszthat egy meglévő tárfiókot a **Tárfiók kiválasztása** elem bejelölésével, majd a fiók kiválasztásával. Vagy hozzon létre egy új fiókot az **Új létrehozása** hivatkozással a **Tárfiók kiválasztása** szakaszban.

      > [!NOTE]
      > Ha az **Új** lehetőséget választja, be kell írnia egy nevet az új tárfiókhoz. Egy zöld pipa jelenik meg, ha a rendszer elfogadja a nevet.

      Az **Alapértelmezett tároló** alapértéke a fürt neve lesz. Hagyja meg ezt az értéket.

      Ha új tárfiókot választott, megjelenik egy kérés a **Hely** kiválasztásához, ahol kiválaszthatja a tárfiók létrehozásának régióját.  

         ![Adatforrás panel](./media/hdinsight-getting-started-with-r/datastore.png)  

      > [!IMPORTANT]
      > Az alapértelmezett adatforrás helyének kiválasztása a HDInsight-fürt helyét is beállítja. A fürtnek és az alapértelmezett adatforrásnak ugyanabban a régióban kell lennie.

   2. Ha egy meglévő Data Lake Store használatát választja, akkor válassza ki a használni kívánt ADLS-tárfiókot, és adja a fürt ADD-identitását a fürthöz a tároló hozzáférésének engedélyezéséhez. Ezen folyamatról további információért tekintse át a [HDInsight-fürt létrehozása a Data Lake Store-ral az Azure Portalon](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) szakaszt.

   A **Kiválasztás** gombbal mentse az adatforrás konfigurációját.


7. Ezután megjelenik az **Összegzés** panel az összes beállítás érvényesítéséhez. Itt módosíthatja a **Fürt méretét**, hogy módosítsa a fürtben lévő kiszolgálók számát, és megadhat minden futtatni kívánt **szkriptműveletet**. Ha tudja, hogy nincs szüksége nagy fürtre, hagyja a munkavégző csomópontok számát az alapértelmezett `4` értéken. A panelen megjelenik a fürt becsült költsége.

   ![fürt összegzése](./media/hdinsight-getting-started-with-r/clustersummary.png)

   > [!NOTE]
   > Szükség esetén később átméretezheti a fürtöt a portálon keresztül (Fürt -> Beállítások -> Fürt méretezése) a munkavégző csomópontok számának növelése vagy csökkentése érdekében.  Ez hasznos lehet a fürt lelassításához, amikor nincs használatban, vagy kapacitás hozzáadásához a nagyobb feladatok igényeinek kielégítése érdekében.
   >
   >

    A fürt, az adatcsomópontok és az élcsomópont átméretezésekor néhány figyelembe veendő tényező a következő:  

   * A Spark rendszeren az elosztott R Server elemzések teljesítménye arányos a munkavégző csomópontok számával, amikor nagyok az adatok.  

   * Az R Server elemzések teljesítménye arányos az elemzett adatok méretével. Példa:  

     * Kis és közepes méretű adatok esetében a teljesítmény akkor a legnagyobb, amikor az élcsomóponton helyi számítási környezetben van elemezve.  Azokról az alkalmazási helyzetekről, ahol a helyi és a Spark számítási környezetek a legjobban működnek, további információért lásd: Számítási környezeti beállítások a HDInsighton belüli R Server esetében.<br>
     * Ha bejelentkezik az élcsomópontra, és futtatja az R-szkriptet, akkor a ScaleR rx-függvényeken kívül mindegyik <strong>helyileg</strong> fut az élcsomóponton, így a memóriát és az élcsomópont magjainak számát ennek megfelelően kell méretezni. Ugyanez érvényes, ha az R Server on HDI-t távoli számítási környezetként használja a laptopjáról.

     ![Csomópontok árképzési szintjei panel](./media/hdinsight-getting-started-with-r/pricingtier.png)

     A **Kiválasztás** gombbal mentse a csomópontok árképzésének konfigurációját.

   Észreveheti, hogy egy **Sablon és paraméterek letöltése** hivatkozás is megjelenik. Erre a hivatkozásra kattintva olyan szkriptek jelennek meg, amelyekkel automatizálható a fürtök létrehozása a kiválasztott konfigurációval. Ezek a szkriptek a fürt Azure Portal bejegyzéséről is elérhetők, miután az létrejött.

   > [!NOTE]
   > Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. A kezdőpulton vagy az **Értesítések** bejegyzésen az oldal bal oldalán lévő csempével ellenőrizheti a létrehozási folyamatot.
   >
   >

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

Ha úgy döntött, hogy belefoglalja az RStudio Server community edition kiadást a telepítésbe, akkor az RStudio bejelentkezési oldalt két módon érheti el.

1. Látogasson el a következő URL-re (ahol a **FÜRTNÉV** a létrehozott fürt neve):

    https://**FÜRTNÉV**.azurehdinsight.net/rstudio/

2. Vagy nyissa meg a fürt bejegyzését az Azure Portalon, válassza az R Server irányítópultok gyorshivatkozást, majd válassza az R Studio irányítópultot:

     ![Az R Studio irányítópult elérése](./media/hdinsight-getting-started-with-r/rstudiodashboard1.png)

     ![Az R Studio irányítópult elérése](./media/hdinsight-getting-started-with-r/rstudiodashboard2.png)

   > [!IMPORTANT]
   > A módszertől függetlenül, amikor először jelentkezik be, kétszer kell hitelesítenie.  Az első hitelesítéskor adja meg a fürt rendszergazdai felhasználói azonosítóját és jelszavát. A második adatkéréskor adja meg az SSH felhasználói azonosítót és jelszót. A későbbi bejelentkezések csak az SSH-jelszót és a felhasználói azonosítót kérik.

## <a name="connect-to-the-r-server-edge-node"></a>Csatlakozás az R Server élcsomóponthoz
Csatlakoztassa a HDInsight-fürt R Server élcsomópontját SSH használatával:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> A `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` címet az Azure Portalon is megtalálhatja, ha kiválasztja a fürtöt, majd az **Összes beállítás**, **Alkalmazások** és **RServer** elemekre kattint. Ez megjeleníti az élcsomópont SSH végpontinformációit.
>
> ![Az élcsomópont SSH végpontjának képe](./media/hdinsight-getting-started-with-r/sshendpoint.png)
>
>

Ha az SSH-felhasználói fiókhoz jelszót használt, a rendszer felkéri annak megadására. Nyilvános kulcs használatakor lehetséges, hogy az `-i` paraméter használatára van szükség a megfelelő titkos kulcs megadásához. Például: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`.

További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

Ha csatlakoztatva van, a következőhöz hasonló adatkérés érkezik.

`username@ed00-myrser:~$`

## <a name="use-the-r-console"></a>Az R-konzol használata

1. Az SSH-munkamenetből a következő paranccsal indíthatja el az R-konzolt.  

   ```
   R

   You will see output similar to the following.
   R version 3.2.2 (2015-08-14) -- "Fire Safety"
   Copyright (C) 2015 The R Foundation for Statistical Computing
   Platform: x86_64-pc-linux-gnu (64-bit)

   R is free software and comes with ABSOLUTELY NO WARRANTY.
   You are welcome to redistribute it under certain conditions.
   Type 'license()' or 'licence()' for distribution details.

   Natural language support but running in an English locale

   R is a collaborative project with many contributors.
   Type 'contributors()' for more information and
   'citation()' on how to cite R or R packages in publications.

   Type 'demo()' for some demos, 'help()' for on-line help, or
   'help.start()' for an HTML browser interface to help.
   Type 'q()' to quit R.

   Microsoft R Server version 8.0: an enhanced distribution of R
   Microsoft packages Copyright (C) 2016 Microsoft Corporation

   Type 'readme()' for release notes.
   >
   ```

2. A `>` adatkérésben beírhatja az R-kódot. Az R Server olyan csomagokat tartalmaz, amelyekkel könnyedén használhatja a Hadoopot és futtathat elosztott számításokat. A következő paranccsal tekintse meg például a HDInsight-fürt alapértelmezett fájlrendszerének gyökerét.

`rxHadoopListFiles("/")`

A WASB stíluscímzést is használhatja.

`rxHadoopListFiles("wasbs:///")`

## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Az R Server használata HDI-n a Microsoft R Server vagy a Microsoft R ügyfél egy távoli példányáról
A nyilvános/titkos kulcspároknak a fürt eléréséhez való használatával kapcsolatos fenti szakasz szerint beállíthatja a HDI Hadoop Spark számítási környezet hozzáférését egy asztali gépen vagy laptopon futó Microsoft R Server vagy Microsoft R ügyfél távoli példányáról (lásd A Microsoft R Server használata Hadoop ügyfélként témakört a [Számítási környezet létrehozása a Sparkhoz](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) szakaszban, az online [A SacaleR on Apache Spark első lépései](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started) dokumentumban.)  Ehhez meg kell adnia a következő beállításokat, amikor meghatározza az RxSpark számítási környezetet a laptopon: hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches és sshProfileScript. Példa:

```
    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )
```


## <a name="use-a-compute-context"></a>Számítási környezet használata
A számítási környezetekkel vezérelheti, hogy a számítás helyben történik-e az élcsomóponton, vagy elosztottan a HDInsight-fürtben lévő csomópontok között.

1. Az RStudio Serverről vagy az R-konzolról (SSH-munkamenetben) a következő használatával töltse be a példaadatokat a HDInsight alapértelmezett tárolójába.

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
        download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
        download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
        download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
        download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
        download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
        download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
        download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
        download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
        download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
        download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
        download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
        download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Ezután hozzon létre adatinformációkat, és határozzon meg két adatforrást, hogy használhassuk az adatokat.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Futtasson logisztikai regressziót az adatokon a helyi számítási környezettel.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Az alábbihoz hasonló sorokkal végződő kimenetnek kell megjelennie.

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Ezután futtassa ugyanezt a logisztikai regressziót a Spark környezettel. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > A MapReduce eszközzel is eloszthatja a számítást a fürtcsomópontok között. A számítási környezetről további információért lásd: [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](hdinsight-hadoop-r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>R-kód elosztása több csomópontra
Az R Serverrel könnyedén futtathatja a meglévő R-kódokat a fürt több csomópontján az `rxExec` használatával. Ez akkor hasznos, amikor paraméteres frissítést vagy szimulációkat végez. A következő az `rxExec` használatának példája.

`rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )`

Ha továbbra is a Spark vagy a MapReduce környezetet használja, ez visszaadja azon munkavégző csomópontok csomópontnév értékét, amelyen az `(Sys.info()["nodename"])` kódot futtatta. Egy négy csomópontból álló fürtön például a következőhöz hasonló kimenetet kaphat.


    ```
    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"
    ```

## <a name="accessing-data-in-hive-and-parquet"></a>Adatok elérése a Hive és a Parquet eszközökben
Az R Server 9.0 és újabb verziókban elérhető új funkció lehetővé teszi az adatok közvetlen elérését a Hive és a Parquet eszközökben a Spark számítási környezet ScaleR-függvényei általi használatra. Ezek a képességek az RxHiveData és RxParquetData nevű új ScaleR adatforrás-függvényeken keresztül érhetők el, amelyek a Spark SQL-en keresztül töltenek adatokat közvetlenül a Spark DataFrame-be a ScaleR által végzett elemzéshez.  

A következő tartalmazza az új függvények használatának néhány mintakódját:



    ```
    #..create a Spark compute context

    myHadoopCluster <- rxSparkConnect(reset = TRUE)
    ```


    ```
    #..retrieve some sample data from Hive and run a model

    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)
    ```

    ```
    #..retrieve some sample data from Parquet and run a model

    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)
    ```


    ```
    #..check on Spark data objects, cleanup, and close the Spark session

    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)
    ```

Ezen új függvények használatával kapcsolatos további információkat az R Server online súgójában találhat a ?RxHivedata és ?RxParquetData parancsok használatával.  


## <a name="install-r-packages"></a>R csomagok telepítése
Ha további R csomagokat szeretne telepíteni az élcsomóponton, az `install.packages()` parancsot használhatja közvetlenül az R-konzolról, amikor SSH-n keresztül csatlakozik az élcsomóponthoz. Ha azonban a fürt munkavégző csomópontjaira kell R csomagokat telepítenie, szkriptműveletet kell használnia.

A szkriptműveletek olyan Bash-szkriptek, amelyekkel konfigurációs módosítások végezhetők a HDInsight-fürtön, vagy további szoftverek telepíthetők. Ebben az esetben további R csomagokat telepít. Ha szkriptművelettel szeretne további csomagokat telepíteni, használja a következő lépéseket.

> [!IMPORTANT]
> A további R csomagok szkriptműveletekkel végzett telepítése csak a fürt létrehozása után használható. Nem használható a fürt létrehozása során, mivel a szkript az R Server teljes telepítésétől és konfigurálásától függ.
>
>

1. Az [Azure Portalon](https://portal.azure.com) válassza ki az R Servert a HDInsight-fürtön.

2. A **Beállítások** panelen válassza a **Szkriptműveletek** elemet, majd az **Új küldése** elemet egy új szkriptművelet elküldéséhez.

   ![A Szkriptműveletek panel képe](./media/hdinsight-getting-started-with-r/scriptaction.png)

3. A **Szkriptművelet küldése** panelen adja meg a következő információkat.

   * **Név**: A szkriptet azonosító egyszerű név

   * **Bash-szkript URI azonosítója**: `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **Átjáró**: Ez **ne legyen bejelölve**

   * **Feldolgozó**: Ez **legyen bejelölve**

   * **Élcsomópontok**: Ez **ne legyen bejelölve**.

   * **Zookeeper**: Ez **ne legyen bejelölve**

   * **Paraméterek**: A telepíteni kívánt R csomagok. Például: `bitops stringr arules`

   * **Ha megőrzi a...**: Ez **legyen bejelölve**  

   > [!NOTE]
   > 1. Alapértelmezés szerint az összes R csomag a telepített R Server verziójának megfelelő Microsoft MRAN tár-pillanatfelvételéből van telepítve.  Ha a csomagok újabb verzióit szeretné telepíteni, akkor kockáztatja az inkompatibilitást, de megadhatja a `useCRAN` elemet a csomaglista első elemeként, például: `useCRAN bitops, stringr, arules`.  
   > 2. Néhány R csomag további Linux rendszerű könyvtárakat igényel. A kényelem érdekében előre telepítettük a 100 legnépszerűbb R csomag szükséges függőségeit. Ha azonban a telepített R csomag(ok) további könyvtárakat telepítését igényli(k), akkor le kell töltenie az itt használt alapvető szkriptet, és lépéseket kell hozzáadnia a rendszerkönyvtárak telepítéséhez. Ezután fel kell töltenie a módosított szkriptet egy nyilvános blob-tárolóba az Azure Storage-ben, és a módosított szkripttel kell telepítenie a csomagokat.
   >    A szkriptműveletek fejlesztésével kapcsolatos további információért lásd: [Szkriptművelet fejlesztése](hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Szkriptműveletek hozzáadása](./media/hdinsight-getting-started-with-r/submitscriptaction.png)

4. Válassza a **Létrehozás** lehetőséget a szkript futtatásához. A szkript befejezése után az R csomagok elérhetők az összes munkavégző csomóponton.

## <a name="using-microsoft-r-server-operationalization"></a>A Microsoft R Server operacionalizálás használata
Amikor elkészült az adatmodellezés, működőképessé teheti a modellt, hogy előrejelzéseket végezzen. A Microsoft R Server működőképessé tételhez való konfigurálásához végezze el az alábbi lépéseket.

Először jelentkezzen be SSH-n keresztül az élcsomópontba. Például: ```ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net```.

Az SSH használata után módosítsa a könyvtárat a következő könyvtárra, és az alábbi sudo dotnet dll parancsot használja.

```
   cd /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil
   sudo dotnet Microsoft.DeployR.Utils.AdminUtil.dll
```

Ha a Microsoft R Server működőképessé tételt egy beépített konfigurációban szeretné konfigurálni, tegye a következőt:

* Válassza az „1. Configure R Server for Operationalization” (Az R Server konfigurálása a Működőképessé tétel művelethez) lehetőséget
* Válassza az „A. One-box (web + compute nodes)” (Beépített (web + számítási csomópontok)) elemet
* Írja be a **rendszergazdai** felhasználó jelszót

![one box op](./media/hdinsight-hadoop-r-server-get-started/admin-util-one-box-.png)

Választható lépésként diagnosztikai ellenőrzéseket végezhet az alább látható diagnosztikai tesztelés futtatásával.

* Válassza a „6. Run diagnostic tests” (Diagnosztikai tesztek futtatása) elemet
* Válassza az „A. Test configuration” (Tesztkonfiguráció) elemet
* Írja be a Username = „admin” parancsot, valamint a jelszót a fenti konfigurációból
* Erősítse meg az Overall Health = pass parancsot
* Lépjen ki az admin segédprogramból
* Lépjen ki az SSH-ból

![Diagnostic for op](./media/hdinsight-hadoop-r-server-get-started/admin-util-diagnostics.png)

Az operacionalizálás konfigurációja ezzel befejeződött. Most már használhatja az „mrsdeploy” csomagot RClientben, hogy kapcsolódhasson az élcsomóponti operacionalizáláshoz, és elkezdhet alkalmazni olyan szolgáltatásokat, mint a [távoli végrehajtás](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) és [webszolgáltatás](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Attól függően, hogy fürt virtuális hálózaton van-e beállítva, szükség lehet porttovábbító bújtatás kialakítására SSH-bejelentkezésen keresztül. Kövesse az alábbi utasításokat:

### <a name="rserver-cluster-on-virtual-network"></a>Az RServer fürt virtuális hálózaton van

Bizonyosodjon meg róla, hogy engedélyezett a forgalom az 12800-as porton az élcsomópont felé. Így az élcsomópont használatával kapcsolódhat az operacionalizálási szolgáltatáshoz.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

Ha a remoteLogin() metódus nem tud kapcsolódni az élcsomóponthoz, de SSH-n be tud jelentkezni az élcsomópontba, győződjön meg róla, hogy a szabály, amely engedélyezi a forgalmat az 12800-as porton, megfelelően van-e beállítva. Ha a probléma továbbra is jelentkezik, egy másik megoldás segítségével is beállíthat porttovábbító alagutat az SSH-n keresztül.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Az RServer fürt nem virtuális hálózaton van beállítva

Ha a fürt nem a virtuális hálózaton van beállítva VAGY problémás a kapcsolódás a virtuális hálózaton keresztül, az alábbiak szerint használhatja az SSH porttovábbító alagutat:

```
ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net
```

A Putty szoftveren is beállítható.

![putty ssh kapcsolat](./media/hdinsight-hadoop-r-server-get-started/putty.png)

Ha az SSH-munkamenet aktív, a rendszer számítógépe 12800-as portjáról az élcsomópont 12800-as portjára továbbítja a forgalmat az SSH-munkameneten keresztül. Győződjön meg róla, hogy a `127.0.0.1:12800` cím szerepel remoteLogin() metódusban. Ezzel a porttovábbításon keresztül jelentkezik be az élcsomóponti operacionalizálásra.

```
library(mrsdeploy)

remoteLogin(
    deployr_endpoint = "http://127.0.0.1:12800",
    username = "admin",
    password = "xxxxxxx"
)
```

## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Hogyan méretezhetők a Microsoft R Server operacionalizálási számítási csomópontjai a HDInsight feldolgozó csomópontjain?


### <a name="decommission-the-worker-nodes"></a>A feldolgozó csomópont(ok) leszerelése
A Microsoft R Servert jelenleg nem a Yarnon keresztül kezeli a rendszer. Ha a feldolgozó csomópontokat nem szereli le, a Yarn Resource Manager nem a várakozásoknak megfelelően fog működni, mert nem fogja látni a kiszolgáló által felhasznált erőforrásokat. Ennek elkerülése érdekében javasoljuk, hogy szerelje le a feldolgozó csomópontokat ott, ahol méretezni szeretné a számítási csomópontokat.

A feldolgozó csomópontok leszerelésének lépései:

* Jelentkezzen be a HDI-fürt Ambari konzoljába, és kattintson a „Hosts” (Gazdagépek) lapra.
* Jelölje ki a leszerelendő feldolgozó csomópontokat, és kattintson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „Hosts” (Gazdagépek) panelen a „Turn ON Maintenance Mode” (Karbantartási mód bekapcsolása) elemre. Az alábbi képen például a wn3 és a wn4 pontokat választottuk ki leszerelésre.  

   ![feldolgozó csomópont(ok) leszerelése](./media/hdinsight-hadoop-r-server-get-started/get-started-operationalization.png)  

* Mutasson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „DataNodes” (Adatcsomópontok) menüpontra, és kattintson a „Decommission” (Leszerelés) elemre.
* Mutasson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „NodeManagers” (Csomópontkezelők) menüpontra, és kattintson a „Decommission” (Leszerelés) elemre.
* Mutasson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „DataNodes” (Adatcsomópontok) menüpontra, és kattintson a „Stop” (Leállítás) elemre.
* Mutasson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „NodeManagers” (Csomópontkezelők) menüpontra, és kattintson a „Stop” (Leállítás) elemre.
* Mutasson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „Hosts” (Gazdagépek) menüpontra, és kattintson a „Stop All Components” (Az összes összetevő leállítása) elemre.
* Szüntesse meg a feldolgozó csomópontok kijelölését, és jelölje ki az átjárócsomópontokat.
* Mutasson az „Actions” (Műveletek) > „Selected Hosts” (Kiválasztott gazdagépek) > „Hosts” (Gazdagépek) menüpontra, és kattintson a „Restart All Components” (Az összes összetevő újraindítása) elemre.


###    <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Számítási csomópontok konfigurálása az összes leszerelt feldolgozó csomóponton

* Jelentkezzen be SSH-n keresztül minden egyes leszerelt feldolgozó csomópontba
* Futtassa az admin segédprogramot a következő paranccsal: `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`
* Írja be az „1” karaktert az „1. Configure R Server for Operationalization” (Az R Server konfigurálása a Működőképessé tétel művelethez) lehetőség kijelöléséhez.
* Írja be a „c” karaktert a „C. Compute node” (Számítási csomópont) lehetőség kijelöléséhez. Ez konfigurálja a számítási csomópontot a feldolgozó csomóponton.
* Lépjen ki az admin segédprogramból

### <a name="add-compute-nodes-details-on-web-node"></a>Számítási csomópontok részleteinek megadása a Web csomóponton
Ha minden leszerelt feldolgozó csomópontot konfigurált a számítási csomópont futtatására, térjen vissza az élcsomóponthoz, és adja hozzá a leszerelt feldolgozó csomópontok IP címét a Microsoft R Server webcsomópontjának konfigurációjában:

* Jelentkezzen be SSH-n keresztül az élcsomópontba
* Futtassa a `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json` parancsot.
* Keresse meg az „URIs” szakaszt, és adja hozzá a feldolgozó csomópontok IP-címét és portrészleteit.

![feldolgozó csomópont(ok) leszerelési parancssora](./media/hdinsight-hadoop-r-server-get-started/get-started-op-cmd.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy már ismeri az új, R Servert magában foglaló HDInsight fürtök létrehozási módját és érti az SSH-munkamenet R konzoljának alapvető használatát, az alábbiak segítségével fedezzen fel más módokat is az R Server HDInsighton belüli alkalmazására.

* [RStudio Server hozzáadása a HDInsight szolgáltatáshoz (ha a fürt létrehozása közben nem telepítette)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Számítási környezeti beállítások a HDInsighton belüli R Server esetében](hdinsight-hadoop-r-server-compute-contexts.md)
* [Azure Storage lehetőségek a HDInsighton belüli R Server esetében](hdinsight-hadoop-r-server-storage.md)

