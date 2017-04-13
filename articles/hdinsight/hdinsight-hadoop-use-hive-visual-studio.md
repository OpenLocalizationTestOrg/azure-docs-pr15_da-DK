<properties
   pageTitle="Hive forespørgsel med Hadoop-værktøjer til Visual Studio | Microsoft Azure"
   description="Lær at bruge Hive med Hadoop i HDInsight ved hjælp af Visual Studio Hadoop værktøjer."
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

#<a name="run-hive-queries-using-the-hdinsight-tools-for-visual-studio"></a>Køre Hive forespørgsler ved hjælp af værktøjerne HDInsight til Visual Studio

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

I denne artikel kan lære du, hvordan du sender Hive forespørgsler til en HDInsight klynge ved hjælp af værktøjerne HDInsight til Visual Studio.

> [AZURE.NOTE] Dette dokument indeholder ikke en detaljeret beskrivelse af, hvad de HiveQL sætninger, der bruges i eksemplerne gøre. Finde oplysninger om den HiveQL, der bruges i dette eksempel, [Brug Hive med Hadoop på HDInsight](hdinsight-use-hive.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende.

* En Azure HDInsight (Hadoop på HDInsight) klynge (Linux eller Windows-baseret)

* Visual Studio (én af følgende versioner):

    Visual Studio 2013 Community/Professional/Premium/Ultimate med [opdatere 4](https://www.microsoft.com/download/details.aspx?id=44921)

    Visual Studio 2015 (Community/Enterprise)

- HDInsight værktøjer til Visual studio. Se [Introduktion til brug af Visual Studio Hadoop værktøjer til HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) for oplysninger om installation og konfiguration af værktøjerne.

##<a id="run"></a>Køre Hive forespørgsler ved hjælp af værktøjerne HDInsight til Visual Studio

1. Åbn **Visual Studio** , og vælg **Ny** > **Project** > **HDInsight** > **Hive programmet**. Angiv et navn for dette projekt.

2. Åbn filen **Script.hql** , der er oprettet med dette projekt, og sæt ind i de følgende HiveQL sætninger:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Disse sætninger udføre følgende handlinger:

    * **Slip tabel**: sletter tabellen og datafilen, hvis der allerede findes i tabellen.
    * **Opret ekstern tabel**: opretter en ny 'ekstern' tabel i Hive. Eksterne tabeller indeholder kun tabeldefinitionen i Hive (data, der er tilbage i den oprindelige placering).

        > [AZURE.NOTE] Eksterne tabeller skal bruges, når du forventer, at de underliggende data skal opdateres af en ekstern kilde (som et automatiseret data overførslen) eller af en anden MapReduce handling, men du vil altid Hive forespørgsler til at bruge de nyeste data.
        >
        > Slippe en ekstern tabel betyder **ikke** slette dataene, kun tabeldefinition.

    * **Række FORMAT**: fortæller Hive hvordan dataene skal formateres. I dette tilfælde er felterne i hver log adskilt af et mellemrum.
    * **GEMT AS TEXTFILE placering**: fortæller Hive hvor dataene er gemt (mappen eksempeldataene /), og det er gemt som tekst.
    * **Vælg**: Vælg en optælling af alle rækker, hvor kolonne **t4** indeholder værdien **[ERROR]**. Dette skal returnere en værdi på **3** , fordi der er tre rækker, der indeholder denne værdi.
    * **INPUT__FILE__NAME som '%.log'** - fortæller Hive, som vi bør kun returnerer data fra filer, der slutter i. log. Dette begrænser søgningen til filen sample.log, der indeholder dataene, og beholder den returnerer data fra andre eksempel-datafiler, der ikke matcher skemaet vi definerede.

3. Vælg **HDInsight klynge** , du vil bruge til denne forespørgsel fra værktøjslinjen, og vælg derefter **Send for at WebHCat** for at køre sætningerne som et Hive job fra WebHCat. Du kan også sende jobbet ved hjælp af knappen __Udfør via HiveServer2__ , hvis HiveServer2 er tilgængelig på din klynge version. **Hive Job oversigt** vises og viser oplysninger om det, der kørende job. Brug linket **Opdater** til at opdatere oplysningerne om jobbet, indtil **Jobstatus** ændres til **fuldført**.

4. Brug linket **Job Output** til at se output for dette job. Der skal vises `[ERROR] 3`, som er den værdi, der returneres af SELECT-sætningen.

5. Du kan også køre Hive forespørgsler uden at oprette et projekt. Brug af **Server Explorer**, udvide **Azure** > **HDInsight**, skal du højreklikke på din HDInsight-server, og vælg derefter **skrive en Hive forespørgsel**.

6. Tilføj følgende HiveQL sætninger i dokumentets **temp.hql** , der vises:

        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Disse sætninger udføre følgende handlinger:

    * **Oprette tabel Hvis ikke findes**: opretter en tabel, hvis den ikke allerede findes. Da nøgleordet **eksterne** ikke bruges, er det en intern tabel, som er gemt i et Hive datalager og administreres helt af Hive.

        > [AZURE.NOTE] I modsætning til **eksterne** tabeller slette slippe en intern tabel også de underliggende data.

    * **GEMT AS ORC**: gemmer dataene i optimerede række kolonne (ORC)-format. Dette er et meget optimerede og effektivt format til lagring af Hive data.
    * **Indsæt OVERSKRIV... Vælg**: vælger rækker fra tabellen **log4jLogs** , der indeholder **[ERROR]**, indsætter derefter dataene i tabellen **errorLogs** .

7. Vælg **Send** for at køre jobbet fra værktøjslinjen. Brug **Jobstatus for** at finde ud af, at jobbet er fuldført.

8. Brug **Server Stifinder** for at bekræfte, at jobbet fuldførte og har oprettet en ny tabel, og udvid **Azure** > **HDInsight** > din HDInsight klynge > **Hive databaser** > og **standard**. Du bør se tabellen **errorLogs** og tabellen **log4jLogs** .

##<a id="summary"></a>Oversigt

Som du kan se den HDInsight-værktøjer til Visual Studio giver en nem måde at køre Hive forespørgsler på en HDInsight klynge, overvåge jobstatus og hente output.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om Hive i HDInsight:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Yderligere oplysninger om HDInsight værktøjerne til Visual Studio:

* [Introduktion til HDInsight værktøjer til Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)


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



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
