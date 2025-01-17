---
title: "Az Azure-on futó Kubernetes oktatóanyaga – Az ACR előkészítése"
description: "AKS-oktatóanyag – Az ACR előkészítése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fe5717b6dece6c6d03586045fb5fa0ccadd5e262
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="deploy-and-use-azure-container-registry"></a>Az Azure Container Registry üzembe helyezése és használata

Az Azure Container Registry (ACR) egy Azure-alapú privát regisztrációs adatbázis Docker-tárolórendszerképekhez. Ez az oktatóanyag, amely egy nyolcrészes sorozat második része, azt ismerteti, hogyan lehet üzembe helyezni egy Azure Container Registry-példányt, és hogyan lehet továbbítani rá egy tárolórendszerképet. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Egy Azure Container Registry- (ACR-) példány üzembe helyezése
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be

Az ezt követő oktatóanyagokban ezt az ACR-példányt integráljuk egy Kubernetes-fürttel az AKS-ben.

## <a name="before-you-begin"></a>Előkészületek

Az [előző lépésben][aks-tutorial-prepare-app] létrehoztunk egy tárolórendszerképet egy egyszerű Azure-szavazóalkalmazáshoz. Ha még nem hozta létre az Azure-szavazóalkalmazás rendszerképét, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

Az oktatóanyaghoz az Azure CLI 2.0.21-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="deploy-azure-container-registry"></a>Azure Container Registry üzembe helyezése

Az Azure Container Registry üzembe helyezéséhez először is szükség van egy erőforráscsoportra. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Ebben a példában egy `myResourceGroup` nevű erőforráscsoport jön létre a `eastus` régióban.

```azurecli
az group create --name myResourceGroup --location eastus
```

Hozzon létre egy Azure tárolóregisztrációs adatbázist az [az acr create][az-acr-create] paranccsal. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Az oktatóanyag hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét.

## <a name="container-registry-login"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Az [az acr login][az-acr-login] paranccsal jelentkezzen be az ACR-példányba. Meg kell adnia a tárolóregisztrációs adatbázis egyedi nevét, amelyet az adatbázis létrehozásakor adott meg.

```azurecli
az acr login --name <acrName>
```

A parancs a „Bejelentkezés sikeres” üzenetet adja vissza, ha befejeződött.

## <a name="tag-container-images"></a>Tárolórendszerképek címkézése

A meglévő rendszerképek listájának megtekintéséhez használja a [docker images][docker-images] parancsot.

```console
docker images
```

Kimenet:

```
REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front             latest              4675398c9172        13 minutes ago      694MB
redis                        latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        9 months ago        694MB
```

Minden tárolórendszerképet fel kell címkézni a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Ezt a címkét a rendszer az útválasztáshoz használja, amikor tárolórendszerképeket küld le egy regisztrációs adatbázisba.

A bejelentkezési kiszolgáló nevének lekéréséhez futtassa a következő parancsot.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Ezután címkézze fel az `azure-vote-front` rendszerképet a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Adja hozzá a `:v1` kifejezést a rendszerkép nevének végéhez. Ez a címke a rendszerkép verziószámát jelöli.

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Ha elkészült a címkézéssel, futtassa a [docker images][docker-images] parancsot a művelet ellenőrzéséhez.

```console
docker images
```

Kimenet:

```
REPOSITORY                                           TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                                     latest              eaf2b9c57e5e        8 minutes ago       716 MB
mycontainerregistry082.azurecr.io/azure-vote-front   v1            eaf2b9c57e5e        8 minutes ago       716 MB
redis                                                latest              a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask                           flask               788ca94b2313        8 months ago        694 MB
```

## <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

Küldje le az `azure-vote-front` rendszerképet a regisztrációs adatbázisba.

Az alábbi példában helyettesítse be az ACR bejelentkezési kiszolgálójának nevét az adott környezet bejelentkezési kiszolgálójának nevével.

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Ez eltarthat néhány percig.

## <a name="list-images-in-registry"></a>A regisztrációs adatbázisban található rendszerképek felsorolása

Az Azure Container Registrybe elküldött rendszerképek listájának lekéréséhez használja az [az acr repository list][az-acr-repository-list] parancsot. Frissítse a parancsot az ACR-példány nevével.

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```azurecli
Result
----------------
azure-vote-front
```

Ezután egy adott rendszerkép címkéinek megtekintéséhez használja az [az acr repository show-tags][az-acr-repository-show-tags] parancsot.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Kimenet:

```azurecli
Result
--------
v1
```

Az oktatóanyag végeztével a tárolórendszerképet egy privát Azure Container Registry-példány tárolja. Ezt a rendszerképet telepítjük az ACR-ből egy Kubernetes-fürtre a következő oktatóanyagok során.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban előkészítettünk egy Azure Container Registry tárolóregisztrációs adatbázist az AKS-fürtben való használatra. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Telepített egy Azure Container Registry-példányt
> * Címkézett egy tárolórendszerképet az ACR-hez
> * Feltöltötte a rendszerképet az ACR-be

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan helyezhető üzembe egy Kubernetes-fürt az Azure-ban.

> [!div class="nextstepaction"]
> [Kubernetes-fürt üzembe helyezése][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#create
[az-acr-login]: https://docs.microsoft.com/cli/azure/acr#az_acr_login
[az-acr-repository-list]: /cli/azure/acr/repository#list
[az-acr-repository-show-tags]: /cli/azure/acr/repository#show-tags
[az-group-create]: /cli/azure/group#az_group_create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md