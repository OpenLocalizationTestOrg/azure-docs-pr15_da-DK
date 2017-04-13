<properties
   pageTitle="Fejlfinding i forbindelse med event-sporing | Microsoft Azure"
   description="De mest almindelige problemer, der er stødt på under installation af tjenester på Microsoft Azure Service struktur."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Foretage fejlfinding af almindelige problemer, når du installerer tjenester på Azure Service struktur

Når du kører tjenester på computeren udvikler, er det nemt at bruge [Visual Studio fejlfindingsværktøjer](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). [Tilstandsrapporter](service-fabric-view-entities-aggregated-health.md) er altid et godt sted at starte til remote klynger. Den nemmeste måde at få adgang til disse rapporter er via PowerShell eller [SFX](service-fabric-visualizing-your-cluster.md). Denne artikel forudsætter, at du foretager fejlfinding af en ekstern klynge og har et grundlæggende kendskab til, hvordan du bruger en af disse værktøjer.

##<a name="application-crash"></a>Programmet går ned
Den "Partition er under målrette replika eller forekomst Tæl" rapporten er en god tegn på, at din tjeneste går ned. Hvis du vil finde ud af, tager hvor din tjeneste går ned lidt flere undersøgelse. Når din tjeneste kører skaleres, bliver dine leve et sæt af well-thought fra sporinger.  Vi anbefaler, at du prøver [Azure diagnosticering](service-fabric-diagnostics-how-to-setup-wad.md) for indsamling af disse sporinger og bruge en løsning som [Elastiske søgning](service-fabric-diagnostic-how-to-use-elasticsearch.md) til visning og søgning på spor.

![SFX Partition tilstand](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###<a name="during-service-or-actor-initialization"></a>Under initialisering af tjenesten eller en agent
Eventuelle undtagelser, før tjenestetypen initialiseret medfører processen til at gå ned. For disse filtyper går ned viser programmets hændelseslog fejlen fra din tjeneste.
Dette er de mest almindelige undtagelser til at se, før tjenesten er initialiseret.

***System.IO.FileNotFoundException***

Denne fejl er ofte på grund af manglende samling afhængigheder. Kontrollere egenskaben CopyLocal i Visual Studio eller den globale samling cache for noden.

***System.Runtime.InteropServices.COMException***
 *på System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory (IntPtr, IFabricStatefulServiceFactory)*
 
 Dette angiver, navnet på registrerede service ikke stemmer overens med tjenestemanifestet.

[Azure diagnosticering](service-fabric-diagnostics-how-to-setup-wad.md) kan konfigureres til at overføre programmets hændelseslog for alle noder automatisk.

###<a name="runasync-or-onactivateasync"></a>RunAsync() eller OnActivateAsync()
Hvis et nedbrud sker der i løbet af initialisering eller kørsel af din registrerede tjenestetype eller en agent, blive fundet undtagelsen ved Azure Service struktur. Du kan få vist disse fra udbyderne EventSource gennemgås i afsnittet "Næste trin".

## <a name="next-steps"></a>Næste trin

Lær mere om eksisterende diagnosticering, der leveres af tjenesten strukturen:

* [Pålidelige aktører diagnosticering](service-fabric-reliable-actors-diagnostics.md)
* [Pålidelige Services diagnosticering](service-fabric-reliable-services-diagnostics.md)
