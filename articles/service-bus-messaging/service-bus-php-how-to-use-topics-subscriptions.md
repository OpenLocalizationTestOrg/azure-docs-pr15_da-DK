<properties 
    pageTitle="Sådan bruges Service Bus emner med PHP | Microsoft Azure" 
    description="Lær at bruge Service Bus emner med PHP i Azure." 
    services="service-bus" 
    documentationCenter="php" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="sethm"/>


# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Sådan bruges Service Bus emner og abonnementer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

I denne artikel viser, hvordan du bruger Service Bus emner og abonnementer. Eksemplerne er skrevet i PHP og bruge [Azure SDK til PHP](../php-download-sdk.md). De scenarier, der er dækket omfatter **oprettelse af emner og abonnementer**, **oprette abonnement filtre**, **sende meddelelser til et emne**, **modtager meddelelser fra et abonnement**og **slette emner og abonnementer**.

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="create-a-php-application"></a>Oprette et PHP tjenesteprogram

Det eneste krav til oprettelse af et PHP-program, der har adgang til tjenesten Azure Blob er refereres klasser i [Azure SDK til PHP](../php-download-sdk.md) fra i din kode til. Du kan bruge en hvilken som helst udviklingsværktøjer til at oprette dit program eller Notesblok.

> [AZURE.NOTE] Installationen af PHP skal også have [OpenSSL lokalnummer](http://php.net/openssl) installeret og aktiveret.

I denne artikel beskrives, hvordan du bruger service-funktioner, der kan ringes i et PHP program lokalt eller i kode, der kører en Azure web rolle, arbejder rolle eller websted.

## <a name="get-the-azure-client-libraries"></a>Få Azure klienten biblioteker

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program bruge Service Bus

For at bruge tjenesten Bus API'er:

1. Reference til automatisk indlæser filen ved hjælp af [require_once] [ require-once] sætning.
2. Henvise til en hvilken som helst klasser, du kan bruge.

I følgende eksempel viser, hvordan du medtage filen automatisk indlæser og henviser til klassen **ServiceBusService** .

> [AZURE.NOTE] I dette eksempel (og andre eksemplerne i denne artikel) antages, at du har installeret PHP klient billedbiblioteker til Azure via komponist. Hvis du har installeret bibliotekerne manuelt eller som en PÆRETRÆER pakke, skal du henvise **WindowsAzure.php** automatisk indlæser filen.

```
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I følgende eksempler på `require_once` sætning altid vises, men kun de klasser, der er nødvendige for eksempel at udføre der refereres til.

## <a name="set-up-a-service-bus-connection"></a>Konfigurere en forbindelse Service Bus

Hvis du vil oprette en forekomst af en Service Bus klient skal du først har en gyldig forbindelsesstreng i dette format:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Hvor `Endpoint` er typisk af formatet `https://[yourNamespace].servicebus.windows.net`.

Hvis du vil oprette en hvilken som helst Azure service-klienten skal du bruge klassen **ServicesBuilder** . Du kan:

* Overføre forbindelsesstrengen direkte til den.
* Bruge **CloudConfigurationManager (CCM)** til at kontrollere flere eksterne kilder til forbindelsesstrengen:
    * Som standard leveres den med understøttelse af en ekstern kilde - miljøet variabler.
    * Du kan tilføje nye kilder ved at forlænge klassen **ConnectionStringSource** .

Eksempler, der er beskrevet her, overføres forbindelsesstrengen direkte.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
    
$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Oprette et emne

Du kan udføre administration handlinger for tjenesten Bus emner via klassen **ServiceBusRestProxy** . Et **ServiceBusRestProxy** objekt er opbygget via metoden **ServicesBuilder::createServiceBusService** fabrik med en passende forbindelsesstreng, der indkapsler token tilladelser til at administrere den.

Følgende eksempel viser, hvordan du kan oprette en forekomst af en **ServiceBusRestProxy** og opkald- **ServiceBusRestProxy -> createTopic** for at oprette et emne med navnet `mytopic` inden for en `MySBNamespace` navneområde:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;
    
// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [AZURE.NOTE] Du kan bruge den `listTopics` metode på `ServiceBusRestProxy` objekter til at kontrollere, om et emne med et angivet navn allerede findes i en tjeneste navneområdet.

## <a name="create-a-subscription"></a>Oprette et abonnement

Emne abonnementer er også oprettet med metoden **ServiceBusRestProxy -> createSubscription** . Abonnementer navngives og kan have et valgfrit filter, der begrænser sæt af meddelelser, der sendes til det abonnement virtuelle kø.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Oprette et abonnement med standardfilteret (MatchAll)

Filteret **MatchAll** er standardfilteret, der bruges, hvis Intet filter er angivet, når der oprettes et nyt abonnement. Når filteret **MatchAll** bruges, placeres alle meddelelser, der er publiceret på emnet i det abonnement virtuelle kø. I følgende eksempel oprettes et abonnement med navnet 'mysubscription' og bruger **MatchAll** standardfilteret.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Oprette abonnementer med filtre

Du kan også konfigurere filtre, der gør det muligt at angive, hvilke meddelelser der sendes til et emne skal vises i et bestemt emne abonnement. Den mest fleksible type filter, der understøttes af abonnementer er **SqlFilter**, som anvender et undersæt af SQL92. SQL filtre fungerer på egenskaberne for de meddelelser, der udgives på emnet. Du kan finde flere oplysninger om SqlFilters, se [SqlFilter.SqlExpression egenskaben][sqlfilter].

> [AZURE.NOTE] Hver regel på et abonnement behandler indgående meddelelser uafhængigt af hinanden, tilføje deres Resultatmeddelelser til abonnementet. Desuden har hvert nyt abonnement et standard **regel** objekt med et filter, der tilføjer alle meddelelser fra emnet til abonnementet. Hvis du vil modtage kun meddelelser, der passer til dine filteret, skal du fjerne standardreglen. Du kan fjerne standardreglen ved hjælp af den `ServiceBusRestProxy->deleteRule` metode.

I følgende eksempel oprettes et abonnement med navnet **HighMessages** med **SqlFilter** , der kun markerer meddelelser, der har en brugerdefineret **MessageNumber** -egenskab, der er større end 3 (se [sende meddelelser til et emne](#send-messages-to-a-topic) for at få oplysninger om at tilføje brugerdefinerede egenskaber i meddelelser):

```
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Bemærk, at denne kode kræver brug af et ekstra navneområde: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

På samme måde oprettes i følgende eksempel et abonnement med navnet **LowMessages** med **SqlFilter** , der kun markerer meddelelser, der har en **MessageNumber** egenskab mindre end eller lig med 3:

```
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Nu, hvor en meddelelse er sendt til den `mytopic` emne, det er altid leveret til modtagere, der er oprettet et abonnement på den `mysubscription` -abonnement og selektivt leveret til modtagere, der er oprettet et abonnement på den `HighMessages` og `LowMessages` abonnementer (afhængigt af meddelelsesindhold).

## <a name="send-messages-to-a-topic"></a>Sende meddelelser til et emne

Hvis du vil sende en meddelelse til en tjeneste Bus emne, kalder dit program metoden **ServiceBusRestProxy -> sendTopicMessage** . Følgende kode viser, hvordan du kan sende en meddelelse til den `mytopic` emne, der tidligere har oprettet i den `MySBNamespace` service navneområde.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");
    
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Meddelelser, der sendes til Service Bus emner er forekomster af klassen **BrokeredMessage** . **BrokeredMessage** objekter har et sæt standardegenskaber og metoder (såsom **getLabel**, **getTimeToLive**, **setLabel**og **setTimeToLive**), samt egenskaber, der kan bruges til at holde brugerdefinerede program-specifikke egenskaber. I følgende eksempel viser, hvordan du sender 5 testmeddelelser til den `mytopic` emne, der tidligere har oprettet. Metoden **AngivEgenskab** bruges til at tilføje en brugerdefineret egenskab (`MessageNumber`) til hver meddelelse. Bemærk, at den `MessageNumber` egenskabsværdi varierer i hver meddelelse (du kan bruge denne værdi til at bestemme, hvilke abonnementer modtager, som vist i afsnittet [oprette et abonnement](#create-a-subscription) ):

```
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);
            
    // Set custom property.
    $message->setProperty("MessageNumber", $i);
            
    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus emner understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i et emne, men der er en knop på den samlede størrelse af de meddelelser, som et emne. Denne øvre grænse på emne størrelse er 5 GB. Du kan finde flere oplysninger om kvoter, [Service Bus kvoter][].

## <a name="receive-messages-from-a-subscription"></a>Modtage meddelelser fra et abonnement

Den bedste måde at modtage meddelelser fra et abonnement er at bruge en **ServiceBusRestProxy -> receiveSubscriptionMessage** metode. Modtagne meddelelser kan arbejde i to forskellige tilstande: **ReceiveAndDelete** (standard) og **PeekLock**.

Når du bruger tilstanden **ReceiveAndDelete** modtager er en enkelt billede handling. Det vil sige, når Service Bus modtager en kvittering anmodning om en meddelelse i et abonnement, det markerer meddelelsen som der consumed og returnerer den til programmet. **ReceiveAndDelete** tilstand er den nemmeste model og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstarter og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

I **PeekLock** tilstand bliver fejlmeddelelsen vises en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at overføre den modtagne meddelelse til **ServiceBusRestProxy -> deleteMessage**. Når Service Bus registrerer **deleteMessage** ringe, den markere meddelelsen som der consumed og fjerne den fra køen.

I følgende eksempel vises, hvordan du kan modtage og behandle en meddelelse ved hjælp af **PeekLock** tilstand (ikke standardtilstanden). 

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
    
    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/azure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan: håndtere programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **unlockMessage** på den modtagne meddelelse (i stedet for metoden **deleteMessage** ). Dette medfører Service Bus at låse op meddelelse i køen og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i køen, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), og derefter Service Bus vil låse op meddelelsen automatisk og gøre det tilgængeligt skal modtages igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før **deleteMessage** anmodningen er udstedt, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst én gang behandling**; Det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til programmer til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af metoden **getMessageId** af e-mailen, der konstant på tværs af leveringsforsøg.

## <a name="delete-topics-and-subscriptions"></a>Slette emner og abonnementer

Brug den- **ServiceBusRestProxy -> deleteTopic** eller metoder **ServiceBusRestProxy -> deleteSubscripton** henholdsvis for at slette et emne eller et abonnement. Bemærk, at slette et emne også slettes eventuelle abonnementer, der er registreret med emnet.

Følgende eksempel viser, hvordan du kan slette et emne, der hedder `mytopic` og dens registrerede abonnementer.

```
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {       
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/azure/dd179357
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Du kan slette et abonnement uafhængigt af hinanden ved hjælp af metoden **deleteSubscription** :

```
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus køer, kan du få flere oplysninger i [køer, emner og abonnementer][] .

[Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[require-once]: http://php.net/require_once
[Service Bus kvoter]: service-bus-quotas.md
