<properties
   pageTitle="Tjenesten strukturen klynge ressourcestyring - forbindelse | Microsoft Azure"
   description="Oversigt over konfigurering forbindelse til tjenesten strukturerede Services"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurere og bruge tjenesten forbindelse i tjenesten struktur

Forbindelse er et kontrolelement, der leveres hovedsageligt for at lette overgangen af større monolitisk programmer i skyen og microservices verden. Det kan også bruges i visse tilfælde som en gyldige optimering til forbedring af ydeevnen af tjenesterne, selvom dette få indflydelse på dog.

Antag, at du bringer en større app eller en, der lige ikke blev udviklet med microservices Husk, at tjenesten struktur. Denne overgang er faktisk fælles, og vi har haft flere kunder (både intern og ekstern) i denne situation. Du starter ved at løfte op på hele app til miljø, få det pakket til at køre. Du starter derefter opdele den i forskellige mindre tjenester, at alle tale med hinanden.

Derefter er der en "Oops...". "Oops" falder normalt i en af disse kategorier:

1. Nogle komponenten X i appen monolitisk havde en udokumenterede afhængighed på Y-komponent, og vi slået blot dem i separate tjenester. Da disse nu kører på forskellige noder i klyngen, er de brudt.
2.  Disse ting kommunikere via (lokal navngivne pipes | delt hukommelse | filer på disk), men jeg virkelig har brug at kunne opdatere uafhængigt til at gå op lidt. Jeg vil fjerne hårde afhængigheden senere.
3.  Alt er fint, men den omdannes ud af, at disse to komponenter er faktisk meget chatty og ydeevne store og små bogstaver. Når de flyttes til separat services overordnede ydeevne i programmet tanked eller ventetid øges. Det overordnede program er derfor ikke opfylder forventninger.

I disse tilfælde vi ikke ønsker at miste vores refactoring arbejde, og ikke vil gå tilbage til monolit, men vi har brug for nogle mening lokalitet. Dette bevares, indtil vi kan omdesigne komponenterne til at arbejde naturligt som tjenester, eller indtil vi kan løse dette ydeevnen i en anden måde, hvis det er muligt.

Sådan gør du? Du kan også prøve at slå forbindelse.

## <a name="how-to-configure-affinity"></a>Sådan konfigureres forbindelse
Hvis du vil oprette forbindelse, definerer du en forbindelse relation mellem to forskellige tjenester. Du kan betragte forbindelse "pege" én tjeneste på en anden og siger "denne tjeneste kan kun køre hvor, tjenesten kører." Nogle gange refererer vi til forbindelsen mellem processorer som et overordnet/underordnet-relationen (hvor du pege på underordnede på overordnet). Forbindelsen mellem processorer sikrer, at replikaer eller forekomster af én tjeneste er placeret på de samme noder som replikaer eller forekomster af en anden.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## <a name="different-affinity-options"></a>Indstillinger for forskellige forbindelse
Forbindelse er repræsenteret via en af flere korrelations farveskemaer og har to forskellige tilstande. Den mest almindelige tilstand af forbindelsen mellem processorer er, hvad vi kalder NonAlignedAffinity. Replikaer eller forekomster af de forskellige tjenester er placeret på samme knuderne i NonAlignedAffinity. Tilstanden andre er AlignedAffinity. Justerede forbindelse er nyttig kun med med høj sikkerhed tjenester. Konfiguration af to med høj sikkerhed services for at har justeret forbindelse sikrer, at primærfarver af disse tjenester er placeret på de samme noder som hinanden. Det får også hvert par af secondaries tjenesterne skal placeres på de samme noder. Det er også muligt (selvom mindre fælles) til at konfigurere NonAlignedAffinity for med høj sikkerhed tjenester. For NonAlignedAffinity forskellige kopier af de to med høj sikkerhed tjenester vil samhuses på de samme noder, men vil være ingen forsøgt at justere deres primærfarver eller secondaries.

![Forbindelse tilstande og deres virkning][Image1]

### <a name="best-effort-desired-state"></a>Bedste indsats beskedteksten tilstand
Der er nogle forskelle mellem forbindelse og monolitisk arkitekturer. Mange af dem der er, fordi en forbindelse relation er den bedste procedure. Tjenesterne på en forbindelse relation er grundlæggende forskellige enheder, der kan mislykkes og flyttes uafhængigt af hinanden. Der er også årsager til, hvorfor en forbindelse relation kan bryde. Kapacitetsbebegrænsninger, hvor kun nogle af tjenesten objekter i relationen forbindelse kan for eksempel være på en given node. I disse tilfælde selvom der findes en forbindelse relation på plads, kan den ikke gennemtvinges på grund af de andre betingelser. Hvis det er muligt at gennemtvinge alle andre begrænsninger og forbindelse på et senere tidspunkt rettes overtrædelse af forbindelsen mellem processorer begrænsningen automatisk.  

### <a name="chains-vs-stars"></a>Kæder kontra stjerner
I dag vi ikke kan model kæder af forbindelsen mellem processorer relationer. Hvad det betyder er det en tjeneste, som er en underordnet i én forbindelse relation kan ikke være en overordnet i forbindelse med en anden forbindelse relation. Hvis du vil bruge som model denne type relation, har du effektivt model som en stjerne i stedet for en kæde. For at kunne udføre, ville de nederste underordnede være overordnet "midterste" barnets overordnede i stedet. Dette kan kræve oprettelse af en "pladsholder" tjeneste skal fungere som overordnet indholdstype for flere underordnede websteder, afhængigt af arrangementet for dine tjenester.

![Kæder kontra stjerner i konteksten af forbindelsen mellem processorer relationer][Image2]

Bemærk om forbindelse relationer i dag er, at de er retningsbestemte. Det betyder, at reglen "forbindelse" kun gennemtvinger, er underordnede, hvor overordnet er. Hvis for eksempel overordnet pludselig skifter til en anden node derefter tror ressourcestyring klynge ikke faktisk, at der er noget galt, indtil den meddelelser, underordnede ikke er placeret til et overordnet relationen gennemtvinges ikke med det samme.

### <a name="partitioning-support"></a>Partitionering support
Den endelige ting at besked om forbindelse er den forbindelse, der ikke understøttes relationer, hvor overordnet er opdelt. Dette er noget, som vi understøtter muligvis til sidst, men i dag det er ikke tilladt.

## <a name="next-steps"></a>Næste trin
- For flere oplysninger om de andre indstillinger, der er tilgængeligt til konfiguration af tjenester udtjekning emnet på de andre klynge ressourcestyring konfigurationer tilgængelige [Få mere at vide om at konfigurere Services](service-fabric-cluster-resource-manager-configure-services.md)
- Mange grunde, hvor folk bruger forbindelse, såsom at begrænse tjenester til et lille sæt computere og forsøger at sammenlægge indlæsning af en samling af tjenesterne, understøttes bedre via programmet grupper. Se [Programmet grupper](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png
