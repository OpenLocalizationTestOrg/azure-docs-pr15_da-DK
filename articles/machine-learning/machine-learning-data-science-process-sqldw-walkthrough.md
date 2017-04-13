<properties
    pageTitle="Team Data videnskabelige processen i praksis: Brug af SQL Data Warehouse | Microsoft Azure"
    description="Avanceret analyse proces og teknologi i praksis"  
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Team Data videnskabelige processen i praksis: Brug af SQL Data Warehouse

I dette selvstudium hjælper vi dig med at opbygge og implementere en machine learning-model ved hjælp af SQL Data Warehouse (SQL DW) for et offentligt tilgængeligt dataset – [NYC Taxi rejser](http://www.andresmh.com/nyctaxitrips/) datasættet. Binær klassifikation modellen opbygget beregner eller ej et tip betales for en rejse og modeller til multiclass klassificering og regression er også som gennemgået, forudsige fordelingen for de udbetalte beløb tip.

Fremgangsmåden følger arbejdsprocessen [Team Data videnskabelige proces (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Vi viser, hvordan du konfigurerer et data videnskabelige miljø, hvordan du indlæser data til SQL DW, og hvordan bruger SQL DW eller en IPython notesbog til at udforske data og engineering funktioner, der model. Vi viser derefter, hvordan du opbygge og anvende en model med Azure Machine Learning.


## <a name="dataset"></a>NYC Taxi rejser datasættet

Datatypen NYC Taxi forretningsrejse består af om 20GB komprimeret csv-filer (~ 48GB ikke-komprimeret format), over 173 millioner individuelle rejser og priser betalt for hver forretningsrejse. Hver forretningsrejse post indeholder afhentning og indlevering placeringer og gange, anonyme hacke (driver) licens tal og tallet Medaillon (taxi's entydige id). Data, der dækker alle rejser år 2013 og fremgår af følgende to datasæt for hver måned:

1. Filen **trip_data.csv** indeholder forretningsrejse oplysninger, som antallet af passagerer, afhentning og dropoff punkter, forretningsrejse varighed og forretningsrejse længde. Her er nogle eksempler på poster:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Filen **trip_fare.csv** indeholder oplysninger om den pris, som er betalt for hver tur som betalingstype, pris beløb, tillæg og moms, tip og vej, og det samlede betalte beløb. Her er nogle eksempler på poster:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

**Entydig nøgle** , der bruges til at deltage i forretningsrejse\_data og forretningsrejse\_pris består af følgende tre felter:

- Medaillon,
- dermed få\_licens og
- afhentning\_datetime.

## <a name="mltasks"></a>Adresse til tre typer forudsigelse opgaver

Vi udforme tre forudsigelse problemer, der er baseret på den *tip\_beløb* til at illustrere tre typer af modeling opgaver:

1. **Binær klassifikation**: til at forudsige hvorvidt et tip er betalt for en rejse, det vil sige en *tip\_beløb* , der er større end $0 er et positivt eksempel, mens en *tip\_beløb* på $0 er et negativt eksempel.

2. **Multiclass klassifikation**: til at forudsige celleområde, tip, der er betalt for forretningsrejse. Vi dividere de *tip\_beløb* til fem placeringer eller klasser:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Regression opgave**: til at forudsige mængden tip, der er betalt for en rejse.  


## <a name="setup"></a>Konfigurere Azure data videnskabelige miljø til avanceret analyse

For at konfigurere dit miljø Azure Data videnskabelige skal du følge disse trin.

**Oprette din egen Azure blob-lager**

- Når du klargør dine egne Azure blob-lager, Vælg en geografisk placering for din Azure blob-lager i, eller så tæt som muligt at **Syd centrale USA**, som er hvor NYC Taxi data er gemt. Dataene, der skal kopieres ved hjælp af AzCopy fra offentlige blob storage beholderen til en objektbeholder i din egen lagerplads konto. Jo nærmere Azure blob-lager er med syd Central os, jo hurtigere denne opgave (trin 4) være fuldført.
- Følg trinnene beskrevet på [om Azure lagerplads konti](../storage/storage-create-storage-account.md)for at oprette din egen Azure-lager-konto. Husk at skrive noter på værdier for følgende lagerplads kontolegitimationsoplysninger, som de skal bruges senere i denne gennemgang.

  - **Kontonavn til lager**
  - **Lagerplads Kontonøgle**
  - **Navnet på objektbeholderen** (som du vil dataene skal gemmes i Azure blob-lager)

**Klargør din Azure SQL DW forekomst.**
Følg i dokumentationen på [Opret et SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) til at klargøre en forekomst af SQL Data Warehouse. Sørg for, at du foretager notation på de følgende SQL Data Warehouse legitimationsoplysninger, der bruges i efterfølgende trin.

  - **Servernavn**: <server Name>. database.windows.net
  - **SQLDW (Database) navn**
  - **Brugernavn**
  - **Adgangskode**

**Installer Visual Studio-2015 og SQL Server Data Tools.** Flere oplysninger under [installere Visual Studio-2015 og/eller SSDT (SQL Server Data Tools) for SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Oprette forbindelse til din Azure SQL-DW med Visual Studio.** Flere oplysninger under trin 1 og 2 i [Opret forbindelse til Azure SQL Data Warehouse med Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Kør følgende SQL-forespørgslen på den database, du oprettede i dine SQL Data Warehouse (i stedet for den forespørgsel, der er angivet i trin 3 i emnet forbinde) til at **oprette en master-nøgle**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Oprette et Azure Machine Learning arbejdsområde under abonnementet Azure.** Flere oplysninger under [oprette et Azure Machine Learning arbejdsområde](machine-learning-create-workspace.md).

## <a name="getdata"></a>Indlæs data til SQL Data Warehouse

Åbn en Windows PowerShell-kommandokonsol. Køre følgende PowerShell kommandoer til at hente eksempel SQL script filer, som vi deler med dig på Github til en lokal mappe, du angiver med parameteren *- DestDir*. Du kan ændre værdien af parameter *-DestDir* til en lokal mappe. Hvis *- DestDir* ikke findes, oprettes ved hjælp af PowerShell-script.

>[AZURE.NOTE] Du muligvis på **Kør som Administrator** , når du kører følgende PowerShell-script, hvis adresselisten *DestDir* kræver administratorrettigheder for at oprette eller skrive til den.

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Efter vellykket udførelse ændres din aktuelle arbejdsmappe til *- DestDir*. Du skal kunne se skærmbillede som nedenfor:

![][19]

Udfør følgende PowerShell-script i administratortilstand i din *-DestDir*:

    ./SQLDW_Data_Import.ps1

Når PowerShell-script kører for første gang, bliver du bedt om at Skriv oplysninger fra din Azure SQL-DW og kontoen Azure blob-lager. Når denne PowerShell-script er fuldført der kører for første gang, legitimationsoplysningerne du input er skrevet til en konfigurationsfil SQLDW.conf i den nuværende arbejdsmappe. Den fremtidige kørsel af denne PowerShell-scriptfil har mulighed for at læse alle behov parametre fra denne konfigurationsfil. Hvis du har brug for til at ændre nogle parametre, kan du vælge at inputparametre på skærmen på prompten ved at slette denne konfigurationsfil og indtastning af parameterværdier, som du bliver bedt om eller ændre parameterværdier ved at redigere filen SQLDW.conf i mappen *- DestDir* .

>[AZURE.NOTE] For at undgå skema navnet er i konflikt med dem, der allerede findes i din Azure SQL-DW, når du læser parametre direkte fra filen SQLDW.conf, føjes et tilfældigt tal 3-cifret til skemanavnet på fra filen SQLDW.conf som standard skemanavnet på hver Kør. PowerShell-script kan beder dig om et skemanavn: Navnet kan angives efter bruger skøn.

Denne **PowerShell-script** -fil er fuldført følgende opgaver:

- **Downloader og installerer AzCopy**, hvis AzCopy ikke allerede er installeret

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Kopier data til din private blob storage konto** fra offentlige blob med AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Indlæse data ved hjælp af Polybase (ved at udføre LoadDataToSQLDW.sql) til din Azure SQL-DW** fra din private blob storage konto med følgende kommandoer.

    - Oprette et skema

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Oprette en database, der er fastsat legitimationsoplysninger

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Oprette en ekstern datakilde for en Azure lagerplads blob

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Oprette et eksternt filformat til en CSV-fil. Data er dekomprimerede og felter er adskilt med tegnet.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Oprette eksterne pris og forretningsrejse tabeller for NYC taxi datasæt i Azure blob-lager.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Indlæser data fra eksterne tabeller i Azure blob-lager til SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Oprette en datatabel med eksempel (NYCTaxi_Sample) og indsætte data til den fra valg af SQL-forespørgsler på tabellerne forretningsrejse og pris. (Nogle trinnene i denne gennemgang skal bruge denne eksempeltabel).

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

Geografiske placering af kontiene lagerplads påvirker indlæsningstider.

>[AZURE.NOTE] Afhængigt af den geografiske placering af kontoen privat blob-lager processen med at kopiere data fra en offentlig blob til kontoen privat lagerplads kan tage 15 minutter, eller endda længere, og processen for at indlæse data fra din lagerplads konto til din Azure SQL-DW kan tage 20 minutter eller længere.  

Du skal beslutte hvilken gør, hvis du har dublerede kilde- og filer.

>[AZURE.NOTE] Hvis .csv filer, der skal kopieres fra offentlige blob-lager til kontoen privat blob storage findes allerede i din private blob storage konto, AzCopy beder dig om du vil overskrive dem.. Hvis du ikke vil overskrive dem, indtast **n** , når du bliver bedt om. Hvis du vil overskrive **alle** dem, indtast **et** når du bliver bedt om. Du kan også input **y** for at overskrive .csv-filer enkeltvis.

![Afbilde #21][21]

Du kan bruge dine egne data. Hvis dine data er i computeren lokalt i virkelige liv programmet, kan du stadig bruge AzCopy til at overføre lokalt data til din private Azure blob-lager. Du kun vil ændre **billedets kildeplacering** `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, i kommandoen AzCopy af PowerShell-script filerne på den lokale mappe, der indeholder dine data.


>[AZURE.TIP] Hvis dine data er allerede i din private Azure blob-lager i virkelige liv programmet, kan du springe AzCopy i PowerShell-script og overføre dataene direkte til Azure SQL DW. Dette kræver yderligere ændringer af scriptet til at skræddersy i formatet af dine data.


Denne Powershell-script også tilsluttes oplysninger, Azure SQL DW-udforske eksempel datafiler SQLDW_Explorations.sql, SQLDW_Explorations.ipynb og SQLDW_Explorations_Scripts.py så disse tre filer er klar til at blive forsøgt med det samme efter PowerShell-script er fuldført.

Efter vellykket udførelse, får du vist skærmen som nedenfor:

![][20]

## <a name="dbexplore"></a>Udforske data og funktion teknisk i Azure SQL Data Warehouse

I dette afsnit foretager vi undersøgelse af data og oprettelse af funktion ved at køre SQL-forespørgsler mod Azure SQL DW direkte ved hjælp af **Visual Studio Data Tools**. Alle SQL-forespørgsler, der bruges i dette afsnit kan findes i eksempelscriptet med navnet *SQLDW_Explorations.sql*. Denne fil er allerede blevet overført til din lokale mappe ved hjælp af PowerShell-script. Du kan også hente det fra [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Men filen i Github har ikke de Azure SQL DW oplysninger, der er tilsluttet.

Oprette forbindelse til din Azure SQL-DW ved hjælp af Visual Studio med SQL DW login-navn og din adgangskode, og Åbn op **SQL Object Explorer** for at bekræfte den database og tabeller er blevet importeret. Hent filen *SQLDW_Explorations.sql* .

>[AZURE.NOTE] For at åbne en Parallel Data Warehouse (PDW) forespørgselseditor skal du bruge kommandoen **Ny forespørgsel** , mens din PDW er markeret i **SQL Object Explorer**. Standard SQL forespørgselseditor understøttes ikke af PDW.

Her er typen data udforske og funktion generering af opgaver, der er udført i dette afsnit:

- Udforske data fordeling af nogle felter i forskellige tid windows.
- Undersøge datakvaliteten af felterne breddegrader og længde.
- Generere binære og multiclass klassifikation etiketter, der er baseret på den **tip\_beløb**.
- Generere funktioner, og Beregn/Sammenlign forretningsrejse afstand.
- Deltag i de to tabeller og udtrække en stikprøve, der skal bruges til at oprette modeller.

### <a name="data-import-verification"></a>Import af forretningsdata bekræftelsen

Disse forespørgsler giver en hurtig kontrol af antallet af rækker og kolonner i tabellerne udfyldt tidligere ved hjælp af Polybases parallelle masseimport importerer,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Output:** Du bør anskaffe 173,179,759 rækker og 14 kolonner.

### <a name="exploration-trip-distribution-by-medallion"></a>Udforske: Forretningsrejse fordeling af Medaillon

Dette eksempel på forespørgsel identificerer medallions (taxi tal), fuldført mere end 100 rejser inden for en given tidsperiode. Forespørgslen ville få glæde af partitioneret tabel access, da det er betinget af partition ordning af **afhentning\_datetime**. Forespørgsler hele datasættet vil også gøre brug af tabellen partitioneret og/eller indeksere scanningen.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Output:** Forespørgslen skal returnere en tabel med rækker, der angiver de 13,369 medallions (taxier) og antallet af forretningsrejse fuldført af dem i 2013. Den sidste kolonne indeholder antallet af antallet rejser fuldført.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Udforske: Forretningsrejse fordeling ved Medaillon og hack_license

I dette eksempel identificerer medallions (taxi tal) og hack_license tallene (drivere), fuldført mere end 100 rejser inden for en given tidsperiode.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Output:** Forespørgslen skal returnere en tabel med 13,369 rækker, der angiver den 13,369 bil/driver-id'er, der har fuldført flere pågældende 100 rejser i 2013. Den sidste kolonne indeholder antallet af antallet rejser fuldført.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Vurdering af kvalitet: bekræfte poster med forkerte breddegrader og/eller længde

I dette eksempel undersøger, hvis et af felterne breddegrader og/eller længde enten indeholder en ugyldig værdi (radian grader skal være mellem -90 og 90), eller har (0; 0) koordinater.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Output:** Forespørgslen returnerer 837,467 rejser, der har ugyldige breddegrader og/eller længde felter.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Udforske: Vippet kontra ikke Vippet rejser fordeling

I dette eksempel søger efter antallet rejser, der blev Vippet kontra tal, der ikke blev Vippet i en bestemt tidsperiode (eller i hele datasættet Hvis dækker det fulde år, som den er konfigureret her). Denne fordeling afspejler den binære etiket fordeling bruges senere til binær klassifikation modellering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Output:** Forespørgslen skal returnere de følgende tip hyppigheden for året 2013: 90,447,622 Vippet og ikke-Vippet 82,264,709.

### <a name="exploration-tip-classrange-distribution"></a>Udforske: Tip klasse/område fordeling

I dette eksempel beregner fordelingen af tip områder i en given tidsperiode (eller i hele datasættet Hvis dækker hele året). Dette er fordelingen af de etiket klasser, der skal bruges senere til multiclass klassifikation modellering.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Output:**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Udforske: Beregne og sammenligne forretningsrejse afstand

I dette eksempel konverterer afhentning og indlevering breddegrader og længde til SQL Geografi peger, beregner forretningsrejse afstanden ved hjælp af SQL Geografi punkter forskellen og returnerer en stikprøve af resultaterne for sammenligning. Eksemplet begrænser resultaterne til gyldige koordinater kun ved hjælp af forespørgslen data kvalitet vurdering dækket tidligere.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Funktionen teknisk ved hjælp af SQL-funktioner

Nogle gange kan SQL-funktioner være en effektiv indstilling for teknisk funktion. I denne gennemgang definerede vi en SQL-funktion for at beregne direkte afstanden mellem afhentning og dropoff placeringerne. Du kan køre følgende SQL-scripts i **Visual Studio Data Tools**.

Her er den SQL-script, der definerer funktionen afstand.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Her er et eksempel til at ringe til denne funktion til at generere funktioner i SQL-forespørgsel:

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Output:** Denne forespørgsel genererer en tabel (med 2,803,538 rækker) med afhentning- og dropoff bredde- og længdegrader og tilsvarende direkte afstandene i sømil. Her er resultaterne for første 3 rækker:

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Forbered dataene til model dokumentkomponent

Følgende forespørgsel joinforbindelser på **nyctaxi\_forretningsrejse** og **nyctaxi\_pris** tabeller, genererer en binær klassifikation etiket **Vippet**, en etiket med flere klasse klassifikation **tip\_klasse**, og henter en stikprøve fra den fulde joinforbundne datasæt. Stikprøverne gøres ved at hente et undersæt af rejser baseret på enhed tid.  Denne forespørgsel kan kopieres og indsættes direkte i [Azure Machine Learning Studio](https://studio.azureml.net) [Importdata] [ import-data] -modul til indtagelse direkte data fra SQL database-forekomsten i Azure. Forespørgslen ikke indeholder poster med forkerte (0; 0) koordinater.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Når du er klar til at fortsætte til Azure maskine undervisning, kan du enten:  

1. Gemme den endelige SQL-forespørgsel for at udtrække og de eksempeldata og kopierer indsætter forespørgslen direkte i [Importdata] [ import-data] modul i Azure Machine Learning, eller
2. Bevare indsamlede og engineering dataene du planlægger at bruge model opbygning af i en ny tabel i SQL DW og den nye tabel i [Importdata] [ import-data] modul i Azure Machine Learning. PowerShell-script i tidligere trin er gjort for dig. Du kan læse direkte fra denne tabel i modulet importdata.


## <a name="ipnb"></a>Udforske data og funktion teknisk i IPython notesbog

I dette afsnit, skal vi udfører undersøgelse af data og generering af funktion ved hjælp af begge Python og SQL-forespørgsler med SQL-DW oprettede tidligere. Et eksempel IPython notesbog, der hedder **SQLDW_Explorations.ipynb** og en Python scriptfil **SQLDW_Explorations_Scripts.py** er blevet overført til din lokale mappe. De er også tilgængelige på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Disse to filer er identiske i Python scripts. Scriptfil Python er angivet for dig, i tilfælde af, at du ikke har en server til IPython notesbog. Disse to eksempel Python filer er designet under **Python 2.7**.

Nødvendige Azure SQL DW oplysninger i stikprøven IPython notesbog og scriptfil Python overført til din lokale computer, der er tilsluttet ved hjælp af PowerShell-script tidligere. De er eksekverbare uden ændring.

Hvis du allerede har konfigureret et AzureML arbejdsområde, kan du direkte overføre stikprøvernes IPython notesbog til tjenesten AzureML IPython notesbog og begynde at køre den. Her er trinnene for at overføre til AzureML IPython notesbog-tjenesten:

1. Log på arbejdsområdet AzureML, klik på "Studio" øverst, og klik på "NOTESBØGER" i venstre side af websiden.

    ![Afbilde #22][22]

2. Klik på "Ny" i det nederste venstre hjørne af websiden, og vælg "Python 2". Derefter Angiv et navn til notesbogen, og klik på afkrydsningsfeltet for at oprette den nye tomme IPython notesbog.

    ![Afbilde #23][23]

3. Klik på "Jupyter"-symbolet på det øverste venstre hjørne af den nye IPython notesbog.

    ![Afbilde #24][24]

4. Træk og slip stikprøvernes IPython notesbog til siden **trævisning** AzureML IPython notesbog-tjenesten, og klik på **Overfør**. Derefter overføres stikprøvernes IPython notesbog til tjenesten AzureML IPython notesbog.

    ![Afbilde #25][25]

For at køre eksemplet scripts IPython notesbog eller Python filer, på følgende Python pakker er det er nødvendigt. Hvis du bruger tjenesten AzureML IPython notesbog, er disse pakker forudinstalleret.

    - Pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

Anbefalede rækkefølge, når du bygger avancerede analytical løsninger på AzureML med store data er følgende:

- Læs i et lille udvalg af dataene i en ramme i hukommelsen data.
- Udføre nogle af de visuelle effekter og udforsk undergrunden ved hjælp af indsamlede dataene.
- Eksperimentere med funktion teknisk ved hjælp af indsamlede dataene.
- Brug Python til at udstede SQL-forespørgsler direkte mod SQL DW for større undersøgelse af data, data strengmanipulation og funktion teknisk.
- Bestem stikprøvestørrelsen egnet til Azure Machine Learning model dokumentkomponent.

Følgende er nogle data udforske, datavisualisering og funktion engineering-eksempler. Udforsk undergrunden flere data kan findes i eksemplet IPython notesbog og eksempel Python script-fil.

### <a name="initialize-database-credentials"></a>Initialiseret databaselegitimationsoplysninger

Initialiseret forbindelsesindstillingerne database i følgende variabler:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Oprette databaseforbindelse

Her er den forbindelsesstreng, der opretter forbindelse til databasen.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Rapport antallet af rækker og kolonner i tabellen < nyctaxi_trip >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Samlet antal rækker = 173179759  
- Samlet antal kolonner = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Rapport antallet af rækker og kolonner i tabellen < nyctaxi_fare >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Samlet antal rækker = 173179759  
- Samlet antal kolonner = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Læs i en lille data stikprøve fra SQL Data Warehouse-databasen

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tid til at læse eksempeltabellen er 14.096495 sekunder.  
Antallet af rækker og kolonner hentet = (1000, 21).

### <a name="descriptive-statistics"></a>Beskrivende statistik

Nu er du klar til at udforske indsamlede data. Vi starter med kigge på nogle beskrivende statistik for den **forretningsrejse\_afstand** (eller eventuelle andre felter, du vil angive).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualisering: Eksempel listefelt afbildning

Næste se vi på feltet afbildning for forretningsrejse afstanden til visualisering af quantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Afbilde #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualisering: Fordeling Afbild eksempel

Områder, Visualiser fordelingen og et histogram for den indsamlede forretningsrejse afstand.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Afbilde #2][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisering: Liggende søjle- og linje plottes

I dette eksempel skal vi placering forretningsrejse afstanden i fem placeringer og visualisere binning resultaterne.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan afbilde ovenfor placering fordelingen i en bjælke eller linjen afbildning med:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Afbilde #3][3]

og

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Afbilde #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualisering: Scatterplot eksempler

Vi viser punktdiagrammer afbildning mellem **forretningsrejse\_tid\_i\_sek** og **forretningsrejse\_afstand** til at se, om der er en hvilken som helst korrelation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Afbilde #6][6]

På samme måde kan vi se forholdet mellem **rente\_kode** og **forretningsrejse\_afstand**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Afbilde #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Udforske data indsamlede data ved hjælp af SQL-forespørgsler i IPython notesbog

I dette afsnit skal nærmere vi på data salgsdistributioner ved hjælp af de indsamlede oplysninger, som er bevaret i den nye tabel, vi oprettede ovenfor. Bemærk, at lignende Udforsk undergrunden kan udføres ved hjælp af de oprindelige tabeller.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Udforske: Rapport antallet af rækker og kolonner i tabellen indsamlede

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Udforske: Vippet/ikke aktiveres fordeling

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Udforske: Tip klasse fordeling

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Udforske: Afbilde tip fordelingen af klasse

    tip_class_dist['tip_freq'].plot(kind='bar')

![Afbilde #26][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Udforske: Daglig fordelingen af rejser

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Udforske: Forretningsrejse fordeling per Medaillon

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Udforske: Forretningsrejse fordeling af Medaillon og hacke licens

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Udforske: Forretningsrejse tid fordeling

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Udforske: Forretningsrejse afstanden fordeling

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Udforske: Betaling type fordeling

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Bekræfte den endelige formular i tabellen featurized

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Oprette modeller på Azure Machine Learning

Vi er nu klar til at gå videre til model opbygning og model installation i [Azure Machine Learning](https://studio.azureml.net). Dataene er klar til at blive brugt i en af de forudsigelse problemer, der er identificeret tidligere, nemlig:

1. **Binær klassifikation**: til at forudsige hvorvidt et tip er betalt for en rejse.

2. **Multiclass klassifikation**: til at forudsige celleområde, tip, der betales ifølge de tidligere defineret klasser.

3. **Regression opgave**: til at forudsige mængden tip, der er betalt for en rejse.  



Log på arbejdsområdet **Azure maskine undervisning** for at starte modellering opgave. Hvis du endnu ikke har oprettet en maskine læ arbejdsområde, kan du se [oprette et Azure ML arbejdsområde](machine-learning-create-workspace.md).

1. For at komme i gang med Azure Machine Learning skal du se [Hvad er Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

2. Log på [Azure maskine læ Studio](https://studio.azureml.net).

3. Hjemmeside Studio indeholder et væld af oplysninger, videoer, selvstudier og links til referencen moduler og andre ressourcer. Se [Azure Machine Learning dokumentation Center](https://azure.microsoft.com/documentation/services/machine-learning/)kan finde flere oplysninger om Azure Machine Learning.

En typisk kursus forsøg består af følgende trin:

1. Oprette en **+ Ny** forsøg.
2. Få vist data til Azure ML.
3. Udfyldt behandle, transformere og arbejde med dataene efter behov.
4. Oprette funktioner, som det er nødvendigt.
5. Opdele dataene i kursus/validering/test datasæt (eller har separat datasæt for hver).
6. Vælg en eller flere machine learning algoritmer afhængigt af learning problemet til at løse. F.eks., binær klassifikation, multiclass klassifikation, regression.
7. Uddannelse af en eller flere modeller ved hjælp af kursus datasættet.
8. Resultat validering datasættet ved hjælp af den erfaren eller-modellerne.
9. Evaluere eller-modellerne for at beregne den relevante måleredskaber for learning problemet.
10. At finjustere eller-modellerne og vælge den bedste model til at installere.

I denne opgave skal har vi allerede set engineering dataene i SQL Data Warehouse og besluttet på stikprøvestørrelsen at indtager i Azure ML. Her er fremgangsmåden til at oprette en eller flere af de forudsigelse modeller:

1. Få vist data til Azure ML ved hjælp af [Importdata] [ import-data] modul, der er tilgængelige i sektionen **datainput og Output** . Få mere at vide under [Importere Data] [ import-data] modul reference side.

    ![Azure ML Importér Data][17]

2. Vælg **Azure SQL-Database** som **datakilde** i panelet **Egenskaber** .

3. Angiv navnet på databasen DNS i feltet **Databasenavn server** . Format:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Angiv **databasenavnet** i det tilsvarende felt.

5. Angiv *SQL brugernavn* i **Server-konto brugernavn**og *adgangskode* i feltet **Server adgangskode til brugerkonto**.

6. Markere indstillingen **acceptere en hvilken som helst servercertifikat** .

7. Indsæt den forespørgsel, som henter nødvendige databasefelter (herunder eventuelle beregnede felter som etiketterne) og ned eksempler i tekstområdet **databaseforespørgsel** Rediger data til den ønskede stikprøvestørrelsen.

Et eksempel på en binær klassifikation forsøg læse data direkte fra SQL Data Warehouse databasen er i nedenstående illustration (Husk at erstatte tabel navne nyctaxi_trip og nyctaxi_fare ved skemanavn og tabelnavne, du har brugt i din gennemgang). Lignende forsøg kan være opbygget multiclass klassifikation og regression problemer.

![Azure ML tog][10]

> [AZURE.IMPORTANT] I dataene, modellering angivet udtrækning af og indsamle forespørgsel eksempler i forrige afsnit, **alle etiketterne til de tre modellering øvelser er medtaget i forespørgslen**. En vigtige (obligatorisk) trin i hver af modellering øvelserne er at **udelade** unødvendige etiketterne til de andre to problemer, og eventuelle andre **target mister**. For eksempel, når du bruger binære klassifikation, bruge etiket **Vippet** og udelade felterne **tip\_klasse**, **tip\_beløb**, og **samlede\_beløb**. Disse er target lækager da de indikerer spidsen betalt.
>
> Hvis du vil udelade alle unødvendige kolonner eller målrette lækager, kan du bruge [Vælg kolonner i Dataset] [ select-columns] modul eller [Redigere Metadata][edit-metadata]. Du kan finde flere oplysninger under [Vælg kolonner i Dataset] [ select-columns] og [Redigere Metadata] [ edit-metadata] henviser sider.

## <a name="mldeploy"></a>Installere modeller i Azure Machine Learning

Når din model er klar, kan du nemt installere det som en webtjeneste direkte fra forsøget. Se [Implementer en Azure Machine Learning webtjeneste](machine-learning-publish-a-machine-learning-web-service.md)kan finde flere oplysninger om installation af Azure ML web services.

Du skal installere en ny webtjeneste, skal du:

1. Oprette en vurdering forsøg.
2. Installer webtjenesten.

Hvis du vil oprette en vurdering forsøg fra en **afsluttet** uddannelse forsøg, skal du klikke på **Oprette VUNDNE EKSPERIMENTERE** i nederste handlingslinjen.

![Azure pointmodel for kundeemner][18]

Azure Machine Learning forsøger at oprette en vurdering forsøg, der er baseret på komponenterne i kursus forsøget. Det skal især:

1. Gem erfaren modellen, og fjern model kursus moduler.
2. Identificere en logisk **input port** for at repræsentere forventede inputdataene skemaet.
3. Identificere en logisk **outputport** for at repræsentere forventede web service output skemaet.

Når der oprettes vurdering forsøget, gennemgå det, og foretag Juster efter behov. En typisk justering er at erstatte input datasæt og/eller forespørgsel med en, der udelader kolonnenavnsfelter, som disse er ikke tilgængelige, når tjenesten kaldes. Det er også en god ide at reducere størrelsen på input datasæt og/eller forespørgsel til et par poster, lige nok til at angive input skemaet. Til outputporten er det almindelige at udelade alle input felter og kun at medtage **Karakter etiketter** og **Karakter sandsynligheder** i outputtet ved hjælp af [Vælg kolonner i Dataset] [ select-columns] modul.

Et eksempel på vundne forsøg er angivet i nedenstående illustration. Når du er klar til at udrulle, skal du klikke på knappen **PUBLICER WEBTJENESTE** i nederste handlingslinjen.

![Azure ML publicere][11]


## <a name="summary"></a>Oversigt
Hvis du vil opsummere, hvad vi har foretaget i selvstudiet gennemgang: du har oprettet et Azure data videnskabelige-miljø, arbejdet med et stort offentlige datasæt, tager det gennem Team Data videnskabelige processen, helt fra dataindsamling model kursus og derefter til installation af en Azure Machine Learning webtjeneste.

### <a name="license-information"></a>Oplysninger om licenser

Dette eksempel gennemgang og dens tilhørende scripts og IPython notebook(s) der deles af Microsoft under MIT-licensen. Kontroller.txt-fil i mappen på eksempelkoden på GitHub kan finde flere oplysninger.

## <a name="references"></a>Referencer

• [Andrés Monroy NYC Taxi rejser overførselssiden](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC'S Taxi forretningsrejse Data efter Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi og Limousine provision forskning og statistik](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
