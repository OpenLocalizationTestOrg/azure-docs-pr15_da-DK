<properties
   pageTitle="Skalerbarhed af tjenesten strukturerede services | Microsoft Azure"
   description="Beskriver, hvordan du skalere Service strukturerede services"
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

# <a name="scaling-service-fabric-applications"></a>Skalering Service-strukturen programmer
Azure Service-strukturen gør det nemt at opbygge SVG programmer ved justering af belastning services partitioner og replikaer på alle noder i en klynge. Dette gør det maksimale ressourceforbrug mere effektivt.

Høj skala til tjenesten strukturen programmer kan der opnås på to måder:

1. Skalering på niveauet for partition

2. Skalering på niveauet for tjenesten navn

## <a name="scaling-at-the-partition-level"></a>Skalering på niveauet for partition
Tjenesten strukturen understøtter partitionering en bestemt tjeneste i flere mindre partitioner. [Partitionering oversigt](service-fabric-concepts-partitioning.md) indeholder oplysninger om typerne leverandør farveskemaer, der understøttes. Kopier af hver partition er udbrede knuderne i en klynge. Overvej en tjeneste, der bruger en ranged partitionsskema med en lav nøgle på 0, en høj tast til 99 og fire partitioner. I en tre-node klynge placeret tjenesten på med fire replikaer, som deler ressourcer på hver node, som vist her:

![Partitionslayout med tre noder](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Øge antallet af knuder kan tjenesten strukturen at udnytte ressourcerne på noderne nye ved at flytte nogle af replikaerne til tom noder. Ved at øge antallet af knuder fire, har tjenesten nu tre replikaer, der kører på hver node (i forskellige partitioner), så for bedre ressourceudnyttelse og ydeevne.

![Partitionslayout med fire noder](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Skalering på niveauet for tjenesten navn
En forekomst af tjenesten er en bestemt forekomst af et programnavn og en tjeneste Skriv et navn (se [Service-strukturen programmets livscyklus](service-fabric-application-lifecycle.md)). Under oprettelse af en tjeneste, du angiver partitionen skema (se [partitionering Service strukturerede services](service-fabric-concepts-partitioning.md)) der skal bruges.

Det første niveau af skalering er ved at navnet på tjenesten. Du kan oprette nye forekomster af en tjeneste, med forskellige niveauer af partitionering, som din ældre forekomster af tjenesten bliver optaget. Dette giver mulighed for nye service forbrugere, der skal bruges forekomster af mindre optaget tjenesten i stedet for dem, der viser flere beskeder.

En af mulighederne for at øge kapacitet samt stigende eller faldende partition tæller, er at oprette en ny forekomst af tjenesten med et nyt partition tema. Dette tilføjer kompleksitet, dog som en hvilken som helst arket klienter behov for at vide, hvornår og hvordan du bruger tjenesten anderledes navngivet.

### <a name="example-scenario-embedded-dates"></a>Eksempel scenarie: integreret datoer
Et muligt scenario ville være at bruge datooplysninger som en del af navnet på tjenesten. Brug en forekomst af tjenesten med et bestemt navn for alle kunder, der har tilsluttet dig i 2013 og et andet navn til kunder, der har tilsluttet dig i 2014. Dette naming skema giver mulighed for at øge navnene afhængigt af datoen fra et program (som 2014 fremgangsmåder forekomsten af for 2014 kan oprettes efter behov).

Denne metode er dog baseret på de klienter, der bruger program-specifikke naming oplysninger, der ikke er omfattet af tjenesten strukturen viden.

- *Ved hjælp af en navngivningsregel*: I 2013, når programmet går direkte, du opretter en tjeneste, kaldet strukturen: / app/service2013. I nærheden af det andet kvartal af 2013, du opretter en anden tjeneste, kaldet strukturen: / app/service2014. Begge af disse tjenester er den samme type. I denne metode skal kunden anvende logik at opbygge navnet på den relevante service, baseret på året.

- *Ved hjælp af en opslag-tjeneste*: en anden mønster er at tilvejebringe en sekundær opslag-tjeneste, som kan angive navnet på tjenesten for den ønskede nøgle. Nye forekomster af tjenesten kan derefter oprettes af tjenesten opslag. Tjenesten lookup selve bevare ikke programmet data, kun data om tjenestenavne, der oprettes. Dermed, for år-baserede ovenstående eksempel klienten ville første kontaktperson Tjenesten Opslag til at finde ud af navnet på tjenesten håndtering af data for et bestemt år, og derefter bruge dette service navn for at udføre handlingen faktisk. Resultatet af den første opslag kan cachelagres.

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger om tjenesten strukturen begreber, se følgende:

- [Tilgængeligheden af tjenesten strukturerede services](service-fabric-availability-services.md)

- [Partitionering Service strukturerede services](service-fabric-concepts-partitioning.md)

- [Definere og administrere tilstand](service-fabric-concepts-state.md)
