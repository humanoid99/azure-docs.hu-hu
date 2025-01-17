---
title: "Kezelheti az alkalmazásokat az Azure Service Fabric több környezetek |} Microsoft Docs"
description: "Azure Service Fabric-alkalmazások is futtatható fürtökön tartományt mérete egyik gépről több ezer gép. Bizonyos esetekben érdemes állítsa be az alkalmazását eltérően ezeket változatos környezetekben. Ez a cikk bemutatja, hogyan adhat környezet egy másik alkalmazás paramétereit."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: mikhegn
ms.openlocfilehash: 959fdb4aceee48863f3914d1b91f2bec6e256d6b
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="manage-applications-for-multiple-environments"></a>Több környezet az alkalmazások kezelése

Az Azure Service Fabric-fürtök lehetővé teszik a hozzon létre egyet a sok ezer bárhol használva fürtök gépek. A legtöbb esetben veszi észre, magát, hogy az alkalmazás telepítése több fürtkonfigurációk között kellene: a helyi fejlesztési fürtöt, a megosztott fejlesztési és a termelési fürtön. Az összes ezeken a fürtökön a kódjának van futtatni különböző környezetek minősülnek. Bináris alkalmazásfájlokat módosítás nélkül futtathatja a széles skálája között, de gyakran konfigurálni szeretné az alkalmazás másképp.

Vegye figyelembe a két egyszerű példát:
  - a szolgáltatás egy meghatározott portot figyeli, de ezt a portot kell lennie a környezetek között van szüksége
  - meg kell adnia a másik kötés hitelesítő adatokat egy adatbázis a környezetek között

## <a name="specifying-configuration"></a>Konfiguráció megadása

Megadja a konfigurációs két kategóriába oszthatók:

- A szolgáltatások futtatásának módját vonatkozik-konfiguráció
  - Például a port számát a végpont vagy a szolgáltatás példányainak száma
  - Ez a konfiguráció van megadva, az alkalmazás vagy a service manifest-fájl
- Az alkalmazás kódjának alkalmazó konfiguráció
  - Egy adatbázis például kötési információ
  - Ez a konfiguráció megadható a konfigurációs fájlok vagy a környezeti változók

> [!NOTE]
> Nem minden tulajdonságot az alkalmazás és a service manifest fájl támogatási paraméterek.
> Ezekben az esetekben akkor támaszkodjon karakterláncok és a telepítési munkafolyamat részeként. A Visual Studio Team Services használható kiterjesztéssel, például a jogkivonatok cserélje le: https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens vagy a Jenkins futtatható a parancsfájl feladat. cserélje le az értékeket.
>

## <a name="specifying-parameters-during-application-creation"></a>Alkalmazás létrehozása során paramétereinek megadása

A Service Fabric egy elnevezett alkalmazáspéldányok létrehozásakor lehetősége van a paraméterek adjon át. Ennek módja attól függ, az alkalmazáspéldány létrehozásában.

  - A PowerShell a [ `New-ServiceFabricApplication` ](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) parancsmag egy kivonattáblát a alkalmazás paraméterek fogadja el.
  - Sfctl, használja a [ `sfctl application create` ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) parancs paraméterek fogadja el a JSON-karakterláncban. A install.sh parancsfájl sfctl használja.
  - A Visual Studio tesz lehetővé a paraméter fájlokat a projektet, a paraméterek mappában. A Visual Studio, a Visual Studio Team szolgáltatás vagy a Team Foundation Server közzététele a paraméter-fájlok esetén használatos. A Visual Studio a paraméter fájlok vannak folyamatban átadja a telepítés-FabricApplication.ps1 parancsfájl.

## <a name="next-steps"></a>További lépések
A következő cikkekben megmutatja, hogyan használja azokat a fogalmakat, lásd itt:

- [A Service Fabric szolgáltatások környezeti változók megadása](service-fabric-how-to-specify-port-number-using-parameters.md)
- [A port számát, a paraméterek használatával a Service Fabric szolgáltatás megadása](service-fabric-how-to-specify-environment-variables.md)
- [Hogyan kell konfigurációs fájlok paraméterezni](service-fabric-how-to-parameterize-configuration-files.md)

- [Környezeti változó hivatkozás](service-fabric-environment-variables-reference.md)
