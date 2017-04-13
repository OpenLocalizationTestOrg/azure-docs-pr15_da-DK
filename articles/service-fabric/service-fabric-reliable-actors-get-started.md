<properties
   pageTitle="Introduktion til tjenesten strukturen pålidelig aktører | Microsoft Azure"
   description="Dette selvstudium vejleder dig gennem trinnene til oprettelse af fejlfinding og implementere en enkelt Agent-baseret tjeneste, der bruger tjenesten strukturen pålidelig aktører."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="getting-started-with-reliable-actors"></a>Introduktion til pålidelig aktører

> [AZURE.SELECTOR]
- [C# i Windows](service-fabric-reliable-actors-get-started.md)
- [Java på Linux](service-fabric-reliable-actors-get-started-java.md)

I denne artikel forklares de grundlæggende elementer af Azure Service-strukturen pålidelig aktører og vejleder dig gennem oprettelse, fejlfinding og implementere et enkelt pålidelig Agent-program i Visual Studio.

## <a name="installation-and-setup"></a>Installation og konfiguration
Inden du starter, Sørg for, at du har udviklingsmiljø Service-strukturen, konfigurere på din computer.
Hvis du vil konfigurere det, skal du se detaljerede oplysninger om, [hvordan du konfigurerer udviklingsmiljøet](service-fabric-get-started.md).

## <a name="basic-concepts"></a>Grundlæggende begreber
For at komme i gang med pålidelig aktører, skal du forstår nogle grundlæggende begreber:

 * **Agent tjeneste**. Pålidelige aktører er pakket i pålidelige tjenester, der kan være installeret i tjenesten strukturen for infrastruktur. Agent forekomster er aktiveret i en forekomst af navngivne tjenesten.
 
 * **Agent registrering**. Som med pålidelig Services skal en pålidelig Agent tjeneste være registreret hos tjenesten strukturen runtime. Desuden skal typen Agent være registreret hos Agent runtime.
 
 * **Agent interface**. Grænsefladen Agent bruges til at definere et stærkt indtastede offentlige grænseflade for en agent. I den pålidelige Agent model terminologi definerer grænsefladen Agent typerne meddelelser, som agenten kan forstå og proces. Grænsefladen Agent bruges af andre aktører og klientprogrammer til "send" (asynkront) meddelelser til agenten. Pålidelige aktører kan implementere flere grænseflader.
 
 * **ActorProxy klasse**. Klassen ActorProxy bruges af klientprogrammer til at kalde metoderne fremvises grænsefladen Agent. Klassen ActorProxy indeholder to vigtige funktioner:
    * Navneoversættelse: den er i stand til at finde agenten i klynge (find noden af den klynge, hvor det hostes).
    * Håndtering af fejl: den kan prøv igen metode starter og løse igen Agent placering efter, for eksempel en fejl, der kræver Agent til flyttes til en anden node i klyngen.

Følgende regler, der vedrører Agent grænseflader er værd at nævne:

- Agent interface metoder må ikke være overfyldt.
- Agent interface metoder ikke må have ud, ref eller valgfrie parametre.
- Generisk grænseflader understøttes ikke.

## <a name="create-a-new-project-in-visual-studio"></a>Oprette et nyt projekt i Visual Studio
Når du har installeret Service-strukturen tools til Visual Studio, kan du oprette nye projekttyper. De nye projekttyper er under kategorien **skyen** i dialogboksen **Nyt projekt** .


![Tjenesten strukturen tools til Visual Studio - projekt][1]

I den næste dialogboks, kan du vælge typen projekt, du vil oprette.

![Tjenesten strukturen project-skabeloner][5]

Project HelloWorld vi bruge tjenesten Service-strukturen pålidelig aktører.

Når du har oprettet løsningen, skal du se følgende struktur:

![Tjenesten strukturen projektstruktur][2]

## <a name="reliable-actors-basic-building-blocks"></a>Pålidelige aktører grundlæggende dokumentkomponenter

En typisk pålidelig aktører løsning består af tre projekter:

* **Programmet projektet (MyActorApplication)**. Dette er det projekt, pakker alle de tjenester, der er samlet til installation. Den indeholder *ApplicationManifest.xml* og PowerShell-scripts til administration af programmet.

* **Interface projektet (MyActor.Interfaces)**. Dette er det projekt, der indeholder agenten interfacedefinitionen. Du kan angive de grænseflader, der anvendes af aktører i løsningen i project MyActor.Interfaces. Agent-grænseflader kan defineres i et projekt med en hvilken som helst navn, men grænsefladen definerer den Agent kontrakt, der deles af Agent implementering og de klienter, der kalder agenten, så det typisk giver mening at definere den i en samling, der er adskilt fra Agent implementering og kan deles af flere projekter.

```csharp
public interface IMyActor : IActor
{
    Task<string> HelloWorld();
}
```

* **Agent service projektet (MyActor)**. Dette er det projekt, der bruges til at definere tjenesten Service struktur, der skal være vært for agenten. Den indeholder implementeringen af agenten. En agent implementering er en klasse, der er afledt af typen grundlæggende `Actor` og implementerer den-grænseflader, der er defineret i MyActor.Interfaces projektet. En agentklasse skal også implementere en parametre, der accepterer en `ActorService` forekomst og en `ActorId` og overfører dem sammen med grundtallet `Actor` klasse. Dette giver mulighed for parametre afhængighed indsættelse af platform afhængigheder.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> HelloWorld()
    {
        return Task.FromResult("Hello world!");
    }
}
```

Tjenesten Agent skal være registreret hos en tjenestetype i tjenesten strukturen runtime. I rækkefølge for tjenesten Agent til at køre din Agent forekomster, skal din Agent type også være registreret hos tjenesten Agent. Den `ActorRuntime` registrering metode udfører denne arbejde for aktører.

```csharp
internal static class Program
{
    private static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<MyActor>(
                (context, actorType) => new ActorService(context, actorType, () => new MyActor())).GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Hvis du starter fra et nyt projekt i Visual Studio, og du har kun én Agent definition, er registrering som standard inkluderes på den kode, der genererer Visual Studio. Hvis du definerer andre aktører i tjenesten, skal du tilføje Agent registrering ved hjælp af:

```csharp
 ActorRuntime.RegisterActorAsync<MyOtherActor>();

```

> [AZURE.TIP] Tjenesten strukturen aktører runtime udsender nogle [begivenheder og tællere i ydeevne, der er relateret til Agent metoder](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). De kan bruges i diagnosticerings- og overvågning.


## <a name="debugging"></a>Fejlfinding

Tjenesten strukturen værktøjer til Visual Studio understøtter fejlfinding på din lokale computer. Du kan starte en session med fejlfinding ved at trykke på F5. Visual Studio opbygger (hvis det er nødvendigt) pakker. Den også installeres programmet på den lokale klynge Service-strukturen og vedhæfter fejlfindingsværktøjet.

I forbindelse med installationsprocessen, kan du se status i vinduet **Output** .

![Tjenesten strukturen fejlfinding output vindue][3]


## <a name="next-steps"></a>Næste trin
 - [Hvordan pålidelig aktører bruge tjenesten strukturen platform](service-fabric-reliable-actors-platform.md)
 - [Agent tilstand administration](service-fabric-reliable-actors-state-management.md)
 - [Agent livscyklus for og garbage af websteder](service-fabric-reliable-actors-lifecycle.md)
 - [Agent API referencedokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Eksempel på kode](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
