<properties
   pageTitle="Oprette tabel som Vælg (CTAS) i SQL Data Warehouse | Microsoft Azure"
   description="Tip til kodningssprog med tabellen Opret som select-sætning (CTAS) i Azure SQL Data Warehouse til udvikling af løsninger."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Oprette tabel som Vælg (CTAS) i SQL datawarehouse
Oprette tabel som Vælg eller `CTAS` findes en af de vigtigste T-SQL-funktioner. Det er en fuldt parallelized handling, der opretter en ny tabel, der er baseret på output fra en SELECT-sætning. `CTAS`er den nemmeste og hurtigste måde at oprette en kopi af en tabel. Du kan overveje at være en supercharged version af `SELECT..INTO` Hvis du vil have. Dette dokument indeholder både eksempler og bedste praksis for `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Bruge CTAS til at kopiere en tabel

Måske en af de mest almindelige bruger af `CTAS` opretter en kopi af en tabel, så du kan ændre DDL. Hvis for eksempel du oprindeligt har oprettet tabellen som `ROUND_ROBIN` og nu vil ændre det til en tabel, der er distribueret på en kolonne, `CTAS` er, hvordan du vil ændre kolonnen fordeling. `CTAS`kan også bruges til at ændre typer partitionering, indeksering eller kolonne.

Antag, at du har oprettet i denne tabel ved hjælp af typen standard fordeling af `ROUND_ROBIN` distribueret da ingen fordeling kolonne er angivet i den `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Nu vil du oprette en ny kopi af denne tabel med et grupperet Columnstore indeks, så kan du drage fordel af ydeevnen for grupperet Columnstore tabeller. Du også vil distribuere denne tabel på ProductKey, da du forventer joinforbindelser i denne kolonne og vil undgå flytning af data under joinforbindelser på ProductKey. Endelig vil du også tilføje partitionering på OrderDateKey, så du kan hurtigt slette gamle data ved at slippe gamle partitioner. Her er sætningen CTAS, vil kopiere din gamle tabel til en ny tabel.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Endelig kan du omdøbe dine tabeller for at udskifte i den nye tabel og derefter slippe din gamle tabel.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Azure SQL Data Warehouse understøtter endnu ikke er support automatisk oprette eller automatisk opdatering statistik.  Hvis du vil have den bedste ydeevne fra dine forespørgsler, det er vigtigt, at statistik oprettes på alle kolonner i alle tabeller efter den første belastning eller væsentlige ændringer forekommer i dataene.  Finde en detaljeret forklaring af statistik under emnet [Statistik][] i gruppen udvikling af emner.

## <a name="using-ctas-to-work-around-unsupported-features"></a>Brug af CTAS til at løse ikke-understøttede funktioner

`CTAS`kan også bruges til at løse en antallet af de understøttede funktioner, der vises nedenfor. Det kan ofte være skal være en gevinst/gevinst situation, som ikke kun din kode bliver kompatibel, men det udføres ofte hurtigere på SQL Data Warehouse. Dette er som et resultat af dens fuldt parallelized design. Scenarier, der kan arbejde omkring med CTAS omfatter:

- VÆLG.. I
- ANSI JOINFORBINDELSER opdateringer
- ANSI joinforbindelser på sletter
- FLETTE sætning

> [AZURE.NOTE] Prøv at tænke "CTAS første". Hvis du tror, at du kan løse et problem med `CTAS` derefter, der er normalt den bedste måde at løse det – også selvom du skriver flere data som et resultat.
>

## <a name="selectinto"></a>VÆLG.. I
Du kan finde `SELECT..INTO` vises i et antal steder i din løsning.

Nedenfor vises et eksempel på en `SELECT..INTO` sætning:

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Konvertere ovenfor for at `CTAS` er helt simple:

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS kræver aktuelt en fordeling kolonne angives.  Hvis du ikke har valgt forsøger at ændre kolonnen fordeling din `CTAS` skal udføre de hurtigste, hvis du vælger en fordeling kolonne, der er den samme som den underliggende tabel, som denne strategi undgår flytning af data.  Hvis du opretter en lille tabel, hvor ydeevne ikke er en faktor, og derefter kan du angive `ROUND_ROBIN` undgå at skulle fastlægge en fordeling kolonne.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI Deltag i stedet for update-sætning

Du kan finde, du har en kompleks opdatering, der forbinder mere end to tabeller sammen ved hjælp af ANSI deltagelse i syntaksen til at udføre opdatering eller sletning.

Forestil dig, at skulle du opdatere denne tabel:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Den oprindelige forespørgsel kan har ledt nogenlunde sådan ud:

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Da SQL Data Warehouse ikke understøtter ANSI joinforbindelser i den `FROM` -delsætningen i en `UPDATE` -sætning, du kan ikke kopiere denne kode over uden at ændre det lidt.

Du kan bruge en kombination af en `CTAS` og en implicit Deltag for at erstatte denne kode:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI Deltag i stedet for slette sætninger
Nogle gange den bedste metode til at slette data, er at bruge `CTAS`. Markér de data, du vil beholde, i stedet for blot at slette dataene. Denne især sande `DELETE` sætninger, der bruger ansi tilslutter syntaks, da SQL Data Warehouse ikke understøtter ANSI joinforbindelser i den `FROM` -delsætningen i en `DELETE` sætning.

Et eksempel på en konverteret sætningen DELETE findes under:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Erstatte Flet sætninger
Flet sætninger kan erstattes, mindst i webdel, ved hjælp af `CTAS`. Du kan konsolidere den `INSERT` og `UPDATE` til en enkelt sætning. Slettede poster skal være lukket fra en anden sætning.

Et eksempel på en `UPSERT` findes under:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>CTAS anbefaling: udtrykkeligt erklærer datatype og gyldighed af output

Du kan finde, du kører på tværs af denne type koder mønster, når du overfører kode:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinktivt kan du tror, du skal overføre denne kode til en CTAS, og du ville være korrekt. Der er dog et skjulte problem her.

Følgende kode giver ikke det samme resultat:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Bemærk, at kolonnen "resultat" udfører fremad data type og muligheden for værdien null værdierne af udtrykket. Dette kan føre til lille varians i værdier, hvis du ikke er forsigtig.

Prøv følgende som et eksempel:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Den værdi, der er gemt til resultatet er anderledes. Da permanente værdien i kolonnen resultat er anvendt i andre udtryk bliver fejlen endnu mere betydeligt.

![][1]

Dette er særligt vigtig for data overførsler. Selvom den anden forespørgsel er nærmere mere nøjagtig opstår der et problem. Dataene, der ville være forskellige sammenlignet med kildesystemet og, der fører til spørgsmål af integritet i overførslen. Dette er en af disse sjældne tilfælde, hvor det "forkerte" svar er faktisk den rigtige!

Der er årsagen til denne forskelle mellem de to resultater kan vi se ned til konvertering af implicitte type. I det første eksempel definerer tabellen kolonnedefinitionen af. Et implicit typekonvertering opstår, når rækken indsættes. I det andet eksempel er der ingen konvertering af implicitte datatyper, som udtrykket definerer datatypen for kolonnen. Bemærk også, at kolonnen i det andet eksempel er defineret som en null-værdi kolonne, mens i det første eksempel ikke har det. Når tabellen er oprettet i første eksempel kolonne gyldighed blev udtrykkeligt er defineret. I anden resulterer eksempel, det er lige efterladt udtryk og som standard dette i en NULL-definition.  

For at løse disse problemer du skal eksplicit angive typekonvertering og muligheden for værdien null i den `SELECT` del af den `CTAS` sætning. Du kan ikke angive disse egenskaber i delen Opret tabel.

I eksemplet nedenfor viser, hvordan du retter koden:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Bemærk følgende:
- CAST eller Konverter kunne er blevet brugt
- ISNULL bruges til at gennemtvinge muligheden for værdien null ikke samling
- ISNULL er funktionen yderste
- Den anden del af ISNULL er en konstant det vil sige 0

> [AZURE.NOTE] For gyldighed lagres korrekt det er vigtigt at benytte `ISNULL` og ikke `COALESCE`. `COALESCE`er ikke en deterministisk funktion, og så resultatet af udtrykket altid være være nul. `ISNULL`er anderledes. Det er deterministisk. Derfor når den anden del af den `ISNULL` funktionen er en konstant eller en konstant vil være den resulterende værdi ikke NULL.

Dette tip er ikke bare nyttigt til sikring af integriteten af dine beregninger. Det er også vigtigt til tabel partition Skift. Forestil dig, at du har denne tabel, der er defineret som din fakultet:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Værdifeltet er dog et beregnet udtryk, der ikke er en del af kildedataene.

Sådan opretter du dit partitioneret datasæt, kan du gøre følgende:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Forespørgslen køres helt i orden. Problemet leveres, når du forsøger at udføre parameteren partition. Tabeldefinitioner stemmer ikke overens. For at gøre tabellen definitionerne svarer til CTAS skal ændres.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Du kan se derfor, at type konsistens og bevare muligheden for værdien null-egenskaber på en CTAS god teknisk bedste praksis. Det hjælper med at bevare integritet i dine beregninger og også sikrer, at partition skifte er muligt.

Se MSDN kan finde flere oplysninger om brug af [CTAS][]. Det er en af de vigtigste erklæringer i Azure SQL-datalager. Kontrollér, at du grundigt forstå den.

## <a name="next-steps"></a>Næste trin
Du kan finde flere tip til udvikling, [udvikling oversigt][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Oversigt over udvikling]: sql-data-warehouse-overview-develop.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
