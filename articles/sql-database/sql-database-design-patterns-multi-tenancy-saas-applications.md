<properties
   pageTitle="Designe mønstre for multiprofiler SaaS programmer og Azure SQL-Database | Microsoft Azure"
   description="I denne artikel beskrives de krav og almindelige data arkitektur mønstre af multiprofiler database programmer, der kører i et skybaseret miljø skal overveje, og der er knyttet til disse mønstre forskellige kompromiserne. Det også beskrives, hvordan Azure SQL-Database med dens elastiske grupper og elastiske værktøjer at løse disse krav på en ingen sammensat måde."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Designe mønstre for multiprofiler SaaS programmer og Azure SQL-Database

I denne artikel kan du læse om krav og fælles data arkitektur mønstre af multiprofiler software som en tjeneste (SaaS) databaseprogrammer, der kører i et skybaseret miljø. Også forklares de faktorer, du skal overveje og kompromiser af forskellige designs. Elastiske grupper og elastiske værktøjer i Azure SQL-Database kan hjælpe dig opfylder dine specifikke krav uden bringe andre målsætninger.

Nogle gange foretager udviklere valgmuligheder, der fungerer sammen med deres langsigtede bedste interesser, når de Designer arkitekturer modeller til data niveauer af multiprofiler programmer. Som udgangspunkt i det mindste kan en udvikler opfatter brugervenlighed udvikling og lavere skyen service provider omkostninger som vigtigere end lejer isolationsniveauet eller skalerbarhed af et program. Dette valg kan føre til kunde kundetilfredshed problemstillinger og dyrt kursus-rettelse senere.

En multiprofiler programmet som et hostet i et skybaseret miljø og, der indeholder det samme sæt af tjenester til hundredvis eller tusindvis af lejere, der ikke dele eller se hinandens data. Et eksempel er et SaaS program, der tilbyder tjenester til lejere i et skybaseret hostet miljø.

## <a name="multitenant-applications"></a>Multiprofiler programmer

I multiprofiler programmer, kan data og arbejdsbelastningen være nemt opdelt. Du kan dele data og arbejdsbelastningen, for eksempel langs lejer grænser, fordi de fleste anmodninger forekommer inden for begrænsninger, en lejer. Denne egenskab er forbundet med dataene og arbejdsbelastningen, og den prioriteres højere de programmet mønstre, der er beskrevet i denne artikel.

Udviklere bruge denne type program på tværs af hele spektret af skybaserede programmer, herunder:

- Partner databaseprogrammer, der er der transitioned til skyen som SaaS programmer
- SaaS programmer, der er bygget til skyen fra bunden
- Direkte, med kundekontakt programmer
- Medarbejder-adgang til virksomhedens programmer

SaaS programmer, der er udviklet til skyen, og hvilke med rødder er som partner databaseprogrammer typisk multiprofiler programmer. Programmerne SaaS leverer en særlig software programmet på computeren som en tjeneste til deres lejere. Lejere kan få adgang til tjenesten application og har fuld ejerskabet af tilknyttede data, der er gemt som en del af programmet. Men for at kunne udnytte fordelene ved SaaS, lejere skal returnere nogle kontrol over deres egne data. De har tillid til udbyderen af SaaS for at holde deres data sikre og isolerede fra andre lejere data. Eksempler på denne slags multiprofiler SaaS program er MYOB SnelStart og Salesforce.com. Hver af disse programmer kan være opdelt langs lejer grænser og understøttelse af programmet designe mønstre gennemgås i denne artikel.

Programmer, som giver en direkte tjeneste til kunder eller medarbejdere i en organisation (ofte kaldet brugere i stedet for lejere) er en anden kategori på multiprofiler programmet spektret. Kunder, abonnere på tjenesten og ejer ikke de data, udbyderen af indsamler og gemmer. Tjenesteudbydere har mindre strenge krav til at holde deres kunders data isolerede fra hinanden ud over bestemmelser government godkendt beskyttelse af personlige oplysninger. Eksempler på denne type med kundekontakt multiprofiler program er medier indholdsudbydere som Netflix, Spotify og Xbox LIVE. Andre eksempler på nemt partitionable programmer med kundekontakt, Internet-skala programmer eller Internet af ting (IoT)-programmer i hvilke hver kunde eller enheden kan fungere som en partition. Partition grænser kan adskille brugere og enheder.

Ikke alle programmer partition nemt langs en enkelt egenskab som lejer, kunde, bruger eller enhed. En kompleks virksomhedsressource planlægning (ERP)-program, for eksempel har produkter, ordrer og kunder. Det er normalt en kompleks skema med tusindvis af meget forbundne tabeller.

Ingen enkelt partition strategi kan gælder for alle tabeller og arbejde på tværs af et programs arbejdsbyrde. I denne artikel fokuserer på multiprofiler programmer, som har nemt partitionable data og arbejdsmængder.

## <a name="multitenant-application-design-trade-offs"></a>Multiprofiler programmet design kompromiser

Det design mønster, der udvikler multiprofiler programmet vælger typisk er baseret på vederlag af følgende faktorer:

-   **Lejer isolationsniveauet**. Udvikleren har brug at sikre, at ingen lejer har uønskede adgang til andre lejere data. Isolationsniveauet kravet udvides til andre egenskaber, som giver beskyttelse fra støj tilstødende netværk, der kan du gendanne en lejer data og implementere lejer-specifikke tilpasninger.
-   **Skyen ressourceomkostninger**. Et SaaS program skal være omkostninger-Konkurrentanalyse. En multiprofiler programmet udvikler kan vælge at optimere billigere i brug af skyen ressourcer, som lager og beregne omkostninger.
-   **Øget DevOps**. En udvikler multiprofiler programmet skal inkorporere isolationsniveauet beskyttelse, vedligeholde, og overvåge tilstanden for deres program og databaseskema og foretage fejlfinding af problemer med lejer. Kompleksitet i programmet udvikling og drift omsættes direkte til øgede omkostninger og udfordringer med lejer kundetilfredshed.
-   **Skalerbarhed**. Muligheden for at trinvist at tilføje flere lejere og kapacitet til lejere, der skal bruge det er nødvendigt at importhandlingen er blevet gennemført SaaS.

Hver af disse faktorer har kompromiser sammenlignet med en anden. De laveste omkostninger skyen tilbyder ikke muligvis tilbyder den mest praktiske udvikling oplevelse. Det er vigtigt for en udvikler du foretager nogen valg om disse indstillinger og deres kompromiser under designprocessen programmet på computeren.

Et populære udvikling mønster er til at pakke flere lejere i en eller flere databaser. Fordelene ved denne fremgangsmåde er lavere, fordi du betaler for nogle databaser og relativ enkel for at arbejde med et begrænset antal databaser. Men over tid, en SaaS multiprofiler programmet udvikler opdager, at dette valg har væsentlig downsides i lejer isolationsniveauet og skalerbarhed. Hvis lejeren isolationsniveauet bliver vigtige, dataserver yderligere at beskytte lejer dataene i delte lager mod uautoriseret adgang eller støj tilstødende netværk. Denne ekstra indsats kan betydeligt gavne udviklingsbestræbelser og isolationsniveauet vedligeholdelsesomkostninger. På samme måde, hvis det er nødvendigt at tilføje lejere, designmønsteret typisk kræver ekspertise til at omfordele lejer data på tværs af databaser korrekt skalere data niveauet i et program.  

Lejer isolationsniveauet ofte er et grundlæggende krav i SaaS multiprofiler programmer, der omfatter til virksomheder og organisationer. En udvikler kan være fristet ved anslåede fordele i enkle og omkostninger i forhold til lejer isolationsniveauet og skalerbarhed. Denne forholdet kan vise komplekse og dyrt som tjenesten vokser og lejer isolationsniveauet behovene bliver mere vigtige og administrerede på programlag. I multiprofiler programmer, som giver en direkte, consumer mod tjeneste til kunder, kan lejer isolationsniveauet være en lavere prioritet end optimere til skyen ressourceomkostninger.

## <a name="multitenant-data-models"></a>Multiprofiler datamodeller

Almindelige design fremgangsmåder til at placere lejer data skal du følge tre forskellige modeller, vises i figur 1.


  ![Multiprofiler programmet datamodeller](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 figur 1: almindelige design fremgangsmåder multiprofiler datamodeller

-   **Database per lejer**. Hver lejer har sin egen database. Alle lejer-specifikke data er begrænset til lejerens databasen og isolerede fra andre lejere og deres data.
-   **Delt database-delt**. Flere lejere dele i flere databaser. Et entydigt sæt lejere er tildelt hver database ved hjælp af en leverandør strategi som hash-, område eller liste partitionering. Denne data fordelingsstrategi kaldes ofte sharding.
-   **Delt database-enkelt**. En enkelt, nogle gange store database indeholder data for alle lejere, som er entydige, i kolonnen en lejer-ID.

> [AZURE.NOTE] Et programudvikler kan vælge at placere forskellige lejere i anden database ved hjælp af skemaer, og brug derefter skemanavnet på for at skelne de forskellige lejere. Vi anbefaler ikke denne metode, fordi det som regel kræver brug af dynamisk SQL, og det kan være effektivt i cachelagring af plan. I resten af denne artikel fokuserer vi på den delte tabel tilgang for denne kategori af multiprofiler program.

## <a name="popular-multitenant-data-models"></a>Populære multiprofiler datamodeller

Det er vigtigt at evaluere de forskellige typer multiprofiler datamodeller med hensyn til de programmet design kompromiser vi allerede har identificeret. Disse faktorer hjælpe dig med at beskrive de tre mest almindelige multiprofiler datamodeller beskrevet tidligere og deres database brugen som vist i figur 2.

-   **Isolationsniveauet**. Graden af isolation mellem lejere kan være et mål for, hvor meget lejer isolationsniveauet opnår en datamodel.
-   **Skyen ressourceomkostninger**. Mængden ressourcedeling mellem lejere kan optimere skyen ressourceomkostninger. En ressource kan defineres som Beregn og lager omkostninger.
-   **DevOps omkostninger**. Brugervenlighed udvikling, implementering og administration reducerer overordnede SaaS handlingen omkostninger.  

Figur 2 viser Y-aksen lejer isolationsniveauet. X-aksen vises niveauer af Deling af ressourcer. Grå diagonale pil i midten angiver retningen af DevOps omkostninger, tendens til at øge eller mindske.

![Populære multiprofiler programmet designs](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) figur 2: populære multiprofiler datamodeller

Den nederste højre kvadrant i figur 2 viser et mønster i programmet, der bruger en potentielt stor delte enkelt database, og den delte tabel (eller separat skema) tilgang. Det er god til deling, fordi alle lejere bruge de samme databaseressourcer (CPU, hukommelse, input/output) i en enkelt database. Men lejer isolationsniveauet er begrænset. Du være nødvendigt at udføre yderligere trin for at beskytte lejere fra hinanden på programlag. Disse yderligere trin kan øge DevOps omkostninger for udvikling og administration af programmet. Skalerbarhed er begrænset af skalaen på den hardware, der er vært databasen.

Den nederste venstre kvadrant i figur 2 illustrerer flere lejere, der er delt på tværs af flere databaser (typisk forskellige hardware skalere enheder). Hver database vært et undersæt af lejere, hvilke adresser skalerbarhed interesse i andre mønstre. Hvis flere kapacitet er påkrævet for flere lejere, kan du nemt markere lejere på nye databaser, der er allokeret til nye hardware skalaenheder. Dog reduceres mængden af Deling af ressourcer. Kun lejere, der er placeret på samme skalaenheder del ressourcer. Denne fremgangsmåde giver lille forbedring af for at lejer isolationsniveauet, fordi mange lejere er stadig samhusningsstedet uden at blive automatisk beskyttet mod hinandens handlinger. Programmet kompleksitet forbliver høj.

Den øverste venstre kvadrant i figur 2 er den tredje tilgang. Hver lejer data placeres i sin egen database. Denne metode er god lejer-isolationsniveauet egenskaber, men begrænser Deling af ressourcer, når hver database har sin egen dedikeret ressourcer. Denne metode er god, hvis alle lejere har forudsigelige arbejdsmængder. Hvis lejer arbejdsbelastninger bliver mindre forudsigelige, kan ikke provideren optimere Deling af ressourcer. Unpredictability er fælles for SaaS programmer. Udbyderen, der skal enten overliggende klargøring til at overholde krav eller lavere ressourcer. Enten handling resulterer i omkostninger, der er højere eller lavere lejer kundetilfredshed. En højere grad af ressourcedeling på tværs af lejere bliver bør gøre løsningen mere økonomisk. Øge antallet af databaser også øger DevOps omkostninger for at distribuere og vedligeholde programmet. Denne metode giver de bedste og nemmeste isolationsniveauet for lejere på trods af disse problemer.

Disse faktorer påvirke også design mønstret en kunde vælger:

-   **Ejerskabet af lejer data**. Et program, hvori lejere Bevar ejerskabet af deres egne data prioriteres højere anvendelse af en enkelt database per lejer.
-   **Skala**. Et program, der er beregnet til hundredvis af tusinder eller millioner af lejere prioriteres højere databasen til at dele fremgangsmåder som sharding. Isolationsniveauet krav stadig kan dog udgøre udfordringer.
-   **Værdi og business model**. Hvis et program per-lejer indtægter Hvis små (mindre end en dollar), isolationsniveauet krav blive mindre kritiske og en delt database giver mening. Hvis-lejer indtægter er et par kroner eller mere, er en database per lejer model mere praktisk anvendeligt. Det kan hjælpe med at reducere omkostningerne til udvikling af.

Hvis du har de design kompromiser vises i figur 2, skal en ideel multiprofiler model inkorporere god lejer isolationsniveauet egenskaber med optimal ressourcedeling mellem lejere. Denne model passer ind i den kategori, der er beskrevet i øverste højre kvadrant i figur 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Understøttelse af multitenancy i Azure SQL-Database

Azure SQL-Database understøtter alle multiprofiler programmet mønstre, der er beskrevet i figur 2. Understøtter også et mønster i programmet, der kombinerer god ressourcedeling med elastiske grupper og isolationsniveauet fordelene ved database-per-lejeren løse (se den øverste højre kvadrant i figur 3). Elastiske Databaseværktøjer og funktioner i SQL-Database til at reducere omkostningerne til udvikle og betjene et program, der indeholder mange databaser (vist i området nedtonet i figur 3). Disse værktøjer kan hjælpe dig med at oprette og administrere programmer, der bruger en af flere databaser mønstre.

![Mønstre i Azure SQL-Database](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) figur 3: multiprofiler programmet mønstre i Azure SQL-Database

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Database per lejer modellen med elastiske grupper og værktøjer

Elastiske grupper i SQL-Database kombinere lejer isolationsniveauet med ressourcedeling mellem lejer databaser til bedre support database per lejer tilgang. SQL-Database er en løsning til niveau for SaaS udbydere, der bygger multiprofiler programmer. Arbejde ved ressourcedeling mellem lejere forskydes fra programlag database service lag. Administrere og forespørgsler skaleres til brug på tværs af databaser kompleksitet forenklet med elastiske job, elastiske forespørgsel, elastiske transaktioner og biblioteket elastiske database klient.

| Krav til | SQL database-funktioner |
| ------------------------ | ------------------------- |
| Lejer isolationsniveauet og deling af ressourcer | [Elastiske grupper](sql-database-elastic-pool.md): allokere en samling af ressourcer, SQL-Database og dele ressourcerne på tværs af forskellige databaser. Individuelle databaser kan også tegne så mange ressourcer fra puljen efter behov for at tage højde for kapacitet demand spidser på grund af ændringer i lejer arbejdsmængder. Elastiske puljen selve kan skaleres op eller ned efter behov. Elastiske grupper giver også nem administration og overvågning og fejlfinding på niveauet for gruppen. |
| Øget DevOps på tværs af databaser | [Elastiske grupper](sql-database-elastic-pool.md): som nævnt tidligere.|
||[Elastiske forespørgsel](sql-database-elastic-query-horizontal-partitioning.md): forespørgsel på tværs af databaser til rapportering eller kryds-lejer analyse.|
||[Elastiske job](sql-database-elastic-jobs-overview.md): pakke og pålidelig implementering af database vedligeholdelsesaktiviteter eller database skemaændringer til flere databaser.|
||[Elastiske transaktioner](sql-database-elastic-transactions-overview.md): proces ændres til flere databaser i en atomisk og isolerede måde. Elastiske transaktioner er påkrævet ved programmer skal bruge "alt eller intet" garantier over flere databasehandlinger. |
||[Elastiske database klientbibliotek](sql-database-elastic-database-client-library.md): administrere data salgsdistributioner og knytte lejere til databaser. |

## <a name="shared-models"></a>Delte modeller

Som beskrevet tidligere, for de fleste SaaS udbydere, kan en delt model fremgangsmåde udgøre problemer med lejer isolationsniveauet problemer og kompleksiteterne ved med at udvikle programmer og vedligeholdelse. Dog for multiprofiler programmer, som giver en tjeneste direkte til forbrugere, lejer isolationsniveauet krav muligvis ikke med høj en prioritet som minimering omkostninger. De kan muligvis pakke lejere i en eller flere databaser med en høj tæthed at reducere omkostninger. Delt database modeller ved hjælp af en enkelt database eller flere delt databaser kan medføre yderligere effektiviteten i ressource deling og samlede omkostninger. Azure SQL-Database indeholder nogle funktioner, der hjælper kunder opbygge isolationsniveauet for øget sikkerhed og administration på skala i data lag.

| Krav til | SQL database-funktioner |
| ------------------------ | ------------------------- |
| Isolationsniveauet sikkerhedsfunktioner | [Række-sikkerhed på brugerniveau](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Databaseskema](https://msdn.microsoft.com/library/dd207005.aspx) |
| Øget DevOps på tværs af databaser | [Elastiske forespørgsel](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Elastiske job](sql-database-elastic-jobs-overview.md) |
|| [Elastiske transaktioner](sql-database-elastic-transactions-overview.md) |
|| [Elastiske database klientbibliotek](sql-database-elastic-database-client-library.md) |
|| [Elastiske database opdelt og flette](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Oversigt

Lejer isolationsniveauet krav er vigtige for de fleste SaaS multiprofiler programmer. Den bedste måde at give isolationsniveauet leans stærkt mod database per lejer tilgang. De andre to metoder kræver investeringer i komplekse programlag, der kræver kvalificerede udvikling personale til at levere isolationsniveauet, hvilket øger både omkostninger og risici. Hvis isolationsniveauet krav ikke er indgår tidligt i tjenesten udvikling, kan det være endnu mere dyrt i de første to modeller omprogrammerbar dem. De primære ulemper der er knyttet til database per lejer modellen er relateret til øget skyen ressourceomkostninger på grund af nedsat deling og vedligeholde og administrere mange databaser. Ofte stillede SaaS programmet udviklere, når de foretager disse kompromiser.

Selvom kompromiser kan være store hindringer med de fleste tjenesteudbydere i skyen-database, fjerner Azure SQL-Database hindringer med dens elastiske puljen og elastiske databasefunktioner. SaaS udviklere kan kombinere en database per lejer model isolationsniveauet karakteristika og optimere Deling af ressourcer og administration forbedringer af mange databaser ved hjælp af elastiske grupper og tilknyttede værktøjer.

Multiprofiler programmet udbydere, der har ingen lejer isolationsniveauet krav og hvem der kan pack lejere i en database på en stor tæthed kan finde, delte data-modeller resultatet i yderligere effektivitet i deling af ressourcer og reducere samlede omkostninger. Azure SQL-Database elastiske Databaseværktøjer, sharding biblioteker og sikkerhedsfunktioner hjælpe SaaS udbydere opbygge og administrere multiprofiler programmer.

## <a name="next-steps"></a>Næste trin

[Komme i gang med elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md) med en eksempel-app, der viser biblioteket klient.

Oprette et [elastiske puljen brugerdefinerede dashboard til SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) med en eksempel-app, der bruger elastiske grupper efter en løsning økonomisk SVG-database.

Bruge værktøjerne Azure SQL-Database til at [overføre eksisterende databaser skalere ud](sql-database-elastic-convert-to-use-elastic-tools.md).

Se vores selvstudium om hvordan du [opretter en elastiske puljen](sql-database-elastic-pool-create-portal.md).  

Få mere at vide om at [overvåge og administrere en elastiske puljen](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Yderligere ressourcer

- [Hvad er en Azure elastiske puljen?](sql-database-elastic-pool.md)
- [Skalering af med Azure SQL-Database](sql-database-elastic-scale-introduction.md)
- [Multiprofiler programmer med elastiske Databaseværktøjer og sikkerhed på brugerniveau række](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Godkendelse i multiprofiler apps ved hjælp af Azure Active Directory og OpenID forbinde](../guidance/guidance-multitenant-identity-authenticate.md)
- [Hold undersøgelser program](../guidance/guidance-multitenant-identity-tailspin.md)
- [Løsning Hurtig start](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Spørgsmål og anmode om funktioner

Spørgsmål, skal du finde os i [SQL-Database forum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Tilføje en funktion anmodning i [SQL-Database feedback-forummet](https://feedback.azure.com/forums/217321-sql-database/).
