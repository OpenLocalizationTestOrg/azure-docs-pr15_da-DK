<properties
   pageTitle="Vejledning til brug af PolyBase i SQL Data Warehouse | Microsoft Azure"
   description="Retningslinjer for og anbefalinger til brug af PolyBase i SQL Data Warehouse scenarier."
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
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Vejledning til brug af PolyBase i SQL Data Warehouse

Denne vejledning indeholder praktiske oplysninger om brug af PolyBase i SQL Data Warehouse.

For at komme i gang skal du se [Indlæs data med PolyBase][] selvstudiet.


## <a name="rotating-storage-keys"></a>Rotere lagerplads taster

Fra tid til anden vil du ændre nøglen adgang til din blob-lager af sikkerhedsmæssige årsager.

Den mest elegante måde at udføre denne opgave er at følge en proces, kaldet "rotere tasterne". Du har muligvis bemærket, at du har to lagerplads nøgler til kontoen blob-lager. Dette er, så du kan udføre overgangen

Rotere dine Azure lagerplads konto nøgler er en proces enkel tre trin

1. Oprette anden database, der er fastsat legitimationsoplysninger, der er baseret på hurtigtast sekundær lagerplads
2. Oprette en anden ekstern datakilde, der er baseret på denne nye legitimationsoplysninger
3. Slip og oprette den eksterne tabeller, der peger på den eksterne datakilde

Når du har overført alle eksterne tabeller til den eksterne datakilde og derefter kan du udføre kan oprydningen opgaver:

1. Slip første ekstern datakilde
2. Slip første database fastsat legitimationsoplysninger, der er baseret på hurtigtast primære lagerplads
3. Log på Azure og for at gendanne den primære hurtigtast er du klar til næste gang

## <a name="query-azure-blob-storage-data"></a>Azure blob storage forespørgselsdata
Forespørgsler eksterne tabeller blot bruge navnet på tabel, som hvis det var en relationel tabel.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] En forespørgsel på en ekstern tabel kan mislykkes med fejlen *"Forespørgsel afbrudt – det maksimale antal afvise grænseværdi er nået under læsning fra en ekstern kilde"*. Dette angiver, at dine eksterne data indeholder *snavset* poster. En datapost betragtes 'snavset', hvis den faktiske data typer/antallet af kolonner ikke stemmer overens kolonnedefinitioner af den eksterne tabel, eller hvis dataene ikke er i overensstemmelse med det angivne eksterne filformat. Sikre, at din ekstern tabel og en ekstern fil format definitioner er korrekte, og dine eksterne data er i overensstemmelse med disse definitioner for at løse dette problem. I tilfælde af et undersæt af eksterne dataposter er beskadiget, kan du vælge at afvise disse poster til dine forespørgsler ved hjælp af indstillingerne for Afvis i oprette eksterne tabel DDL.


## <a name="load-data-from-azure-blob-storage"></a>Indlæse data fra Azure blob-lager
I dette eksempel indlæser data fra Azure blob-lager til SQL Data Warehouse database.

Lagring af data direkte fjerner data filoverførsel tid til forespørgsler. Lagring af data med et columnstore indeks forbedrer forespørgselsydelse for analyse forespørgsler ved op til 10 x.

I dette eksempel bruges oprette tabel som SELECT-sætningen indlæse data. Den nye tabel arver de kolonner i forespørgslen. Datatyperne for kolonnerne arver fra den eksterne tabeldefinition.

OPRETTE tabellen AS Vælg er en meget performant Transact-SQL-sætning, der indlæser data parallelt til alle Beregn knudepunkter på din SQL Data Warehouse.  Det blev oprindeligt blev udviklet til stort omfang parallel behandling (MPP)-program i analysesystem Platform og er nu i SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Se [oprette tabel som Vælg Transact-SQL ()][].

## <a name="create-statistics-on-newly-loaded-data"></a>Oprette statistik på nyligt indlæst data

Azure SQL Data Warehouse understøtter endnu ikke er support automatisk oprette eller automatisk opdatering statistik.  Hvis du vil have den bedste ydeevne fra dine forespørgsler, det er vigtigt, at statistik oprettes på alle kolonner i alle tabeller efter den første belastning eller væsentlige ændringer forekommer i dataene.  Finde en detaljeret forklaring af statistik under emnet [Statistik][] i gruppen udvikling af emner.  Nedenfor finder du en hurtig eksempel på, hvordan du opretter statistik på den tabled indlæst i dette eksempel.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Eksportere data til Azure blob-lager
Dette afsnit viser, hvordan du eksporterer data fra SQL Data Warehouse til Azure blob-lager. I dette eksempel bruges oprette eksterne tabel som Vælg, hvilket er en meget performant Transact-SQL-sætning at eksportere data parallelt fra alle Beregn noder.

I følgende eksempel oprettes en ekstern tabel Weblogs2014 ved hjælp af kolonnedefinitioner og data fra dbo. Weblogs tabel. Den eksterne tabeldefinition er gemt i SQL Data Warehouse og resultaterne af SELECT-sætningen eksporteres til mappen "/ arkivere/log2014 /" under objektbeholderen blob, der er angivet af datakilden. Dataene er eksporteret i det angivne tekst-filformat.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Arbejde omkring kravet PolyBase UTF-8
Understøtter indlæsning datafiler, der er blevet UTF-8-kodet på Præsenter PolyBase. Som UTF-8 bruger den samme tegnkodning som ASCII-PolyBase også vil support indlæsning data, som er ASCII-kodet. Dog PolyBase understøtter ikke andre tegnkodning som UTF-16 / Unicode eller udvidede ASCII-tegn. Udvidet ASCII omfatter tegn med accenter som omlyd, som er fælles for tysk.

Du kan løse dette krav er det bedste svar til at skrive igen til UTF-8-kodning.

Der er flere måder at gøre dette. Herunder finder du ved hjælp af Powershell en af følgende fremgangsmåder:

### <a name="simple-example-for-small-files"></a>Simpelt eksempel for små filer

Nedenfor finder du en simpel én linje Powershell-script, som opretter en fil.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Mens dette er en nem måde at igen kode dataene er det dog ikke på nogen måde den mest effektive. EY streaming eksemplet nedenfor er meget, meget hurtigere og opnår det samme resultat.

### <a name="io-streaming-example-for-larger-files"></a>EY Streaming eksempel for større filer

Kodeeksemplet nedenfor, er mere kompleks, men som den streamer rækkerne af data fra kilder målrette det er meget mere effektivt. Brug denne fremgangsmåde for større filer.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Næste trin
Hvis du vil vide mere om at flytte data til SQL Data Warehouse, skal du se [Oversigt over dataoverførsel][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Indlæse data med PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistik]: ./sql-data-warehouse-tables-statistics.md
[Oversigt over dataoverførsel]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Opret eksterne-FILFORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026) .aspx [Opret ekstern tabel (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[OPRETTE tabel som Vælg (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
