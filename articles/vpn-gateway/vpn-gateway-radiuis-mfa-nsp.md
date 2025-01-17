---
title: "Biztonságos Azure VPN gateway RADIUS-hitelesítés a hálózati házirend-kiszolgáló a multi-factor Authentication |} Microsoft Docs"
description: "Ismerteti az Azure átjáró RADIUS-hitelesítés integrálása a hálózati házirend-kiszolgáló a multi-factor Authentication."
services: vpn-gateway
documentationcenter: na
author: genlin
manager: willchen
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: genli
ms.openlocfilehash: 371e65dd1d80ed24046f3da5c588c21a1958c2e4
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló a multi-factor Authentication Azure VPN gateway RADIUS-hitelesítés integrálása 

A cikk ismerteti a hálózati házirend-kiszolgáló (NPS) integrálása az Azure VPN gateway RADIUS-hitelesítés képes biztosítani a multi-factor Authentication (MFA) pont-pont VPN-kapcsolatokhoz. 

## <a name="prerequisite"></a>Előfeltétel

Ahhoz, hogy a többtényezős Hitelesítést, a felhasználók az Azure Active Directoryban (Azure AD), amely kell kell-e szinkronizálva, vagy a helyszíni vagy felhőalapú környezetben kell lennie. Emellett a felhasználó elvileg elvégezte az automatikus beléptetési folyamatot az MFA szolgáltatásra.  További információkért lásd: [a kétlépéses ellenőrzéshez a fiók beállítása](../multi-factor-authentication/end-user/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Részletes lépések

### <a name="step-1-create-a-virtual-network-gateway"></a>1. lépés: A virtuális hálózati átjáró létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Válassza ki a virtuális hálózat, amely a virtuális hálózati átjáró fogja futtatni, **alhálózatok**, majd válassza ki **átjáróalhálózatot** egy alhálózat létrehozásához. 

    ![A kép kapcsolatos átjáró alhálózatának hozzáadása](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. A virtuális hálózati átjáró létrehozása a következő beállítások megadásával:

    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN-típus**: válasszon **útválasztó-alapú**.
    - **SKU**: a követelmények alapján SKU-típus.
    - **Virtuális hálózati**: válassza ki a virtuális hálózatot, amelyben létrehozta az átjáró alhálózatának.

        ![A virtuális hálózati átjáró beállításaival kapcsolatos kép](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>2. lépés a hálózati házirend-kiszolgáló konfigurálása az Azure MFA használatára

1. A hálózati házirend-kiszolgálón [az NPS-bővítményének telepítése az Azure MFA](../multi-factor-authentication/multi-factor-authentication-nps-extension.md#install-the-nps-extension).
2. Nyissa meg a NSP-konzolt, kattintson a jobb gombbal **RADUIS ügyfelek**, majd válassza ki **új**. Hozza létre a RADUIS ügyfél a következő beállítások megadásával:

    - **Rövid név**: adjon neki tetszőleges nevet.
    - **(IP vagy DNS-) cím**: írja be az átjáró alhálózatának az 1. lépésben létrehozott.
    - **Közös titkos kulcs**: írja be a titkos kulcsot, és jegyezze meg későbbi felhasználás céljából.

    ![A kép RADUIS ügyfél beállításaival kapcsolatos](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Az a **speciális** lapon, a gyártó neve **RADIUS szabványos** , és győződjön meg arról, hogy a **további beállítások** jelölőnégyzet nincs bejelölve.

    ![A kép RADUIS ügyfél speciális beállításairól](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Ugrás **házirendek** > **hálózati házirendek**, kattintson duplán a **Microsoft Routing és a távelérési kiszolgálóval létesített kapcsolatok** szabályzatot, jelölje be  **Hozzáférést**, és kattintson a **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>3. lépés konfigurálása a virtuális hálózati átjáró

1. Jelentkezzen be [Azure-portálon](https://portal.azure.com).
2. Nyissa meg a létrehozott virtuális hálózati átjáró. Győződjön meg arról, hogy az átjáró típusa beállításai **VPN** , és hogy a VPN-típus **útválasztó-alapú**.
3. Kattintson a **Helykonfiguráció mutasson** > **konfigurálásával**, és adja meg a következő beállításokat:

    - **-Címkészlet**: írja be az 1. lépésben létrehozott átjáró-alhálózatot.
    - **Hitelesítés típusa**: válasszon **RADIUS-hitelesítés**.
    - **Kiszolgáló IP-címe**: a hálózati házirend-kiszolgáló IP-címét.

    ![A kép pontjának hely beállításainak ismertetése](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>További lépések

- [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure Multi-Factor Authenticationnel](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)
