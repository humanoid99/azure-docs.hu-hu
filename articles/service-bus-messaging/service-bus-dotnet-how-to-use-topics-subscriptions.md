---
title: "Az Azure Service Bus-üzenettémakörök használata a .NET keretrendszerrel | Microsoft Docs"
description: "Megismerheti a Service Bus-üzenettémakörök és előfizetések a .NET-keretrendszerrel való használatát az Azure-ban. A kódminták .NET-alkalmazásokhoz íródtak."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 03/23/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: bec18e91ef8798a791d4b1fe93bd529593197e01
ms.lasthandoff: 03/24/2017


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>A Service Bus-üzenettémakörök és -előfizetések használata
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Ez a cikk a Service Bus-üzenettémakörök és -előfizetések használatát ismerteti. A kódminták C# nyelven íródtak, és a .NET API-kat használják. Az ismertetett forgatókönyvek között megtalálható az üzenettémák és előfizetések létrehozása, az előfizetés-szűrők létrehozása, az üzenetek küldése egy üzenettémakörbe, az üzenetek fogadása egy előfizetésből, valamint az üzenettémák és előfizetések törlése. Az üzenettémakörökkel és előfizetésekkel kapcsolatos további információkért lásd a [További lépések](#next-steps) szakaszt.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Az alkalmazás konfigurálása a Service Bus használatára
Amikor egy Service Bust használó alkalmazást hoz létre, fel kell vennie egy, a Service Bus-összeállításra mutató hivatkozást, és bele kell foglalnia a megfelelő névtereket. Ennek legegyszerűbb módja a megfelelő [NuGet](https://www.nuget.org)-csomag letöltése.

## <a name="get-the-service-bus-nuget-package"></a>A Service Bus NuGet-csomag beszerzése
Az alkalmazások az összes szükséges Service Bus-függőséggel való konfigurálásának legegyszerűbb módja a [Service Bus NuGet-csomag](https://www.nuget.org/packages/WindowsAzure.ServiceBus) telepítése. A Service Bus NuGet-csomagnak a projektben való telepítéséhez tegye a következőket:

1. A Megoldáskezelőben kattintson a jobb gombbal a **Hivatkozások** elemre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.
2. Kattintson a **Browse** (Tallózás) gombra, keressen rá az „Azure Service Bus” kifejezésre, majd válassza ki a **Microsoft Azure Service Bus** elemet. Kattintson az **Install** (Telepítés) gombra a telepítés befejezéséhez, majd zárja be a párbeszédpanelt:
   
   ![][7]

Készen áll arra, hogy kódot írjon a Service Bushoz.

## <a name="create-a-service-bus-connection-string"></a>Service Bus kapcsolati karakterlánc létrehozása
A Service Bus egy kapcsolati karakterláncot használ a végpontok és a hitelesítő adatok tárolásához. A következő esetekben érdemes lehet a kapcsolati karakterláncot egy konfigurációs fájlban elhelyezni a rögzített megadás helyett:

* Az Azure-szolgáltatások használata esetén ajánlott a kapcsolati karakterláncot az Azure szolgáltatás konfigurációs rendszerével tárolni (.csdef és .cscfg fájlokban).
* Azure-webhelyek vagy Azure virtuális gépek használata esetén ajánlott a kapcsolati karakterláncot a. NET konfigurációs rendszerével tárolni (például a Web.config fájlban).

Mindkét esetben a(z) `CloudConfigurationManager.GetSetting` metódussal kérheti le a kapcsolati karakterláncot, amint az a cikk későbbi részében látható lesz.

### <a name="configure-your-connection-string"></a>A kapcsolati karakterlánc konfigurálása
A szolgáltatás konfigurációs mechanizmusa lehetővé teszi a konfigurációs beállítások dinamikus módosítását az [Azure Portalról][Azure portal] az alkalmazás újbóli telepítése nélkül. Hozzáadhat például egy `Setting` címkét a szolgáltatás definíciós (**.csdef**) fájljához a következő példában látható módon.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Ezután értékeket határozhat meg a szolgáltatás konfigurációs (.cscfg) fájljában.

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Használja a portálról lekért közös hozzáférésű jogosultságkód (SAS-) kulcs nevét és a kulcs értékeit az előzőekben leírtak szerint.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>A kapcsolati karakterlánc konfigurálása Azure-webhelyek vagy Azure virtuális gépek használatakor
Webhelyek vagy virtuális gépek használata esetén ajánlott a. NET konfigurációs rendszerét használni (például a Web.config fájlt). A kapcsolati karakterláncot a(z) `<appSettings>` elem használatával tárolhatja.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Használja az [Azure Portalról][Azure portal] lekért SAS-nevet és -kulcsértékeket az előzőekben leírtak szerint.

## <a name="create-a-topic"></a>Üzenettémakör létrehozása
A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) osztály használatával kezelési műveleteket hajthat végre a Service Bus-üzenettémakörökön és előfizetéseken. Ez az osztály metódusokat biztosít az üzenettémakörök létrehozásához, enumerálásához és törléséhez.

A következő példa egy `NamespaceManager` objektumot állít össze az Azure `CloudConfigurationManager` osztály használatával egy olyan kapcsolati karakterlánccal, amely egy Service Bus-névtér alapszintű címéből és a megfelelő SAS hitelesítő adatokból áll, és rendelkezik a kezeléséhez szükséges engedélyekkel. Ez a kapcsolati karakterlánc a következő formátumot követi:

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Használja a következő példát az előző szakaszban megadott konfigurációs beállításokkal.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

A [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager) metódus túlterhelésekkel rendelkezik, amelyek lehetővé teszik a témakör tulajdonságainak hangolását (például a témakörbe küldött üzenetek alapértelmezett élettartam (TTL) értékének meghatározását). Ezek a beállítások a [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) használatával alkalmazhatók. A következő példa bemutatja, hogyan hozható létre egy **TestTopic** nevű üzenettémakör 5 GB maximális mérettel és 1 perces alapértelmezett üzenet-élettartammal.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektumokon alkalmazhatja a [ZopicExists](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) metódust annak ellenőrzéséhez, hogy már létezik-e egy adott nevű témakörben.
> 
> 

## <a name="create-a-subscription"></a>Előfizetés létrehozása
A [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) osztály használatával is létrehozhat üzenettémakör-előfizetéseket. Az előfizetések el vannak nevezve, és rendelkezhetnek olyan szűrőkkel, amelyek korlátozzák az előfizetés virtuális üzenetsorának átadott üzenetek készletét.

> [!IMPORTANT]
> Ahhoz, hogy egy előfizetés üzeneteket kapjon, létre kell hoznia az előfizetés, mielőtt még bármilyen üzenetet küldene a témakörbe. Ha egy témakörhöz nem tartoznak előfizetések, a témakör figyelmen kívül hagyja az üzeneteket.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Előfizetés létrehozása az alapértelmezett (MatchAll) szűrővel
Ha nincs meghatározva szűrő egy új előfizetés létrehozásakor, akkor a **MatchAll** szűrő az alapértelmezett használandó szűrő. A **MatchAll** szűrő használatakor a rendszer a témakörbe közzétett összes üzenetet elhelyezi az előfizetés virtuális üzenetsorában. A következő példa egy „AllMessages” nevű előfizetést hoz létre, és az alapértelmezett **MatchAll** szűrőt használja.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Előfizetések létrehozása szűrőkkel
Beállíthat szűrőket, amelyek lehetővé teszik annak meghatározását, hogy mely üzenetek jelenjenek meg egy adott üzenettémakör-előfizetésben.

Az előfizetések által támogatott legrugalmasabb típusú szűrő az [SqlFilter][SqlFilter] osztály, amely az SQL92 egy részhalmazát valósítja meg. Az SQL-szűrők az üzenettémába közzétett üzenetek tulajdonságain működnek. Az SQL-szűrőkkel használható kifejezésekkel kapcsolatos további információkat az [SqlFilter.SqlExpression][SqlFilter.SqlExpression] szintaxisa tartalmaz.

A következő példa egy **HighMessages** nevű előfizetést hoz létre egy [SqlFilter][SqlFilter] objektummal, amely csak azokat az üzeneteket választja ki, amelyek egyéni **MessageNumber** tulajdonságának értéke nagyobb, mint 3.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageId > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

A következő példa hasonlóképpen egy **LowMessages** nevű előfizetést hoz létre egy [SqlFilter][SqlFilter] objektummal, amely csak azokat az üzeneteket választja ki, amelyek egyéni **MessageNumber** tulajdonságának értéke kisebb vagy egyenlő, mint 3.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageId <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Ekkor, ha a(z) `TestTopic` egy üzenetet kap, a rendszer mindig kézbesíti az üzenetet az **AllMessages** üzenettémakör-előfizetésre feliratkozott címzetteknek, és szelektív módon kézbesíti a **HighMessages** és a **LowMessages** üzenettémakör-előfizető címzetteknek (az üzenet tartalmától függően).

## <a name="send-messages-to-a-topic"></a>Üzenetek küldése egy üzenettémakörbe
Az alkalmazás a kapcsolati karakterlánc használatával létrehoz egy [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) objektumot egy Service Bus-témakörbe való üzenetküldéshez.

A következő kód bemutatja, hogyan hozható létre egy [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) objektum a [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_) API-val korábban létrehozott **TestTopic** témakörhöz.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

A Service Bus-üzenettémakörbe küldött üzenetek a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) osztály példányai. A **BrokeredMessage** objektumok egy szabványos tulajdonságkészlettel (például a [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) és a [TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), az egyéni alkalmazásspecifikus tulajdonságokat tároló könyvtárral, valamint egy tetszőleges alkalmazásadatokból álló törzzsel rendelkeznek. Az alkalmazás beállíthatja az üzenet törzsét egy bármilyen szerializálható objektumnak a **BrokeredMessage** objektum konstruktorának való átadásával, ezután a megfelelő **DataContractSerializer** osztály szerializálja az objektumot. Azt is megteheti, hogy megad egy **System.IO.Stream** objektumot.

A következő példa bemutatja, hogyan küldhető öt tesztüzenet az előző példakódban beszerzett **TestTopic** [TopicClient](/dotnet/api/microsoft.servicebus.messaging.topicclient) objektumba. Vegye figyelembe, hogy az egyes üzenetek [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) tulajdonságának értéke a ciklus ismétléseinek számától függően változik (ez határozza meg, hogy melyik előfizetések fogják megkapni).

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

A Service Bus-üzenettémakörök a [Standard csomagban](service-bus-premium-messaging.md) legfeljebb 256 KB, a [Prémium csomagban](service-bus-premium-messaging.md) legfeljebb 1 MB méretű üzeneteket támogatnak. A szabványos és az egyéni alkalmazástulajdonságokat tartalmazó fejléc mérete legfeljebb 64 KB lehet. A témakörökben tárolt üzenetek száma korlátlan, a témakörök által tárolt üzenetek teljes mérete azonban korlátozva van. A témakör ezen méretét a létrehozáskor kell meghatározni, és a felső korlátja 5 GB. Ha a particionálás engedélyezve van, a felső korlát magasabb. További információkért lásd: [Particionált üzenetküldési entitások](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Üzenetek fogadása egy előfizetésből
Az üzenetek előfizetésből való fogadásához egy [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) objektum használata ajánlott. A **SubscriptionClient** objektumok két különböző módban tudnak működni: [*ReceiveAndDelete* és *PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode). A **PeekLock** az alapértelmezett érték.

A **ReceiveAndDelete** mód használatakor a fogadás egy egylépéses művelet – vagyis amikor a Service Bus egy olvasási kérést kap egy előfizetésben lévő üzenetre vonatkozóan, feldolgozottként jelöli meg az üzenetet, és visszaadja az alkalmazásnak. A **ReceiveAndDelete** mód a legegyszerűbb modell, és az olyan forgatókönyvekben működik a legjobban, ha az alkalmazás működését nem zavarja, hogy hiba esetén nem dolgoz fel üzenetet. Ennek megértéséhez képzeljen el egy forgatókönyvet, amelyben a fogyasztó kiad egy fogadási kérést, majd összeomlik a feldolgozása előtt. Mivel ekkor a Service Bus már feldolgozottként jelölte meg az üzenetet, az alkalmazás újraindításakor és az üzenetek feldolgozásának megkezdésekor ki fogja hagyni az összeomlás előtt feldolgozott üzenetet.

**PeekLock** módban (az alapértelmezett módban) a fogadás kétszakaszos művelet, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek működését zavarják a hiányzó üzenetek. Amikor a Service Bus fogad egy kérést, megkeresi és zárolja a következő feldolgozandó üzenetet, hogy más fogyasztók ne tudják fogadni, majd visszaadja az alkalmazásnak. Miután az alkalmazás befejezi az üzenet feldolgozását (vagy megbízható módon tárolja a jövőbeli feldolgozáshoz), végrehajtja a fogadási folyamat második a [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) meghívásával a fogadott üzenethez. Amikor a Service Bus látja a **Complete** hívást, feldolgozottként jelöli meg az üzenetet, és eltávolítja az előfizetésből.

A következő példa bemutatja, hogyan fogadhatók és dolgozhatók fel az üzenetek az alapértelmezett **PeekLock** mód használatával. Egy másik [ReceiveMode](/dotnet/api/microsoft.servicebus.messaging.receivemode) érték meghatározásához a [CreateFromConnectionString](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_) egy másik túlterhelését használhatja. Ez a példa az [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) visszahívást használja az üzenetek feldolgozásához, amikor megérkeznek a **HighMessages** előfizetésbe.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

A példa egy [OnMessageOptions](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions) objektum használatával konfigurálja az [OnMessage](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) visszahívást. Az [AutoComplete](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) **hamis** értékre van állítva, így manuálisan vezérelhető, hogy mikor legyen meghívva a [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) metódus a fogadott üzenethez. Az [AutoRenewTimeout](/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) 1 percre van beállítva, aminek következtében az ügyfél legfeljebb egy percet vár, mielőtt leállítja az automatikus megújítási szolgáltatást, és új hívást indít az üzenetek ellenőrzése érdekében. Ez a tulajdonságérték csökkenti azon alkalmak számát, amikor az ügyfél olyan felszámítható hívásokat kezdeményez, amelyek nem kérnek le üzeneteket.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Az alkalmazás-összeomlások és nem olvasható üzenetek kezelése
A Service Bus olyan funkciókat biztosít, amelyekkel zökkenőmentesen helyreállíthatja az alkalmazás hibáit vagy az üzenetek feldolgozásának nehézségeit. Ha egy fogadó alkalmazás valamilyen okból nem tud feldolgozni egy üzenetet, akkor meghívhatja az [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__) metódust a fogadott üzenethez (a [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) metódus helyett). Ennek hatására a Service Bus feloldja az üzenet zárolását az előfizetésen belül, és lehetővé teszi az ugyanazon vagy egy másik fogyasztó alkalmazás általi ismételt fogadását.

Emellett egy időtúllépés van hozzárendelve az előfizetésben lévő üzenetekhez, és ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási idő lejárta előtt (például ha az alkalmazás összeomlik), akkor a Service Bus automatikusan feloldja az üzenet zárolását, és lehetővé teszi az újbóli fogadását.

Ha az alkalmazás az üzenet feldolgozása után, de a [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) kérés kiadása előtt omlik össze, az üzenet újból kézbesítve lesz az alkalmazásnak, amikor újraindul. Ezt a módszert gyakran *Legalább egyszeri feldolgozásnak* nevezik. Ez azt jelenti, hogy minden üzenet legalább egyszer fel lesz dolgozva, de bizonyos helyzetekben előfordulhat ugyanazon üzenet újbóli kézbesítése. Ha a forgatókönyvben nem lehetségesek a duplikált üzenetek, akkor az alkalmazásfejlesztőnek további logikát kell az alkalmazásba építenie az üzenetek ismételt kézbesítésének kezeléséhez. Ez gyakran az üzenet [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek során.

## <a name="delete-topics-and-subscriptions"></a>Témakörök és előfizetések törlése
A következő példa bemutatja, hogy hogyan törölhető a **TestTopic** témakör a **HowToSample** szolgáltatásnévtérből.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Egy témakör törlése az adott témakörre regisztrált összes előfizetést is törli. Az előfizetések független módon is törölhetők. A következő kód bemutatja, hogyan törölhető a **HighMessages** nevű előfizetés a **TestTopic** témakörből.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Service Bus-témakörök és -előfizetések alapjait, az alábbi hivatkozásokból tudhat meg többet.

* [Üzenetsorok, témakörök és előfizetések][Queues, topics, and subscriptions].
* [Témakörszűrők – minta][Topic filters sample].
* Az [SqlFilter][SqlFilter] API-referenciája.
* A [Service Bus által felügyelt üzenettovábbítás .NET oktatóanyaga][Service Bus brokered messaging .NET tutorial] segítségével összeállíthat egy működő alkalmazást, amely üzeneteket küld egy Service Bus-üzenetsorba, illetve üzeneteket fogad belőle.
* Service Bus-minták: letöltheti őket az [Azure-minták][Azure samples] közül, vagy átnézheti az [áttekintésüket](service-bus-samples.md).

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2

