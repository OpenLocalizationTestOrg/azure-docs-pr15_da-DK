<properties
   pageTitle="Brug af Hive shell i HDInsight (Hadoop) | Microsoft Azure"
   description="Lær at bruge Hive shell med en Linux-baserede HDInsight klynge. Du vil lære at oprette forbindelse til den HDInsight klynge, ved hjælp af SSh og derefter bruge Hive Shell til at køre interaktivt forespørgsler."
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
   ms.date="10/04/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-in-hdinsight-with-ssh"></a>Bruge Hive med Hadoop i HDInsight med SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

I denne artikel kan lære du at bruge Secure Shell (SSH) til at oprette forbindelse til et Hadoop på Azure HDInsight klynge og Send derefter interaktivt Hive forespørgsler ved hjælp af kommandolinjen Hive (CLI).

> [AZURE.IMPORTANT] Mens kommandoen Hive er tilgængelig på Linux-baserede HDInsight klynger, bør du overveje at bruge Beeline. Beeline er en nyere klient til at arbejde med Hive og er inkluderet i din HDInsight klynge. Du kan finde flere oplysninger om brug af dette, kan du se [Brug Hive med Hadoop i HDInsight med Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* Et Linux-baserede Hadoop på HDInsight klynge.

* En SSH-klient. Linux, Unix og Mac OS skal leveres med en SSH-klient. Windows-brugere skal hente en klient, som [trykfarver](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Oprette forbindelse til SSH

Oprette forbindelse til det fulde domænenavn (fulde Domænenavn) af din HDInsight klynge ved hjælp af kommandoen SSH. Det fulde Domænenavn er det navn, du har givet klynge, derefter **. azurehdinsight.net**. For eksempel følgende vil oprette forbindelse til en klynge med navnet **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Hvis du har angivet en certifikatnøgle til SSH godkendelse** , da du oprettede HDInsight klynge, du muligvis angive placeringen af den private nøgle på dit klientsystem:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Hvis du har angivet en adgangskode til godkendelse af SSH** , da du oprettede HDInsight klynge, skal du angive adgangskoden, når du bliver bedt om.

Finde flere oplysninger om brug af SSH med HDInsight, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Linux, OS X, og Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###<a name="putty-windows-based-clients"></a>Trykfarver (Windows-baserede klienter)

Windows indeholder ikke en indbygget SSH-klient. Det anbefales at bruge **trykfarver**, som kan hentes fra [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Finde flere oplysninger om brug af trykfarver, [Brug SSH med Linux-baserede Hadoop på HDInsight fra Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Bruge kommandoen Hive

2. Når forbindelse, kan du begynde på Hive CLI ved hjælp af følgende kommando:

        hive

3. Angiv følgende udtalelser for at oprette en ny tabel med navnet **log4jLogs** ved hjælp af eksempeldataene ved at bruge CLI:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Disse sætninger udføre følgende handlinger:

    * **DROP TABLE** - sletter tabellen og datafilen, i tilfælde af, at tabellen allerede findes.
    * **Opret eksterne tabel** - opretter en ny 'ekstern' tabel i Hive. Eksterne tabeller indeholder kun tabeldefinitionen i Hive. Data, der er tilbage i den oprindelige placering.
    * **Række FORMAT** – som fortæller Hive hvordan dataene skal formateres. I dette tilfælde er felterne i hver log adskilt af et mellemrum.
    * **GEMT AS TEXTFILE placering** - fortæller Hive hvor dataene er gemt (mappen eksempeldataene /), og at det er gemt som tekst.
    * **Vælg** - markerer en optælling af alle rækker, hvor kolonne **t4** indeholder værdien **[ERROR]**. Dette skal returnere en værdi på **3** , når der er tre rækker, der indeholder denne værdi.
    * **INPUT__FILE__NAME som '%.log'** - fortæller Hive, som vi bør kun returnerer data fra filer, der slutter i. log. Dette begrænser søgningen til filen sample.log, der indeholder dataene, og beholder den returnerer data fra andre eksempel-datafiler, der ikke matcher skemaet vi definerede.

    > [AZURE.NOTE] Eksterne tabeller skal bruges, når du forventer, at de underliggende data opdateres ved en ekstern kilde, som et automatiseret data overførslen eller af en anden MapReduce handling, men vil altid Hive forespørgsler til at bruge de nyeste data.
    >
    > Slippe en ekstern tabel betyder **ikke** slette dataene, kun tabeldefinition.

4. Brug følgende udtalelser til at oprette en ny 'interne' tabel med navnet **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Disse sætninger udføre følgende handlinger:

    * **Oprette tabel Hvis ikke findes** - opretter en tabel, hvis den ikke allerede findes. Da nøgleordet **eksterne** ikke bruges, er dette en intern tabel, som er gemt i et Hive datalager og administreres helt af Hive.
    * **GEMT AS ORC** - gemmer dataene i optimeret række kolonne (ORC)-format. Dette er et meget optimerede og effektivt format til lagring af Hive data.
    * **Indsæt OVERSKRIV... Vælg** - markerer rækker fra tabellen **log4jLogs** , der indeholder **[ERROR]**og derefter indsætter dataene i tabellen **errorLogs** .

    For at bekræfte, at kun de rækker, der indeholder **[ERROR]** i kolonnen t4 blev gemt til tabellen **errorLogs** , skal du bruge følgende sætning til at returnere alle rækkerne fra **errorLogs**:

        SELECT * from errorLogs;

    Tre rækker af data skal returneres, alle med **[ERROR]** i kolonnen t4.

    > [AZURE.NOTE] I modsætning til eksterne tabeller slippe en intern tabel, slettes de underliggende data.

##<a id="summary"></a>Oversigt

Som du kan se, indeholder kommandoen Hive en nem måde at interaktivt køre Hive forespørgsler på en HDInsight klynge, overvåge jobstatus og hente output.

##<a id="nextsteps"></a>Næste trin

Generelle oplysninger om Hive i HDInsight:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Hvis du bruger Tez med Hive, skal du se følgende dokumenter til fejlfindingsoplysninger:

* [Brug Tez Brugergrænsefladen på Windows-baseret HDInsight](hdinsight-debug-tez-ui.md)

* [Brug af Ambari Tez visningen på Linux-baserede HDInsight](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

