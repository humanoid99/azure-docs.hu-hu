---
title: "Privát Docker-tárolójegyzékek az Azure-ban | Microsoft Docs"
description: "Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: dd504c95e22d322707c55818815b09d8a36c7ca4
ms.lasthandoff: 03/24/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>A privát Docker-tárolójegyzékek bemutatása


Az Azure Container Registry egy felügyelt [Docker jegyzékszolgáltatás](https://docs.docker.com/registry/), amely a nyílt forráskódú Docker Registry 2.0 technológiára épül. Az Azure-beli tároló-beállításjegyzékek létrehozásával és fenntartásával tárolhatja és kezelheti privát [Docker-tárolóinak](https://www.docker.com/what-docker) rendszerképeit. Az Azure-beli tároló-beállításjegyzékeit meglévő tárolófejlesztési és üzembe helyezési folyamataival együtt használhatja, és közben számíthat a Docker közösség szakértelmére.

A Dockerrel és a tárolókkal kapcsolatos háttérinformációk:

* [Docker felhasználói útmutató](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>Használati esetek
Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) és [Kubernetes](http://kubernetes.io/docs/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve a [Container Service](../container-service/index.md), az [App Service](/app-service/index.md), a [Batch](../batch/index.md) és a [Service Fabric](../service-fabric/index.md) szolgáltatást.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy olyan folyamatos integrációs és üzembe helyezési eszközből, mint a [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) vagy a [Jenkins](https://jenkins.io/).





## <a name="key-concepts"></a>Fő fogalmak
* **Beállításjegyzék** – Létrehozhat egy vagy több tároló-beállításjegyzéket Azure-előfizetésében. Mindegyik beállításjegyzék egy, ugyanazon a helyen található standard Azure [Storage-fiókra](../storage/storage-introduction.md) épül. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit.

  A beállításjegyzékek a gyökértartományban az előfizetés [Azure Active Directory-bérlője](../active-directory/active-directory-howto-tenant.md) alapján vannak elnevezve. Ha például egy szervezeti fiókkal rendelkezik a Contoso tartományban, a teljes tartománynév `myregistry-contoso.azurecr.io` formában lesz.

  A tároló-beállításjegyzékhez való [hozzáférés szabályozása](container-registry-authentication.md) egy, az Azure Active Directory által támogatott [egyszerű szolgáltatással](../active-directory/active-directory-application-objects.md) vagy a rendszergazdai fiókkal lehetséges. A beállításjegyzéken való hitelesítéshez futtassa a szabványos `docker login` parancsokat.

* **Tár** – A beállításjegyzékek egy vagy több tárat tartalmaznak, amelyek tárolórendszerképek csoportjai. Az Azure Container Registry támogatja a többszintű adattárnévtereket. Ezzel a szolgáltatással egy adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit csoportba rendezheti az egyes fejlesztői és üzemeltetői csoportok számára. Példa:

  * A(z) `myregistry.azurecr.io/aspnetcore:1.0.1` egy, a teljes vállalatban elérhető rendszerképet jelöl
  * A(z) `myregistry.azurecr.io/warrantydept/dotnet-build` egy .NET-alkalmazások felépítéséhez használt rendszerképet jelöl, amely a jótállási részlegen van megosztva
  * A(z) `myregistry.azrecr.io/warrantydept/customersubmissions/web` egy, az ügyfélbeadványok alkalmazásban csoportosított webes rendszerképet jelöl, amely a jótállási részleg tulajdona

* **Rendszerkép** – Az adattárakban tárolt egyes rendszerképek egy-egy Docker-tároló csak olvasható pillanatfelvételei. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan.

* **Tároló** – A tároló egy szoftveralkalmazást határoz meg annak függőségeivel együtt egy teljes fájlrendszerbe csomagolva, beleértve a kódot, a futtatókörnyezetet, a rendszereszközöket és a könyvtárakat. A Docker-tárolókat a tároló-beállításjegyzékekből előhívott Windows- vagy Linux-rendszerképek alapján futtathatja. Az egy gépen futó tárolók osztoznak az operációs rendszer kernelén. A Docker-tárolók teljes mértékben portolhatók az összes nagyobb Linux-disztribúcióra, Macre és Windowsra is.




## <a name="next-steps"></a>Következő lépések
* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)
* Egy folyamatos integrációt és üzembe helyezést megvalósító munkafolyamat a Visual Studio Team Services, az Azure Container Service és az Azure Container Registry használatával történő kiépítéséhez lásd [ezt az oktatóanyagot](../container-service/container-service-setup-ci-cd.md).
* Ha privát Docker-beállításjegyzéket szeretne beállítani az Azure-ban (nyilvános végpont nélkül), tekintse meg [a saját privát Docker-beállításjegyzék Azure-beli üzembe helyezésével kapcsolatos](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md) cikket.

