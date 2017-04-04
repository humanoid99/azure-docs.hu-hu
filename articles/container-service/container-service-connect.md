---
title: "Csatlakozás Azure tárolószolgáltatási fürthöz | Microsoft Docs"
description: "Csatlakozás egy Kubernetes-, DC/OS- vagy Docker Swarm-fürthöz az Azure Container Service-ben egy távoli számítógépről"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 25a35912b72f7f44b83ccd49f298b44665318418
ms.lasthandoff: 03/28/2017


---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Távoli kapcsolat létrehozása egy Kubernetes-, DC/OS- vagy Docker Swarm-fürttel
Miután létrehozott egy Azure Container Service-fürtöt, csatlakoznia kell hozzá a számítási feladatok üzembe helyezéséhez és felügyeletéhez. Ez a cikk leírja, hogyan csatlakozhat a fürt fő virtuális gépéhez egy távoli számítógépről. 

A Kubernetes-, DC/OS- és Docker Swarm-fürtök helyi HTTP-végpontokat biztosítanak. A Kubernetes esetében ez a végpont biztonságosan van közzétéve az interneten, és az internethez csatlakozó bármely gépről elérhető a `kubectl` parancssori eszköz futtatásával. 

A DC/OS és a Docker Swarm esetében létre kell hoznia egy belső rendszerbe vezető Secure Shell- (SSH-) alagutat. Az alagút létrehozása után futtathat HTTP-végpontokat használó parancsokat, és megtekintheti a fürt webes felületét a helyi rendszerről. 


## <a name="prerequisites"></a>Előfeltételek

* Az [Azure Container Service](container-service-deployment.md)-ben üzembe helyezett Kubernetes-, DC/OS- vagy Docker Swarm-fürt.
* Titkos SSH RSA-kulcsfájl, amely a fürthöz az üzembe helyezéskor hozzáadott nyilvános kulcshoz tartozik. Ezek a parancsok feltételezik, hogy a titkos SSH-kulcs a következő helyen található a számítógépen: `$HOME/.ssh/id_rsa`. További információkat az [OS X és Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) rendszerekre vagy a [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) rendszerre vonatkozó útmutatókban találhat. Ha az SSH-kapcsolat nem működik, lehetséges, hogy [új SSH-kulcsot kell létrehoznia](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md).

## <a name="connect-to-a-kubernetes-cluster"></a>Csatlakozás Kubernetes-fürthöz

Kövesse a következő lépéseket a `kubectl` telepítéséhez és konfigurálásához.

> [!NOTE] 
> Előfordulhat, hogy Linux vagy OS X rendszereken a jelen szakaszban leírt parancsokat a `sudo` használatával kell futtatni.
> 

### <a name="install-kubectl"></a>A kubectl telepítése
Az eszköz telepítésének egyik módja az `az acs kubernetes install-cli` Azure CLI 2.0 parancs használata. A parancs futtatása előtt győződjön meg arról, hogy [telepítve van](/cli/azure/install-az-cli2) a legfrissebb Azure CLI 2.0, és hogy bejelentkezett egy Azure-fiókba (`az login`).

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

Másik lehetőségként letöltheti a legújabb ügyfelet közvetlenül a [Kubernetes-kiadások oldaláról](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md). További információ: [A kubectl telepítése és beállítása](https://kubernetes.io/docs/user-guide/prereqs/).

### <a name="download-cluster-credentials"></a>A fürt hitelesítő adatainak letöltése
A `kubectl` telepítését követően át kell másolnia a fürt hitelesítő adatait a gépre. A hitelesítő adatok beszerzésének egyik módja az `az acs kubernetes get-credentials` parancs használata. Továbbítsa az erőforráscsoport nevét és a tárolószolgáltatás erőforrásának nevét:


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

Ez a parancs letölti a fürt hitelesítő adatait a `$HOME/.kube/config` helyre, ahol a `kubectl` megtalálja majd.

Másik lehetőségként az `scp` használatával is biztonságosan átmásolhatja a fájlt a fő virtuális gép `$HOME/.kube/config` mappájából a helyi gépre. Példa:

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Windows rendszeren a Windowson futó Ubuntu Bash-környezetet, a PuTTy biztonságos fájlmásoló ügyfelét vagy egy hasonló eszközt kell használnia.



### <a name="use-kubectl"></a>Kubectl használata

A `kubectl` konfigurálását követően a fürt csomópontjainak listázásával tesztelheti a kapcsolatot:

```console
kubectl get nodes
```

Egyéb `kubectl` parancsokat is kipróbálhat. Például megtekintheti a Kubernetes irányítópultot. Először futtasson egy proxyt a Kubernetes API-kiszolgálóhoz:

```console
kubectl proxy
```

A Kubernetes felhasználói felülete a következő címen érhető el: `http://localhost:8001/ui`.

További információ: [A Kubernetes gyors üzembe helyezése](http://kubernetes.io/docs/user-guide/quick-start/).

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>Csatlakozás DC/OS- vagy Swarm-fürthöz

Az Azure Container Service által üzembe helyezett DC/OS- és Docker Swarm-fürtök használatához kövesse a Secure Shell- (SSH-) alagút helyi Linux, OS X vagy Windows rendszerről való létrehozására vonatkozó utasításokat. 

> [!NOTE]
> A jelen útmutatások elsősorban a TCP-forgalom SSH-n keresztüli bújtatására vonatkoznak. Interaktív SSH-munkamenetet az egyik belső fürtkezelő rendszerrel is indíthat, ez azonban nem ajánlott. A közvetlenül a belső rendszeren végzett munka növeli a konfiguráció véletlen módosításának kockázatát.  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>SSH-alagút létrehozása Linux vagy OS X rendszeren
Amikor Linux vagy OS X rendszeren hoz létre SSH-alagutat, először meg kell keresnie az elosztott terhelésű főkiszolgálók nyilvános DNS-nevét. Kövesse az alábbi lépéseket:


1. Az [Azure portálon](https://portal.azure.com) lépjen a tárolószolgáltatási fürtjét tartalmazó erőforráscsoporthoz. Bontsa ki az erőforráscsoportot, hogy az egyes erőforrások megjelenjenek. 

2. Kattintson a tárolószolgáltatás erőforrására, majd az **Áttekintés** elemre. Ekkor az **Alapvető erőforrások** területen megjelenik a fürt **teljes fő tartományneve**. Mentse ezt a nevet a későbbi felhasználásra. 

    ![Nyilvános DNS-név](media/pubdns.png)

    Másik lehetőségként futtassa az `az acs show` parancsot a tárolószolgáltatáson. Keresse meg a **Master Profile:fqdn** tulajdonságot a parancskimenetben.

3. Most nyisson meg egy kezelőfelületet, és futtassa az `ssh` parancsot a következő értékek megadásával: 

    a **LOCAL_PORT** az alagút szolgáltatásoldali TCP-portja, amelyhez csatlakozni kell. Swarm esetén állítsa ezt 2375 értékre. DC/OS esetén állítsa ezt 80 értékre.  
   a  **REMOTE_PORT** az elérhetővé tenni kívánt végpont portja. Swarm esetén használja a 2375-ös portot. DC/OS esetén használja a 80-as portot.  
   a  **USERNAME** a fürt telepítésekor megadott felhasználónév.  
   a  **DNSPREFIX** a fürt telepítésekor megadott DNS-előtag.  
   a  **REGION** az a régió, ahol az erőforráscsoport megtalálható.  
   a  **PATH_TO_PRIVATE_KEY** [NEM KÖTELEZŐ] a fürt létrehozásakor megadott nyilvános kulcshoz tartozó titkos kulcs elérési útja. Ezt a beállítást a `-i` jelzővel együtt kell használni.

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > Az SSH-kapcsolati port 2200, és nem a szokásos 22-es port. A több fő virtuális géppel rendelkező fürtökön ez az első fő virtuális gép kapcsolódási portja.
    > 



A DC/OS-re és a Swarmra vonatkozó példák a következő szakaszokban találhatók.    

### <a name="dcos-tunnel"></a>DC/OS-alagút
A DC/OS-végpontok alagútjának megnyitásához futtasson egy olyan parancsot, amely a következőhöz hasonló:

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> Beállíthat más, a 80-as porttól eltérő helyi portokat is (például a 8888-as portot). Ha azonban ezt a portot használja, előfordulhat, hogy egyes webes felhasználói felületek hivatkozásai nem működnek.

Most már elérheti a DC/OS-hez kapcsolódó végpontokat a helyi rendszerről, az alábbi URL-címeken keresztül (ha a 80-as helyi portot használja):

* DC/OS:`http://localhost:80/`
* Marathon:`http://localhost:80/marathon`
* Mesos:`http://localhost:80/mesos`

Ehhez hasonlóan az egyes alkalmazások REST API-jait is ezen az alagúton keresztül érheti el.

### <a name="swarm-tunnel"></a>Swarm-alagút
A Swarm-végponthoz vezető alagút megnyitásához futtasson egy olyan parancsot, amely a következőhöz hasonló:

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

Most beállíthatja a DOCKER_HOST környezeti változót az alábbi módon. A Docker parancssori felületét továbbra is a szokott módon használhatja.

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>SSH-alagút létrehozása Windows rendszeren
Windows-rendszeren az SSH-alagutak többféleképpen is létrehozhatók. Ez a témakör ismerteti, hogyan hozható létre az alagút a PuTTY használatával.

1. [Töltse le a PuTTY alkalmazást](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) Windows rendszerére.

2. Futtassa az alkalmazást.

3. Adjon meg egy állomásnevet, amely a fürt rendszergazdai felhasználónevéből és a fürt első főkiszolgálójának nyilvános DNS-nevéből áll. A **Host Name** (Gazdagép neve) a következőhöz hasonló: `adminuser@PublicDNSName`. A **Port** mezőben adja meg a 2200-as értéket.

    ![A PuTTY-konfigurálásának 1. lépése](media/putty1.png)

4. Válassza az **SSH > Auth** (SSH > Hitelesítés) parancsot. Adja meg a hitelesítéshez használandó titkos kulcsfájl (.ppk) elérési útját. Ez a fájl a [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) vagy egy hasonló eszköz segítségével hozható létre a fürt létrehozásakor használt SSH-kulcsból.

    ![A PuTTY-konfigurálásának 2. lépése](media/putty2.png)

5. Válassza az **SSH > Tunnels** (SSH > Alagutak) elemet, és konfigurálja az alábbi továbbított portokat:

    * **Source port** (Forrásport): DC/OS esetén használja a 80-as, Swarm estén a 2375-ös portot.
    * **Destination** (Cél): DC/OS esetén használja a localhost:80, Swarm esetén a localhost:2375 portot.

    Az alábbi példa DC/OS-re van konfigurálva, de Docker Swarm esetén is hasonló.

    > [!NOTE]
    > Amikor ezt az alagutat létrehozza, a 80-as port nem lehet használatban.
    > 

    ![A PuTTY-konfigurálásának 3. lépése](media/putty3.png)

6. Amikor elkészült, a **Session > Save** (Munkamenet > Mentés) paranccsal mentse a kapcsolat konfigurációját.

7. A PuTTY-munkamenethez az **Open** (Megnyitás) gombra kattintva csatlakozhat. A csatlakozás után a port konfigurációját a PuTTY eseménynaplójában tekintheti meg.

    ![A PuTTY eseménynaplója](media/putty4.png)

Miután az alagutat DC/OS-re konfigurálta, a kapcsolódó végpont a következő helyeken érhető el:

* DC/OS:`http://localhost/`
* Marathon:`http://localhost/marathon`
* Mesos:`http://localhost/mesos`

Miután az alagutat Swarmra konfigurálta, nyissa meg a Windows beállításait, és konfiguráljon egy `DOCKER_HOST` elnevezésű rendszerkörnyezeti változót a `:2375` értékkel. Ezután a Swarm-fürtöt a Docker parancssori felületén keresztül érheti el.

## <a name="next-steps"></a>Következő lépések
Tárolók telepítése és felügyelete a fürtben:

* [Az Azure Container Service és a Kubernetes használata](container-service-kubernetes-ui.md)
* [Az Azure Container Service és a DC/OS használata](container-service-mesos-marathon-rest.md)
* [Az Azure Container Service és a Docker Swarm használata](container-service-docker-swarm.md)


