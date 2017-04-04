---
title: "Ismerkedés az Azure Mobile Engagement Cordova/Phonegap-alkalmazásokkal való használatával"
description: "Ismerje meg, hogyan használható az Azure Mobile Engagement a Cordova/Phonegap-alkalmazásokhoz kapcsolódó elemzések és leküldéses értesítések tekintetében."
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 54fe9113-e239-4ed7-9fd1-a502d7ac7f47
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-phonegap
ms.devlang: js
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: d7a761310782faab1dda023785f93cf90742e2ae
ms.lasthandoff: 03/28/2017


---
# <a name="get-started-with-azure-mobile-engagement-for-cordovaphonegap"></a>Ismerkedés az Azure Mobile Engagement Cordova/Phonegap-alkalmazásokkal való használatával
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Ebben a témakörben elsajátíthatja, hogy miként használható az Azure Mobile Engagement az alkalmazás használatának megértéséhez, valamint leküldéses értesítéseknek a Cordova használatával fejlesztett mobilalkalmazásba történő küldéséhez a szegmentált felhasználók számára.

Ebben az oktatóanyagban létre fogunk hozni egy üres Cordova-alkalmazást Mac használatával, majd integráljuk a Mobile Engagement SDK-t. Az alkalmazás alapszintű elemzési adatokat gyűjt össze, és leküldéses értesítéseket fogad az iOS rendszerhez készült Apple leküldéses értesítési rendszerének (APNS) és az Android Google Cloud Messaging (GCM) szolgáltatásának a használatával. Az alkalmazást iOS- vagy Android-eszközön fogjuk üzembe helyezni tesztelés céljából. 

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-cordova-get-started).
> 
> 

Az oktatóanyaghoz az alábbiakra lesz szükség:

* XCode, amely a MAC App Store áruházából telepíthető (iOS rendszeren történő üzembe helyezéshez),
* [Android SDK és -emulátor](http://developer.android.com/sdk/installing/index.html) (Android rendszeren történő üzembe helyezéshez),
* leküldéses értesítési tanúsítvány (.p12), amelyet az Apple APNS fejlesztési központjában szerezhet be,
* GCM-projektszám, amelyet a Google Developer Console for GCM konzolon keresztül szerezhet be,
* [Mobile Engagement Cordova beépülő modul](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [!NOTE]
> A Cordova beépülő modul forráskódját és a ReadMe fájlt a [GitHub](https://github.com/Azure/azure-mobile-engagement-cordova) webhelyén érheti el.
> 
> 

## <a id="setup-azme"></a>A Mobile Engagement beállítása a Cordova-alkalmazáshoz
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
Ez az oktatóanyag egy „alapszintű integrációt” mutat be, ami minimálisan szükséges az adatok gyűjtéséhez és leküldéses értesítés küldéséhez. 

Létre fogunk hozni egy alapszintű alkalmazást a Cordova segítségével az integráció bemutatásához:

### <a name="create-a-new-cordova-project"></a>Új Cordova-projekt létrehozása
1. Indítsa el a *Terminál* ablakot a Mac számítógépen, és írja be az alábbiakat, amellyel létrehoz egy új Cordova-projektet az alapértelmezett sablon alapján. Győződjön meg róla, hogy az iOS-alkalmazás majdani üzembe helyezéséhez használni kívánt közzétételi profil a „com.mycompany.myapp” alkalmazásazonosítót használja. 
   
        $ cordova create azme-cordova com.mycompany.myapp
        $ cd azme-cordova
2. Hajtsa végre az alábbiakat a projekt konfigurálásához **iOS** esetén, majd futtassa azt az iOS Simulator alkalmazásban:
   
        $ cordova platform add ios 
        $ cordova run ios
3. Hajtsa végre az alábbiakat a projekt konfigurálásához **Android** esetén, majd futtassa azt az Android-emulátorban. Győződjön meg róla, hogy az Android SDK Emulator beállításainál a Target (Cél) beállítás értéke Google APIs (Google Inc.), a CPU/ABI értéke pedig a Google APIs ARM.  
   
        $ cordova platform add android
        $ cordova run android
4. Adja hozzá a Cordova-konzol beépülő modulját. 

    ```
    $ cordova plugin add cordova-plugin-console
    ``` 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Az alkalmazás csatlakoztatása a Mobile Engagement háttérrendszeréhez
1. Telepítse az Azure Mobile Engagement Cordova beépülő modulját, és közben adja meg a beépülő modul konfigurálásához szükséges változók értékeit:
   
        cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
             --variable AZME_IOS_CONNECTION_STRING=<iOS Connection String> 
            --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
            --variable AZME_ANDROID_CONNECTION_STRING=<Android Connection String> 
            --variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
            --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
            --variable AZME_ACTION_URL =... (URL scheme which triggers the app for deep linking)
            --variable AZME_ENABLE_NATIVE_LOG=true|false
            --variable AZME_ENABLE_PLUGIN_LOG=true|false

*Android Reach Icon*: az erőforrás nevének kell lennie kiterjesztés vagy a rajzolható előtag nélkül (például: mynotificationicon), és az ikonfájlt az Android-projektbe kell másolni (platforms/android/res/drawable).

*iOS Reach Icon*: az erőforrás nevének kell lennie a kiterjesztéssel együtt (például: mynotificationicon.png), és az ikonfájlt hozzá kell adni az iOS-projekthez az XCode segítségével (az Add Files Menu (Fájl hozzáadása menü) használatával).

## <a id="monitor"></a>Valós idejű figyelés engedélyezése
1. A Cordova-projektben módosítsa a **www/js/index.js** fájlt úgy, hogy hozzáadja a Mobile Engagementre irányuló hívást egy új tevékenység deklaráláshoz a *deviceReady* esemény fogadását követően.
   
         onDeviceReady: function() {
                Engagement.startActivity("myPage",{});
            }
2. Futtassa az alkalmazást:
   
   * **iOS esetén**
     
       A `Terminal` ablakban indítsa el az alkalmazást a Simulator új példányában az alábbiak végrehajtásával:
     
           cordova run ios
   * **Android esetén**
     
       A `Terminal` ablakban indítsa el az alkalmazást az emulátor új példányában az alábbiak végrehajtásával:
     
           cordova run android
3. A konzolnaplófájlokban az alábbiakat láthatja:
   
        [Engagement] Agent: Session started
        [Engagement] Agent: Activity 'myPage' started
        [Engagement] Connection: Established
        [Engagement] Connection: Sent: appInfo
        [Engagement] Connection: Sent: startSession
        [Engagement] Connection: Sent: activity name='myPage'

## <a id="monitor"></a>Az alkalmazás csatlakoztatása a valós idejű megfigyeléshez
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Leküldéses értesítések és alkalmazáson belüli üzenetek engedélyezése
A Mobile Engagement lehetővé teszi a felhasználókkal folytatott interakciót a kampányok részeként megjelenő leküldéses értesítésekkel és alkalmazáson belüli üzenetekkel. Ez a modul REACH (Elérés) néven érhető el a Mobile Engagement portálon.
Az alábbi szakaszok állítják be az alkalmazást a fogadásukra.

### <a name="configure-push-credentials-for-mobile-engagement"></a>Leküldési hitelesítő adatok konfigurálása a Mobile Engagementhez
Annak engedélyezéséhez, hogy a Mobile Engagement leküldéses értesítéseket küldhessen a nevében, hozzáférést kell biztosítania számára az Apple iOS-tanúsítvány vagy a GCM-kiszolgáló API-kulcsához. 

1. Nyissa meg a Mobile Engagement portált. Nyissa meg a projekthez használt alkalmazást, majd kattintson a lap alján található **Engage** (Aktiválás) gombra:
   
    ![][1]
2. Ezzel megnyitja az Engagement portál beállításokat tartalmazó lapját. A lapon kattintson a **Native Push** (Natív leküldés) szakaszra:
   
    ![][2]
3. iOS-tanúsítvány/GCM-kiszolgáló API-kulcsának konfigurálása
   
    **iOS**
   
    a. Jelölje ki .p12 tanúsítványát, töltse fel, és írja be jelszavát:
   
    ![][3]
   
    **Android**
   
    a. Kattintson az **API Key** (API-kulcs) előtt található szerkesztési ikonra a GCM Settings (GCM-beállítások) szakaszban, illessze be a GCM kiszolgálói kulcsot az előugró ablakban, majd kattintson az **OK** gombra. 
   
    ![][4]

### <a name="enable-push-notifications-in-the-cordova-app"></a>Leküldéses értesítések engedélyezése a Cordova-alkalmazásban
Módosítsa a **www/js/index.js** fájlt úgy, hogy hozzáadja a Mobile Engagementre irányuló hívást leküldéses értesítések kéréséhez és egy kezelő deklarálásához:

     onDeviceReady: function() {
           Engagement.initializeReach(  
                 // on OpenUrl  
                 function(_url) {   
                 alert(_url);   
                 });  
            Engagement.startActivity("myPage",{});  
        }

### <a name="run-the-app"></a>Az alkalmazás futtatása
**iOS**

1. Mivel az iOS csak tényleges eszközre teszi lehetővé leküldéses értesítések küldését, az XCode használatával fogjuk létrehozni és üzembe helyezni az alkalmazást az eszközön a leküldéses értesítések teszteléséhez. Lépjen a Cordova-projekt létrehozásának helyére, majd navigáljon a **...\platforms\ios** helyre. Nyissa meg a natív .xcodeproj fájlt az XCode-ban. 
2. Hozza létre és helyezze üzembe a Cordova-alkalmazást az iOS eszközön annak a fióknak a használatával, amely tartalmazza a Mobile Engagement portálra most feltöltött tanúsítványt és a Cordova-alkalmazás létrehozásakor megadott azonosítóval egyező alkalmazásazonosítót magában foglaló kiépítési profilt. Az egyeztetéshez megtekintheti a *csomagazonosítót* a **Resources\*-info.plist** fájlban az XCode-ban. 
3. Az eszközön megjelenik a szokásos iOS előugró ablak, amely engedélyt kér az alkalmazás számára értesítések küldéséhez. Adja meg az engedélyt. 

**Android**

Nyugodtan használhatja az emulátort az Android-alkalmazás futtatásához, mivel az Android-emulátor támogatja a GCM-értesítéseket. 

    cordova run android

## <a id="send"></a>Értesítés küldése az alkalmazásnak
Most létre fogunk hozni egy egyszerű leküldéses értesítési kampányt, amely egy leküldéses üzenetet fog küldeni az eszközön futó alkalmazásnak:

1. Lépjen a Mobile Engagement portál **Reach** (Elérés) lapjára.
2. Kattintson a **New Announcement** (Új közlemény) elemre a leküldéses kampány létrehozásához.
   
    ![][6]
3. Adja meg a bemeneti adatokat a kampány létrehozásához **[Android]**.
   
   * Adja meg a kampány nevét a **Name** mezőben. 
   * A **Delivery Type** (Kézbesítési típus) értékeként jelölje be a *System notification* és *Simple* (Rendszerértesítés, Egyszerű) választógombot.
   * A **Delivery time** (Kézbesítési idő) értékeként jelölje be az *Any time* (Bármikor) választógombot.
   * Írja be az értesítés címét a **Title** (Cím) mezőbe. A cím a leküldéses értesítés első sora lesz.
   * Írja be az értesítés szövegét a **Message** (Üzenet) mezőbe. Ez a szöveg lesz az üzenet törzse. 
     
     ![][11]
4. Adja meg a bemeneti adatokat a kampány létrehozásához **[iOS]**.
   
   * Írja be a kampány nevét a **Name** mezőbe. 
   * A **Delivery time** (Kézbesítési idő) értékeként jelölje be az *Out of app only* (Csak alkalmazáson kívül) választógombot.
   * Írja be az értesítés címét a **Title** (Cím) mezőbe. A cím a leküldéses értesítés első sora lesz.
   * Írja be az értesítés szövegét a **Message** (Üzenet) mezőbe. Ez a szöveg lesz az üzenet törzse. 
     
     ![][12]
5. Görgessen le, és a Content (Tartalom) részen jelölje be a **Notification only** (Csak értesítés) választógombot.
   
    ![][8]
6. (Választható) Megadhatja egy művelet URL-címét is. Győződjön meg róla, hogy a cím a beépülő modul **AZME\_REDIRECT\_URL** változójának konfigurálásánál megadott URL-sémát használja, például: *myapp://test*.  
7. Ezzel befejezte a lehető legalapvetőbb kampány beállítását. Görgessen újra le, és kattintson a **Create** (Létrehozás) gombra a kampány mentéséhez.
8. Végül aktiválja a kampányt az **Activate** (Aktiválás) gombra kattintva.
   
    ![][10]
9. Ekkor meg kellene jelennie egy leküldéses értesítésnek az eszközön vagy az emulátorban a jelen kampány részeként. 

## <a id="next-steps"></a>Következő lépések
[A Cordova Mobile Engagement SDK-val elérhető összes módszer áttekintése](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- Images. -->

[1]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[2]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[3]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[4]: ./media/mobile-engagement-cordova-get-started/api-key.png
[6]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[8]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[10]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[11]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-android.png
[12]: ./media/mobile-engagement-cordova-get-started/campaign-first-params-ios.png


