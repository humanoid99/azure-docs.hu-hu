---
title: "Linux virtuális gépek Azure-ban való létrehozásának módszerei | Microsoft Azure"
description: "Ez a cikk a Linux virtuális gépek Azure-ban való létrehozásának különböző módszereit ismerteti, és minden módszer esetén további eszközökre és oktatóanyagokra mutató hivatkozásokat tartalmaz."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Különböző módszerek Linux rendszerű virtuális gépek létrehozásához
Az Azure-ban rugalmasan hozhat létre Linux virtuális gépeket olyan eszközökkel és munkafolyamatokkal, amelyeket szívesen használ. Ez a cikk a Linux-alapú virtuális gépek létrehozásának ezen különböző módszereit és példáit foglalja össze, az Azure CLI 2.0-s verzióját is beleértve. Emellett megtekintheti a létrehozási lehetőségeket, beleértve az [Azure CLI 1.0](creation-choices-nodejs.md) használatát.

Az [Azure CLI 2.0](/cli/azure/install-az-cli2) több platformon elérhető egy npm-csomagon, disztribúció által biztosított csomagokon vagy a Docker-tárolón keresztül. Telepítheti a környezet számára legmegfelelőbb buildet, és bejelentkezhet egy Azure-fiókba az [az login](/cli/azure/#login) paranccsal

Az alábbi példák az Azure CLI 2.0-s verzióját veszik alapul. Olvassa el az egyes cikkeket a bemutatott parancsok további részleteivel kapcsolatban. Emellett példákat találhat a Linux rendszeren elérhető létrehozási lehetőségekre az [Azure CLI 1.0](creation-choices-nodejs.md) használata esetén.

* [Linux virtuális gép létrehozása az Azure CLI 2.0-val](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Ez a példa létrehoz egy `myResourceGroup` nevű erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Ez a példa létrehoz egy `myVM` nevű virtuális gépet a legújabb Debian rendszerképet az Azure Managed Disks eszközzel és egy `id_rsa.pub` nevű nyilvános kulcs és az [az vm create](/cli/azure/vm#create) parancs használatával:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Ha nem felügyelt lemezeket szeretne használni, adja a `--use-unmanaged-disks` jelzőt a fenti parancshoz. Létrejön egy tárfiók. További információ: [Azure Managed Disks – áttekintés](../../storage/storage-managed-disks-overview.md).

* [Biztonságos Linux virtuális gép létrehozása Azure-sablon alapján](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Az alábbi példa egy virtuális gépet hoz létre a GitHubon tárolt sablonnal az [az group deployment create](/cli/azure/group/deployment#create) paranccsal:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Teljes Linux-környezet létrehozása az Azure parancssori felülettel](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Magában foglalja egy terheléselosztó és több virtuális gép létrehozását egy rendelkezésre állási csoportban.

* [Add a disk to a Linux VM (Lemez hozzáadása Linux rendszerű virtuális géphez)](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Az alábbi példa hozzáad egy 50 GB-os nem felügyelt lemezt a `myVM` nevű meglévő virtuális géphez az [az vm disk attach-new](/cli/azure/vm/disk#attach-new) paranccsal:
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Azure Portal
Az [Azure portálon](https://portal.azure.com) gyorsan létrehozhat egy virtuális gépet, mivel semmit nem kell telepítenie a rendszerre. A virtuális gép létrehozása az Azure Portallal:

* [Linux virtuális gép létrehozása az Azure Portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Lemez csatolása az Azure Portal használatával](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Választható operációs rendszerek és rendszerképek
A virtuális gépek létrehozásakor egy rendszerképet választ ki a futtatni kívánt operációs rendszer alapján. Az Azure és a partnerei számos rendszerképet kínálnak, amelyek némelyike előre telepített alkalmazásokat és eszközöket tartalmaz. Feltöltheti az egyik saját rendszerképét is (lásd [a következő szakaszt](#use-your-own-image)).

### <a name="azure-images"></a>Azure-rendszerképek
Az [az vm image](/cli/azure/vm/image) paranccsal megtekintheti az elérhető elemeket közzétevő, disztribúciós kiadás, illetve build szerint.

Az elérhető közzétevők listázása:

```azurecli
az vm image list-publishers --location WestUS
```

Egy adott közzétevő elérhető termékeinek (ajánlatainak) listázása:

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

Egy adott ajánlat elérhető termékváltozatainak (disztribúciós kiadásainak) listázása:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Egy adott kiadás összes elérhető rendszerképének listázása:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Az elérhető rendszerképek tallózásával és használatával kapcsolatos további példák: [Navigate and select Azure virtual machine images with the Azure CLI](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Azure virtuális gépek rendszerképének keresése és kiválasztása az Azure parancssori felülettel).

Az **az vm create** parancs rendelkezik aliasokkal, amelyek segítségével gyorsan hozzáférhet a leggyakoribb disztribúciókhoz és azok legújabb kiadásaihoz. Az aliasok használata gyakran gyorsabb, mintha meg kellene adnia a közzétevőt, ajánlatot, termékváltozatot és verziót, valahányszor létrehoz egy virtuális gépet:

| Alias | Közzétevő | Ajánlat | SKU | Verzió |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |legújabb |
| CoreOS |CoreOS |CoreOS |Stable |legújabb |
| Debian |credativ |Debian |8 |legújabb |
| openSUSE |SUSE |openSUSE |13.2 |legújabb |
| RHEL |Redhat |RHEL |7.2 |legújabb |
| SLES |SLES |SLES |12-SP1 |legújabb |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |legújabb |

### <a name="use-your-own-image"></a>Saját rendszerkép használata
Ha speciális egyéni beállításokra van szüksége, használhat egy meglévő Azure virtuális gépen alapuló rendszerképet a virtuális gép *rögzítésével*. Emellett feltölthet egy helyszínen létrehozott rendszerképet is. A támogatott disztribúciókkal és a saját rendszerképek használatával kapcsolatban az alábbi cikkekben tekinthet meg további információt:

* [Azure által támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Nem támogatott disztribúciókkal kapcsolatos tudnivalók](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [How to capture a Linux virtual machine as a Resource Manager template](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Linux virtuális gép rögzítése Resource Manager-sablonként).
  
  * Gyors üzembe helyezési **az vm** példaparancsok egy meglévő virtuális gép rögzítésére nem felügyelt lemezek használatával:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Következő lépések
* Hozzon létre egy Linux virtuális gépet a [portálon](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [parancssori felülettel](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy [Azure Resource Manager-sablonnal](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* A Linux virtuális gép létrehozása után [adjon hozzá egy adatlemezt](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Gyors lépések [jelszó vagy SSH-kulcsok alaphelyzetbe állításához és felhasználók kezeléséhez](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

