---
title: "NoSQL-oktatóanyag: DocumentDB .NET Core SDK | Microsoft Docs"
description: "NoSQL-oktatóanyag, amely létrehoz egy online adatbázist és egy C# konzolalkalmazást a DocumentDB .NET SDK Core használatával. A DocumentDB egy NoSQL-alapú adatbázis a JSON formátumhoz."
services: documentdb
documentationcenter: .net
author: arramac
manager: jhubbard
editor: 
ms.assetid: 9f93e276-9936-4efb-a534-a9889fa7c7d2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/23/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: c8a915055318697ade229837653df4c105279299
ms.lasthandoff: 03/24/2017


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application-on-net-core"></a>NoSQL-oktatóanyag: DocumentDB C# konzolalkalmazás létrehozása .NET Core-on
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Köszönti az Azure DocumentDB .NET Core SDK-hoz készült NoSQL-oktatóanyag! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazással fog rendelkezni, amely DocumentDB erőforrásokat hoz létre és kérdez le.

Az oktatóanyag a következőket ismerteti:

* DocumentDB-fiók létrehozása, és az ahhoz való csatlakozás
* A Visual Studio megoldás konfigurálása
* Online adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* Adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started). A gyors utasításokért ugorjon [A teljes megoldás beszerzése szakaszra](#GetSolution).

Xamarin iOS, Android vagy Forms alkalmazást szeretne építeni a DocumentDB .NET Core SDK-val? Lásd a [Xamarin-mobilalkalmazások DocumentDB-vel történő fejlesztésével](documentdb-mobile-apps-with-xamarin.md) foglalkozó témakört.

Utána a lap tetején és alján található szavazógomb használatával küldjön visszajelzést felénk. Ha szeretne közvetlenül kapcsolatba lépni velünk, a hozzászólásaiban tüntesse fel az e-mail-címét.

> [!NOTE]
> Az ebben az oktatóanyagban használt DocumentDB .NET Core SDK-k egyelőre nem kompatibilisek az Univerzális Windows-platformra (UWP) épülő alkalmazásokkal. A .NET Core SDK UWP-alkalmazásokat is támogató előzetes verziójáért küldjön e-mailt a következő címre: [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 
    * Vagy használhatja az [Azure DocumentDB Emulatort](documentdb-nosql-local-emulator.md) ebben az oktatóanyagban.
* [Visual Studio 2017](https://www.visualstudio.com/vs/) 
    * Ha MacOS vagy Linux rendszeren dolgozik, a parancssorból is fejleszthet .NET Core-alkalmazásokat, ha telepíti a [.NET Core SDK-t](https://www.microsoft.com/net/core#macos) a választott platformra. 
    * Ha Windows rendszeren dolgozik, a parancssorból is fejleszthet .NET Core alkalmazásokat, ha telepíti a [.NET Core SDK-t](https://www.microsoft.com/net/core#windows). 
    * Használhat saját szerkesztőt is, vagy letöltheti az ingyenes [Visual Studio Code](https://code.visualstudio.com/) alkalmazást, amely Windows, Linux és MacOS rendszeren egyaránt működik. 

## <a name="step-1-create-a-documentdb-account"></a>1. lépés: DocumentDB-fiók létrehozása
Hozzon létre egy DocumentDB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha a DocumentDB Emulatort használja, kövesse az [Azure DocumentDB Emulatornál](documentdb-nosql-local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio megoldás beállítása](#SetupVS) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>2. lépés: A Visual Studio megoldás beállítása
1. Nyissa meg a **Visual Studio 2017-et** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. A **New Project** (Új projekt) párbeszédpanelen válassza a **Templates** (Sablonok)  / **Visual C#** / **.NET Core**/**Console Application (.NET Core)** (Konzolalkalmazás (.NET Core)) elemet, adja a projektnek a **DocumentDBGettingStarted** nevet, majd kattintson az **OK** gombra.

   ![Képernyőfelvétel az Új projekt ablakról](./media/documentdb-dotnetcore-get-started/nosql-tutorial-new-project-2.png)
4. A **Solution Explorer** (Megoldáskezelő) felületén kattintson a jobb gombbal a **DocumentDBGettingStarted** elemre.
5. Ezután továbbra is a menüben kattintson a **Manage NuGet Packages…** (NuGet-csomagok kezelése…) lehetőségre.

   ![A Projekt jobb gombos kattintással elérhető menüjének képernyőfelvétele](./media/documentdb-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)
6. A **NuGet** lapon kattintson a **Browse** (Tallózás) elemre az ablak tetején, majd írja be az **azure documentdb** kifejezést a keresőmezőbe.
7. A találatok között keresse meg a **Microsoft.Azure.DocumentDB.Core** elemet, majd kattintson a **Telepítés** lehetőségre.
   A .NET Core DocumentDB ügyfélkódtárának csomagazonosítója a következő: [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Ha a .NET-keretrendszer olyan verzióját célozza meg (például net461), amelyet ez a .NET Core NuGet csomag nem támogat, használja a [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)-t, amely a .NET-keretrendszer 4.5-ös verziójától kezdődően minden verziót támogat.
8. Amikor a rendszer kéri, fogadja el a NuGet-csomag telepítését és a licencmegállapodást.

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! A [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a id="Connect"></a>3. lépés: Csatlakozás DocumentDB-fiókhoz
Először adja hozzá az alábbi hivatkozásokat a C# alkalmazás elejéhez a Program.cs fájlban:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

> [!IMPORTANT]
> A NoSQL-oktatóanyag befejezéséhez mindenképpen adja hozzá az alábbi függőségeket.

Most adja hozzá ezt a két állandót és az *ügyfél* változót a *Program* nyilvános osztály alatt.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Ezután látogasson el az [Azure-portálra](https://portal.azure.com) az URI és az elsődleges kulcs beszerzéséért. A DocumentDB URI és az elsődleges kulcs azért szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, a DocumentDB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure portálon lépjen a DocumentDB-fiókra, majd kattintson a **Kulcsok** elemre.

Másolja ki az URI-t a portálról, és illessze be a program.cs fájl `<your endpoint URI>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your key>` elembe. Ha az Azure DocumentDB Emulatort használja, használjon `https://localhost:8081` értéket végpontként, valamint a jól definiált engedélyezési kulcsot a [Fejlesztés a DocumentDB Emulator használatával](documentdb-nosql-local-emulator.md) című részből. Mindenképp távolítsa el a < és a > jelet, azonban az idézőjeleket hagyja meg a végpont és a kulcs körül.

![Képernyőfelvétel a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt Azure-portálról. Megjelenít egy DocumentDB-fiókot, amelyen az ACTIVE központ, a DocumentDB-fiók panelén lévő KEYS gomb, valamint a Kulcsok panelen lévő URI, PRIMARY KEY és SECONDARY KEY értékek vannak kiemelve.][keys]

Először létrehozunk egy új **DocumentClient** példányt az első lépések alkalmazáshoz.

A **Fő** metódus alatt adja hozzá a **GetStartedDemo** elnevezésű új aszinkron feladatot, amely létrehozza nekünk az új **DocumentClient** példányt.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Adja hozzá a következő kódot az aszinkron feladat **Fő** metódusból való futtatásához. A **Fő** metódus észleli a kivételeket, és a konzolba írja azokat.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás létrehozásához és futtatásához.

Gratulálunk! Sikeresen csatlakozott egy DocumentDB-fiókhoz. Most vessünk egy pillantást a DocumentDB-erőforrások használatára.  

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása
Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz.

Másolja, majd illessze be a **WriteToConsoleAndPromptToContinue** metódust a **GetStartedDemo** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

A DocumentDB [adatbázis](documentdb-resources.md#databases) a **DocumentClient** osztály [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) metódusának használatával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az ügyfél létrehozása alatt. Ezzel létrehoz egy *FamilyDB* elnevezésű adatbázist.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott egy DocumentDB-adatbázist.  

## <a id="CreateColl"></a>5. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **CreateDocumentCollectionAsync** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/documentdb/).

Egy [gyűjtemény](documentdb-resources.md#collections) a **DocumentClient** osztály [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) metódusának használatával hozható létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba az adatbázis létrehozása alatt. Ezzel létrehoz egy *FamilyCollection_oa* elnevezésű dokumentumgyűjteményt.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.CreateDatabaseIfNotExists("FamilyDB_oa");

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott egy DocumentDB-dokumentumgyűjteményt.  

## <a id="CreateDoc"></a>6. lépés: JSON-dokumentumok létrehozása
A [dokumentumok](documentdb-resources.md#documents) a **DocumentClient** osztály [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) metódusának használatával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, használhatja a DocumentDB [adatáttelepítési eszközét](documentdb-import-data.md).

Először létre kell hozni egy **Család** osztályt, amely ebben a mintában a DocumentDB-ben tárolt objektumokat képviseli. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Ne feledje, hogy a dokumentumoknak rendelkezniük kell egy **Azonosító** tulajdonsággal, amely a JSON-fájlban **id**-ként van szerializálva. Az osztályok létrehozásához adja hozzá az alábbi belső alosztályokat a **GetStartedDemo** metódus után.

Másolja, majd illessze be a **Család**, **Szülő**, **Gyermek**, **Háziállat** és **Cím** osztályokat a **WriteToConsoleAndPromptToContinue** metódus alá.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
public class Family
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    public string LastName { get; set; }
    public Parent[] Parents { get; set; }
    public Child[] Children { get; set; }
    public Address Address { get; set; }
    public bool IsRegistered { get; set; }
    public override string ToString()
    {
            return JsonConvert.SerializeObject(this);
    }
}

public class Parent
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
}

public class Child
{
    public string FamilyName { get; set; }
    public string FirstName { get; set; }
    public string Gender { get; set; }
    public int Grade { get; set; }
    public Pet[] Pets { get; set; }
}

public class Pet
{
    public string GivenName { get; set; }
}

public class Address
{
    public string State { get; set; }
    public string County { get; set; }
    public string City { get; set; }
}
```

Másolja, majd illessze be a **CreateFamilyDocumentIfNotExists** metódust a **CreateDocumentCollectionIfNotExists** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Ezután szúrjon be két dokumentumot, egyet az Andersen családhoz, egyet pedig a Wakefield családhoz.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentumgyűjtemény létrehozása alatt.

```csharp
await this.CreateDatabaseIfNotExists("FamilyDB_oa");

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
                new Parent { FirstName = "Thomas" },
                new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FirstName = "Henriette Thaulow",
                        Gender = "female",
                        Grade = 5,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Fluffy" }
                        }
                }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
                new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
                new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
                new Child
                {
                        FamilyName = "Merriam",
                        FirstName = "Jesse",
                        Gender = "female",
                        Grade = 8,
                        Pets = new Pet[]
                        {
                                new Pet { GivenName = "Goofy" },
                                new Pet { GivenName = "Shadow" }
                        }
                },
                new Child
                {
                        FamilyName = "Miller",
                        FirstName = "Lisa",
                        Gender = "female",
                        Grade = 1
                }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
};

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen létrehozott két DocumentDB-dokumentumot.  

![A diagram a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt fiók, online adatbázis, gyűjtemény és dokumentumok hierarchikus kapcsolatát ábrázolja.](./media/documentdb-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. lépés: DocumentDB-erőforrások lekérdezése
A DocumentDB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes [lekérdezéseket](documentdb-sql-query.md).  Az alábbi kódminta több olyan lekérdezést mutat be – a DocumentDB SQL-szintaxis és a LINQ használatával egyaránt – amelyeket az előző lépésben beszúrt dokumentumokon futtathatunk.

Másolja, majd illessze be a **ExecuteSimpleQuery** metódust a **CreateFamilyDocumentIfNotExists** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a második dokumentum létrehozása alá.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeres lekérdezést végzett egy DocumentDB-gyűjteményen.

Az alábbi diagram bemutatja, hogyan indít hívást a DocumentDB SQL-lekérdezési szintaxisa a létrehozott gyűjteményre. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre is.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/documentdb-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

A [FROM](documentdb-sql-query.md#FromClause) kulcsszó kihagyható a lekérdezésből, mivel a DocumentDB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. A DocumentDB úgy veszi, hogy a Families, a root vagy a tetszőleges változónév alapértelmezés szerint az aktuális gyűjteményre hivatkozik.

## <a id="ReplaceDocument"></a>8. lépés: JSON-dokumentumok cseréje
A DocumentDB támogatja a JSON-dokumentumok cseréjét.  

Másolja, majd illessze be a **ReplaceFamilyDocument** metódust az **ExecuteSimpleQuery** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    try
    {
        await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
        this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }
    catch (DocumentClientException de)
    {
        throw;
    }
}
```

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a lekérdezés végrehajtása alá. Ezáltal a dokumentum cseréje után ismét ugyanaz a lekérdezés fog lefutni a megváltozott dokumentum megtekintéséhez.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen kicserélt egy DocumentDB-dokumentumot.

## <a id="DeleteDocument"></a>9. lépés: JSON-dokumentumok törlése
A DocumentDB támogatja a JSON-dokumentumok törlését.  

Másolja, majd illessze be a **DeleteFamilyDocument** metódust a **ReplaceFamilyDocument** metódus alá.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    try
    {
        await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine("Deleted Family {0}", documentName);
    }
    catch (DocumentClientException de)
    {
        throw;
    }
}
```

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a második lekérdezés végrehajtása alá.

```cshrp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen törölt egy DocumentDB-dokumentumot.

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentum törlése alá az adatbázis és az összes gyermek-erőforrás törléséhez.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás futtatásához.

Gratulálunk! Sikeresen törölt egy DocumentDB-adatbázist.

## <a id="Run"></a>11. lépés: Futtassa a teljes C# konzolalkalmazást!
A Visual Studióban nyomja meg a **DocumentDBGettingStarted** gombot az alkalmazás hibakeresési módban történő létrehozásához.

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

```
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Gratulálunk! Elvégezte a NoSQL-oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a id="GetSolution"></a>A NoSQL-oktatóanyagban szereplő teljes megoldás beszerzése
A cikkben szereplő összes mintát tartalmazó GetStarted-megoldás összeállításához az alábbiakra lesz szüksége:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [DocumentDB-fiók][documentdb-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started) megoldás.

Ahhoz, hogy visszaállítsa a DocumentDB .NET Core SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a **GetStarted** megoldásra a Megoldáskezelőben, majd kattintson a **NuGet-csomagok visszaállításának engedélyezése** lehetőségre. Ezután a Program.cs fájlban frissítse az EndpointUrl és az AuthorizationKey értékeket a [Csatlakozás DocumentDB-fiókhoz](#Connect) című részben leírtak szerint.

## <a name="next-steps"></a>Következő lépések
* Összetettebb ASP.NET MVC NoSQL-oktatóanyagot szeretne? Tekintse meg a következőt: [ASP.NET MVC-vel rendelkező webalkalmazás összeállítása a DocumentDB használatával](documentdb-dotnet-application.md).
* Xamarin iOS, Android vagy Forms alkalmazást szeretne kifejleszteni a DocumentDB .NET Core SDK-val? Lásd a [Xamarin-mobilalkalmazások DocumentDB-vel történő fejlesztésével](documentdb-mobile-apps-with-xamarin.md) foglalkozó témakört.
* Méret- és teljesítménytesztelést szeretne elvégezni a DocumentDB használatával? Tekintse meg a következőt: [Teljesítmény- és mérettesztelés az Azure DocumentDB használatával](documentdb-performance-testing.md)
* Ismerje meg, hogyan [felügyelhet egy DocumentDB-fiókot](documentdb-monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat a [DocumentDB dokumentációs oldalának](https://azure.microsoft.com/documentation/services/documentdb/) Develop (Fejlesztés) szakaszában találja.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-dotnetcore-get-started/nosql-tutorial-keys.png

