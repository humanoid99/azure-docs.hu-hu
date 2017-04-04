---
title: "Privát Docker-beállításjegyzék létrehozása – Azure Portal | Microsoft Docs"
description: "Bevezetés a privát Docker-beállításjegyzékek létrehozásába és kezelésébe az Azure Portalon"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Privát Docker-tárolójegyzék létrehozása az Azure Portalon
Az Azure Portalon létrehozhat egy tároló-beállításjegyzéket, és kezelheti a beállításait. A tároló-beállításjegyzékeket létrehozhatja és kezelheti az [Azure CLI 2.0 parancsaival](container-registry-get-started-azure-cli.md) vagy programozott módon a tároló-beállításjegyzék [REST API-jával](https://go.microsoft.com/fwlink/p/?linkid=834376) is.

Háttér-információkért és a fogalmakkal kapcsolatban lásd [az áttekintést](container-registry-intro.md).



## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
1. Az [Azure Portalon](https://portal.azure.com) kattintson az **+Új** elemre.
2. Keresse meg a piactéren az **Azure Container Registry-t**.
3. Válassza ki az **Azure Container Registry-t**, amelynek közzétevője a **Microsoft**.
    ![Container Registry szolgáltatás az Azure Marketplace-en](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Kattintson a **Létrehozás** gombra. Megjelenik az **Azure Container Registry** panel.

    ![A tároló-beállításjegyzékek beállításai](./media/container-registry-get-started-portal/container-registry-settings.png)
5. Az **Azure Container Registry** panelen adja meg a következő információkat. Amikor elkészült, kattintson a **Létrehozás** gombra.

    a. **Beállításjegyzék neve**: egy globálisan egyedi legfelső szintű tartománynév az adott beállításjegyzékhez. Ebben a példában a beállításjegyzék neve *myRegistry01*, de helyettesítsen be egy saját, egyedi nevet. A név csak betűket és számokat tartalmazhat.

    b. **Erőforráscsoport**: Válasszon egy meglévő [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét.

    c. **Hely**: Válasszon ki egy Azure-adatközpontot, ahol a szolgáltatás [elérhető](https://azure.microsoft.com/regions/services/), például az **USA déli középső régióját**.

    d. **Rendszergazdai felhasználó**: Ha szeretné, engedélyezze egy rendszergazdai felhasználó számára a beállításjegyzék elérését. Ezt a beállítást a beállításjegyzék létrehozását követően módosíthatja.

    > [!IMPORTANT]
    > A tároló-beállításjegyzékek, amellett, hogy hozzáférést biztosítanak egy rendszergazdai felhasználói fiókon keresztül, támogatják az Azure Active Directory egyszerű szolgáltatásaira épülő hitelesítést. További információkat és szempontokat [a tároló-beállításjegyzékkel való hitelesítéssel kapcsolatos cikkben](container-registry-authentication.md) találhat.


    e. **Storage-fiók**: Hozzon létre egy [Storage-fiókot](../storage/storage-introduction.md) az alapértelmezett beállítással, vagy válasszon egy meglévő tárfiókot ugyanezen a helyen. A Premium Storage jelenleg nem támogatott.


## <a name="manage-registry-settings"></a>Beállításjegyzék beállításainak kezelése
A beállításjegyzék létrehozását követően a beállításjegyzék-beállításokat a portál **Tároló-beállításjegyzékek** paneljéről kiindulva találja meg. Például szüksége lehet a beállításjegyzékbe való bejelentkezés beállításaira, vagy esetleg szeretné engedélyezni vagy letiltani a rendszergazdai felhasználót.

1. A **Tároló-beállításjegyzékek** panelen kattintson a beállításjegyzék nevére.

    ![Tároló-beállításjegyzék panel](./media/container-registry-get-started-portal/container-registry-blade.png)
2. A hozzáférési beállítások kezeléséhez kattintson a **Hozzáférési kulcs** elemre.

    ![Hozzáférés a tároló-beállításjegyzékhez](./media/container-registry-get-started-portal/container-registry-access.png)
3. Ügyeljen a következő beállításokra:

   * **Bejelentkezési kiszolgáló** – A teljes név, amelyet a beállításjegyzékbe való bejelentkezéshez használ. Ebben a példában ez `myregistry01.azurecr.io`.
   * **Rendszergazdai felhasználó** – Ki- és bekapcsolhatja a beállításjegyzék rendszergazdai felhasználói fiókját.
   * **Felhasználó** és **Jelszó** – A rendszergazdai felhasználói fiók (ha engedélyezve van) hitelesítő adatai, amelyek használatával bejelentkezhet a beállításjegyzékbe. Szükség esetén újból létrehozhatja a jelszavakat. Két jelszó jön létre, így az egyik jelszó ismételt létrehozása esetén a másikkal közben fenntarthatja a beállításjegyzék kapcsolatait. Ha ehelyett egyszerű szolgáltatást szeretne használni a hitelesítéshez, tekintse meg a [privát Docker-tároló beállításjegyzékével történő hitelesítést leíró szakaszt](container-registry-authentication.md).

## <a name="next-steps"></a>Következő lépések
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)

