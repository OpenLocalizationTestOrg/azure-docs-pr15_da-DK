<properties 
    pageTitle="Opdelt køer og emner | Microsoft Azure"
    description="Beskriver, hvordan du partition Service Bus køer og emner ved hjælp af flere meddelelse mæglere."
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
    ms.date="09/02/2016"
    ms.author="sethm;hillaryc" />

# <a name="partitioned-queues-and-topics"></a>Partitioneret køer og emner

Azure Service Bus anvender flere meddelelse mæglere til at behandle meddelelser og flere SMS butikker til lagring af meddelelser. En traditionelt kø eller emne er håndteret af en enkelt meddelelse mægler og gemmes i en SMS store. Service Bus kan også køer eller -emnerne for at blive opdelt på tværs af flere meddelelse mæglere og SMS butikker. Det betyder, at den overordnede overførsel af en partitioneret kø eller emne ikke længere er begrænset ydeevnen for en enkelt meddelelse broker eller SMS butik. Desuden gengives en midlertidig afbrydelse af en SMS store ikke en partitioneret kø eller emne ikke tilgængelig. Partitioneret køer og emner kan indeholde alle avancerede Service Bus funktioner som understøttelse af transaktioner og sessioner.

Få mere at vide om Service Bus interne emnet [Service Bus arkitektur][] .

## <a name="how-it-works"></a>Sådan fungerer det

Hver partitioneret kø eller emne består af flere fragmenter. Hver fragment er gemt i et andet SMS lager og håndteret af en anden meddelelse mægler. Når en meddelelse er sendt til en partitioneret kø eller emne, tildeler Service Bus meddelelsen til en af fragmenter. Valgmuligheden gøres tilfældigt ved Service Bus eller ved hjælp af en Partitionsnøgle, som afsenderen kan angive.

Når en kunde ønsker at modtage en meddelelse fra en partitioneret kø eller fra et abonnement på en partitioneret emnet Service Bus forespørgsler alle fragmenter for meddelelser, returnerer den første meddelelse, der er hentet fra enhver af SMS butikker til modtageren. Service Bus cache den anden meddelelser og returnerer dem når den modtager yderligere modtage anmodninger. En modtagelse klient kender ikke til partitionering; klient mod funktionsmåden for en partitioneret kø eller emne (for eksempel læse, fuldført, skal du udskyde inaktive, prefetching) er identisk med funktionsmåden for en almindelig enhed.

Der er ingen ekstra omkostninger, når du sender en meddelelse til, eller modtager en meddelelse fra en partitioneret kø eller emne.

## <a name="enable-partitioning"></a>Aktivere partitionering

Hvis du vil bruge partitioneret køer og emner med Azure Service Bus, bruge Azure SDK version 2,2 eller nyere eller angive `api-version=2013-10` i din HTTP anmoder om.

Du kan oprette Service Bus køer og emner i 1, 2, 3, 4 eller 5 GB størrelser (standard er 1 GB). Med partitionering aktiveret, opretter Service Bus 16 partitioner for hver GB, du angiver. Således, hvis du opretter en kø, der er 5 GB i størrelse, med 16 partitioner den maksimale kø størrelse bliver (5 \* 16) = 80 GB. Du kan se den maksimale størrelse på din partitioneret kø eller emne ved at kigge på tilbagekaldelsen på [Azure-portalen][].

Der findes flere måder at oprette en partitioneret kø eller emne. Når du opretter den eller de emne fra dit program, kan du aktivere partitionering for kø eller emne ved at angive egenskaben [QueueDescription.EnablePartitioning][] eller [TopicDescription.EnablePartitioning][] henholdsvis til **Sand**. Disse egenskaber skal angives på tid køen eller emne er blevet oprettet. Det er ikke muligt at ændre disse egenskaber på en eksisterende kø eller emne. Eksempel:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternativt kan du oprette en partitioneret kø eller et emne i Visual Studio eller [Azure-portalen][]. Når du opretter en ny kø eller et emne i portalen, kan du angive indstillingen **Aktivér partitionering** i bladet **generelle indstillinger** på den eller de emne vindue med **Indstillinger** til **Sand**. Klik på afkrydsningsfeltet **Aktivér partitionering** i dialogboksen **Ny kø** eller **Nyt emne** i Visual Studio.

## <a name="use-of-partition-keys"></a>Brug af partitionsnøgler

Når en meddelelse er sat i kø til en partitioneret kø eller emne, kontrollerer Service Bus for tilstedeværelse af en Partitionsnøgle. Hvis den findes, markerer det fragment, der er baseret på tasten. Hvis der ikke findes en Partitionsnøgle, markerer det fragment, der er baseret på en intern algoritme.

### <a name="using-a-partition-key"></a>Ved hjælp af en Partitionsnøgle

Nogle scenarier, som sessioner eller transaktioner, kræver meddelelser skal gemmes i et bestemt fragment. Alle disse scenarier kræver brug af en Partitionsnøgle. Alle meddelelser, der bruger den samme Partitionsnøgle er tildelt til det samme fragment. Hvis fragmentet er ikke tilgængelig i øjeblikket, returnerer Service Bus en fejl.

Afhængigt af scenariet bruges anden meddelelsesegenskaber som en Partitionsnøgle:

**Session-id**: Hvis en meddelelse indeholder egenskaben [BrokeredMessage.SessionId][] er angivet, vil Service Bus bruger denne egenskab som tasten partition. Denne måde, håndteres alle meddelelser, der hører til den samme session af den samme meddelelse broker. Dette giver mulighed at garantere meddelelse ordning samt konsistens for session fejltilstande Service Bus.

**PartitionKey**: Hvis en meddelelse med egenskaben [BrokeredMessage.PartitionKey][] , men ikke [BrokeredMessage.SessionId][] egenskaben angivet og derefter Service Bus bruger egenskaben [PartitionKey][] som tasten partition. Hvis meddelelsen er både [Session-id-][] og sættet [PartitionKey][] egenskaber, skal begge egenskaber være identiske. Hvis egenskaben [PartitionKey][] er indstillet til en anden værdi end egenskaben [Session-id][] , returnerer Service Bus en undtagelse for **InvalidOperationException opstår** . Egenskaben [PartitionKey][] skal bruges, hvis en afsender sender ikke session opmærksom på transaktions meddelelser. Tasten partition sikrer, at alle de meddelelser, der sendes i en transaktion håndteres af den samme SMS broker.

**MessageId**: Hvis kø eller emne indeholder egenskaben [QueueDescription.RequiresDuplicateDetection][] indstillet til **Sand** og egenskaberne [BrokeredMessage.SessionId][] eller [BrokeredMessage.PartitionKey][] ikke er angivet, vil [BrokeredMessage.MessageId][] egenskaben fungerer som tasten partition. (Bemærk, at Microsoft .NET og AMQP biblioteker automatisk tildeler en meddelelses-ID, hvis programmet afsendelse ikke). I dette tilfælde skal håndteres alle kopier af den samme meddelelse af den samme meddelelse broker. Dette giver mulighed for tjenesten Bus til at registrere og fjerne dublerede meddelelser. Hvis egenskaben [QueueDescription.RequiresDuplicateDetection][] ikke er angivet til **Sand**, overveje Service Bus ikke egenskaben [MessageId][] partition nøgle.

### <a name="not-using-a-partition-key"></a>Ikke bruger en Partitionsnøgle

I fravær af en Partitionsnøgle distribuerer Service Bus meddelelser i en round robin-måde at alle fragmenter af partitioneret kø eller emne. Hvis det valgte fragment ikke er tilgængelig, tildeler Service Bus meddelelsen til en anden fragment. Denne måde handlingen send lykkes på trods af en SMS store midlertidige hvorfor.

Hvis du vil give Service Bus tid nok til at enqueue meddelelsen i et andet fragment [MessagingFactorySettings.OperationTimeout][] værdien angivet af den klient, der sender meddelelsen skal være større end 15 sekunder. Det anbefales, at du angive egenskaben [OperationTimeout][] til standardværdien for 60 sekunder.

Bemærk, at en Partitionsnøgle "fastlåser" en meddelelse til en bestemt fragment. Hvis den SMS butik, som indeholder denne fragment er tilgængelig, returnerer Service Bus en fejl. Service Bus kan vælge en anden fragment uden af en Partitionsnøgle, og importen er udført. Det anbefales derfor, at du ikke angiver en Partitionsnøgle, medmindre det er nødvendigt.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Avancerede emner: bruge transaktioner med partitioneret enheder

Meddelelser, der er sendt som en del af en transaktion skal angive en Partitionsnøgle. Det kan være en af følgende egenskaber: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][]eller [BrokeredMessage.MessageId][]. Alle meddelelser, der er sendt som en del af den samme transaktion skal angive den samme Partitionsnøgle. Hvis du forsøger at sende en meddelelse uden en Partitionsnøgle i en transaktion, returnerer Service Bus en undtagelse for **InvalidOperationException opstår** . Hvis du forsøger at sende flere meddelelser i samme transaktion, der har forskellige partition taster, returnerer Service Bus en undtagelse for **InvalidOperationException opstår** . Eksempel:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Hvis et af de egenskaber, der fungerer som en Partitionsnøgle er angivet, fastlåser Service Bus meddelelsen til en bestemt fragment. Dette sker, uanset om der bruges en transaktion. Det anbefales, at du ikke angiver en Partitionsnøgle, hvis det ikke er nødvendigt.

## <a name="using-sessions-with-partitioned-entities"></a>Brug af sessioner med partitioneret enheder

Hvis du vil sende en transaktions meddelelse til en session-aware emne eller kø, skal meddelelsen have egenskaben [BrokeredMessage.SessionId][] er angivet. Hvis egenskaben [BrokeredMessage.PartitionKey][] angives også, kan det være identisk med egenskaben [Session-id][] . Hvis de er forskellige, returnerer Service Bus en undtagelse for **InvalidOperationException opstår** .

I modsætning til almindelig (ikke-opdelt) køer eller emner er det ikke muligt at bruge en enkelt transaktion til at sende flere meddelelser til forskellige sessioner. Hvis forsøgt, returnerer Service Bus en undtagelse for **InvalidOperationException opstår **. Eksempel:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatisk videresendelse med partitioneret enheder

Azure Service Bus understøtter automatisk videresendelse fra, til eller mellem partitioneret objekter. For at aktivere automatisk videresendelse af meddelelser skal du angive egenskaben [QueueDescription.ForwardTo][] på den kilde kø eller et andet abonnement. Hvis meddelelsen angiver en Partitionsnøgle ([Session-id][], [PartitionKey][]eller [MessageId][]), bruges partition tasten for objektet destination.

## <a name="considerations-and-guidelines"></a>Overvejelser i forbindelse med og vejledninger

- **Høj konsistens funktioner**: Hvis en person bruger funktioner som sessioner, dubletter eller eksplicitte kontrol af partitionering nøgle og derefter handlingerne SMS, altid distribueres til bestemte fragmenter. Hvis et af fragmenter oplever stor trafik eller det underliggende lager er beskadiget, disse handlinger mislykkes og tilgængelighed er reduceret. Overordnet set er sammenhæng stadig meget højere end ikke opdelt enheder kun et undersæt af trafik problemer med, i modsætning til al trafik.
- **Administration**: handlinger som Opret, Opdater og Slet skal udføres på alle fragmenter af objektet. Det kan medføre fejl for disse handlinger, hvis fragmenter er beskadiget. For handlingen Hent skal oplysninger, som meddelelsen tæller være sammenlagt fra alle fragmenter. Hvis fragmenter er beskadiget, rapporteres enhed tilgængelighedsstatus som begrænset.
- **Lav lydstyrke meddelelse scenarier**: For disse scenarier, især hvis ved hjælp af HTTP-protokollen, du kan være nødvendigt at udføre flere modtage handlinger for at få alle meddelelser. Modtagelsesanmodninger om front end udfører en Modtag på alle fragmenter og gemmer alle svar, der er modtaget. Vil drage fordel af denne cachelagring og modtage en anmodning om efterfølgende Modtag på den samme forbindelse latenstider bliver lavere. Men hvis du har flere forbindelser eller bruge HTTP, der opretter en ny forbindelse for hver enkelt anmodning. Der er ingen garanti, det vil lander på den samme node som f.eks. Hvis alle eksisterende meddelelser er låst og cachelagret i en anden front-end, returnerer handlingen Modtag **null**. Meddelelser til sidst udløber, og du kan få dem igen. HTTP heller anbefales.
- **Gennemse/oversigten meddelelser**: PeekBatch ikke altid returnerer antallet af meddelelser, der er angivet i [MessageCount egenskab](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Der er to almindelige årsager til dette. Én skyldes, at aggregeret størrelsen på samlingen af meddelelser overstiger den maksimale størrelse på 256KB. En anden mulig grund er, at hvis kø eller emne har [EnablePartitioning egenskab](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) indstillet til **Sand**, en partition muligvis ikke tilstrækkelig meddelelser til at fuldføre det ønskede antal meddelelser. Generelt, hvis et program ønsker at modtage et bestemt antal meddelelser, skal den ringe til [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) flere gange, indtil det bliver pågældende antallet af beskeder, eller der er ingen flere meddelelser til Kig. Finde flere oplysninger, herunder kodeeksempler, [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) eller [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Seneste ekstra funktioner

- Tilføje eller fjerne regel understøttes nu med partitioneret enheder. Forskellig fra ikke-opdelt enheder, disse handlinger understøttes ikke under transaktioner. 
- AMQP understøttes nu for at sende og modtage meddelelser til og fra en partitioneret enhed.
- AMQP understøttes nu for følgende handlinger: [Batchen sende](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Batchen modtage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [Modtag af serienummer](https://msdn.microsoft.com/library/azure/hh330765.aspx), [Kig](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Forny Lås](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Tidsplan meddelelse](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Annullere planlagt meddelelse](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Tilføj regel](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Fjerne regel](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Session forny Lås](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Angive Session tilstand](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Få Session tilstand](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Kig Session meddelelser](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx)og [Optæl sessioner](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Partitioneret enheder begrænsninger

I øjeblikket giver Service Bus følgende begrænsninger på partitioneret køer og emner:

-   Partitioneret køer og emner, der understøtter ikke afsendelsen af meddelelser, der tilhører forskellige sessioner i en enkelt transaktion.
-   Tjenesten Bus giver aktuelt op til 100 partitioneret køer eller emner per navneområde. Hver partitioneret kø eller emne tæller mod kvoten for 10.000 enheder per navneområde (ikke gælder for Premium niveau).

## <a name="next-steps"></a>Næste trin

Se diskussion af [AMQP 1.0 understøttelse af Service Bus opdelt køer og -emnerne for][] at få mere at vide om partitionering SMS enheder. 

  [Service Bus arkitektur]: service-bus-architecture.md
  [Azure-portalen]: https://portal.azure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [Session-id]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0 understøttelse af Service Bus opdelt køer og emner]: service-bus-partitioned-queues-and-topics-amqp-overview.md
