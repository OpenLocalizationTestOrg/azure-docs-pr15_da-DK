<properties
    pageTitle="Team Data videnskabelige processen i praksis: Brug af HDInsight Hadoop klynger på 1 TB Criteo datasættet | Microsoft Azure"
    description="Ved hjælp af Team Data videnskabelige processen til en til slut scenarie, der anvender en HDInsight Hadoop-klynge for at opbygge og anvende en model, ved hjælp af et stort offentligt tilgængeligt (1 TB)-datasæt"
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
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="the-team-data-science-process-in-action---using-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>Team Data videnskabelige processen i aktion - ved hjælp af Azure HDInsight Hadoop klynger på et 1 TB datasæt

I denne gennemgang viser vi ved hjælp af Team Data videnskabelige proces i en til slut scenarie med en [Azure HDInsight Hadoop klynge](https://azure.microsoft.com/services/hdinsight/) at gemme, udforske funktion engineering og ned eksempeldataene fra en af de offentligt tilgængelige [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datasæt. Vi bruger Azure Machine Learning til at opbygge en binær klassifikation model på disse data. Vi viser også, hvordan du publicerer en af disse modeller som en webtjeneste.

Du kan også bruge en IPython notesbog til at udføre de opgaver, der vises i denne gennemgang. Brugere, der vil prøve denne metode bør kontakte emnet [Criteo gennemgang ved hjælp af en Hive ODBC-forbindelse](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .


## <a name="dataset"></a>Beskrivelse af Criteo datasæt

Criteo data er et klik på forudsigelse datasæt, der er 370GB gzip komprimeret TSV filer (~1.3TB ikke-komprimeret format), der omfatter mere end 4,3 milliarder poster. Det er hentet fra 24 dage efter Klik på data, der er gjort tilgængelige ved [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Vi har pakket data, der er tilgængelige for os til at eksperimentere med for at gøre nemmere for videnskabelige eksperter data.

Hver post i dette dataset indeholder 40 kolonner:

- den første kolonne er en kolonne med etiketter, der angiver, om en bruger klikker på en **tilføje** (værdi 1) eller ikke klikke på et (værdien 0)
- næste 13 kolonner er numeriske, og
- sidste 26 er kategoriske kolonner

Kolonnerne er anonyme og bruge en række specificeret navne: "Kol1" (for kolonnen navn) til ' Col40 "(for den sidste kategoriske kolonne).            

Her er et uddrag af de første 20 kolonner på to observationer (rækker) fra dette dataset:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10   Col11   Col12   Col13   Col14   Col15           Col16           Col17           Col18           Col19       Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Der er manglende værdier i begge de numeriske og kategoriske kolonner i dette dataset. Vi beskriver en enkelt metode til håndtering af manglende værdier. Yderligere oplysninger om dataene, der er set, når vi gemme dem i Hive tabeller.

**Definition:** *Gennemkliksprocent (Arbejdscenter):* Dette er procentdelen af klikker i dataene. I dette Criteo dataset er Arbejdscenter om 3.3% eller 0.033.

## <a name="mltasks"></a>Eksempler på forudsigelse opgaver
To eksempel forudsigelse problemer behandles i denne gennemgang:

1. **Binær klassifikation**: beregner om en bruger har klikket på et Tilføj:
    - Klasse 0: Ikke er noget Klik
    - Klasse 1: Klik på

2. **Regression**: beregner sandsynligheden for et ad Klik fra brugerfunktioner.


## <a name="setup"></a>Angive en HDInsight Hadoop-klynge i videnskabelige data

**Note:** Dette er typisk en opgave i en **administrator** .

Konfigurere dit Azure Data videnskabelige miljø til opbygning af skønnet analytics løsninger med HDInsight klynger med tre trin:

1. [Opret en lagerplads konto](../storage/storage-create-storage-account.md): denne lagerplads konto bruges til at gemme data i Azure Blob-lager. De data, der bruges i HDInsight klynger er gemt her.

2. [Tilpasse Azure HDInsight Hadoop klynger for Data videnskabelige](machine-learning-data-science-customize-hadoop-cluster.md): dette trin opretter en Azure HDInsight Hadoop-klynge med 64-bit Anaconda Python 2.7 installeret på alle noder. Der er to vigtige trin (beskrevet i dette emne) til at udføre, når du tilpasser HDInsight klynge.

    * Du skal knytte kontoen lagerplads oprettede i trin 1 med din HDInsight klynge, når den er oprettet. Denne lagerplads konto bruges til at få adgang til data, der kan behandles inden for klyngen.

    * Du skal aktivere fjernadgang til noden hoved af klyngen, når den er oprettet. Husk på fjernadgang til legitimationsoplysninger, du angiver her (forskellige fra dem, der er angivet for klynge på oprettelsen): Du skal bruge dem til at udføre følgende procedurer.

3. [Opret en Azure ML arbejdsområde](machine-learning-create-workspace.md): denne Azure maskine læ arbejdsområde er bruges til at bygge machine learning modeller efter en indledende data udforske og ned stikprøver, hvor på HDInsight klynge.

## <a name="getdata"></a>Hente og bruge data fra en offentlig datakilde

[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datasættet kan åbnes ved at klikke på hyperlinket, accepterer vilkårene for anvendelse og indtaste et navn. Et øjebliksbillede af hvordan det ser ud, er vist her:

![Acceptér Criteo vilkår](./media/machine-learning-data-science-process-hive-criteo-walkthrough/hLxfI2E.png)

Klik på **Fortsæt for at Download** for at få mere at vide om datasættet og dens tilgængelighed.

Data, der er placeret i en offentlig [Azure blob storage](../storage/storage-dotnet-how-to-use-blobs.md) placering: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" refererer til Azure Blob-lager placering. 

1. Dataene i denne offentlige blob-lager består af tre undermapper udpakket data.

    1. Undermappen *rå/antal/* indeholder de første 21 dage data - fra dag\_00 til dag\_20
    2. Undermappen *rå/tog/* består af en enkelt dag af data, dag\_21
    3. Undermappen *rå/afprøve/* består af to dage af data, dag\_22 og dag\_23

2. For dem, der vil starte med dataene, rå gzip, dette er også tilgængelig i den primære mappe *rå /* som day_NN.gz, hvor NN går fra 00 til 23.

En alternativ metode til at få adgang til, udforske og model disse data, der ikke kræver en hvilken som helst lokale overførsler forklares senere i denne gennemgang, når vi opretter Hive tabeller.

## <a name="login"></a>Log på klynge headnode

For at logge på headnode af klyngen skal du bruge [Azure portal](https://ms.portal.azure.com) til at finde klyngen. Klik på ikonet HDInsight legetøjselefant til venstre, og dobbeltklik derefter på navnet på din klynge. Gå til fanen **konfiguration** , dobbeltklik på ikonet Opret forbindelse nederst på siden, og Angiv dine fjernadgang til legitimationsoplysninger, når du bliver bedt om. Du føres til headnode af klyngen.

Her er, hvad en typisk første log på klynge headnode ser sådan ud:

![Log på klynge](./media/machine-learning-data-science-process-hive-criteo-walkthrough/Yys9Vvm.png)


Vi se "Hadoop kommandolinjen", som er vores arbejdshesten for undersøgelse af data i venstre side. Vi kan også se to nyttige URL-adresser - "Hadoop garn Status" og "Hadoop navn Node". URL-adressen med garn status viser status for job og URL-adressen med navnet node giver detaljer på klyngekonfigurationen.

Nu vi er konfigureret og klar til at starte første del af denne gennemgang: undersøgelse af data ved hjælp af Hive og henter data er du klar til Azure Machine Learning.

## <a name="hive-db-tables"></a>Oprette Hive database og tabeller

For at oprette Hive tabeller til vores Criteo datasæt, Åbn ***Hadoop kommandolinjen*** på skrivebordet på noden hoved, og Angiv Hive-mappen ved at angive kommandoen

    cd %hive_home%\bin

>[AZURE.NOTE] Køre alle Hive-kommandoer i denne gennemgang fra Hive placeringen / prompten til mappen. Dette sig tager af de problemer, stien automatisk. Vi bruger betingelserne "Hive directory prompt", "Hive placering / prompten til mappen", og "Hadoop-kommandolinjen" i flæng.

>[AZURE.NOTE]  Hvis du vil udføre en Hive-forespørgsel, kan en altid bruge følgende kommandoer:

        cd %hive_home%\bin
        hive

Når Hive Erstat vises med en "hive >"logge, skal du blot klippe og indsætte forespørgsel for at udføre den.

Følgende kode opretter en database "criteo" og derefter opretter 4 tabeller:


* en *tabel til at oprette tæller* bygget på dage dag\_00 til dag\_20,
* en *tabel til brug som tog datasættet* bygget på dagen\_21, og
* to *tabeller til brug som test datasæt* bygget på dagen\_22 og dag\_23 henholdsvis.

Vi Opdel vores test datasæt i to forskellige tabeller, fordi en af dagene, der er en fridag, og vi vil gerne vide, hvis modellen kan registrere forskelle mellem en Feriekalender og ikke-helligdage fra gennemkliksprocent.

Scriptet [eksempel & #95; hive & #95; oprette & #95; criteo & #95; database & #95; og & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) vises her for at gøre nemt:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Vi Bemærk, at alle disse tabeller er eksterne, som vi blot peger på Azure Blob-lager (wasb) placeringer.

**Der er to måder at udføre alle Hive forespørgsel, som vi nu omtale.**

1. **Ved hjælp af den kommandolinjen Hive Erstat**: først er at udstede en "hive" kommandoen og kopiere og indsætte en forespørgsel på den Hive Erstat kommandolinjen. Gør følgende for at gøre dette:

        cd %hive_home%\bin
        hive

    Nu på Erstat kommandolinjen udfører klippe og indsætte forespørgslen den.

2. **Gemme forespørgsler til en fil og udfører kommandoen**: andet er at gemme forespørgsler til en .hql-fil ([eksempel & #95; hive & #95; oprette & #95; criteo & #95; database & #95; og & #95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) og derefter udstede følgende kommando for at udføre forespørgslen:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### <a name="confirm-database-and-table-creation"></a>Bekræft oprettelse af database og tabel

Dernæst skal vi bekræfter, at oprettelsen af databasen med følgende kommando fra Hive placeringen / prompten til mappen:

        hive -e "show databases;"

Resultatet er:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Dette bekræfter, at oprettelsen af den nye database, "criteo".

For at se, hvilke tabeller, vi oprettede, vi blot udstede kommandoen fra Hive placeringen / prompten til mappen:

        hive -e "show tables in criteo;"

Vi derefter får vist i følgende eksempel:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Udforske data i Hive

Vi er nu klar til at gøre nogle grundlæggende data udforske i Hive. Vi begynder ved at tælle antallet af eksemplerne i tog og teste datatabeller.

### <a name="number-of-train-examples"></a>Antallet af tog eksempler

Indholdet af [eksempel & #95; hive & #95; Tæl & #95; tog & #95; tabel & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) er vist her:

        SELECT COUNT(*) FROM criteo.criteo_train;

Dette giver:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Du kan også en også udstede følgende kommando fra Hive placeringen / prompten til mappen:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Antallet af test eksemplerne i de to test-datasæt

Vi nu tælle antallet af eksemplerne i de to datasæt test. Indholdet af [eksempel & #95 hive & #95 Tæl & #95; criteo & #95; test & #95; dag & #95; 22 & #95; tabel & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) er her:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Dette giver:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Som du plejer, vi kan også ringe til scriptet fra Hive placeringen / mappe meddelelse ved udstede kommandoen:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Til sidst skal vi undersøge antallet test eksempler i test datasættet baseret på dag\_23.

Kommandoen du gør dette, der ligner den, der kun vises (se [eksemplet på et & #95; hive & #95; Tæl & #95; criteo & #95; test & #95; dag & #95; 23 & #95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Resultatet er:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Etiket fordeling i tog datasættet

Etiket fordelingen i datasættet tog er af interesse. Hvis du vil se den, viser vi indholdet af [eksempel & #95; hive & #95; criteo & #95; etiket & #95; fordeling & #95; tog & #95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Dette giver en etiket fordeling:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Bemærk, at procentdelen af positive etiketter om 3.3% (i overensstemmelse med det oprindelige datasæt).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram fordeling af nogle numeriske variabler i tog datasættet

Vi kan bruge hives oprindelig "histogram\_numeriske" funktionen til at finde ud af, hvordan fordelingen af numeriske variabler ser ud. Her er indholdet af [eksempel & #95; hive & #95; criteo & #95; histogram & #95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Dette giver følgende:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

TVÆRGÅENDE Vis - adskille kombination i Hive tjener at producere en SQL-lignende afgang i stedet for listen normalt. Bemærk, at denne tabel, den første kolonne svarer til placeringen center og andet frekvens placering.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Tilnærmet fraktiler af nogle numeriske variabler i tog datasættet

Er også beregning af tilnærmet fraktiler af interesse med numeriske variabler. Hive er oprindelige "fraktil\_ca." gør dette til os. Indholdet af [eksempel & #95; hive & #95; criteo & #95; tilnærmet & #95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) er:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Dette giver:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Vi en bemærkning, at fordelingen af fraktiler er tæt forbundet med histogram fordelingen af et numerisk variabel som regel.        

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Finde antallet af unikke værdier for nogle kategoriske kolonner i tog datasættet

Fortsætter data udforske finde vi nu, for nogle kategoriske kolonner, antallet af entydige værdier, der måske. For at gøre dette, viser vi indholdet af [eksempel & #95; hive & #95; criteo & #95; entydige & #95, værdier og #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Dette giver:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Vi Bemærk, at Col15 har 19M entydige værdier! Brug af naïve teknikker som "én hot kodning" er for at kode sådanne høj flerdimensionale kategoriske variabler umuligt. Især vi forklare og viser en effektiv, robust teknik, der kaldes [Learning med tæller](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) i effektivt håndteringen af dette problem.

Vi afslutte denne underafsnit ved at se antallet af entydige værdier for nogle andre kategoriske kolonner. Indholdet af [eksempel & #95; hive & #95; criteo & #95; entydige & værdier og #95 #95; multiplum & #95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) er:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Dette giver:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Vi se igen, med undtagelse af Col20, alle de øvrige kolonner har mange unikke værdier.

### <a name="co-occurence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Tæller samtidig forekomst af par af kategoriske variabler i tog datasættet

Antallet af samtidig forekomst af par af kategoriske variabler er også interesse. Dette kan bestemmes ved hjælp af koden i [eksempel & #95; hive & #95; criteo & #95; parvis & #95; kategoriske & #95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Vi modsat Sortér antallet af poster efter deres forekomst og se øverst 15 i dette tilfælde. Dette giver os:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a>Eksempel på datasæt til Azure Machine Learning ned

Har du set på datasæt og vist, hvordan vi kan gøre denne type udforske for en hvilken som helst variabler (herunder kombinationer), vi nu ned eksempel datasættene så vi kan udvikle modeller i Azure Machine Learning. Tilbagekalde, som vi fokusere på problemet er: Hvis du har et sæt eksempel attributter (funktionen værdier fra Kol2 - Col40), vi forudsige om Kol1 er 0 (ikke er noget klik) eller værdien 1 (klik).

Hvis du vil ned eksempel vores tog og afprøve datasæt til 1% af den oprindelige størrelse, skal bruge vi hives oprindelige funktionerne RAND() funktionen. Det næste script [eksempel & #95; hive & #95; criteo & #95; Reducer pixelantallet & #95; tog & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) du gør dette for tog datasæt:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Dette giver:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Script [eksempel & #95; hive & #95; criteo & #95; Reducer pixelantallet & #95; test & #95; dag & #95; 22 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) betyder det for testdata, dag\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Dette giver:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Til sidst skal script [eksempel & #95; hive & #95; criteo & #95; Reducer pixelantallet & #95; test & #95; dag & #95; 23 & #95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) betyder det for testdata, dag\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Dette giver:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Med dette er vi klar til at bruge vores ned indsamlede tog og teste datasæt opbygning af modeller i Azure Machine Learning.

Er der en endelige vigtige komponent, før vi gå videre til Azure Machine Learning, som er problemstillinger tabellen antal. I den næste underordnede afsnit beskriver vi dette nogle detaljeret.

##<a name="count"></a>En kort beskrivelse i tabellen antal

Som vi har set, har flere kategoriske variabler en meget høj dimensionalitet. I vores gennemgang præsentere vi en effektiv teknik, der kaldes [Learning med tæller](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) for at kode disse variabler i en effektiv, robust måde. Flere oplysninger om denne metode er i hyperlinket findes.

**Note:** I denne gennemgang fokuserer vi på ved hjælp af antal tabeller til at producere kompakt repræsentationer af høj flerdimensionale kategoriske funktioner. Dette er ikke den eneste måde til at kode kategoriske funktioner. Yderligere oplysninger om andre teknikker interesseret brugere kan se [en-hot-kodning](http://en.wikipedia.org/wiki/One-hot) og [funktion krypteres](http://en.wikipedia.org/wiki/Feature_hashing).

For at opbygge antal tabeller på Tæl dataene skal bruge vi dataene i rå/antallet mapper. I sektionen modellering vi vise brugere, hvordan du kan opbygge disse antal tabeller til kategoriske funktioner fra bunden, eller du kan også bruge en færdigbyggede Tæl tabel til deres Udforsk undergrunden. I hvilke følger, når vi refererer til "færdigbyggede antal tabeller" mener vi ved hjælp af de antal tabeller, som vi giver. Der findes detaljerede instruktioner til, hvordan du kan få adgang til disse tabeller i næste afsnit.

## <a name="aml"></a>Oprette en model med Azure Machine Learning

Vores model oprettelsesproces i Azure Machine Learning følger disse trin:

1. [Hente data fra Hive tabeller til Azure Machine Learning](#step1)
2. [Oprette forsøget: rydde dataene, skal du vælge en læring og featurize med antal tabeller](#step2)
3. [Uddannelse af modellen](#step3)
4. [Resultat modellen på testdata](#step4)
5. [Evaluere modellen](#step5)
6. [Publicere modellen som en webtjeneste til forbrug](#step6)

Vi er nu klar til at oprette modeller i Azure Machine Learning studio. Vores ned indsamlede data er gemt som Hive tabeller i klyngen. Vi bruger modulet Azure Machine Learning **Importere Data** til at læse disse data. Legitimationsoplysninger for at få adgang til kontoen lagring af denne klynge findes i hvad følger.

### <a name="step1"></a>Trin 1: Hente data fra Hive tabeller til Azure maskine læ bruger modulet importdata og vælge den til en maskine læ forsøg

Start ved at vælge en **+ Ny** -> **forsøg** -> **Tom forsøg**. Derefter fra **søgefeltet i øverste venstre hjørne,** søge efter "Importer Data". Træk og slip modulet **Importere Data** til forsøg lærredet (den midterste del af skærmen) for at bruge modulet til adgang til datakilder.

Dette er hvordan **Importdata** ser ud, mens du henter data fra tabellen Hive:

![Importdata henter data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/i3zRaoj.png)

For modulet **Importdata** er værdierne for de parametre, der findes i grafikken kun eksempler på værdier, skal du angive type. Her er nogle generelle vejledning om, hvordan du udfylder den parameter, der er angivet for modulet **Importdata** .

1. Vælg "Forespørgsel om Hive" for **Datakilde**
2. I feltet **Hive databaseforespørgsel** , en simpel SELECT * FROM < din\_database\_name.your\_tabel\_navn >-er nok.
3. **Hcatalog URI-serveren**: Hvis din klynge er "abc" og derefter dette er blot: https://abc.azurehdinsight.net
4. **Navnet på Hadoop-brugerkonto**: brugernavnet, der er valgt på tidspunktet for ibrugtagning klyngen. (Ikke fjernadgang brugernavnet!)
5. **Hadoop-adgangskode til brugerkonto**: adgangskoden til det brugernavn, der er valgt på tidspunktet for ibrugtagning klyngen. (Ikke fjernadgang adgangskoden!)
6. **Placeringen af outputdata**: Vælg "Azure"
7. **Kontonavn Azure lager**: kontoen lagerplads, der er knyttet til klyngen
8. **Azure-lager kontonøgle**: tasten lagerplads-konto, der er knyttet til klyngen.
9. **Azure objektbeholder navn**: Hvis klyngenavn er "abc" og derefter dette er blot "abc," som regel.


Når **Importdata** afsluttes henter data (du ser den grønne aksemærker på modulet), skal du gemme dataene som et datasæt (med et navn på dit valg). Hvordan den ser ud:

![Importdata gemme data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oxM73Np.png)

Højreklik på outputporten i modulet **Importdata** . Dette viser valgmuligheden **Gem som datasæt** og en indstilling for **visuelle effekter** . Indstillingen **visuelle effekter** , viser Hvis du har klikket på, 100 rækker af data sammen med en højre panel, som egner sig til nogle oversigt statistik. For at gemme data, skal du blot vælge **Gem som datasæt** og følg vejledningen.

Du kan vælge den gemte datasæt til brug i en machine learning forsøg, Find datasæt ved hjælp af feltet **Søg** , der er vist i følgende figur. Derefter skal du blot skrive navnet, du har givet datasæt delvist til at få adgang til den, og træk datasættet til panelet primære. Slippe den på panelet primære markerer det til brug i machine learning modellering.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/cl5tpGw.png)

>[AZURE.NOTE] Gøre dette for både tog og test datasæt. Husk også at bruge databasenavnet og tabelnavne, som du har givet til dette formål. Værdierne, der bruges i figur er udelukkende til illustration purposes.* *

### <a name="step2"></a>Trin 2: Oprette en simpel forsøg i Azure Machine Learning til at forudsige Klik / ingen Klik

Vores Azure ML forsøg ser sådan ud:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xRpVfrY.png)

Vi undersøger nu de vigtigste komponenter i dette forsøg. Som en påmindelse, har vi brug at trække vores gemte tog og teste datasæt til vores forsøg lærred først.

#### <a name="clean-missing-data"></a>Rydde Data, der mangler

Modulet **Ren manglende Data** indeholder hvad navnet foreslår: den rydder manglende data på måder, som kan være brugerdefineret. Søger i dette modul, se vi dette:

![Rens manglende data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0ycXod6.png)

Vi valgte her, til at erstatte alle manglende værdier med 0. Der findes andre konfigurationsmuligheder samt, som kan ses ved at kigge på rullemenuerne i modulet.

#### <a name="feature-engineering-on-the-data"></a>Funktionen teknisk på dataene

Der kan være millioner af entydige værdier for nogle af store datasæt kategoriske funktioner. Brug af naïve metoder som en hot kodning til at repræsentere disse høj flerdimensionale kategoriske funktioner er helt umuligt. I denne gennemgang viser vi, hvordan du bruger Tæl funktioner ved hjælp af indbyggede Azure Machine Learning moduler til at generere kompakt repræsentationer af variablerne høj flerdimensionale kategoriske. Afslut-resultatet er mindre model, kursus hurtigere og ydeevne målinger, som er helt sammenlignes med brug af andre teknikker.

##### <a name="building-counting-transforms"></a>Opbygning af optælling omdanner

For at opbygge Tæl funktioner skal bruge vi modulet **Opbygge optælling transformere** , der er tilgængelige i Azure Machine Learning. Modulet, der ser sådan ud:


![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/e0eqKtZ.png)
![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OdDN0vw.png)


**Vigtig bemærkning** : Vi Angiv disse kolonner, som vi ønsker at udføre tæller på i feltet **antal kolonner** . Det er normalt (som nævnt) høj flerdimensionale kategoriske kolonner. I starten, nævnt, Criteo datasættet har 26 kategoriske kolonner: fra Col15 til Col40. Her kan vi tælle på alle dem, og giv deres indeks (fra 15 til 40 adskilt af kommaer som vist).

For at anvende modul i tilstanden MapReduce (relevant for store datasæt) vi skal have adgang til en HDInsight Hadoop-klynge (det, der bruges til funktionen udforske kan genbruges til dette formål samt) og dens legitimationsoplysninger. Forrige tallene illustrere hvilke udfyldt værdierne ud (erstatte de værdier, der er knyttet til illustration med de relevante for dit eget use case).

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/05IqySf.png)

I den ovenstående figur viser vi hvordan du angiver input blob placeringen. Denne placering indeholder de data, der er reserveret til bygger antal tabeller på.


Når dette modul er færdig, kan vi gemme transformeringen til senere ved at højreklikke på modulet og vælge indstillingen **Gem som transformation** :

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IcVgvHR.png)

I vores forsøg arkitektur ovenstående, svarer datasættet "ytransform2" nøjagtigt til en gemt Tæl transformation. For resten af dette forsøg antager vi, at læseren bruges et **Opbygge optælling transformere** modul på nogle data til at generere tæller, og derefter kan bruge disse tæller til at generere Tæl funktioner på tog og teste datasæt.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Vælge, hvilke antal funktioner, hvis du vil medtage som en del af tog og test datasæt

Når vi har en optælling, transformere klar, kan brugeren vælge de funktioner, som skal medtages i deres tog og teste datasæt, der bruger modulet **Ændre antal tabel parametre** . Vi blot at vise dette modul her for fuldstændighed, men hensyn til enkel faktisk bruger den ikke i vores forsøg.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/PfCHkVg.png)

I dette tilfælde, som kan ses, har vi valgt at bruge lige log-sandsynligheden og ignorere baggrunden fra kolonne. Vi kan også angive parametre som i grænseværdi for garbage placering, hvor mange pseudo forudgående eksempler for at tilføje for udglatning, og om du vil bruge en hvilken som helst Laplacian støj eller ej. Alle disse avancerede funktioner, og det er at der er angivet, standardværdierne er et godt udgangspunkt for brugere, der er ny bruger af denne type generering af funktion.

##### <a name="data-transformation-before-generating-the-count-features"></a>Datatransformation før der oprettes funktionerne Tæl

Nu vi fokusere på et vigtigt punkt om at transformere vores tog og teste data før faktisk genererende Tæl funktioner. Bemærk, at der er to **Udføre R Script** moduler, der bruges før vi anvender Tæl transformeringen til vores data.

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/aF59wbc.png)

Her er det første R-script:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/3hkIoMx.png)


I denne R-script Omdøb vi vores kolonner til navne "Kol1" til "Col40". Dette skyldes, at Tæl transformationen forventer navnene på dette format.

I den anden R-script, vi saldo fordeling mellem positive og negative klasser (klasser henholdsvis 1 og 0) ved reduktion af pixelantallet klassen negative. R scriptet her viser, hvordan du gør dette:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/91wvcwN.png)

I denne enkle R-script, vi bruge "pos\_neg\_forholdet" til at angive mængden balance mellem positivt og negative klasser. Dette er vigtigt, at da forbedring klasse ubalance normalt har forbedret ydeevne for klassifikation problemer, hvor den klasse fordeling er skæv (tilbagekaldelsen, i dette tilfælde har vi 3.3% positive klasse og 96.7% negative klasse).

##### <a name="applying-the-count-transformation-on-our-data"></a>Anvende Tæl transformation på vores data

Til sidst, kan vi bruge modulet **Anvende Transformation** til at anvende antal transformationer på vores tog og teste datasæt. Dette modul tager den gemte Tæl transformation som ét input og tog eller test datasæt som andre input, og returnerer data med Tæl funktioner. Det er vist her:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Et uddrag af Tæl funktioner ud

Det er vejledende at se, hvordan funktionerne Tæl ser ud i vores store og små bogstaver. Vi viser her et uddrag af dette:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/FO1nNfw.png)

I denne uddrag viser vi, at for de kolonner, som vi tælles på vi få antallet af og log sandsynligheden ud over eventuelle relevante backoffs.

Vi er nu klar til at opbygge en Azure Machine Learning-model ved hjælp af disse transformerede datasæt. I næste afsnit viser vi, hvordan dette kan gøres.

#### <a name="azure-machine-learning-model-building"></a>Azure Machine Learning model dokumentkomponent

##### <a name="choice-of-learner"></a>Valg af læring

Først skal vi brug at vælge en læring. Vi vil bruge et beslutningstræ to klasse øget som vores læring. Her er standardindstillingerne for denne læring:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/bH3ST2z.png)

Vi vil vælge standardværdierne for vores forsøg. Vi Bemærk, at standardindstillingerne er som regel beskrivende og en god måde at få hurtig grundlinjer på ydeevne. Du kan forbedre ydeevnen ved hele parametre, hvis du vælger at, når du har en oprindelig plan.

#### <a name="train-the-model"></a>Uddannelse af modellen

Til uddannelse, kan vi blot kalde et **Tog Model** modul. De to input til den er to klasse øget beslutningstræ læring og vores tog datasæt. Dette er vist her:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/2bZDZTy.png)


#### <a name="score-the-model"></a>Resultat modellen

Når vi har en erfaren model, er vi klar til at give på test datasættet og til at evaluere dens ydeevne. Vi gør dette ved hjælp af modulet **Karakteren Model** , der er vist i følgende figur, sammen med et **Evaluere Model** modul:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/fydcv6u.png)


### <a name="step5"></a>Trin 5: Evaluere modellen

Til sidst skal vil vi analysere model ydeevne. Normalt for to klasse (binære) klassifikation problemer er en god måling på AUC. For at visualisere, Tilslut vi modulet **Karakteren Model** til et **Evaluere Model** -modul til dette. Klikke på **visuelle effekter** i modulet **Evaluere Model** giver et billede som på følgende:

![Evaluere modul BDT model](./media/machine-learning-data-science-process-hive-criteo-walkthrough/0Tl0cdg.png)

I binær (eller to klasse) klassifikation problemer, der er en god mål for nøjagtigheden af Tekstfuldførelse er i området Under kurve (AUC). I hvad følger, viser vi vores resultater ved at bruge denne model på vores test datasættet. Hvis du vil have, skal du højreklikke på outputport på **Evaluere Model** modul og derefter **visuelle effekter**.

![Visualisere evaluere Model modul](./media/machine-learning-data-science-process-hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step6"></a>Trin 6: Publicere modellen som en webtjeneste
Muligheden for at publicere en Azure Machine Learning model som webtjenester med et minimum af besvær er en nyttig funktion til at oprette den alment tilgængelig. Når det er gjort, kan alle, foretage opkald til webtjenesten med indtastede data, de skal bruge prognoser for, og webtjenesten bruger modellen til at returnere disse prognoser.

For at gøre dette, gemme vi først vores erfaren model som en erfaren Model objekt. Det gør du ved at højreklikke på modulet, der **Tog Model** og ved hjælp af indstillingen **Gem som erfaren Model** .

Dernæst skal vi brug for til at oprette input og output porte til vores webtjeneste:

* en input port henter data i den samme form som de data, som vi skal prognoser for
* en outputport returnerer etiketterne karakter og de tilknyttede sandsynligheder.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Vælg et par rækker af data for input port

Det er praktisk at bruge et **Anvende SQL Transformation** modul til at vælge blot 10 rækker, der skal fungere som input port dataene. Vælg kun disse rækker af data til vores input port ved hjælp af den SQL-forespørgsel, der er vist her:

![Indtast port data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webtjeneste
Vi er nu klar til at køre en lille forsøg, der kan bruges til at publicere vores webtjeneste.

#### <a name="generate-input-data-for-webservice"></a>Generere inputdataene til webtjeneste

Som et trin i zeroth, da tabellen antal er stort, vi tage et par linjer af testdata og generere outputdata fra den med Tæl funktioner. Dette kan fungere som input dataformatet til vores webtjeneste. Dette er vist her:

![Oprette BDT indtastede data](./media/machine-learning-data-science-process-hive-criteo-walkthrough/OEJMmst.png)

>[AZURE.NOTE] For det indtastede dataformat bruge vi nu OUTPUT fra modulet **Tæl Featurizer** . Når dette forsøg er færdig, kan du gemme output fra modulet **Tæl Featurizer** som et datasæt. Dette Dataset bruges til at inputdataene i webtjeneste.

#### <a name="scoring-experiment-for-publishing-webservice"></a>Vundne forsøg til publicering webtjeneste

Først skal viser vi hvordan den ser ud. Væsentlige strukturen er et **Resultat Model** modul, der accepterer vores erfaren model objekt og et par linjer af indtastede data, som vi oprettede i de forrige trin, der bruger modulet **Tæl Featurizer** . Vi bruger "Vælg kolonner i Dataset" til project Scored etiketterne og karakteren sandsynligheder.

![Vælg kolonner i datasættet](./media/machine-learning-data-science-process-hive-criteo-walkthrough/kRHrIbe.png)

Bemærk, hvordan modulet **Vælg kolonner i Dataset** kan bruges til 'filtrering' data fra et datasæt. Vi viser indholdet her:

![Filtrering med de Vælg kolonner i Dataset modul](./media/machine-learning-data-science-process-hive-criteo-walkthrough/oVUJC9K.png)

For at få de blå input og output porte skal du blot klikke på **forberede webtjeneste** nederst til højre. Køre dette forsøg også giver os mulighed at publicere webtjenesten: Klik på ikonet **PUBLICERE WEBTJENESTE** nederst til højre, vist her:

![Publicere en webtjeneste](./media/machine-learning-data-science-process-hive-criteo-walkthrough/WO0nens.png)


Når webtjeneste er udgivet, får vi omdirigeret til en side, der ser således:

![](./media/machine-learning-data-science-process-hive-criteo-walkthrough/YKzxAA5.png)

Vi se to links til webtjenestenøgle i venstre side:

* **Anmodning/svar** tjenesten (eller Ressourceposter) er beregnet til enkelt prognoser og er, hvad vi bruge i denne workshop.
* **BATCHEN udførelse af** Service (BES) bruges til batchen prognoser og kræver, at inputdataene bruges til at forudsige Azure Blob-lager er placeret.

Klikke på linket **Anmodning/svar** tager os til en side, der giver os allerede på dåse kode i C#, python og R. Denne kode kan nemt bruges til at foretage opkald til webtjeneste. Bemærk, at API-nøglen på denne side skal bruges til godkendelse.

Det er praktisk at kopiere koden python over til en ny celle i IPython notesbogen.

Vi viser her en målgruppe af python kode med den korrekte API-nøgle.

![Python kode](./media/machine-learning-data-science-process-hive-criteo-walkthrough/f8N4L4g.png)


Bemærk, at vi erstattet standard API-nøglen med vores webtjenestenøgle API-nøgle. Klikke på **Kør** på denne celle i en IPython notesbog giver følgende svaret:

![IPython svar](./media/machine-learning-data-science-process-hive-criteo-walkthrough/KSxmia2.png)

Vi se, at de to test eksempler vi stillede om (i forbindelse med python scriptet JSON), vi komme tilbage svar i formularen "Scored etiketter, Scored sandsynligheder". Bemærk, at i dette tilfælde vi valgte standard værdier, der allerede dåsefoder koden indeholder (0 til alle numeriske kolonner og strengen "værdi" for alle kategoriske kolonner).

Dette fuldfører vores til slut gennemgang, der viser, hvordan til at håndtere store datasæt ved hjælp af Azure Machine Learning. Vi i gang med en terabyte data, opbygget en grænseværdien og installeres som en webtjeneste i skyen.
