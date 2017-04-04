---
title: "A Data Lake Store használatának első lépései a REST API használatával | Microsoft Docs"
description: "Műveletek végrehajtása a Data Lake Store-on WebHDFS REST API-k használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 33574b0c1f023a8a5f83b1bf06f0523623891757
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Az Azure Data Lake Store használatának első lépései a REST API használatával
> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Ebből a cikkből megtudhatja, hogyan kezelheti a fiókokat, illetve hogyan hajthat végre fájlrendszer-műveleteket a WebHDFS REST API-k és az Azure Data Lake Store REST API-k használatával. Az Azure Data Lake Store saját REST API-kkal rendelkezik a fiókkezelési műveletekhez. Mivel azonban a Data Lake Store kompatibilis a HDFS- és a Hadoop-ökoszisztémával, támogatja a WebHDFS REST API-k használatát a fájlrendszer-műveletekhez.

> [!NOTE]
> A Data Lake Store REST API támogatásával kapcsolatos részletekért lásd: [Azure Data Lake Store REST API Reference](https://msdn.microsoft.com/library/mt693424.aspx) (Azure Data Lake Store REST API-referencia).
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Active Directory-alkalmazás létrehozása**. A Data Lake Store alkalmazás Azure AD-val történő hitelesítéséhez az Azure AD alkalmazást kell használni. Az Azure AD-val többféle módon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatos útmutatást és további információkat a [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Store-ral az Azure Active Directoryt használva).
* [cURL](http://curl.haxx.se/). Ez a cikk a cURL használatával mutatja be, hogyan lehet REST API-hívásokat indítani a Data Lake Store-fiókra.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hogyan végezhető el a hitelesítés az Azure Active Directory használatával?
Az Azure Active Directory használatával történő hitelesítést két módon végezheti el.

### <a name="end-user-authentication-interactive"></a>Végfelhasználó hitelesítése (interaktív)
Ebben az esetben az alkalmazás bejelentkezésre kéri a felhasználót, és minden művelet a felhasználó kontextusában lesz végrehajtva. Az interaktív hitelesítéshez hajtsa végre a következő lépéseket.

1. Az alkalmazáson keresztül irányítsa át a felhasználót az alábbi URL-címre:
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > A \<REDIRECT-URI> értéket kódolni kell az URL-ben való használatra. A https://localhost esetében tehát használja a következőt: `https%3A%2F%2Flocalhost`)
   > 
   > 
   
    A jelen oktatóanyagban kicserélheti a fenti URL-ben szereplő helyőrző értékeket, és beillesztheti egy webböngésző címsorába. A rendszer átirányítja az Azure bejelentkezési azonosítójával történő hitelesítéshez. Miután sikeresen bejelentkezett, a válasz megjelenik a böngésző címsorában. A válasz az alábbi formátumban jelenik meg:
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. Rögzítse a válaszban szereplő engedélyezési kódot. A jelen oktatóanyagban kimásolhatja a webböngésző címsorában szereplő engedélyezési kódot, majd a POST kérelemben továbbíthatja a jogkivonat végpontjához az alább látható módon:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > Ebben az esetben a \<REDIRECT-URI> kódolása nem szükséges.
   > 
   > 
3. A válasz egy JSON-objektum, amely egy hozzáférési (pl. `"access_token": "<ACCESS_TOKEN>"`) és egy frissítési (pl. `"refresh_token": "<REFRESH_TOKEN>"`) jogkivonatot tartalmaz. Az alkalmazás a hozzáférési jogkivonatot az Azure Data Lake Store-hoz való hozzáféréshez, a frissítési jogkivonatot pedig egy új hozzáférési jogkivonat beszerzéséhez használja, amikor az előző lejár.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. Amikor a hozzáférési jogkivonat lejár, a frissítési jogkivonat használatával kérhet egy újat az alább látható módon:
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

További információk az interaktív felhasználói hitelesítéssel kapcsolatban: [Authorization code grant flow](https://msdn.microsoft.com/library/azure/dn645542.aspx) (Az engedélyezési kód engedélyezési folyamata).

### <a name="service-to-service-authentication-non-interactive"></a>Szolgáltatások közötti hitelesítés (nem interaktív)
Ebben az esetben az alkalmazás maga biztosítja saját hitelesítő adatait a műveletek végrehajtásához. Ehhez egy alábbihoz hasonló POST-kérelmet kell kiadnia. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

A kérelem kimenete egy engedélyezési jogkivonatot fog tartalmazni (amelyet az alábbi kimenetben `access-token` jelöl), amelyet ezután a REST API-hívásokkal fog átadni. Mentse ezt az engedélyezési jogkivonatot egy szövegfájlba, mivel később még szüksége lesz rá a cikkben.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Ez a cikk a **nem interaktív** módszert alkalmazza. További információk a nem interaktív (szolgáltatások közötti) hívásokról: [Szolgáltatások közötti hívások hitelesítő adatok használatával](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Data Lake Store-fiók létrehozása
Ez a művelet az [itt](https://msdn.microsoft.com/library/mt694078.aspx) definiált REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

A fenti parancsban cserélje le a(z) \<`REDACTED`\> részt a korábban kapott engedélyezési jogkivonatra. A kérelem hasznos adatai ezen parancs esetében az **input.json** fájlban találhatók, amely a fenti `-d` paraméterhez lett megadva. Az input.json fájl tartalmai az alábbiakhoz hasonlók:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="create-folders-in-a-data-lake-store-account"></a>Mappák létrehozása Data Lake Store-fiókban
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

A fenti parancsban cserélje le a(z) \<`REDACTED`\> részt a korábban kapott engedélyezési jogkivonatra. Ez a parancs egy **mytempdir** nevű könyvtárat hoz létre a Data Lake Store-fiók gyökérkönyvtárában.

Ha a művelet sikeresen befejeződik, a következőhöz hasonló választ kell kapnia:

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Data Lake Store-fiók mappáinak listázása
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory) definiált WebHDFS REST API-híváson alapul.

Használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

A fenti parancsban cserélje le a(z) \<`REDACTED`\> részt a korábban kapott engedélyezési jogkivonatra.

Ha a művelet sikeresen befejeződik, a következőhöz hasonló választ kell kapnia:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Adatok feltöltése egy Data Lake Store-fiókba
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File) definiált WebHDFS REST API-híváson alapul.

Az adatok WebHDFS REST API használatával történő feltöltése kétlépéses folyamat, ahogy az alábbi leírásban is látható.

1. Küldjön el egy HTTP PUT-kérelmet a fájladatok feltöltés céljából történő elküldése nélkül. A következő parancsban cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.
   
        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE
   
    A parancs kimenete az alábbihoz hasonló ideiglenes átirányítási URL-címet fog tartalmazni.
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...
2. Ezután el kell küldenie egy másik HTTP PUT-kérelmet a válaszban szereplő **Location** (Hely) tulajdonságban listázott URL-cím alapján. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.
   
        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true
   
    A kimenet az alábbihoz hasonló lesz:
   
        HTTP/1.1 100 Continue
   
        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Adatok beolvasása a Data Lake Store-fiókból
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File) definiált WebHDFS REST API-híváson alapul.

Az adatok beolvasása a Data Lake Store-fiókból egy kétlépéses folyamat.

* Először küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` végponthoz. Ez visszaadja azt a helyet, ahová a következő GET kérelmet kell küldenie.
* Ezután küldenie kell egy GET kérelmet a `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` végponthoz. Ez megjeleníti a fájl tartalmát.

Mivel azonban az első és második lépésben nincs különbség a bemeneti paraméterek között, az első kérelem elküldéséhez használhatja az `-L` paramétert. A(z) `-L` kapcsoló lényegében egyesít két kérelmet, és megismételteti a kérelmet a cURL-paranccsal az új helyen. Végül megjelenik az összes kérelemhívás kimenete az alább látható módon. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

A következőhöz hasonló kimenetnek kell megjelennie:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Fájl átnevezése a Data Lake Store-fiókban
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl átnevezéséhez használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

A következőhöz hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Fájl törlése a Data Lake Store-fiókból
Ez a művelet az [itt](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory) definiált WebHDFS REST API-híváson alapul.

Fájl törléséhez használja a következő cURL-parancsot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

A következőhöz hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Data Lake Store-fiók törlése
Ez a művelet az [itt](https://msdn.microsoft.com/library/mt694075.aspx) definiált REST API-híváson alapul.

Az alábbi cURL-parancs segítségével törölheti a Data Lake Store-fiókot. Cserélje le a **\<yourstorename>** elemet saját Data Lake Store-nevére.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

A következőhöz hasonló kimenetnek kell megjelennie:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Lásd még:
* [Az Azure Data Lake Store-ral kompatibilis nyílt forráskódú big data-alkalmazások](data-lake-store-compatible-oss-other-applications.md)


