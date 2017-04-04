---
title: "NoSQL-oktatóanyag: Azure DocumentDB Java SDK | Microsoft Docs"
description: "NoSQL-oktatóanyag, amely létrehoz egy online adatbázist és egy Java konzolalkalmazást a DocumentDB Java SDK használatával. Az Azure DocumentDB egy NoSQL-alapú adatbázis a JSON formátumhoz."
keywords: "nosql-oktatóanyag, online adatbázis, java konzolalkalmazás"
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: da7907ffc515ea2e3040075c93bcd53840cf3ff5
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>NoSQL-oktatóanyag: DocumentDB Java konzolalkalmazás létrehozása
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Köszönti az Azure DocumentDB Java SDK-hoz készült NoSQL-oktatóanyag Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazással fog rendelkezni, amely DocumentDB erőforrásokat hoz létre és kérdez le.

Az oktatóanyag a következőket ismerteti:

* DocumentDB-fiók létrehozása, és az ahhoz való csatlakozás
* A Visual Studio megoldás konfigurálása
* Online adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* Adatbázis törlése

Most pedig lássunk neki!

## <a name="prerequisites"></a>Előfeltételek
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/). Vagy használhatja az [Azure DocumentDB Emulatort](documentdb-nosql-local-emulator.md) ebben az oktatóanyagban.
* [Git](https://git-scm.com/downloads)
* [Java fejlesztői készlet (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>1. lépés: DocumentDB-fiók létrehozása
Hozzon létre egy DocumentDB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A GitHub-projekt klónozása](#GitClone) című lépéssel. Ha a DocumentDB Emulatort használja, kövesse az [Azure DocumentDB Emulatornál](documentdb-nosql-local-emulator.md) leírt lépéseket az emulátor beállításához, majd ugorjon [A GitHub-projekt klónozása](#GitClone) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>2. lépés: A GitHub-projekt klónozása
A GitHub-adattár klónozásával kezdheti meg [a DocumentDB és a Java használatának első lépéseit](https://github.com/Azure-Samples/documentdb-java-getting-started). Futtassa például egy helyi könyvtárból az alábbi parancsot a mintaprojekt helyi lekéréséhez.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

A könyvtár tartalmazza a projekt `pom.xml` fájlját és egy `src` mappát, amelyben megtalálható a Java forráskód, valamint a `Program.java` fájl, amely bemutatja, hogyan hajthat végre egyszerű műveleteket az Azure DocumentDB-vel, például dokumentumok létrehozását vagy adatlekérdezést egy gyűjteményen belül. A `pom.xml` fájl tartalmaz egy [Maven DocumentDB Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)-függőséget.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>3. lépés: Csatlakozás DocumentDB-fiókhoz
Térjen vissza az [Azure Portalra](https://portal.azure.com) a végpont és az elsődleges főkulcs beszerzéséért. A DocumentDB végpont és az elsődleges kulcs ahhoz szükséges, hogy az alkalmazás tudja, hova kell csatlakoznia, a DocumentDB pedig megbízzon az alkalmazás által létesített kapcsolatban.

Az Azure portálon lépjen a DocumentDB-fiókra, majd kattintson a **Kulcsok** elemre. Másolja ki az URI-t a portálról, és illessze be a Program.java fájl `<your endpoint URI>` elemébe. Ezután másolja ki a PRIMARY KEY kulcsot a portálról, és illessze be a `<your key>` elembe.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Képernyőfelvétel a NoSQL-oktatóanyagban a Java konzolalkalmazás létrehozásához használt Azure Portalról. Megjelenít egy DocumentDB-fiókot, amelyen az ACTIVE központ, a DocumentDB-fiók panelén lévő KEYS gomb, valamint a Kulcsok panelen lévő URI, PRIMARY KEY és SECONDARY KEY értékek vannak kiemelve.][keys]

## <a name="step-4-create-a-database"></a>4. lépés: Adatbázis létrehozása
A DocumentDB [adatbázis](documentdb-resources.md#databases) a **DocumentClient** osztály [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) metódusával hozható létre. Az adatbázis a JSON-dokumentumtároló gyűjtemények között particionált logikai tárolója.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>5. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **createCollection** létrehoz egy fenntartott adattovábbítási kapacitással rendelkező új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Egy [gyűjtemény](documentdb-resources.md#collections) a **DocumentClient** osztály [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) metódusával hozható létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>6. lépés: JSON-dokumentumok létrehozása
A [dokumentumok](documentdb-resources.md#documents) a **DocumentClient** osztály [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) metódusával hozhatók létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beilleszthetünk egy vagy több dokumentumot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, a DocumentDB [adatáttelepítési eszközének](documentdb-import-data.md) használatával importálhat adatokat az adatbázisba.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![A diagram a NoSQL-oktatóanyagban a Java konzolalkalmazás létrehozásához használt fiók, online adatbázis, gyűjtemény és dokumentumok hierarchikus kapcsolatát ábrázolja.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>7. lépés: DocumentDB-erőforrások lekérdezése
A DocumentDB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett részletes [lekérdezéseket](documentdb-sql-query.md).  Az alábbi mintakód bemutatja, hogyan kérdezheti le a dokumentumokat a DocumentDB-ben az SQL-szintaxis és a [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-) metódus együttes használatával.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>8. lépés: JSON-dokumentumok cseréje
A DocumentDB támogatja a JSON-dokumentumok [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-) metódussal végrehajtott frissítését.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>9. lépés: JSON-dokumentumok törlése
A DocumentDB a JSON-dokumentumok törlését is támogatja a [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-) metódus használatával.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>10. lépés: Az adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>11. lépés: Futtassa a teljes Java konzolalkalmazást!
A konzolról futtatandó alkalmazást először fordítsa le a Mavennel:
    
    mvn package

A `mvn package` futtatása letölti a legújabb DocumentDB-erőforrástárat a Mavenről, és létrehozza a `GetStarted-0.0.1-SNAPSHOT.jar` fájlt. Ezután futtassa az alkalmazást az alábbi paranccsal:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Gratulálunk! Elvégezte a NoSQL-oktatóanyagot, és egy működőképes Java konzolalkalmazással rendelkezik!

## <a name="next-steps"></a>Következő lépések
* Szüksége van egy Java-webalkalmazás létrehozására vonatkozó oktatóanyagra? Tekintse meg a [Java-webalkalmazás létrehozása a DocumentDB használatával](documentdb-java-application.md) című cikket.
* Ismerje meg, hogyan [felügyelhet egy DocumentDB-fiókot](documentdb-monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat a [DocumentDB dokumentációs oldalának](https://azure.microsoft.com/documentation/services/documentdb/) Develop (Fejlesztés) szakaszában találja.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

