<properties
   pageTitle="Pålidelige aktører begivenheder | Microsoft Azure"
   description="Introduktion til begivenheder til tjenesten strukturen pålidelig aktører."
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
   ms.date="08/30/2016"
   ms.author="amanbha"/>


# <a name="actor-events"></a>Agent begivenheder
Agent begivenheder kan bruges til at sende bedste evne meddelelser fra agenten til klienterne. Agent hændelser er udviklet til Agent-klient kommunikation og der ikke bør benyttes til Agent-Agent kommunikation.

De følgende kodestykker viser, hvordan du bruger Agent begivenheder i dit program.

Definere en brugergrænseflade, der beskriver de hændelser, der er publiceret af aktør. Denne grænseflade skal være afledt af den `IActorEvents` interface. Argumenterne af metoder skal være [data sammentrække serialiserbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metoderne skal returnere tomme, som begivenhed meddelelser, der er en måde og bedste kræfter.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Definer de hændelser, der er udgivet af Agent i grænsefladen Agent.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

Implementere hændelseshandleren på klientsiden.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Oprette en proxy til Agent, hvor der publiceres begivenheden på klienten, og abonnere på dets begivenheder.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

I tilfælde af failover mislykkes muligvis agenten til en anden proces eller node. Agent proxy administrerer aktive abonnementer og abonnerer automatisk dem igen. Du kan styre intervallet fornyet abonnement via den `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Hvis du vil ophæve abonnement, du bruger den `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

På agenten blot Udgiv hændelserne, når de foretager. Hvis der er abonnenter på begivenheden, sende aktører runtime dem beskeden.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## <a name="next-steps"></a>Næste trin
 - [Agent. flere brugere](service-fabric-reliable-actors-reentrancy.md)
 - [Agent diagnosticerings- og overvågning af ydeevnen](service-fabric-reliable-actors-diagnostics.md)
 - [Agent API referencedokumentation](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Eksempel på kode](https://github.com/Azure/servicefabric-samples)
