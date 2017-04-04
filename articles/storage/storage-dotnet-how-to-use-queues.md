---
title: "Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel | Microsoft Docs"
description: "Az Azure-üzenetsorok megbízható, aszinkron üzenetkezelést biztosítanak az alkalmazások összetevői között. A felhőbeli üzenetkezelésnek köszönhetően az alkalmazások összetevői függetlenül méretezhetők."
services: storage
documentationcenter: .net
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: c0f82537-a613-4f01-b2ed-fc82e5eea2a7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 5876a22651a5ebee2d60992cf08aeae54256fb7d
ms.lasthandoff: 03/28/2017

---

# <a name="get-started-with-azure-queue-storage-using-net"></a>Az Azure Queue Storage használatának első lépései a .NET-keretrendszerrel
[!INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Áttekintés
Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag bemutatja, hogyan írhat .NET-kódot néhány, az Azure Queue Storage szolgáltatást használó általános forgatókönyvhöz. Az ismertetett forgatókönyvek az üzenetsorok létrehozására és törlésére, valamint az üzenetsor üzeneteinek hozzáadására, olvasására és törlésére vonatkoznak.

**Az oktatóanyag áttekintésének becsült ideje:** 45 perc

**Előfeltételek:**

* [Microsoft Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx)
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Egy [Azure-tárfiók](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Adja hozzá a következő `using` irányelveket a `Program.cs` fájl elejéhez:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="parse-the-connection-string"></a>Kapcsolati karakterlánc elemzése
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>A Queue szolgáltatásügyfél létrehozása
A **CloudQueueClient** osztály segítségével lekérheti a Queue Storage-ban tárolt üzenetsorokat. A szolgáltatásügyfél létrehozásának egyik módja:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```
    
Most már készen áll a Queue Storage-ból adatokat olvasó és abba adatokat író kód írására.

## <a name="create-a-queue"></a>Üzenetsor létrehozása
A példa bemutatja, hogyan hozhat létre üzenetsort, ha még nem rendelkezik vele:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Üzenet beszúrása egy üzenetsorba
Ha üzenetet szeretne beszúrni egy létező üzenetsorba, először hozzon létre egy **CloudQueueMessage** elemet. Ezután hívja meg az **AddMessage** módszert. A **CloudQueueMessage** egy karakterláncból (UTF-8 formátumban) vagy egy **bájttömbből** hozható létre. Az alábbi kód létrehoz egy üzenetsort (ha még nem létezik), és beszúrja a „Hello, World” üzenetet:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Betekintés a következő üzenetbe
Egy üzenetsor elején található üzenetbe anélkül is bepillanthat, hogy eltávolítaná az üzenetsorból. Ehhez hívja meg a **PeekMessage** módszert.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Üzenetsorban található üzenet tartalmának módosítása
Egy üzenetet tartalmát helyben, az üzenetsorban módosíthatja. Ha az üzenet munkafeladatot jelöl, ezzel a funkcióval frissítheti a munkafeladat állapotát. Az alábbi kód frissíti az üzenetsorban található üzenetet az új tartalommal, és a láthatósági időkorlátot további 60 másodperccel bővíti. Elmenti az üzenethez társított feladat állapotát, és az ügyfél számára további egy percet biztosít az üzenet használatának folytatására. Ezzel a technikával többlépéses munkafolyamatokat is nyomon követhet az üzenetsor üzenetein anélkül, hogy újra kéne kezdenie, ha a folyamat valamelyik lépése hardver- vagy szoftverhiba miatt meghiúsul. A rendszer általában nyilván tartja az újrapróbálkozások számát, és ha az üzenettel *n* alkalomnál többször próbálkoznak, akkor törlődik. Ez védelmet biztosít az ellen, hogy egy üzenetet minden feldolgozásakor kiváltson egy alkalmazáshibát.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent("Updated contents.");
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>A következő üzenet kivétele az üzenetsorból
A kód két lépésben távolít el egy üzenetet az üzenetsorból. A **GetMessage** meghívásával lekéri az üzenetsor következő üzenetét. A **GetMessage** módszerrel lekért üzenet láthatatlanná válik az adott üzenetsorban található üzeneteket olvasó többi kód számára. Alapértelmezés szerint az üzenet 30 másodpercig marad láthatatlan. Ha végleg el szeretné távolítani az üzenetet az üzenetsorból, meg kell hívnia a **DeleteMessage** módszert is. Az üzenetek kétlépéses eltávolítása lehetővé teszi, hogy ha a kód hardver- vagy szoftverhiba miatt nem tud feldolgozni egy üzenetet, a kód egy másik példánya megkaphassa ugyanazt az üzenetet, és újra megpróbálkozhasson a feldolgozásával. A kód a **DeleteMessage** módszert rögtön az üzenet feldolgozása után hívja meg.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Async-Await mintázat használata közös Queue Storage API-kkal
Ez a példa bemutatja, hogyan használható az Async-Await mintázat a közös Queue Storage API-kkal. A minta meghívja az adott módszerek aszinkron verzióját. Az aszinkronitást az egyes módszerek *Async* utótagja jelöli. Ha Async módszert használ, az Async-Await mintázat felfüggeszti a helyi végrehajtást a hívás befejeződéséig. Ez a viselkedés lehetővé teszi, hogy az aktuális szál más feladatokkal foglalkozzon. Ennek segítségével elkerülhetők a szűk keresztmetszetek a teljesítményben, és az alkalmazás általános válaszkészsége is javul. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Async és Await (C# és Visual Basic)).

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```
    
## <a name="leverage-additional-options-for-de-queuing-messages"></a>Egyéb lehetőségek az üzenetek eltávolításához az üzenetsorból
Két módon szabhatja testre az üzenetek lekérését egy üzenetsorból.
Az első lehetőség az üzenetkötegek (legfeljebb 32) lekérése. A második lehetőség az, hogy beállít egy hosszabb vagy rövidebb láthatatlansági időkorlátot, így a kódnak lehetősége van hosszabb vagy rövidebb idő alatt teljesen feldolgozni az egyes üzeneteket. Az alábbi példakód a **GetMessages** módszer segítségével egyszerre 20 üzenetet kér le. Ezután minden üzenetet feldolgoz egy **foreach** hurok segítségével. Mindemellett a láthatatlansági időkorlátot minden üzenethez öt percre állítja be. Vegye figyelembe, hogy az 5 perc minden üzenetnél ugyanakkor kezdődik, tehát 5 perccel a **GetMessages** hívása után a nem törölt üzenetek újra láthatóvá válnak.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Az üzenetsor hosszának lekérése
Megbecsülheti egy üzenetsorban található üzenetek számát. A **FetchAttributes** módszer lekéri a Queue szolgáltatásból az üzenetsorra vonatkozó attribútumokat, amelyek között megtalálható az üzenetek száma is. Az **ApproximateMessageCount** tulajdonság visszaadja a **FetchAttributes** módszer által lekért legutóbbi értéket a Queue szolgáltatás hívása nélkül.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Üzenetsor törlése
Egy üzenetsor és az összes benne foglalt üzenet törléséhez hívja meg a **Delete** módszert az üzenetsor-objektumhoz.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```
    

## <a name="next-steps"></a>Következő lépések
Most, hogy már megismerte a Queue Storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről is.

* A Queue szolgáltatás elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:
  * [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API – referencia](http://msdn.microsoft.com/library/azure/dd179355)
* Megtudhatja, hogyan egyszerűsítheti az [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) használatával az Azure Storage használatához írt kódot.
* Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
  * [Get started with Azure Table Storage using .NET](storage-dotnet-how-to-use-tables.md) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel) a strukturált adatok tárolásához.
  * [Get started with Azure Blob storage using .NET](storage-dotnet-how-to-use-blobs.md) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel) a strukturálatlan adatok tárolásához.
  * [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../sql-database/sql-database-develop-dotnet-simple.md) a relációs adatok tárolásához.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

