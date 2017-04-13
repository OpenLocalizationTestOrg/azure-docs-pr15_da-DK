<properties
    pageTitle="Introduktion til begivenhed Hubs i Java | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med Azure begivenhed Hubs; sende hændelser med Java og modtage dem på ved hjælp af EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="core"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en meget SVG indtagelse system, der kan indtag millioner af hændelser sekundet aktivere et program til at behandle og analysere store datamængder produceret af dine forbundne enheder og programmer. Når der indsamles i begivenhed Hubs, kan du transformere og gemme data ved hjælp af enhver realtid analytics-udbyder eller lagerplads klynge.

Få vist [begivenhed Hubs oversigt][]kan finde flere oplysninger.

Dette selvstudium viser, hvordan til indtager meddelelser til en begivenhed Hub ved hjælp af en console-program i Java, og til at hente dem på parallelle ved hjælp af biblioteket Java begivenhed Processor Host.

For at udføre dette selvstudium, skal du følgende:

+ Et Java udviklingsmiljø. I dette selvstudium antager vi [Eklipse](https://www.eclipse.org/).

+ En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure gratis prøveversion</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-java](../../includes/service-bus-event-hubs-get-started-send-java.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1.  Kør **modtager** projektet.

    ![][21]

2.  Kør **afsender** projektet.

    ![][22]

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet et arbejde-program, der opretter en begivenhed Hub og sender og modtager data, kan du gå videre til følgende scenarier:

- En komplet [Northwind, der bruger begivenhed Hubs][].
- Eksemplet [Skaler ud begivenhed behandling med begivenhed Hubs][] .

Du kan finde yderligere oplysninger finder [Java Developer Center](/develop/java/).

<!-- Images. -->
[21]: ./media/event-hubs-java-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-java-ephjava-getstarted/java-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 