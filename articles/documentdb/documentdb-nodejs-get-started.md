---
title: "NoSQL Node.js-oktatóanyag a DocumentDB-hez | Microsoft Docs"
description: "Ez egy NoSQL Node.js-oktatóanyag, amely egy NoSQL-adatbázis és egy konzolalkalmazás a DocumentDB Node.js SDK használatával történő létrehozását ismerteti. A DocumentDB egy NoSQL-alapú adatbázis a JSON formátumhoz."
keywords: "node.js-oktatóanyag, node-adatbázis"
services: documentdb
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2b8ac838e9387b04467f03d0608da05b3edfdd26
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>NoSQL Node.js oktatóanyag: DocumentDB Node.js-konzolalkalmazás
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js MongoDB-hez](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Üdvözöljük az Azure DocumentDB Node.js SDK-hoz készült Node.js-oktatóanyagban! Az oktatóanyag lépéseinek követésével egy olyan konzolalkalmazással fog rendelkezni, amely DocumentDB erőforrásokat hoz létre és kérdez le.

Az oktatóanyag a következőket ismerteti:

* DocumentDB-fiók létrehozása, és az ahhoz való csatlakozás
* Az alkalmazás beállítása
* Node-adatbázis létrehozása
* Gyűjtemény létrehozása
* JSON-dokumentumok létrehozása
* A gyűjtemény lekérdezése
* Dokumentum cseréje
* Dokumentum törlése
* A Node-adatbázis törlése

Nincs elég ideje? Ne aggódjon! A teljes megoldás elérhető a [GitHubon](https://github.com/Azure-Samples/documentdb-node-getting-started). Gyors útmutatásért tekintse meg [A teljes megoldás beszerzése](#GetSolution) című szakaszt.

A Node.js-oktatóanyag befejezése után a lap tetején vagy alján található szavazógomb használatával küldhet visszajelzést. Ha szeretne közvetlenül kapcsolatba lépni velünk, a hozzászólásaiban tüntesse fel az e-mail-címét.

Most pedig lássunk neki!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>A Node.js-oktatóanyag előfeltételei
Győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Aktív Azure-fiók. Ha még nincs fiókja, regisztrálhat az [Azure ingyenes próbaverziójára](https://azure.microsoft.com/pricing/free-trial/).
    * Vagy használhatja az [Azure DocumentDB Emulatort](documentdb-nosql-local-emulator.md) ebben az oktatóanyagban.
* [Node.js](https://nodejs.org/)-verzió: 0.10.29-s vagy újabb.

## <a name="step-1-create-a-documentdb-account"></a>1. lépés: DocumentDB-fiók létrehozása
Hozzon létre egy DocumentDB-fiókot. Ha már rendelkezik egy használni kívánt fiókkal, folytassa [A Node.js-alkalmazás beállítása](#SetupNode) című lépéssel. Ha a DocumentDB Emulatort használja, kövesse az [Azure DocumentDB Emulatornál](documentdb-nosql-local-emulator.md) leírt lépéseket az emulátor telepítéséhez, majd ugorjon előre a [Node.js alkalmazás beállítása](#SetupNode) című lépésre.

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>2. lépés: A Node.js-alkalmazás beállítása
1. Nyissa meg kedvenc terminálját.
2. Keresse meg azt a mappát vagy könyvtárat, ahova a Node.js-alkalmazást menteni szeretné.
3. Hozzon létre két üres JavaScript-fájlt az alábbi parancsokkal:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Telepítse a DocumentDB modult az npm segítségével Használja az alábbi parancsot:
   * ```npm install documentdb --save```

Remek! A beállítás befejeztével nekiláthat a kód írásának.

## <a id="Config"></a>3. lépés: Az alkalmazás konfigurációnak megadása
Nyissa meg a ```config.js``` fájlt egy tetszőleges szövegszerkesztőben.

Ezután másolja és illessze be az alábbi kódrészletet, majd állítsa be a ```config.endpoint``` és ```config.primaryKey``` tulajdonságot a saját DocumentDB-végpontjának URI és elsődleges kulcs értékeire. Mindkettő konfiguráció megtalálható az [Azure portálon](https://portal.azure.com).

![Node.js-oktatóanyag – Képernyőfelvétel az Azure portálról, amely egy DocumentDB-fiókot jelenít meg az ACTIVE központ, a DocumentDB-fiók panelen lévő KEYS (KULCSOK) gomb, valamint a Keys (Kulcsok) panelen lévő URI, PRIMARY KEY (ELSŐDLEGES KULCS) és SECONDARY KEY (MÁSODLAGOS KULCS) értékek kiemelésével – Node-adatbázis][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Másolja és illessze be a ```database id```, ```collection id``` és ```JSON documents``` értékeket az alábbi ```config```-objektumba oda, ahol megadta a ```config.endpoint``` és ```config.authKey``` tulajdonságot. Ha van olyan adat, amelyet szeretne az adatbázisban tárolni, használhatja a DocumentDB [adatáttelepítési eszközét](documentdb-import-data.md) a dokumentumdefiníciók hozzáadása helyett.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Ebben az esetben az adatbázis-, gyűjtemény- és dokumentum-definíciók szolgálnak majd a DocumentDB ```database id```, ```collection id``` és dokumentumadataiként.

Végül exportálja a ```config```-objektumot, hogy hivatkozhasson rá az ```app.js``` fájlban.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>4. lépés: Csatlakozás DocumentDB-fiókhoz
Nyissa meg az üres ```app.js``` fájlt a szövegszerkesztőben. Másolja és illessze be az alábbi kódot a ```documentdb```, valamint az újonnan létrehozott ```config``` modul importálásához.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Másolja és illessze be a kódot, ha az előzőleg mentett ```config.endpoint``` és ```config.primaryKey``` használatával szeretne létrehozni egy új DocumentClient-ügyfelet.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Most, hogy rendelkezik a documentdb-ügyfél elindításához szükséges kóddal, vessünk egy pillantást a DocumentDB-erőforrások használatára.

## <a name="step-5-create-a-node-database"></a>5. lépés: Node-adatbázis létrehozása
Másolja és illessze be az alábbi kódot, hogy megadja a Not Found (Nem található) HTTP-állapotot, az adatbázis URL-címét, valamint a gyűjtemény URL-címét. A DocumentDB-ügyfél ezen URL-címek alapján találja meg a megfelelő adatbázist és gyűjteményt.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Az [adatbázis](documentdb-resources.md#databases) a **DocumentClient** osztály [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) függvényének használatával hozható létre. Az adatbázis a dokumentumtároló gyűjtemények között particionált logikai tárolója.

Másolja és illessze be a **getDatabase** függvényt az adatbázis az app.js fájlban az ```id``` objektum ```config``` azonosítójával történő létrehozásához. A függvény ellenőrzi, hogy létezik-e már adatbázis ugyanazzal a ```FamilyRegistry```-azonosítóval. Ha már létezik, a rendszer új adatbázis létrehozása helyett visszaadja a már létezőt.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Másolja és illessze be az alábbi kódot oda, ahol megadta a **getDatabase** függvényt. Ezzel hozzáadhatja az **exit** (kilépés) segédfüggvényt, amely megjeleníti a kilépési üzenetet, valamint a **getDatabase** függvény hívását.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Gratulálunk! Sikeresen létrehozott egy DocumentDB-adatbázist.

## <a id="CreateColl"></a>6. lépés: Gyűjtemény létrehozása
> [!WARNING]
> A **CreateDocumentCollectionAsync** létrehoz egy új gyűjteményt, amely költségeket von maga után. További részletekért látogasson el az [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

[Gyűjteményt](documentdb-resources.md#collections) a **DocumentClient** osztály [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) függvényével hozhat létre. A gyűjtemény egy JSON-dokumentumokat és a kapcsolódó JavaScript-alkalmazáslogikát tartalmazó tároló.

Másolja és illessze be a **getCollection** függvényt a **getDatabase** függvény alá a app.js fájlban az új gyűjtemény az ```config``` objektum ```id``` azonosítójával történő létrehozásához. A rendszer ismét ellenőrzi, hogy létezik-e már gyűjtemény ugyanazzal a ```FamilyCollection```-azonosítóval. Ha már létezik, a rendszer új gyűjtemény létrehozása helyett visszaadja a már létezőt.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Másolja és illessze be az alábbi kódot a **getDatabase** függvény meghívásához, valamint a **getCollection** függvény végrehajtásához.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Gratulálunk! Sikeresen létrehozott egy DocumentDB-gyűjteményt.

## <a id="CreateDoc"></a>7. lépés: Dokumentum létrehozása
[Dokumentumot](documentdb-resources.md#documents) a **DocumentClient** osztály [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) függvényével hozhat létre. A dokumentumok a felhasználó által megadott (tetszőleges) JSON-tartalmak. Most már beszúrhat egy dokumentumot a DocumentDB-be.

Másolja és illessze be a **getFamilyDocument** függvényt a **getCollection** függvény alá a ```config``` objektumban mentett JSON-adatokat tartalmazó dokumentumok létrehozásához. A rendszer ismét ellenőrzi, hogy létezik-e már dokumentum ugyanazzal az azonosítóval.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Másolja és illessze be az alábbi kódot a **getCollection** függvény meghívásához, valamint a **getFamilyDocument** függvény végrehajtásához.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Gratulálunk! Sikeresen létrehozott egy DocumentDB-dokumentumot.

![Node.js-oktatóanyag – A fiók, az adatbázis, a gyűjtemény és a dokumentumok hierarchikus kapcsolatát ábrázoló diagram – Node-adatbázis](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>8. lépés: DocumentDB-erőforrások lekérdezése
A DocumentDB támogatja az egyes gyűjteményekben tárolt JSON-dokumentumokon végzett [részletes lekérdezéseket](documentdb-sql-query.md). Az alábbi mintakód egy olyan lekérdezést mutat be, amelyet a gyűjteményben található dokumentumokra vonatkozóan futtathat le.

Másolja és illessze be a **queryCollection** függvényt a **getFamilyDocument** függvény alá az app.js fájlban. A DocumentDB támogatja az SQL-szerű lekérdezéseket, ahogyan azt az alábbi példa is mutatja. A bonyolult lekérdezések felépítésével kapcsolatos további információkért tekintse meg a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) szakaszt, valamint a [lekérdezésekre vonatkozó dokumentációt](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Az alábbi diagram bemutatja, hogyan indít hívást a DocumentDB SQL-lekérdezés szintaxisa a létrehozott gyűjteményre.

![Node.js-oktatóanyag – A lekérdezés hatókörét és jelentését ábrázoló diagram – Node-adatbázis](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

A [FROM](documentdb-sql-query.md#FromClause) kulcsszó kihagyható a lekérdezésből, mivel a DocumentDB-lekérdezések hatóköre eleve egyetlen gyűjtemény. Ezért a „FROM Families f” lecserélhető a „FROM root r” vagy bármilyen tetszőleges változónévre. A DocumentDB úgy veszi, hogy a Families, a root vagy a tetszőleges változónév alapértelmezés szerint az aktuális gyűjteményre hivatkozik.

Másolja és illessze be az alábbi kódot a **getFamilyDocument** függvény meghívásához, valamint a **queryCollection** függvény végrehajtásához.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Gratulálunk! Sikeresen lekérdezett egy DocumentDB-dokumentumot.

## <a id="ReplaceDocument"></a>9. lépés: Dokumentum cseréje
A DocumentDB támogatja a JSON-dokumentumok cseréjét.

Másolja és illessze be a **replaceFamilyDocument** függvényt a **queryCollection** függvény alá az app.js fájlban.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Másolja és illessze be az alábbi kódot a **queryCollection** függvény meghívásához, valamint a **replaceDocument** függvény végrehajtásához. Továbbá adja hozzá a **queryCollection** függvényt meghívó kódot annak megerősítése érdekében, hogy a dokumentum módosítása sikeres volt.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Gratulálunk! Sikeresen kicserélt egy DocumentDB-dokumentumot.

## <a id="DeleteDocument"></a>10. lépés: Dokumentum törlése
A DocumentDB támogatja a JSON-dokumentumok törlését.

Másolja és illessze be a **deleteFamilyDocument** függvényt a **replaceFamilyDocument** függvény alá.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Másolja és illessze be az alábbi kódot a második **queryCollection** függvény meghívásához, valamint a **deleteDocument** függvény végrehajtásához.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Gratulálunk! Sikeresen törölt egy DocumentDB-dokumentumot.

## <a id="DeleteDatabase"></a>11. lépés: A Node-adatbázis törlése
A létrehozott adatbázis törlésével az adatbázis és az összes gyermekerőforrás (gyűjtemények, dokumentumok stb.) is törlődik.

Másolja és illessze be a **cleanup** függvényt a **deleteFamilyDocument** függvény alá az adatbázis, valamint minden gyermekerőforrásának törléséhez.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Másolja és illessze be az alábbi kódot a **deleteFamilyDocument** függvény meghívásához, valamint a **cleanup** függvény végrehajtásához.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>12. lépés: A teljes Node.js-alkalmazás futtatása
A függvényeket meghívó teljes sorozatnak így kell kinéznie:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

A terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```

Meg kell jelennie az első lépések alkalmazás kimenetének. A kimenetnek meg kell egyeznie az alábbi példaszöveggel.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Gratulálunk! Ezennel befejezte a Node.js-oktatóanyagot, és létrehozta első saját DocumentDB-konzolalkalmazását.

## <a id="GetSolution"></a>A Node. js-oktatóanyagban szereplő teljes megoldás beszerzése
Ha nincs ideje az oktatóanyag lépéseinek végrehajtására, vagy csak szeretné letölteni a kódot, a [GitHubon](https://github.com/Azure-Samples/documentdb-node-getting-started) beszerezheti azt.

A cikkben szereplő összes mintát tartalmazó GetStarted-megoldás futtatásához az alábbiakra lesz szüksége:

* [DocumentDB-fiók][documentdb-create-account].
* A GitHubon elérhető [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) megoldás.

Telepítse a **DocumentDB** modult az npm segítségével. Használja az alábbi parancsot:

* ```npm install documentdb --save```

Ezután, a ```config.js``` fájlban frissítse a config.endpoint és config.authKey értékeket a [3. lépés: Az alkalmazás konfigurációnak megadásában](#Config) leírtak alapján. 

Majd a terminálban keresse meg az ```app.js``` fájlt, és futtassa az alábbi parancsot: ```node app.js```.

Ennyi az egész! Építse ki, és máris jó úton jár! 

## <a name="next-steps"></a>Következő lépések
* Összetettebb Node.js-mintát szeretne használni? Lásd: [Node.js-webalkalmazás létrehozása a DocumentDB használatával](documentdb-nodejs-application.md)
* Ismerje meg, hogyan [felügyelhet egy DocumentDB-fiókot](documentdb-monitor-accounts.md).
* Futtasson lekérdezéseket a minta-adatkészleteken a [Query Playground](https://www.documentdb.com/sql/demo) (Tesztlekérdezések) használatával.
* A programozási modellel kapcsolatos további tudnivalókat a [DocumentDB dokumentációs oldalának](https://azure.microsoft.com/documentation/services/documentdb/) Develop (Fejlesztés) szakaszában találja.

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

