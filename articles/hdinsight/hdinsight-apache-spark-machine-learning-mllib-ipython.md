<properties 
    pageTitle="Bruge Apache knallertmotor til at oprette machine learning programmer på HDInsight | Microsoft Azure" 
    description="Trinvis vejledning til, hvordan du bruger notesbøger med Apache knallertmotor til at opbygge machine learning programmer" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="machine-learning-predictive-analysis-on-food-inspection-data-using-mllib-with-apache-spark-cluster-on-hdinsight-linux"></a>Machine learning: skønnet analyse af mad inspektion data ved hjælp af MLlib med Apache knallertmotor klynge på HDInsight Linux

> [AZURE.TIP] Dette selvstudium findes også som en Jupyter notesbog på en knallertmotor (Linux) klynge, du opretter i HDInsight. Notebook-oplevelse kan du køre Python kodestykker fra notesbogen sig selv. For at udføre selvstudiet i en notesbog, du opretter en knallertmotor klynge, Start en Jupyter notesbog (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), og derefter køre notesbogen **knallertmotor Machine Learning - skønnet analyse af mad inspektion data med MLLib.ipynb** under mappen **Python** .


I denne artikel viser, hvordan du bruger **MLLib**, Gnisters indbyggede machine learning-biblioteker til at udføre en simpel skønnet analyse på en åben datasættet. MLLib er et grundlæggende knallertmotor bibliotek, der indeholder en række funktioner, der er praktiske, når maskine learning opgaver, herunder programmer, der er velegnet til:

* Klassifikation

* Simple lineære regression

* Klynge

* Emne modellering

* Enkelt værdi opdelingstræ (SVD) og vigtigste komponent analyse (PCA)

* Hypotetisk test og beregning af statistik for eksempel

I denne artikel indeholder en enkelt metode til *klassifikation* gennem logistisk regression.

## <a name="what-are-classification-and-logistic-regression"></a>Hvad er klassifikation og logistisk regression?

*Klassifikation*, en meget almindelig maskine læ opgave, er processen med at sortere inputdataene i kategorier. Det er kørslen af en klassifikation algoritme til at finde ud af, hvordan du tildeler "navne" for at angive data, du angiver. For eksempel kan du betragte en maskine learning algoritme, der accepterer aktiekurser oplysninger som input og opdeler aktie i to kategorier: lagre, som du skal sælge og lagre, skal du bevare.

Logistisk regression er den algoritme, du bruger til klassificering. Knallertmotors logistisk regression API er nyttig til *binær klassifikation*eller klassificering inputdataene i én af to grupper. Du kan finde flere oplysninger om logistiske regressioner, [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Resultatet af logistisk regression resulterer i oversigt, *logistiske funktion* , der kan bruges til at forudsige sandsynligheden for, at en input vektor hører i en gruppe eller det andet.  

## <a name="what-are-we-trying-to-accomplish-in-this-article"></a>Hvad forsøger vi at udføre i denne artikel?

Du skal bruge knallertmotor til at udføre nogle skønnet analyse på mad inspektion data (**Food_Inspections1.csv**), der blev hentet via [portalen til datastyring by i Chicago](https://data.cityofchicago.org/). Dette dataset indeholder oplysninger om mad inspektioner, der blev udført i Chicago, herunder oplysninger om hver mad virksomhed, der blev kontrolleres, typekonverteringsfejl, der blev fundet (hvis relevant) og resultaterne af undersøgelsen. Csv-datafilen findes allerede i kontoen lagerplads, der er knyttet til klynge på **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

I nedenstående trin udvikler du en model for at se, hvad det tager at bestået eller mislykket en mad inspektion. 

## <a name="start-building-a-machine-learning-application-using-spark-mllib"></a>Begynde at opbygge et machine learning-program ved hjælp af knallertmotor MLlib

1. Klik på feltet for din knallertmotor klynge fra [Azure-portalen](https://portal.azure.com/), fra startboard (hvis det fastgjort til startboard). Du kan også gå til din klynge under **Gennemse alle** > **HDInsight klynger**.   

2. Klik på **Klynge Dashboard**fra bladet knallertmotor klynge, og klik derefter på **Jupyter notesbog**. Hvis du bliver bedt om det, kan du angive Administratoroplysninger for-klyngen.

    > [AZURE.NOTE] Du kan også når Jupyter notesbogen til din klynge ved at åbne følgende URL-adressen i din browser. Erstat __CLUSTERNAME__ med navnet på din klynge:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Oprette en ny notesbog. Klik på **Ny**, og klik derefter på **PySpark**.

    ![Opret en ny Jupyter notesbog] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Opret en ny Jupyter notesbog")

3. En ny notesbog er oprettet og åbnes med navnet Untitled.pynb. Klik på navnet på notesbogen øverst, og Angiv et fuldt navn.

    ![Angiv et navn til notesbogen] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Angiv et navn til notesbogen")

3. Fordi du har oprettet en notesbog ved hjælp af PySpark kernen, behøver du ikke at oprette en hvilken som helst kontekster eksplicit. Tænding og Hive kontekster oprettes automatisk for dig, når du kører den første celle i kode. Du kan begynde at opbygge din machine learning-program ved at importere de typer, der kræves til dette scenario. Placer markøren i cellen for at gøre det, og tryk på **SKIFT + ENTER**.


        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Oprette en input dataframe

Vi kan bruge `sqlContext` til at udføre transformationer på strukturerede data. Den første opgave er at indlæse eksempeldataene ((**Food_Inspections1.csv**)) i en knallertmotor SQL *dataframe*. 

1. Da den rækkedata er i en CSV-format, skal vi bruge knallertmotor kontekst til at adskille hver linje i filen i hukommelsen som ustrukturerede tekst; derefter skal du bruge Pythons csv-bibliotek til at fortolke hver linje enkeltvis. 


        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value
        
        inspections = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)


2. Vi har nu csv-filen som en RDD. Lad os hente én række fra RDD at forstå skema for dataene.


        inspections.take(1)


    Du burde se output ud som følger:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]


3. Ovenstående output giver os en ide om skemaet for den input fil. filen, der indeholder navnet på hver virksomhed, typen virksomhed, adressen, data fra kontrollen samt placeringen, blandt andet. Lad os markere nogle kolonner, der skal være nyttige til vores skønnet analyse og gruppere resultatet som en dataframe, som vi bruge til at oprette en midlertidig tabel.


        schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')

4. Vi har nu en *dataframe* `df` , vi kan udføre vores analyse. Vi har også en midlertidig tabel ringe **CountResults**. Vi har medtaget 4 kolonner af interesse i dataframe: **id**, **navn**, **resultater**og **overtrædelse af**. 
    
    Lad os få et lille udvalg af dataene:

        df.show(5)

    Du burde se output ud som følger:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Forstå dataene

1. Lad os starte at få en ide om, hvad vores datasæt indeholder. For eksempel Hvad er de forskellige værdier i kolonnen **resultater** ?


        df.select('results').distinct().show()

    
    Du burde se output ud som følger:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
    
2. En hurtig visualisering kan hjælpe os årsag om fordelingen af disse resultater. Vi har allerede dataene i en midlertidig tabel **CountResults**. Du kan køre følgende SQL-forespørgslen mod tabellen for at få en bedre forståelse af, hvordan resultaterne er fordelt.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    Den `%%sql` magisk efterfulgt af `-o countResultsdf` sikrer, at forespørgslens output er bevaret lokalt på Jupyter serveren (typisk headnode af klyngen). Output bevaret som en [Pandas](http://pandas.pydata.org/) dataframe med det angivne navn **countResultsdf**.
    
    Du burde se output ud som følger:
    
    ![SQL forespørgslens output] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "SQL forespørgslens output")

    Du kan finde flere oplysninger om den `%%sql` magisk samt andre magics, der er tilgængelige med kernen PySpark se [kerner, der er tilgængelige på Jupyter notesbøger med knallertmotor HDInsight klynger](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. Du kan også bruge Matplotlib, et bibliotek, der bruges til at oprette visualisering af data, du opretter en afbildning. Fordi afbildning skal oprettes fra lokalt permanente **countResultsdf** dataframe, kodestykket skal starte med den `%%local` magisk. Dette sikrer, at koden køres lokalt på serveren Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        
        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Du burde se output ud som følger:

    ![Resultatet output](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Du kan se, at der er 5 forskellige resultater, der kan have en inspektion:
    
    * Virksomheder, der ikke findes 
    * Mislykkes
    * Overfører
    * PSS m / betingelser, og
    * Af Business 

    Lad os udvikle en model, der kan gætte med resultatet af en mad inspektion givet typekonverteringsfejl. Da logistisk regression er en metode til binær klassificering, det giver mening at gruppere dine data i to kategorier: **mislykkes** og **overføre**. En "overfører m / betingelser" er stadig en gang, så når vi uddannelse af modellen, vi vil overveje de to resultater tilsvarende. Data med andre resultaterne ("Business ikke fundet", "ikke til Business") er ikke nyttig, så vi vil fjerne dem fra vores kurser sæt. Dette bør være orden, da disse to kategorier udgør en meget lille procentdel af resultaterne alligevel.

5. Lad os gå videre og konvertere vores eksisterende dataframe (`df`) til en ny dataframe, hvor hver inspektion repræsenteres som et etiket-overtrædelse af par. I dette tilfælde en etiket af `0.0` repræsenterer en fejl, en etiket af `1.0` repræsenterer et gunstigt udfald og en etiket af `-1.0` repræsenterer nogle resultater ud over disse to. Vi vil bortfiltrere disse andre resultater ved beregning af den nye data ramme.


        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


    Lad os hente én række fra den mærket data for at se, hvordan det ser ud.


        labeledData.take(1)


    Du burde se output ud som følger:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Oprette en logistisk regressionsmodel fra input dataframe

Vores endelige opgave er at konvertere mærket dataene til et format, der kan analysere ved logistisk regression. Input til en logistisk regression algoritme skal være et sæt af *etiket-funktion vektor par*, hvor "funktion vektor" er en vektor af tal, der repræsenterer det input punkt på en måde. Så vi har brug for en metode til at konvertere kolonnen "overtrædelse af", som er semistrukturerede og indeholder mange af kommentarer i ledig tekst, til en matrix med reelle tal, en maskine nemt kunne forstå. 

Et almindeligt maskine læ metode til at behandle naturligt sprog er til at tildele hvert særskilte ord "indeks", og derefter sende en vektor til maskinen læ algoritme, således at alle index værdi indeholder den relative hyppighed af ordet i tekststrengen. 

MLLib gør det nemt at udføre denne handling. Først skal vi vil "tokenize" hver overtrædelse af streng for at få de enkelte ord i de enkelte strenge og derefter vi bruger en `HashingTF` til at konvertere hvert sæt af tokens til en funktion vektor, derefter skal overføres til algoritmen logistisk regression at opbygge en model. Vi skal udføre alle disse trin i rækkefølge ved hjælp af en "rørledning".


    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    model = pipeline.fit(labeledData)


## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Evaluere modellen på en separat test datasættet

Vi kan bruge den model, vi oprettede tidligere til at *forudsige,* hvordan resultatet af nye inspektioner bliver baseret på typekonverteringsfejl, der blev observeret. Vi uddannelse denne model på datasættet **Food_Inspections1.csv**. Lad os bruge et andet datasæt, **Food_Inspections2.csv**, til at *evaluere* styrken af denne model på nye data. Dette andet datasæt (**Food_Inspections2.csv**) skal allerede være i beholderen standard lagerplads, der er knyttet til klyngen.

1. Den nedenstående kodestykke opretter en ny dataframe, **predictionsDf** , der indeholder forudsigelse genereres af modellen. Kodestykket opretter også en midlertidig tabel **prognoser** baseret på dataframe.


        testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns


    Du burde se output ud som følger:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
        
        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']

2. Se på en af prognoser. Kør denne kodestykke:

        predictionsDf.take(1)

    Du får vist forudsigelse for den første post i datasættet test.

3. Den `model.transform()` metode skal anvende den samme transformering til nye data med det samme skema, og ankommer til en forudsigelse af, hvordan du klassificere dataene. Vi kan gøre nogle enkle statistik for at få en ide om, hvordan nøjagtige vores prognoser er:


        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
                                              (prediction = 1 AND (results = 'Pass' OR 
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()
        
        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    Output ser ud som følger:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate


    Brug af logistisk regression med knallertmotor giver os en nøjagtig model af forholdet mellem overtrædelse af beskrivelser på engelsk og om en given virksomhed vil bestået eller mislykket en mad inspektion. 

## <a name="create-a-visual-representation-of-the-prediction"></a>Oprette en visuel repræsentation af forudsigelse

Vi kan nu oprette en endelige visualisering til at hjælpe os årsag om resultatet af denne test. 

1. Vi starter med uddrage forskellige prognoser og resultater fra tabellen **prognoser** midlertidige oprettede tidligere. Følgende forespørgsler adskille output som *true_positive*, *false_positive*, *true_negative*og *false_negative*. I nedenstående forespørgsler vi deaktivere visualisering ved hjælp af `-q` og også gemme output (ved hjælp af `-o`) som dataframes, der kan bruges derefter med den `%%local` magisk. 

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Til sidst skal du bruge følgende kodestykke til at generere afbildning ved hjælp af **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')
    
    Du bør se følgende output.
    
    ![Forudsigelse output](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


    I dette diagram refererer et "positive" resultat til inspektion mislykkedes mad, mens et negativt resultat refererer til en overførte inspektion.

## <a name="shut-down-the-notebook"></a>Lukke notesbogen

Når du er færdig med at køre programmet, skal du lukke notesbogen for at frigøre ressourcerne. Hvis du vil gøre det, skal du fra menuen **filer** på notesbogen, skal du klikke på **Luk og standse**. Denne blive lukket, og Luk notesbogen.


## <a name="seealso"></a>Se også


* [Oversigt: Apache knallertmotor på Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til analyse af bygning temperaturen ved hjælp af VVS-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Knallertmotor Streaming: Brug knallertmotor i HDInsight til udvikling af realtid streaming programmer](hdinsight-apache-spark-eventhub-streaming.md)

* [Websted log analyse ved hjælp af knallertmotor i HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Oprette og køre programmer

* [Oprette en enkeltstående program, ved hjælp af Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Køre job fra en fjernplacering på en knallertmotor klynge, ved hjælp af Livius](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Værktøjer og filtypenavne

* [Brug HDInsight værktøjer plug-in til IntelliJ ide at oprette og sende knallertmotor Scala programmer](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Brug HDInsight værktøjer plug-in til IntelliJ ide for at fejlfinde knallertmotor programmer fra en fjernplacering](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Bruge Zeppelin notesbøger med en knallertmotor klynge på HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kerner, der er tilgængelige for Jupyter notesbog i knallertmotor klynge i HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Bruge eksterne pakker med Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installere Jupyter på din computer og oprette forbindelse til en HDInsight Spark klynge](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)
