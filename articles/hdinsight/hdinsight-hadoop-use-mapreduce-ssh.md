<properties
   pageTitle="MapReduce og SSH forbindelse med Hadoop i HDInsight | Microsoft Azure"
   description="Lær at bruge SSH til at køre MapReduce job ved hjælp af Hadoop på HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Bruge MapReduce med Hadoop på HDInsight med SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

I denne artikel lærer du at bruge Secure Shell (SSH) til at oprette forbindelse til et Hadoop på HDInsight klynge og derefter sende MapReduce job ved hjælp af Hadoop-kommandoer.

> [AZURE.NOTE] Hvis du allerede er vant til at bruge Linux-baserede Hadoop-servere, men du er ny bruger af HDInsight skal du se [Linux-baserede HDInsight tip](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* En Linux-baserede HDInsight (Hadoop på HDInsight) klynge

* En SSH-klient. Linux, Unix og Mac-operativsystemer skal leveres med en SSH-klient. Windows-brugere skal hente en klient, som [trykfarver](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Oprette forbindelse til SSH

Oprette forbindelse til det fulde domænenavn (fulde Domænenavn) af din HDInsight klynge ved hjælp af kommandoen SSH. Det fulde Domænenavn er det navn, du har givet klynge, efterfulgt af **. azurehdinsight.net**. For eksempel følgende vil oprette forbindelse til en klynge med navnet **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Hvis du har angivet en certifikatnøgle til SSH godkendelse** , da du oprettede HDInsight klynge, du muligvis angive placeringen af den private nøgle på klientsystemet, f.eks.:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Hvis du har angivet en adgangskode til godkendelse af SSH** , da du oprettede HDInsight klynge, skal du angive adgangskoden, når du bliver bedt om.

Finde flere oplysninger om brug af SSH med HDInsight, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X, og Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-clients"></a>Trykfarver (Windows-klienter)

Windows indeholder ikke en indbygget SSH-klient. Det anbefales at bruge **trykfarver**, som kan hentes fra [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Finde flere oplysninger om brug af trykfarver, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Brug Hadoop-kommandoer

1. Når du har forbindelse til HDInsight klyngen, kan du bruge kommandoen følgende **Hadoop** til at starte et job MapReduce:

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

    Dette starter klassen **wordcount** , der er indeholdt i filen **hadoop-mapreduce-examples.jar** . Som input, den anvender **wasbs://example/data/gutenberg/davinci.txt** dokumentet, og output er gemt på **wasbs: / / / eksempel/data/WordCountOutput**.

    > [AZURE.NOTE] Du kan finde flere oplysninger om dette MapReduce job og eksempeldataene, [Brug MapReduce i Hadoop på HDInsight](hdinsight-use-mapreduce.md).

2. Jobbet udsender detaljer, som det behandler, og den oplysning stil med følgende når jobbet er fuldført:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Når jobbet er fuldført, kan du bruge følgende kommando til at få vist de outputfiler, der er gemt på **wasbs://example/data/WordCountOutput**:

        hdfs dfs -ls wasbs:///example/data/WordCountOutput

    Dette bør vise to filer, **_SUCCESS** og **del-r-00000**. **Del-r-00000** -filen indeholder output til dette job.

    > [AZURE.NOTE] Nogle MapReduce job du kan opdele resultaterne på tværs af flere filer er **en del-r-###** . Hvis det er tilfældet, skal du bruge den ### suffiks til at angive rækkefølgen af filerne.

4. Brug følgende kommando for at få vist output:

        hdfs dfs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Dette viser en liste over de ord, der er indeholdt i filen **wasbs://example/data/gutenberg/davinci.txt** og antallet gange hvert ord opstod. Følgende er et eksempel på de data, der findes i filen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Oversigt

Som du kan se, er Hadoop-kommandoer en nem måde at køre MapReduce job i en HDInsight klynge og derefter få vist jobbet output.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om MapReduce sager i HDInsight:

* [Bruge MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)
