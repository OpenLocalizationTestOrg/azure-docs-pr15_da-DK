<properties
    pageTitle="Data videnskabelige ved hjælp af Scala og knallertmotor på Azure | Microsoft Azure"
    description="Sådan bruges Scala for tilsyn machine learning opgaver med knallertmotor SVG MLlib og knallertmotor ML pakkerne på en Azure HDInsight knallertmotor klynge."  
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
    ms.date="08/01/2016"
    ms.author="bradsev;deguhath"/>


# <a name="data-science-using-scala-and-spark-on-azure"></a>Data videnskabelige ved hjælp af Scala og knallertmotor på Azure

I denne artikel viser, hvordan du bruger Scala for tilsyn machine learning opgaver med knallertmotor SVG MLlib og knallertmotor ML pakkerne på en Azure HDInsight knallertmotor klynge. Det vejleder dig gennem de opgaver, der udgør tilsammen [Data videnskabelige proces](http://aka.ms/datascienceprocess): data indtagelse og udforske, visualisere, funktion teknisk, modellering og model forbrug. Modeller i artiklen omfatter logistiske og lineære regression, tilfældige områder og graduering øget træer (GBTs), ud over to almindelige tilsyn machine learning opgaver:

- Regression problem: forudsigelse af tip-beløb ($) for en taxi rejse
- Binær klassifikation: forudsigelse af tip eller ingen tip (1/0) til en taxi rejse

Modelleringsprocessen kræver kursus og evaluering på et datasæt test og relevante nøjagtigheden målepunkter. I denne artikel kan du lære, hvordan du kan gemme disse modeller i Azure Blob-lager og Sådan resultat og evaluerer deres skønnet ydeevne. Denne artikel omhandler også mere avancerede emner af, hvordan du kan optimere modeller ved hjælp af tværs validering og hyper-parameter gadefejning. De data, der bruges er et eksempel på 2013 NYC taxi forretningsrejse og pris datasættet tilgængelig på GitHub.

[Scala](http://www.scala-lang.org/), et sprog, der er baseret på virtual machine til Java integrerer objektorienteret og funktionelle sprog begreber. Det er et SVG sprog, der er velegnet til distribueret databehandling i skyen, og kører på Azure knallertmotor klynger.

[Knallertmotor](http://spark.apache.org/) er et open source-parallel behandling struktur, der understøtter i hukommelsen behandling for at øge ydeevnen for stor data analytics-programmer. Knallertmotor behandlingsprogram er bygget til hastighed, brugervenlighed, og avancerede analyser. Knallertmotors i hukommelsen fordelt beregning funktioner gør det et godt valg for gentagen algoritmer i maskine læring og graph beregninger. Pakken [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) indeholder et ensartet sæt overordnet API'er indbygget oven på data rammer, der kan hjælpe dig med at oprette og finjustere praktiske machine learning-rørledninger. [MLlib](http://spark.apache.org/mllib/) er Knallertmotors SVG machine learning bibliotek, som skaber viser modelfunktioner i denne distribueret miljø.

[HDInsight knallertmotor](../hdinsight/hdinsight-apache-spark-overview.md) er Azure-hostet tilbud af open source-gnister. Det også indeholder understøttelse af Jupyter Scala notesbøger på knallertmotor klynge og kan køre interaktive knallertmotor SQL-forespørgsler for at transformere, filtrere og visualisere data, der er gemt i Azure Blob-lager. De Scala kodestykker i denne artikel, der indeholder løsningerne og viser de relevante: at få vist dataene køre i Jupyter notesbøger, der er installeret på knallertmotor klynger. Modellering trinnene i disse emner har kode, der viser, hvordan du oplære, evaluere, gemme og bruge de forskellige typer model.

At konfigurationstrin og kode i denne artikel er til Azure HDInsight 3.4 knallertmotor 1,6. Dog koden i denne artikel og i [Scala Jupyter notesbog](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) er generisk og skal arbejde på en knallertmotor klynge. Trinnene til konfiguration og administration klynge kan være en smule anderledes end hvad der vises i denne artikel, hvis du ikke bruger HDInsight Spark.

> [AZURE.NOTE] Du kan finde et emne, der viser, hvordan du bruger Python i stedet for Scala til at udføre opgaver for en til slut Data videnskabelige proces, [Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight](machine-learning-data-science-spark-overview.md).


## <a name="prerequisites"></a>Forudsætninger

-   Du skal have et Azure-abonnement. Hvis du ikke allerede har en [hente en gratis prøveversion af Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

-   Du har brug for en Azure HDInsight 3.4 knallertmotor 1,6 klynge for at fuldføre følgende procedurer. Hvis du vil oprette en klynge, se instruktionerne i [Introduktion: oprette Apache knallertmotor på Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Angiv den klynge og version i menuen **Vælg klynge Type** .

![HDInsight klynge type konfiguration](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Du kan finde en beskrivelse af de NYC taxi forretningsrejse data og oplysninger om, hvordan til at udføre kode fra en Jupyter notesbog på knallertmotor klynge, de relevante afsnit i [Oversigt over Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight](machine-learning-data-science-spark-overview.md).  


## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Udføre Scala kode fra en Jupyter notesbog på knallertmotor klynge

Du kan starte en Jupyter notesbog fra Azure-portalen. Find knallertmotor klyngen i dashboardet, og klik derefter på den for at angive siden til administration af for din klynge. Derefter skal du klikke på **Klynge Dashboards**, og klik derefter på **Jupyter notesbog** for at åbne den notesbog, der er knyttet til knallertmotor klynge.

![Klynge dashboard og Jupyter notesbøger](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Du kan også åbne Jupyter notesbøger på https://&lt;clustername&gt;.azurehdinsight.net/jupyter. Erstat *clustername* med navnet på din klynge. Du skal bruge adgangskoden til din administratorkonto for at få adgang til notesbøgerne Jupyter.

![Gå til Jupyter notesbøger ved hjælp af](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Vælg **Scala** at se en mappe, der er nogle få eksempler på færdigpakkede notesbøger, der bruger API'EN PySpark. Udforske Modeling og resultat ved hjælp af Scala.ipynb notesbog, der indeholder kodeeksempler denne pakke af knallertmotor emner findes på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).


Du kan overføre notesbogen direkte fra GitHub til serveren Jupyter notesbog på din knallertmotor klynge. Klik på knappen **Overfør** på startsiden Jupyter. I Stifinder skal du indsætte GitHub (rå indhold) URL-adressen på notesbogen, Scala, og klik derefter på **Åbn**. Scala notesbogen er tilgængelig på følgende URL-adressen:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Installation: Forudindstillet tænding og Hive kontekster, gnister magics og knallertmotor biblioteker

### <a name="preset-spark-and-hive-contexts"></a>Forudindstillede tænding og Hive kontekster

    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


De knallertmotor kerner, der følger med Jupyter notesbøger har forudindstillede kontekster. Du behøver ikke at eksplicit er angivet på knallertmotor eller Hive kontekster, før du går i gang med at arbejde med programmet, du udvikler. De forudindstillede kontekster er:

- `sc`for SparkContext
- `sqlContext`for HiveContext


### <a name="spark-magics"></a>Knallertmotor magics

Knallertmotor kernen indeholder nogle foruddefinerede "magics", som er specielle kommandoer, som du kan ringe til med `%%`. To af disse kommandoer bruges i eksemplerne i følgende.

- `%%local`Angiver, at koden i efterfølgende linjer vil blive udført lokalt. Koden skal være gyldig Scala kode.
- `%%sql -o <variable name>`udfører en Hive forespørgsel mod `sqlContext`. Hvis den `-o` parameter er overført, er bevaret resultatet af forespørgslen i de `%%local` Scala kontekst som en knallertmotor data ramme.

For flere oplysninger om kerner for Jupyter notesbøger og deres foruddefinerede "magics", som du ringe til med `%%` (for eksempel `%%local`), se [kerner, der er tilgængelige for Jupyter notesbøger med HDInsight knallertmotor Linux klynger på HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### <a name="import-libraries"></a>Importere biblioteker

Importere gnister, MLlib og andre biblioteker, du skal bruge ved hjælp af følgende kode.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Data indtagelse

Det første trin i processen Data videnskabelige er at indtager de data, du vil analysere. Du kan overføre data fra eksterne kilder eller systemer, hvor det er placeret i dine data udforske og modellering miljø. I denne artikel er de data, du indtager en joinforbundne 0,1% prøve af taxi forretningsrejse og pris filen (gemt som en .tsv-fil). Data udforske og modellering miljøet er gnister. Dette afsnit indeholder koden for at fuldføre den følgende række opgaver:

1. Angive directory stier til lagring af data og model.
2. Læs i input datasættet (gemt som en .tsv-fil).
3. Definere et skema for dataene, og rydde dataene.
4. Opretter en ramme til rensede data og gemme den i cachen i hukommelsen.
5. Registrere dataene som en midlertidig tabel i SQLContext.
6. Forespørge tabellen og importere resultaterne i en ramme data.


### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Angive directory stier for lagerplads placeringer i Azure Blob-lager

Knallertmotor kan læse og skrive til Azure Blob-lager. Du kan bruge knallertmotor til at behandle nogen af dine eksisterende data og derefter gemme resultatet igen i Blob-lager.

Hvis du vil gemme modeller eller filer i Blob-lager, skal du angive stien korrekt. Henviser til objektbeholderen standard, der er knyttet til knallertmotor klynge ved hjælp af en sti, som begynder med `wasb:///`. Henviser til andre placeringer ved hjælp af `wasb://`.

Følgende eksempel-kode angiver placeringen af den indtastede data til at blive læst og stien til Blob-lager, der er knyttet til knallertmotor klynge hvor modellen skal gemmes.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importere data, oprette en RDD og definere en data ramme i henhold til skemaet

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tid til at køre cellen: 8 sekunder.


### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Forespørge tabellen og importere resultaterne i en ramme til data

Dernæst skal forespørge tabellen til pris, passager og tip data. filtrere beskadiget og ydre data. og udskrive flere rækker.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Output:**

fare_amount|passenger_count|tip_amount|Vippet
-----------|---------------|----------|------
       13,5|            1.0|       2.9|   1.0
       16,0|            2.0|       3.4|   1.0
       10.5|            2.0|       1.0|   1.0


## <a name="data-exploration-and-visualization"></a>Udforske data og visualisering

Når du sætter dataene i gnister, er næste trin i processen Data videnskabelige til at få en bedre forståelse af data via udforske og visuelle effekter. I dette afsnit, skal kontrollere du taxi dataene ved hjælp af SQL-forespørgsler. Derefter importere resultaterne i en ramme for data at afbilde target variabler og mulige funktioner for visuel inspektion ved hjælp af funktionen automatisk visualisering af Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Brug af lokal og SQL magisk afbilde data

Output fra en hvilken som helst kodestykke, som du køre fra en Jupyter notesbog er tilgængelige i forbindelse med den session, som er bevaret på noderne arbejder som standard. Hvis du vil gemme en rejse noderne arbejder for hver beregning, og hvis alle de data, du skal bruge til din beregning er tilgængelig lokalt på noden Jupyter server (som er noden hoved), kan du bruge den `%%local` magisk til at køre kodestykket på Jupyter-serveren.

- **SQL magisk** (`%%sql`). HDInsight Spark kernen understøtter nemt indbygget HiveQL forespørgsler SQLContext. Den (`-o VARIABLE_NAME`) argument fortsætter output fra SQL-forespørgslen som en Pandas data ramme på Jupyter-serveren. Det betyder, at den bliver tilgængelig i den lokale tilstand.
- `%%local`**magisk**. Den `%%local` magisk kører koden lokalt på Jupyter server, som er noden hoved af HDInsight klynge. Du bruger typisk `%%local` magiske sammen med den `%%sql` magiske med den `-o` parameter. Den `-o` parameter ville fastholdes output fra SQL-forespørgslen lokalt, og derefter `%%local` magisk vil udløse kodestykke til at køre lokalt mod output for SQL-forespørgsler, som er bevaret lokalt næste sæt.

### <a name="query-the-data-by-using-sql"></a>Forespørgsel data ved hjælp af SQL
Denne forespørgsel henter taxi rejser ved pris beløb passager Tæl og omkostninger.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

I den følgende kode i `%%local` magisk opretter en ramme til lokale data, sqlResults. Du kan bruge sqlResults afbilde ved hjælp af matplotlib.

> [AZURE.TIP] Lokale magisk bruges flere gange i denne artikel. Hvis datasættet er stort, skal du eksempel for at oprette en ramme til data, der kan være i den lokale hukommelse.

### <a name="plot-the-data"></a>Afbilde data

Du kan afbilde ved hjælp af Python kode, når data rammen er i lokale kontekst som en Pandas data ramme.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 Knallertmotor kernen visualiseres automatisk output fra SQL (HiveQL)-forespørgsler, når du har kørt koden. Du kan vælge mellem flere forskellige typer af visualiseringer:
 
- Tabel
- Cirkeldiagram
- Linje
- Område
- Liggende

Her er koden for at afbilde data:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Output:**

![Tip beløb histogram](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Tip mængden af passager Tæl](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tip beløb med pris afstand](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)


## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Oprette funktioner og transformere funktioner og derefter forberede data om input til modeling funktioner

Baseret træstruktur modelleringsfunktioner fra knallertmotor ML og MLlib har du til at forberede mål og funktioner ved hjælp af en række forskellige teknikker, som binning, indeksering, en hot kodning og vectorization. Her er procedurer til at følge i dette afsnit:

1. Opret en ny funktion med **binning** timer i trafik tid filsæt.
2. Anvende **indeksering og en hot kodning** til kategoriske funktioner.
3. **Eksempel og opdele datasæt** til uddannelse og test brøker.
4. **Angiv kursus variabel og funktioner**, og derefter oprette indekserede eller meget én varmt kodet kurser og test af input mærket punkt tolerant distribueret datasæt (RDDs) eller datarammer.
5. Automatisk **kategorisere og vectorize funktioner og mål, der** skal bruges som input til machine learning modeller.


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Opret en ny funktion ved binning timer i trafik tid filsæt

Denne kode viser dig, hvordan du opretter en ny funktion med binning timer i trafik tid filsæt og Sådan cache den resulterende data ramme i hukommelsen. Hvor RDDs og data rammer bruges flere gange, cachelagring kundeemner til forbedret udførelse af klokkeslæt. Du skal derfor cache RDDs og datarammer i flere faser i følgende procedurer.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indeksering og en hot kodning af kategoriske funktioner

Modellering og forudsige MLlib-funktioner kræver funktioner med kategoriske inputdataene indekseret eller kodet før brug. Dette afsnit viser, hvordan du indeksere eller kode kategoriske funktioner for input til funktionerne modellering.

Du har brug at indeksere eller kode din modeller på forskellige måder, afhængigt af modellen. For eksempel kræver logistiske og lineære regression modeller en hot kodning. En funktion med tre kategorier kan for eksempel udvides til tre funktion kolonner. Hver kolonne indeholder 0 eller 1 afhængigt af kategorien af en bemærkning. MLlib indeholder funktionen [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) til en hot kodning. Denne encoder knytter en kolonne med navnet indeks til en kolonne med binære vektorer med højst en enkelt én-værdi. Med denne kodning kan algoritmer, der forventer numeriske værdier funktioner, som logistisk regression, anvendes til kategoriske funktioner.

Her kan du transformere kun fire variabler for at få vist eksempler, der er tegnstrenge. Du kan også indeksere andre variabler, såsom ugedag, repræsenteret af numeriske værdier, som kategoriske variabler.

Brug af til indeksering, `StringIndexer()`, og til en hot kodning, bruge `OneHotEncoder()` funktioner fra MLlib. Her er koden til at indeksere og kode kategoriske funktioner:


    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tid til at køre cellen: 4 sekunder.



### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Eksempel og opdele datasæt til uddannelse og test brøker

Denne kode opretter en stikprøver data (25%, i dette eksempel). Selvom stikprøver, hvor der ikke er påkrævet i dette eksempel på grund af størrelsen af datasættet, i artiklen du viser, hvordan du kan eksempel, så du ved, hvordan du kan bruge den til din egen problemer, når det er nødvendigt. Når eksempler er stor, kan dette spare betydeligt tid, mens du træne modeller. Opdel derefter eksemplet i et kursus del (75%, i dette eksempel) og en test del (25%, i dette eksempel) Hvis du vil bruge i klassificering og regression modellering.

Føje et tilfældigt tal (mellem 0 og 1) til hver række (i en kolonne med "SLUMP"), der kan bruges til at vælge folder i tværs validering under kursus.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tid til at køre cellen: to sekunder.


### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Angiver kursus variabel og funktioner, og opret derefter indekserede eller meget én varmt kodet kurser og test af input mærket punkt RDDs eller data rammer

Dette afsnit indeholder kode, der viser, hvordan du indeksere kategoriske tekstdata som en mærket punkt datatype, og kode den, så du kan bruge det til at undervise og teste MLlib logistisk regression og andre klassifikation modeller. Mærket punkt objekter er RDDs, der er formateret på en måde, der skal bruges som input data ved at de fleste af machine learning-algoritmer i MLlib. En [mærket punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) er en lokal vektor, tæt eller sparse, der er knyttet til en etiket/svar.

I denne kode, skal angive du variablen mål (afhængige) og funktionerne, der skal bruges til at undervise modeller. Derefter skal oprette du indekserede eller meget én varmt kodet kurser og test af input mærket punkt RDDs eller data rammer.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tid til at køre cellen: 4 sekunder.


### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatisk at kategorisere og vectorize funktioner og destinationer skal bruges som input til machine learning-modeller

Bruge knallertmotor ML til at kategorisere mål og funktioner, der skal bruge i baseret træstruktur modelleringsfunktioner. Koden er fuldført to opgaver:

-   Opretter en binær destination for klassifikation ved at tildele en værdi på 0 eller 1 til hvert datapunkt mellem 0 og 1 ved hjælp af en grænseværdi på 0,5.
- Automatisk kategoriserer funktioner. Hvis antallet af entydige numeriske værdier for en hvilken som helst funktion er mindre end 32, er denne funktion kategoriseret.

Her er koden for de to opgaver.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binær klassifikation model: forudsige, om der skal betales et tip

I dette afsnit, skal oprette du tre typer binære klassifikation modeller til at forudsige hvorvidt et tip skal betales:

- En **model logistisk regression** ved hjælp af de knallertmotor ML `LogisticRegression()` funktionen
- En **tilfældigt skov klassifikation model** ved hjælp af de knallertmotor ML `RandomForestClassifier()` funktionen
- En **gradueret at træet klassifikation model** ved hjælp af MLlib `GradientBoostedTrees()` funktionen

### <a name="create-a-logistic-regression-model"></a>Oprette en logistisk regressionsmodel

Nu skal oprette en logistisk regressionsmodel ved hjælp af de knallertmotor ML `LogisticRegression()` funktionen. Du opretter den model, opbygning af kode i en række trin:

1. Angive **tog modellen** data med en parameter.
2. **Evaluer modellen** på datasættet test med målepunkter.
3. **Gem modellen** i Blob-lager til fremtidige forbrug.
4. **Resultat modellen** mod testdata.
5. **Afbilde resultaterne** med modtager operativsystem egenskab (ROC) kurver.

Her er koden for disse procedurer:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Indlæse, resultat, og Gem resultaterne.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Output:**

ROC på testdata = 0.9827381497557599


Brug Python på lokale Pandas datarammer afbilde ROC kurven.


    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()


**Output:**

![Tip eller ingen tip ROC kurve](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### <a name="create-a-random-forest-classification-model"></a>Oprette en tilfældigt skov klassifikation model

Dernæst skal oprette en tilfældigt skov klassifikation model ved hjælp af de knallertmotor ML `RandomForestClassifier()` fungerer, og derefter evaluere modellen på testdata.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Output:**

ROC på testdata = 0.9847103571552683


### <a name="create-a-gbt-classification-model"></a>Oprette en GBT klassifikation model

Dernæst skal oprette en GBT klassifikation model ved hjælp af Mllib's `GradientBoostedTrees()` fungerer, og derefter evaluere modellen på testdata.


    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Output:**

Området under ROC kurve: 0.9846895479241554


## <a name="regression-model-predict-tip-amount"></a>Regressionsmodel: forudsige tip beløb

I dette afsnit, skal oprette du to typer regression modeller til at forudsige beløbet tip:

- En **afklaret lineær regressionsmodel** ved hjælp af de knallertmotor ML `LinearRegression()` funktionen. Du skal gemme modellen og evaluerer modellen på testdata.
- En **graduering øger træet regressionsmodel** ved hjælp af de knallertmotor ML `GBTRegressor()` funktionen.


### <a name="create-a-regularized-linear-regression-model"></a>Oprette en model afklaret lineær regression

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tid til at køre cellen: 13 sekunder.


    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Output:**

R-sqr på testdata = 0.5960320470835743


Derefter forespørge på testresultater som en data ramme, og brug AutoVizWidget og matplotlib til visualisering af den.


    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

Koden opretter en ramme for lokale data fra forespørgslens output, og der plottes dataene. Den `%%local` magisk opretter en lokale data ramme `sqlResults`, som du kan bruge til at afbilde med matplotlib.

>[AZURE.NOTE] Denne knallertmotor magisk bruges flere gange i denne artikel. Hvis mængden data er stort, skal du eksempel for at oprette en ramme til data, der kan være i den lokale hukommelse.

Oprette områder ved hjælp af Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Output:**

![Tip beløb: faktisk kontra estimerede](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### <a name="create-a-gbt-regression-model"></a>Oprette en GBT regressionsmodel

Oprette en GBT regressionsmodel ved hjælp af de knallertmotor ML `GBTRegressor()` fungerer, og derefter evaluere modellen på testdata.

[Øget graduering træer](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) er sæt af beslutningstræer. GBTs Oplær beslutningstræer flere gange for at minimere en tab-funktion. Du kan bruge GBTs til regression og klassificering. De kan håndtere kategoriske funktioner, kræver ikke funktion skalering og kan registrere nonlinearities og funktion interaktioner. Du kan også bruge dem i en multiclass klassifikation indstilling.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Output:**

Test R-sqr er: 0.7655383534596654



## <a name="advanced-modeling-utilities-for-optimization"></a>Avanceret modellering funktioner til optimering

I dette afsnit, skal bruge du machine learning funktioner, der ofte bruger udviklere til optimering af modellen. Du kan nærmere betegnet kan optimere machine learning modeller tre forskellige måder ved hjælp af parameter gadefejning og tværs valideringsregler:

-   Opdele dataene i tog og validering sæt, optimere modellen ved hjælp af hyper-parameter gadefejning på et kursus, vises og evaluere på et validering vises (lineær regression)
-   Optimere modellen ved hjælp af tværs validering og hyper-parameter hele ved hjælp af knallertmotor ML CrossValidator funktion (binære klassifikation)
-   Optimere modellen ved hjælp af brugerdefineret kode i tværs validering og parameter gadefejning for at bruge en hvilken som helst computer, kan funktionen og parameter sæt (lineær regression)


**Tværs datavalidering** er en teknik, der vurderer hvorvidt en model, inden for en kendt mængde data bliver generalize for at forudsige funktionerne i datasæt, som det ikke er blevet uddannelse. Denne metode generelle formålet er, at en model oplæring i et datasæt med kendte data, og derefter nøjagtigheden af dens prognoser er testet mod en uafhængig datasæt. En fælles implementering er at inddele datasættet i *k*-folder, og klik derefter træne modellen i en round robin-måde på alle undtagen én af folder.

**Optimering af Hyper-parameter** er problemet ved at vælge et sæt af hyper-parametre for en learning algoritme normalt med formålet med optimering af en måling af den algoritme ydeevne på en uafhængig datasæt. En hyper-parameter er en værdi, skal du angive uden for model kursus procedure. Forudsætninger om hyper-parameterværdier kan påvirke fleksibilitet og nøjagtigheden af modellen. Beslutningstræer have hyper-parametre, for eksempel som den ønskede dybde og antallet af blade i træet. Du skal angive et misclassification sanktioner udtryk for en support vektor maskine (SVM).

En almindelig måde til at udføre hyper-parameter optimering er at bruge en gitter søgning, også kaldet en **parameter oprydning**. En omfattende søgning udføres i en gitter søgning via værdierne i et angivet delsæt af hyper-parameter plads til en learning algoritme. Tværs validering kan give dig en metrikværdi i ydeevne til at sortere ud af de bedste resultater produceret af algoritmen gitter søgning: Hvis du bruger tværs validering hyper-parameter gadefejning, kan du øge grænse problemer som overfitting en model til uddannelsesdata. Denne måde modellen bevarer kapacitet til at anvende til de generelle hvorfra kursus dataene blev udpakkede data.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimere en lineær regressionsmodel med hyper-parameter gadefejning

Derefter skal du opdele data i tog og validering sæt, Brug hyper-parameter hele på et kursus, vises du optimerer modellen og evaluere på et validering vises (lineær regression).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Output:**

Test R-sqr er: 0.6226484708501209


### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimere binære klassifikation modellen ved hjælp af tværs validering og hyper-parameter gadefejning

Dette afsnit viser, hvordan du kan optimere en binær klassifikation model ved hjælp af tværs validering og hyper-parameter gadefejning. Dette bruger knallertmotor ML `CrossValidator` funktionen.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Output:**

Tid til at køre cellen: 33 sekunder.


### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimere modellen lineær regression ved hjælp af brugerdefineret kode i tværs validering og parameter gadefejning

Dernæst skal optimere modellen ved hjælp af brugerdefineret kode, og identificere de bedste model parametre ved hjælp af kriteriet højeste nøjagtighed. Derefter skal du oprette den endelige model, evaluere modellen på testdata og Gem modellen i Blob-lager. Til sidst skal du indlæse modellen, resultat testdata og evaluere nøjagtigheden.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Output:**

Tid til at køre cellen: 61 sekunder.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Bruge knallertmotor indbygget machine learning modeller automatisk med Scala

Du kan finde en oversigt over emner, der fører dig gennem de opgaver, der udgør Data videnskabelige processen i Azure, [Team Data videnskabelige proces](http://aka.ms/datascienceprocess).

[Team Data videnskabelige proces gennemgange](data-science-process-walkthroughs.md) beskrives andre til slut gennemgange, som viser trinnene i processen Team Data videnskabelige for bestemte scenarier. Gennemgangene illustrere også hvordan du kan kombinere skyen og on-premises værktøjer og tjenester i en arbejdsproces eller pipeline til at oprette et intelligent program.

[Resultat knallertmotor indbygget machine learning-modeller](machine-learning-data-science-spark-model-consumption.md) viser, hvordan du bruger Scala kode til automatisk at indlæse og resultat nye datasæt med machine learning modeller indbygget i knallertmotor og gemmes i Azure Blob-lager. Du kan følge vejledningen der og bare erstatte Python koden med Scala kode i denne artikel til automatiseret forbrug.
