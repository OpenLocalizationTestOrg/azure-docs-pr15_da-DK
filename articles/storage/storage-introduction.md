<properties
    pageTitle="Introduktion til lager | Microsoft Azure"
    description="En oversigt over Azure-lager, Microsofts online datalagring i skyen. Lær, hvordan du bruger den bedste løsning for tilgængelige cloud storage i dine programmer."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="introduction-to-microsoft-azure-storage"></a>Introduktion til Microsoft Azure-lager

## <a name="overview"></a>Oversigt

Azure-lager er cloud storageløsning til moderne programmer, der er afhænger af holdbarhed, tilgængelighed og skalerbarhed til imødekommer behovet hos deres kunder. Ved at læse denne artikel, udviklere, IT-medarbejdere og virksomhedsbeslutning virksomhed kan få mere at vide om:

- Hvad er Azure-lager, og hvordan du kan drage fordel af den i din skyen, mobile, server og computerprogrammer
- Hvilke typer af data, du kan gemme med tjenesterne Azure-lager: blob (objekt) data, NoSQL tabeldata, kø meddelelser og filshares.
- Hvordan administreres adgang til dine data i Azure-lager
- Hvordan dataene Azure-lager er gjort robust via redundans og gentagelse
- Sådan kommer du ud til at oprette dit første Azure-lager program

Op at køre med Azure-lager hurtigt, kan du se [Introduktion til Azure-lager i fem minutter](storage-getting-started-guide.md).

Få at vide om værktøjer, biblioteker og andre ressourcer til at arbejde med Azure-lager, skal du se [Næste trin](#next-steps) nedenfor.

## <a name="what-is-azure-storage"></a>Hvad er Azure-lager?

Cloud computing gør det muligt for nye scenarier for programmer, der kræver SVG, pålidelig og meget tilgængelige lagerplads til deres data – som er præcis, hvorfor Microsoft udviklet Azure-lager. Ud over gør det muligt for udviklere til at oprette store programmer til at understøtte nye scenarier, indeholder Azure-lager også lagerplads grundlaget for virtuelle Azure-computere, en yderligere testament til dens stabilitet.

Azure-lager er stort omfang SVG, så du kan gemme og proces hundredvis af TB data, der understøtter stor data scenarier, der kræves af videnskabelig, finansielle analyse og medieprogrammer. Eller du kan gemme de lille mængde data, der kræves til en small business-websted. Det sted, hvor dine behov falde, betaler du kun for de data, du har gemt. Azure-lager i øjeblikket gemmer mange trillions entydige kunde objekter, og håndterer millioner af anmodninger om sekundet i gennemsnit.

Azure-lager er elastiske, så du kan designe programmer for et stort antal deltagere globale og skalere disse programmer efter behov - både med hensyn til mængden data, der er gemt og antallet af anmodninger mod den. Du betaler kun for du bruger, og kun, når du bruger den.

Azure-lager bruger et automatisk partitionering system, der automatisk indlæsning-saldi dataene baseret på trafik. Det betyder, at som kravene til din programmet vokse, Azure-lager tildeler automatisk de relevante ressourcer for at imødekomme dem.

Azure-lager er tilgængelig fra et vilkårligt sted i verden, fra en hvilken som helst type af programmet, om den kører i skyen, på skrivebordet, på en lokal server eller på en mobiltelefon eller tavle-pc-enhed. Du kan bruge Azure-lager i mobile scenarier, hvor programmet gemmer et undersæt af data på enheden og synkroniserer med et komplet sæt af data, der er gemt i skyen.

Azure-lager understøtter klienter, der bruger et bredt udvalg af operativsystemer (herunder Windows og Linux) og en række programmeringssprog (herunder .NET, Java, Node.js, Python, fonetisk, PHP og C++ og mobile programmeringssprog) for praktisk udvikling. Azure-lager Fremviser også dataressourcer via enkel REST API'er, der er tilgængelige for alle stand til at sende og modtage data via HTTP/HTTPS klienter.

Azure Premium lagerplads tilbyder høj ydeevne og lav ventetid disk understøttelse af i/o-intensivt arbejdsbelastninger, som kører på virtuelle Azure-computere. Du kan vedhæfte flere fast datadisce til en virtuel maskine og konfigurere dem, så det opfylder dine ydeevnekrav til med Azure Premium lager. Hver datadisk understøttes af en SSD disk i Azure Premium lager til optimal i/o-ydeevne. Se [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](storage-premium-storage.md) få mere at vide.

## <a name="introducing-the-azure-storage-services"></a>Introduktion til Azure lagerplads tjenesterne

Azure-lager indeholder følgende fire tjenester: Blob storage, Table storage, kø lager og fillagring.

- BLOB Storage gemmer ustrukturerede objektdata. En blob kan være en hvilken som helst type af tekst eller binære data, som et dokument, mediefil eller program installer. BLOB-lager kaldes også objekt lagerplads.
- Table Storage gemmer strukturerede datasæt. Tabellagring er en NoSQL nøgle-attribut-datalager, som giver mulighed for hurtig udvikling og hurtig adgang til store datamængder.
- Kø lagerplads indeholder pålidelig messaging til arbejdsprocesser og af kommunikation mellem komponenter i skytjenester.
- Fillagring indeholder delt storage til ældre programmer ved hjælp af standard små og mellemstore virksomheder-protokollen. Azure virtuelle maskiner og skytjenester kan dele data fra en fil på tværs af programkomponenter via tilsluttede aktier, og lokale programmer kan få adgang til data fra en fil i en del via tjenesten fil REST-API.

En Azure-lager-konto er en sikker konto, der giver dig adgang til tjenester i Azure-lager. Kontoen lagerplads indeholder det entydige navneområde til ressourcerne lagerplads. Billedet nedenfor viser relationerne mellem Azure lagerplads ressourcer i en lagerplads konto:

![Azure-lagerressourcer](./media/storage-introduction/storage-concepts.png)

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

[AZURE.INCLUDE [storage-versions-include](../../includes/storage-versions-include.md)]

## <a name="blob-storage"></a>BLOB-lager

For brugere med store datamængder ustrukturerede objekt til at gemme i skyen, tilbyder Blob-lager en økonomisk og SVG løsning. Du kan bruge Blob-lager til at gemme indhold f.eks.:

- Dokumenter
- Sociale data som billeder, videoer, musik og blogge
- Sikkerhedskopier af filer, computere, databaser og enheder
- Billeder og tekst for webprogrammer
- Af konfigurationsdata til skyen programmer
- Stor data, som logfiler og andre store datasæt

Alle blob er organiseret i en objektbeholder. Beholdere giver også en god måde at tildele sikkerhedspolitikker til grupper af objekter. En lagerplads konto kan indeholde et vilkårligt antal beholdere, og en objektbeholder kan indeholde et vilkårligt antal BLOB op til 500 TB kapacitetsgrænsen på kontoen lagerplads.  

BLOB storage tilbud tre typer blob, blokere blob, tilføje BLOB og siden BLOB (diske).

- Bloker BLOB er optimeret til streaming og lagring af skyen objekter, og er et godt valg til lagring af dokumenter, mediefiler, sikkerhedskopier osv.
- Tilføje BLOB ligner Bloker blob, men er optimeret til tilføje handlinger. En Føj blob kan opdateres kun ved at tilføje en ny blok til slutningen. Tilføje BLOB er et godt valg for scenarier som logføring, hvor nye data skal skrives kun til slutningen af blob.
- Siden BLOB er optimeret til at repræsentere IaaS diske og understøttende tilfældige skriver og kan være op til 1 TB i størrelse. Et netværk med Azure virtuelt vedhæftet IaaS disken er en virtuel harddisk, der er gemt som en side blob.

For meget store datasæt, hvor netværk begrænsninger foretage overfører eller henter data til Blob-lager over en urealistiske netværksforbindelse, kan du leverer en harddisk til Microsoft for at importere eller eksportere data direkte fra datacenter. Se [Brug af tjenesten Microsoft Azure Importér/Eksportér til at overføre Data for at Blob Storage](storage-import-export-service.md).

## <a name="table-storage"></a>-Tabellager

Moderne programmer kræver ofte data butikker med større skalerbarhed og fleksibilitet end tidligere generationer af software, der kræves. Tabellagring indeholder meget tilgængelig, stort omfang SVG lagerplads, så programmet kan automatisk skaleres til at opfylde brugernes behov. Tabellagring er Microsofts NoSQL nøgle/attribut store – den har et schemaless design, hvilket gør det adskiller sig fra traditionelle relationsdatabaser. Det er nemt at tilpasse dine data som behovet i dit program evolve med et schemaless datalager. Tabellagring er nem at bruge, så udviklere kan oprette programmer hurtigt. Adgang til data er hurtigt og økonomisk for alle typer programmer.  Tabellagring er typisk betydeligt lavere i omkostninger end traditionelle SQL for lignende datamængder.

Tabellagring er en nøgle-attribut butik, hvilket betyder, at hver værdi i en tabel er gemt med et indtastede egenskabsnavn. Egenskabsnavn kan bruges til filtrering og angive udvælgelseskriterier. En samling af egenskaber og deres værdier omfatter et objekt. Da Table storage er schemaless, to objekter i den samme tabel kan indeholde forskellige samlinger af egenskaber, og disse egenskaber kan være forskellige datatyper.

Du kan bruge Table storage til at gemme fleksible datasæt, som brugerdata for webprogrammer, adressekartoteker, enhedsoplysninger og enhver anden type metadata, der kræver, at din tjeneste.  Du kan gemme et vilkårligt antal objekter i en tabel, og en lagerplads konto kan indeholde et vilkårligt antal tabeller, op til kapacitetsgrænsen til kontoen, lagerplads.

Som BLOB og køer udviklere kan administrere og access-tabel-lager, med standard RESTEN protokoller, men Table storage understøtter også et undersæt af OData-protokollen, forenkle avancerede forespørgsler funktioner og aktivere både JSON og AtomPub (XML-baseret) formater.

Til dags Internet-baserede programmer tilbyder NoSQL databaser som Table storage et populære alternativ til traditionel relationsdatabaser.

## <a name="queue-storage"></a>Kø lagerplads

Designe programmer til skala, Afkoblet programkomponenter ofte, så de kan skalere uafhængigt af hinanden. Kø lagerplads indeholder en pålidelig messaging-løsninger til asynkron kommunikation mellem programkomponenter, uanset om de kører i skyen, på skrivebordet, på en lokal server eller på en mobilenhed. Kø lagerplads understøtter også administration af asynkrone opgaver og opbygning af arbejdsprocesser.

En lagerplads konto kan indeholde et vilkårligt antal køer. En kø kan indeholde et vilkårligt antal meddelelser, op til kapacitetsgrænsen til kontoen, lagerplads. Individuelle meddelelser kan være op til 64 KB.

## <a name="file-storage"></a>Lagring af filer

Azure fillagring indeholder skybaseret små og mellemstore virksomheder filshares, så du kan overføre ældre programmer, som er afhænger af filshares til Azure hurtigt og uden dyrt de. Med Azure fillagring programmer, der kører i Azure virtuelle maskiner eller skytjenester kan oprette forbindelse til et filshare i skyen, ligesom et desktopprogram tilslutter en typisk små og mellemstore virksomheder del. Et vilkårligt antal programkomponenter kan derefter tilslutte og få adgang til det pågældende lagerplads filshare samtidigt.

Da et filshare lagerplads er et standard filshare til små og mellemstore virksomheder, kan programmer, der kører i Azure kan få adgang til data i dialogboksen del via fil systemet I/O APIs. Udviklere kan derfor udnytte deres eksisterende kode og færdigheder til at overføre eksisterende programmer. IT-fagfolk kan bruge PowerShell-cmdlet'er til at oprette, tilslutte og administrere lagerplads filshares som en del af administrationen af Azure-programmer.

Som andre tjenester for Azure lagerplads Fremviser fillagring en REST-API til at få adgang til data i en del. Lokalt programmer kan ringe til fillagring REST-API til at få adgang til data i et filshare. Denne måde, en virksomhed kan vælge at overføre visse ældre programmer til Azure og fortsætte med at køre andre fra i deres egen organisation. Bemærk, at tilslutte et filshare er kun muligt for programmer, der kører i Azure; et lokalt program kan kun få adgang til det pågældende filshare via REST-API.

Distribuerede programmer kan også bruge fillagring til at gemme og dele nyttige programmet data og udvikling og test værktøjer. For eksempel et program kan gemme en konfigurationsfil og diagnosticering data som logfiler, målepunkter og nedbrud gemmer en fil, lagerplads dele, så de er tilgængelige for flere virtuelle maskiner eller roller. Udviklere og administratorer kan gemme værktøjer, som de skal bruge til at oprette eller administrere et program i et filshare lagerplads, der er tilgængelige for alle komponenter i stedet for at installere dem på hver virtuel computer eller rolle forekomst.

## <a name="access-to-blob-table-queue-and-file-resources"></a>Adgang til Blob, tabel, kø og filressourcer

Som standard kontoejeren lagerplads har adgang til ressourcer i kontoen lagerplads. For sikkerheden for dine data, skal være godkendt alle anmodninger mod ressourcer i din konto. Godkendelse er afhængig af en delt nøgle-model. BLOB kan også være konfigureret til at understøtte anonym godkendelse.

Kontoen lagerplads er tildelt to privat access-taster ved oprettelse, der bruges til godkendelse. Har du to nøgler sikrer, at dit program forbliver tilgængelige, når du jævnligt genoprette tasterne almindelige key management sikkerhed.

Hvis du har brug for at tillade brugere kontrolleret adgang til dine lagerplads ressourcer, kan du oprette en delt adgang signatur. En delt adgang signatur (SAS) er et token, der kan føjes til en URL-adresse, der giver delegeret adgang til en ressource, lagerplads. Alle, der har tokenet har adgang til ressourcen, den peger på med de tilladelser, der angiver, til tidsrum, at den er gyldig. Begynder med version 2015-04-05, Azure-lager understøtter to typer af delt adgang signaturer: service SAS og SAS-konto.

Tjenesten SAS uddelegerer adgang til en ressource i kun én af lagerplads tjenester: Tjenesten Blob, kø, tabel eller fil.

En konto SAS delegerer adgang til ressourcer i en eller flere af lagerplads tjenester. Du kan uddelegere adgang til tjenesten niveau handlinger, der ikke er tilgængelige med en tjeneste SAS. Du kan også stedfortræderadgang til at læse, skrive og slette handlinger på blob beholdere, tabeller, forespørgsler og filshares, der ikke er tilladt sammen med en tjeneste SAS.

Til sidst, kan du angive, at en objektbeholder og dens BLOB eller en bestemt blob er tilgængelige for offentlig adgang. Når du angiver, at en objektbeholder eller blob er offentlig, kan alle åbne den anonymt; Ingen godkendelse er påkrævet.  Offentlige beholdere og BLOB er nyttige til visning af ressourcer som medier og dokumenter, der er hostet på websteder.  For at reducere netværksventetid til en global målgruppe, kan du gemme i cachen blob-data, der bruges af websteder med Azure CDN.

Du kan finde flere oplysninger om delt adgang signaturer i [Ved hjælp af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md) . [Administrere anonym adgang til beholdere og BLOB](storage-manage-access-to-resources.md) og [godkendelse til Azure lagerplads Services](https://msdn.microsoft.com/library/azure/dd179428.aspx) yderligere oplysninger finder du på sikker adgang til kontoen lagerplads.

## <a name="replication-for-durability-and-high-availability"></a>Replikering for holdbarhed og høj tilgængelighed

Dataene i din Microsoft Azure-lager konto replikeres altid for at sikre holdbarhed og høj tilgængelighed. Replikering kopierer dine data, i det samme datacenter eller til et andet datacenter, afhængigt af hvilken gentagelse indstilling du vælger. Replikering beskytter dine data og bevarer dit program op og klokkeslæt i tilfælde af midlertidige hardware mislykkede forsøg. Hvis dine data replikeres til et andet datacenter, beskytter, der også dine data mod opstår nedbrud i den primære placering.

Replikering sikrer, at kontoen lagerplads opfylder [Service-serviceaftale (SLA) for lagerplads](https://azure.microsoft.com/support/legal/sla/storage/) selv trods mislykkede forsøg. Se SLA oplysninger om Azure-lager garanterer for holdbarhed og tilgængelighed. 

Når du opretter en lagerplads konto, kan du vælge en af følgende indstillinger for gentagelse:  

- **Lokalt overflødige lagerplads (LRS).** Lokalt overflødige lagerplads fører tre kopier af dine data. LRS replikeres tre gange i et enkelt datacenter i et enkelt område. LRS beskytter dine data fra mislykkede forsøg på normal hardware, men ikke fra fejl i et enkelt datacenter.  

    LRS tilbydes på rabat. For signaltabet anbefales det, at du bruger geografisk overflødige lagring, beskrevet nedenfor.


- **Zone overflødige lagerplads (ZRS).** Zone overflødige lagerplads vedligeholder tre kopier af dine data. ZRS replikeres tre gange på tværs af to eller tre faciliteter, i et enkelt område eller på tværs af to områder, give højere holdbarhed end LRS. ZRS sikrer, at dine data er robust inden for et enkelt område.  

    ZRS giver et højere niveau af holdbarhed end LRS; dog for signaltabet anbefaler vi, at du bruger geografisk overflødige lagring, beskrevet nedenfor.  

    > [AZURE.NOTE] ZRS er i øjeblikket kun tilgængelig for Bloker BLOB og er kun understøttes for versioner 2014-02-14 eller nyere.
    >
    > Når du har oprettet kontoen lager og markeret ZRS, kan du ikke konvertere den til en anden type gentagelse, bruge eller omvendt.

- **Geografisk overflødige lagerplads (GRS)**. GRS vedligeholder seks kopier af dine data. Med GRS, dine data replikeres tre gange i det primære område og replikeres også tre gange i en sekundær region hundredvis i sømil væk fra det primære region, giver det højeste niveau af holdbarhed. I tilfælde af en fejl i det primære område der Azure-lager failover til den sekundære område. GRS sikrer, at dine data er robust i to separate områder.

    Du kan finde oplysninger om primære og sekundære par efter område [Azure områder](https://azure.microsoft.com/regions/).

- **Læseadgang geografisk overflødige lagerplads (RA-GRS)**. Læseadgang geografisk overflødige lagerplads kopieres dataene til en sekundær geografiske placering, og som også giver læseadgang til dine data i den sekundære placering. Læseadgang geografisk overflødige lagerplads giver dig adgang til dine data fra primært eller den sekundære placering, hvis én placering bliver utilgængeligt. Læseadgang geografisk overflødige lagerplads er standardindstillingen for kontoen lagerplads som standard, når du opretter den. 

    > [AZURE.IMPORTANT] Du kan ændre, hvordan dine data replikeres efter kontoen lagerplads er blevet oprettet, medmindre du har angivet ZRS, når du har oprettet kontoen. Bemærk imidlertid, kan du blive pålagt et ekstra enkeltstående dataoverførsel omkostninger, hvis du skifter fra LRS til GRS eller RA-GRS.

Du kan finde yderligere oplysninger om indstillinger for lagring af gentagelse i [Azure-lager gentagelse](storage-redundancy.md) .

Prisoplysninger for lagerplads konto gentagelse, se [Azure lagerplads priser](https://azure.microsoft.com/pricing/details/storage/). Du kan få flere oplysninger om hvilke tjenester der er tilgængelige i hvert område i [Azure områder](https://azure.microsoft.com/regions/#services) .

Arkitektonisk oplysninger om levetid Azure-lager [SOSP papir – Azure-lager: A meget tilgængelige Skylagringstjeneste med stærke konsistens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).


## <a name="transferring-data-to-and-from-azure-storage"></a>Overførsel af Data til og fra Azure-lager

Du kan bruge værktøjet AzCopy kommandolinjen til at kopiere blob, filer og tabeldata i kontoen lagerplads eller på tværs af lagerplads konti. Yderligere oplysninger finder du i [overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md) .

AzCopy er bygget oven på [Azure Data bevægelse bibliotek](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/), som er tilgængelige i preview.

Tjenesten Azure Importér/Eksportér giver en metode til at importere blob data til eller eksportere blob-data fra din lagerplads konto via en harddisk disk, der er sendt til Azure datacentret. Se [Brug af Microsoft Azure Importér/Eksportér Service for at overføre Data til Blob-lager](storage-import-export-service.md), du kan finde flere oplysninger om tjenesten Importér/Eksportér.

## <a name="pricing"></a>Priser

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

## <a name="storage-apis-libraries-and-tools"></a>API'er storage, biblioteker og værktøjer

Azure lagerplads ressourcer kan åbnes af et sprog, kan foretage HTTP/HTTPS-anmodninger. Desuden tilbyder Azure-lager programming biblioteker for flere populære sprog. Disse biblioteker forenkle mange aspekter af arbejde med Azure-lager ved håndtering af oplysninger som synkron og Asynkron aktivering, samling af handlinger, undtagelse management, automatisk forsøg, funktionsdygtige funktionsmåde osv. Biblioteker er tilgængelige for øjeblikket til følgende sprog og platforme, med andre i pipeline:

### <a name="azure-storage-data-services"></a>Azure-lager Data Services

- [Lagerplads Services REST-API](http://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Lagerplads klientbibliotek til .NET, Windows Phone og Windows Runtime](https://www.nuget.org/packages/WindowsAzure.Storage/)
- [Lagerplads klientbibliotek til C++](https://github.com/Azure/azure-storage-cpp)
- [Lagerplads klientbibliotek til Java/Android](/develop/java/)
- [Lagerplads klientbibliotek til Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
- [Lagerplads klientbibliotek til PHP](/develop/php/)
- [Lagerplads klientbibliotek til fonetisk](/develop/ruby/)
- [Lagerplads klientbibliotek til Python](/develop/python/)
- [Lagerplads cmdletter til PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt269418.aspx)

### <a name="azure-storage-management-services"></a>Azure-lager Management Services

- [Lagerplads ressource udbyder RESTEN API Reference](https://msdn.microsoft.com/library/azure/mt163683.aspx)
- [Lagerplads ressource udbyder klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx)
- [Lagerplads ressource udbyder cmdletter til PowerShell 1.0](https://msdn.microsoft.com/library/azure/mt607151.aspx)
- [Lagerplads Service Management REST-API (klassisk)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-services"></a>Azure-lager datatjenester bevægelse

- [Lagerplads Importér/Eksportér Service REST-API](https://msdn.microsoft.com/library/azure/dn529096.aspx)
- [Lagerplads Data bevægelse klientbibliotek til .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### <a name="tools-and-utilities"></a>Værktøjer og funktioner

- [Azure-lager Explorer](http://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
- [Azure-lager klientværktøjer](storage-explorers.md)
- [Azure SDK'er og værktøjer](https://azure.microsoft.com/tools/)
- [Azure-lager Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
- [Azure PowerShell](../powershell-install-configure.md)
- [AzCopy kommandolinjen Utility](http://aka.ms/downloadazcopy)

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Azure-lager, kan du gennemse disse ressourcer:

### <a name="documentation"></a>Dokumentation

- [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)

### <a name="for-administrators"></a>For administratorer

- [Brug af Azure PowerShell med Azure-lager](storage-powershell-guide-full.md)
- [Brug af Azure CLI med Azure-lager](storage-azure-cli.md)

### <a name="for-net-developers"></a>For udviklere .NET

- [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md)
- [Introduktion til Azure Table storage ved hjælp af .NET](storage-dotnet-how-to-use-tables.md)
- [Introduktion til Azure kø lagerplads ved hjælp af .NET](storage-dotnet-how-to-use-queues.md)
- [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md)

### <a name="for-javaandroid-developers"></a>For udviklere Java/Android

- [Sådan bruger du Blob-lager fra Java](storage-java-how-to-use-blob-storage.md)
- [Sådan bruger du Table storage fra Java](storage-java-how-to-use-table-storage.md)
- [Sådan bruger du kø lagerplads fra Java](storage-java-how-to-use-queue-storage.md)
- [Sådan bruger du fillagring fra Java](storage-java-how-to-use-file-storage.md)

### <a name="for-nodejs-developers"></a>For Node.js udviklere

- [Sådan bruger du Blob-lager fra Node.js](storage-nodejs-how-to-use-blob-storage.md)
- [Sådan bruger du Table storage fra Node.js](storage-nodejs-how-to-use-table-storage.md)
- [Sådan bruger du kø lagerplads fra Node.js](storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>For PHP udviklere

- [Sådan bruger du Blob-lager fra PHP](storage-php-how-to-use-blobs.md)
- [Sådan bruger du Table storage fra PHP](storage-php-how-to-use-table-storage.md)
- [Sådan bruger du kø lagerplads fra PHP](storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>For fonetisk udviklere

- [Sådan bruger du Blob-lager fra fonetisk](storage-ruby-how-to-use-blob-storage.md)
- [Sådan bruger du Table storage fra fonetisk](storage-ruby-how-to-use-table-storage.md)
- [Sådan bruger du kø lagerplads fra fonetisk](storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>For Python udviklere

- [Sådan bruger du Blob-lager fra Python](storage-python-how-to-use-blob-storage.md)
- [Sådan bruger du Table storage fra Python](storage-python-how-to-use-table-storage.md)
- [Sådan bruger du kø lagerplads fra Python](storage-python-how-to-use-queue-storage.md)
- [Sådan bruger du fillagring fra Python](storage-python-how-to-use-file-storage.md)
