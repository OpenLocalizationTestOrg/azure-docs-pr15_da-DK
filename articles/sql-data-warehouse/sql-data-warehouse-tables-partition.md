<properties
   pageTitle="Partitionering tabeller i SQL Data Warehouse | Microsoft Azure"
   description="Introduktion til tabel partitionering i Azure SQL-datalager."
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
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Partitionering tabeller i SQL datalager

> [AZURE.SELECTOR]
- [Oversigt][]
- [Datatyper][]
- [Distribuere][]
- [Indeks][]
- [Partition][]
- [Statistik][]
- [Midlertidige][]

Partitionering understøttes alle SQL Data Warehouse tabel forskellige; herunder grupperet columnstore, grupperet indeks og heap.  Partitionering understøttes også på alle fordeling typer, herunder både hash eller round robin distribueret.  Partitionering kan er du opdele dataene i mindre grupper af data og i de fleste tilfælde partitionering udført på en datokolonne.

## <a name="benefits-of-partitioning"></a>Fordele ved partitionering

Partitionering kan være en fordel data vedligeholdelse og forespørgsel ydeevne.  Om få gavn af begge eller kun er afhængig af hvordan data indlæses og om den samme kolonne kan bruges til begge formål, da partitionering kan kun udføres af én kolonne.

### <a name="benefits-to-loads"></a>Fordele ved at indlæse

Den primære fordel af partitionering i SQL Data Warehouse er forbedre effektiviteten og ydeevnen af indlæsning data ved brug af partition sletningen, Skift og flette.  I de fleste tilfælde opdelt data på en datokolonne, der er tæt forbundet med den rækkefølge, data, er indlæst til databasen.  En af de største fordele ved at bruge partitioner for data det at undgå transaktionslog.  Mens blot indsætte, opdatere eller slette data kan være den mest enkle fremgangsmåde med et lille tanken og kræfter, kan ved hjælp af partitionering under indlæsningsprocessen væsentligt forbedre ydeevnen.

Skifte partition kan bruges til hurtigt at fjerne eller erstatte en sektion i en tabel.  For eksempel kan en salg faktatabel indeholde kun data for de seneste 36 måneder.  I slutningen af hver måned slettes den ældste måned salgsdata fra tabellen.  Disse data kan slettes ved hjælp af en delete-sætning vil slette data til den ældste måned.  Men hvis du sletter en stor mængde data række for række med en delete-sætning kan tage meget lang tid samt oprette risikoen for store transaktioner, hvilket kan tage lang tid at rollback, hvis noget går galt.  En mere optimal metode egner sig til blot slippe den ældste partition af data.  Hvor slette de enkelte rækker kan tage timer, kan Hvis du sletter en hel partition tage sekunder.

### <a name="benefits-to-queries"></a>Fordele ved at forespørgsler

Partitionering kan også bruges til at forbedre ydeevnen i forespørgslen.  Hvis en forespørgsel anvender et filter på en partitioneret kolonne, kan dette begrænse søgningen til kun kvalificerende partitioner som kan være en meget mindre delsæt af data, undgår en fuld tabelscanning.  Prædikat fjernelse ydeevne fordelene er mindre nyttige med Introduktion af grupperet columnstore indeks, men i nogle tilfælde kan der være en fordel til forespørgsler.  Eksempelvis hvis salg faktatabel er opdelt i 36 måneder ved hjælp af feltet Salgsdato, skal du forespørger filteret på datoen, salg kan springe over søgning i partitioner, der ikke svarer til filteret.

## <a name="partition-sizing-guidance"></a>Partition størrelseshåndtag vejledning

Mens partitionering kan bruges til at forbedre ydeevnen nogle scenarier, kan det reducere ydeevnen under visse omstændigheder at oprette en tabel med **for mange** partitioner.  Disse problemer er især sand for grupperet columnstore tabeller.  Det er vigtigt at forstå, hvornår du skal bruge partitionering og antallet af partitioner til at oprette til opdeling for at være nyttig.  Der er ingen hårde hurtig regel som hvor mange partitioner er for mange, det afhænger af dine data, og hvor mange partitioner du indlæser til samtidigt.  Men som en generel tommelfingerregel betragte tilføje 10 'er til 100s af partitioner, ikke 1000 'er.

Når du opretter partitionering på **grupperet columnstore** tabeller, er det vigtigt at overveje, hvor mange rækker der lander i hver partition.  Et minimum af 1 million rækker per fordeling og partition er nødvendig ved optimal komprimering og ydeevnen af grupperet columnstore tabeller.  Før der oprettes partitioner, opdeler SQL Data Warehouse allerede hver tabel i 60 fordelt databaser.  En hvilken som helst partitionering føjet til en tabel er ud over fordelingerne oprettet i baggrunden.  Brug af dette eksempel, hvis salg faktatabellen indeholdt 36 månedlige partitioner, og SQL Data Warehouse er opfyldt 60 salgsdistributioner, klik derefter på salg faktatabel skal indeholde 60 million rækker månedsbasis eller 2.1 milliarder rækker når alle måneder er udfyldt.  Hvis en tabel indeholder betydeligt mindre rækker end det anbefalede mindste antal rækker i partition, kan du overveje at bruge færre partitioner for at gøre øge antallet af rækker i partition.  Se også [indeksering][indeks] -artiklen som indeholder forespørgsler, der kan køres på SQL Data Warehouse til bedømmelse af kvaliteten af klynge columnstore indeks.

## <a name="syntax-difference-from-sql-server"></a>Syntaksen for forskellig fra SQL Server

SQL Data Warehouse introducerer en forenklet definition af partitioner, som er en smule anderledes end SQL Server.  Leverandør funktioner og farveskemaer bruges ikke i SQL datalager, som de er i SQL Server.  I stedet alt, du skal gøre, er identificere partitioneret kolonne og rammen punkter.  Syntaksen for partitionering kan være en smule anderledes end SQL Server, er de grundlæggende begreber de samme.  SQL Server og SQL Data Warehouse understøtter én partition kolonne i tabel, som kan være varierede partition.  Hvis du vil vide mere om partitionering, skal du se [opdelt tabeller og indeks][].

De partitioner tabellen FactInternetSales på kolonnen OrderDateKey under eksempel på en SQL Data Warehouse opdelt [oprette tabel][] -sætning:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Overføre partitionering fra SQL Server

Til at overføre SQL Server partition definitioner til SQL Data Warehouse blot:

- Fjerne SQL Server- [partition farveskema][].
- Tilføje [funktionen partition][] definitionen i oprette tabellen.

Hvis du overfører en partitioneret tabel fra en SQL Server-forekomst på under SQL kan hjælpe dig med at læse på antallet af rækker, der findes i hver partition.  Husk på, hvis den samme leverandør granularitet bruges på SQL Data Warehouse, reduceres antallet af rækker i partition med en faktor på 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Administration af arbejdsbelastning

Én færdige arbejde overvejelser med forhold i tabel partition beslutning er [Administration af arbejdsbelastning][].  Administration af arbejdsbelastning i SQL Data Warehouse er primært for administration af hukommelse og på dokumentsammenfald.  I SQL Data Warehouse er den maksimale hukommelse, der allokeres til hver fordeling under udførelse af forespørgsel regulerede ressourceklasser.  Ideelt bliver partitionerne tilpasses under hensyntagen andre faktorer, såsom hukommelse behovet i forbindelse med opbygning grupperet columnstore indeks.  Grupperet columnstore indeks benefit betydeligt, når de er tildelt mere hukommelse.  Derfor skal du vil sikre dig, at en partition indeks Genopbyg ikke starved hukommelse. Øge mængden af tilgængelig hukommelse til at din forespørgsel kan der opnås ved at skifte fra standardrollen smallrc, til en af de andre roller som largerc.

Oplysninger om tildeling af hukommelse per fordeling findes ved at forespørge ressourcevisninger regulatoren dynamisk administration. Giv din hukommelse vil være mindre end tallene nedenfor i virkeligheden. Det er dog et niveau for retningslinjer, der kan bruges, når sidetilpasning dine partitioner for handlinger for administration af data.  Prøv at undgå sidetilpasning partitionerne ud over hukommelse ret af Ressourceklassen meget stor. Hvis dine partitioner vokse ud over denne figur køre risikoen for hukommelse tryk hvilket igen fører til mindre optimal komprimering.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Skifte partition

SQL Data Warehouse understøtter partition opdeling, fletning og skifte. Hver af disse funktioner er excuted ved hjælp af sætningen [ALTER TABLE][] .

Hvis du vil skifte partitioner mellem to tabeller skal du sikre, at justere partitionerne på deres respektive grænser og, svarer til tabeldefinitioner. Som Kontrollér begrænsninger ikke er tilgængelige til at gennemtvinge området af værdier i en tabel skal i kildetabellen indeholde samme partition grænserne som måltabellen. Hvis dette ikke er tilfældet, mislykkes parameteren partition, som partition metadata ikke synkroniseres.

### <a name="how-to-split-a-partition-that-contains-data"></a>Sådan opdeles en partition, der indeholder data

Den mest effektive metode til at opdele en partition, som allerede indeholder data, er at bruge en `CTAS` sætning. Hvis tabellen partitioneret er et grupperet columnstore skal derefter tabellen partition være tom før den kan opdeles.

Nedenfor finder du en stikprøve partitioneret columnstore tabel, der indeholder én række i hver partition:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Ved at oprette objektet statistikken, sikre vi pågældende tabel metadata er mere nøjagtig. Hvis vi udelader oprettelse af statistik, bruger SQL Data Warehouse standardværdier. Gennemgå [Statistik][]få at vide om statistik.

Vi kan derefter en forespørgsel for den række Tæl ved hjælp af den `sys.partitions` for en katalogside visning:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Hvis vi forsøger at opdele denne tabel, får vi en fejl:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Meddelelse 35346, niveau 15 State 1, linje 44 OPDELE delsætning i sætningen ALTER PARTITION mislykkedes, fordi partitionen ikke er tom.  Kun tomme partitioner kan opdeles i, når et indeks med columnstore findes på tabellen. Overveje at deaktivere columnstore indekset før udstedelse sætningen ALTER PARTITION, og klik derefter genopbygge columnstore indekset, når ændre PARTITION er fuldført.

Men vi kan bruge `CTAS` til at oprette en ny tabel for at holde dine data.

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

En parameter er tilladt, som er justeret partition grænserne. Dette efterlader kildetabellen med en tom partition, som vi kan efterfølgende opdele.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Alt, hvad der resterer er at justere vores data til de nye partition grænser ved hjælp af `CTAS` , og Skift vores data ind igen til den primære tabel

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Når du har udført flytning af dataene er det en god ide at opdatere statistikken på måltabellen at sikre, at de præcist afspejler den nye fordeling af datasættet i deres respektive partitioner:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tabel partitionering versionsstyring

Hvis du vil undgå tabeldefinitionen for din fra **rust** i systemet kilde kontrolelement vil du måske kan du overveje følgende fremgangsmåde:

1. Oprette en tabel, som en partitioneret tabel, men uden partition værdier

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`tabellen som en del af installationsprocessen:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Med denne metode koden i versionsstyring forbliver statisk og leverandør rammen værdierne har tilladelse til at være dynamisk; udvikling med lageret over tid.

## <a name="next-steps"></a>Næste trin

Se artiklerne på [Tabel oversigt][Oversigt], [Tabel-datatyper][Datatyper], [distribuere en tabel][fordel], [indeksering en tabel][indeks], [Vedligeholde tabel statistik][Statistik] og [Midlertidige tabeller][midlertidige]for at få mere for at vide.  Få mere at vide om bedste fremgangsmåder for, i [SQL Data Warehouse bedste fremgangsmåder][].

<!--Image references-->

<!--Article references-->
[Oversigt]: ./sql-data-warehouse-tables-overview.md
[Datatyper]: ./sql-data-warehouse-tables-data-types.md
[Distribuere]: ./sql-data-warehouse-tables-distribute.md
[Indeks]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[Midlertidige]: ./sql-data-warehouse-tables-temporary.md
[Administration af arbejdsbelastning]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioneret tabeller og indeks]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[OPRETTE TABEL]: https://msdn.microsoft.com/library/mt203953.aspx
[funktionen partition]: https://msdn.microsoft.com/library/ms187802.aspx
[partition farveskema]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
