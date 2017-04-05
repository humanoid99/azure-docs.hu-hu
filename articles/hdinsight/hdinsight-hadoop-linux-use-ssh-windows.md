---
title: "SSH és PuTTY HDInsight-fürtök együttes használata Windows rendszerben | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre és használhat SSH-kulcsokat a Linux-alapú HDInsight-fürtök hitelesítéséhez a Windows-alapú ügyfelektől a PuTTY SSH-ügyfél használatával."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 639328ca-d800-4fa9-97ed-5664477b88cd
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2017
ms.author: larryfr
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b1806950581e0adbeec52839f12c70599d28100d
ms.lasthandoff: 03/21/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-putty-on-windows"></a>SSH és PuTTY HDInsight (Hadoop) együttes használata Windows rendszerben

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows, Linux, Unix, OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

A [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) lehetővé teszi, hogy egy parancssori felülettel távolról hajtson végre műveleteket a Linux-alapú HDInsight-fürtökön. Ez a dokumentum arra vonatkozó információkat tartalmaz, hogyan csatlakozhat Windows-alapú ügyfelekről a HDInsight-hoz a PuTTY SSH-ügyfél segítségével.

> [!NOTE]
> A cikkben ismertetett eljárásokhoz Windows-alapú, PuTTY SSH-ügyféllel rendelkező rendszert kell használnia. Ha olyan Linux, Unix, OS X vagy Windows rendszert használ, amely biztosítja a(z) `ssh` parancsot, tekintse meg [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md) című témakört.

## <a name="prerequisites"></a>Előfeltételek

* **PuTTY** és **PuTTYGen** Windows-alapú ügyfelekhez. Ezek a segédprogramok a [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) címen tölthetők le.
* Egy HTML5-támogatással rendelkező modern webböngésző.

## <a name="what-is-ssh"></a>Mi az az SSH?

Az SSH egy olyan eszköz, amellyel beléphet egy távoli kiszolgálóra, és távolról parancsokat hajthat rajta végre. Az SSH a Linux-alapú HDInsight segítségével titkosított kapcsolatot hoz létre a fürt átjárócsomópontjával, és megjelenít egy parancssort, amelybe parancsokat írhat. A parancsok végrehajtására közvetlenül a kiszolgálón kerül sor.

A Windows a korábbiakban nem biztosított SSH-ügyfelet. A PuTTY egy, a Windowsra is feltelepíthető grafikus SSH-ügyfél.

### <a name="ssh-user-name"></a>SSH-felhasználónév

Az SSH-felhasználónév az a név, amelyet a HDInsight-fürtön a hitelesítéshez használ. Amikor a fürt létrehozása során megad egy SSH-felhasználónevet, ez a felhasználó a fürt összes csomópontján létrejön. Miután a fürt létrejött, ezzel a felhasználónévvel csatlakozhat a HDInsight-fürt átjárócsomópontjaihoz. Ezután az átjárócsomópontokról csatlakozhat az egyes munkavégző csomópontokhoz.

### <a name="ssh-password-or-public-key"></a>SSH-jelszó vagy nyilvános kulcs

Az SSH-felhasználó jelszót vagy nyilvános kulcsot is használhat a hitelesítéshez. A jelszó egy Ön által megadott szöveges karakterlánc, míg a nyilvános kulcs egy kriptografikus kulcspár része, amely az Ön egyedi azonosítására szolgál.

A kulcs biztonságosabb, mint a jelszó, azonban a kulcs létrehozásához további lépések szükségesek, és a kulcsot tartalmazó fájlokat biztonságos helyen kell tárolni. Ha bárki hozzáfér a kulcsfájlokhoz, utána a fiókjához is hozzáférhet. Ha a kulcsfájlok elvesznek, nem fog tudni belépni a fiókjába.

Egy kulcspár egy nyilvános kulcsból (amelyet a rendszer elküld a HDInsight-kiszolgálóra) és egy titkos kulcsból (amelyet az ügyfélszámítógép tárol) áll. Amikor az SSH-val a HDInsight-kiszolgálóhoz csatlakozik, az SSH-ügyfél a számítógépén található titkos kulccsal hitelesíti a kiszolgálót.

## <a name="create-an-ssh-key"></a>SSH-kulcs létrehozása

Támaszkodjon az alábbi információkra, ha a fürtön SSH-kulcsokat szeretne használni. Ha inkább jelszó használatát tervezi, kihagyhatja ezt a szakaszt.

1. Nyissa meg a PuTTYGen eszközt.

2. A **Type of key to generate** (Létrehozni kívánt kulcs típusa) elemnél válassza az **SSH-2 RSA** értéket, majd kattintson a **Generate** (Létrehozás) gombra.
   
    ![A PuTTYGen felülete](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. Mozgassa az egeret a folyamatjelző alatti területen, amíg a sáv végig nem ér. Az egér mozgatásával véletlenszerű adatokat hoz létre, amelyeket a rendszer a kulcs létrehozásához használ.
   
    ![Az egér mozgatása](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)
   
    A kulcs létrehozása után megjelenik a nyilvános kulcs.

4. A biztonság növelése érdekében megadhat egy hozzáférési kódot a **Key passphrase** (Kulcs hozzáférési kódja) mezőben. Ezután ugyanezt az értéket írja be a **Confirm passphrase** (Hozzáférési kód megerősítése) mezőbe.
   
    ![Hozzáférési kód](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)
   
   > [!NOTE]
   > Határozottan javasoljuk, hogy a kulcshoz egy biztonságos hozzáférési kódot használjon. Ha azonban elfelejti a hozzáférési kódját, nincs lehetőség a helyreállításra.

5. A **Save private key** (Titkos kulcs mentése) gombra kattintva mentse a kulcsot egy **.ppk** fájlba. Ezzel a kulccsal hitelesítheti majd a Linux-alapú HDInsight-fürtöt.
   
   > [!NOTE]
   > Érdemes ezt a kulcsot biztonságos helyen tárolni, mivel hozzáférést biztosít a Linux-alapú HDInsight-fürthöz.

6. A **Save public key** (Nyilvános kulcs mentése) gombra kattintva mentse a kulcsot egy **.txt** fájlba. Így később újra felhasználhatja a nyilvános kulcsot, ha további Linux-alapú HDInsight-fürtöket szeretne létrehozni.
   
   > [!NOTE]
   > A nyilvános kulcs szintén megjelenik a PuTTYGen felső részében. A mezőbe a jobb gombbal kattintva kimásolhatja az értéket, majd beillesztheti egy űrlapba, amikor az Azure portállal egy fürtöt hoz létre.

## <a name="create-a-linux-based-hdinsight-cluster"></a>Linux-alapú HDInsight-fürt létrehozása

Egy Linux-alapú HDInsight-fürt létrehozásakor meg kell adnia az előzőleg létrehozott nyilvános kulcsot. Windows-alapú ügyfelekről kétféle módon hozhat létre Linux-alapú HDInsight-fürtöt:

* **Azure portál** – A fürt létrehozásához egy webes portált használ.

* **Azure parancssori felület (CLI) Mac, Linux és Windows rendszerekhez** – A fürtöt parancssori parancsok segítségével hozza létre.

Mindkét módszerhez a nyilvános kulcs megadása szükséges. A Linux-alapú HDInsight-fürtök létrehozására vonatkozó teljes körű információkat a [Provision Linux-based HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú HDInsight-fürtök kiépítése) című témakör tartalmazza.

### <a name="azure-portal"></a>Azure Portal

Amikor az [Azure Portal][preview-portal] segítségével hoz létre egy Linux-alapú HDInsight-fürtöt, egy **SSH Username** (SSH felhasználónév) megadása szükséges, valamint választania kell egy **PASSWORD** (JELSZÓ) vagy egy **SSH PUBLIC KEY** (SSH NYILVÁNOS KULCS) megadása között.

Ha az **SSH PUBLIC KEY** (SSH NYILVÁNOS KULCS) megadását választja, a nyilvános kulcsot (amely a PuTTYGen **Public key for pasting into OpenSSH authorized\_keys file** (OpenSSH authorized_keys fájlba másolandó nyilvános kulcs) mezőjében jelenik meg) bemásolhatja az **SSH PublicKey** (SSH nyilvános kulcs) mezőbe, vagy a **Select a file** (Fájl kijelölése) lehetőséggel megkeresheti és kiválaszthatja a nyilvános kulcsot tartalmazó fájlt.

![A nyilvános kulcsot kérő űrlap képe](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

Bejelentkezési adatokat hoz létre az adott felhasználó számára, és engedélyezi a jelszóval vagy az SSH-kulccsal végzett hitelesítést.

### <a name="azure-command-line-interface-for-mac-linux-and-windows"></a>Azure parancssori felület Mac, Linux és Windows rendszerekhez

Az [Azure parancssori felület (CLI) Mac, Linux és Windows rendszerekhez](../cli-install-nodejs.md) lehetővé teszi, hogy az `azure hdinsight cluster create` paranccsal egy új fürtöt hozzon létre.

További információk a parancs használatáról: [Provision Hadoop Linux clusters in HDInsight using custom options](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök kiépítése a HDInsightban egyéni beállításokkal).

## <a name="connect-to-a-linux-based-hdinsight-cluster"></a>Csatlakozás egy Linux-alapú HDInsight-fürthöz

1. Nyissa meg a PuTTY eszközt.
   
    ![Putty felülete](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)
2. Ha a felhasználói fiók létrehozásakor egy SSH-kulcsot adott meg, a következő lépéseket kell elvégeznie a fürt hitelesítéséhez használt titkos kulcs kiválasztásához:
   
    A **Category** (Kategória) szakaszban bontsa ki a **Connection** (Kapcsolat), majd az **SSH** elemet, és válassza az **Auth** (Hitelesítés) lehetőséget. Végül kattintson a **Browse** (Tallózás) gombra, és válassza ki a titkos kulcsot tartalmazó .ppk fájlt.
   
    ![Putty felülete, titkos kulcs kiválasztása](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. A **Category** (Kategória) szakaszban válassza a **Session** (Munkamenet) elemet. A **Basic options for your PuTTY session** (Alapszintű beállítások a PuTTY-munkamenethez) képernyőn adja meg a HDInsight-kiszolgáló SSH-címét a **Host name (or IP address)** (Kiszolgálónév (vagy IP-cím)) mezőben. Egy fürthöz két lehetséges SSH-cím segítségével csatlakozhat:

    ![Putty felülete egy megadott ssh-címmel](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

    * **Átjárócsomópont címe**: ha a fürt átjárócsomópontjához szeretne csatlakozni, használja a fürt nevét az **-ssh.azurehdinsight.net** kifejezéssel kiegészítve. Például: **mycluster-ssh.azurehdinsight.net**.
   
    * **Élcsomópont címe**: ha a HDInsight-fürt egyik R Server kiszolgálójához csatlakozik, az R Server élcsomópontjához az **RServer.CLUSTERNAME.ssh.azurehdinsight.net** címmel csatlakozhat, ahol CLUSTERNAME a fürt neve. Például: **RServer.mycluster.ssh.azurehdinsight.net**.
     

4. A csatlakozási információkat elmentheti későbbi felhasználásra, ha a **Saved Sessions** (Mentett munkamenetek) alatt megad egy nevet a kapcsolat számára, majd a **Save** (Mentés) gombra kattint. A kapcsolat felkerül a mentett munkamenetek listájára.
5. Az **Open** (Megnyitás) gombra kattintva csatlakozhat a fürthöz.
   
   > [!NOTE]
   > Ha ez az első alkalom, amikor a fürthöz csatlakozik, a rendszer egy biztonsági figyelmeztetést jelenít meg. Ez nem jelent rendellenes működést. A folytatáshoz válassza a **Yes** (Igen) lehetőséget a kiszolgáló RSA2-kulcsának gyorsítótárazásához.

6. Amikor a rendszer erre kéri, adja meg a fürt létrehozásakor megadott felhasználónevet. Ha a felhasználónévhez jelszót adott meg, a rendszer ezt is elkéri.

> [!NOTE]
> A fenti lépések azt feltételezik, hogy a 22-es portot használja, és ezzel a HDInsight-fürt elsődleges átjárócsomópontjával létesít kapcsolatot. A 23-as port használata esetén a másodlagos átjárócsomóponthoz fog csatlakozni. Az átjárócsomópontokkal kapcsolatos további információkat lásd: [Availability and reliability of Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (A Hadoop-fürtök rendelkezésre állása és megbízhatósága a HDInsightban).

### <a name="connect-to-worker-nodes"></a>Csatlakozás a munkavégző csomópontokhoz

A munkavégző csomópontok az Azure adatközponton kívülről nem érhetők el közvetlenül, de SSH-n keresztül hozzáférhetők a fürt átjárócsomópontjából.

Ha megadott egy SSH-kulcsot a felhasználói fiók létrehozásakor, a következő lépéseket kell elvégeznie a személyes kulcs használatához a fürt hitelesítésekor, ha a munkavégző csomópontokhoz szeretne csatlakozni.

1. Telepítse a Pageant eszközt a [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) címről. Ezzel a segédprogrammal gyorsítótárazhatók az SSH-kulcsok a PuTTY rendszerhez.

2. Futtassa a Pageant eszközt. Ikon méretűvé válik az állapottálcán. Kattintson a jobb gombbal az ikonra, és válassza az **Add key** (Kulcs hozzáadása) parancsot.
   
    ![kulcs hozzáadása](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. Amikor megjelenik a böngészés párbeszédpanel, válassza ki a kulcsot tartalmazó .ppk fájlt, majd kattintson az **Open** (Megnyitás) gombra. Ez a kulcsot a Pageant programhoz adja, amely átadja azt a PuTTY rendszernek, amikor a fürthöz csatlakozik.
   
   > [!IMPORTANT]
   > Ha SSH-kulccsal védte a fiókját, el kell végeznie az előző lépéseket, mielőtt munkavégző csomópontokhoz csatlakozhatna.

4. Nyissa meg a PuTTY eszközt.

5. Ha SSH-kulcs használatával hitelesít, a **Category** (Kategória) szakaszban bontsa ki a **Connection** (Kapcsolat), majd az **SSH** elemet, és válassza az **Auth** (Hitelesítés) lehetőséget.
   
    Az **Authentication parameters** (Hitelesítési paraméterek) szakaszban engedélyezze az **Allow agent forwarding** (Ügynöktovábbítás engedélyezése) beállítást. Ez lehetővé teszi, hogy a PuTTY automatikusan továbbítsa a tanúsítványhitelesítést a kapcsolaton keresztül a fürt átjárócsomópontjába, amikor munkavégző csomópontokhoz csatlakozik.
   
    ![ügynöktovábbítás engedélyezése](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. A korább dokumentált módon csatlakozzon a fürthöz. Ha SSH-kulcsot használ a hitelesítéshez, nem kell kiválasztania a kulcsot – a hitelesítés a Pageant eszközhöz hozzáadott kulccsal történik a fürtön.

7. A kapcsolat létrehozása után a következővel kérje le a fürtben lévő csomópontok listáját. Cserélje le az *ADMINPASSWORD* elemet a fürt rendszergazdai fiókjának jelszavára. Cserélje le a *CLUSTERNAME* elemet a fürt nevére.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    Ez JSON formátumban ad meg információkat a fürt csomópontjaihoz, beleértve a `host_name` elemet is, amely tartalmazza az egyes csomópontok teljes tartománynevét (FQDN-jét). A következőkben egy példa látható a **curl** parancs által visszaadott `host_name` bejegyzésre:
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. Amikor már van egy listája azokról a munkavégző csomópontokról, amelyekhez csatlakozni szeretne, használja a PuTTY-munkamenet következő parancsát a munkavégző csomópont csatlakoztatásához:
   
        ssh USERNAME@FQDN
   
    Cserélje le a *USERNAME* karakterláncot az SSH felhasználónevére, és az *FQDN* karakterláncot a munkavégző csomópont FQDN-jére. Például: `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
    
    > [!NOTE]
    > Ha jelszóval hitelesíti az SSH-munkamenetet, a rendszer ismét elkéri a jelszót. Ha SSH-kulcsot használ, a kapcsolat további kérések nélkül létrejön.

9. A munkamenet létrehozása után a PuTTY-munkamenet parancssora `username@hn#-clustername` értékről `username@wn#-clustername` értékre változik annak jelzéséhez, hogy a munkavégző csomópont csatlakoztatva van. Innentől a futtatott parancsok a munkavégző csomóponton fognak futni.

10. Amikor már nem szeretne több műveletet végrehajtani a munkavégző csomóponton, az `exit` paranccsal zárja be a munkamenetét. Ezzel visszatér a `username@hn#-clustername` parancssorhoz.

## <a name="add-more-accounts"></a>További fiókok hozzáadása

Ha további fiókokat kell a fürthöz adnia, végezze el a következő lépéseket:

1. Hozzon létre egy új nyilvános és egy új titkos kulcsot az új felhasználói fiókhoz a fent leírt módon.

2. Egy SSH-munkamenetből adjon új felhasználót a fürthöz a következő paranccsal:
   
        sudo adduser --disabled-password <username>
   
    Ez létrehoz egy új felhasználói fiókot, de letiltja a jelszó-hitelesítést.

3. Hozza létre a kulcs tárolására szolgáló könyvtárat és fájlokat a következő parancsokkal:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. Amikor megnyílik a nano szerkesztő, másolja be a nyilvános kulcs a tartalmát az új felhasználói fiókhoz. A **Ctrl-X** billentyűparanccsal mentse a fájlt, és lépjen ki a szerkesztőből.
   
    ![A nano szerkesztő képe egy példakulccsal](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. A következő paranccsal módosítsa az .ssh mappa és a benne található tartalmak tulajdonosát az új felhasználói fiókra:
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh

6. Mostantól az új felhasználói fiókkal és titkos kulccsal is tud hitelesítést végezni a kiszolgálón.

## <a id="tunnel"></a>SSH-bújtatás

Az SSH-val helyi kérések, például webes kérések bújtatását is elvégezheti a HDInsight-fürthöz. A rendszer ilyenkor úgy irányítja a kérést a kért erőforráshoz, mintha a HDInsight-fürt átjárócsomópontból származna.

> [!IMPORTANT]
> Az SSH-alagút előfeltétele annak, hogy el tudja érni néhány Hadoop-szolgáltatás webes felhasználói felületét. Például a Feladatelőzmények felhasználói felület és az Erőforrás-kezelő felhasználói felület is csak SSH-alagúton keresztül érhető el.

Az SSH-alagutak létrehozásáról és használatáról szóló további információkért lásd: [Use SSH Tunneling to access Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie, and other web UI's](hdinsight-linux-ambari-ssh-tunnel.md) (Az Ambari webes felhasználói felület, a ResourceManager, a JobHistory, a NameNode, az Oozie és egyéb webes felhasználói felületek elérése SSH-alagútkezeléssel).

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerkedett az SSH-kulccsal végzett hitelesítésről, arról is tájékozódhat, hogyan használhatja a MapReduce-t a HDInsight-alapú Hadoopban.

* [A Hive használata a HDInsightban](hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hdinsight-use-pig.md)
* [MapReduce-feladatok használata a HDInsightban](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

