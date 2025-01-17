---
title: "Egy Azure virtuális gép felügyelt Szolgáltatásidentitás használatát a bejelentkezéshez"
description: "Lépésenkénti útmutatásért és példákért használatával jelentkezzen be a parancsfájl-ügyfél egy Azure virtuális gép MSI egyszerű szolgáltatásnév és az erőforrás eléréséhez."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 735b673227ac128bbc01d3b2072f4b14d84b0e66
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Egy Azure virtuális gép felügyelt szolgáltatás Identity (MSI) használatát a bejelentkezéshez 

[!INCLUDE [preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk példákat PowerShell és a parancssori felület parancsfájl jelentkezzen be egy egyszerű MSI-szolgáltatást, és útmutatást használatával hibakezelés például a fontos kérdésekben.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha azt tervezi, a cikkben az Azure PowerShell vagy Azure CLI példák használni, ügyeljen arra, hogy telepítse a legújabb verzióját [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) vagy [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Ebben a cikkben minden mintaparancsfájl azt feltételezi, hogy a parancssori ügyfelet futtató egy MSI-kompatibilis virtuális gépen. A virtuális gép "Csatlakozás" szolgáltatást használja az Azure-portálon távolról csatlakozni a virtuális Gépet. A virtuális gép MSI engedélyezésével kapcsolatos részletekért lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](msi-qs-configure-portal-windows-vm.md), vagy a variant cikkekben (a PowerShell, CLI, sablon vagy egy Azure SDK használatával). 
> - Megelőzése érdekében erőforrások elérése során, a virtuális gép MSI meg kell adni legalább "Olvasó" elérni a megfelelő hatókörben (a virtuális gép vagy magasabb) a virtuális gép Azure Resource Manager műveletek engedélyezéséhez. Lásd: [egy felügyelt szolgáltatás identitásának (MSI) hozzáférés hozzárendelése az Azure portál használatával erőforrás](msi-howto-assign-access-portal.md) részleteiről.

## <a name="overview"></a>Áttekintés

Egy olyan MSI Csomaghoz biztosít egy [egyszerű](develop/active-directory-dev-glossary.md#service-principal-object), amely [MSI engedélyezése után létrehozott](msi-overview.md#how-does-it-work) a virtuális Gépen. A szolgáltatás egyszerű való Azure-erőforrások hozzáférése, és használja az identitás parancsfájl/parancssor-line-ügyfelek a bejelentkezési és erőforrás-hozzáférés. Hagyományosan a saját identitással védett erőforrások eléréséhez a parancsfájl ügyfél kell:  

   - regisztrált és átadni kívánt hozzájárult e bizalmas vagy webes ügyfél alkalmazásként Azure AD-val
   - Jelentkezzen be a szolgáltatás egyszerű, (amelyek ágyazva a parancsfájlba) az alkalmazás hitelesítő adatokkal a

Az MSI-fájl a parancsfájl-ügyfél már nem kell módszerekkel, mint azt jelentkezhetnek be az MSI egyszerű szolgáltatás alatt. 

## <a name="azure-cli"></a>Azure CLI

Az alábbi parancsfájl bemutatja, hogyan:

1. Jelentkezzen be az Azure AD-alatt a virtuális gép MSI egyszerű szolgáltatásnév  
2. Hívása az Azure Resource Manager és a virtuális gép szolgáltatás egyszerű azonosító beszerzése Parancssori felület jogkivonat beszerzése vagy használata automatikusan kezelése az Ön gondoskodik. Ügyeljen arra, hogy a virtuális gép nevét `<VM-NAME>`.  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Az alábbi parancsfájl bemutatja, hogyan:

1. Szerezzen be egy MSI-jogkivonatot a virtuális gép számára.  
2. A hozzáférési jogkivonat használatával jelentkezzen be az Azure AD, a megfelelő MSI egyszerű alatt.   
3. Hívja meg az Azure Resource Manager parancsmagot, hogy a virtuális gép adatainak beolvasása. Token használható automatikusan felügyelete PowerShell gondoskodik.  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Erőforrás-azonosítók az Azure-szolgáltatások

Lásd: [Azure-szolgáltatások, hogy támogatja az Azure AD hitelesítési](msi-overview.md#azure-services-that-support-azure-ad-authentication) erőforrásokat, amelyek támogatják az Azure AD, és az MSI lettek tesztelve, és a megfelelő erőforrás-azonosítók listáját.

## <a name="error-handling-guidance"></a>Hiba kezelési útmutató 

A következő válaszok azt jelezheti, hogy a virtuális gép MSI-fájl nincs megfelelően konfigurálva:

- PowerShell: *Invoke-WebRequest: nem lehet kapcsolódni a távoli kiszolgáló*
- Parancssori felület: *MSI: nem sikerült beolvasni a "http://localhost:50342/oauth2/token" hiba történt a származó jogkivonat "HTTPConnectionPool (host"localhost", port = = 50342)* 

Ha egyik ezeket a hibákat, térjen vissza az Azure virtuális gép a [Azure-portálon](https://portal.azure.com) és:

- Lépjen a **konfigurációs** lapon, és ellenőrizze a "Felügyelt szolgáltatásidentitás" értéke "Yes".
- Lépjen a **bővítmények** lapon, és ellenőrizze az MSI-bővítmény sikeresen telepítve.

Vagy nem megfelelő, ha szeretne újra telepíteni a erőforráson MSI, vagy a központi telepítési hiba elhárítása. Lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](msi-qs-configure-portal-windows-vm.md) Ha Virtuálisgép-konfiguráció segítségre van szüksége.

## <a name="related-content"></a>Kapcsolódó tartalom

- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálja a virtuális gép felügyelt szolgáltatás Identity (MSI) PowerShell-lel](msi-qs-configure-powershell-windows-vm.md), vagy [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md)

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.








