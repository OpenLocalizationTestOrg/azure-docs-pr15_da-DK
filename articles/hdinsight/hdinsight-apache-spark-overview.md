<properties 
    pageTitle="En oversigt over Apache knallertmotor i HDInsight | Microsoft Azure" 
    description="En introduktion til Apache tænding i HDInsight og scenarier, hvor du bruger knallertmotor på HDInsight i dine programmer." 
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
    ms.topic="get-started-article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="overview-apache-spark-on-hdinsight-linux"></a>Oversigt: Apache knallertmotor på HDInsight Linux
 
<a href="http://spark.apache.org/" target="_blank">Apache knallertmotor</a> er et open source-parallel behandling af framework, der understøtter i hukommelsen behandling for at øge ydeevnen for stor data analytisk programmer. Knallertmotor behandlingsprogram er bygget til hastighed, brugervenlighed, og avancerede analyser. Knallertmotors i hukommelsen beregning funktioner gør det et godt valg for gentagen algoritmer i maskine læring og graph beregninger. Knallertmotor er også kompatibel med Azure Blob-lager (WASB), så dine eksisterende data, der er gemt i Azure kan nemt behandles via gnister.

Når du opretter en knallertmotor klynge i HDInsight, kan du oprette Azure Beregn ressourcer med knallertmotor installeret og konfigureret. Det kræver kun om ti minutter til at oprette en knallertmotor klynge i HDInsight. Dataene skal behandles er gemt i Azure Blob-lager. Se [Brug Azure Blob-lager med HDInsight][hdinsight-storage].

![Apache knallertmotor på Azure HDInsight] (./media/hdinsight-apache-spark-overview/hdispark.architecture.png  "Apache knallertmotor på Azure HDInsight")


**Vil du kommer i gang med Apache knallertmotor på Azure HDInsight?** Se [Hurtig start: oprette en knallertmotor klynge på HDInsight Linux og køre eksempelprogrammer, der bruger Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md).

>[AZURE.NOTE] En liste over kendte problemer og begrænsninger i forbindelse med den aktuelle udgave, kan du se [kendte problemer i Apache knallertmotor i Azure HDInsight (Linux)](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="why-use-spark-on-azure-hdinsight"></a>Hvorfor bruge knallertmotor på Azure HDInsight? 

Azure HDInsight indeholder en komplet administreret knallertmotor tjeneste. Fordelene ved at bruge knallertmotor på HDInsight er:

| Funktion                             | Beskrivelse       |
|-------------------------------------|-------------------|
| Nemmere at oprette klynger            | Du kan oprette en ny knallertmotor klynge på HDInsight i minutter ved hjælp af portalen Azure Management, Azure PowerShell eller HDInsight .NET SDK. Se [Introduktion til knallertmotor klynge i HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Brugervenlig                     | Knallertmotor i HDInsight klynger omfatter Jupyter notesbøger, der er konfigureret på forhånd. Du kan bruge disse oplysninger til interaktive databehandling og visuelle effekter. URL-adresser til den er https://CLUSTERNAME.azurehdinsight.net/jupyter. Erstat __CLUSTERNAME__ med navnet på din klynge knallertmotor HDInsight.|
| REST API'er                       | Knallertmotor i HDInsight omfatter [Livius](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), en REST-API baseret knallertmotor jobserver for at sende og overvåge igangværende job fra en fjernplacering. |
| Understøttelse af Azure sø datalager | Knallertmotor på HDInsight kan være konfigureret til at bruge Azure datalager sø som en ekstra lagerplads. Du kan finde flere oplysninger om sø datalager, [Oversigt over Azure sø datalager](../data-lake-store/data-lake-store-overview.md).
| Integration med Azure services | Knallertmotor på HDInsight leveres med en forbindelse til Azure begivenhed hubber. Kunder kan oprette streaming programmer, der bruger den begivenhed Hubs, ud over [Kafka](http://kafka.apache.org/), der allerede findes som en del af knallertmotor. |
| Understøttelse af R Server  | Du kan konfigurere en R Server på HDInsight Spark klynge til at køre fordelt R beregninger med hastigheder bekræftet med en knallertmotor klynge. Yderligere oplysninger finder du se [Introduktion til brug af R Server på HDInsight](hdinsight-hadoop-r-server-get-started.md).   |
| Integration med IntelliJ ide  | Du kan bruge HDInsight Plugin for IntelliJ til at oprette og sende programmer til en HDInsight Spark klynge. Du kan finde flere oplysninger [Brug HDInsight værktøjer plug-in til IntelliJ ide at oprette knallertmotor programmer for HDInsight knallertmotor Linux klynge](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Samtidige forespørgsler              | Knallertmotor i HDInsight understøtter samtidige forespørgsler. Dette gør det muligt for flere forespørgsler fra én bruger eller flere forespørgsler fra forskellige brugere og programmer til at dele de samme klyngeressourcer. |
| Cachelagring på SSDs                 | Du kan vælge at cachedata i hukommelsen eller i SSDs, der er knyttet til klyngenoderne. Cachelagring i hukommelsen giver den bedste ydeevne i forespørgslen, men vil kunne dyr; cachelagring i SSDs giver en god indstilling for at forbedre ydeevnen i forespørgslen uden at skulle oprette en klynge af en størrelse, der skal bruges til at passe ind hele datasættet i hukommelsen.|
| Integration med BI-værktøjer       | Knallertmotor for HDInsight indeholder forbindelser til BI-værktøjer som [Power BI](http://www.powerbi.com/) og [Tableau](http://www.tableau.com/products/desktop) for data analyser.|
| Forudindlæst Anaconda biblioteker        | Knallertmotor klynger på HDInsight følger med Anaconda biblioteker, der allerede er installeret. [Anaconda](http://docs.continuum.io/anaconda/) indeholder tæt 200 biblioteker til machine learning, dataanalyse, visualisering osv.|
| Skalerbarhed                     | Selvom du kan angive antallet af knuder i din klynge under oprettelse, kan du få Forøg eller Formindsk klynge så det svarer til arbejdsbelastningen. Alle HDInsight klynger gør det muligt at ændre antallet af knuder i klyngen. Knallertmotor klynger kan også bliver ignoreret uden tab af data, da alle dataene er gemt i Azure Blob-lager. |
| Understøttelse af 24/7                    | Knallertmotor på HDInsight leveres med enterprise-niveau 24/7 support og en SERVICENIVEAUAFTALE 99,9% op-tid.|



## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Hvad er use cases for gnister på HDInsight?

Apache knallertmotor i HDInsight gør det muligt for følgende vigtige scenarier.

### <a name="interactive-data-analysis-and-bi"></a>Interaktive dataanalyse og BI

[Se på et selvstudium](hdinsight-apache-spark-use-bi-tools.md)

Apache knallertmotor i HDInsight gemmes data i Azure BLOB. Forretningsspecialister og beslutningstagere kan analysere og oprette rapporter over dataene og bruge Microsoft Power BI til at oprette interaktive rapporter fra analyserede dataene. Analytikere kan starte fra ustrukturerede/semi strukturerede data i Azure-lager, definere et skema for data med notesbøger og derefter bygge datamodeller ved hjælp af Microsoft Power BI. Knallertmotor i HDInsight understøtter også en række fra tredjepart BI-værktøjer som Tableau, Qlikview og SAP Lumira gør det en ideel platform til data analytikere, forretningsspecialister og beslutningstagere.

### <a name="iterative-machine-learning"></a>Gentaget Machine Learning

[Se på et selvstudium: forudsige opbygning temperaturer uisng VVS-data](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Se på et selvstudium: forudsige mad undersøgelsesresultaterne](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Apache knallertmotor leveres med [MLlib](http://spark.apache.org/mllib/), en machine learning-bibliotek, der er bygget oven på gnister. Ud over dette skal indeholder knallertmotor på HDInsight også Anaconda, en Python fordeling med en række forskellige pakker til machine learning. Lade dette med en indbygget understøttelse af Jupyter notesbøger, og du har en øverste linje miljø til oprettelse af machine learning programmer.  

### <a name="streaming-and-real-time-data-analysis"></a>Streaming og realtid dataanalyse

[Se på et selvstudium](hdinsight-apache-spark-eventhub-streaming.md)

Realtidsdata analyse bruges til scenarier, lige fra reduceres tid til indsigt data med behandling af data, som den lander, til at lave en SAND streaming løsning. Knallertmotor i HDInsight tilbyder en omfattende understøttelse af opbygning af realtid analytics løsninger. Selvom knallertmotor ikke allerede har forbindelser til indtager data fra mange kilder som Kafka, Flume, Twitter, ZeroMQ eller TCP sockets, tilføjer knallertmotor i HDInsight førsteklasses understøttelse af ingesting data fra Azure begivenhed Hubs. Begivenhed hubber er den mest udbredte kø service på Azure. Har du en out box support for begivenhed Hubs gør sætte gang i HDInsight en ideel platform opbygning af realtid analytics pipeline.

##<a name="next-steps"></a>Hvilke komponenter er inkluderet som en del af en knallertmotor klynge?

Knallertmotor i HDInsight omfatter følgende komponenter, der er tilgængelige på klynger som standard.

- [Knallertmotor Core](https://spark.apache.org/docs/1.5.1/). Omfatter knallertmotor Core, gnister SQL, gnister streaming API'er, GraphX og MLlib.
- [Anaconda](http://docs.continuum.io/anaconda/)
- [Livius](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Jupyter notesbog](https://jupyter.org)

Knallertmotor i HDInsight giver også en [ODBC-driver](http://go.microsoft.com/fwlink/?LinkId=616229) for forbindelse til knallertmotor klynger i HDInsight fra BI-værktøjer som Microsoft Power BI og Tableau.

## <a name="where-do-i-start"></a>Hvor kommer jeg i gang?

Start med at oprette en knallertmotor klynge på HDInsight Linux. Se [Hurtig start: oprette en knallertmotor klynge på HDInsight Linux og køre eksempelprogrammer, der bruger Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Næste trin

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

* [Bruge eksterne pakker med Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installere Jupyter på din computer og oprette forbindelse til en HDInsight Spark klynge](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
