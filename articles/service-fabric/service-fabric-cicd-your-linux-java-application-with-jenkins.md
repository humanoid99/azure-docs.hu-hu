---
title: "Az Azure Service Fabric linuxos Java-alkalmazás folyamatos felépítése és integrálása a Jenkins használatával | Microsoft Docs"
description: "A linuxos Java-alkalmazás folyamatos felépítése és integrálása a Jenkins használatával"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 71e3d130f22515d22dc7f486f3dede936b874049
ms.lasthandoff: 03/25/2017


---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>A linuxos Java-alkalmazás felépítése és üzembe helyezése a Jenkins használatával
A Jenkins egy népszerű eszköz az alkalmazások folyamatos integrációjához és üzembe helyezéséhez. Ebből a témakörből megtudhatja, hogyan helyezheti üzembe Azure Service Fabric-alkalmazásait a Jenkins használatával.

## <a name="general-prerequisites"></a>Általános előfeltételek
- A Git legyen telepítve a helyi számítógépen. A Git megfelelő verzióját [a Git letöltési oldaláról](https://git-scm.com/downloads) telepítheti a különböző operációs rendszerekhez. Ha még nem járatos a Git használatában, további információkért tekintse meg a [Git dokumentációját](https://git-scm.com/docs).
- Szüksége lesz a Jenkins Service Fabric beépülő moduljára is. Ezt a [Service Fabric letöltési](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi) oldaláról töltheti le.

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>A Jenkins beállítása egy Service Fabric-fürtben

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön belüli beállítást mutatják be.

### <a name="prerequisites"></a>Előfeltételek
1. Szükséges egy kész linuxos Service Fabric-fürt. Az Azure Portallal létrehozott Service Fabric-fürtökön már telepítve van a Docker. A fürt helyi futtatása esetén ellenőrizze a ``docker info`` paranccsal, hogy a Docker telepítve van-e. Ha nincs telepítve, telepítse a következő parancsokkal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Helyezze üzembe a Service Fabric tárolóalkalmazását a fürtön a következő lépésekkel:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git -b JenkinsDocker
cd service-fabric-java-getting-started/Services/JenkinsDocker/
azure servicefabric cluster connect http://PublicIPorFQDN:19080   # Azure CLI cluster connect command
bash Scripts/install.sh
```
Ezzel telepít a fürtön egy Jenkins-tárolót, amely a Service Fabric Explorerrel figyelhető meg.

### <a name="steps"></a>Lépések
1. Nyissa meg a ``http://PublicIPorFQDN:8081`` URL-címet a böngészőben. Így megkapja a bejelentkezéshez szükséges kezdeti rendszergazdai jelszó elérési útját. Továbbra is használhatja a Jenkinst rendszergazdaként, de miután bejelentkezett a kezdeti rendszergazdai fiókkal, módosíthatja a felhasználót, és újat is létrehozhat.

   > [!NOTE]
   > Ügyeljen arra, hogy a fürt létrehozásakor a 8081-es portot adja meg az alkalmazás végponti portjaként.
   >

2. Kérje le a tároló példányazonosítóját a ``docker ps -a`` paranccsal.
3. Secure Shell-lel (SSH) jelentkezzen be a tárolóba, és illessze be a Jenkins portálon látott elérési utat. Például ha a portálon a `PATH_TO_INITIAL_ADMIN_PASSWORD` elérési út jelenik meg, futtassa a következőt:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Állítsa be a GitHubot a Jenkins használatához az [új SSH-kulcs létrehozásával és SSH-ügynökhöz adásával](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) foglalkozó cikkben található lépéseket követve.
    * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
    * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
    * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsot:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>A Jenkins beállítása Service Fabric-fürtön kívül

A Jenkinst egy Service Fabric-fürtben vagy azon kívül is beállíthatja. A következő szakaszok a fürtön kívüli beállítást mutatják be.

### <a name="prerequisites"></a>Előfeltételek
A Dockernek telepítve kell lennie. A következő parancsokkal telepítheti a Dockert a terminálról:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Így amikor a ``docker info`` fut a terminálon, a kimenetből látszódik, hogy a Docker szolgáltatás fut.

### <a name="steps"></a>Lépések
  1. Kérje le a Service Fabric Jenkins-tárolójának rendszerképét: ``docker pull raunakpandya/jenkins:v1``
  2. Futtassa a tároló rendszerképét: ``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Kérje le a tárolórendszerkép-példány azonosítóját. A ``docker ps –a`` paranccsal az összes Docker-tárolót listázhatja
  4. Jelentkezzen be a Jenkins portálra a következő lépésekkel:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Ha a tároló azonosítója 2d24a73b5964, használja a 2d24 értéket.
    * Ez a jelszó szükséges ahhoz, hogy bejelentkezzen a Jenkins-irányítópultra a ``http://<HOST-IP>:8080`` portálról.
    * Az első bejelentkezés után létrehozhatja saját felhasználói fiókját, ha a jövőben ezt kívánja használni, vagy maradhat a rendszergazdai fiók használata mellett. Ha létrehoz egy felhasználót, a továbbiakban azt kell használnia.
  5. Állítsa be a GitHubot a Jenkins használatához az [új SSH-kulcs létrehozásával és SSH-ügynökhöz adásával](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) foglalkozó cikkben található lépéseket követve.
        * A GitHub utasításait követve hozza létre az SSH-kulcsot, majd adja hozzá ahhoz a GitHub-fiókhoz, amelyen az adattár üzemel.
        * Futtassa a fenti hivatkozással elérhető parancsokat a Jenkins Docker-felületén (és ne a saját gazdagépén).
        * Ahhoz, hogy a saját gazdagépéről jelentkezhessen be a Jenkinsbe, használja a következő parancsokat:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Győződjön meg arról, hogy a fürt vagy gép, ahol a Jenkins-tároló rendszerképe fut, nyilvános IP-címmel rendelkezik. Ez lehetővé teszi, hogy a Jenkins-példány megkapja a GitHub értesítéseit.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>A Jenkins Service Fabric beépülő moduljának telepítése a portálról

1. Nyissa meg a következőt: ``http://PublicIPorFQDN:8081``
2. A Jenkins irányítópultján válassza ki a következőt: **Manage Jenkins** (Jenkins kezelése)  > **Manage Plugins** (Beépülő modulok kezelése)  > **Advanced** (Speciális).
Itt feltöltheti a beépülő modult. Válassza a **Choose file** (Fájl kiválasztása) lehetőséget, majd a **serviceFabric.hpi** fájlt, amelyet az előfeltételek szakaszban már letöltött. Ha kiválasztja az **Upload** (Feltöltés) lehetőséget, a Jenkins automatikusan telepíti a beépülő modult. Ha a rendszer kéri, engedélyezze az újraindítást.

## <a name="create-and-configure-a-jenkins-job"></a>Jenkins-feladatok létrehozása és konfigurálása

1. Hozzon létre **új elemet** az irányítópultról.
2. Adjon nevet az elemnek (pl. **MyJob**). Válassza a **free-style project** (szabad projekt) lehetőséget, majd kattintson az **OK** gombra.
3. Nyissa meg a feladat oldalát, majd kattintson a **Configure** (Konfigurálás) elemre.

   a. Az általános beállítások **Github project** (GitHub-projekt) területén adja meg a GitHub-projekt URL-címét. Ez az URL-cím üzemelteti azt a Service Fabric Java-alkalmazást, amelyet integrálni szeretne a Jenkins folyamatos integrációs és üzembe helyezési (CI/CD) folyamatával (például: ``https://github.com/sayantancs/SFJenkins``).

   b. A **Source Code Management** (Forráskódkezelés) szakaszban válassza a **Git** elemet. Adja meg annak az adattárnak az URL-címét, amely a Jenkins CI/CD folyamatával integrálni kívánt Service Fabric Java-alkalmazást tartalmazza (például: ``https://github.com/sayantancs/SFJenkins.git``). Itt adhatja meg azt is, hogy melyik ágat kívánja létrehozni, például: **/master**.
4. Adja meg a *GitHub* beállítását (amelyiken az adattár üzemel), így létrejöhet a kommunikáció a Jenkinsszel. Ehhez a következő lépések szükségesek:

   a. Nyissa meg a GitHub-adattár oldalát. Lépjen a **Settings** (Beállítások)  > **Integrations and Services** (Integrációk és szolgáltatások) részbe.

   b. Válassza az **Add Service** (Szolgáltatás hozzáadása) elemet, írja be a **Jenkins** kifejezést, majd válassza ki a **Jenkins-GitHub beépülő modult**.

   c. Adja meg a Jenkins-webhook URL-címét (alapértelmezés szerint ez a következő: ``http://<PublicIPorFQDN>:8081/github-webhook/``). Kattintson az **add/update service** (Szolgáltatás hozzáadása/frissítése) elemre.

   d. A Jenkins-példányra egy tesztesemény érkezik. A GitHubban egy zöld pipa jelenik meg a webhook mellett, és a projekt létrejön.

   e. A **Build Triggers** (Eseményindítók létrehozása) területen válassza ki a kívánt felépítési lehetőséget. Jelen esetben minden alkalommal szeretnénk elindítani egy felépítést, amikor valamilyen módosításra kerül sor az adattárban. Így a kiválasztandó lehetőség a következő: **GitHub hook trigger for GITScm polling** (GitHub beavatkozási pont eseményindító GITScm lekérdezés esetén). (Korábban ez **Build when a change is pushed to GitHub** (Felépítés módosítások GitHubon való közzétételekor) volt.)

   f. A **Build** (Felépítés) szakaszban az **Add build step** (Felépítési lépés hozzáadása) legördülő listából válassza az **Invoke Gradle Script** (Gradle szkript meghívása) lehetőséget. A megjelenő widgeten adja meg a **fő felépítési szkript** elérési útját az alkalmazásához. A rendszer felveszi a build.gradle elemet a megadott elérési útból, és annak megfelelően működik. Ha (az Eclipse beépülő modul vagy a Yeoman-generátor használatával) létrehozott projekt neve ``MyActor``, akkor a fő felépítési szkriptnek tartalmaznia kell a következőt: ``${WORKSPACE}/MyActor``. Tekintse meg erre példaként az alábbi képernyőképet:

    ![Service Fabric, Jenkins felépítési művelet][build-step]

   g. A **Post-Build Actions** (Felépítés utáni műveletek) legördülő menüből válassza a **Deploy Service Fabric Project** (Service Fabric-projekt üzembe helyezése) elemet. Itt meg kell adnia annak a fürtnek az adatait, ahová a Jenkins által lefordított Service Fabric-alkalmazást üzembe kívánja helyezni. Az üzembe helyezéshez szükséges egyéb alkalmazásadatokat is megadhatja. Tekintse meg erre példaként az alábbi képernyőképet:

    ![Service Fabric, Jenkins felépítési művelet][post-build-step]

   > [!NOTE]
   > Ha a Service Fabricet használja a Jenkins-tároló rendszerképének üzembe helyezéséhez, a fürtnek meg kell egyeznie azzal, ahol a Jenkins-tárolóalkalmazás található.
   >

## <a name="next-steps"></a>Következő lépések
A GitHub és a Jenkins beállítása kész. Érdemes lehet elvégezni néhány mintamódosítást a ``MyActor`` projekten az adattárban (https://github.com/sayantancs/SFJenkins). A módosításokat továbbíthatja egy távoli ``master`` ágra (vagy bármilyen egyéb olyan ágra, amelyet beállított). Ez aktiválja a konfigurált ``MyJob`` Jenkins-feladatot. A feladat lekéri a módosításokat a GitHubról, érvénybe lépteti őket, és üzembe helyezi az alkalmazást a fürt azon végpontján, amelyet a felépítés utáni műveletekben adott meg.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png

