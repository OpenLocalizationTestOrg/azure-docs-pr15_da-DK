<properties 
    pageTitle="Service Bus transaktioner | Microsoft Azure" 
    description="Oversigt over Azure Service Bus atomiske transaktioner, og send via" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="10/04/2016"
    ms.author="clemensv;sethm"/>

# <a name="overview-of-service-bus-transaction-processing"></a>Oversigt over Service Bus transaktionsbehandling

I denne artikel beskrives transaktion funktionerne i Azure Service Bus. Meget af diskussionen illustreres ved [Atomiske transaktioner med Service Bus eksempel](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions). I denne artikel er begrænset til en oversigt over behandling af transaktioner og funktionen *send via* i Service Bus, mens atomiske transaktioner prøven er bredere og mere komplekse i omfang.

## <a name="transactions-in-service-bus"></a>Transaktioner i Service Bus

En [transaktion](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions#what-are-transactions) grupperer to eller flere handlinger sammen i en *udførelse af omfang*. Efter art, skal sådanne en transaktion sikre, at alle handlinger, der hører til en bestemt gruppe af handlinger enten lykkes eller mislykkes Fællesskab. I denne forbindelse fungere transaktioner som én enhed, som kaldes ofte *atomicitet*. 

Service Bus er en transaktions meddelelse mægler og sikrer transaktions integritet for alle interne handlinger mod dens meddelelse butikker. Alle overførsler af meddelelser i tjenesten Bus, såsom at flytte meddelelser til en [kø til inaktive meddelelser](service-bus-dead-letter-queues.md) eller [automatisk videresendelse](service-bus-auto-forwarding.md) af meddelelser mellem enheder, er transaktioner. Som sådan Hvis Service Bus accepterer en meddelelse, er den allerede gemt og mærket med et sekvens tal. Fra en hvilken som helst meddelelse overførsler inden for Service Bus er koordineret handlinger på tværs af enheder, og vil hverken medføre tab (kilde lykkes og mål mislykkes) eller til kopiering (mislykkes, kilde og destination lykkes) af meddelelsen.

Service Bus understøtter gruppering handlinger mod en enkelt SMS enhed (kø, emne, abonnement) er omfattet af en transaktion. For eksempel kan du sende flere meddelelser til én kø fra inden for et transaktion omfang, og meddelelserne, der vil kun blive anvendt på den kø log, når posteringen er fuldført.

## <a name="operations-within-a-transaction-scope"></a>Handlinger i et transaktion omfang 

De handlinger, der kan udføres i et transaktion omfang er som følger:

- ** [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx), [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx)**: sender, SendAsync, SendBatch, SendBatchAsync 

- **[BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)**: fuldført, CompleteAsync, Abandon, AbandonAsync, inaktive, DeadletterAsync, udskyde, DeferAsync, RenewLock, RenewLockAsync 

Modtage handlinger medtages ikke, da det antages, at programmet får meddelelser ved hjælp af [ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) tilstanden i nogle modtager løkke eller med en [OnMessage](https://msdn.microsoft.com/library/azure/dn369601.aspx) tilbagekald og kun derefter åbnes et transaktion omfang til at behandle meddelelsen.

Fordeling af meddelelsen (fuldført, abandon, uanbringelige, udskyde) forekommer i omfanget af, og afhængige på overordnede resultatet af transaktionen.

## <a name="transfers-and-send-via"></a>Overførsler og "send via"

Hvis du vil aktivere transaktions overdragelse af data fra en kø til en processor, og klik derefter til en anden kø, understøtter Service Bus *overførsler*. En afsender først sender en meddelelse til en "Filoverførsel kø" i en overførslen og filoverførsel køen med det samme flytter meddelelsen til den ønskede destinationskø ved hjælp af den samme robust filoverførsel implementering, mulighed for automatisk Videresend er afhængig af. Meddelelsen er aldrig anvendt til overførsel køen log på en måde, at det bliver synlige til overførsel køen forbrugere.

En potens af denne transaktions egenskab bliver synlige, når overførslen køen selve er kilden til afsenderens input meddelelser. Med andre ord Service Bus kan overføre meddelelsen til destinationskøen "via" kø Filoverførsel under udførelse af en komplet (eller udskyde, eller kø til inaktive) handling på den meddelelse, alt sammen i én atomisk handling. 

### <a name="see-it-in-code"></a>Se den i kode

Hvis du vil konfigurere sådanne overførsler, opretter du en e-mailen, der er beregnet til destinationskøen via filoverførsel køen. Du får også en modtager, der trækker meddelelser fra den pågældende samme kø. Eksempel:

```
var sender = this.messagingFactory.CreateMessageSender(destinationQueue, myQueueName);
var receiver = this.messagingFactory.CreateMessageReceiver(myQueueName);
```

En enkelt transaktion derefter bruger disse elementer, som i følgende eksempel:

```
var msg = receiver.Receive();

using (scope = new TransactionScope())
{
    // Do whatever work is required 

    var newmsg = ... // package the result 

    msg.Complete(); // mark the message as done
    sender.Send(newmsg); // forward the result

    scope.Complete(); // declare the transaction done
} 
```

## <a name="next-steps"></a>Næste trin

Se følgende artikler for at finde flere oplysninger om Service Bus køer:

- [Sammenkædning Service Bus enheder med automatisk videresendelse](service-bus-auto-forwarding.md)
- [Automatisk Videresend eksempel](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AutoForward)
- [Atomiske transaktioner med Service Bus eksempel](https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/AtomicTransactions)
- [Azure køer og Service Bus køer sammenlignet](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- [Sådan bruger du Service Bus køer](service-bus-dotnet-get-started-with-queues.md)