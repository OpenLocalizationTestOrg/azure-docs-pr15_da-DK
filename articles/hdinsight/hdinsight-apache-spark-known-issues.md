<properties 
    pageTitle="Kendte problemer i Apache knallertmotor i HDInsight | Microsoft Azure" 
    description="Kendte problemer i Apache knallertmotor i HDInsight." 
    services="hdinsight" 
    documentationCenter="" 
    authors="mumian" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="nitinme"/>

# <a name="known-issues-for-apache-spark-cluster-on-hdinsight-linux"></a>Kendte problemer for Apache knallertmotor klynge på HDInsight Linux

Dette dokument holder styr på alle de kendte problemer med HDInsight Spark public preview.  

##<a name="livy-leaks-interactive-session"></a>Livius mister interaktiv session
 
Når Livius genstartes med en interaktiv session (fra Ambari eller på grund af headnode 0 virtuelt genstart) stadig lever, lækkes en interaktiv job session. På grund af dette, nye job kan sidder fast i tilstanden accepterede og kan ikke startes.

**Afhjælpning:**

Benyt følgende fremgangsmåde kan løse problemet:

1. SSH til headnode. 
2. Kør følgende kommando for at finde programmet id'erne for de interaktive job gennem Livius i gang. 

        yarn application –list

    Standard jobbet navne bliver Livius, hvis job er i gang med en Livius interaktive session uden eksplicitte navne, der er angivet, til Livius session i gang ved at Jupyter notesbog, jobbet navnet begynder med remotesparkmagics_ *. 

3. Kør følgende kommando for at afbryde disse job. 

        yarn application –kill <Application ID>

Nyt job starter kører. 

##<a name="spark-history-server-not-started"></a>Knallertmotor historik Server ikke startet 

Knallertmotor historik Server er ikke startet automatisk, når der oprettes en klynge.  

**Afhjælpning:** 

Starte manuelt historik serveren fra Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Tilladelse problem i biblioteket over knallertmotor logfiler 

Når hdiuser sender en sag med spark-submit, der er en fejl java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (adgang nægtet) og driver loggen er ikke skrevet. 

**Afhjælpning:**
 
1. Føje hdiuser til gruppen Hadoop. 
2. Giv 777 tilladelser på /var/log/spark efter oprettelse af klynge. 
3. Opdatere knallertmotor logplaceringen ved hjælp af Ambari skal være en mappe med 777 tilladelser.  
4. Kør knallertmotor-sende som sudo.  

## <a name="issues-related-to-jupyter-notebooks"></a>Problemer i forbindelse med Jupyter notesbøger

Følgende er nogle kendte problemer i forbindelse med Jupyter notesbøger.


### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notesbøger med ikke-ASCII-tegn i filnavne

Jupyter notesbøger, som kan bruges i knallertmotor HDInsight klynger må ikke have ikke-ASCII-tegn i filnavne. Hvis du forsøger at overføre en fil via den Jupyter UI som har en ikke-ASCII-filnavn, det går ned (det vil sige, Jupyter ikke gør det muligt at overføre filen, men det ikke Udløs fejlen synlige enten). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fejl under indlæsning af notesbøger af større

Du kan få vist en fejlmeddelelse **`Error loading notebook`** når du indlæser notesbøger, der er større i størrelse.  

**Afhjælpning:**

Hvis du får denne fejl, betyder det ikke dine data er beskadiget eller tabt.  Dine notesbøger er stadig på disken i `/var/lib/jupyter`, og du kan SSH i klynge for at få adgang til dem. Du kan kopiere dine notesbøger fra din klynge til din lokale computer (ved hjælp af SCP eller WinSCP) som en sikkerhedskopi til at undgå tab af alle vigtige data i notesbogen. Du kan derefter SSH tunnel til din headnode på port 8001 at få adgang til Jupyter uden at gå gennem gatewayen.  Derfra kan du rydde output fra din notesbog og Gem den for at minimere størrelsen på notesbogens igen.

Hvis du vil forhindre, at denne fejl sker fremover, skal du udføre nogle af de bedste fremgangsmåder:

* Det er vigtigt at holde notesbog størrelsen lille. Noget output fra din knallertmotor job, der sendes tilbage til Jupyter bevares i notesbogen.  Det er en god fremgangsmåde med Jupyter generelt at undgå at løbe `.collect()` til store RDD eller dataframes; Hvis du vil vise en RDD indholdet, i stedet kører `.take()` eller `.sample()` så din output ikke finde for stor.
* Også, når du gemmer en notesbog, Ryd alle outputformater celler for at reducere størrelsen.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Notesbog første start tager længere tid end forventet 

Første kode sætning i Jupyter notesbog ved hjælp af knallertmotor magisk kan tage mere end et minut.  

**Forklaring:**
 
Dette sker, fordi når den første celle i kode køres. I baggrunden dette starter session konfiguration og gnister, SQL, og Hive sammenhænge, der er angivet. Når disse kontekster angives, den første sætning kører og dette giver indtryk, der tog lang tid at fuldføre sætningen.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter notesbog timeout i at oprette sessionen

Når knallertmotor klynge er uden for ressourcer, der tænding og Pyspark kerner i notesbogen Jupyter timeout forsøg på at oprette sessionen. 

**Afhjælpninger:** 

1. Frigøre nogle ressourcer i din knallertmotor klynge ved at:

    - Forhindrer andre knallertmotor notesbøger ved at gå til menuen Luk og standse eller at klikke på Luk i explorer notesbog.
    - Forhindrer andre knallertmotor programmer fra GARN.

2. Genstart den notesbog, du forsøgte at starte. Tilstrækkelig ressourcer skal være tilgængelige for dig at oprette en session nu.

##<a name="see-also"></a>Se også

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

* [Bruge eksterne pakker med Jupyter notesbøger](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Installere Jupyter på din computer og oprette forbindelse til en HDInsight Spark klynge](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administrere ressourcer

* [Administrere ressourcer for Apache knallertmotor klynge i Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Holde styr på og fejlfinding job, der kører på en Apache knallertmotor klynge i HDInsight](hdinsight-apache-spark-job-debugging.md)
