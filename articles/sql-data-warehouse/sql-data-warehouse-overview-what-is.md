<properties
   pageTitle="Hvad er Azure SQL Data Warehouse? | Microsoft Azure"
   description="Professionel distribueret database kan behandle petabyte mængder relationelle og ikke-relationelle data. Det er den branche første skybaseret datalager med Tilpas, Formindsk, og peg i sekunder."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>Hvad er Azure SQL Data Warehouse?

Azure SQL Data Warehouse er en skybaseret skala op-database kan behandle store mængder data, både relationelle og ikke-relationelle. Bygget på vores stort omfang parallel behandling (MPP) arkitektur håndtere SQL Data Warehouse arbejdsbelastningen enterprise.

SQL datawarehouse:

- Kombinerer den relationelle SQL Server-database med Azure skyen skala op-funktioner. Du kan øge, Formindsk, Afbryd midlertidigt eller genoptage Beregn i sekunder. Du gemmer omkostninger ved skalering af CPU'EN, når det er nødvendigt, og skærer brugen top-tidspunkter.
- Bruger Azure-platformen. Det er let at udrulle, vedligeholdes problemfrit og fejl fuldt tolerant på grund af automatisk back-sig.
- Supplerer økosystemet for SQL Server. Du kan udvikle med velkendte SQL Server Transact-SQL (T-SQL) og værktøjer.

I denne artikel beskrives de vigtigste funktioner i SQL Data Warehouse.

## <a name="massively-parallel-processing-architecture"></a>Stort omfang parallel behandling arkitektur

SQL Data Warehouse er et stort omfang parallel behandling (MPP) distribueret databasesystem. Ved at dividere data og behandling egenskab på tværs af flere noder, kan SQL Data Warehouse tilbyde kæmpe skalerbarhed – langt ud over en enkelt system.  I baggrunden opslag SQL Data Warehouse dine data på tværs af mange delt lager og behandling enheder. Data, der er gemt i Premium lokalt overflødige lager og sammenkædet for at beregne noder til udførelse af forespørgsler. Med denne arkitektur tager SQL Data Warehouse en "dividere og besejre" fremgangsmåde til at køre indlæse og komplekse forespørgsler. Anmodninger om er modtaget af noden kontrolelement, optimeret og derefter overføres til Beregn noder til at udføre deres arbejde parallelt.

Ved at kombinere MPP arkitektur og Azure lagerplads egenskaber, kan SQL Data Warehouse:

- Forøg eller Formindsk lagerplads uafhængigt af Beregn.
- Forøg eller Formindsk Beregn uden at flytte data.
- Pause beregne kapacitet og holde data intakt.
- CV beregne kapacitet med et øjeblik varsel.

I følgende diagram vises arkitekturen mere detaljeret.

![SQL Data Warehouse arkitektur][1]


**Kontrolelement node:** Noden kontrolelement administrerer og optimerer forespørgsler. Det er front end, der skal arbejde sammen med alle programmer og -forbindelser. I SQL Data Warehouse noden kontrolelementet er drevet af SQL-Database, og oprette forbindelse til det ser ud og fungerer på samme måde. Under overfladen koordinater kontrol node alle flytning af data og beregning, der er nødvendige for at køre parallelle forespørgsler på dataene fordelt. Når du sender en T-SQL-forespørgsel til SQL Data Warehouse, omdanner noden kontrolelement den til separat forespørgsler, der kører på hver beregningsnode parallelt.

**Beregne noder:** Beregn knuderne fungere som power bag SQL Data Warehouse. De er SQL-databaser, der gemmer dataene og behandle din forespørgsel. Når du føjer data, distribuerer SQL Data Warehouse din Beregn noder rækker. Noderne Beregn er de medarbejdere, der kører parallelle forespørgsler på dine data. Efter behandling overfører de resultaterne tilbage til noden kontrolelement. Noden kontrolelement samler resultaterne for at afslutte forespørgslen, og returnerer det endelige resultat.

**Lagerplads:** Dine data er gemt i Azure Blob-lager. Når Beregn noder interagere med dine data, skrive og læse direkte til og fra blob-lager. Da Azure lagerplads udvider transparent og hvilket, SQL Data Warehouse kan gøre det samme. Da Beregn og lager er uafhængige, kan SQL Data Warehouse automatisk skalere lagerplads adskilt fra skalering Beregn og omvendt. Azure Blob-lager er også fuldt fejlsikrede og strømliner sikkerhedskopiering og gendannelse.

**Bevægelse datatjeneste:** Data bevægelse Service (DMS) flytter data mellem knuderne. DMS giver Beregn noder adgang til data, de skal bruge joinforbindelser og sammenlægninger. DMS er ikke en Azure service. Det er en Windows-tjeneste, der kører sammen med SQL-Database på alle noderne. Da DMS kører i baggrunden, arbejde du ikke med den direkte. Men, når du ser på forespørgselsplaner, vil du bemærke de indeholder nogle DMS handlinger, da flytning af data er nødvendig for at køre hver forespørgsel parallelt.


## <a name="optimized-for-data-warehouse-workloads"></a>Optimeret til data warehouse arbejdsmængder

MPP tilgangen hjulpet af et antal data opbevaring særlig ydeevne optimeringer, herunder:

- En distribueret forespørgsel optimering og et sæt af komplekse statistik på tværs af alle data. Brug af oplysninger på datastørrelse og fordeling, er tjenesten stand til at optimere forespørgsler ved at vurdere omkostningerne for bestemte distribueret forespørgsel handlinger.

- Avancerede algoritmer og teknikker integreret i data bevægelse processen til at flytte data mellem computing ressourcer efter behov for at udføre forespørgslen effektivt. Disse data bevægelse, der er indbygget i, og alle optimeringer til bevægelse datatjeneste sker automatisk.

- Grupperet **columnstore** indeks som standard. Ved hjælp af kolonne-baserede lagerplads, bliver SQL Data Warehouse gennemsnitligt 5 x komprimering gevinst over traditionelle rækkeorienteret lager og op til 10 x eller flere forespørgsel ydeevne. Analytics-forespørgsler, der har brug for at scanne et stort antal rækker arbejde godt ud på columnstore indeks.


## <a name="predictable-and-scalable-performance"></a>Mere forudsigelige og SVG ydeevne

SQL Data Warehouse adskiller lager og Beregn, som giver mulighed for hver skalere uafhængigt af hinanden. SQL Data Warehouse kan hurtigt og blot skalere for at tilføje yderligere Beregn ressourcer med et øjeblik varsel. Supplering dette er brugen af Azure Blob-lager. BLOB giver ikke kun stabil, replikerede lagerplads, men også infrastrukturen for ubesværet udvidelse til lave omkostninger. Ved hjælp af denne kombination af skyen skalering lager og Azure Beregn SQL Data Warehouse, kan du betale for forespørgselsydeevne og lagerplads, når du har brug for. Ændre antallet af Beregn er så enkelt som flyttes en skyder i portalen Azure til venstre eller højre, eller den kan også planlægges ved hjælp af T-SQL og PowerShell.

Sammen med muligheden for at styre mængden af Beregn uafhængigt af lagerplads fuldt ud, kan SQL Data Warehouse du fuldt Peg dit datawarehouse, hvilket betyder, at du ikke betale for Beregn, når du ikke har brug for den. Mens din lagerplads på plads, frigives alle Beregn til den overordnede gruppe af Azure, så du sparer penge. Når det er nødvendigt, skal du blot genoptage på Beregn og har dine data og beregne tilgængelig for din arbejdsbyrde.

## <a name="data-warehouse-units"></a>Data Warehouse enheder

Tildeling af ressourcer til dine SQL Data Warehouse måles i Data Warehouse enheder (DWUs). DWUs er en måling af underliggende ressourcer som CPU, hukommelse, IOP'ER, som er allokeret til din SQL Data Warehouse. Øge antallet af DWUs øges ressourcer og ydeevne. Nærmere betegnet kan at DWUs sikre, at:

- Du kan skalere dit datawarehouse nemt, uden at bekymre dig om den underliggende hardware eller software.

- Du kan forudsige forbedring af ydeevnen for en DWU niveau, før du ændrer størrelsen på dit datawarehouse.

- Den underliggende hardware og software af din forekomst kan ændre eller flytte uden at påvirke din ydeevne med arbejdsbyrde.

- Microsoft kan foretage justeringer til den underliggende arkitektur for tjenesten uden at påvirke ydeevnen for din arbejdsbyrde.

- Microsoft kan hurtigt forbedre ydeevnen i SQL Data Warehouse på en måde, der er SVG og jævnt effekter systemet.

Data Warehouse enheder giver et mål for tre præcise mål, som er nøje sammen med data opbevaring arbejdsbelastningen ydeevne. Målet er, at følgende vigtige arbejdsbelastningen målene skaleres lineært med de DWUs, du har valgt til dit datawarehouse.

**Scanning/sammenlægning:** Denne arbejdsbelastningen metrikværdi tager en standarddata opbevaring forespørgsel, der scanner et stort antal rækker og derefter udfører en kompleks sammenlægning. Dette er en i/o- og CPU-intensivt handling.

**Belastning:** Denne metrikværdi måler muligheden for at indtager data til tjenesten. Indlæse fuldføres med PolyBase indlæsning af datasættet repræsentant fra Azure Blob-lager. Denne metrikværdi er udviklet til belastning Netværks- og CPU aspekter af tjenesten.

**Opret tabel som Vælg (CTAS):** CTAS måler muligheden for at kopiere en tabel. Dette omfatter læser data fra lagerplads, distribuere på tværs af noderne i maskinen og skrive den til lager igen. Det er en CPU, EY og netværk intensivt handling.

## <a name="pause-and-scale-on-demand"></a>Afbryd midlertidigt og skalering efter behov

Når du har brug for hurtigere resultater, øge din DWUs og betale for større ydeevne. Når du har brug for mindre beregne power, Formindsk din DWUs og betaler kun for de ønskede oplysninger. Du kan synes om at ændre din DWUs i følgende scenarier:

- Når du ikke behøver at køre forespørgsler, måske i aftener eller weekender, suspendere dine forespørgsler. Afbryd Beregn ressourcerne for at undgå betaler for DWUs, når du ikke har brug for dem.

- Når dit system har lav behov, kan du overveje at reducere DWU til en mindre størrelse. Du kan stadig få adgang til dataene, men på en betydeligt penge.

- Når du udfører en handling af tunge data indlæsning eller transformation, kan du vil skalere op, så dine data findes hurtigere.

At forstå, hvad din ideel DWU værdi er, prøv skalering op og ned, og nogle forespørgsler, der kører efter indlæsning af dine data. Da skalering er hurtig, kan du prøve et antal forskellige niveauer af ydeevne i en time eller mindre.  Huske på, at SQL Data Warehouse er udviklet til at behandle store mængder data og se dens sande kapacitet til skalering, især på de større satser vi tilbyder, du skal bruge et stort datasæt som nærmer sig eller overstiger 1 TB.


## <a name="built-on-sql-server"></a>Bygget på SQL Server

SQL Data Warehouse er baseret på SQL Server relationel database engine og indeholder mange af de funktioner, du forventer, fra en enterprise datalager. Hvis du allerede kender T-SQL, er det nemt at overføre din viden til SQL Data Warehouse. Om du er avancerede eller lige gået i gang, hjælper eksempler på tværs af dokumentationen starter. Overordnede, kan du synes om den måde, vi har opbygget sprog elementerne i SQL Data Warehouse på følgende måde:

- SQL Data Warehouse bruger T-SQL-syntaks for mange handlinger. Det understøtter også en lang række traditionelle SQL konstruktioner som lagrede procedurer, brugerdefinerede funktioner, tabel partitionering, indeks og sorteringer.

- SQL Data Warehouse indeholder også en række nyere SQL Server-funktioner, herunder: grupperet **columnstore** indeks, PolyBase integration og overvågning (fuldført med truslen vurdering) data.

- Visse T-SQL sprogelementer, der er mindre almindelige til opbevaring arbejdsbelastninger data eller er nyere til SQL Server, muligvis ikke tilgængelig i øjeblikket. Du kan finde kan finde flere oplysninger i [dokumentationen til overførsel][].

Med funktionen og Transact-SQL-kompatibilitet mellem SQL Server, SQL Data Warehouse, SQL-Database og Platform analysesystem, kan du udvikle en løsning, der passer til dine data behov. Du kan beslutte, hvor du vil bevare dataene, baseret på ydeevne, sikkerhed og skala krav, og derefter overføre data efter behov mellem forskellige systemer.

## <a name="data-protection"></a>Beskytte data

SQL Data Warehouse gemmer alle data i Azure Premium lokalt overflødige lagerplads. Flere synkron kopier af dataene bevares i lokale datacenter til at garantere transparent databeskyttelse i tilfælde af oversatte mislykkede forsøg. Desuden sikkerhedskopierer SQL Data Warehouse automatisk dine aktive (ophævelse standsede) databaser med jævne mellemrum ved hjælp af Azure lagerplads Snapshots. Få mere at vide om sikkerhedskopiering og gendannelse fungerer under [Oversigt over sikkerhedskopiering og gendannelse][].

## <a name="integrated-with-microsoft-tools"></a>Integreret med Microsoft-værktøjer

SQL Data Warehouse integrerer mange af værktøjerne også den SQL Server, som brugere kan være fortrolig med. Dette omfatter:

**Traditionelle SQL Server-værktøjer:** SQL Data Warehouse er fuldt integreret med SQL Server Analysis Services, Integration Services og Reporting Services.

**Skybaserede funktioner:** SQL Data Warehouse kan bruges sammen med en række nye værktøjer i Azure, herunder Data Factory, Stream Analytics, maskine læring og Power BI. Se [Oversigt over integrerede værktøjer][]til en mere komplet liste.

**Tredjepartsværktøjer:** Et stort antal værktøj fra tredjepart til udbydere har certificeret integration af deres værktøjer med SQL Data Warehouse. Du kan finde en komplet liste [SQL Data Warehouse løsning partnere][].

## <a name="hybrid-data-sources-scenarios"></a>Hybrid datakilder scenarier

Brug af SQL Data Warehouse med PolyBase giver brugere hidtil ukendt mulighed for at flytte data på tværs af deres økosystem, låse muligheden for at konfigurere avancerede hybrid scenarier med ikke-relationelle og lokale datakilder.

Polybase kan du benytte dine data fra forskellige kilder ved hjælp af velkendte T-SQL-kommandoer. Polybase gør det muligt at forespørge om ikke-relationelle data opbevares i Azure Blob-lager, som hvis det er en almindelig tabel. Brug Polybase, at forespørgslen ikke-relationelle data eller for at importere ikke-relationelle data til SQL Data Warehouse.

- PolyBase bruger eksterne tabeller til at få adgang til ikke-relationelle data. Tabeldefinitioner er gemt i SQL Data Warehouse, og du kan få adgang til dem ved hjælp af SQL og værktøjer, som du ville få adgang til normal relationelle data.

- Polybase er agnostic i dets integration. Det viser de samme funktioner og funktionalitet til alle de kilder, der understøtter. De data, der læses af Polybase kan være i en række forskellige formater, herunder afgrænsede filer eller ORC filer.

- PolyBase kan bruges til at få adgang til blob-lager, der også bruges som lager til en HDInsight klynge. Dette giver dig adgang til de samme data med Relations- og ikke-relationelle værktøjer.

## <a name="sla"></a>SLA

SQL Data Warehouse tilbyder en produkt niveau serviceniveauaftale (SLA) som en del af Microsoft Online Services SERVICENIVEAUAFTALE. Få mere at vide ved at besøge [SERVICENIVEAUAFTALE for SQL Data Warehouse][]. SLA kan finde oplysninger om alle andre produkter kan du besøge [Niveau serviceaftaler] Azure-side eller hente dem på siden [Volume Licensing][] . 

## <a name="next-steps"></a>Næste trin

Nu hvor du kender lidt om SQL Data Warehouse, lære, hvordan du kan hurtigt [oprette et SQL Data Warehouse][] og [indlæse eksempeldata][]. Hvis du er ny bruger af Azure, kan du finde [Azure ordliste][] nyttige som du støder på nye terminologi. Eller se nærmere på nogle af disse andre SQL Data Warehouse ressourcer.  

- [Kundernes succeshistorier]
- [Blogge]
- [Anmode om funktioner]
- [Videoer]
- [Kunde vejledende Team blogge]
- [Oprette supportbilletter]
- [MSDN-forum]
- [Stable overløb-forum]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Oprette supportbilletter]: ./sql-data-warehouse-get-started-create-support-ticket.md
[indlæse eksempeldata]: ./sql-data-warehouse-load-sample-databases.md
[oprette et SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md
[Overførsel dokumentation]: ./sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse løsning partnere]: ./sql-data-warehouse-partner-business-intelligence.md
[Oversigt over integrerede værktøjer]: ./sql-data-warehouse-overview-integrate.md
[Oversigt over sikkerhedskopiering og gendannelse]: ./sql-data-warehouse-restore-database-overview.md
[Azure-ordliste]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Kundernes succeshistorier]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogge]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Kunde vejledende Team blogge]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Anmode om funktioner]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN-forum]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Stable overløb-forum]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videoer]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for SQL datawarehouse]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Volumenlicens]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Serviceaftaler]: https://azure.microsoft.com/en-us/support/legal/sla/
