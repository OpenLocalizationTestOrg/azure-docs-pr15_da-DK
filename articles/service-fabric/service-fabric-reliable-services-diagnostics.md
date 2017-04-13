<properties
   pageTitle="Med høj sikkerhed pålidelig Services diagnosticering | Microsoft Azure"
   description="Diagnosticering funktionalitet for med høj sikkerhed pålidelig tjenester"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/17/2016"
   ms.author="alanwar"/>

# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnosticering funktionalitet for med høj sikkerhed pålidelig tjenester
Klassen med høj sikkerhed pålidelig Services StatefulServiceBase udsender [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) hændelser, der kan bruges til at foretage fejlfinding af tjenesten, giver indsigt i hvordan på kørselstidspunktet fungerer, og hjælp til fejlfinding.

## <a name="eventsource-events"></a>EventSource begivenheder
EventSource navnet på klassen med høj sikkerhed pålidelig Services StatefulServiceBase er "Microsoft-ServiceFabric-tjenester". Begivenheder fra denne begivenhed kilde vises i vinduet [Diagnosticering begivenheder](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) , når tjenesten der [foretages fejlfinding i Visual Studio](service-fabric-debugging-your-application.md).

Eksempler på værktøjer og -teknologier, hjælpe med at indsamle og/eller vise EventSource hændelser er [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Microsoft Azure diagnosticering](../cloud-services/cloud-services-dotnet-diagnostics.md)og [Microsoft TraceEvent bibliotek](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Begivenheder

|Navn på denne begivenhed|Hændelsesid|Niveau|Beskrivelse af begivenhed|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativ|Når tjenesten RunAsync opgave startes|
|StatefulRunAsyncCancellation|2|Informativ|Når tjenesten RunAsync opgave er annulleret|
|StatefulRunAsyncCompletion|3|Informativ|Når tjenesten RunAsync opgave er afsluttet|
|StatefulRunAsyncSlowCancellation|4|Advarsel|Når tjenesten RunAsync opgave tager for lang tid at fuldføre aflysning|
|StatefulRunAsyncFailure|5|Fejl|Når tjenesten RunAsync opgave medfører en undtagelse|

## <a name="interpret-events"></a>Fortolke begivenheder

StatefulRunAsyncInvocation, StatefulRunAsyncCompletion og StatefulRunAsyncCancellation hændelser er nyttige til tjenesten writer at forstå livscyklus for en tjeneste – samt en tidsindstilling for når en tjeneste startes, annulleret eller afsluttet. Det kan være nyttige når fejlfinding af problemer med tjenesten eller forstå service livscyklus.

Tjenesten forfattere skal være opmærksom på StatefulRunAsyncSlowCancellation og StatefulRunAsyncFailure hændelser, fordi de angiver problemer med tjenesten.

StatefulRunAsyncFailure udsendes, når tjenesten RunAsync() opgaven medfører en undtagelse. En undtagelse udløst angiver typisk en fejl eller fejl i tjenesten. Desuden får undtagelsen tjenesten mislykkes, så den er flyttet til en anden node. Dette kan være en dyr handling og kan forsinke indgående anmodninger, mens tjenesten flyttes. Tjenesten forfattere skal årsagen til undtagelsen og, hvis det er muligt at reducere den.

StatefulRunAsyncSlowCancellation udsendes, når en annullering anmodning om opgaven RunAsync tager længere tid end fire sekunder. Når en tjeneste tager for lang tid at fuldføre annulleringen, påvirker muligheden for tjenesten genstartes hurtigt på en anden node. Dette kan påvirke den overordnede tilgængelighed til tjenesten.
