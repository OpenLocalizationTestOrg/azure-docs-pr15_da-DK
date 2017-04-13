<properties
   pageTitle="Brug Hadoop gris i HDInsight | Microsoft Azure"
   description="Lær at bruge gris med Hadoop på HDInsight."
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
   ms.date="09/14/2016"
   ms.author="larryfr"/>

# <a name="use-pig-with-hadoop-on-hdinsight"></a>Brug gris med Hadoop på HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache gris](http://pig.apache.org/) er en platform til oprettelse af programmer til Hadoop ved hjælp af en beskrivelse af kaldet *gris latinsk*sprog. Gris er et alternativ til Java til oprettelse af *MapReduce* løsninger, og den er taget med Azure HDInsight.

I denne artikel kan du lære, hvordan du kan bruge gris med HDInsight.

##<a id="why"></a>Hvorfor bruge gris?

En af udfordringer i forbindelse med behandling af data ved hjælp af MapReduce i Hadoop implementerer din behandlingslogik ved hjælp af kun et kort og en reducere funktion. Kompleks behandling, du ofte har at inddele behandling i flere MapReduce handlinger, som er kædet sammen for at opnå det ønskede resultat.

I stedet for at tvinge dig til at bruge kun kort og nedsætte funktioner, gris giver dig mulighed at definere behandling som en serie af transformationer, som data, der bevæger sig gennem for at producere ønsket afgang.

Gris latinsk sprog giver dig mulighed at beskrive dataflow fra rå input gennem en eller flere transformationer til at producere den ønskede output. Gris latinsk programmer skal du følge denne generelle mønster:

- **Indlæse**: læse data, der skal ændres fra filsystemet
- **Transformere**: arbejde med dataene
- **Lagring eller store**: eksportere data til skærmen eller gemme den til behandling

Gris latinsk understøtter også brugerdefinerede funktioner (UDF), som gør det muligt at kalde eksterne komponenter, der implementerer logik, der er svært at model i gris latinsk.

Se [gris latinsk Reference manuel 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) og [2 for gris latinsk Reference manuel](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html)kan finde flere oplysninger om gris latinsk.

Du kan finde et eksempel på brug af brugerdefinerede funktioner med gris følgende dokumenter:

* [Brug DataFu med gris i HDInsight](hdinsight-hadoop-use-pig-datafu-udf.md) - DataFu er en samling af nyttige brugerdefinerede funktioner vedligeholdes af Apache

* [Bruge Python med gris og Hive i HDInsight](hdinsight-python.md)

* [Bruge C# med Hive og gris i HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Om eksempeldataene

I dette eksempel bruges en *log4j* eksempelfil, som er gemt på **/example/data/sample.log** i din blob storage beholder. Hver logfil inde i filen består af en linje med felter, der indeholder en `[LOG LEVEL]` feltet for at vise typen og alvorlighed, f.eks.:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

I det forrige eksempel er logføringsniveau fejl.

> [AZURE.NOTE] Du kan også oprette en log4j-fil ved hjælp af værktøjet [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) logføring og derefter overføre filen til din blob. Du kan finde instruktioner i [Overføre Data til HDInsight](hdinsight-upload-data.md) . Du kan finde flere oplysninger om, hvordan BLOB i Azure lagerplads bruges med HDInsight [Brug Azure Blob-lager med HDInsight](hdinsight-hadoop-use-blob-storage.md).

Eksempeldataene er gemt i Azure Blob-lager, som HDInsight bruger som standard-filsystemet til Hadoop klynger. HDInsight kan få adgang til filer, der er gemt i BLOB ved hjælp af præfikset **wasb** . For at få adgang til sample.log-filen, skal du bruge følgende syntaks:

    wasbs:///example/data/sample.log

Da WASB er standard lagerplads for HDInsight, kan du også adgang til filen ved hjælp af **/example/data/sample.log** fra gris latinsk.

> [AZURE.NOTE] Syntaksen, **wasbs: / / /**, der bruges til at få adgang til filer, der er gemt i objektbeholderen til lagring af standard for din HDInsight klynge. Hvis du har angivet ekstra lagerplads konti, når du har klargjort din klynge, og du vil have adgang til filer, der er gemt på disse konti, kan du få adgang til dataene ved at angive objektbeholder navn og lager konto-adressen for eksempel: **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Om kørslen eksempel

Følgende gris latinsk jobbet indlæser filen **sample.log** fra standard lagerplads på din HDInsight klynge. Derefter udfører en række transformationer, der medfører en antal hvor mange gange hver logføringsniveau opstod i inputdataene. Resultaterne er gemt i STDOUT.

    LOGS = LOAD 'wasbs:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;

Følgende billede viser en oversigt over, hvad hver transformation betyder til dataene.

![Grafisk repræsentation af transformeringer][image-hdi-pig-data-transformation]

##<a id="run"></a>Kørslen gris latinsk

HDInsight kan køre gris latinsk job ved hjælp af en række forskellige metoder. Brug tabellen nedenfor til at afgøre, hvilken metode der passer til dine og derefter følge linket for en gennemgang.

| **Brug denne indstilling** , hvis du vil...                                   | .. .an **interaktive** shell | ... **batchbehandling** | .. .with **klynge operativsystem** | .. .from **klientens operativsystem** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X eller Windows        |
| [Krøllet](hdinsight-hadoop-use-pig-curl.md)                      |           &nbsp;            |            ✔            | Linux eller Windows                          | Linux, Unix, Mac OS X eller Windows        |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux eller Windows                          | Windows (for nu)                        |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md)  |           &nbsp;            |            ✔            | Linux eller Windows                          | Windows                                  |
| [Fjernskrivebord](hdinsight-hadoop-use-pig-remote-desktop.md)  |              ✔              |            ✔            | Windows                                   | Windows                                  |


## <a name="running-pig-jobs-on-azure-hdinsight-using-on-premises-sql-server-integration-services"></a>Køre gris job på Azure HDInsight ved hjælp af lokal SQL Server Integration Services

Du kan også bruge SQL Server Integration Services (SSIS) til at køre en gris sag. Azure Feature Pack til SSIS indeholder følgende komponenter, der arbejder med gris job på HDInsight.


- [Azure HDInsight gris opgave][pigtask]
- [Azure abonnement Forbindelsesstyring][connectionmanager]


Få mere at vide om funktionspakken til Azure SSIS [her][ssispack].


##<a id="nextsteps"></a>Næste trin

Nu hvor du har lært, hvordan du bruger gris med HDInsight, kan du bruge følgende links for at undersøge andre måder at arbejde med Azure HDInsight.

* [Overføre data til HDInsight][hdinsight-upload-data]
* [Bruge Hive med HDInsight][hdinsight-use-hive]
* [Bruge Sqoop med HDInsight](hdinsight-use-sqoop.md)
* [Bruge Oozie med HDInsight](hdinsight-use-oozie.md)
* [Brug MapReduce sager med HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/
[pigtask]: http://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../powershell-install-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
