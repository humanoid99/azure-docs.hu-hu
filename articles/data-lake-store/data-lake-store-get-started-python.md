---
title: "Az Azure Data Lake Store használatának első lépései Python SDK használatával | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja a Python SDK-t a Data Lake Store-fiókokkal és a fájlrendszerrel végzett munkához."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8a3f3d8bfe670f2a4d1a4642b2380764aa6daeb4
ms.lasthandoff: 01/24/2017


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Az Azure Data Lake Store használatának első lépései a Python használatával

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

A cikkből megtudhatja, hogyan végezhet el olyan alapvető műveleteket a Python SDK for Azure és az Azure Data Lake Store segítségével, mint például mappák létrehozása vagy adatfájlok le- és feltöltése. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* **Python**. A Pythont [innen](https://www.python.org/downloads/) töltheti le. Ez a cikk a Python 3.5.2-es verzióját használja.

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Egy Azure Active Directory-alkalmazás létrehozása**. A Data Lake Store alkalmazás Azure AD-val történő hitelesítéséhez az Azure AD alkalmazást kell használni. Az Azure AD-val többféle módon is lehet hitelesíteni. Ezek a következők: **végfelhasználói hitelesítés** vagy **szolgáltatások közötti hitelesítés**. A hitelesítéssel kapcsolatos útmutatást és további információkat a [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Hitelesítés a Data Lake Store-ral az Azure Active Directoryt használva).

## <a name="install-the-modules"></a>A modulok telepítése

A Data Lake Store a Pythonnal való használatához három modult kell telepítenie.

* Az `azure-mgmt-resource` modult. Ez további Azure-modulokat tartalmaz az Active Directoryhoz és más eszközökhöz.
* Az `azure-mgmt-datalake-store` modult. Ez az Azure Data Lake Store fiókkezelési műveleteit tartalmazza. További információkat erről a modulról [az Azure Data Lake Store kezelési moduljához készült referenciaanyagban](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html) talál.
* Az `azure-datalake-store` modult. Ez az Azure Data Lake Store fájlrendszer-műveleteit tartalmazza. További információkat erről a modulról [az Azure Data Lake Store fájlrendszermoduljához készült referenciaanyagban](http://azure-datalake-store.readthedocs.io/en/latest/) talál.

A modulok telepítéséhez használja a következő parancsokat.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Új Python-alkalmazás létrehozása

1. A választott IDE-ben hozzon létre egy új Python-alkalmazást, például **mysample.py** néven.

2. Adja hozzá a következő sorokat a szükséges modulok importálásához.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Mentse a mysample.py módosításait.

## <a name="authentication"></a>Hitelesítés

Ebben a szakaszban az Azure AD-hitelesítés különböző módjait tárgyaljuk. Az elérhető lehetőségek:

* Végfelhasználói hitelesítés
* Szolgáltatások közötti hitelesítés
* Multi-Factor Authentication

Ezeket a hitelesítési módokat kell használnia a fiókkezelési és a fájlrendszerkezelési modulokban egyaránt.

### <a name="end-user-authentication-for-account-management"></a>Végfelhasználói hitelesítés fiókkezeléshez

Használja ezt az eljárást az Azure AD-val való hitelesítésre a fiókkezelési műveleteknél (Data Lake Store-fiók létrehozása/törlése stb). Az Azure AD-felhasználók számára meg kell adni egy felhasználónevet és egy jelszót. Ügyeljen arra, hogy a felhasználókat ne többtényezős hitelesítéssel konfigurálja.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>Végfelhasználói hitelesítés fájlrendszerműveletekhez

Használja ezt az eljárást az Azure AD-val való hitelesítésre a fájlrendszerműveleteknél (mappa létrehozása, fájl feltöltése stb). Egy meglévő **natív Azure AD-ügyfélalkalmazással** használja. Az Azure AD-felhasználót a hitelesítő adatok kiosztása során ne többtényezős hitelesítéssel konfigurálja.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fiókkezeléshez

Használja ezt az eljárást az Azure AD-val való hitelesítésre a fiókkezelési műveleteknél (Data Lake Store-fiók létrehozása/törlése stb). A következő kódrészlet használható az alkalmazás nem interaktív hitelesítéséhez, az alkalmazás/egyszerű szolgáltatás titkos ügyfélkódjának használatával. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>Szolgáltatások közötti, titkos ügyfélkulccsal történő hitelesítés a fájlrendszerműveletekhez

Használja ezt az eljárást az Azure AD-val való hitelesítésre a fájlrendszerműveleteknél (mappa létrehozása, fájl feltöltése stb). A következő kódrészlet használható az alkalmazás nem interaktív hitelesítéséhez, az alkalmazás/egyszerű szolgáltatás titkos ügyfélkódjának használatával. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>Többtényezős hitelesítés fiókkezeléshez

Használja ezt az eljárást az Azure AD-val való hitelesítésre a fiókkezelési műveleteknél (Data Lake Store-fiók létrehozása/törlése stb). A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>Többtényezős hitelesítés fájlrendszerkezeléshez

Használja ezt az eljárást az Azure AD-val való hitelesítésre a fájlrendszerműveleteknél (mappa létrehozása, fájl feltöltése stb). A következő kódrészlet használható az alkalmazás többtényezős hitelesítés használatával történő hitelesítéséhez. Ezt meglévő „webes” Azure AD-alkalmazással használhatja.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Azure-erőforráscsoport létrehozásához használja a következő kódrészletet:

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>Ügyfelek és Data Lake Store-fiókok létrehozása

Az alábbi kódrészlet először a Data Lake Store-fiókügyfelet hozza létre. Az ügyfélobjektum használatával hoz majd létre egy Data Lake Store-fiókot. Végül pedig létrehoz egy fájlrendszerügyfél-objektumot.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>A Data Lake Store-fiókok kilistázása

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>Könyvtár létrehozása

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Fájl feltöltése


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Fájl letöltése

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Könyvtár törlése

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>Lásd még:

- [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
- [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
- [A Data Lake Store .NET SDK dokumentációja](https://msdn.microsoft.com/library/mt581387.aspx)
- [A Data Lake Store REST dokumentációja](https://msdn.microsoft.com/library/mt693424.aspx)

