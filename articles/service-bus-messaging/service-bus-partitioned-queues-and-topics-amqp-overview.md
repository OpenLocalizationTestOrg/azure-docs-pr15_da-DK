<properties 
    pageTitle="AMQP 1.0 understøttelse af Service Bus opdelt køer og emner | Microsoft Azure" 
    description="Få mere at vide om at bruge de avancerede meddelelse Queuing Protocol (AMQP) 1.0 med Service Bus opdelt køer og emner." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="hillaryc" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="hillaryc;sethm"/>

# <a name="amqp-10-support-for-service-bus-partitioned-queues-and-topics"></a>AMQP 1.0 understøttelse af Service Bus opdelt køer og emner 

Azure Service Bus understøtter nu de avancerede Message Queuing Protocol (**AMQP**) 1.0 for tjenesten Bus **opdelt køer og emner.**

**AMQP** er en kø åbne standarder meddelelse-protokollen, der gør det muligt at udvikle på tværs af platforme programmer, der bruger forskellige programmeringssprog. Generelle oplysninger om AMQP understøttelse i Service Bus skal du se [AMQP 1.0 support i Service Bus](service-bus-amqp-overview.md).

**Partitioned køer og emner**, også kaldet *partitioneret enheder*, du tilbyder højere tilgængelighed, pålideligheden og hurtigere end traditionelt ikke opdelt køer og emner. Du kan finde flere oplysninger om partitioneret enheder, [Partitioneret Messaging enheder](service-bus-partitioning.md).

Tilføjelse af AMQP 1.0 som en protokol til at kommunikere med partitioneret køer og emner, der gør det muligt at bygge kompatible programmer, der kan drage fordel af højere tilgængeligheden, pålidelighed, og i hele tilbydes af Service Bus opdelt enheder.

Have en detaljeret tråd niveau AMQP 1.0 protocol vejledning, som forklarer, hvordan Service Bus implementerer og bygger på OASIS AMQP tekniske specifikationen, se [AMQP 1.0 i Azure Service Bus og begivenhed Hubs protokol vejledning](service-bus-amqp-protocol-guide.md).    

## <a name="use-amqp-with-partitioned-queues"></a>Brug AMQP med partitioneret køer

Køer er nyttige til scenarier, der kræver tidsmæssig afkobling, Indlæs udjævning, justering af belastning og løs kobling. Med en kø udgivere sende meddelelser til køen og forbrugere modtage meddelelser fra køen, i situationer, hvor meddelelsen kan kun modtages én gang. Et godt eksempel på dette er et lager system, hvori POS klienter udgive data til en kø i stedet for direkte til lager management system. Administrationssystem lager forbruger derefter dataene til enhver tid til at administrere lager Genbestilling. Dette har flere fordele, herunder administrationssystem lager, ikke behøver at være online for hele tiden. Du kan finde flere oplysninger om Service Bus køer, [oprette programmer, der bruger Service Bus køer](service-bus-create-queues.md). 

En partitioneret kø yderligere øges tilgængelighed, pålideligheden og overførsel for programmer, som disse køer er opdelt på tværs af flere meddelelse mæglere og SMS butikker.     

### <a name="create-partitioned-queues"></a>Oprette partitioneret køer

Du kan oprette en partitioneret kø ved hjælp af [Azure klassisk portal][] og Service Bus SDK. For at oprette en partitioneret kø skal du angive egenskaben [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) til **Sand** i [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) forekomsten. Følgende kode viser, hvordan du opretter en partitioneret kø ved hjælp af tjenesten Bus SDK. 
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Sende og modtage meddelelser ved hjælp af AMQP

Du kan sende meddelelser til og modtage meddelelser fra en partitioneret kø ved hjælp af AMQP som protokol, ved at angive egenskaben [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) til [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) , som vist i følgende kode.  

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="use-amqp-with-partitioned-topics"></a>Bruge AMQP med partitioneret emner

Emner findes lignende til køer, men emner kan sende en kopi af den samme meddelelse til flere *abonnementer*. Udgivere sende meddelelser til emnet i et emne og forbrugere modtage meddelelser fra abonnementer. I dette lager system POS scenario udgive klienter data til emnet. Administrationssystem lager derefter modtager meddelelser fra et abonnement. Desuden kan et system til overvågning modtage den samme meddelelse fra et andet abonnement. Du kan finde flere oplysninger om Service Bus emner og abonnementer, [oprette programmer, der bruger Service Bus emner og abonnementer](service-bus-create-topics-subscriptions.md). 

Som med køer, øge partitioneret emner yderligere tilgængelighed, pålideligheden og overførsel for programmer, som er opdelt disse emner og deres abonnementer på tværs af flere meddelelse mæglere og SMS butikker. 

### <a name="create-partitioned-topics"></a>Oprette partitioneret emner

Du kan oprette et partitioneret emne ved hjælp af [Azure klassisk portal][] og Service Bus SDK. For at oprette et partitioneret emne skal du angive egenskaben [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) til **Sand** i [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) forekomsten. Følgende kode viser, hvordan du opretter et partitioneret emne ved hjælp af tjenesten Bus SDK.
    
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### <a name="send-and-receive-messages-using-amqp"></a>Sende og modtage meddelelser ved hjælp af AMQP

Du kan sende meddelelser til og modtage meddelelser fra et partitioneret emne abonnement ved hjælp af AMQP som en protokol, ved at angive egenskaben [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) til [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), som vist i følgende kode.  

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
    
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
    
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## <a name="next-steps"></a>Næste trin

Se følgende yderligere oplysninger for at få flere oplysninger om partitioneret SMS objekter samt AMQP.

*    [Partitioneret SMS enheder](service-bus-partitioning.md)
*    [Avancerede meddelelse Queuing Protocol (AMQP) Version 1.0 OASIS](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Understøttelse af AMQP 1.0 i Service Bus](service-bus-amqp-overview.md)
*    [AMQP 1.0 i Azure Service Bus og begivenhed Hubs protocol vejledning](service-bus-amqp-protocol-guide.md)
*    [Sådan bruges de Java meddelelse Service (JMS) API med Service Bus og AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Sådan bruges AMQP 1.0 med Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)

[Azure klassisk portal]: http://manage.windowsazure.com
