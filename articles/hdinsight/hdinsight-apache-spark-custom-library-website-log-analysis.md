<properties 
    pageTitle="Bruge brugerdefinerede biblioteker med en HDInsight Spark klynge til at analysere websted logfiler | Microsoft Azure" 
    description="Brug brugerdefinerede biblioteker med en HDInsight Spark klynge til at analysere logfiler over websted" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analysere websted logfiler, der bruger et brugerdefineret bibliotek med Apache knallertmotor klynge på HDInsight Linux

Denne notesbog viser, hvordan til at analysere logdata ved hjælp af et brugerdefineret bibliotek med knallertmotor på HDInsight. Biblioteket brugerdefinerede vi bruge er et Python dokumentbibliotek kaldet **iislogparser.py**.

> [AZURE.TIP] Dette selvstudium findes også som en Jupyter notesbog på en knallertmotor (Linux) klynge, du opretter i HDInsight. Notebook-oplevelse kan du køre Python kodestykker fra notesbogen sig selv. For at udføre selvstudiet i en notesbog, du opretter en knallertmotor klynge, Start en Jupyter notesbog (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), og derefter køre notesbogen, **analysér logger med knallertmotor ved hjælp af en brugerdefineret library.ipynb** under mappen **PySpark** .

**Forudsætninger for:**

Du skal have følgende:

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- En Apache knallertmotor klynge på HDInsight Linux. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Gemme rækkedata som en RDD

I dette afsnit skal bruge vi den [Jupyter](https://jupyter.org) notesbog, der er knyttet til en Apache knallertmotor klynge i HDInsight til at køre job, der behandler dine rå eksempeldata og gemme den som en Hive-tabel. Eksempeldataene er en .csv-fil (hvac.csv) tilgængelige på alle klynger som standard.

Når dine data er gemt som en Hive-tabel, i næste afsnit opretter vi forbindelse til tabellen Hive ved hjælp af BI-værktøjer, som Power BI og Tableau.

1. Klik på feltet for din knallertmotor klynge fra [Azure-portalen](https://portal.azure.com/), fra startboard (hvis det fastgjort til startboard). Du kan også gå til din klynge under **Gennemse alle** > **HDInsight klynger**.   

2. Klik på **Klynge Dashboard**fra bladet knallertmotor klynge, og klik derefter på **Jupyter notesbog**. Hvis du bliver bedt om det, kan du angive Administratoroplysninger for-klyngen.

    > [AZURE.NOTE] Du kan også når Jupyter notesbogen til din klynge ved at åbne følgende URL-adressen i din browser. Erstat __CLUSTERNAME__ med navnet på din klynge:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Oprette en ny notesbog. Klik på **Ny**, og klik derefter på **PySpark**.

    ![Opret en ny Jupyter notesbog] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Opret en ny Jupyter notesbog")

3. En ny notesbog er oprettet og åbnes med navnet Untitled.pynb. Klik på navnet på notesbogen øverst, og Angiv et fuldt navn.

    ![Angiv et navn til notesbogen] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Angiv et navn til notesbogen")

4. Fordi du har oprettet en notesbog ved hjælp af PySpark kernen, behøver du ikke at oprette en hvilken som helst kontekster eksplicit. Tænding og Hive kontekster oprettes automatisk for dig, når du kører den første celle i kode. Du kan starte ved at importere de typer, der kræves til dette scenario. Indsæt følgende kodestykke i en tom celle, og tryk derefter på **SKIFT + ENTER**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Oprette en RDD ved hjælp af log eksempeldataene allerede findes på klyngen. Du kan få adgang til dataene i standardkontoen lagerplads der er knyttet til klynge på **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Hente en eksempel logfil, der er angivet til at kontrollere, at det forrige trin, der er fuldført.

        logs.take(5)

    Du burde se output ligner følgende:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analysere logdata ved hjælp af et brugerdefineret Python bibliotek

7. De første par linjer medtage overskrift i outputtet ovenfor, og hver resterende linje svarer til skemaet, der er beskrevet i denne sidehoved. Parsing af disse logfiler kan være komplicerede. Så bruge vi et brugerdefineret Python-bibliotek (**iislogparser.py**), der gør parsing af disse logfiler, der er meget nemmere. Dette bibliotek er inkluderet i din knallertmotor klynge på HDInsight på **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**som standard.

    Dette bibliotek er dog ikke i den `PYTHONPATH` så vi ikke kan bruge den ved hjælp af en import-sætning som `import iislogparser`. Hvis du vil bruge dette bibliotek, skal vi distribuere den til alle arbejder noder. Kør følgende kodestykke.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`indeholder en funktion `parse_log_line` , der returnerer `None` Hvis en log linje er en kolonneoverskrift, og returnerer en forekomst af den `LogLine` klasse, hvis der opstår en log linje. Brug den `LogLine` klasse til at udtrække linjerne log fra RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Hente et par udpakkede log linjer at kontrollere, at det trin, der er fuldført.

        logLines.take(2)

    Output skal ligne følgende:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. Den `LogLine` klassen, har også nogle nyttige metoder, vil `is_error()`, som returnerer om post i en logfil har en fejlkode. Brug dette til at beregne antallet af fejl i udpakkede log linjerne, og log derefter alle fejl i en anden fil.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Du burde se output ud som følger:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. Du kan også bruge **Matplotlib** for at opbygge en visualisering af dataene. Hvis du vil isolere årsagen til anmodninger, der kører i lang tid, kan du for eksempel vil til at finde de filer, der tager mest tid til at fungere i gennemsnit.
Den nedenstående kodestykke henter de øverste 25 ressourcer, der tog de fleste tid at kunne levere en anmodning.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Du burde se output ud som følger:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. Du kan også præsentere oplysningerne i form af afbildning. Som et første trin til at oprette en afbildning, Lad os først oprette en midlertidig tabel **AverageTime**. Tabellen grupperer loggene efter tid til at se, om der opstod en hvilken som helst usædvanlige ventetid spidser på et bestemt tidspunkt.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Du kan derefter køre følgende SQL-forespørgslen for at få alle posterne i tabellen **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    Den `%%sql` magisk efterfulgt af `-o averagetime` sikrer, at forespørgslens output er bevaret lokalt på Jupyter serveren (typisk headnode af klyngen). Output bevares som en [Pandas](http://pandas.pydata.org/) dataframe med det angivne navn **averagetime**.

    Du burde se output ud som følger:

    ![SQL forespørgslens output] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "SQL forespørgslens output")

    Du kan finde flere oplysninger om den `%%sql` magisk samt andre magics, der er tilgængelige med kernen PySpark se [kerner, der er tilgængelige på Jupyter notesbøger med knallertmotor HDInsight klynger](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Du kan nu bruge Matplotlib, et bibliotek, der bruges til at oprette visualisering af data, du opretter en afbildning. Fordi afbildning skal oprettes fra lokalt permanente **averagetime** dataframe, kodestykket skal starte med den `%%local` magisk. Dette sikrer, at koden køres lokalt på serveren Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Du burde se output ud som følger:

    ![Matplotlib output] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Matplotlib output")

16. Når du er færdig med at køre programmet, skal du lukke notesbogen for at frigøre ressourcerne. Hvis du vil gøre det, skal du fra menuen **filer** på notesbogen, skal du klikke på **Luk og standse**. Denne blive lukket, og Luk notesbogen.
    

## <a name="seealso"></a>Se også


* [Oversigt: Apache knallertmotor på Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til analyse af bygning temperaturen ved hjælp af VVS-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til at forudsige mad undersøgelsesresultaterne](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Knallertmotor Streaming: Brug knallertmotor i HDInsight til udvikling af realtid streaming programmer](hdinsight-apache-spark-eventhub-streaming.md)

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
