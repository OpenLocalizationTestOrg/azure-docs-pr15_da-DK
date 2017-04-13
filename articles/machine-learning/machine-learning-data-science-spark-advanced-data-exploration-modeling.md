<properties
    pageTitle="Avanceret undersøgelse af data og modellering med knallertmotor | Microsoft Azure"
    description="Brug HDInsight Spark til at gøre data udforske og træne binære klassificering og regression modellerne ved hjælp af tværs validering og hyperparameter optimering."
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

# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Avancerede data udforske og modellering med knallertmotor 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Denne gennemgang bruger HDInsight Spark til at gøre data udforske og træne binære klassificering og regression modellerne ved hjælp af tværs validering og hyperparameter optimering af en stikprøve af NYC taxi forretningsrejse og pris 2013 datasæt. Det vejleder dig gennem trinnene til [Data videnskabelige proces](http://aka.ms/datascienceprocess), slutpunkterne til hinanden, ved hjælp af en HDInsight Spark klynge til behandling og Azure BLOB for at gemme dataene og modellerne. Processen udforsker og visualiseres data fra en Azure-lager Blob hentet og derefter laver dataene til at oprette skønnet modeller. Python er blevet brugt til at kode løsningen og for at vise de relevante:. Disse modeller er build ved hjælp af værktøjerne knallertmotor MLlib til at udføre binære klassifikation og regression modellering opgaver. 

- **Binær klassifikation** opgaven er at forudsige eller ej et tip er betalt for forretningsrejse. 
- Den **simple lineære regression** opgave er at forudsige mængden spidsen baseret på andre tip funktioner. 

Modellering trinnene indeholder også kode, der viser, hvordan du træne, evaluere og gemme de forskellige typer model. Emnet beskrives nogle af den samme begrundelse som emnet [undersøgelse af Data og modellering med gnister](machine-learning-data-science-spark-data-exploration-modeling.md) . Men det er mere "Avanceret", bruges også tværs validering sammen med hyperparameter hele for at træne optimalt nøjagtige klassificering og regression modeller. 

**Kryds-validering (CV)** er en teknik, der vurderer, hvor godt en model, inden for en kendt mængde data generalizes til at forudsige funktionerne i datasæt, som det ikke er blevet uddannelse. Denne metode generelle formålet er, at en model oplæring i et datasæt af kendte data på og derefter nøjagtigheden af dens prognoser er testet mod en uafhængig datasæt. En fælles implementering bruges her er at inddele et datasæt i K Folder og derefter træne modellen i en round robin-måde på alle undtagen én af folder. 

**Hyperparameter optimering** er problemet ved at vælge et sæt af hyperparameters for en learning algoritme normalt med formålet med optimering af en måling af den algoritme ydeevne på en uafhængig datasæt. **Hyperparameters** er værdier, der skal angives uden for model kursus procedure. Forudsætninger om disse værdier kan påvirke den fleksibilitet og nøjagtigheden af modellerne. Beslutningstræer har hyperparameters, for eksempel som den ønskede dybde og antallet af blade i træet. Support vektor computere (SVMs) kræver, at et misclassification sanktioner udtryk. 

En almindelig måde til at udføre hyperparameter optimering bruges her er et gitter søgning eller en **parameter oprydning**. Består af udføre en omfattende søgning via værdierne et angivet delsæt af hyperparameter afstanden for en learning algoritme. Cross validering kan give dig en metrikværdi i ydeevne til at sortere ud af de bedste resultater produceret af algoritmen gitter søgning: CV bruges sammen med hyperparameter hele hjælper med at begrænse problemer som overfitting en model til uddannelsesdata, så modellen bevarer kapacitet til at anvende til de generelle data, hvorfra kursus dataene blev hentet.

Modeller vi bruge omfatter logistiske og lineære regression, tilfældige områder og graduering øget træer:

- [Lineær regression med SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) er en lineær regressionsmodel, der bruger en Stochastic graduering afstamning (SGD) metode og til optimering og funktion skalering til at forudsige tip Beløb betalt. 
- [Logistisk regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller "logit" regression er en regressionsmodel, der kan bruges, når den afhængige variabel er kategoriske til at gøre data klassificering. LBFGS er en kvasi Newton optimering algoritme, der tilnærmer algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS) ved hjælp af en begrænset mængde computerens hukommelse og, der anvendes på machine learning.
- [Tilfældig områder](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) er sæt af beslutningstræer.  De kombinerer mange beslutningstræer for at reducere risikoen for overfitting. Tilfældige områder bruges til regression og klassifikation og kan håndtere kategoriske funktioner og kan udvides til indstillingen multiclass klassificering. De kræver ikke funktion skalering og kan registrere ikke-linearities og funktionen interaktioner. Tilfældige områder er en af de mest vellykket machine learning-modeller for klassificering og regression.
- [Graduering øget træer](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) er sæt af beslutningstræer. GBTs Oplær beslutningstræer flere gange for at minimere en tab-funktion. GBTs bruges til regression og klassifikation og kan håndtere kategoriske funktioner, kræver ikke funktion skalering og kan registrere ikke-linearities og funktionen interaktioner. De kan også bruges i en multiclass klassifikation indstilling.

Modeling eksempler på brug af CV og Hyperparameter oprydning vises for binære klassifikation problemet. Nemmere eksempler vises (uden parameter har vundet) i emnet med den primære for regression opgaver. Men i tillæg, validering med elastiske redskaber til lineær regression og CV med parameter oprydning ved hjælp af til tilfældige skov regression præsenteres også. **Elastiske redskaber** er en metode til at tilpasse lineær regression modeller afklaret regression, der kombinerer lineært L1 og L2 målepunkter som sanktioner af [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) og [rand](https://en.wikipedia.org/wiki/Tikhonov_regularization) metoder.   



>[AZURE.NOTE] Selvom knallertmotor MLlib værktøjerne er designet til at arbejde på store datasæt, bruges en lille prøve (~ 30 Mb ved hjælp af 170K rækker, om 0,1% af det oprindelige NYC datasæt) her til nemmere. Den opgave, der er angivet her kører effektivt (i omkring 10 minutter) på en HDInsight klynge med 2 arbejder noder. Den samme kode, med mindre ændringer, kan bruges til at behandle større-datasæt, med relevante ændringer til cachelagring af data i hukommelsen og ændre klyngestørrelse.


## <a name="prerequisites"></a>Forudsætninger

Du har brug for en Azure-konto og en HDInsight knallertmotor du har du brug for en HDInsight 3.4 knallertmotor 1,6 klynge for at fuldføre denne gennemgang. Se [Oversigt over Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight](machine-learning-data-science-spark-overview.md) vejledning om, hvordan du opfylder kravene. Dette emne indeholder også en beskrivelse af NYC 2013 Taxi data, der bruges her og vejledning til, hvordan du kan udføre kode fra en Jupyter notesbog på knallertmotor klynge. Den **pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb** notesbog, der indeholder kodeeksempler i dette emne er tilgængelig i [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installation: lagerplads placeringer, biblioteker og konteksten forudindstillede knallertmotor

Knallertmotor kan læse og skrive til Azure-lager Blob (også kaldet WASB). Så et af dine eksisterende data gemt kan der behandles ved hjælp af tænding og de resultater, der er gemt igen i WASB.

Hvis du vil gemme modeller eller filer i WASB, skal stien være angivet korrekt. Objektbeholderen standard, der er knyttet til knallertmotor klynge kan skal refereres til ved hjælp af en sti, som begynder med: "wasb: / / /". Der refereres til andre placeringer efter "wasb: / /".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Angive directory stier for lagerplads placeringer i WASB

Følgende eksempel-kode angiver placeringen af data til at blive læst og stien til mappen model storage som model dokumentet gemmes:

    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

    # PRINT START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT**

DateTime.DateTime (2016, 4, 18, 17, 36, 27, 832799)


### <a name="import-libraries"></a>Importere biblioteker

Importere nødvendige biblioteker med følgende kode:

    # LOAD PYSPARK LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime
    

### <a name="preset-spark-context-and-pyspark-magics"></a>Forudindstillede knallertmotor kontekst og PySpark magics

De PySpark kerner, der følger med Jupyter notesbøger har en forudindstillet kontekst. Så du behøver ikke at angive knallertmotor eller Hive kontekster eksplicit, før du begynder at arbejde med programmet, du udvikler. Disse kontekster er tilgængelige for dig som standard. Disse kontekster er:

- SC - for gnister 
- sqlContext - for Hive

PySpark kernen indeholder nogle foruddefinerede "magics", som er specielle kommandoer, som du kan ringe til med %%. Der findes to sådanne kommandoer, der bruges i disse kodeeksempler.

- **%% lokale** Angiver, at koden i efterfølgende linjer, der skal udføres lokalt. Koden skal være gyldig Python kode.
- **%%sql -o <variable name>** Udfører en Hive-forespørgsel til sqlContext. Hvis parameteren -o er gået, bevares resultatet af forespørgslen i de %% lokale Python kontekst som en Pandas DataFrame.
 

For flere oplysninger om kerner for Jupyter notesbøger og den foruddefinerede "magics", der indeholder de, se [kerner, der er tilgængelige for Jupyter notesbøger med HDInsight knallertmotor Linux klynger på HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="data-ingestion-from-public-blob"></a>Data indtagelse fra offentlige blob: 

Det første trin i processen data videnskabelige er at indtager dataene for at analysere fra kilder, hvor det er placeret i din undersøgelse af data og modelmiljø. Dette miljø er knallertmotor i denne gennemgang. Dette afsnit indeholder koden for at fuldføre en række opgaver:

- indtager eksemplet data til at være udformet
- Læs i input datasættet (gemt som en .tsv-fil)
- formatere og rydde dataene
- oprette og gemme cachen objekter (RDDs eller data rammer) i hukommelsen
- registrere den som en temp-tabel i SQL-kontekst.

Her er koden for data indtagelse.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
        
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tid det tager at udføre over celle: 276.62 sekunder


## <a name="data-exploration--visualization"></a>Udforske data og visualisering 

Når dataene er blevet hentet ind i gnister, er næste trin i processen data videnskabelige til at få dybere forståelse af data via udforske og visuelle effekter. I dette afsnit skal vi undersøger taxi data med SQL-forespørgsler og afbilde target variabler og mulige funktioner for visuel inspektion. Nærmere betegnet kan afbilde vi hyppigheden for passager tæller i taxi rejser, hyppigheden for tip beløb og hvordan tip varierer afhængigt af betalingsbeløbet og type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Afbilde et histogram over passager Tæl frekvens inden for eksempel på taxi rejser

Denne kode og efterfølgende kodestykker Brug SQL magisk forespørgslen prøve- og lokale magisk at afbilde data.

- **SQL magisk (`%%sql`)** HDInsight PySpark kernen understøtter nemt indbygget HiveQL forespørgsler på sqlContext. Den (-o VARIABLE_NAME) argument fortsætter output fra SQL-forespørgslen som en Pandas DataFrame på Jupyter-serveren. Det betyder, at den er tilgængelig i den lokale tilstand.
- Den ** `%%local` magisk** bruges til at køre kode lokalt på Jupyter server, som er headnode af HDInsight klynge. Du bruger typisk `%%local` magiske sammen med den `%%sql` magiske med -o parameter. Parameteren -o ville fastholdes output fra SQL-forespørgslen lokalt og derefter %% lokale magisk vil udløse kodestykke til at køre lokalt mod output for SQL-forespørgsler, som er bevaret lokalt næste sæt

Output visualiseres automatisk, når du har kørt koden.

Denne forespørgsel henter rejser ved passager Tæl. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # SQL QUERY
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count


Denne kode opretter en lokale data-ramme fra forespørgslens output, og der plottes dataene. Den `%%local` magisk opretter en lokale data-ramme, `sqlResults`, som kan bruges til at angive med matplotlib. 

>[AZURE.NOTE] Denne PySpark magisk bruges flere gange i denne gennemgang. Hvis mængden data er stort, skal du eksempel for at oprette en data-ramme, der kan være i den lokale hukommelse.


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Her er koden for at afbilde rejser ved passager tæller

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    # PLOT PASSENGER NUMBER VS TRIP COUNTS
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT**

![Hyppigheden for rejser passager manuelt](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Du kan vælge mellem flere forskellige typer visualiseringer (tabel, cirkeldiagram, linje, et område eller liggende) ved hjælp af knapperne **Type** menu i notesbogen. Liggende afbildning er vist her.


### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Afbilde et histogram over tip beløb, og hvordan tip beløb varierer efter passager Tæl og pris beløb.

Bruge en SQL-forespørgsel til eksempeldata..
    
    # SQL SQUERY
    %%sql -q -o sqlResults
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train 
        WHERE passenger_count > 0 
        AND passenger_count < 7
        AND fare_amount > 0 
        AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 
        AND tip_amount < 25
    

Denne kode celle bruger SQL-forespørgslen til at oprette tre områder dataene.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    
    # TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount ($) by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
    ax.set_title('Tip amount by Fare amount ($)')
    ax.set_xlabel('Fare Amount')
    ax.set_ylabel('Tip Amount')
    plt.axis([-2, 120, -2, 30])
    plt.show()
    

**OUTPUT:** 

![Tip fordeling](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tip mængden af passager Tæl](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip mængden af pris beløb](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionen teknisk, transformation og data forberedelse til modellering

Dette afsnit beskrives og giver koden for procedurer, der bruges til at forberede data til brug i ML modellering. Det viser, hvordan du udføre følgende opgaver:

- Opret en ny funktion ved binning timer i trafik tid filsæt
- Indeksere og på hot kode kategoriske funktioner
- Oprette mærket punkt objekter, som input til ML funktioner
- Oprette et underordnet stikprøver af dataene og opdele den i kursus og test af sæt
- Funktionen skalering
- Cache objekter i hukommelsen


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Opret en ny funktion ved binning timer i trafik tid filsæt

Denne kode viser, hvordan du opretter en ny funktion med binning timer i trafik tid filsæt og derefter Sådan cache den resulterende data ramme i hukommelsen. Hvor distribueret af tolerant forsendelse datasæt (RDDs) og data rammer bruges flere gange, fører cachelagring til forbedret udførelse af klokkeslæt. Tilsvarende, vi cache RDDs og data-rammer i flere faser i denne gennemgang.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**OUTPUT**

126050


### <a name="index-and-one-hot-encode-categorical-features"></a>Indeksere og en hot kode kategoriske funktioner

Dette afsnit viser, hvordan du indeksere eller kode kategoriske funktioner for input til funktionerne modellering. Modellering og forudsige MLlib-funktioner kræver funktioner med kategoriske inputdataene indekseret eller kodet før brug. 

Afhængigt af modellen vil du indeksere eller kode dem på forskellige måder. For eksempel Logistic og lineær Regression modeller kræver en hot kodning, hvor, for eksempel en funktion med tre kategorier kan udvides til tre funktion kolonner med hver med 0 eller 1 afhængigt af kategorien af en bemærkning. MLlib indeholder [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funktionen for at gøre en hot kodning. Denne encoder knytter en kolonne med navnet indeks til en kolonne med binære vektorer med højst en enkelt én-værdi. Denne kodning kan algoritmer, der forventer numeriske værdier funktioner, som logistisk regression, der skal anvendes til kategoriske funktioner.

Her er koden til at indeksere og kode kategoriske funktioner:


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tid det tager at udføre over celle: 3.14 sekunder


### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Oprette mærket punkt objekter, som input til ML funktioner

Dette afsnit indeholder kode, der viser, hvordan du indeksere kategoriske tekstdata som en mærket punkt datatype og kode den, så den kan bruges til at undervise og teste MLlib logistisk regression og andre klassifikation modeller. Mærket punkt objekter er tolerant distribueret datasæt (RDD), der er formateret på en måde, der skal bruges som input data ved at de fleste af ML algoritmer i MLlib. En [mærket punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) er en lokal vektor, tæt eller sparse, der er knyttet til en etiket/svar.

Her er koden til at indeksere og kode tekstfunktioner for binære klassificering.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                             line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                   line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Her er koden at kode og indeksere kategoriske tekstfunktioner for lineær regressionsanalyse.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Oprette et underordnet stikprøver af dataene og opdele den i kursus og test af sæt

Denne kode opretter en stikprøver data (25% bruges her). Selvom det ikke er nødvendigt i dette eksempel på grund af størrelsen af datasættet, viser vi, hvordan du kan indsamle her så du ved, hvordan du kan bruge den til din egen problemet, når det er nødvendigt. Når eksempler er stor, kan dette spare betydeligt tid under kursus modeller. Næste opdele vi eksemplet til et kursus del (her 75%) og en test del (25% her) til brug i klassifikation og regression modellering.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    from pyspark.sql.functions import rand
    
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # CACHE TRAIN AND TEST DATA
    trainData.cache()
    testData.cache()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Tid det tager at udføre over celle: 0.31 sekunder


### <a name="feature-scaling"></a>Funktionen skalering

Funktionen skalering, også kendt som datanormalisering, sikrer, at er funktioner med værdier, der er meget udbetales ikke givet unødvendig afveje i funktionen målsætning. Koden for funktionen skalering bruger [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) til at skalere funktionerne til enhed variansen. Leveres af MLlib til brug i lineær regression med Stochastic graduering afstamning (SGD), en populære algoritme for kursus et bredt udvalg af andre machine learning-modeller som afklaret regressioner eller support vektor maskiner (SVM).   

>[AZURE.TIP] Vi har fundet LinearRegressionWithSGD algoritmen skal være følsomme skal være skalering.   

Her er koden til skala variabler til brug sammen med den regularized lineære SGD algoritme.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Tid det tager at udføre over celle: 11.67 sekunder


### <a name="cache-objects-in-memory"></a>Cache objekter i hukommelsen

Kan du reducere den tid, det tager til uddannelse og test af ML algoritmer ved at cachelagre inputdataene rammen objekter bruges til klassificering, regression og skaleret funktioner.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT** 

Tid det tager at udføre over celle: 0,13 sekunder


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Forudsige eller ej et tip som betales med binære klassifikation modeller

I dette afsnit beskrives, hvordan Brug tre modeller på opgaven binære klassifikation af forudsigelser, uanset om et tip er betalt for en taxi rejse. Datamodeller præsenteres er:

- Logistisk regression 
- Tilfældige skov
- Graduering at træer

Hver model opbygning af kodeafsnittet er opdelt i trin: 

1. **Model kursus** data med en parameter er indstillet
2. **Model evaluering** på datasættet test med målepunkter
3. **Gemme model** i blob til fremtidige forbrug

Vi viser, hvordan du gør kryds-validering (CV) med for hele på to måder:

1. Brug af **generisk** brugerdefineret kode, som kan knyttes til en hvilken som helst algoritme i MLlib og til en hvilken som helst parameter angiver i en algoritme. 
1. Brug af **pySpark CrossValidator pipeline funktionen**. Bemærk, at selvom det er praktisk, baseret på vores erfaring CrossValidator har nogle begrænsninger for gnister 1.5.0: 

    - Pipeline modeller blev ikke gemt/bevaret til fremtidige forbrug.
    - Kan ikke bruges for hver parameter i en model.
    - Kan ikke bruges for hver MLlib algoritme.


### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Standard på tværs af validering og hyperparameter gadefejning bruges med logistisk regression algoritmen til binær klassifikation

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en logistisk regressionsmodel med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , der beregner eller ej et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasættet. Modellen er uddannelse ved hjælp af på tværs af validering (CV) og hyperparameter gadefejning implementeret med brugerdefineret kode, der kan anvendes på en af learning algoritmerne i MLlib.   

>[AZURE.NOTE] Kørsel af denne brugerdefinerede CV-kode kan tage flere minutter.

**Uddannelse af modellen logistisk regression ved hjælp af CV og hyperparameter gadefejning**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    
    # CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
    from sklearn.grid_search import ParameterGrid
    grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
    paramGrid = list(ParameterGrid(grid))
    numModels = len(paramGrid)
    
    # SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
    nFolds = 3;
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    # BEGIN CV WITH PARAMETER SWEEP
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create LabeledPoints from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowOneHotBinary)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowOneHotBinary)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            regt = paramGrid[j]['regType']
            regp = paramGrid[j]['regParam']
            iters = paramGrid[j]['iterations']
            tol = paramGrid[j]['tolerance']
            # Train logistic regression model with hypermarameter set
            model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                      regParam=regp, tolerance = tol, intercept=True)
            predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
            # Use ROC-AUC as accuracy metrics
            validMetrics = BinaryClassificationMetrics(predictionAndLabels)
            metric = validMetrics.areaUnderROC
            metricSum[j] += metric
    
    avgAcc = metricSum / nFolds;
    bestParam = paramGrid[np.argmax(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
        
    # TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
    logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                                  iterations=bestParam['iterations'], 
                                                  regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                                  intercept=True)
    
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitBest.weights))
    print("Intercept: " + str(logitBest.intercept))
    
    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Koefficienter: [0.0082065285375-0.0223675576104-0.0183812028036, og - 3.48124578069e-05-0.00247646947233-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, og 0.054518719222]

Skæringspunktet:-0.0111216486893

Tid det tager at udføre over celle: 14.43 sekunder


**Evaluere binære klassifikation modellen med standard målepunkter**

Koden i dette afsnit viser, hvordan evaluere en logistisk regressionsmodel mod test-datasættet, herunder en afbildning af ROC kurven.


    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT LIBRARIES
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # PREDICT ON TEST DATA WITH BEST/FINAL MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");

    # PRINT ELAPSED TIME    
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Området under kurs = 0.985336538462

Området under ROC = 0.983383274312

Oversigt over statistik

Præcision = 0.984174341679

Tilbagekalde = 0.984174341679

F1 Resultat = 0.984174341679

Tid det tager at udføre over celle: 2.67 sekunder


**Afbilde ROC kurven.**

*PredictionAndLabelsDF* er registreret som en tabel, *tmp_results*, i den forrige celle. *tmp_results* kan bruges til at gøre forespørgsler og outputresultater til sqlResults data-ramme til afbildning. Her er koden.


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Her er koden for at foretage prognoser og afbilde ROC-kurven.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES                              
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc
    
    #PREDICTIONS
    predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    # PLOT ROC CURVES
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
    

**OUTPUT**

![Logistisk regression ROC kurven for generisk metode](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)


**Fastholdes model i en blob til fremtidige forbrug**

Koden i dette afsnit viser, hvordan logistisk regressionsmodellen til forbrug skal gemmes.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    # PERSIST MODEL
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    
    logitBest.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";


**OUTPUT**

Tid det tager at udføre over celle: 34.57 sekunder


### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Funktionen Mllib's CrossValidator rørledning med logistisk regression (elastiske regression) model

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en logistisk regressionsmodel med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , der beregner eller ej et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasættet. Modellen er uddannelse ved hjælp af på tværs af validering (CV) og hyperparameter gadefejning implementeres med funktionen MLlib CrossValidator pipeline til CV med parameter oprydning.   


>[AZURE.NOTE] Kørsel af denne MLlib CV-kode kan tage flere minutter.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    from sklearn.metrics import roc_curve,auc
    
    # DEFINE ALGORITHM / MODEL
    lr = LogisticRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build()
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=BinaryClassificationEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    ## PREDICT AND EVALUATE ON TEST DATA-SET

    # USE TEST DATASET FOR PREDICTION
    testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
    test_predictions = cv_model.transform(testDataFrame)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT**

Tid det tager at udføre over celle: 107.98 sekunder


**Afbilde ROC kurven.**

*PredictionAndLabelsDF* er registreret som en tabel, *tmp_results*, i den forrige celle. *tmp_results* kan bruges til at gøre forespørgsler og outputresultater til sqlResults data-ramme til afbildning. Her er koden.


    # QUERY RESULTS
    %%sql -q -o sqlResults
    SELECT label, prediction, probability from tmp_results

Her er koden for at afbilde ROC kurven.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
    %%local
    from sklearn.metrics import roc_curve,auc
    
    # ROC CURVE
    prob = [x["values"][1] for x in sqlResults["probability"]]
    fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)
    
    #PLOT
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


**OUTPUT**

![Logistisk regression ROC kurve ved hjælp af Mllib's CrossValidator](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)



### <a name="random-forest-classification"></a>Tilfældige skov klassifikation

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en tilfældigt skov regression, der beregner hvorvidt et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasættet.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Området under ROC = 0.985336538462

Tid det tager at udføre over celle: 26.72 sekunder


### <a name="gradient-boosting-trees-classification"></a>Graduering at træer klassifikation

Koden i dette afsnit viser, hvordan til uddannelse, evaluere, og gemme en gradueret at træer model, der beregner eller ej et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasæt.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                    numIterations=10)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # Area under ROC curve
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Området under ROC = 0.985336538462

Tid det tager at udføre over celle: 28.13 sekunder


## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Forudsige tip beløb med regression modeller (ikke med CV)

I dette afsnit beskrives, hvordan Brug tre modeller for den simple lineære regression opgave i at forudsige mængden spidsen betalt for en taxi forretningsrejse baseret på andre tip funktioner. Datamodeller præsenteres er:

- Afklaret lineær regression
- Tilfældige skov
- Graduering at træer

Disse modeller er beskrevet i introduktionen. Hver model opbygning af kodeafsnittet er opdelt i trin: 

1. **Model kursus** data med en parameter er indstillet
2. **Model evaluering** på datasættet test med målepunkter
3. **Gemme model** i blob til fremtidige forbrug   


>AZURE Bemærk: Kryds-validering ikke bruges med de tre regression modeller i dette afsnit, da dette blev vist i detaljer for logistisk regression modeller. Et eksempel, der viser, hvordan du bruger CV med elastiske Net til lineær regression er angivet i tillæg i dette emne.


>AZURE Bemærk: Vores erfaring, der kan være problemer med overensstemmelse mellem LinearRegressionWithSGD modeller, og parametrene skal være ændret/optimeret omhyggeligt for at opnå en gyldig model. Skalering af variabler betydeligt hjælper med konvergens. Elastiske net regression, vises i tillæg til dette emne, kan også bruges i stedet for LinearRegressionWithSGD.


### <a name="linear-regression-with-sgd"></a>Lineær regression med SGD

Kode i dette afsnit viser, hvordan du bruger skalerede funktioner til at undervise en lineær regression, som bruger stochastic graduering afstamning (SGD) til optimering, og hvordan du resultat, evaluere og Gem modellen i Azure Blob Storage (WASB).

>[AZURE.TIP] I vores oplevelse, der kan være problemer med overensstemmelse mellem LinearRegressionWithSGD-modeller og parametrene skal være ændret/optimeret omhyggeligt for at opnå en gyldig model. Skalering af variabler betydeligt hjælper med konvergens.


    # LINEAR REGRESSION WITH SGD 

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

Koefficienter: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

SKÆRING: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

Tid det tager at udføre over celle: 38.62 sekunder


### <a name="random-forest-regression"></a>Tilfældig skov regression

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en tilfældigt skov model, der beregner omkostninger for datatypen NYC taxi forretningsrejse.   

>[AZURE.NOTE] Kryds-validering med hele ved hjælp af brugerdefineret kode-parameter er angivet i tillægget.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    # UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT**

RMSE = 0.931981967875

R-sqr = 0.733445485802

Tid det tager at udføre over celle: 25.98 sekunder


### <a name="gradient-boosting-trees-regression"></a>Graduering at træer regression

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en gradueret at træer model, der beregner omkostninger for datatypen NYC taxi forretningsrejse.

**Uddannelse og evaluerer**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    # TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    # EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
    test_predictions= sqlContext.createDataFrame(predictionAndLabels)
    test_predictions_pddf = test_predictions.toPandas()
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Tid det tager at udføre over celle:: 20,9 sekunder


**Afbilde**
    
*tmp_results* er registreret som en Hive tabel i den forrige celle. Resultaterne fra tabellen er output til *sqlResults* data-ramme til afbildning. Her er koden

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Her er koden for at afbilde data ved hjælp af Jupyter-server.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import numpy as np
    
    # PLOT
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 15])
    plt.show(ax)

![Faktisk-vs-skønsmæssigt beregnet-tip-beløb](./media/machine-learning-data-science-spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)


## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Tillæg: Andre regressionsspecifikke opgaver ved hjælp af cross validering med parameter Sweep

Dette tillæg indeholder kode, der viser, hvordan du gør CV med elastiske redskaber til lineær regression og hvordan du gør CV med parameter oprydning ved hjælp af brugerdefineret kode til tilfældige skov regression.


### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Cross validering ved hjælp af elastiske net til lineær regression

Kode i dette afsnit viser, hvordan du cross validering med elastiske redskaber til lineær regression og til at evaluere modellen mod testdata.

    ###  CV USING ELASTIC NET FOR LINEAR REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.regression import LinearRegression
    from pyspark.ml import Pipeline
    from pyspark.ml.evaluation import RegressionEvaluator
    from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
    
    # DEFINE ALGORITHM/MODEL
    lr = LinearRegression()
    
    # DEFINE GRID PARAMETERS
    paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                                  .addGrid(lr.maxIter, (5, 10))\
                                  .addGrid(lr.tol, (1e-4, 1e-5))\
                                  .addGrid(lr.elasticNetParam, (0.25,0.75))\
                                  .build() 
    
    # DEFINE PIPELINE 
    # SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
    pipeline = Pipeline(stages=[lr])
    
    # DEFINE CV WITH PARAMETER SWEEP
    cv = CrossValidator(estimator= lr,
                        estimatorParamMaps=paramGrid,
                        evaluator=RegressionEvaluator(),
                        numFolds=3)
    
    # CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
    trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])
    
    # TRAIN WITH CROSS-VALIDATION
    cv_model = cv.fit(trainDataFrame)
    

    # EVALUATE MODEL ON TEST SET
    testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])
    
    # MAKE PREDICTIONS ON TEST DOCUMENTS
    # cvModel uses the best model found (lrModel).
    predictionAndLabels = cv_model.transform(testDataFrame)
    
    # CONVERT TO DF AND SAVE REGISER DF AS TABLE
    predictionAndLabels.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

Tid det tager at udføre over celle: 161.21 sekunder

**Evaluere med R-SQR metrikværdi**

*tmp_results* er registreret som en Hive tabel i den forrige celle. Resultaterne fra tabellen er output til *sqlResults* data-ramme til afbildning. Her er koden

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT label,prediction from tmp_results


Her er koden til at beregne R-sqr.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    from scipy import stats
    
    #R-SQR TEST METRIC
    corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
    r2 = (corstats[2]*corstats[2])
    print("R-sqr = %s" % r2)


**OUTPUT**

R-sqr = 0.619184907088


### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Cross validering med parameter oprydning ved hjælp af brugerdefineret kode til tilfældige skov regression

Kode i dette afsnit viser, hvordan du cross validering med parameter oprydning ved hjælp af brugerdefineret kode til tilfældige skov regression og til at evaluere modellen mod testdata.


    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    # GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    from sklearn.grid_search import ParameterGrid
    
    ## CREATE PARAMETER GRID
    grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
    paramGrid = list(ParameterGrid(grid))
    
    ## SPECIFY LEVELS OF CATEGORICAL VARIBLES
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
    nFolds = 3;
    numModels = len(paramGrid)
    h = 1.0 / nFolds;
    metricSum = np.zeros(numModels);
    
    for i in range(nFolds):
        # Create training and x-validation sets
        validateLB = i * h
        validateUB = (i + 1) * h
        condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
        validation = trainData.filter(condition)
        # Create labeled points from data-frames
        if i > 0:
            trainCVLabPt.unpersist()
            validationLabPt.unpersist()
        trainCV = trainData.filter(~condition)
        trainCVLabPt = trainCV.map(parseRowIndexingRegression)
        trainCVLabPt.cache()
        validationLabPt = validation.map(parseRowIndexingRegression)
        validationLabPt.cache()
        # For parameter sets compute metrics from x-validation
        for j in range(numModels):
            maxD = paramGrid[j]['maxDepth']
            numT = paramGrid[j]['numTrees']
            # Train logistic regression model with hypermarameter set
            rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=numT, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=maxD, maxBins=32)
            predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
            predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
            # Use ROC-AUC as accuracy metrics
            validMetrics = RegressionMetrics(predictionAndLabels)
            metric = validMetrics.rootMeanSquaredError
            metricSum[j] += metric
    
    avgAcc = metricSum/nFolds;
    bestParam = paramGrid[np.argmin(avgAcc)];
    
    # UNPERSIST OBJECTS
    trainCVLabPt.unpersist()
    validationLabPt.unpersist()
            
    ## TRAIN FINAL MODL WIHT BEST PARAMETERS
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

    # EVALUATE MODEL ON TEST DATA
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
    
    #PRINT TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT**

RMSE = 0.906972198262

R-sqr = 0.740751197012

Tid det tager at udføre over celle: 69.17 sekunder


### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Rydde op i objekter fra hukommelse og udskrive model placeringer

Brug `unpersist()` til at slette objekter, der er cachelagret i hukommelsen.

    # UNPERSIST OBJECTS CACHED IN MEMORY

    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    trainData.unpersist()
    trainData.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


**OUTPUT**

PythonRDD [122] på RDD på PythonRDD.scala: 43


**Udskrift stien til modelfiler skal bruges i notesbogen, forbrug.** For at bruge og resultat en uafhængig datasæt, skal du kopiere og indsætte disse filnavne i "forbrug notesbogen".


    # PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Hvad skal der ske nu?

Nu hvor du har oprettet regression og klassifikation modeller med styret MlLib, er du klar til at se, hvordan du resultat og evaluerer disse modeller.

**Model forbrug:** Hvis du vil lære at resultat og evaluerer de klassificering og regression modeller, der er oprettet i dette emne, skal du se [karakteren og evaluerer knallertmotor indbygget machine learning modeller](machine-learning-data-science-spark-model-consumption.md).
