<properties
   pageTitle="Midlertidige tabeller i SQL Data Warehouse | Microsoft Azure"
   description="Introduktion til midlertidige tabeller i Azure SQL-datalager."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>Midlertidige tabeller i SQL datalager

> [AZURE.SELECTOR]
- [Oversigt][]
- [Datatyper][]
- [Distribuere][]
- [Indeks][]
- [Partition][]
- [Statistik][]
- [Midlertidige][]

Midlertidige tabeller er meget nyttig, når databehandlingen - især under transformation, hvor mellemliggende resultaterne er midlertidige. I SQL Data Warehouse findes midlertidige tabeller på niveauet for session.  De er kun synligt for den session, hvori de blev oprettet og udelades automatisk, når sessionen logger af.  Midlertidige tabeller tilbyder en ydeevne fordel, fordi deres resultater skrives til lokale i stedet for remote lagerplads.  Midlertidige tabeller er en smule anderledes i Azure SQL Data Warehouse end Azure SQL-Database, som de kan åbnes fra et vilkårligt sted i den session, herunder både i og uden for en lagret procedure.

I denne artikel indeholder væsentlige vejledning til brug af midlertidige tabeller og fremhæver principper for session niveau midlertidige tabeller. Brug af oplysningerne i denne artikel kan hjælpe dig modularize din kode, forbedring af både genanvende og brugervenlighed vedligeholdelse i din kode.

## <a name="create-a-temporary-table"></a>Oprette en midlertidig tabel

Midlertidige tabeller oprettes ved blot præfikser for tabelnavnet på din med en `#`.  Eksempel:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

Du kan også oprette midlertidige tabeller med en `CTAS` bruger samme fremgangsmåde:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`er en meget effektiv kommando og har tilføjet fordelen ved at være meget effektivt med at anvende transaktion plads til logfilen. 


## <a name="dropping-temporary-tables"></a>Slippe midlertidige tabeller

Når der oprettes en ny session, skal der findes ingen midlertidige tabeller.  Hvis du ringer til den samme lagrede procedure, som opretter en midlertidig med det samme navn, at sikre, at din `CREATE TABLE` sætninger er vellykket en simpel foreløbig kontrol med en `DROP` kan bruges som i den nedenstående eksempel:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Til kodningssprog konsistens, er det en god ide at bruge denne mønster for både tabeller og midlertidige tabeller.  Det er også en god ide at bruge `DROP TABLE` fjerne midlertidige tabeller, når du er færdig med dem i din kode.  Udvikling, det er helt almindeligt at se kommandoerne slip samlet i slutningen af en procedure for at sikre, at disse objekter der ryddes op i lagret procedure.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing kode

Da midlertidige tabeller kan ses et vilkårligt sted i en brugersession, kan dette lykkes, for at hjælpe dig med at modularize din programkode.  For eksempel samler den lagrede procedure nedenfor de anbefalede fremgangsmåder fra ovenfor til at generere DDL som opdaterer alle statistiske data i databasen efter statistikken navn.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

På dette tidspunkt er den eneste handling, der er opstået oprettelse af en lagret procedure, skal du blot vil genereres en midlertidig tabel, #stats_ddl, med DDL-sætninger.  Denne lagrede procedure slippe #stats_ddl, hvis den findes allerede for at sikre, at det ikke mislykkes, hvis køre mere end én gang i en session.  Men, da der er ingen `DROP TABLE` i slutningen af den lagrede procedure, når den lagrede procedure er fuldført, det vil stadig lade den oprettede tabel så den kan læses uden for den lagrede procedure.  I SQL Data Warehouse er i modsætning til andre SQL Server-databaser, det muligt at bruge den midlertidige tabel uden for den fremgangsmåde, der har oprettet den.  SQL Data Warehouse midlertidige tabeller kan være anvendte **et vilkårligt sted** i sessionen. Det kan føre til flere modul og administreres kode som i den nedenstående eksempel:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Midlertidig tabel begrænsninger

SQL Data Warehouse angive nogle begrænsninger, når du implementerer midlertidige tabeller.  På nuværende tidspunkt kun session relateret midlertidige tabeller understøttes.  Global midlertidige tabeller understøttes ikke.  Desuden kan ikke oprettes visninger på midlertidige tabeller.

## <a name="next-steps"></a>Næste trin

Se artiklerne på [Tabel oversigt][Oversigt], [Tabel-datatyper][Datatyper], [distribuere en tabel][fordel], [indeksering en tabel][indeks], [partitionering en tabel][Partition] og [Vedligeholde tabel statistik][Statistik]for at få mere for at vide.  Få mere at vide om bedste fremgangsmåder for, i [SQL Data Warehouse bedste fremgangsmåder][].

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

<!--MSDN references-->

<!--Other Web references-->
