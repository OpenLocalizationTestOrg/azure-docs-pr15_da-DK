<properties
    pageTitle="Team Data videnskabelige processen i praksis: Brug Hadoop klynger | Microsoft Azure"
    description="Bruge Team Data videnskabelige processen for en til slut scenarie, der anvender en HDInsight Hadoop-klynge for at opbygge og anvende en model, ved hjælp af et offentligt tilgængeligt datasæt."
    services="machine-learning,hdinsight"
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
    ms.author="hangzh;bradsev" />


# <a name="the-team-data-science-process-in-action-using-hdinsight-hadoop-clusters"></a>Team Data videnskabelige processen i praksis: Brug af HDInsight Hadoop klynger

I denne gennemgang skal bruge vi [Team Data videnskabelige proces (TDSP)](data-science-process-overview.md) i et scenarie med til slut ved hjælp af en [Azure HDInsight Hadoop klynge](https://azure.microsoft.com/services/hdinsight/) at gemme, udforske og funktionen engineering data fra offentligt tilgængelige [NYC Taxi rejser](http://www.andresmh.com/nyctaxitrips/) datasættet og til nedadgående eksempel dataene. Modeller af dataene, der er oprettet med Azure Machine Learning til at håndtere binære og multiclass klassificering og regression skønnet opgaver.

Du kan finde en gennemgang, der viser, hvordan du skal håndtere et større (1 terabyte) datasæt for et lignende scenarie med HDInsight Hadoop klynger til databehandling, [Team Data videnskabelige proces - ved hjælp af Azure HDInsight Hadoop klynger på et datasæt 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md).

Du kan også bruge en IPython notesbog til at udføre de opgaver, der præsenteres gennemgangen ved hjælp af datasættet 1 TB. Brugere, der vil prøve denne metode bør kontakte emnet [Criteo gennemgang ved hjælp af en Hive ODBC-forbindelse](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Beskrivelse af NYC Taxi rejser datasæt

Datatypen NYC Taxi forretningsrejse er om 20GB komprimeret kommasepareret (CSV) filer (~ 48GB ikke-komprimeret format), der omfatter mere end 173 millioner individuelle rejser og priser betalt for hver forretningsrejse. Hver forretningsrejse post omfatter afhentning og indlevering placering og klokkeslæt, anonyme hacke (driver) licens tal og Medaillon (taxi's entydige id) tal. Data, der dækker alle rejser år 2013 og findes i de følgende to datasæt for hver måned:

1. 'Trip_data' csv-filerne indeholder forretningsrejse detaljer, som antallet af passagerer, afhentning og dropoff punkter, forretningsrejse varighed og forretningsrejse længde. Her er nogle eksempler på poster:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'Trip_fare' csv-filerne indeholder oplysninger om den pris, som er betalt for hver tur som betalingstype, pris beløb, tillæg og moms, tip og vej, og det samlede betalte beløb. Her er nogle eksempler på poster:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Den entydige nøgle til at deltage i forretningsrejse\_data og forretningsrejse\_pris består af felterne: Medaillon, hacke\_licens og afhentning\_datetime.

For at få alle detaljer relevant for en bestemt rejse, det er tilstrækkeligt at deltage med tre taster: "Medaillon", "dermed få\_licens" og "afhentning\_dato/klokkeslæt".

Vi beskriver nogle flere oplysninger om dataene, når vi gemme dem i Hive tabeller kort.

## <a name="mltasks"></a>Eksempler på forudsigelse opgaver
Når nærmer afgørende slags prognoser, du vil gøre data baseret på analysen hjælper med at tydeliggøre de opgaver, skal du medtage i din proces.
Her er tre eksempler på forudsigelse problemer, som vi adresse i denne gennemgang, hvis udformning er baseret på den *tip\_beløb*:

1. **Binær klassifikation**: forudsige eller ej et tip er betalt for en rejse, det vil sige en *tip\_beløb* , der er større end $0 er et positivt eksempel, mens en *tip\_beløb* på $0 er et negativt eksempel.

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0

2. **Multiclass klassifikation**: til at forudsige området af tip Beløb betalt for forretningsrejse. Vi dividere de *tip\_beløb* til fem placeringer eller klasser:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Regression opgave**: til at forudsige mængden spidsen betalt for en rejse.  


## <a name="setup"></a>Konfigurere en HDInsight Hadoop-klynge i avanceret analyse

>[AZURE.NOTE] Dette er typisk en opgave i en **administrator** .

Du kan konfigurere en Azure miljø til avanceret analyse, der anvender en HDInsight klynge i tre trin:

1. [Opret en lagerplads konto](../storage/storage-create-storage-account.md): denne lagerplads konto bruges til lagring af data i Azure Blob-lager. De data, der bruges i HDInsight klynger findes også her.

2. [Tilpasse Azure HDInsight Hadoop klynger for avancerede Analytics proces og teknologi](machine-learning-data-science-customize-hadoop-cluster.md). Dette trin opretter en Azure HDInsight Hadoop-klynge med 64-bit Anaconda Python 2.7 installeret på alle noder. Der er to vigtige trin at huske under tilpasse din HDInsight klynge.

    * Husk at sammenkæde kontoen lagerplads oprettede i trin 1 med din HDInsight klynge, når du opretter den. Denne lagerplads konto bruges til at få adgang til data, der er behandlet i klyngen.

    * Når klyngen er oprettet, skal du aktivere fjernadgang til noden hoved af klyngen. Gå til fanen **konfiguration** , og klik på **Aktivér Remote**. Dette trin angiver de logonoplysninger, der bruges til at logge på remote.

3. [Opret en Azure maskine læ arbejdsområde](machine-learning-create-workspace.md): denne Azure maskine læ arbejdsområde bruges til at oprette machine learning modeller. Denne opgave er rettet, efter du har udført en indledende data udforske og ned stikprøver, hvor der bruger HDInsight klynge.

## <a name="getdata"></a>Hente data fra en offentlig datakilde

>[AZURE.NOTE] Dette er typisk en opgave i en **administrator** .

For at få [NYC Taxi rejser](http://www.andresmh.com/nyctaxitrips/) dataset fra dens offentlige placering, kan du bruge en af de beskrevne i [Flytte Data til og fra Azure Blob-lager](machine-learning-data-science-move-azure-blob.md) metoder til at kopiere dataene til din computer.

Her vi beskriver, hvordan bruge AzCopy til overførsel af filer, der indeholder data. For at hente og installere AzCopy, skal du følge vejledningen i [Introduktion til AzCopy kommandolinjeparametre Utility](../storage/storage-use-azcopy.md).

1. Udstede følgende kommandoer i AzCopy, erstatte *< path_to_data_folder >* med den ønskede placering fra et kommandopromptvindue:


        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

2. Når kopien er fuldført, er op til 24 zip-filer i mappen data, der er valgt. Udpakke de hentede filer til den samme mappe på din lokale computer. Noter på den mappe, hvor de dekomprimerede filer er placeret. Denne mappe der refereres til som den *< stien\_til\_unzipped_data\_filer\> * er, hvad følger.


## <a name="upload"></a>Overføre dataene til objektbeholderen standard af Azure HDInsight Hadoop klynge

>[AZURE.NOTE] Dette er typisk en opgave i en **administrator** .

Erstat følgende parametre i de følgende kommandoer i AzCopy med de faktiske værdier, du har angivet, når du opretter Hadoop klynge og udpakke datafiler.

* ***& #60; path_to_data_folder >*** directory (sammen med stien) på din computer, der indeholder udpakket datafilerne  
* ***& #60; kontonavn lager af Hadoop klynge >*** kontoen lagerplads, der er knyttet til din HDInsight klynge
* ***& #60; standard objektbeholder af Hadoop klynge >*** objektbeholderen standard anvendes af din klynge. Bemærk, at navnet på objektbeholderen standard er som regel den samme navn som klyngen selve. Hvis klyngen kaldes "abc123.azurehdinsight.net", er objektbeholderen standard for eksempel abc123.
* ***& #60; lagerplads kontonøgle >*** tasten for kontoen lagerplads, der anvendes af din klynge

Kør følgende to AzCopy kommandoer fra en kommandoprompt eller i en Windows PowerShell-vinduet på computeren.

Denne kommando uploader forretningsrejse data til ***nyctaxitripraw*** directory i beholderen standard af Hadoop-klynge.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Denne kommando uploader pris data til ***nyctaxifareraw*** directory i beholderen standard af Hadoop-klynge.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Dataene skal nu i Azure Blob-lager og klar til at blive brugt i HDInsight klynge.

## <a name="#download-hql-files"></a>Log på noden hoved af Hadoop klynge og og forberede forsøgsfiskeri dataanalyse

>[AZURE.NOTE] Dette er typisk en opgave i en **administrator** .

For at få adgang til noden hoved af klynge forsøgsfiskeri dataanalyse og ned udvalg af dataene, skal du følge fremgangsmåden i [Access i afsnit-Node af Hadoop klynge](machine-learning-data-science-customize-hadoop-cluster.md#headnode).

I denne gennemgang bruger vi primært forespørgsler, der er skrevet i [Hive](https://hive.apache.org/), en SQL-lignende forespørgselssprog til at udføre Udforsk undergrunden foreløbige data. Hive forespørgsler er gemt i .hql filer. Vi derefter eksempeldata ned denne kan bruges i Azure Machine Learning opbygning af modeller.

For at forberede klyngen til forsøgsfiskeri dataanalyse, hente vi .hql filer, der indeholder de relevante Hive scripts fra [github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) til en lokal mappe (C:\temp) på den hovedsæde node. Åbn **kommandoprompten** fra i noden hoved af klyngen for at gøre dette, og udstede de to følgende kommandoer:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Disse to kommandoer henter alle .hql filer, der skal bruges i denne gennemgang til den lokale mappe ***C:\temp & #92;*** i noden hoved.

## <a name="#hive-db-tables"></a>Oprette Hive database og de tabeller, opdelt efter måned

>[AZURE.NOTE] Dette er typisk en opgave i en **administrator** .

Vi er nu klar til at oprette Hive tabeller til vores NYC taxi datasæt.
Åbn ***Hadoop kommandolinjen*** på skrivebordet på noden hoved i noden hoved af Hadoop-klynge, og Angiv Hive-mappen ved at angive kommandoen

    cd %hive_home%\bin

>[AZURE.NOTE] **Køre alle Hive-kommandoer i denne gennemgang fra ovenstående Hive placeringen / prompten til mappen. Dette vil automatisk tager sig af de problemer, sti. Vi bruger betingelserne "Hive directory prompt", "Hive placering / prompten til mappen", og "Hadoop kommandolinjen" i flæng i denne gennemgang.**

Fra Hive directory prompt, Skriv følgende kommando i Hadoop kommandolinjen for noden hoved til at sende Hive forespørgsel for at oprette Hive database og tabeller:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Her er indholdet af den ***C:\temp\sample\_hive\_oprette\_db\_og\_tables.hql*** -fil, der opretter Hive database ***nyctaxidb*** og tabeller ***forretningsrejse*** og ***pris***.

    create database if not exists nyctaxidb;

    create external table if not exists nyctaxidb.trip
    (
        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double)  
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

    create external table if not exists nyctaxidb.fare
    (
        medallion string,
        hack_license string,
        vendor_id string,
        pickup_datetime string,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double)
    PARTITIONED BY (month int)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
    STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Dette Hive script opretter to tabeller:

* tabellen "forretningsrejse" indeholder forretningsrejse detaljerne for hver kørende (Driverdetaljer, enhed tid, forretningsrejse afstand og gange)
* tabellen "pris" indeholder oplysninger om pris (pris beløb, tip beløb, vej og tillæg).

Hvis du har brug for en hvilken som helst yderligere hjælp med disse procedurer, eller du vil undersøge alternative dem, skal du se afsnittet [sende Hive forespørgsler direkte fra Hadoop-kommandolinjen ](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="#load-data"></a>Indlæser Data til Hive tabeller ved partitioner

>[AZURE.NOTE] Dette er typisk en opgave i en **administrator** .

NYC taxi datasættet har en fysisk partitionering efter måned, som vi bruge til at aktivere behandling og forespørgsel hurtigere. PowerShell kommandoerne nedenfor (udstedt fra mappen Hive ved hjælp af **Hadoop kommandolinjen**) indlæse dataene i tabellerne "forretningsrejse" og "pris" Hive opdelt efter måned.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Den *eksempel\_hive\_indlæse\_data\_ved\_partitions.hql* filen indeholder følgende **INDLÆSE** kommandoer.

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Bemærk, at et antal Hive forespørgsler vi bruge her i processen udforske involverer søger på en enkelt partition eller i et par partitioner. Men disse forespørgsler kan støde på tværs af hele dataene.

### <a name="#show-db"></a>Vis databaser i HDInsight Hadoop-klynge

For at vise de databaser, der er oprettet i HDInsight Hadoop klynge i vinduet Hadoop kommandolinjen skal du køre følgende kommando i Hadoop kommando:

    hive -e "show databases;"

### <a name="#show-tables"></a>Vise Hive tabellerne i databasen nyctaxidb

For at vise tabellerne i databasen nyctaxidb skal du køre følgende kommando i Hadoop kommandolinjen:

    hive -e "show tables in nyctaxidb;"

Vi kan bekræfte, at tabellerne, der er opdelt ved at udstede kommandoen nedenfor:

    hive -e "show partitions nyctaxidb.trip;"

Den forventede afgang er vist nedenfor:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 2.075 seconds, Fetched: 12 row(s)

På samme måde, kan vi sikre, at tabellen pris er opdelt ved at udstede kommandoen nedenfor:

    hive -e "show partitions nyctaxidb.fare;"

Den forventede afgang er vist nedenfor:

    month=1
    month=10
    month=11
    month=12
    month=2
    month=3
    month=4
    month=5
    month=6
    month=7
    month=8
    month=9
    Time taken: 1.887 seconds, Fetched: 12 row(s)

## <a name="#explore-hive"></a>Udforske data og funktion teknisk i Hive

>[AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Funktionen undersøgelse af data og engineering-opgaver for de data, der er indlæst i tabellerne Hive kan gøres ved hjælp af Hive forespørgsler. Her er eksempler på disse opgaver, som vi fører dig igennem i dette afsnit:

- Få vist de 10 vigtigste poster i begge tabeller.
- Udforske data fordeling af nogle felter i forskellige tid windows.
- Undersøge datakvaliteten af felterne breddegrader og længde.
- Generere binære og multiclass klassifikation etiketter, der er baseret på den **tip\_beløb**.
- Generere funktioner ved computing direkte forretningsrejse afstanden.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Udforske: Få vist de 10 vigtigste poster i tabel forretningsrejse

>[AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Hvis du vil se, hvordan dataene ser ud, skal undersøger vi 10 poster fra hver tabel. Kør følgende to forespørgsler separat fra Hive directory prompten i konsollen Hadoop kommandolinjen undersøge posterne.

At få de 10 vigtigste poster i tabellen "forretningsrejse" fra den første måned:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

At få de øverste 10 poster i tabellen "pris" fra den første måned:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Det er ofte nyttigt at gemme posterne i en fil til praktisk visning. En lille ændring af den ovenstående forespørgsel udfører dette:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Udforske: Få vist antallet af poster i hver af de 12 partitioner

>[AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Rente, der er Sådan antallet af rejser varierer i kalenderåret. Gruppering efter måned giver os mulighed at se, hvordan denne distribution af rejser ser ud.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Dette giver os output:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 283.406 seconds, Fetched: 12 row(s)

Her, den første kolonne er måneden, og andet er antallet af rejser for den pågældende måned.

Vi kan også tælle det samlede antal poster i vores forretningsrejse datasæt ved at udstede følgende kommando for kommandoprompten Hive directory.

    hive -e "select count(*) from nyctaxidb.trip;"

Dette giver:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Ved hjælp af kommandoer, der ligner dem, der vises for forretningsrejse datasæt, kan vi udstede Hive forespørgsler fra Hive directory prompten for datasættet pris til at validere antallet af poster.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Dette giver os output:

    1       14776615
    2       13990176
    3       15749228
    4       15100468
    5       15285049
    6       14385456
    7       13823840
    8       12597109
    9       14107693
    10      15004556
    11      14388451
    12      13971118
    Time taken: 253.955 seconds, Fetched: 12 row(s)

Bemærk, at det nøjagtige samme antal rejser månedsbasis returneres for begge datasæt. Dette giver den første validering, at dataene er blevet indlæst korrekt.

Tælle det samlede antal poster i datasættet pris kan udføres ved hjælp af kommandoen under fra Hive directory prompten:

    hive -e "select count(*) from nyctaxidb.fare;"

Dette giver:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Det samlede antal poster i begge tabeller er også den samme. Dette giver en anden validering, at dataene er blevet indlæst korrekt.

### <a name="exploration-trip-distribution-by-medallion"></a>Udforske: Forretningsrejse fordeling af Medaillon

>[AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

I dette eksempel identificerer Medaillon (taxi tal) med mere end 100 rejser inden for en given tidsperiode. Forespørgslen fordel af partitioneret tabel access, da det er betinget partition variable **måned**. Forespørgselsresultaterne skrives til en lokal fil queryoutput.tsv i `C:\temp` på noden hoved.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Her er indholdet af *eksempel\_hive\_forretningsrejse\_Tæl\_ved\_medallion.hql* fil til undersøgelsen.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medaillon i datasættet NYC taxi identificerer entydigt kabine. Vi kan identificere, hvilke førerhus er "optaget" ved at spørge, hvilke foretaget mere end et bestemt antal rejser i et bestemt tidsrum. I følgende eksempel identificerer førerhus, der har produceret mere end en 100 rejser i de første tre måneder og gemmer forespørgslen resultaterne til en lokal fil, C:\temp\queryoutput.tsv.

Her er indholdet af *eksempel\_hive\_forretningsrejse\_Tæl\_ved\_medallion.hql* fil til undersøgelsen.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Fra Hive directory bliver spurgt, skal du udstede kommandoen nedenfor:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Udforske: Forretningsrejse fordeling ved Medaillon og hack_license

>[AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Når bag et datasæt, vil vi ofte undersøge antallet co-forekomster af grupper af værdier. Dette afsnit indeholder et eksempel på, hvordan du gør dette for førerhus og drivere.

Den *eksempel\_hive\_forretningsrejse\_Tæl\_ved\_Medaillon\_license.hql* fil grupperer datasættet pris på "Medaillon" og "hack_license" og returnerer optællinger for hver kombination. Nedenfor finder du dens indhold.

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Denne forespørgsel returnerer cab og bestemt driver kombinationer sorteret efter faldende antal rejser.

Fra Hive directory bliver spurgt, skal du køre:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Forespørgselsresultaterne skrives til en lokal fil C:\temp\queryoutput.tsv.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitudelatitude-records"></a>Udforske: Vurdering af datakvaliteten ved at markere for ugyldige breddegrader/længde poster

>[AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Et fælles mål for forsøgsfiskeri dataanalyse er at luger ugyldige eller beskadigede poster. Eksemplet i dette afsnit bestemmer, om felterne breddegrader eller længde indeholde en værdi langt uden for området NYC. Da det antages, at disse poster har en forkert breddegrader-længde værdier, vil vi fjerne dem fra de data, der skal bruges til modellering.

Her er indholdet af *eksempel\_hive\_kvalitet\_assessment.hql* fil til undersøgelsen.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Fra Hive directory bliver spurgt, skal du køre:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Argumentet *-S* inkluderet i denne kommando undertrykker status skærmen udskrift af Hive kort/reducere job. Det er nyttigt, fordi det giver skærmbilledet udskrivning af Hive forespørgslens output lettere at læse.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Udforske: Binær klasse fordeling af forretningsrejse tip

> [AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Til binær klassifikation problemet beskrevet i afsnittet [eksempler på forudsigelse opgaver](machine-learning-data-science-process-hive-walkthrough.md#mltasks) , er det praktisk at vide om et tip fik eller ej. Denne fordelingen af tip er binære:

* tip, der er givet (klasse 1, tip\_beløb > $0)  
* ingen tip (klasse 0, tip\_beløb = $0).

Den *eksempel\_hive\_Vippet\_frequencies.hql* fil, der er vist nedenfor gør dette.

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Fra Hive directory bliver spurgt, skal du køre:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Udforske: Klasse salgsdistributioner i indstillingen multiclass

> [AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Til det multiclass klassifikation problem, der er beskrevet i afsnittet [eksempler på forudsigelse opgaver](machine-learning-data-science-process-hive-walkthrough.md#mltasks) er dette datasæt selve til en naturlig klassifikation, hvor vi vil gerne forudsige mængden af tip givet. Vi kan bruge placeringer til at definere tip områder i forespørgslen. For at få klasse fordelingerne for de forskellige tip områder skal vi bruge den *eksempel\_hive\_tip\_område\_frequencies.hql* fil. Nedenfor finder du dens indhold.

    SELECT tip_class, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount=0, 0,
            if(tip_amount>0 and tip_amount<=5, 1,
            if(tip_amount>5 and tip_amount<=10, 2,
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

Kør følgende kommando fra Hadoop kommandolinjen konsol:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-direct-distance-between-two-longitude-latitude-locations"></a>Udforske: Beregne direkte afstanden mellem to forskellige steder breddegrader længde

> [AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Har du et mål for direkte afstanden giver mulighed at finde ud af forskellen mellem den og faktisk forretningsrejse afstanden. Vi motiverer denne funktion ved at pege ud af, at en passager kan være mindre sandsynligt, at tip Hvis de finde ud af, at driveren bevidst har truffet dem ved en meget længere tid rute.

For at se sammenligningen mellem faktiske forretningsrejse afstand og [Haversine afstanden](http://en.wikipedia.org/wiki/Haversine_formula) mellem to breddegrader længde punkter ("" storcirkelafstanden) skal bruge vi de tilgængelige trigonometriske funktioner i Hive, således:

    set R=3959;
    set pi=radians(180);

    insert overwrite directory 'wasb:///queryoutputdir'

    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
    ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
     *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
     *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
     /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
     +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
     pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
    from nyctaxidb.trip
    where month=1
    and pickup_longitude between -90 and -30
    and pickup_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
    and dropoff_latitude between 30 and 90;

I den ovenstående forespørgsel, R er radius for Earth i sømil og pi konverteres til radianer. Bemærk, at de breddegrader længde punkter "filtreres" for at fjerne værdier, der er langt fra området NYC.

I dette tilfælde skrive vi vores resultater til en mappe med navnet "queryoutputdir". Række kommandoer, der er vist nedenfor først opretter denne outputmappe og derefter kører kommandoen Hive.

Fra Hive directory bliver spurgt, skal du køre:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Forespørgselsresultaterne skrives til 9 Azure BLOB ***queryoutputdir/000000\_0*** til ***queryoutputdir/000008\_0*** under objektbeholderen standard af Hadoop-klynge.

For at se størrelsen på de enkelte BLOB skal køre vi følgende kommando fra Hive directory prompt:

    hdfs dfs -ls wasb:///queryoutputdir

For at se indholdet af en given fil skal du sige 000000\_0, vi bruger Hadoop's `copyToLocal` kommando, og som derfor.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [AZURE.WARNING] `copyToLocal`kan være meget længe for store filer, og anbefales ikke til brug med dem.  

Vigtige udnytte har disse data, der er placeret i en Azure blob er, at vi kan undersøge data i Azure Machine Learning ved hjælp af [Importdata] [ import-data] modul.


## <a name="#downsample"></a>Ned eksempel data og opbygge modeller i Azure Machine Learning

> [AZURE.NOTE] Dette er typisk en **Data Scientist** opgave.

Efter forsøgsfiskeri data analysis fasen er vi nu klar til at ned eksempel dataene til at oprette modeller på Azure Machine Learning. I dette afsnit skal vi viser, hvordan du bruger en Hive-forespørgsel til at ned eksempel de data, som derefter adgang til fra [Importdata] [ import-data] modul i Azure Machine Learning.

### <a name="down-sampling-the-data"></a>Ned indsamle data

Der er to trin i denne procedure. Først vi deltage i tabellerne **nyctaxidb.trip** og **nyctaxidb.fare** på tre taster, der findes i alle poster: "Medaillon", "dermed få\_licens", og "afhentning\_dato/klokkeslæt". Vi derefter oprette en binær klassifikation etiket **Vippet** og en etiket med flere klasse klassifikation **tip\_klasse**.

Prøver fra data direkte fra [Importdata] skal kunne bruge ned[ import-data] modul i Azure Machine Learning, det er nødvendigt at gemme resultaterne af den ovenstående forespørgsel til en intern Hive-tabel. I hvad følger, vi oprette en intern Hive tabel og udfylde dens indhold med de joinforbundne og ned indsamlede data.

Forespørgslen gælder Hive standardfunktioner direkte for at generere timen på dag, uge i året, weekday (1 repræsenterer mandag og 7 repræsenterer søndag) fra den "afhentning\_dato/klokkeslæt" felt og direkte afstanden mellem afhentning og dropoff placeringerne. Brugere kan referere til [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) for en komplet liste over disse funktioner.

Forespørgslen derefter eksempler ned dataene så forespørgselsresultaterne kan være i Azure Machine Learning Studio. Kun omkring 1% af det oprindelige datasæt importeres til Studio.

Nedenfor finder du indholdet af *eksempel\_hive\_forberede\_for\_aml\_full.hql* -fil, der laver data til model opbygning af Azure maskine mere.

        set R = 3959;
        set pi=radians(180);

        create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

        medallion string,
        hack_license string,
        vendor_id string,
        rate_code string,
        store_and_fwd_flag string,
        pickup_datetime string,
        dropoff_datetime string,
        pickup_hour string,
        pickup_week string,
        weekday string,
        passenger_count int,
        trip_time_in_secs double,
        trip_distance double,
        pickup_longitude double,
        pickup_latitude double,
        dropoff_longitude double,
        dropoff_latitude double,
        direct_distance double,
        payment_type string,
        fare_amount double,
        surcharge double,
        mta_tax double,
        tip_amount double,
        tolls_amount double,
        total_amount double,
        tipped string,
        tip_class string
        )
        row format delimited fields terminated by ','
        lines terminated by '\n'
        stored as textfile;

        --- now insert contents of the join into the above internal table

        insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
        select
        t.medallion,
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        t.direct_distance,
        f.payment_type,
        f.fare_amount,
        f.surcharge,
        f.mta_tax,
        f.tip_amount,
        f.tolls_amount,
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
        if(tip_amount>0 and tip_amount<=5,1,
        if(tip_amount>5 and tip_amount<=10,2,
        if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

        from
        (
        select
        medallion,
        hack_license,
        vendor_id,
        rate_code,
        store_and_fwd_flag,
        pickup_datetime,
        dropoff_datetime,
        passenger_count,
        trip_time_in_secs,
        trip_distance,
        pickup_longitude,
        pickup_latitude,
        dropoff_longitude,
        dropoff_latitude,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
        rand() as sample_key

        from nyctaxidb.trip
        where pickup_latitude between 30 and 90
            and pickup_longitude between -90 and -30
            and dropoff_latitude between 30 and 90
            and dropoff_longitude between -90 and -30
        )t
        join
        (
        select
        medallion,
        hack_license,
        vendor_id,
        pickup_datetime,
        payment_type,
        fare_amount,
        surcharge,
        mta_tax,
        tip_amount,
        tolls_amount,
        total_amount
        from nyctaxidb.fare
        )f
        on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
        where t.sample_key<=0.01

For at køre denne forespørgsel fra Hive directory prompten:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Vi har nu en intern tabel "nyctaxidb.nyctaxi_downsampled_dataset", som kan åbnes ved hjælp af [Importdata] [ import-data] modul fra Azure Machine Learning. Desuden kan vi bruge dette dataset opbygning af Machine Learning modeller.  

### <a name="use-the-import-data-module-in-azure-machine-learning-to-access-the-down-sampled-data"></a>Bruge modulet importere Data i Azure Machine Learning til at få adgang til ned indsamlede dataene

Som forudsætninger for udstedelse Hive forespørgsler i dialogboksen [Importdata] [ import-data] modul med Azure Machine Learning vi have adgang til en Azure Machine Learning-arbejdsområde og adgang til legitimationsoplysninger for klyngen og dens tilknyttede lagerplads konto.

Nogle oplysninger om [Importdata] [ import-data] modul og parametrene til at indtaste:

**HCatalog URI-serveren**: Hvis klyngenavnet er abc123 og derefter dette er blot: https://abc123.azurehdinsight.net

**Navnet på Hadoop-brugerkonto** : brugernavnet valgt for klynge (**ikke** brugernavnet fjernadgang)

**Hadoop ser kontoadgangskode** : den adgangskode, du valgte for klynge (**ikke** fjernadgang adgangskoden)

**Placeringen af outputdata** : Dette er valgt at være Azure.

**Kontonavn Azure lager** : navnet på standard lagerplads konto, der er knyttet til klyngen.

**Azure objektbeholder navn** : Dette er objektbeholder standardnavnet for-klyngen, og den er typisk den samme som klyngenavnet. For en klynge, kaldet "abc123", er det kun abc123.

> [AZURE.IMPORTANT] **Alle tabeller, vi vil forespørgsel ved hjælp af [Importdata] [ import-data] modul i Azure Machine Learning skal være en intern tabel.** Et tip for at bestemme om en tabel T i en database D.db er en intern tabel er som følger.

Udstede kommandoen fra Hive directory prompten:

    hdfs dfs -ls wasb:///D.db/T

Hvis tabellen er en intern tabel, og det er udfyldt, skal indholdet vises her. En anden måde at afgøre, om en tabel er en intern tabel er at bruge Azure Storage Explorer. Bruge det til at gå til objektbeholder standardnavnet på klyngen, og klik derefter filtrere efter tabelnavnet. Hvis tabellen og dens indhold vises, skal bekræfter dette, at det er en intern tabel.

Her er et øjebliksbillede af Hive forespørgslen og [Importere Data] [ import-data] modul:

![](./media/machine-learning-data-science-process-hive-walkthrough/1eTYf52.png)

Vær opmærksom på, at siden vores nedad indsamlede data er placeret i beholderen standard, Hive forespørgslen fra Azure Machine Learning er meget simpel og er kun en "Vælg * fra nyctaxidb.nyctaxi\_reduceret\_data".

Datasættet kan nu bruges som udgangspunkt til opbygning af Machine Learning modeller.

### <a name="mlmodel"></a>Oprette modeller på Azure Machine Learning

Vi er nu kunne gå videre til model opbygning og model installation i [Azure Machine Learning](https://studio.azureml.net). Dataene er klar til os til at bruge i håndtere forudsigelse problemerne identificeret ovenfor:

**1. binære klassifikation**: til at forudsige hvorvidt et tip er betalt for en rejse.

**Læring bruges:** To-klasse logistisk regression

en. Til dette problem er vores mål (eller klasse) etiket "Vippet". Vores oprindelige prøver fra ned datasæt har nogle kolonner, der er destinationen lækager for denne klassifikation forsøg. Især: tip\_klasse, tip\_beløb og total\_beløb afsløre oplysninger om mål etiketten, ikke som findes på Test tid. Vi fjerne disse kolonner fra overvejelser ved hjælp af [Vælg kolonner i Dataset] [ select-columns] modul.

Snapshot nedenfor viser vores forsøg at forudsige eller ej et tip er betalt for en given rejse.

![](./media/machine-learning-data-science-process-hive-walkthrough/QGxRz5A.png)

b. For dette forsøg er vores mål etiketten salgsdistributioner omkring 1:1.

Snapshot nedenfor viser fordelingen af tip klasse etiketterne til binær klassifikation problemet.

![](./media/machine-learning-data-science-process-hive-walkthrough/9mM4jlD.png)

Som et resultat, får vi en AUC af 0.987, som vist i nedenstående illustration.

![](./media/machine-learning-data-science-process-hive-walkthrough/8JDT0F8.png)

**2. multiclass klassifikation**: til at forudsige området af tip Beløb betalt for forretningsrejse, ved hjælp af de tidligere defineret klasser.

**Læring bruges:** Multiclass logistisk regression

en. Til dette problem vores mål (eller klasse) etiket er "tip\_klasse" hvilket kan tage et af fem værdier (0,1,2,3,4). Som det binære klassifikation er tilfældet har vi et par kolonner, der er destinationen lækager for dette forsøg. Især: Vippet, tip\_beløb, samlet\_beløb afsløre oplysninger om mål etiketten, ikke som findes på Test tid. Vi fjerne disse kolonner ved hjælp af [Vælg kolonner i Dataset] [ select-columns] modul.

Nedenstående snapshot viser vores forsøg at forudsige hvilken placering et tip der sandsynligvis falder (klasse 0: tip = $0, klassen 1: tip > $0 og tip < = $5, klasse 2: tip > $5 og tip < = $10, klasse 3: tip > $10 og tip < = 20, klasse 4: tip > 20)

![](./media/machine-learning-data-science-process-hive-walkthrough/5ztv0n0.png)

Vi viser nu ser vores faktiske test klasse fordeling ud. Vi se, at mens klasse 0 og klasse 1 er udbredte, andre klasser sjældne.

![](./media/machine-learning-data-science-process-hive-walkthrough/Vy1FUKa.png)

b. For dette forsøg bruge vi en forvirring matrix for at kigge på vores forudsigelse nøjagtighed. Dette er vist nedenfor.

![](./media/machine-learning-data-science-process-hive-walkthrough/cxFmErM.png)

Bemærk, at vores klasse nøjagtighed på almindeligt kendt klasser er helt god, modellen ikke en god sag af "learning" på de sjældne klasser.


**3. regression opgave**: til at forudsige mængden tip, der er betalt for en rejse.

**Læring bruges:** Øget beslutningstræ

en. Til dette problem vores mål (eller klasse) etiket er "tip\_beløb". I dette tilfælde er vores mål lækager: Vippet, tip\_klasse, samlet\_beløb; alle disse variabler videregive oplysninger om det tip beløb, der typisk ikke tilgængelig på Test tid. Vi fjerne disse kolonner ved hjælp af [Vælg kolonner i Dataset] [ select-columns] modul.

Øjebliksbillede belows viser vores forsøg at forudsige mængden af den angivne tip.

![](./media/machine-learning-data-science-process-hive-walkthrough/11TZWgV.png)

b. For regression problemer mål vi nøjagtighed af vores forudsigelse ved at kigge på fejlen kvadrerede i prognoser, koefficient for bestemmelse og lignende. Vi viser disse nedenfor.

![](./media/machine-learning-data-science-process-hive-walkthrough/Jat9mrz.png)

Vi se, om koefficienten for bestemmelse er 0.709, forklares hvilket indebærer omkring 71% af variansen ved vores model koefficienter.

> [AZURE.IMPORTANT] Hvis du vil vide mere om Azure maskine læring og hvordan du kan få adgang til og bruge det, skal du henvise til [Hvad er Machine Learning?](machine-learning-what-is-machine-learning.md). En nyttig ressource for at afspille med en gruppe af Machine Learning forsøg på Azure Machine Learning er [Cortana Intelligence galleriet](https://gallery.cortanaintelligence.com/). Galleriet dækker en farveskala af forsøg og indeholder en fuldstændig Introduktion til udvalg af funktioner i Azure Machine Learning.

## <a name="license-information"></a>Oplysninger om licenser

Dette eksempel gennemgang og dens tilhørende scripts deles af Microsoft under MIT-licensen. Kontroller.txt-fil i mappen på eksempelkoden på GitHub kan finde flere oplysninger.

## <a name="references"></a>Referencer

• [Andrés Monroy NYC Taxi rejser overførselssiden](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC'S Taxi forretningsrejse Data efter Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi og Limousine provision forskning og statistik](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
