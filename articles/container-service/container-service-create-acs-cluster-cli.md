---
title: "Docker-tárolófürt üzembe helyezése – Azure CLI | Microsoft Docs"
description: "Kubernetes-, DC/OS- vagy Docker Swarm-megoldás üzembe helyezése az Azure Container Service-ben az Azure CLI 2.0 használatával"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: saudas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1dad536939f179cd8231d0f8805c1ff4335850d5
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-cli-20"></a>Docker tárolóüzemeltetési megoldás üzembe helyezése az Azure CLI 2.0 használatával

Az Azure CLI 2.0 `az acs` parancsaival fürtöket hozhat létre és felügyelhet az Azure Container Service-ben. Az Azure Container Service-fürtöket az [Azure Portal](container-service-deployment.md) vagy az Azure Container Service API-k használatával is üzembe helyezheti.

A `az acs` parancsaival kapcsolatos segítségért adja át a `-h` paramétert bármelyik parancsnak. Például: `az acs create -h`.



## <a name="prerequisites"></a>Előfeltételek
Az Azure Container Service-fürtök az Azure CLI 2.0-s verziójával való létrehozásának feltételei:
* szükség van egy Azure-fiókra ([ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/));
* az [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítésére és beállítására

## <a name="get-started"></a>Bevezetés 
### <a name="log-in-to-your-account"></a>Bejelentkezés a fiókjába
```azurecli
az login 
```

Kövesse az utasításokat az interaktív bejelentkezéshez. A bejelentkezés egyéb módjaival kapcsolatban lásd: [Bevezetés az Azure CLI 2.0-s verziójának használatába](/cli/azure/get-started-with-az-cli2).

### <a name="set-your-azure-subscription"></a>Azure-előfizetés beállítása

Ha több Azure-előfizetéssel rendelkezik, állítsa be az alapértelmezett előfizetést. Példa:

```
az account set --subscription "f66xxxxx-xxxx-xxxx-xxx-zgxxxx33cha5"
```


### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Javasoljuk, hogy mindegyik fürthöz hozzon létre egy erőforráscsoportot. Válasszon ki egy Azure-régiót, amelyben az Azure Container Service [elérhető](https://azure.microsoft.com/en-us/regions/services/). Példa:

```azurecli
az group create -n acsrg1 -l "westus"
```
A kimenet a következőkhöz hasonló:

![Hozzon létre egy erőforráscsoportot](media/container-service-create-acs-cluster-cli/rg-create.png)


## <a name="create-an-azure-container-service-cluster"></a>Azure Container Service-fürt létrehozása

Fürtöt létrehozásához használja a következőt: `az acs create`.
Egy fürt nevét és az előző lépésben létrehozott erőforráscsoportot kötelező paraméterként megadni. 

A többi bemenet alapértelmezett értékre van állítva (lásd a következő képernyőt), hacsak felül nem írták őket a megfelelő kapcsolókkal. A vezénylő például alapértelmezés szerint DC/OS értékre van állítva. Ha nem ad meg DNS-névelőtagot, a rendszer a fürt neve alapján létrehoz egyet.

![az acs create használata](media/container-service-create-acs-cluster-cli/create-help.png)


### <a name="quick-acs-create-using-defaults"></a>Gyors `acs create` alapértelmezett beállítások használatával
Ha az alapértelmezett helyen egy nyilvános SSH RSA-kulcsfájl `id_rsa.pub` található (vagy létrehozott egyet az [OS X és Linux](../virtual-machines/linux/mac-create-ssh-keys.md) vagy a [Windows](../virtual-machines/linux/ssh-from-windows.md) rendszerekhez), használjon a következőkhöz hasonló parancsot:

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789
```
Ha nincs nyilvános SSH-kulcsa, használja ezt a második parancsot. Ez a parancs a `--generate-ssh-keys` kapcsolóval létrehoz egyet.

```azurecli
az acs create -n acs-cluster -g acsrg1 -d applink789 --generate-ssh-keys
```

A parancs beírása után várjon körülbelül 10 percet a fürt létrehozására. A parancs kimenete tartalmazza a fő- és ügyfélcsomópontok teljes tartományneveit (FQDN), valamint egy SSH-parancsot az első főcsomóponthoz való kapcsolódáshoz. Íme egy rövidített kimenet:

![Kép az ACS create parancsról](media/container-service-create-acs-cluster-cli/cluster-create.png)

> [!TIP]
> A [Kubernetes-útmutató](container-service-kubernetes-walkthrough.md) bemutatja, hogyan használható az `az acs create` az alapértelmezett értékekkel Kubernetes-fürtök létrehozásához.
>

## <a name="manage-acs-clusters"></a>ACS-fürtök kezelése

A fürt kezeléséhez használjon további `az acs` parancsokat. Néhány példa:

### <a name="list-clusters-under-a-subscription"></a>Előfizetés alá tartozó fürtök listázása

```azurecli
az acs list --output table
```

### <a name="list-clusters-in-a-resource-group"></a>Erőforráscsoportba tartozó fürtök listázása

```azurecli
az acs list -g acsrg1 --output table
```

![acs list](media/container-service-create-acs-cluster-cli/acs-list.png)


### <a name="display-details-of-a-container-service-cluster"></a>Container Service-fürt részleteinek megjelenítése

```azurecli
az acs show -g acsrg1 -n acs-cluster --output list
```

![acs show](media/container-service-create-acs-cluster-cli/acs-show.png)


### <a name="scale-the-cluster"></a>A fürt átméretezése
Az ügyfélcsomópontok horizontális le- és felskálázása is engedélyezett. A `new-agent-count` paraméter az ACS-fürtben található ügyfelek új száma.

```azurecli
az acs scale -g acsrg1 -n acs-cluster --new-agent-count 4
```

![acs scale](media/container-service-create-acs-cluster-cli/acs-scale.png)

## <a name="delete-a-container-service-cluster"></a>Container Service-fürt törlése
```azurecli
az acs delete -g acsrg1 -n acs-cluster 
```
Ez a parancs nem törli a tárolószolgáltatás létrehozásakor létrehozott összes (hálózati és tárolási) erőforrást. Az összes erőforrás egyszerű törléséhez ajánlott minden egyes fürtöt külön erőforráscsoportban üzembe helyezni. Ezután törölje az erőforráscsoportot, ha a fürtre már nincs szükség.

## <a name="next-steps"></a>Következő lépések
Most, hogy működő fürtje van, tekintse meg ezeket a dokumentumokat a kapcsolatra és a felügyeletre vonatkozó részletekért:

* [Csatlakozás Azure Container Service-fürthöz](container-service-connect.md)
* [Az Azure Container Service és a DC/OS használata](container-service-mesos-marathon-rest.md)
* [Az Azure Container Service és a Docker Swarm használata](container-service-docker-swarm.md)
* [Az Azure Container Service és a Kubernetes használata](container-service-kubernetes-walkthrough.md)
