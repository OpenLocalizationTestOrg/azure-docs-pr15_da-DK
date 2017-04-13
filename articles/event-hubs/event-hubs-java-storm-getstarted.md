<properties
    pageTitle="Introduktion til begivenhed Hubs i Java med Apache Storm | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med Azure begivenhed Hubs; sende hændelser med Java og modtage dem i en Apache Storm klynge."
    services="event-hubs"
    documentationCenter=""
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en meget SVG indtagelse system, der kan indtag millioner af hændelser sekundet aktivere et program til at behandle og analysere store datamængder produceret af dine forbundne enheder og programmer. Når der indsamles i begivenhed Hubs, kan du transformere og gemme data ved hjælp af enhver realtid analytics-udbyder eller lagerplads klynge.

Få vist [Begivenhed Hubs oversigt][]kan finde flere oplysninger.

Dette selvstudium beskriver, hvordan at samle meddelelser i en begivenhed Hub ved hjælp af en console-program i Java og for at hente dem parallelt ved at bruge Apache Storm.

For at fuldføre dette selvstudium, skal du følgende:

+ Et konfigureret til at køre [Maven](http://maven.apache.org/)Java-udviklingsmiljø. I dette selvstudium antager vi [Eklipse](https://www.eclipse.org/).

+ En active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1.  Køre klassen **LogTopology** fra Eklipse, og derefter vente på den til at starte modtagere af alle partitioner.

2.  Kør **afsender** projektet, skal du trykke på **Enter** i vinduet console, og se hændelserne, der vises i vinduet modtager.

    ![][22]

> [AZURE.NOTE] I dette selvstudium kun at bruge Storm i lokal tilstand til udviklingsformål. Se [HDInsight Storm oversigt][] og dokumentationen [Apache Storm][] officielle kan finde flere oplysninger til Storm installationer og -mønstre.

## <a name="next-steps"></a>Næste trin

I følgende ressourcer er tilgængelige for udviklingsprogrammer integrere begivenhed Hubs og Storm.

- [Analysere føler data med Storm og HDInsight] er en fuld scenarie selvstudium ved hjælp af begivenhed hubber, Storm og HBase til indtager føler data i en Hadoop-klynge.
- [Udvikle streaming databehandling programmer med SCP.NET og C# på Storm og HDInsight][] er et selvstudium om, hvordan du kan skrive Storm rørledninger ved hjælp af C#.

<!-- Images. -->
[22]: ./media/event-hubs-java-storm-getstarted/receive-storm2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Oversigt over Hubs begivenhed]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Oversigt over HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[Analysere føler data med Storm og HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Udvikle streaming databehandling programmer med SCP.NET og C# på Storm og HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 