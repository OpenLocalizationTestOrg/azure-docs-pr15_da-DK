<properties
   pageTitle="Oversigt over tabeller i SQL Data Warehouse | Microsoft Azure"
   description="Introduktion til Azure SQL Data Warehouse tabeller."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Oversigt over tabeller i SQL datalager

> [AZURE.SELECTOR]
- [Oversigt][]
- [Datatyper][]
- [Distribuere][]
- [Indeks][]
- [Partition][]
- [Statistik][]
- [Midlertidige][]

Det er nemt at komme i gang med oprettelse af tabeller i SQL Data Warehouse.  Grundlæggende [CREATE TABLE][] syntaksen følger almindelige syntaksen du oftest allerede kender, i at arbejde med andre databaser.  Hvis du vil oprette en tabel, skal du blot navngive din tabel, navngive dine kolonner og definere datatyper for hver kolonne.  Hvis du har oprette tabeller i andre databaser, ser det meget bekendt for dig til dig.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

Ovenstående eksempel opretter en tabel, der hedder kunder med to kolonner, fornavn og efternavn.  Hver kolonne defineres med datatypen VARCHAR(25), som begrænser dataene på 25 tegn.  Disse grundlæggende attributterne for en tabel, samt andre, er men ellers primært den samme som andre databaser.  Datatyper er defineret for hver kolonne, og sikre integriteten af dine data.  Indeks kan tilføjes til at forbedre ydeevnen ved at reducere I/O.  Partitionering kan tilføjes til at forbedre ydeevnen, når du har brug for til at ændre data.

[Omdøbe] [ RENAME] en SQL Data Warehouse tabel ser sådan ud:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Fordelt tabeller

En ny grundlæggende attribut introduceret ved distribuerede systemer som SQL Data Warehouse er **fordeling kolonne**.  Kolonnen fordeling er stort set hvad det lyder.  Det er den kolonne, der bestemmer, hvordan du kan distribuere eller opdele dataene i baggrunden.  Når du opretter en tabel uden at angive kolonnen fordeling, fordeles tabellen automatisk bruger **round robin**.  Mens round robin tabeller kan være tilstrækkelige i visse scenarier, kan definerer fordeling kolonner reducere flytning af data under forespørgsler, og som derfor optimering af ydeevne.  Se [distribuere en tabel][fordel] mere at vide om hvordan du vælger en fordeling kolonne.

## <a name="indexing-and-partitioning-tables"></a>Indeksering og partitionering tabeller

Når du bliver mere avanceret brug af SQL Data Warehouse og vil optimere ydeevnen, får du vil vide mere om Design af tabeller.  Se artiklerne på [Tabel-datatyper][Datatyper], [distribuere en tabel][fordel], [indeksering en tabel][indeks] og [partitionering en tabel][Partition]for at få mere for at vide.

## <a name="table-statistics"></a>Tabel statistik

Statistik er en meget vigtige til at opnå den bedste ydeevne ud af dine SQL Data Warehouse.  Da SQL Data Warehouse endnu opretter ikke automatisk, og Opdater statistik for dig, som du måske er vant til i Azure SQL-Database, læse vores artikel om [Statistik][] kan være en af de vigtigste artikler læse du, for at sikre, at du får den bedste ydeevne fra dine forespørgsler.

## <a name="temporary-tables"></a>Midlertidige tabeller

Midlertidige tabeller er tabeller, som kun findes for varigheden af dit logon og kan ikke ses af andre brugere.  Midlertidige tabeller kan være en god metode til at forhindre andre i at se midlertidige resultater og også reducere behovet for oprydning.  Da midlertidige tabeller anvender også lokale lager, kan de giver hurtigere ydeevne for nogle handlinger.  Se [Midlertidig tabel][midlertidige] artikler for at få flere oplysninger om midlertidige tabeller.

## <a name="external-tables"></a>Eksterne tabeller

Eksterne tabeller, også kaldet Polybase tabeller, er tabeller, som kan forespørges fra SQL Data Warehouse, men punkt til eksterne data fra SQL Data Warehouse.  For eksempel kan du oprette en ekstern tabel hvilke peger på filer på Azure Blob-lager.  Du kan finde flere oplysninger om, hvordan du kan oprette og få vist en ekstern tabel [Indlæs data med Polybase][].  

## <a name="unsupported-table-features"></a>Ikke-understøttede tabelfunktioner

Mens SQL Data Warehouse indeholder mange af de samme tabelfunktioner, som andre databaser, er der nogle funktioner, der ikke understøttes endnu.  Nedenfor finder du en liste over nogle af de tabelfunktioner, ikke understøttes endnu.

| Ikke-understøttede funktioner |
| --- |
|[Egenskaben Identity][] (se [Tildele erstatning nøgle løsning][])|
|Primær nøgle, fremmede nøgler, entydige og Kontrollér [Tabelbegrænsninger][]|
|[Entydige indekser][]|
|[Beregnede kolonner][]|
|[Sparse kolonner][]|
|[Brugerdefinerede typer][]|
|[Sekvens][]|
|[Udløsere][]|
|[Indekserede visninger][]|
|[Synonymer][]|

## <a name="table-size-queries"></a>Tabel størrelse forespørgsler

En enkelt metode til at identificere mellemrum og rækker, der bruges af en tabel i hver af 60 fordelingerne er at bruge [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Men ved hjælp af DBCC kommandoer kan helt begrænse.  Administration af dynamiske visninger (DMVs), så du kan se meget mere detaljeret samt giver dig meget større kontrol over forespørgselsresultaterne.  Starte med at oprette denne visning, der henvises til i mange af vores eksemplerne i dette og andre artikler.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Tabel mellemrum oversigt

Denne forespørgslen returnerer rækker og mellemrum efter tabel.  Det er en god forespørgsel for at se, hvilke tabeller der er dine største tabeller og om de er round robin eller hash distribueret.  For hash distribueret tabeller viser det også kolonnen fordeling.  I de fleste tilfælde skal største tabellerne hash fordeles med et grupperet columnstore indeks.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabel mellemrum efter fordelingstype

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabel mellemrum efter Indekstype

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Fordeling mellemrum oversigt

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Næste trin

Se artiklerne på [Tabel-datatyper][Datatyper], [distribuere en tabel][fordel], [indeksering en tabel][indeks], [partitionering en tabel][Partition], [Vedligeholde tabel statistik][Statistik] og [Midlertidige tabeller][midlertidige]for at få mere for at vide.  Få mere at vide om bedste fremgangsmåder for, i [SQL Data Warehouse bedste fremgangsmåder][].

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
[Indlæse data med Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[OPRETTE TABEL]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Egenskaben Identity]: https://msdn.microsoft.com/library/ms186775.aspx
[Tildele erstatning vigtige løsning]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Tabelbegrænsninger]: https://msdn.microsoft.com/library/ms188066.aspx
[Beregnede kolonner]: https://msdn.microsoft.com/library/ms186241.aspx
[Sparse kolonner]: https://msdn.microsoft.com/library/cc280604.aspx
[Brugerdefinerede typer]: https://msdn.microsoft.com/library/ms131694.aspx
[Sekvens]: https://msdn.microsoft.com/library/ff878091.aspx
[Udløsere]: https://msdn.microsoft.com/library/ms189799.aspx
[Indekserede visninger]: https://msdn.microsoft.com/library/ms191432.aspx
[Synonymer]: https://msdn.microsoft.com/library/ms177544.aspx
[Entydige indekser]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
