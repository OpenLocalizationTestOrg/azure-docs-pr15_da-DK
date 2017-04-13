<properties
   pageTitle="Indeksering tabeller i SQL Data Warehouse | Microsoft Azure"
   description="Introduktion til tabel indeksering i Azure SQL Data Warehouse."
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
   ms.date="07/12/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="indexing-tables-in-sql-data-warehouse"></a>Indeksering tabeller i SQL datalager

> [AZURE.SELECTOR]
- [Oversigt][]
- [Datatyper][]
- [Distribuere][]
- [Indeks][]
- [Partition][]
- [Statistik][]
- [Midlertidige][]

SQL Data Warehouse indeholder flere indekseringsindstillinger, herunder [grupperet columnstore indeks][], [grupperet indeks og ikke-grupperede indeks][].  Desuden indeholder det også et indeks indstillingen ingen også kendt som [heap][].  I denne artikel beskrives fordelene ved hver Indekstype samt tip til at opnå den de fleste ydeevne ud af dit indeks. Se [oprette tabel syntaks][] for at få flere detaljer om, hvordan du opretter en tabel i SQL Data Warehouse.

## <a name="clustered-columnstore-indexes"></a>Grupperet columnstore indeks

Som standard opretter SQL Data Warehouse et grupperet columnstore indeks, når du ikke er angivet nogen indeks parametre i en tabel. Grupperet columnstore tabeller tilbyder både det højeste niveau af datakomprimering samt den bedste ydeevne for overordnede forespørgsel.  Grupperet columnstore tabeller vil generelt slå grupperet indeks eller heap tabeller og er som regel den bedste løsning til store tabeller.  Disse årsager er grupperet columnstore det bedste sted at starte, når du er usikker på, hvordan indeksere tabellen.  

For at oprette en grupperet columnstore tabel skal du blot angive GRUPPERET COLUMNSTORE indeks i delsætningen med, eller lad delsætningen med:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Der er nogle scenarier, hvor grupperet columnstore ikke kan være en god indstilling:

- Columnstore tabeller understøtter ikke sekundær ikke-grupperede indeks.  Overvej at heap eller grupperet indeks tabeller i stedet.
- Columnstore tabeller understøtter ikke nvarchar(max) nvarchar(Max),varchar(Max) og varbinary(max).  Overvej at heap eller grupperet indeks i stedet.
- Columnstore tabeller kan være mindre effektivt til midlertidige data.  Overvej at heap og eventuelt midlertidige tabeller.
- Lille tabeller med mindre end 100 millioner rækker.  Overvej heap tabeller.

## <a name="heap-tables"></a>Heap tabeller

Når du midlertidigt lander data på SQL Data Warehouse, kan du finde ved hjælp af en heap tabel vil gøre den overordnede proces hurtigere.  Dette skyldes, at indlæse til heaps er hurtigere end til indeks tabeller og i nogle tilfælde kan udføres efterfølgende læse fra cachen.  Hvis du indlæser data til kun at fase før du kører flere transformationer, vil det være meget hurtigere end indlæsning af data til en grupperet columnstore tabel indlæsning af tabellen til heap tabel. Desuden indlæses indlæsning af data til en [midlertidig tabel][midlertidige] også meget hurtigere end indlæsning af en tabel til permanent lager.  

Til small opslagstabeller, mindre end 100 millioner rækker, mening ofte heap tabeller.  Klynge columnstore tabeller begynde at opnå optimal komprimering, når der er mere end 100 millioner rækker.

Hvis du vil oprette en heap tabel, du skal blot angive HEAP i delsætningen med:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Grupperede og ikke-grupperede indeks

Grupperet indeks kan slå grupperet columnstore tabeller, når en enkelt række skal hentes hurtigt.  Forespørgsler, hvor en enkelt eller meget få række opslag er påkrævet for at ydeevne med største hastighed, kan du overveje et grupperet indeks eller grupperet sekundære indeks.  Ulempe til at bruge et grupperet indeks er, at kun forespørgsler, der bruger en meget selektiv filter på kolonnen grupperet indeks opnår.  En grupperet indeks kan føjes til andre kolonner for at forbedre filter på andre kolonner.  Dog kan hver indeks, der er føjet til en tabel føje både behandlingstid og rum at indlæse.

Hvis du vil oprette en grupperet indeks-tabel, du skal blot angive GRUPPERET indeks i delsætningen med:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Hvis du vil tilføje et ikke-grupperede indeks i en tabel, skal du blot bruge følgende syntaks:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

> [AZURE.NOTE] Et ikke-grupperede indeks oprettes som standard, når CREATE INDEX bruges. Desuden er en ikke-grupperede indeks kun tilladt i en række lagerplads tabel (HEAP eller GRUPPERET indeks). Ikke grupperet indeks oven på et GRUPPERET COLUMNSTORE indeks er ikke tilladt på nuværende tidspunkt.


## <a name="optimizing-clustered-columnstore-indexes"></a>Optimere grupperet columnstore indeks

Grupperet columnstore tabeller er organiseret i data i målgrupper.  Har du høj segmentet kvalitet er meget vigtigt for at opnå optimal forespørgselsydelse på en tabel, columnstore.  Segmentet kvaliteten kan måles med antallet af rækker i en komprimeret rækkegruppe.  Segmentet kvalitet er mest optimale, hvor der ikke er mindst 100K rækker per komprimeret række gruppere og få ydeevne som antallet af rækker i række gruppe tilgang 1.048.576 rækker, som er en rækkegruppe kan indeholde de fleste rækkerne.

Den under Vis kan oprettes og bruges på dit system til at beregne de gennemsnitlige rækker per række gruppere og identificere en hvilken som helst optimal klynge columnstore indeks.  Den sidste kolonne i denne visning genererer som SQL-sætning, som kan bruges til at genopbygge dit indeks.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,   COUNT(DISTINCT rg.[partition_number])                   AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,   CEILING ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Nu, hvor du har oprettet visningen, du Kør denne forespørgsel for at identificere tabeller med Rækkegrupper med mindre end 100K rækker.  Naturligvis, kan du øge grænseværdi på 100K, hvis du leder efter flere optimal segmentet kvalitet. 

```sql
SELECT  *
FROM    [dbo].[vColumnstoreDensity]
WHERE   COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Når du har kørt den forespørgsel, kan du begynde at se på dataene og analysere dine resultater. Denne tabel forklarer, hvad du skal søge efter i din række gruppen analyse.


| Kolonne                             | Sådan bruges disse data                                                                                                                                                                      |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table_partition_count]            | Hvis tabellen er opdelt, kan du forvente at se højere Åbn rækkegruppe tæller. Hver partition i distributionen kunne i teorien har en åben rækkegruppe, der er knyttet til den. Angive dette til din analyse. En lille tabel, der er blevet opdelt kan optimeres ved at fjerne partitionering helt som dette vil forbedre komprimering.                                                                        |
| [row_count_total]                  | Rækken Total antal for tabellen. For eksempel kan du bruge denne værdi til at beregne procentdel af rækker i komprimeret tilstand.                                                                      |
| [row_count_per_distribution_MAX]   | Hvis alle rækker er jævnt fordelt denne værdi skal være target antallet af rækker i fordeling. Sammenligne denne værdi med compressed_rowgroup_count.                                 |
| [COMPRESSED_rowgroup_rows]         | Samlet antal rækker i columnstore format for tabellen.                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_AVG]     | Hvis det gennemsnitlige antal rækker er betydeligt mindre end det maksimale antal rækker for en rækkegruppe, kan du vælge CTAS eller ændre indeks GENOPBYGGE for at komprimere dataene                     |
| [COMPRESSED_rowgroup_count]        | Antallet af Rækkegrupper i columnstore format. Hvis tallet er meget høj i forhold til tabellen er det en indikator, columnstore tæthed er lav.                                  |
| [COMPRESSED_rowgroup_rows_DELETED] | Rækker slettes logisk i columnstore format. Hvis tallet er høj i forhold til tabelstørrelse, kan du overveje at genoprette partitionen eller genopbygge indekset, som dette fjerner fysisk. |
| [COMPRESSED_rowgroup_rows_MIN]     | Brug denne sammen med kolonnerne, gennemsnit og Maks for at forstå værdiinterval for grupperne række i din columnstore. Et lave tal over Indlæs grænseværdi (102,400 per partition justeret fordeling) foreslår, at optimeringer findes i indlæsningen af data                                                                                                                                                 |
| [COMPRESSED_rowgroup_rows_MAX]     | Ovenstående                                                                                                                                                                                  |
| [OPEN_rowgroup_count]              | Åbn Rækkegrupper er normal. En forventer praktisk én åben rækkegruppe hver tabel fordeling (60). For mange tal foreslå indlæsning på tværs af partitioner af data. Dobbelt kontrol leverandør strategi for at sikre, at det er lyd                                                                                                                                                                                                |
| [OPEN_rowgroup_rows]               | Hver rækkegruppe kan have 1.048.576 rækker i den som en maksimum. Brug denne værdi til at se, hvordan fuld grupperne åben række er på nuværende tidspunkt                                                                 |
| [OPEN_rowgroup_rows_MIN]           | Åbn grupper angiver, at dataene er enten strøm ikke indlæses i tabellen eller, at den forrige belastning flyttet om resterende rækker til denne rækkegruppe. Brug MIN og Maks, AVG kolonner for at se, hvor meget data er sat i Åbn række grupper. Det kan være 100% af alle dataene for små tabeller! I så fald ændre indeks GENOPBYGGE for at tvinge dataene til columnstore.                                                                       |
| [OPEN_rowgroup_rows_MAX]           | Ovenstående                                                                                                                                                                                  |
| [OPEN_rowgroup_rows_AVG]           | Ovenstående                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows]             | Se på lukket række gruppere rækker for at kontrollere blive.                                                                                                                                       |
| [CLOSED_rowgroup_count]            | Antallet af lukket Rækkegrupper skal være lav, hvis nogen kan ses. Lukket Rækkegrupper kan konverteres til komprimeret rowg roups ved hjælp af ændre INDEKSET... OMORGANISERE kommandoen. Dette er dog ikke kræves normalt. Lukket grupper, automatisk konverteres til columnstore Rækkegrupper af processen baggrund "tupel mover".                                                                                               |
| [CLOSED_rowgroup_rows_MIN]         | Lukket Rækkegrupper skal have en meget høj fyld sats. Hvis fyld rente for en lukket rækkegruppe er lav, derefter er yderligere analyse af columnstore påkrævet.                                   |
| [CLOSED_rowgroup_rows_MAX]         | Ovenstående                                                                                                                                                                                  |
| [CLOSED_rowgroup_rows_AVG]         | Ovenstående                                                                                                                                                                                  |
| [Rebuild_Index_SQL]         | SQL at genopbygge columnstore indeks for en tabel                                                                                                                                                     |

## <a name="causes-of-poor-columnstore-index-quality"></a>Årsager til dårlig columnstore indeks kvalitet

Hvis du har identificeret tabeller med dårlig segmentet kvalitet, vil du identificere den egentlige årsag.  Nedenfor finder du nogle andre almindelige årsager til dårlig segmentet quaility:

1. Hukommelse tryk når indeks blev oprettet
2. Stor mængde DML handlinger
3. Lille eller kommer lidt efter lidt Indlæs handlinger
4. For mange partitioner

Disse faktorer kan medføre et columnstore indeks have betydeligt, der er mindre end 1 million optimal rækkerne hver rækkegruppe.  De kan også medføre rækker, der skal gå til gruppen delta række i stedet for en komprimeret rækkegruppe. 

### <a name="memory-pressure-when-index-was-built"></a>Hukommelse tryk når indeks blev oprettet

Antallet af rækker i komprimeret rækkegruppe er direkte relateret til bredden af rækken og mængden af tilgængelig hukommelse til at behandle gruppen række.  Når rækker er skrevet til columnstore tabeller under hukommelsesforbrug, kan columnstore segmentet kvaliteten lide.  Derfor er den bedste fremgangsmåde at give den session, der skriver til din columnstore indeks tabeller adgang til den ønskede hukommelse som muligt.  Da der er en forholdet mellem hukommelse og på dokumentsammenfald, vejledningen på den højre hukommelsesallokering afhænger af dataene i hver række i tabellen, mængden DWU, du har tildelt til systemet og mængden på dokumentsammenfald stik, kan du give til den session, der skriver data til tabellen.  Som en bedste fremgangsmåde anbefaler vi starter med xlargerc, hvis du bruger DW300 eller derunder, largerc, hvis du bruger DW400 DW600 og mediumrc, hvis du bruger DW1000 og derover.

### <a name="high-volume-of-dml-operations"></a>Stor mængde DML handlinger

En stor mængde DML handlinger, opdatere og slette rækker kan en manglende effektivitet i columnstore. Dette er tilfældet, når fleste af rækkerne i en rækkegruppe er ændret.

- Slette en række fra en komprimeret rækkegruppe kun logisk markerer rækken, som slettet. Rækken forbliver i gruppen komprimeret række, indtil den partition eller tabel genopbygges.
- Rækken for at føjer indsætte en række til en intern rowstore tabel, der kaldes en delta rækkegruppe. Den indsatte række konverteres ikke til columnstore, indtil gruppen delta række er fuld og er markeret som lukket. Rækkegrupper er lukket, når de har nået det maksimale kapacitet for 1.048.576 rækker. 
- Opdatere en række i columnstore format behandles som en logisk Slet og derefter en Indsæt. Den indsatte række kan blive gemt i delta store.

Batches opdatering og indsætte handlinger, der overskrider flere grænseværdi for 102,400 rækker per partition justerede fordeling skrives direkte i formatet columnstore. Men hvis en lige fordeling, vil skal du redigere mere end 6.144 million rækker i en enkelt handling for denne indstilling for at opstå. Hvis antallet af rækker til en given partition justeret er fordeling mindre end 102,400 rækkerne går til store delta og forbliver der, indtil tilstrækkelige rækker er blevet indsat eller ændret for at lukke gruppen række eller er blevet genopbygges indekset.

### <a name="small-or-trickle-load-operations"></a>Lille eller kommer lidt efter lidt Indlæs handlinger

Små indlæser, flow i SQL Data Warehouse er også kendt som kommer lidt efter lidt indlæse. De repræsenterer typisk en nær konstant strøm af data, der optagelse af systemet. Som denne strøm er i nærheden af fortløbende er lydstyrken for rækker dog ikke særligt store. Data, der er mere ofte end ikke betydeligt under i grænseværdi for kræves til en direkte belastning på columnstore format.

I følgende situationer er det ofte bedre at lander dataene først Azure blob-lager og lad den point, der kræves før indlæsning. Denne metode er ofte kaldet *micro-samling*.

### <a name="too-many-partitions"></a>For mange partitioner

En anden, skal du tænke på, er påvirkningen af partitionering på dine grupperet columnstore tabeller.  Før partitionering opdeler SQL Data Warehouse allerede dine data i 60 databaser.  Partitionering yderligere dividerer dine data.  Hvis du partition dine data, skal du overveje, at **hver** partition skal have mindst 1 million rækker kan få glæde af et grupperet columnstore indeks.  Hvis du partition tabellen i 100 partitioner, derefter skal tabellen have mindst 6 milliarder rækker for at få glæde af et grupperet columnstore indeks (60 fordelingerne *100 partitioner* 1 million rækker). Hvis 100 partitionstabellen ikke har 6 milliarder rækker, enten reducere antallet af partitioner, eller du overveje at bruge en heap tabel i stedet.

Når dine tabeller er blevet indlæst med nogle data, skal du følge den under trin til at identificere og genopbygge tabeller med optimal klynge columnstore indeks.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Genopbygge indeks til at forbedre kvaliteten på segmentet

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>Trin 1: Identificere eller brugeroprettelsen som bruger den rigtige ressourceklasse

En hurtig metode til at forbedre kvaliteten på segmentet med det samme er at genopbygge indekset.  SQL-Sætningen returneres af visningen ovenfor returnerer sætningen ALTER indeks GENOPBYGGE som kan bruges til at genopbygge dit indeks.  Når genopbygge dit indeks, skal du sikre, at du allokerer tilstrækkelig hukommelse til sessionen som genopbygger indekset.  Øge Ressourceklassen for en bruger, som har tilladelse til at genopbygge indeks i denne tabel til det anbefalede minimum for at gøre dette.  Ressourceklassen af ejer databasebruger kan ikke ændres, så hvis du ikke har oprettet en bruger på systemet, skal du gøre det først.  Vi anbefaler, at du som minimum er xlargerc, hvis du bruger DW300 eller derunder largerc Hvis du bruger DW400 DW600 og mediumrc, hvis du bruger DW1000 og derover.

Nedenfor er et eksempel på, hvordan du allokerer mere hukommelse til en bruger ved at øge deres ressourceklasse.  Du kan finde flere oplysninger om ressource klasser, og hvordan du opretter en ny bruger kan findes i [på dokumentsammenfald og arbejdsbelastningen] [ Concurrency] artikel.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>Trin 2: Genopbygge grupperet columnstore indeks med højere ressource klasse bruger
Log på som bruger fra trin 1 (fx LoadUser), som er nu ved hjælp af en højere ressourceklasse, og udføre ændre indeks-sætninger.  Sørg for, at brugeren har ALTER tilladelse til at de tabeller, hvor indekset genopbygges.  Disse eksempler viser, hvordan at genopbygge hele columnstore indekset eller genopbygge en enkelt partition. På store tabeller er det flere praktiske at genopbygge indekserer en enkelt partition ad gangen.

Du kan også i stedet for at genopbygge indekset, kan du kopiere tabellen til en ny tabel ved hjælp af [CTAS][].  Hvilken metode er den bedste? For store mængder data er [CTAS][] som regel hurtigere end [Ændre indeks][]. [Ændre indeks][] er nemmere at bruge for mindre datamængder og kræver ikke, at du du kan udskifte tabellen.  Du kan finde flere oplysninger om, hvordan du genopbygger indekser med CTAS i **under genopbygning indeks med CTAS og partition skifte** nedenfor.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Genopbygge et indeks i SQL Data Warehouse er en offline handling.  Du kan finde flere oplysninger om genopbygge indeks i afsnittet ændre GENOPBYGGE indeks i [Columnstore indeks defragmentering][] og emnet syntaksen [Ændre indeks][].
 
### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>Trin 3: Bekræft grupperet columnstore segmentet kvalitet er forbedret
Kør forespørgslen, hvilken identificerede tabel med dårlig segment skal tildeles kvalitet og bekræfte segmentet kvalitet er forbedret.  Hvis segmentet kvalitet ikke forbedre, kan det skyldes, at rækkerne i tabellen er ekstra brede.  Overvej at bruge en højere ressourceklasse eller DWU, når genopbygge dit indeks.

 
## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Genopbygge indeks med CTAS og partition Skift

I dette eksempel bruges [CTAS][] og partition Skift for at genopbygge partition en tabel. 

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Create a SWITCH out table
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Få mere at vide om at oprette partitioner, der bruger `CTAS`, artiklen [Partition][] .

## <a name="next-steps"></a>Næste trin

Se artiklerne på [Tabel oversigt][Oversigt], [Tabel-datatyper][Datatyper], [distribuere en tabel][fordel], [partitionering en tabel][Partition], [Vedligeholde tabel statistik][Statistik] og [Midlertidige tabeller][midlertidige]for at få mere for at vide.  Få mere at vide om bedste fremgangsmåder under [SQL Data Warehouse bedste fremgangsmåder][].

<!--Image references-->

<!--Article references-->
[Oversigt]: ./sql-data-warehouse-tables-overview.md
[Datatyper]: ./sql-data-warehouse-tables-data-types.md
[Distribuere]: ./sql-data-warehouse-tables-distribute.md
[Indeks]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[Midlertidige]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ÆNDRE INDEKS]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[grupperet indeks og ikke-grupperede indeks]: https://msdn.microsoft.com/library/ms190457.aspx
[oprette tabel syntaks]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore indeks defragmentering]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[grupperet columnstore indeks]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
