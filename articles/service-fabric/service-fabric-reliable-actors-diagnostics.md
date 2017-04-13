<properties
   pageTitle="Aktører diagnosticerings- og overvågning | Microsoft Azure"
   description="I denne artikel beskrives diagnosticering og ydeevne overvågningsfunktioner i tjenesten strukturen pålidelig aktører runtime, herunder begivenheder og tællere i ydeevne fra den."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnosticerings- og overvågning af ydeevnen for pålidelig aktører
Pålidelige aktører runtime udsender [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) begivenheder og [tællere i ydeevne](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Dette giver indsigt i hvordan fungerer runtime og hjælp til fejlfinding og overvågning.

## <a name="eventsource-events"></a>EventSource begivenheder
EventSource udbydernavn for den pålidelige aktører runtime er "Microsoft-ServiceFabric-aktører". Begivenheder fra denne begivenhed kilde vises i vinduet [Diagnosticering begivenheder](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) , når programmet Agent der [foretages fejlfinding i Visual Studio](service-fabric-debugging-your-application.md).

Eksempler på værktøjer og -teknologier, hjælpe med at indsamle og/eller vise EventSource hændelser er [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Azure diagnosticering](../cloud-services/cloud-services-dotnet-diagnostics.md), [Semantisk logføring](https://msdn.microsoft.com/library/dn774980.aspx)og [Microsoft TraceEvent bibliotek](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Nøgleord
Alle hændelser, der hører til pålidelig aktører EventSource er knyttet til et eller flere nøgleord. Dette gør det muligt for filtrering af hændelser, der er indsamlet. Følgende nøgleord bit er defineret.

|Bit|Beskrivelse|
|---|---|
|0x1|Sæt vigtige begivenheder, som opsummerer handlingen af strukturen aktører runtime.|
|0x2|Sæt af hændelser, der beskriver Agent metode opkald. Du kan finde yderligere oplysninger finder [introduktionsemnet på aktører](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Sæt af hændelser, der er relateret til Agent tilstand. Du kan finde flere oplysninger under emnet [Agent tilstand administration](service-fabric-reliable-actors-state-management.md).|
|0x8|Sæt af hændelser, der er relateret til slå-baseret på dokumentsammenfald i agenten. Du kan finde flere oplysninger under emnet [på dokumentsammenfald](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Tællere i ydeevne
Pålidelige aktører runtime definerer følgende ydeevne tæller kategorier.

|Kategori|Beskrivelse|
|---|---|
|Tjenesten strukturen Agent|Tællere specifikke for Azure Service strukturen aktører, f.eks. tid tager at gemme Agent tilstand|
|Tjenesten strukturen Agent metode|Tællere specifikke for metoder, der er implementeret af tjenesten strukturen aktører, f.eks. hvor ofte en agent metode startes|

Hver af disse kategorier har en eller flere tællere.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) programmet, der er tilgængelige som standard i Windows-operativsystemet kan bruges til at indsamle og få vist tæller ydelsesdata. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) er en anden mulighed for at indsamle ydelsesdata, tæller og overføre den til Azure tabeller.

### <a name="performance-counter-instance-names"></a>Ydeevnen tæller forekomstnavne
En klynge, der indeholder et stort antal Agent services eller Agent service partitioner har et stort antal Agent ydeevne tællerforekomster. Ydeevnen tæller forekomstnavnene kan hjælpe med at identificere den specifikke [partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) og Agent metode (hvis relevant), den ydeevne tæller forekomst er knyttet til.

#### <a name="service-fabric-actor-category"></a>Kategorien Service-strukturen Agent
For kategorien `Service Fabric Actor`, der tæller forekomstnavne i følgende format:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* er Strengrepræsentationen af tjenesten strukturen partition-ID'ET, som er tilknyttet den ydeevne tæller forekomst. Partition ID er et GUID og dens strengrepræsentation genereres gennem den [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) metode med format angiver "D".

*ActorRuntimeInternalID* er Strengrepræsentationen af et 64-bit heltal, der er oprettet af strukturen aktører runtime til den interne brug. Dette er inkluderet i ydeevne tæller forekomstnavn for at sikre at dens entydige og undgå konflikt med andre ydeevne tæller forekomstnavne. Brugere skal ikke forsøge at fortolke denne del af navnet på den ydeevne tæller forekomst.

Følgende er et eksempel på et tæller forekomstnavn til en tæller, der hører til den `Service Fabric Actor` kategori:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

I eksemplet ovenfor, `2740af29-78aa-44bc-a20b-7e60fb783264` er Strengrepræsentationen af tjenesten strukturen partition ID, og `635650083799324046` 64-bit-ID, der genereres for runtime er interne bruge.

#### <a name="service-fabric-actor-method-category"></a>Tjenesten strukturen Agent metode kategori
For kategorien `Service Fabric Actor Method`, der tæller forekomstnavne i følgende format:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* er navnet på metoden Agent, som er tilknyttet den ydeevne tæller forekomst. Formatet af navnet på metoden bestemmes baseret på nogle logik i til kørsel af strukturen aktører, der afbalancerer læsbarheden af navn med begrænsninger på den maksimale længde af ydeevnen tæller forekomstnavnene i Windows.

*ActorsRuntimeMethodId* er Strengrepræsentationen af en 32-bit heltal, der genereres af strukturen aktører runtime til den interne brug. Dette er inkluderet i ydeevne tæller forekomstnavn for at sikre at dens entydige og undgå konflikt med andre ydeevne tæller forekomstnavne. Brugere skal ikke forsøge at fortolke denne del af navnet på den ydeevne tæller forekomst.

*ServiceFabricPartitionID* er Strengrepræsentationen af tjenesten strukturen partition-ID'ET, som er tilknyttet den ydeevne tæller forekomst. Partition ID er et GUID og dens strengrepræsentation genereres gennem den [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) metode med format angiver "D".

*ActorRuntimeInternalID* er Strengrepræsentationen af et 64-bit heltal, der er oprettet af strukturen aktører runtime til den interne brug. Dette er inkluderet i ydeevne tæller forekomstnavn for at sikre at dens entydige og undgå konflikt med andre ydeevne tæller forekomstnavne. Brugere skal ikke forsøge at fortolke denne del af navnet på den ydeevne tæller forekomst.

Følgende er et eksempel på et tæller forekomstnavn til en tæller, der hører til den `Service Fabric Actor Method` kategori:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

I eksemplet ovenfor, `ivoicemailboxactor.leavemessageasync` er navnet på metoden `2` er 32-bit ID genereres for den runtime intern brug, `89383d32-e57e-4a9b-a6ad-57c6792aa521` er Strengrepræsentationen af tjenesten strukturen partition ID, og `635650083804480486` er det 64-bit-ID, der er oprettet for runtime er interne bruger.

## <a name="list-of-events-and-performance-counters"></a>Liste over begivenheder og tællere i ydeevne

### <a name="actor-method-events-and-performance-counters"></a>Agent metode begivenheder og tællere i ydeevne
Pålidelige aktører runtime udsender følgende hændelser relateret til [Agent metoder](service-fabric-reliable-actors-introduction.md#actors).

|Navn på denne begivenhed|Hændelsesid|Niveau|Nøgleord|Beskrivelse|
|---|---|---|---|---|
|ActorMethodStart|7|Detaljeret|0x2|Aktører runtime er ved at aktivere en agent metode.|
|ActorMethodStop|8|Detaljeret|0x2|En agent metode er blevet udført. Det vil sige, at runtime asynkron kald til metoden Agent returnerede, og den opgave, der returneres af metoden Agent er færdig.|
|ActorMethodThrewException|9|Advarsel|0x3|Der opstod en undtagelse under kørsel af en agent metode, enten under den runtime asynkron kald til metoden agent eller under udførelse af opgaven returneres af metoden Agent. Denne hændelse angiver en slags fejl i koden Agent, der skal undersøgelse.|

Pålidelige aktører runtime udgiver de følgende tællere i ydeevne, relateret til udførelse af Agent metoder.

|Kategorinavn|Tællernavn|Beskrivelse|
|---|---|---|
|Tjenesten strukturen Agent metode|Aktivering/Sec|Antal gange, metoden Agent tjenesten startes sekundet|
|Tjenesten strukturen Agent metode|Gennemsnitlig millisekunder per aktivering|Tid det tager at udføre metoden Agent tjeneste i millisekunder|
|Tjenesten strukturen Agent metode|Undtagelser udløst/Sec|Antal gange, at agenten-metode udløste en undtagelse sekundet|

### <a name="concurrency-events-and-performance-counters"></a>På dokumentsammenfald begivenheder og tællere i ydeevne
Pålidelige aktører runtime udsender følgende hændelser relateret til [på dokumentsammenfald](service-fabric-reliable-actors-introduction.md#concurrency).

|Navn på denne begivenhed|Hændelsesid|Niveau|Nøgleord|Beskrivelse|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Detaljeret|0x8|Denne hændelse er skrevet i starten af hver ny Aflever en agent. Den indeholder antallet af ventende Agent opkald, der venter på at låse per Agent, håndhæves slå-baseret på dokumentsammenfald.|

Pålidelige aktører runtime udgiver de følgende tællere i ydeevne, der vedrører på dokumentsammenfald.

|Kategorinavn|Tællernavn|Beskrivelse|
|---|---|---|
|Tjenesten strukturen Agent|# af Agent opkald venter på Agent Lås|Antallet af ventende Agent opkald venter på at låse per Agent, håndhæves slå-baseret på dokumentsammenfald|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder per Lås vente|Tid (i millisekunder) at låse per Agent, håndhæves slå-baseret på dokumentsammenfald|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder Agent Lås|Tid (i millisekunder), for hver agent låsen|

### <a name="actor-state-management-events-and-performance-counters"></a>Agent tilstand management begivenheder og tællere i ydeevne
Pålidelige aktører runtime udsender følgende hændelser relateret til [Administration af Agent tilstand](service-fabric-reliable-actors-state-management.md).

|Navn på denne begivenhed|Hændelsesid|Niveau|Nøgleord|Beskrivelse|
|---|---|---|---|---|
|ActorSaveStateStart|10|Detaljeret|0x4|Aktører runtime er ved at gemme tilstanden Agent.|
|ActorSaveStateStop|11|Detaljeret|0x4|Aktører runtime er færdig med at gemme tilstanden Agent.|

Pålidelige aktører runtime udgiver de følgende tællere i ydeevne, der vedrører Agent tilstand administration.

|Kategorinavn|Tællernavn|Beskrivelse|
|---|---|---|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder per gemme tilstand handling|Tid, det tager at gemme Agent tilstand i millisekunder|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder per Handlingen Indlæs tilstand|Tid, det tager at indlæse Agent tilstand i millisekunder|

### <a name="events-related-to-actor-replicas"></a>Hændelser, der er relateret til Agent replikaer
Pålidelige aktører runtime udsender følgende hændelser relateret til [Agent replikaer](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Navn på denne begivenhed|Hændelsesid|Niveau|Nøgleord|Beskrivelse|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativ|0x1|Agent replika ændres rolle til primær. Det betyder, at aktørerne for denne partition der oprettes i denne replika.|
|ReplicaChangeRoleFromPrimary|2|Informativ|0x1|Agent replika ændres rolle til ikke-primær. Det betyder, at aktørerne for denne partition oprettes ikke længere i denne replika. Ingen nye anmodninger vil blive leveret til aktører allerede har oprettet i denne replika. Aktører bliver slettet, når du har udført igangværende anmodninger.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Agent aktivering og deaktivering af begivenheder og tællere i ydeevne
Pålidelige aktører runtime udsender følgende hændelser relateret til [Agent aktivering og deaktivering af](service-fabric-reliable-actors-lifecycle.md).

|Navn på denne begivenhed|Hændelsesid|Niveau|Nøgleord|Beskrivelse|
|---|---|---|---|---|
|ActorActivated|5|Informativ|0x1|En agent er aktiveret.|
|ActorDeactivated|6|Informativ|0x1|En agent er blevet deaktiveret.|

Pålidelige aktører runtime udgiver de følgende tællere i ydeevne, der vedrører Agent aktivering og deaktivering af.

|Kategorinavn|Tællernavn|Beskrivelse|
|---|---|---|
|Tjenesten strukturen Agent|Beregne gennemsnittet af OnActivateAsync millisekunder|Tid det tager at udføre OnActivateAsync metode i millisekunder|

### <a name="actor-request-processing-performance-counters"></a>Behandling af mødeindkaldelser Agent tællere i ydeevne
Når en klient kalder en metode via en agent proxy-objekt, medfører det en anmodning om meddelelse, der sendes via netværket til tjenesten Agent. Tjenesten behandler anmodningsmeddelelsen og sender svar tilbage til klienten. Pålidelige aktører runtime udgiver de følgende tællere i ydeevne, der vedrører Agent behandling af mødeindkaldelser.

|Kategorinavn|Tællernavn|Beskrivelse|
|---|---|---|
|Tjenesten strukturen Agent|# af udestående anmodninger|Antallet af anmodninger, der behandles i tjenesten|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder per anmodning|Tid (i millisekunder) af tjenesten at behandle en anmodning|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder til anmodning om fjernelse af serienummeret|Tid (i millisekunder) at deserialisere Agent anmodningsmeddelelsen, når den modtages på tjenesten|
|Tjenesten strukturen Agent|Gennemsnitlig millisekunder for svar serialisering|Tid (i millisekunder) at sekventielt svarmeddelelse Agent på tjenesten, inden svaret sendes til klienten|

## <a name="next-steps"></a>Næste trin
 - [Hvordan pålidelig aktører bruge tjenesten strukturen platform](service-fabric-reliable-actors-platform.md)
 - [Agent API referencedokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Eksempel på kode](https://github.com/Azure/servicefabric-samples)
