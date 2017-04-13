<properties
   pageTitle="MapReduce og Fjernskrivebord med Hadoop i HDInsight | Microsoft Azure"
   description="Lær at bruge Fjernskrivebord til at oprette forbindelse til Hadoop på HDInsight og køre MapReduce job."
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
   ms.date="09/27/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Brug MapReduce i Hadoop på HDInsight med Fjernskrivebord

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

I denne artikel kan lære du at oprette forbindelse til et Hadoop på HDInsight klynge ved hjælp af Remote Desktop og derefter køre MapReduce job ved hjælp af kommandoen Hadoop.

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* En Windows-baseret HDInsight (Hadoop på HDInsight) klynge

* En klientcomputer, der kører Windows 10, Windows 8 eller Windows 7

##<a id="connect"></a>Oprette forbindelse til Fjernskrivebord

Aktivere Fjernskrivebord for HDInsight-klyngen, og derefter oprette forbindelse til den ved at følge vejledningen på [Opret forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Bruge kommandoen Hadoop

Når du har forbindelse til skrivebordet for HDInsight-klyngen, kan du bruge følgende trin til at køre en MapReduce sag ved hjælp af kommandoen Hadoop:

1. Starte **Hadoop kommandolinjen**fra skrivebordet HDInsight. Dette åbner en ny kommandoprompt i den **c:\apps\dist\hadoop-&lt;versionsnummeret >** directory.

    > [AZURE.NOTE] Versionsnummeret ændres, når Hadoop opdateres. Miljøvariablen **HADOOP_HOME** kan bruges til at finde sti. For eksempel `cd %HADOOP_HOME%` ændrer kataloger til Hadoop-mappen uden at du skal vide versionsnummeret.

2. For at bruge kommandoen **Hadoop** til at køre en eksempel MapReduce job skal du bruge følgende kommando:

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Dette starter klassen **wordcount** , der er indeholdt i filen **hadoop-mapreduce-examples.jar** i den aktuelle mappe. Som input, den anvender **wasbs://example/data/gutenberg/davinci.txt** dokumentet, og output er gemt på: **wasbs: / / / eksempel/data/WordCountOutput**.

    > [AZURE.NOTE] Du kan finde flere oplysninger om dette MapReduce job og eksempeldataene, <a href="hdinsight-use-mapreduce.md">Brug MapReduce i Hadoop-HDInsight</a>.

2. Jobbet udsender detaljer, som behandles, og den oplysning stil med følgende når jobbet er fuldført:

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Når jobbet er fuldført, skal du bruge følgende kommando til at få vist de outputfiler, der er gemt på **wasbs://example/data/WordCountOutput**:

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Dette bør vise to filer, **_SUCCESS** og **del-r-00000**. **Del-r-00000** -filen indeholder output til dette job.

    > [AZURE.NOTE] Nogle MapReduce job du kan opdele resultaterne på tværs af flere filer er **en del-r-###** . Hvis det er tilfældet, skal du bruge den ### suffiks til at angive rækkefølgen af filerne.

4. Brug følgende kommando for at få vist output:

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Dette viser en liste over de ord, der er indeholdt i filen **wasbs://example/data/gutenberg/davinci.txt** sammen med antallet gange hvert ord opstod. Følgende er et eksempel på de data, der findes i filen:

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

##<a id="summary"></a>Oversigt

Som du kan se, indeholder kommandoen Hadoop en nem måde at køre MapReduce job på en HDInsight klynge og derefter få vist jobbet output.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om MapReduce sager i HDInsight:

* [Bruge MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)
