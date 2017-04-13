<properties
    pageTitle="Tjenesten Bus Premium og Standard Messaging priser niveauer oversigt | Microsoft Azure"
    description="Tjenesten Bus Premium og Standard Messaging"
    services="service-bus"
    documentationCenter=".net"
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/02/2016"
    ms.author="darosa;sethm"/>

# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Tjenesten Bus Premium og Standard SMS niveauer 

Tjenesten Bus messaging, indeholder som messaging objekter som køer og emner, kombinerer enterprise messaging RTF-funktioner udgivelse abonnement semantik skaleres til skyen. Service Bus messaging bruges som det grundlæggende element kommunikation til mange avancerede skyen løsninger.

*Premium* niveauet i Service Bus messaging-adresser for almindelige spørgsmål fra kunder omkring skala, ydeevne og tilgængelighed til vigtige programmer. Selvom funktionssæt er næsten identiske, er disse to niveauer af Service Bus messaging designet til at fungere forskellige Brug sager.

Nogle overordnet forskelle er fremhævet i tabellen nedenfor.

| Premium                               | Standard                       |
|---------------------------------------|--------------------------------|
| Høj overførselshastighed                       | Variabelt overførselshastighed            |
| Mere forudsigelige ydeevne               | Variabelt ventetid               |
| Mere forudsigelige priser                   | Betal efterhånden variable priser |
| Muligheden for at skalere op og ned arbejdsbelastning | I/T.                            |
| Meddelelse størrelse > 256KB                  | Meddelelsesstørrelse er 256KB          |

**Tjenesten Bus Premium Messaging** indeholder ressource isolationsniveauet på CPU og hukommelse lag, så hver kunde arbejdsbelastningen kører i isolationsniveauet. Denne ressource objektbeholder kaldes en *messaging enhed*. Hver premium navneområde fordeles mindst én SMS enhed. Du kan købe 1, 2 eller 4 SMS enheder for hver tjeneste Bus Premium navneområde. Et enkelt arbejdsbelastningen eller en enhed kan strækker sig over flere SMS enheder og antallet af beskeder enheder kan ændres efter behov, selvom fakturering er i 24-timers eller daglig rente gebyrer. Resultatet er mere forudsigelige og gentaget ydeevnen til din Service Bus-baseret løsning.

Ikke kun er denne ydeevne mere forudsigelige og tilgængelig, men det er også hurtigere. Tjenesten Bus Premium messaging bygger på lagerplads programmet blev introduceret i [Azure begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/). Med Premium messaging er optimal ydeevne meget hurtigere end med det almindelige niveau.

## <a name="premium-messaging-technical-differences"></a>Premium Messaging tekniske forskelle

Følgende er nogle forskelle mellem Premium og Standard SMS niveauer.

### <a name="partitioned-queues-and-topics"></a>Partitioneret køer og emner

Partitioneret køer og emner, der understøttes i Premium-beskeder, men de fungerer ikke på samme måde som Standard og grundlæggende niveauer af Service Bus messaging. Premium messaging ikke bruge SQL som et datalager, og ikke længere er mulige ressource konkurrence knyttet til en delt platform. Som et resultat er partitionering ikke nødvendigt. Desuden er blevet ændret partition antallet fra 16 partitioner i Standard-beskeder til 2 partitioner i Premium. Har du to partitioner sikrer tilgængelighed og er et mere passende tal til Premium runtime-miljø. Du kan finde flere oplysninger om partitionering, se [Partitioned køer og emner](service-bus-partitioning.md).

### <a name="express-entities"></a>Express enheder

Da Premium Messaging kører i et helt isolerede runtime-miljø, der udtrykkelig objekter ikke understøttes i Premium navneområder. Du kan finde flere oplysninger om funktionen Hurtig egenskaben [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx) .

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Service Bus messaging, under følgende emner.

- [Introduktion til Azure Service Bus Premium messaging (blogindlæg)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduktion til Azure Service Bus Premium messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Service Bus SMS oversigt](service-bus-messaging-overview.md)
- [Sådan bruger du Service Bus køer](service-bus-dotnet-get-started-with-queues.md)
