---
title: "Csatlakozás Windows Server-rendszerű virtuális géphez | Microsoft Docs"
description: "Megtudhatja, hogyan csatlakozhat és jelentkezhet be egy Windows virtuális gépre az Azure Portal és a Resource Manager-alapú üzemi modell használatával."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 88431377a36d5bc36220c630f0c8d4a46ab4a434
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>Csatlakozás és bejelentkezés Windows rendszert futtató Azure virtuális gépre
Használja az Azure Portal **Csatlakozás** gombját egy távoli asztali (RDP) munkamenet elindításához egy Windows asztali rendszerről. Először csatlakozzon a virtuális géphez, majd jelentkezzen be.

Ha Mac gépről szeretne Windows-alapú virtuális géphez csatlakozni, telepítenie kell a Mac gépre egy RDP-ügyfelet például a [Microsoft Távoli asztalt](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417).

## <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A központi menüben kattintson a **Virtuális gépek** elemre.
3. Válassza ki a virtuális gépet a listából.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
   
    ![A virtuális géphez való csatlakozást ismertető képernyőkép az Azure Portalról.](./media/connect-logon/connect.png)
   
   > [!TIP]
   > Ha a portálon a **Csatlakozás** gomb szürkén jelenik meg, és nem [ExpressRoute](../../expressroute/expressroute-introduction.md)- vagy [telephelyek közötti VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)-kapcsolaton keresztül kapcsolódik az Azure-hoz, létre kell hoznia egy nyilvános IP-címet, és hozzá kell rendelnie a virtuális géphez, mielőtt az RDP-t használhatná. További információ: [nyilvános IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md).
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre
[!INCLUDE [virtual-machines-log-on-win-server](../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Következő lépések
A csatlakozási kísérlet során felmerülő hibákkal kapcsolatban tekintse meg [Távoli asztali kapcsolatok hibaelhárítása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) című témakört. Ez a cikk útmutatást nyújt a gyakori problémák diagnosztizálásához és elhárításához.


