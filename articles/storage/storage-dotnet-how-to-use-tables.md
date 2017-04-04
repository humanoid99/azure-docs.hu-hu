---
title: "Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel | Microsoft Docs"
description: "Az Azure Table Storage, amely egy NoSQL-adattár, a strukturált adatok felhőben való tárolásához használható."
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/27/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 7a9a28ce8be7587c84a1188d643c990cc4fb7355
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-table-storage-using-net"></a>Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Áttekintés
Az Azure Table Storage egy olyan szolgáltatás, amely strukturált NoSQL-adatokat tárol a felhőben. A Table Storage egy séma nélküli kulcs-/attribútumtár. Mivel a Table Storage séma nélküli, az adatokat könnyen az alkalmazás változó igényeihez igazíthatja. Az adatok hozzáférése gyors és költséghatékony, bármilyen alkalmazásról legyen is szó. Hasonló adatmennyiséggel számolva a Table Storage általában határozottan kevesebb költséggel jár, mint egy hagyományos SQL.

A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

### <a name="about-this-tutorial"></a>Az oktatóanyag ismertetése
Ez az oktatóanyag bemutatja, hogyan írhat .NET kódot néhány, az Azure Table Storage szolgáltatást használó általános forgatókönyvhöz, beleértve a táblák létrehozását és törlését, valamint táblaadatok beszúrását, frissítését, törlését és lekérdezését.

**Előfeltételek:**

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Az Azure Storage .NET-hez készült ügyféloldali kódtára](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Azure Configuration Manager a .NET-hez](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* Egy [Azure-tárfiók](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>További példák
További példák a Table Storage használatára: [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel). Letöltheti és futtathatja a mintaalkalmazást, vagy megkeresheti a kódot a GitHubon.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Hozzáadás irányelvekkel
Adja hozzá a következő **using** irányelveket a `Program.cs` fájl elejéhez:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Kapcsolati karakterlánc elemzése
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>A Table szolgáltatásügyfél létrehozása
A **CloudTableClient** osztály segítségével lekérheti a Table Storage-ban tárolt táblákat és entitásokat. A szolgáltatásügyfél létrehozásának egyik módja:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Most már készen áll a Table Storage-ból adatokat olvasó és abba adatokat író kód írására.

## <a name="create-a-table"></a>Tábla létrehozása
A példa bemutatja, hogyan hozhat létre táblát, ha még nem rendelkezik vele:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Az entitásokat a rendszer C\# objektumokká képezi le egy **TableEntity** osztályból származtatott egyéni osztály használatával. Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy osztályt, amely meghatározza az entitás tulajdonságait. Az alábbi kód meghatároz egy entitásosztályt, amely az ügyfél keresztnevét használja sorkulcsnak és a vezetéknevét partíciókulcsnak. Egy entitás partíció- és sorkulcsa együttesen azonosítja az entitást a táblán belül. Az azonos partíciókulcsú entitások gyorsabban lekérdezhetők, mint a különböző partíciókulcsúak, de az eltérő partíciókulcsok használata a párhuzamos műveletek nagyobb méretezhetőségét teszi lehetővé. A Table Service szolgáltatásban tárolni kívánt tulajdonságoknak egy, a beállítási és lekérési értéket is elérhetővé tévő támogatott típus nyilvános tulajdonságának kell lenniük.
Az entitástípusnak emellett elérhetővé *kell* tennie egy paraméter nélküli konstruktort is.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

Az entitásokat is tartalmazó táblaműveleteket a korábban, a „Tábla létrehozása” szakaszban létrehozott **CloudTable** objektum végzi el. A végrehajtandó műveletet egy **TableOperation** objektum képviseli.  Az alábbi példakód bemutatja a **CloudTable** objektum, majd egy **CustomerEntity** objektum létrehozását.  A művelet előkészítéséhez létrejön egy **TableOperation** objektum, amely beszúrja az ügyfélentitást a táblába.  Maga a művelet a **CloudTable.Execute** meghívásával hajtható végre.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Entitásköteg beszúrása
Egyetlen írási művelettel egy teljes entitásköteget is beszúrhat egy táblába. Néhány további megjegyzés a kötegműveletekkel kapcsolatban:

* Egyetlen kötegművelettel frissítéseket, törléseket és beszúrásokat hajthat végre.
* Egy kötegművelet legfeljebb 100 entitást tartalmazhat.
* Egy kötegművelet összes entitásának ugyanazzal a partíciókulccsal kell rendelkeznie.
* Ugyan lekérdezést is végre lehet hajtani kötegműveletként, de ilyenkor ez lehet a köteg egyetlen művelete.

<!-- -->
Az alábbi példakód létrehoz két entitásobjektumot, és mindkettőt hozzáadja a **TableBatchOperation** művelethez az **Insert** módszerrel. Ezután meghívja a **CloudTable.Execute** objektumot a művelet végrehajtásához.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Ha egy táblából egy partíció összes entitását le szeretné kérni, használja a **TableQuery** objektumot.
Az alábbi példakód megad egy szűrőt a „Smith” partíciókulcsú entitásokra. A példa megjeleníti a konzolon a lekérdezés eredményei között szereplő entitásokhoz tartozó mezőket.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Partíció entitástartományának lekérése
Ha nem szeretné az összes entitást lekérdezni egy partícióból, megadhat egy tartományt a partíciókulcs és a sorkulcs szűrőjének kombinálásával. Az alábbi példakód két szűrő segítségével kéri le az összes olyan entitást a „Smith” partícióból, ahol a sorkulcs (keresztnév) az ábécében az „E”-t megelőző betűvel kezdődik, majd megjeleníti a lekérdezés eredményeit.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
Írhat egy lekérdezést egy adott entitás lekérdezéséhez. A következő kódban a **TableOperation** művelettel adja meg a „Ben Smith” nevű ügyfelet.
Ezzel a módszerrel a rendszer egy gyűjtemény helyett csak egyetlen entitást ad vissza, és az **ableResult.Result** táblában visszaadott érték egy **CustomerEntity** objektum.
Ha egyetlen entitást szeretne lekérdezni a Table szolgáltatásból, ennek leggyorsabb módja a partíció- és sorkulcsok megadása a lekérdezésben.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Entitás cseréje
Ha frissíteni kíván egy entitást, kérje le a Table szolgáltatásból, módosítsa az entitásobjektumot, majd mentse a módosításokat a Table szolgáltatásba. A következő kód egy meglévő ügyfél telefonszámát módosítja. Az **Insert** parancs hívása helyett a kód a **Replace** parancsot használja. A rendszer így teljesen lecseréli az entitást a kiszolgálón, hacsak az a lekérdezés óta nem módosult, mert ez esetben a művelet sikertelen lesz.  Erre a hibára azért van szükség, hogy az alkalmazás ne írhasson felül véletlenül egy olyan módosítást, amelyet az alkalmazás egy másik összetevője hozott létre a lekérés és a frissítés között.  A hiba megfelelő kezeléséhez kérje le újra az entitást, végezze el a módosításokat (ha még érvényesek), majd hajtson végre egy újabb **Replace** műveletet.  A következő szakaszban megtudhatja, hogyan bírálhatja felül ezt a viselkedést.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Entitás beszúrása vagy lecserélése
Ha az entitás a kiszolgálóról való lekérdezés óta módosult, a **Replace** műveletek sikertelenek lesznek.  Ezenkívül a **Replace** művelet sikeres végrehajtásához először le kell kérnie az entitást a kiszolgálóról.
Néha azonban nem tudható, hogy az entitás létezik-e a kiszolgálón, és hogy a benne tárolt aktuális értékek irrelevánsak-e. A frissítés mindent felülír.  Ehhez használja az **InsertOrReplace** műveletet.  Ha nem létezik az entitás, ez a művelet beszúrja, ha pedig létezik, akkor a legutóbbi frissítés idejétől függetlenül lecseréli.  Az alábbi kódpéldában a rendszer lekérdezi Ben Smith ügyfélentitását, majd az **InsertOrReplace** művelettel menti a kiszolgálóra.  Az entitáson a lekérési és a frissítési művelet között történt összes módosítást felül lesz írva.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-1234";

    // Create the InsertOrReplace TableOperation.
    TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

    // Execute the operation.
    table.Execute(insertOrReplaceOperation);

    Console.WriteLine("Entity was updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése
Egy táblalekérdezéssel egy entitás bizonyos tulajdonságait is lekérdezheti az összes helyett. Ez a leképezésnek hívott technika csökkenti a sávszélesség felhasználását, és javítja a lekérdezési teljesítményt, főleg a nagy entitások esetében. Az alábbi kódban szereplő lekérdezés csak a táblában található entitásokhoz tartozó e-mail-címeket kérdezi le. Ez a **DynamicTableEntity** és az **EntityResolver** lekérdezésekkel hajtható végre. A kivetítésről az [Introducing Upsert and Query Projection (Az upsert (frissítés/beszúrás) és a lekérdezésleképezés bemutatása) blogbejegyzés][Introducing Upsert and Query Projection blog post] című blogbejegyzésből tudhat meg többet. A helyi Storage Emulator nem támogatja a leképezést, így a kód csak a Table szolgáltatásbeli fiók használatával működik.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Entitás törlése
A lekérdezés után az entitás frissítésénél bemutatott minta alapján egyszerűen törölheti az entitásokat.  Az alábbi kód lekérdez, majd töröl egy ügyfélentitást.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Tábla törlése
Végezetül pedig az alábbi példakóddal törölhető egy tábla a tárfiókból. A törölt táblák a törlés után egy ideig nem hozhatók létre újra.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Oldalak entitásainak aszinkron lekérése
Ha sok entitást olvas, és az összes visszaadott entitás helyett csak az éppen lekérdezett entitásokat szeretné feldolgozni/megjeleníteni, szegmentált lekérdezéssel kérje le az entitásokat. A példa bemutatja, hogy az Async-Await mintázattal hogyan kérhetők le eredmények az oldalakról úgy, hogy ne legyen letiltva a végrehajtás, amíg egy nagy eredménykészletre várakozik. További információk az Async-Await mintázat használatáról .NET-keretrendszerben: [Asynchronous Programming with Async and Await (C# and Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx) (Aszinkron programozás az Async és Await műveletekkel (C# és Visual Basic)).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Következő lépések
Most, hogy mér megismerte a Table Storage alapjait, az alábbi hivatkozásokból tájékozódhat az összetettebb tárolási feladatok elvégzéséről is:

* További Table Storage-példákat a [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/) (Az Azure Table Storage használatának első lépései a .NET-keretrendszerrel) című cikkben tekinthet meg.
* A Table Service elérhető API-kat részletesen ismertető referenciadokumentációjának megtekintése:
  * [Az Azure Storage .NET-hez készült ügyféloldali kódtára – referencia](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API – referencia](http://msdn.microsoft.com/library/azure/dd179355)
* Az [Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md) használatával megtudhatja, hogyan egyszerűsítheti az Azure Storage használatához írt kódot
* Az Azure-ban való adattárolás további lehetőségeiről tekintse meg a többi szolgáltatás-útmutatót.
  * [Get started with Azure Blob storage using .NET](storage-dotnet-how-to-use-blobs.md) (Az Azure Blob Storage használatának első lépései a .NET-keretrendszerrel) a strukturálatlan adatok tárolásához.
  * [Csatlakozzon az SQL Database adatbázishoz .NET (C#) használatával](../sql-database/sql-database-develop-dotnet-simple.md) a relációs adatok tárolásához.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
[Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
[Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
[Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
[Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png

[Introducing Upsert and Query Projection blog post]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[.NET Client Library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Azure Storage Team blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Configure Azure Storage connection strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
[OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
[How to: Programmatically access Table storage]: #tablestorage

