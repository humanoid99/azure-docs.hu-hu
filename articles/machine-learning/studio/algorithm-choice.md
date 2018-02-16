---
title: "Gépi tanulási algoritmusok kiválasztása |} Microsoft Docs"
description: "A felügyelt és nem felügyelt tanítás Azure Machine Learning algoritmusok kiválasztása a fürtszolgáltatás, besorolási vagy regressziós kísérletekben módját."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/18/2017
ms.author: garye
ms.openlocfilehash: 1b30e4dbf20cac653c323720de779aa5f8edba68
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>A Microsoft Azure Machine Learning-algoritmusok kiválasztása
A "Milyen gépi tanulási algoritmus érdemes használni?" kérdésre adott válasz mindig kapcsolva "Attól függ." Azt a mérete, a minőségi és a adatok jellegének függ. Ez attól függ, hogy mit kíván tenni a választ. Ez attól függ, hogy hogyan algoritmus matematikai utasításokat a számítógépet, hogy lett lefordítva. Annak függvénye, és mennyi idővel rendelkezik. Még a legtöbb tapasztalt adatszakértőkön nem sikerült megállapítani, mely algoritmus hajtja végre a legjobb előtt őket.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>A gépi tanulási algoritmus Cheat lap
A **Microsoft Azure Machine Learning algoritmus Cheat lap** segítségével úgy dönt, hogy a jobb oldali számítógép a prediktív elemzési megoldások a Microsoft Azure Machine Learning-könyvtárból algoritmusok tanulási algoritmus.
Ez a cikk bemutatja, hogyan való használatát.

> [!NOTE]
> Töltse le a Adatlap, és kövesse az ebben a cikkben együtt, keresse fel [gépi tanulási algoritmus-adatlap a Microsoft Azure Machine Learning Studióban](algorithm-cheat-sheet.md).
> 
> 

Ez az adatlap egy nagyon adott célközönséget van tartva: egy kezdete adatok tudósok undergraduate szintű machine Learning segítségével, megpróbálja válasszunk algoritmust kezdje az Azure Machine Learning Studióban. Ez azt jelenti, hogy néhány általánosítást és oversimplifications teszi, de biztonságos irányba mutat. Azt is jelenti, hogy vannak-e nagy mennyiségű algoritmusok itt nem látható. Növekedésével Azure Machine Learning magában foglaló rendelkezésre álló metódusok több teljes készletét, fel kell venni őket.

Ezek a javaslatok még lefordított visszajelzések és tippek sok adatszakértőkön és machine learning szakértői. Jelenleg nem fogadja el a licencfeltételeket a mindent, de a vélemények összehangolni a nyers együttműködési próbált I. Legtöbb utasítás érjen véget kezdődhet "Előfeltétel..."

### <a name="how-to-use-the-cheat-sheet"></a>Az Adatlap használata
Olvassa el a diagram elérési útját és algoritmus címkéinek "a  *&lt;elérési útját címke&gt;*, használjon  *&lt;algoritmus&gt;*." Például "a *sebesség*, használjon *két osztály logisztikai regresszió*." Egyes esetekben több fiókirodai vonatkozik.
Egyes esetekben tökéletes egyikük sem. Ezek éppen használhatók szabály az USB-a javaslatok, így nem kell aggódni pontos alatt.
Több adatszakértőkön I volt szó, az említett, amely a csak meg arról, hogy a legjobb algoritmus található módja próbálja az összes.

A példában a [Azure Eszközintelligencia-katalógus](http://gallery.cortanaintelligence.com/) egy kísérlet, amely megpróbál több algoritmusok elleni ugyanazokat az adatokat, és összehasonlítja az eredményeket: [több osztály osztályozó összehasonlítása: felismerés betűs](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Ha szeretne egy letölthető és kinyomtatható diagramot a Machine Learning Studio funkcióiról, tekintse meg [Az Azure Machine Learning Studio funkcióit bemutató diagram](studio-overview-diagram.md) című cikket.
> 
> 

## <a name="flavors-of-machine-learning"></a>Változatban is elkészíti a gépi tanulás
### <a name="supervised"></a>Felügyelt
Felügyelt tanítás során algoritmusok előrejelzésekhez példák alapján. Például korábbi készlet árak veszély Találgatások jövőbeli áron használható. Minden példában képzési érdeklő értékkel lett címkézve – ebben az esetben a készlet árát. Felügyelt tanulási algoritmus ezen érték címkék minták keresi. Használhat bármilyen vonatkozó információk – napja a héten, az évszak, a vállalati pénzügyi adatokat, iparági típusú, zavaró geopolitikai események meglétét, – és minden algoritmus minták különböző típusú keresi. Miután az algoritmus megtalálta a legjobb mintát azt is, minta használja a címke nélküli tesztelési adatokat vonatkozó előrejelzésekhez – jövő árak.

Felügyelt tanítás során olyan népszerű és hasznos a gépi tanulás. Az egyetlen kivétel az Azure Machine Learning a modulok felügyelete tanulási algoritmus. Több adott típusú felügyelt tanítás során, amely az Azure Machine Learning belül vannak megadva: besorolás, a regresszióra és anomáliadetektálási észlelése.

* **Besorolási**. Ha az adatok használatban van egy kategóriát előre jelezni, felügyelt tanítás során rövidítése besorolás. Ez a helyzet a "cat" vagy "kutya" képként lemezkép hozzárendelésekor. Ha csak két választási lehetőség, hívott **két osztályú** vagy **binomiális besorolás**. További kategóriájuk van, mint amikor az nyer, a NCAA március Madness sportverseny előrejelzésére, ha a probléma nevezik **több osztály besorolás**.
* **Regressziós**. Egy érték van folyamatban előre jelezni, csakúgy, mint a mennyisége, felügyelt tanítás során regressziós neve.
* **Anomáliadetektálás**. Egyes esetekben a célja egyszerűen szokatlan adatok pontok azonosításához. A csalások felderítését például minden nagyon szokatlan hitelkártya költségkeret minták a gyanús. A lehetséges változata, számos, és az így néhány, hogy nem található megtudhatja, milyen csalárd tevékenység példák a következőképpen néz. A megközelítést anomáliadetektálás időt vesz igénybe, hogy egyszerűen megtudhatja, milyen szokásos tevékenység néz (egy előzmények nem csalárd tranzakciókat használatával), és mindent, ami jelentősen eltérő azonosításához.

### <a name="unsupervised"></a>Felügyeletlen
Felügyeletlen tanulási adatpontok hozzájuk társított címkéket nem rendelkezhet. Ehelyett felügyeletlen tanulási algoritmus célja az adatok valamilyen módon vagy struktúrája ismertetik. Ez azt csoportokba a csoport, vagy megtekint összetett adatokat úgy, hogy egyszerűbb vagy több szervezett különböző módokat kereséséhez.

### <a name="reinforcement-learning"></a>Learning megerősítése
A tanulási megerősítése az algoritmus lekérdezi minden adatpontnál válaszul művelet kiválasztását. A tanulási algoritmus is kap egy ellenszolgáltatás jel rövid időn belül később jelző mennyire jó a döntést.
Ennek alapján, az algoritmus módosítja a stratégia a legmagasabb ellenszolgáltatás elérése érdekében. Jelenleg nincsenek nem tanulási algoritmus Azure Machine Learning modulok megerősítése. Megerősítése tanulási közös az olyan robotics, ahol adatpont érzékelő szivattyútelepek érzékelőinek adatai egy helyen időben lesz, és az algoritmus ki kell választania a robot a következő művelet. Akkor is természetes alkalmas, az eszközök internetes hálózata alkalmazások.

## <a name="considerations-when-choosing-an-algorithm"></a>Egy algoritmus kiválasztása szempontjai
### <a name="accuracy"></a>Pontosság
Első lehető legpontosabb válasz nem mindig szükséges.
Egyes esetekben közelítés számára, attól függően, hogy szeretné használni. Ha ez a helyzet, lehet kivágni a feldolgozási ideje jelentős mértékben további hozzávetőleges metódusával váltani. Egy másik hozzávetőleges módszerek további előnye, hogy azok természetesen az egyes elkerülése érdekében [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Képzési idő
Perc vagy a modell betanításához szükséges órák száma jelentős mértékben közötti algoritmusok függ. Idő betanítása gyakran szorosan kötődik pontossága – egy általában kísérik, a másik. Emellett az egyes algoritmusok érzékenyebb a számú adatpontot tartalmaznak, mint a többire is.
Korlátozott idő esetén azt is meghajtó a választott algoritmust, különösen akkor, ha az adatkészlet nagy.

### <a name="linearity"></a>Lineáris
Ellenőrizze a nagy mennyiségű gépi tanulási algoritmusok lineáris használatát. Lineáris besorolás algoritmusok azt feltételezik, hogy osztályok választhatók el egymástól egyenes (vagy annak újabb dimenziós analóg). Ezek közé tartozik a logisztikai regresszió és vektoros gép támogatása (az Azure Machine Learning megvalósuló).
Lineáris regressziós algoritmus azt feltételezik, hogy a adatok trendek egyenes kövesse. Ilyen Előfeltevések nem alkalmasak olyan problémák, de a többi azok leállíthatja pontossága.

![Az osztály nem lineáris határ][1]

***Az osztály nem lineáris határ*** *-egy lineáris osztályozó algoritmus hagyatkoznia eredményeznének alacsony pontossága*

![Adatok lineáris trend][2]

***Egy lineáris trend adatok*** *-lineáris regressziós módszerrel hoz létre sok szükségesnél nagyobb hibák*

Annak ellenére, hogy a veszélyek lineáris algoritmusok népszerű támadások első sorként. Ezek általában algorithmically gyorsan és egyszerűen kell még betanítani.

### <a name="number-of-parameters"></a>A paraméterek száma
Paraméterei a forgatógombját egy adatok tudósok lekérdezi a bekapcsolás lehetőséget algoritmus beállítása. Azok a számok, amelyek hatással vannak az algoritmus viselkedését, például a hibatűrés vagy ismétlési vagy a beállítások között a algoritmus viselkedését változatának számát. A képzési időt és az algoritmus pontosságát néha lehet meglehetősen érzékeny csak a megfelelő beállítások beolvasásakor. Sok paraméterekkel algoritmusok általában a legtöbb próbaverzió és hiba található jó kombinációjából szükséges.

Azt is megteheti, hogy van egy [paraméter abszolút](algorithm-parameters-optimize.md) modul letiltása az Azure Machine Learning, amely automatikusan megkísérli az összes paraméterkombinációk bármilyen részletességű választja. Ez nem kiváló módja annak, győződjön meg arról, hogy ölel paraméter terület, a modell betanításához szükséges idő exponenciálisan a paraméterek számát növeli.

A feje, hogy ha sok paraméter általában azt jelzi, hogy rendelkezik-e az algoritmus nagyobb rugalmasságot. Nagyon jó pontossága gyakran érhető el. A megadott paraméter-beállításainak megfelelő kombinációja található.

### <a name="number-of-features"></a>Szolgáltatások száma
Bizonyos típusú adatok, szolgáltatások száma lehet nagyon nagy adatpontok száma képest. Ez a helyzet gyakran genetics vagy szöveges adatok. A szolgáltatásokat nagyszámú le néhány tanulási algoritmusok, így képzési unfeasibly hosszú időt is bog. Támogatási vektoros gépek különösen kiválóan alkalmas (lásd alább) ebben az esetben a.

### <a name="special-cases"></a>Bizonyos esetekben
Néhány tanulási algoritmusok ellenőrizze az adatokat, vagy a kívánt eredményeket szerkezete adott feltételezéseket. Ha talál egyet, amely megfelel az igényeinek, azt tudhatja meg több eredményeket, több pontos előrejelzéseket vagy gyorsabb képzési.

| **Algoritmus** | **Pontosság** | **Képzési idő** | **Lineáris** | **Paraméterek** | **Megjegyzések** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Két osztályú osztályozás** | | | | | |
| [logisztikai regresszió](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [döntési erdő](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [döntési Dzsungel](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Alacsony memóriaigény |
| [súlyozott döntési fája](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Nagy memóriaigény |
| [Neurális hálózat](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[További testreszabási lehetőség.](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [átlagolt perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [támogatja a vektoros gép](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Jó nagy szolgáltatás beállítása |
| [helyileg mély támogatási vektoros gép](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Jó nagy szolgáltatás beállítása |
| [Bayes' pontozó gépet](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Több osztály besorolás** | | | | | |
| [logisztikai regresszió](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [döntési erdő](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [döntési Dzsungel](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Alacsony memóriaigény |
| [Neurális hálózat](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[További testreszabási lehetőség.](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [egyik-v-összes](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Tekintse meg a kiválasztott két osztályú módszer tulajdonságait |
| **Regressziós** | | | | | |
| [lineáris](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayes-féle lineáris](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [döntési erdő](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [súlyozott döntési fája](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Nagy memóriaigény |
| [gyors erdő ki osztóérték](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Terjesztési pont előrejelzéseket helyett |
| [Neurális hálózat](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[További testreszabási lehetőség.](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [A POISSON](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Technikailag napló lineáris. Előrejelzésére száma |
| [sorszám](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Előrejelzésére dimenziószáma-rendezés |
| **Anomáliadetektálás** | | | | | |
| [támogatja a vektoros gép](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Különösen hasznos nagy szolgáltatás beállítása |
| [PEM-alapú anomáliadetektálás](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-közép](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |A csoportosítási algoritmus |

**Algoritmus tulajdonságai:**

**●** -kiváló pontosságának, gyors képzési alkalommal és lineáris használatát jeleníti meg

**○** -jeleníti meg a helyes pontosságát és mérsékelt képzési alkalommal

## <a name="algorithm-notes"></a>Algoritmus megjegyzések
### <a name="linear-regression"></a>Lineáris regresszió
Ahogy korábban említettük [lineáris regressziós](https://msdn.microsoft.com/library/azure/dn905978.aspx) vonal (vagy vezérlősík vagy hyperplane) megfelel az adatkészletben. Egy workhorse, egyszerű és gyors, de lehet, hogy bizonyos problémák túlságosan simplistic.
Itt keressen egy [lineáris regressziós oktatóanyag](linear-regression-in-azure.md).

![Adatok lineáris trend][3]

***Adatok lineáris trend***

### <a name="logistic-regression"></a>Logisztikai regresszió
Bár a neve "regressziós" megtévesztően tartalmaz, logisztikai regresszió ténylegesen hatékony eszköz [két osztályú](https://msdn.microsoft.com/library/azure/dn905994.aspx) és [multiclass](https://msdn.microsoft.com/library/azure/dn905853.aspx) besorolás. Gyors és egyszerű. Az, hogy használja s '-alakú görbe egyenes helyett egy természetes méretezési csoportokba adatok megosztásának teszi. Logisztikai regresszió által biztosított lineáris osztály határok, így használatánál, győződjön meg arról lineáris közelítéséről valami is él, az.

![Csak egyetlen szolgáltatás két osztályú adatok logisztikai regresszió][4]

***A két osztályú adatokhoz csak egy szolgáltatás logisztikai regresszió*** *-osztály határa az a pont, ahol logisztikai csak lehető legjobban hasonlítson mindkét osztályok*

### <a name="trees-forests-and-jungles"></a>Jungles, fák és erdők
Erdők döntési ([regressziós](https://msdn.microsoft.com/library/azure/dn905862.aspx), [két osztályú](https://msdn.microsoft.com/library/azure/dn906008.aspx), és [multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx)), jungles döntési ([két osztályú](https://msdn.microsoft.com/library/azure/dn905976.aspx) és [multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx)), és a súlyozott döntési fák ([regressziós](https://msdn.microsoft.com/library/azure/dn905801.aspx) és [két osztályú](https://msdn.microsoft.com/library/azure/dn906025.aspx)) alapulnak döntési fák eligazodást machine learning-fogalom. A döntési fák számos változata létezik, de mindegyiknek azonos – a szolgáltatás terület tovább legtöbbször azonos címkével ellátott területekre. Ezek lehetnek régiók konzisztens kategória vagy állandó érték, attól függően, hogy osztályozási vagy regressziós végzi.

![Döntési fa alterületét adható meg a szolgáltatás][5]

***A döntési fa alterületét szolgáltatás adhatja többé-kevésbé egységes értékek területekre***

Szolgáltatás adhatja önkényesen kis régiók osztható meg, mert is könnyen képzelhető el, elég finom osztani több adatpont régiónként hozzá. Ez az overfitting szélsőséges példát. Ennek elkerülése érdekében számos fák végrehajtott matematikai gondosan össze, hogy a fa nem közötti kapcsolatot. A "döntési erdő" átlaga, amellyel elkerülhető a overfitting fát. Döntési erdők nagy mennyiségű memóriát használhat. Döntési jungles variant érték, amely csökkenti a képzési némileg hosszabb ideig kevesebb memóriával akkor.

Súlyozott döntési fák overfitting hányszor tovább lehet, és hogyan kevés adatpont engedélyezettek minden régióban korlátozásával elkerülése érdekében. Az algoritmus fák, sorozata, amelyek mindegyike megtanulja kiegyensúlyozása érdekében a hiba előtt a fa által hátrahagyott hoz létre. Eredménye egy nagyon pontos tanuló, amely általában nagy mennyiségű memóriát használja. A részletes műszaki leírását, tekintse meg [Friedman tartozó eredeti papír](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Erdő ki osztóérték regressziós gyors](https://msdn.microsoft.com/library/azure/dn913093.aspx) a különleges esetben, ha szeretné tudni nem csak egy régió, hanem a kiosztás quantiles formájában belüli adatok tipikus (közepes) érték a döntési fák mértékben eltérő változata.

### <a name="neural-networks-and-perceptrons"></a>Neurális hálózatokat és perceptrons
Neurális hálózatokat vannak agy által ihletett tanulási algoritmus kiterjedő [multiclass](https://msdn.microsoft.com/library/azure/dn906030.aspx), [két osztályú](https://msdn.microsoft.com/library/azure/dn905947.aspx), és [regressziós](https://msdn.microsoft.com/library/azure/dn905924.aspx) problémákat. Egy végtelen különböző származnak, de a Neurális hálózatokat belül Azure Machine Learning-e minden olyan irányított aciklikus diagramjait formájában. Ez azt jelenti, hogy bemeneti szolgáltatások továbbítja a rendszer előre (soha nem visszafelé haladva) rétegek sorozatát előtt kikapcsolta a kimenetek be. Minden egyes rétegben bemeneti adatokat különböző kombinációkban súlyozott, összesítve, és átadja a következő rétege. Egyszerű számítási Ez a kombinációja képes által magic látszólag ismerje meg a kifinomult osztály határokat és az adatok trendeket, eredményez. Az ilyen jellegű több rétegből hálózatok hajtsa végre a "részletes tanulás" túl nagy reporting műszaki és tudományos üzemanyag.

A nagy teljesítményű nem tartalmaz szabad, azonban. Neurális hálózat betanítása, különösen a szolgáltatások sok nagy adatkészletek hosszú időt vehet igénybe. Is rendelkeznek, mint a legtöbb algoritmusok, ami azt jelenti, hogy a paraméter abszolút bővíti a képzési idő jelentős mértékben több paramétert.
És az adott overachievers kívánó [adja meg a saját hálózati struktúra](http://go.microsoft.com/fwlink/?LinkId=402867), a lehetséges a következők inexhaustible.

![Határok Neurális hálózatokat által ismert][6]
***a határokat, amelyeket a Neurális hálózatokat összetett és szabálytalan lehet.***

A [két osztályú átlagosan perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) skyrocketing képzési idők Neurális hálózatokat válasz. A hálózati struktúra, amely lineáris osztály határokat használja. Majdnem egyszerű mai szabványokkal, de abroncsnyomásmérők erős működik-e hosszú ideje, ezért kicsi ismerje meg gyorsan.

### <a name="svms"></a>SVMs
Támogatási vektoros gépek (SVMs) található, amely elválasztja az osztályok által lehető legszélesebb margó, a határ. A két osztály nem egyértelműen el kell választani, az algoritmus a legjobb határt, a következőkre található. Mivel az Azure Machine Learning ír a [két osztályú SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) ezt a csak egy lineáris végzi. (A SVM beszéd használ egy lineáris kernel.) Ez biztosítja ugyanis e lineáris közelítés, akkor meglehetősen gyorsan futtatható. Ha a valóban helyezi van szolgáltatás-intenzív adatokkal, például szöveget vagy genomikus. Ezekben az esetekben SVMs képesek különböző osztályú gyorsabb és kisebb, mint a legtöbb más algoritmusok mellett memória mennyisége mérsékelt igénylő overfitting.

![Támogatási vektoros gép osztály határ][7]

***A szokásos támogatási vektoros gép osztály határ a lehető legnagyobbra növeli a két osztály elválasztó margó***

Egy másik szoftver a Microsoft Research a [két osztályú helyileg mély SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) nem lineáris változatát, amely megőrzi a sebesség és a memória hatékonyságát lineáris verziója a legtöbb SVM van. Ideális esetekben, ahol a lineáris módszert nem biztosítják a elég pontos válaszokat. A fejlesztők megőrzi azt gyors bontásához, a probléma a álló, lemezcsoport típusú kisebb lineáris SVM problémák. Olvassa el a [teljes körű ismertetését](http://research.microsoft.com/um/people/manik/pubs/Jose13.pdf) hogyan azok lekért ki ez a trükk a leírását.

Egy intelligens lineáris SVMs bővítményének használatával a [egy szintű SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) megrajzolja a határ, amely a teljes adatkészlet szorosan ismerteti. Akkor célszerű közüli. Minden új adatponthoz sokkal adott határán kívül eső szokatlanok elég kell megjegyezni.

### <a name="bayesian-methods"></a>Bayes-féle módszerek
Bayes-féle módszerek rendelkezik, célszerű lenne minőségi: azok overfitting elkerülése érdekében. Így tesznek azáltal, hogy néhány feltételezéseket előzetesen válasz valószínűleg terjesztési. Az ezt a módszert használja egy másik byproduct arról, hogy nagyon kevés paramétert. Az Azure Machine Learning rendelkezik mindkét Bayes algoritmus mindkét besorolási ([két osztályú Bayes pontozó gépet](https://msdn.microsoft.com/library/azure/dn905930.aspx)) és regressziós ([bayes-féle lineáris regressziós](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Vegye figyelembe, hogy ezek azt feltételezik, hogy az adatok felosztásához vagy egyenes felel.

Egy korábbi megjegyzés, a Bayes' pont gépek készültek, a Microsoft Research. Néhány kivételesen gyönyörű elméleti munkahelyi hátulról rendelkeznek. Az érdekelt student van irányítva a [JMLR eredeti cikk](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) és egy [által Chris Bishop osztályon blog](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Speciális algoritmusok
Ha a meghatározott célja esetleg számolva. Az Azure Machine Learning gyűjteményen belül van, amely a szolgáltatástelepítések algoritmusok:

- Előrejelzés rangsorolja ([sorszám regressziós](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- előrejelzés száma ([Poisson regressziós](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- anomáliadetektálás (egy alapján [fő összetevőit elemzés](https://msdn.microsoft.com/library/azure/dn913102.aspx) és alapján egy [támogatási vektoros gép](https://msdn.microsoft.com/library/azure/dn913103.aspx)s)
- Fürtszolgáltatás ([K-közép](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![PEM-alapú anomáliadetektálás][8]

***PEM-alapú anomáliadetektálás*** *-túlnyomó része az adatok beleesik stereotypical terjesztési; el jelentősen eltérő, hogy a terjesztési pontok az olyan gyanús*

![Adatkészlet csoportosítja K-közép használatával][9]

***Adatkészlet szerint vannak csoportosítva K-közép-öt fürtök***

Is van a ruhaegyüttes [egyik-v-összes multiclass osztályozó](https://msdn.microsoft.com/library/azure/dn905887.aspx), amely az N-osztályú osztályozási problémához bontja N-1 két osztályú osztályozás problémákat. A pontosság képzési időt és lineáris tulajdonságok a használt két osztályú osztályozó határozza meg.

![Két osztályú osztályozó alkot három-osztály besorolás][10]

***Egy kulcspár két osztályú osztályozó egyesítése egy három-osztály osztályozó kialakításához.***

Az Azure Machine Learning is egy hatékony gépi tanulás keretrendszere címe alatt [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW szakmai kategorizálási itt, mert besorolás és a regressziós problémák megtudhatja, és akkor is megtudhatja, részben címke nélküli adatokból. A tanulási algoritmusok, adatvesztés funkciók és optimalizálási algoritmusok számos használatára konfigurálhatja. Kialakítása az alapoktól mentése hatékony, párhuzamos és nagyon gyorsan kell lennie. Kevés a nyilvánvaló munkát ridiculously nagy szolgáltatáskészletek kezeli.
Megkezdődött, és a Microsoft Research által saját John Langford által vezetett, VW bejegyzés egy képletet egy készlet car algoritmusok mezőben. Nem minden probléma megfelelő VW, de ha saját, valószínűleg érdemes eléri a maximumot, a tanulási görbére illesztőjén. Azt is rendelkezésre áll, mint a [önálló nyílt forráskódú kód](https://github.com/JohnLangford/vowpal_wabbit) több nyelven is.

## <a name="more-help-with-algorithms"></a>További segítséget itt találhat algoritmusok
* Letölthető infographic, amely leírja a algoritmusok, és a példákat mutat be, lásd: [letölthető Infographic: gépi tanulási algoritmus példákkal alapjai](basics-infographic-with-algorithm-examples.md).
* Kategória az Azure Machine Learning Studióban elérhető összes gépi tanulási algoritmusok listájáért lásd: [inicializálása modell] [ initialize-model] a Machine Learning Studio algoritmus és a modul segítségével.
* Teljes betűrendben listáját az algoritmusok és a modulok az Azure Machine Learning Studióban, tekintse meg a [a Machine Learning Studio moduljai A-Z listában] [ a-z-list] a Machine Learning Studio algoritmus és a modul segítségével.
* Töltse le és kinyomtatható egy Azure Machine Learning Studio funkcióiról, tekintse meg [Azure Machine Learning Studio képességeit áttekintő diagram](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
