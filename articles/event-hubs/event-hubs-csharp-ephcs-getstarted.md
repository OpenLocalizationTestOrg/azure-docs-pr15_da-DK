<properties
    pageTitle="Introduktion til begivenhed Hubs i C# | Microsoft Azure"
    description="Følge dette selvstudium for at komme i gang ved hjælp af Azure begivenhed Hubs med C# og EventProcessorHost."
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
    ms.date="09/02/2016"
    ms.author="jotaub;sethm"/>

# <a name="get-started-with-event-hubs"></a>Introduktion til begivenhed Hubs

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## <a name="introduction"></a>Introduktion

Begivenhed Hubs er en tjeneste, der behandler store datamængder begivenhed (telemetri) fra forbundne enheder og programmer. Når du indsamle data i begivenhed Hubs, kan du gemme dataene ved hjælp af en lagerplads klynge eller transformere den med en realtid analytics-udbyder. Denne omfattende begivenhed indsamling og behandling ikke er en vigtig komponent i moderne programmet arkitekturer, herunder Internet af ting (IoT).

Dette selvstudium viser, hvordan du bruger Azure klassisk portalen til at oprette en begivenhed Hub. Også viser, hvordan du kan samle meddelelser i en begivenhed Hub ved hjælp af en console-program, der er skrevet i C#, og hvordan du kan hente dem på parallelle ved hjælp af biblioteket C# [Begivenhed Processor Host][] .

For at fuldføre dette selvstudium, skal du bruge følgende:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ En active Azure-konto. Hvis du ikke har en, kan du oprette en gratis konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/free/).

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## <a name="run-the-applications"></a>Køre programmer

Nu er du klar til at køre programmerne.

1. Fra i Visual Studio, skal du åbne projektet **modtager** du oprettede tidligere.

2. Højreklik på løsning **modtager** og derefter klikke på **Tilføj**, og klik derefter på **Eksisterende projekt**.
 
3. Find den eksisterende Sender.csproj-fil, og dobbeltklik på den for at føje det til løsningen.
 
4. Igen, skal du højreklikke på **modtageren** løsningen, og klik derefter på **Egenskaber**. Siden **modtager** egenskab vises.

5. Klik på **Start projekt**, og derefter klikke på knappen **flere Start projekter** . Angiv feltet **Action** for både **modtager** og **afsender** projekter til at **starte**.

    ![][19]

6. Klik på **Projektafhængigheder**. Klik på **afsender**i boksen **projekter** . Kontrollér, at **modtageren** er markeret i feltet **afhænger af** .

    ![][20]

7. Klik på **OK** for at lukke dialogboksen **Egenskaber** .

1.  Tryk på F5 for at køre **modtager** projektet fra Visual Studio, og derefter vente på den til at starte modtagere af alle partitioner.

    ![][21]

2.  **Afsenderen** projektet køres automatisk. Tryk på **Enter** i konsoltræet, og se hændelserne, der vises i vinduet modtager.

    ![][22]

Tryk på **Ctrl + C** i vinduet **afsenderen** for at afslutte programmet afsender, og tryk på **Enter** i vinduet modtager at lukke pågældende program.

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet et arbejde-program, der opretter en begivenhed Hub og sender og modtager data, kan du gå videre til følgende scenarier:

- En komplet [Northwind, der bruger begivenhed Hubs][].
- Eksemplet [Skaler ud begivenhed behandling med begivenhed Hubs][] .
- [Oversigt over Hubs begivenhed][]

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Begivenhed Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Oversigt over Hubs begivenhed]: event-hubs-overview.md
[eksempelprogram, der bruger begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Skalere ud begivenhed behandling med begivenhed Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[queued messaging solution]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 
