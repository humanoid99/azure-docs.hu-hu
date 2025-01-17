---
title: "Az Azure Data Factory Visual szerzői |} Microsoft Docs"
description: "Az Azure Data Factory visual szerzői használata"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 81b97bb6b6abb5431bedd4efec5f807fa577c4e4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="visual-authoring-in-azure-data-factory"></a>Az Azure Data Factory Visual készítése
Az Azure Data Factory felhasználói felület élmény (UX) lehetővé teszi vizuálisan hozhatnak létre és telepítését erőforrások az a data factory kód írása nélkül. Húzzon tevékenységeket a feldolgozási sor vásznon, hajtsa végre a teszt futtatása, debug ismételt, és telepítheti és figyelheti a folyamat futtatása. Kétféleképpen használatához a UX visual szerzői műveletek végrehajtásához:

- Szerző közvetlenül a Data Factory szolgáltatással.
- A Visual Studio Team Services (VSTS) Git integráció együttműködés, verziókezelő vagy versioning a szerző.

## <a name="author-directly-with-the-data-factory-service"></a>Szerző közvetlenül a Data Factory szolgáltatással
A Data Factory szolgáltatással Visual szerzői eltér az visual szerkesztése a VSTS két módon:

- A Data Factory szolgáltatásnak egy helyen tárolásához a módosítások a JSON-entitások nem tartalmaz.
- A Data Factory szolgáltatásnak együttműködés vagy verziókezelést nincs optimalizálva.

![A Data Factory szolgáltatásnak konfigurálása ](media/author-visually/configure-data-factory.png)

A UX használatakor **vászonra szerzői** ahhoz, hogy közvetlenül a Data Factory szolgáltatásnak, csak az a **közzététel** módban érhető el. A végrehajtott módosításokat közvetlenül a Data Factory szolgáltatásnak kerülnek közzétételre.

![Közzétételi mód](media/author-visually/data-factory-publish.png)

## <a name="author-with-vsts-git-integration"></a>Szerző az VSTS Git integráció
Visual szerkesztése VSTS Git integráció a támogatja a verziókövetési rendszerrel és együttműködés a data factory folyamatok munka. Egy adat-előállító társíthatja egy VSTS Git-fiók tárházat verziókezelő, együttműködés, versioning, és így tovább. Egyetlen VSTS Git fiók rendelkezhet több tárházak találhatók, de lehet, hogy a VSTS Git-tárház csak egy adat-előállító tartozik. Ha nem rendelkezik a VSTS-fiókok vagy tárház, hajtsa végre a [ezeket az utasításokat](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student) az erőforrások létrehozásához.

### <a name="configure-a-vsts-git-repository-with-azure-data-factory"></a>A VSTS Git-tárház konfigurálása az Azure Data Factoryvel
A data Factory két módszerrel konfigurálhatja egy VSTS GIT-tárházat.

<a name="method1"></a>
#### <a name="configuration-method-1-lets-get-started-page"></a>Konfiguráció 1. módszer: folytassuk lépéseket ismertető oldal felkereséséhez
Azure Data Factory, keresse meg a **lássunk** lap. Válassza ki **kód tárház konfigurálása**:

![A VSTS kód tárház konfigurálása](media/author-visually/configure-repo.png)

A **tárház beállításainak** konfigurációs ablaktáblán jelenik meg:

![A kód tárház beállításainak konfigurálása](media/author-visually/repo-settings.png)

A ablaktábla megjeleníti azokat a következő VSTS-kód tárház beállítások:

| Beállítás | Leírás | Érték |
|:--- |:--- |:--- |
| **Tárház típusa** | A VSTS kód tárház típusa.<br/>**Megjegyzés:**: GitHub jelenleg nem támogatott. | Visual Studio Team Services Git |
| **Visual Studio Team Services Account** | A VSTS-fiók nevét. Megkeresheti a VSTS-fiók nevére, `https://{account name}.visualstudio.com`. Is [jelentkezzen be a VSTS-fiókba](https://www.visualstudio.com/team-services/git/) érhető el a Visual Studio-profil és a tárolóhelyekkel és a projektek. | \<a fiók neve > |
| **ProjectName** | A VSTS-projekt nevét. Megkeresheti a VSTS projektnévre, `https://{account name}.visualstudio.com/{project name}`. | \<a VSTS-projekt neve > |
| **RepositoryName** | A VSTS kód tárház nevét. VSTS-projektek Git tárhelyek kezelése a forráskódot, a projekt növekedésével tartalmaz. Hozzon létre egy új tárházat, vagy egy meglévő tárházon, amely már a projekthez. | \<a VSTS tárház neve > |
| **Importálja a meglévő adat-előállító erőforrások tárházba** | Megadja, hogy a meglévő data factory erőforrások importálása a UX **vászonra szerzői** be egy VSTS Git-tárházat. Jelölje be a jelölőnégyzetet annak a data factory erőforrások importálnia kell a társított Git-tárház JSON formátumban. Ez a művelet exportálja az egyes erőforrások külön-külön (Ez azt jelenti, hogy a társított szolgáltatások és az adatkészletek exportálása külön JSONs be). Ha ez a mező nincs bejelölve, a nem importált a meglévő erőforrásokat. | Kijelölt (alapértelmezett) |

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Konfigurációs 2. módszer: UX szerzői vászonra
Az az Azure Data Factory UX **vászonra szerzői**, keresse meg a data factory. Válassza ki a **adat-előállító** legördülő menüre, és válassza **konfigurálása kód tárház**.

Egy konfigurációs ablaktáblán jelenik meg. További konfigurációs beállításokkal kapcsolatos információkért lásd: a leírásokat <a href="#method1">konfigurációs módszer 1</a>.

![A kód tárház UX szerzői beállításainak konfigurálása](media/author-visually/configure-repo-2.png)

### <a name="use-version-control"></a>Verziókövetés alkalmazása
Verzió rendszerek (más néven _verziókövető_) segítségével a fejlesztők közösen dolgozzon a kódot, és nyomon követése végzett módosításokat a kód alap. A verziókövetési rendszerrel több fejlesztői projektek alapvető eszközét.

Minden adat-előállító társított VSTS Git-tárház egy főághoz rendelkezik. Amikor hozzáfér a VSTS Git-tárházba, a kódot választva módosíthatja **szinkronizálási** vagy **közzététel**:

![A kód módosítása vagy szinkronizálása](media/author-visually/sync-publish.png)

#### <a name="sync-code-changes"></a>Szinkronizálási kódmódosításokat
Miután kiválasztotta a **szinkronizálási**, akkor is lekéréses értéke a főágba helyett a helyi fiókiroda vagy változások az Ön helyi ágából a főágba.

![Szinkronizálási kódmódosításokat](media/author-visually/sync-change.png)

#### <a name="publish-code-changes"></a>Kódmódosításokat közzététele
Válassza ki **közzététel** közzététele kézzel a kódmódosításokat a főágba a Data Factory szolgáltatásnak.

> [!IMPORTANT]
> A főágba nincs képviselő mi történik a Data Factory szolgáltatásban. A főágba *kell* manuálisan közzé kell tenni a Data Factory szolgáltatásnak.

## <a name="use-the-expression-language"></a>A kifejezés nyelv használatával
A kifejezés nyelv, amely támogatja-e az Azure Data Factory használatával megadhatja a tulajdonságértékek kifejezések. Támogatott kifejezésekkel kapcsolatos információkért lásd: [kifejezések és az Azure Data Factory funkciók](control-flow-expression-language-functions.md).

Adja meg a tulajdonságok értékeit az adott kifejezések a UX használatával **vászonra szerzői**:

![A kifejezés nyelv használatával](media/author-visually/expression-language.png)

## <a name="specify-parameters"></a>Adja meg a paraméterek
Az Azure Data Factory adatcsatornákat és adathalmazokat paramétereinek megadhat **paraméterek** fülre. Könnyen használható paraméterek tulajdonságai kiválasztásával **dinamikus tartalom hozzáadása**:

![Dinamikus tartalom hozzáadása](media/author-visually/dynamic-content.png)

Használjon meglévő paramétereket, vagy adjon meg új paramétereket a tulajdonságértékek:

![Adja meg a tulajdonságértékek paraméterek](media/author-visually/parameters.png)

## <a name="provide-feedback"></a>Visszajelzés küldése
Válassza ki **visszajelzés** funkciókkal kapcsolatos megjegyzés, vagy értesítést küldeni a Microsoft az eszközzel kapcsolatos problémák:

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>További lépések
További információk figyelését és folyamatok kezelése című témakörben talál [figyelő programozott folyamatok kezelését és](monitor-programmatically.md).
