<properties 
    pageTitle="Sådan bruges Service Bus køer med PHP | Microsoft Azure" 
    description="Lær at bruge Service Bus køer i Azure. Kodeeksempler, der er skrevet i PHP." 
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
    ms.date="10/04/2016" 
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Sådan bruger du Service Bus køer

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Denne vejledning viser, hvordan du bruger Service Bus køer. Eksemplerne er skrevet i PHP og bruge [Azure SDK til PHP](../php-download-sdk.md). De scenarier, der er omfattet omfatter **oprettelse af køer**, **sende og modtage meddelelser**og **slette køer**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="create-a-php-application"></a>Oprette et PHP tjenesteprogram

Det eneste krav til oprettelse af et PHP-program, der har adgang til tjenesten Azure Blob er refererer til klasser i [Azure SDK til PHP](../php-download-sdk.md) fra i din kode. Du kan bruge en hvilken som helst udviklingsværktøjer til at oprette dit program eller Notesblok.

> [AZURE.NOTE] Installationen af PHP skal også have [OpenSSL lokalnummer](http://php.net/openssl) installeret og aktiveret.

I denne vejledning, skal bruge du servicefunktioner, der kan kaldes fra, i et PHP program lokalt eller i kode, der kører en Azure web rolle, arbejder rolle eller websted.

## <a name="get-the-azure-client-libraries"></a>Få Azure klienten biblioteker

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program bruge Service Bus

Hvis du vil bruge Service Bus køen API'er, skal du gøre følgende:

1. Reference til automatisk indlæser filen ved hjælp af [require_once] [ require_once] sætning.
2. Henvise til en hvilken som helst klasser, du kan bruge.

I følgende eksempel viser, hvordan du medtage filen automatisk indlæser og henviser til klassen **ServicesBuilder** .

> [AZURE.NOTE] I dette eksempel (og andre eksemplerne i denne artikel) antages, at du har installeret PHP klient billedbiblioteker til Azure via komponist. Hvis du har installeret bibliotekerne manuelt eller som en PÆRETRÆER pakke, skal du henvise **WindowsAzure.php** automatisk indlæser filen.

```
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

I eksemplerne nedenfor, den `require_once` sætning altid vises, men kun de klasser, der er nødvendige for eksempel at udføre der refereres til.

## <a name="set-up-a-service-bus-connection"></a>Konfigurere en forbindelse Service Bus

Hvis du vil oprette en forekomst af en Service Bus klient, skal du først har en gyldig forbindelsesstreng i dette format:

```
Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]
```

Hvor **slutpunkt** er typisk af formatet `[yourNamespace].servicebus.windows.net`.

Hvis du vil oprette en hvilken som helst Azure service-klienten skal du bruge klassen **ServicesBuilder** . Du kan:

* Overføre forbindelsesstrengen direkte til den.
* Bruge **CloudConfigurationManager (CCM)** til at kontrollere flere eksterne kilder til forbindelsesstrengen:
    * Som standard leveres den med understøttelse af en ekstern kilde - miljøet variabler
    * Du kan tilføje nye kilder ved at forlænge klassen **ConnectionStringSource**

Eksempler, der er beskrevet her, sendes forbindelsesstrengen direkte.

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="how-to-create-a-queue"></a>Sådan: oprette en kø

Du kan udføre administration handlinger for tjenesten Bus køer via klassen **ServiceBusRestProxy** . Et **ServiceBusRestProxy** objekt er opbygget via metoden **ServicesBuilder::createServiceBusService** fabrik med en passende forbindelsesstreng, der indkapsler token tilladelser til at administrere den.

Følgende eksempel viser, hvordan du kan oprette en forekomst af en **ServiceBusRestProxy** , og kald- **ServiceBusRestProxy -> createQueue** for at oprette en kø med navnet `myqueue` inden for en `MySBNamespace` service navneområde:

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
    
try {
    $queueInfo = new QueueInfo("myqueue");
        
    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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

> [AZURE.NOTE] Du kan bruge den `listQueues` metode på `ServiceBusRestProxy` objekter til at kontrollere, om en kø med et angivet navn allerede findes i et navneområde.

## <a name="how-to-send-messages-to-a-queue"></a>Sådan: sende meddelelser til en kø

Hvis du vil sende en meddelelse til en tjeneste Bus kø, kalder dit program metoden **ServiceBusRestProxy -> sendQueueMessage** . Følgende kode viser, hvordan du kan sende en meddelelse til den `myqueue` kø tidligere har oprettet i den `MySBNamespace` service navneområde.

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
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // http://msdn.microsoft.com/library/windowsazure/hh780775
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Meddelelser, der er sendt til (og modtaget fra) Service Bus køer er forekomster af klassen **BrokeredMessage** . **BrokeredMessage** objekter har et sæt standard metoder (såsom **getLabel**, **getTimeToLive**, **setLabel**og **setTimeToLive**) og egenskaber, der bruges til at holde brugerdefineret program-specifikke egenskaber og helhed af vilkårlig application data.

Service Bus køer understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i en kø, men der er en knop på den samlede størrelse af de meddelelser, som en kø. Denne øvre grænse på kø størrelse er 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Hvordan du kan modtage meddelelser fra en kø

Den bedste måde at modtage meddelelser fra en kø er at bruge en **ServiceBusRestProxy -> receiveQueueMessage** metode. Meddelelser kan modtages i to forskellige tilstande: **ReceiveAndDelete** (standard) og **PeekLock**.

Når bruger tilstanden **ReceiveAndDelete** modtager er en handling af enkelt billede – det vil sige, når Service Bus modtager en kvittering anmodning om en meddelelse i en kø, det markerer meddelelsen som bliver brugt, og returnerer den til programmet. **ReceiveAndDelete** tilstand er den nemmeste model og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstartes og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

I **PeekLock** tilstand bliver fejlmeddelelsen vises en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre, at andre forbrugere modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at overføre den modtagne meddelelse til **ServiceBusRestProxy -> deleteMessage**. Når Service Bus registrerer **deleteMessage** ringe, den markere meddelelsen som der consumed og fjerne den fra køen.

I følgende eksempel viser, hvordan en meddelelse kan modtages og behandlet ved hjælp af **PeekLock** tilstand (ikke standardtilstanden).

```
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
try {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();
        
    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";
        
    /*---------------------------
        Process message here.
    ----------------------------*/
        
    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // http://msdn.microsoft.com/library/windowsazure/hh780735
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan: håndtere programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **unlockMessage** på den modtagne meddelelse (i stedet for metoden **deleteMessage** ). Dette medfører Service Bus at låse op meddelelse i køen og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i køen, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), og derefter Service Bus vil låse op meddelelsen automatisk og gøre det tilgængeligt skal modtages igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før **deleteMessage** anmodningen er udstedt, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst én gang behandling**; Det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, anbefales derefter tilføje yderligere logik til programmer til at håndtere dublerede meddelelseslevering. Dette er ofte opnået ved hjælp af metoden **getMessageId** af e-mailen, der konstant på tværs af leveringsforsøg.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus køer, kan du få flere oplysninger i [køer, emner og abonnementer][] .

Yderligere oplysninger finder du også se [PHP Developer Center](/develop/php/).

[Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
[require_once]: http://php.net/require_once

 
