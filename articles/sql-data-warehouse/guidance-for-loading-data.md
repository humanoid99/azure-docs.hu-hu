---
title: "Az adatbetöltés ajánlott eljárásai – Azure SQL Data Warehouse | Microsoft Docs"
description: "Javaslatok az adatok betöltéséhez és ELT végrehajtásához az Azure SQL Data Warehouse segítségével"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 80974f7660696887783e97b674e2d9921fe2feac
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="best-practices-for-loading-data-into-azure-sql-data-warehouse"></a>Az adatok Azure SQL Data Warehouse-ba való betöltésének ajánlott eljárásai
Javaslatok és teljesítményoptimalizálás az adatok betöltéséhez az Azure SQL Data Warehouse-ba 

- További információ a PolyBase-ről és egy kinyerési, betöltési és átalakítási (ETL) folyamat megtervezéséről: [ETL tervezése SQL Data Warehouse-hoz](design-elt-data-loading.md).
- Betöltési oktatóanyag: [Adatok betöltése az Azure Blob Storage-ból az Azure SQL Data Warehouse-ba a PolyBase használatával](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="preparing-data-in-azure-storage"></a>Adatok előkészítése az Azure Storage-ban
A késés minimalizálása érdekében helyezze egymás mellé a tárolási réteget és az adattárházat.

Az adatok ORC fájlformátumba való exportálásakor Java memóriahiány-hibák jelentkezhetnek, ha a szövegoszlopok túl nagyok. Ezt a korlátozást úgy küszöbölheti ki, ha az oszlopok csak egy részhalmazát exportálja.

A PolyBase nem képes 1 000 000 bájtnál több adatot tartalmazó sorok betöltésére. Az Azure Blob Storage-ba vagy az Azure Data Lake Store-ba helyezett szöveges fájlok nem tartalmazhatnak 1 000 000 bájtnál több adatot. Ez a bájtkorlátozás a táblasémától függetlenül érvényes.

Minden fájlformátum eltérő teljesítményjellemzővel rendelkezik. A leggyorsabb betöltés érdekében használjon tömörített, tagolt szövegfájlokat. Az UTF-8 és UTF-16 formátum teljesítménye között minimális a különbség.

A nagy tömörített fájlokat ossza fel kisebb tömörített fájlokra.

## <a name="running-loads-with-enough-compute"></a>Betöltések futtatása elegendő számítási teljesítménnyel

A leggyorsabb betöltési sebesség érdekében egyszerre egy betöltési feladatot futtasson. Ha ez nem lehetséges, egyszerre a lehető legkevesebb betöltést futtassa. Ha nagy betöltési feladatra számít, érdemes felskálázni az adattárházat a betöltés előtt.

A betöltések megfelelő számítási erőforrásokkal való futtatásához hozzon létre betöltések futtatására kijelölt betöltést végző felhasználókat. Az egyes betöltést végző felhasználókat rendelje hozzá egy adott erőforrásosztályhoz. Betöltés futtatásához jelentkezzen be az egyik betöltést végző felhasználóként, majd futtassa a betöltést. A betöltés a felhasználó erőforrásosztályával fut.  Ez a módszer egyszerűbb, mint a felhasználó erőforrásosztályának módosításával próbálkozni, hogy az megfeleljen az aktuális erőforrásosztály-igénynek.

### <a name="example-of-creating-a-loading-user"></a>Példa egy betöltést végző felhasználó létrehozására
Ez a példa létrehoz egy betöltést végző felhasználót a staticrc20 erőforrásosztályhoz. Ennek első lépése a **főkiszolgálóhoz való csatlakozás** és egy bejelentkezés létrehozása.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```
Kapcsolódjon az adattárházhoz, majd hozzon létre egy felhasználót. A következő kód azt feltételezi, hogy a mySampleDataWarehouse nevű adatbázishoz kapcsolódik. A kód azt mutatja, hogyan lehet létrehozni egy LoaderRC20 nevű felhasználót, illetve hogyan lehet vezérlői jogosultságot adni számára egy adatbázishoz. Ezután a kód felveszi a felhasználót a staticrc20 adatbázis-szerepkör tagjaként.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```
Ha a staticRC20 erőforrásosztályokhoz tartozó erőforrással szeretne betöltést futtatni, egyszerűen jelentkezzen be LoaderRC20 felhasználóként, és futtassa a betöltést.

A betöltéseket inkább statikus, mint dinamikus erőforrásosztályokkal futtassa. A statikus erőforrásosztályok használata garantálja az azonos erőforrásokat a [szolgáltatási szinttől](performance-tiers.md#service-levels) függetlenül. Ha dinamikus erőforrásosztályt használ, az erőforrások a szolgáltatásszinttől függően változhatnak. Dinamikus osztályok esetében egy alacsonyabb szolgáltatási szint azt jelenti, hogy feltehetően nagyobb erőforrásosztályt kell használnia a betöltést végző felhasználóhoz.

## <a name="allowing-multiple-users-to-load"></a>Betöltés engedélyezése több felhasználó számára

Gyakran van szükség több olyan felhasználóra, akik adatokat töltenek egy adattárházba. A [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] paranccsal való betöltéshez CONTROL engedélyekre van szükség az adatbázishoz.  A CONTROL engedély az összes séma vezérlését biztosítja. Előfordulhat, hogy nem szeretné, hogy minden betöltést végző felhasználó vezérelési jogot kapjon az összes sémához. Az engedélyek korlátozására használja a DENY CONTROL utasítást.

Vegyünk például két adatbázissémát: schema_A az A részleghez, és schema_B a B részleghez. Legyen user_A és user_B két PolyBase-betöltést végző adatbázis-felhasználó az A, illetve a B részlegen. Mindkét felhasználó kapott adatbázisszintű CONTROL jogosultságokat. Az A és B séma létrehozói zárolják a sémáikat a DENY utasítás segítségével:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

User_A és user_B számára mostantól nem lesz hozzáférhető a másik részleg sémája.


## <a name="loading-to-a-staging-table"></a>Betöltés előkészítési táblába

Az adattárház táblájába való adatáthelyezés leggyorsabb betöltési sebességének eléréséhez töltse be az adatokat egy előkészítési táblába.  Határozza meg az előkészítési táblát halomként, és használjon ciklikus időszeletelést a terjesztési beállításhoz. 

Vegye figyelembe, hogy a betöltés általában két lépésből álló folyamat, amely során először az előkészítési táblába tölti be, majd beszúrja az adatokat egy éles adattárháztáblába. Ha az éles tábla kivonatoló terjesztést használ, a betöltés és a beszúrás teljes ideje gyorsabb lehet, ha meghatároz egy előkészítési táblát a kivonatoló terjesztéssel. Az előkészítési táblába való betöltés több időt vesz igénybe, de a sorok az éles táblába való beszúrásának második lépése nem jár a disztribúciók közötti adatmozgatással.

## <a name="loading-to-a-columnstore-index"></a>Betöltés oszlopcentrikus indexbe

Az oszlopcentrikus indexek sok memóriát igényelnek az adatok jó minőségű sorcsoportokba való tömörítéséhez. A legjobb tömörítési és indexelési hatékonyság érdekében az oszlopcentrikus indexnek a maximális 1 048 576 sort kell tömörítenie az egyes sorcsoportokba. Ha korlátozott a rendelkezésre álló memória mennyisége, előfordulhat, hogy az oszlopcentrikus index nem éri el a maximális tömörítési sebességet. Ez hatással van a lekérdezés teljesítményére. A témakör részletes bemutatása: [Oszloptár memóriájának optimalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

- Annak érdekben, hogy elég memória álljon a betöltést végző felhasználók rendelkezésére a maximális tömörítési sebesség eléréséhez, használjon olyan betöltést végző felhasználókat, akik közepes vagy nagy erőforrásosztály tagjai. 
- Töltsön be elég sort az új sorcsoportok teljes feltöltéséhez. Kötegelt betöltés során minden 1 048 576. sor teljes sorcsoportként közvetlenül az oszloptárba van tömörítve. A 102 400 sornál kisebb betöltések a deltatárba küldik a sorokat, ahol a sorok B-fában vannak tárolva. Ha kevesebb sort tölt be, előfordulhat, hogy mind a deltatárba kerül, és a rendszer nem tömöríti azokat azonnal oszloptár formátumba.


## <a name="handling-loading-failures"></a>Betöltési hibák kezelése

Külső táblát használó betöltés meghiúsulhat a következő hibával: *„A lekérdezés megszakadt – a rendszer elérte a felső visszautasítási küszöbértéket külső forrásból való beolvasás során”*. Ez az üzenet azt jelzi, hogy a külső adatok szabálytalan rekordokat tartalmaznak. Az adatrekord akkor számít „szabálytalannak”, ha az oszlopok adattípusai és száma nem felel meg a külső tábla definícióinak, vagy ha az adatok nem felelnek meg a megadott külső fájlformátumnak. 

A szabálytalan rekordok kijavításához győződjön meg arról, hogy a külső tábla- és fájlformátum-definíciók helyesek, és hogy a külső adatok megfelelnek ezeknek a definícióknak. Amennyiben a külső adatrekordok egy részhalmaza szabálytalan, dönthet úgy, hogy nem tart igényt ezekre a rekordokra a lekérdezéseihez. Ehhez használja a CREATE EXTERNAL TABLE visszautasítási lehetőségeit.

## <a name="inserting-data-into-a-production-table"></a>Adatok beszúrása az éles táblába
A kis táblák [INSERT utasítással](/sql/t-sql/statements/insert-transact-sql.md) végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is megfelelő lehet, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Az egyszeres beszúrásoknál azonban hatékonyabb egy kötegelt betöltés végrehajtása. 

Ha több ezer egyszeres beszúrást hajt végre egy nap, kötegelje a beszúrásokat, hogy kötegelve tölthesse be őket.  Fejlesszen folyamatokat, amelyek az egyszeres beszúrásokat egy fájlhoz fűzik, majd hozzon létre egy másik folyamatot, amely időszakosan betölti a fájlt.

## <a name="creating-statistics-after-the-load"></a>Statisztika létrehozása a betöltés után

A lekérdezési teljesítmény javításához fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, illetve az adatok minden lényeges módosítását követően.  A statisztika részletes ismertetése: [Statistics][Statistics]. A következő példa a Customer_Speed tábla öt oszlopára vonatkozó statisztikákat hoz létre.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Tárkulcsok rotálása
Biztonsági szempontból érdemes rendszeresen módosítani a Blob Storage hozzáférési kulcsát. A Blob Storage-fiókhoz két tárkulcs tartozik, amely lehetővé teszi a kulcsok közötti váltást.

Az Azure Storage-fiók kulcsainak rotálása:

1. Hozzon létre egy második adatbázishoz kötődő hitelesítőadat-egységet másodlagos tárelérési kulccsal.
2. Hozzon létre egy második külső adatforrást az új hitelesítőadatok alapján.
3. Törölje, majd hozza létre a külső táblá(ka)t, hogy az új külső adatforrásokra mutassanak. 

A külső táblák új adatforrásba való migrálása után hajtsa végre a következő karbantartási feladatokat:

1. Vesse el az első külső adatforrást.
2. Törölje az elsődleges tárelérési kulcs alapján készült, első adatbázishoz kötődő hitelesítő adatokat.
3. Jelentkezzen be az Azure-ba, és hozza létre újra az elsődleges hozzáférési kulcsot, hogy készen álljon a következő rotálásra.


## <a name="next-steps"></a>További lépések
Az adatbetöltések monitorozása: [A számítási feladat monitorozása DMV-kkel](sql-data-warehouse-manage-monitor.md).



