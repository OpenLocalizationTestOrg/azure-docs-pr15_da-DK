<properties
   pageTitle="Distribuere tabeller i SQL Data Warehouse | Microsoft Azure"
   description="Introduktion til distribution af tabeller i Azure SQL-datalager."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribuere tabeller i SQL datalager

> [AZURE.SELECTOR]
- [Oversigt][]
- [Datatyper][]
- [Distribuere][]
- [Indeks][]
- [Partition][]
- [Statistik][]
- [Midlertidige][]

SQL Data Warehouse er et stort omfang parallel behandling (MPP) distribueret databasesystem.  Ved at dividere data og behandling egenskab på tværs af flere noder, kan SQL Data Warehouse tilbyde kæmpe skalerbarhed – langt ud over en enkelt system.  Bestem, hvordan du distribuere dine data i dine SQL Data Warehouse er en af de vigtigste faktorer for at opnå optimal ydeevne.   Tasten for at opnå optimal ydeevne er minimering flytning af data og tur. tasten for at minimere flytning af data er at vælge den rigtige fordelingsstrategi.

## <a name="understanding-data-movement"></a>Forstå flytning af data

I en MPP-systemet deles data fra hver tabel på tværs af flere underliggende databaser.  De mest optimerede forespørgsler på en MPP-system kan blot passerede gennem kan udføre for de enkelte fordelte databaser med ingen interaktion mellem andre databaser.  For eksempel Antag, at du har en database med salgsdata, som indeholder to tabeller, salg og kunder.  Hvis du har en forespørgsel, der skal deltage i dit salgstabel til din kunde-tabel, og du dividere både salg og kunde tabeller op efter kundenummer, hvordan man anbringer hver kunde i en separat database, kan alle forespørgsler, deltage i salgs- og løses inden for hver database med ingen kendskab til de andre databaser.  Derimod hvis du divideres din salgsdata med rækkefølge tal og dine kundedata efter kundenummer, og klik derefter en given database har ikke de tilsvarende data for hver kunde og dermed Hvis du vil deltage i dit salgsdata til kundedata, du har brug at hente dataene for hver kunde fra de andre databaser.  I denne andet eksempel skal flytning af data udføres for at flytte kundedata til salgsdata, så de to tabeller kan sammenføjes.  

Flytning af data ikke altid godt, nogle gange er det nødvendigt at løse en forespørgsel.  Men når dette ekstra trin kan undgås, naturligt forespørgslen køres hurtigere.  Flytning af data i opstår oftest, når tabeller er joinforbundne eller sammenlægninger udføres.  Ofte du skal gøre begge dele, så når du muligvis optimere til et scenarie, som en joinforbindelse, du stadig har brug for flytning af data kan hjælpe dig med at løse for andet scenariet, som en sammenlægning.  Hemmeligheden er forståelse af som er mindre arbejde.  I de fleste tilfælde er den mest effektive måde for at reducere den flytning af data i de fleste distribuere store faktatabeller på en ofte joinforbundne kolonne.  Distribuere data på Deltag i kolonner er en meget mere almindelige metode til at reducere flytning af data end distribuere data i kolonner, der er involveret i en sammenlægning.

## <a name="select-distribution-method"></a>Vælg fordelingsmetode

I baggrunden opdeler SQL Data Warehouse dine data i 60 databaser.  Hver enkelt database kaldes en **fordeling**.  Når dataene er indlæst i de enkelte tabeller, skal SQL Data Warehouse vide, hvordan du dividere dine data på tværs af disse 60 salgsdistributioner.  

Metoden fordeling er defineret på niveauet for tabellen, og der er i øjeblikket to muligheder:

1. **Afrund robin** som distribuere data jævnt men tilfældigt.
2. **Hash distribueret** som distribuerer data baseret på krypteres værdier fra en enkelt kolonne

Som standard, når du ikke angiver en metode til distribution af data, skal tabellen distribueres ved hjælp af metoden **round robin** fordeling.  Men, når du bliver mere avancerede i implementeringen, ønsker du overveje at bruge **hash distribueret** tabeller for at minimere flytning af data, også optimerer forespørgselsydelse.

### <a name="round-robin-tables"></a>Round Robin tabeller

Ved hjælp af metoden Round Robin af distribution af data er stort set hvordan det lyder.  Når dine data er indlæst, skal sendes hver række blot til den næste fordeling.  Denne metode til at distribuere dataene fordeler altid tilfældigt dataene meget jævnt på tværs af alle fordelingerne.  Der er ingen sortering færdig under processen round robin som placerer dine data.  En round robin fordeling kaldes også en tilfældigt hash derfor.  Med en round robin-fordelt tabel er der ikke nødvendigt at forstå dataene.  Derfor skal Round Robin tabeller ofte god indlæsning destinationer.

Som standard, hvis fordeling på nogen måde er valgt, bruges metoden round robin fordeling.  Mens round robin tabeller er nem at bruge, fordi data distribueres tilfældigt overalt i systemet, betyder det, at systemet ikke kan garantere hvilke fordeling er hver række dog på.  Som et resultat skal systemet nogle gange kalde en flytningen af data nemmere at organisere dine data, før den kan løse en forespørgsel.  Dette ekstra trin langsommere dine forespørgsler.

Overvej at bruge Round Robin fordeling for tabellen i følgende situationer:

- Når komme i gang som et enkelt udgangspunkt
- Hvis der ikke er nogen indlysende tilslutter nøgle
- Hvis der ikke er velegnet kolonne for hash distribuere tabellen
- Hvis tabellen ikke deler en fælles joinnøgle med andre tabeller
- Hvis joinforbindelsen er mindre betydeligt end andre joinforbindelser i forespørgslen
- Når tabellen er en midlertidig midlertidige tabel

Begge af disse eksempler opretter en Round Robin tabel:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Mens round robin er standardtypen tabel der eksplicit i din DDL betragtes som bedste fremgangsmåde så hensigt af din tabellayout er klar til andre.

### <a name="hash-distributed-tables"></a>Hash fordelt tabeller

Ved hjælp af en **Hash distribueret** algoritme til at distribuere dine tabeller kan forbedre ydeevnen for mange scenarier ved at reducere flytning af data på Forespørgselsestid.  Hash distribueret tabeller er tabeller, som er opdelt mellem fordelt databaser ved hjælp af en hashing-algoritme på en enkelt kolonne, du vælger.  Kolonnen fordeling er, hvad bestemmer, hvordan data, der deles på fordelt databaser.  Funktionen hash bruger kolonnen fordeling skal tildeles salgsdistributioner rækker.  Hashing-algoritme og resulterende fordeling er deterministisk.  Som er den samme værdi med den samme datatype bliver altid har til samme distribution.    

I dette eksempel opretter en tabel, der er distribueret på-id:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Vælg fordeling kolonne

Når du vælger at **hash distribuere** en tabel, skal du vælge en enkelt fordeling kolonne.  Når du vælger en fordeling kolonne, er der tre overordnede faktorer, du skal overveje.  

Vælg en enkelt kolonne, der:

1. Opdateres ikke
2. Distribuere data jævnt, undgår data skævhed
3. Minimere flytning af data

### <a name="select-distribution-column-which-will-not-be-updated"></a>Vælg fordeling kolonne, ikke opdateres

Fordeling kolonner kan ikke opdateres, derfor skal du vælge en kolonne med statiske værdier.  Hvis en kolonne skal opdateres, er det normalt ikke en god fordeling kandidat.  Hvis der er en sag, hvor du skal opdatere en fordeling kolonne, kan dette kan gøres ved først at slette rækken og derefter indsætte en ny række.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Vælg fordeling kolonne, distribuere data jævnt

Da et distribueret system udfører kun så hurtigt som dens lavest fordeling, er det vigtigt at opdele arbejdet jævnt på tværs af fordelingerne for at opnå ikke-opgjorte udførelse af hele systemet.  Den måde, arbejdet, der er inddelt i et distribueret system er baseret på hvor data for hver af de findes.  Det gør det meget vigtigt at vælge den rigtige fordeling kolonne til at distribuere dataene, så hver fordeling er lig med arbejde og tager på samme tid at fuldføre del af arbejdet.  Når arbejde divideres godt overalt i systemet, fordeles dataene mellem fordelingerne.  Når data ikke fordeles jævnt, kan vi kalder denne **data vride**.  

Overvej følgende for at opdele data jævnt og undgå data skævhed, når du vælger din fordeling kolonne:

1. Vælg en kolonne, der indeholder et stort antal entydige værdier.
2. Undgå at distribuere data i kolonner med et par entydige værdier. 
3. Undgå at distribuere data i kolonner med en høj frekvens af Null-værdier.
4. Undgå at distribuere data på datokolonner.

Da hver værdi hashes til 1 af 60 salgsdistributioner, for at opnå lige fordeling vil du markere en kolonne, der er meget entydige og indeholder mere end 60 entydige værdier.  For at illustrere, bør du overveje en sag, hvor en kolonne kun har 40 entydige værdier.  Hvis denne kolonne er valgt som tasten fordeling, vil datatypen for den pågældende tabel lande på 40 salgsdistributioner højst at forlade 20 salgsdistributioner med ingen data og ingen behandling til at gøre.  Andre 40 fordelingerne ville derimod har mere arbejde at udføre, hvis dataene er jævnt sprede over 60 fordelingerne.  Dette scenario er et eksempel på data skævhed.

I MPP-systemet venter forespørgselstrin alle salgsdistributioner til at udføre deres del af arbejdet.  Hvis en fordeling er at udføre mere arbejde end de andre, derefter ressourcen af andre fordelingerne er grundlæggende spildte lige venter på den optaget fordeling.  Når arbejde, ikke fordeles jævnt på tværs af alle salgsdistributioner, kan vi kalder denne **behandling skævhed**.  Behandling skævhed medfører forespørgsler til at køre langsommere end hvis arbejdsbelastningen kan fordeles jævnt på tværs af fordelingerne.  Data skævhed føre til behandling skævhed.

Undgå at distribuere på meget null-værdi kolonne, som null-værdier vil alle lande på den samme fordeling. Distribuere på en datokolonne, kan det også medføre på behandling skævhed fordi alle data for en given dato vil lander på den samme fordeling. Hvis flere brugere udførelse af forespørgsler vil al filtrering på den samme dato, og derefter kun 1 af 60 fordelingerne skal udføre alt arbejde siden en bestemt dato kun være på en fordeling. I dette scenarie skal køre forespørgsler sandsynligt 60 gange langsommere end hvis dataene er lige spredes over alle fordelingerne. 

Når nogen velegnet kolonner findes, bør du overveje at bruger round robin som metoden fordeling.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Vælg fordeling kolonne, minimeres flytning af data

Minimere flytning af data ved at vælge den rigtige fordeling kolonne er et af de vigtigste strategier til optimering af ydeevnen for din SQL Data Warehouse.  Flytning af data i opstår oftest, når tabeller er joinforbundne eller sammenlægninger udføres.  Kolonner, der bruges i `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` og `HAVING` delsætninger alle foretage til **god** hash fordeling kandidater. 

På anden side, kolonner i den `WHERE` delsætningen gør **ikke** foretage for god hash-kolonne kandidater fordi de begrænse, hvilke fordelingerne deltage i forespørgslen, forårsager behandling vride.  Et godt eksempel på en kolonne, der kan være nærliggende at distribuere på, men ofte kan medføre denne skævhed behandling er en datokolonne.

Generelt, hvis du har to store faktatabeller ofte involveret i en joinforbindelse, får du de fleste ydeevne ved at distribuere begge tabeller på én af joinforbindelseskolonnerne.  Hvis du har en tabel, der er aldrig knyttet til en anden stor faktatabel, derefter se til kolonner, der er ofte i de `GROUP BY` delsætningen.

Der er nogle vigtige kriterier, som skal være opfyldt for at undgå flytning af data under en joinforbindelse:

1. De tabeller, der er involveret i joinforbindelsen skal være hash fordelt på **én** af de kolonner, der deltager i joinforbindelsen.
2. Datatyperne for joinforbindelseskolonnerne skal overens begge tabeller.
3. Kolonnerne, der skal være medlem med en er lig med operator.
4. Jointypen muligvis ikke en `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Fejlfinding i forbindelse med data skævhed

Når tabeldata distribueres ved hjælp af metoden hash fordeling er der mulighed for, at nogle salgsdistributioner vil skæv for at få uforholdsmæssigt flere data end andre. Mange data skævhed kan påvirke forespørgselsydelse, fordi det endelige resultat af en distribueret forespørgsel skal vente på den længst kører fordeling at afslutte. Afhængigt af graden af data skævhed du muligvis en adresse til den.

### <a name="identifying-skew"></a>Identificere skævhed

En nem måde at identificere en tabel, som skæv er at bruge `DBCC PDW_SHOWSPACEUSED`.  Dette er en meget hurtig og nem metode til at se antallet af rækker i tabellen, der er gemt i hver af de 60 fordeling af databasen.  Husk, at den mest ikke-opgjorte ydeevne, rækkerne i fordelt tabellen skal fordeles jævnt på tværs af alle fordelingerne.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Hvis du forespørger visningerne Azure SQL Data Warehouse dynamiske management (DMV) kan du udføre en mere detaljeret analyse.  Opret visning [dbo.vTableSizes][] visning ved hjælp af SQL-koden fra [Tabellen oversigt][Oversigt] artikel for at starte skal.  Når visningen er oprettet, skal du køre denne forespørgsel for at identificere, hvilke tabeller der har mere end 10% data skævhed.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Løse data skævhed

Ikke alle skævhed er nok til at gøre en løsning.  I nogle tilfælde kan ydeevnen for en tabel i nogle forespørgsler opveje skade data skævhed.  For at afgøre, hvis du skal løse data skævhed i en tabel, skal du vide som muligt om datamængder og forespørgsler i din arbejdsbyrde.   En metode til at se på gennemslagskraft skævhed er at følge trinnene i artiklen [Forespørgsel overvågning][] til at overvåge gennemslagskraft skævhed på forespørgslens ydeevne og specifikt virkning til forespørgsler, hvor lang tid tager for at fuldføre på individuelle fordelingerne.

Distribuere data er et spørgsmål til at finde den rette balance mellem minimering data skævhed og minimere flytning af data. Disse kan modsatrettede målsætninger, og nogle gange vil du beholde data skævhed for at reducere flytning af data. For eksempel når kolonnen fordeling er ofte kolonnen delt i joinforbindelser og sammenlægninger, vil du minimering flytning af data. Fordelen ved at have minimale data bevægelse kan opveje virkning for data med vride. 

Den enkle måde at løse data skævhed er at genskabe tabellen med en anden fordeling kolonne. Da der er ikke muligt at ændre fordelingen kolonne på en eksisterende tabel, den måde at ændre fordelingen af en tabel den for at oprette den igen med et [CTAS] [].  Her er to eksempler på, hvordan løse skævhed data:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Eksempel 1: Oprette igen tabellen med en ny kolonne til distribution

I dette eksempel bruges [CTAS] [] til at oprette en tabel med en anden hash fordeling kolonne igen. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Eksempel 2: Genskabe tabellen ved hjælp af round robin fordeling

I dette eksempel bruges [CTAS] [] til at oprette en tabel med round robin i stedet for en hash fordeling igen. Denne ændring vil give selv datafordeling bekostning flytning af øget data. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om design af tabeller, se [fordel][], [indeks][], [Partition][], [Datatyper][], [Statistik][] og [Midlertidige tabeller][midlertidige] artikler.

Du kan finde en oversigt over bedste fremgangsmåder, [SQL Data Warehouse bedste fremgangsmåder][].


<!--Image references-->

<!--Article references-->
[Oversigt]: ./sql-data-warehouse-tables-overview.md
[Datatyper]: ./sql-data-warehouse-tables-data-types.md
[Distribuere]: ./sql-data-warehouse-tables-distribute.md
[Indeks]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[Midlertidige]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md
[Overvågning af forespørgsel]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
