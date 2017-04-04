---
title: "A fejlesztési környezet beállítása Mac OS X-en | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások Mac OS X-en történő létrehozására."
services: service-fabric
documentationcenter: java
author: saysa
manager: raunakp
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/28/2017


---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>A fejlesztési környezet beállítása Mac OS X-en
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Linux-fürtökön futó Service Fabric-alkalmazásokat hozhat létre a Mac OS X-en. Ez a cikk bemutatja, hogyan állíthatja be Mac gépét a fejlesztéshez.

## <a name="prerequisites"></a>Előfeltételek
A Service Fabric nem fut natív módon az OS X-en. A helyi Service Fabric-fürt futtatásához egy Vagrant és VirtualBox alkalmazásokat használó, előre konfigurált Ubuntu-virtuális gépet biztosítunk. A kezdés előtt a következőkre lesz szüksége:

* [Vagrant (1.8.4-es vagy újabb verzió)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> A Vagrant és a VirtualBox kölcsönösen támogatott verzióját kell használnia. A Vagrant kiszámíthatatlan módon viselkedhet egy nem támogatott VirtualBox-verzión.
>

## <a name="create-the-local-vm"></a>A helyi virtuális gép létrehozása
Egy 5 csomópontos Service Fabric-fürtöt tartalmazó virtuális gép létrehozásához hajtsa végre a következő lépéseket:

1. A `Vagrantfile` adattár klónozása

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Ezzel a lépéssel lekéri a virtuálisgép-konfigurációt tartalmazó `Vagrantfile` fájlt valamint azt a helyet, ahonnan a virtuális gép le lett töltve.

   
2. Navigáljon az adattár helyi klónjához

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Nem kötelező) Módosítsa a virtuális gép alapértelmezett beállításait

    Alapértelmezés szerint a helyi virtuális gép konfigurációja a következő:

   * 3 GB lefoglalt memória
   * A 192.168.50.50 IP-címre konfigurált privát gazdagép-hálózat, amely átengedi a Mac-gazdagépről érkező forgalmat

     A `Vagrantfile`-lal bármelyik beállítást módosíthatja, és más konfigurációkat adhat a virtuális géphez. A konfigurációs beállítások teljes listájáért tekintse meg a [Vagrant dokumentációját](http://www.vagrantup.com/docs).
4. Virtuális gép létrehozása

    ```bash
    vagrant up
    ```

   Ebben a lépésben megtörténik az előre konfigurált virtuálisgép-rendszerkép letöltése, helyi indítása, és végül egy helyi Service Fabric-fürt beállítása. Ez eltarthat néhány percig. Ha a beállítás sikeres, a kimenetben megjelenik egy üzenet, amely jelzi, hogy a fürt indítása folyamatban van.

    ![A fürt beállításának megkezdése a virtuális gép kiépítése után][cluster-setup-script]

>[!TIP]
> Ha a virtuális gép letöltése hosszú ideig tart, letöltheti a wget vagy a curl használatával, vagy egy böngészőből a `Vagrantfile` fájlban található **config.vm.box_url** által megadott hivatkozásra lépve. Miután letöltötte helyileg, szerkessze a `Vagrantfile`-t, hogy arra a helyi elérési útra mutasson, ahová a rendszerképet letöltötte. Ha a rendszerképet például a /home/users/test/azureservicefabric.tp8.box helyre töltötte le, állítsa a **config.vm.box_url** címet erre az elérési útra.
>

5. Keresse fel a Service Fabric Explorert a http://192.168.50.50:19080/Explorer címen (feltéve, hogy megtartotta az alapértelmezett magánhálózati IP-címet) a fürt megfelelő beállításának teszteléséhez.

    ![A Service Fabric Explorer a Mac gazdagépről megtekintve][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Az Eclipse Neonhoz készült Service Fabric beépülő modul telepítése

A Service Fabric egy beépülő modult biztosít a **Java Eclipse Neon IDE-hez**, amely leegyszerűsítheti a Java-szolgáltatások összeállításának, létrehozásának és üzembe helyezésének folyamatát. A Service Fabric Eclipse beépülő modul telepítéséhez és frissítéséhez érdemes az ebben az általános [dokumentációban](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) szereplő telepítési lépéseket követnie.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>A Service Fabric Eclipse beépülő modul használata Mac gépen

Győződjön meg arról, hogy elvégezte a [Service Fabric Eclipse beépülő modul dokumentációjában](service-fabric-get-started-eclipse.md) szereplő lépéseket. Service Fabric Java-alkalmazások Mac-gazdagépen vagrant-vendégtároló használatával történő összeállításának, létrehozásának és üzembe helyezésének lépései nagyrészt megegyeznek az általános dokumentációban szereplőkkel, a következők kivételével:

* Mivel a Service Fabric Java-alkalmazáshoz szükségesek a Service Fabric-könyvtárak, az Eclipse-projektet megosztott elérési úton kell létrehozni. Alapméretezés szerint a ``Vagrantfile``-t tartalmazó gazdagép elérési útján található tartalmak a ``/vagrant`` elérési úton vannak megosztva a vendéggépen.
* Ha a ``Vagrantfile`` például a ``~/home/john/allprojects/`` elérési úton található, akkor a ``MyActor`` Service Fabric-projektet a ``~/home/john/allprojects/MyActor`` helyen kell létrehoznia, és az Eclipse-munkaterületének elérési útja a ``~/home/john/allprojects`` lesz.

## <a name="next-steps"></a>Következő lépések
<!-- Links -->
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren Yeoman használatával](service-fabric-create-your-first-linux-application-with-java.md)
* [Az első Service Fabric Java-alkalmazás létrehozása és üzembe helyezése Linux rendszeren az Eclipse Service Fabric beépülő modul használatával](service-fabric-get-started-eclipse.md)
* [Service Fabric-fürt létrehozása az Azure Portalon](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric-fürt létrehozása az Azure Resource Managerrel](service-fabric-cluster-creation-via-arm.md)
* [A Service Fabric-alkalmazásmodell megismerése](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

