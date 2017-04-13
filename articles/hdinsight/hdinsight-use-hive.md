<properties
    pageTitle="Få mere at vide, hvad er Hive, og hvordan du bruger HiveQL | Microsoft Azure"
    description="Få mere at vide om Apache Hive og hvordan du kan bruge det sammen med Hadoop i HDInsight. Vælg, hvordan du afspiller Hive tingene og bruge HiveQL til at analysere et eksempel Apache log4j-fil."
    keywords="hiveql, hvad er hive"
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
    ms.date="09/19/2016"
    ms.author="larryfr"/>

# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight-to-analyze-a-sample-apache-log4j-file"></a>Bruge Hive og HiveQL med Hadoop i HDInsight til at analysere et eksempel Apache log4j-fil

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


I dette selvstudium skal du se, hvordan du bruger Apache Hive i Hadoop på HDInsight, og vælg at køre Hive tingene. Du lærer også om HiveQL, og hvordan til at analysere et eksempel Apache log4j-fil.

##<a id="why"></a>Hvad er Hive og hvorfor bruge den?
[Apache Hive](http://hive.apache.org/) er et data warehouse system til Hadoop, som gør det muligt data opsummering, forespørgsler og analyse af data ved hjælp af HiveQL (et forespørgselssprog ligner SQL). Hive kan bruges til interaktivt at udforske dine data eller oprette der kan genbruges batchen behandler job.

Hive kan du projektstruktur på stort set ustrukturerede data. Når du definerer strukturen, kan du bruge Hive for at forespørge på disse data uden kendskab til Java eller MapReduce. **HiveQL** (Hive forespørgselssprog) kan du skrive forespørgsler med sætninger, der ligner T-SQL.

Hive forstår om at arbejde med strukturerede og semistrukturerede data, som tekstfiler, hvor felterne, der er adskilt af bestemte tegn. Hive understøtter også brugerdefinerede **serialiseringsfunktion/deserializers (SerDe)** til komplekse eller uregelmæssigt strukturerede data. Se, [hvordan du bruger en brugerdefineret JSON SerDe med HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx)kan finde flere oplysninger.

## <a name="user-defined-functions-udf"></a>Brugerdefinerede funktioner (UDF)

Hive kan også udvides til **brugerdefinerede funktioner (UDF)**. EN gør det muligt at implementere funktionalitet or -logik, der ikke er nemt udformet i HiveQL. Et eksempel på brug af brugerdefinerede funktioner med Hive, skal du se følgende:

* [Brug en Java bruger defineret funktionen med Hive](hdinsight-hadoop-hive-java-udf.md)

* [Brug af Python med Hive og gris i HDInsight](hdinsight-python.md)

* [Bruge C# med Hive og gris i HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Sådan føjer du en brugerdefineret Hive UDF til HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Eksempel på brugerdefineret Hive UDF konvertere dato/klokkeslætsformater til Hive tidsstempel](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Hive interne tabeller eller-eksterne tabeller

Der er et par ting, du bør vide om Hive intern tabel og ekstern tabel:

- Kommandoen **Opret tabel** opretter en intern tabel. Datafilen skal være placeret i beholderen standard.
- Kommandoen **Opret tabel** flytter datafilen til /hive/warehouse/<TableName> mappe.
- Kommandoen **Opret ekstern tabel** opretter en ekstern tabel. Datafilen kan være placeret uden for objektbeholderen standard.
- Kommandoen **Opret ekstern tabel** flyttes ikke datafilen.
- Kommandoen **Opret ekstern tabel** tillader ikke, at mapperne på PLACERINGEN. Det er grunden til hvorfor selvstudiet opretter en kopi af filen sample.log.

Du kan finde flere oplysninger, se [HDInsight: Hive interne og eksterne tabeller Introduktion][cindygross-hive-tables].


##<a id="data"></a>Om eksempeldataene, en Apache log4j-fil

I dette eksempel bruges en *log4j* eksempelfil, som er gemt på **/example/data/sample.log** i din blob storage beholder. Hver logfil inde i filen består af en linje med felter, der indeholder en `[LOG LEVEL]` feltet for at vise typen og alvorlighed, f.eks.:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

I det forrige eksempel er logføringsniveau fejl.

> [AZURE.NOTE] Du kan også oprette en log4j-fil ved hjælp af værktøjet [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) logføring og derefter overføre filen til objektbeholderen blob. Du kan finde instruktioner i [Overføre Data til HDInsight](hdinsight-upload-data.md) . Du kan finde flere oplysninger om hvordan Azure Blob-lager bruges sammen med HDInsight, [Brug Azure Blob-lager med HDInsight](hdinsight-hadoop-use-blob-storage.md).

Eksempeldataene er gemt i Azure Blob-lager, som HDInsight bruger som standard-filsystemet. HDInsight kan få adgang til filer, der er gemt i BLOB ved hjælp af præfikset **wasb** . For at få adgang til sample.log-filen, skal du bruge følgende syntaks:

    wasbs:///example/data/sample.log

Da Azure Blob-lager er standard lagerplads for HDInsight, kan du også adgang til filen ved hjælp af **/example/data/sample.log** fra HiveQL.

> [AZURE.NOTE] Syntaksen, **wasbs: / / /**, der bruges til at få adgang til filer, der er gemt i objektbeholderen til lagring af standard for din HDInsight klynge. Hvis du har angivet ekstra lagerplads konti, når du har klargjort din klynge, og du vil have adgang til filer, der er gemt på disse konti, kan du få adgang til dataene ved at angive objektbeholder navn og lager konto adressen, for eksempel **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Eksempel job: Project kolonner til separerede data

Følgende HiveQL udtalelser bliver project kolonner til afgrænset data, der er gemt i den **wasbs: / / / / eksempeldataene** directory:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

I det forrige eksempel sætningerne HiveQL, skal du udføre følgende handlinger:

* __angive hive.execution.engine=tez;__: angiver som udførelse af programmet skal benytte Tez. Bruge Tez i stedet for MapReduce kan give en stigning i forespørgslens ydeevne. Flere oplysninger om Tez, i afsnittet [Brug Apache Tez til forbedret ydeevne](#usetez) .

    > [AZURE.NOTE] Denne sætning er kun påkrævet, når du bruger en Windows-baseret HDInsight klynge; Tez er udførelse af standardprogrammet for Linux-baserede HDInsight.

* **Slip tabel**: sletter tabellen og datafilen, hvis der allerede findes i tabellen.
* **Opret ekstern tabel**: opretter en ny **ekstern** tabel i Hive. Eksterne tabeller kun gemme tabeldefinitionen i Hive; data, der er tilbage i den oprindelige placering og i det oprindelige format.
* **Række FORMAT**: fortæller Hive hvordan dataene skal formateres. I dette tilfælde er felterne i hver log adskilt af et mellemrum.
* **GEMT AS TEXTFILE placering**: fortæller Hive hvor dataene er gemt (mappen eksempeldataene /), og det er gemt som tekst. Dataene kan være i én fil eller et opslag på tværs af flere filer i mappen.
* **Vælg**: markerer en optælling af alle rækker, hvor kolonne **t4** indeholder værdien **[ERROR]**. Dette skal returnere en værdi på **3** , fordi der er tre rækker, der indeholder denne værdi.
* **INPUT__FILE__NAME som '%.log'** - fortæller Hive, som vi bør kun returnerer data fra filer, der slutter i. log. Dette begrænser søgningen til filen sample.log, der indeholder dataene, og beholder den returnerer data fra andre eksempel-datafiler, der ikke matcher skemaet vi definerede.

> [AZURE.NOTE] Eksterne tabeller skal bruges, når du forventer de underliggende data skal opdateres af en ekstern kilde, som et automatiseret data overførslen eller af en anden MapReduce handling, og du altid vil Hive forespørgsler til at bruge de nyeste data.
>
> Slippe en ekstern tabel betyder **ikke** slette dataene, slettes kun tabeldefinitionen.

Når du har oprettet den eksterne tabel, er følgende udtalelser bruges til at oprette en **intern** tabel.

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

Disse sætninger udføre følgende handlinger:

* **Oprette tabel Hvis ikke findes**: opretter en tabel, hvis den ikke allerede findes. Da nøgleordet **eksterne** ikke bruges, er det en intern tabel, som er gemt i et Hive datalager og administreres helt af Hive.
* **GEMT AS ORC**: gemmer dataene i optimeret række kolonne (ORC)-format. Dette er et meget optimerede og effektivt format til lagring af Hive data.
* **Indsæt OVERSKRIV... Vælg**: markerer rækker fra tabellen **log4jLogs** , der indeholder **[ERROR]**, og derefter indsætter dataene i tabellen **errorLogs** .

> [AZURE.NOTE] I modsætning til eksterne tabeller slette slippe en intern tabel også de underliggende data.

##<a id="usetez"></a>Bruge Apache Tez til forbedret ydeevne

[Apache Tez](http://tez.apache.org) er en struktur, som muliggør data intensivt programmer, som Hive til at køre meget mere effektivt skaleres til brug. I den nyeste version af HDInsight understøtter Hive kører på Tez. Tez er aktiveret som standard for Linux-baserede HDInsight klynger.

> [AZURE.NOTE] Tez er deaktiveret som standard til Windows-baseret HDInsight klynger og det skal være aktiveret. For at kunne udnytte Tez, skal følgende værdi være angivet for en Hive-forespørgsel:
>
> ```set hive.execution.engine=tez;```
>
>Dette kan sendes på grundlag af hver forespørgsel ved at placere det i starten af din forespørgsel. Du kan også angive denne indstilling for at være til som standard på en klynge ved at angive Konfigurationsværdien, når du opretter klyngen. Du kan finde flere oplysninger i [Klargøring HDInsight klynger](hdinsight-provision-clusters.md).

Den [Hive på Tez designdokumenter](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) indeholder et antal oplysninger om implementering valgmuligheder og justering konfigurationer.

For at hjælpe i forbindelse med fejlfinding job kørte ved hjælp af Tez, HDInsight indeholder følgende internettet computerens brugergrænseflade, så du kan få vist oplysninger om Tez job:

* [Brug Tez Brugergrænsefladen på Windows-baseret HDInsight](hdinsight-debug-tez-ui.md)

* [Brug af Ambari Tez visningen på Linux-baserede HDInsight](hdinsight-debug-ambari-tez-view.md)

##<a id="run"></a>Vælge, hvordan du udfører kørslen HiveQL

HDInsight kan køre HiveQL job ved hjælp af en række forskellige metoder. Brug tabellen nedenfor til at afgøre, hvilken metode der passer til dine og derefter følge linket for en gennemgang.

| **Brug denne indstilling** , hvis du vil...                                                     | .. .an **interaktive** shell | ... **batchbehandling** | .. .with **klynge operativsystem** | .. .from **klientens operativsystem** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Hive visning](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Enhver (browser baseret) |
| [Beeline kommandoen (fra en SSH session)](hdinsight-hadoop-use-hive-beeline.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X eller Windows        |
| [Hive kommandoen (fra en SSH session)](hdinsight-hadoop-use-hive-ssh.md)                                         |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X eller Windows        |
| [Krøllet](hdinsight-hadoop-use-hive-curl.md)                                       |           &nbsp;            |            ✔            | Linux eller Windows                          | Linux, Unix, Mac OS X eller Windows        |
| [Forespørgsel console](hdinsight-hadoop-use-hive-query-console.md)                     |           &nbsp;            |            ✔            | Windows                                   | Enhver (browser baseret)                            |
| [HDInsight værktøjer til Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |           &nbsp;            |            ✔            | Linux eller Windows                          | Windows                                  |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md)                   |           &nbsp;            |            ✔            | Linux eller Windows                          | Windows                                  |
| [Fjernskrivebord](hdinsight-hadoop-use-hive-remote-desktop.md)                   |              ✔              |            ✔            | Windows                                   | Windows                                  |

## <a name="running-hive-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Køre Hive job på Azure HDInsight ved hjælp af lokal SQL Server Integration Services

Du kan også bruge SQL Server Integration Services (SSIS) til at køre en Hive-job. Azure Feature Pack til SSIS indeholder følgende komponenter, der arbejder med Hive job på HDInsight.


- [Azure HDInsight Hive opgave][hivetask]
- [Azure abonnement Forbindelsesstyring][connectionmanager]


Få mere at vide om funktionspakken til Azure SSIS [her][ssispack].


##<a id="nextsteps"></a>Næste trin

Nu hvor du har lært hvad Hive er, og hvordan du kan bruge det sammen med Hadoop i HDInsight, kan du bruge følgende links for at undersøge andre måder at arbejde med Azure HDInsight.


- [Overføre data til HDInsight][hdinsight-upload-data]
- [Brug gris med HDInsight][hdinsight-use-pig]
- [Bruge Sqoop med HDInsight](hdinsight-use-sqoop.md)
- [Bruge Oozie med HDInsight](hdinsight-use-oozie.md)
- [Brug MapReduce sager med HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
