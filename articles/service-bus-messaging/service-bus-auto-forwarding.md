<properties 
    pageTitle="Automatisk viderestilling Service Bus messaging enheder | Microsoft Azure"
    description="Sådan kæde en kø eller et andet abonnement til en anden kø eller emne."
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
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Sammenkædning Service Bus enheder med automatisk videresendelse

Funktionen *automatisk videresendelse* gør det muligt at sammenkæde en kø eller et andet abonnement til en anden kø eller emne, der er en del af det samme navneområde. Når automatisk viderestilling er aktiveret, fjerner meddelelser, der er placeret i den første kø eller et andet abonnement (kilde) Service Bus automatisk, og lægger dem ind i den anden kø eller emne (destination). Bemærk, at det er stadig muligt at sende en meddelelse til destination for enhed direkte. Desuden er det ikke muligt at sammenkæde en underkø som en inaktive kø til en anden kø eller emne.

## <a name="using-auto-forwarding"></a>Brug af automatisk videresendelse

Du kan aktivere automatisk videresendelse ved at angive [QueueDescription.ForwardTo][] eller [SubscriptionDescription.ForwardTo][] egenskaber på [QueueDescription][] eller [SubscriptionDescription][] objekter til kilden, som i følgende eksempel.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Destination for den enhed, skal findes på det tidspunkt kildeobjekt er oprettet. Hvis destination for enhed ikke findes, returnerer Service Bus en undtagelse, når du bliver bedt om at oprette kildeobjekt.

Du kan bruge automatisk videresendelse skalere ud af et bestemt emne. Service Bus begrænser [antallet af abonnementer på et bestemt emne](service-bus-quotas.md) til 2.000. Du kan medtage flere abonnementer ved at oprette emner i andet niveau. Bemærk, selvom du ikke er bundet ved Service Bus begrænsning på antallet af abonnementer, tilføje et andet niveau af emner kan forbedre den overordnede overførsel af dit emne.

![Automatisk viderestilling af scenarie][0]

Du kan også bruge automatisk viderestilling til decouple afsendere fra modtagere. For eksempel kan du overveje en ERP-system, der består af tre moduler: behandling, Lagerstyring og kundestyring relationer. Hver af disse moduler genererer meddelelser, der er sat i kø til en tilsvarende emne. Bent og Bob er sælgere, der er interesseret i alle meddelelser, der er relateret til deres kunder. Hvis du vil modtage meddelelserne, oprette Bent og Bob en personlig kø og et abonnement på hver af de ERP-emner, der automatisk at videresende alle meddelelser til deres kø.

![Automatisk viderestilling af scenarie][1]

Hvis Bent går på ferie, sin personlige kø i stedet for emnet ERP, fylder. I dette scenarie, fordi en sælger ikke har modtaget nogen meddelelser, når ingen af ERP-emnerne nogensinde kvote.

## <a name="auto-forwarding-considerations"></a>Overvejelser i forbindelse med automatisk videresendelse

Hvis destination for enhed er akkumuleret mange meddelelser og overstiger kvoten eller destination for enhed er deaktiveret, føjer kildeobjekt meddelelser til sin [kø til inaktive meddelelser](service-bus-dead-letter-queues.md) , indtil der er plads i destinationen (eller objektet er aktiveret igen). Disse meddelelser vil fortsat live i kø til inaktive køen, så du skal eksplicit modtage og behandle dem fra de kø til inaktive meddelelser.

Når sammenkædning sammen enkelte emner for at få et sammensat emne med mange abonnementer, anbefales det, at du har et moderat antal abonnementer på det første niveau emne og mange abonnementer på andet niveau emner. For eksempel en første niveau emne med 20 abonnementer, hver af dem kædet til et andet niveau emne med 200 abonnementer giver mulighed for højere overførselshastighed end et første niveau emne med 200 abonnementer hver kædet til et andet niveau emne med 20-abonnementer.

Service Bus fakturerer én gang til hver videresendte meddelelser. For eksempel der sender en meddelelse til et emne med 20 abonnementer, hver af dem konfigureret til at automatisk Videresend meddelelser til en anden kø eller emne, faktureres som 21 handlinger Hvis alle abonnementer på første niveau modtager en kopi af meddelelsen.

Hvis du vil oprette et abonnement, der er knyttet til en anden kø eller emne, skal forfatteren af abonnementet have **Administrer** tilladelser på både kilde- og destination for enhed. Sende meddelelser til emnet kilden kun kræver **sende** tilladelser på emnet kilde.

## <a name="next-steps"></a>Næste trin

Du kan finde detaljerede oplysninger om automatisk viderestilling af følgende referenceemner:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Hvis du vil vide mere om Service Bus forbedring af ydeevnen, se [Partitioned messaging enheder][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Partitioneret SMS enheder]: service-bus-partitioning.md