<properties
    pageTitle="Introduktion til begivenhed Hubs i C# med Apache Storm | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang med Azure begivenhed Hubs; afsendelse af hændelser i C# og modtage dem i en Apache Storm klynge."
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
    ms.topic="article" 
    ms.date="09/06/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en meget SVG indtagelse system, der kan indtag millioner af hændelser sekundet aktivere et program til at behandle og analysere store datamængder produceret af dine forbundne enheder og programmer. Når der indsamles i begivenhed Hubs, kan du transformere og gemme data ved hjælp af enhver realtid analytics-udbyder eller lagerplads klynge.

Du kan finde flere oplysninger, skal du se [begivenhed Hubs oversigt].

I dette selvstudium lærer du, hvordan du indtager meddelelser til en begivenhed Hub ved hjælp af en console-program i C#, og til at hente dem parallelt ved at bruge Apache Storm.

For at udføre dette selvstudium skal du følgende:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Et konfigureret til at køre [Maven](http://maven.apache.org/)Java-udviklingsmiljø. I dette selvstudium antager vi [Eklipse](https://www.eclipse.org/)

+ En active Azure-konto. <br/>Hvis du ikke har en konto, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure gratis prøveversion</a>.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1.  Køre klassen **LogTopology** fra Eklipse, og derefter vente på den til at starte modtagere af alle partitioner.

2.  Kør **afsender** projektet, skal du trykke på **Enter** i vinduet console, og se hændelserne, der vises i vinduet modtager.

    ![][22]

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet et arbejde-program, der opretter en begivenhed Hub og sender og modtager data, kan du gå videre til følgende scenarier:

- En komplet [Northwind, der bruger begivenhed Hubs][].
- Eksemplet [Skaler ud begivenhed behandling med begivenhed Hubs][] .

<!-- Images. -->
[22]: ./media/event-hubs-csharp-storm-getstarted/receive-storm1.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
 