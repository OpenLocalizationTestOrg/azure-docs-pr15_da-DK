<properties
    pageTitle="Sådan bruges Service Bus emner med Java | Microsoft Azure"
    description="Lær at bruge Service Bus emner og abonnementer i Azure. Kodeeksempler, der er skrevet til Java-programmer."
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="sethm"/>

# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Sådan bruges Service Bus emner og abonnementer

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Denne vejledning beskriver, hvordan du bruger Service Bus emner og abonnementer. Eksemplerne er skrevet i Java og bruge [Azure SDK til Java][]. De scenarier, der er dækket omfatter **oprettelse af emner og abonnementer**, **oprette abonnement filtre**, **sende meddelelser til et emne**, **modtager meddelelser fra et abonnement**og **slette emner og abonnementer**.

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Hvad er Service Bus emner og abonnementer?

Service Bus emner og abonnementer understøtter en *udgivelse/abonnement* messaging kommunikation model. Når du bruger emner og abonnementer, Kommuniker komponenterne i et distribueret program ikke direkte med hinanden, dvs. i stedet for exchange de meddelelser via et emne, der fungerer som mellemliggende.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Indeholder en "en-til-mange"-form for kommunikation, ved hjælp af en Udgiv/Abonner mønster udtryk Service Bus køer, hvor meddelelserne behandles af en enkelt forbruger, emner og abonnementer. Det er muligt at registrere flere abonnementer til et emne. Når en meddelelse er sendt til et emne, er det derefter gøres tilgængelige for hvert abonnement til håndtaget/arbejde uafhængigt af hinanden.

Et abonnement til et emne ligner en virtuel kø, der modtager kopier af de meddelelser, der blev sendt til emnet. Du kan også kan du registrere filterregler for et emne på grundlag abonnement, som gør det muligt at filtrere/begrænse, hvilke meddelelser der skal et emne modtages af abonnementer, emne.

Service Bus emner og abonnementer gør det muligt at skalere til at behandle en meget store antallet af beskeder på tværs af en meget store antallet af brugere og programmer.

## <a name="create-a-service-namespace"></a>Oprette et service navneområde

Hvis du vil begynde at bruge Service Bus emner og abonnementer i Azure, skal du først oprette en tjeneste navneområde. Et navneområde indeholder en angiver området objektbeholder til at håndtere Service Bus ressourcer i dit program.

Sådan oprettes et navneområde:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurere dit program bruge Service Bus

Sørg for, at du har installeret [Azure SDK til Java][] før du kan bygge dette eksempel. Hvis du bruger Eklipse, kan du installere [Azure-værktøjskassen til Eklipse][] , der indeholder Azure SDK til Java. Du kan derefter tilføje **Microsoft Azure-biblioteker til Java** til dit projekt:

![](media/service-bus-java-how-to-use-topics-subscriptions/eclipselibs.png)

Tilføj følgende Importér udtalelser til toppen af filen Java:

```
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

Føje Azure-biblioteker til Java til din build sti og medtage dem i dit projekt installation samling.

## <a name="create-a-topic"></a>Oprette et emne

Administration af handlinger for tjenesten Bus emner kan udføres via klassen **ServiceBusContract** . Et **ServiceBusContract** objekt er opbygget med en passende konfiguration, indkapsler SAS token med tilladelser til at administrere den, og klassen **ServiceBusContract** er det eneste udgangspunktet for kommunikation med Azure.

Klassen **ServiceBusService** indeholder metoder til at oprette, optælle og slette emner. I følgende eksempel viser, hvordan et **ServiceBusService** objekt kan bruges til at oprette et emne, der hedder `TestTopic`, med et navneområde, kaldet `HowToSample`:

    Configuration config =
        ServiceBusConfiguration.configureWithSASAuthentication(
          "HowToSample",
          "RootManageSharedAccessKey",
          "SAS_key_value",
          ".servicebus.windows.net"
          );

    ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
    try  
    {
        CreateTopicResult result = service.createTopic(topicInfo);
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

Der er metoder på **TopicInfo** , der aktiverer egenskaberne for emnet, der skal angives (for eksempel: angive time to live (TTL) standardværdien skal anvendes på meddelelser, der sendes til emnet). Følgende eksempel viser, hvordan du opretter et emne, der hedder `TestTopic` med en maksimal størrelse på 5 GB:

    long maxSizeInMegabytes = 5120;  
    TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateTopicResult result = service.createTopic(topicInfo);

Bemærk, at du kan bruge metoden **listTopics** på **ServiceBusContract** objekter og Kontrollér, om et emne med et angivet navn allerede findes i en tjeneste navneområdet.

## <a name="create-subscriptions"></a>Oprette abonnementer

Abonnementer til emner, der også oprettes med klassen **ServiceBusService** . Abonnementer navngives og kan have et valgfrit filter, der begrænser sæt af meddelelser, der sendes til det abonnement virtuelle kø.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Oprette et abonnement med standardfilteret (MatchAll)

Filteret **MatchAll** er standardfilteret, der bruges, hvis Intet filter er angivet, når der oprettes et nyt abonnement. Når filteret **MatchAll** bruges, placeres alle meddelelser, der er publiceret på emnet i det abonnement virtuelle kø. I følgende eksempel oprettes et abonnement med navnet "AllMessages" og bruger **MatchAll** standardfilteret.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result =
        service.createSubscription("TestTopic", subInfo);

### <a name="create-subscriptions-with-filters"></a>Oprette abonnementer med filtre

Du kan også oprette filtre, der giver mulighed for at omfang, som du sender til et emne skal vises i et bestemt emne abonnement.

Den mest fleksible type filter, der understøttes af abonnementer er [SqlFilter][], som anvender et undersæt af SQL92. SQL filtre fungerer på egenskaberne for de meddelelser, der udgives på emnet. Gennemse [SqlFilter.SqlExpression][] syntaksen for flere oplysninger om de udtryk, der kan bruges med en SQL-filter.

I følgende eksempel oprettes et abonnement med navnet `HighMessages` [SqlFilter][] -objekter, som kun markerer meddelelser, der har en brugerdefineret **MessageNumber** -egenskab, der er større end 3:

```
// Create a "HighMessages" filtered subscription  
SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "HighMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "HighMessages", "$Default");
```

På samme måde i følgende eksempel oprettes et abonnement med navnet `LowMessages` [SqlFilter][] -objekter, som kun markerer meddelelser, der har en **MessageNumber** egenskab mindre end eller lig med 3:

```
// Create a "LowMessages" filtered subscription
SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
CreateSubscriptionResult result = service.createSubscription("TestTopic", subInfo);
RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
CreateRuleResult ruleResult = service.createRule("TestTopic", "LowMessages", ruleInfo);
// Delete the default rule, otherwise the new rule won't be invoked.
service.deleteRule("TestTopic", "LowMessages", "$Default");
```

Når en meddelelse sendes nu til `TestTopic`, den altid vil blive leveret til modtagere, der er oprettet et abonnement på den `AllMessages` -abonnement og selektivt leveret til modtagere, der er oprettet et abonnement på den `HighMessages` og `LowMessages` abonnementer (afhængigt af meddelelsesindhold).

## <a name="send-messages-to-a-topic"></a>Sende meddelelser til et emne

Hvis du vil sende en meddelelse til et emne, Service Bus, henter dit program et **ServiceBusContract** objekt. Følgende kode demonstrerer, hvordan du kan sende en meddelelse den `TestTopic` emne, der tidligere har oprettet i den `HowToSample` navneområde:

```
BrokeredMessage message = new BrokeredMessage("MyMessage");
service.sendTopicMessage("TestTopic", message);
```

Meddelelser, der sendes til tjenesten Bus emner er forekomster af klassen [BrokeredMessage][] . [BrokeredMessage][] *objekter har et sæt standard metoder (såsom * *setLabel* * og * *TimeToLive**), en ordbog, der bruges til at holde brugerdefinerede program-specifikke egenskaber og en brødteksten i vilkårlig programmet data. Et program kan indstille brødteksten i meddelelsen ved at overføre serialiserbar objekter i constructor i [BrokeredMessage][], og de relevante * *DataContractSerializer* * derefter bruges til at sekventielt objektet. Du kan også en * *java.io.InputStream** kan leveres.

Følgende eksempel viser, hvordan du kan sende fem test mails til den `TestTopic` **MessageSender** vi der fås i kodestykket ovenfor.
Bemærk, hvordan **MessageNumber** egenskabsværdi for hver meddelelse varierer på gentagelse i feltet Gentag (Dette afgør, hvilke abonnementer modtager den):

```
for (int i=0; i<5; i++)  {
// Create message, passing a string message for the body
BrokeredMessage message = new BrokeredMessage("Test message " + i);
// Set some additional custom app-specific property
message.setProperty("MessageNumber", i);
// Send message to the topic
service.sendTopicMessage("TestTopic", message);
}
```

Service Bus emner understøtter en maksimale størrelse på 256 KB [Standard niveau](service-bus-premium-messaging.md) og 1 MB i [Premium niveau](service-bus-premium-messaging.md). Hovedet, som indeholder programmet på computeren standardfarver og brugerdefinerede egenskaber, kan have en maksimal størrelse på 64 KB. Der er ingen begrænsninger på antallet af beskeder i et emne, men der er en knop på den samlede størrelse af de meddelelser, som et emne. Dette emne størrelse er defineret på oprettelsestidspunkt med en øvre grænse på 5 GB.

## <a name="how-to-receive-messages-from-a-subscription"></a>Hvordan du kan modtage meddelelser fra et abonnement

Hvis du vil modtage meddelelser fra et abonnement, kan bruge et **ServiceBusContract** objekt. Modtagne meddelelser kan arbejde i to forskellige tilstande: **ReceiveAndDelete** og **PeekLock**.

Når bruger tilstanden **ReceiveAndDelete** modtager er en handling af enkelt billede – det vil sige, når Service Bus modtager en kvittering anmodning om en meddelelse, det markerer meddelelsen som bliver brugt, og returnerer den til programmet. **ReceiveAndDelete** tilstand er den nemmeste model og fungerer bedst for scenarier, hvor et program kan acceptere ikke behandle en meddelelse i tilfælde af en fejl. For at forstå, bør du overveje et scenarie, hvor forbrugeren udsteder Modtag anmodningen og derefter går ned, før du behandler den. Fordi Service Bus har markeret meddelelsen som bliver brugt, og derefter når programmet genstarter og begynder forbrug meddelelser igen, den gået glip den meddelelse, der blev brugt før går ned.

I **PeekLock** tilstand modtage bliver en handling af to trin, som gør det muligt for understøttelse af programmer, der ikke kan acceptere manglende meddelelser. Når Service Bus modtager en anmodning, finder den næste meddelelse at blive brugt det, låser den for at forhindre andre forbrugere, modtager den og returnerer den derefter til programmet. Når programmet afsluttes behandling af meddelelsen (eller gemmer den pålideligt til fremtidige behandling), er fuldført i anden fase af Modtag processen ved at ringe **slette** på den modtagne meddelelse. Når Service Bus registrerer **slette** opkaldet, den markere meddelelsen som der consumed og fjerne det fra emnet.

I eksemplet nedenfor viser, hvordan meddelelser kan modtages og behandles ved hjælp af **PeekLock** tilstand (ikke standardtilstanden). Eksemplet herunder udfører en løkke og behandler meddelelser i "HighMessages"-abonnement og derefter afslutter når der er ingen flere meddelelser (du kan også den kan sættes til vente til nye meddelelser).

```
try
{
    ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
    opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

    while(true)  {
        ReceiveSubscriptionMessageResult  resultSubMsg =
            service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
        BrokeredMessage message = resultSubMsg.getValue();
        if (message != null && message.getMessageId() != null)
        {
            System.out.println("MessageID: " + message.getMessageId());
            // Display the topic message.
            System.out.print("From topic: ");
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
                message.getProperty("MessageNumber"));
            // Delete message.
            System.out.println("Deleting this message.");
            service.deleteMessage(message);
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

Service Bus indeholder funktioner for at hjælpe dig med at gendanne problemfrit fra fejl i programmet eller problemer behandling af en meddelelse. Hvis en modtager programmet er i stand til at behandle meddelelsen eller anden grund, kan det kalde metoden **unlockMessage** på den modtagne meddelelse (i stedet for metoden **deleteMessage** ). Dette medfører Service Bus låse op meddelelsen inden for emnet, og gøre det tilgængeligt igen, der modtages, af det samme arket program eller af en anden arket program.

Der er også en timeout, der er knyttet til en meddelelse, der er låst i emnet, og hvis der opstår fejl i programmet til at behandle meddelelsen før låsetimeout udløber (for eksempel hvis programmet går ned), og derefter Service Bus vil låse op meddelelsen automatisk og gøre det tilgængeligt skal modtages igen.

I tilfælde af, at programmet går ned, efter behandling af meddelelsen, men før **deleteMessage** anmodningen er udstedt, derefter meddelelsen vil være udleveres igen til programmet når genstartes. Dette kaldes ofte **Mindst når behandling**, det vil sige, hver meddelelse behandles mindst én gang, men i nogle situationer kan være udleveres igen den samme meddelelse. Hvis dette scenario ikke kan acceptere dublerede behandling, bør derefter programmet udviklere tilføje yderligere logik til deres program tilladelse til at håndtere dublerede meddelelseslevering. Dette opnås ofte ved hjælp af metoden **getMessageId** af e-mailen, der forbliver konstante på tværs af leveringsforsøg.

## <a name="delete-topics-and-subscriptions"></a>Slette emner og abonnementer

Den primære metode til at slette emner og abonnementer er at bruge et **ServiceBusContract** objekt. Slette et emne, slettes eventuelle abonnementer, der er registreret med emnet også. Abonnementer kan også blive slettet uafhængigt af hinanden.

```
// Delete subscriptions
service.deleteSubscription("TestTopic", "AllMessages");
service.deleteSubscription("TestTopic", "HighMessages");
service.deleteSubscription("TestTopic", "LowMessages");

// Delete a topic
service.deleteTopic("TestTopic");
```

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende omkring Service Bus køer, kan du få flere oplysninger i [Service Bus køer, emner og abonnementer][] .

  [Azure SDK til Java]: http://azure.microsoft.com/develop/java/
  [Azure-værktøjskassen til Eklipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Azure classic portal]: http://manage.windowsazure.com/
  [Service Bus køer, emner og abonnementer]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  
  [0]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-13.png
  [2]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-04.png
  [3]: ./media/service-bus-java-how-to-use-topics-subscriptions/sb-queues-09.png
