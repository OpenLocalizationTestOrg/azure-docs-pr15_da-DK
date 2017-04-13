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


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Opbygge Machine Learning programmer til at køre på Apache knallertmotor klynger på HDInsight Linux

Lær, hvordan du opretter en machine learning-program ved hjælp af en Apache knallertmotor klynge i HDInsight. I denne artikel viser, hvordan du bruger den Jupyter notesbog, der er tilgængelige med klyngen til at opbygge og teste vores program. Programmet bruger HVAC.csv eksempeldataene, der er tilgængelige på alle klynger som standard.

**Forudsætninger for:**

Du skal have følgende:

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- En Apache knallertmotor klynge på HDInsight Linux. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Vis mig dataene

Før vi går i gang med at oprettelsen af programmet, Lad os forstå strukturen i dataene og typen analyse vi vil gøre på dataene. 

Vi bruger eksempel **HVAC.csv** -datafil, som er tilgængelig i kontoen Azure-lager, som du har knyttet til HDInsight klyngen i denne artikel. Filen er i lagerplads konto på **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Download og Åbn csv-filen for at få et øjebliksbillede af dataene.  

![VVS-data øjebliksbillede] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Øjebliksbillede af VVS-data")

Data, der viser mål temperaturen og faktisk temperaturen bygning, der indeholder VVS-systemer, der er installeret. Lad os antage kolonnen **System** repræsenterer system-ID og kolonnen **SystemAge** repræsenterer antallet af år VVS-systemet har været på plads, så building.

Vi bruger disse data til at forudsige, om en bygning skal være hotter eller colder baseret på mål temperaturen, ud fra en system-ID og system alder.

##<a name="app"></a>Skriv et machine learning-program ved hjælp af knallertmotor MLlib

I dette program bruger vi en knallertmotor ML pipeline til at udføre en dokument klassifikation. I pipeline, vi opdelt dokumentet i ord, konverterer en numerisk funktion vektor ordene og til sidst opbygge en forudsigelse model ved hjælp af funktionen vektorer og etiketter. Udfør følgende trin for at oprette programmet.

1. Klik på feltet for din knallertmotor klynge fra [Azure-portalen](https://portal.azure.com/), fra startboard (hvis det fastgjort til startboard). Du kan også gå til din klynge under **Gennemse alle** > **HDInsight klynger**.   

2. Klik på **Klynge Dashboard**fra bladet knallertmotor klynge, og klik derefter på **Jupyter notesbog**. Hvis du bliver bedt om det, kan du angive Administratoroplysninger for-klyngen.

    > [AZURE.NOTE] Du kan også når Jupyter notesbogen til din klynge ved at åbne følgende URL-adressen i din browser. Erstat __CLUSTERNAME__ med navnet på din klynge:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Oprette en ny notesbog. Klik på **Ny**, og klik derefter på **PySpark**.

    ![Opret en ny Jupyter notesbog] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Opret en ny Jupyter notesbog")

3. En ny notesbog er oprettet og åbnes med navnet Untitled.pynb. Klik på navnet på notesbogen øverst, og Angiv et fuldt navn.

    ![Angiv et navn til notesbogen] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Angiv et navn til notesbogen")

3. Fordi du har oprettet en notesbog ved hjælp af PySpark kernen, behøver du ikke at oprette en hvilken som helst kontekster eksplicit. Tænding og Hive kontekster oprettes automatisk for dig, når du kører den første celle i kode. Du kan starte ved at importere de typer, der kræves til dette scenario. Indsæt følgende kodestykke i en tom celle, og tryk derefter på **SKIFT + ENTER**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. Du skal nu indlæser data (hvac.csv), fortolkes det og bruge det til at undervise modellen. For det definerer du en funktion, der kontrollerer, om faktiske temperaturen af building er større end temperaturen mål. Hvis den faktiske temperatur er større, er building varm, markeret med værdien **1.0**. Hvis den faktiske temperatur er mindre, er building koldtvandssystemer, markeret med værdien **0,0**. 

    Indsæt følgende kodestykke i en tom celle og trykke på **SKIFT + ENTER**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Konfigurere knallertmotor machine learning rørledning, der består af tre faser: tokenizer, hashingTF og lr. Du kan finde flere oplysninger om, hvad er en rørledning, og hvordan det fungerer <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">knallertmotor machine learning-pipeline</a>.

    Indsæt følgende kodestykke i en tom celle og trykke på **SKIFT + ENTER**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Tilpasse pipeline til uddannelse dokumentet. Indsæt følgende kodestykke i en tom celle og trykke på **SKIFT + ENTER**.

        model = pipeline.fit(training)

7. Kontrol undervisningsdokument til kontrolpunkt forløbet med programmet. Indsæt følgende kodestykke i en tom celle og trykke på **SKIFT + ENTER**.

        training.show()

    Dette bør give output ligner følgende:

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Gå tilbage og bekræfte output mod rå csv-filen. For eksempel har den første række csv-filen disse data:

    ![VVS-data øjebliksbillede] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Øjebliksbillede af VVS-data")

    Bemærk, hvordan faktiske temperaturen er mindre end den destinationsadresse temperaturen tyder building er kolde. Værdien for **etiket** i den første række er derfor **0,0**, hvilket betyder, at building ikke er varmt i outputtet kursus.

8.  Forberede en stokastisk variabel til at køre erfaren modellen mod. Hvis du vil gøre det, vi sender på et system-ID og system alder (angivet som **SystemInfo** i kursus output), og modellen ville forudsige om building med den system-ID og system alder ville være hotter (angivet med 1.0) eller smartere (angivet med 0,0).

    Indsæt følgende kodestykke i en tom celle og trykke på **SKIFT + ENTER**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Til sidst skal gøre prognoser på testdataene. Indsæt følgende kodestykke i en tom celle og trykke på **SKIFT + ENTER**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Du burde se output ligner følgende:

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    Fra den første række i forudsigelse, kan du se, til en VVS-systemer med id-20 og 25 år system alder, vil være en genvejstast building (**forudsigelse = 1.0**). Den første værdi for DenseVector (0.49999) svarer til forudsigelse 0,0 og den anden værdi (0.5001) svarer til forudsigelse 1.0. I outputtet, selvom den anden værdi er kun smule højere modellen viser **forudsigelse = 1.0**.

11. Når du er færdig med at køre programmet, skal du lukke notesbogen for at frigøre ressourcerne. Hvis du vil gøre det, skal du fra menuen **filer** på notesbogen, skal du klikke på **Luk og standse**. Denne blive lukket, og Luk notesbogen.
           

##<a name="anaconda"></a>Brug Anaconda scikit-Lær bibliotek til computeren Learning

Apache knallertmotor klynger på HDInsight omfatter Anaconda biblioteker. Dette omfatter også den **scikit-få mere at vide** bibliotek til computeren learning. Biblioteket indeholder også forskellige datasæt, som du kan bruge til at oprette eksempelprogrammer direkte fra en Jupyter notesbog. Eksempler på ved hjælp af scikit-lære bibliotek, skal du se [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Se også

* [Oversigt: Apache knallertmotor på Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til at forudsige mad undersøgelsesresultaterne](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
