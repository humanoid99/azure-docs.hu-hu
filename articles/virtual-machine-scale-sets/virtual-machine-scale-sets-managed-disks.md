---
title: "Felügyelt lemezek használata Azure-beli virtuálisgép-méretezési csoportokkal | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhat felügyelt lemezeket virtuálisgép-méretezési csoportokkal"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/21/2017
ms.author: negat
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 4ec20a30f423d4b30e953f873100be90167f987b
ms.lasthandoff: 03/28/2017


---
# <a name="azure-vm-scale-sets-and-managed-disks"></a>Azure-beli virtuálisgép-méretezési csoportok és felügyelt lemezek

Az Azure-beli [virtuálisgép-méretezési csoportok](/azure/virtual-machine-scale-sets/) mostantól támogatják a felügyelt lemezekkel rendelkező virtuális gépeket. A felügyelt lemezek méretezési csoportokkal való használatának több előnye van, például:

* Nem kell előre létrehoznia és felügyelnie tárfiókokat a méretezési csoportban lévő virtuális gépek operációsrendszer-lemezeinek tárolásához.

* Felügyelt adatlemezeket csatlakoztathat a méretezési csoporthoz.

* Felügyelt lemezzel a méretezési csoportok kapacitása akár platformlemezkép-alapú 1000 vagy 100 egyéni lemezképen alapuló virtuális gép lehet.

## <a name="get-started"></a>Bevezetés

A felügyelt lemezes méretezési csoportok használatát úgy kezdheti el a legegyszerűbben, ha az Azure Portalon üzembe helyez egyet. További információkért tekintse meg [ezt a cikket](./virtual-machine-scale-sets-portal-create.md). Az első lépések másik egyszerű módja, ha az [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)-s verziójával üzembe helyez egy méretezési csoportot. A következő példa bemutatja, hogyan hozhat létre Ubuntu-alapú méretezési csoportot 10 virtuális géppel, amelyek mindegyike 50 GB-os és 100 GB-os adatlemezzel rendelkezik:

```azurecli
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

Vagy az [Azure-gyorsindítási sablonok GitHub-adattárában](https://github.com/Azure/azure-quickstart-templates) a `vmss` elemet tartalmazó mappákra rákeresve megtekintheti a méretezési csoportokat üzembe helyező sablonok előre felépített példáit. [Ebből a listából](https://github.com/Azure/azure-quickstart-templates/blob/master/managed-disk-support-list.md) megtudhatja, hogy melyik sablon használ már felügyelt lemezeket.

## <a name="api-versions"></a>API-verziók

A méretezési csoportok aktuális általánosan elérhető API-verziója a következő: `2016-04-30-preview`. A nem felügyelt lemezekkel rendelkező méretezési csoportok továbbra is a jelenlegi módon működnek, még a felügyelt lemezeket támogató új API-verziókban is. A nem felügyelt lemezekkel rendelkező méretezési csoportok azonban nem használják ki a felügyelt lemezek előnyeit még ezekben az új API-verziókban sem.

## <a name="next-steps"></a>Következő lépések

A felügyelt lemezekkel kapcsolatos további általános információt [ebben a cikkben](../storage/storage-managed-disks-overview.md) talál.

[Ebben a cikkben](./virtual-machine-scale-sets-convert-template-to-md.md) láthatja, hogyan alakíthat át egy Resource Manager-sablont, hogy felügyelt lemezekkel lássa el a méretezési csoportokat. A Resource Manager-sablonok ugyanezen módosításai érvényesek az Azure REST API-ra is.

A felügyelt adatlemezek méretezési csoportokkal való használatáról [ebben a cikkben](./virtual-machine-scale-sets-attached-disks.md) talál további információt.

A nagyméretű méretezési csoportok használatának elkezdéséről [ebben a cikkben](./virtual-machine-scale-sets-placement-groups.md) olvashat.



