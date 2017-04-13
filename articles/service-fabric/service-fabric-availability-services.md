<properties
   pageTitle="Tilgængeligheden af tjenesten strukturerede services | Microsoft Azure"
   description="I denne artikel beskrives fejl registrering, failover og genoprettelse for tjenester"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="availability-of-service-fabric-services"></a>Tilgængeligheden af tjenesten strukturerede services
Azure Service strukturerede services kan være enten med høj sikkerhed eller uden tilstand. I denne artikel giver et overblik over hvordan Service-strukturen fører tilgængeligheden af en tjeneste i tilfælde af mislykkede forsøg.

## <a name="availability-of-service-fabric-stateless-services"></a>Tilgængeligheden af tjenesten strukturerede uden status services
En uden status tjeneste er en programtjeneste, der ikke har en [lokal fast tilstand](service-fabric-concepts-state.md).

Oprette en uden status tjeneste kræver, at du definerer en forekomst Tæl, som er antallet af forekomster af tjenesten uden tilstand, der skal køres i klyngen. Dette er antallet af kopier af programmet logik, der skal oprettes en forekomst i klyngen. Øge antallet af forekomster er den anbefalede måde af skalering en uden status tjeneste.

Når der registreres en fejl på en hvilken som helst forekomst af en uden status tjeneste, der oprettes en ny forekomst på nogle andre berettiget node i klyngen.

## <a name="availability-of-service-fabric-stateful-services"></a>Tilgængeligheden af tjenesten strukturerede med høj sikkerhed services
En med høj sikkerhed tjeneste har nogle tilstand, der er knyttet til den. En med høj sikkerhed tjeneste er formateret som et sæt af replikaer i tjenesten struktur. Hver replika er en forekomst af koden for den tjeneste, der har en kopi af tilstanden. Læse- og skrivetilladelser handlinger udføres i én replika (kaldet primært). Ændringer i tilstand fra skrive handlinger er *replikeres* til flere andre replikaer (kaldet aktive secondaries). Kombination af primære og aktive sekundær replikaer er replikasættet til tjenesten.

Der kan være kun en primær replika vedligeholdelse læse og skrive anmodninger, men der kan være flere aktive sekundær replikaer. Antallet af aktive sekundær replikaer kan konfigureres, og et stort antal replikaer kan acceptere et større antal samtidige software og hardware mislykkede forsøg.

I tilfælde af en fejl (når den primære replika går ned) Service-strukturen gør et af de aktive sekundære replikaer den nye primære replika. Denne aktive sekundær replika har allerede den opdaterede version af tilstanden (via *Gentagelse*), og det kan fortsætte med at behandle yderligere læse og skrive handlinger.

Denne begrebet – en replika, der enten en primær eller aktiv sekundær – kendes som rollen replika.

### <a name="replica-roles"></a>Replika roller
En replika rolle bruges til at administrere den tilstand, der administreres af pågældende replika livscyklus. En replika, hvis rolle er primære tjenester Læs anmodninger. Det tjenester også skriveanmodninger ved opdatering af dens tilstand og kopiere ændringerne til de aktive secondaries i dets replikasæt. En aktiv sekundær rolle er at modtage tilstandsændringer, som den primære replika har replikeres og opdatere dens visning af tilstanden.

>[AZURE.NOTE] Et højere programming modeller som [pålidelig aktører framework](service-fabric-reliable-actors-introduction.md) abstrakt ikke til stede replika rolle fra udvikleren, som repræsenterer.

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger om tjenesten strukturen begreber, se følgende:

- [Skalerbarhed af tjenesten strukturerede services](service-fabric-concepts-scalability.md)

- [Partitionering Service strukturerede services](service-fabric-concepts-partitioning.md)

- [Definere og administrere tilstand](service-fabric-concepts-state.md)
