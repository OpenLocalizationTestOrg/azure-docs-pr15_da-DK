<properties 
    pageTitle="Service Bus parvis navneområder | Microsoft Azure"
    description="Parvis navneområde implementeringsdetaljer og omkostninger"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/04/2016"
    ms.author="sethm" />

# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Parvis navneområde implementeringsdetaljer og omkostninger konsekvenserne

Metoden [PairNamespaceAsync][] , ved hjælp af en forekomst af [SendAvailabilityPairedNamespaceOptions][] , udfører synlige opgaver på dine vegne. Det er nyttigt at forstå disse opgaver, så du kan forvente funktionsmåden, når der sker, fordi der er omkostninger overvejelser, når du bruger funktionen. API deltager følgende automatiske funktionsmåde på dine vegne:

-   Oprettelse af ordrebeholdningen køer.
-   Oprettelse af et [MessageSender][] objekt, der taler med køer eller emner.
-   Når en SMS enhed ikke er tilgængelig, ping sender meddelelser til objektet i et forsøg på at registrere, hvornår objektet bliver tilgængelig igen.
-   Du kan også opretter af et sæt "meddelelse pumper", flytte meddelelser fra ordrebeholdningen køerne til de primære køer.
-   Koordinater lukning/fejlagtigt af de primære og sekundære [MessagingFactory][] forekomster.

På et højt niveau, funktionen fungerer på følgende måde: Når det primære objekt er i orden, der optræder ingen funktionsmåde ændringer. Når [FailoverInterval][] varigheden standardtimeouttiden er udløbet, og det primære objekt ser ikke lykkes sender efter en permanent [MessagingException][] eller en [TimeoutException][], sker der følgende:

1.  Send handlinger til den primære enhed er deaktiveret og systemet pinger det primære objekt, indtil pinger kan være leveret.

2.  En tilfældigt ordrebeholdningen kø er markeret.

3.  [BrokeredMessage][] objekter, distribueres til den valgte ordrebeholdning kø.

1.  Hvis handlingen send til den valgte ordrebeholdning kø mislykkes, køen trækkes fra rotationen, og en ny kø er markeret. Alle afsendere på forekomsten [MessagingFactory][] bekendt med fejlen.

Følgende tallene afbilde rækkefølgen. Først skal sender afsenderen meddelelser.

![Parvis navneområder][0]

Ved fejl skal sendes til den primære kø, begynder afsenderen, sende meddelelser til en tilfældigt valgte ordrebeholdningen kø. Det starter samtidigt, en ping-opgave.

![Parvis navneområder][1]

På dette tidspunkt meddelelser findes stadig i sekundær køen og er ikke blevet leveret til den primære kø. Når den primære kø er sund igen, skal mindst én proces køre syphon. Syphon leverer meddelelser fra alle de forskellige ordrebeholdning køer til de korrekte destination enheder (køer og emner).

![Parvis navneområder][2]

Resten af dette emne beskrives de specifikke oplysninger om, hvordan disse elementer virker.

## <a name="creation-of-backlog-queues"></a>Oprettelse af ordrebeholdningen køer

Objektet [SendAvailabilityPairedNamespaceOptions][] overføres til metoden [PairNamespaceAsync][] angiver antallet af ordrebeholdningen køer, du vil bruge. Hver ordrebeholdningen kø derefter oprettes med følgende egenskaber eksplicit sæt (alle andre værdier er angivet til [QueueDescription][] standardindstillingerne):

| Sti                                   | [primære navneområde] / x-servicebus-overførsel / [indeksere] hvor [indeks] er en værdi i [0, BacklogQueueCount) |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes                     | 5120                                                                                                 |
| MaxDeliveryCount                       | heltal. MaxValue                                                                                         |
| DefaultMessageTimeToLive               | TimeSpan.MaxValue                                                                                    |
| AutoDeleteOnIdle                       | TimeSpan.MaxValue                                                                                    |
| LockDuration                           | 1 minut                                                                                             |
| EnableDeadLetteringOnMessageExpiration | SAND                                                                                                 |
| EnableBatchedOperations                | SAND                                                                                                 |

For eksempel første ordrebeholdningen kø oprettet for navneområde **contoso** hedder `contoso/x-servicebus-transfer/0`.

Når du opretter køerne, kontrollerer koden først, hvis der findes sådanne en kø. Hvis køen ikke findes, oprettes køen. Koden ikke oprydning "ekstra" ordrebeholdningen køer. Specifikt, hvis program med primære navneområde **contoso** anmoder om fem ordrebeholdningen køer men en ordrebeholdningen kø med stien `contoso/x-servicebus-transfer/7` findes, ekstra ordrebeholdningen køen findes stadig, men ikke bruges. Systemet tillader eksplicit ekstra ordrebeholdningen køer til, findes der ikke ville blive brugt. Som ejeren af navneområde er du ansvarlig for at rydde op eventuelle ubrugte/uønskede ordrebeholdningen forespørgsler. Årsagen til denne beslutning er, at Service Bus ikke kan ved, hvilke formål findes for alle køer i din navneområde. Desuden, hvis en kø findes med det angivne navn, men ikke opfylder formodet [QueueDescription][], derefter dine grunde er din egen til at ændre standardfunktionen. Ingen garantier ikke foretages ændringer til ordrebeholdningen køer af din kode. Sørg for at teste ændringerne grundigt.

## <a name="custom-messagesender"></a>Brugerdefineret MessageSender

Når du sender, går alle meddelelser, der igennem et internt [MessageSender][] objekt, der normalt skal reagere, når alt fungerer, og omdirigerer til ordrebeholdningen køerne, når ting "bryder." Ved modtagelse af en fejl permanent, starter en timer. Når du har en [TimeSpan][] periode bestående af egenskabsværdi [FailoverInterval][] , hvor der sendes ingen vellykket meddelelser, har sekundære. På dette tidspunkt sker der følgende for hver enhed:

- En ping opgave udfører hver [PingPrimaryInterval][] for at kontrollere, om objektet er tilgængelig. Når denne opgave er oprettet, starter alle klient-kode, der bruger objektet med det samme sende nye meddelelser til det primære navneområde.

- Fremtidige anmodninger om at sende til den samme enhed fra andre afsendere, medfører det [BrokeredMessage][] der sendes til ændres til at stå i køen ordrebeholdningen. Ændringen fjerner nogle egenskaber fra [BrokeredMessage][] objektet og gemmer dem et andet sted. Følgende egenskaber er fjernet og tilføjet under et nyt alias tillade Service Bus og SDK til at behandle meddelelser ensartet:

| Gamle egenskabsnavn       | Ny egenskabsnavn |
|-------------------------|-------------------|
| Session-id               | x-ms-session-id    |
| TimeToLive              | x-ms-timetolive   |
| ScheduledEnqueueTimeUtc | x-ms-sti         |

Den oprindelige destination sti gemmes også i meddelelsen som en egenskab med navnet x-ms-sti. Dette design giver mulighed for meddelelser for mange objekter til at fungere sammen i en enkelt ordrebeholdningen kø. Egenskaberne oversættes tilbage ved syphon.

Det brugerdefinerede [MessageSender][] objekt kan støder på problemer, når meddelelser løse grænsen på 256 KB og failover er involveret. Brugerdefinerede [MessageSender][] objektet indeholder meddelelser for alle køer og emner sammen i ordrebeholdning køerne. Dette objekt blander meddelelser fra mange primærfarver sammen i ordrebeholdning køerne. Hvis du vil håndtere justering af belastning mellem mange klienter, der ikke kender hinanden, henter SDK tilfældigt én ordrebeholdningen kø for hver [QueueClient][] eller [TopicClient][] du opretter i kode.

## <a name="pings"></a>Pinger

En pingmeddelelse er en tom [BrokeredMessage][] med egenskaben [ContentType][] indstillet til programmet/vnd.ms-servicebus-ping og [TimeToLive][] værdien 1 sekund. Denne ping har en særlig egenskab i tjenesten Bus: serveren, der leverer aldrig en ping, når en hvilken som helst opkalds anmoder om en [BrokeredMessage][]. Dermed, du aldrig behøver at se, hvordan du får vist og ignorere disse meddelelser. De enkelte objekter (entydige kø eller emne) per [MessagingFactory][] forekomst per klient pinges, når de betragtes som ikke er tilgængelig. Dette sker én gang i minuttet som standard. Ping meddelelser der betragtes som normalt Service Bus meddelelser og kan medføre gebyrer for båndbredde og meddelelser. Så snart klienterne, der registrerer, at systemet er tilgængelige, stop meddelelser.

## <a name="the-syphon"></a>Syphon

Mindst én eksekverbart program i programmet på computeren skal aktivt kørende på syphon. Syphon udfører en lang afstemning modtager, der varer 15 minutter. Når alle objekter er tilgængelige, og du har 10 ordrebeholdningen køer, kræver det program, der er vært på syphon handlingen Modtag 40 gange i timen 960 gange om dagen og 28800 i 30 dage. Når syphon er aktivt Flyt meddelelser fra ordrebeholdningen til den primære kø, har alle meddelelser, de følgende gebyrer (standard gebyrer for meddelelsesstørrelse og båndbredde anvender i alle faser):

1.  Sende til ordrebeholdningen.

2.  Modtage fra ordrebeholdningen.

3.  Sende til primært.

4.  Modtage fra primært.

## <a name="closefault-behavior"></a>Luk/fejl funktionsmåde

I et program, der hoster syphon, når de primære eller sekundære [MessagingFactory][] fejl eller er lukket uden dens partner registrerer også blive afbrudt/lukket og syphon denne tilstand syphon handlinger. Hvis de andre [MessagingFactory][] ikke er lukket inden for 5 sekunder, fejl i syphon stadig er åben [MessagingFactory][].

## <a name="next-steps"></a>Næste trin

Se [asynkron SMS mønstre og høj tilgængelighed][] til en detaljeret beskrivelse af tjenesten Bus asynkron messaging. 

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Asynkron SMS mønstre og høj tilgængelighed]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png
