---
title: "Az Apache Kafka használatának első lépései a HDInsightban | Microsoft Docs"
description: "Sajátítsa el a Kafka létrehozásának és használatának alapjait a HDInsightban."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1c7a95354358d91859ce9d2019cef93b6662811f
ms.lasthandoff: 03/25/2017

---
# <a name="get-started-with-apache-kafka-preview-on-hdinsight"></a>Az Apache Kafka (előzetes verzió) használatának első lépései a HDInsightban

Az [Apache Kafka](https://kafka.apache.org) egy, a HDInsighttal együtt elérhető, nyílt forráskódú elosztott adatstreamelési platform. Sokszor használják üzenetközvetítőként, mivel a közzétételi-feliratkozási üzenetsorokhoz hasonló funkcionalitást kínál. A dokumentumban foglaltakat követve elsajátíthatja a Kafka létrehozását a HDInsight-fürtökön, valamint az adatok küldését és fogadását Java-alkalmazásokból.

> [!NOTE]
> Jelenleg a Kafka két verziója érhető el a HDInsighttal: a 0.9.0 (HDInsight 3.4) és a 0.10.0 (HDInsight 3.5). A dokumentum lépései azt feltételezik, hogy a Kafkát a HDInsight 3.5-ös verzióján használja.

## <a name="prerequisite"></a>Előfeltétel

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Ennek az Apache Kafka-oktatóanyagnak a sikeres befejezéséhez az alábbiakra van szükség:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **SSH- és SCP-ismeretek**. További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) vagy azzal egyenértékű, például az OpenJDK.

* [Apache Maven](http://maven.apache.org/) 

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények

[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-a-kafka-cluster"></a>Kafka-fürt létrehozása

Egy Kafka HDInsight-fürtön történő létrehozásához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **+ ÚJ**, **Intelligencia és analitika**, majd a **HDInsight** elemet.
   
    ![HDInsight-fürt létrehozása](./media/hdinsight-apache-kafka-get-started/create-hdinsight.png)

2. Az **Alapvető beállítások** panelen adja meg a következőket:

    * **Fürt neve**: A HDInsight-fürt neve.
    * **Előfizetés**: Válassza ki a használni kívánt előfizetést.
    * **Fürt bejelentkezési felhasználóneve** és **Fürt bejelentkezési jelszava**: A fürt HTTPS-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Ezekkel a hitelesítő adatokkal érheti el az olyan szolgáltatásokat, mint az Ambari webes felület vagy a REST API.
    * **SSH-felhasználónév**: A fürt SSH-kapcsolaton keresztüli elérésekor használt bejelentkezési adatok. Alapértelmezés szerint a jelszó megegyezik a fürt bejelentkezési jelszavával.
    * **Erőforráscsoport**: Az az erőforráscsoport, amelyben a fürt létre lesz hozva.
    * **Hely**: Az az Azure-régió, amelyben a fürt létre lesz hozva.
   
    ![Előfizetés kiválasztása](./media/hdinsight-apache-kafka-get-started/hdinsight-basic-configuration.png)

3. Válassza ki a **Fürt típusát**, majd állítsa be a következő értékeket a **Fürtkonfiguráció** panelen:
   
    * **Fürt típusa**: Kafka

    * **Verzió**: Kafka 0.10.0 (HDI 3.5)

    * **Fürt szintje**: Standard
     
    Végül mentse a beállításokat a **Kiválasztás** gomb használatával.
     
    ![Fürttípus kiválasztása](./media/hdinsight-apache-kafka-get-started/set-hdinsight-cluster-type.png)

    > [!NOTE]
    > Ha az Azure-előfizetéséről a Kafka előzetes verziója nem érhető el, megjelenik egy útmutató, amely elmagyarázza, hogyan szerezheti be a hozzáférést. A megjelenő utasítások a következő képen láthatókhoz hasonlók lesznek:
    >
    > ![előzetes verzió üzenete: ha szeretne üzembe helyezni egy felügyelt Apache Kafka-fürtöt a HDInsightban, kérjük, küldjön egy e-mailt, amelyben hozzáférést igényel az előzetes verzióhoz.](./media/hdinsight-apache-kafka-get-started/no-kafka-preview.png)

4. A fürt típusának kijelölése után erősítse meg a beállítást a __Kiválasztás__ gombbal. Ezután kattintson a __Tovább__ gombra az alapszintű konfiguráció befejezéséhez.

5. A **Tárolás** panelen válasszon ki vagy hozzon létre egy Storage-fiókot. A jelen dokumentumban leírt lépések esetében a panel többi mezőjét hagyja az alapértelmezett értékeken. Kattintson a __Tovább__ gombra a tárolókonfiguráció mentéséhez.

    ![A tárfiók HDInsight-beállításainak konfigurálása](./media/hdinsight-apache-kafka-get-started/set-hdinsight-storage-account.png)

6. Az **Összegzés** lapon tekintse át a fürt konfigurációját. A __Szerkesztés__ hivatkozásai használatával módosítsa a hibás beállításokat. Végül kattintson a__Létrehozás__ gombra a fürt létrehozásához.
   
    ![A fürtkonfiguráció összegzése](./media/hdinsight-apache-kafka-get-started/hdinsight-configuration-summary.png)
   
    > [!NOTE]
    > A fürt létrehozása 20 percig is eltarthat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Az ügyfélről SSH használatával csatlakozhat a fürthöz. Linux, Unix, MacOS vagy Bash on Windows 10 használata esetén alkalmazza a következő parancsot:

```ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net```

Cserélje le az **SSHUSER** elemet a fürt létrehozásakor megadott SSH-felhasználónévre. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.

Ha a rendszer kéri, írja be az SSH-fiókhoz használt jelszót.

További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

##<a id="getkafkainfo"></a>A Zookeeper és a közvetítő gazdagép információinak lekérése

A Kafka használata során két gazdagép értékeit kell ismernie: a *Zookeeper*-gazdagépekét és a *Közvetítő* gazdagépekét. A Kafka API és a Kafkában elérhető számos segédprogram használja ezeket a gazdagépeket.

Az alábbi lépésekkel létrehozhatja a gazdagép adatait tartalmazó környezeti változókat. A jelen dokumentumban lévő lépésekben használjuk ezeket a környezeti változókat.

1. Egy, a fürthöz csatlakozó SSH-kapcsolaton használja a következő parancsot a `jq` segédprogram telepítéséhez. A segédprogram JSON-dokumentumok elemzését végzi, és hasznos szerepet tölt be a közvetítő gazdagép adatainak lekérésében:
   
    ```bash
    sudo apt -y install jq
    ```

2. A következő parancsokkal beállíthatja a környezeti változókat az Ambariból lekért adatokkal. Cserélje le a __KAFKANAME__ elemet a Kafka-fürt nevére. Cserélje le a __PASSWORD__ elemet a fürt létrehozásakor használt bejelentkezési (rendszergazdai) jelszavára.

    ```bash
    export KAFKAZKHOSTS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`

    export KAFKABROKERS=`curl --silent -u admin:'PASSWORD' -G http://headnodehost:8080/api/v1/clusters/KAFKANAME/services/HDFS/components/DATANODE | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'`

    echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    A következő szöveg egy példa a `$KAFKAZKHOSTS` tartalmára:
   
    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`
   
    A következő szöveg egy példa a `$KAFKABROKERS` tartalmára:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`
   
    > [!WARNING]
    > Ne bízzon abban, hogy az ebben a munkamenetben visszaadott adatok mindig pontosak lesznek. A fürt átméretezésekor új közvetítők lesznek hozzáadva vagy eltávolítva. Ha hiba esetén egy fürt lecserélődik, a fürt gazdaneve változhat. 
    > 
    > A Zookeeper- és a közvetítő gazdagépek adatait rövid idővel a felhasználásuk előtt érdemes lekérni, hogy biztosan érvényes információkkal rendelkezzen.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása

A Kafka *témaköröknek* nevezett kategóriákban tárolja az adatstreameket. Témakör létrehozásához használja a Kafkához biztosított szkripet egy fürt átjárócsomójával létesített SSH-kapcsolatból:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
```

Ez a parancs a `$KAFKAZKHOSTS`-ban tárolt állomásadatok használatával kapcsolódik a Zookeeperhez, majd létrehoz egy **test** nevű Kafka-témakört. A témakör létrehozását a témakörök listázására szolgáló alábbi szkripttel ellenőrizheti:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
```

A parancs kimenete listázza a Kafka-témaköröket, és tartalmazza a **test** témakört.

## <a name="produce-and-consume-records"></a>Rekordok létrehozása és felhasználása

A Kafka témakörökben tárolja a *rekordokat*. A rekordokat *előállítók* hozzák létre, és *fogyasztók* használják fel. Az előállítók *Kafka-közvetítőktől* kérik le a rekordokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

Kövesse az alábbi lépéseket a rekordoknak a korábban létrehozott test témakörben való eltárolására, majd a beolvasásukra egy fogyasztó használatával:

1. A Kafkához biztosított szkript használatával írja a rekordokat a témakörbe az SSH-munkamenetben:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    A parancs után a rendszer nem lép vissza a parancssorhoz. Ezért írjon inkább néhány szöveges üzenetet, majd a **Ctrl + C** billentyűparancs használatával állítsa le a témakörbe történő küldést. A rendszer minden sort külön rekordként küld el.

2. A Kafkához biztosított szkript használatával olvassa be rekordokat a témakörből:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic test --from-beginning
    ```
   
    A parancs lekéri a rekordokat a témakörből, majd megjeleníti őket. A `--from-beginning` használata arra utasítja a fogyasztót, hogy a stream elejétől kezdje a műveletet, így az összes rekord lekérése megtörténik.

3. Használja a __Ctrl + C__ billentyűparancsot a fogyasztó leállításához.

## <a name="producer-and-consumer-api"></a>Előállítói és fogyasztói API

Szoftveresen is létrehozhat és felhasználhat rekordokat a [Kafka API-k](http://kafka.apache.org/documentation#api) használatával. Az alábbi lépéseket követve töltsön le, és hozzon létre egy Java-alapú előállítót és fogyasztót:

1. A példákat a [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) címről töltheti le. Az előállítói/fogyasztói példához használja a `Producer-Consumer` könyvtárban található projektet. Ez a példa az alábbi osztályokat tartalmazza:
   
    * **Run** – elindítja a fogyasztót vagy az előállítót.

    * **Producer** – 1 000 000 rekordot tárol a témakörben.

    * **Consumer** – rekordokat olvas be a témakörből.

2. A fejlesztési környezet parancssorában lépjen a példa `Producer-Consumer` könyvtárának helyére, majd használja az alábbi parancsot egy JAR-csomag létrehozásához:
   
    ```
    mvn clean package
    ```
   
    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-producer-consumer-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Az alábbi parancsokkal másolja a `kafka-producer-consumer-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtbe:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

4. Amint az `scp` parancs befejezi a fájl másolását, kapcsolódjon a fürthöz SSH használatával, majd rekordoknak a korábban létrehozott test témakörbe történő íráshoz használja az alábbi parancsot.
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS
    ```
   
    A parancs elindítja az előállítót, és rekordokat ír. Megjelenik egy számláló, így láthatja, hogy hány rekord írása történt meg.

    > [!NOTE]
    > Ha az engedély megtagadását jelző hibaüzenetet kap, használja a következő parancsot a fájl végrehajthatóvá tételéhez: ```chmod +x kafka-producer-consumer.jar```

5. A folyamat befejeződését követően használja az alábbi parancsot a témakörből történő olvasáshoz:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    A rendszer megjeleníti a beolvasott rekordokat a rekordok számával együtt. Kicsivel több mint 1 000 000 naplózást láthat, mivel számos rekordot elküldött a témakörbe az egyik korábbi lépés szkriptjének használatával.

6. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez.

### <a name="multiple-consumers"></a>Több fogyasztó

A Kafka egyik fontos vonása, hogy a fogyasztók fogyasztói csoportot (amelyet egy csoportazonosító határoz meg) használnak a rekordok olvasásánál. Ugyanazon csoport használata több fogyasztó esetén a terhelés szempontjából kiegyensúlyozott olvasást eredményez a témakörökből történő olvasáskor. A csoport mindegyik fogyasztója a rekordok egy részét kapja meg. Ha szeretné látni, hogy hogyan zajlik a folyamat, kövesse az alábbi lépéseket:

1. Nyisson meg egy új, a fürttel létesített SSH-munkamenetet, így már kettővel fog rendelkezni. Az egyes munkamenetekben használja az alábbi parancsot az ugyanazon csoportazonosítóval rendelkező fogyasztó elindításához:
   
    ```bash
    ./kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```

    > [!NOTE]
    > Mivel ez egy új SSH-munkamenet, használja [A Zookeeper és a közvetítő állomásadatainak beszerzése](#getkafkainfo) című szakaszban található parancsot a `$KAFKABROKERS` beállításához.

2. Figyelje, ahogy az egyes munkamenetek számlálják a témakörtől fogadott rekordokat. A két munkamenet által számlált értéknek meg kell egyeznie a korábban egy fogyasztótól kapott értékkel.

Az ugyanazon csoportban található ügyfelek általi felhasználás kezelése a témakör partícióinak használatával történik. A korábban létrehozott `test` témakör nyolc partícióval rendelkezik. Ha megnyit nyolc SSH-munkamenetet, és mindegyikben elindít egy fogyasztót, az egyes fogyasztók a témakör egyetlen partíciójából fognak rekordokat olvasni.

> [!IMPORTANT]
> A fogyasztói csoportban található fogyasztói példányok száma nem haladhatja meg a partíciók számát. Ebben a példában egy fogyasztói csoport legfeljebb 8 fogyasztót tartalmazhat, mivel a témakörben ennyi partíció található. Emellett lehet több, legfeljebb 8 fogyasztóval rendelkező fogyasztói csoportja is.

A Kafkában tárolt rekordok mentése a partíción belüli fogadásuk sorrendje szerint történik. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *egy partíción belül*, hozzon létre egy fogyasztói csoportot, amelyben a fogyasztói példányok száma egyezik a partíciók számával. Ha a rekordokat az érkezési sorrendben szeretné kézbesíteni *a témakörön belül*, hozzon létre egy olyan fogyasztói csoportot, amely csak egyetlen fogyasztói példánnyal rendelkezik.

## <a name="streaming-api"></a>Streamelési API

A streamelési API a Kafka 0.10.0-s verziójában vált elérhetővé, a korábbi verziók az Apache Sparkot vagy Stormot használnak a streamfeldolgozáshoz.

1. Ha még nem tette meg, töltse le a példákat a [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) címről a fejlesztési környezetbe. A streamelési példához használja a `streaming` könyvtárban található projektet.
   
    Ez a projekt csak a `Stream` osztályt tartalmazza, amely rekordokat olvas be a korábban létrehozott `test` témakörből. Számlálja a beolvasott szavakat, és minden szót és számlált értéket a `wordcounts` témakörbe küld el. A `wordcounts` témakört a jelen szakasznak egy későbbi lépésében hozza majd létre.

2. A fejlesztési környezet parancssorában lépjen a `Streaming` könyvtár helyére, majd használja az alábbi parancsot egy JAR-csomag létrehozásához:
   
    ```
    mvn clean package
    ```
   
    A parancs létrehozza a `target` nevű könyvtárat, amely a `kafka-streaming-1.0-SNAPSHOT.jar` nevű fájlt tartalmazza.

3. Az alábbi parancsokkal másolja a `kafka-streaming-1.0-SNAPSHOT.jar` fájlt a HDInsight-fürtbe:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Cserélje le az **SSHUSER** elemet a fürt SSH-felhasználójára, illetve a **CLUSTERNAME** elemet a fürt nevére. Ha a rendszer kéri, adja meg az SSH-felhasználó jelszavát.

4. Amint az `scp` parancs befejezi a fájl másolását, kapcsolódjon a fürthöz SSH használatával, majd használja az alábbi parancsot a `wordcounts` témakör létrehozásához:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

5. Ezután indítsa el a streamelési folyamatot az alábbi paranccsal:
   
    ```bash
    ./kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS 2>/dev/null &
    ```
   
    A parancs elindítja a streamelési folyamatot a háttérben.

6. Üzeneteknek a `test` témakörbe történő küldéséhez használja az alábbi parancsot. Az üzeneteket a streamelési példa dolgozza fel:
   
    ```bash
    ./kafka-producer-consumer.jar producer $KAFKABROKERS &>/dev/null &
    ```

7. A streamelési folyamat által a `wordcounts` témakörbe írt kimenet megtekintéséhez használja az alábbi parancsot:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic wordcounts --from-beginning --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer
    ```
   
    > [!NOTE]
    > Az adatok megtekintéséhez utasítsa a fogyasztót a kulcs létrehozására, illetve a deszerializálót a kulcs és az érték felhasználására. A kulcsnév a szó, a számot pedig a kulcsérték tartalmazza.
   
    A kimenet az alábbi szöveghez hasonló:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
        a       13805
        snow    13637
   
    > [!NOTE]
    > A szám nő minden egyes szó beolvasott szóval.

7. Használja a __Ctrl + C__ billentyűparancsot a fogyasztóból történő kilépéshez, majd az `fg` paranccsal hozza az előtérbe a háttérben futó streamelési feladatot. Használja a __Ctrl + C__ billentyűparancsot a feladatból való kilépéshez.

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Következő lépések

A jelen dokumentumban megismerkedett az Apache Kafka HDInsightban való használatának az alapjaival. Az alábbiak további információt biztosítanak a Kafka használatával kapcsolatban:

* Az [Apache Kafka dokumentációja](http://kafka.apache.org/documentation.html) a kafka.apache.org webhelyen.
* [A MirrorMaker használata a Kafka replikájának HDInsighton való létrehozásához](hdinsight-apache-kafka-mirroring.md)
* [Az Apache Storm használata a HDInsighton futó Kafkával](hdinsight-apache-storm-with-kafka.md)
* [Az Apache Spark használata a Kafkával a HDInsighton](hdinsight-apache-spark-with-kafka.md)


