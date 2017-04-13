<properties 
    pageTitle="Bruge eksterne pakker med Jupyter notesbøger i Apache knallertmotor klynger på HDInsight | Azure"
    description="Trinvis vejledning til, hvordan du konfigurerer Jupyter notesbøger, der er tilgængelige med HDInsight Spark klynger bruge eksterne knallertmotor pakker." 
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
    ms.date="10/28/2016" 
    ms.author="nitinme"/>


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Bruge eksterne pakker med Jupyter notesbøger i Apache knallertmotor klynger på HDInsight Linux

>[AZURE.NOTE] I denne artikel gælder for tænding 1.5.2 på HDInsight 3.3 og knallertmotor 1.6.2 på HDInsight 3.4. 

Lær, hvordan du kan konfigurere en Jupyter notesbog i Apache knallertmotor klynge på HDInsight (Linux) til at bruge eksterne, bidraget til community-pakker, der ikke er inkluderet out box i klyngen. 

Du kan søge [Maven lager](http://search.maven.org/) til den komplette liste over pakker, der er tilgængelige. Du kan også finde en liste over tilgængelige pakker fra andre kilder. For eksempel er en komplet liste over bidraget til community-pakker tilgængelig på [Knallertmotor pakker](http://spark-packages.org/).

I denne artikel lærer du, hvordan du bruger [knallertmotor-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) -pakke med Jupyter notesbogen.

##<a name="prerequisites"></a>Forudsætninger

Du skal have følgende:

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- En Apache knallertmotor klynge på HDInsight Linux. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Bruge eksterne pakker med Jupyter notesbøger 

1. Klik på feltet for din knallertmotor klynge fra [Azure-portalen](https://portal.azure.com/), fra startboard (hvis det fastgjort til startboard). Du kan også gå til din klynge under **Gennemse alle** > **HDInsight klynger**.   

2. Klik på **Hurtige Links**fra bladet knallertmotor klynge, og klik derefter på **Jupyter notesbog**fra bladet **Klynge Dashboard** . Hvis du bliver bedt om det, kan du angive Administratoroplysninger for-klyngen.

    > [AZURE.NOTE] Du kan også når Jupyter notesbogen til din klynge ved at åbne følgende URL-adressen i din browser. Erstat __CLUSTERNAME__ med navnet på din klynge:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Oprette en ny notesbog. Klik på **Ny**, og klik derefter på **gnister**.

    ![Opret en ny Jupyter notesbog] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Opret en ny Jupyter notesbog")

3. En ny notesbog er oprettet og åbnes med navnet Untitled.pynb. Klik på navnet på notesbogen øverst, og Angiv et fuldt navn.

    ![Angiv et navn til notesbogen] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Angiv et navn til notesbogen")

4. Du skal bruge den `%%configure` magisk til at konfigurere notesbog for at bruge en ekstern pakke. Kontroller, at du ringer i notesbøger, der bruger eksterne pakker, den `%%configure` magiske i den første celle i kode. Dette sikrer, at kernen er konfigureret til at bruge funktionen pakke, før sessionen starter.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Hvis du glemmer at konfigurere kernen i den første celle, kan du bruge den `%%configure` med den `-f` parameter, men som genstartes sessionen og alle fremdrift, går tabt.

5. I kodestykket ovenfor, `packages` forventer en liste over maven koordinater i Maven centrallager. I denne kodestykket `com.databricks:spark-csv_2.10:1.4.0` er maven koordinere for **gnister-csv** -pakke. Her er, hvordan du opbygger koordinater for en pakke.

    en. Find pakken i Maven lager. Dette selvstudium skal bruge vi [knallertmotor-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Indsaml værdierne fra lageret til **gruppe-id**, **ArtifactId**og **Version**.

    ![Brug eksterne pakker med Jupyter notesbog] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Brug eksterne pakker med Jupyter notesbog")

    c. Sammenkæde de tre værdier, adskilt af et kolon (**:**).

        com.databricks:spark-csv_2.10:1.4.0

6. Køre kode cellen med den `%%configure` magisk. Dette vil konfigurere det underliggende Livius session for at bruge funktionen pakke, du har angivet. I de efterfølgende celler i notesbogen, kan du nu bruge pakke, som vist nedenfor.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. Du kan derefter køre kodestykker, som vist nedenfor, til at få vist data fra den dataframe, du har oprettet i ovenstående trin.

        df.show()

        df.select("Time").count()


## <a name="seealso"></a>Se også


* [Oversigt: Apache knallertmotor på Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)

* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til analyse af bygning temperaturen ved hjælp af VVS-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

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

* [Installere Jupyter på din computer og oprette forbindelse til en HDInsight Spark klynge](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)
