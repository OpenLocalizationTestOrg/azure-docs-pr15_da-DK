<properties
   pageTitle="Tjenesten arkitektur | Microsoft Azure"
   description="Tjenesten struktur er en distribuerede systemer platform, der bruges til at opbygge SVG, pålidelig og nemt administrerede programmer til skyen. I denne artikel viser arkitekturen i tjenesten struktur."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Tjenesten arkitektur

Tjenesten strukturen er oprettet med lagdelte delsystemer. Disse delsystemer gør det muligt at skrive programmer, der er:

* Høj tilgængelighed
* SVG
* Administrere
* Testable

I det følgende diagram viser de vigtigste delsystemer af Service-strukturen.

![Diagram over Service arkitektur](media/service-fabric-architecture/service-fabric-architecture.png)

I et distribueret system er muligheden for at kommunikere sikkert mellem noderne i en klynge afgørende. Er den transport undersystem, som giver sikker kommunikation mellem noder i bunden af stablen. Over transport ligger undersystem undersystem sammenslutning, som klynger forskellige knuderne i et enkelt objekt (kaldet klynger), så Service-strukturen kan registrere fejl, udføre fyldtegn valg og muliggøre ensartet routing. Pålidelighed undersystem, lagt oven på undersystem sammenslutning er ansvarlig for pålideligheden af tjenesten strukturerede services gennem metode som gentagelse, ressourcestyring og failover. Sammenslutning undersystem også ligger til grund for vært og aktivering-undersystem, som administrerer livscyklus for et program på en enkelt node. Administration-undersystem administrerer livscyklus for programmer og tjenester. Mulighed for at teste undersystem hjælper programmet udviklere teste deres tjenesterne via simuleret fejl, før og efter installation af programmer og tjenester til fremstilling miljøer. Tjenesten strukturen giver mulighed for at løse service placeringer gennem dens kommunikation undersystem. De application programming modeller vises for udviklere er lagt oven på disse delsystemer sammen med program, der skal aktivere værktøj.

## <a name="transport-subsystem"></a>Transport-undersystem
Transport-undersystem implementerer et punkt til punkt datagramkanalen kommunikation. Denne kanal bruges til kommunikation i tjenesten strukturen klynger og kommunikationen mellem den service-strukturen klynge og klienter. Understøtter envejs og anmodning om autosvar kommunikationsmønstre, som ligger til grund for implementering af udsendelse og multicast i sammenslutning lag. Transport-undersystem sikrer kommunikation ved hjælp af X509 certifikater eller Windows-sikkerhed. Dette undersystem bruges internt af Service-strukturen og er ikke direkte adgang til udviklere til programmet programmering.

## <a name="federation-subsystem"></a>Sammenslutning undersystem
For at kunne årsag om et sæt af knuder i et distribueret system, skal du have en ensartet visning af systemet. Sammenslutning undersystem bruger de kommunikation primitiver leveres af transport-undersystem og kombinerer de forskellige noder til en enkelt samlet klynge, det kan årsag om. Den indeholder de distribuerede systemer primitiver bruges til de andre delsystemer - registrering af fejl, fyldtegn valg og ensartet distribution. Sammenslutning undersystem er bygget oven på fordelt hash tabeller med et 128-bit token mellemrum. Undersystemet opretter en ring-topologi over noderne med hver node på listen ring, der allokeres et undersæt af sikkerhedstoken afstanden for ejerskab. For registrering af fejl bruger laget en leasingskontrakter ordning baseret på hjerte slår og voldgift. Sammenslutning undersystem garanterer også gennem besværlige deltage i og afgang protokoller, som kun en enkelt ejer af et token findes på en hvilken som helst tidspunkt. Dette giver fyldtegn valg og ensartet routing garantier.

## <a name="reliability-subsystem"></a>Pålidelighed undersystem
Pålidelighed undersystem giver mulighed for at gøre en tjeneste-strukturen tjenestens tilstand meget tilgængeligt ved hjælp _af Microsoft_, _Failover Manager_og _Ressource belastningsjusteringstjenesten_.

* I Microsoft sikrer, at tilstandsændringer i den primære tjeneste replika automatisk replikeres til sekundær replikaer bevarer konsistens mellem de primære og sekundære replikaer i replikasættet en tjeneste. Replikatoren er ansvarlig for administration af quorum mellem replikaer i replikasættet. Det skal arbejde sammen med failover enheden til at hente en liste over handlinger til at kopiere, og konfigureres igen agent leverer den med konfigurationen af replikasættet. Konfigurationen angiver, hvilke replikaer handlingerne skal replikeres. Tjenesten strukturen indeholder en kaldet strukturen af Microsoft, som kan bruges ved programmering model API til at foretage-tjenestetilstand yderst tilgængelig og pålidelig standard af Microsoft.
* Failover Manager sikrer, at når noder er tilføjet eller fjernet fra klyngen, afkrydsningsfeltet Indlæs automatisk fordeles på tværs af de tilgængelige noder. Hvis en node i klyngen mislykkes, omkonfigurere klyngen automatisk service replikaer for at bevare tilgængelighed.
* Ressourcestyring placerer tjenesten replikaer på tværs af fejl ved domæne i klyngen og sikrer, at alle failover enheder er funktionsdygtige. Ressourcestyring saldi også serviceressourcer på tværs af underliggende delte puljen af klyngenoder til at opnå optimal ensartet belastning fordeling.

## <a name="management-subsystem"></a>Undersystem til styring af
Administration-undersystem yder til slut service og programadministration livscyklus. PowerShell-cmdletter og administrative API'er gør det muligt at klargøre, installere, programrettelse, opgradere og deaktivere Klargør programmer uden tab af tilgængelighed. Administration-undersystem udfører dette gennem følgende tjenester.

* **Klynge Manager**: Dette er den primære tjeneste, der interagerer med Failover Manager fra pålidelighed at placere programmerne på noderne baseret på tjenesten placeringen begrænsninger. Ressourcestyring i failover undersystem sikrer, at betingelserne, der aldrig er brudt. Klynge manager håndterer livscyklus af programmerne fra bestemmelse til at deaktivere klargøre. Den integrerer med sundhed manager til at sikre, at programmet tilgængelighed ikke går tabt fra en semantisk sundhed perspektiv under opgraderinger.
* **Sundhed Manager**: denne tjeneste aktiverer sundhedsovervågning af programmer, tjenester og klynge enheder. Klynge enheder (såsom noder, service partitioner og replikaer) kan rapportere sundhed oplysninger, som sammenlægges klik derefter på butikken, centralt tilstand. I denne tilstand oplysninger indeholder en overordnede punkt i tid sundhed snapshot af tjenesterne og noder fordeles over flere noder i klynge, så du kan foretage eventuelle nødvendige korrigerende handlinger. Sundhed forespørgsel API'er gør det muligt at forespørge sundhed begivenheder rapporteret, at sundheds-undersystem. Forespørgslen sundhed API'er returnere rå tilstand data gemt i butikken tilstand eller aggregeret, fortolkes sundhed data for en bestemt klynge enhed.
* **Billede af Store**: denne tjeneste indeholder opbevaring og distribution af de binære filer programmet på computeren. Denne tjeneste indeholder en enkelt fordelt fillager hvor programmerne, der er overført til og hentes fra.


## <a name="hosting-subsystem"></a>Vært undersystem
Klynge manager oplyser det hosting undersystem (kører på hver node), hvilke tjenester der skal administrere for en bestemt node. Hosting undersystem administrerer derefter livscyklus af programmet på noden. Det skal arbejde sammen med komponenterne pålidelighed og tilstand for at sikre, at replikaerne placeres korrekt og er sund.

## <a name="communication-subsystem"></a>Kommunikation undersystem
Dette undersystem indeholder pålidelig messaging i klynge og tjenesten registrering i Naming-tjenesten. Tjenesten Naming løser tjenestenavne til en placering i klyngen og brugere mulighed for at administrere tjenestenavne og egenskaber. Ved hjælp af tjenesten Naming kan klienter sikkert kommunikere med en hvilken som helst node i klynge til at oversætte et tjenestenavn og hente service metadata. Med en enkel Naming klient API kan udvikle brugere af tjenesten strukturen tjenester og -klienter i stand til at løse den aktuelle netværksplacering på trods af node dynamik eller igen tilpasning af klyngen.

## <a name="testability-subsystem"></a>Mulighed for at teste undersystem
Mulighed for at teste er en række værktøjer, der er udviklet specielt til test tjenester er bygget på tjenesten struktur. Værktøjerne lade en udvikler nemt forårsage sigende fejl og køre test scenarier for at øve og validere flere tilstande og overgange, vil opleve en tjeneste i hele dets levetid, alle på en kontrolleret og sikker måde. Mulighed for at teste indeholder også en funktion for at køre længere test, der kan navigere gennem forskellige mulige fejl uden at miste tilgængelighed. Dette giver dig test i produktionsmiljø.
