---
title: "SSH-kulcspárok létrehozása és használata Linux rendszerű virtuális gépekhez Azure-on | Microsoft Docs"
description: "Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez a hitelesítési folyamat biztonságának fejlesztése érdekében."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/07/2017
ms.author: iainfou
experimental: true
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 4316f0690ac0941521f84d92f62b4fc3f42f76e0
ms.lasthandoff: 04/03/2017


---

# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez
Egy SSH-kulcspárral létrehozhat olyan virtuális gépeket az Azure-ban, amelyek SSH-kulcsokat használnak a hitelesítéshez, így nincs szükség jelszavakra a bejelentkezéshez. Ez a cikk bemutatja, hogyan hozhat létre gyorsan és hogyan használhat az SSH-protokoll 2. verziójára épülő nyilvános és titkosított RSA-kulcspárt a Linux rendszerű virtuális gépekhez. Részletes lépéseket és a további példákat – például a klasszikus portálon való használatot – az [SSH-kulcspárok és -tanúsítványok létrehozásának lépései](create-ssh-keys-detailed.md) között talál.

## <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Használja az `ssh-keygen` parancsot nyilvános és titkos SSH-kulcs fájljainak létrehozására. Ezek a fájlok alapértelmezés szerint a `~/.ssh` könyvtárban jönnek létre, de megadhat más helyet és egy további hozzáférési kódot (a titkosított kulcsfájl eléréséhez), amikor a parancssori felület erre kéri. Futtassa a következő parancsot egy Bash-rendszerhéjból, és a kéréseket válaszolja meg saját adatainak megfelelően.

```bash
ssh-keygen -t rsa -b 2048 
```

## <a name="use-the-ssh-key-pair"></a>Az SSH-kulcspár használata
Az Azure-ban a Linux virtuális gépre helyezett nyilvános kulcs alapértelmezés szerint a `~/.ssh/id_rsa.pub` könyvtárban található, ha a létrehozás során nem változtatta meg a helyet. Ha az [Azure CLI 2.0](/cli/azure) használatával hozta létre a virtuális gépet, határozza meg a nyilvános kulcs helyét, mikor a [az vm create](/cli/azure/vm#create) parancsot használja a `--ssh-key-path` lehetőséggel. Ha másolja és beilleszti a nyilvános kulcsfájl tartalmát az Azure Portalon vagy egy Resource Manager-sablonban való használathoz, bizonyosodjon meg róla, hogy nem másol felesleges szóközöket. Ha például OS X-et használ, átadhatja a nyilvános kulcsfájlt (alapértelmezetten, **~/.ssh/id_rsa.pub**) a **pbcopy** számára, hogy az másolja a tartalmat (erre a műveletre más Linux programok is képesek, például az `xclip`). 

Ha nem ismeri a nyilvános SSH-kulcsokat, megnézheti a saját nyilvános kulcsát a `cat` parancs futtatásával az alábbiak szerint. A `~/.ssh/id_rsa.pub` helyet cserélje le a saját nyilvános kulcsának helyére:

```bash
cat ~/.ssh/id_rsa.pub
```

Az Azure-beli virtuális gépén található nyilvános kulccsal jelentkezzen be SSH-n keresztül a virtuális gépbe a gép IP-címének vagy DNS-nevének használatával (ne felejtse el kicserélni az `azureuser` és a `myvm.westus.cloudapp.azure.com` tartalmát a rendszergazdai felhasználónévre és a teljes tartománynévre vagy IP-címre):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Ha kulcspár létrehozása során megadott hozzáférési kódot, adja meg a hozzáférési kódot, mikor a bejelentkezési folyamat során a rendszer kéri. (A rendszer hozzáadja a kiszolgálót a `~/.ssh/known_hosts` mappájához, és nem kér ismételt csatlakozást, amíg a nyilvános kulcs Azure-beli virtuális gépén nem változik, vagy amíg a kiszolgálónevet nem törlik a `~/.ssh/known_hosts` helyről.)

## <a name="next-steps"></a>Következő lépések

Az SSH-kulcsokkal létrehozott virtuális gépek alapértelmezés szerint letiltott jelszavakkal vannak konfigurálva, ami a találgatásos támadáson alapuló betörési kísérleteket sokkal drágábbá, ennek megfelelően nehezebbé teszi. Ez a témakör a gyors használatra készített egyszerű SSH-kulcspár létrehozását tárgyalja. Ha szüksége van további segítségre az SSH-kulcspár létrehozásához vagy további tanúsítványokra van szüksége, például a klasszikus portál használatához, tekintse meg az [SSH-kulcspárok és -tanúsítványok létrehozásának lépéseit](create-ssh-keys-detailed.md).

Az Azure Portal, a parancssori felület és sablonok segítségével is létrehozhat olyan virtuális gépeket, amelyek saját SSH-kulcspárját használják:

* [Biztonságos Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Biztonságos Linux virtuális gép létrehozása az Azure 2.0 parancssori felülettel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

