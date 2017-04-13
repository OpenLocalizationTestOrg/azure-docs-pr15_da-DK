<properties
    pageTitle="Udforske data og modellering med knallertmotor | Microsoft Azure"
    description="Viser data udforske og modellering funktionerne i knallertmotor MLlib værktøjerne."
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
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="data-exploration-and-modeling-with-spark"></a>Udforske data og modellering med knallertmotor

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Denne gennemgang bruger HDInsight Spark til at gøre undersøgelse af data og binære klassificering og regression modeling opgaver på en stikprøve af NYC taxi forretningsrejse og pris 2013 datasæt.  Det vejleder dig gennem trinnene til [Data videnskabelige proces](http://aka.ms/datascienceprocess), slutpunkterne til hinanden, ved hjælp af en HDInsight Spark klynge til behandling og Azure BLOB for at gemme dataene og modellerne. Processen udforsker og visualiseres data fra en Azure-lager Blob hentet og derefter laver dataene til at oprette skønnet modeller. Disse modeller er build ved hjælp af værktøjerne knallertmotor MLlib til at udføre binære klassifikation og regression modellering opgaver.

- **Binær klassifikation** opgaven er at forudsige eller ej et tip er betalt for forretningsrejse. 
- Den **simple lineære regression** opgave er at forudsige mængden spidsen baseret på andre tip funktioner. 

Modeller vi bruge omfatter logistiske og lineære regression, tilfældige områder og graduering øget træer:

- [Lineær regression med SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) er en lineær regressionsmodel, der bruger en Stochastic graduering afstamning (SGD) metode og til optimering og funktion skalering til at forudsige tip Beløb betalt. 
- [Logistisk regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller "logit" regression er en regressionsmodel, der kan bruges, når den afhængige variabel er kategoriske til at gøre data klassificering. LBFGS er en kvasi Newton optimering algoritme, der tilnærmer algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS) ved hjælp af en begrænset mængde computerens hukommelse og, der anvendes på machine learning.
- [Tilfældig områder](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) er sæt af beslutningstræer.  De kombinerer mange beslutningstræer for at reducere risikoen for overfitting. Tilfældige områder bruges til regression og klassifikation og kan håndtere kategoriske funktioner og kan udvides til indstillingen multiclass klassificering. De kræver ikke funktion skalering og kan registrere ikke-linearities og funktionen interaktioner. Tilfældige områder er en af de mest vellykket machine learning-modeller for klassificering og regression.
- [Graduering øget træer](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) er sæt af beslutningstræer. GBTs Oplær beslutningstræer flere gange for at minimere en tab-funktion. GBTs bruges til regression og klassifikation og kan håndtere kategoriske funktioner, kræver ikke funktion skalering og kan registrere ikke-linearities og funktionen interaktioner. De kan også bruges i en multiclass klassifikation indstilling.

Modellering trinnene indeholder også kode, der viser, hvordan du træne, evaluere og gemme de forskellige typer model. Python er blevet brugt til at kode løsningen og for at vise de relevante:.   


>[AZURE.NOTE] Selvom knallertmotor MLlib værktøjerne er designet til at arbejde på store datasæt, bruges en lille prøve (~ 30 Mb ved hjælp af 170K rækker, om 0,1% af det oprindelige NYC datasæt) her til nemmere. Den opgave, der er angivet her kører effektivt (i omkring 10 minutter) på en HDInsight klynge med 2 arbejder noder. Den samme kode, med mindre ændringer, kan bruges til at behandle større-datasæt, med relevante ændringer til cachelagring af data i hukommelsen og ændre klyngestørrelse.

## <a name="prerequisites"></a>Forudsætninger

Du har brug for en Azure-konto og en HDInsight knallertmotor du har du brug for en HDInsight 3.4 knallertmotor 1,6 klynge for at fuldføre denne gennemgang. Se [Oversigt over Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight](machine-learning-data-science-spark-overview.md) vejledning om, hvordan du opfylder kravene. Dette emne indeholder også en beskrivelse af NYC 2013 Taxi data, der bruges her og vejledning til, hvordan du kan udføre kode fra en Jupyter notesbog på knallertmotor klynge. Den **pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb** notesbog, der indeholder kodeeksempler i dette emne er tilgængelig i [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark). 


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
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Importere biblioteker

Konfigurere kræver også importere nødvendige biblioteker. Angive knallertmotor kontekst og importere det er nødvendigt biblioteker med følgende kode:


    # IMPORT LIBRARIES
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
 

## <a name="data-ingestion-from-public-blob"></a>Indtagelse data fra offentlige blob

Det første trin i processen data videnskabelige er at indtager dataene for at analysere fra kilder, hvor der er placeret i dine data udforske og modellering miljø. Miljøet er knallertmotor i denne gennemgang. Dette afsnit indeholder koden for at fuldføre en række opgaver:

- indtager eksemplet data til at være udformet
- Læs i input datasættet (gemt som en .tsv-fil)
- formatere og rydde dataene
- oprette og gemme cachen objekter (RDDs eller data rammer) i hukommelsen
- registrere den som en temp-tabel i SQL-kontekst.

Her er koden for data indtagelse.

    # INGEST DATA

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

    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tid det tager at udføre over celle: 51.72 sekunder


## <a name="data-exploration--visualization"></a>Udforske data og visualisering

Når dataene er blevet hentet ind i gnister, er næste trin i processen data videnskabelige til at få dybere forståelse af data via udforske og visuelle effekter. I dette afsnit skal vi undersøger taxi data med SQL-forespørgsler og afbilde target variabler og mulige funktioner for visuel inspektion. Nærmere betegnet kan afbilde vi hyppigheden for passager tæller i taxi rejser, hyppigheden for tip beløb og hvordan tip varierer afhængigt af betalingsbeløbet og type.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Afbilde et histogram over passager Tæl frekvens inden for eksempel på taxi rejser

Denne kode og efterfølgende kodestykker Brug SQL magisk forespørgslen prøve- og lokale magisk at afbilde data.

- **SQL magisk (`%%sql`)** HDInsight PySpark kernen understøtter nemt indbygget HiveQL forespørgsler på sqlContext. Den (-o VARIABLE_NAME) argument fortsætter output fra SQL-forespørgslen som en Pandas DataFrame på Jupyter-serveren. Det betyder, at den er tilgængelig i den lokale tilstand.
- Den ** `%%local` magisk** bruges til at køre kode lokalt på Jupyter server, som er headnode af HDInsight klynge. Du bruger typisk `%%local` magiske sammen med den `%%sql` magiske med -o parameter. Parameteren -o ville fastholdes output fra SQL-forespørgslen lokalt og derefter %% lokale magisk vil udløse kodestykke til at køre lokalt mod output for SQL-forespørgsler, som er bevaret lokalt næste sæt

Output visualiseres automatisk, når du har kørt koden.

Denne forespørgsel henter rejser ved passager Tæl. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Denne kode opretter en lokale data-ramme fra forespørgslens output, og der plottes dataene. Den `%%local` magisk opretter en lokale data-ramme, `sqlResults`, som kan bruges til at angive med matplotlib. 

>[AZURE.NOTE] Denne PySpark magisk bruges flere gange i denne gennemgang. Hvis mængden data er stort, skal du eksempel for at oprette en data-ramme, der kan være i den lokale hukommelse.

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Her er koden for at afbilde rejser ved passager tæller

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**OUTPUT:**

![Forretningsrejse frekvens passager manuelt](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Du kan vælge mellem flere forskellige typer visualiseringer (tabel, cirkel, linje, et område eller liggende) ved hjælp af knapperne **Type** menu i notesbogen. Liggende afbildning er vist her.
    
### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Afbilde et histogram over tip beløb, og hvordan tip beløb varierer efter passager Tæl og pris beløb.

Bruge en SQL-forespørgsel til eksempeldata.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE
    
    # HIVEQL QUERY AGAINST THE sqlContext
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

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**OUTPUT:** 

![Tip fordeling](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Tip mængden af passager Tæl](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tip beløb med pris afstand](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktionen teknisk, transformation og data forberedelse til modellering
Dette afsnit beskrives og giver koden for procedurer, der bruges til at forberede data til brug i ML modellering. Det viser, hvordan du udføre følgende opgaver:

- Opret en ny funktion ved binning timer i trafik tid filsæt
- Indeksere og kode kategoriske funktioner
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

**OUTPUT:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indeksere og kode kategoriske funktioner for input i modeling funktioner

Dette afsnit viser, hvordan du indeksere eller kode kategoriske funktioner for input til funktionerne modellering. Modellering og forudsige MLlib-funktioner kræver funktioner med kategoriske inputdataene indekseret eller kodet før brug. Afhængigt af modellen vil du indeksere eller kode dem på forskellige måder:  

- **Baseret træstruktur modellering** kræver kategorier for at være kodet som numeriske værdier (for eksempel en funktion med tre kategorier kan kodes med 0, 1, 2). Dette er angivet af Mllib's [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) funktion. Denne funktion koder en strengkolonne med etiketter til en kolonne af etiket indeks, der er sorteret efter etiket hyppigheden. Selvom indekseret med numeriske værdier for input og håndtering af data, kan de træ-baserede algoritmer angives til at behandle dem korrekt som kategorier. 

- **Logistic og lineær Regression modeller** kræver en hot kodning, hvor, for eksempel en funktion med tre kategorier kan udvides til tre funktion kolonner med hver med 0 eller 1 afhængigt af kategorien af en bemærkning. MLlib indeholder [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) funktionen for at gøre en hot kodning. Denne encoder knytter en kolonne med navnet indeks til en kolonne med binære vektorer med højst en enkelt én-værdi. Denne kodning kan algoritmer, der forventer numeriske værdier funktioner, som logistisk regression, der skal anvendes til kategoriske funktioner.

Her er koden til at indeksere og kode kategoriske funktioner:


    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
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

**OUTPUT:**

Tid det tager at udføre over celle: 1,28 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Oprette mærket punkt objekter, som input til ML funktioner

Dette afsnit indeholder kode, der viser, hvordan du indeksere kategoriske tekstdata som en mærket punkt datatype og kode den, så den kan bruges til at undervise og teste MLlib logistisk regression og andre klassifikation modeller. Mærket punkt objekter er tolerant distribueret datasæt (RDD), der er formateret på en måde, der skal bruges som input data ved at de fleste af ML algoritmer i MLlib. En [mærket punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) er en lokal vektor, tæt eller sparse, der er knyttet til en etiket/svar.  

Dette afsnit indeholder kode, der viser, hvordan du indeksere kategoriske tekstdata som en [mærket punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) datatype og kode den, så den kan bruges til at undervise og teste MLlib logistisk regression og andre klassifikation modeller. Mærket punkt objekter er tolerant distribueret datasæt (RDD) bestående af en etiket (target/svar variabel) og funktionen vektor. Dette format skal bruges som input ved mange ML algoritmer i MLlib.

Her er koden til at indeksere og kode tekstfunktioner for binære klassificering.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
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
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
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

**OUTPUT:**

Tid det tager at udføre over celle: 0,24 sekunder


### <a name="feature-scaling"></a>Funktionen skalering

Funktionen skalering, også kendt som datanormalisering, sikrer, at er funktioner med værdier, der er meget udbetales ikke givet unødvendig afveje i funktionen målsætning. Koden for funktionen skalering bruger [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) til at skalere funktionerne til enhed variansen. Leveres af MLlib til brug i lineær regression med Stochastic graduering afstamning (SGD), en populære algoritme for kursus et bredt udvalg af andre machine learning-modeller som afklaret regressioner eller support vektor maskiner (SVM).

>[AZURE.NOTE] Vi har fundet LinearRegressionWithSGD algoritmen skal være følsomme skal være skalering.

Her er koden til skala variabler til brug sammen med den regularized lineære SGD algoritme.

    # FEATURE SCALING

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

**OUTPUT:**

Tid det tager at udføre over celle: 13.17 sekunder


### <a name="cache-objects-in-memory"></a>Cache objekter i hukommelsen

Kan du reducere den tid, det tager til uddannelse og test af ML algoritmer ved at cachelagre inputdataene rammen objekter bruges til klassificering, regression, og skaleret funktioner.

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

**OUTPUT:** 

Tid det tager at udføre over celle: 0,15 sekunder


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Forudsige eller ej et tip som betales med binære klassifikation modeller

I dette afsnit beskrives, hvordan Brug tre modeller på opgaven binære klassifikation af forudsigelser, uanset om et tip er betalt for en taxi rejse. Datamodeller præsenteres er:

- Afklaret logistisk regression 
- Tilfældige skov model
- Graduering at træer

Hver model opbygning af kodeafsnittet er opdelt i trin: 

1. **Model kursus** data med en parameter er indstillet
2. **Model evaluering** på datasættet test med målepunkter
3. **Gemme model** i blob til fremtidige forbrug

### <a name="classification-using-logistic-regression"></a>Klassifikation ved hjælp af logistisk regression

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en logistisk regressionsmodel med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) , der beregner hvorvidt et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasættet.

**Uddannelse af modellen logistisk regression ved hjælp af CV og hyperparameter gadefejning**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    
    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT:** 

Koefficienter: [0.0082065285375-0.0223675576104-0.0183812028036, og - 3.48124578069e-05-0.00247646947233-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, og 0.054518719222]

Skæringspunktet:-0.0111216486893

Tid det tager at udføre over celle: 14.43 sekunder

**Evaluere binære klassifikation modellen med standard målepunkter**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
    
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


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:** 

Området under kurs = 0.985297691373

Området under ROC = 0.983714670256

Oversigt over statistik

Præcision = 0.984304060189

Tilbagekalde = 0.984304060189

F1 Resultat = 0.984304060189

Tid det tager at udføre over celle: 57.61 sekunder

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

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
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
    

**OUTPUT:**

![Logistisk regression ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)


### <a name="random-forest-classification"></a>Tilfældige skov klassifikation

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en tilfældigt skov model, der beregner hvorvidt et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasættet.
    
    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

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
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
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

**OUTPUT:**

Området under ROC = 0.985297691373

Tid det tager at udføre over celle: 31.09 sekunder


### <a name="gradient-boosting-trees-classification"></a>Graduering at træer klassifikation

Koden i dette afsnit viser, hvordan til uddannelse, evaluere, og gemme en gradueret at træer model, der beregner hvorvidt et tip er betalt for en rejse i NYC taxi forretningsrejse og pris datasæt.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
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


**OUTPUT:**

Området under ROC = 0.985297691373

Tid det tager at udføre over celle: 19.76 sekunder


## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Forudsige tip beløb for taxi rejser med regression modeller

I dette afsnit beskrives, hvordan Brug tre modeller for den simple lineære regression opgave i at forudsige mængden spidsen betalt for en taxi forretningsrejse baseret på andre tip funktioner. Datamodeller præsenteres er:

- Afklaret lineær regression
- Tilfældige skov
- Graduering at træer

Disse modeller er beskrevet i introduktionen. Hver model opbygning af kodeafsnittet er opdelt i trin: 

1. **Model kursus** data med en parameter er indstillet
2. **Model evaluering** på datasættet test med målepunkter
3. **Gemme model** i blob til fremtidige forbrug

### <a name="linear-regression-with-sgd"></a>Lineær regression med SGD 

Kode i dette afsnit viser, hvordan du bruger skalerede funktioner til at undervise en lineær regression, som bruger stochastic graduering afstamning (SGD) til optimering, og hvordan du resultat, evaluere og Gem modellen i Azure Blob Storage (WASB).

>[AZURE.TIP] I vores oplevelse, der kan være problemer med overensstemmelse mellem LinearRegressionWithSGD-modeller og parametrene skal være ændret/optimeret omhyggeligt for at opnå en gyldig model. Skalering af variabler betydeligt hjælper med konvergens. 


    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

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
    
    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Koefficienter: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

SKÆRING: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Tid det tager at udføre over celle: 58.42 sekunder


### <a name="random-forest-regression"></a>Tilfældig skov regression

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en tilfældigt skov regression, der beregner omkostninger for datatypen NYC taxi forretningsrejse.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
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

**OUTPUT:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Tid det tager at udføre over celle: 49.21 sekunder


### <a name="gradient-boosting-trees-regression"></a>Graduering at træer regression

Koden i dette afsnit viser, hvordan til uddannelse, evaluere og gemme en gradueret at træer model, der beregner omkostninger for datatypen NYC taxi forretningsrejse.

**Uddannelse og evaluerer**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Tid det tager at udføre over celle: 34.52 sekunder

**Afbilde**

*tmp_results* er registreret som en Hive tabel i den forrige celle. Resultaterne fra tabellen er output til *sqlResults* data-ramme til afbildning. Her er koden

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Her er koden for at afbilde data ved hjælp af Jupyter-server.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)
    

**OUTPUT:**

![Faktisk-vs-skønsmæssigt beregnet-tip-beløb](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

    
## <a name="clean-up-objects-from-memory"></a>Rydde op i objekter fra hukommelse

Brug `unpersist()` til at slette objekter, der er cachelagret i hukommelsen.
        
    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    
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


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Post lagerplads placeringer af modeller til forbrug og pointmodel for kundeemner

Bruge og resultat en uafhængig datasæt, der er beskrevet i de [karakteren og evaluerer knallertmotor indbygget machine learning modeller](machine-learning-data-science-spark-model-consumption.md) emne, du har brug at kopiere og indsætte disse filnavne, der indeholder de gemte modeller oprettes her til notesbogen, forbrug Jupyter. Her er koden for at udskrive stier til modelfiler, du skal bruge der.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"


## <a name="whats-next"></a>Hvad skal der ske nu?

Nu hvor du har oprettet regression og klassifikation modeller med styret MlLib, er du klar til at se, hvordan du resultat og evaluerer disse modeller. Avancerede data-udforske og -modellering notesbog dykker ned dybere i tværs-validering, hyper-parameter hele, herunder og modellere evaluering. 

**Model forbrug:** Hvis du vil lære at resultat og evaluerer de klassificering og regression modeller, der er oprettet i dette emne, skal du se [karakteren og evaluerer knallertmotor indbygget machine learning modeller](machine-learning-data-science-spark-model-consumption.md).

**Tværs validering og hyperparameter hele**: se [Avanceret undersøgelse af data og modellering med knallertmotor](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) på hvordan modeller kan være uddannelse ved hjælp af tværs validering og hyper-parameter gadefejning



