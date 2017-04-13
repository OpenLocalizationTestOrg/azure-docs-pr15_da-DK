<properties
   pageTitle="Brug Hadoop Hive og Fjernskrivebord i HDInsight | Microsoft Azure"
   description="Lær, hvordan du opretter forbindelse til Hadoop klynge i HDInsight ved hjælp af Fjernskrivebord, og derefter køre Hive forespørgsler ved hjælp af kommandolinjen Hive."
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
   ms.date="09/06/2016"
   ms.author="larryfr"/>

# <a name="use-hive-with-hadoop-on-hdinsight-with-remote-desktop"></a>Bruge Hive med Hadoop på HDInsight med Fjernskrivebord

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

I denne artikel kan du se, hvordan du opretter forbindelse til en HDInsight klynge ved hjælp af Remote Desktop og derefter køre Hive forespørgsler ved hjælp af Hive kommandolinjen (CLI).

> [AZURE.NOTE] Dette dokument indeholder ikke en detaljeret beskrivelse af, hvad de HiveQL sætninger, der bruges i eksemplerne gøre. Finde oplysninger om den HiveQL, der bruges i dette eksempel, [Brug Hive med Hadoop på HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* En Windows-baseret HDInsight (Hadoop på HDInsight) klynge

* En klientcomputer, der kører Windows 10, vinduet 8 eller Windows 7

##<a id="connect"></a>Oprette forbindelse til Fjernskrivebord

Aktivere Fjernskrivebord for HDInsight-klyngen, og derefter oprette forbindelse til den ved at følge vejledningen på [Opret forbindelse til HDInsight klynger ved hjælp af RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hive"></a>Bruge kommandoen Hive

Når du har forbindelse til skrivebordet for HDInsight-klyngen, kan du bruge følgende trin til at arbejde med Hive:

1. Starte **Hadoop kommandolinjen**fra skrivebordet HDInsight.

2. Skriv følgende kommando for at starte den Hive CLI:

        %hive_home%\bin\hive

    Når CLI er startet, får du vist bliver bedt om det Hive CLI: `hive>`.

3. Angiv følgende udtalelser for at oprette en ny tabel med navnet **log4jLogs** med eksempeldata ved at bruge CLI:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Disse sætninger udføre følgende handlinger:

    * **Slip tabel**: sletter tabellen og datafilen, hvis der allerede findes i tabellen.

    * **Opret ekstern tabel**: opretter en ny 'ekstern' tabel i Hive. Eksterne tabeller indeholder kun tabeldefinition i Hive (data, der er tilbage i den oprindelige placering).

        > [AZURE.NOTE] Eksterne tabeller skal bruges, når du forventer, at de underliggende data skal opdateres af en ekstern kilde (som et automatiseret data overførslen) eller af en anden MapReduce handling, men du vil altid Hive forespørgsler til at bruge de nyeste data.
        >
        > Slippe en ekstern tabel betyder **ikke** slette dataene, kun tabeldefinition.

    * **Række FORMAT**: fortæller Hive hvordan dataene skal formateres. I dette tilfælde er felterne i hver log adskilt af et mellemrum.

    * **GEMT AS TEXTFILE placering**: fortæller Hive hvor dataene er gemt (mappen eksempeldataene /), og det er gemt som tekst.

    * **Vælg**: markerer en optælling af alle rækker, hvor kolonne **t4** indeholder værdien **[ERROR]**. Dette skal returnere en værdi på **3** , fordi der er tre rækker, der indeholder denne værdi.

    * **INPUT__FILE__NAME som '%.log'** - fortæller Hive, som vi bør kun returnerer data fra filer, der slutter i. log. Dette begrænser søgningen til filen sample.log, der indeholder dataene, og beholder den returnerer data fra andre eksempel-datafiler, der ikke matcher skemaet vi definerede.


4. Brug følgende udtalelser til at oprette en ny 'interne' tabel med navnet **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Disse sætninger udføre følgende handlinger:

    * **Oprette tabel Hvis ikke findes**: opretter en tabel, hvis den ikke allerede findes. Da nøgleordet **eksterne** ikke bruges, er det en intern tabel, som er gemt i et Hive datalager og administreres helt af Hive.

        > [AZURE.NOTE] I modsætning til **eksterne** tabeller slette slippe en intern tabel også de underliggende data.

    * **GEMT AS ORC**: gemmer dataene i optimerede række kolonne (ORC)-format. Dette er et meget optimerede og effektivt format til lagring af Hive data.

    * **Indsæt OVERSKRIV... Vælg**: vælger rækker fra tabellen **log4jLogs** , der indeholder **[ERROR]**, indsætter derefter dataene i tabellen **errorLogs** .

    For at bekræfte, at kun de rækker, der indeholder **[ERROR]** i kolonnen t4 blev gemt til tabellen **errorLogs** , skal du bruge følgende sætning til at returnere alle rækkerne fra **errorLogs**:

        SELECT * from errorLogs;

    Tre rækker af data skal returneres, alle med **[ERROR]** i kolonnen t4.

##<a id="summary"></a>Oversigt

Som du kan se den kommandoen Hive gør det nemt at interaktivt at køre Hive forespørgsler på en HDInsight klynge, overvåge jobstatus og hente output.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om Hive i HDInsight:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Hvis du bruger Tez med Hive, skal du se følgende dokumenter til fejlfindingsoplysninger:

* [Brug Tez Brugergrænsefladen på Windows-baseret HDInsight](hdinsight-debug-tez-ui.md)

* [Brug af Ambari Tez visningen på Linux-baserede HDInsight](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

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





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

