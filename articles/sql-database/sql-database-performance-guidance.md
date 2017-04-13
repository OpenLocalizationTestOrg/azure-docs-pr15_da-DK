<properties
    pageTitle="Azure SQL-Database og ydeevne for enkelt databaser | Microsoft Azure"
    description="I denne artikel kan hjælpe dig med at finde ud af, hvilke webtjenesten kan vælge til dit program. Den anbefaler metoder til at finjustere dit program tilladelse til at få mest muligt ud af Azure SQL-Database."
    services="sql-database"
    documentationCenter="na"
    authors="CarlRabeler"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/13/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-and-performance-for-single-databases"></a>Azure SQL-Database og ydeevne for enkelt databaser

Azure SQL-Database indeholder tre [service niveauer](sql-database-service-tiers.md): Basic, som Standard og Premium. Hver webtjenesten isolerer nøje de ressourcer, SQL-database kan bruge, og garanterer forudsigelige ydeevne for det pågældende tjeneste niveau. I denne artikel tilbyder vi vejledning, der kan hjælpe dig med at vælge webtjenesten for dit program. Vi diskuterer også måder, du kan finjustere dit program tilladelse til at få mest muligt ud af Azure SQL-Database.

>[AZURE.NOTE] I denne artikel fokuserer på ydeevne vejledning til enkelt databaser i Azure SQL-Database. Du kan finde ydeevne vejledning, der vedrører elastiske database grupper, [pris og ydeevne overvejelser i forbindelse med elastiske database grupper](sql-database-elastic-pool-guidance.md). Bemærk, at dog, du kan anvende mange af de justering anbefalinger i denne artikel for databaser i en database elastiske puljen og få samme fordele i ydeevne.

Dette er de tre service lag til Azure SQL-Database, som du kan vælge mellem (performance måles i databasen overførselshastighed enheder eller [DTUs](sql-database-what-is-a-dtu.md):

- **Grundlæggende**. Grundlæggende tjeneste niveau tilbud god ydeevne forudsigelighed for hver database, time over time. I en grundlæggende database understøtter tilstrækkelige ressourcer god ydeevne i en lille database, der ikke har flere samtidige anmodninger.
- **Standard**. Standard service niveau indeholder forbedret ydeevne forudsigelighed og hæver niveauet for databaser, der har flere samtidige anmodninger, som arbejdsgruppe og web-programmer. Når du vælger en Standard service niveau database, du kan tilpasse størrelsen af dit databaseprogram, der er baseret på mere forudsigelige ydeevne, minut over minut.
- **Premium**. Webtjenesten Premium giver mere forudsigelige ydeevne, anden over, for hver Premium-database. Når du vælger webtjenesten Premium, kan du tilpasse størrelsen af din databaseprogram, der er baseret på Spidsbelastning for den pågældende database. Planen fjerner tilfælde, i hvilken ydeevne variansen kan medføre small forespørgsler til at tage længere tid end forventet i ventetid-følsomme handlinger. Denne model kan gør det meget nemmere udvikling og produkt validering skifter til programmer, der skal træffe stærke udtalelser om spidsbelastning ressourcebehov, ydeevne variansen eller forespørgselsventetid.

Hver tjeneste lag, kan du angive niveauet ydeevne, så du har mulighed for at betale kun for den kapacitet, du har brug for. Du kan [justere kapacitet](sql-database-scale-up.md), op eller ned, som arbejdsbelastningen ændringer. Hvis databasens arbejdsbelastning er høj under back-skole i jul, kan du øge niveauet af ydeevnen for databasen for et bestemt tidspunkt, juli gennem September. Du kan reducere den, når din spidsbelastning season stopper. Du kan minimere hvad du betaler med optimering af dit skyen miljø til sæsonudsvingene om din virksomhed. Denne model fungerer også godt til software produkt release skifter. En test team kan tildele kapacitet, mens det teste kører, og slip kapaciteten, når de er færdig med at teste. I en kapacitet anmodning model, er du betaler for kapacitet som du ønsker det, og undgå at bruge på dedikeret ressourcer, som du kan bruge sjældent.

## <a name="why-service-tiers"></a>Hvorfor service niveauer?

Selvom hver database arbejdsbelastningen kan variere, er formålet med tjenesten niveauer at skabe forudsigelige forhold ydeevne på forskellige ydeevneniveauer. Kunder med store database ressourcekrav kan arbejde i et mere dedikeret-miljø.

### <a name="common-service-tier-use-cases"></a>Almindelige webtjenesten use cases

#### <a name="basic"></a>Grundlæggende

- **Du er lige gået i gang med Azure SQL-Database**. Programmer, der er under udvikling ofte behøver høj ydeevne. Grundlæggende databaser er en ideel miljø til databaseudvikling af, på et sted i lav pris.
- **Du har en database med en enkelt bruger**. Programmer, der typisk knytte en enkelt bruger til en database har ikke høj krav om på dokumentsammenfald og ydeevne. Disse programmer er kandidater til de grundlæggende webtjenesten.

#### <a name="standard"></a>Standard

- **Databasen har flere samtidige anmodninger**. Programmer, der skal kunne mere end én bruger ad gangen normalt skal højere niveauer af ydeevne. For eksempel er websteder, der kommer moderat trafik eller afdelings-programmer, der kræver flere ressourcer gode kandidater til Standard service niveau.

#### <a name="premium"></a>Premium

De fleste Premium service niveau Brug tilfælde har en eller flere af følgende egenskaber:

- **Høj spidsbelastning indlæse**. Et program, der kræver en masse CPU, hukommelse eller input/output (I/O) for at fuldføre dens handlinger kræver et dedikeret høj ydeevneniveau. En databasehandling, der er blevet konstateret, at bruge flere CPU-kerner i længere tid er for eksempel en kandidat til Premium-webtjenesten.
- **Mange samtidige anmodninger**. Visse databaseprogrammer serviceanmodninger mange samtidige, f.eks, når der tjener et websted, der har en stor trafik lydstyrken. Grundlæggende og standardversionen service niveauer begrænse antallet af samtidige anmodninger per database. Programmer, der kræver flere forbindelser har brug at vælge en relevant reservation størrelse til at håndtere det maksimale antal nødvendige anmodninger.
- **Lav ventetid**. Nogle programmer, der har brug for at garantere et svar fra databasen minimale tidspunkt. Hvis en bestemt lagret procedure kaldes som en del af handlingen bredere kunde, kan det være nødvendigt et krav om har en afkastet pågældende opkald i højst 20 millisekunder, 99 procent af tiden. Denne type af programmet fordele fra webtjenesten Premium at sikre, at der findes en påkrævet databehandling potens.

Niveauet for tjenesten, som du har brug for SQL-database afhænger af spidsbelastning Indlæs kravene til hver ressource dimension. Nogle programmer bruger en trivial mængden af en enkelt ressource, men har betydeligt krav til andre ressourcer.

## <a name="service-tier-capabilities-and-limits"></a>Tjenesten niveau funktioner og begrænsninger
Hver tjeneste niveau og ydeevne niveau er knyttet til forskellige begrænsninger og ydeevne egenskaber. I denne tabel beskrives disse egenskaber for en enkelt database.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

I næste afsnit indeholder flere oplysninger om, hvordan du får vist brug, der er relateret til disse begrænsninger.

### <a name="maximum-in-memory-oltp-storage"></a>Maksimale i hukommelsen OLTP lagerplads

Du kan bruge visningen **sys.dm_db_resource_stats** til at overvåge Azure i hukommelsen lagerplads brug. Du kan finde flere oplysninger om overvågning, [skærm i hukommelsen OLTP lagerplads](sql-database-in-memory-oltp-monitoring.md).

>[AZURE.NOTE] Azure i hukommelsen online transaction processing (OLTP) preview understøttes i øjeblikket kun for enkelt databaser. Du kan ikke bruge det i databaser i elastiske database grupper.

### <a name="maximum-concurrent-requests"></a>Maksimale samtidige anmodninger

For at se antallet af samtidige anmodninger skal du køre denne Transact-SQL-forespørgsel på din SQL-database:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

For at analysere arbejdsbelastningen for en lokal SQL Server-database, skal du redigere denne forespørgsel til at filtrere på bestemte databasen, du vil analysere. Hvis du har en lokal database med navnet mindatabase, returnerer denne Transact-SQL-forespørgsel for eksempel antallet af samtidige anmodninger i den pågældende database:

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Dette er et øjebliksbillede på et bestemt tidspunkt. For at få en bedre forståelse af din arbejdsbyrde og samtidige anmodning om krav, skal du indsamle mange prøver over tid.

### <a name="maximum-concurrent-logins"></a>Maksimale samtidige logon

Du kan analysere dit bruger- og mønstre for at få en ide om hyppigheden for logon. Du kan også køre reale indlæses i et testmiljø for at sikre dig, at du ikke nå dette eller andre begrænsninger, der gennemgås i denne artikel. Der er ikke en enkelt forespørgsel eller dynamisk management visning (DMV), kan du se samtidige login tæller eller historik.

Hvis flere klienter bruger den samme forbindelsesstreng, godkender tjenesten hver logon. Hvis 10 brugere oprette samtidigt forbindelse til en database ved hjælp af det samme brugernavn og adgangskode, vil der være 10 samtidige logon. Denne begrænsning gælder kun for varigheden af logon og godkendelse. Hvis de samme 10 brugere oprette sekventielt forbindelse til databasen, vil antallet samtidige logon aldrig være større end 1.

>[AZURE.NOTE] I øjeblikket gælder denne grænse ikke for databaser i elastiske databasen grupper.

### <a name="maximum-sessions"></a>Maksimalt antal sessioner

For at se antallet af aktuelle aktive sessioner skal du køre denne Transact-SQL-forespørgsel på din SQL-database:

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Hvis du analysere arbejdsbelastningen en lokal SQL Server kan du ændre forespørgslen til at fokusere på en bestemt database. Denne forespørgsel kan du bestemme mulige session behov for databasen, hvis du overvejer at flytte den til Azure SQL-Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Disse forespørgsler returnere igen, en punkt-in-time optælling. Hvis du har indsamlet flere eksempler over tid, har du den bedste forståelse af din session bruge.

Analysen af SQL-Database, kan du få historiske statistik på sessioner. Forespørgsel **sys.resource_stats**, og brug kolonnen **active_session_count** . Se næste afsnit kan finde flere oplysninger om at bruge denne visning.

## <a name="monitor-resource-use"></a>Overvåge brugen af ressourcer
To visninger, som kan hjælpe dig med at overvåge brugen af ressourcer til en SQL-database i forhold til dens webtjenesten:

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Du kan bruge visningen [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) i hver SQL-database. Visningen **sys.dm_db_resource_stats** viser seneste ressource Brug data i forhold til webtjenesten. Gennemsnitlig procent for CPU, data I/O, log skriver og hukommelse registreres hver 15 sekunder og vedligeholdes for 1 time.

Da denne visning indeholder en mere detaljeret Kig på brugen af ressourcer, kan du bruge **sys.dm_db_resource_stats** første til en hvilken som helst aktuel tilstand analyse eller fejlfinding. Denne forespørgsel vises eksempelvis brugen af den gennemsnitlige og maksimale ressourcer for den aktuelle database over den seneste time:

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Se eksemplerne i [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)for andre forespørgsler.

### <a name="sysresourcestats"></a>sys.resource_stats

Visningen [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) i **master** databasen har flere oplysninger, der kan hjælpe dig med at overvåge ydeevnen for SQL-database på dens bestemte service niveau og ydeevne niveau. Data, der indsamles hver 5 minutter og vedligeholdes for cirka 35 dage. Denne visning er nyttig langsigtede historiske analysen af hvordan ressourcer, der bruges i SQL-database.

Følgende grafen viser CPU ressource brug for en Premium-database med niveauet P2 ydeevne for hver time i ugen. Dette diagram starter på en mandag, viser 5 arbejdsdage og derefter viser en weekend, når meget mindre sker der i programmet.

![SQL-database ressource brug](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

Fra dataene, har denne database i øjeblikket en spidsbelastning CPU-belastning på lige over 50 procent CPU-brug i forhold til niveauet P2 ydeevne (middag tirsdag). Hvis CPU er den dominerende faktor i programmets ressource profil, og derefter kan du beslutter, P2 er niveauet højre ydeevne for at sikre, at arbejdsbelastningen altid passer. Hvis du forventer et program til vokse over tid, er det en god ide at have en ekstra ressource bufferen, så programmet ikke nogensinde når grænsen for ydeevnen niveau. Hvis du øger niveauet for ydeevnen, kan du undgå kunde-synlige fejl, der kan opstå, når en database ikke har strøm nok til at behandle anmodninger effektivt, især i ventetid-følsomme miljøer. Et eksempel er en database, der understøtter et program, der tegner websider, der er baseret på resultaterne af funktionskald.

Bemærk, at andre typer af programmet kan fortolke samme grafen anderledes. Eksempelvis hvis et program forsøger at behandle løndata hver dag og har det samme diagram, denne slags "kørslen" model kan du gøre fint på et P1 ydeevneniveau. Niveauet P1 ydeevne har 100 DTUs sammenlignet med 200 DTUs på niveauet for P2 ydeevne. P1 ydeevneniveau indeholder halvdelen ydeevnen for niveauet P2 ydeevne. Så 50 procent af CPU-brug i P2 er lig med 100 procent CPU-brug i P1. Hvis programmet ikke har timeout, kan det ikke noget, om et job tager to timer eller 2,5 timer at afslutte, hvis den bliver gjort i dag. Et program i denne kategori kan sandsynligvis bruge P1 ydeevneniveau. Du kan udnytte fakultet, at der er tid den dag, når brugen af ressourcer er lavere, så en hvilken som helst "stor spidsbelastning" kan spilder ind i troughs senere på dagen. Niveauet P1 ydeevnen kan være god for type af programmet (og Gem penge), så længe job kan afsluttes tid hver dag.

Azure SQL-Database viser consumed ressourceoplysninger for hver aktive database i visningen **sys.resource_stats** af **master** databasen i hver enkelt server. Dataene i tabellen sammenlægges for 5 minutters intervaller. Dataene kan tage mere end fem minutter skal vises i tabellen, så disse data er mere praktisk for historiske analyse i stedet for i nærheden af-realtid analyse med de grundlæggende, Standard og Premium service lag. Forespørge **sys.resource_stats** visningen kan se den seneste historik til en database og kontrollere, om den reservation, du vælger leveret ydeevnen du ønsker, når det er nødvendigt.

>[AZURE.NOTE] Du skal have forbindelse til den **overordnede** database af din logiske SQL-databaseserver at forespørge på **sys.resource_stats** i eksemplerne nedenfor.

I dette eksempel vises, hvordan dataene i denne visning er vises:

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Visningen sys.resource_stats katalog](./media/sql-database-performance-guidance/sys_resource_stats.png)

Det næste eksempel viser forskellige måder, du kan bruge visningen **sys.resource_stats** katalog til at få oplysninger om, hvordan din SQL-database anvender ressourcer:

1. Se på den seneste uge ressource bruges til databasen userdb1, kan du køre denne forespørgsel:

        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;

2. For at evaluere, hvor godt arbejdsbelastningen passer niveauet ydeevne, du vil analysere ned i alle aspekter af ressource målepunkter: CPU, læser, skriver, antal medarbejdere og antallet af sessioner. Her er en ændret forespørgsel, der bruger **sys.resource_stats** til at rapportere de gennemsnitlige og maksimale værdier for denne ressource statistik:

        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

3. Disse oplysninger om de gennemsnitlige og maksimale værdier for hver ressource metrikværdi, kan du vurdere, hvor godt arbejdsbelastningen passer ind i niveauet ydeevne, du vælger. Normalt giver gennemsnitlige værdier fra **sys.resource_stats** dig en god oprindelig plan bruge mod målet størrelse. Det skal være din primære mål stick. Et eksempel, kan du bruge Standard webtjenesten med S2 ydeevneniveau. Gennemsnitlige Brug procent for CPU- og i/o-læser og skriver er under 40%, er det gennemsnitlige antal medarbejdere under 50, og det gennemsnitlige antal sessioner er under 200. Arbejdsbelastningen måske passer ind i niveauet S1 ydeevne. Det er nemt at se, om din database, der passer ind i arbejder og session grænserne. For at se, om en database, der passer ind i et lavere ydeevneniveau for i forbindelse med CPU, læser og skriver, dividere DTU antallet af det lavere ydeevneniveau ved DTU antallet af din aktuelle ydeevneniveau og multiplicerer resultatet med 100:

    * *S1 DTU / S2 DTU* 100 = 20 / 50* 100 = 40 **

    Resultatet er relativ ydeevne forskellen mellem de to ydeevneniveauer i procent. Hvis din ressource Brug ikke overskrider dette beløb, kan din arbejdsbyrde passer ind i det lavere ydeevneniveau. Men, skal du se på alle områder af ressource brug værdier, og bestemme med procentdel, hvor ofte databasens arbejdsbelastning ville passer ind i det lavere ydeevneniveau. Følgende forespørgsel skriver Tilpas procentdelen ressource dimension, hvis baseret på grænseværdi for 40%, vi beregnes i dette eksempel:

        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Baseret på din database service niveau målsætning (Langsomme), kan du beslutte, om din arbejdsbyrde passer ind i det lavere ydeevneniveau. Hvis databasens arbejdsbelastning Langsomme er 99,9%, og den foregående forespørgsel returnerer værdier større end 99,9 procent for alle tre ressource dimensioner, vil sandsynligvis arbejdsbyrde passe ind i det lavere ydeevneniveau.

    Studere Tilpas procentdelen giver dig også indsigt i, om du har til at flytte til næste ydeevneniveau til at opfylde dine Langsomme. For eksempel viser userdb1 følgende CPU-brug for den seneste uge:

  	| Gennemsnitlig CPU procent | Maks CPU. |
  	|---|---|
  	| 24,5 | 100,00 |

    Den gennemsnitlige CPU er omkring en fjerdedel af grænsen på niveauet for ydeevnen, som vil passer ind i databasen præstationsniveau. Men den maksimale værdi vises, når databasen grænsen for niveauet for ydeevnen. Har du brug at flytte til næste ydeevneniveau? Du skal se, hvordan man mange gange din arbejdsbelastningen når 100 procent, og Sammenlign det derefter til din database arbejdsbyrde Langsomme.

        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent’
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());

    Hvis denne forespørgsel returnerer en værdi mindre end 99,9 procent på grund af de tre ressource dimensioner overveje at enten flytte til næste ydeevneniveau eller bruge justering af programmet teknikker til at reducere belastningen på SQL-databasen.

4. Denne øvelse finder også øge din planlagte arbejdsmængde på i fremtiden.

## <a name="tune-your-application"></a>Finjustere dit program

I traditionelle lokalt SQL Server, er processen med at planlægge indledende kapacitet ofte adskilt fra processen med at køre et program i fremstilling. Hardware og produkt licenser købes først, og justering af ydeevnen er udført bagefter. Når du bruger Azure SQL-Database, er det en god ide at interweave processen med at køre et program og justering af den. Du kan finjustere dit program tilladelse til at bruge de nødvendige nu i stedet for overprovisioning på hardware, der er baseret på gæt af fremtidig vækstplaner for et program, som ofte er forkerte mindste ressourcer med modellen for betaling for kapacitet efter behov. Nogle kunder kan vælge ikke at finjustere et program, og i stedet vælge at overprovision hardwareressourcer. Denne metode kan være en god ide, hvis du ikke vil ændre et vigtige program i en optaget periode. Men justering af et program kan minimere ressourcekrav og nederste månedlige regninger når du bruger tjenesten niveauer i Azure SQL-Database.

### <a name="application-characteristics"></a>Programmet egenskaber

Selvom Azure SQL-Database service niveauer er designet til at forbedre ydeevnen stabilitet og forudsigeligheden for et program, kan nogle af de bedste fremgangsmåder hjælpe dig med at finjustere dit program til bedre at drage fordel af ressourcerne, der på et ydeevneniveau for. Selvom mange programmer har betydeligt ydeevne blot ved at skifte til et højere ydeevneniveau for eller webtjenesten, nogle programmer skal yderligere tilpasning for at få glæde af et højere niveau-tjenesten. Forbedret ydeevne, kan du overveje yderligere programmet justering for programmer, der har disse egenskaber:

- **Programmer, der har langsom ydeevne på grund af "chatty" funktionsmåde**. Chatty programmer gøre mange data access handlinger, der er følsomme til netværksventetid. Du kan være nødvendigt at ændre disse typer af programmer for at reducere antallet af handlinger med access data til SQL-database. For eksempel kan du forbedre programmets ydeevne ved hjælp af teknikker som samling ad hoc-forespørgsler eller flytte forespørgsler til lagrede procedurer. Få mere at vide under [batchen forespørgsler](#batch-queries).
- **Databaser med en intensivt arbejdsbyrde, der ikke understøttes af en hel enkelt computer**. Databaser, der overskrider ressourcer til det højeste Premium ydeevnen kan få glæde af skalering af arbejdsbelastningen. Se [sharding i tværs af databaser](#cross-database-sharding) og [funktionelle partitionering](#functional-partitioning)kan finde flere oplysninger.
- **Programmer, der har ikke-optimal forespørgsler**. Programmer, især i data access lag, der har dårligt indstillet forespørgsler kan ikke få glæde af et højere ydeevneniveau. Dette omfatter forespørgsler, der mangler en WHERE-delsætning, have manglende indeks eller har forældede statistik. Disse programmer drage fordel af standard forespørgsel justering af ydeevnen teknikker. Se [manglende indeks](#missing-indexes) og [forespørgsel justering og vink](#query-tuning-and-hinting)kan finde flere oplysninger.
- **Programmer, der indeholder ikke-optimal data adgang design**. Programmer, der har naturlige data på dokumentsammenfald adgangsproblemer, for eksempel deadlocking, kan ikke drage fordel af et højere ydeevneniveau. Overvej at reducere Runder mod SQL Azure-databasen ved at cachelagrede data på klientsiden med Azure cachelagring tjenesten eller en anden cachelagring teknologi. Se [cachelagring af programmet niveau](#application-tier-caching).

## <a name="tuning-techniques"></a>Justering af teknikker
I dette afsnit skal se vi på nogle teknikker, som du kan bruge til at finjustere Azure SQL-Database for at få den bedste ydeevne for dit program og køre på niveauet for laveste mulige ydeevne. Nogle af disse metoder svarer til traditionel SQL Server justering bedste fremgangsmåder, men andre er specifikke for Azure SQL-Database. I nogle tilfælde kan du undersøge de anvendte ressourcer til en database til at finde områder til yderligere at finjustere og udvide traditionelle SQL Server-teknikker til at arbejde i Azure SQL-Database.

### <a name="azure-portal-tools"></a>Azure portalen værktøjer
Du finder to værktøjer i portalen Azure, der kan hjælpe dig med at analysere og løse problemer med ydeevnen med SQL-database:

- [Forespørgsel ydeevne indsigt](sql-database-query-performance.md)
- [SQL-Database Advisor](sql-database-advisor.md)

Portalen Azure indeholder flere oplysninger om begge af disse værktøjer og hvordan du bruger dem. Hvis du vil effektivt diagnosticere og løse problemer, anbefaler vi, at du først forsøger værktøjerne i portalen Azure. Vi anbefaler, at du bruger manuel tilpasning fremgangsmåder, der beskriver vi derefter efter manglende indeks og justering af forespørgsel, i særlige tilfælde.

### <a name="missing-indexes"></a>Manglende indeks
Et almindeligt problem i OLTP databasens ydeevne relaterer til de fysiske databasedesign. Ofte, er ved hjælp af databaseskemaer udviklet og leveret uden test skaleres (enten indlæsning eller i data lydstyrke). Desværre kan kan ydeevnen for en forespørgselsplan være acceptable på en lille skala men forringe væsentligt under fremstilling niveau datamængder. De mest almindelige kilden til dette problem er manglen relevante indeks at tilfredsstille filtre eller andre begrænsninger i en forespørgsel. Manglende indeks manifester som en tabel scanner ofte, når en indeks-seek kunne tilstrækkeligt.

I dette eksempel bruger den valgte forespørgselsplan en scanning, når en seek tilstrækkeligt:

    DROP TABLE dbo.missingindex;
    CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
    SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;

![En forespørgselsplan med manglende indeks](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL-Database kan hjælpe dig, find og løs almindelige mangler indeksere betingelser. DMVs, der er indbygget i Azure SQL-Database Kig på forespørgsel samlinger, ville et indeks betydeligt reducere de estimerede omkostninger for at køre en forespørgsel. SQL-Database registrerer under udførelse af forespørgsler, hvor ofte hver forespørgselsplan udføres, og registrerer anslåede mellemrummet mellem udførelsen forespørgselsplanen og den imaginært, hvor indekset fandtes. Du kan bruge disse DMVs for at gætte hurtigt, hvilke ændringer af databasedesignet fysisk kan forbedre overordnede arbejdsbelastningen omkostninger for en database og dens reelle arbejdsbyrde.

Du kan bruge denne forespørgsel til at evaluere potentielle manglende indeks:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

I dette eksempel udløste forespørgslen denne forslag:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Når den er oprettet, henter den samme SELECT-sætning en anden plan, som bruger en seek i stedet for en søgning, og derefter udfører planen for mere effektivt:

![En forespørgselsplan med rettede indeks](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

De vigtigste indsigt er, et delt, vare system i/o-kapacitet er mere begrænset end en dedikeret servercomputeren. Der er en premium på minimere unødvendige I/O for at bedre at udnytte af systemet i DTU af hver præstationsniveau Azure SQL-Database service niveauer. Relevante fysiske databasedesign valgmuligheder kan forbedre betydeligt ventetid for forespørgsler i individuelle, forbedre overførselshastighed af samtidige anmodninger håndteres per enhed for tidsskala og minimere de omkostninger, der kræves for at tilfredsstille forespørgslen. Du kan finde flere oplysninger om manglende indekset DMVs, [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Justering af forespørgsel og vink
Query optimizer i Azure SQL-Database, der ligner traditionelle SQL Server-forespørgsel optimering. De fleste af de bedste fremgangsmåder til justering af forespørgsler og forstå begrundelsen model begrænsninger for query optimizer gælder også for Azure SQL-Database. Hvis du finjustere forespørgsler i Azure SQL-Database, kan du få ekstra fordel ved at reducere Sammenlæg ressource krav. Dit program muligvis til at køre med lavere omkostninger end en untuned tilsvarende, fordi den kan køre på et lavere ydeevneniveau.

Et eksempel, der er fælles for SQL Server, og som gælder også for Azure SQL-Database er hvordan query optimizer "sniffs" parametre. Under kompilering evaluerer query optimizer den aktuelle værdi af en parameter for at afgøre, om den kan genererer en mere optimal forespørgselsplan. Selvom denne strategi kan ofte fører til en forespørgselsplan, der er væsentligt hurtigere end en plan, der er kompileret uden kendte parameterværdier, det virker aktuelt trykfarverne begge i SQL Server og i Azure SQL-Database. Nogle gange sniffed parameteren ikke, og nogle gange parameteren er sniffed, men den oprettede plan er ikke-optimal for det samlede sæt af parameterværdier i en arbejdsbyrde. Microsoft indeholder forespørgsel tip (direktiver), så du kan angive formålet mere bevidst og tilsidesætte standardfunktionsmåden for parameter sniffing. Hvis du bruger tip, kan du ofte løse tilfælde, hvor standardfunktionsmåden SQL Server eller Azure SQL-Database er ufuldstændig for en bestemt kunde arbejdsbyrde.

Det næste eksempel viser, hvordan forespørgsel processor kan oprette en plan, der er ikke-optimal både til ydeevne og ressourcekrav. I dette eksempel vises også, hvis du bruger en forespørgsel tip, kan du reducere forespørgsel kører kravene til tid og ressourcer for SQL-databasen:

    DROP TABLE psptest1;
    CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));

    DECLARE @a int = 0;
    SET NOCOUNT ON;
    BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO psptest1(col2) values (1);
        INSERT INTO psptest1(col2) values (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION
    CREATE INDEX i1 on psptest1(col2);
    GO

    CREATE PROCEDURE psp1 (@param1 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1
        WHERE col2 = @param1
        ORDER BY col2;
    END
    GO

    CREATE PROCEDURE psp2 (@param2 int)
    AS
    BEGIN
        INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
        ORDER BY col2
        OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
    END
    GO

    CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
    GO

Konfiguration af koden opretter en tabel, der er forskudt datafordeling. Den optimale forespørgselsplan er forskellig alt efter hvilken parameter er markeret. Desværre Rekompiler ikke den plan, cachelagring funktionsmåde altid den forespørgsel, der er baseret på den hyppigste værdi for parameteren. Det er tilfældet, muligt for en ikke-optimal plan cachelagret og bruges til mange værdier, selv når en anden plan kan være et bedre valg plan i gennemsnit. Derefter opretter forespørgselsplanen to lagrede procedurer, der er identiske, bortset fra at én har en særlig forespørgsel tip.

**Eksempel, del 1**

    -- Prime Procedure Cache with scan plan
    EXEC psp1 @param1=1;
    TRUNCATE TABLE t1;

    -- Iterate multiple times to show the performance difference
    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp1 @param1=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

**Eksempel, del 2**

(Anbefaler vi, at du venter mindst 10 minutter, før du går i gang del 2 i eksemplet, så resultatet er væsentligt forskellige i den resulterende telemetridata.)

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Hver del af dette eksempel forsøger at køre en parameteriseret insert-sætning 1.000 gange (for at oprette en tilstrækkelige belastning skal bruges som datasættet test). Når det afvikler lagrede procedurer, undersøger forespørgsel processor den værdi for parameteren, som der overføres til proceduren under den første kompilering (parameter "sniffing"). Processoren gemmer den resulterende plan og bruger den til senere starter, selvom værdien for parameteren er forskellige. Optimal plan kan ikke bruges i alle tilfælde. Du skulle vejledning optimering for at vælge en plan, som er bedre til den gennemsnitlige sag i stedet for det specifikke tilfælde fra når forespørgslen først er kompileret. I dette eksempel genererer en "scanning"-plan, som læser alle rækker for at finde hver værdi, der svarer til parameteren, den oprindelige plan:

![Forespørgsel justering ved hjælp af en scanning plan](./media/sql-database-performance-guidance/query_tuning_1.png)

Da vi udført fremgangsmåden ved hjælp af værdien 1, den resulterende plan blev optimal for værdien 1 men blev ikke-optimal for alle andre værdier i tabellen. Der er resultatet sandsynligvis ikke ville du vil, hvis du skulle vælge hver enkelt plan tilfældigt, fordi planen udfører langsommere og bruger flere ressourcer.

Hvis du kører en test med `SET STATISTICS IO` indstillet til `ON`, det logiske scanning arbejde i dette eksempel er udført i baggrunden. Du kan se, at der er 1,148 læser, der er udført af den plan (som er ineffektiv, hvis den gennemsnitlige sag til at returnere kun én række):

![Forespørgsel justering ved hjælp af en logisk scanning](./media/sql-database-performance-guidance/query_tuning_2.png)

Den anden del af eksemplet bruges en forespørgsel tip til at fortælle optimering til at bruge en bestemt værdi under kompilering processen. I dette tilfælde den tvinger forespørgsel processor at ignorere den værdi, der overføres som parameteren, og i stedet skal have `UNKNOWN`. Dette refererer til en værdi, der indeholder den gennemsnitlige hyppighed i tabellen (ignorere skævhed). Den resulterende plan er en seek-baserede plan, som er hurtigere og bruger færre ressourcer gennemsnitligt planen i del 1 i dette eksempel:

![Justering af forespørgsel ved hjælp af en forespørgsel tip](./media/sql-database-performance-guidance/query_tuning_3.png)

Du kan se effekten i tabellen **sys.resource_stats** (der er en forsinkelse fra tidspunktet for, at du udfører en test, og når dataene fylder tabellen). Til dette eksempel, del 1 udføres under tidsramme 22:25:00 og del 2 udføres på 22:35:00. Bemærk, at en tidligere tidsramme bruges flere ressourcer i det pågældende tidspunkt vindue end den senere (på grund af plan effektivitet forbedringer).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Justering af eksempelresultater forespørgsel](./media/sql-database-performance-guidance/query_tuning_4.png)

>[AZURE.NOTE] Selvom lydstyrken i dette eksempel er bevidst lille, kan der være effekten af ikke-optimal parametre omfattende, især på større databaser. Forskellen i værste fald kan være mellem sekunder for hurtig tilfælde og timer for langsom sager.

Du kan undersøge **sys.resource_stats** for at se, om ressourcen for en test, der bruger flere eller færre ressourcer end en anden test. Når du sammenligner data, du adskille tidsindstilling for test, så de ikke er i det samme 5 minutters vindue i visningen **sys.resource_stats** . Formålet med øvelsen er at minimere den samlede mængde af ressourcer, der bruges, og ikke for at minimere spidsbelastning ressourcer. Generelt reducerer optimering af en del af en kode for ventetid også Ressourceforbrug. Sørg for, at de ændringer, du foretager i et program er nødvendigt, og at ændringerne ikke forstyrre kundeoplevelsen for en person, der bruger forespørgsel tip i programmet på computeren.

Hvis en arbejdsbyrde har et sæt af gentagne forespørgsler, giver ofte det mening at registrere og validere optimality af dine plan valg, fordi det drev den mindste ressource størrelse enhed, der kræves for at være vært for databasen. Når du validerer den, undertiden igen planer, der kan hjælpe dig med at kontrollere, at de ikke har nedsat. Du kan få mere at vide om [forespørgsel tip (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Sharding i tværs-database
Da Azure SQL-Database kører på vare hardware, er kapacitetsgrænser for en enkelt database lavere end til en traditionel lokalt SQL Server-installation. Nogle kunder anvender sharding teknikker til at sprede Databasefunktioner over flere databaser, når handlingerne er ikke passet ind i begrænsningerne for en enkelt database i Azure SQL-Database. De fleste kunder, der bruger sharding teknikker i Azure SQL-Database opdele deres data på en enkelt dimension på tværs af flere databaser. I denne metode skal du forstår, at OLTP programmer ofte udfører transaktioner, der gælder for kun én række eller for en lille gruppe af rækker i skemaet.

>[AZURE.NOTE] SQL-Database indeholder nu et bibliotek til at hjælpe med at sharding. Se [Oversigt over elastiske databasen klienten bibliotek](sql-database-elastic-database-client-library.md)kan finde flere oplysninger.

Hvis en database har Kundenavn, rækkefølge og ordreoplysninger (som traditionelle eksempel Northwind-databasen, som leveres med SQL Server), kan du f.eks, opdele disse data i flere databaser ved at gruppere en kunde med relaterede rækkefølge og rækkefølge detaljerede oplysninger. Du kan sikre, at kundens data forbliver i en enkelt database. Programmet vil opdele forskellige kunder på tværs af databaser, effektivt sprede belastning på tværs af flere databaser. Kunder kan ikke kun undgå størrelsesgrænsen maksimale database med sharding, men Azure SQL-Database kan også behandle arbejdsbelastninger, som er betydeligt større end begrænsningerne de forskellige ydeevne niveauer, som hver enkelt database, der passer ind i dets DTU.

Selvom databasen sharding ikke reducere Sammenlæg ressourcekapacitet ved en løsning, er det meget effektivt med understøttende meget store løsninger, der er spredes over flere databaser. Hver database kan køre på et andet ydeevneniveau til at understøtte meget store "effektive" databaser med høj ressourcekrav.

### <a name="functional-partitioning"></a>Funktionelle partitionering
SQL Server-brugere kombinere ofte mange funktioner i en enkelt database. Hvis et program indeholder logik du administrerer lager for en butik, kan databasen for eksempel har logik, der er knyttet til lager, sporing af indkøbsordrer, lagrede procedurer og indekserede eller indtruffet visninger, der administrerer slutningen af måneden rapportering. Denne metode er det nemt at administrere databasen, handlinger som sikkerhedskopi, men det kræver også muligt at ændre størrelsen på hardware for at håndtere spidsbelastning på tværs af alle funktioner i et program.

Hvis du bruger en skala fra arkitektur i Azure SQL-Database, er det en god ide at opdele forskellige funktioner i et program i forskellige databaser. Ved hjælp af denne metode, skaleres hvert program uafhængigt af hinanden. Som et program bliver viser flere beskeder (og belastning på databasen øges), kan administratoren vælge uafhængige ydeevneniveauer for hver funktion i programmet på computeren. På grænsen, med denne arkitektur være et program større end en enkelt vare maskine kan håndtere, fordi afkrydsningsfeltet Indlæs fordeles på tværs af flere computere.

### <a name="batch-queries"></a>Batchen forespørgsler
For programmer, få adgang til data ved hjælp af store mængder, hyppige, ad hoc-forespørgsler, en omfattende svar er tidsforbrug netværk kommunikationen mellem programmet niveau og Azure SQL-Database niveau. Selvom både programmet og Azure SQL-Database er i den samme datacenter, kan netværksventetid mellem to forstørres ved et stort antal data access-handlinger. Du kan reducere netværket Runder for data access handlinger, skal du bruge indstillingen til enten batchen ad hoc-forespørgsler, eller at samle dem som lagrede procedurer. Hvis du batch ad hoc-forespørgsler, kan du sende flere forespørgsler som ét stort batch i en enkelt rejse til Azure SQL-Database. Hvis du samle ad hoc-forespørgsler i en lagret procedure, kan du opnå det samme resultat, som om du batch dem. Ved hjælp af en lagret procedure giver dig også fordelen ved at øge risikoen for cachelagring forespørgselsplaner i Azure SQL-Database, så du kan bruge den lagrede procedure igen.

Nogle programmer er skrives meget. Nogle gange kan du reducere den samlede i/o-belastning på en database ved at overveje Sådan batch skriver sammen. Dette er ofte, så enkelt som at bruge eksplicitte transaktioner i stedet for automatisk bekræftelse transaktioner i lagrede procedurer og ad hoc-navne. Du kan finde en vurdering af forskellige teknikker, du kan bruge [Batching teknikker til SQL-Database programmer i Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Eksperimentere med dine egne arbejdsbelastningen til at finde den rigtige model for samling. Sørg for at forstå, at en model kan have en smule anderledes transaktions konsistens garantier. Finde den rigtige arbejdsbyrde, der minimerer brugen af ressourcer kræver, at finde den rigtige kombination af konsistens og ydeevne kompromiser.

### <a name="application-tier-caching"></a>Cachelagring af programmet lag
Nogle databaseprogrammer har læst tunge arbejdsbelastninger. Cachelagre lag kan reducere belastning på databasen og kan potentielt reducere niveauet ydeevne, der kræves for at understøtte en database ved hjælp af Azure SQL-Database. Med [Azure Redis cachen](https://azure.microsoft.com/services/cache/), hvis du har en læst tungt arbejdsbyrde, kan du læse dataene én gang (eller måske én gang for hver programmet lag maskine, afhængigt af hvordan det er konfigureret), og derefter gemme dataene uden for din SQL-database. Dette er en måde at reducere indlæsning af database (CPU og læst I/O), men der er en effekt på transaktions konsistens, fordi de data, der læses fra cachen kan være synkroniseret med data i databasen. Selvom nogle niveau af uoverensstemmelse kan accepteres i mange programmer, gælder ikke for alle arbejdsbelastninger. Du bør forstår krav til en hvilken som helst fuldt ud, inden du implementerer en strategi for cachelagring i programmet lag.

## <a name="next-steps"></a>Næste trin

- Du kan finde flere oplysninger om tjenesten niveauer, se [Indstillinger for SQL-Database og ydeevne](sql-database-service-tiers.md)
- Finde flere oplysninger om elastiske database grupper [Hvad er en Azure elastiske database puljen?](sql-database-elastic-pool.md)
- Finde oplysninger om ydeevne og elastiske database grupper, [hvor du skal overveje en elastiske database puljen](sql-database-elastic-pool-guidance.md)
