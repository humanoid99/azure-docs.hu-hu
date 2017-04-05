---
title: "Oktatóanyag – A Pythonhoz készült Azure Batch SDK használata | Microsoft Docs"
description: "Megismerheti az Azure Batch alapvető fogalmait és létrehozhat egy egyszerű megoldást a Python használatával."
services: batch
documentationcenter: python
author: tamram
manager: timlt
editor: 
ms.assetid: 42cae157-d43d-47f8-88f5-486ccfd334f4
ms.service: batch
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d1c327e90ca3343703784c22aba93280d4599723
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-the-batch-sdk-for-python"></a>Ismerkedés a Pythonhoz készült Batch SDK-val

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
>
>

Ebben a cikkben megismerheti az [Azure Batch][azure_batch] alapjait és a [Batch Python][py_azure_sdk]-ügyfelet egy Pythonban írt kisméretű Batch-alkalmazás részletes ismertetésén keresztül. Áttekintjük, hogyan használja ez a két mintául szolgáló parancsfájl a Batch szolgáltatást párhuzamos számítási feladat feldolgozásához Linux virtuális gépeken a felhőben, valamint hogyan használják az [Azure Storage-ot](../storage/storage-introduction.md) a fájlok előkészítéséhez és lekéréséhez. Megismerheti a Batch-alkalmazások általános munkafolyamatát, és a Batch fő összetevőivel, például a tevékenységekkel, feladatokkal, készletekkel és számítási csomópontokkal kapcsolatos alapvető ismereteket is elsajátíthatja.

![Batch-megoldás munkafolyamata (alapszintű)][11]<br/>

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk a Python és a Linux gyakorlati ismeretét feltételezi. Azt is feltételezi, hogy az Azure, a Batch és a Storage alábbi fióklétrehozási követelmények teljesülnek.

### <a name="accounts"></a>Fiókok
* **Azure-fiók**: Ha még nincs Azure-előfizetése, [hozzon létre egy ingyenes Azure-fiókot][azure_free_account].
* **Batch-fiók**: Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy Azure Batch-fiókot](batch-account-create-portal.md).
* **Tárfiók**: Lásd a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) cikk [Tárfiók létrehozása](../storage/storage-create-storage-account.md#create-a-storage-account) szakaszát.

### <a name="code-sample"></a>Kódminta
A Python-oktatóprogram [kódmintája][github_article_samples] a GitHubon lévő [azure-batch-samples][github_samples] tárban található számos Batch-kódminta egyike. Az összes minta letöltéséhez kattintson a **Clone or download > Download ZIP** (Klónozás vagy letöltés > ZIP letöltése) elemre a tár kezdőlapján, vagy kattintson az [azure-batch-samples-master.zip][github_samples_zip] közvetlen letöltésére szolgáló hivatkozásra. Amikor kibontotta a ZIP-fájl tartalmát, az oktatóprogram két parancsfájlja található meg az `article_samples` könyvtárban:

`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_client.py`<br/>
`/azure-batch-samples/Python/Batch/article_samples/python_tutorial_task.py`

### <a name="python-environment"></a>Python-környezet
A *python_tutorial_client.py* minta parancsfájl helyi munkaállomáson való futtatásához a **2.7-es** vagy a **3.3-as vagy újabb** verzióval kompatibilis **Python-fordítóra** lesz szüksége. A parancsfájl Linux és Windows rendszeren is tesztelve lett.

### <a name="cryptography-dependencies"></a>titkosítási függőségek
Telepítenie kell az `azure-batch` és az `azure-storage` Python-csomagokhoz szükséges [titkosítási][crypto] könyvtár függőségeit. Hajtsa végre az alábbiak közül a platformjának megfelelő műveleteket, vagy további információkért tekintse meg a [titkosítás telepítésével][crypto_install] kapcsolatos részleteket:

* Ubuntu

    `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython-dev python-dev`
* CentOS

    `yum update && yum install -y gcc openssl-dev libffi-devel python-devel`
* SLES/OpenSUSE

    `zypper ref && zypper -n in libopenssl-dev libffi48-devel python-devel`
* Windows

    `pip install cryptography`

> [!NOTE]
> Ha Python 3.3-as vagy újabb verziója számára Linux rendszeren, a Python-függőségekhez használja a python3-beli megfelelőket. Például az Ubuntu rendszeren: `apt-get update && apt-get install -y build-essential libssl-dev libffi-dev libpython3-dev python3-dev`
>
>

### <a name="azure-packages"></a>Azure-csomagok
Ezután telepítse az **Azure Batch** és az **Azure Storage** Python-csomagot. Mindkét csomag az itt található **pip** és a *requirements.txt* fájl segítségével telepíthető:

`/azure-batch-samples/Python/Batch/requirements.txt`

Adja ki a következő **pip** parancsot a Batch- és Storage-csomagok telepítéséhez:

`pip install -r requirements.txt`

Az [azure-batch][pypi_batch] és az [azure-storage][pypi_storage] Python-csomagot manuálisan is telepítheti:

`pip install azure-batch`<br/>
`pip install azure-storage`

> [!TIP]
> Lehet, hogy a `sudo` előtagot kell hozzáadnia a parancsokhoz, ha megfelelő jogosultságokkal nem rendelkező fiókot használ. Például: `sudo pip install -r requirements.txt`. Ha további információt szeretne kapni a Python-csomagok telepítésével kapcsolatban, olvassa el a [csomagok telepítését][pypi_install] ismertető cikket a python.org webhelyen.
>
>

## <a name="batch-python-tutorial-code-sample"></a>Batch Python-oktatóprogram kódmintája
A Batch Python-oktatóprogram kódmintája két Python-parancsfájlból és néhány adatfájlból áll.

* **python_tutorial_client.py**: a Batch és a Storage szolgáltatással együttműködve végez párhuzamos számítási feladatokat a számítási csomópontokon (virtuális gépeken). A *python_tutorial_client.py* parancsfájl a helyi munkaállomáson fut.
* **python_tutorial_task.py**: Az a parancsfájl, amely az Azure számítási csomópontjain fut a tényleges munka elvégzéséhez. A mintában a *python_tutorial_task.py* elemzi az Azure Storage-ból letöltött fájl (a bemeneti fájl) szövegét. Ezután egy szövegfájlt hoz létre (a kimeneti fájlt), amely a bemeneti fájlban megjelenő első három szó listáját tartalmazza. A kimeneti fájl létrehozása után a *python_tutorial_task.py* feltölti a fájlt az Azure Storage-ba. Így a munkaállomáson futó ügyfélparancsfájl letöltheti azt. A *python_tutorial_task.py* parancsfájl párhuzamosan fut több számítási csomóponton a Batch szolgáltatásban.
* **./data/taskdata\*.txt**: Ez a három szövegfájl nyújt bemenetet a számítási csomópontokon futó feladatokhoz.

A következő diagram az ügyfél- és a feladatparancsfájlok által végrehajtott elsődleges műveleteket ábrázolja. Ez az alapvető munkafolyamat számos, a Batch használatával létrehozott számítási megoldásra jellemző. Bár nem mutatja be a Batch szolgáltatásban elérhető összes funkciót, majdnem mindegyik Batch-forgatókönyv tartalmazza a munkafolyamat egyes részeit.

![Példa Batch-munkafolyamat][8]<br/>

[**1. lépés**](#step-1-create-storage-containers) **Tárolók** létrehozása az Azure Blob Storage-ban.<br/>
[**2. lépés**](#step-2-upload-task-script-and-data-files) Feladatparancsfájlok és bemeneti fájlok feltöltése tárolókba.<br/>
[**3. lépés**](#step-3-create-batch-pool) Batch-**készlet** létrehozása.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** A **StartTask** készlet letölti a feladatparancsfájlt (python_tutorial_task.py) a csomópontokra, amikor azok a készlethez csatlakoznak.<br/>
[**4. lépés**](#step-4-create-batch-job) Batch-**feladat** létrehozása.<br/>
[**5. lépés**](#step-5-add-tasks-to-job) Adjon hozzá **tevékenységeket** a feladathoz.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** A feladtok végrehajtásának ütemezése a csomópontokon.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Mindegyik tevékenység letölti a bemeneti adatait az Azure Storage-ból, majd elkezdi a végrehajtást.<br/>
[**6. lépés**](#step-6-monitor-tasks) Tevékenységek figyelése.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** A befejezett tevékenységek feltöltik a kimeneti adataikat az Azure Storage-ba.<br/>
[**7. lépés**](#step-7-download-task-output) Tevékenység kimenetének letöltése a Storage-ból.

Ahogy említettük, nem minden Batch-megoldás végzi el pontosan ezeket a lépéseket, és sok más lépést is tartalmazhatnak, de ez a minta bemutatja a Batch-megoldások általános folyamatait.

## <a name="prepare-client-script"></a>Az ügyfélparancsfájl előkészítése
A minta futtatása előtt adja hozzá a Batch- és a Storage-fiók hitelesítő adatait a *python_tutorial_client.py* fájlhoz. Ha még nem tette meg, nyissa meg a fájlt a kedvenc szerkesztőjében, és frissítse a következő sorokat a hitelesítő adataival.

```python
# Update the Batch and Storage account credential strings below with the values
# unique to your accounts. These are used when constructing connection strings
# for the Batch and Storage client objects.

# Batch account credentials
batch_account_name = "";
batch_account_key  = "";
batch_account_url  = "";

# Storage account credentials
storage_account_name = "";
storage_account_key  = "";
```

A Batch- és a Storage-fiók hitelesítő adatai a szolgáltatások fiókpaneljein találhatók az [Azure Portalon][azure_portal]:

![Batch hitelesítő adatai a portálon][9]
![Storage hitelesítő adatai a portálon][10]<br/>

A következő szakaszokban a parancsfájlok által a számítási feladatok Batch-szolgáltatásban való feldolgozásához használt lépéseket elemezzük. Javasoljuk, hogy rendszeresen tekintse meg a szerkesztőben lévő parancsfájlokat cikk további részének feldolgozása közben.

Keresse meg a következő sort a **python_tutorial_client.py** fájlban az 1. lépés elkezdéséhez:

```python
if __name__ == '__main__':
```

## <a name="step-1-create-storage-containers"></a>1. lépés: Storage-tárolók létrehozása
![Tárolók létrehozása az Azure Storage-ban][1]
<br/>

A Batch beépített támogatást tartalmaz az Azure Storage használatához. A Storage-fiókban lévő tárolók biztosítják a Batch-fiókban futó tevékenységekhez szükséges fájlokat. A tárolók a tevékenységek által létrehozott kimeneti adatok tárolásához is helyet biztosítanak. A *python_tutorial_client.py* parancsfájl először három tárolót hoz létre az [Azure Blob Storage](../storage/storage-introduction.md#blob-storage)-tárolóban:

* **application**: Ez a tároló tárolja a feladatok által futtatott, *python_tutorial_task.py* nevű Python-parancsfájlt.
* **input**: A tevékenységek az *input* tárolóból töltik le a feldolgozni kívánt adatfájlokat.
* **output**: Amikor a tevékenységek befejezik a bemeneti fájl feldolgozását, feltöltik az eredményeket a *output* tárolóba.

A Storage-fiók használatához és a tárolók létrehozásához az [azure-storage][pypi_storage] csomaggal hozunk létre egy [BlockBlobService][py_blockblobservice]-objektumot – a „blob-ügyfelet”. Ezután három tárolót hozunk létre a Storage-fiókban a blob-ügyféllel.

```python
 # Create the blob client, for use in obtaining references to
 # blob storage containers and uploading files to containers.
 blob_client = azureblob.BlockBlobService(
     account_name=_STORAGE_ACCOUNT_NAME,
     account_key=_STORAGE_ACCOUNT_KEY)

 # Use the blob client to create the containers in Azure Storage if they
 # don't yet exist.
 app_container_name = 'application'
 input_container_name = 'input'
 output_container_name = 'output'
 blob_client.create_container(app_container_name, fail_on_exist=False)
 blob_client.create_container(input_container_name, fail_on_exist=False)
 blob_client.create_container(output_container_name, fail_on_exist=False)
```

A tárolók létrehozása után az alkalmazás feltöltheti a tevékenységek által használandó fájlokat.

> [!TIP]
> [How to use Azure Blob storage from Python](../storage/storage-python-how-to-use-blob-storage.md) (A Blob-tároló használata Pythonból) szakasz jó áttekintést nyújt az Azure Storage-tárolók és blobok használatáról. A Batch használatának elkezdésekor ez az egyik legfontosabb forrásanyag.
>
>

## <a name="step-2-upload-task-script-and-data-files"></a>2. lépés: Feladatparancsfájl és adatfájlok feltöltése
![Tevékenységalkalmazás- és bemeneti (adat-) fájlok feltöltése tárolókba][2]
<br/>

A fájl feltöltése műveletben a *python_tutorial_client.py* először az **alkalmazás-** és a **bemeneti** fájlok elérési útjainak gyűjteményeit határozza meg a helyi gép alapján. Ezután feltölti ezeket a fájlokat az előző lépésben létrehozott tárolókba.

```python
 # Paths to the task script. This script will be executed by the tasks that
 # run on the compute nodes.
 application_file_paths = [os.path.realpath('python_tutorial_task.py')]

 # The collection of data files that are to be processed by the tasks.
 input_file_paths = [os.path.realpath('./data/taskdata1.txt'),
                     os.path.realpath('./data/taskdata2.txt'),
                     os.path.realpath('./data/taskdata3.txt')]

 # Upload the application script to Azure Storage. This is the script that
 # will process the data files, and is executed by each of the tasks on the
 # compute nodes.
 application_files = [
     upload_file_to_container(blob_client, app_container_name, file_path)
     for file_path in application_file_paths]

 # Upload the data files. This is the data that will be processed by each of
 # the tasks executed on the compute nodes in the pool.
 input_files = [
     upload_file_to_container(blob_client, input_container_name, file_path)
     for file_path in input_file_paths]
```

A rendszer listaértelmezéssel hívja meg az `upload_file_to_container` függvényt a gyűjtemények egyes fájljaihoz, és két [ResourceFile][py_resource_file] gyűjteményt tölt fel. Az `upload_file_to_container` függvényt alul láthatja:

```python
def upload_file_to_container(block_blob_client, container_name, file_path):
    """
    Uploads a local file to an Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param str container_name: The name of the Azure Blob storage container.
    :param str file_path: The local path to the file.
    :rtype: `azure.batch.models.ResourceFile`
    :return: A ResourceFile initialized with a SAS URL appropriate for Batch
    tasks.
    """
    blob_name = os.path.basename(file_path)

    print('Uploading file {} to container [{}]...'.format(file_path,
                                                          container_name))

    block_blob_client.create_blob_from_path(container_name,
                                            blob_name,
                                            file_path)

    sas_token = block_blob_client.generate_blob_shared_access_signature(
        container_name,
        blob_name,
        permission=azureblob.BlobPermissions.READ,
        expiry=datetime.datetime.utcnow() + datetime.timedelta(hours=2))

    sas_url = block_blob_client.make_blob_url(container_name,
                                              blob_name,
                                              sas_token=sas_token)

    return batchmodels.ResourceFile(file_path=blob_name,
                                    blob_source=sas_url)
```

### <a name="resourcefiles"></a>ResourceFiles
A [ResourceFile][py_resource_file] tevékenységeket biztosít a Batchben az Azure Storage azon fájljának URL-jével, amely a számítási csomópontra töltődik le a feladat futtatása előtt. A [ResourceFile][py_resource_file].**blob_source** tulajdonság úgy határozza meg a fájl teljes URL-jét, ahogyan az az Azure Storage-ban található. Az URL a fájl biztonságos elérését nyújtó közös hozzáférésű jogosultságkódot (SAS-t) is tartalmazhat. A Batch legtöbb feladattípusa tartalmaz *ResourceFiles* tulajdonságot, beleértve a következőket:

* [CloudTask][py_task]
* [StartTask][py_starttask]
* [JobPreparationTask][py_jobpreptask]
* [JobReleaseTask][py_jobreltask]

Ez a minta nem használja a JobPreparationTask és a JobReleaseTask tevékenységtípust, azonban további információt tekinthet meg ezekkel kapcsolatban a [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Feladat-előkészítési és -befejezési műveletek futtatása Azure Batch számítási csomópontokon) című témakörben.

### <a name="shared-access-signature-sas"></a>Közös hozzáférésű jogosultságkód (SAS)
A közös hozzáférésű jogosultságkódok olyan karakterláncok, amelyek biztonságos hozzáférést nyújtanak a tárolókhoz és a blobokhoz az Azure Storage-ban. A *python_tutorial_client.py* parancsfájl a blobok és a tárolók közös hozzáférésű jogosultságkódjait is használja, és bemutatja, hogyan szerezheti be a közös hozzáférésű jogosultságkódok karakterláncait a Storage szolgáltatásból.

* **Blob közös hozzáférésű jogosultságkódjai**: A készlet StartTask tevékenysége blobok közös hozzáférésű jogosultságkódjait használja a feladatparancsfájlnak és a bemeneti adatfájloknak a Storage-ból való letöltéséhez (lásd az alábbi [3. lépést](#step-3-create-batch-pool)). A *python_tutorial_client.py* `upload_file_to_container` függvénye tartalmazza a kódot, amely lekéri az egyes blobok közös hozzáférésű jogosultságkódját. Ezt a [BlockBlobService.make_blob_url][py_make_blob_url] meghívásával végzi el a Storage modulban.
* **Tároló közös hozzáférésű jogosultságkódja**: Amikor az egyes tevékenységek befejezik a munkájukat a számítási csomóponton, feltöltik a kimeneti fájljukat az Azure Storage *output* tárolójába. Ehhez a *python_tutorial_task.py* tároló közös hozzáférésű jogosultságkódot használ, amely írási hozzáférést nyújt a tárolóhoz. A *python_tutorial_client.py* `get_container_sas_token` függvénye beszerzi a tároló közös hozzáférésű jogosultságkódját, amelyet ezután parancssori argumentumként kapnak meg a feladatok. Az 5. lépés, a [Tevékenységek hozzáadása a feladathoz](#step-5-add-tasks-to-job), a tároló SAS használatát tárgyalja.

> [!TIP]
> A Storage-fiókban található adatok biztonságos elérésének biztosításával kapcsolatos további információkért tekintse meg a közös hozzáférésű jogosultságkódokkal kapcsolatos, két részből álló sorozatot: [Part 1: Understanding the SAS model](../storage/storage-dotnet-shared-access-signature-part-1.md) (1. rész: A SAS-modell ismertetése) és a [Part 2: Create and use a SAS with the Blob service](../storage/storage-dotnet-shared-access-signature-part-2.md) (2. rész: SAS létrehozása és használata a Blob szolgáltatással).
>
>

## <a name="step-3-create-batch-pool"></a>3. lépés: Batch-készlet létrehozása
![Batch-készlet létrehozása][3]
<br/>

A Batch-**készletek** olyan számítási csomópontok (virtuális gépek) gyűjteményei, amelyeken a Batch a feladatok tevékenységeit végzi el.

Miután feltölti a feladatparancsfájlt és az adatfájlokat a Storage-fiókba, a *python_tutorial_client.py* a Batch Python-modullal kezdi el a kommunikációt a Batch szolgáltatással. Ehhez létrejön egy [BatchServiceClient][py_batchserviceclient]:

```python
 # Create a Batch service client. We'll now be interacting with the Batch
 # service in addition to Storage.
 credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                              _BATCH_ACCOUNT_KEY)

 batch_client = batch.BatchServiceClient(
     credentials,
     base_url=_BATCH_ACCOUNT_URL)
```

Ezután a számítási csomópontok készlete jön létre a Batch-fiókban a `create_pool` hívásával.

```python
def create_pool(batch_service_client, pool_id,
                resource_files, publisher, offer, sku):
    """
    Creates a pool of compute nodes with the specified OS settings.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str pool_id: An ID for the new pool.
    :param list resource_files: A collection of resource files for the pool's
    start task.
    :param str publisher: Marketplace image publisher
    :param str offer: Marketplace image offer
    :param str sku: Marketplace image sku
    """
    print('Creating pool [{}]...'.format(pool_id))

    # Create a new pool of Linux compute nodes using an Azure Virtual Machines
    # Marketplace image. For more information about creating pools of Linux
    # nodes, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/

    # Specify the commands for the pool's start task. The start task is run
    # on each node as it joins the pool, and when it's rebooted or re-imaged.
    # We use the start task to prep the node for running our task script.
    task_commands = [
        # Copy the python_tutorial_task.py script to the "shared" directory
        # that all tasks that run on the node have access to.
        'cp -r $AZ_BATCH_TASK_WORKING_DIR/* $AZ_BATCH_NODE_SHARED_DIR',
        # Install pip and the dependencies for cryptography
        'apt-get update',
        'apt-get -y install python-pip',
        'apt-get -y install build-essential libssl-dev libffi-dev python-dev',
        # Install the azure-storage module so that the task script can access
        # Azure Blob storage
        'pip install azure-storage']

    # Get the node agent SKU and image reference for the virtual machine
    # configuration.
    # For more information about the virtual machine configuration, see:
    # https://azure.microsoft.com/documentation/articles/batch-linux-nodes/
    sku_to_use, image_ref_to_use = \
        common.helpers.select_latest_verified_vm_image_with_node_agent_sku(
            batch_service_client, publisher, offer, sku)

    new_pool = batch.models.PoolAddParameter(
        id=pool_id,
        virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
            image_reference=image_ref_to_use,
            node_agent_sku_id=sku_to_use),
        vm_size=_POOL_VM_SIZE,
        target_dedicated=_POOL_NODE_COUNT,
        start_task=batch.models.StartTask(
            command_line=
            common.helpers.wrap_commands_in_shell('linux', task_commands),
            run_elevated=True,
            wait_for_success=True,
            resource_files=resource_files),
        )

    try:
        batch_service_client.pool.add(new_pool)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Készlet létrehozásakor egy [PoolAddParameter][py_pooladdparam] paramétert ad meg, amely a készlet több tulajdonságát meghatározza:

* A készlet **azonosítója** (*id* – kötelező)<p/>A Batch legtöbb entitásához hasonlóan az új készletnek egyedi azonosítóval kell rendelkeznie a Batch-fiókban. A kód erre a készletre hivatkozik az azonosítót használva, és így azonosítható a készlet az Azure [Portalon][azure_portal].
* **Számítási csomópontok száma** (*target_dedicated* – kötelező)<p/>Ez a tulajdonság azt határozza meg, hogy hány virtuális gépet kell üzembe helyezni a készletben. Fontos megjegyezni, hogy az összes Batch-fiók alapértelmezett **kvótával** rendelkezik, amely korlátozza a **magok** (és így a számítási csomópontok) számát a Batch-fiókokban. Az alapértelmezett kvótákat és a [kvóták növelésével](batch-quota-limit.md#increase-a-quota) (például a Batch-fiókban lévő magok maximális számának növelésével) kapcsolatos útmutatásokat [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Az Azure Batch szolgáltatás kvótái és korlátai) című témakörben találja. Ha azt kérdezi magától, hogy „Miért nem ér el a készletem X-nél több csomópontot?”, akkor a magkvóta lehet ennek az oka.
* Csomópontok **operációs rendszere** (*virtual_machine_configuration* **vagy** *cloud_service_configuration* – kötelező)<p/>A *python_tutorial_client.py* fájlban létrehozzuk a Linux-csomópontok egy készletét a [VirtualMachineConfiguration][py_vm_config] osztállyal. A `common.helpers` `select_latest_verified_vm_image_with_node_agent_sku` függvénye leegyszerűsíti az [Azure Virtual Machines-piactér][vm_marketplace] rendszerképeinek használatát. További információ a piactérről származó rendszerképek használatával kapcsolatban: [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Linux számítási csomópontok létrehozása Azure Batch-készletekben).
* **Számítási csomópontok mérete** (*vm_size* – kötelező)<p/>Mivel Linux-csomópontokat határozunk meg a [VirtualMachineConfiguration][py_vm_config] számára, megadunk egy virtuálisgép-méretet (`STANDARD_A1` ebben a példában) [az Azure-ban található virtuális gépek méreteivel](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) foglalkozó szakaszban. Lásd ismét: [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md) (Linux számítási csomópontok létrehozása Azure Batch-készletekben).
* **Tevékenység indítása** (*start_task* – nem kötelező)<p/>A fenti fizikaicsomópont-tulajdonságok mellett egy [StartTask][py_starttask] is megadható a készlethez (nem kötelező). A StartTask mindegyik csomóponton fut, amikor a csomópont csatlakozik a készlethez, vagy amikor újraindul. A StartTask különösen hasznos a számítási csomópontok tevékenységek végrehajtására való előkészítéséhez, például a tevékenységek által futtatott alkalmazások telepítéséhez.<p/>Ebben a mintaalkalmazásban a StartTask átmásolja a Storage-ból letöltött fájlokat (amelyek a StartTask **resource_files** tulajdonságával határozhatók meg) a StartTask *munkakönyvtárból* abba a *megosztott* könyvtárba, amelyet a csomóponton futó összes tevékenység elér. Ez lényegében mindegyik csomóponton a megosztott könyvtárba másolja a `python_tutorial_task.py` fájlt, amikor a csomópont csatlakozik a készlethez, hogy a csomóponton futó összes tevékenység elérje a fájlt.

Bizonyára észrevette a `wrap_commands_in_shell` segédfüggvényre irányuló hívást. Ez a függvény különálló parancsok gyűjteményéből hoz létre a tevékenység parancssori tulajdonságának megfelelő egyetlen parancssort.

A fenti kódrészletben a StartTask **command_line** tulajdonságában lévő két környezeti változó használatát is érdemes kiemelni: az `AZ_BATCH_TASK_WORKING_DIR` és az `AZ_BATCH_NODE_SHARED_DIR` változóét. A Batch-készletben lévő összes számítási csomópont konfigurálása automatikusan történik a Batch-készletre jellemző számos környezeti változóval. A tevékenységek által futtatott összes folyamat hozzáféréssel rendelkezik ezekhez a környezeti változókhoz.

> [!TIP]
> A Batch-készletekben található számítási csomópontokon elérhető környezeti változókkal és a feladatok munkakönyvtárával kapcsolatban [Az Azure Batch szolgáltatásainak áttekintése](batch-api-basics.md) című témakör „**Környezeti beállítások feladatok esetén**” és „**Fájlok és könyvtárak**” szakaszában tekinthet meg további információt.
>
>

## <a name="step-4-create-batch-job"></a>4. lépés: Batch-feladat létrehozása
![Batch-feladat létrehozása][4]<br/>

A Batch-**feladatok** számítási csomópontok készletéhez társított tevékenységek gyűjteményei. A feladatok tevékenységei a társított készlet számítási csomópontjain futnak.

A feladatokat nemcsak a tevékenységek rendszerezéséhez és nyomon követéséhez használhatja a kapcsolódó számítási feladatokban, hanem bizonyos kényszerek betartatásához is – mint például a feladat (és annak tevékenységei) maximális futásideje, valamint a feladat prioritása a Batch-fiókban lévő egyéb feladatokhoz képest. Ebben a példában azonban a feladat csak a 3. lépésben létrehozott készlettel van társítva. Nincsenek konfigurálva további tulajdonságok.

Mindegyik Batch-feladat egy adott készlettel van társítva. Ez a társítás jelzi, hogy a feladat tevékenységei melyik csomópontokon futnak. A készletet a [PoolInformation][py_poolinfo] tulajdonsággal határozhatja meg, ahogyan azt az alábbi kódrészlet is mutatja.

```python
def create_job(batch_service_client, job_id, pool_id):
    """
    Creates a job with the specified ID, associated with the specified pool.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID for the job.
    :param str pool_id: The ID for the pool.
    """
    print('Creating job [{}]...'.format(job_id))

    job = batch.models.JobAddParameter(
        job_id,
        batch.models.PoolInformation(pool_id=pool_id))

    try:
        batch_service_client.job.add(job)
    except batchmodels.batch_error.BatchErrorException as err:
        print_batch_exception(err)
        raise
```

Most, hogy létrejött egy feladat, hozzáadhatók a munka végrehajtásáért felelős feladatok.

## <a name="step-5-add-tasks-to-job"></a>5. lépés: Tevékenységek hozzáadása a feladathoz
![Tevékenységek hozzáadása a feladathoz][5]<br/>
*(1) A feladathoz hozzáadja a tevékenységeket, (2) ütemezi a tevékenységeket a csomópontokon való futáshoz, és (3) a tevékenységek letöltik a feldolgozni kívánt adatfájlokat*

A Batch-**tevékenységek** a számítási csomópontokon futtatott egyéni munkaegységek. A tevékenységek parancssorral rendelkeznek, és a parancssorban megadott parancsfájlokat vagy futtatható fájlokat futtatnak.

Egy feladat tényleges elvégzéséhez tevékenységeket kell hozzáadnia a feladathoz. Mindegyik [CloudTask][py_task] egy parancssori tulajdonsággal és [ResourceFiles][py_resource_file] használatával konfigurálható (mint a készlet StartTask tevékenysége is), amelyet a tevékenység letölt a csomópontra a parancssora automatikus futtatása előtt. A mintában mindegyik tevékenység csak egy fájlt dolgoz fel. A ResourceFiles gyűjtemény így egyetlen elemet tartalmaz.

```python
def add_tasks(batch_service_client, job_id, input_files,
              output_container_name, output_container_sas_token):
    """
    Adds a task for each input file in the collection to the specified job.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The ID of the job to which to add the tasks.
    :param list input_files: A collection of input files. One task will be
     created for each input file.
    :param output_container_name: The ID of an Azure Blob storage container to
    which the tasks will upload their results.
    :param output_container_sas_token: A SAS token granting write access to
    the specified Azure Blob storage container.
    """

    print('Adding {} tasks to job [{}]...'.format(len(input_files), job_id))

    tasks = list()

    for input_file in input_files:

        command = ['python $AZ_BATCH_NODE_SHARED_DIR/python_tutorial_task.py '
                   '--filepath {} --numwords {} --storageaccount {} '
                   '--storagecontainer {} --sastoken "{}"'.format(
                    input_file.file_path,
                    '3',
                    _STORAGE_ACCOUNT_NAME,
                    output_container_name,
                    output_container_sas_token)]

        tasks.append(batch.models.TaskAddParameter(
                'topNtask{}'.format(input_files.index(input_file)),
                wrap_commands_in_shell('linux', command),
                resource_files=[input_file]
                )
        )

    batch_service_client.task.add_collection(job_id, tasks)
```

> [!IMPORTANT]
> Amikor környezeti változókat érnek el (például az `$AZ_BATCH_NODE_SHARED_DIR` változót) vagy a csomópont `PATH` elemében nem található alkalmazást hajtanak végre, a feladat parancssorainak explicit módon kell meghívnia a rendszerhéjat, például a `/bin/sh -c MyTaskApplication $MY_ENV_VAR` paranccsal. Ez a követelmény nem szükséges, ha a tevékenységek a csomóponti `PATH` helyen futtatják az alkalmazást, és nem hivatkoznak környezeti változókra.
>
>

A fenti kódrészletben a `for` hurokban láthatja, hogy a tevékenység parancssora úgy van felépítve, hogy öt parancssori argumentumot továbbítson a *python_tutorial_task.py* számára:

1. **filepath**: Ez a fájl helyi elérési útja, ahogyan az a csomóponton szerepel. Az `upload_file_to_container` elemben lévő ResourceFile objektumnak a 2. lépésben történő létrehozásakor a fájlnevet használtuk ehhez a tulajdonsághoz (a ResourceFile konstruktor `file_path` paramétere). Ez azt jelzi, hogy a fájl ugyanabban a könyvtárban található a csomóponton, mint a *python_tutorial_task.py*.
2. **numwords**: Az első *N* szót a kimeneti fájlba kell írni.
3. **storageaccount**: Azon tárolót birtokló Storage-fiók neve, amelybe a tevékenység kimenetét fel kell tölteni.
4. **storagecontainer**: Azon Storage-fiók neve, amelybe a kimeneti fájlokat fel kell tölteni.
5. **sastoken**: Azon közös hozzáférésű jogosultságkód (SAS), amely írási hozzáférést biztosít a **kimeneti** tárolóhoz az Azure Storage-ban. A *python_tutorial_task.py* parancsfájl ezt a közös hozzáférésű jogosultságkódot használja a BlockBlobService-hivatkozás létrehozásakor. Ez írási hozzáférést biztosít a tárolóhoz a tárfiók elérési kulcsának igénylése nélkül.

```python
# NOTE: Taken from python_tutorial_task.py

# Create the blob client using the container's SAS token.
# This allows us to create a client that provides write
# access only to the container.
blob_client = azureblob.BlockBlobService(account_name=args.storageaccount,
                                         sas_token=args.sastoken)
```

## <a name="step-6-monitor-tasks"></a>6. lépés: Tevékenységek figyelése
![Tevékenységek figyelése][6]<br/>
*A parancsfájl (1) megfigyeli a tevékenységek végrehajtása állapotát, és (2) a tevékenységek eredményadatokat töltenek fel az Azure Storage-ba*

A feladatokhoz hozzáadott tevékenységet a rendszer automatikusan várólistára helyezi, és ütemezi a futtatásukat az adott feladathoz társított készleten belüli számítási csomópontokon. A Batch a megadott beállítások alapján elvégzi a tevékenységek várólistára helyezésével, ütemezésével, újrapróbálásával kapcsolatos minden feladatot, valamint a tevékenységekhez kapcsolódó egyéb rendszergazdai teendőket.

A tevékenységek végrehajtása sokféleképpen megfigyelhető. A *python_tutorial_client.py* `wait_for_tasks_to_complete` függvénye adott állapotra vonatkozó megfigyelési feladatok egyszerű példáját nyújtja, ebben az esetben a [befejezett][py_taskstate] állapotra vonatkozót.

```python
def wait_for_tasks_to_complete(batch_service_client, job_id, timeout):
    """
    Returns when all tasks in the specified job reach the Completed state.

    :param batch_service_client: A Batch service client.
    :type batch_service_client: `azure.batch.BatchServiceClient`
    :param str job_id: The id of the job whose tasks should be to monitored.
    :param timedelta timeout: The duration to wait for task completion. If all
    tasks in the specified job do not reach Completed state within this time
    period, an exception will be raised.
    """
    timeout_expiration = datetime.datetime.now() + timeout

    print("Monitoring all tasks for 'Completed' state, timeout in {}..."
          .format(timeout), end='')

    while datetime.datetime.now() < timeout_expiration:
        print('.', end='')
        sys.stdout.flush()
        tasks = batch_service_client.task.list(job_id)

        incomplete_tasks = [task for task in tasks if
                            task.state != batchmodels.TaskState.completed]
        if not incomplete_tasks:
            print()
            return True
        else:
            time.sleep(1)

    print()
    raise RuntimeError("ERROR: Tasks did not reach 'Completed' state within "
                       "timeout period of " + str(timeout))
```

## <a name="step-7-download-task-output"></a>7. lépés: Feladat kimenetének letöltése
![Tevékenység kimenetének letöltése a Storage-ból][7]<br/>

Most, hogy a feladat befejeződött, a tevékenységek kimenete letölthető az Azure Storage-ból. Ez a `download_blobs_from_container` hívásával végezhető el a *python_tutorial_client.py* fájlban:

```python
def download_blobs_from_container(block_blob_client,
                                  container_name, directory_path):
    """
    Downloads all blobs from the specified Azure Blob storage container.

    :param block_blob_client: A blob service client.
    :type block_blob_client: `azure.storage.blob.BlockBlobService`
    :param container_name: The Azure Blob storage container from which to
     download files.
    :param directory_path: The local directory to which to download the files.
    """
    print('Downloading all files from container [{}]...'.format(
        container_name))

    container_blobs = block_blob_client.list_blobs(container_name)

    for blob in container_blobs.items:
        destination_file_path = os.path.join(directory_path, blob.name)

        block_blob_client.get_blob_to_path(container_name,
                                           blob.name,
                                           destination_file_path)

        print('  Downloaded blob [{}] from container [{}] to {}'.format(
            blob.name,
            container_name,
            destination_file_path))

    print('  Download complete!')
```

> [!NOTE]
> A *python_tutorial_client.py* `download_blobs_from_container` függvényének meghívása határozza meg, hogy a fájlokat a kezdőkönyvtárba kell letölteni. Ezt a kimeneti helyet nyugodtan módosíthatja.
>
>

## <a name="step-8-delete-containers"></a>8. lépés: Tárolók törlése
Mivel az Azure Storage-ban lévő adatok díjkötelesek, mindig célszerű eltávolítani azokat a blobokat, amelyekre már nincs szükség a Batch-feladatokhoz. A *python_tutorial_client.py* fájlban ez a [BlockBlobService.delete_container][py_delete_container] három meghívásával végezhető el:

```python
# Clean up storage resources
print('Deleting containers...')
blob_client.delete_container(app_container_name)
blob_client.delete_container(input_container_name)
blob_client.delete_container(output_container_name)
```

## <a name="step-9-delete-the-job-and-the-pool"></a>9. lépés: A feladat és a készlet törlése
Az utolsó lépésben a rendszer arra kéri, hogy törölje a *python_tutorial_client.py* parancsfájl által létrehozott feladatot és készletet. Bár magukért a munkákért és feladatokért nem kell fizetnie, a számítási csomópontokért *igen*. Ezért ajánlott csak szükség szerint lefoglalni a csomópontokat. A nem használt készletek törlése a karbantartási folyamat része lehet.

A BatchServiceClient [JobOperations][py_job] és [PoolOperations][py_pool] művelete is rendelkezik megfelelő törlési metódusokkal, amelyeket a rendszer meghív, ha megerősíti a törlést:

```python
# Clean up Batch resources (if the user so chooses).
if query_yes_no('Delete job?') == 'yes':
    batch_client.job.delete(_JOB_ID)

if query_yes_no('Delete pool?') == 'yes':
    batch_client.pool.delete(_POOL_ID)
```

> [!IMPORTANT]
> Ne feledje, hogy a számítási erőforrások díjkötelesek – a nem használt készletek törlése minimalizálja a költségeket. Azt is vegye figyelembe, hogy a készletek törlése törli a készleten belüli összes számítási csomópontot, és hogy a csomópontokon lévő adatok nem állíthatók helyre a készlet törlése után.
>
>

## <a name="run-the-sample-script"></a>A minta parancsfájl futtatása
Amikor a *python_tutorial_client.py* parancsfájlt az oktatóanyag [kódmintájából][github_article_samples] futtatja, a konzol kimenete a következőképpen fog kinézni. Szünet következik be a `Monitoring all tasks for 'Completed' state, timeout in 0:20:00...` részen a készlet számítási csomópontjainak létrehozása, elindítása és a készlet indítási tevékenységében található parancsok futtatása során. Az [Azure Portal][azure_portal] használatával megfigyelheti a készletet, a számítási csomópontokat, a feladatokat és a tevékenységeket a végrehajtás alatt és után. Az [Azure Portallal][azure_portal] vagy a [Microsoft Azure Storage Explorer][storage_explorer] használatával tekintheti meg az alkalmazás által létrehozott Storage-erőforrásokat (tárolókat és blobokat).

> [!TIP]
> Futtassa a *python_tutorial_client.py* szkriptet az `azure-batch-samples/Python/Batch/article_samples` könyvtárból. A `common.helpers` modul importálásához relatív elérési utat használ, ezért ha nem ebből a könyvtárból futtatja a szkriptet, előfordulhat, hogy megjelenik a következő hibaüzenet: `ImportError: No module named 'common'`.
>
>

A jellemző végrehajtási idő **körülbelül 5–7 perc**, ha az alapértelmezett konfigurációban futtatja a mintát.

```
Sample start: 2016-05-20 22:47:10

Uploading file /home/user/py_tutorial/python_tutorial_task.py to container [application]...
Uploading file /home/user/py_tutorial/data/taskdata1.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata2.txt to container [input]...
Uploading file /home/user/py_tutorial/data/taskdata3.txt to container [input]...
Creating pool [PythonTutorialPool]...
Creating job [PythonTutorialJob]...
Adding 3 tasks to job [PythonTutorialJob]...
Monitoring all tasks for 'Completed' state, timeout in 0:20:00..........................................................................
  Success! All tasks reached the 'Completed' state within the specified timeout period.
Downloading all files from container [output]...
  Downloaded blob [taskdata1_OUTPUT.txt] from container [output] to /home/user/taskdata1_OUTPUT.txt
  Downloaded blob [taskdata2_OUTPUT.txt] from container [output] to /home/user/taskdata2_OUTPUT.txt
  Downloaded blob [taskdata3_OUTPUT.txt] from container [output] to /home/user/taskdata3_OUTPUT.txt
  Download complete!
Deleting containers...

Sample end: 2016-05-20 22:53:12
Elapsed time: 0:06:02

Delete job? [Y/n]
Delete pool? [Y/n]

Press ENTER to exit...
```

## <a name="next-steps"></a>Következő lépések
Nyugodtan módosítsa a *python_tutorial_client.py* és a *python_tutorial_task.py* fájlt a különböző számítási forgatókönyvekkel való kísérletezéshez. Próbáljon meg például végrehajtási késleltetést adni a *python_tutorial_task.py* elemhez, így hosszan futó feladatokat szimulálhat, és megfigyelheti őket a portálon. Próbáljon meg több tevékenységet hozzáadni, vagy módosítani a számítási csomópontok számát. Adja hozzá az ellenőrizni kívánt logikát, és engedélyezze meglévő készlet használatát a futtatási idő csökkentéséhez.

Most, hogy megismerte a Batch-megoldások alapvető munkafolyamatát, a Batch szolgáltatás további funkcióit is áttekintheti.

* Ha korábban nem használta a szolgáltatást, olvassa el [az Azure Batch szolgáltatásainak áttekintését](batch-api-basics.md) tartalmazó cikket.
* Kezdje tanulmányozni a többi Batch-fejlesztéssel kapcsolatos cikket a [Batch képzési terv][batch_learning_path] **Fejlesztés részletesebben** részében.
* Az „első N szó” számítási feladat Batch használatával való feldolgozásának egy másik megvalósítását a [TopNWords][github_topnwords] példában találja.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[crypto]: https://cryptography.io/en/latest/
[crypto_install]: https://cryptography.io/en/latest/installation/
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[github_article_samples]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/article_samples

[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build

[py_account_ops]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations
[py_azure_sdk]: https://pypi.python.org/pypi/azure
[py_batch_docs]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.html
[py_batch_package]: https://pypi.python.org/pypi/azure-batch
[py_batchserviceclient]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html#azure.batch.BatchServiceClient
[py_blockblobservice]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.blockblobservice.html#azure.storage.blob.blockblobservice.BlockBlobService
[py_cloudtask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_computenodeuser]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ComputeNodeUser
[py_cs_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudServiceConfiguration
[py_delete_container]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.delete_container
[py_gen_blob_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_blob_shared_access_signature
[py_gen_container_sas]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.generate_container_shared_access_signature
[py_image_ref]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_imagereference]: http://azure-sdk-for-python.readthedocs.org/en/latest/ref/azure.batch.models.html#azure.batch.models.ImageReference
[py_job]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.JobOperations
[py_jobpreptask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobPreparationTask
[py_jobreltask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.JobReleaseTask
[py_list_skus]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.AccountOperations.list_node_agent_skus
[py_make_blob_url]: http://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html#azure.storage.blob.baseblobservice.BaseBlobService.make_blob_url
[py_pool]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.operations.html#azure.batch.operations.PoolOperations
[py_pooladdparam]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolAddParameter
[py_poolinfo]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.PoolInformation
[py_resource_file]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.ResourceFile
[py_samples_github]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch/
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_starttask]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.StartTask
[py_task]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.CloudTask
[py_taskstate]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.TaskState
[py_vm_config]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.models.html#azure.batch.models.VirtualMachineConfiguration
[pypi_batch]: https://pypi.python.org/pypi/azure-batch
[pypi_storage]: https://pypi.python.org/pypi/azure-storage
[pypi_install]: https://packaging.python.org/installing/
[storage_explorer]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-python-tutorial/batch_workflow_01_sm.png "Tárolók létrehozása az Azure Storage-ban"
[2]: ./media/batch-python-tutorial/batch_workflow_02_sm.png "Tevékenységalkalmazás- és bemeneti (adat-) fájlok feltöltése tárolókba"
[3]: ./media/batch-python-tutorial/batch_workflow_03_sm.png "Batch-készlet létrehozása"
[4]: ./media/batch-python-tutorial/batch_workflow_04_sm.png "Batch-feladat létrehozása"
[5]: ./media/batch-python-tutorial/batch_workflow_05_sm.png "Tevékenységek hozzáadása a feladathoz"
[6]: ./media/batch-python-tutorial/batch_workflow_06_sm.png "Tevékenységek figyelése"
[7]: ./media/batch-python-tutorial/batch_workflow_07_sm.png "Tevékenység kimenetének letöltése a Storage-ból"
[8]: ./media/batch-python-tutorial/batch_workflow_sm.png "Batch-megoldás munkafolyamata (teljes diagram)"
[9]: ./media/batch-python-tutorial/credentials_batch_sm.png "A Batch hitelesítő adatai a portálon"
[10]: ./media/batch-python-tutorial/credentials_storage_sm.png "A Storage hitelesítő adatai a portálon"
[11]: ./media/batch-python-tutorial/batch_workflow_minimal_sm.png "Batch-megoldás munkafolyamata (minimális diagram)"

