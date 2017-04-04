---
title: "SSH használata a HDInsight (Hadoop) eszközzel Windows, Linux, Unix vagy OS X rendszerben | Microsoft Docs"
description: " A HDInsight a Secure Shell (SSH) segítségével érhető el. Ez a dokumentum információt nyújt arról, hogyan csatlakozhat SSH-val a HDInsighthoz Windows-, Linux-, Unix- vagy OS X-ügyfeleken."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/16/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 89d44476e9de8ac32195efaf66535cdd9fb4260e
ms.lasthandoff: 03/25/2017

---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Csatlakozás a HDInsighthoz (Hadoop) SSH-val

Megtudhatja, hogyan használhatja a [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) eszközt a HDInsighthoz való biztonságos csatlakozáshoz. A HDInsight használhatja a Linux (Ubuntu) rendszert a fürtben lévő csomópontok operációs rendszereként. SSH-val csatlakozhat egy Linux-alapú fürt átjáró- vagy élcsomópontjaihoz, és közvetlenül a csomópontokon futtathat parancsokat.

A következő tábla tartalmazza a szükséges cím- és portinformációkat, az SSH-val a HDInsighthoz történő csatlakozáshoz:

| Cím | Port | A következőhöz csatlakozik: |
| ----- | ----- | ----- |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Élcsomópont (ha van) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Elsődleges átjárócsomópont |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Másodlagos átjárócsomópont |

> [!NOTE]
> Cserélje le az `<edgenodename>` elemet az élcsomópont nevére. Az élcsomópontok használatával kapcsolatban további információért lásd: [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node).
>
> Cserélje le a `<clustername>` kifejezést a HDInsight-fürt nevére.
>
> Ajánlott __mindig az élcsomóponthoz csatlakozni__, ha van. Az átjárócsomópontok olyan szolgáltatásokat futtatnak, amelyek kritikus fontosságúak a fürt állapota szempontjából. Az élcsomópont csak azt futtatja, amit Ön telepít rá.

## <a name="ssh-clients"></a>SSH-ügyfelek

A legtöbb operációs rendszer biztosítja az `ssh`-ügyfelet. A Microsoft Windows alapértelmezés szerint nem biztosít SSH-ügyfelet. A következő csomagokban elérhető egy Windows rendszeren használható SSH-ügyfel:

* [Bash on Ubuntu on Windows 10](https://msdn.microsoft.com/commandline/wsl/about): Az `ssh` parancs a Bash on Windows parancssorából érhető el.

* [Git (https://git-scm.com/)](https://git-scm.com/): Az `ssh` parancs a GitBash parancssorából érhető el.

* [GitHub Desktop (https://desktop.github.com/)](https://desktop.github.com/) Az `ssh` parancs a Git Shell parancssorából érhető el. A GitHub Desktop konfigurálható a Bash, a Windows-parancssor vagy a PowerShell használatára a Git Shell parancssoraként.

* [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH): A PowerShell csapata portolja az OpenSSH-t Windows rendszerre, és tesztkiadásokat biztosít.

    > [!WARNING]
    > Az OpenSSH csomag tartalmazza az `sshd` SSH-kiszolgálóösszetevőt. Ez az összetevő elindít egy SSH-kiszolgálót a rendszerén, így mások csatlakozhatnak ahhoz. Ne konfigurálja ezt az összetevőt, és ne nyissa meg a 22-es portot, ha nem szeretne SSH-kiszolgálót futtatni a rendszerén. Ez nem szükséges a HDInsighttal való kommunikációhoz.

Több grafikus SSH-ügyfél is elérhető, például a [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) és a [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Ezekkel az ügyfelekkel csatlakozhat a HDInsighthoz, azonban a kiszolgálóhoz csatlakozás nem az `ssh` segédprogrammal történik. További információt az Ön által használt grafikus ügyfél dokumentációjában talál.

## <a id="sshkey"></a>Hitelesítés: SSH-kulcsok

Az SSH-kulcsok [nyilvános kulcsú titkosítással](https://en.wikipedia.org/wiki/Public-key_cryptography) védik a fürtöt. Az SSH-kulcsok biztonságosabbak, mint a jelszavak, és egyszerű módszert kínálnak a HDInsight-fürt védelmére.

Ha az SSH-fiókja kulccsal van védve, az ügyfélnek meg kell adnia az egyező titkos kulcsot, amikor csatlakozik:

* A legtöbb ügyfél konfigurálható __alapértelmezett kulcs__ használatára. Az `ssh`-ügyfél például a titkos kulcsot a `~/.ssh/id_rsa` helyen keresi Linux- és Unix-környezetekben.

* Megadhatja __egy titkos kulcs elérési útját__. Az `ssh`-ügyfél esetében az `-i` paraméterrel adható meg a titkos kulcs elérési útja. Például: `ssh -i ~/.ssh/hdinsight sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Ha __több titkos kulccsal__ rendelkezik, amelyeket különböző kiszolgálókhoz használ, segédprogramokkal, például az [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) eszközzel választhatja ki automatikusan a használni kívánt kulcsot.

> [!IMPORTANT]
>
> Ha jelszóval védi a titkos kulcsot, a kulcs használatakor be kell írnia a jelszót. Az `ssh-agent` és hasonló segédprogramokkal a kényelmes használat érdekében gyorsítótárazhatja a jelszót.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az `ssh-keygen` paranccsal hozhat létre nyilvános- és titkoskulcs-fájlokat. A következő parancs egy 2048 bites RSA-kulcspárt hoz létre, amely a HDInsighttal használható:

    ssh-keygen -t rsa -b 2048

A kulcs létrehozása során a rendszer információk megadását kéri. Például a kulcsok tárolási helyét kell megadnia, vagy azt, hogy használ-e jelszót. A folyamat befejezése után két fájl jön létre; egy nyilvános kulcs és egy titkos kulcs.

* A __nyilvános kulccsal__ hozható létre a HDInsight-fürt. A nyilvános kulcs kiterjesztése `.pub`.

* A __titkos kulccsal__ hitelesíthető az ügyfél a HDInsight-fürtön.

> [!IMPORTANT]
> A kulcsokat jelszóval védheti. Ez lényegében egy kód a titkos kulcson. Ilyen esetekben még ha valaki meg is szerzi a titkos kulcsát, a jelszóra is szüksége van a kulcs használatához.

### <a name="create-hdinsight-using-the-public-key"></a>HDInsight létrehozása a nyilvános kulccsal

| Létrehozási metódus | A nyilvános kulcs használata |
| ------- | ------- |
| **Azure Portal** | Törölje a __Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor__ jelölőnégyzet jelölését, majd válassza a __Nyilvános kulcs__ elemet az SSH-hitelesítés típusaként. Végül válassza ki a nyilvános kulcs fájlját, vagy illessze be a fájl szöveges tartalmát a __Nyilvános SSH-kulcs__ mezőbe.</br>![Nyilvános SSH-kulcs párbeszédpanel a HDInsight-fürt létrehozásakor](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | A `New-AzureRmHdinsightCluster` parancsmag `-SshPublicKey` paraméterével illesztheti be a nyilvános kulcs tartalmát karakterláncként.|
| **Azure CLI 1.0** | Az `azure hdinsight cluster create` parancs `--sshPublicKey` paraméterével illesztheti be a nyilvános kulcs tartalmát karakterláncként. |
| **Resource Manager-sablon** | Az SSH-kulcsok sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) fájlban lévő `publicKeys` elemmel illeszthetők be a kulcsok az Azure-ba a fürt létrehozásakor. |

## <a id="sshpassword"></a>Hitelesítés: Jelszó

Az SSH-fiókok jelszóval védhetők. Amikor SSH-fiókkal csatlakozik a HDInsighthoz, a rendszer jelszót kér.

> [!WARNING]
> Nem ajánlott jelszavas hitelesítést használni az SSH-hoz. A jelszavakat ki lehet találni, és védtelenek a találgatásos támadásokkal szemben. Ehelyett azt javasoljuk, hogy használjon [SSH-kulcsokat a hitelesítéshez](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>HDInsight létrehozása jelszóval

| Létrehozási metódus | Jelszó megadása |
| --------------- | ---------------- |
| **Azure Portal** | Alapértelmezés szerint az SSH-felhasználói fióknak ugyanaz a jelszava, mint a fürt bejelentkezési fiókjának. Ha más jelszót szeretne használni, törölje a __Használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor__ jelölőnégyzet jelölését, majd írja be a jelszót az __SSH-jelszó__ mezőbe.</br>![SSH-jelszó párbeszédpanel a HDInsight-fürt létrehozásakor](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | A `New-AzureRmHdinsightCluster` parancsmag `--SshCredential` paraméterével illessze be az SSH-felhasználói fiók nevét és jelszavát tartalmazó `PSCredential` objektumot. |
| **Azure CLI 1.0** | Az `azure hdinsight cluster create` parancs `--sshPassword` paraméterével adja meg a jelszó értékét. |
| **Resource Manager-sablon** | A jelszavak sablonnal történő használatának példájáért tekintse meg a [HDInsight Linux rendszeren, SSH-kulccsal való telepítéséről](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) szóló témakört. Az [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) fájlban lévő `linuxOperatingSystemProfile` elemmel illeszthető be az SSH-fióknév és -jelszó az Azure-ba a fürt létrehozásakor.|

### <a name="change-the-ssh-password"></a>Az SSH-jelszó módosítása

Az SSH-felhasználói fiók jelszavának módosításával kapcsolatos információért tekintse meg a [HDInsight kezelése](hdinsight-administer-use-portal-linux.md#change-passwords) dokumentum __Jelszavak módosítása__ szakaszát.

## <a id="domainjoined"></a>Hitelesítés: Tartományhoz csatlakoztatott HDInsight

Ha __tartományhoz csatlakoztatott HDInsight-fürtöt__ használ, a `kinit` parancsot kell használnia az SSH-hoz való kapcsolódás után. Ez a parancs tartományi felhasználónevet és jelszót kér, és hitelesíti a munkamenetet a fürttel társított Azure Active Directory-tartománnyal.

További információkat itt talál: [Tartományhoz csatlakoztatott HDInsight konfigurálása](hdinsight-domain-joined-configure.md).

## <a name="connect-to-worker-and-zookeeper-nodes"></a>Csatlakozás a feldolgozó és Zookeeper-csomópontokhoz

A feldolgozó csomópontok és a Zookeeper-csomópontok nem érhetők el közvetlenül az internetről, de a fürt átjárócsomópontjaiból vagy élcsomópontjaiból el lehet őket érni. A következő általános lépésekkel csatlakozhat más csomópontokhoz:

1. Az SSH-val csatlakozzon egy átjáró- vagy élcsomóponthoz:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. Az átjáró- vagy élcsomópont felé fennálló SSH-kapcsolatból használja az `ssh` parancsot a fürt egyik munkavégző csomópontjához való csatlakozáshoz:

        ssh sshuser@wn0-myhdi

    A fürtben található csomópontok tartománynevei listájának lekéréséhez tekintse meg a [HDInsight az Ambari REST API-val való kezelését](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) ismertető dokumentumban lévő példákat.

Ha az SSH-fiókot __jelszó__ védi, a rendszer megkéri, hogy adja meg a jelszót, és létrejön a kapcsolat.

Ha az SSH-fiók __SSH-kulcsokkal__ védett, győződjön meg arról, hogy a helyi környezet SSH-ügynöktovábbításra van konfigurálva.

> [!NOTE]
> A fürtben lévő összes csomópont közvetlen elérésének másik módja, ha a HDInsightot Azure virtuális hálózatra telepíti. Ezután csatlakoztathatja a távoli gépet ugyanehhez a virtuális hálózathoz, és közvetlenül érheti el a fürtben lévő összes csomópontot.
>
> További információ: [Virtuális hálózat használata HDInsighttal](hdinsight-extend-hadoop-virtual-network.md).

### <a name="configure-ssh-agent-forwarding"></a>SSH-ügynöktovábbítás konfigurálása

> [!IMPORTANT]
> Az alábbi lépések Linux-/UNIX-alapú rendszert feltételeznek, és a Bash on Windows 10 esetén működnek. Ha a lépések nem működnek a rendszerén, lehet, hogy át kell tekintenie az SSH-ügyfél dokumentációját.

1. Egy szövegszerkesztővel nyissa meg a `~/.ssh/config` fájlt. Ha a fájl nem létezik, létrehozhatja a parancssoron az `touch ~/.ssh/config` karakterlánc beírásával.

2. Adja hozzá a következő szöveget az `config` fájlhoz.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Cserélje le a __Gazda__ információit azon csomópont címére, amelyet SSH-val csatlakoztat. Az előző példa az élcsomópontot használja. Ez a bejegyzés konfigurálja az SSH-ügynöktovábbítást az adott csomópont számára.

3. Tesztelje az SSH-ügynöktovábbítást a terminálból a következő parancs segítségével:

        echo "$SSH_AUTH_SOCK"

    Ez a parancs az alábbi szöveghez hasonló információt ad vissza:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Ha a parancs nem ad vissza semmit, a(z) `ssh-agent` nem fut. Az ügynök indítási parancsfájljaival kapcsolatos információkért tekintse meg a [Using ssh-agent with ssh (Az ssh-agent és az ssh együttes használata – http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) című cikket, vagy olvassa el az SSH-ügynök dokumentációját az `ssh-agent` telepítésének és konfigurálásának részletes lépéseiért.

4. Ha meggyőződött róla, hogy az **ssh-agent** fut, a következő segítségével adja hozzá a titkos SSH-kulcsot az ügynökhöz:

        ssh-add ~/.ssh/id_rsa

    Ha a titkos kulcsot egy másik fájl tárolja, a `~/.ssh/id_rsa` részt cserélje ki a fájl elérési útjára.

5. Csatlakozzon a fürt élcsomópontjához vagy átjárócsomópontjaihoz SSH-val. Ezután az SSH-paranccsal csatlakozzon egy feldolgozó vagy Zookeeper-csomóponthoz. A kapcsolat létrejön a továbbított kulccsal.

## <a name="next-steps"></a>Következő lépések

* [SSH-alagútkezelés használata a HDInsighttal](hdinsight-linux-ambari-ssh-tunnel.md)
* [Virtuális hálózat használata a HDInsighttal](hdinsight-extend-hadoop-virtual-network.md)
* [Élcsomópontok használata a HDInsightban](hdinsight-apps-use-edge-node.md#access-an-edge-node)
