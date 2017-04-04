---
title: "NoSQL-oktatóanyag: DocumentDB .NET SDK | Microsoft Docs"
description: "NoSQL-oktatóanyag, amely létrehoz egy online adatbázist és egy C# konzolalkalmazást a DocumentDB .NET SDK használatával. A DocumentDB egy NoSQL-alapú adatbázis a JSON formátumhoz."
keywords: "nosql-oktatóanyag, online adatbázis, c# konzolalkalmazás"
services: documentdb
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/19/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 648aa01beaffccf69250f1028e27bc13ed70837c
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-tutorial-build-a-documentdb-c-console-application"></a>NoSQL-oktatóanyag: DocumentDB C# konzolalkalmazás létrehozása
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Köszönti az Azure DocumentDB .NET SDK-hoz készült NoSQL-oktatóanyag! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazással fog rendelkezni, amely DocumentDB erőforrásokat hoz létre és kérdez le.

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

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). A gyors utasításokért ugorjon [A teljes NoSQL-oktatóanyag megoldás beszerzése szakaszra](#GetSolution).

Utána a lap tetején és alján található szavazógomb használatával küldjön visszajelzést felénk. Ha szeretne közvetlenül kapcsolatba lépni velünk, a hozzászólásaiban tüntesse fel az e-mail-címét.

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). 
    * Vagy használhatja az [Azure DocumentDB Emulatort](documentdb-nosql-local-emulator.md) ebben az oktatóanyagban.
* [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com/).

## <a name="step-1-create-a-documentdb-account"></a>1. lépés: DocumentDB-fiók létrehozása
Hozzon létre egy DocumentDB-fiókot. Ha van már olyan fiókja, amelyet használni szeretne, ugorjon előre a [Visual Studio megoldás beállítása](#SetupVS) című lépésre. Ha a DocumentDB Emulatort használja, kövesse az [Azure DocumentDB Emulatornál](documentdb-nosql-local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre [A Visual Studio megoldás beállítása](#SetupVS) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupVS"></a>2. lépés: A Visual Studio megoldás beállítása
1. Nyissa meg a **Visual Studio 2015-öt** a számítógépén.
2. A **Fájl** menüben válassza az **Új**, majd a **Projekt** elemet.
3. Az **Új projekt** párbeszédpanelen válassza a **Sablonok** / **Visual C#** / **Konzolalkalmazás** elemet, nevezze el a projektet, majd kattintson az **OK** gombra.
   ![Képernyőfelvétel az Új projekt ablakról](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. A **Megoldáskezelőben** kattintson a jobb gombbal az új konzolalkalmazásra, amely a Visual Studio megoldás alatt található, majd kattintson a **NuGet-csomagok kezelése...** lehetőségre.
    
    ![A Projekt jobb gombos kattintással elérhető menüjének képernyőfelvétele](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. A **NuGet** lapon kattintson a **Tallózás** elemre, majd írja be az **azure documentdb** kifejezést a keresőmezőbe.
6. A találatok között keresse meg a **Microsoft.Azure.DocumentDB** elemet, majd kattintson a **Telepítés** lehetőségre.
   A DocumentDB ügyfélkódtárának csomagazonosítója a következő: [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).
   ![Képernyőfelvétel a Nuget menüről a DocumentDB ügyféloldali SDK megkereséséhez](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Ha a megoldás módosításainak áttekintéséről szóló üzenetet kap, kattintson az **OK** gombra. Ha a licenc elfogadásáról szóló üzenetet kap, kattintson az **Elfogadom** gombra.

Remek! Most, hogy befejeztük a beállítást, lássunk neki a kód megírásának! A [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) megtalálhatja az oktatóanyagban szereplő kódprojekt befejezett változatát.

## <a id="Connect"></a>3. lépés: Csatlakozás DocumentDB-fiókhoz
Először adja hozzá az alábbi hivatkozásokat a C# alkalmazás elejéhez a Program.cs fájlban:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> A NoSQL-oktatóanyag befejezéséhez mindenképpen adja hozzá az alábbi függőségeket.
> 
> 

Most adja hozzá ezt a két állandót és az *ügyfél* változót a *Program* nyilvános osztály alatt.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Ezután lépjen vissza az [Azure Portalra](https://portal.azure.com) a végponti URL-cím és az elsődleges kulcs beszerzéséért. A végponti URL-cím és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, a DocumentDB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure portálon lépjen a DocumentDB-fiókra, majd kattintson a **Kulcsok** elemre.

Másolja ki az URI-t a portálról, és illessze be a program.cs fájl `<your endpoint URL>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your primary key>` elembe.

![Képernyőfelvétel a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt Azure-portálról. Megjelenít egy DocumentDB-fiókot, amelyen az ACTIVE központ, a DocumentDB-fiók panelén lévő KEYS gomb, valamint a Kulcsok panelen lévő URI, PRIMARY KEY és SECONDARY KEY értékek vannak kiemelve.][keys]

Ezt követően létrehozunk egy új **DocumentClient** példányt az alkalmazás elindításához.

A **Fő** metódus alatt adja hozzá a **GetStartedDemo** elnevezésű új aszinkron feladatot, amely létrehozza nekünk az új **DocumentClient** példányt.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

Adja hozzá a következő kódot az aszinkron feladat **Fő** metódusból való futtatásához. A **Fő** metódus észleli a kivételeket, és a konzolba írja azokat.

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

Az alkalmazás futtatásához nyomja le az **F5** billentyűt. A konzolablak kimenete a következő üzenet megjelenítésével erősíti meg a kapcsolat létrejöttét: `End of demo, press any key to exit.`.  Ezután bezárhatja a konzolablakot. 

Gratulálunk! Sikeresen csatlakozott egy DocumentDB-fiókhoz. Most vessünk egy pillantást a DocumentDB-erőforrások használatára.  

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása
Mielőtt hozzáadja a kódot az adatbázis létrehozásához, adjon hozzá egy segédmetódust a konzolba való íráshoz.

Másolja, majd illessze be a **WriteToConsoleAndPromptToContinue** metódust a **GetStartedDemo** metódus után.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

A DocumentDB-[adatbázis](documentdb-resources.md#databases) a **DocumentClient** osztály [CreateDatabaseIfNotExistAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) metódusának használatával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

Az ügyfél létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba. Ezzel létrehoz egy *FamilyDB* elnevezésű adatbázist.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott egy DocumentDB-adatbázist.  

## <a id="CreateColl"></a>5. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **CreateDocumentCollectionIfNotExistsAsync** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Egy [gyűjtemény](documentdb-resources.md#collections) a **DocumentClient** osztály [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) metódusának használatával hozható létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

Az adatbázis létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba. Ezzel létrehoz egy *FamilyCollection* elnevezésű dokumentumgyűjteményt.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott egy DocumentDB-dokumentumgyűjteményt.  

## <a id="CreateDoc"></a>6. lépés: JSON-dokumentumok létrehozása
A [dokumentumok](documentdb-resources.md#documents) a **DocumentClient** osztály [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) metódusának használatával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, a DocumentDB [adatáttelepítési eszközének](documentdb-import-data.md) használatával importálhat adatokat az adatbázisba.

Először létre kell hozni egy **Család** osztályt, amely ebben a mintában a DocumentDB-ben tárolt objektumokat képviseli. Létrehozunk még egy **Szülő**, **Gyermek**, **Háziállat** és **Cím** alosztályt is a **Család** osztályban való használatra. Ne feledje, hogy a dokumentumoknak rendelkezniük kell egy **Azonosító** tulajdonsággal, amely a JSON-fájlban **id**-ként van szerializálva. Az osztályok létrehozásához adja hozzá az alábbi belső alosztályokat a **GetStartedDemo** metódus után.

Másolja, majd illessze be a **Család**, **Szülő**, **Gyermek**, **Háziállat** és **Cím** osztályokat a **WriteToConsoleAndPromptToContinue** metódus után.

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

Másolja, majd illessze be a **CreateFamilyDocumentIfNotExists** metódust az **Address** osztály alá.

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

Ezután szúrjon be két dokumentumot, egyet az Andersen családhoz, egyet pedig a Wakefield családhoz.

A dokumentumgyűjtemény létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen létrehozott két DocumentDB-dokumentumot.  

![A diagram a NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt fiók, online adatbázis, gyűjtemény és dokumentumok hierarchikus kapcsolatát ábrázolja.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. lépés: DocumentDB-erőforrások lekérdezése
A DocumentDB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes [lekérdezéseket](documentdb-sql-query.md).  Az alábbi kódminta több olyan lekérdezést mutat be – a DocumentDB SQL-szintaxis és a LINQ használatával egyaránt – amelyeket az előző lépésben beszúrt dokumentumokon futtathatunk.

Másolja, majd illessze be a **ExecuteSimpleQuery** metódust a **CreateFamilyDocumentIfNotExists** metódus után.

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

A második dokumentum létrehozása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeres lekérdezést végzett egy DocumentDB-gyűjteményen.

Az alábbi diagram bemutatja, hogyan indít hívást a DocumentDB SQL-lekérdezési szintaxisa a létrehozott gyűjteményre. Ugyanez a logika vonatkozik a LINQ-lekérdezésekre is.

![A NoSQL-oktatóanyagban a C# konzolalkalmazás létrehozásához használt lekérdezés hatókörét és jelentését ábrázoló diagram.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

A [FROM](documentdb-sql-query.md#FromClause) kulcsszó kihagyható a lekérdezésből, mivel a DocumentDB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. A DocumentDB úgy veszi, hogy a Families, a root vagy a tetszőleges változónév alapértelmezés szerint az aktuális gyűjteményre hivatkozik.

## <a id="ReplaceDocument"></a>8. lépés: JSON-dokumentumok cseréje
A DocumentDB támogatja a JSON-dokumentumok cseréjét.  

Másolja, majd illessze be a **ReplaceFamilyDocument** metódust az **ExecuteSimpleQuery** metódus után.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

A lekérdezés végrehajtása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba. Ezáltal a dokumentum cseréje után ismét ugyanaz a lekérdezés fog lefutni a megváltozott dokumentum megtekintéséhez.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen kicserélt egy DocumentDB-dokumentumot.

## <a id="DeleteDocument"></a>9. lépés: JSON-dokumentumok törlése
A DocumentDB támogatja a JSON-dokumentumok törlését.  

Másolja, majd illessze be a **DeleteFamilyDocument** metódust a **ReplaceFamilyDocument** metódus után.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

A második lekérdezés végrehajtása után másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba.

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen törölt egy DocumentDB-dokumentumot.

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja, majd illessze be az alábbi kódot a **GetStartedDemo** metódusba a dokumentum törlése után az adatbázis és az összes gyermek-erőforrás törléséhez.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Az alkalmazás futtatásához nyomja le az **F5** billentyűt.

Gratulálunk! Sikeresen törölt egy DocumentDB-adatbázist.

## <a id="Run"></a>11. lépés: Futtassa a teljes C# konzolalkalmazást!
Nyomja le az F5 billentyűt a Visual Studióban az alkalmazás hibakeresési módban történő összeállításához.

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenet megjeleníti a hozzáadott lekérdezések eredményeit, amelynek meg kell egyeznie az alábbi mintaszöveggel.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
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

Gratulálunk! Elvégezte a NoSQL-oktatóanyagot, és egy működőképes C# konzolalkalmazással rendelkezik!

## <a id="GetSolution"></a>A NoSQL-oktatóanyagban szereplő teljes megoldás beszerzése
Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a mintakódokat, a [GitHubon](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) beszerezhetőek. 

A GetStarted-megoldás létrehozásához a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy [DocumentDB-fiók][documentdb-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) megoldás.

Ahhoz, hogy visszaállítsa a DocumentDB .NET SDK-ra mutató hivatkozásokat a Visual Studióban, kattintson a jobb gombbal a **GetStarted** megoldásra a Megoldáskezelőben, majd kattintson a **NuGet-csomagok visszaállításának engedélyezése** lehetőségre. Ezután az App.config fájlban frissítse az EndpointUrl és az AuthorizationKey értékeket a [Csatlakozás DocumentDB-fiókhoz](#Connect) című részben leírtak szerint.

Ennyi az egész! Építse ki, és máris jó úton jár!


## <a name="next-steps"></a>Következő lépések
* Összetettebb ASP.NET MVC NoSQL-oktatóanyagot szeretne? Tekintse meg a következőt: [ASP.NET MVC-vel rendelkező webalkalmazás összeállítása a DocumentDB használatával](documentdb-dotnet-application.md).
* Méret- és teljesítménytesztelést szeretne elvégezni a DocumentDB használatával? Tekintse meg a következőt: [Teljesítmény- és mérettesztelés az Azure DocumentDB használatával](documentdb-performance-testing.md)
* Ismerje meg, hogyan [felügyelhet egy DocumentDB-fiókot](documentdb-monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat a [DocumentDB dokumentációs oldalának](https://azure.microsoft.com/documentation/services/documentdb/) Develop (Fejlesztés) szakaszában találja.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

