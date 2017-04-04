---
title: "Az Azure mikroszolgáltatások fejlesztési környezetének kialakítása | Microsoft Docs"
description: "Telepítse a futtatókörnyezetet, az SDK-t és az eszközöket, majd hozzon létre egy helyi fejlesztési fürtöt. A beállítás befejezése után készen áll az alkalmazások létrehozására."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: ryanwi, mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 91049ee124999269a326106cbd6fc211cc027d26
ms.lasthandoff: 03/22/2017


---
# <a name="prepare-your-development-environment"></a>A fejlesztőkörnyezet előkészítése
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

 Az [Azure Service Fabric-alkalmazásoknak][1] a fejlesztői gépen való létrehozásához és futtatásához telepítse a futtatókörnyezetet, az SDK-t és az eszközöket. Továbbá engedélyeznie kell az SDK-ban található Windows PowerShell-parancsfájlok végrehajtását.

## <a name="prerequisites"></a>Előfeltételek
### <a name="supported-operating-system-versions"></a>Támogatott operációsrendszer-verziók
A fejlesztéshez a következő operációsrendszer-verziók támogatottak:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Alapértelmezés szerint a Windows 7 csak a Windows PowerShell 2.0-t tartalmazza. A Service Fabric PowerShell-parancsmagokhoz a PowerShell 3.0 vagy újabb verziója szükséges. A [Windows PowerShell 5.0-t letöltheti][powershell5-download] a Microsoft letöltőközpontból.
> 
> 

## <a name="install-the-sdk-and-tools"></a>Az SDK és az eszközök telepítése
### <a name="to-use-visual-studio-2017"></a>A Visual Studio 2017 használata
A Service Fabric-eszközök a Visual Studio 2017 Azure Development and Management Workload munkafolyamatának részét képezik. A Visual Studio telepítésének részeként engedélyezze ezt a munkafolyamatot.
Emellett telepítenie kell a Microsoft Azure Service Fabric SDK-t is a webplatform-telepítővel.

* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>A Visual Studio 2015 használata (Visual Studio 2015 2. frissítés vagy újabb szükséges)
A Visual Studio 2015 esetében a Service Fabric-eszközök az SDK-val együtt települnek a webplatform-telepítő használatával:

* [A Microsoft Azure Service Fabric SDK és eszközök telepítése][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Csak az SDK telepítése
Ha csak az SDK-ra van szükség, telepítse a következő csomagot:
* [A Microsoft Azure Service Fabric SDK telepítése][core-sdk]

> [!WARNING]
> Néhány ügyfél hibákat jelzett, amikor a telepítés során ezeket az indítási hivatkozásokat használták, vagy amikor a hivatkozásokat Chrome böngészőben használták. Ezek a webplatform-telepítő ismert hibái, amelyek javítása folyamatban van.  Próbálkozzon a következő áthidaló megoldásokkal:
>- Indítsa a fenti hivatkozásokat Internet Explorer vagy Edge böngészővel, vagy
>- Indítsa el a webplatform-telepítőt a Start menüből, keressen rá a „Service Fabric” kifejezésre, majd telepítse az SDK-t
> 
> Elnézést kérünk az okozott kellemetlenségekért. 

Az aktuális verziók a következők:
* Service Fabric SDK 2.5.216
* Service Fabric-futtatókörnyezet 5.5.216
* Visual Studio 2015-eszközök 1.5.50311.1

A támogatott verziók listáját lásd: [Service Fabric-támogatás](service-fabric-support.md).

## <a name="enable-powershell-script-execution"></a>A PowerShell-parancsfájl végrehajtásának engedélyezése
A Service Fabric Windows PowerShell-parancsfájlokat használ a helyi fejlesztési fürtök létrehozásához és az alkalmazások Visual Studióból történő üzembe helyezéséhez. Alapértelmezés szerint a Windows blokkolja ezen szkriptek futását. Az engedélyezésükhöz módosítania kell a PowerShell végrehajtási házirendjét. Nyissa meg a PowerShellt rendszergazdaként, és írja be a következő parancsot:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Következő lépések
Most, hogy végzett a fejlesztőkörnyezet beállításával, belefoghat az alkalmazások létrehozásába és futtatásába.

* [Az első Service Fabric-alkalmazás létrehozása a Visual Studióban](service-fabric-create-your-first-application-in-visual-studio.md)
* [Ismerje meg az alkalmazások helyi fürtön történő üzembe helyezését és kezelését](service-fabric-get-started-with-a-local-cluster.md)
* [További tudnivalók a programozási modellekről: Reliable Services és Reliable Actors](service-fabric-choose-framework.md)
* [A Service Fabric mintakódjainak megtekintése a GitHubon](https://aka.ms/servicefabricsamples)
* [A fürt megjelenítése a Service Fabric Explorer segítségével](service-fabric-visualizing-your-cluster.md)
* [A platform átfogó bemutatásának megtekintéséhez kövesse a Service Fabric képzési tervét](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "A Service Fabric kampányoldala"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI-hivatkozás"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI-hivatkozás"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI-hivatkozás"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395

