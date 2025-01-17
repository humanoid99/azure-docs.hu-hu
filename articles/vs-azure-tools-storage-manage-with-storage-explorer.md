---
title: "Ismerkedés a Tártallózó alkalmazással (előzetes verzió) | Microsoft Docs"
description: "Azure tárerőforrások kezelése a Tártallózó alkalmazással (előzetes verzió)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 7f86c5abb47cdd69cf07410570b02ba0076d1c18
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="get-started-with-storage-explorer-preview"></a>Ismerkedés a Tártallózó alkalmazással (előzetes verzió)
## <a name="overview"></a>Áttekintés
Az Azure Tártallózó (előzetes verzió) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken. Ebből a cikkből megismerheti számos módon csatlakozik, és az Azure storage-fiókok kezelése.

![Microsoft Azure Tártallózó (előzetes verzió)][0]

## <a name="prerequisites"></a>Előfeltételek
* [A Tártallózó (előzetes verzió) letöltése és telepítése](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Csatlakozás egy tárfiókhoz vagy -szolgáltatáshoz
A Tártallózó (előzetes verzió) számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz. Megteheti például a következőt:
* Csatlakozhat az Azure-előfizetéséhez kapcsolt tárfiókokhoz.
* Csatlakozhat olyan tárfiókokhoz és szolgáltatásokhoz, amelyek más Azure-előfizetésekből vannak megosztva.
* Csatlakozhat egy helyi tárolóhoz az Azure Storage Emulator használatával, és kezelheti azt. 

Emellett használhatja a tárfiókokat a globális és az országos Azure-ban:

* [Csatlakozás Azure-előfizetéshez](#connect-to-an-azure-subscription): Az Azure-előfizetéséhez tartozó tárolási erőforrások kezelése.
* [Munkavégzés helyi fejlesztési tárterülettel](#work-with-local-development-storage): Helyi tárterület kezelése az Azure Storage Emulator használatával.
* [Külső tárterület csatolása](#attach-or-detach-an-external-storage-account): Más Azure-előfizetések vagy az országos Azure-felhők alá tartozó tárolási erőforrások kezelése a tárfiók fióknevének, kulcsának és végpontjainak használatával.
* [Tárfiók csatolása SAS használatával](#attach-storage-account-using-sas): Más Azure-előfizetések alá tartozó tárolási erőforrások kezelése közös hozzáférésű jogosultságkód (SAS) használatával.
* [Szolgáltatás csatolása SAS használatával](#attach-service-using-sas): Más Azure-előfizetések alá tartozó adott tárolási szolgáltatás (blob tároló, üzenetsor vagy tábla) kezelése SAS használatával.
* [Csatlakozás Azure Cosmos DB fiók egy kapcsolati karakterlánc használatával](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): kezelése Cosmos-adatbázis fiók egy kapcsolati karakterlánc használatával.

## <a name="connect-to-an-azure-subscription"></a>Csatlakozás Azure-előfizetéshez
> [!NOTE]
> Ha nincs Azure-fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja Visual Studio-előfizetése kiemelt előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. A Tártallózó (előzetes verzió), válassza ki a **fiókok kezelése** gomba a **fiók felügyeleti Panel**.

    ![Fiókok kezelése][1]

2. A bal oldali ablaktáblán most jelentkezett be Azure fiókokat jeleníti meg. Szeretne csatlakozni egy másik fiókot, válassza ki a **fiók hozzáadása**

3. Ha azt szeretné, egy nemzeti felhőbe vagy egy Azure verem bejelentkezni, kattintson a a **Azure környezetben** legördülő lista, mely használni kívánt Azure-felhő kiválasztásához. Miután kiválasztotta a környezetben, kattintson a **bejelentkezés...**  gombra. Bejelentkezés Azure verem, ha [Tártallózó csatlakozni a veremben Azure-előfizetéshez](azure-stack/user/azure-stack-storage-connect-se.md) további információt.

    ![Jelentkezzen be a beállítást][2]

3. Miután sikeresen bejelentkezett az Azure-fiók, a fiók és a fiókhoz társított Azure-előfizetések bekerülnek a bal oldali ablaktáblán. Válassza ki az Azure-előfizetéseket, és válassza ki a kívánt **alkalmaz** (Selecting **előfizetéseket:** ki- vagy a felsorolt Azure-előfizetések kiválasztásával és).

    ![Azure-előfizetések kiválasztása][3]

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure-előfizetésekhez társított összes tárfiókot.

    ![Kiválasztott Azure-előfizetések][4]

## <a name="work-with-local-development-storage"></a>Munkavégzés helyi fejlesztési tárterülettel
A Tártallózó (előzetes verzió) segítségével a helyi tárterületen is dolgozhat az Azure Storage Emulator használatával. Ez a megközelítés lehetővé teszi az Azure Storage szimulálhatja anélkül feltétlenül egy tárfiókot Azure, rendszerbe, mert a tárfiók tárfiókra az Azure Storage Emulator.

> [!NOTE]
> Az Azure Storage Emulator jelenleg kizárólag Windows rendszeren támogatott.
>
>

> [!NOTE]
> Az Azure Storage Emulator nem támogatja a fájlmegosztások.
>
>

1. A Tártallózó (előzetes verzió) bal oldali ablaktáblán, bontsa ki a **(helyi és kapcsolódó)** > **Tárfiókok** > **(fejlesztés)**  >  **Blobtárolók** csomópont.

    ![Helyi fejlesztési csomópont][5]

2. Ha még nem telepítette az Azure Storage Emulator, egy információs sáv keresztül erre kéri. Ha az információs sáv megjelenik, válassza a **Legújabb verzió letöltése** lehetőséget, és telepítse az emulátort.

    ![Azure Storage Emulator letöltése prompt][6]

3. Miután telepítette az emulátort, létrehozhat helyi blobokat, üzenetsorokat és táblákat, és kezelheti őket. Egyes tárfióktípusok munkavégzés további tudnivalókért tekintse meg az alábbi útmutatók:

    * [Azure Blob Storage-erőforrások kezelése](vs-azure-tools-storage-explorer-blobs.md)
    * Azure File Share Storage-erőforrások kezelése: *Hamarosan elérhető*
    * Azure Queue Storage-erőforrások kezelése: *Hamarosan elérhető*
    * Azure Table Storage-erőforrások kezelése: *Hamarosan elérhető*

## <a name="attach-or-detach-an-external-storage-account"></a>Külső tárfiók csatolása vagy leválasztása
A Tártallózó (előzetes verzió) segítségével külső tárfiókokat csatolhat, így azok könnyen megoszthatók. Ez a szakasz külső tárfiókok csatolását (és leválasztását) írja le.

### <a name="get-the-storage-account-credentials"></a>Tárfiók hitelesítő adatainak lekérése
Külső tárfiókok megosztásához az adott fiók tulajdonosának kell szereznie a fiókhoz tartozó hitelesítő adatok (fióknevet és kulcsot) és oszthat meg, hogy információkat csatlakoztatni kívánó személlyel említett fiók. A tárfiók hitelesítő adatait az Azure-portálon szerezheti be az alábbi lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza a **Tallózás** lehetőséget.

3. Válassza a **Tárfiókok** lehetőséget.

4. A közül **Tárfiókok**, válassza ki a kívánt tárfiókot.

5. A **beállítások**, jelölje be **hívóbetűk**.

    ![Hozzáférési kulcs lehetőség][7]

6. Másolás a **tárfióknév** és **key1**.

    ![Elérési kulcs][8]

### <a name="attach-to-an-external-storage-account"></a>Külső tárfiók csatolása
Külső tárfiók csatolásához szükség van a fiók nevére és kulcsára. A „Tárfiók hitelesítő adatainak lekérése” szakasz ismerteti ezen értékek lekérését az Azure Portalról. A portálon azonban a fiókkulcs neve: **kulcs1**. Igen, amikor Tártallózó (előzetes verzió) fiókkulcs kér, akkor adja meg a **key1** érték.

1. A Tártallózó (előzetes verzió), nyissa meg a **párbeszédpanelen csatlakozzon**.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Az a **párbeszédpanelen csatlakozzon**, válassza a **a tárfiók nevét és a kulcs használata**

    ![A név és kulcs lehetőséggel hozzáadása][10]

3. Illessze be a fiók nevére a **fióknevet** szöveg mezőbe, majd illessze be a fiókkulcs (a **key1** érték az Azure-portálon) azokat a **fiókkulcs** szövegmezőbe, majd válassza ki **Következő**.

    ![Név és kulcs][11]

    > [!NOTE]
    > A név és a kulcs használatához a nemzeti felhőből a **tárolási végpontok tartomány:** jelölje be a megfelelő végpontok tartomány legördülő lista: 
    >
    >

4. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. Ha bármin változtatni szeretne, válassza a **Vissza** lehetőséget, és írja be újra a kívánt beállításokat. 

5. Kattintson a **Csatlakozás** gombra.

6. Miután a tárfiók sikeresen csatlakoztatva van, a tárfiók jelenik meg, amely **(külső)** a névhez.

    ![Külső tárfiók csatolásának eredménye][12]

### <a name="detach-from-an-external-storage-account"></a>Külső tárfiók leválasztása
1. Kattintson a jobb gombbal a leválasztani kívánt külső tárfiókra, és válassza a **Leválasztás** lehetőséget.

    ![Tár leválasztása lehetőség][13]

2. A megerősítő üzenetben kattintson az **Igen** gombra a külső tárfiók leválasztásának jóváhagyásához.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Csatlakoztassa a storage-fiókok egy közös hozzáférésű Jogosultságkód (SAS) segítségével
A közös hozzáférésű Jogosultságkód vagy [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md), lehetővé teszi, hogy a rendszergazda az Azure-előfizetés hozzáférést ideiglenes a storage-fiókok Azure-előfizetés hitelesítő adatok megadása nélkül.

E forgatókönyv szemléltetésére tegyük fel, hogy az „A” felhasználó valamely Azure-előfizetés rendszergazdája, és hozzáférést szeretne engedélyezni „B” felhasználó számára a tárfiókhoz adott időtartamra és meghatározott engedélyekkel:

1. "A" felhasználó hoz létre egy SAS-kapcsolati karakterláncot, egy adott időszakra vonatkozóan, és a kívánt engedélyekkel.

2. "A" felhasználó megosztja a SAS a tárfiók eléréséhez kívánó személlyel ("b" felhasználó, ebben a példában).  

3. A „B” felhasználó a Tártallózó (előzetes verzió) segítségével csatolja az „A” felhasználóhoz tartozó fiókot a megadott SAS-kód használatával.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>A megosztani kívánt fiókhoz SAS kapcsolati karakterlánc generálása
1. A Tártallózó (előzetes verzió), kattintson a jobb gombbal szeretné osztani, majd válassza ki a tárfiók **közös hozzáférésű Jogosultságkód beolvasása...** .

    ![SAS beszerzése menüpont][14]

2. Az a **közös hozzáférésű Jogosultságkód létrehozása** párbeszédpanelen adja meg az időtartamot és engedélyeket, a fiók, és kattintson a **létrehozása** gombra.

    ![SAS párbeszédpanel][15]  

3. Mellett a **kapcsolati karakterlánc** szövegmezőben, jelölje be **másolási** másolja a vágólapra, majd **Bezárás**.

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>A storage-fiók csatolása SAS kapcsolati karakterlánc használatával
1. A Tártallózó (előzetes verzió), nyissa meg a **párbeszédpanelen csatlakozzon**.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. A a **párbeszédpanelen csatlakozzon** párbeszédpanelen válasszon **egy kapcsolati karakterláncot vagy a közös hozzáférésű jogosultságkódot URI** , majd **tovább**.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][16]

3. Válasszon **használjon kapcsolati karakterláncot** és illessze be a kapcsolati karakterláncot a a **kapcsolati karakterlánc:** mező. Kattintson a **következő** gombra.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][17]

4. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. A változtatáshoz válassza ki a **Vissza** elemet, majd adja meg a kívánt beállításokat. 

5. Kattintson a **Csatlakozás** gombra.

6. Miután a tárfiók sikeresen csatlakoztatva van, a tárfiók jelenik meg, amely **(SAS)** a névhez.

    ![SAS használatával végzett fiókhoz csatolás eredménye][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Csatlakoztassa a szolgáltatás segítségével egy közös hozzáférésű Jogosultságkód (SAS)
A "A storage-fiók csatolása SAS használatával" szakasz ismerteti, hogyan egy Azure-előfizetés rendszergazdája ideiglenes hozzáférést biztosíthat a storage-fiók létrehozása és megosztása a tárfiók SAS-kód. Ehhez hasonlóan SAS-kód létrehozható adott szolgáltatásokhoz (blob tároló, várólista, táblázat vagy fájlmegosztás) egy tárfiókon belül.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>SAS-kód létrehozása a megosztani kívánt fiókhoz
Ebben a környezetben a szolgáltatás egy blob tároló, a várólista, a tábla, vagy fájlmegosztásba. SAS létrehozása listázott szolgáltatáshoz az alábbiak szerint:

* [SAS lekérése blob tárolóhoz](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* SAS lekérése fájlmegosztáshoz: *Hamarosan elérhető*
* SAS lekérése üzenetsorhoz: *Hamarosan elérhető*
* SAS lekérése táblához: *Hamarosan elérhető*

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Közös fiókszolgáltatás csatolása a SAS URI használatával
1. A Tártallózó (előzetes verzió), nyissa meg a **párbeszédpanelen csatlakozzon**.

    ![Csatlakozás Azure Storage-hoz lehetőség][9]

2. Az a **párbeszédpanelen csatlakozzon** párbeszédpanelen válassza ki **egy kapcsolati karakterláncot vagy a közös hozzáférésű jogosultságkódot URI** , majd **tovább**.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][16]

3. Válasszon **SAS URI-használ** , majd illessze be az URI azokat a **URI:** mező. Kattintson a **következő** gombra.

    ![Csatlakozás az Azure Storage-hoz párbeszédpanel][19]

3. A **Kapcsolatok összegzése** párbeszédpanelen ellenőrizze az adatokat. A változtatáshoz válassza ki a **Vissza** elemet, majd adja meg a kívánt beállításokat. 

4. Kattintson a **Csatlakozás** gombra.

5. Miután sikeresen csatlakozik a szolgáltatáshoz, a szolgáltatás alatt jelenik meg a **(SAS-Attached szolgáltatások)** csomópont.

    ![SAS használatával megosztott szolgáltatáshoz végzett csatolás eredménye][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Csatlakozás Azure Cosmos DB fiók egy kapcsolati karakterlánc használatával
Emellett a Azure előfizetéssel Azure Cosmos DB fiókok kezelése, kapcsolódni egy Azure Cosmos DB megadásának alternatív módja annak használjon kapcsolati karakterláncot. Az alábbi lépéseket követve csatlakozhat kapcsolati karakterlánccal.

1. Keresse meg a **Helyi és csatolt** elemet a bal oldali fában, kattintson a jobb gombbal az **Azure Cosmos DB-fiókok** elemre, majd válassza a **Csatlakozás az Azure Cosmos DB-hez…** lehetőséget.

    ![Csatlakozás Azure Cosmos DB kapcsolati karakterlánc][21]

2. Válassza ki az Azure Cosmos DB API, és beillesztheti a **kapcsolati karakterlánc**, és kattintson a **OK** Azure Cosmos DB fiók kapcsolódni. A kapcsolati karakterlánc lekérésével kapcsolatos információk: [A kapcsolati karakterlánc lekérése](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

 ## <a name="connect-to-azure-data-lake-store-by-uri"></a>Csatlakozás az Azure Data Lake Store URI
Ha szeretne hozzáférni az erőforrásokhoz, amelyek nem szerepelnek az előfizetés. De mások adja meg, hogy az URI azonosító lekérése az erőforrásokat. Ebben az esetben csatlakozhat Data Lake Store Uri segítségével, miután bejelentkezett. Tekintse meg a következő lépéseket.
1. Nyissa meg a Tártallózót (előzetes verzió).
2. A bal oldali ablaktáblán bontsa ki a **helyi és a kapcsolódó**.
3. Kattintson a jobb gombbal **Data Lake Store**, és válassza – a helyi menüből – a **kapcsolódás a Data Lake Store...** .

    ![csatlakozni a Data Lake Store helyi menü](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Adja meg az Uri, majd az eszköz navigál, hol található a megadott URL-CÍMÉT.

    ![csatlakozni a Data Lake Store környezet párbeszédpanel](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![csatlakozni a Data Lake Store eredménye](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="search-for-storage-accounts"></a>Tárfiókok keresése
Ha szeretne egy tároló-erőforrás található és nem tudja, ahol van, használja a keresési mezőbe a bal oldali ablaktábla tetején a erőforrás kereséséhez.

Írja be a keresőmezőbe, a bal oldali panelen megjeleníti a pontig, hogy a megadott keresési értéknek megfelelő összes erőforrást. Például a Keresés **végpontok** az alábbi képernyőfelvételen látható:

![Tárfiók keresése][23]

> [!NOTE]
> Használja a **fiók felügyeleti Panel** bármely előfizetések, amelyek nem tartalmaznak a végrehajtás ideje, a keresés javítása érdekében a keresett elem kijelölését. Is kattintson a jobb gombbal a csomópont, és válassza a **keresési az itt** egy adott csomópont a keresés indításához.
>
>

## <a name="next-steps"></a>További lépések
* [Azure Blob Storage-erőforrások kezelése a Tártallózó (előzetes verzió) használatával](vs-azure-tools-storage-explorer-blobs.md)
* [Az Azure Tártallózó (előzetes verzió) Azure Cosmos DB kezelése](./cosmos-db/storage-explorer.md)
* [A Tártallózó (előzetes verzió) Azure Data Lake Store-erőforrások kezelése](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/DevelopmentNode.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/EmulatorNotInstalled.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
