<properties
    pageTitle="Introduktion til begivenhed Hubs i C# | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med Azure begivenhed Hubs; sende begivenheder i C# og modtage dem i Java ved hjælp af EventProcessorHost."
    services="event-hubs"
    documentationCenter=""
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/27/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en tjeneste, der behandler store datamængder begivenhed (telemetri) fra forbundne enheder og programmer. Når du indsamle data i begivenhed Hubs, kan du gemme dataene ved hjælp af en lagerplads klynge eller transformere den med en realtid analytics-udbyder. Denne omfattende begivenhed indsamling og behandling ikke er en vigtig komponent i moderne programmet arkitekturer, herunder Internet af ting (IoT).

Dette selvstudium viser, hvordan du bruger Azure klassisk portalen til at oprette en begivenhed Hub. Også viser, hvordan du kan samle meddelelser i en begivenhed Hub ved hjælp af en console-program, der er skrevet i C#, og hvordan du hente dem på parallelle ved hjælp af biblioteket Java begivenhed Processor Host.

For at fuldføre dette selvstudium, skal du bruge følgende:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ En active Azure-konto. <br/>Hvis du ikke har en, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

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
- [Oversigt over Hubs begivenhed][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
