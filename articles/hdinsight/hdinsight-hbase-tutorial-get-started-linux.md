---
title: "A HBase használatának első lépései az Azure HDInsightban | Microsoft Docs"
description: "Ez a HBase oktatóanyag segítséget nyújt az Apache HBase Hadoop-eszközzel végzett használatának első lépéseiben a HDInsight eszközben. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel."
keywords: "apache hbase,hbase,hbase rendszerhéj,hbase-oktatóanyag"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 4d6a2658-6b19-4268-95ee-822890f5a33a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 21d8dff230e045607b70013f4eabf1bfe8ec3993
ms.lasthandoff: 03/25/2017


---
# <a name="hbase-tutorial-get-started-using-apache-hbase-in-hdinsight"></a>HBase oktatóanyag: Az Apache HBase használatának első lépései a HDInsight eszközben

Megtudhatja, hogyan hozhat létre HBase-fürtöket a HDInsight eszközben, illetve hogyan hozhat létre HBase táblákat és lekérdezéstáblákat a Hive eszközzel. A HBase-re vonatkozó általános információért lásd: [HDInsight HBase overview][hdinsight-hbase-overview] (A HDInsight HBase áttekintése).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Előfeltételek
Ezen HBase oktatóanyag elkezdéséhez a következőkkel kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Biztonságos rendszerhéj (SSH)](hdinsight-hadoop-linux-use-ssh-unix.md). 
* [curl](http://curl.haxx.se/download.html).

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-hbase-cluster"></a>HBase-fürt létrehozása
Az alábbi eljárás egy Azure Resource Manager-sablont használ egy 3.4 verziójú Linux-alapú HBase-fürt és a függő Azure Storage-fiók létrehozására. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban).

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablon egy nyilvános blobtárolóban található. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. A **Custom deployment** (Egyéni üzembe helyezés) panelen adja meg a következőket:
   
   * **Subscription** (Előfizetés): Válassza ki a fürt létrehozásához használni kívánt Azure-előfizetését.
   * **Resource group** (Erőforráscsoport): Hozzon létre egy új Azure Resource Management-csoportot vagy használjon egy meglévőt.
   * **Location** (Hely): Adja meg az erőforráscsoport helyét. 
   * **ClusterName** (Füret neve): Adjon nevet a létrehozandó HBase-fürtnek.
   * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
   * **SSH-felhasználónév és jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni.
     
     Más paraméterek opcionálisak.  
     
     Minden egyes fürt az Azure Storage-fióktól függ. A fürtök törlése után az adatok megmaradnak a tárfiókban. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A név szoftveresen kötött a sablonváltozók szakaszban.
3. Válassza az **I agree to the terms and conditions stated above** (Elfogadom a fenti feltételeket) lehetőséget majd kattintson a **Purchase** (Vásárlás) gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

> [!NOTE]
> A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.
> 
> 

## <a name="create-tables-and-insert-data"></a>Táblák létrehozása és adatok beszúrása
Az SSH-val HBase-fürtökhöz csatlakozhat, majd a HBase-rendszerhéjjal Hbase-táblákat hozhat létre, adatokat szúrhat be, és adatokat kérdezhet le. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight HBase táblázatos adatok][img-hbase-sample-data-tabular]

A HBase eszközben, amely a BigTable implementációja, ugyanezen az adatok a következőképpen néznek ki:

![HDInsight HBase BigTable-adatok][img-hbase-sample-data-bigtable]

Több értelme lesz a következő eljárás befejezése után.  

**A Hbase-rendszerhéj használata**

1. Az SSH-ból futtassa az alábbi parancsot:
   
        hbase shell
2. Hozzon létre egy HBase-t kétoszlopos családokkal:
   
        create 'Contacts', 'Personal', 'Office'
        list
3. Adatok beszúrása:
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![HDInsight Hadoop HBase-rendszerhéj][img-hbase-shell]
4. Egyetlen sor lekérése
   
        get 'Contacts', '1000'
   
    Ugyanazokat az eredményeket látja, mint a vizsgálat parancskor, mert csak egy sor van.
   
    A Hbase-táblasémáról további információért lásd: [Introduction to HBase Schema Design][hbase-schema] (Bevezetés a Hbase-sématervezésbe). További Hbase-parancsokért lásd: [Apache HBase reference guide][hbase-quick-start] (Apache HBase referencia-útmutató).
5. Kilépés a rendszerhéjból
   
        exit

**Adatok kötegelt betöltése a névjegyek HBase-táblába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](http://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).

A rendszer feltöltött egy minta adatfájlt egy nyilvános blob-tárolóba, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Az adatfájl tartalma a következő:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába, ha szeretné. Az utasításokért lásd: [Upload data for Hadoop jobs in HDInsight][hdinsight-upload-data] (Adatok feltöltése Hadoop-feladatokhoz a HDInsightban).

> [!NOTE]
> Ez az eljárás az utolsó eljárás során létrehozott Contacts HBase táblát használja.
> 
> 

1. Az SSH-ból futtassa az alábbi parancsot, hogy az adatfájlt StoreFiles-fájllá alakítsa, és a Dimporttsv.bulk.output által meghatározott relatív elérési úton tárolja:  Ha a HBase rendszerhéjban van, a kilépés paranccsal lépjen ki.
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
2. Futtassa a következő parancsot, hogy adatokat töltsön fel az /example/data/storeDataFileOutput mappából a HBase táblába:
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
3. Megnyithatja a HBase rendszerhéjat, és a vizsgálat paranccsal listázhatja a tábla tartalmát.

## <a name="use-hive-to-query-hbase"></a>A Hive használata a HBase lekérdezéséhez
A HBase táblákban lévő adatokat a Hive eszközzel kérdezheti le. Ez a szakasz olyan Hive táblát hoz létre, amely a HBase táblára képez le, és azzal kérdezi le a HBase táblában lévő adatokat.

> [!NOTE]
> Ha a Hive és a HBase eltérő fürtökön vannak egyazon virtuális hálózaton, teljesítenie kell a zookeeperkvórumot a Hive-rendszerhéj meghívása során:
>
>       hive --hiveconf hbase.zookeeper.quorum=zk0-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk1-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net,zk2-xxxx.xxxxxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net --hiveconf zookeeper.znode.parent=/hbase-unsecure  
>
>

1. Nyissa meg a **PuTTY** eszközt, és csatlakozzon a fürthöz.  Lásd az előző eljárás utasításait.
2. Nyissa meg a Hive rendszerhéjat.
   
       hive
       
3. Futtassa a következő HiveQL-szkriptet, hogy egy, a HBase-táblára leképező Hive-táblát hozzon létre. Ellenőrizze, hogy létrehozta-e az oktatóanyag korábbi részében hivatkozott mintatáblát az utasítás futtatása előtt a HBase rendszerhéjjal.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
4. Futtassa a következő HiveQL-parancsfájlt a HBase-tábla adatainak lekérdezéséhez:
   
         SELECT count(*) FROM hbasecontacts;

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API-k használata Curl használatával
> [!NOTE]
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.
> 
> Ezen szakasz parancsaiban cserélje le a **USERNAME** elemet a fürthöz hitelesíteni kívánt felhasználóval, és a **PASSWORD** elemet pedig a felhasználói fiók jelszavával. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
> 
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.
> 
> 

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
   
    A következőhöz hasonló választ kell kapnia:
   
        {"status":"ok","version":"v1"}
   
    Ezen parancs paraméterei a következők:
   
   * **-u** - A kérés hitelesítéséhez használt felhasználónév és jelszó.
   * **-G** - Jelzi, hogy ez egy GET kérés.
2. Használja az alábbi parancsot a meglévő HBase-táblák listázásához:
   
        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/
3. Használja az alábbi parancsot egy új, kétoszlopos családokkal rendelkező HBase-tábla létrehozásához:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v
   
    A séma a JSon formátumban van megadva.
4. Használja az alábbi parancsot néhány adat beviteléhez:
   
        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
        -v
   
    A -d kapcsolóban megadott értékeket a base64 használatával kell kódolnia.  A példában:
   
   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole
     
     A [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) lehetővé teszi több (kötegelt) érték beszúrását.
5. Használja az alábbi parancsot egy sor lekéréséhez:
   
        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

További információ a HBase REST-ről: [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Apache HBase referencia-útmutató).

>
> [!NOTE]
> A HBase nem támogatja a Thriftet a HDInsightban.
>

## <a name="check-cluster-status"></a>A fürt állapotának ellenőrzése
A HBase a HDInsightban a fürtök megfigyelésére szolgáló webes felhasználói felülettel kapható. A webes felhasználói felülettel a régiók statisztikáit vagy információit kérheti le.

**A HBase mesterfelületének elérése**

1. Nyissa meg az Ambari webes felületet a https://&lt;Fürtnév>.azurehdinsight.net helyen.
2. Kattintson a **HBase** elemre a bal oldali menüben.
3. Kattintson a **Gyorshivatkozások** elemre a lap tetején, mutasson az aktív Zookeeper-csomópont hivatkozására, majd kattintson a **HBase-mesterfelület** elemre.  A felület egy új böngészőlapon nyílik meg:

  ![HDInsight HBase HMaster felhasználói felülete](./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

  A HBase-mesterfelület az alábbi részeket tartalmazza:

  - régiós kiszolgálók
  - biztonsági mentési főkiszolgálók
  - táblák
  - feladatok
  - szoftverattribútumok

## <a name="delete-the-cluster"></a>A fürt törlése
Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Következő lépések
A HDInsight ezen HBase oktatóprogramjában megtanulta, hogyan hozhat létre HBase-fürtöt, hogyan hozhat létre táblákat, és tekintheti meg ezen táblák adatait a HBase rendszerhéjból. Azt is megtanulta, hogyan használhat Hive-lekérdezést a HBase-táblákban lévő adatokon, és hogyan használhatja a HBase C# REST API-kat egy HBase-tábla létrehozásához és adatok lekérdezéséhez a táblából.

További tudnivalókért lásd:

* [HDInsight HBase overview][hdinsight-hbase-overview] (A HDInsight HBase áttekintése): A HBase egy Apache, nyílt forráskódú, a Hadoopra épülő NoSQL-adatbázis, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

