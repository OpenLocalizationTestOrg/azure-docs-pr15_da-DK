<properties
    pageTitle="Oversigt over Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight | Microsoft Azure"
    description="Knallertmotor MLlib værktøjerne viser betydelige machine learning udformning af mulighederne i den distribuerede HDInsight-miljø."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Oversigt over Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Denne række emner viser, hvordan du bruger HDInsight Spark til at udføre almindelige data videnskabelige opgaver som data indtagelse, funktion teknisk, modellering og beregning i modellen. De data, der bruges er et eksempel på 2013 NYC taxi forretningsrejse og pris datasættet. Modeller indbygget omfatter logistiske og lineære regression, tilfældige områder og graduering øget træer. Emnerne viser også, hvordan du kan gemme disse modeller i Azure blob-lager (WASB), og Sådan resultat og evaluerer deres skønnet ydeevne. Mere avancerede emner dækker hvordan modeller kan være uddannelse ved hjælp af tværs validering og hyper-parameter gadefejning. Emnet oversigt beskriver også, hvordan du konfigurerer den knallertmotor-klynge, du har brug for at fuldføre trinnene i de tre gennemgange, der findes. 

[Knallertmotor](http://spark.apache.org/) er et open source-parallel behandling af framework, der understøtter i hukommelsen behandling for at øge ydeevnen for stor data analytisk programmer. Knallertmotor behandlingsprogram er bygget til hastighed, brugervenlighed, og avancerede analyser. Knallertmotors i hukommelsen fordelt beregning funktioner gør det et godt valg for gentagen algoritmer i maskine læring og graph beregninger. [MLlib](http://spark.apache.org/mllib/) er Knallertmotors SVG machine learning bibliotek, der bringer modelfunktioner denne distribueret miljø. 

[HDInsight knallertmotor](../hdinsight/hdinsight-apache-spark-overview.md) er Azure hostet tilbud af open source-gnister. Den indeholder også understøttelse af **Jupyter PySpark notesbøger** på den knallertmotor klynge, som kan køre interaktive knallertmotor SQL-forespørgsler til at transformere, filtrering og visualisere data, der er gemt i Azure BLOB (WASB). PySpark er Python API til gnister. Kodestykker, der indeholder løsningerne og viser de relevante: at få vist dataene her køre i Jupyter notesbøger, der er installeret på knallertmotor klynger. Modellering trinnene i disse emner indeholder kode, der viser, hvordan du oplære, evaluere, gemme og bruge de forskellige typer model. 

Det konfigurationstrin og kode, der er angivet på denne gennemgang gælder for HDInsight 3.4 knallertmotor 1,6. Men koden her og i notesbøgerne er standard, og skal arbejde på en knallertmotor klynge. Hvis du ikke bruger HDInsight Spark, være klynge konfiguration og administration af trinnene en smule anderledes end det, der vises her.

## <a name="prerequisites"></a>Forudsætninger

1. Du skal have et Azure-abonnement. Hvis du ikke allerede har en, kan du se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. du har brug for en HDInsight 3.4 knallertmotor 1,6 klynge til at udføre denne gennemgang. Se vejledningen i for at oprette en [Introduktion: oprette Apache knallertmotor på Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Klynge type og version er angivet i menuen **Vælg klynge Type** . 


![](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [AZURE.NOTE] Efter et emne, der viser, hvordan du bruger Scala i stedet for Python til at udføre opgaver for en til slut data videnskabelige proces, kan du se den [videnskabelige Data ved hjælp af Scala med knallertmotor på Azure](machine-learning-data-science-process-scala-walkthrough.md).

<!-- -->

>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="the-nyc-2013-taxi-data"></a>Datatypen NYC 2013 Taxi

Datatypen NYC Taxi forretningsrejse er om 20 GB komprimeret kommasepareret (CSV) filer (~ 48 GB ikke-komprimeret format), der omfatter mere end 173 millioner individuelle rejser og priser betalt for hver forretningsrejse. Hver forretningsrejse post indeholder Vælg op og indlevering placering og klokkeslæt, anonyme hacke (driver) licens tal og Medaillon (taxi's entydige id) tal. Data, der dækker alle rejser år 2013 og findes i de følgende to datasæt for hver måned:

1. 'Trip_data' csv-filerne indeholder forretningsrejse detaljer, som tal med personer, opfanger og dropoff datamærker, trykke varighed og forretningsrejse længde. Her er nogle eksempler på poster:

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


Vi har taget en 0,1% stikprøve af disse filer og tilsluttet sig forretningsrejse\_data og forretningsrejse\_pris CVS-filer i et enkelt datasæt skal bruges som input datasættet for denne gennemgang. Den entydige nøgle til at deltage i forretningsrejse\_data og forretningsrejse\_pris består af felterne: Medaillon, hacke\_licens og afhentning\_datetime. Hver post for datasættet indeholder følgende attributter, der repræsenterer en NYC Taxi rejse:


|Felt| Kort beskrivelse
|------|---------------------------------
| Medaillon |Anonyme taxi Medaillon (entydige taxi id)
| hack_license |    Anonyme Hackney transport licens tal
| vendor_id |   Taxi leverandør-id
| rate_code | NYC taxi rente pris
| store_and_fwd_flag | Gemme og videresende flag
| pickup_datetime | Vælge dato og klokkeslæt
| dropoff_datetime | Dropoff dato og klokkeslæt
| pickup_hour | Vælg indstillinger for time
| pickup_week | Vælge uge i året
| ugedag | Weekday (række 1-7)
| passenger_count | Tal med personer i en taxi rejse
| trip_time_in_secs | Forretningsrejse tid i sekunder
| trip_distance | Forretningsrejse afstanden kørte kilometer i sømil
| pickup_longitude | Vælge længdegrader
| pickup_latitude | Vælge længde
| dropoff_longitude | Dropoff længdegrader
| dropoff_latitude | Dropoff længde
| direct_distance | Direkte afstanden mellem Vælg op og dropoff placeringer
| payment_type | Betalingstype (nøglecentre, med et kreditkort osv.)
| fare_amount | Pris beløb i
| tillæg | Tillæg
| mta_tax | MTA moms
| tip_amount | Tip beløb
| tolls_amount | Vej beløb
| total_amount | Samlede beløb
| Vippet | Vippet (0/1 for Nej eller Ja)
| tip_class | Tip klasse (0: $0, 1: $0-5, 2: $6-10, 3: 11-20, 4: > 20)


## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Udføre kode fra en Jupyter notesbog på knallertmotor klynge 

Du kan starte Jupyter notesbogen fra Azure-portalen. Find din knallertmotor klynge i dashboardet, og klik på den for at angive administrationssiden for din klynge. For at åbne den notesbog, der er knyttet til knallertmotor klynge, skal du klikke på **Klynge Dashboards** -> **Jupyter notesbog** .

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Du kan også gå til ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** adgang til Jupyter notesbøger. Erstat CLUSTERNAME del af denne URL-adresse med navnet på din egen klynge. Du skal bruge adgangskoden til din administratorkonto til at få adgang til notesbøgerne.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Vælg PySpark at se en mappe, der indeholder nogle få eksempler på forhånd pakket notesbøger, der bruger API'EN PySpark. De notesbøger, der indeholder kodeeksempler denne pakke af knallertmotor emne findes på [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)


Du kan overføre notesbøger direkte fra Github til serveren Jupyter notesbog på din knallertmotor klynge. Startsiden for din Jupyter, klik på knappen **Overfør** på højre del af skærmen. En Stifinder åbnes. Her kan du indsætte Github (rå indhold) URL-adressen på notesbogen, og klik på **Åbn**. PySpark notesbøger findes på følgende URL-adresser:

1.  [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2.  [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3.  [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Du få filnavnet på listen Jupyter fil med knappen **Overfør** igen. Klik på knappen **Overfør** . Du har nu importeret notesbogen. Gentag disse trin for at overføre de andre notesbøger fra denne gennemgang.

> [AZURE.TIP] Du kan højreklikke på linkene i din browser og vælg **Kopiér Link** for at få github rå indhold URL-adressen. Du kan indsætte denne URL-adresse i dialogboksen Jupyter Overfør fil explorer.

Nu kan du:

- Se koden ved at klikke på notesbogen.
- Udføre hver celle ved at trykke på **SKIFT + ENTER**.
- Køre hele notesbogen ved at klikke på **celle** -> **køre**.
- Du kan bruge den automatiske visualisering af forespørgsler.

> [AZURE.TIP] PySpark kernen visualiseres automatisk output fra SQL (HiveQL)-forespørgsler. Du får mulighed for at vælge mellem flere forskellige typer visualiseringer (tabel, cirkeldiagram, linje, et område eller liggende) ved hjælp af knapperne **Type** menu i notesbogen:

![Logistisk regression ROC kurven for generisk metode](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Hvad skal der ske nu?

Nu, hvor du er konfigureret med en HDInsight Spark klynge og har overført Jupyter notesbøger, er du klar til at gennemgå de emner, der svarer til de tre PySpark notesbøger. De vises, hvordan du kan udforske dine data og derefter at oprette og bruge modeller. Avancerede data udforske og modellering notesbogen viser, hvordan du medtage kryds-validering, hyper-parameter hele, og modellere evaluering. 

**Undersøgelse af data og modellering med knallertmotor:** Udforske datasættet og oprette resultatet, og evaluere den maskine, læ modeller ved gennemgår emnet [oprette binære klassifikation og regression modeller til data med knallertmotor MLlib værktøjerne](machine-learning-data-science-spark-data-exploration-modeling.md) .

**Model forbrug:** Hvis du vil lære at resultat de klassificering og regression modeller, der er oprettet i dette emne, skal du se [karakteren og evaluerer knallertmotor indbygget machine learning modeller](machine-learning-data-science-spark-model-consumption.md).

**Tværs validering og hyperparameter hele**: se [Avanceret undersøgelse af data og modellering med knallertmotor](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) på hvordan modeller kan være uddannelse ved hjælp af tværs validering og hyper-parameter gadefejning

