<properties
    pageTitle="Introduktion til begivenhed Hubs i C | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med Azure begivenhed Hubs; afsendelse af hændelser i C og modtager dem i Java ved hjælp af begivenhed Processor Host."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="c"
    ms.devlang="csharp"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en meget SVG indtagelse system, der kan indtag millioner af hændelser sekundet aktivere et program til at behandle og analysere store datamængder produceret af dine forbundne enheder og programmer. Når der indsamles i begivenhed Hubs, kan du transformere og gemme data ved hjælp af enhver realtid analytics-udbyder eller lagerplads klynge.

Du kan finde flere oplysninger, skal du se [begivenhed Hubs oversigt][].

I dette selvstudium lærer du, hvordan du indtager meddelelser til en begivenhed Hub ved hjælp af en console-program i C, og til at hente dem på parallelle ved hjælp af biblioteket C# [Begivenhed Processor Host][] .

For at udføre dette selvstudium skal du følgende:

+ Et C udviklingsmiljø. I dette selvstudium antager vi gcc stablen på en [Azure Linux VM](../virtual-machines/virtual-machines-linux-quick-create-cli.md) med Ubuntu 14.04. Instruktioner til andre miljøer vil blive leveret i eksterne links.

+ Microsoft Visual Studio Express til Windows

+ En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure gratis prøveversion</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1.  Kør **modtager** projektet.

    ![][21]

2.  Køre programmet **afsender** , og Bemærk hændelserne, der vises i vinduet modtager.

    ![][24]

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet et arbejde-program, der opretter en begivenhed Hub og sender og modtager data, kan du gå videre til følgende scenarier:

- En komplet [Northwind, der bruger begivenhed Hubs][].
- Eksemplet [Skaler ud begivenhed behandling med begivenhed Hubs][] .
- [Oversigt over Hubs begivenhed][]

<!-- Images. -->
[21]: ./media/event-hubs-c-ephjava-getstarted/ephjava.png
[24]: ./media/event-hubs-c-ephjava-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Begivenhed Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
