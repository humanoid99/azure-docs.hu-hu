---
title: "Adatok elemzése Azure Machine Learning segítségével | Microsoft Docs"
description: "Az Azure Machine Learning segítségével létrehozhat egy prediktív gépi tanulási modellt, amely az Azure SQL Data Warehouse-ban tárolt adatokon alapul."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 95635460-150f-4a50-be9c-5ddc5797f8a9
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 03/02/2017
ms.author: kevin
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: a5befbf1dc1862b3b0803eb4940341d246ec036c
ms.lasthandoff: 03/14/2017


---
# <a name="analyze-data-with-azure-machine-learning"></a>Adatok elemzése Azure Machine Learning segítségével
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Ebben az oktatóanyagban az Azure Machine Learning segítségével hozunk létre egy prediktív gépi tanulási modellt, amely az Azure SQL Data Warehouse-ban tárolt adatokon alapul. Egy célzott marketingkampányt hozunk létre az Adventure Works kerékpáráruház számára, annak előre jelzése alapján, hogy egy vásárló valószínűleg megvesz-e egy kerékpárt vagy nem.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Integrating-Azure-Machine-Learning-with-Azure-SQL-Data-Warehouse/player]
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Egy SQL Data Warehouse, előre feltöltve az AdventureWorksDW mintaadataival. Ennek létrehozásához olvassa el az [SQL Data Warehouse létrehozása][Create a SQL Data Warehouse] című cikket, és válassza a mintaadatok betöltését. Ha már rendelkezik egy adatraktárral, de nincsenek mintaadatai, [a mintaadatokat manuálisan is betöltheti][load sample data manually].

## <a name="1-get-the-data"></a>1. Az adatok lekérése
Az adatok az AdventureWorksDW adatbázis dbo.vTargetMail nézetében találhatók. Az adatok olvasása:

1. Jelentkezzen be az [Azure Machine Learning Studio][Azure Machine Learning studio] szolgáltatásba, majd kattintson a Saját kísérletek elemre.
2. Kattintson a **+ ÚJ** opcióra, és válassza ki az **Üres kísérlet** opciót.
3. Adjon nevet a Célzott marketing kísérletnek.
4. Húzza az **Olvasó** modult a modulpanelről a vászonra.
5. Adja meg az SQL Data Warehouse adatbázis adatait a Tulajdonságok panelen.
6. Adja meg az adatbázishoz a **lekérdezést** az Önt érdeklő adatok olvasásához.

```sql
SELECT [CustomerKey]
  ,[GeographyKey]
  ,[CustomerAlternateKey]
  ,[MaritalStatus]
  ,[Gender]
  ,cast ([YearlyIncome] as int) as SalaryYear
  ,[TotalChildren]
  ,[NumberChildrenAtHome]
  ,[EnglishEducation]
  ,[EnglishOccupation]
  ,[HouseOwnerFlag]
  ,[NumberCarsOwned]
  ,[CommuteDistance]
  ,[Region]
  ,[Age]
  ,[BikeBuyer]
FROM [dbo].[vTargetMail]
```

Futtassa a kísérletet a kísérlet vászon alatt található**Futtatás** opcióra kattintva.
![Kísérlet futtatása][1]

A kísérlet futtatásának sikeres befejezése után kattintson az Olvasó modul alján található kimeneti portra, és válassza a **Képi megjelenítés** opciót az importált adatok megtekintéséhez.
![Importált adatok megtekintése][3]

## <a name="2-clean-the-data"></a>2. Az adatok megtisztítása
Az adatok megtisztításához el kell vetni néhány, a modell szempontjából érdektelen oszlopot. Ehhez tegye a következőket:

1. Húzza a **Projektoszlopok** modult a vászonra.
2. Kattintson a Tulajdonságok panelen az **Oszlopválasztás indítása** opcióra az elvetni kívánt oszlopok megadásához.
   ![Projektoszlopok][4]
3. Két oszlop kizárása: CustomerAlternateKey és GeographyKey.
   ![Felesleges oszlopok eltávolítása][5]

## <a name="3-build-the-model"></a>3. A modell létrehozása
80:20 arányban osztjuk fel az adatokat: az adatok 80 % -át a gépi tanulási modell tanításához, 20 %-át a modell teszteléséhez használjuk. A bináris osztályozási problémához "Két osztályú" algoritmusokat használunk.

1. Húzza a **Felosztás** modult a vászonra.
2. A Tulajdonságok panelen az első kimeneti adatkészletnél a Sorok felosztása opciónál adja meg a 0,8 értéket.
   ![Adatok felosztása tanítási és tesztelési adatkészletre][6]
3. Húzza a **Két osztályú súlyozott döntési fa** modult a vászonra.
4. Húzza a **Tanítási modell** modult a vászonra, és adja meg a bemeneteket. Majd kattintson a Tulajdonságok panelen az **Oszlopválasztás indítása** opcióra.
   * Első bemenet: gépi tanulási algoritmus.
   * Második bemenet: adatok az algoritmus tanításához.
     ![Csatlakozás a Tanítási modell modulhoz][7]
5. Válassza ki a **BikeBuyer** oszlopot előrejelzési oszlopként.
   ![Előrejelzési oszlop kiválasztása][8]

## <a name="4-score-the-model"></a>4. A modell pontozása
Most teszteljük, hogyan kezeli a modell a tesztadatokat. Az általunk választott algoritmust összehasonlítjuk egy másik algoritmussal, hogy megtudjuk, melyik teljesít jobban.

1. Húzza a **Pontszám modell** modult a vászonra.
    Első bemenet: tanított modell Második bemenet: tesztadatok ![Pontszámok számolása][9]
2. Húzza a **két osztályú Bayes pontozó gépet** a kísérlet vászonra. Összehasonlítjuk ennek az algoritmusnak a teljesítményét a Két osztályú súlyozott döntési fa teljesítményével.
3. Másolja és illessze be a vászonra a Tanítási és Pontszám modelleket.
4. Húzza a **Modell kiértékelése ** modult a vászonra a két algoritmus összehasonlításához.
5. **Futtassa** a kísérletet.
   ![Kísérlet futtatása][10]
6. Kattintson a Modell kiértékelése modul alsó részén található kimeneti portra, majd kattintson a Képi megjelenítés opcióra.
   ![Kiértékelés eredményeinek képi megjelenítése][11]

Az eredményként megjelenő metrikák: ROC-görbe, pontosság-visszahívási diagram és emelkedő görbe. A metrikák azt mutatják, hogy az első modell jobban teljesített, mint a második. Az első modell előrejelzésének megtekintéséhez kattintson a Pontszám modell kimeneti portjára, majd a Képi megjelenítés opcióra.
![Pontszám modell eredményeinek képi megjelenítése][12]

Látni fogja, hogy a tesztelési adatkészletnél megjelent két új oszlop.

* Pontozott valószínűség: annak valószínűsége, hogy az ügyfél kerékpárvásárló.
* Pontozott címkék: a modell által elvégzett osztályozás – kerékpárvásárló (1) vagy sem (0). A címkézés valószínűségi küszöbértéke 50 százalékra van beállítva és módosítható.

A Kerékpárvásárló (tényleges) és a Pontozott címkék (előrejelzés) oszlopok összehasonlításával láthatja, milyen jól teljesített a modell. Következő lépésként alkalmazhatja ezt a modellt új ügyfelekre vonatkozó előrejelzésekhez, és közzéteheti webszolgáltatásként, vagy visszajelezheti az eredményeket az SQL Data Warehouse számára.

## <a name="next-steps"></a>Következő lépések
A prediktív gépi tanulási modellek létrehozásával kapcsolatos további tudnivalókért olvassa el [Az Azure Machine Learning bemutatása][Introduction to Machine Learning on Azure] című részt.

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img1_reader.png
[2]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img2_visualize.png
[3]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img3_readerdata.png
[4]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img4_projectcolumns.png
[5]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img5_columnselector.png
[6]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img6_split.png
[7]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img7_train.png
[8]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img8_traincolumnselector.png
[9]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img9_score.png
[10]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img10_evaluate.png
[11]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img11_evalresults.png
[12]: media/sql-data-warehouse-get-started-analyze-with-azure-machine-learning/img12_scoreresults.png


<!--Article references-->
[Azure Machine Learning studio]:https://studio.azureml.net/
[Introduction to Machine Learning on Azure]:https://azure.microsoft.com/documentation/articles/machine-learning-what-is-machine-learning/
[load sample data manually]: sql-data-warehouse-load-sample-databases.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

