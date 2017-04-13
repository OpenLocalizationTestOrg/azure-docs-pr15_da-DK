<properties
   pageTitle="Polymorfi i pålidelige aktører framework | Microsoft Azure"
   description="Oprette hierarkier af .NET grænseflader og typer i pålidelige aktører framework genbruge funktionalitet og API definitioner."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfi i en pålidelig aktører ramme

Den pålidelige aktører, som gør det muligt at opbygge aktører ved hjælp af mange af de samme metoder, du vil bruge i objektorienteret design. En af disse metoder er polymorfi, som gør det muligt for typer og grænseflader for at nedarve fra mere generalized overordnede. Nedarvning af i den pålidelige aktører, som følger Generelt .NET modellen med et par yderligere begrænsninger.

## <a name="interfaces"></a>Grænseflader

Den pålidelige aktører, som kræver, at du til at definere mindst én grænseflade skal indføres af typen Agent. Denne grænseflade bruges til at oprette en proxyklasse, der kan bruges af klienter til at kommunikere med dine aktører. Grænseflader kan nedarve fra andre grænseflader, som hver brugergrænseflade, der er implementeret af typen Agent og alle dens overordnede i sidste ende er afledt af IActor. IActor er platform brugerdefineret base grænseflade til aktører. Eksemplet klassisk polymorfi ved hjælp af figurer kan derfor se nogenlunde sådan ud:

![Interface hierarki for figur aktører][shapes-interface-hierarchy]


## <a name="types"></a>Datatyper

Du kan også oprette et hierarki af Agent typer, der er afledt af den grundlæggende agentklasse, som leveres af platformen. I forbindelse med figurer, du muligvis en base `Shape` type:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes af `Shape` kan tilsidesætte metoder fra bunden.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Bemærk de `ActorService` attribut på typen Agent. Denne attribut fortæller en pålidelig Agent ramme, der automatisk skal oprettes en tjeneste til at være vært aktører af denne type. I nogle tilfælde kan du oprette en grundlæggende type, der er kun beregnet til at dele funktionalitet med undertyper og aldrig bruges til at oprette en forekomst af cement aktører. I disse tilfælde skal du bruge den `abstract` nøgleord for at angive, at du aldrig vil oprette en agent, der er baseret på typen.


## <a name="next-steps"></a>Næste trin

- Se, [hvordan den pålidelige aktører, som bruger tjenesten strukturen platformen](service-fabric-reliable-actors-platform.md) til at levere pålidelighed, skalerbarhed og konsistent tilstand.
- Få mere at vide om [Agent livscyklus](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
