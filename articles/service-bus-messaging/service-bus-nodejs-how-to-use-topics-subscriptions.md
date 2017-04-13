<properties 
    pageTitle="Sådan bruges Service Bus emner med Node.js | Microsoft Azure" 
    description="Lær at bruge Service Bus emner og abonnementer i Azure fra en Node.js app." 
    services="service-bus" 
    documentationCenter="nodejs" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Sådan Brug Service Bus emner og abonnementer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Denne vejledning beskriver, hvordan du bruger Service Bus emner og abonnementer fra Node.js programmer. De scenarier, der er omfattet omfatter **oprettelse af emner og abonnementer**, **du opretter abonnement filtre**, **sende meddelelser** til et emne, **modtager meddelelser fra et abonnement**og **slette emner og abonnementer**. Du kan finde flere oplysninger om emner og abonnementer, afsnittet [Næste trin](#next-steps) .

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-nodejs-application"></a>Oprette et Node.js program

Oprette en tom Node.js-program. Flere oplysninger om oprettelse af et Node.js program under [oprette og anvende et Node.js program til en Azure-websted], [Node.js skybaseret tjeneste][] ved hjælp af Windows PowerShell eller et websted med WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program bruge Service Bus

Hvis du vil bruge Service Bus, kan du hente pakken Node.js Azure. Pakken indeholder en række biblioteker, kommunikerer med tjenesten Bus RESTEN tjenester.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Brug Node pakke Manager (NPM) til at hente pakken

1.  Bruge en kommandolinjen som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix), gå til den mappe, hvor du har oprettet din Northwind.

2.  Skriv **npm installere azure** i kommandovinduet, som skal resultere i følgende output:

    ```
        azure@0.7.5 node_modules\azure
    ├── dateformat@1.0.2-1.2.3
    ├── xmlbuilder@0.4.2
    ├── node-uuid@1.2.0
    ├── mime@1.2.9
    ├── underscore@1.4.4
    ├── validator@1.1.1
    ├── tunnel@0.0.2
    ├── wns@0.5.3
    ├── xml2js@0.2.7 (sax@0.5.2)
    └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```

3.  Du kan manuelt køre kommandoen **ls** at kontrollere, at en **node\_moduler** mappe blev oprettet. Find den **azure** -pakke, som indeholder de biblioteker, du har brug for at få adgang til tjenesten Bus emner i denne mappe.

### <a name="import-the-module"></a>Importere modulet

Bruger Notesblok eller et andet tekstredigeringsprogram, skal du tilføje følgende til toppen af filen **server.js** af programmet:

```
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Konfigurere en forbindelse Service Bus

Modulet Azure læser miljøvariabler AZURE\_SERVICEBUS\_NAVNEOMRÅDE og AZURE\_SERVICEBUS\_ACCESS\_vigtige oplysninger, der kræves for at oprette forbindelse til tjenesten Bus. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger, når du ringer til **createServiceBusService**.

Du kan finde et eksempel på konfiguration miljøvariabler i en konfigurationsfil i en Azure skybaseret tjeneste, [Node.js skytjeneste med lagerplads][].

Du kan finde et eksempel på indstilling miljøvariabler i [Azure klassisk portal][] til et websted, der Azure, [Node.js webprogram med lagerplads][].

## <a name="create-a-topic"></a>Oprette et emne

Objektet **ServiceBusService** gør det muligt at arbejde med emner. Følgende kode opretter et **ServiceBusService** objekt. Tilføje den i øverst del af filen **server.js** efter sætningen importere azure modulet:

```
var serviceBusService = azure.createServiceBusService();
```

Det angivne emne returneres (hvis den findes), ved at ringe **createTopicIfNotExists** for objektet **ServiceBusService** , eller der oprettes et nyt emne med det angivne navn. Følgende kode bruger **createTopicIfNotExists** til at oprette eller oprette forbindelse til det emne, der hedder 'MyTopic':

```
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

**createServiceBusService** understøtter også flere indstillinger, som gør det muligt at tilsidesætte emne standardindstillingerne som meddelelse tid til live eller maksimale emne størrelse. I følgende eksempel angiver den maksimale emne til 5GB med et tidspunkt for live af 1 minut:

```
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filtre

Valgfri spamfiltrering handlinger kan anvendes på Handlinger, der udføres ved hjælp af **ServiceBusService**. Filtrering handlinger kan medtage logføring, automatisk prøver igen osv. Filtre er objekter, der implementerer en metode til signaturen:

```
function handle (requestOptions, next)
```

Når du har udført forbehandling på indstillingerne for anmodning om metoden kalder `next` der passerer et tilbagekald med den følgende signatur:

```
function (returnObject, finalCallback, next)
```

I denne tilbagekald, og efter behandling af **returnObject** (svar fra anmodningen på serveren), kalde tilbagekald behov for enten næste, hvis den findes for at fortsætte med at behandle andre filtre eller blot kalde **finalCallback** ellers for at afslutte op service aktiveringen.

To filtre, der implementerer forsøg er inkluderet med Azure SDK til Node.js, **ExponentialRetryPolicyFilter** og **LinearRetryPolicyFilter**. Følgende opretter en **ServiceBusService** -objekt, der bruger **ExponentialRetryPolicyFilter**:

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## <a name="create-subscriptions"></a>Oprette abonnementer

Emne abonnementer oprettes også sammen med objektet **ServiceBusService** . Abonnementer navngives og kan have et valgfrit filter, der begrænser sæt af meddelelser, der er leveret til det abonnement virtuelle kø.

> [AZURE.NOTE] Abonnementer er fast og fortsætter med at findes indtil enten de eller emnet de er forbundet med, slettes. Hvis dit program indeholder logik for at oprette et abonnement, skal den først Kontrollér, om abonnementet, der allerede findes ved hjælp af metoden **getSubscription** .

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Oprette et abonnement med standardfilteret (MatchAll)

Filteret **MatchAll** er standardfilteret, der bruges, hvis Intet filter er angivet, når der oprettes et nyt abonnement. Når filteret **MatchAll** bruges, placeres alle meddelelser, der er publiceret på emnet i det abonnement virtuelle kø. I følgende eksempel oprettes et abonnement med navnet 'AllMessages' og bruger **MatchAll** standardfilteret.

```
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Oprette abonnementer med filtre

Du kan også oprette filtre, der tillader dig at omfang som meddelelser sendes til et emne skal vises i et bestemt emne abonnement.

Den mest fleksible type filter, der understøttes af abonnementer er **SqlFilter**, som anvender et undersæt af SQL92. SQL filtre fungerer på egenskaberne for de meddelelser, der udgives på emnet. For flere oplysninger om de udtryk, der kan bruges med filteret SQL Gennemse [SqlFilter.SqlExpression] [ SqlFilter.SqlExpression] syntaks.

Filtre kan føjes til et abonnement ved hjælp af metoden **createRule** for objektet **ServiceBusService** . Denne metode kan du føje nye filtre til et eksisterende abonnement.

> [AZURE.NOTE] Fordi standardfilteret anvendes automatisk på alle nye abonnementer, skal du først fjerne standardfilteret eller **MatchAll** tilsidesætter andre filtre, som du kan angive. Du kan fjerne standardreglen ved hjælp af metoden **deleteRule** for objektet **ServiceBusService** .

I følgende eksempel oprettes et abonnement med navnet `HighMessages` med **SqlFilter** , der kun markerer meddelelser, der har en brugerdefineret **messagenumber** egenskab, der er større end 3:

```
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'HighMessages', 
            'HighMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

På samme måde i følgende eksempel oprettes et abonnement med navnet `LowMessages` med **SqlFilter** , der kun markerer meddelelser, der har en **messagenumber** egenskab mindre end eller lig med 3:

```
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages', 
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic', 
            'LowMessages', 
            'LowMessageFilter', 
            ruleOptions, 
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Når en meddelelse sendes nu til `MyTopic`, den altid vil blive leveret til modtagere, der er oprettet et abonnement på den `AllMessages` emne abonnement og selektivt leveret til modtagere, der er oprettet et abonnement på den `HighMessages` og `LowMessages` emne abonnementer (afhængigt af meddelelsesindhold).

## <a name="how-to-send-messages-to-a-topic"></a>Hvordan du kan sende meddelelser til et emne

For at sende en meddelelse til en tjeneste Bus emne, dit program skal du bruge metoden **sendTopicMessage** for objektet **ServiceBusService** .
Meddelelser, der sendes til Service Bus emner er **BrokeredMessage** objekter.
**BrokeredMessage** objekter har et sæt standardegenskaber (såsom **etiket** og **TimeToLive**), en ordbog, der bruges til at holde brugerdefineret program specifikke egenskaber og en brødteksten i strengdata. Et program kan indstille brødteksten i meddelelsen ved at overføre en strengværdi til **sendTopicMessage** og eventuelle nødvendige standardegenskaber udfyldes ved standardværdier.

I følgende eksempel viser, hvordan du kan sende fem test mails til 'MyTopic'. Bemærk, at værdien af hver meddelelse **messagenumber** egenskaben varierer på gentagelse i feltet Gentag (Dette afgør, hvilke abonnementer modtager den):

```
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus emner understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i et emne, men der er en knop på den samlede størrelse af de meddelelser, som et emne. Dette emne størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Modtage meddelelser fra et abonnement

Meddelelser er modtaget fra et abonnement, ved hjælp af metoden **receiveSubscriptionMessage** på **ServiceBusService** objektet. Som standard slettes meddelelser fra abonnementet, som de læses; Du kan læse (oversigten) og låse meddelelsen uden at slette den fra abonnementet ved at angive valgfri parameter **isPeekLock** til **Sand**.

Standardfunktionsmåden for læsning og slette meddelelsen som en del af modtag-handlingen er den nemmeste model, og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstarter og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

Hvis parameteren **isPeekLock** er indstillet til **Sand**, bliver Modtag en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet.
Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af processen modtag opkald **deleteMessage** metode og til at give meddelelsen, der skal slettes som en parameter. Metoden **deleteMessage** vil markere meddelelsen som der consumed og fjerne det fra abonnementet.

I følgende eksempel vises, hvordan meddelelser kan modtages og behandles ved hjælp af **receiveSubscriptionMessage**. Eksemplet først modtager og sletter en meddelelse fra 'LowMessages'-abonnement og derefter modtager en meddelelse fra 'HighMessages' abonnementet ved hjælp af **isPeekLock** indstillet til sand. Den sletter den meddelelse, ved hjælp af **deleteMessage**:

```
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        }
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan håndteres programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **unlockMessage** på **ServiceBusService** objektet. Dette medfører Service Bus låse op meddelelsen inden for abonnementet, og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i abonnementet, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), så Service Bus låser meddelelsen automatisk, og gør det tilgængeligt for modtagne igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før metoden **deleteMessage** kaldes, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst når behandling**, det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af egenskaben **MessageId** for e-mailen, der forbliver konstante på tværs af leveringsforsøg.

## <a name="delete-topics-and-subscriptions"></a>Slette emner og abonnementer

Emner og abonnementer er fast, og skal slettes eksplicit, enten via [Azure klassisk portal][] eller fra et program.
Følgende eksempel viser, hvordan du sletter det emne, der hedder `MyTopic`:

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

Slette et emne, slettes eventuelle abonnementer, der er registreret med emnet også. Abonnementer kan også blive slettet uafhængigt af hinanden. Følgende eksempel viser, hvordan du kan slette et abonnement med navnet `HighMessages` fra den `MyTopic` emne:

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus emner, skal du følge disse links for at få mere at vide.

-   Se [køer, emner og abonnementer][].
-   API reference til [SqlFilter][].
-   Besøg [Azure SDK for Node][] lager på GitHub.

  [Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
  [Azure klassisk portal]: https://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Node.js skytjeneste]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Oprette og installere et Node.js program til en Azure-websted]: ../app-service-web/web-sites-nodejs-develop-deploy-mac.md
  [Node.js skytjeneste med lagerplads]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
  [Node.js webprogram med lagerplads]: ../cloud-services/storage-nodejs-use-table-storage-cloud-service-app.md
 
