<properties
    pageTitle="Introduktion til begivenhed hubber med C og Apache Storm | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med Azure begivenhed Hubs; sende begivenheder i C og modtage dem i en Apache Storm klynge."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="java"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en meget SVG indtagelse system, der kan indtag millioner af hændelser sekundet aktivere et program til at behandle og analysere store datamængder produceret af dine forbundne enheder og programmer. Når der indsamles i begivenhed Hubs, kan du transformere og gemme data ved hjælp af enhver realtid analytics-udbyder eller lagerplads klynge.

Du kan finde flere oplysninger, skal du se [begivenhed Hubs oversigt].

I dette selvstudium lærer du, hvordan du indtager meddelelser til en begivenhed Hub ved hjælp af en console-program i C, og til at hente dem parallelt ved at bruge Apache Storm.

For at fuldføre dette selvstudium, skal du følgende:

+ Et C udviklingsmiljø. I dette selvstudium antager vi gcc stablen på en [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) med Ubuntu 14.04. Instruktioner til andre miljøer vil blive leveret i eksterne links.

+ Et konfigureret til at køre [Maven](http://maven.apache.org/)Java-udviklingsmiljø. I dette selvstudium antager vi [Eklipse](https://www.eclipse.org/).

+ En active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1.  Køre klassen **LogTopology** fra Eklipse, og derefter vente på den til at starte modtagere af alle partitioner.

2.  Køre programmet **afsender** , og se hændelserne, der vises i vinduet modtager.

    ![][23]

> [AZURE.NOTE] I dette selvstudium kun at bruge Storm i lokal tilstand til udviklingsformål. Se [Oversigt over HDInsight Storm] og dokumentationen [Apache Storm] officielle kan finde flere oplysninger til Storm installationer og -mønstre.

## <a name="next-steps"></a>Næste trin

I følgende ressourcer er tilgængelige for udviklingsprogrammer integrere begivenhed Hubs og Storm.

- [Analysere føler data med Storm og HDInsight][] er en fuld scenarie selvstudium ved hjælp af begivenhed hubber, Storm og HBase til indtager føler data i en Hadoop-klynge.
- [Udvikle streaming databehandling programmer med SCP.NET og C# på Storm og HDInsight][] er et selvstudium om, hvordan du kan skrive Storm rørledninger ved hjælp af C#.

<!-- Images. -->
[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Oversigt over Hubs begivenhed]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Oversigt over HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md/
[Analysere føler data med Storm og HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Udvikle streaming databehandling programmer med SCP.NET og C# på Storm og HDInsight]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
