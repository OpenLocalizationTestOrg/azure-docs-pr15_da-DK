<properties
   pageTitle="Indlæse data fra SQL Server til Azure SQL Data Warehouse (PolyBase) | Microsoft Azure"
   description="Bruger bcp til at eksportere data fra SQL Server til flad filer, AZCopy til at importere data til Azure blob-lager og PolyBase til indtager dataene til Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-with-polybase-in-sql-data-warehouse"></a>Indlæse data med PolyBase i SQL Data Warehouse

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [BCP](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Dette selvstudium viser, hvordan indlæse data til SQL Data Warehouse ved hjælp af AzCopy og PolyBase. Når du er færdig, du, hvordan du:

- Brug AzCopy til at kopiere data til Azure blob-lager
- Oprette databaseobjekter for at definere dataene
- Køre en T-SQL-forespørgsel for at genindlæse dataene

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## <a name="prerequisites"></a>Forudsætninger

Hvis du vil gennemgå dette selvstudium, skal du

- En SQL Data Warehouse database.
- En Azure lagerplads konto af typen Standard lokalt overflødige lagerplads (Standard-LRS), Standard geografisk overflødigt lagerplads (Standard-GRS) eller Standard læseadgang geografisk overflødigt lagerplads (Standard-RAGRS).
- AzCopy kommandolinjen Utility. Hente og installere den [nyeste version af AzCopy][] , der er installeret med værktøjerne Microsoft Azure-lager.

    ![Azure-lager værktøjer](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## <a name="step-1-add-sample-data-to-azure-blob-storage"></a>Trin 1: Tilføje eksempeldata til Azure blob-lager

For at indlæse data, har vi brug at sætte nogle eksempeldata i en Azure blob-lager. I dette trin udfylde vi en Azure-lager blob med eksempeldata. Senere, skal vi bruge PolyBase indlæse denne eksempeldata til databasen SQL Data Warehouse.

### <a name="a-prepare-a-sample-text-file"></a>A. Forberede en eksempelfil tekst

Sådan forbereder du dig en eksempelfil tekst:

1. Åbn Notesblok, og Kopiér følgende linjer af data til en ny fil. Gemme den i mappen lokale temp som % temp%\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

### <a name="b-find-your-blob-service-endpoint"></a>B. Finde din blob tjenesteslutpunkt

Sådan finder du din blob tjenesteslutpunkt:

1. Vælg **Gennemse**fra portalen Azure > **Lagerplads konti**.
2. Klik på kontoen lagerplads, du vil bruge.
3. Klik på BLOB i bladet lagerplads konto

    ![Klik på BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Gemme din blob slutpunkt URL-adressen til senere.

    ![BLOB tjenesteslutpunkt](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### <a name="c-find-your-azure-storage-key"></a>C. Find produktnøglen Azure-lager

Sådan finder produktnøglen Azure-lager:

1. Fra Azure-portalen, Vælg **Gennemse** > **Lagerplads konti**.
2. Klik på kontoen lagerplads, du vil bruge.
3. Vælg **Indstillinger for alle** > **Access-taster**.
4. Klik på feltet kopi for at kopiere en af dine access-taster til Udklipsholder.

    ![Kopiere Azure lagerplads nøgle](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### <a name="d-copy-the-sample-file-to-azure-blob-storage"></a>D. Kopiere eksempelfilen til Azure blob-lager

Kopiere dataene til Azure blob-lager:

1. Åbn en kommandoprompt, og skifte til mappen AzCopy installation. Denne kommando ændres til standardmappen for installation på en 64-bit Windows-klient.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Kør følgende kommando for at overføre filen. Angiv din blob slutpunkt URL-adressen til <blob service endpoint URL> og din Azure lagerplads kontonøgle for < azure_storage_account_key >.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Se også [Komme i gang med AzCopy kommandolinjeparametre Utility][seneste version af AzCopy].

### <a name="e-explore-your-blob-storage-container"></a>E. Udforske dine blob storage objektbeholder

Hvis du vil se fil, du har overført til blob-lager:

1. Gå tilbage til din Blob service blade.
2. Dobbeltklik på **datacontainer**under beholdere.
3. Klik på mappen **datedimension** for at udforske stien til dine data, og du får vist overførte filen **DimDate2.txt**.
4. For at få vist egenskaber, skal du klikke på **DimDate2.txt**.
5. Bemærk, at i bladet Blob egenskaber, du kan hente eller slette filen.

    ![Vis Azure lagerplads blob](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## <a name="step-2-create-an-external-table-for-the-sample-data"></a>Trin 2: Oprette en ekstern tabel for eksempeldataene

I dette afsnit oprette vi en ekstern tabel, der definerer eksempeldataene.

PolyBase bruger eksterne tabeller til at få adgang til data i Azure blob-lager. Da dataene ikke gemmes i SQL Data Warehouse, håndterer PolyBase godkendelse til de eksterne data ved hjælp af en database-fastsat legitimationsoplysninger.

Eksempel i dette trin bruges disse Transact-SQL-sætninger til at oprette en ekstern tabel.

- [Oprette Master Key (Transact-SQL)][] til at kryptere hemmeligt af databasen fastsat legitimationsoplysninger.
- [Oprette databasen fastsat legitimationsoplysninger (Transact-SQL)][] til at angive godkendelsesoplysninger for kontoen Azure-lager.
- [Opret eksterne datakilde (Transact-SQL)][] for at angive placeringen af din Azure blob-lager.
- [Oprette eksterne-filformat (Transact-SQL)][] til at angive formatet for dine data.
- [Opret eksterne tabel (Transact-SQL)][] til at angive tabeldefinition og placeringen af data.

Kør denne forespørgsel i forhold til databasen SQL Data Warehouse. Det opretter en ekstern tabel med navnet DimDate2External i dbo skemaet, der peger på DimDate2.txt eksempeldata i Azure blob-lager.


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


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```


I SQL Server Object Explorer i Visual Studio, kan du se det eksterne filformat, ekstern datakilde og tabellen DimDate2External.

![Vis ekstern tabel](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## <a name="step-3-load-data-into-sql-data-warehouse"></a>Trin 3: Indlæse data til SQL Data Warehouse

Når den eksterne tabel er oprettet, kan du indlæser data til en ny tabel eller indsætte den i en eksisterende tabel.

- For at indlæse dataene i en ny tabel, du Kør sætningen [Oprette tabellen AS Vælg (Transact-SQL)][] . Den nye tabel har kolonnerne med navnet i forespørgslen. Datatyperne for kolonnerne, der matcher datatyper i den eksterne tabeldefinition.
- Brug [Indsæt for at indlæse dataene i en eksisterende tabel... Vælg (Transact-SQL)][] sætning.

```sql
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```

## <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Trin 4: Oprette statistik på dine nyligt indlæst data

SQL Data Warehouse ikke automatisk oprettelse af eller automatisk opdatering statistik. Derfor for at opnå høj forespørgselsydelse, er det vigtigt at oprette statistik på hver kolonne med alle tabeller, efter den første belastning. Det er også vigtigt at opdatere statistik efter væsentlige ændringer i dataene.

I dette eksempel opretter enkelt kolonne statistik på den nye DimDate2 tabel.

```sql
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
CREATE STATISTICS [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Få vist [Statistik][]for at få mere for at vide.  


## <a name="next-steps"></a>Næste trin
Se [PolyBase vejledning][] til yderligere oplysninger, du bør vide, når du udvikler en løsning, der bruger PolyBase.

<!--Image references-->


<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[PolyBase vejledning]: ./sql-data-warehouse-load-polybase-guide.md
[seneste version af AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[OPRETTE eksterne DATAKILDE (Transact-SQL)]:https://msdn.microsoft.com/library/dn935022.aspx
[OPRETTE eksterne FILFORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/dn935026.aspx
[OPRETTE eksterne tabel (Transact-SQL)]:https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]:https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]:https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]:https://msdn.microsoft.com/library/mt130698.aspx

[OPRETTE tabel som Vælg (Transact-SQL)]:https://msdn.microsoft.com/library/mt204041.aspx
[INDSÆT... Vælg (Transact-SQL)]:https://msdn.microsoft.com/library/ms174335.aspx
[OPRETTE MASTER KEY (Transact-SQL)]:https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189522.aspx
[OPRETTE databasen fastsat LEGITIMATIONSOPLYSNINGER (Transact-SQL)]:https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]:https://msdn.microsoft.com/library/ms189450.aspx
