<properties
    pageTitle="Brug Service Bus emner med .NET | Microsoft Azure"
    description="Lær at bruge Service Bus emner og abonnementer med .NET i Azure. Kodeeksempler, der er skrevet til .NET-programmer."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Sådan bruges Service Bus emner og abonnementer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I denne artikel beskrives, hvordan du bruger Service Bus emner og abonnementer. Eksemplerne er skrevet i C# og bruger .NET API'er. Scenarier dækket omfatter oprettelse af emner og abonnementer, oprette abonnement filtre, sende meddelelser til et emne, modtager meddelelser fra et abonnement og slette emner og abonnementer. Du kan finde flere oplysninger om emner og abonnementer, afsnittet [Næste trin](#next-steps) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Konfigurere programmet til at bruge Service Bus

Når du opretter et program, der bruger Service Bus, skal du tilføje en reference til samlingen Service Bus og medtage de tilsvarende navneområder. Den nemmeste måde at gøre dette er at hente den relevante [NuGet](https://www.nuget.org) -pakke.

## <a name="get-the-service-bus-nuget-package"></a>Få pakken Service Bus NuGet

[Tjenesten Bus NuGet pakke](https://www.nuget.org/packages/WindowsAzure.ServiceBus) er den nemmeste måde at få Service Bus API og for at konfigurere dit program med alle de nødvendige Service Bus afhængigheder. For at installere Service Bus NuGet pakke i dit projekt skal du gøre følgende:

1.  I Solution Explorer, skal du højreklikke på **referencer**, og klik derefter på **Administrer NuGet pakker**.
2.  Søg efter "Service Bus", og vælg elementet, **Microsoft Azure Service Bus** . Klik på **Installer** for at fuldføre installationen, og derefter lukke dialogboksen følgende:

    ![][7]

Du er nu klar til at skrive programkode for tjenesten Bus.

## <a name="create-a-service-bus-connection-string"></a>Oprette en forbindelsesstreng Service Bus

Service Bus bruger en forbindelsesstreng til at gemme slutpunkter og legitimationsoplysninger. Du kan anbringe din forbindelsesstreng i en konfigurationsfil i stedet for hårde-kodning det:

- Når du bruger Azure tjenester, anbefales det, at du gemmer din forbindelsesstreng, ved hjælp af Azure service Konfigurationssystemet (.csdef og .cscfg filer).
- Når du bruger Azure websteder eller virtuelle Azure-computere, anbefales det, at du gemmer din forbindelsesstreng, ved hjælp af .NET Konfigurationssystemet (for eksempel filen Web.config).

I begge tilfælde kan du hente din forbindelse streng ved hjælp af den `CloudConfigurationManager.GetSetting` metode, som vist senere i denne artikel.

### <a name="configure-your-connection-string"></a>Konfigurere din forbindelsesstreng

Tjenesten konfiguration ordning gør det muligt at ændre dynamisk indstillinger for søgekonfiguration fra [Azure portal][] uden geninstallation af dit program. For eksempel tilføje en `Setting` navn til filen service definition (**.csdef**), som vist i det næste eksempel.

```
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

Derefter skal du angive værdier i service-konfigurationsfil (.cscfg).

```
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

Bruge delte Access signatur (SAS) vigtige navn og nøgleværdier, der er hentet fra portalen, som beskrevet tidligere.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Konfigurere din forbindelsesstreng, når du bruger Azure websteder eller virtuelle Azure-computere

Når du bruger websteder eller virtuelle maskiner, anbefales det, at du bruger .NET Konfigurationssystemet (for eksempel Web.config). Du gemmer den forbindelse streng ved hjælp af den `<appSettings>` element.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Bruge SAS navn og nøgleværdier, som du har hentet fra [Azure-portalen][], som beskrevet tidligere.

## <a name="create-a-topic"></a>Oprette et emne

Du kan udføre administration handlinger for tjenesten Bus emner og abonnementer ved hjælp af klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Denne klasse indeholder metoder til at oprette, optælle og slette emner.

I følgende eksempel oprettes en `NamespaceManager` objekt ved hjælp af Azure `CloudConfigurationManager` klasse med en forbindelsesstreng bestående af basisadressen for et Service Bus navneområde og den relevante SAS legitimationsoplysninger med tilladelse til at administrere den. Denne forbindelsesstreng er følgende format:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Brug følgende eksempel givet konfigurationsindstillinger i det forrige afsnit.

```
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

Der er overloads af metoden [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) , der gør det muligt at angive egenskaber for emnet for eksempel for at angive standarden værdi time to live (TTL), der anvendes på meddelelser, der sendes til emnet. Disse indstillinger anvendes ved hjælp af klassen [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) . I følgende eksempel viser, hvordan du opretter et emne, der er navngivet **TestTopic** med en maksimal størrelse på 5 GB og standardmeddelelsen TTL på 1 minut.

```
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

> [AZURE.NOTE] Du kan bruge metoden [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) på [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) objekter til at kontrollere, om et emne med et angivet navn allerede findes i et navneområde.

## <a name="create-a-subscription"></a>Oprette et abonnement

Du kan også oprette emne abonnementer ved hjælp af klassen [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) . Abonnementer navngives og kan have et valgfrit filter, der begrænser sæt af meddelelser, der sendes til det abonnement virtuelle kø.

> [AZURE.IMPORTANT] I rækkefølge for meddelelser, der modtages af et abonnement, skal du oprette abonnementet før afsendelse af meddelelser til emnet. Hvis der ikke er nogen abonnementer til et emne, sletter emnet disse meddelelser.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Oprette et abonnement med standardfilteret (MatchAll)

Hvis Intet filter er angivet, når der oprettes et nyt abonnement, er filteret **MatchAll** standardfilteret, der bruges. Når du bruger filteret **MatchAll** , placeres alle meddelelser, der er publiceret på emnet i det abonnement virtuelle kø. I følgende eksempel oprettes et abonnement med navnet "AllMessages" og bruger **MatchAll** standardfilteret.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Oprette abonnementer med filtre

Du kan også konfigurere filtre, der gør det muligt at angive, hvilke meddelelser der sendes til et emne skal vises i et bestemt emne abonnement.

Den mest fleksible type filter, der understøttes af abonnementer er klassen [SqlFilter][] , som anvender et undersæt af SQL92. SQL filtre fungerer på egenskaberne for de meddelelser, der udgives på emnet. Du kan finde flere oplysninger om de udtryk, der kan bruges med en SQL-filter, [SqlFilter.SqlExpression][] syntaksen.

I følgende eksempel oprettes et abonnement med navnet **HighMessages** [SqlFilter][] -objekter, som kun markerer meddelelser, der har en brugerdefineret **MessageNumber** -egenskab, der er større end 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

På samme måde oprettes i følgende eksempel et abonnement med navnet **LowMessages** med [SqlFilter][] , der kun markerer meddelelser, der har en **MessageNumber** egenskab mindre end eller lig med 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Nu, hvor en meddelelse er sendt til `TestTopic`, det er altid leveret til modtagere, der er oprettet et abonnement på **AllMessages** emne abonnementet, og selektivt leveret til modtagere, der er oprettet et abonnement på **HighMessages** og **LowMessages** emne abonnementer (afhængigt af meddelelsesindhold).

## <a name="send-messages-to-a-topic"></a>Sende meddelelser til et emne

Hvis du vil sende en meddelelse til et emne, Service Bus, opretter dit program et [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) objekt ved hjælp af forbindelsesstrengen.

Følgende kode demonstrerer, hvordan du opretter et [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) objekt efter emnet **TestTopic** oprettet tidligere ved hjælp af den [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) API opkald.

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Meddelelser, der sendes til Service Bus emner er forekomster af klassen [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) objekter har et sæt standardegenskaber (såsom [etiket](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) og [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), en ordbog, der bruges til at holde brugerdefinerede program-specifikke egenskaber og en brødteksten i vilkårlig programmet data. Et program kan indstille brødteksten i meddelelsen ved at overføre serialiserbar objekter til parametre for objektet [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) , og den relevante **DataContractSerializer** bruges derefter til sekventielt objektet. Du kan også kan en **System.IO.Stream** leveres.

I følgende eksempel viser, hvordan til at sende fem testmeddelelser til objektet **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) , der fås i det forrige kodeeksempel. Bemærk, at [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) egenskabsværdi for hver meddelelse varierer afhængigt af gentagelsen af løkken (Dette bestemmer, hvilke abonnementer modtager den).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Service Bus emner understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i et emne, men der er en knop på den samlede størrelse af de meddelelser, som et emne. Dette emne størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB. Hvis partitionering er aktiveret, er den øvre grænse højere. Få mere at vide under [Partitioned beskeder enheder](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Hvordan du kan modtage meddelelser fra et abonnement

Den anbefalede måde at modtage meddelelser fra et abonnement er at bruge et [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) objekt. [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) objekter kan arbejde i to forskellige tilstande: [ *ReceiveAndDelete* og *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Når du bruger tilstanden **ReceiveAndDelete** modtager er en enkelt billede handling. Det vil sige, når Service Bus modtager en kvittering anmodning om en meddelelse i et abonnement, det markerer meddelelsen som der consumed og returnerer den til programmet. **ReceiveAndDelete** tilstand er den nemmeste model og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen, som consumed, når programmet genstarter og begynder forbrug meddelelser igen, vil gået glip, den meddelelse, der blev brugt før går ned.

I **PeekLock** -tilstand (som er standardtilstanden) Modtag processen bliver en handling af to faser, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at ringe [udført](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) på den modtagne meddelelse. Når Service Bus registrerer **færdigt** ringe, markerer meddelelsen som der consumed og fjerner den fra abonnementet.

I følgende eksempel vises, hvordan meddelelser kan modtages og behandles ved hjælp af **PeekLock** standardtilstanden. Hvis du vil angive en anden [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) værdi, kan du bruge en anden overbelastning for [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). I dette eksempel bruges [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) tilbagekald at behandle meddelelser, når de modtages i **HighMessages** abonnementet.

```
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

I dette eksempel konfigurerer den [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) tilbagekald ved hjælp af et [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) objekt. [Autofuldførelse](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) er indstillet til **Falsk** til at aktivere manuel styring af hvornår du skal ringe [udført](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) på den modtagne meddelelse. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) er angivet til 1 minut, hvilket medfører, at klienten at vente op til et minut før afslutning af funktionen automatisk fornyelse og klienten foretager et nyt opkald til at søge efter meddelelser. Denne egenskabsværdi reducerer antallet af gange klienten foretager fakturerbar kald, der ikke hente meddelelser.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan håndteres programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis et modtagelse program er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden [afbryde](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) på den modtagne meddelelse (i stedet for metoden [færdigt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ). Derved Service Bus låse op meddelelsen inden for abonnementet, og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i abonnementet, og hvis der opstår fejl i programmet til at behandle meddelelsen før Lås timeout udløber (for eksempel hvis programmet går ned), så Service Bus låser meddelelsen automatisk, og gør det tilgængeligt for modtagne igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før [færdigt](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) anmodningen er udstedt, meddelelsen skal være udleveres igen til programmet når genstartes. Dette kaldes ofte *mindst én gang behandling*; Det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af egenskaben [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) for e-mailen, der konstant på tværs af leveringsforsøg.

## <a name="delete-topics-and-subscriptions"></a>Slette emner og abonnementer

I følgende eksempel viser, hvordan til at slette emnet **TestTopic** fra navneområdet **HowToSample** tjeneste.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

Slette et emne, slettes også eventuelle abonnementer, der er registreret med emnet. Abonnementer kan også blive slettet uafhængigt af hinanden. Følgende kode demonstrerer, hvordan du kan slette et abonnement med navnet **HighMessages** fra **TestTopic** emne.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært det grundlæggende omkring af Service Bus emner og abonnementer, skal du følge disse links for at få mere at vide.

-   [Køer, emner og abonnementer][].
-   [Emne filtre eksempel][]
-   API reference til [SqlFilter][].
-   Opbygge et arbejde-program, der sender og modtager meddelelser til og fra en tjeneste Bus kø: [Service Bus formidlet SMS .NET selvstudium][].
-   Service Bus eksempler: hente fra [Azure eksempler][] eller få vist [Oversigt](service-bus-samples.md).

  [Azure-portalen]: https://portal.azure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
  [Emne filtre eksempel]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Service Bus formidlet SMS .NET-selvstudium]: service-bus-brokered-tutorial-dotnet.md
  [Azure eksempler]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
