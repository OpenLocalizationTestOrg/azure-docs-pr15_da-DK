<properties
    pageTitle="SQL i hukommelsen, komme i gang | Microsoft Azure"
    description="SQL i hukommelsen teknologier forbedre ydeevnen af transaktioner og analytics arbejdsmængder. Lær at udnytte disse teknologier."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Komme i gang med i hukommelsen (Preview) i SQL-Database

Funktioner i hukommelsen forbedre ydeevnen af transaktioner og analytics arbejdsmængder i højre situationer.

Dette emne fremhæver to demonstrationer, én til i hukommelsen OLTP og én for i hukommelsen analyser. Hver enkelt demo leveres med de trin og kode, du har brug for at køre. Kan du enten:

- Bruge koden til at teste variationer for at se forskelle i resultater; eller
- Læs koden at forstå dette scenario, og se, hvordan du opretter og udnytter objekterne i hukommelsen.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [Hurtig Start 1: I hukommelsen OLTP teknologier hurtigere T-SQL ydeevne](http://msdn.microsoft.com/library/mt694156.aspx) -er en anden artikel for at hjælpe dig med at komme i gang.

#### <a name="in-memory-oltp"></a>I hukommelsen OLTP

Funktionerne i i hukommelsen, [OLTP](#install_oltp_manuallink) (online transaction processing) er:

- Hukommelse optimeret tabeller.
- Indbygget kompileret lagrede procedurer.


En hukommelse optimeret tabel har en repræsentation af sig selv i den aktive hukommelse, ud over den standard repræsentation på en harddisk. Business transaktioner mod tabellen køres hurtigere, fordi de direkte interagere med kun den præsentation, der er i den aktive hukommelse.

Med OLTP i hukommelsen, kan du opnå op til 30 gange få i transaktion overførselshastighed, afhængigt af de specifikke oplysninger i arbejdsbelastningen.


Indbygget kompileret lagrede procedurer kræver færre maskine instruktionerne på kørselstidspunktet end traditionelle fortolket lagrede procedurer. Vi har set oprindelige kompileringsresultatet i varighed, der er 1/100 af fortolket varigheden.


#### <a name="in-memory-analytics"></a>I hukommelsen Analytics 

Funktionen i i hukommelsen [Analytics](#install_analytics_manuallink) er:

Columnstore indeks forbedre ydeevnen for analyser og -rapportering forespørgsler. 


#### <a name="real-time-analytics"></a>Realtid Analytics

Du kan kombinere i hukommelsen OLTP og analyser for at få for [Realtid analyser](http://msdn.microsoft.com/library/dn817827.aspx) :

- Indblik i realtid virksomheden baseret på funktionsdygtige data.


#### <a name="availability"></a>Tilgængelighed


GA, generelt tilgængelig:

- [Columnstore indeks](http://msdn.microsoft.com/library/dn817827.aspx) , der findes *på disken*.


Eksempel:

- I hukommelsen OLTP
- Realtid funktionsdygtige Analytics


Overvejelser i forbindelse med mens funktionerne i hukommelsen er i Preview er beskrevet [senere i dette emne](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Disse preview-funktioner er kun tilgængelig for [*Premium*](sql-database-service-tiers.md) databaser ikke i elastiske grupper og er ikke tilgængelig for en hvilken som helst Basic eller Standard databaser.  Understøttelse af Premium databaser i elastiske grupper kommer snart. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>A. Installere på hukommelse OLTP-eksempel

Du kan oprette eksempeldatabasen AdventureWorksLT [version 12] ved nogle få klik på [Azure-portalen](https://portal.azure.com/). Derefter beskriver trinnene i dette afsnit, hvordan du kan forbedre AdventureWorksLT databasen med:

- I hukommelsen tabeller.
- En indbygget kompileret lagret procedure.


#### <a name="installation-steps"></a>Installationstrin

1. Oprette en Premium-database på en version 12-server i [Azure-portalen](https://portal.azure.com/). Angiv **kilde** til eksempeldatabasen AdventureWorksLT [version 12].
 - Finde detaljerede oplysninger, kan du se [oprette din første Azure SQL-database](sql-database-get-started.md).

2. Oprette forbindelse til databasen med SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Kopiere [i hukommelsen OLTP Transact-SQL-script](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) til Udklipsholder.
 - T-SQL-script opretter de nødvendige i hukommelse objekter i AdventureWorksLT Northwind du oprettede i trin 1.

4. Indsæt scriptet T-SQL i SSMS, og udfør derefter scriptet.
 - Afgørende er den `MEMORY_OPTIMIZED = ON` delsætningen CREATE TABLE-sætninger, som i:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Fejl 40536


Hvis du får fejl 40536, når du kører T-SQL-script, kan du køre følgende T-SQL-script for at bekræfte, om databasen understøtter i hukommelsen:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Et resultat på **0** betyder i hukommelsen understøttes ikke, og 1 betyder det understøttes. At diagnosticere problemet:

- Kontrollér, at databasen er oprettet, når funktionerne i hukommelse OLTP blev aktive til Preview.
- Sikre, at databasen er Premium service lag.


#### <a name="about-the-created-memory-optimized-items"></a>Om de oprettede hukommelse optimeret elementer

**Tabeller**: prøven indeholder følgende hukommelse optimeret tabeller:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Du kan undersøge hukommelse optimeret tabeller via **Object Explorer** i SSMS ved at:

- Højreklik på **tabeller** > **Filter** > **Filterindstillingerne** > **Optimeres hukommelse** er lig med 1.


Eller du kan forespørge visningerne katalog f.eks.:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Indbygget kompileret lagret procedure**: SalesLT.usp_InsertSalesOrder_inmem kan undersøges via en katalog visningen forespørgsel:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Køre eksempel OLTP arbejdsbelastningen

Den eneste forskel mellem de følgende to *lagrede procedurer* er, at den første procedure bruger hukommelse optimeret versioner af tabellerne, mens andet procedure bruges tabellerne normalt på disken:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


I dette afsnit, skal se du, hvordan du bruger værktøjet praktiske **ostress.exe** til at udføre de to lagrede procedurer på belastning niveauer. Du kan sammenligne, hvor lang tid det tager to belastning kører at fuldføre.


Når du kører ostress.exe, anbefaler vi, at du overfører parameterværdier, der er udviklet til at begge dele:

- Køre et stort antal samtidige forbindelser, ved hjælp af - n100.
- Har hver forbindelse løkke hundredvis af gange, ved hjælp af - r500.


Dog kan du starte med meget mindre værdier som - n10 og - r 50 at sikre, at den alt arbejde.


### <a name="script-for-ostressexe"></a>Script til ostress.exe


Dette afsnit viser det T-SQL-script, der er integreret i vores ostress.exe kommandolinjen. Scriptet bruger elementer, der er oprettet ved hjælp af den T-SQL-script, du tidligere har installeret.


Følgende script indsætter en stikprøve salg rækkefølge med fem linjeelementer i de følgende hukommelse optimeret *tabeller*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Hvis du vil gøre _ondisk version af den foregående T-SQL for ostress.exe, vil du bare erstatte begge forekomster af *_inmem* understreng med *_ondisk*. Disse erstatter påvirker navne på tabeller og lagrede procedurer.


### <a name="install-rml-utilities-and-ostress"></a>Installere RML værktøjer og ostress


Ideelt ville du planlægger at køre ostress.exe på en Azure VM. Du vil oprette en [Azure virtuelt](https://azure.microsoft.com/documentation/services/virtual-machines/) i den samme Azure geografiske område, hvor databasen AdventureWorksLT er placeret. Men du kan køre ostress.exe på din bærbare computer i stedet.


På VM eller på det hoste du vælge, installerer programmerne genafspilning Markup Language (RML), som omfatter ostress.exe.

- Se ostress.exe diskussionen i [Northwind til i hukommelsen OLTP](http://msdn.microsoft.com/library/mt465764.aspx).
 - Eller se [i hukommelsen OLTP-eksempeldatabasen](http://msdn.microsoft.com/library/mt465764.aspx).
 - Eller se [Blog til installation af ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Kør _inmem belastning arbejdsbelastningen først


Du kan bruge en *RML Cmd Spørg* vindue til at køre vores ostress.exe kommandolinjen. Kommandolinjeparametrene direkte ostress til:

- Køre 100 forbindelser samtidigt (-n100).
- Har hver forbindelse, der kører T-SQL-script 50 gange (-r 50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


For at køre den foregående ostress.exe kommandolinje:


1. Nulstil databaseindholdet data ved at køre følgende kommando i SSMS, slette alle de data, der er indsat ved en hvilken som helst forrige kører:
```
EXECUTE Demo.usp_DemoReset;
```

2. Kopiér teksten i den foregående ostress.exe kommandolinje til din Udklipsholder.

3. Erstatte den `<placeholders>` for de parametre -S - U -P -d med de korrekte reelle værdier.

4. Køre din redigerede kommando i en RML Cmd-vinduet.


#### <a name="result-is-a-duration"></a>Resultatet er en varighed


Når ostress.exe er fuldført, skriver med køre varighed som den sidste linje i output i vinduet RML Cmd. For eksempel bestået en kortere test Kør ved 1,5 minutter:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Nulstille, redigere for _ondisk, og udfør derefter


Når du har resultatet fra _inmem Kør, kan du udføre følgende trin for _ondisk køre:


1. Nulstille databasen ved at køre følgende kommando i SSMS, slette alle de data, der er indsat ved den forrige Kør:
```
EXECUTE Demo.usp_DemoReset;
```

2. Redigere ostress.exe kommandolinjen for at erstatte alle *_inmem* med *_ondisk*.

3. Kør ostress.exe for anden gang, og registrere varighed resultatet.

4. Nulstille databasen, ansvarlig sletning af det kan være en stor mængde testdata igen.


#### <a name="expected-comparison-results"></a>Forventede sammenligningsresultater

Vores i hukommelsen test har vises en **9 gange** forbedring af ydeevnen for denne simplistic arbejdsbyrde med ostress, der kører på en Azure VM i det samme Azure område som databasen.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Installere på hukommelse Analytics-eksempel


I dette afsnit, skal sammenligne du EY og statistik resultaterne, når du bruger et columnstore indeks kontra en traditionel b-træet indeks.


For realtid analyser på en OLTP arbejdsbelastningen er det ofte bedst at bruge et ikke-grupperede columnstore indeks. Yderligere oplysninger finder du [Columnstore indeks beskrevet](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Forberede columnstore analytics-test


1. Bruge Azure-portalen til at oprette et nyt AdventureWorksLT database fra eksemplet.
 - Bruge det nøjagtige navn.
 - Vælg en hvilken som helst Premium webtjenesten.

2. Kopiere [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) til Udklipsholder.
 - T-SQL-script opretter de nødvendige i hukommelse objekter i AdventureWorksLT Northwind du oprettede i trin 1.
 - Scriptet opretter tabellen Dimension og to faktatabeller. Faktatabellerne udfyldes med 3.5 million rækker.
 - Scriptet kan tage 15 minutter at gennemføre.

3. Indsæt scriptet T-SQL i SSMS, og udfør derefter scriptet.
 - Afgørende er nøgleordet **COLUMNSTORE** på en sætningen **CREATE INDEX** , som i:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Du kan indstille AdventureWorksLT til kompatibilitetsniveau 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Niveau 130 ikke er direkte relateret til i hukommelsen funktioner. Men niveau 130 Generelt giver hurtigere forespørgselsydelse end 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Afgørende tabeller og columnstore indeks


- dbo. FactResellerSalesXL_CCI er en tabel, der indeholder et grupperet **columnstore** indeks, som har avancerede komprimering på *niveauet for* .

- dbo. FactResellerSalesXL_PageCompressed er en tabel, der har en tilsvarende normalt grupperet indeks, der er komprimeret kun på niveauet for *siden* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Afgørende forespørgsler til at sammenligne columnstore indekset


[Her](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) er flere typer på T-SQL-forespørgsel kan du køre at se forbedring af ydeevnen. Fra trin 2 i T-SQL-script er der et par af forespørgsler, der er direkte interessante. De to forespørgsler er forskellige kun på én linje:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Et grupperet columnstore indeks er på tabellen FactResellerSalesXL**_CCI** .

Følgende T-SQL-script uddrag udskriver statistik for EY og klokkeslæt for forespørgslen for hver tabel.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>I hukommelsen OLTP Preview overvejelser i forbindelse med


Funktionerne i hukommelse OLTP i Azure SQL-Database blev [aktive til preview på oktober 28 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


I den aktuelle preview understøttes i hukommelsen OLTP kun for:

- Databaser, der er en *Premium* service lag.

- Databaser, der er oprettet efter funktionerne i hukommelse OLTP blev aktive.
 - En ny database understøtter ikke i hukommelsen OLTP, hvis det er gendannet fra en database, der blev oprettet, inden funktionerne i hukommelse OLTP fik aktive.


Når du er i tvivl, kan du altid køre følgende T-SQL Vælg at fastslå, om din database understøtter i hukommelsen OLTP. Et resultat af **1** betyder, at databasen understøtter i hukommelsen OLTP:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Hvis forespørgslen returnerer **1**, i hukommelsen OLTP understøttes i denne database og en databasekopi og database Gendan oprettet baseret på denne database.


#### <a name="objects-allowed-only-at-premium"></a>Objekter, der er tilladt kun på Premium


Hvis en database indeholder et af følgende typer af i hukommelsen OLTP objekter eller typer, understøttes nedgradere webtjenesten af databasen fra Premium til grundlæggende eller Standard ikke. Hvis du vil nedgradere databasen, du først slippe disse objekter:

- Hukommelse optimeret tabeller
- Tabeltyper af hukommelse optimeret
- Indbygget kompileret moduler


#### <a name="other-relationships"></a>Andre relationer


- Ved hjælp af i hukommelsen OLTP funktioner med databaser i elastiske grupper understøttes ikke i Preview.
 - Hvis du vil flytte en database, der er eller har været i hukommelsen OLTP objekter til en elastiske puljen, skal du følge disse trin:
  - 1. Slip en hvilken som helst hukommelse optimeret tabeller, tabeltyper og indbygget kompileret T-SQL-moduler i databasen
  - 2. Ændre webtjenesten af databasen til standard
  - 3. Flytte databasen til elastiske puljen

- Ved hjælp af i hukommelsen OLTP med SQL Data Warehouse understøttes ikke.
 - Funktionen columnstore indeks i i hukommelsen Analytics understøttes i SQL Data Warehouse.

- Forespørgsel Store registrere ikke forespørgsler indenfor kompileret oprindeligt moduler.

- Nogle Transact-SQL-funktioner understøttes ikke med i hukommelsen OLTP. Dette gælder for både Microsoft SQL Server og Azure SQL-Database. Yderligere oplysninger finder du:
 - [Transact-SQL-understøttelse af i hukommelsen OLTP](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Transact-SQL-skemakonstruktioner, der ikke understøttes af i hukommelsen OLTP](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Næste trin


- Prøv [Brug i hukommelsen OLTP i et eksisterende Azure SQL-program.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Yderligere ressourcer

#### <a name="deeper-information"></a>Tillader en mere dybdegående oplysninger

- [Få mere at vide om i hukommelsen OLTP, som gælder for både Microsoft SQL Server og Azure SQL-Database](http://msdn.microsoft.com/library/dn133186.aspx)

- [Få mere at vide om realtid funktionsdygtige Analytics på MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Hvidbog på [almindelige arbejdsbelastningen mønstre og overvejelser i forbindelse med overflytning](http://msdn.microsoft.com/library/dn673538.aspx), som beskriver arbejdsbelastningen mønstre i hukommelsen OLTP indeholder hvor ofte betydeligt ydeevne.

#### <a name="application-design"></a>Programmet design

- [Hukommelse OLTP (i hukommelsen optimering)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Brug i hukommelsen OLTP i et eksisterende Azure SQL-program.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Værktøjer

- [SQL Server Data Tools Preview (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), efter den seneste månedlige version.

- [Beskrivelse af genafspilning Markup Language (RML) værktøjer til SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Overvåge Hukommelseslager](sql-database-in-memory-oltp-monitoring.md) til i hukommelsen OLTP.

