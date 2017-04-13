<properties
   pageTitle="Pålidelig service arkitektur | Microsoft Azure"
   description="Oversigt over pålidelig Service arkitekturen for med høj sikkerhed og uden status"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/30/2016"
   ms.author="alanwar"/>

# <a name="architecture-for-stateful-and-stateless-reliable-services"></a>Arkitektur for pålidelig med høj sikkerhed og uden status

En Azure Service-strukturen pålidelig Service kan være med høj sikkerhed eller uden tilstand. Hver type tjeneste kører inden for en bestemt arkitektur. Disse arkitekturer er beskrevet i denne artikel.
Se [Oversigt over pålidelige Service](service-fabric-reliable-services-introduction.md) kan finde flere oplysninger om forskellene mellem med høj sikkerhed og uden tilstand.

## <a name="stateful-reliable-services"></a>Med høj sikkerhed pålidelig tjenester

### <a name="architecture-of-a-stateful-service"></a>Arkitekturen i en med høj sikkerhed tjeneste
![Arkitektur diagram over en med høj sikkerhed tjeneste](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### <a name="stateful-reliable-service"></a>Med høj sikkerhed pålidelig Service

En med høj sikkerhed pålidelig Service kan være afledt af enten StatefulService eller StatefulServiceBase klasse. Begge af disse grundlæggende klasser, som leveres af tjenesten struktur. De tilbyder forskellige niveauer af support og fremstilling for tjenesten med høj sikkerhed arbejde sammen med tjenesten strukturen – og til at deltage som en tjeneste i tjenesten strukturen klynge.

StatefulService stammer fra StatefulServiceBase. StatefulServiceBase tilbyder større fleksibilitet, men det kræver flere forståelse af interne af tjenesten struktur.
Se [Oversigt over pålidelige Service](service-fabric-reliable-services-introduction.md) og [pålidelig Service avancerede brugen](service-fabric-reliable-services-advanced-usage.md) yderligere oplysninger om de specifikke oplysninger ved at skrive services ved hjælp af StatefulService og StatefulServiceBase klasser.

Begge base klasser Administrer levetid og rolle service implementeringen. Tjenesten implementeringen kan tilsidesætte virtuelle metoder til enten grundlæggende klasse, hvis tjenesten implementeringen har arbejde at udføre på disse tidspunkter i tjenesten implementering livscyklus – eller hvis den ønsker at oprette et kommunikation lytteren objekt. Bemærk, selvom en tjeneste implementering kan implementere sin egen kommunikation lytteren objekt, udsætte ICommunicationListener, i diagrammet ovenfor, implementeres lytteren kommunikation af tjenesten strukturen – som service implementeringen bruger en kommunikation lytter, der er implementeret af tjenesten struktur.

En med høj sikkerhed pålidelig Service bruges stabil tilstand manager for at kunne udnytte pålidelig af websteder. Pålidelige samlinger lokale datastrukturer, som er meget tilgængelige for tjenesten –, der er, de er altid tilgængelig, uanset service failover. De forskellige typer af pålidelige websteder er implementeret af en pålidelig tilstand udbyder.
Du kan finde flere oplysninger om pålidelig samlinger, [Oversigt over pålidelige websteder](service-fabric-reliable-services-reliable-collections.md).

### <a name="reliable-state-manager-and-state-providers"></a>Stabil tilstand overordnet og tilstand udbydere

Stabil tilstand manager er det objekt, der administrerer stabil tilstand udbydere. Der er funktioner for at oprette, slette, Optæl og sikre, at udbyderne stabil tilstand er permanente og meget tilgængelige. En pålidelig tilstand udbyder forekomst repræsenterer en forekomst af en permanente og fleksibelt datastruktur, som en ordbog eller en kø.

Hver stabil tilstand udbyder Fremviser en grænseflade, der bruges af en med høj sikkerhed tjeneste til at interagere med provideren stabil tilstand. For eksempel IReliableDictionary bruges til brugergrænsefladen med pålidelig ordbogen, mens IReliableQueue bruges til brugergrænsefladen med pålidelig køen. Alle stabil tilstand udbydere implementere grænsefladen IReliableState.

Stabil tilstand manager har en grænseflade med navnet IReliableStateManager, som giver adgang til den fra en med høj sikkerhed tjeneste. Grænseflader til stabil tilstand udbydere returneres gennem IReliableStateManager.

Stabil tilstand manager bruger en plug-in'en arkitektur, så nye typer af pålidelige websteder kan være tilsluttet dynamisk.

Pålidelige ordbog og pålidelig kø er bygget på implementeringen af en høj ydeevne, versionsnummer forskelle butik.

### <a name="transactional-replicator"></a>Transaktions af Microsoft

Komponenten transaktions af Microsoft er ansvarlig for at sikre, at status for en tjeneste (det vil sige, tilstanden i stabil tilstand manager og pålidelig af websteder) er ensartet på tværs af alle kopier, der kører tjenesten. Det sikrer også, at tilstanden er bevaret log. Stabil tilstand manager grænseflader med transaktions Microsoft via en privat ordning.

Transaktions Microsoft bruger en netværksprotokol til at kommunikere tilstanden med andre replikaer for forekomsten af tjenesten, så alle kopier har opdateret tilstandsoplysninger.

Transaktions Microsoft bruger en logfil til at bevare tilstandsoplysninger, så tilstandsoplysninger survives proces eller node går ned. Grænsefladen til loggen er via en privat ordning.

### <a name="log"></a>Log

Komponenten log giver en høj ydeevne fast butik, der kan optimeres for skrivning til drejer rundt eller Solid State diske.  Designet af logfilen er til fast opbevaring (det vil sige harddiske) skal være lokale for knuderne, der kører tjenesten med høj sikkerhed. Dette giver mulighed for lav latenstider og høj overførselshastighed sammenlignet med remote fast lagring, som ikke er lokale til noden.

Komponenten log bruger flere logfiler. Der er en node hele delte logfil, der alle kopier bruger som det kan give ventetid for laveste og højeste overførselshastighed til lagring af tilstand data. Som standard delte logfilen er placeret i mappen Service-strukturen node arbejde, men også konfigureret til at være placeret hos en anden placering, ideelt set på en disk reserveret til den delte log. Hver replika for tjenesten er også en dedikeret logfil og dedikeret logfilen er placeret i tjenestens arbejde systembiblioteket. Der findes ingen metode til at konfigurere dedikeret loggen skal anbringes i en anden placering.

Delte loggen er en overgangs område til replikaens tilstandsoplysninger, mens dedikeret logfilen er den endelige destination, hvor det er bevaret. I dette design statusoplysninger først skrives til delte logfilen og derefter lazily flyttes til dedikeret logfilen i baggrunden. På denne måde, ville Skriv til delte loggen have ventetid for laveste og højeste overførselshastighed, som giver mulighed for tjenesten komme videre hurtigere.

Læser og skriver til den delte log udføres via direkte EY til forudallokerede plads på disken for delt logfilen. Hvis du vil tillade optimal brug af diskplads på drevet med dedikeret logfiler, oprettes dedikeret logfilen som en NTFS sparsefil. Bemærk, at dette gør overprovisioning diskplads og Operativsystemet vises dedikeret logfilerne ved hjælp af meget mere plads på harddisken end hvad der faktisk bruges.

Ud over en minimale bruger-tilstand brugergrænseflade til loggen skrives loggen som en driver kerne-tilstand. Loggen kan angive den højeste ydeevne til alle tjenester, der bruger den ved at køre som en driver kerne-tilstand.

Du kan finde flere oplysninger om konfiguration af logfilen i [Konfiguration af med høj sikkerhed pålidelig Services](service-fabric-reliable-services-configuration.md).

## <a name="stateless-reliable-service"></a>Uden status pålidelig Service

### <a name="architecture-of-a-stateless-service"></a>Arkitekturen i en uden status tjeneste
![Arkitektur diagram over en uden status tjeneste](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### <a name="stateless-reliable-service"></a>Uden status pålidelig Service

Uden status service installationer afledt af klassen StatelessService eller StatelessServiceBase. Klassen StatelessServiceBase giver en større fleksibilitet end klassen StatelessService.
Begge base klasser Administrer levetid og rolle til en tjeneste.

Tjenesten implementeringen kan tilsidesætte virtuelle metoder til enten grundlæggende klasse, hvis tjenesten har arbejde at udføre på disse punkter i tjenesten livscyklus – eller hvis den ønsker at oprette et kommunikation lytteren objekt. Bemærk, selvom tjenesten kan implementere sin egen kommunikation lytteren objekt, udsætte ICommunicationListener, i diagrammet ovenfor, implementeres lytteren kommunikation af tjenesten struktur, som implementering af, der bruger en kommunikation lytter, der er implementeret af tjenesten struktur.

Se [Oversigt over pålidelige Service](service-fabric-reliable-services-introduction.md) og [pålidelig Service avancerede brugen](service-fabric-reliable-services-advanced-usage.md) yderligere oplysninger om de specifikke oplysninger ved at skrive-tjenester med StatelessService og StatelessServiceBase klasser.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om tjenesten strukturen, i:

[Oversigt over pålidelige service](service-fabric-reliable-services-introduction.md)

[Hurtig start](service-fabric-reliable-services-quick-start.md)

[Oversigt over pålidelige websteder](service-fabric-reliable-services-reliable-collections.md)

[Pålidelig service avancerede brugen](service-fabric-reliable-services-advanced-usage.md)

[Pålidelige tjenestekonfiguration](service-fabric-reliable-services-configuration.md)  
