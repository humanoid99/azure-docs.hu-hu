---
title: "Azure Portal: SQL-adatbázis létrehozása | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával. Megtanulhatja azt is, hogyan kérdezhet le egy SQL-adatbázist az Azure Portal használatával."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: be5839e04fae457b889db11dffe56f31afe723a5
ms.lasthandoff: 03/28/2017


---
# <a name="create-an-azure-sql-database-in-the-azure-portal"></a>Azure SQL Database létrehozása az Azure Portalon

Ez a rövid útmutató végigvezeti azon, hogyan hozhat létre SQL Database adatbázist az Azure-ban.  Az Azure SQL Database egy adatbázis-szolgáltatási ajánlat, amellyel magas rendelkezésre állású SQL Server-adatbázisokat futtathat és méretezhet a felhőben.  Ez a rövid útmutató bemutatja, hogyan teheti meg az új SQL Database adatbázisok létrehozásának első lépéseit az Azure Portalon.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

Az Azure SQL-adatbázis [számítási és tárolási erőforrások](sql-database-service-tiers.md) egy meghatározott készletével együtt jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) jön létre. 

Kövesse az alábbi lépéseket az Adventure Works LT mintaadatokat tartalmazó SQL-adatbázis létrehozásához. 

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **SQL Database** lehetőséget.

    ![adatbázis létrehozása-1](./media/sql-database-get-started/create-database-1.png)

3. Töltse ki az SQL Database űrlapját a következő információkkal az előző képen látható módon: 
   - Adatbázis neve: használja a **mySampleDatabase** nevet
   - Erőforráscsoport: használja a **myResourceGroup** nevet
   - Forrás: Válassza a **Minta (AdventureWorksLT)** lehetőséget

4. Kattintson a **Kiszolgáló** lehetőségre új kiszolgáló létrehozásához és konfigurálásához az új adatbázis számára. Töltse ki az **új kiszolgáló űrlapját**: adjon meg egy globálisan egyedi kiszolgálónevet, a kiszolgáló rendszergazdai bejelentkezési nevét, majd adjon meg egy tetszőleges jelszót. 

    ![adatbázis-kiszolgáló létrehozása](./media/sql-database-get-started/create-database-server.png)
5. Kattintson a **Kiválasztás** gombra.

6. Kattintson a **Tarifacsomag** parancsra az új adatbázis szolgáltatás- és teljesítményszintjének megadásához. Ehhez a rövid útmutatóhoz válassza a **20 DTU** egységet, illetve a **250** GB tárhelyet

    ![adatbázis létrehozása-s1](./media/sql-database-get-started/create-database-s1.png)

7. Kattintson az **Alkalmaz** gombra.  

8. Kattintson a **Létrehozás** elemre az adatbázis létrehozásához. Az üzembe helyezés eltarthat néhány percig. 

9. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

    ![értesítés](./media/sql-database-get-started/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén, amely megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha létrehoz tűzfalszabályt, hogy adott IP-címek számára megnyissa a tűzfalat. A következő lépésekkel hozzon létre egy [kiszolgálószintű SQL Database-tűzfalszabályt](sql-database-firewall-configure.md) az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatokat csak az Ön IP-címéhez az SQL Database-tűzfalon keresztül. 

1. Az üzembe helyezés befejezése után kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson az új **mySampleDatabase** adatbázisra az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például **mynewserver20170327.database.windows.net**), valamint a további konfigurálható beállítások.

      ![kiszolgálói tűzfalszabály](./media/sql-database-get-started/server-firewall-rule.png) 

2. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron az előző képen látható módon. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

3. Kattintson az **Ügyfél IP-címének hozzáadása** elemre az eszköztárban, majd kattintson a **Mentés** gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez.

      ![kiszolgálótűzfal-szabály beállítása](./media/sql-database-get-started/server-firewall-rule-set.png) 

4. Kattintson az **OK** gombra, majd az **X**-re a **Tűzfalbeállítások** oldal bezárásához.

Mostantól csatlakozhat az adatbázishoz és annak kiszolgálójához az SQL Server Management Studio vagy más választott eszköz használatával.

## <a name="query-the-sql-database"></a>Az SQL-adatbázis lekérdezése

Amikor létrehoztuk az SQL-adatbázist, az **AdventureWorksLT** mintaadatbázissal töltöttük fel (ez a rövid útmutató során korábban, a felhasználói felület létrehozásával kapcsolatban kiválasztott egyik lehetőség volt). Most az Azure Portalon lévő beépített lekérdezési eszközzel kérdezzük le az adatokat. 

1. Az adatbázishoz tartozó SQL Database oldalon kattintson az **Eszközök** elemre az eszköztárban. Megnyílik az **Eszközök** lap.

     ![eszközök menü](./media/sql-database-get-started/tools-menu.png) 

2. Kattintson a **Lekérdezésszerkesztő (előzetes verzió)** lehetőségre, kattintson az **Előzetes verziójú szolgáltatás feltételei** jelölőnégyzetre, majd az **OK** gombra. Megnyílik a Lekérdezésszerkesztő lapja.

3. Kattintson a **Bejelentkezés** elemre, majd amikor a rendszer kéri, válassza ki az **SQL Server-hitelesítés** lehetőséget, és adja meg a kiszolgáló korábban létrehozott rendszergazdai bejelentkezési nevét és jelszavát.

    ![bejelentkezés](./media/sql-database-get-started/login.png) 

4. A bejelentkezéshez kattintson az **OK** gombra.

5. A hitelesítés után írja be a következő lekérdezést a lekérdezésszerkesztő panelén.

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Kattintson a **Futtatás** gombra, majd tekintse át a lekérdezési eredményeket az **Eredmények** ablaktáblán.

    ![lekérdezésszerkesztő: eredmények](./media/sql-database-get-started/query-editor-results.png)

7. A **Lekérdezésszerkesztő** lap bezárásához kattintson az **X**-re, majd kattintson ismét az **X**-re az **Eszközök** lap bezárásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épít. Ha azt tervezi, hogy az ezt rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

- Az SQL Server Management Studióval történő csatlakozáshoz és lekérdezéshez lásd [az SSMS segítségével történő csatlakozással és lekérdezéssel](sql-database-connect-query-ssms.md) foglalkozó témakört.
- Ha a Visual Studióval szeretne kapcsolódni, lásd a [Visual Studióval végzett csatlakozásról és lekérdezésről](sql-database-connect-query.md) szóló témakört.
- Az SQL Database műszaki áttekintését az [SQL Database szolgáltatást ismertető](sql-database-technical-overview.md) témakörben találja.

