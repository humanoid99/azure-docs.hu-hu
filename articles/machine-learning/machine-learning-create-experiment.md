---
title: "Egy egyszerű kísérlet a Machine Learning Studióban | Microsoft Docs"
description: "Ez a Machine Learning-oktatóanyag egy egyszerű adatelemezési kísérletet mutat be. Egy regressziós algoritmus használatával fogjuk előre megbecsülni egy autó árát."
keywords: "kísérlet,lineáris regresszió,machine learning-algoritmusok,machine learning-oktatóanyag,prediktív modellezési technikák,adatelemzési kísérlet"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: 0539e9d04c61d35de56a29d350c07654c095c576
ms.lasthandoff: 03/22/2017


---

# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Machine Learning-oktatóanyag: Az első adatelemzési kísérlet létrehozása az Azure Machine Learning Studióban

Ha korábban még soha nem használta az **Azure Machine Learning Studiót**, ez az oktatóanyag Önnek szól.

Ebben az oktatóanyagban végigvezetjük azon, hogyan hozhat létre egy gépi tanulási kísérletet első alkalommal a Studio használatával. A kísérlet egy olyan analitikai modellt tesztel, amely különböző változók (például márka, műszaki jellemzők) alapján előre megbecsüli egy autó árát.

> [!NOTE]
> Az oktatóanyag bemutatja az alapokat, azt, hogy hogyan húzhat be modulokat a kísérletbe és kapcsolhatja össze azokat, és hogyan futtathatja a kísérletet és tekintheti meg az eredményeket. Nem térünk ki a gépi tanulás általános témakörére, mint ahogy arra sem, hogyan választhat a Studióban lévő 100+ beépített algoritmus és adatmanipulációs modell közül, és hogyan használhatja azokat.
>
>Ha most ismerkedik a gépi tanulással, az [Adatelemzés kezdőknek](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) videósorozat nagyszerű hely az induláshoz. Ez a videósorozat remek bevezetőt kínál hétköznapi nyelven és elterjedt fogalmak használatával a gépi tanulás témakörébe.
>
>Ha a gépi tanulás már ismerős az Ön számára, és csak a Machine Learning Studióval kapcsolatos általános információk, valamint a Studióban elérhető gépi tanulási algoritmusok érdeklik, íme néhány remek forrásanyag:
>
- [Mi a Machine Learning Studio?](machine-learning-what-is-ml-studio.md) - Ez a Studio magas szintű áttekintése.
- [A Machine Learning alapjai algoritmus példákkal](machine-learning-basics-infographic-with-algorithm-examples.md) – Ez az infografika hasznos lehet, ha többet szeretne megtudni a Machine Learning Studióban elérhető különféle gépi tanulási algoritmusokról.
- [Gépi tanulási útmutató](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) – Ez az útmutató hasonló információkat tartalmaz, mint a fenti infografika, de interaktív formában.
- [Gépi tanulási algoritmus-adatlap](machine-learning-algorithm-cheat-sheet.md) és [Algoritmusok kiválasztása a Microsoft Azure Machine Learninghez](machine-learning-algorithm-choice.md) – Ez a letölthető poszter és a kísérő cikk mélységükben tárgyalják a Studio algoritmusait.
- [Machine Learning Studio: Súgó az algoritmusokhoz és modulokhoz](https://msdn.microsoft.com/library/azure/dn905974.aspx) – Ez az összes Studio modul teljes referenciaanyaga, beleértve a gépi tanulási algoritmusokat.

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>Hogyan lehet a segítségére a Machine Learning Studio?

A Machine Learning Studio prediktív modellezési módszerekkel előre programozott, húzással kezelhető moduljaival megkönnyíti a kísérletek beállítását.

Az interaktív, vizuális munkaterület használatával az ***adathalmazokat*** és elemzési ***modulokat*** egy interaktív vászonra húzhatja. Itt összekapcsolja őket a Machine Learning Studio eszközben futtatható ***kísérletekké***.
***Létrehoz egy modellt***, ***betanítja a modellt***, majd ***pontozza és teszteli a modellt***.

A újra és újra átdolgozhatja a modellt, módosíthatja és futtathatja a kísérletet, amíg azokat ez eredményeket nem hozza, amelyeket vár. Ha a modell elkészült, közzéteheti ***webszolgáltatásként***, így mások is küldhetnek bele adatokat és kaphatnak előjelzéseket.

## <a name="open-machine-learning-studio"></a>A Machine Learning Studio megnyitása

A Studio használatának elkezdéséhez lépjen a [https://studio.azureml.net](https://studio.azureml.net) helyre. Ha korábban már bejelentkezett a Machine Learning Studióba, kattintson a **Sign in** (Bejelentkezés) lehetőségre. Ellenkező esetben kattintson a **Sign up here** (Regisztráció itt) lehetőségre, majd válasszon az ingyenes vagy a fizetős lehetőség használata közül.

![Bejelentkezés a Machine Learning Studióba][sign-in-to-studio]
<br/>
***Bejelentkezés a Machine Learning Studióba***

## <a name="five-steps-to-create-an-experiment"></a>A kísérlet létrehozásának öt lépése

Ebben a Machine Learning oktatóanyagban öt lépés végrehajtásával fogjuk megalkotni a Machine Learning Studio-kísérletet, amely elvégzi a modell létrehozását, betanítását és pontozását. Ezek:

- **Modell létrehozása**
    - [1. lépés: Az adatok beszerzése]
    - [2. lépés: Az adatok előkészítése]
    - [3. lépés: A jellemzők meghatározása]
- **A modell betanítása**
    - [4. lépés: Tanulási algoritmus kiválasztása és alkalmazása]
- **A modell pontozása és tesztelése**
    - [5. lépés: Új autó árának előrejelzése]

[1. lépés: Az adatok beszerzése]: #step-1-get-data
[2. lépés: Az adatok előkészítése]: #step-2-prepare-the-data
[3. lépés: A jellemzők meghatározása]: #step-3-define-features
[4. lépés: Tanulási algoritmus kiválasztása és alkalmazása]: #step-4-choose-and-apply-a-learning-algorithm
[5. lépés: Új autó árának előrejelzése]: #step-5-predict-new-automobile-prices

> [!TIP] 
> A [Cortana Intelligence Galleryben](https://gallery.cortanaintelligence.com) megtalálja az alábbi kísérlet egy működő példányát. Lépjen a **[Your first data science experiment - Automobile price prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** (Az első adatelemzési kísérlet – Autó árának előrejelzése) lapra, és kattintson az **Open in Studio** (Megnyitás a Studióban) lehetőségre a kísérlet Machine Learning Studio munkaterületre való letöltéséhez.


## <a name="step-1-get-data"></a>1. lépés: Az adatok beszerzése

A Machine Learning alkalmazásához először is adatokra van szüksége.
A Machine Learning Studio számos mintaként használható adathalmazt tartalmaz, de számos más forrásból is importálhat adatokat. Ebben a példában a munkaterületén megtalálható **Automobile price data (Raw)** (Nyers autóáradatok) nevű mintahalmazt fogjuk használni.
Ebben az adathalmazban számos különböző autót bemutató bejegyzés szerepel. A bejegyzések számos adatot (például márka, típus, műszaki specifikációk, ár) tartalmaznak.

A következőképpen vonhatja be az adathalmazt a kísérletbe.

1. Új kísérlet létrehozásához kattintson a Machine Learning Studio ablakának alsó részén található **+NEW** (Új létrehozása) gombra, és válassza az **EXPERIMENT** (Kísérlet), majd a **Blank Experiment** (Üres kísérlet) lehetőséget.

2. A kísérlet kap egy alapértelmezett nevet, amelyet a vászon tetején láthat. Jelölje ki ezt a szöveget, és módosítsa valami értelmesebbre, például arra, hogy **Autó árának előrejelzése**. A névnek nem kell egyedinek lennie.

    ![A kísérlet átnevezése][rename-experiment]

2. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. A paletta tetején található keresőmezőbe gépelje be, hogy **automobile**. A rendszer megjeleníti az **Automobile price data (Raw)** (Nyers autóáradatok) nevű adathalmazt. Húzza rá az adathalmazt a kísérletvászonra.

    ![Az autókat tartalmazó adathalmaz megkeresése és a kísérleti vászonra húzása][type-automobile]
    <br/>
    ***Az autókat tartalmazó adathalmaz megkeresése és a kísérleti vászonra húzása***

Ha szeretné megtekinteni az adatokat, kattintson az autókat tartalmazó adathalmaz alsó részén látható kimeneti portra, és válassza a **Visualize** (Képi megjelenítés) elemet.

![Kattintson a kimeneti portra, majd válassza a „Visualize” (Képi megjelenítés) lehetősége][select-visualize]
<br/>
***Kattintson a kimeneti portra, majd válassza a „Visualize” (Képi megjelenítés) lehetőséget***

> [!TIP]
> Az adathalmazok és modulok kis körökkel jelölt bemeneti és kimeneti portokkal rendelkeznek – a bemeneti portok felül, a kimeneti portok alul találhatók.
Az adatfolyam létrehozásához a kísérlet során össze fogja kötni az egyik modul kimeneti portját egy másik modul bemeneti portjával.
Ha meg szeretné tekinteni, hogyan jelennek meg az adatok az adatfolyam egy adott pontján, kattintson az adathalmaz vagy modul kimeneti portjára.

Ebben a minta adatkészletben minden autópéldány külön sorban szerepel, az egyes autókhoz tartozó változók pedig oszlopokban jelennek meg. Az egy adott autóhoz tartozó változók alapján megpróbáljuk előrejelezni az árát a jobb szélső oszlopban (26. oszlop, a neve „price” (ár)).

![Az autókra vonatkozó adatok megtekintése az adatokat megjelenítő ablakban][visualize-auto-data]
<br/>
***Az autókra vonatkozó adatok megtekintése az adatokat megjelenítő ablakban***

A jobb felső sarokban látható „**x**” gombra kattintva zárja be a képi megjelenítési ablakot.

## <a name="step-2-prepare-the-data"></a>2. lépés: Az adatok előkészítése

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette például, hogy az oszlopok számos sorából hiányoznak az értékek. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Ebben az esetben törölni fogjuk azokat a sorokat, amelyekből értékek hiányoznak. A **normalized-losses** (normalizált veszteségek) című oszlopból ráadásul rendkívül sok érték hiányzik, ezért ezt az oszlopot teljesen kizárjuk a modellből.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

Először hozzáadunk egy modult, amely eltávolítja a **normalized-losses** (normalizált veszteségek) oszlopot, majd hozzáadunk egy másik modult, amely eltávolítja az összes sort, amelyből adatok hiányoznak.

1. A modulpaletta tetején található keresőmezőbe gépelje be a **select columns** (oszlopok kijelölése) kifejezést, és amikor a rendszer megjeleníti a [Adathalmaz oszlopainak kijelölése][select-columns] modult, húzza azt a kísérletvászonra. Ezzel a modullal kiválaszthatjuk, hogy melyik adatoszlopokat szeretnénk bevonni a modellbe, vagy éppen kizárni a modellből.

2. Kösse össze az **Automobile price data (Raw)** (Autóárak adatai (nyers)) című adathalmaz kimeneti portját a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul bemeneti portjával.

    ![A „Select Columns in Dataset” (Adathalmaz oszlopainak kijelölése) modul hozzáadása a kísérletvászonhoz, majd összekötése][type-select-columns]
    <br/>
    ***A „Select Columns in Dataset” (Adathalmaz oszlopainak kijelölése) modul hozzáadása a kísérletvászonhoz, majd összekötése***

3. Kattintson a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, majd a **Properties** (Tulajdonságok) panelen kattintson a **Launch column selector** (Oszlopválasztó elindítása) elemre.

    - A bal oldalon kattintson a **With rules** (Szabályokkal) lehetőségre
    - A **Begin With** (Kezdés a következővel) területen kattintson az **All columns** (Minden oszlop) lehetőségre. Ezzel megadja, hogy a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul az összes oszlopot feldolgozza (kivéve azokat, amelyeket hamarosan ki fogunk zárni).
    - A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Megjelenik az oszlopnevek listája. Válassza a **normalized-losses** (normalizált veszteségek) lehetőséget, amely aztán bekerül a szövegdobozba.
    - Az oszlopválasztó bezárásához kattintson a pipa (OK) gombra (a jobb alsó területen).

    ![Az oszlopválasztó elindítása és a „normalized-losses” (normalizált veszteségek) oszlop kizárása][launch-column-selector]
    <br/>
    ***Az oszlopválasztó elindítása és a „normalized-losses” (normalizált veszteségek) oszlop kizárása***

    Ekkor a **Select Columns in Dataset** (Adathalmaz oszlopainak kijelölése) modul Properties (Tulajdonságok) panelje jelzi, hogy a modul a **normalized-losses** ( normalizált veszteségek) kivételével az adathalmaz összes oszlopát fel fogja dolgozni.

    ![A tulajdonságok panelen látható, hogy a „normalized-losses” ( normalizált veszteségek) oszlop ki van zárva][showing-excluded-column]
    <br/>
    ***A tulajdonságok panelen látható, hogy a „normalized-losses” ( normalizált veszteségek) oszlop ki van zárva***

    > [!TIP]
    A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. A jelen esetben kattintson duplán a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, és írja be az „Exclude normalized losses” (A normalized-losses oszlop kizárása) szöveget.
    >
    >


    ![Megjegyzés hozzáadása egy modulhoz dupla kattintással][add-comment]
    <br/>
    ***Megjegyzés hozzáadása egy modulhoz dupla kattintással***

3. Húzza a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modult a kísérletvászonra, és kösse össze a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal. A **Properties** (Tulajdonságok) panel **Cleaning mode** (Törlés módja) beállításánál válassza a **Remove entire row** (Teljes sor eltávolítása) lehetőséget. Ezzel arra utasítja a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modult, hogy ha hiányzó adatot talál, az egész sort törölje. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

    ![Törlés módjának beállítása „Remove entire row” (Teljes sor eltávolítása) lehetőségre a „Clean Missing Data” (Hiányzó adatok törlése) modulnál][set-remove-entire-row]
    <br/>
    ***Törlés módjának beállítása „Remove entire row” (Teljes sor eltávolítása) lehetőségre a „Clean Missing Data” (Hiányzó adatok törlése) modulnál***

4. A kísérlet futtatásához kattintson a lap alján található **RUN** (Futtatás) parancsra.

    A kísérlet befejezését követően az összes modulnál megjelenik egy zöld pipa, amely jelzi, hogy az adott modul sikeresen lefutott. A jobb felső sarokban pedig megjelenik a **Finished running** (Futtatás befejeződött) állapot.

![A kísérlet várható megjelenése a futtatás után][early-experiment-run]
<br/>
***A kísérlet várható megjelenése a futtatás után***

> [!TIP]
> Miért futtattuk a kísérletet most? A kísérlet futtatásával biztosítható, hogy az adatokhoz tartozó oszlopdefiníciók az adatkészletből áthaladnak a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulon és a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulon. Ez azt jelenti, hogy a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulhoz kapcsolt modulok is megkapják ugyanezeket az adatokat.

Eddig még csupán az adatok megtisztítását végeztük el a kísérletben. Ha szeretné megtekinteni a megtisztított adathalmazt, kattintson a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portjára, és válassza a **Visualize** (Képi megjelenítés) lehetőséget. Láthatja, hogy a **normalized-losses** oszlop eltűnt, ahogy a hiányzó értékek is.

Most, hogy megtisztítottuk az adatokat, megadhatjuk, hogy mely jellemzőket szeretnénk felhasználni a prediktív modellben.

## <a name="step-3-define-features"></a>3. lépés: A jellemzők meghatározása

A gépi tanulásban a *jellemzők* azok a külön-külön mérhető tulajdonságok, amelyekre kíváncsiak vagyunk. Adathalmazunk minden sora egy-egy autót képvisel, az oszlopok pedig az autók különböző jellemzőit tartalmazzák.

A prediktív modellben használandó jellemzők helyes megválasztásához fontos a kísérletezés, illetve a megoldani kívánt probléma jó ismerete. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Ráadásul egyes jellemzők erős korrelációban állnak más jellemzőkkel, és eltávolíthatók. A példánkban például szorosan összefügg a city-mpg (fogyasztás városban) és highway-mpg (fogyasztás autópályán), ezért az egyiket eltávolíthatjuk anélkül, hogy lényegesen befolyásolnánk az előrejelzést.

Ideje, hogy létrehozzuk a modellt az adathalmaz jellemzőinek meghatározott részhalmaza alapján. Később visszatérhet ehhez a lépéshez, és más jellemzőket kiválasztva ismét lefuttathatja a kísérletet, ha kíváncsi rá, hogy úgy jobb eredményeket kap-e. Kezdésként azonban a következő funkciókat próbáljuk ki:

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Húzzon egy újabb [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modult a kísérletvászonra. Kösse össze a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portját a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul bemenetével.

    ![A „Select Columns in Dataset” (Adathalmaz oszlopainak kijelölése) modul összekötése a „Clean Missing Data” (Hiányzó adatok törlése) modullal][connect-clean-to-select]
    <br/>
    ***A „Select Columns in Dataset” (Adathalmaz oszlopainak kijelölése) modul összekötése a „Clean Missing Data” (Hiányzó adatok törlése) modullal***

2. Kattintson duplán a modulra, és írja be: „Az előrejelzéshez használatos jellemzők kiválasztása”.

2. Kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére.

3. Kattintson a **With rules** (Szabályokkal) lehetőségre.

4. A **Begin With** (Kezdés a következővel) területen kattintson a **No columns** (Egyetlen oszlop sem) lehetőségre. A szűrősorban válassza ki az **Include** (Belefoglalás) és a **column names** (oszlopnevek) lehetőséget, és jelölje ki az oszlopnevek listáját a szövegmezőben. Ezzel arra utasítja a modult, hogy csak az általunk megadott oszlopokat (tulajdonságokat) dolgozza fel.

5. Kattintson a pipa (OK) gombra.

    ![Az előrejelzésbe bevonni kívánt oszlopok (tulajdonságok) kijelölése][select-columns-to-include]
    <br/>
    ***Az előrejelzésbe bevonni kívánt oszlopok (tulajdonságok) kijelölése***

Ezzel létrehoz egy szűrt adathalmazt, amelyben csak a következő lépésben használandó tanulási algoritmusnak továbbítani kívánt tulajdonságok szerepelnek. Később visszatérhet ide, és más jellemzőkkel is elvégezheti az előrejelzést.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>4. lépés: Tanulási algoritmus kiválasztása és alkalmazása

Most, hogy előkészítettük az adatokat, a prediktív modell létrehozásához már csak a tanítás és a tesztelés szükséges. A következőkben az adatok segítségével elvégezzük a modell betanítását, majd a modell tesztelésével megállapítjuk, hogy milyen pontossággal képes előre jelezni az árakat.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

A *besorolás* és a *regresszió* két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. Besoroláskor a válaszok előrejelzése megadott kategóriakészletből történik (például: színek (vörös, kék vagy zöld)). A rendszer a számok előrejelzésére regressziós módszert használ.

Mivel az árat szeretnénk előre jelezni, ami egy szám, regressziós algoritmust fogunk használni. Ebben a példában egy egyszerű *lineáris regressziós* modellt használunk.

> [!TIP]
> Ha szeretne többet megtudni a Machine Learning algoritmusok különböző típusairól és az alkalmazási területükről, érdemes megnéznie az Adatelemzés kezdőknek sorozat [The five questions data science answers](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) (Öt kérdés, amelyre az adatelemzés megadja a választ) című első videóját. Érdemes lehet megtekintenie a [Machine learning basics with algorithm examples](machine-learning-basics-infographic-with-algorithm-examples.md) (A Machine Learning alapjai algoritmuspéldákkal) infografikát is, vagy a [Machine learning algorithm cheat sheet](machine-learning-algorithm-cheat-sheet.md) (Machine Learning algoritmus-adatlap) ábrát.

A modell betanításához az árat tartalmazó adathalmazt biztosítunk számára. A modell megvizsgálja adatokat, és összefüggéseket keres az autó tulajdonságai és az ára között. Ezután teszteljük a modellt. Ehhez olyan autók tulajdonságkészletét töltjük be, amelyeket ismerünk, és megnézzük, hogy mennyire sikeresen tudja a modell előre jelezni az ismert árakat.

Az adatok a modell betanítására és tesztelésére is használhatók. Ehhez két halmazra, egy tanítási és egy tesztelési halmazra osztjuk fel az adatokat.

1. Jelölje ki, majd húzza a kísérletvászonra a [Split Data][split] (Adatok felosztása) modult, majd kösse össze a legutóbb használt [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal.

2. Kattintással jelölje ki a [Split Data][split] (Adatok felosztása) modult. Keresse meg a **Properties** (Tulajdonságok) panelen a vászontól jobbra a **Fraction of rows in the first output dataset** (Sorok hányadosa az első kimeneti adathalmazban) beállítást, és adja meg a 0,75 értéket. Így az adatok 75 százalékát a modell betanítására, 25 százalékát pedig a modell tesztelésére használhatjuk (a későbbiekben más százalékokkal is elvégezheti a kísérletet).

    ![A „Split Data” (Adatok felosztása) modul felosztási értékének beállítása 0,75-re][set-split-data-percentage]
    <br/>
    ***A „Split Data” (Adatok felosztása) modul felosztási értékének beállítása 0,75-re***

    > [!TIP]
    > A **Random seed** (Véletlenszám-generálás kezdőértéke) paraméter módosításával különböző véletlenszerűen kiválasztott mintákat hozhat létre, amelyeket szintén felhasználhat a modell betanítására és tesztelésére. Ez a paraméter szabályozza a pszeudo-véletlenszám-generátor kezdőértékét.

2. Futtassa a kísérletet. A kísérlet futtatásakor a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) és a [Split Data][split] (Adatok felosztása) modul átadja a következőkben hozzáadott moduloknak az oszlopdefiníciókat.  

3. A tanulási algoritmus kiválasztásához bontsa ki a vászontól balra, a modulpalettán található **Machine Learning** (Gépi tanulás) kategóriát, majd bontsa ki az **Initialize Model** (Inicializálási modell) kategóriát is. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus. Ehhez a kísérlethez válassza a **Regression** (Regresszió) kategóriában található [Linear Regression][linear-regression] (Lineáris regresszió) modult, majd húzza a kísérletvászonra.
(A modult úgy is megkeresheti, ha a paletta keresőmezőjébe beírja a „linear regression” kifejezést.)

4. Keresse meg, majd húzza a kísérletvászonra a [Train Model][train-model] (Modell betanítása) modult. Kösse össze a [Linear Regression][linear-regression] (Lineáris regresszió) modul kimenetét a [Train Model][train-model] (Modell betanítása) modul bal oldali bemenetével, és kösse össze a [Split Data][split] (Adatok felosztása) modul adatbetanítási kimenetét (bal oldali port) a [Train Model][train-model] (Modell betanítása) modul jobb oldali bemenetével.

    ![A „Train Model” (Modell betanítása) modul összekötése a „Linear Regression” (Lineáris regresszió) és a „Split Data” (Adatok felosztása) modulokkal][connect-train-model]
    <br/>
    ***A „Train Model” (Modell betanítása) modul összekötése a „Linear Regression” (Lineáris regresszió) és a „Split Data” (Adatok felosztása) modulokkal***

5. Kattintson a [Train Model][train-model] (Modell betanítása) modulra, kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére, és válassza ki a **price** (ár) oszlopot. Ez az az érték, amelyet a modellünk megpróbál előre jelezni.

    Jelölje ki a **price** (ár) oszlopot az oszlopválasztóban. Ehhez helyezze át az **Available columns** (Elérhető oszlopok) listáról a **Selected columns** (Kiválasztott oszlopok) listára.

    ![Az ár oszlop kiválasztása a „Train Model” (Modell betanítása) modulhoz][select-price-column]
    <br/>
    ***Az ár oszlop kiválasztása a „Train Model” (Modell betanítása) modulhoz***

6. Futtassa a kísérletet.

Ezzel kapunk egy betanított regressziós modellt, amely képes pontszámot rendelni az új autóadatokhoz, és így előre jelezni az árakat.

![A kísérlet várható megjelenése a futtatás után][second-experiment-run]
<br/>
***A kísérlet várható megjelenése a futtatás után***

## <a name="step-5-predict-new-automobile-prices"></a>5. lépés: Új autó árának előrejelzése

Most, hogy adataink 75 százalékával betanítottuk a modellt, a maradék 25 százalék pontozásával megállapíthatjuk, hogy mennyire működik jól.

1. Keresse meg, majd húzza a kísérletvászonra a [Score Model][score-model] (Modell pontozása) modult. Kösse össze a [Train Model][train-model] (Modell betanítása) modul kimenetét a [Score Model][score-model] (Modell pontozása) modul bal oldali bemeneti portjával. Kösse össze a [Split Data][split] (Adatok felosztása) modul tesztelési adatokat tartalmazó kimenetét (jobb oldali portját) a [Score Model][score-model] (Modell pontozása) modul jobb oldali bemeneti portjával.

    ![A „Score Model” (Modell pontozása) modul összekötése a „Train Model” (Modell betanítása) és a „Split Data” (Adatok felosztása) modulokkal][connect-score-model]
    <br/>
    ***A „Score Model” (Modell pontozása) modul összekötése a „Train Model” (Modell betanítása) és a „Split Data” (Adatok felosztása) modulokkal***

2. Futtassa a kísérletet, és tekintse meg a [Score Model][score-model] (Modell pontozása) modul eredményét (kattintson a [Score Model][score-model] modul kimeneti portjára, majd válassza a **Visualize** (Képi megjelenítés) lehetőséget). A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.  

    ![A „Score Model” (Modell pontozása) modul kimenete][score-model-output]
    <br/>
    ***A „Score Model” (Modell pontozása) modul kimenete***

3. Végül teszteljük az eredmény minőségét. Jelölje ki, majd húzza a kísérletvászonra az [Evaluate Model][evaluate-model] (Modell kiértékelése) modult, és kösse össze a [Score Model][score-model] (Modell pontozása) modul kimenetét az [Evaluate Model][evaluate-model] (Modell kiértékelése) bal oldali bemeneti portjával.

    > [!TIP]
    > Az [Evaluate Model][evaluate-model] (Modell kiértékelése) modulon két bemeneti port található, mivel két modell összehasonlítására is használható. Később hozzáadhat egy másik algoritmust a kísérlethez, és az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul segítségével ellenőrizheti, melyik ad jobb eredményeket.

4. Futtassa a kísérletet.

Az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul eredményének megtekintéséhez kattintson a kimeneti portra, majd válassza a **Visualize** (Képi megjelenítés) lehetőséget.

![A kísérlet kiértékelésének eredménye][evaluation-results]
<br/>
***A kísérlet kiértékelésének eredménye***

A következő statisztikák tekinthetők meg:

- **Mean Absolute Error** (átlagos abszolút eltérés, MAE): az abszolút eltérések átlaga (*eltérésnek* az előre jelzett érték és a tényleges érték közötti különbséget nevezzük).
- **Root Mean Squared Error** (gyökátlagos négyzetes eltérés, RMSE): a tesztelési adathalmazon végzett előrejelzések eltéréseinek négyzetéből számított átlag négyzetgyöke.
- **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
- **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
- **Coefficient of Determination** (determinációs együttható): ez az **R-négyzet értéke** néven is ismert statisztikai mérőszám azt mutatja, hogy a modell mennyire illik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A **Coefficient of Determination** (determinációs együttható) értéke minél közelebb van az egyhez (1,0-hoz), annál pontosabb az előrejelzés.

## <a name="final-experiment"></a>Elkészült kísérlet

Az elkészült kísérletnek a következőképpen kell kinéznie:

![Az elkészült kísérlet][complete-linear-regression-experiment]
<br/>
***Az elkészült kísérlet***

## <a name="next-steps"></a>Következő lépések

Most, hogy az első Machine Learning oktatóanyag végére ért, és beállította kísérletét, tovább dolgozhat a modell javításán, majd üzembe helyezheti prediktív webszolgáltatásként.

- **A modell továbbfejlesztése a művelet ismétlésével** – Például módosíthatja az előrejelzéshez használt jellemzők körét. Emellett módosíthatja a [Linear Regression][linear-regression] (Lineáris regresszió) algoritmus tulajdonságait, vagy akár egy teljesen más algoritmust is kipróbálhat. Akár két különböző gépi tanulási algoritmus segítségével is futtathatja a kísérletet, majd az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul használatával összehasonlíthatja az eredményeket.
Több modell összehasonlítására egyetlen kísérletben a [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) [Compare Regressors](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) (Regresszorok összehasonlítása) részében találhat példát.

    > [!TIP]
    > Az ismétlések egy példányának másolásához használja az oldal alján található **SAVE AS** (Mentés másként) gombot. A kísérlet összes ismétlésének megtekintéséhez kattintson az oldal alján található **VIEW RUN HISTORY** (Futtatási előzmények megtekintése) parancsra. További információ: [Kísérlet ismétléseinek kezelése az Azure Machine Learning Studióban][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

- **A modell telepítése prediktív webszolgáltatásként** – Ha már elégedett a modellel, helyezze üzembe webszolgáltatásként, amely új adatok alapján képes előre jelezni az autók árát. További információ: [Azure Machine Learning-webszolgáltatás üzembe helyezése][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Szeretne többet megtudni? Ha szeretné részletesebben megismerni a modellek létrehozásához, tanításához, pontozásához és üzembe helyezéséhez használható folyamatot, olvassa el [a prediktív megoldások Azure Machine Learning segítségével való fejlesztését][walkthrough] ismertető cikket.

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/machine-learning-create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/machine-learning-create-experiment/rename-experiment.png
[visualize-auto-data]:./media/machine-learning-create-experiment/visualize-auto-data.png
[select-visualize]: ./media/machine-learning-create-experiment/select-visualize.png
[showing-excluded-column]:./media/machine-learning-create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/machine-learning-create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/machine-learning-create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/machine-learning-create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/machine-learning-create-experiment/second-experiment-run.png
[connect-score-model]:./media/machine-learning-create-experiment/connect-score-model.png
[evaluation-results]:./media/machine-learning-create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/machine-learning-create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/machine-learning-create-experiment/type-automobile.png
[type-select-columns]:./media/machine-learning-create-experiment/type-select-columns.png
[launch-column-selector]:./media/machine-learning-create-experiment/launch-column-selector.png
[add-comment]:./media/machine-learning-create-experiment/add-comment.png
[connect-clean-to-select]:./media/machine-learning-create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/machine-learning-create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/machine-learning-create-experiment/connect-train-model.png
[select-price-column]:./media/machine-learning-create-experiment/select-price-column.png

[score-model-output]:./media/machine-learning-create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

