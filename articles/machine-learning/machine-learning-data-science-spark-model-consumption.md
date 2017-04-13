<properties
    pageTitle="Resultat knallertmotor indbygget machine learning modeller | Microsoft Azure"
    description="Sådan karakteren learning modeller, der er gemt i Azure Blob Storage (WASB)."
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

# <a name="score-spark-built-machine-learning-models"></a>Resultat knallertmotor indbygget machine learning modeller 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Dette emne beskrives, hvordan du indlæse modeller maskine læ (ML), der er opbygget ved hjælp af knallertmotor MLlib og gemmes i Azure Blob-lager (WASB), og hvordan til at give dem med datasæt, der er gemt i WASB. Det viser hvordan du allerede behandle inputdataene, transformere funktioner ved hjælp af funktionerne indeksering og kodning i MLlib værktøjerne, og hvordan du opretter et mærket punkt dataobjekt, der kan bruges som input til vundne med ML-modeller. De modeller, der bruges til vundne med lineær Regression, logistisk Regression, tilfældige skov-modeller og graduering øger træet modeller.


## <a name="prerequisites"></a>Forudsætninger

1. Du har brug for en Azure-konto og en HDInsight knallertmotor du har du brug for en HDInsight 3.4 knallertmotor 1,6 klynge for at fuldføre denne gennemgang. Se [Oversigt over Data videnskabelige ved hjælp af knallertmotor på Azure HDInsight](machine-learning-data-science-spark-overview.md) vejledning om, hvordan du opfylder kravene. Dette emne indeholder også en beskrivelse af NYC 2013 Taxi data, der bruges her og vejledning til, hvordan du kan udføre kode fra en Jupyter notesbog på knallertmotor klynge. Den **pySpark-machine-learning-data-science-spark-model-consumption.ipynb** notesbog, der indeholder kodeeksempler i dette emne er tilgængelig i [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. Du skal også oprette machine learning-modeller til vurderes her ved gennemgår emnet [undersøgelse af Data og modellering med gnister](machine-learning-data-science-spark-data-exploration-modeling.md) .   


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installation: lagerplads placeringer, biblioteker og konteksten forudindstillede knallertmotor

Knallertmotor kan læse og skrive til en Azure lagerplads Blob (WASB). Så et af dine eksisterende data gemt kan der behandles ved hjælp af tænding og de resultater, der er gemt igen i WASB.

Hvis du vil gemme modeller eller filer i WASB, skal stien være angivet korrekt. Objektbeholderen standard, der er knyttet til knallertmotor klynge kan skal refereres til ved hjælp af en sti, som begynder med: *"wasb / /"*. Følgende eksempel-kode angiver placeringen af data til at blive læst og stien til mappen model storage som model output er gemt. 


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Angive directory stier for lagerplads placeringer i WASB

Modeller gemmes i: "wasb: / / / brugere/remoteuser/NYCTaxi/modeller". Hvis denne sti ikke er angivet korrekt, indlæses ikke modeller resultatværdier.

Scored resultaterne er blevet gemt i: "wasb: / / / brugere/remoteuser/NYCTaxi/ScoredResults". Hvis stien til mappen er forkert, gemmes ikke resultater i den pågældende mappe.   


>[AZURE.NOTE] Stien filplaceringer kan kopieres og indsættes i pladsholderne i denne kode fra outputtet af den sidste celle i **machine-learning-data-science-spark-data-exploration-modeling.ipynb** notesbogen.   


Her er koden for at angive directory stier: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
    
    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
    
    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**OUTPUT:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)


### <a name="import-libraries"></a>Importere biblioteker

Angive knallertmotor kontekst og importere det er nødvendigt biblioteker med følgende kode

    #IMPORT LIBRARIES
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

De PySpark kerner, der følger med Jupyter notesbøger har en forudindstillet kontekst. Så du behøver ikke at angive knallertmotor eller Hive kontekster eksplicit, før du begynder at arbejde med programmet, du udvikler. Disse er tilgængelige for dig som standard. Disse kontekster er:

- SC - for gnister 
- sqlContext - for Hive

PySpark kernen indeholder nogle foruddefinerede "magics", som er specielle kommandoer, som du kan ringe til med %%. Der findes to sådanne kommandoer, der bruges i disse kodeeksempler.

- **%% lokale** Angivet, koden i efterfølgende linjer udført lokalt. Koden skal være gyldig Python kode.
- **%% sql -o<variable name>** 
- Udfører en Hive-forespørgsel til sqlContext. Hvis parameteren -o er gået, bevares resultatet af forespørgslen i de %% lokale Python kontekst som en Pandas dataframe.
 

For flere oplysninger om kerner for Jupyter notesbøger og den foruddefinerede "magics", der indeholder de, se [kerner, der er tilgængelige for Jupyter notesbøger med HDInsight knallertmotor Linux klynger på HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Indtager data og oprette en rensede data ramme

Dette afsnit indeholder koden for en række opgaver, der kræves til indtager dataene skal bedømmes. Læs i en joinforbundne 0,1% stikprøve af taxi forretningsrejse og pris filen (gemt som en .tsv-fil), formatere dataene og opretter derefter en ren data ramme.

Taxi forretningsrejse og pris filer er joinforbundne baseret på den procedure i den: [Team Data videnskabelige proces i praksis: Brug af HDInsight Hadoop klynger](machine-learning-data-science-process-hive-walkthrough.md) emne.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
        
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
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
    
    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tid det tager at udføre over celle: 46.37 sekunder


## <a name="prepare-data-for-scoring-in-spark"></a>Forbered dataene til pointsystemet i knallertmotor 

Dette afsnit viser, hvordan at indeksere, kode og skalere kategoriske funktioner til at klargøre dem til brug i MLlib kontrolleret learning algoritmer for klassificering og regression.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funktionen transformation: indeksere og kode kategoriske funktioner for input i modeller resultatværdier. 

Dette afsnit beskrives, hvordan indeksere kategoriske data ved hjælp af en `StringIndexer` og kode funktioner med `OneHotEncoder` input i modellerne.

[StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) koder en streng tekstkolonne til en kolonne med navnet indeks. Indeksene er sorteret efter etiket hyppigheden. 

[OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) knytter en kolonne med navnet indeks til en kolonne med binære vektorer med højst en enkelt én-værdi. Denne kodning kan algoritmer, der forventer fortløbende valgmuligheder funktioner, som logistisk regression, der skal anvendes til kategoriske funktioner.
    
    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()
    
    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
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
    
    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tid det tager at udføre over celle: 5.37 sekunder


### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Oprette RDD objekter med funktion matrixer om input i modeller

Dette afsnit indeholder kode, der viser, hvordan du indeksere kategoriske tekstdata som et RDD objekt og en hot kode den, så den kan bruges til at undervise og teste MLlib logistisk regression og trævisning-baserede modeller. De indekserede data er gemt i [Tolerant distribueret datasæt (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) objekter. Dette er grundlæggende fremstilling i gnister. Et RDD objekt repræsenterer en fast, partitioneret samling elementer, der kan være drevet på parallelt med gnister.

Den også indeholder kode, der viser, hvordan du kan tilpasse data med den `StandardScalar` leveres af MLlib til brug i lineær regression med Stochastic graduering afstamning (SGD), en populære algoritme for kursus et bredt udvalg af machine learning modeller. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) bruges til at skalere funktionerne til enhed variansen. Funktionen skalering, også kendt som datanormalisering, sikrer, at er funktioner med værdier, der er meget udbetales ikke givet unødvendig afveje i funktionen målsætning. 


    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
    
    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
    
    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
    
    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTPUT:**

Tid det tager at udføre over celle: 11.72 sekunder


## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Resultat med logistiske regressionsmodellen og gemme output til blob

Koden i dette afsnit viser, hvordan at indlæse en logistiske Regression Model, der er gemt i Azure blob-lager og bruge det til at forudsige eller ej et tip som betales på et taxi forretningsrejse, resultat med standard klassifikation statistik, og derefter gemme og afbilde resultaterne til blob-lager. Scored resultaterne er gemt i RDD objekter. 


    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
    
    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tid det tager at udføre over celle: 19.22 sekunder


## <a name="score-a-linear-regression-model"></a>Resultat af en lineær regressionsmodel

Vi har brugt [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) til at undervise en lineær regressionsmodel ved hjælp af Stochastic graduering afstamning (SGD) for at optimere til at forudsige mængden tip, som er betalt. 

Koden i dette afsnit viser, hvordan du indlæse en lineær Regression Model fra Azure blob-lager, resultat ved hjælp af skalerede variabler, og Gem derefter resultaterne tilbage til blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
    
    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**OUTPUT:**

Tid det tager at udføre over celle: 16.63 sekunder


## <a name="score-classification-and-regression-random-forest-models"></a>Resultat klassificering og regression tilfældige skov modeller

Kode i dette afsnit viser, hvordan at indlæse den gemte klassificering og regression tilfældige skov modeller gemmes i Azure blob-lager, resultat deres ydeevne med standard klassificering og regression målinger, og Gem derefter resultaterne tilbage til blob-lager.

[Tilfældig områder](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) er sæt af beslutningstræer.  De kombinerer mange beslutningstræer for at reducere risikoen for overfitting. Tilfældige områder kan håndtere kategoriske funktioner, udvide til indstillingen multiclass klassifikation, kræver ikke funktion skalering og kan registrere ikke-linearities og funktionen interaktioner. Tilfældige områder er en af de mest vellykket machine learning-modeller for klassificering og regression.

[Spark.mllib](http://spark.apache.org/mllib/) understøtter tilfældige områder binære og multiclass klassifikation og simple lineære regression ved hjælp af både fortløbende og kategoriske funktioner. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES 
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**OUTPUT:**

Tid det tager at udføre over celle: 31.07 sekunder


## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Resultat klassificering og regression graduering øger træet modeller

Koden i dette afsnit viser, hvordan du indlæse klassificering og regression graduering øger træet modeller fra Azure blob-lager, resultat deres ydeevne med standard klassificering og regression målinger, og Gem derefter resultaterne tilbage til blob-lager. 

**Spark.mllib** understøtter GBTs binære klassifikation og simple lineære regression ved hjælp af både fortløbende og kategoriske funktioner. 

[Graduering at træer](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) er sæt af beslutningstræer. GBTs Oplær beslutningstræer flere gange for at minimere en tab-funktion. GBTs kan håndtere kategoriske funktioner, kræver ikke funktion skalering og kan registrere ikke-linearities og funktionen interaktioner. De kan også bruges i en multiclass klassifikation indstilling.


    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
    
**OUTPUT:**

Tid det tager at udføre over celle: 14.6 sekunder


## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Rydde op i objekter fra hukommelse og udskrive karakter filplaceringer

    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**OUTPUT:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt



## <a name="consume-spark-models-through-a-web-interface"></a>Bruge knallertmotor modeller via en webgrænseflade

Knallertmotor giver en mulighed for at sende fra en fjernplacering batchjob eller interaktive forespørgsler via en RESTEN grænseflade med en komponent, kaldet Livius. Livius er aktiveret som standard på din HDInsight Spark klynge. Finde flere oplysninger om Livius: [sende knallertmotor job ved hjælp af Livius](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md). 

Du kan bruge Livius fra en fjernplacering sende et job, der batch pointtal, der er en fil, der er gemt i en Azure blob og skriver derefter resultaterne til en anden blob. For at gøre dette skal overføre du Python scriptet fra  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) til blob af knallertmotor klynge. Du kan bruge et værktøj som **Microsoft Azure lagerplads Explorer** eller **AzCopy** til at kopiere scriptet til klynge blob. I dette tilfælde har vi overført scriptet til ***wasb:///example/python/ConsumeGBNYCReg.py***.   


>[AZURE.NOTE] Hurtigtasterne, som du har brug for kan findes på portalen for kontoen lagerplads, der er knyttet til knallertmotor klynge. 


Når overført til denne placering, kører dette script i knallertmotor klynge i en fordelt kontekst. Det indlæser modellen og køre prognoser på input filer, der er baseret på modellen.  

Du kan aktivere denne script fra en fjernplacering ved at oprette en simpel HTTPS/RESTEN anmodning på Livius.  Her er en krøllet kommando til at oprette HTTP-anmodning om at kalde Python scriptet fra en fjernplacering. Erstat CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME med de relevante værdier for din knallertmotor klynge.


    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Du kan bruge et sprog på det eksterne system til at starte knallertmotor jobbet gennem Livius ved at gøre et enkelt HTTPS-opkald med basisgodkendelse.   


>[AZURE.NOTE] Det kan være praktisk at bruge biblioteket Python anmodninger, når du foretager denne HTTP-opkald, men det er ikke aktuelt er installeret som standard i Azure funktioner. Så ældre HTTP-biblioteker, der bruges i stedet.   


Her er Python koden for HTTP opkaldet:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64
    
    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'
    
    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
    
    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Du kan også tilføje denne Python kode til [Azure funktioner](https://azure.microsoft.com/documentation/services/functions/) til at udløse en knallertmotor jobbet, pointtal, der er en blob baseret på forskellige hændelser som en timer, oprettelse eller opdatering af en blob. 

Hvis du foretrækker en kode gratis klient oplevelse, kan du bruge [Azure logik Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) til at starte knallertmotor batchen vundne ved at definere en HTTP-handling på **Logik Apps Designer** og angive dens parametre. 

- Fra Azure-portalen, oprette en ny logik App ved at vælge **+ Ny** -> **Web + Mobile** -> **Logik App**. 
- Skriv navnet på den logik App og App-tjenesten Plan for at få vist **Logik Apps Designer**.
- Vælg en HTTP-handling, og Angiv de parametre, der er vist i følgende figur:

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## <a name="whats-next"></a>Hvad skal der ske nu? 

**Tværs validering og hyperparameter hele**: se [Avanceret undersøgelse af data og modellering med knallertmotor](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) på hvordan modeller kan være uddannelse ved hjælp af tværs validering og hyper-parameter gadefejning.
