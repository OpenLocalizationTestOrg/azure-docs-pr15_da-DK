<properties
   pageTitle="Oversigt over Service-strukturen | Microsoft Azure"
   description="En oversigt over Service-strukturen, hvor programmer består af mange microservices til at levere skala og fleksibilitet. Tjenesten struktur er en distribuerede systemer platform bruges til at opbygge SVG, pålidelig og nemt at administrere programmer til skyen"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="mfussell"/>

# <a name="overview-of-service-fabric"></a>Oversigt over Service-strukturen
Tjenesten struktur er en distribuerede systemer platform, som gør det nemt at pakke, installere og administrere SVG og pålidelige microservices. Tjenesten strukturen adresser også betydeligt udfordringerne ved at udvikle og administrere skyen programmer. Udviklere og administratorer kan undlade at løse problemer med komplekse infrastruktur og fokus i stedet om implementering af vigtige, krævende arbejdsmængder viden om, at de er SVG, stabil og nemmere. Tjenesten strukturen repræsenterer den næste-generering af programmer platform til oprettelse og administration af disse professionel, niveau 1 skyen skalering programmer.

Denne [korte video](https://aka.ms/servicefabricvideo) giver en introduktion til Service-strukturen og microservices.


## <a name="applications-composed-of-microservices"></a>Programmer bestående af microservices
Tjenesten strukturen gør det muligt at oprette og administrere SVG og pålidelige programmer bestående af microservices, der kører med meget høj tæthed på en delt samling af computere (kaldes en klynge). Den indeholder en avanceret runtime opbygning af fordelt, SVG uden status og med høj sikkerhed microservices. Den indeholder også omfattende programmet administrationsfunktioner til klargøring, implementering, overvågning, opgradering/rettelse, og slette installeres programmer.

Hvorfor er en microservices tilgang vigtige? De to primære årsager er:

1. De gør det muligt at skalere forskellige dele af dit program afhængigt af dets behov.

2. Udviklingsteams kan være mere fleksible i udlægning af ændringer og dermed leverer funktioner til dine kunder hurtigere og mere ofte.

Tjenesten strukturen styrer mange Microsoft-tjenester i dag, herunder Azure SQL-Database, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure begivenhed hubber, Azure IoT, Skype for Business og mange core Azure tjenester.

Tjenesten strukturen er tilpasset til oprettelse af skyen indbyggede tjenester, der kan starte fra bunden, efter behov, og vokser til massive Skaler med hundredvis eller tusindvis af computere.

Dags Internet skalering services er udviklet af microservices. Eksempler på microservices omfatter protocol gateways, brugerprofiler, indkøb vogne, lager behandling, køer og gemmer. Tjenesten struktur er en microservices platform, som giver alle microservice et entydigt navn, der kan være enten uden tilstand eller med høj sikkerhed.

Tjenesten strukturen indeholder omfattende runtime og livscyklus administrationsfunktioner til bestående af disse microservices-programmer. Det er vært for microservices i beholdere installeret og aktiveret på tværs af tjenesten strukturen klynge. Flytte fra FOS til beholdere gør det muligt øget rækkefølge af omfanget i tætheden af. På samme måde, en anden omfang i tætheden bliver så muligt ved at flytte fra beholdere til microservices. For eksempel omfatter en enkelt Azure SQL-Database klynge hundredvis af maskiner, der kører tusindvis af objektbeholdere vært alt hundreder af tusindvis af databaser. Hver database er en tjeneste-strukturen med høj sikkerhed microservice. Den samme er sandt om de andre tjenester, som tidligere nævnt, hvilket er årsagen ordet "store" bruges til at beskrive Service-strukturen funktioner. Hvis beholdere giver dig høj tæthed, derefter giver microservices dig store.

Få mere at vide om microservices tilgangen, [Hvorfor en microservices metode til at lave programmer?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Objektbeholder installation og organisering
Tjenesten struktur er en [orchestrator](service-fabric-cluster-resource-manager-introduction.md) af microservices på tværs af en klynge af computere. Microservices kan udvikles på mange måder i at bruge [tjenesten strukturen programming modeller](service-fabric-choose-framework.md) til installation af [Gæst eksekverbare filer](service-fabric-deploy-existing-app.md). Tjenesten strukturen kan installere tjenester i beholderen billeder, og det vigtigste er kan du blande både tjenester i processer og tjenester i beholdere sammen i det samme program. Hvis du blot ønsker at [installere og administrere objektbeholder billeder](service-fabric-containers-overview.md) på tværs af en klynge af computere, er Service-struktur perfekte valg til dette.


## <a name="create-service-fabric-clusters-anywhere"></a>Oprette Service-strukturen klynger overalt
Du kan oprette Service-strukturen klynger i mange miljøer, herunder Azure eller lokalt, på Windows Server eller på Linux. Desuden er udviklingsmiljø i SDK identisk med produktionsmiljø med ingen involveret emulatorer. Det vil sige, hvis den kører på din lokale udvikling klynge er det installerer til den samme klynge i andre miljøer.

Du kan finde flere oplysninger om oprettelse af klynger lokalt, læse, [oprette en klynge på Windows Server eller Linux](service-fabric-deploy-anywhere.md) eller til Azure oprettelse af en klynge [via Azure-portalen](service-fabric-cluster-creation-via-portal.md).

![Tjenesten strukturen platform][Image1]

## <a name="stateless-and-stateful-service-fabric-microservices"></a>Uden status og med høj sikkerhed Service-strukturen microservices

Tjenesten strukturen gør det muligt at bygge programmer bestående af microservices. Uden status microservices (protocol gateways, web proxyer osv.) ikke længere er tilstanden af uden for en given og dens svar fra tjenesten. Azure Cloud Services arbejder roller er et eksempel på en uden status tjeneste. Med høj sikkerhed microservices (brugerkonti, databaser, enheder, indkøb vogne, køer, osv.) vedligeholde tilstanden af, autoritative ud over den og dens svar. Dags Internet skalering programmer består af en kombination af uden status og med høj sikkerhed microservices.

Hvorfor har med høj sikkerhed microservices sammen med uden status dem? De to primære årsager er:

1. Muligheden for at opbygge høj overførselshastighed, lav ventetid, manglende tolerant behandling (OLTP) tjenester ved at holde kode og data tæt på den samme maskine af transaktioner. Nogle eksempler er interaktive præsentationer, søgning, Internet af ting (IoT) systemer, handel systemer, kreditkort behandling og svindel registrering systemer og personlige Poststyring.

2. Programmet design forenkling. Med høj sikkerhed microservices fjerne brug for yderligere køer og gemmer, kræves for traditionelt at håndtere et rent uden status program tilgængelighed og ventetid krav. Med høj sikkerhed services er naturligt høj tilgængelighed og lav ventetid, hvorved antallet af bevægelige dele til at administrere i dit program som helhed.

Yderligere oplysninger om programmet mønstre med Service-strukturen, læst [programmet scenarier](service-fabric-application-scenarios.md) og [vælge en programming model ramme](service-fabric-choose-framework.md) til din tjeneste

## <a name="application-lifecycle-management"></a>Programadministration livscyklus
Tjenesten strukturen indeholder førsteklasses support til den fulde livscyklus programadministration (ALM) i skyen programmer – fra udvikling ved implementering, daglig administration og vedligeholdelse til eventuel udfase.

Funktionerne Service-strukturen ALM aktivere administratorer / IT operatorer til brug af simple, lav touch arbejdsprocesser at blive klargjort, installere, programrettelse, og overvåge programmer. Disse indbyggede arbejdsprocesser reducere belastning på IT operatorer til at holde programmerne konstant er tilgængelig.

De fleste programmer består af en kombination af uden status og med høj sikkerhed microservices og andre eksekverbare filer/eksekverbar kode, der installeres sammen. Har stærke typer på programmer og pakket microservices, aktiverer Service-strukturen installationen af flere forekomster af tjenesteprogrammer. Hver forekomst administreres og opgraderet uafhængigt af hinanden. Tjenesten struktur er det vigtigste er, kan du installere *en hvilken som helst* eksekverbare filer eller runtime og gøre dem pålidelig. For eksempel installerer tjenesten strukturen ASP.NET Core 1, Node.js, Java FOS, scripts eller andet, der udgør dit program.

Læse [programmet supportlivscyklus](service-fabric-application-lifecycle.md) kan finde flere oplysninger om supportlivscyklus programadministration og på installation af enhver kode, se [Implementer gæst eksekverbare](service-fabric-deploy-existing-app.md)

## <a name="key-capabilities"></a>Vigtige funktioner
Ved hjælp af tjenesten strukturen, kan du:

- Udvikle stort omfang SVG programmer, der er selvreparerende.

- Udvikle programmer bestående af microservices ved hjælp af tjenesten strukturen programming modellen. Eller blot hoste gæst eksekverbare filer og andre programmer strukturer efter eget valg, som ASP.NET Core 1 eller Node.js.

- Udvikle særdeles pålidelig uden status og med høj sikkerhed microservices.

- Installere og dirigerer beholdere omfatter Windows beholdere og Docker beholdere på tværs af en klynge. Disse beholdere kan objektbeholder gæst eksekverbare filer eller pålidelige uden status og med høj sikkerhed microservices.  I begge tilfælde får du beholder port host porttilknytning, objektbeholder synlighed og automatiseret failover.

- Forenkle designet af dit program ved hjælp af med høj sikkerhed microservices i stedet for cachelagre og køer.

- Installer Azure eller lokale skyer, der kører Windows Server eller Linux med nul kodeændringer. Skrive én gang, og derefter installere et vilkårligt sted på en tjeneste-strukturen klynge.

- Udvikle med en "datacenter på din maskine" fremgangsmåde. Den lokale udviklingsmiljø er den samme kode, der kører i Azure datacentre.

- Implementere programmer i sekunder.

- Implementere programmer med højere tæthed end virtuelle maskiner, implementere hundredvis eller tusindvis af programmer per computer.

- Installere forskellige versioner af samme programmet side om side, kan opgraderes hver uafhængigt af hinanden.

- Administrere dine med høj sikkerhed programmer uden en hvilken som helst nedetid, herunder udslåningen og hård opgraderinger livscyklus.

- Administrere ved hjælp af .NET API'er, Java (Linux), PowerShell, Azure CLI (Linux) eller RESTEN grænseflader-programmer.

- Opgradering og programrettelse microservices i programmerne, uafhængigt af hinanden.

- Overvåge og diagnosticere tilstanden for dine programmer og indstille politikker for at udføre automatisk reparationer.

- Mulighed for at skala ud eller skala i antallet af knuder i en klynge samt skalere op eller skala ned størrelsen af hver node, viden om, at dine programmer automatisk skalere og er distribueret ifølge de tilgængelige ressourcer.

- Se den selvreparerende ressource af belastning dirigerer videredistribution af programmer på tværs af klyngen. Tjenesten genopretter fra fejl og optimerer fordelingen af baseret på de tilgængelige ressourcer.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Næste trin

* Du kan finde flere oplysninger:
    * [Hvorfor løse en microservices til at lave programmer?](service-fabric-overview-microservices.md)
    * [Oversigt over terminologi](service-fabric-technical-overview.md)
* Konfiguration af dit Service-strukturen [udviklingsmiljø](service-fabric-get-started.md)  
* [Vælge en programming model ramme](service-fabric-choose-framework.md) til din tjeneste

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
