---
title: "Get started with Azure Blob storage (object storage) using .NET (Az Azure Blob Storage (objektumtár) használatának első lépései a .NET-keretrendszerrel) | Microsoft Docs"
description: "Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló))."
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: d18a8fc8-97cb-4d37-a408-a6f8107ea8b3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 29d8693dd1d6d1ef26ccb21e3a5b29cf1adbfcc5
ms.lasthandoff: 03/28/2017

---
# <a name="get-started-with-azure-blob-storage-using-net"></a>Get started with Azure Blob Storage using .NET (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel)

[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

Az Azure Blob Storage egy olyan szolgáltatás, amely a strukturálatlan adatokat objektumként/blobként tárolja a felhőben. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag bemutatja, hogyan írhat .NET-kódot néhány, az Azure Blob Storage szolgáltatást használó általános forgatókönyvhöz. A tárgyalt forgatókönyvekben szerepel a blobok feltöltése, listázása, letöltése és törlése.

**Előfeltételek:**

* [Microsoft Visual Studio](https://www.visualstudio.com/)
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Egy [Azure-tárfiók](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>További példák
További példák a Blob Storage használatára: [Getting Started with Azure Blob Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel). Letöltheti és futtathatja a mintaalkalmazást, vagy megkeresheti a kódot a GitHubon.

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Adja hozzá a következő **using** irányelveket a `Program.cs` fájl elejéhez:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Blob; // Namespace for Blob storage types
```

### <a name="parse-the-connection-string"></a>Kapcsolati karakterlánc elemzése
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-blob-service-client"></a>A Blob szolgáltatásügyfél létrehozása
A **CloudBlobClient** osztály lehetővé teszi a Blob Storage-fiókban tárolt tárolók és blobok lekérését. A szolgáltatásügyfél létrehozásának egyik módja:

```csharp
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```
Most már készen áll a Blob Storage-ból adatokat olvasó és abba adatokat író kód írására.

## <a name="create-a-container"></a>Tároló létrehozása
[!INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

A példa bemutatja, hogyan hozhat létre tárolót, ha még nem rendelkezik vele:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();
```

Alapértelmezés szerint az új tároló privát, azaz mielőtt blobokat tölthetne le ebből a tárolóból, meg kell adnia a tárelérési kulcsát. Ha a tárolóban lévő fájlokat mindenki számára elérhetővé kívánja tenni, akkor az alábbi kód segítségével nyilvánossá teheti a tárolót:

```csharp
container.SetPermissions(
    new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });
```

A nyilvános tárolókban lévő blobokat bármely internetfelhasználó megtekintheti. De csak azok módosíthatják vagy törölhetik őket, akik rendelkeznek a megfelelő fiókelérési kulccsal vagy közös hozzáférésű jogosultságkóddal.

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba
Az Azure Blob Storage támogatja a blokkblobokat és a lapblobokat.  A legtöbb esetben a blokkblobok használata javasolt.

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Ha megszerezte a blobhivatkozást, akkor az **UploadFromStream** metódus hívásával bármilyen streamet feltölthet. Ez az eljárás létrehozza a blobot, ha az még nem létezett, és felülírja, ha már igen.

Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with contents from a local file.
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    blockBlob.UploadFromStream(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>A tárolóban lévő blobok listázása
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. Ezt követően a tároló **ListBlobs** metódusának segítéségével lekérheti a benne lévő blobokat és/vagy könyvtárakat. A **IListBlobItem** parancs visszaadásakor elérhető számos tulajdonság és metódus eléréséhez át kell alakítania azt **CloudBlockBlob**, **CloudPageBlob** vagy **CloudBlobDirectory** objektummá. Ha a típus ismereten, akkor a típusellenőrzés segítségével határozza meg, hogy milyen típussá kell átalakítani. Az alábbi kód bemutatja, hogyan kérhető le és küldhető el a _photos_ tárolóban lévő egyes elemek URI azonosítója:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("photos");

// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, false))
{
    if (item.GetType() == typeof(CloudBlockBlob))
    {
        CloudBlockBlob blob = (CloudBlockBlob)item;

        Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

    }
    else if (item.GetType() == typeof(CloudPageBlob))
    {
        CloudPageBlob pageBlob = (CloudPageBlob)item;

        Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

    }
    else if (item.GetType() == typeof(CloudBlobDirectory))
    {
        CloudBlobDirectory directory = (CloudBlobDirectory)item;

        Console.WriteLine("Directory: {0}", directory.Uri);
    }
}
```

Ha belefoglalja az elérési úttal kapcsolatos információkat a blobnevekbe, olyan virtuális könyvtárstruktúrát hozhat létre, amelyet egy hagyományos fájlrendszerhez hasonlóan rendszerezhet és bejárhat. A könyvtárstruktúra csupán virtuális – a Blob Storage szolgáltatásban elérhető erőforrások kizárólag a tárolók és a blobok. A Storage ügyféloldali kódtára azonban **CloudBlobDirectory** objektumot biztosít a virtuális könyvtárra való hivatkozáshoz és az így rendszerezett blobokkal való munkavégzés folyamatainak egyszerűsítéséhez.

Vegyük példaként az alábbi blokkblobokat a *photos* nevű tárolóban:

```
photo1.jpg
2010/architecture/description.txt
2010/architecture/photo3.jpg
2010/architecture/photo4.jpg
2011/architecture/photo5.jpg
2011/architecture/photo6.jpg
2011/architecture/description.txt
2011/photo7.jpg
```

A **ListBlobs** meghívása a *photos* tárolóban (az előző kódrészlet szerint) hierarchikus listát ad vissza. **CloudBlobDirectory** és **CloudBlockBlob** objektumokat is tartalmaz, amelyek a tárolóban található kódtárakat, illetve blobokat jelölik. Ennek kimenete a következőképpen néz ki:

```
Directory: https://<accountname>.blob.core.windows.net/photos/2010/
Directory: https://<accountname>.blob.core.windows.net/photos/2011/
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

Beállíthatja a **ListBlobs** metódus **UseFlatBlobListing** paraméterét **true** értékre. Ebben az esetben a tárolóban található minden blobot **CloudBlockBlob** objektumként adja vissza a rendszer. A **ListBlobs** hívása egy strukturálatlan lista visszaadásáért így néz ki:

```csharp
// Loop over items within the container and output the length and URI.
foreach (IListBlobItem item in container.ListBlobs(null, true))
{
    ...
}
```

az eredmények pedig így néznek ki:

```
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg
```

## <a name="download-blobs"></a>Blobok letöltése
Blobok letöltéséhez először kérjen le egy blobhivatkozást, majd hívja a **DownloadToStream** metódust. A következő példa a **DownloadToStream** metódus segítségével helyezi át a blob tartalmát egy stream objektumra, amelyet egy helyi fájlban megőrizhet.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save blob contents to a file.
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    blockBlob.DownloadToStream(fileStream);
}
```

A **DownloadToStream** metódussal a blob tartalmát szöveges karakterláncként is letöltheti.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt"
CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

string text;
using (var memoryStream = new MemoryStream())
{
    blockBlob2.DownloadToStream(memoryStream);
    text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
}
```

## <a name="delete-blobs"></a>Blobok törlése
Blobok törléséhez először kérjen le egy blobhivatkozást, majd hívja a **Delete** metódust.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve reference to a previously created container.
CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

// Retrieve reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
blockBlob.Delete();
```

## <a name="list-blobs-in-pages-asynchronously"></a>Blobok listázása több oldalon aszinkron módon
Nagyszámú blob listázásakor, vagy ha szabályozni szeretné a listázási művelet által megjelenített eredmények számát, a blobokat több eredményoldalra is listázhatja. Ez a példa bemutatja, hogyan kérhetők le eredmények több oldalon aszinkron módon úgy, hogy amíg egy nagy eredménykészletre várakozik, a rendszer ne tiltsa le a végrehajtást.

Ez a lista egy strukturálatlan bloblistát mutat be, de hierarchikus listát is kérhet, ha a _useFlatBlobListing_ paramétert a **ListBlobsSegmentedAsync** metódusban átállítja _false_ értékre.

Mivel a mintametódus aszinkrón metódust használ, előtagként az _async_ kulcsszót kell megadni, és **Task** objektumot kell visszaadnia. A **ListBlobsSegmentedAsync** metódushoz megadott await kulcsszó a listázási feladat befejezéséig felfüggeszti a mintametódus futtatását.

```csharp
async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
{
    //List blobs to the console window, with paging.
    Console.WriteLine("List blobs in pages:");

    int i = 0;
    BlobContinuationToken continuationToken = null;
    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

## <a name="writing-to-an-append-blob"></a>Írás hozzáfűző blobba
A hozzáfűző blob hozzáfűzési feladatokra, például naplózásra van optimalizálva. A blokkblobhoz hasonlóan a hozzáfűző blobot is blokkok alkotják, de ha egy hozzáfűző blobhoz új blokkot ad hozzá, az mindig a blob végéhez lesz hozzáfűzve. Hozzáfűző blob meglévő blokkja nem frissíthető és nem törölhető. A hozzáfűző blob blokkazonosítói nincsenek közzétéve, mert azok egy blokkblobhoz tartoznak.

Egy hozzáfűző blob minden blokkja különböző méretű lehet – 4 MB maximális értékig –, egy hozzáfűző blob pedig legfeljebb 50 000 blokkot tartalmazhat. A hozzáfűző blob maximális mérete ezért valamivel több mint 195 GB (4 MB X 50 000 blokk).

Az alábbi példa új hozzáfűző blob létrehozását és adatok hozzáfűzését mutatja be, egy egyszerű naplózási műveletet szimulálva.

```csharp
//Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

//Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

//Create the container if it does not already exist.
container.CreateIfNotExists();

//Get a reference to an append blob.
CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

//Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
//You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
appendBlob.CreateOrReplace();

int numBlocks = 10;

//Generate an array of random bytes.
Random rnd = new Random();
byte[] bytes = new byte[numBlocks];
rnd.NextBytes(bytes);

//Simulate a logging operation by writing text data and byte data to the end of the append blob.
for (int i = 0; i < numBlocks; i++)
{
    appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
        DateTime.UtcNow, bytes[i], Environment.NewLine));
}

//Read the append blob to the console window.
Console.WriteLine(appendBlob.DownloadText());
```

A blobok három különböző típusa közötti különbségekről bővebb információt az [Understanding Block Blobs, Page Blobs, and Append Blobs](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) (A blokkblobok, a lapblobok és a hozzáfűző blobok ismertetése) című részben talál.

## <a name="managing-security-for-blobs"></a>A blobok biztonságának kezelése
Alapértelmezés szerint az Azure Storage úgy biztosítja az adatok védelmét, hogy a hozzáférést a fiók tulajdonosára korlátozza, aki a fiók tárelérési kulcsaival rendelkezik. Ha a tárfiókjában lévő blobadatokat kell megosztania, fontos, hogy azt a fiók tárelérési kulcsai biztonságának fenyegetése nélkül tegye. A blobadatokat titkosíthatja, hogy továbbításuk biztonságosan történjen a hálózaton és az Azure Storage szolgáltatásban.

[!INCLUDE [storage-account-key-note-include](../../includes/storage-account-key-note-include.md)]

### <a name="controlling-access-to-blob-data"></a>A blobadatokhoz való hozzáférés szabályozása
Alapértelmezés szerint a tárfiókban tárolt adatok csak a fiók tulajdonosa számára hozzáférhetőek. Alapértelmezés szerint a Blob Storage-fiókhoz intézett hitelesítési kérésekhez szükség van a tárelérési kulcs megadására. Lehetséges azonban, hogy bizonyos blobadatokat más felhasználók számára is elérhetővé szeretne tenni. Erre két lehetősége van:

* **Névtelen hozzáférés:** Egy tárolót vagy a benne található blobokat nyilvánosan elérhetővé teheti névtelen hozzáférés céljából. További információk: [Manage anonymous read access to containers and blobs](storage-manage-access-to-resources.md) (Tárolók és blobok névtelen olvasási hozzáférésének kezelése).
* **Közös hozzáférésű jogosultságkód:** A közös hozzáférésű jogosultságkód (SAS) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz az Ön által meghatározott időtartamra és jogosultságokkal. További információk: [Using Shared Access Signatures (SAS) (Közös hozzáférésű jogosultságkód (SAS) használata)](storage-dotnet-shared-access-signature-part-1.md)

### <a name="encrypting-blob-data"></a>Blobadatok titkosítása
Az Azure Storage mind az ügyfél, mind a kiszolgáló oldalán támogatja a titkosítást:

* **Ügyféloldali titkosítás:** A Storage .NET-keretrendszerhez készült ügyféloldali kódtára támogatja az adatok ügyfélalkalmazásokon belüli titkosítását az Azure Storage-ba való feltöltésük előtt, illetve az adatok visszafejtését az ügyfélre történő letöltéskor. A kódtár emellett támogatja az Azure Key Vault rendszerrel való integrálást a tárfiókkulcs-kezelés biztosítása érdekében. További információ: [Client-Side Encryption with .NET for Microsoft Azure Storage](storage-client-side-encryption.md) (Ügyféloldali titkosítás a .NET for Microsoft Azure Storage szolgáltatással). Lásd még: [Tutorial: Encrypt and decrypt blobs in Microsoft Azure Storage using Azure Key Vault](storage-encrypt-decrypt-blobs-key-vault.md) (Oktatóanyag: Blobok titkosítása és visszafejtése a Microsoft Azure Storage-ban az Azure Key Vault használatával).
* **Kiszolgálóoldali titkosítás**: Az Azure Storage már a kiszolgálóoldali titkosítást is támogatja. Lásd: [Azure Storage Service Encryption for Data at Rest (Preview)](storage-service-encryption.md) (Az Azure Storage szolgáltatás inaktívadat-titkosítása (Előzetes verzió)).

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Blob Storage alapjait, az alábbi hivatkozásokat követve tudhat meg többet.

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* A [Microsoft Azure Storage Explorer (MASE)](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

### <a name="blob-storage-samples"></a>Blob Storage-minták
* [Getting Started with Azure Blob Storage in .NET (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel)](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)

### <a name="blob-storage-reference"></a>Blob Storage-hivatkozás
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](https://msdn.microsoft.com/library/azure/mt347887.aspx)
* [REST API – referencia](/rest/api/storageservices/fileservices/azure-storage-services-rest-api-reference)

### <a name="conceptual-guides"></a>Fogalmi útmutatók
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)
* [Get started with File storage for .NET (Bevezetés a File Storage for .NET használatába)](storage-dotnet-how-to-use-files.md)
* [How to use Azure blob storage with the WebJobs SDK (Az Azure Blob Storage használata a WebJobs SDK-val)](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

