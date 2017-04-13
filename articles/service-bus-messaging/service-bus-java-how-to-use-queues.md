<properties
    pageTitle="Sådan bruges Service Bus køer med Java | Microsoft Azure"
    description="Lær at bruge Service Bus køer i Azure. Kodeeksempler, der er skrevet i Java."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-queues"></a>Sådan bruger du Service Bus køer

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I denne artikel beskrives, hvordan du bruger Service Bus køer. Eksemplerne er skrevet i Java og bruge [Azure SDK til Java][]. De scenarier, der er omfattet omfatter **oprettelse af køer**, **sende og modtage meddelelser**og **slette køer**.

## <a name="what-are-service-bus-queues"></a>Hvad er Bus servicekøer?

Service Bus køer understøtter en **formidlet messaging** kommunikation model. Når du bruger køer, Kommuniker komponenterne i et distribueret program ikke direkte med hinanden, dvs. i stedet for exchange de-meddelelser via en kø, der fungerer som mellemliggende (broker). En meddelelse producent (afsenderen) hænder fra en meddelelse til køen og fortsætter derefter dens behandling.
Asynkront, en meddelelse forbruger (modtager) trækker meddelelsen fra køen og behandler den. Producenten har ikke at vente et svar fra forbrugeren for at fortsætte med at behandle og sende meddelelser til yderligere. Køer tilbyder **første i skal du først ud (FIFU)** levering af meddelelser til en eller flere konkurrerende forbrugere. Meddelelser er det vil sige, typisk modtaget og behandles af modtagere i den rækkefølge, hvori de blev føjet til køen, og alle meddelelser er modtaget og behandles som en enkelt meddelelse forbruger.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

Service Bus køer er en generel teknologi, der kan bruges til en bred vifte af scenarier:

- Kommunikation mellem internettet og arbejder roller i et med flere lag Azure-program.
- Kommunikation mellem lokale apps og Azure hostet apps i en hybridløsning.
- Kommunikation mellem komponenterne i et distribueret program, der kører lokalt i forskellige organisationer eller afdelinger af en organisation.

Brug af køer gør det muligt at skalere ud af dine programmer nemmere, og Aktivér fleksibilitet i din arkitektur.

## <a name="create-a-service-namespace"></a>Oprette et service navneområde

Hvis du vil begynde at bruge Service Bus køer i Azure, skal du først oprette et navneområde. Et navneområde indeholder en angiver området objektbeholder til at håndtere Service Bus ressourcer i dit program.

Sådan oprettes et navneområde:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program bruge Service Bus

Sørg for, at du har installeret [Azure SDK til Java][] før du kan bygge dette eksempel. Hvis du bruger Eklipse, kan du installere [Azure-værktøjskassen til Eklipse][] , der indeholder Azure SDK til Java. Du kan derefter tilføje **Microsoft Azure-biblioteker til Java** til dit projekt:

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Tilføj følgende `import` sætninger til toppen af filen Java:

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## <a name="create-a-queue"></a>Oprette en kø

Administration af handlinger for tjenesten Bus køer kan udføres via klassen **ServiceBusContract** . Et **ServiceBusContract** objekt er opbygget med en passende konfiguration, indkapsler SAS token med tilladelser til at administrere den, og klassen **ServiceBusContract** er det eneste udgangspunktet for kommunikation med Azure.

Klassen **ServiceBusService** indeholder metoder til at oprette, optælle og slette køer. I eksemplet nedenfor viser, hvordan et **ServiceBusService** objekt kan bruges til at oprette en kø med navnet "TestQueue" med et navneområde med navnet "HowToSample":

```
Configuration config =
    ServiceBusConfiguration.configureWithSASAuthentication(
            "HowToSample",
            "RootManageSharedAccessKey",
            "SAS_key_value",
            ".servicebus.windows.net"
            );

ServiceBusContract service = ServiceBusService.create(config);
QueueInfo queueInfo = new QueueInfo("TestQueue");
try
{
    CreateQueueResult result = service.createQueue(queueInfo);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Der er metoder på **QueueInfo** , der tillader egenskaber for køen at finjustere (for eksempel: angive time to live (TTL) standardværdien skal anvendes på meddelelser, der sendes til køen). Følgende eksempel viser, hvordan du opretter en kø med navnet `TestQueue` med en maksimal størrelse på 5GB:

````
long maxSizeInMegabytes = 5120;
QueueInfo queueInfo = new QueueInfo("TestQueue");
queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
CreateQueueResult result = service.createQueue(queueInfo);
````

Bemærk, at du kan bruge metoden **listQueues** på **ServiceBusContract** objekter og Kontrollér, om en kø med et angivet navn allerede findes i en tjeneste navneområdet.

## <a name="send-messages-to-a-queue"></a>Sende meddelelser til en kø

Hvis du vil sende en meddelelse til en tjeneste Bus kø, henter dit program et **ServiceBusContract** objekt. Følgende kode viser, hvordan du kan sende en meddelelse den `TestQueue` kø, der tidligere blev oprettet i den `HowToSample` navneområde:

```
try
{
    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendQueueMessage("TestQueue", message);
}
catch (ServiceException e)
{
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

Meddelelser sendes til, og har modtaget fra Service Bus køer er forekomster af klassen [BrokeredMessage][] . [BrokeredMessage][] objekter har et sæt standardegenskaber (såsom [etiket](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) og [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), en ordbog, der bruges til at holde brugerdefineret program specifikke egenskaber og en brødteksten i vilkårlig programmet data. Et program kan indstille brødteksten i meddelelsen ved at overføre serialiserbar objekter i constructor i [BrokeredMessage][], og den relevante serialiseringsfunktion derefter bruges til at sekventielt objektet. Alternativt kan du angive en **java. EY. InputStream** objekt.

Følgende eksempel viser, hvordan du kan sende fem test mails til den `TestQueue` **MessageSender** vi der fås i det forrige kodestykke:

```
for (int i=0; i<5; i++)
{
     // Create message, passing a string message for the body.
     BrokeredMessage message = new BrokeredMessage("Test message " + i);
     // Set an additional app-specific property.
     message.setProperty("MyProperty", i);
     // Send message to the queue
     service.sendQueueMessage("TestQueue", message);
}
```

Service Bus køer understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i en kø, men der er en knop på den samlede størrelse af de meddelelser, som en kø. Denne kø størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB.

## <a name="receive-messages-from-a-queue"></a>Modtage meddelelser fra en kø

Den primære måde at modtage meddelelser fra en kø er at bruge et **ServiceBusContract** objekt. Modtagne meddelelser kan arbejde i to forskellige tilstande: **ReceiveAndDelete** og **PeekLock**.

Når bruger tilstanden **ReceiveAndDelete** modtager er en handling af enkelt billede – det vil sige, når Service Bus modtager en kvittering anmodning om en meddelelse i en kø, det markerer meddelelsen som bliver brugt, og returnerer den til programmet. **ReceiveAndDelete** -tilstand (som er standardtilstanden) er den nemmeste model og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den.
Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstartes og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

I **PeekLock** tilstand modtage bliver en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at ringe **slette** på den modtagne meddelelse. Når Service Bus registrerer **slette** opkaldet, den markere meddelelsen som der consumed og fjerne den fra køen.

I følgende eksempel vises, hvordan meddelelser kan modtages og behandles ved hjælp af **PeekLock** tilstand (ikke standardtilstanden). Eksemplet herunder bruges en uendelig løkke og behandler meddelelser, når de modtages i vores "TestQueue":

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
         ReceiveQueueMessageResult resultQM =
                service.receiveQueueMessage("TestQueue", opts);
        BrokeredMessage message = resultQM.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the queue message.
            System.out.print("From queue: ");
            byte[] b = new byte[200];
            String s = null;
            int numRead = message.getBody().read(b);
            while (-1 != numRead)
            {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
            }
            System.out.println();
            System.out.println("Custom Property: " +
                message.getProperty("MyProperty"));
            // Remove message from queue.
            System.out.println("Deleting this message.");
            //service.deleteMessage(message);
        }  
        else  
        {
            System.out.println("Finishing up - no more messages.");
            break;
            // Added to handle no more messages.
            // Could instead wait for more messages to be added.
        }
    }
}
catch (ServiceException e) {
    System.out.print("ServiceException encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
catch (Exception e) {
    System.out.print("Generic exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sådan håndteres programmet går ned, og ikke kan læses meddelelser

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **unlockMessage** på den modtagne meddelelse (i stedet for metoden **deleteMessage** ). Dette medfører Service Bus at låse op meddelelse i køen og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i køen, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (f.eks., hvis programmet går ned), og derefter Service Bus vil låse op meddelelsen automatisk og gøre det tilgængeligt skal modtages igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før **deleteMessage** anmodningen er udstedt, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst når behandling**, det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af metoden **getMessageId** af e-mailen, der forbliver konstante på tværs af leveringsforsøg.

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus køer, kan du få flere oplysninger i [køer, emner og abonnementer][] .

Du kan finde yderligere oplysninger finder [Java Developer Center](/develop/java/).


  [Azure SDK til Java]: http://azure.microsoft.com/develop/java/
  [Azure-værktøjskassen til Eklipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx

