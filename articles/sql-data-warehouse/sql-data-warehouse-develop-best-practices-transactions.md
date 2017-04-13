<properties
   pageTitle="Optimering af transaktioner for SQL Data Warehouse | Microsoft Azure"
   description="Bedste praksis vejledning til at skrive effektiv transaktion opdateringer i Azure SQL Data Warehouse"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Optimering af transaktioner for SQL Data Warehouse

I denne artikel forklares, hvordan du optimerer ydeevnen for din transaktions kode og minimere risikoen for lang rollback.

## <a name="transactions-and-logging"></a>Transaktioner og logføring

Transaktioner er en vigtig komponent i en relationel database engine. SQL Data Warehouse bruger transaktioner under ændring af data. Disse transaktioner kan være eksplicit eller implicit. Enkelt `INSERT`, `UPDATE` og `DELETE` sætninger er alle eksempler på implicitte transaktioner. Eksplicitte transaktioner skrevet eksplicit af en udvikler, ved hjælp af `BEGIN TRAN`, `COMMIT TRAN` eller `ROLLBACK TRAN` og bruges typisk, når flere ændring sætninger skal knyttes sammen i én enkelt atomisk enhed. 

Azure SQL Data Warehouse anvender ændringer til databasen ved hjælp af transaktionslog. Hver fordelingen har sin egen transaktionslog. Transaktions log skriver er automatisk. Der er ingen konfiguration, der er påkrævet. Mens denne proces garanterer på Skriv udgør den en en omkostninger i systemet. Du kan minimere denne virkning ved at skrive en transaktion effektiv kode. En transaktion effektiv kode bredt falder inden for to kategorier.

- Udnytte minimale logføring konstruktioner, hvor det er muligt
- Processen data ved hjælp af fastsat navne for at undgå enkelt længerevarende transaktioner
- Indføre en partition skifte mønster for store ændringer i en given partition

## <a name="minimal-vs-full-logging"></a>Minimale kontra fuld logføring

I modsætning til fuldt logget handlinger, der bruger transaktionsloggen til at holde styr på enhver række ændring, hold styr på minimalt logget handlinger omfang tildelinger og kun metadata ændringer. Derfor minimale logføring omfatter logføring kun de oplysninger, der er påkrævet for at rollback posteringen i tilfælde af en mislykket eller anmodning om en eksplicit (`ROLLBACK TRAN`). Som meget mindre oplysninger registreres i transaktionsloggen, udfører en handling af minimalt logget bedre end en samme størrelse fuldt logget handling. Desuden da færre skriver gå transaktionsloggen, der genereres en meget mindre mængde data i og så er flere I/O effektivt.

Beskyttelsesniveauer posteringen gælder kun for fuldt logget handlinger.

>[AZURE.NOTE] Minimalt logget handlinger kan deltage i eksplicitte transaktioner. Det er muligt at annullere minimalt logget handlinger, som alle ændringer i allokering strukturer, registreres. Det er vigtigt at forstå, ændringen logføres "minimalt" det er ikke logget ophævelse.

## <a name="minimally-logged-operations"></a>Minimalt logget handlinger

Følgende handlinger er stand til at blive minimalt logget:

- OPRETTE tabel som Vælg ([CTAS][])
- INDSÆT.. VÆLG
- OPRETTE INDEKS
- ALTER INDEKS GENOPBYG
- SLIP INDEKS
- AFKORTE TABEL
- SLIP TABEL
- ALTER TABEL SKIFTE PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Interne data bevægelse handlinger (såsom `BROADCAST` og `SHUFFLE`) påvirkes ikke af grænsen transaktion sikkerhed.

## <a name="minimal-logging-with-bulk-load"></a>Laveste logføringsniveau med flere belastning

`CTAS`og `INSERT...SELECT` er begge masseforsendelse af belastning handlinger. Dog begge er påvirket af tabeldefinition mål og afhænger af scenariet Indlæs. Nedenfor vises en tabel, der forklarer, hvis flere handlingen helt eller minimalt logføres:  

| Primært indeks               | Indlæse scenarie                                            | Logføringstilstand |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Heap                        | En hvilken som helst                                                      | **Minimale**  |
| Grupperet indeks             | Tom måltabellen                                       | **Minimale**  |
| Grupperet indeks             | Indlæst rækker ikke overlapper med eksisterende sider i mål | **Minimale**  |
| Grupperet indeks             | Indlæst rækker overlappe eksisterende sider i mål        | Fuld         |
| Grupperet Columnstore indeks | Batch størrelse > = 102,400 per partition justeret fordeling | **Minimale**  |
| Grupperet Columnstore indeks | Batch størrelse < 102,400 per partition justeret fordeling  | Fuld         |

Det er værd at bemærke, at en hvilken som helst skriver opdatere sekundær eller ikke-grupperede indeks altid være fuldt logget handlinger.

> [AZURE.IMPORTANT] SQL Data Warehouse har 60 salgsdistributioner. Derfor, hvis alle rækker er jævnt fordelt og lander i en enkelt partition, skal din batchen skal indeholde 6,144,000 rækker eller større logføres minimalt, når du skriver til et grupperet Columnstore indeks. Hvis tabellen er opdelt og de rækker, der indsættes dække partition grænser, derefter skal du 6,144,000 rækker per partition rammen, hvis du selv datafordeling. Hver partition i hver fordeling skal uafhængigt overstige 102,400 række grænsen for Indsæt minimalt være logget på fordelingen.

Indlæse data i en ikke-tomme tabel med et grupperet indeks kan indeholde en blanding af fuldt logget og minimalt logget rækker ofte. Et grupperet indeks er en ikke-opgjorte træ (b-træet) af sider. Hvis siden der skrives til allerede indeholder rækker fra en anden transaktion, derefter logføres disse skriver fuldt. Men hvis siden er tom derefter skriveadgang til den pågældende side minimalt logføres.

## <a name="optimizing-deletes"></a>Optimere sletter

`DELETE`er en fuldt logget handling.  Hvis du vil slette en stor mængde data i en tabel eller en partition, skal det ofte giver mere mening at `SELECT` de data, du vil beholde, som kan køres som en minimalt logget handling.  For at opnå dette, skal du oprette en ny tabel med [CTAS][].  Når oprettet, skal du bruge [OMDØBE][] du kan udskifte dit gamle tabel med den nyligt oprettede tabel.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimering af opdateringer

`UPDATE`er en fuldt logget handling.  Hvis du vil opdatere et stort antal rækker i en tabel eller en partition det kan ofte være langt mere effektivt at bruge handlingen minimalt logget som [CTAS][] for at gøre dette.

I eksemplet herunder en fuld tabel opdatering er blevet konverteret til en `CTAS` så minimale logføring er muligt.

I dette tilfælde tilføjer vi efterfølgende et rabatbeløb og salg i tabellen:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Genoprette store tabeller kan få glæde af ved hjælp af funktioner til administration af SQL Data Warehouse arbejdsbyrde. Se afsnittet arbejdsbelastningen management i artiklen [på dokumentsammenfald][] kan finde flere oplysninger.

## <a name="optimizing-with-partition-switching"></a>Optimere med partition Skift

Når det gælder med store ændringer i en [tabel partition][], gør en partition skifte mønster en masse mening. Hvis dine ændringer er betydeligt og strækker sig over flere partitioner, opnås skal du blot gentagelse over partitionerne derefter det samme resultat.

Trin til at udføre en partition parameter er som følger:
1. Oprette en tom ud partition
2. Udføre 'update' som en CTAS
3. Skifte ud af de eksisterende data til tabellen out
4. Skifte i de nye data
5. Ryd op i dataene

Men for at identificere partitionerne til at skifte skal vi først oprette en hjælper procedure som det er vist nedenfor. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Denne procedure maksimerer kode genbruge og bevarer den partition, skifte eksempel, der er mere kompakt.

Nedenstående kode viser de fem trin, der er nævnt ovenfor til at opnå en fuld partition skifte rutinemæssige.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimere logføring med mindre grupper

For stor ændring datahandlinger, god det være en ide at inddele handlingen i dele eller batches til at begrænse måleenheden for arbejde.

Et arbejdseksempel er angivet nedenfor. Batchstørrelse, der er angivet til et trivial tal til at fremhæve metode. I virkeligheden ville på batchen være betydeligt større. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Afbryd midlertidigt og skalering vejledning

Azure SQL Data Warehouse gør det muligt at afbryde, genoptage og skalere dit datawarehouse efter behov. Når du pause eller skalere dit SQL Data Warehouse er det vigtigt at forstå, en hvilken som helst startet transaktioner er afsluttet. forårsager eventuelle åbne poster annulleres. Hvis din arbejdsbyrde havde udstedt ændring af en lang tid, der kører og ufuldstændig data før handlingen Afbryd midlertidigt eller skala, derefter skal dette arbejde ikke fortrydes. Dette kan påvirke den tid, det tager at standse eller skalere databasen Azure SQL Data Warehouse. 

> [AZURE.IMPORTANT] Begge `UPDATE` og `DELETE` er fuldt logget operationer, og så disse fortryde/annullere Fortryd handlinger kan tage betydeligt længere tid end tilsvarende logget minimalt handlinger. 

Det bedste scenario er at lade i flight datahandlinger fuldført før afbrydelsen eller skalering SQL Data Warehouse. Men dette kan ikke altid være praktisk. Hvis du vil reducere risikoen for en lang rollback, skal du overveje et af følgende indstillinger:

- Skrive igen ved hjælp af [CTAS][] lange handlinger
- Opdel handlingen i stykker; operativsystem på et undersæt af rækker

## <a name="next-steps"></a>Næste trin

Se [transaktioner i SQL Data Warehouse][] mere at vide om isolationsniveauet niveauer og transaktions begrænsninger.  Du kan finde en oversigt over andre bedste fremgangsmåder, [SQL Data Warehouse bedste fremgangsmåder][].

<!--Image references-->

<!--Article references-->
[Transaktioner i SQL datawarehouse]: ./sql-data-warehouse-develop-transactions.md
[tabel partition]: ./sql-data-warehouse-tables-partition.md
[På dokumentsammenfald]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse bedste fremgangsmåder]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[OMDØB]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

