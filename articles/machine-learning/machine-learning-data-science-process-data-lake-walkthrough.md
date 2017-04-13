<properties
    pageTitle="SVG Data videnskab i Azure Data sø: en gennemgang af til slut | Microsoft Azure"
    description="Sådan bruges Azure Data sø til at gøre data udforske og binær klassifikation opgaver på et datasæt."  
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
    ms.date="09/19/2016"
    ms.author="bradsev;weig"/>


# <a name="scalable-data-science-in-azure-data-lake-an-end-to-end-walkthrough"></a>SVG Data videnskab i Azure Data sø: en gennemgang af til slut

Denne gennemgang viser, hvordan du bruger Azure Data sø til at gøre data udforske og binære klassifikation opgaver på en stikprøve af NYC taxi forretningsrejse og pris datasæt for at forudsige hvorvidt et tip, der betales ved en pris. Det vejleder dig gennem trinnene til [Team Data videnskabelige proces](http://aka.ms/datascienceprocess),-til-slut, fra dataindsamling model kursus og derefter til installation af en webtjeneste, hvor der publiceres modellen.


### <a name="azure-data-lake-analytics"></a>Azure Data sø Analytics

[Microsoft Azure Data sø](https://azure.microsoft.com/solutions/data-lake/) har alle de nødvendige funktioner til gør det nemt for data forskere til at gemme data for en hvilken som helst størrelse, form og hastighed og til at gennemføre databehandling, avancerede analyser og machine learning-modellering med høj skalerbarhed i en økonomisk måde.   Du betaler på grundlag-job kun, når data behandles faktisk. Azure Data sø Analytics omfatter U-SQL, et sprog, farveblandinger SQL deklarativ art med udtryksfuld styrken i C# til at levere SVG distribueret forespørgsel egenskab. Det gør det muligt at behandle ustrukturerede data ved at anvende skemaet på læst, skal du indsætte brugerdefineret logik og brugerdefinerede funktioner, og omfatter udvidelsesmuligheder af for at aktivere fint kornet styring over, hvordan du kan udføre skaleres til brug. Hvis du vil vide mere om designfilosofi bag U-SQL skal du se [Visual Studio blogindlæg](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Data sø Analytics er også en central del af Cortana Analytics pakke og fungerer med Azure SQL Data Warehouse, Power BI og Data Factory. Det giver dig en komplet skyen big data og avancerede analytics platform.

Denne gennemgang begynder ved at beskrive forudsætninger og ressourcer, der skal bruges til at udføre opgaver med Data sø Analytics, der danner data videnskabelige processen, og hvordan skal installeres. Derefter beskrives trinnene til behandling af data ved hjælp af U-SQL og slutter ved at vise, hvordan du bruger Python og Hive med Azure Machine Learning Studio til at opbygge og anvende de skønnet modeller. 


### <a name="u-sql-and-visual-studio"></a>U-SQL og Visual Studio

Denne gennemgang anbefaler at bruge Visual Studio til at redigere U-SQL-scripts for at behandle datasættet. U-SQL-scripts er beskrevet her og leveret i en separat fil. Processen omfatter ingesting, udforske og indsamle data. Det viser også at køre et U-SQL scripts job fra Azure-portalen. Hive tabeller oprettes for dataene i en tilknyttet HDInsight-klynge for at lette opbygning og installation af en binær klassifikation model i Azure Machine Learning Studio.  


### <a name="python"></a>Python

Denne gennemgang indeholder også en sektion, der viser, hvordan du kan opbygge og anvende en skønnet model ved hjælp af Python med Azure Machine Learning Studio.  Vi giver dig en Jupyter notesbog med Python scriptene til disse trin i processen. Notesbogen indeholder kode for nogle ekstra funktion engineering trin og modeller byggeri som multiclass klassifikation og regression modeling ud over den binære klassifikation model, der er beskrevet her. Den simple lineære regression opgave er at forudsige mængden spidsen baseret på andre tip funktioner. 


### <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning Studio bruges til at opbygge og anvende de skønnet modeller. Dette gøres ved hjælp af en af følgende fremgangsmåder: først med Python scripts og derefter med Hive tabeller på en HDInsight (Hadoop) klynge.


### <a name="scripts"></a>Scripts

Kun de vigtigste trin er beskrevet i denne gennemgang. Du kan hente den fulde **U-SQL-script** og **Jupyter notesbog** fra [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## <a name="prerequisites"></a>Forudsætninger

Før du går i gang med disse emner, skal du have følgende:

- Et Azure-abonnement. Hvis du ikke allerede har en, kan du se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- [Anbefales] Visual Studio 2013 eller 2015. Hvis du ikke allerede har en af disse versioner, der er installeret, kan du hente en gratis Community udgave fra [her](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Klik på knappen **Hent Community 2015** under afsnittet Visual Studio. 

>[AZURE.NOTE] Du kan også bruge portalen Azure til at sende Azure Data sø forespørgsler i stedet for Visual Studio. Vi giver vejledning om, hvordan du gør både med Visual Studio og på portalen i afsnittet **proces data med U-SQL**. 

- Tilmelding til Azure Data sø Preview

>[AZURE.NOTE] Du har brug at få godkendelse til at bruge Azure Data sø Store (ADLS) og Azure Data sø Analytics (ADLA), som disse tjenester er i Vis udskrift. Du bliver bedt om at tilmelde dig, når du opretter dit første ADLS eller ADLA. Sigh op, klikke på **Tilmeld dig for at få vist et eksempel**, læse aftalen, og klik på **OK**. Her er for eksempel ADLS Tilmeld dig side:

 ![2](./media/machine-learning-data-science-process-data-lake-walkthrough/2-ADLA-preview-signup.PNG)


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Forberede data videnskabelige miljø til Azure Data sø
For at forberede data videnskabelige miljø til denne gennemgang skal du oprette følgende ressourcer:

- Azure sø datalager (ADLS) 
- Azure Data sø Analytics (ADLA)
- Azure Blob-lager-konto
- Azure Machine Learning Studio-konto
- Azure Data sø Tools til Visual Studio (anbefales)

Dette afsnit indeholder en vejledning i at oprette hver af disse ressourcer. Hvis du vælger at bruge Hive tabeller med Azure Machine Learning, i stedet for Python, for at opbygge en model, skal du også til at klargøre en HDInsight (Hadoop) klynge. Denne alternative procedure i beskrevet i det relevante afsnit nedenfor.
<br/>
>AZURE. Bemærk **Azure datalager sø** kan oprettes enten separat eller når du opretter **Azure Data sø Analytics** som standard-lagerplads. Der henvises til instruktioner til oprettelse af hver af disse ressourcer separat nedenfor, men kontoen Data sø lagerplads skal ikke oprettes separat.
<br/>
### <a name="create-an-azure-data-lake-store"></a>Oprette et Azure datalager sø

Oprette en ADLS fra [Azure-portalen](http://portal.azure.com). Yderligere oplysninger finder du [oprette en HDInsight klynge med sø datalager Azure portalen](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Sørg for at konfigurere klynge AAD identitet i bladet **datakilde** af bladet **Valgfri konfiguration** , der er beskrevet. 

 ![3](./media/machine-learning-data-science-process-data-lake-walkthrough/3-create-ADLS.PNG)


### <a name="create-an-azure-data-lake-analytics-account"></a>Oprette en Azure Data sø Analytics-konto
Opret en ADLA konto fra [Azure-portalen](http://portal.azure.com). Yderligere oplysninger finder du [Selvstudium: Introduktion til Azure Data sø analyser ved hjælp af Azure Portal](../data-lake-analytics/data-lake-analytics-get-started-portal.md). 

 ![4](./media/machine-learning-data-science-process-data-lake-walkthrough/4-create-ADLA-new.PNG)


### <a name="create-an-azure-blob-storage-account"></a>Opret en konto med Azure Blob-lager
Opret en Azure Blob storage konto fra [Azure-portalen](http://portal.azure.com). Få mere at vide under oprette et lager konto afsnit i [om Azure lagerplads konti](../storage/storage-create-storage-account.md).
    
 ![5](./media/machine-learning-data-science-process-data-lake-walkthrough/5-Create-Azure-Blob.PNG)


### <a name="set-up-an-azure-machine-learning-studio-account"></a>Konfigurere en Azure Machine Learning Studio-konto
Log op/til Azure Machine Learning Studio fra siden [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) . Klik på knappen **Kom i gang nu** , og vælg derefter et "Gratis arbejdsområde" eller "Standard arbejdsområde". Herefter vil du kunne oprette forsøg i Azure ML Studio.  

### <a name="install-azure-data-lake-tools-recommended"></a>Installer Azure sø Dataværktøjer [anbefales]
Installer Azure Dataværktøjer sø til din version af Visual Studio fra [Azure Data sø Tools til Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Når installationen afsluttes korrekt, skal du åbne op Visual Studio. Du bør se den Data sø fanen menuen øverst. Dine Azure ressourcer bør fremgå i panelet til venstre, når du logger på din Azure-konto.

 ![7](./media/machine-learning-data-science-process-data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)


## <a name="the-nyc-taxi-trips-dataset"></a>NYC Taxi rejser datasættet
Datasættet brugte vi her er et offentligt tilgængeligt datasæt – [NYC Taxi rejser datasæt](http://www.andresmh.com/nyctaxitrips/). Datatypen NYC Taxi forretningsrejse består af om 20GB komprimeret csv-filer (~ 48GB ikke-komprimeret format), over 173 millioner individuelle rejser og priser betalt for hver forretningsrejse. Hver forretningsrejse post indeholder afhentning og indlevering placeringer og gange, anonyme hacke (driver) licens tal og tallet Medaillon (taxi's entydige id). Data, der dækker alle rejser år 2013 og fremgår af følgende to datasæt for hver måned:

 - 'trip_data' CSV indeholder forretningsrejse oplysninger, som antallet af passagerer, afhentning og dropoff punkter, forretningsrejse varighed og forretningsrejse længde. Her er nogle eksempler på poster:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - 'trip_fare' CSV indeholder oplysninger om den pris, som er betalt for hver tur som betalingstype, pris beløb, tillæg og moms, tip og vej, og det samlede betalte beløb. Her er nogle eksempler på poster:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den entydige nøgle til at deltage i forretningsrejse\_data og forretningsrejse\_pris består af følgende tre felter: Medaillon, hacke\_licens og afhentning\_datetime. Rå csv-filer kan åbnes fra en offentlig Azure lagerplads blob. U-SQL-script for denne joinforbindelse findes i afsnittet [Deltag forretningsrejse og pris tabeller](#join) .

## <a name="process-data-with-u-sql"></a>Behandle data med U-SQL

Databehandling opgaverne illustreret i dette afsnit indeholder ingesting, kontrol kvalitet, udforske og indsamle data. Vi viser også Sådan deltager du i tabellerne forretningsrejse og pris. I sidste afsnit viser køre et script U-SQL-job fra Azure-portalen. Her er links til hver underafsnit:

- [Data indtagelse: Læs data fra offentlige blob](#ingest)
- [Data kvaliteten på trykkeri](#quality)
- [Udforske data](#explore)
- [Deltage i tabellerne forretningsrejse og pris](#join)
- [Udvalg af data](#sample)
- [Køre U-SQL-job](#run)

U-SQL-scripts er beskrevet her og leveret i en separat fil. Du kan hente de fulde **U-SQL-scripts** fra [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Hvis du vil udføre U-SQL, åbne Visual Studio, skal du klikke på **fil--> Ny--> Project**, Vælg **U-SQL-projekt**, navngive og gemme den til en mappe.

![8](./media/machine-learning-data-science-process-data-lake-walkthrough/8-create-USQL-project.PNG)

>[AZURE.NOTE] Det er muligt at bruge portalen Azure til at udføre U-SQL i stedet for Visual Studio. Du kan navigere til Azure Data sø Analytics ressourcen på portalen og sende forespørgsler direkte som illustreret i følgende figur.

![9](./media/machine-learning-data-science-process-data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="ingest"></a>Data indtagelse: Læs data fra offentlige blob

Placeringen af dataene i Azure blob der refereres som **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name** og kan udtrækkes ved hjælp af **Extractors.Csv()**. Erstatte dine egne objektbeholder navn og lagerplads kontonavn i følgende scripts til container_name@blob_storage_account_name wasb webadresse. Fordi filnavnene er i samme format, kan vi bruge **forretningsrejse\_data_ {\*\}.csv** at læse i alle 12 forretningsrejse filer. 

    ///Read in Trip data
    @trip0 =
        EXTRACT 
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Fordi der er sidehoveder i den første række, skal vi fjerne overskrifterne og ændre kolonnetyper til relevante dem. Vi kan enten Gem de behandlede data til Azure sø lagring af Data ved hjælp af **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ eller til Azure Blob-lager-konto ved hjælp af **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**. 

    // change data types
    @trip =
        SELECT 
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        DateTime.Parse(pickup_datetime) AS pickup_datetime,
        DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
        Int32.Parse(passenger_count) AS passenger_count,
        Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
        Double.Parse(trip_distance) AS trip_distance,
        (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
        (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
        (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
        (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

    ////output data to ADL
    OUTPUT @trip   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
    USING Outputters.Csv(); 

    ////Output data to blob
    OUTPUT @trip   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
    USING Outputters.Csv();  

På samme måde kan vi læse i datasættene pris. Højreklik på Azure sø datalager, kan du vælge at se dine data i **Azure Portal--> Data Explorer** eller **Stifinder** i Visual Studio. 

 ![10](./media/machine-learning-data-science-process-data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/machine-learning-data-science-process-data-lake-walkthrough/11-data-in-ADL.PNG)


### <a name="quality"></a>Data kvaliteten på trykkeri

Når forretningsrejse og pris tabeller er læst i, kan du gøre data kvalitet Kontroller på følgende måde. De resulterende csv-filer kan være output til Azure Blob-lager eller Azure Data sø Store. 

Finde antallet af medallions og entydige antallet af medallions:

    ///check the number of medallions and unique number of medallions
    @trip2 =
        SELECT
        medallion,
        vendor_id,
        pickup_datetime.Month AS pickup_month
        FROM @trip;
    
    @ex_1 =
        SELECT
        pickup_month, 
        COUNT(medallion) AS cnt_medallion,
        COUNT(DISTINCT(medallion)) AS unique_medallion
        FROM @trip2
        GROUP BY pickup_month;
        OUTPUT @ex_1   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
    USING Outputters.Csv(); 

Finde disse medallions med mere end 100 rejser:

    ///find those medallions that had more than 100 trips
    @ex_2 =
        SELECT medallion,
               COUNT(medallion) AS cnt_medallion
        FROM @trip2
        //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
        GROUP BY medallion
        HAVING COUNT(medallion) > 100;
        OUTPUT @ex_2   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
    USING Outputters.Csv(); 

Søge efter disse ugyldige poster med hensyn til pickup_longitude:

    ///find those invalid records in terms of pickup_longitude
    @ex_3 =
        SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
        FROM @trip
        WHERE
        pickup_longitude <- 90 OR pickup_longitude > 90;
        OUTPUT @ex_3   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
    USING Outputters.Csv(); 

Finde manglende værdier for nogle variabler:

    //check missing values
    @res =
        SELECT *,
               (medallion == null? 1 : 0) AS missing_medallion
        FROM @trip;
    
    @trip_summary6 =
        SELECT 
            vendor_id,
        SUM(missing_medallion) AS medallion_empty, 
        COUNT(medallion) AS medallion_total,
        COUNT(DISTINCT(medallion)) AS medallion_total_unique  
        FROM @res
        GROUP BY vendor_id;
    OUTPUT @trip_summary6
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
    USING Outputters.Csv();



### <a name="explore"></a>Udforske data

Vi kan gøre nogle undersøgelse af data for at få en bedre forståelse af dataene.

Finde fordelingen af Vippet og ikke-Vippet rejser:

    ///tipped vs. not tipped distribution
    @tip_or_not =
        SELECT *,
               (tip_amount > 0 ? 1: 0) AS tipped
        FROM @fare;
    
    @ex_4 =
        SELECT tipped,
               COUNT(*) AS tip_freq
        FROM @tip_or_not
        GROUP BY tipped;
        OUTPUT @ex_4   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
    USING Outputters.Csv(); 

Finde fordelingen af tip beløbet med afbrydelse værdier: 0,5,10 og 20 kroner.

    //tip class/range distribution
    @tip_class =
        SELECT *,
               (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
        FROM @fare;
    @ex_5 =
        SELECT tip_class,
               COUNT(*) AS tip_freq
        FROM @tip_class
        GROUP BY tip_class;
        OUTPUT @ex_5   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
    USING Outputters.Csv(); 

Finde grundlæggende statistik for forretningsrejse afstand:

    // find basic statistics for trip_distance
    @trip_summary4 =
        SELECT 
            vendor_id,
            COUNT(*) AS cnt_row,
            MIN(trip_distance) AS min_trip_distance,
            MAX(trip_distance) AS max_trip_distance,
            AVG(trip_distance) AS avg_trip_distance 
        FROM @trip
        GROUP BY vendor_id;
    OUTPUT @trip_summary4
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
    USING Outputters.Csv();

Finde fraktiler for forretningsrejse afstand:

    // find percentiles of trip_distance
    @trip_summary3 =
        SELECT DISTINCT vendor_id AS vendor,
                        PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                        PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
        FROM @trip;
       // group by vendor_id;
    OUTPUT @trip_summary3
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
    USING Outputters.Csv(); 


### <a name="join"></a>Deltage i tabellerne forretningsrejse og pris

Forretningsrejse og pris tabeller kan sammenføjes ved Medaillon, hack_license og pickup_time.

    //join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

    //// output to blob
    OUTPUT @model_data_full   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 

    ////output data to ADL
    OUTPUT @model_data_full   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
    USING Outputters.Csv(); 


Beregne antallet af poster, omkostninger, variansen af tip beløb, procentdel af Vippet rejser for hvert niveau af passager optælling.

    // contigency table
    @trip_summary8 =
        SELECT passenger_count,
               COUNT(*) AS cnt,
               AVG(tip_amount) AS avg_tip_amount,
               VAR(tip_amount) AS var_tip_amount,
               SUM(tipped) AS cnt_tipped,
               (float)SUM(tipped)/COUNT(*) AS pct_tipped
        FROM @model_data_full
        GROUP BY passenger_count;
        OUTPUT @trip_summary8
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
    USING Outputters.Csv();


### <a name="sample"></a>Udvalg af data

Vi Markér tilfældigt først 0,1% af dataene fra den sammenkædede tabel:

    //random select 1/1000 data for modeling purpose
    @addrownumberres_randomsample =
    SELECT *,
            ROW_NUMBER() OVER() AS rownum
    FROM @model_data_full;
    
    @model_data_random_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_randomsample
    WHERE rownum % 1000 == 0;
    
    OUTPUT @model_data_random_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
    USING Outputters.Csv(); 

Vi gør derefter stratified udvalg af binære variable tip_class:

    //stratified random select 1/1000 data for modeling purpose
    @addrownumberres_stratifiedsample =
    SELECT *,
            ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
    FROM @model_data_full;
    
    @model_data_stratified_sample_1_1000 =
    SELECT *
    FROM @addrownumberres_stratifiedsample
    WHERE rownum % 1000 == 0;
    //// output to blob
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 
    ////output data to ADL
    OUTPUT @model_data_stratified_sample_1_1000   
    TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
    USING Outputters.Csv(); 


### <a name="run"></a>Køre U-SQL-job

Når du er færdig med at redigere U-SQL-scripts, kan du sende dem til serveren ved hjælp af din Azure Data sø Analytics-konto. Klik på **Data sø**, **Sende Job**, Vælg din **Analytics-konto**, Vælg **parallelitet**, og klik på knappen **Send** .  

 ![12](./media/machine-learning-data-science-process-data-lake-walkthrough/12-submit-USQL.PNG)

Når jobbet er i overensstemmelse korrekt, vises status for dit arbejde i Visual Studio til overvågning. Når jobbet er færdig, kan du endda genafspilning job udførelse af processen og find ud af flaskehals trinnene til at forbedre din sag effektivitet. Du kan også gå til Azure-portalen for at kontrollere status for dine U-SQL-job.

 ![13](./media/machine-learning-data-science-process-data-lake-walkthrough/13-USQL-running-v2.PNG)


 ![14](./media/machine-learning-data-science-process-data-lake-walkthrough/14-USQL-jobs-portal.PNG)


Du kan nu se outputfiler i enten Azure Blob-lager eller Azure-portalen. Vi bruger stratified eksempeldataene til vores modellering i næste trin.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/machine-learning-data-science-process-data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)


## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Opbygge og anvende modeller i Azure Machine Learning

Vi viser to muligheder for tilgængelige for dig at hente data ind i Azure Machine Learning at opbygge og 

- I den første indstilling, du bruger de indsamlede data, der er skrevet til en Azure Blob (i trinnet **Data udvalg** ovenfor) og bruge Python til at opbygge og anvende modeller fra Azure Machine Learning. 
- I den anden mulighed forespørge du dataene i Azure Data sø direkte ved hjælp af en Hive-forespørgsel. Denne indstilling kræver, at oprette en ny HDInsight klynge eller bruge en eksisterende HDInsight klynge, hvor tabellerne Hive peg på NY Taxi dataene i Azure sø datalagring.  Vi diskuterer begge disse indstillinger, der er nedenfor. 

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Mulighed 1: Brug Python at opbygge og anvende machine learning modeller

Oprette en Jupyter notesbog for at opbygge og anvende machine learning modellerne ved hjælp af Python, på din lokale computer eller Azure maskine læ Studio. Jupyter notesbogen under [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) indeholder den fulde kode til at udforske, visualisere data, funktion teknisk, modellering og installation. I denne artikel viser vi blot modellering og installation. 

### <a name="import-python-libraries"></a>Importere Python biblioteker

For at køre eksemplet scripts Jupyter notesbog eller Python filer, på følgende Python pakker er det er nødvendigt. Hvis du bruger tjenesten AzureML notesbog, er disse pakker forudinstalleret.

    import pandas as pd
    from pandas import Series, DataFrame
    import numpy as np
    import matplotlib.pyplot as plt
    from time import time
    import pyodbc
    import os
    from azure.storage.blob import BlobService
    import tables
    import time
    import zipfile
    import random
    import sklearn
    from sklearn.linear_model import LogisticRegression
    from sklearn.cross_validation import train_test_split
    from sklearn import metrics
    from __future__ import division
    from sklearn import linear_model
    from azureml import services


### <a name="read-in-the-data-from-blob"></a>Læs data fra blob

- Forbindelsesstreng   

        CONTAINERNAME = 'test1'
        STORAGEACCOUNTNAME = 'XXXXXXXXX'
        STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
        BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
        blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    
- Læs i som tekst

        t1 = time.time()
        data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
        t2 = time.time()
        print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![17](./media/machine-learning-data-science-process-data-lake-walkthrough/17-python_readin_csv.PNG)    
 
- Tilføje kolonnenavne, og Adskil kolonner

        colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
        'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
        df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
    


- Ændre nogle af kolonnerne til numeriske

        cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
        'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
        ,'tipped','tip_class','rownum']
        for col in cols_2_float:
            df1[col] = df1[col].astype(float)

### <a name="build-machine-learning-models"></a>Oprette machine learning modeller

Her kan vi bygge en binær klassifikation model for at forudsige, om en rejse Vippet eller ej. Du kan finde andre to modeller i Jupyter notesbog: multiclass klassificering og regression modeller.

- Først skal vi oprette dummy variabler, der kan bruges i scikit-Lær modeller

        df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
        df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Oprette data ramme for modellering

        cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
        data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
        
        X = data.iloc[:,1:]
        Y = data.tipped

- Kurser og test af 60-40 opdelt

        X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Logistisk Regression i kursus sæt

        model = LogisticRegression()
        logit_fit = model.fit(X_train, Y_train)
        print ('Coefficients: \n', logit_fit.coef_)
        Y_train_pred = logit_fit.predict(X_train)

       ![c1](./media/machine-learning-data-science-process-data-lake-walkthrough/c1-py-logit-coefficient.PNG)

- Resultat test datasæt

        Y_test_pred = logit_fit.predict(X_test)

- Beregne evaluering målepunkter

        fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
        print fpr_train, tpr_train, thresholds_train
        
        fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
        print fpr_test, tpr_test, thresholds_test
        
        #AUC
        print metrics.auc(fpr_train,tpr_train)
        print metrics.auc(fpr_test,tpr_test)
        
        #Confusion Matrix
        print metrics.confusion_matrix(Y_train,Y_train_pred)
        print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![c2](./media/machine-learning-data-science-process-data-lake-walkthrough/c2-py-logit-evaluation.PNG)


 
### <a name="build-web-service-api-and-consume-it-in-python"></a>Opbygge Web Service API og bruge det i Python

Vi vil gerne giver muligheder for maskinen læ model, når det er bygget. Vi bruger her binære logistiske modellen som et eksempel. Sørg for, at scikit-få version i din lokale computer er 0.15.1. Du behøver at bekymre dig om dette, hvis du bruger Azure ML studio service.

- Finde dine legitimationsoplysninger arbejdsområde fra Azure ML studio indstillinger. Klik på **Indstillinger**i Azure Machine Learning Studio --> **navn** --> **Godkendelse Tokens**. 

    ![C3](./media/machine-learning-data-science-process-data-lake-walkthrough/c3-workspace-id.PNG)


        workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
        auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Oprette en webtjeneste

        @services.publish(workspaceid, auth_token) 
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(int) #0, or 1
        def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
            return logit_fit.predict(inputArray)

- Få web service legitimationsoplysninger

        url = predictNYCTAXI.service.url
        api_key =  predictNYCTAXI.service.api_key
        
        print url
        print api_key

        @services.service(url, api_key)
        @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
        @services.returns(float)
        def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
            pass

- Ring til Web service API. Du skal vente 5-10 sekunder efter det forrige trin.

        NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![c4](./media/machine-learning-data-science-process-data-lake-walkthrough/c4-call-API.PNG)


## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Mulighed 2: Oprette og installere modeller direkte i Azure Machine Learning

Azure Machine Learning Studio kan læse data direkte fra Azure sø datalager og derefter bruges til at oprette og installere modeller. Denne metode bruger en Hive-tabel, der peger på Azure sø datalager. Dette kræver, at klargøres en separat Azure HDInsight klynge, på tabellen Hive oprettes. De følgende afsnit viser, hvordan du gør dette. 

### <a name="create-an-hdinsight-linux-cluster"></a>Oprette en HDInsight Linux klynge

Oprette en HDInsight klynge (Linux) fra [Azure-portalen](http://portal.azure.com). Få mere at vide i afsnittet **oprette en HDInsight klynge med adgang til Azure datalager sø** i [oprette en HDInsight klynge med sø datalager Azure portalen](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/machine-learning-data-science-process-data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Oprette Hive tabel i HDInsight

Nu skal vi oprette Hive tabeller, der skal bruges i Azure Machine Learning Studio i den HDInsight klynge, ved hjælp af de data, der er gemt i Azure datalager sø i ovenstående trin. Gå til den HDInsight klynge lige har oprettet. Klik på **Indstillinger for** --> **Egenskaber** --> **Klynge AAD identitet** --> **ADLS Access**, Sørg for, at din Azure datalager sø kontoen er tilføjet på listen med læse, skrive og udføre rettigheder. 

 ![19](./media/machine-learning-data-science-process-data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)


Klik derefter på **Dashboard** ud for knappen **Indstillinger** , og et vindue kommer frem. Klik på **Hive visning** i øverste højre hjørne af siden, og du får vist **Forespørgselseditor**.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/machine-learning-data-science-process-data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)


Indsæt følgende Hive scripts til at oprette en tabel. Placeringen af datakilden er i Azure datalager sø reference på denne måde: **adl://data_lake_store_name.azuredatalakestore.net:443/mappenavn/file_name**.

    CREATE EXTERNAL TABLE nyc_stratified_sample
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count string,
        trip_time_in_secs string,
        trip_distance string,
        pickup_longitude string,
        pickup_latitude string,
        dropoff_longitude string,
        dropoff_latitude string,
      payment_type string,
      fare_amount string,
      surcharge string,
      mta_tax string,
      tolls_amount string,
      total_amount string,
      tip_amount string,
      tipped string,
      tip_class string,
      rownum string
      )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';


Når forespørgslen er færdig, skal du se resultaterne således:

 ![22](./media/machine-learning-data-science-process-data-lake-walkthrough/22-Hive-Query-results.PNG)



### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Opbygge og anvende modeller i Azure Machine Learning Studio

Vi er nu klar til at opbygge og anvende en model, der beregner hvorvidt et tip som betales med Azure Machine Learning. Stratified eksempeldataene er klar til brug i denne binære klassifikation (tip eller ej) problemet. Skønnet modellerne ved hjælp af multiclass klassifikation (tip_class) og regression (tip_amount) kan også indbygget og installeret med Azure Machine Learning Studio, men her vi kun viser, hvordan du skal håndtere store og små bogstaver ved hjælp af binær klassifikation modellen.

1. Få vist data til Azure ML brugen af **Importdata** -modulet, der er tilgængelige i sektionen **datainput og Output** . Få mere at vide på siden [importdata modul](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) reference.
2. Vælg **Hive forespørgsel** som **datakilde** i panelet **Egenskaber** .
3. Indsæt følgende Hive scriptet i editoren **Hive databaseforespørgsel**

        select * from nyc_stratified_sample;

4. Angiv URI HDInsight klynge (findes i Azure Portal), Hadoop-legitimationsoplysninger, placering af outputdata og Azure lagerplads navn/nøgle/objektbeholder kontonavn.

 ![23](./media/machine-learning-data-science-process-data-lake-walkthrough/23-reader-module-v3.PNG)  

Et eksempel på en binær klassifikation forsøg, du læser data fra Hive tabel er vist i nedenstående illustration.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthrough/24-AML-exp.PNG)

Når forsøget er oprettet, skal du klikke på **Angiv webtjeneste** --> **Skønnet webtjeneste**

 ![25](./media/machine-learning-data-science-process-data-lake-walkthrough/25-AML-exp-deploy.PNG)

Køre den automatisk oprettede vundne forsøg, når den er færdig, skal du klikke på **Installere webtjeneste**

 ![26](./media/machine-learning-data-science-process-data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Dashboardet web service vises kort:

 ![27](./media/machine-learning-data-science-process-data-lake-walkthrough/27-AML-web-api.PNG)


## <a name="summary"></a>Oversigt

Du har oprettet en data videnskabelige miljø til opbygning af SVG til slut løsninger i Azure Data sø ved at udføre denne gennemgang. Dette miljø blev brugt til at analysere store offentlige datasæt, tager det vedtaget trinene til Data videnskabelige processen, fra dataindsamling gennem model kursus og derefter til installation af modellen som en webtjeneste. U-SQL blev brugt til at behandle, udforske og eksempel på dataene. Python og Hive blev brugt med Azure Machine Learning Studio til at opbygge og anvende skønnet modeller.

## <a name="whats-next"></a>Hvad skal der ske nu?

Læringssti til [Team Data videnskabelige proces (TDSP)](http://aka.ms/datascienceprocess) indeholder links til emner, der beskriver hvert trin i processen avanceret analyse. Der er en række gennemgange af specificeret på siden [gennemgange af Team Data videnskabelige proces](data-science-process-walkthroughs.md) , der viser hvordan du bruger ressourcer og -tjenester i forskellige skønnet analytics scenarier:

- [Team Data videnskabelige processen i praksis: Brug af SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md)
- [Team Data videnskabelige processen i praksis: Brug af HDInsight Hadoop klynger](machine-learning-data-science-process-hive-walkthrough.md)
- [Team Data videnskabelige processen: Brug af SQL Server](machine-learning-data-science-process-sql-walkthrough.md)
- [Oversigt over processen til videnskab, der bruger knallertmotor på Azure HDInsight](machine-learning-data-science-spark-overview.md)

