<properties
    pageTitle="Azure Premium opbevaring: Designe ydeevne | Microsoft Azure"
    description="Designe høj ydeevne programmer, der bruger Azure Premium-lager. Premium lagerplads giver høj ydeevne og lav ventetid disk understøttelse af jeg/O-intensivt arbejdsbelastninger, som kører på virtuelle Azure-computere."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium opbevaring: Design med høj ydeevne

## <a name="overview"></a>Oversigt  
I denne artikel indeholder retningslinjer for opbygning af programmer med høj ydeevne ved hjælp af Azure Premium-lager. Du kan bruge instruktionerne i dette dokument kombineres med ydeevnen bedste fremgangsmåder, der gælder for teknologier, der bruges af dit program. For at illustrere retningslinjerne, har vi brugt SQL Server, der kører på Premium lagerplads som et eksempel i hele dokumentet.

Vi sig ydeevne scenarier for lagerplads lag i denne artikel, skal du optimere programlag. Hvis du er vært for en SharePoint-Farm på Azure Premium lager, kan du bruge SQL Server-eksempler fra i denne artikel til at optimere databaseserveren. Desuden kan optimere SharePoint-Farm webserver og programserver i at få de fleste ydeevne.

Denne artikel kan hjælpe answer følge ofte stillede spørgsmål om optimering af ydeevne i programmet på Azure Premium lager

-   Sådan måle dit program ydeevne?  
-   Hvorfor du ikke kan se forventede høj ydeevne?  
-   Hvilke faktorer påvirke dit program ydeevne på Premium lagerplads?  
-   Hvordan disse faktorer påvirke ydeevnen for dit program på Premium lagerplads?  
-   Hvordan kan du optimerer for IOP'ER, båndbredde og ventetid?  

Vi har angivet disse retningslinjer specifikt for Premium lagerplads, fordi arbejdsbelastninger, som kører på Premium lagerplads er meget følsomme ydeevne. Vi har angivet eksempler, hvor det er relevant. Du kan også anvende nogle af disse retningslinjer til programmer, der kører på IaaS FOS med Standard lagerplads diske.

Før du begynder, hvis du er ny bruger af Premium lagerplads, først læser den [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](storage-premium-storage.md) artikel og [Azure Premium lagerplads skalerbarhed og ydeevne destinationer](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Program-indikatorer  
Vi vurdere, om et program udfører godt eller ikke bruger ydeevne som indikatorer, hvor hurtigt et program behandling af en brugeranmodning, hvor meget data, der er et program, der behandler per anmodning, hvor mange, som er et programbehandling i en bestemt tidsperiode, hvor lang tid en bruger har vente på at få et svar, når du har sendt deres anmodning. Tekniske vilkårene for disse indikatorer er IOP'ER, overførselshastighed eller båndbredde og ventetid.

I dette afsnit beskriver vi almindelige indikatorer i forbindelse med Premium lagerplads. I følgende afsnit, krav til indsamling, lærer du at måle disse indikatorer for dit program. Senere i optimering af programmets ydeevne, vil du lære om de faktorer, der påvirker disse indikatorer og anbefalinger til at optimere dem.

## <a name="iops"></a>IOP'ER  
IOP'ER er tal af anmodninger, som dit program sender til lagerplads diske i et sekund. Handlingen input/output kunne læses eller skrive, sekventielle eller vilkårlige. OLTP computerprogrammer som et onlinedetail websted har brug at behandle mange samtidige anmodninger med det samme. Brugeranmodninger er Indsæt og opdatere omfattende databasetransaktioner, som programmet skal behandle hurtigt. Derfor kræver OLTP programmer meget høj IOP'ER. Disse programmer kan håndtere millioner af små og tilfældige IO-anmodning. Hvis du har disse et program, skal du designe programmet infrastrukturen optimere til IOP'ER. I sektionen senere, *Optimering af ydeevne i programmet*, gennemgås detaljerede oplysninger om de faktorer, der skal du overveje for at få høj IOP'ER.

Når du vedhæfte en premium lagerplads disk med din høj skalering VM, Azure bestemmelser for dig et garanterede antal IOP'ER ud fra specifikationen disk. For eksempel danner en P30 disk 5000 IOP'ER. Hver høj skala VM størrelse er også en bestemt IOP'ER grænse, som det kan opretholde. Eksempelvis har en Standard GS5 VM 80.000 IOP'ER begrænse.

## <a name="throughput"></a>Overførselshastighed  
Gennemløb eller båndbredde er mængden data, der sender dit program til lagerplads diske inden for et angivet tidsinterval. Hvis dit program er udført input/output-handlinger med store EY enhedsstørrelser, kræver høj overførselshastighed. Data warehouse programmer har tendens til at udstede scanning intensivt handlinger, som store dele af data på et tidspunkt og ofte udføre flere handlinger. Disse programmer kræver med andre ord højere overførselshastighed. Hvis du har disse et program, skal du designe infrastrukturen optimere til overførsel. I næste afsnit beskriver vi detaljerede oplysninger om de faktorer, du skal justere for at opnå dette.

Når du vedhæfte en premium lagerplads disk med en høj skalering VM, Azure bestemmelser overførselshastighed ud fra denne disk specifikation. For eksempel danner en P30 disk 200 MB hver anden disk overførselshastighed. Hver høj skala VM størrelse har også som bestemt overførselshastighed grænse, som det kan opretholde. For eksempel har Standard GS5 VM en maksimale antal gennemløb 2000 MB sekundet.

Der findes en relation mellem overførselshastighed og IOP'ER som vist i nedenstående formlen.

![](media/storage-premium-storage-performance/image1.png)

Det er derfor vigtigt til at bestemme de optimal overførselshastighed og IOP'ER værdier, der kræver, at dit program. Når du forsøger at optimere en, bliver den anden også påvirket. I en nyere sektion, *Optimering af ydeevne i programmet*, vil vi diskutere i få mere at vide om optimering af IOP'ER og overførselshastighed.

## <a name="latency"></a>Ventetid  
Ventetid er den tid, det tager et program, der skal modtage en enkelt anmodning, sende det til lagerplads diskene og sende svar til klienten. Dette er et kritiske mål for et program ydeevne ud over IOP'ER og overførselshastighed. Ventetid på en disk med premium lagerplads er den tid, det tager at hente oplysninger om en anmodning og kommunikere den tilbage til dit program. Premium lagerplads indeholder ensartet lav latenstider. Hvis du aktiverer skrivebeskyttet host cachelagring på premium lagerplads diske, kan du få meget lavere Læs ventetid. Vi vil diskutere Disk cachelagring mere detaljeret i senere afsnit på *Optimering af ydeevne i programmet*.

Når du optimerer din program tilladelse til at få højere IOP'ER og overførselshastighed, påvirker det forsinkelse på dit program. Efter justering af ydeevnen programmet, altid evaluere forsinkelse af programmet for at undgå uventede lang ventetid funktionsmåde.

## <a name="gather-application-performance-requirements"></a>Indsamle krav til ydeevne  
Det første trin i designe høj ydeevne programmer, der kører på Azure Premium lager er at forstå kravene til ydeevne af programmet. Når du har indsamlet krav til ydeevne, kan du optimere dit program tilladelse til at opnå den mest optimal ydeevne.

Vi forklaring almindelige indikatorer, IOP'ER, overførselshastighed og ventetid i det forrige afsnit. Du skal angive, hvilken af disse indikatorer er vigtige for dit program til at levere den ønskede brugeroplevelse. For eksempel vigtig høj IOP'ER for OLTP programmer behandling millioner af transaktioner i et sekund. Mens høj overførselshastighed er afgørende for Data Warehouse programmer behandling af store mængder data i et sekund. Meget lav ventetid er afgørende for realtid computerprogrammer som direkte video-streaming websteder.

Dernæst skal mål optimal ydeevnekravene i dit program i hele dets levetid. Du kan bruge eksempel tjekliste under som en start. Registrere de optimal ydeevnekrav under normal, top og uden for normal arbejdstid arbejdsbelastningen perioder. Ved at identificere krav til alle arbejdsbelastninger niveauer, vil du kunne bestemme det overordnede ydeevne kravet af dit program. Normal arbejdsbelastningen for en e-handel websted kan for eksempel transaktioner det fungerer i de fleste dage i året. Spidsbelastning arbejdsbelastningen til webstedet, bliver de transaktioner, den fungerer under jul eller speciel salg begivenheder. Spidsbelastning arbejdsbelastningen er typisk erfarne i en begrænset periode, men kan kræve, at dit program tilladelse til at skalere to eller flere gange driften normal. Find ud af 50 fraktil, 90 fraktil og 99 fraktil krav. Dette kan filtrere en hvilken som helst outliers i kravene til ydeevne, og du kan fokusere din indsats på optimere til de rigtige værdier.

**Tjekliste for programmet ydeevne krav**

| **Krav til ydeevne** | **50 fraktil** | **90 fraktil** | **99 fraktil** |
|---|---|---|---|
| Maks. Transaktioner i sekundet | | | |
| % Læst handlinger            | | | |
| % Skrivehandlinger           | | | |
| % Tilfældigt handlinger          | | | |
| % Sekventielle handlinger      | | | |
| EY anmodning størrelse              | | | |
| Gennemsnitlig overførselshastighed           | | | |
| Maks. Overførselshastighed              | | | |
| Min. Ventetid                 | | | |
| Gennemsnitlig forsinkelse              | | | |
| Maks. CPU                     | | | |
| Gennemsnitlig CPU                  | | | |
| Maks. Hukommelse                  | | | |
| Gennemsnitlig hukommelse               | | | |
| Dybde i kø                  | | | |

>**Vigtig bemærkning:**  
>Du skal overveje skalering disse tal sammen ud fra forventede fremtidig vækst af dit program. Det er en god ide at planlægge vækst forvejen, da det kan være sværere at ændre infrastrukturen til forbedring af ydeevnen senere.

Hvis du har et eksisterende program, og du vil flytte til Premium-lager, først opbygge tjekliste over til det eksisterende program. Derefter opbygge en prototyper af dit program på Premium lager og designe det program, der er baseret på retningslinjer, der er beskrevet i *Optimering af ydeevne i programmet* i en senere afsnit i dette dokument. I næste afsnit beskrives de værktøjer, du kan bruge til at indsamle de ydeevne mål.

Oprette en tjekliste, der svarer til din eksisterende program for model. Ved hjælp af Benchmarking værktøjer kan du simulere arbejdsmængder og måle ydeevne på programmet prototyper. I afsnittet på [Benchmarking](#benchmarking) til at få mere at vide. Ved at gøre, så du kan bestemme, om Premium lagerplads kan svarer eller overskrider dine krav til ydeevne programmet på computeren. Du kan derefter implementere de samme retningslinjer for dit fremstilling program.

### <a name="counters-to-measure-application-performance-requirements"></a>Tællere til at måle krav til ydeevne  
Den bedste måde at måle krav til ydeevne i dit program, er at bruge overvågning af ydeevnen-værktøjer, der leveres af operativsystemet på serveren. Du kan bruge PerfMon til Windows og iostat for Linux. Disse værktøjer registrere tællere, der svarer til de enkelte foranstaltninger, der er beskrevet i afsnittet ovenfor. Du skal hente værdierne i disse tællere, når programmet kører dens normal, top og uden for normal arbejdstid arbejdsmængder.

Ydelsestællerne er tilgængelige for processor, hukommelse og, hver logisk disk og fysisk disk på din server. Når du bruger premium lagerplads diske med en VM, fysisk disktællere får for hver premium lagerplads disk, og logisk disktællere for hver enhed, der er oprettet på premium lagerplads disk. Du skal hente værdierne om en disk, der hoster dit programs arbejdsbyrde. Hvis der er en en til en tilknytning mellem logiske og fysiske, kan du referere til fysisk disktællere; Ellers referere til de logiske disktællere. Kommandoen iostat genererer på Linux, en CPU og diskplads anvendelsen rapport. Rapporten disk anvendelsen indeholder statistik per fysisk enhed eller partition. Hvis du har en databaseserver med dens data og log på separate diske, indsamle disse oplysninger om en begge disk. Beskriver tællere til diske, processor og hukommelse under tabel:

| Tæller | Beskrivelse | PerfMon | Iostat |
|---|---|---|---|
| **IOP'ER eller transaktioner i sekundet** | Antallet af i/o-anmodninger udstedt til lagerplads disken sekundet. | Disken læser/sec <br> Disken skriver/sec | kreative <br> r/s <br> w/s |
| **Disken læsning og skrivning** | % af læser og skrive handlinger, der udføres på disken. | % Disken læst tid <br> % Disk skrive klokkeslæt | r/s <br> w/s |
| **Overførselshastighed** | Mængde af data, der læses fra eller skrives på disken sekundet. | Disken læste byte/sekund <br> Skrevne byte/sekund | kB_read/s <br> kB_wrtn/s |
| **Ventetid** | Samlet tid at fuldføre en disk IO-anmodning. | Gennemsnitlig Disk sec/læst <br> Gennemsnitlig diskplads sec-og skriveadgang | Venter på at blive <br> svctm |
| **EY størrelse** | Størrelsen på I/O, der anmoder om problemer, der skal lagerplads diskene. | Gennemsnitlig disken byte/læst <br> Gennemsnitlig diskplads byte-og skriveadgang | avgrq sz |
| **Dybde i kø** | Antal udestående I/O anmodninger vente til at læse formular eller skrives på disken lagerplads. | Aktuel længde på Disk kø | avgqu sz |
| **Maks. Hukommelse** | Mængden af hukommelse, der er nødvendige for at køre programmet hurtigere | % Aktiverede byte i brug | Brug vmstat |
| **Maks. CPU** | Beløb CPU, der er nødvendige for at køre programmet hurtigere | % Processortid | % util |

Få mere at vide mere om [iostat](http://linuxcommand.org/man_pages/iostat1.html) og [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Optimering af ydeevne i programmet  
De vigtigste faktorer, der påvirker ydeevnen i et program, der kører på Premium lagerplads er art af IO-anmodning, VM størrelse, diskstørrelse, antal disk Disk cachelagring, Multithreading og dybde i kø. Du kan styre nogle af disse faktorer med knapperne leveret af systemet. De fleste programmer kan ikke giver dig mulighed for at ændre EY størrelse og dybde i kø direkte. Hvis du bruger SQL Server, kan du vælge EY størrelse og kø dybden. SQL Server vælger de optimal EY størrelse og kø dybde værdier, der får den ydeevne, de fleste. Det er vigtigt at forstå virkningen af begge typer faktorer på din programmets ydeevne, så du kan klargøre relevante ressourcer for at overholde krav til ydeevne.

I dette afsnit, skal referere til de Tjekliste for krav program, du har oprettet, for at identificere, hvor meget du har brug at optimere din ydeevne med programmet. Baseret på, som kan du bestemme, hvilke faktorer fra denne sektion skal du finjustere. Køre benchmarking værktøjer på konfigurationen af programmet bevidne virkningerne af hver faktor på programmets ydeevne. Se afsnittet [Benchmarking](#Benchmarking) i slutningen af denne artikel for at få trin til at køre almindelige benchmarking værktøjer i Windows og Linux FOS.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimere IOP'ER, overførselshastighed og ventetid overblik  
Tabellen nedenfor opsummerer alle ydeevne faktorer og trinnene for at optimere IOP'ER, overførselshastighed og ventetid. Sektionerne følge dette resumé som beskriver hver faktor er meget mere dybde.

| | **IOP'ER** | **Overførselshastighed** | **Ventetid** |
|---|---|---|---|
| **Eksempel-Scenario** | Enterprise OLTP programmet kræver meget høj transaktioner hver anden rente.                                                                                                                                 | Virksomhedsdata opbevaring programmet behandling store datamængder. | I nærheden af realtid programmer, der kræver øjeblikkelig svar på anmodninger, som spiller på internettet. |
| Ydeevnen faktorer  | | | |
| **EY størrelse** | EY mindre giver højere IOP'ER.                                                                                                                                                                           | Større EY giver højere overførselshastighed. | |
| **VM størrelse** | Brug en VM størrelse, der tilbyder IOP'ER, der er større end din kravet om programmet. Se VM størrelser og deres IOP'ER grænser her. | Bruge en VM størrelse med overførselshastighed grænse større end din kravet om programmet. Se VM størrelser og deres overførselshastighed grænser her. | Brug en VM størrelse, tilbud skalere begrænsninger, der er større end din kravet om programmet. Se VM størrelser og deres begrænsninger her. |
| **Diskstørrelse** | Brug en diskstørrelse, der tilbyder IOP'ER, der er større end din kravet om programmet. Se diskstørrelser og deres IOP'ER grænser her. | Bruge en diskstørrelse med overførselshastighed grænse større end din kravet om programmet. Se diskstørrelser og deres overførselshastighed grænser her. | Brug en diskstørrelse, tilbud skalere begrænsninger, der er større end din kravet om programmet. Se diskstørrelser og deres begrænsninger her. |
| **VM og begrænsninger for disken skala** | IOP'ER grænsen for størrelsen VM valgt skal være større end samlede IOP'ER drives af premium lagerplads diske knyttet til den. | Overførselshastighed grænsen for størrelsen VM valgt skal være større end samlede gennemløb, der drives af premium lagerplads diske knyttet til den. | Begrænsninger for skala af VM størrelse valgt skal være større end samlede skala begrænsninger vedhæftede premium lagerplads diske. |
| **Cachelagring af disk** | Aktivere skrivebeskyttet Cache på premium lagerplads diske med læst tunge handlinger i at få højere læst IOP'ER. | | Aktivere skrivebeskyttet Cache på premium lagerplads diske med klar tunge handlinger i at få meget lav læse latenstider. |
| **Disken Striping** | Brug flere diske og fordeler dem sammen for at få en kombineret højere IOP'ER og overførselshastighed grænse. Bemærk, at den kombinerede grænse per VM skal være højere end de kombinerede grænser vedhæftede premium diske. | |
| **Stripestørrelse** | Mindre stribe for vilkårligt lille EY mønster fremgår af OLTP-programmer. Brug f.eks., stripestørrelse på 64KB for SQL Server OLTP program. | Større stribe for sekventielle store EY mønster fremgår af Data Warehouse programmer. Brug f.eks., 256KB stripestørrelse for SQL Server Data warehouse program. | |
| **Flere tråde** | Brug flere tråde push højere antallet af anmodninger til Premium-lager, der kan medføre højere IOP'ER og overførselshastighed. For eksempel angive en høj MAXDOP værdi skal fordeles flere CPU'er til SQL Server på SQL Server. | |
| **Dybde i kø** | Større dybde i kø giver højere IOP'ER. | Større dybde i kø giver højere overførselshastighed. | Mindre dybde i kø giver nederste latenstider. |

## <a name="nature-of-io-requests"></a>Karakter IO-anmodning  
Anmodning om en EY består af input/output handling, der skal udføre dit program. Identificere art IO-anmodning, tilfældigt eller sekventielt, kan læse- eller skrivetilladelser, lille eller stor, du se kravene til ydeevne af programmet. Det er meget vigtigt at forstå, hvilke EY anmodninger om, at de rigtige beslutninger, når du designer infrastrukturen programmet.

EY størrelse er en af de vigtigste faktorer. EY størrelse er størrelsen på input/output handlingen anmodningen genereres af programmet. EY størrelsen har en betydeligt indflydelse på ydeevnen især på IOP'ER og båndbredde, som programmet kan opnå. Følgende formel viser forholdet mellem IOP'ER EY størrelse og båndbredde/overførselshastighed.  
    ![](media/storage-premium-storage-performance/image1.png)

Nogle programmer gør det muligt at ændre deres EY størrelse, mens nogle programmer ikke gør. For eksempel SQL Server bestemmer optimal EY størrelsen selve og giver ikke brugere med en hvilken som helst knapperne til at ændre den. På den anden side Oracle indeholder en parameter ved navn [DB\_BLOKERE\_størrelse](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) ved hjælp af hvor du kan konfigurere i/o-anmodning om størrelsen på databasen.

Hvis du bruger et program, der ikke tillader, at du ændrer størrelsen EY, Brug retningslinjer i denne artikel for at optimere ydeevnen KPI, der er mest relevante for dit program. For eksempel

-   Et OLTP program genererer millioner af små og tilfældige IO-anmodning. Hvis du vil håndtere disse type IO-anmodning, skal du designe programinfrastrukturen for at få højere IOP'ER.  
-   Datatypen opbevaring programmet genererer store og sekventielle IO-anmodning. Hvis du vil håndtere disse type IO-anmodning, skal du designe infrastrukturen programmet på computeren til at hente højere båndbredde eller overførselshastighed.

Hvis du bruger et program, hvor du kan ændre EY størrelse, kan du bruge denne tommelfingerregel for EY størrelsen ud over andre ydeevne retningslinjer

-   Mindre EY at få højere IOP'ER. For eksempel 8 KB for et OLTP-program.  
-   Større EY at få højere båndbredde/overførselshastighed. For eksempel 1024 KB for et data warehouse program.

Her er et eksempel på, hvordan du kan beregne IOP'ER og overførselshastighed/båndbredde for dit program. Overvej at et program ved hjælp af en P30 disk. Det maksimale antal IOP'ER og overførselshastighed/båndbredde en P30 disk kan opnå er 5000 IOP'ER og 200 MB sekundet henholdsvis. Nu, hvis programmet kræver den maksimale IOP'ER fra P30 disken, og du bruger en mindre EY som 8 KB, er den resulterende båndbredde, du vil kunne få 40 MB sekundet. Men hvis programmet kræver den maksimale overførselshastighed/båndbredde fra P30 disk, og du bruger en større EY som 1024 KB, den resulterende IOP'ER vil være mindre, 200 IOP'ER. Finjustere derfor EY størrelsen, så det opfylder kravet om både dit program IOP'ER og overførselshastighed/båndbredde. Tabellen nedenfor opsummerer de forskellige EY størrelser og deres tilsvarende IOP'ER og overførselshastighed for en P30 disk.

| **Programmet krav** | **I/o-størrelse** | **IOP'ER** | **Overførselshastighed/båndbredde** |
|-----------------------------|--------------|----------|--------------------------|
| Maks IOP'ER                    | 8 KB         | 5.000    | 40 MB sekundet         |
| Maks overførselshastighed              | 1024 KB      | 200      | 200 MB sekundet        |
| Max Throughput + høj IOP'ER  | 64 KB        | 3,200    | 200 MB sekundet        |
| Max IOPS + høj overførselshastighed  | 32 KB        | 5.000    | 160 MB sekundet        |

For at få IOP'ER og båndbredde, der er højere end den maksimale værdi på en enkelt premium lagerplads disk skal du bruge flere premium diske spredt sammen. For eksempel stribe to P30 diske for at få en kombineret IOP'ER af 10.000 IOP'ER eller en kombinerede gennemløb af 400 MB sekundet. Som beskrevet i næste afsnit, skal du bruge en VM størrelse, der understøtter den kombinerede disk IOP'ER og overførselshastighed.

>**Bemærk!**  
>Kontrollér, at du ikke rammer gennemløb eller IOP'ER begrænsninger på disken eller VM når øge enten et, som du øger IOP'ER eller overførselshastighed den anden også øges.

Du kan køre benchmarking værktøjer på VM og diske bevidne virkningerne af EY størrelse på programmets ydeevne. Oprette flere test kører og bruge forskellige EY størrelse for hver Kør til at se, hvilken virkning. Se afsnittet [Benchmarking](#Benchmarking) i slutningen af denne artikel for at få mere at vide.

## <a name="high-scale-vm-sizes"></a>Høj skala VM størrelser  
Når du begynder at designe et program, er en af de første ting at gøre, Vælg en VM vært for dit program. Premium lagerplads følger der med høj skala VM størrelser, som kan køre programmer, der kræver højere Beregn power og en høj lokal disk i/o-ydeevne. Disse FOS giver hurtigere processorer, en højere hukommelse-til-core-bredde-forhold og en Solid-State Drive (SSD) for den lokale disk. Eksempler på høj skala FOS understøttende Premium lagerplads er DS, DSv2 og GS serien FOS.

Høj skala FOS er tilgængelige i forskellige størrelser med et andet antal CPU kerner, hukommelse, OS og midlertidige diskstørrelse. Hver VM størrelse har også maksimale antal datadisce, som du kan vedhæfte til VM. Derfor valgte VM størrelsen påvirker hvor meget behandling hukommelse, og lagerkapacitet er tilgængelige for dit program. Det påvirker også Beregn og lagerplads omkostninger. For eksempel er specifikationer af den største VM størrelse i en DS serie, DSv2 serie og en GS serie under:

| VM størrelse | CPU-kerner | Hukommelse | VM diskstørrelser | Maks. datadisce | Cachestørrelse | IOP'ER | Båndbredde Cache EY begrænsninger |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | 112 GB | OS = 1023 GB <br> Lokale SSD = 224 GB | 32 | 576 GB | 50.000 IOP'ER <br> 512 MB sekundet | 4.000 IOP'ER og 33 MB sekundet |
| Standard_GS5 | 32 | 448 GB | OS = 1023 GB <br> Lokale SSD = 896 GB | 64 | 4224 GB | 80.000 IOP'ER <br> 2000 MB sekundet | 5.000 IOP'ER og 50 MB sekundet |

Hvis du vil se en komplet liste over alle tilgængelige Azure VM størrelser, referere til [Windows VM størrelser](../virtual-machines/virtual-machines-windows-sizes.md) eller [Linux VM størrelser](../virtual-machines/virtual-machines-linux-sizes.md). Vælg en VM størrelse, der kan mødes med andre og tilpasse til dine krav til ydeevne ønskede program. Ud over dette skal du tage hensyn følge vigtige overvejelser, når du vælger VM størrelser.


*Begrænsninger for skala*  
De grænseværdier IOP'ER efter VM og efter disken er forskellige fra og uafhængigt af hinanden. Sørg for, at programmet, der driver IOP'ER inden for VM samt premium diskene knyttet til den. Ellers oplevelse ydeevne af programmet (throttling).

Forestil dig, at et program krav er maksimalt 4.000 IOP'ER som et eksempel. For at opnå skal klargøre du en P30 disk på en DS1 VM. P30 disken kan levere op til 5.000 IOP'ER. DS1 VM er dog begrænset til 3,200 IOP'ER. Derfor programmets ydeevne vil være begrænset af VM grænsen på 3,200 IOP'ER, og der vil være forringet ydeevne. For at undgå problemet skal du vælge en VM og diskplads størrelse, at begge opfylder programmet krav.

*Omkostninger for handling*  
Det er muligt, at dit samlede omkostninger for handlingen med Premium lagerplads er lavere end ved hjælp af Standard-lager i mange situationer.

For eksempel bør du overveje et program, der kræver 16.000 IOP'ER. For at opnå denne ydeevne, du skal bruge en Standard\_D14 Azure IaaS VM, som kan give en maksimale IOP'ER af 16.000 ved hjælp af 32 standard lagerplads 1TB diske. Hver 1TB standard lagerplads disk kan opnå op til 500 IOP'ER. De estimerede omkostninger for denne VM månedsbasis bliver $1,570. Månedlig omkostninger for 32 standard lagerplads diske bliver $1,638. De anslåede samlede månedlige udgifter bliver $3,208.

Men hvis du har placeret det samme program på Premium lagerplads, skal du et mindre VM og færre premium lagerplads disk, hvilket reducerer de samlede omkostninger. En Standard\_DS13 VM kan opfylder 16.000 IOP'ER kravet ved hjælp af fire P30 diske. DS13 VM har en maksimale IOP'ER af 25,600 og hver P30 disk har en maksimale IOP'ER af 5.000. Overordnet set denne konfiguration kan opnå 5.000 x 4 = 20.000 IOP'ER. De estimerede omkostninger for denne VM månedsbasis bliver $1,003. Den månedlige pris af fire P30 premium lagerplads diske bliver $544.34. De anslåede samlede månedlige udgifter bliver $1,544.

Tabellen nedenfor opsummerer omkostninger opdeling af dette scenario til Standard- og Premium lagerplads.

| | **Standard** | **Premium** |
|---|---|---|
| **Omkostninger for VM månedsbasis** | $1,570.58 (standard\_D14)   | $1,003.66 (standard\_DS13) |
| **Omkostninger for diske månedsbasis** | $1,638.40 (32 x 1 TB diske) | $544.34 (4 x P30 diske) |
| **Samlede omkostninger månedsbasis**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Med Azure Premium-lager får du det samme niveau af ydeevne for FOS kører Windows og Linux. Vi understøtter mange typer af Linux distros, og du kan se den komplette liste [her](../virtual-machines/virtual-machines-linux-endorsed-distros.md). Det er vigtigt at være opmærksom på, at forskellige distros egner sig bedre for forskellige typer arbejdsmængder. Du får vist forskellige niveauer af ydeevne afhængigt af den distro arbejdsbelastningen der kører på. Teste Linux distros med programmet, og vælg det, der fungerer bedst.


Når du kører Linux med Premium lagerplads, kan du se de seneste opdateringer om nødvendige drivere til at sikre høj ydeevne.

## <a name="premium-storage-disk-sizes"></a>Premium lagerplads diskstørrelser  
Azure Premium lagerplads indeholder tre diskplads størrelser i øjeblikket. Hver diskstørrelse har en anden skala grænse for IOP'ER, båndbredde og lagerplads. Vælg højre Premium lagerplads Disk størrelse afhængigt af programmet kravene og høj skalaen VM størrelse. Tabellen nedenfor viser tre diske størrelsen og deres egenskaber.

| **Disken Type**       | **P10**           | **R20 =**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Diskstørrelse           | 128 giB           | 512 giB           | 1024 giB (1 TB)   |
| IOP'ER per disk       | 500               | 2300              | 5000              |
| Overførselshastighed per disk | 100 MB sekundet | 150 MB sekundet | 200 MB sekundet |

Hvor mange diske du vælger, afhænger af disken størrelse er valgt. Du kan bruge en enkelt P30 disk eller flere P10 diske til at opfylde dine behov programmet. Tage konto overvejelser vises nedenfor, når du foretager valg.

*Skala begrænsninger (IOP'ER og overførselshastighed)*  
IOP'ER og overførselshastighed grænserne for hver Premium diskstørrelse er forskellige fra og uafhængige fra VM skala begrænsninger. Sørg for, at den samlede IOP'ER og overførselshastighed fra diskene er inden for skala grænser for den valgte VM størrelse.

For eksempel, hvis et program krav er op til 250 MB/sec overførselshastighed, og du bruger en DS4 VM med en enkelt P30 disk. DS4 VM kan give op til 256 MB/sec overførselshastighed. En enkelt P30 disk har dog overførselshastighed grænsen på 200 MB/sec. Programmet kan derfor være begrænset på 200 MB/sec på grund af grænsen på disken. Hvis du vil løse denne grænse, du klargør mere end ét datadisce til VM.

>**Bemærk!**  
>Læser served ved cachen medtages ikke i disken IOP'ER og overførselshastighed derfor ikke underlagt begrænsninger for disken. Cachen har separat IOP'ER og overførselshastighed grænsen per VM.
>
>For eksempel er har din læsning og skrivning 60MB/sec 40MB/sec henholdsvis og. Over tid, cachen warms og fungerer mere og mere af læser fra cachen. Derefter kan du få højere Skriv overførselshastighed fra disken.

*Antallet af diske*  
Bestemme antallet af diske skal du ved at vurdere programmet krav. Hver VM størrelse har også en begrænsning på antallet af diske, som du kan vedhæfte til VM. Dette er typisk to gange antallet kerner. Sørg for, at den VM størrelse, du vælger kan understøtte antallet af diske, det er nødvendigt.

Husk, at diskene Premium lagerplads har højere ydeevne funktioner sammenlignet med Standard lagerplads diske. Hvis du overfører dit program fra Azure IaaS VM ved hjælp af Standard lagerplads til Premium-lager, vil du derfor sandsynligt skal færre premium disk, der opnå den samme eller højere ydeevne for dit program.

## <a name="disk-caching"></a>Cachelagring af disk  
Høj skala VM'er, der udnytte Azure Premium lagerplads har en med flere lag cachelagring teknologi kaldet BlobCache. BlobCache bruger en kombination af virtuelt RAM og lokale SSD for cachelagring. Denne cache er tilgængelig for de Premium lagerplads fast og de VM lokale diske. Som standard er cache indstillingen angivet til læse-og skriveadgang for OS diske og skrivebeskyttet om en data disk hostes på Premium lagerplads. Med disk cachelagring aktiveret på Premium lagerplads disk, opnår høj skalaen FOS meget høj grad af pålidelighed, der overskrider den underliggende disk ydeevne.

>[AZURE.WARNING] Hvis du ændrer indstillingen cache på en Azure disk afbryder forbindelsen og igen vedhæfter target disken. Hvis det er operativsystemet disken, genstartes VM. Stop alle programmer og tjenester, der kan være påvirket af denne afbrydelser før du ændrer indstillingen diskplads cache.

Hvis du vil vide mere om, hvordan BlobCache fungerer, skal referere til indvendige [Azure Premium lager](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogindlæg.

Det er vigtigt at aktivere cache på det rigtige sæt diske. Vil håndtering af om du skal aktivere disk cachelagring på en disk premium eller ikke afhænger af arbejdsbelastningen mønstret disken. Tabellen nedenfor viser standard cacheindstillinger for OS og Data.

| **Disken Type** | **Standardindstilling Cache** |
|---|---|
| OS diskplads | ReadWrite |
| Datadisk | Ingen |

Følgende er de anbefalede disk cacheindstillinger om data disk,

| **Indstillingen for cachelagring disk** | **Anbefaling til at bruge denne indstilling** |
|---|---|
| Ingen | Konfigurere host-cache som ingen for kun skrive-og skriveadgang aktiveret. |
| Skrivebeskyttet | Konfigurere host-cache som skrivebeskyttet til skrivebeskyttet og læse-og skriveadgang. |
| ReadWrite | Konfigurere host-cache som ReadWrite kun, hvis dit program håndterer korrekt skriver cachelagrede data for fast diske, når det er nødvendigt. |

*Skrivebeskyttet*  
Ved at konfigurere skrivebeskyttet cachelagring af Premium lagerplads data diske, kan du opnå kort læst ventetid og får meget høj læst IOP'ER og overførselshastighed for dit program. Dette er forfaldsdato to årsager

1.  Læser udføres fra cachen, som er på VM hukommelse og lokale SSD, er meget hurtigere end læser fra disken data, som er Azure blob-lager.  
2.  Premium lagerplads tæller ikke læser served fra cachen mod disken IOP'ER og overførselshastighed. Dit program er derfor kan opnå højere samlede IOP'ER og overførselshastighed.

*ReadWrite*  
Som standard har OS diskene ReadWrite cachelagring aktiveret. Vi har tilføjet understøttelse af ReadWrite cachelagring af data samt diske nylig. Hvis du bruger ReadWrite cachelagring, skal du have en stort metode til at skrive dataene fra cachen til fast diske. SQL Server håndterer for eksempel skriver cachelagrede data for fast lagerplads diskene af sig selv. Brug af ReadWrite cache med et program, der ikke kan håndtere vedvarer de nødvendige data, kan det medføre tab af data, hvis VM går ned.

Du kan anvende disse retningslinjer som et eksempel på SQL Server, der kører på Premium lager ved at gøre følgende

1.  Konfigurere "Skrivebeskyttet" cache på premium lagerplads diske vært datafiler.  
    en.  Hurtig læser fra cachen til nederste SQL Server-Forespørgselsestid, da datasider er meget hurtigere hentet fra cachen sammenlignet med direkte fra dataene diske.  
    b.  Fungerer læser fra cachen, betyder, at der er flere overførselshastighed fra premium datadisce. SQL Server kan bruge denne yderligere overførselshastighed mod hentning af flere datasider og andre handlinger som sikkerhedskopiering/gendannelse, batch indlæse og indekset genopbygges.  
2.  Konfigurere "Ingen" cache på premium lagerplads diske vært logfilerne.  
    en.  Logfilerne har primært Skriv tungt handlinger. De derfor ikke skaber værdi fra cachen til skrivebeskyttet.

## <a name="disk-striping"></a>Disken Striping  
Når en høj skala VM er tilsluttet med flere premium lagerplads fast diske, kan diskene være Stribet sammen for at sammenlægge deres IOP'er, båndbredde og lagerkapacitet.

I Windows, kan du bruge lagerplads mellemrum til stribe diske sammen. Du skal konfigurere én kolonne for hver disk i en gruppe. Ellers kan overordnede ydeevne på Stribet lydstyrken være mindre end forventet, på grund af ulige fordelingen af trafikken på tværs af diskene.

Vigtigt: Brug af Server Manager UI, kan du angive det samlede antal kolonner op til 8 for et Stribet drev. Når du vedhæfter mere end 8 diske, kan du bruge PowerShell til at oprette lydstyrken. Ved hjælp af PowerShell, kan du angive antallet kolonner lig med antallet af diske. For eksempel, hvis der er 16 diske i et enkelt stribe sæt; Angiv 16 kolonner i parameteren *NumberOfColumns* i *Ny VirtualDisk* PowerShell-cmdlet.


Brug værktøjet MDADM til stribe diske sammen på Linux. Se [Konfigurere softwaren RAID på Linux](../virtual-machines/virtual-machines-linux-configure-raid.md)have en detaljeret vejledning på striping diske på Linux.


*Stripestørrelse*  
En vigtige konfiguration i disk striping er stripestørrelse. Stribe størrelsen eller blokstørrelse er den mindste portion data, der kan tale om programmet på computeren på en stribet lydstyrken. Du konfigurerer stribe størrelsen afhænger af programtype og dens anmodning mønster. Hvis du vælger den forkerte stripestørrelse, kan det medføre EY justeret, som fører til forringet ydelse i dit program.

Eksempelvis hvis en anmodning om en EY genereres af dit program er større end størrelsen på disken stribe, skriver systemet lagring det på tværs af stribe enhed grænser på mere end én disk. Når det er tid til at få adgang til dataene, vil det skulle søge på tværs af mere end én stribe enheder til at fuldføre anmodningen. Akkumuleret effekten af at sådanne funktionsmåde kan føre til væsentlig ydeevne er forringet. På den anden side, kan Hvis EY anmodning størrelse er mindre end stripestørrelse, og hvis det er tilfældigt karakter, IO-anmodning tilføje på den samme disk forårsager en flaskehals og i sidste ende forværrer ydelsen EY.


Afhængigt af typen arbejdsbelastningen dit program kører, Vælg en passende stripestørrelse. Brug en mindre stribe vilkårligt lille EY-anmodninger om. Mens for store sekventielle EY Brug anmodninger om en større stribe. Find ud af stribe størrelse anbefalinger til programmet kan du skal køre på Premium lagerplads. Konfigurere stribe størrelsen på 64KB for OLTP arbejdsmængder og 256KB for lager arbejdsbelastninger, som data til SQL Server. Se [ydeevne bedste fremgangsmåder til SQL Server på Azure FOS](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) til at få mere at vide.


>**Bemærk!**  
>Du kan sammen fordeler maksimalt 32 premium lagerplads på en DS række VM og 64 premium lagerplads diske på en GS række VM.

## <a name="multi-threading"></a>Multitrådning  
Azure har udviklet Premium lagerplads platform skal være stort omfang parallelle. Derfor opnås et flertrådet program meget højere ydeevne sammenlignet med et enkelt-threaded program. Et flertrådet program opdeler sine opgaver på tværs af flere tråde og øger effektiviteten af dens udførelse af ved at benytte VM og diskplads ressourcer til maksimum.

Eksempelvis hvis dit program, der kører på en enkelt kerne VM ved hjælp af to tråde, kan CPU'EN skifte mellem de to tråde at opnå effektivitet. Mens én tråd der venter på en disk EY at fuldføre kan CPU'EN skifte til de andre tråd. To tråde kan udføre mere end en enkelt tråd ville på denne måde. Hvis VM har mere end én grundlæggende, reducerer den yderligere kører tid da hver kerne kan udføre opgaver parallelt.

Du kan muligvis ikke ændre den måde, et standardprogram implementerer enkelt tråde eller multitrådning. SQL Server er for eksempel kan håndtere multi-CPU- og multi core. SQL Server bestemmer, hvilke betingelser den udnytter en eller flere tråde for at behandle en forespørgsel. Det kan køre forespørgsler og opbygge indeks ved hjælp af multitrådning. SQL Server anvender sandsynligt flere tråde for en forespørgsel, der involverer deltagelse i store tabeller og sortering af data før vende tilbage til brugeren. En bruger kan ikke styres dog om SQL Server kører en forespørgsel ved hjælp af en enkelt tråd eller flere tråde.

Der er konfigurationsindstillinger, der kan du ændre for at påvirke dette multitrådning eller parallelle behandling af et program. For eksempel i tilfælde af SQL Server er det den maksimale grad af parallelitet konfiguration. Denne indstilling, kaldet MAXDOP, kan du konfigurere det maksimale antal processorer, SQL Server kan bruge, når parallelle behandling. Du kan konfigurere MAXDOP for individuelle forespørgsler eller indeks handlinger. Dette er nyttige, når du vil saldo ressourcer for dit system til et ydeevne kritiske program.

Lad os antage, at dit program ved hjælp af SQL Server kører en stor forespørgsel og en indeks-operation på samme tid. Lad os antage, at du gerne vil have handlingen indeks skal være flere performant sammenlignet med store forespørgslen. Du kan angive MAXDOP værdien af handlingen indeks skal være højere end værdien MAXDOP til forespørgslen i så fald. Denne måde, har SQL Server flere antal processorer, som det kan udnytte for handlingen indeks sammenlignet med antallet processorer, det kan bruge til forespørgslen store. Husk, at du ikke styre antallet tråde, der anvender SQL Server for hver handling. Du kan styre det maksimale antal processorer, der dedikeret til multitrådning.

Lær mere om [Grader af parallelitet](https://technet.microsoft.com/library/ms188611.aspx) i SQL Server. Find ud af disse indstillinger, der påvirker multitrådning i dit program og deres konfigurationer optimering af ydeevne.

## <a name="queue-depth"></a>Dybde i kø  
Kø dybde eller kø længden eller kø størrelse er antallet af ventende IO-anmodning i systemet. Værdien af dybde i kø bestemmer, hvor mange IO-handlinger dit program kan justere op, som lagerplads diske vil behandling. Påvirker det alle de tre programmet indikatorer, som beskrevet i denne artikel såsom IOP'ER, overførselshastighed og ventetid.

Kø dybde og multitrådning er tæt forbundne. Værdien dybde i kø angiver, hvor meget multitrådning kan opfyldes af programmet. Hvis kø dybden er stort, programmet kan udføre flere handlinger samtidig, med andre ord mere multitrådning. Hvis kø dybden er lille, selvom programmet er flertrådet, har det ikke nok ledig anmodninger, der er angivet til samtidig udførelse.

Typisk fra på placeringen programmer Tillad ikke muligt at ændre dybden kø fordi Hvis angive forkert det vil gøre mere skade end godt. Programmer vil angive den korrekte værdi af dybde i kø til at opnå optimal ydeevne. Men det er vigtigt at forstå begrebet, så du kan foretage fejlfinding af problemer med ydeevnen med dit program. Du kan også se virkningerne af dybde i kø ved at køre benchmarking funktioner på dit system.

Nogle programmer indeholder indstillinger for at påvirke den dybde i kø. For eksempel MAXDOP (maksimale grad af parallelitet) indstillingen i SQL Server, der er beskrevet i forrige afsnit. MAXDOP er en måde at påvirke dybde i kø og multitrådning, selvom den ikke ændrer direkte dybde i kø værdien af SQL Server.

*Høj kø dybde*  
En høj kø dybde understregninger af flere handlinger på disken. Disken ved den næste anmodning i sin kø forvejen. Disken kan derfor planlægge operationer forvejen og behandle dem i en optimal rækkefølge. Da programmet sender flere forespørgsler til disken, kan disken behandle flere parallelle IOs. I sidste ende kunne programmet opnå højere IOP'ER. Da programmet behandling af flere forespørgsler, øger det samlede gennemløb af programmet også.

Typisk et program kan opnå maksimale antal gennemløb med 8-16 + udestående IOs per tilknyttet disk. Hvis en dybde i kø er en, programmet skubber ikke nok IOs i systemet, og behandler mindre mængde i en given periode. Med andre ord mindre overførselshastighed.

For eksempel i SQL Server informerer angive værdien MAXDOP for en forespørgsel til "4" SQL Server, kan det bruge op til fire kerner til at køre forespørgslen. SQL Server bestemmer Hvad er bedste kø dybde værdi og antallet af borekerner til udførelse af forespørgsler.

*Optimal kø dybde*  
Meget høj kø dybde værdi har også dens ulemper. Hvis kø dybde værdien er for stor, forsøger programmet at drive meget høj IOP'ER. Medmindre er fast diske med tilstrækkelige klargjort IOP'ER i programmet på computeren, kan det have en negativ påvirke programmet latenstider. Følgende formel viser forholdet mellem IOP'ER, ventetid og dybde i kø.  
    ![](media/storage-premium-storage-performance/image6.png)

Du bør ikke konfigurere dybde i kø på en hvilken som helst høj værdi, men på en optimal værdi, som kan levere tilstrækkelig IOP'ER for programmet uden at påvirke latenstider. Eksempelvis hvis programmet ventetid skal være 1 millisekunder, kø dybden kræves for at opnå 5.000 IOP'ER er, QD = 5000 x 0,001 = 5.

*Kø dybde for Stribet lydstyrken*  
Vedligeholde en høj nok kø dybde for et Stribet beholdning, således, at hver disk har en spidsbelastning kø dybde enkeltvis. For eksempel kan du overveje et program, der flytter en dybde i kø af 2, og der er 4 diske i stribe. Resterende to diske bliver inaktiv og to IO-anmodning går til to diske. Derfor konfigurere kø dybde, så alle diske kan være optaget. Formlen nedenfor viser, hvordan du bestemme kø dybde Stribet enheder.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>(Throttling)  
Azure Premium lagerplads bestemmelser angivet antal IOP'ER og overførselshastighed, afhængigt af VM størrelser og diskstørrelser, du vælger. Når programmet forsøger at drive IOP'ER eller overførselshastighed over disse begrænsninger af hvad den VM eller en disk, kan håndtere, sænker Premium lagerplads den. Dette manifester i form af forringet ydeevne i programmet. Dette kan betyder højere ventetid, sænke overførselshastighed eller sænke IOP'ER. Hvis Premium lagerplads ikke begrænses, mislykkedes dit program kunne helt ved at overskride hvad sine ressourcer er i stand til at opnå. Så, for at undgå problemer med ydeevnen på grund af (throttling), klargøre altid tilstrækkelige ressourcer for dit program. Tage hensyn til, hvad vi beskrevet i de VM størrelser og Disk størrelser sektioner ovenfor. Udviklingskurver er den bedste måde at finde ud af, hvilke ressourcer, skal du være vært for dit program.

## <a name="benchmarking"></a>Udviklingskurver  
Processen med at simulere forskellige arbejdsbelastningen på dit program og måle programmet ydelse for hver arbejdsbyrde er udviklingskurver. Ved hjælp af de trin, der er beskrevet i en tidligere sektion, har du indsamlet kravene til programmet ydeevne. Ved at køre benchmarking værktøjer på FOS vært for programmet, kan du se de ydeevneniveauer, dit program kan opnå med Premium lagerplads. I dette afsnit giver vi dig eksempler på udviklingskurver en Standard DS14 VM klargjort med Azure Premium lager diske.

Vi har brugt almindelige benchmarking værktøjer Iometer og FIO, til Windows og Linux henholdsvis. Disse værktøjer opsplitte flere tråde simulere en fremstilling som arbejdsbelastningen og mål systemets ydeevne. Du kan også ved hjælp af værktøjerne til at konfigurere parametre som Bloker størrelse og kø dybde, som du normalt ikke kan ændre til et program. Det giver dig større fleksibilitet til at drive optimal ydeevne på en høj skala VM klargjort med premium diske for forskellige typer arbejdsbelastninger. Hvis du vil vide besøg mere om hvert benchmarking værktøj [Iometer](http://www.iometer.org/) og [FIO](http://freecode.com/projects/fio).

Opret en almindelig DS14 VM for at følge eksemplerne nedenfor, og vedhæft 11 Premium lagerplads diske til VM. Konfigurere 10 diske med værten for cachelagring som "Ingen" af 11 disk og fordeler dem til en disk med navnet NoCacheWrites. Konfigurere host cachelagring som "Skrivebeskyttet" på den anden disk, og Opret en disk med navnet CacheReads med denne disk. Ved hjælp af denne konfiguration, vil du kunne se den maksimale læse- og skrivetilladelser ydeevne fra en Standard DS14 VM. Detaljeret vejledning om oprettelse af en DS14 VM med premium diske, skal du gå til [Opret og brug Premium lagerplads kontoen for en virtuelt datadisk](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Opvarmning cachen*  
Disken med skrivebeskyttet host cachelagring vil kunne give højere IOP'ER end grænsen på disken. For at få denne maksimal Læs ydeevne fra cachen til host skal skal først du varme op cachen af denne disk. Dette sikrer, at den læst IOs, hvilket benchmarking værktøj vil drive på CacheReads lydstyrken faktisk rammer cachen og ikke disken direkte. Besøg cache resultatet i yderligere IOP'ER fra cachen til enkelt aktiveret disk.

>**Vigtigt:**  
>Du skal varm cachen før du kører udviklingskurver, hver gang VM genstartes.

#### <a name="iometer"></a>Iometer   
[Hente værktøjet Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) på VM.

*Testfil*  
Iometer bruger en testfil, der er gemt på enheden, hvor du skal køre benchmarking testen. Det drev læser og skriver på denne testfil måle disken IOP'ER og overførselshastighed. Iometer opretter denne testfil, hvis du ikke har angivet en. Oprette en 200GB testfil kaldet iobw.tst på CacheReads og NoCacheWrites enheder.

*Specifikationer i Access*  
Specifikationer, anmode om EY størrelse, % læse-og skriveadgang, % tilfældigt/sekventielle er konfigureret under fanen "Specifikationer i Access" i Iometer. Oprette en access-specifikation for hver af de scenarier, der er beskrevet nedenfor. Oprette access-specifikationer og "Gem" med et passende navn som – RandomWrites\_8K, RandomReads\_8K. Vælg den tilsvarende specifikation, når du kører scenariet test.

Et eksempel på access-specifikationer for maksimalt skrive IOP'ER scenarie er vist nedenfor,  
    ![](media/storage-premium-storage-performance/image8.png)

*Maksimale IOP'ER Test specifikationer*  
Brug anmodning mindre for at vise maksimale IOP'er. Bruge 8K anmodning om størrelse og oprette specifikationer for vilkårlige skriver og læser.

| Specifikation af Access | Anmode om størrelse | Tilfældige % | Læs % |
|----------------------|--------------|----------|--------|
| RandomWrites\_8K     | 8K           | 100      | 0      |
| RandomReads\_8K      | 8K           | 100      | 100    |

*Maksimale antal gennemløb Test specifikationer*  
Brug anmodning større for at vise maksimale overførselshastighed. Bruge 64 KB anmodning om størrelse og oprette specifikationer for vilkårlige skriver og læser.

| Specifikation af Access | Anmode om størrelse | Tilfældige % | Læs % |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K    | 64 KB          | 100      | 0      |
| RandomReads\_64K     | 64 KB          | 100      | 100    |

*Køre en Iometer Test*  
Udfør nedenstående trin for at varme op cache

1.  Opret to access-specifikationer med værdier, der er vist nedenfor,

  	| Navn              | Anmode om størrelse | Tilfældige % | Læs % |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1MB | 1MB          | 100      | 0      |
  	| RandomReads\_1MB  | 1MB          | 100      | 100    |

2.  Køre en Iometer test for initialisering af cache disk med følgende parametre. Bruge tre arbejdstråde til destinationen lydstyrken og en kø dybde på 128. Angiv "Kørselstid" varigheden af en test til 2hrs under fanen "Test installationen".

  	| Scenarie              | Target Volume | Navn              | Varighed |
  	|-----------------------|---------------|-------------------|----------|
  	| Initialisering Cache Disk | CacheReads    | RandomWrites\_1MB | 2hrs     |

3.  Køre en Iometer test for opvarmning cache disk med følgende parametre. Bruge tre arbejdstråde til destinationen lydstyrken og en dybde i kø 128. Angiv "Kørselstid" varigheden af en test til 2hrs under fanen "Test installationen".

  	| Scenarie           | Target Volume | Navn             | Varighed |
  	|--------------------|---------------|------------------|----------|
  	| Varm op Cache diskplads | CacheReads    | RandomReads\_1MB | 2hrs     |

Når cache diskplads varmet op, fortsætte med de test scenarier, der vises nedenfor. Brug mindst tre arbejdstråde for **hver** destinationsadresse-enhed til at køre Iometer testen. For hver arbejdstråd vælge target lydstyrken, indstille dybde i kø og vælge en af de gemte test specifikationer som vist i nedenstående tabel, til at køre scenariet tilsvarende test. Tabellen viser også forventede resultater for IOP'ER og overførselshastighed, når du kører disse test. For alle scenarier bruges en lille EY størrelse på 8KB og en høj kø dybde af 128.

| Test scenarie      | Target Volume | Navn              | Resultat       |
|--------------------|---------------|-------------------|--------------|
| Maks. Læs IOP'ER     | CacheReads    | RandomWrites\_8K  | 50.000 IOP'ER  |
| Maks. Skrive IOP'ER    | NoCacheWrites | RandomReads\_8K   | 64.000 IOP'ER  |
| Maks. Kombinerede IOP'ER | CacheReads    | RandomWrites\_8K  | 100.000 IOP'ER |
|                    | NoCacheWrites | RandomReads\_8K   |              |
| Maks. Læs MB/sek   | CacheReads    | RandomWrites\_64K | 524 MB/sek   |
| Maks. Skrive MB/sek  | NoCacheWrites | RandomReads\_64K  | 524 MB/sek   |
| Kombinerede MB/sek    | CacheReads    | RandomWrites\_64K | 1000 MB/sek  |
|                    | NoCacheWrites | RandomReads\_64K  |              |

Nedenfor finder du skærmbilleder af Iometer testresultater for kombinerede IOP'ER og overførselshastighed scenarier.

*Kombinerede læser og skriver maksimale IOP'ER*  
![](media/storage-premium-storage-performance/image9.png)

*Kombinerede læser og skriver maksimale antal gennemløb*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO er et populære værktøj til benchmark-lager på Linux FOS. Det har mulighed for at vælge andre EY størrelser, sekventielle eller tilfældige læser og skriver. Det lægge æg arbejdstråde eller processer for at udføre angivne i/o-handlinger. Du kan angive typen i/o-handlinger hver arbejdstråd skal udføre ved hjælp af jobbet filer. Vi oprettede én jobfil per scenarie, der er vist i eksemplerne nedenfor. Du kan ændre specifikationer i filerne job til benchmark-forskellige arbejdsbelastninger, som kører på Premium lagerplads. I eksemplerne bruger vi en Standard DS 14 VM kører **Ubuntu**. Brug af samme opsætning beskrevet i starten af den [Benchmarking sektion](#Benchmarking) og varmt af cachen før du kører de simuleringstest.

Før du går i gang, [hente FIO](https://github.com/axboe/fio) og installere den på din virtuelle maskine.

Køre følgende kommando for Ubuntu,

        apt-get install fio

Vi bruger fire arbejdstråde for at få en skrivning og fire arbejdstråde for fører læst handlinger på disk. Skriv medarbejdere vil kørsel trafik på "UDENCACHE" beholdning, som har 10 diske med cache indstillet til "Ingen". Læs medarbejdere vil kørsel trafik på lydstyrken "readcache", som har 1 disk med cache er indstillet til "Skrivebeskyttet".

*Maksimale Skriv IOP'ER*  
Opret filen jobbet med følgende specifikationer at få maksimale skrive IOP'ER. Navngive den "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Bemærk de Følg vigtigste ting, der er i overensstemmelse med retningslinjer for design, der er beskrevet i forrige afsnit. Disse specifikationer er vigtige for at drive maksimale IOP'ER  
-   En høj kø dybde 256.  
-   En lille del udgave af 8KB.  
-   Flere tråde udføre tilfældige skriver.

Kør følgende kommando for at starte en FIO test for 30 sekunder  

    sudo fio --runtime 30 fiowrite.ini

Mens testen kører, vil du kunne se antallet af skrive IOP'ER VM og Premium diske leverer. Som vist i eksemplet nedenfor, den maksimale Skriv IOP'ER grænsen på 50.000 IOP'ER fremvisning af DS14 VM.  
    ![](media/storage-premium-storage-performance/image11.png)

*Maksimale læst IOP'ER*  
Opret filen jobbet med følgende specifikationer at få maksimale læst IOP'ER. Navngive den "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Bemærk de Følg vigtigste ting, der er i overensstemmelse med retningslinjer for design, der er beskrevet i forrige afsnit. Disse specifikationer er vigtige for at drive maksimale IOP'ER

-   En høj kø dybde 256.  
-   En lille del udgave af 8KB.  
-   Flere tråde udføre tilfældige skriver.

Kør følgende kommando for at starte en FIO test for 30 sekunder

    sudo fio --runtime 30 fioread.ini

Mens testen kører, vil du kunne se antallet af læse IOP'ER VM og Premium diske leverer. Som vist i eksemplet nedenfor, DS14 VM fremvisning af mere end 64.000 læst IOP'ER. Dette er en kombination af disken og cache-ydeevne.  
    ![](media/storage-premium-storage-performance/image12.png)

*Maksimum læse og skrive IOP'ER*  
Oprette job-filen med følgende specifikationer for at få maksimale kombineret læse og skrive IOP'ER. Navngive den "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Bemærk de Følg vigtigste ting, der er i overensstemmelse med retningslinjer for design, der er beskrevet i forrige afsnit. Disse specifikationer er vigtige for at drive maksimale IOP'ER

-   En høj kø dybde 128.  
-   En lille del udgave af 4KB.  
-   Flere tråde udføre tilfældige læser og skriver.

Kør følgende kommando for at starte en FIO test for 30 sekunder

    sudo fio --runtime 30 fioreadwrite.ini

Mens testen kører, du vil kunne se antallet af kombinerede læse og skrive IOP'ER VM og Premium diske leverer. Som vist i eksemplet nedenfor, DS14 VM fremvisning af mere end 100.000 kombinerede læse og skrive IOP'ER. Dette er en kombination af disken og cache-ydeevne.  
    ![](media/storage-premium-storage-performance/image13.png)

*Maksimale kombinerede overførselshastighed*  
For at få det maksimale antal kombineret læse og skrive overførselshastighed ved at bruge en blok større og store kø dybde med flere tråde udføre læsning og skrivning. Du kan bruge en blokstørrelse på 64KB og dybde i kø 128.

## <a name="next-steps"></a>Næste trin  

Lær mere om Azure Premium lager:

- [Premium opbevaring: High-Performance lagerplads til Azure virtuelt arbejdsmængder](storage-premium-storage.md)  

Læs artikler på ydeevne bedste fremgangsmåder for SQL Server for SQL Server-brugere:

- [Ydeevnen bedste fremgangsmåder til SQL Server på Azure virtuelle computere](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Azure Premium lagerplads giver højeste ydeevne til SQL Server på Azure VM](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
