<properties
   pageTitle="Forskelle mellem Skytjenester og Service-strukturen | Microsoft Azure"
   description="En oversigt for at overføre programmer fra Cloud Services til tjenesten struktur."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Få mere at vide om forskellene mellem Skytjenester og Service-strukturen inden overførsel af programmer.
Microsoft Azure Service-strukturen er den næste-generering af sky programmet platform til meget SVG, særdeles pålidelig distribuerede programmer. Det introducerer mange nye funktioner til emballagen, implementere, opgradering og administration af distribuerede skyen programmer. 

Dette er en indledende guide til at overføre programmer fra Cloud Services til tjenesten struktur. Det fokuserer primært på arkitektonisk og designforskelle mellem Skytjenester og Service-strukturen.
 
## <a name="applications-and-infrastructure"></a>Programmer og infrastruktur

Et grundlæggende forskellen mellem Skytjenester og Service-strukturen er forholdet mellem FOS, arbejdsmængder og programmer. En arbejdsbyrde her er defineret som den kode, du skriver til at udføre en bestemt opgave eller angive en tjeneste.
 
 - **Cloud Services er om installation af programmer som FOS.** Den kode, du skriver er tæt forbundet til en forekomst af VM, som en webside eller arbejder rolle. Hvis du vil installere en arbejdsbyrde i Cloud Services, er at installere en eller flere VM forekomster, der kører arbejdsbelastningen. Der er ingen adskillelse af programmer og FOS, og så der findes ingen formelle definition af et program. Et program kan betragtes som et sæt af internettet eller arbejder rolle forekomster i en Cloud Services installation eller en hel Cloud Services-installation. I dette eksempel vises et program som et sæt af rolle forekomster.
 
![Cloud Services-programmer og topologi][1]

 - **Tjenesten struktur er om installation af programmer til eksisterende FOS eller maskiner, der kører tjenesten strukturen på Windows eller Linux.** De tjenester, du skriver er helt Afkoblet fra den underliggende struktur, som repræsenteres ikke til stede af tjenesten strukturen programmet platformen, så programmet kan installeres på flere miljøer. Belastning i tjenesten strukturen kaldes en "tjeneste", og en eller flere tjenester grupperes i et formelt defineret til computeren, der kører på en tjeneste-strukturen programmet platform. Flere programmer kan installeres på en enkelt Service-strukturen klynge.
 
![Tjenesten strukturen programmer og topologi][2]
 
Tjenesten struktur selve er en platform programlag, der kører på Windows eller Linux, mens Cloud Services er et system til implementering af Azure-administreret FOS med arbejdsbelastninger, som vedhæftet.
Tjenesten strukturen programmet modellen har en lang række fordele:

 - Hurtig installation gange. Oprette VM forekomster kan være tidskrævende. I tjenesten strukturen installeres FOS kun, når for at danne en klynge, der er vært Service-strukturen programmet platform. Fra tidspunkt, kan programpakker installeres på klyngen meget hurtigt.
 - Kompakte vært. I Cloud Services vært en kollega rolle VM én arbejdsbyrde. Programmer er adskilt fra de VM'er, der kører dem, hvilket betyder, at du kan installere et stort antal programmer til et lille antal VM'er, der kan reducere de samlede omkostninger for større installationer i tjenesten struktur.
 - Den Service-strukturen platform kan køre et vilkårligt sted, der har Windows Server eller Linux-computere, uanset om det er Azure eller en lokal. Platformen giver et fremstilling lag over den underliggende struktur, så programmet kan køre på forskellige miljøer. 
 - Distribueret programadministration. Tjenesten struktur er en platform, ikke kun hosts distribueret programmer, men også hjælper med at administrere deres livscyklus uafhængigt af hosting VM eller maskine livscyklus.

## <a name="application-architecture"></a>Programmet arkitektur

Arkitekturen i et Cloud Services-program omfatter normalt flere ekstern tjeneste afhængigheder, som Service Bus, Azure-tabel og Blob-lager, SQL, Redis og andre til at administrere tilstand og data fra et program og kommunikation mellem internettet og arbejder roller i et Cloud Services-installation. Et eksempel på en komplet Cloud Services-program kan se sådan ud:  

![Cloud Services arkitektur][9]

Tjenesten strukturen programmer kan også vælge at bruge de samme eksterne services i et færdigt til computeren. I dette eksempel Cloud Services arkitektur skal er stien overførsel nemmeste fra Cloud Services til tjenesten strukturen at erstatte kun Cloud Services-installationen med et Service-strukturen-program, holde styr på den overordnede arkitektur på samme måde. Internettet og arbejder roller kan blive overført til tjenesten strukturerede uden status services med minimale kodeændringer.

![Tjenesten arkitektur efter simple overførslen][10]

På dette tidspunkt bør systemet fortsat fungerer på samme måde som før. Udnytte Service-strukturen med høj sikkerhed funktioner, eksterne tilstand butikker kan være internalized, som med høj sikkerhed services, hvis det er relevant. Dette er mere avanceret end en simpel overførsel af internettet og arbejder roller til tjenesten strukturerede uden status services, som den, skal du skrive brugerdefinerede tjenester, der indeholder tilsvarende funktionalitet i dit program, som eksterne tjenesterne gjorde tidligere. Fordelene ved at gøre dette, omfatter: 

 - Fjerne eksterne afhængigheder 
 - Gøre ensartede installation, administration og opgradering modeller. 
 
Et eksempel resulterende arkitekturen i internalizing disse tjenester kan se ud som dette:

![Tjenesten arkitektur efter fuld overførsel][11]

## <a name="communication-and-workflow"></a>Kommunikation og arbejdsprocesser

De fleste skyen tjenesteprogrammer består af mere end ét niveau. På samme måde, en tjeneste-strukturen programmet består af mere end én tjeneste (typisk mange services). To almindelige kommunikation modeller er direkte kommunikation og via en ekstern robust lagerplads.

### <a name="direct-communication"></a>Direkte kommunikation

Med direkte kommunikation kommunikere niveauer direkte via slutpunkt, der vises af hvert niveau. I uden status miljøer som Skytjenester, denne betyder, at vælge en forekomst af en VM rolle, enten tilfældigt eller round robin-til balance belastning, og oprette forbindelse til dens slutpunkt direkte.

![Cloud Services direkte kommunikation][5]

 Direkte kommunikation er en fælles kommunikation model i tjenesten struktur. Vigtige forskellen mellem Service-strukturen og Cloud Services er pågældende i Skytjenester, du opretter forbindelse til en VM, mens i tjenesten struktur, du opretter forbindelse til en tjeneste. Dette er et vigtigt skelnes nogle årsager:

 - Tjenester i tjenesten strukturen ikke er bundet til VM'er, der hoster dem. tjenester kan flytte rundt i klyngen og faktisk forventes at navigere til forskellige årsager: ressource justering, failover, program og infrastruktur opgraderinger og placeringen eller indlæse begrænsninger. Det betyder, at du kan ændre en tjenesteforekomst adresse når som helst. 
 - En VM i tjenesten strukturen kan hoste tjenesterne, hver med entydige slutpunkter.

Service-strukturen indeholder en tjeneste registrering ordning, kaldet tjenesten navngive, som kan bruges til at oversætte slutpunkt adresserne på tjenester. 

![Tjenesten struktur direkte kommunikation][6]

### <a name="queues"></a>Køer

En fælles ordning af kommunikation mellem niveauer i uden status miljøer som Cloud Services er at bruge en ekstern storage kø solidt gemme arbejdsopgaver fra ét niveau til en anden. Et almindeligt scenarie er et web trin, der sender job til en Azure eller Service Bus hvor arbejder rolle forekomster kan annullering i kø og behandle job.

![Cloud Services kø kommunikation][7]

Samme kommunikation model kan bruges i tjenesten struktur. Det kan være nyttigt, når du overfører et eksisterende Cloud Services-program til tjenesten struktur. 

![Tjenesten struktur direkte kommunikation][8]
 
## <a name="next-steps"></a>Næste trin

Stien overførsel nemmeste fra Cloud Services til tjenesten strukturen er at erstatte Cloud Services installationen med et Service-strukturen-program, holde styr på den overordnede arkitektur i dit program stort set det samme. I følgende artikel indeholder en vejledning for at konvertere et websted eller arbejder rolle til en tjeneste-strukturen uden status tjeneste.

 - [Enkel overførsel: konvertere et websted eller arbejder rolle til en tjeneste-strukturen uden status tjeneste](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
