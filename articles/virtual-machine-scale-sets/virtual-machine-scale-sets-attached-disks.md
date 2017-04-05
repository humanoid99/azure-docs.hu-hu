---
title: "Azure-beli virtuálisgép-méretezési csoportok és csatlakoztatott adatlemezek | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhat csatlakoztatott adatlemezeket virtuálisgép-méretezési csoportokkal."
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/6/2017
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 91d36d5321f455a2af31093fa460ddf6640942d4
ms.lasthandoff: 03/31/2017


---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Azure-beli virtuálisgép-méretezési csoportok és csatlakoztatott adatlemezek
Az Azure-beli [virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets/) mostantól támogatják a csatlakoztatott adatlemezekkel rendelkező virtuális gépeket. Adatlemezeket az Azure Managed Disks használatával létrehozott méretezési csoportok tárolóprofiljában lehet definiálni. Korábban a méretezési csoportokban található virtuális gépekhez csak az operációs rendszer meghajtóját és az ideiglenes meghajtókat lehetett közvetlenül csatlakoztatni.

> [!NOTE]
>  Amikor olyan méretezési csoportot hoz létre, amely rendelkezik definiált csatlakoztatott adatlemezekkel, ugyanúgy csatlakoztatnia kell a lemezeket, és formáznia kell azokat a virtuális gépen belül a használatukhoz (akárcsak egy különálló Azure-beli virtuális gép esetén). Ez kényelmesen elvégezhető egy olyan szkriptbővítmény használatával, amely egy normál szkript hívásával particionálja, és formázza a virtuális gép összes adatlemezét.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Csatlakoztatott adatlemezekkel rendelkező méretezési csoport létrehozása
A csatlakoztatott adatlemezekkel rendelkező méretezési csoport létrehozásának egyik egyszerű módja az [Azure parancssori felület](https://github.com/Azure/azure-cli) _vmss create_ parancsának használata. A következő példa egy Azure-erőforráscsoportot és egy 10 Ubuntu-alapú virtuális gépből álló virtuálisgép-méretezési csoportot hoz létre, amelyek közül mindkettő 2 darab (egy 50 és egy 100 GB-os) csatlakoztatott adatlemezzel rendelkezik.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
Ügyeljen arra, hogy a _vmss create_ parancs alapértelmezett értékeket használ egyes konfigurációs értékek esetében, ha nem határozza meg azokat. Az elérhető és felülbírálható beállítások megtekintéséhez használja a következő parancsot:
```bash
az vmss create --help
```
Csatlakoztatott adatlemezekkel rendelkező méretezési csoport úgy is létrehozható, ha definiál egy méretezési csoportot egy Azure Resource Manager-sablonban, belefoglalja a _dataDisks_ szakaszt a _storageProfile_ részbe, majd üzembe helyezi a sablont. A fenti 50 GB-os és 100 GB-os lemezt bemutató példa a következőképpen lenne definiálva a sablonban:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
A következő címen egy teljes, üzembe helyezésre kész példát láthat egy olyan méretezésicsoport-sablonra, amely rendelkezik definiált csatlakoztatott lemezzel: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Adatlemez hozzáadása egy már létező méretezési csoporthoz
Adatlemez az Azure parancssori felület _az vmss disk attach_ parancsával adható hozzá virtuálisgép-méretezési csoporthoz. Győződjön meg arról, hogy olyan logikaiegység-számot ad meg, amely még nincs használatban. A következő parancssori példa egy 50 GB-os meghajtót ad hozzá a 3. logikai egységhez:
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```
> [!NOTE]
> A különböző méretű virtuális gépek eltérő korlátokkal rendelkeznek a csatlakoztatott meghajtók támogatott számát illetően. Új lemez hozzáadása előtt ellenőrizze a [virtuális gép méretjellemzőit](../virtual-machines/windows/sizes.md).

Úgy is hozzáadhat lemezt, hogy hozzáad egy új bejegyzést a _dataDisks_ tulajdonsághoz a méretezési csoport definíciójának _storageProfile_ szakaszában, majd alkalmazza a módosítást. Ennek teszteléséhez keressen egy már létező méretezésicsoport-definíciót az [Azure Resource Explorer](https://resources.azure.com/) alatt. Válassza ki a _Szerkesztés_ elemet, és adjon hozzá egy új lemezt az adatlemezek listájához. Például a fenti példa használata esetén:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```
Ezután válassza ki a _PUT_ parancsot, és alkalmazza a módosításokat a méretezési csoportra. Ez a példa akkor működik, ha a használt virtuális gép mérete kettőnél több csatlakoztatott adatlemezt támogat.

> [!NOTE]
> Ha módosítja egy méretezési csoport definícióját (pl. adatlemezt ad hozzá vagy távolít el róla), akkor a módosítás minden újonnan létrehozott virtuális gépre érvényes lesz. Már létező virtuális gépekre azonban csak akkor lesz érvényes, ha az _upgradePolicy_ tulajdonság „Automatic” (Automatikus) értékre van állítva. Ha ez a tulajdonság „Manual” (Manuális) értékre van állítva, akkor manuálisan kell alkalmazni az új modellt a már létező virtuális gépekre. Ezt a portálon az _Update-AzureRmVmssInstance_ PowerShell-parancs, vagy az Azure parancssori felület _az vmss update-instances_ parancsának használatával teheti meg.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Adatlemez eltávolítása méretezési csoportból
Adatlemez az Azure parancssori felület _az vmss disk detach_ parancsával távolítható el a virtuálisgép-méretezési csoportokból. Az alábbi parancs például a 2. logikai egységben definiált lemezt távolítja el:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
Hasonlóképpen, úgy is eltávolíthat lemezt méretezési csoportból, hogy eltávolít egy bejegyzést a _storageProfile_ szakaszban található _dataDisks_ tulajdonságból, és alkalmazza a módosítást. 

## <a name="additional-notes"></a>További megjegyzések
Az Azure Managed Disks, valamint a csatlakoztatott adatlemezzel rendelkező méretezési csoportok támogatása a Microsoft.Compute API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) verziójától vált elérhetővé. Az API ezen vagy újabb verzióival létrehozott bármely SDK vagy parancssori eszköz használata támogatott.

A csatlakoztatott lemezek méretezési csoportok esetében való támogatásának kezdeti implementációjában nincs lehetőség adatlemezek csatlakoztatására, vagy azok eltávolítására a méretezési csoportokba tartozó különálló virtuális gépek esetében.

Az Azure Portal kezdetben csak korlátozott támogatást biztosít a méretezési csoportok csatlakoztatott adatlemezei számára. A követelményektől függően Azure-sablonok, a parancssori felület, a PowerShell, SDK-k és a REST API használatával is kezelheti a csatlakoztatott lemezeket.



