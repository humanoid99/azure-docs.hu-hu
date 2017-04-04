---
title: "Docker-rendszerképek leküldése a privát Azure-beállításjegyzékbe | Microsoft Docs"
description: "Docker-rendszerképek leküldése és lekérése egy Azure-beli privát tároló beállításjegyzékébe és -jegyzékéből a Docker parancssori felületével"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 64fbe43f-fdde-4c17-a39a-d04f2d6d90a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: b6c26f28aa1e574ba3aabe53eda359cb6bf2edcc
ms.lasthandoff: 03/27/2017

---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése egy privát Docker-tároló beállításjegyzékébe a Docker parancssori felületével
Az Azure-beli tároló-beállításjegyzékek privát [Docker](http://hub.docker.com)-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a [Docker Hub](https://hub.docker.com/) nyilvános Docker-rendszerképeket tárol. A[Docker parancssori felületével](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) végezheti a [bejelentkezést](https://docs.docker.com/engine/reference/commandline/login/), a [leküldést](https://docs.docker.com/engine/reference/commandline/push/), a [lekérést](https://docs.docker.com/engine/reference/commandline/pull/) és a tároló-beállításjegyzék további műveleteit.

Részletes háttér-információkért és a fogalmakkal kapcsolatban lásd [az áttekintést](container-registry-intro.md).



## <a name="prerequisites"></a>Előfeltételek
* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Ehhez például használhatja az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI 2.0-t](container-registry-get-started-azure-cli.md).
* **A Docker parancssori felülete** – Ha szeretné helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker parancssori felületének parancsait, telepítse a [Docker Engine-t](https://docs.docker.com/engine/installation/).

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe
Futtassa a(z) `docker login` parancsot a tároló-beállításjegyzékbe való bejelentkezéshez a [beállításjegyzékhez tartozó hitelesítő adataival](container-registry-authentication.md).

Az alábbi példában a rendszer egy Azure Active Directory [egyszerű szolgáltatás](../active-directory/active-directory-application-objects.md) azonosítóját és jelszavát adja át. Például lehet, hogy hozzárendelt egy egyszerű szolgáltatást a beállításjegyzékhez egy automatizálási forgatókönyvhöz.

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

> [!TIP]
> Ügyeljen arra, hogy megadja a teljes beállításjegyzéknevet (csak kisbetűvel). Ebben a példában ez `myregistry.azurecr.io`.

## <a name="steps-to-pull-and-push-an-image"></a>Rendszerképek leküldésének és lekérésének lépései
Az alábbi példa az Nginx rendszerképet tölti le a nyilvános Docker Hub-jegyzékből, címkézi azt a privát, Azure-beli tárolójegyzékhez, leküldi a beállításjegyzékbe, majd újból lekéri.

**1. A Docker hivatalos Nginx-rendszerképének lekérése**

Először kérje le a nyilvános Nginx-rendszerképet a helyi számítógépre.

```
docker pull nginx
```
**2. A Nginx-tároló indítása**

A következő paranccsal a helyi Nginx-tároló interaktívan indítható a 8080-as porton, így megjelenik az Nginx kimenete. Ha leállítja, azzal eltávolítja a futó tárolót.

```
docker run -it --rm -p 8080:80 nginx
```

Lépjen a [http://localhost:8080](http://localhost:8080) helyre a futó tároló megtekintéséhez. A következőhöz hasonló képernyő jelenik meg.

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

A futó tároló leállításához nyomja le a [CTRL]+[C] billentyűkombinációt.

**3. Rendszerkép aliasának létrehozása a beállításjegyzékben**

A következő parancs létrehoz egy aliast a rendszerképről a beállításjegyzékre mutató teljes elérési úttal. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```  

**4. A rendszerkép leküldése a beállításjegyzékbe**

```
docker push myregistry.azurecr.io/samples/nginx
```

**5. A rendszerkép lekérése a beállításjegyzékből**

```
docker pull myregistry.azurecr.io/samples/nginx
```

**6. A Nginx-tároló elindítása a beállításjegyzékből**

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Lépjen a [http://localhost:8080](http://localhost:8080) helyre a futó tároló megtekintéséhez.

A futó tároló leállításához nyomja le a [CTRL]+[C] billentyűkombinációt.

**7. (Nem kötelező) A rendszerkép eltávolítása**

```
docker rmi myregistry.azurecr.io/samples/nginx
```

##<a name="concurrent-limits"></a>Egyidejű korlátok
Bizonyos forgatókönyvekben a párhuzamos hívások végrehajtása hibát eredményezhet. Az alábbi tábla tartalmazza a párhuzamos hívások korlátait az Azure-beli tároló-beállításjegyzék „leküldés” és „lekérés” műveleteinél:

| Művelet  | Korlát                                  |
| ---------- | -------------------------------------- |
| LEKÉRÉS       | Legfeljebb 10 párhuzamos lekérés beállításjegyzékenként |
| LEKÜLDÉSES ÉRTESÍTÉSEK       | Legfeljebb 5 párhuzamos leküldés beállításjegyzékenként |

## <a name="next-steps"></a>Következő lépések
Most, hogy elsajátította az alapokat, készen áll a beállításjegyzéke használatára. Például üzembe helyezhet tárolórendszerképeket egy [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/)-fürtön.

