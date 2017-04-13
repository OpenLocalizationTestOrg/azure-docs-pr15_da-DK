<properties
   pageTitle="Indlæse data fra Azure blob-lager til SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Lær, hvordan du bruger PolyBase til at indlæse data fra Azure blob-lager til SQL Data Warehouse. Indlæse et par tabeller fra offentlige data i Contoso detailsalg Data Warehouse skemaet."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Indlæse data fra Azure blob-lager til SQL Data Warehouse (PolyBase)

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Bruge PolyBase og T-SQL-kommandoer til at indlæse data fra Azure blob-lager til Azure SQL Data Warehouse. 

For at beholde det enkle, indlæser dette selvstudium to tabeller fra en offentlig Azure lagerplads Blob i Contoso detailsalg Data Warehouse skemaet. Køre eksemplet [indlæse fuld Contoso detailsalg Data Warehouse][] fra Microsoft SQL Server eksempler lager for at indlæse det komplette datasæt.

I dette selvstudium skal du:

1. Konfigurere PolyBase at indlæse fra Azure blob-lager
2. Indlæse offentligt tilgængelige data i databasen
3. Udføre optimeringer, når afkrydsningsfeltet Indlæs er fuldført.


## <a name="before-you-begin"></a>Inden du går i gang
For at køre dette selvstudium, skal du en Azure-konto, der allerede har en SQL Data Warehouse database. Hvis du ikke allerede har det, kan du se [oprette et SQL Data Warehouse][].

## <a name="1-configure-the-data-source"></a>1. konfigurere datakilden

PolyBase bruger T-SQL eksterne objekter til at definere placeringen af og attributterne for de eksterne data. Eksternt objektdefinitioner er gemt i SQL Data Warehouse. Selve dataene gemmes eksternt.

### <a name="11-create-a-credential"></a>1.1. Oprette en legitimationsoplysninger

Hvis du indlæser Contoso offentlige dataene **Spring dette trin over** . Du behøver sikker adgang til den offentlige data, da den allerede er tilgængelig for alle.

**Ikke kan springe dette trin over** , hvis du bruger dette selvstudium som en skabelon til at indlæsning af dine egne data. Brug følgende script til at oprette en database-fastsat legitimationsoplysninger for at få adgang til data via en legitimationsoplysninger, og derefter bruge den, når du definerer placeringen af datakilden.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Gå til trin 2.

### <a name="12-create-the-external-data-source"></a>1.2. Oprette den eksterne datakilde

Bruge kommandoen [Opret ekstern DATAKILDE][] til at gemme sted, hvor dataene, og typen data. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Hvis du vælger at offentliggøre dine azure blob objektbeholdere, kan du huske, som dataejeren af du vil betale for data udgangspunkt gebyrer når data forlader datacenteret. 

## <a name="2-configure-data-format"></a>2. Konfigurer dataformat

Data, der er gemt i tekstfiler i Azure blob-lager, og hvert felt er adskilt med en afgrænser. Kør denne [Oprette eksterne FILFORMAT][] kommando til at angive formatet for dataene i tekstfilerne. Contoso dataene er dekomprimerede og pipe afgrænset.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. Opret de eksterne tabeller

Nu hvor du har angivet dataformat kilde- og filer, er du klar til at oprette de eksterne tabeller. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Oprette et skema for dataene. 

Hvis du vil oprette et sted at gemme Contoso data i databasen, skal du oprette et skema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3.2. Oprette de eksterne tabeller. 

Køre dette script for at oprette de DimProduct og FactOnlineSales eksterne tabeller. Alle vi laver her definerer kolonnenavne og datatyper og binder dem til placeringen af og formatet af Azure blob storage filer. Definitionen er gemt i SQL Data Warehouse og dataene, findes stadig i Azure Blob-lager.

Parameteren **placering** er mappen under rodmappen Azure BLOB-lager. De enkelte tabeller er i en anden mappe.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. Indlæs data
Der er forskellige måder at få adgang til eksterne data.  Du kan søge data direkte fra den eksterne tabel, indlæser data til nye databasetabeller eller føje eksterne data til eksisterende databasetabeller.  


### <a name="41-create-a-new-schema"></a>4.1. Oprette et nyt skema

CTAS opretter en ny tabel, der indeholder data.  Opret først et skema for contoso dataene.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Indlæs data til nye tabeller

Bruge sætningen [Oprette tabellen AS Vælg (Transact-SQL)][] for at indlæse data fra Azure blob-lager og gemme den i en tabel i databasen. Indlæse med CTAS bruger de på det kraftigste indtastede eksterne tabeller, du lige har oprettet. For at indlæse dataene i nye tabeller, kan bruge én [CTAS][] sætning hver tabel. 

CTAS opretter en ny tabel og udfylder det med resultaterne af en select-sætning. CTAS definerer den nye tabel for at få de samme kolonner og datatyper som et resultat af select-sætningen. Hvis du vælger alle kolonner fra en ekstern tabel, bliver den nye tabel en kopi af kolonner og datatyper i den eksterne tabel.

I dette eksempel skal oprette vi både dimensionen og faktatabel som hash fordelt tabeller. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4,3 opgavestatus indlæsning

Du kan spore status for dine indlæst ved hjælp af dynamiske management visninger (DMVs). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. optimere columnstore komprimering

Som standard gemmes SQL Data Warehouse tabellen som et grupperet columnstore indeks. Når en belastning er fuldført, kan nogle af datarækkerne ikke komprimeres til columnstore.  Der er forskellige årsager, hvorfor dette kan ske. Hvis du vil have mere for at vide, kan du se [administrere columnstore indeks][].

Hvis du vil optimere forespørgselsydelse og columnstore komprimering efter en belastning, genopbygge tabellen for at tvinge columnstore indeks til at komprimere alle rækker. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Yderligere oplysninger om vedligeholde columnstore indeks, i artiklen [administrere columnstore indeks][] .

## <a name="6-optimize-statistics"></a>6. optimere statistik

Det er bedst at oprette enkelt kolonne statistik umiddelbart efter en belastning. Der er nogle valgmuligheder for statistik. Hvis du opretter enkelt kolonne statistik på hver kolonne kan det tage lang tid at genopbygge alle statistik. Hvis du kender bestemte kolonner der ikke skal være i forespørgsel prædikaterne, kan du springe over oprettelse af statistik på kolonnerne.

Hvis du beslutter at oprette enkelt kolonne statistik på hver kolonne i hver tabel, kan du bruge den lagrede procedure kode prøve `prc_sqldw_create_stats` i artiklen [Statistik][] .

I følgende eksempel er et godt udgangspunkt til oprettelse af statistik. Det opretter enkelt kolonne statistik for hver kolonne i dimensionstabellen, og af hver tilslutter kolonne i faktatabellerne. Du kan altid tilføje enkelte eller flere kolonner statistik til andre fakultet tabelkolonner senere.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Resultat, der åbnes ved angivelse!

Du har indlæst offentligt tilgængelige data til Azure SQL Data Warehouse. Super job!

Nu kan du begynde at forespørge på tabeller ved hjælp af forespørgsler ud som følger:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Næste trin
For at indlæse den fulde Contoso detailsalg Data Warehouse data, bruge scriptet i finde flere tip til udvikling, skal du se [Oversigt over SQL Data Warehouse udvikling][].

<!--Image references-->

<!--Article references-->
[Oprette et SQL datawarehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Oversigt over SQL Data Warehouse udvikling]: sql-data-warehouse-overview-develop.md
[administrere columnstore indeks]: sql-data-warehouse-tables-index.md
[Statistik]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[OPRETTE EKSTERNE DATAKILDE]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[OPRETTE EKSTERNE FILFORMAT]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[OPRETTE tabel som Vælg (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Indlæse fuld Contoso detailsalg Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
