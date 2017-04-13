<properties
   pageTitle="Pålidelige aktører noter på Agent Skriv serialisering | Microsoft Azure"
   description="I denne artikel beskrives grundlæggende krav til at definere serialiserbar klasser, der kan bruges til at definere Service-strukturen pålidelig aktører stater og grænseflader"
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
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Skriv noter på Service-strukturen pålidelig aktører serialisering


Argumenterne for alle metoder, resultattyper af de opgaver, der returneres af hver enkelt metode i en agent grænseflade og objekter, der er gemt i en agent tilstand Manager skal være [Datakontrakt serialiserbar](https://msdn.microsoft.com/library/ms731923.aspx).. Dette gælder også for argumenterne for de metoder, der er defineret i [Agent begivenhed grænseflader](service-fabric-reliable-actors-events.md#actor-events). (Agent begivenhed interface metoder altid returnerer ugyldig.)

## <a name="custom-data-types"></a>Brugerdefinerede datatyper

I dette eksempel følgende Agent grænseflade definerer en metode, der returnerer en brugerdefineret datatype, kaldet `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

Grænsefladen er impelemented ved en agent, som bruger tilstand Manager til at gemme en `VoicemailBox` objekt:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

I dette eksempel på `VoicemailBox` objekt er serienummereret når:
 - Objektet, der overføres mellem en agent forekomst og et opkald.
 - Objektet er gemt i den tilstand Manager, hvor det er bevaret til disk og replikeres til andre noder.
 
Den pålidelige Agent, som bruger DataContract serialisering. Derfor skal brugerdefinerede data-objekter og deres medlemmer være forsynet med attributterne **DataContract** og **DataMember** henholdsvis

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Næste trin
 - [Agent livscyklus for og garbage af websteder](service-fabric-reliable-actors-lifecycle.md)
 - [Agent timerne og påmindelser](service-fabric-reliable-actors-timers-reminders.md)
 - [Agent begivenheder](service-fabric-reliable-actors-events.md)
 - [Agent. flere brugere](service-fabric-reliable-actors-reentrancy.md)
 - [Agent polymorfi og objektorienteret design mønstre](service-fabric-reliable-actors-polymorphism.md)
 - [Agent diagnosticerings- og overvågning af ydeevnen](service-fabric-reliable-actors-diagnostics.md)
