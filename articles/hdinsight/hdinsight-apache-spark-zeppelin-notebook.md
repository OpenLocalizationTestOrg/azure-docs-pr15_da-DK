<properties 
    pageTitle="Bruge Zeppelin notesbøger med knallertmotor klynge på HDInsight Linux | Microsoft Azure" 
    description="Trinvis vejledning til, hvordan du bruger Zeppelin notesbøger med knallertmotor klynger på HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Bruge Zeppelin notesbøger med Apache knallertmotor klynge på HDInsight Linux

HDInsight Spark klynger omfatter Zeppelin notesbøger, som du kan bruge til at køre knallertmotor job. I denne artikel kan lære du at bruge Zeppelin notesbog på en HDInsight klynge.


**Forudsætninger for:**

* Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* En Apache knallertmotor klynge. Flere oplysninger under [oprette Apache knallertmotor klynger i Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Start en Zeppelin notesbog

1. Klik på **Klynge Dashboard**fra bladet knallertmotor klynge, og klik derefter på **Zeppelin notesbog**. Hvis du bliver bedt om det, kan du angive Administratoroplysninger for-klyngen.

    > [AZURE.NOTE] Du kan også når Zeppelin notesbogen til din klynge ved at åbne følgende URL-adressen i din browser. Erstat __CLUSTERNAME__ med navnet på din klynge:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Oprette en ny notesbog. Klik på **notesbog**fra ruden sidehoved, og klik derefter på **Opret ny Note**.

    ![Opret en ny Zeppelin notesbog] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Opret en ny Zeppelin notesbog")

    Angiv et navn til notesbogen, og klik derefter på **Opret Note**.

3. Du skal også kontrollere overskriften notesbog viser en forbundne status. Det er markeret med den grønne prik i øverste højre hjørne.

    ![Zeppelin notesbog status] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "Zeppelin notesbog status")

4. Indlæse eksempeldata i en midlertidig tabel. Når du opretter en knallertmotor klynge i HDInsight, kopieres data eksempelfilen, **hvac.csv**, til den tilknyttede lagerplads konto under **\HdiSamples\SensorSampleData\hvac**.

    Indsæt følgende kodestykke i det tomme afsnit, der er oprettet som standard i den nye notesbog.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Tryk på **SKIFT + ENTER** , eller klik på knappen **Afspil** for afsnittet for at køre kodestykket. Status på det højre hjørne af afsnittet skal fremskridt fra klar AFVENTER kører til færdig. Output vises nederst i det samme afsnit. Skærmbilledet ser ud som følger:

    ![Opret en midlertidig tabel fra rækkedata] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Opret en midlertidig tabel fra rækkedata")

    Du kan også angive en titel for hvert afsnit. Klik på ikonet **Indstillinger** fra den højre hjørne, og klik derefter på **Vis titel**.

5. Nu kan du køre knallertmotor SQL-sætninger på **VVS** -tabellen. Indsæt følgende forespørgsel i et nyt afsnit. Forespørgslen henter dokumentkomponent-ID og forskellen mellem mål og faktisk temperaturer for hver bygger på en bestemt dato. Tryk på **SKIFT + ENTER**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    **% Sql** -sætningen i starten fortæller notesbog til brug Livius Scala fortolker.

    Følgende skærmbillede viser output.

    ![Køre en knallertmotor SQL-sætning, ved hjælp af notesbogen] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Køre en knallertmotor SQL-sætning, ved hjælp af notesbogen")

     Klik på Indstillinger for visning (fremhævet i rektangel) for at skifte mellem forskellige repræsentationer for den samme output. Klik på **Indstillinger** for at vælge, hvilke consitutes nøgle og værdier i outputtet. Skærmbilledet over bruger **buildingID** som nøglen og gennemsnittet af **temp_diff** denne værdi.

    
6. Du kan også køre knallertmotor SQL-sætninger ved hjælp af variabler i forespørgslen. Den næste kodestykke viser, hvordan du definerer en variabel, **Temp**i forespørgslen med de mulige værdier, du vil forespørge med. Første gang du kører forespørgslen, udfyldes automatisk en rulleliste med de værdier, du har angivet for variablen.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Indsæt denne kodestykke i et nyt afsnit, og tryk på **SKIFT + ENTER**. Følgende skærmbillede viser output.

    ![Køre en knallertmotor SQL-sætning, ved hjælp af notesbogen] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Køre en knallertmotor SQL-sætning, ved hjælp af notesbogen")

    Du kan vælge en ny værdi fra rullelisten efterfølgende forespørgsler og kør forespørgslen igen. Klik på **Indstillinger** for at vælge, hvilke consitutes nøgle og værdier i outputtet. Skærmbilledet ovenfor bruger **buildingID** som tasten gennemsnittet af **temp_diff** som værdien og **targettemp** som gruppen.

7. Genstart Livius fortolker for at afslutte programmet. Åbne fortolker indstillinger ved at klikke på den gemte brugernavn i øverste højre hjørne for at gøre det, og klik derefter på **fortolker**.

    ![Start fortolker] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive output")

2. Rul til Livius fortolker indstillinger, og klik derefter på **Genstart**.

    ![Genstart Livius intepreter] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Genstart Zeppelin intepreter")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Hvordan bruger jeg eksterne pakker med notesbogen?

Du kan konfigurere Zeppelin notesbogen i Apache knallertmotor klynge på HDInsight (Linux) for at bruge eksterne, community-bidraget pakker, der ikke er inkluderet ud for feltet-box-i klyngen. Du kan søge [Maven lager](http://search.maven.org/) til den komplette liste over pakker, der er tilgængelige. Du kan også finde en liste over tilgængelige pakker fra andre kilder. For eksempel er en komplet liste over bidraget til community-pakker tilgængelig på [Knallertmotor pakker](http://spark-packages.org/).

I denne artikel skal du se, hvordan du bruger [knallertmotor-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) -pakke med Jupyter notesbogen.

1. Åbn indstillinger for fortolker. Klik på den gemte i brugernavn i øverste højre hjørne, og klik derefter på **fortolker**.

    ![Start fortolker] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive output")

2. Rul til Livius fortolker indstillinger, og klik derefter på **Rediger**.

    ![Skift fortolker indstillinger] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Skift fortolker indstillinger")

3. Tilføje en ny nøgle, kaldes **livy.spark.jars.packages** og angive dens værdi i formatet `group:id:version`. Så, hvis du vil bruge [knallertmotor-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) -pakke, skal du angive værdien af nøglen til `com.databricks:spark-csv_2.10:1.4.0`.

    ![Skift fortolker indstillinger] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Skift fortolker indstillinger")

    Klik på **Gem** , og genstart derefter Livius fortolker.

4. **Tip**: Hvis du vil forstå, hvordan du kommer til værdien af tasten angivet ovenfor, her kan du se hvordan.

    en. Find pakken i Maven lager. I dette selvstudium brugte vi [knallertmotor-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. Indsaml værdierne fra lageret til **gruppe-id**, **ArtifactId**og **Version**.

    ![Brug eksterne pakker med Jupyter notesbog] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Brug eksterne pakker med Jupyter notesbog")

    c. Sammenkæde de tre værdier, adskilt af et kolon (**:**).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Hvor gemmes Zeppelin notesbøger?

Zeppelin notesbøgerne gemmes i klynge headnodes. Så hvis du sletter klyngen, slettes notesbøgerne samt. Hvis du vil bevare dine notesbøger til senere brug på andre klynger, skal du eksportere dem, når du er færdig, kører job. Klik på ikonet **Eksporter** for at eksportere en notesbog, som vist i billedet nedenfor.

![Downloade notesbogen] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Downloade notesbogen")

Dette gemmer notesbogen som en JSON-fil i din downloadplaceringen.

## <a name="livy-session-management"></a>Livius sessionsstyring

Når du kører den første kode afsnit i notesbogen Zeppelin, oprettes en ny Livius session i din HDInsight Spark klynge. Denne session, der deles på tværs af alle Zeppelin notesbøger, som du opretter efterfølgende. Hvis en eller anden grund Livius session er blevet slået ned (klynge genstart osv.), kan du kan ikke køre job fra Zeppelin notesbog.

I dette tilfælde skal du udføre følgende trin, før du kan begynde at køre job fra en Zeppelin notesbog. 

1. Genstart Livius fortolker fra Zeppelin notesbog. Åbne fortolker indstillinger ved at klikke på den gemte brugernavn i øverste højre hjørne for at gøre det, og klik derefter på **fortolker**.

    ![Start fortolker] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Hive output")

2. Rul til Livius fortolker indstillinger, og klik derefter på **Genstart**.

    ![Genstart Livius intepreter] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Genstart Zeppelin intepreter")

3. Køre en kode celle fra en eksisterende Zeppelin notesbog. Dette opretter en ny Livius session i HDInsight klynge.

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

* [Kerner, der er tilgængelige for Jupyter notesbog i knallertmotor klynge i HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Bruge eksterne pakker med Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installere Jupyter på din computer og oprette forbindelse til en HDInsight Spark klynge](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







