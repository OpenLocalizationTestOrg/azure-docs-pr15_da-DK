<properties
    pageTitle="Sende knallertmotor job ved hjælp af Livius | Microsoft Azure"
    description="Lær, hvordan du bruger Livius med HDInsight klynger til at sende knallertmotor job fra en fjernplacering."
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


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Sende knallertmotor job fra en fjernplacering til en Apache knallertmotor klynge på HDInsight Linux ved hjælp af Livius

Apache knallertmotor klynge på Azure HDInsight omfatter Livius, en RESTEN grænseflade til afsendelse af job fra en fjernplacering til en knallertmotor klynge. Du kan finde detaljerede dokumentation [Livius](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Du kan bruge Livius at køre interaktive knallertmotor er kommet eller sende batchen opgaverne skal udføres på gnister. I denne artikel taler om brug af Livius til at sende batchjob. Syntaksen nedenfor bruger krøllet til RESTEN opkald til Livius slutpunkt.

**Forudsætninger for:**

Du skal have følgende:

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- En Apache knallertmotor klynge på HDInsight Linux. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Sende en kørslen af klynge

Før du sender en kørslen, skal du overføre programmet glas på det klynge lager, der er knyttet til klyngen. Du kan bruge [**AzCopy**](../storage/storage-use-azcopy.md), en kommandolinjen til at gøre dette. Der findes mange andre klienter, du kan bruge til at overføre data. Du kan finde flere oplysninger om dem på [overføre data til Hadoop-job i HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Eksempler**:

* Hvis filen glas på klynge lagerplads (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Hvis du vil videregive glas filnavnet og klassenavn som en del af en input-fil (i dette eksempel input.txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Få oplysninger om navne, der kører på klyngen

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Eksempler**:

* Hvis du vil hente alle de navne, der kører på klyngen:

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Hvis du vil hente en bestemt gruppe med en given batchId

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Slette en kørsel

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Eksempel**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Livius og høj tilgængelighed

Livius giver høj tilgængelighed til knallertmotor job, der kører på klyngen. Her er nogle eksempler på.

* Hvis tjenesten Livius går ned, når du har sendt et job fra en fjernplacering til en knallertmotor klynge, fortsætter jobbet til at køre i baggrunden. Når Livius er op, gendanner status for den sag og rapporter, der er det i igen.

* Jupyter notesbøger til HDInsight er udstyret med Livius i backend-version. Hvis en notesbog, der kører et knallertmotor job og tjenesten Livius bliver genstartet, fortsætter notesbogen til at køre kode celler. 

## <a name="show-me-an-example"></a>Vis et eksempel

I dette afsnit skal se vi på nogle eksempler om, hvordan du bruger Livius til at sende et styret program, overvåge forløbet af programmet og derefter slette jobbet. Programmet vi bruge i dette eksempel er det et udviklet i artiklen [oprette en enkeltstående Scala program og ved at køre på HDInsight knallertmotor klynge](hdinsight-apache-spark-create-standalone-application.md). Følgende fremgangsmåde forudsætter følgende:

* Du har allerede kopieret over programmet glas til kontoen lagerplads, der er knyttet til klyngen.
* Du har krøllet installeret på den computer, hvor du prøver disse trin.

Udfør følgende trin.

1. Lad os først bekræfte, at Livius kører på klyngen. Vi kan gøre det ved at få en liste over kører batches. Hvis dette er første gang du kører et job fra Livius, skal det returnere nul.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Du skal have en output ligner følgende:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Bemærk, hvordan den sidste linje i outputtet står **total: 0**, som foreslår ikke kører batches.

2. Lad os nu sende en kørsel. Den nedenstående kodestykke anvender en input fil (input.txt) til at videregive glas navnet og klassenavnet som parametre. Dette er den anbefalede fremgangsmåde, hvis du kører disse trin fra en Windows-computer.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Parametre i filen **input.txt** defineres således:

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Du burde se output ligner følgende:

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Bemærk, hvordan den sidste linje i output står **tilstand: starte**. Der også står, **id: 0**. Dette er batch-ID.

3. Nu kan du hente den status for denne specifikke batch via batchen-ID.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Du burde se output ligner følgende:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Output viser nu **tilstand: succes**, som foreslår, jobbet blev gennemført.

4. Hvis du vil, kan du nu slette batchen.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Du burde se output ligner følgende:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Den sidste linje i output viser batchen blev slettet. Hvis du sletter et job, mens den kører, bliver det grundlæggende afbryde jobbet. Hvis du sletter et job, der er afsluttet, korrekt eller på anden måde, sletter den oplysningerne om jobbet helt.

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
