---
title: "Python- és PostgreSQL-webalkalmazás létrehozása az Azure-ban | Microsoft Docs"
description: "Megtudhatja, hogyan állíthat üzembe egy PostgreSQL-adatbáziskapcsolattal rendelkező Python-alkalmazást az Azure-ban."
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 87ed4015e06e0a05e628e8e356b835b9b886eb5c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="build-a-python-and-postgresql-web-app-in-azure"></a>Python- és PostgreSQL-webalkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _Linuxon_ futó App Service-ben való üzembe helyezéssel kapcsolatban lásd a [Docker Python- és PostgreSQL-webalkalmazás az Azure-ban történő létrehozását](./containers/tutorial-docker-python-postgresql-app.md) ismertető cikket.
>

Az [Azure App Service](app-service-web-overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy alapszintű Python-webalkalmazást az Azure-ban. Az alkalmazást egy PostgreSQL-adatbázishoz fogjuk csatlakoztatni. Az oktatóanyag eredménye egy, az App Service-ben futó Python Flask-alkalmazás lesz.

![Python Flask-alkalmazás a Linux App Service-ben](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * PostgreSQL-adatbázis létrehozása az Azure-ban
> * Python-alkalmazás csatlakoztatása a MySQL-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Az alkalmazás kezelése az Azure Portalon

Az oktatóanyag lépései macOS rendszerre vonatkoznak. Linux és Windows rendszeren a legtöbb esetben ugyanezek az utasítások érvényesek, az oktatóanyag azonban nem tér ki az eltérésekkel kapcsolatos részletekre.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
1. [Telepítse a Pythont](https://www.python.org/downloads/)
1. [A PostgreSQL telepítése és futtatása](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>A helyi PostgreSQL-telepítés tesztelése és egy adatbázis létrehozása

A helyi PostgreSQL-kiszolgálóhoz való csatlakozáshoz nyissa meg a terminálablakot, és futtassa a `psql` parancsot.

```bash
sudo -u postgres psql
```

Ha a kapcsolat létrejött, a PostgreSQL-adatbázis fut. Ha nem, mindenképp a [Letöltések – PostgreSQL központi kiadás](https://www.postgresql.org/download/) szakaszban ismertetett lépéseket követve indítsa el a helyi PostgreSQL-adatbázist.

Hozzon létre egy adatbázist *eventregistration* néven, majd hozzon létre egy adatbázis-felhasználót *manager* néven és a *supersecretpass* jelszóval.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>Helyi Python Flask-alkalmazás létrehozása

Ebben a lépésben beállítjuk a helyi Python Flask-projektet.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

Nyissa meg a terminálablakot, és a `CD` paranccsal hozzon létre egy munkakönyvtárat.

Az alábbi parancsok futtatásával klónozza a mintatárházat, majd álljon vissza a kezdeti alkalmazás véglegesítésére (a `modelChange` előtt).

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

A mintaadattár tartalmaz egy [Flask](http://flask.pocoo.org/)-alkalmazást. 

### <a name="run-the-application"></a>Az alkalmazás futtatása

Telepítse a szükséges csomagokat, és indítsa el az alkalmazást.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Egy böngészőben nyissa meg a `http://localhost:5000` oldalt. Kattintson a **Register!** (Regisztrálás) gombra, és hozzon létre egy tesztfelhasználót.

![Helyileg futó Python Flask-alkalmazás](./media/app-service-web-tutorial-python-postgresql/local-app.png)

A Flask-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. A felhasználó sikeres regisztrálása esetén az alkalmazás a helyi PostgreSQL-adatbázisba írja az adatokat.

Ha bármikor le szeretné állítani a Flask-kiszolgálót, nyomja le a Ctrl+C billentyűparancsot a terminálban. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>PostgreSQL létrehozása az Azure-ban

Ebben a lépésben egy PostgreSQL-adatbázist hozunk létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>PostgreSQL-kiszolgáló létrehozása

A PostgreSQL-kiszolgálót az [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) paranccsal hozhatja létre.

Az alábbi parancsban írjon egy egyedi kiszolgálónevet a *\<postgresql_name>*, valamint egy felhasználónevet az *\<admin_username>* helyőrző helyére. A kiszolgálónév a postgreSQL-végpont (`https://<postgresql_name>.postgres.database.azure.com`) részét képezi majd, így egyedi kiszolgálónévnek kell lennie a teljes Azure-ban. A felhasználónév a kezdeti adatbázis-rendszergazdai felhasználói fiók neve lesz. A rendszer megkéri, hogy adjon meg egy jelszót ehhez a felhasználóhoz.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

Az Azure Database for PostgreSQL-kiszolgáló létrehozását követően az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="configure-server-firewall"></a>Kiszolgáló tűzfalának konfigurálása

A következő Azure CLI-parancs futtatásával engedélyezze az adatbázishoz való hozzáférést minden IP-címről.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=255.255.255.255 --name AllowAllIPs
```

Az Azure CLI az alábbi példához hasonló kimenettel igazolja vissza a tűzfalszabály létrehozását:

```json
{
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

### <a name="create-a-production-database-and-user"></a>Éles adatbázis és felhasználó létrehozása

Hozzon létre egy adatbázis-felhasználót egyetlen adatbázishoz való hozzáféréssel. Ezekkel a hitelesítő adatokkal elkerülheti, hogy az alkalmazás teljes hozzáférést kapjon a kiszolgálóhoz.

Csatlakozzon az adatbázishoz (a rendszer kérni fogja a rendszergazdai jelszót).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Hozza létre az adatbázist és a felhasználót a PostgreSQL parancssori felületén.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

A PostgreSQL-ügyfél bezárásához írja be a `\q` parancsot.

### <a name="test-app-locally-with-azure-postgresql"></a>Az alkalmazás helyi tesztelése az Azure PostgreSQL segítségével

Ha visszalép a klónozott GitHub-adattár *app* mappájába, az adatbázis környezeti változóinak frissítésével futtathatja a Python Flask-alkalmazást.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Nyissa meg egy böngészőben a http://localhost:5000 címet. Kattintson a **Register!** (Regisztrálás) gombra, és hozzon létre egy tesztregisztrációt. Most az Azure-ban lévő adatbázisba írunk adatokat.

![Helyileg futó Python Flask-alkalmazás](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Üzembe helyezés az Azure-ban

Ebben a lépésben üzembe helyezi a PostgreSQL-hez csatlakoztatott Python-alkalmazást az Azure App Service-ben.

A Git-adattár már tartalmazza a következő, a Flask-webalkalmazás App Service-ben való futtatásához szükséges fájlokat:

- `.deployment`: ez határozza meg a futtatni kívánt egyéni üzembehelyezési szkriptet.
- `deploy.cmd`: az üzembehelyezési szkript. Itt fut a `pip install`.
- `web.config`: ez adja meg az IIS szolgáltatásban, egy `httpPlatformHandler` elemben futtatni kívánt belépésipont-szkriptet.
- `run_waitress_server.py`: a belépési pont szkriptje. Elindítja a Flask-webalkalmazást egy [`waitress`](https://docs.pylonsproject.org/projects/waitress)-kiszolgálón.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Telepítse a Pythont

Ebben a lépésben telepíti a Python 3.6.2-es verzióját [webhelybővítményekkel](https://www.siteextensions.net/packages?q=Tags%3A%22python%22) együtt az App Service-ben. A REST-végponton való hitelesítéshez az [üzembe helyező felhasználó konfigurálását](#configure-a-deployment-user) ismertető szakaszban konfigurált hitelesítő adatokat fogja használni.

A Cloud Shellben a Python 3.6.2-es verzió csomaginformációinak lekéréséhez futtassa a következő parancsot. Cserélje le a *\<deployment_user>* elemet a konfigurált üzembehelyezési felhasználónévre, az *\<app_name>* elemet pedig az alkalmazás nevére. Ha a rendszer kéri, használja a beállított üzembehelyezési jelszót.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

A Cloud Shellben a Python-csomag telepítéséhez futtassa a következő parancsot. Cserélje le a *\<deployment_user>* elemet a konfigurált üzembehelyezési felhasználónévre, az *\<app_name>* elemet pedig az alkalmazás nevére. Ha a rendszer kéri, használja a beállított üzembehelyezési jelszót.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

A parancskimenetből látható, hogy a rendszer a `D:\home\python362x86\python.exe` útvonalon telepítette a Pythont.

### <a name="configure-database-settings"></a>Adatbázis-beállítások konfigurálása

Az oktatóanyag korábbi részében meghatároztunk környezeti változókat a PostgreSQL-adatbázishoz való kapcsolódáshoz.

Az App Service-ben, a környezeti változókat _alkalmazásbeállításként_ adhatja meg az [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) paranccsal.

Az alábbi példa az adatbázis kapcsolati adatait alkalmazásbeállításokként adja meg. Cserélje le az *\<app_name>* elemet az alkalmazás nevére, a *\<postgresql_name>* elemet pedig a PostgreSQL-kiszolgáló nevére.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Az Azure webalkalmazás megkeresése 

Keresse meg az üzembe helyezett webalkalmazást a webböngésző használatával. 

```bash 
http://<app_name>.azurewebsites.net 
```

Láthatja az előzőleg regisztrált vendégeket, akik az előző lépésben lettek mentve az éles Azure-adatbázisban.

![Helyileg futó Python Flask-alkalmazás](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**Gratulálunk!** Egy Python Flask-alkalmazást futtat az Azure App Service-ben.

## <a name="update-data-model-and-redeploy"></a>Az adatmodell frissítése és ismételt üzembe helyezése

Ebben a lépésben adott számú résztvevőt ad hozzá az egyes eseményregisztrációkhoz a `Guest` modell frissítésével.

Tekintse meg a `modelChange` véglegesítés által megjelölt fájlokat:

```bash
git checkout modelChange -- *
```

Ebben a kiadásban már végre lettek hajtva a szükséges módosítások a nézeteken, a vezérlőkön és a modellen. Emellett tartalmaz egy, az *alembic* (`flask db migrate`) használatával létrehozott adatbázismigrálást is. A [GitHub véglegesítési nézetben](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e) az összes fájl módosításait láthatja.

### <a name="test-your-changes-locally"></a>Módosítások helyi tesztelése

Az alábbi parancsok futtatásával helyben tesztelheti a módosításokat a Flask-kiszolgáló futtatásával.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

A módosítások megtekintéséhez a böngészőben keresse fel a http://localhost:5000 címet. Hozzon létre egy tesztregisztrációt.

![Helyileg futó Python Flask-alkalmazás](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A helyi terminálablakban mentse az összes módosítást a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Nyissa meg az Azure-webalkalmazást, és próbálja ki ismét az új funkciót. Hozzon létre egy másik eseményregisztrációt.

```bash 
http://<app_name>.azurewebsites.net 
```

![Python Flask-alkalmazás az Azure App Service-ben](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Az Azure-webalkalmazás kezelése

Lépjen az [Azure Portalra](https://portal.azure.com), és tekintse meg a létrehozott webalkalmazást.

A bal oldali menüben kattintson az **App Services** lehetőségre, majd az Azure-webapp nevére.

![Navigálás a portálon az Azure-webapphoz](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

Alapértelmezés szerint a portálon a webalkalmazás **Áttekintés** oldala jelenik meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet a webalkalmazáshoz.

> [!div class="nextstepaction"]
> [Meglévő egyéni DNS-név hozzákapcsolása az Azure-webalkalmazásokhoz](app-service-web-tutorial-custom-domain.md)
