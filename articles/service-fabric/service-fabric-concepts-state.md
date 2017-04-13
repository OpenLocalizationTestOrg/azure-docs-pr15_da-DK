<properties
   pageTitle="Definere og administrere tilstand | Microsoft Azure"
   description="Sådan definerer og administrere tjenestetilstand i tjenesten struktur"
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

# <a name="service-state"></a>Tjenestetilstand
**Tjenestetilstand** refererer til de data, der kræver tjenesten for at kunne fungere. Den indeholder datastrukturer og variabler, tjenesten læser og skriver til at arbejde.

Overvej en simpel Lommeregner tjeneste, f.eks. Denne tjeneste tager to tal og returnerer deres sum. Dette er en rent uden status tjeneste, der indeholder ingen data, der er knyttet til den.

Nu kan du overveje de samme Lommeregner, men ud over databehandling sum, der er også en metode til at returnere sidste summen det har beregnes. Denne tjeneste er nu med høj sikkerhed – den indeholder en tilstand, som den skriver til (når den beregner en ny sum) og læser fra (når returnerer den sidste beregnede summen).

I Azure Service-strukturen kaldes den første tjeneste en uden status tjeneste. Den anden tjeneste kaldes en med høj sikkerhed tjeneste.

## <a name="storing-service-state"></a>Lagring af tjenestetilstand
Staten kan være enten externalized eller placeret samtidig med den kode, der manipulere tilstanden. Externalization for stat gøres typisk ved hjælp af en ekstern database eller store. Det kan skyldes en SQL-database, hvor det aktuelle resultat er gemt i en tabel i eksemplet Lommeregner. Alle anmodninger til at beregne summen udfører en opdatering i denne række.

Staten kan også placeres sammen med den kode, der opstår denne kode. Med høj sikkerhed tjenester i tjenesten strukturen er oprettet ved hjælp af denne model. Tjenesten strukturen indeholder infrastruktur til at sikre, at denne tilstand er meget tilgængelig og fejlsikrede i tilfælde af en fejl.

## <a name="next-steps"></a>Næste trin

Yderligere oplysninger om tjenesten strukturen begreber, se følgende:

- [Tilgængeligheden af tjenesten strukturerede services](service-fabric-availability-services.md)

- [Skalerbarhed af tjenesten strukturerede services](service-fabric-concepts-scalability.md)

- [Partitionering Service strukturerede services](service-fabric-concepts-partitioning.md)
