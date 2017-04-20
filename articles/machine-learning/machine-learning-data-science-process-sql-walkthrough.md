<properties
    pageTitle="Den Team videnskab proces i aktion: Brug af SQL Server | Microsoft Azure"
    description="Avanceret analyse proces og teknologi i aktion"  
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
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Den Team videnskab proces i aktion: Brug af SQL Server

I dette selvstudium, du gennemgang opbygning og implementering af en machine learning model ved hjælp af SQL Server og et offentligt tilgængelige dataset-- [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) dataset. Proceduren, der følger en standard data videnskab arbejdsproces: indtager og udforske data, tekniker funktioner, der skal fremme læring, og derefter opbygge og implementere en model.


## <a name="dataset"></a>NYC Taxi Trips Dataset beskrivelse

NYC Taxi tur-data er ca. 20GB komprimeret csv-filer (~ 48GB ukomprimeret), består af mere end 173 millioner individuelle trips og billetpriser, der er betalt for hvert enkelt togt. Hver rejse post omfatter afhentning og indlevering placering og tid, anonyme hacke (driver) licensnummer og antallet af Medaillon (taxi's entydigt id). Data, der dækker alle ture i året 2013 og er angivet i de følgende to datasæt for hver måned:

1. 'trip_data' CSV indeholder oplysninger om tur, som antallet af passagerer, afhentning og dropoff punkter, rejse varighed og længde af rejsen. Her er nogle eksempler på poster:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip_fare' CSV indeholder oplysninger om den billetpris, der er betalt for hvert togt, som betalingstype, billetpris beløb, tillæg og afgifter, tip og vejafgifter, og det samlede betalte beløb. Her er nogle eksempler på poster:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Entydig nøgle til at deltage i rejse\_data og rejse\_billetpris består af felter: Medaillon, hacke\_licens og afhentning\_dato og klokkeslæt.

## <a name="mltasks"></a>Eksempler på opgaver, forudsigelse

Vi vil fremsætte tre forudsigelse problemer, der er baseret på den *tip\_beløb*, nemlig:

1. Binære klassificering: forudsige, om et tip er betalt for en rejse, dvs. en *tip\_beløb* , der er større end 0 kr. er et positivt eksempel, mens en *tip\_beløb* $ 0 er et negativt eksempel.

2. Multiclass klassificering: til at forudsige en række tip, der er betalt for rejsen. Vi kan opdele den *tip\_beløb* i fem placeringer eller kategorier:

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. Regression opgave: at forudsige mængden af tip, der er betalt for en rejse.  


## <a name="setup"></a>Indstilling af the Azure videnskab datamiljøet til avanceret analyse

Som du kan se fra programguiden [Planlægge dit miljø](machine-learning-data-science-plan-your-environment.md) , er der flere muligheder for at arbejde med NYC Taxi Trips datasættet i Azure:

- Arbejde med dataene i Azure BLOB derefter model i Azure Machine Learning
- Indlæse data i en SQL Server-databasen og modellen i Azure Machine Learning

I dette selvstudium vil vi demonstrere parallelle masseimport af data til en SQL Server data udforskning, funktion engineering og ved hjælp af SQL Server Management Studio ned prøveudtagning samt bruge IPython notesbog. [Eksempelscripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) og [IPython-pc'er](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) , der deles på GitHub. En prøve IPython notesbog til at arbejde med dataene i Azure BLOB er også tilgængelig på samme sted.

Sådan opsættes Azure Data videnskab-miljø:

1. [Opret en konto i lagerplads](../storage/storage-create-storage-account.md)

2. [Oprette et arbejdsområde på Azure Machine Learning](machine-learning-create-workspace.md)

3. [Bestemmelse af en virtuel maskine til Data videnskab](machine-learning-data-science-setup-sql-server-virtual-machine.md), der fungerer som en SQL Server en server med IPython notesbog.

    > [AZURE.NOTE] Eksempelscripts og IPython bærbare pc'er bliver overført til din Data videnskab virtuelle maskine under installationen. Når scriptet VM efter installationen er fuldført, kan du finde eksemplerne i din VM dokumentbibliotek:  
    > - Eksempel Scripts:`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - Eksempel IPython-pc'er:`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > hvor `<user_name>` er din VM Windows-logonnavn. Vi vil henvise til mapperne eksempel som **Eksempelscripts** og **Prøve IPython notesbøger**.


Baseret på datasættet størrelse, placering af datakilde og valgte Azure destinationsmiljøet, denne situation ligner [scenarie \#5: store datasæt i lokale filer, målrette SQL Server i Azure VM](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Få vist Data fra offentlige kilde

For at få [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) -datasæt fra den delte placering, kan du bruge nogen af de metoder, der er beskrevet i [Flytte Data til og fra Azure Blob Storage](machine-learning-data-science-move-azure-blob.md) til at kopiere dataene til din nye virtuelle maskine.

Kopiere data ved hjælp af AzCopy:

1. Log på din virtual machine (VM)

2. Opret en ny mappe i den VM-datadisk (Note: Brug ikke den midlertidige Disk som følger med VM som en Data-Disk).

3. Kør følgende Azcopy kommandolinjen, skal du erstatte < path_to_data_folder > med mappen data, der er oprettet i (2) i et kommandopromptvindue:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    Når du har fuldført AzCopy, i alt 24 zip-csv-filer (12 til rejse\_data og 12 for rejse\_billetpris) bør være i mappen data.

4. Unzip de hentede filer. Bemærk den mappe, hvor de dekomprimerede filer er placeret. Denne mappe skal kaldes i < stien\_til\_data\_filer\>.

## <a name="dbload"></a>Importer massedata i SQL Server-Database

Du kan forbedre ydeevnen af indlæsning/overførsel af store mængder data til en SQL-database og efterfølgende forespørgsler ved hjælp af _partitioneret tabeller og visninger_. I dette afsnit vil vi følge de instruktioner, der er beskrevet i [Parallelle Bulk Data Import ved hjælp af SQL partitionstabeller](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) til at oprette en ny database og indlæse dataene i partitioneret tabeller parallelt.

1. Mens du er logget din VM, kan du starte **SQL Server Management Studio**.

2. Opret forbindelse ved hjælp af Windows-godkendelse.

    ![Tilslut SSMS][12]

3. Hvis du har endnu ikke ændret SQL Server-godkendelsestilstanden og oprettet en ny SQL-logon-bruger, kan du åbne scriptfil med navnet **ændre\_auth.sql** i mappen **Eksempler på Scripts** . Ændre standardbrugernavn og adgangskode. Klik på **! Udføre** på værktøjslinjen for at køre scriptet.

    ![Kør Script][13]

4. Kontroller og/eller ændre SQL Server standard database- og logfiler mapperne til at sikre, at nyoprettede databaser gemmes på en datadisk. SQL Server-VM-billede, der er optimeret til datawarehousing belastninger er forudkonfigurerede med data- og logfiler diske. Hvis din VM medtog en datadisk, og du har tilføjet nye virtuelle harddiske under installationsprocessen VM, kan du ændre standardmapperne på følgende måde:

    - Højreklik på SQL Server-navn i panelet til venstre, og klik på **Egenskaber**.

        ![Egenskaber for SQL Server][14]

    - Vælg **Databaseindstillinger** på listen **Vælg en side** til venstre.

    - Kontroller og/eller ændre **standardplaceringerne for databasen** til **Datadisk** placeringen af dit valg. Dette er, hvor nye databaser findes, hvis oprettet med standardindstillingerne for placering.

        ![SQL-Database som standard][15]  

5. Hvis du vil oprette en ny database og en række filgrupper til at opbevare de tabeller, der er partitioneret, åbne eksempelscriptet **oprette\_db\_default.sql**. Scriptet opretter en ny database med navnet **TaxiNYC** og 12 filgrupper på standardplaceringen for data. Hver filgruppen skal indeholde en måned efter tur\_data og rejse\_billetpris data. Ændre databasenavnet på, hvis det ønskes. Klik på **! Udføre** til at køre scriptet.

6. Dernæst skal du oprette to partitionstabeller, én for turen\_data og en anden til rejsen\_billetpris. Åbn eksempelscriptet **oprette\_partitioneret\_table.sql**, som vil:

    - Oprette en partition funktion for at opdele dataene efter måned.
    - Oprette en partition ordning til tilknytning af data fra hver måned til en anden filgruppe.
    - Opret to partitioneret tabeller, der er tilknyttet ordningen partition: **nyctaxi\_rejse** rejsen skal indeholde\_data og **nyctaxi\_billetpris** rejsen skal indeholde\_billetpris data.

    Klik på **! Udføre** til at køre scriptet og oprette de tabeller, der er partitioneret.

7. I mappen **Eksempler på Scripts** , der er to PowerShell eksempelscripts, der findes for at dokumentere parallelle bulk import af data til SQL Server-tabeller.

    - **bcp\_parallelle\_generic.ps1** er et generisk script til parallel import massedata i en tabel. Ændre dette script for at angive input- og variabler, som det fremgår af bemærkningslinjer i scriptet.
    - **bcp\_parallelle\_nyctaxi.ps1** er en forudkonfigurerede version af det generelle script, og kan bruges til at indlæse begge tabeller for NYC Taxi Trips-data.  

8. Højreklik på den **bcp\_parallelle\_nyctaxi.ps1** script-navn, og klik på **Rediger** for at åbne den i PowerShell. De foruddefinerede variabler, og foretag ændringer i henhold til dine valgte databasenavn, inputdata mappe, log destinationsmappe og stier til eksempel format filerne **nyctaxi_trip.xml** og **nyctaxi\_fare.xml** (findes i mappen **Eksempler på Scripts** ).

    ![Bulk importdata][16]

    Du kan også vælge godkendelsestilstanden, standard Windows-godkendelse. Klik på den grønne pil på værktøjslinjen til at køre. Scriptet starter 24 import massehandlinger i parallel-12 for hver tabel, der er partitioneret. Du kan overvåge data import status ved at åbne mappen SQL Server standard data som angivet ovenfor.

9. PowerShell-script rapporterer start- og sluttidspunkter. Når alle masse importen er fuldført, rapporteres sluttidspunktet. Kontroller destinationsmappen log for at kontrollere, at masseimporter blev gennemført, dvs. ingen fejl rapporteret i logfilen destinationsmappe.

10. Databasen er nu klar til udforskning, funktion, teknik og andre handlinger efter behov. Da tabellerne, der er opdelt i henhold til den **afhentning\_datetime** feltet forespørgsler, der omfatter **afhentning\_datetime** betingelserne i **WHERE** -udtrykket vil drage fordel af ordningen partition.

11. Gå på opdagelse i det angivne script i **SQL Server Management Studio** **eksempel\_queries.sql**. Marker linjerne forespørgsel for at køre et af eksempler på forespørgslerne, og klik på **! Udføre** på værktøjslinjen.

12. NYC Taxi Trips dataene indlæses i to separate tabeller. For at forbedre joinhandlinger, anbefales det at indeksere tabellerne. Eksempelscriptet **oprette\_partitioneret\_index.sql** opretter partitioneret indekser på den sammensatte joinnøgle **Medaillon, hacke\_licens og afhentning\_dato og klokkeslæt**.

## <a name="dbexplore"></a>Undersøgelse af data og manipulation af funktionen i SQL Server

I dette afsnit skal udfører vi dataundersøgelse og oprettelse af funktion ved at køre SQL-forespørgsler direkte i **SQL Server Management Studio** ved hjælp af SQL Server-database, der er oprettet tidligere. Et eksempel på script med navnet **eksempel\_queries.sql** findes i mappen **Eksempler på Scripts** . Redigere scriptet for at ændre databasenavnet på, hvis den er forskellig fra standard: **TaxiNYC**.

I denne øvelse vil vi:

- Oprette forbindelse til **SQL Server Management Studio** ved hjælp af enten Windows-godkendelse eller bruge SQL-godkendelse og SQL-logon brugernavn og adgangskode.
- Udforsk data fordeling af nogle få felter i forskellige tidsvinduer.
- Undersøge datakvaliteten i felterne længde- og breddegrad.
- Generere binær-og multiclass-etiketter, der er baseret på den **tip\_beløb**.
- Generer funktioner og Beregn/Sammenlign rejse afstande.
- Joinforbinde to tabeller og udtrække en stikprøve, der skal bruges til at oprette modeller.

Når du er klar til at gå videre til Azure Machine Learning, kan du enten:  

1. Gem det endelige SQL-forespørgsel for at udtrække og indsamle data og kopier Indsæt forespørgslen direkte i en [Import af Data] [ import-data] modul i Azure Machine Learning, eller
2. Fortsætter de indsamlede og engineering data, du planlægger at bruge modellen bygger i en ny databasetabel og den nye tabel i [Importdata] [ import-data] modul i Azure Machine Learning.

I dette afsnit vil vi gemme endelige forespørgslen for at udtrække og indsamle data. Den anden metode er dokumenteret i afsnittet [dataundersøgelse og funktion i IPython notesbog](#ipnb) .

For en hurtig kontrol af antallet af rækker og kolonner i tabellerne udfyldes tidligere ved hjælp af parallelle masseimport

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>Udforskning: Rejse distribution af Medaillon

I dette eksempel identificerer Medaillon (taxi tal) med mere end 100 ture inden for en given periode. Forespørgslen kan med fordel partitioneret tabeladgang da det er betinget af partition ordningen af **afhentning\_dato og klokkeslæt**. Forespørgsel om hele datasættet vil også gøre brug af tabellen partitioneret og/eller indeksere scanning.

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Udforskning: Rejse distribution af Medaillon og hack_license

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Vurdering af kvalitet: Kontrollere poster med forkerte længdegrad og/eller latitude

I dette eksempel undersøger, hvis nogen af felterne længdegrad og/eller latitude enten indeholder en ugyldig værdi (radian grader skal være mellem -90 og 90), eller har (0, 0) koordinater.

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Udforskning: Tipped vs. ikke Vippet Trips distribution

I dette eksempel finder antallet af ture, der blev Vippet ikke Vippet i en given tid periode (eller i hele datasættet Hvis dækker hele året). Denne fordeling afspejler fordelingen binære etiket skal bruges senere til modellering af binære klassificering.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>Udforskning: Tip klasse/område Distribution

I dette eksempel beregner fordelingen af tip områder i en bestemt tidsperiode (eller hele datasættet Hvis dækker hele året). Dette er fordelingen af klasserne etiket, der skal bruges senere til modellering af multiclass klassificering.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>Efterforskning: Beregne og sammenligne tur afstand

I dette eksempel konverterer afhentning og indlevering østlig længde og latitude til SQL Geografi peger, beregner afstanden rejse ved hjælp af SQL Geografi point forskel og returnerer en tilfældig stikprøve af resultaterne af sammenligningen. I eksempel begrænser resultaterne til gyldige koordinater kun bruge forespørgslen data kvalitet vurdering omfattet tidligere.

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>Funktionen teknik i SQL-forespørgsler

Etiket generation og Geografi konvertering udforskning forespørgslerne kan også bruges til at generere etiketter og-funktioner ved at fjerne delen optælling. Ekstra funktion engineering SQL eksempler findes i afsnittet [dataundersøgelse og funktion i IPython notesbog](#ipnb) . Det er mere effektivt at køre funktionen til generering af forespørgsler på komplette datasæt eller en stor delmængde af den ved hjælp af SQL-forespørgsler, der kører direkte på forekomsten af SQL Server-database. Forespørgslerne, der kan udføres i **SQL Server Management Studio**, IPython notesbog eller ethvert værktøj/udviklingsmiljø, har adgang til databasen lokalt eller eksternt.

#### <a name="preparing-data-for-model-building"></a>Forberedelse af Data til modellen bygning

Følgende forespørgsel joinforbindelser i **nyctaxi\_rejse** og **nyctaxi\_billetpris** tabeller, opretter en binær klassificering etiket **Vippet**, en etiket med flere klasse klassifikation **tip\_klasse**, og uddrager en tilfældig stikprøve på 1% fra det fulde joinforbundne dataset. Denne forespørgsel kan kopieres og indsættes direkte i [Azure Machine Learning Studio](https://studio.azureml.net) [Importdata] [ import-data] modul til indtagelse af direkte data fra forekomsten af SQL Server-database i Azure. Forespørgslen udelukker poster med forkerte (0, 0) koordinater.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Dataundersøgelse og funktion teknik i IPython notesbog

I dette afsnit vil vi udføre dataundersøgelse og oprettelse af funktion ved hjælp af både Python og SQL forespørgsler i SQL Server-database, der er oprettet tidligere. En prøve IPython notesbog, der hedder **machine-Learning-data-science-process-sql-story.ipynb** findes i mappen **Eksempler IPython-pc'er** . Notesbogen er også tilgængelig på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

Den anbefalede rækkefølge, når du arbejder med store data er følgende:

- Læs i et lille udvalg af dataene i en ramme af data i hukommelsen.
- Udføre nogle visuelle effekter og udforsk undergrunden ved hjælp af de indsamlede data.
- Eksperimenter med funktion engineering ved hjælp af de indsamlede data.
- Bruge Python udsende SQL-forespørgsler direkte til SQL Server-databasen i Azure VM for større dataundersøgelse, datamanipulation, og funktionen teknik.
- Bestemme prøvestørrelse skal bruges til opbygning af Azure Machine Learning model.

Når du er klar til at gå videre til Azure Machine Learning, kan du enten:  

1. Gem det endelige SQL-forespørgsel for at udtrække og indsamle data og kopier Indsæt forespørgslen direkte i en [Import af Data] [ import-data] modul i Azure Machine Learning. Denne metode er påvist i afsnittet [Bygning modeller i Azure Machine Learning](#mlmodel) .    
2. Fortsætter de indsamlede og engineering data, du vil bruge til model bygger i en ny databasetabel, og derefter bruge den nye tabel i [Importdata] [ import-data] modul.

Følgende er nogle data udforskning, datavisualisering og funktion engineering eksempler. Yderligere eksempler finder du under eksempel SQL IPython notesbogen i mappen **Eksempler IPython-pc'er** .

#### <a name="initialize-database-credentials"></a>Initialisere databaselegitimationsoplysninger

Initialisering indstillingerne for forbindelsen til databasen i følgende variabler:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>Oprette databaseforbindelse

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Rapporten antallet af rækker og kolonner i tabellen nyctaxi_trip

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Samlede antal rækker = 173179759  
- Samlet antal kolonner = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Læs i en lille dataprøve fra SQL Server-databasen

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Tid til at læse eksempeltabellen er 6.492000 sekunder  
Antallet af rækker og kolonner, der er hentet = (84952, 21)

#### <a name="descriptive-statistics"></a>Beskrivende statistik

Du er nu klar til at udforske de indsamlede data. Vi starter med at kigge på beskrivende statistik for den **rejse\_afstand** (eller andre) nøglefelterne:

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>Visualisering: Boksen observationsområdet eksempel

Næste skal vi kigge på boksen observationsområdet for rejse afstanden til at visualisere quantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Afbilde #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisering: Distribution observationsområdet eksempel

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Afbilde #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisering: Søjle og linje observationsområder

I dette eksempel vi placering rejse afstanden til fem placeringer og visualisere binning resultater.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Vi kan afbilde ovenfor placering distribution i et panel eller linje område som nedenfor

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Afbilde #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Afbilde #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisering: Scatterplot eksempel

Vi viser punkt område mellem **rejse\_tid\_i\_sek** og **rejse\_afstand** at se, hvis der er nogen sammenhæng

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Afbilde #6][6]

På samme måde kan vi undersøge forholdet mellem **hastighed\_kode** og **rejse\_afstand**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Afbilde #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Lavere prøveudtagning Data i SQL

Når forberedelse af data for model opbygning i [Azure Machine Learning Studio](https://studio.azureml.net), kan enten beslutte på **SQL-forespørgsel direkte i modulet importdata** eller vedvarende engineering og indsamlede data i en ny tabel, som du kan bruge de [Importerede Data] [ import-data] modul med en enkel * *vælge* fra < din\_nye\_tabel\_navn >**.

I dette afsnit vil vi oprette en ny tabel til at holde de indsamlede og engineering data. Et eksempel på en direkte SQL-forespørgsel for modellen bygning findes i afsnittet [dataundersøgelse og funktion i SQL Server](#dbexplore) .

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Oprette en tabel og udfylde med 1% af de joinforbundne tabeller. Slip første tabel, hvis den findes.

I dette afsnit skal vi deltage i tabellerne **nyctaxi\_rejse** og **nyctaxi\_billetpris**, udtrække en stikprøve på 1% og fastholde de indsamlede data i et nyt navn i tabellen **nyctaxi\_et\_%**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Undersøgelse af data ved hjælp af SQL-forespørgsler i IPython notesbog

I dette afsnit undersøger vi data fordelinger ved hjælp af 1% prøver fra dataene som bevares i den nye tabel vi oprettede ovenfor. Bemærk, at lignende Udforsk undergrunden kan udføres ved hjælp af de oprindelige tabeller, eventuelt ved hjælp af **TABLESAMPLE** til at begrænse udforskning eksempel eller ved at begrænse resultaterne til en given tid periode ved hjælp af den **afhentning\_datetime** partitioner, som vist i afsnittet [dataundersøgelse og funktion i SQL Server](#dbexplore) .

#### <a name="exploration-daily-distribution-of-trips"></a>Udforskning: Daglig distribution af trips

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Udforskning: Rejse fordeling pr. Medaillon

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Oprettelse af funktion ved hjælp af SQL-forespørgsler i IPython notesbog

I dette afsnit vil vi oprette nye etiketter og funktioner direkte ved hjælp af SQL-forespørgsler, opererer på 1% eksempeltabel vi oprettede i det forrige afsnit.

#### <a name="label-generation-generate-class-labels"></a>Generering af label: Generere klasse etiketter

I følgende eksempel opretter vi to sæt etiketter, der skal bruges til modellering:

1. Binære klasse etiketter **Vippet** (at forudsige, hvis et tip får)
2. Multiclass etiketter **tip\_klasse** (at forudsige tip placering eller område)

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funktionen teknik: Antal funktioner til kategoriske kolonner

I dette eksempel omdanner en kategoriske felt til et numerisk felt ved at erstatte hver kategori med antallet af dets forekomster på dataene.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funktionen teknik: Placeringen funktioner til numeriske kolonner

I dette eksempel omdanner en kontinuerlig numerisk felt til forudindstillede kategori områder, dvs. Omdan numerisk felt i et kategoriske felt.

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funktionen teknik: Udtrække placering funktioner fra Decimal Latitude/længdegrad

I dette eksempel inddeler den decimale gengivelse af et felt til latitude og/eller længdegrad i flere felter i området af forskellige granularitet f.eks land, by, by, blok osv. Bemærk, at de nye geo-felter ikke er knyttet til faktiske steder. Du kan finde oplysninger om tilknytning af geocode placeringer, [Bing Maps RESTEN tjenester](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Kontroller, at den endelige form i tabellen featurized

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Vi er nu klar til at gå videre til model opbygning og implementering af modellen i [Azure Machine Learning](https://studio.azureml.net). Data, der er klar til de forudsigelser-problemer, der er beskrevet tidligere, nemlig:

1. Binære klassificering: til at forudsige, om et tip er betalt for en rejse.

2. Multiclass klassificering: til at forudsige en række tip, der aflønnes i henhold til de tidligere definerede klasser.

3. Regression opgave: at forudsige mængden af tip, der er betalt for en rejse.  


## <a name="mlmodel"></a>Bygning af modeller i Azure Machine Learning

For at begynde at udøve modellering, logge på Azure Machine Learning-arbejdsområdet. Hvis du endnu ikke har oprettet en machine learning-arbejdsområde, kan du se [oprette et Azure Machine Learning arbejdsområde](machine-learning-create-workspace.md).

1. For at komme i gang med Azure Machine Learning, se [Hvad er Azure Machine Learning Studio?](machine-learning-what-is-ml-studio.md)

2. Log på [Azure Machine Learning Studio](https://studio.azureml.net).

3. Hjemmesiden for Studio tilbyder et væld af oplysninger, videoer, selvstudier, links til referencen moduler og andre ressourcer. Du kan få yderligere oplysninger om Azure Machine Learning høre [Azure Learning dokumentation produktionsressource](https://azure.microsoft.com/documentation/services/machine-learning/).

En typisk uddannelse eksperiment består af følgende:

1. Oprette en **+ Ny** eksperiment.
2. Få vist data på Azure Machine Learning.
3. Forudbehandle, transformere og manipulere data efter behov.
4. Generer funktioner efter behov.
5. Opdele dataene i uddannelse / / valideringstest datasæt (eller har separate datasæt for hver).
6. Vælg en eller flere machine learning algoritmer afhængigt af learning problemet til at løse. F.eks. binære klassificering, multiclass klassificering, regression.
7. Træne en eller flere modeller med uddannelse dataset.
8. Resultat ved hjælp af de uddannede modellen eller modellerne validering dataset.
9. Evaluere modellen eller modellerne for at beregne de relevante målestokke for learning problemet.
10. Fint stil på modellen eller modellerne og vælge den bedste model til at installere.

I denne øvelse har vi allerede set og engineering data i SQL Server og besluttet på prøvestørrelse at indtager i Azure Machine Learning. For at opbygge en eller flere af modellerne, der forudsiger vi besluttet:

1. Få vist data på Azure Machine Learning ved hjælp af [Importdata] [ import-data] modul, findes i sektionen **Data Input og Output** . Yderligere oplysninger finder du under [Importere Data] [ import-data] modulet side.

    ![Azure Machine Learning importdata][17]

2. Vælg **SQL Azure-Database** som **datakilde** i panelet **Egenskaber** .

3. Angiv DNS-navnet på databasen i feltet **Databasenavn server** . Format:`tcp:<your_virtual_machine_DNS_name>,1433`

4. Angiv **navnet på databasen** i det tilsvarende felt.

5. Angiv **SQL-brugernavnet** i det **-aqccount brugernavn og adgangskode i de **Server bruger konto adgangskode **.

6. Kontroller indstillingen **accepterer et servercertifikat** .

7. Indsæt den forespørgsel, der udpakker nødvendige databasefelter (herunder alle beregnede felter som etiketter) og ned prøver i tekstområdet **databaseforespørgsel** Rediger dataene til den ønskede stikprøvestørrelsen.

Et eksempel på en binær klassificering forsøg, der læser data direkte fra SQL Server-databasen er i nedenstående figur. Lignende forsøg kan konstrueres til multiclass klassificering og regression problemer.

![Azure Machine Learning tog][10]

> [AZURE.IMPORTANT] I modellering dataene leveres udvinding og prøveudtagning forespørgsel eksempler i foregående afsnit, **alle etiketter til de tre modellering øvelser er medtaget i forespørgslen**. Vigtigt i alle øvelserne modellering (obligatorisk) er at **udelade** unødvendige etiketterne for de to problemer, og alle andre **mål lækker**. F.eks. Når du bruger binære klassificering, bruge etiket **Vippet** og udelade felter **tip\_klasse**, **tip\_beløb**, og **samlede\_beløb**. Sidstnævnte er målet lækager da de indebærer spidsen betalt.
>
> Du kan bruge [Vælg kolonner i Dataset] for at udelade unødvendige kolonner og/eller målværdier utætheder,[ select-columns] modul eller [Redigere Metadata][edit-metadata]. Yderligere oplysninger finder du under [Vælge kolonner i Dataset] [ select-columns] og [Redigere Metadata] [ edit-metadata] henviser til sider.

## <a name="mldeploy"></a>Anvendelse af modeller i Azure Machine Learning

Når modellen er klar, kan du nemt implementere det som en webtjeneste direkte fra forsøget. Yderligere oplysninger om installation af Azure Machine Learning webtjenester under [installation en Azure Machine Learning-webtjeneste](machine-learning-publish-a-machine-learning-web-service.md).

Du skal installere en ny webtjeneste, skal du:

1. Oprette et resultatmønster eksperiment.
2. Installation af webtjenesten.

For at oprette et resultatmønster forsøg fra en **afsluttet** uddannelse eksperiment, skal du klikke på **Opret VUNDNE EKSPERIMENTERE** på handlingslinjen lavere.

![Azure vundne][18]

Azure Machine Learning forsøger at oprette et resultatmønster forsøg, der er baseret på komponenter i forsøget uddannelse. Det skal navnlig:

1. Gem den model, der er uddannet og fjerne undervisningsmoduler model.
2. Identificere en logisk **inputporten** repræsenterer det forventede inputdata skema.
3. Identificere en logisk **outputport** repræsenterer forventede web service-outputskema.

Når der er oprettet resultatmønster forsøget, gennemgår det og justere efter behov. En typisk justering er at erstatte input dataset og/eller forespørgsel med en, der udelukker etiket felter, som disse er ikke tilgængelig, når tjenesten kaldes. Det er også en god ide at reducere størrelsen af input dataset og/eller forespørgsel med få poster, lige nok til at angive input skemaet. Det er almindeligt at udelukke alle inputfelter og kun inkludere **Scoret etiketter** og **Scoret sandsynligheder** i outputtet ved hjælp af [Vælg kolonner i Dataset] til outputporten,[ select-columns] modul.

En prøve vundne eksperiment er i nedenstående figur. Når du er klar til at installere, klik på **Udgiv WEB SERVICE** på handlingslinjen lavere.

![Udgive Azure Machine Learning][11]

For at opsummere: i dette selvstudium gennemgang, har du oprettet en Azure videnskab datamiljøet, arbejdet med hele vejen fra dataindsamling model, uddannelse og implementering af en webtjeneste Azure Machine Learning offentlige store datasæt.

### <a name="license-information"></a>Licensoplysninger

Denne gennemgang af prøven og dens tilhørende scripts og IPython notebook(s) deles med Microsoft under MIT-licens. Kontroller filen LICENSE.txt i mappen i eksempelkoden på GitHub for at få yderligere oplysninger.

### <a name="references"></a>Referencer

• [Andrés Monroy NYC Taxi Trips overførselssiden](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC'S Taxi rejse Data af Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi og Limousine Kommissionen forskning og statistik](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
