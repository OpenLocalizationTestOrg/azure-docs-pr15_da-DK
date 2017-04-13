<properties
   pageTitle="Introduktion til tjenesten strukturen klynge ressourcestyring | Microsoft Azure"
   description="En introduktion til tjenesten strukturen klynge ressourcestyring."
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

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introduktion til tjenesten strukturen klynge ressourcestyring
Traditionelt administration af IT-systemer eller et sæt af tjenester mente få nogle fysiske eller virtuelle maskiner dedikeret til de specifikke tjenester eller systemer. Mange vigtige tjenester er blevet opdelt i en "web" niveau og en "-data" eller "lagerplads" niveau måske med nogle andre specialiserede komponenter som en cache. Andre typer programmer ville have et SMS trin, hvor anmodninger løber ind og ud forbindelse til et arbejde trin til analyse og transformation nødvendige som en del af kommunikation. Hver type af arbejdsbyrde har en bestemt maskiner dedikeret til det: databasen har nogle maskiner dedikeret, webserverne en få. Hvis en bestemt type arbejdsbelastningen forårsaget maskiner den var på til at køre for varme og derefter tilføjet flere computere med slags arbejdsbelastningen konfigureret til at køre på den og erstattet nogle af maskiner med større computere. Let. Hvis en maskine mislykkedes, løb del af det overordnede program på nederste kapacitet, indtil maskinen kan gendannes. Stadig ret nemt (hvis det er ikke nødvendigvis sjov).

Nu, men har Lad os sig du fundet et behov for at skalere ud og har taget beholdere og/eller microservice springet. Pludselig finde dig selv med mange, hundredvis eller tusindvis af maskiner, massevis af forskellige typer tjenester, måske hundredvis af forskellige forekomster af disse tjenester, hver med en eller flere forekomster eller replikaer for høj tilgængelighed (HA).

Pludselig administration af dit miljø er ikke så enkelt som at administrere et par maskiner dedikeret til enkelt typer arbejdsmængder. Serverne er virtuelle og ikke længere har navne (du *har* skiftet mindsets fra [kæledyr til dyr](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) alligevel). Konfiguration er mindre om maskiner og flere oplysninger om tjenesterne sig selv. Dedikeret hardware er en ting hører fortiden og tjenester er blevet små fordelt systemer, der strækker sig over flere mindre stykker vare hardware.

Som følge af opdele din tidligere monolitisk, lagdelte app i separate services kører på vare hardware, har du nu mange flere kombinationer håndtere. Der bestemmer, hvilke typer arbejdsmængder kan køre på hvilken hardware, eller hvor mange? Hvilke arbejdsbelastninger, som fungerer godt på den samme hardware, og som er i konflikt? Når en maskine går ned... Hvad kørte selv der? Hvem er ansvarlig for at sikre, at arbejdsbelastningen begynder at køre igen? Vent til den (virtuelle?) maskine kommer tilbage, eller din arbejdsbelastninger automatisk skifte over til andre computere og Behold kører? Er human brugerinput påkrævet? Hvad med opgraderinger i denne type miljø?

Vi har brug for hjælp til at administrere denne kompleksitet som udviklere og operatorer, der bor i denne type verden, og du får mening, en ansættelse binge og forsøger at papir over kompleksitet med personer ikke er det rigtige svar.

Sådan gør du?

## <a name="introducing-orchestrators"></a>Introduktion til orchestrators
En "Orchestrator" er den generelle term efter en del af software, der hjælper med at administrere følgende typer miljøer administratorer. Orchestrators er de komponenter, der tager med anmodninger, såsom "Jeg vil 5 kopier af denne tjeneste, der kører i min miljø", gør det SAND, og derefter (prøv) til at lade markeringen stå.

Orchestrators (ikke mennesker) er, hvad komme i gang, når en maskine mislykkes eller en arbejdsbyrde ophører for en uventede eller anden grund. De fleste Orchestrators mere end bare håndtere fejl, som hjælper med nye installationer, håndtering af opgraderinger og håndtere Ressourceforbrug, men alle er grundlæggende om vedligeholdelse af nogle beskedteksten tilstanden for konfiguration af i miljøet. Du vil kunne fortælle en Orchestrator, hvad du vil og har den gøre det hårde arbejde. Chronos eller Marathon oven på Mesos, flåden, sværme, Kubernetes og Service-strukturen er alle eksempler på Orchestrators (eller få dem indbygget i). Yderligere oplysninger der skal oprettes hele tiden som lede administrere virkelige installationer i forskellige typer miljøer og betingelser vokser og ændre.

## <a name="orchestration-as-a-service"></a>Organisering som en tjeneste
Kørslen af Orchestrator inden for en tjeneste-strukturen klynge håndteres primært ved klynge ressourcestyring. Tjenesten strukturen klynge ressourcestyring er et af systemets tjenester i Service-strukturen og startes automatisk i hver klynge.  Klynge ressource chefens job Generelt er opdelt i tre dele:

1. Gennemtvinge regler
2. Optimering af dit miljø
3. Hjælpe med at andre processer

### <a name="what-it-isnt"></a>Hvad det ikke er
I traditionelle N niveau web-apps er der altid nogle begrebet "Belastningsjustering", som regel kaldes en netværk Indlæs belastningsjusteringstjenesten (NLB) eller et program Indlæs belastningsjusteringstjenesten (ALB) afhængigt af hvor den SID i netværk stablen. Nogle Indlæs balancere er baseret som F5's BigIP tilbyder Hardware, andre baseret som Microsoft-software NLB. I andre miljøer kan du se noget i retning af HAProxy i denne rolle. I disse arkitekturer er kørslen af belastning at sørge for, at alle de forskellige uden status front-end-computere eller på forskellige computere i klyngen (omkring) modtager samme mængden af arbejde. Strategier for dette varieret fra at sende hver anden opkald til en anden server til session Fastgør/klæbrighed, til faktiske skøn og viderestilling af opkald fordeling baseret på den forventede omkostninger og aktuelle maskine belastning.

Bemærk, at det var i bedste metode til at sikre, kan web niveauet forblev omkring afstemmes. Strategier for justering af data niveau var helt forskellige og afhængige af den data lagerplads, som regel centrering omkring data sharding, cachelagring, database, der administreres visninger og lagrede procedurer osv.

Mens nogle af disse strategier er interessante, er Service strukturen klynge ressource Manager ikke noget, som belastningsjustering netværk eller en cache. Mens netværk belastning sikrer, at front enderne afstemmes ved at flytte trafik til hvor tjenesterne kører, Service strukturen klynge ressourcestyring tager en helt anden strategi – grundlæggende, Service-strukturen flytter *tjenester* til hvor de giver mest mening (og forventer trafik eller indlæse følge). Dette kan ske, for eksempel knuder, der er i øjeblikket koldtvandssystemer, fordi de tjenester, som er der laver ikke en masse arbejde lige nu, eller som er blevet slettet eller flyttet et andet sted. Som et andet eksempel flytte ressourcestyring klynge også en tjeneste væk fra en computer, som er ved at blive opgraderet, eller som er overbelastet på grund af en samling i forbrug ved de tjenester, som kører på den. Da ressourcestyring klynge er ansvarlig for at flytte services omkring (ikke levere netværkstrafik på hvor services allerede findes), indeholder en signifikant forskellige funktionssæt sammenlignet med hvad du vil finde i belastningsjustering netværk, og anvender grundlæggende forskellige strategier for at sikre, at Hardwareressourcerne i klyngen godt anvendes.

## <a name="next-steps"></a>Næste trin
- Læs [denne artikel](service-fabric-cluster-resource-manager-architecture.md) for oplysninger om arkitektur og oplysninger strømmen i klynge Ressourcestyring,
- Ressourcestyring klynge indeholder mange indstillinger til at beskrive klyngen. Hvis du vil vide Læs mere om dem denne artikel på [en beskrivelse af en tjeneste-strukturen klynge](service-fabric-cluster-resource-manager-cluster-description.md)
- For flere oplysninger om de andre indstillinger, der er tilgængeligt til konfiguration af tjenester udtjekning emnet på de andre klynge ressourcestyring konfigurationer tilgængelige [Få mere at vide om at konfigurere Services](service-fabric-cluster-resource-manager-configure-services.md)
- Målepunkter er, hvordan Service-strukturen klynge Resource Manager administrerer forbrug og kapacitet i klyngen. Hvis du vil vide Læs mere om dem og hvordan du konfigurerer dem [denne artikel](service-fabric-cluster-resource-manager-metrics.md)
- Ressourcestyring klynge fungerer med funktioner til administration af tjenesten struktur. Læs [denne artikel](service-fabric-cluster-resource-manager-management-integration.md) for at få mere at vide om, integration,
- For at vide om, hvordan ressourcestyring klynge administrerer og afbalancerer Indlæs i klyngen, skal du se artiklen på [justering af belastning](service-fabric-cluster-resource-manager-balancing.md)
