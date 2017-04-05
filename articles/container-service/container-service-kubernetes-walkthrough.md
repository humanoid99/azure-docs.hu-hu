---
title: "Kubernetes-fürt gyors üzembe helyezése az Azure-ban | Microsoft Docs"
description: "Kubernetes-fürt üzembe helyezése az Azure Container Service-ben és az első lépések"
services: container-service
documentationcenter: 
author: anhowe
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: anhowe
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e4f47341554e2de514c8be2f5c85983d09bbb760
ms.lasthandoff: 04/03/2017

---

# <a name="get-started-with-a-kubernetes-cluster-in-container-service"></a>Első lépések a Kubernetes-fürtökkel a Container Service-ben


A cikk útmutatásai bemutatják, hogyan használhatja az Azure CLI 2.0 parancsait egy Kubernetes-fürt létrehozásához. Ezután a `kubectl` parancssori eszközzel elkezdheti a fürtben lévő tárolók használatát.

A következő kép egy tárolószolgáltatási fürt architektúráját mutatja be, amely egy főkiszolgálóval és két ügynökkel rendelkezik: A főcsomópont a Kubernetes REST API-t szolgálja ki. Az ügyfélcsomópontok egy Azure-beli rendelkezésre állási csoportba vannak besorolva, és a tárolókat futtatják. Minden virtuális gép ugyanazon a privát virtuális hálózaton található, és teljes mértékben elérhetők egymás számára.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-walkthrough/kubernetes.png)

## <a name="prerequisites"></a>Előfeltételek
Az útmutatás feltételezi, hogy telepítette és beállította a következőt: [Azure CLI 2.0](/cli/azure/install-az-cli2). SSH RSA nyilvános kulccsal is rendelkeznie kell a következő helyen: `~/.ssh/id_rsa.pub`. Ha nincs ilyen kulcsa, tekintse meg az [OS X és Linux](../virtual-machines/linux/mac-create-ssh-keys.md) vagy a [Windows](../virtual-machines/linux/ssh-from-windows.md) rendszerhez készült útmutató lépéseit.






## <a name="create-your-kubernetes-cluster"></a>Kubernetes-fürt létrehozása

Itt rövid rendszerhéjparancsokat talál a fürt Azure CLI 2.0-val történő létrehozásához. További információ: [Az Azure CLI 2.0 használata Azure Container Service-fürtök létrehozására](container-service-create-acs-cluster-cli.md).

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
A fürt létrehozásához először létre kell hoznia egy erőforráscsoportot egy adott helyen. Futtasson az alábbihoz hasonló parancsokat:

```console
RESOURCE_GROUP=my-resource-group
LOCATION=westus
az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="create-a-cluster"></a>Fürt létrehozása
Ha rendelkezik erőforráscsoporttal, létrehozhat benne egy fürtöt:

```console
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name
az acs create --orchestrator-type=kubernetes --resource-group $RESOURCE_GROUP --name=$CLUSTER_NAME --dns-prefix=$DNS_PREFIX
```

> [!NOTE]
> Az üzembe helyezés közben a CLI feltölti az `~/.ssh/id_rsa.pub` fájlt a linuxos virtuális gépekre.
>

A parancs végrehajtása után a Kubernetes-fürttel működik.

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A következőkben olyan Azure CLI-parancsokat láthat, amelyekkel a Kubernetes-fürthöz csatlakozhat a számítógépéről a Kubernetes `kubectl` nevű parancssori ügyfelével. Több információ: [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md).

Ha a `kubectl` még nincs telepítve, a következővel telepítheti:

```console
az acs kubernetes install-cli
```

A `kubectl` telepítése után az alábbi parancs futtatásával töltheti le a fő Kubernetes-fürt konfigurációját a ~/.kube/config fájlba:

```console
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

Most már készen áll arra, hogy hozzáférjen a fürthöz a gépéről. Próbálja meg futtatni a következőt:
```console
kubectl get nodes
```

Ellenőrizze, hogy látja-e a fürtben található gépek listáját.

## <a name="create-your-first-kubernetes-service"></a>Az első Kubernetes-szolgáltatás létrehozása

Az oktatóanyag végére elsajátíthatja a következőket:
 * Dockler-alkalmazások üzembe helyezése és elérhetővé tétele
 * a `kubectl exec` használata parancsok futtatására egy tárolóban, 
 * hozzáférés a Kubernetes-irányítópulthoz

### <a name="start-a-simple-container"></a>Egyszerű tároló indítása
Az egyszerű tárolókat (ebben az esetben az Nginx webkiszolgálót) a következővel futtathatja:

```console
kubectl run nginx --image nginx
```

Ez a parancs elindítja az Nginx Docker-tárolót az egyik csomópont valamelyik podján.

A futó tároló megtekintéséhez futtassa a következőt:

```console
kubectl get pods
```

### <a name="expose-the-service-to-the-world"></a>A szolgáltatás elérhetővé tétele
A szolgáltatás elérhetővé tételéhez egy `LoadBalancer` típusú Kubernetes-`Service` létrehozása szükséges:

```console
kubectl expose deployments nginx --port=80 --type=LoadBalancer
```

Emiatt a Kubernetes létrehoz egy nyilvános IP-címmel rendelkező Azure Load Balancer-szabályt. A rendszer néhány percen belül propagálja a módosítást a terheléselosztóra. További információ: [Tárolók terheléselosztása Kubernetes-fürtön az Azure Container Service-ben](container-service-kubernetes-load-balancing.md).

Ha látni szeretné, ahogy a szolgáltatás `pending` állapotról átvált egy külső IP-cím megjelenítésére, futtassa a következő parancsot:

```console
watch 'kubectl get svc'
```

  ![A függőben állapotról külső IP-címre való váltás megtekintését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx3.png)

Ha már látja a külső IP-címet, megnyithatja a böngészőben:

  ![Az Nginx keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/kubernetes-nginx4.png)  


### <a name="browse-the-kubernetes-ui"></a>Böngészés a Kubernetes felhasználói felületén
A Kubernetes webes felületének megtekintéséhez a következőt használhatja:

```console
kubectl proxy
```
Ez egy egyszerű hitelesített proxyt futtat a localhoston, amellyel megtekintheti a [http://localhost:8001/ui](http://localhost:8001/ui) helyen futó Kubernetes webes felhasználói felületét. További információ: [A Kubernetes webes felhasználói felületének használata az Azure Container Service-szel](container-service-kubernetes-ui.md).

![Kubernetes-irányítópult képe](media/container-service-kubernetes-walkthrough/kubernetes-dashboard.png)

### <a name="remote-sessions-inside-your-containers"></a>Távoli munkamenetek a tárolókon belül
A Kubernetes lehetővé teszi a parancsok futtatását a fürtben futó távoli Docker-tárolókban.

```console
# Get the name of your nginx pods
kubectl get pods
```

A podnév használatával távoli parancsokat futtathat a podján.  Példa:

```console
kubectl exec <pod name> date
```

Az `-it` jelölőkkel teljesen interaktív munkamenetet indíthat:

```console
kubectl exec <pod name> -it bash
```

![Távoli munkamenet tárolón belül](media/container-service-kubernetes-walkthrough/kubernetes-remote.png)



## <a name="next-steps"></a>Következő lépések

A Kubernetes-fürt használatának további módjaiért tekintse meg a következő forrásanyagokat:

* [Kubernetes Bootcamp](https://katacoda.com/embed/kubernetes-bootcamp/1/) – bemutatja, hogyan lehet üzembe helyezni, méretezni és frissíteni a tárolóalapú alkalmazásokat, illetve hibakeresést végezni rajtuk.
* [Kubernetes felhasználói útmutató](http://kubernetes.io/docs/user-guide/) – információkat tartalmaz a programok meglévő Kubernetes-fürtökben való futtatásáról.
* [Kubernetes-példák](https://github.com/kubernetes/kubernetes/tree/master/examples) – példákkal szolgál a valódi alkalmazások Kubernetes használatával való futtatására.

