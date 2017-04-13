<properties
   pageTitle="Brug MapReduce og krøllet med Hadoop i HDInsight | Microsoft Azure"
   description="Lær at køre MapReduce job med Hadoop på HDInsight ved hjælp af krøllet."
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

#<a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-curl"></a>Køre MapReduce job med Hadoop på HDInsight ved hjælp af krøllet

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

I dette dokument lære du, hvordan du bruger krøllet til at køre MapReduce job på et Hadoop på HDInsight klynge.

Krøllet bruges til at vise, hvordan du kan arbejde med HDInsight ved hjælp af rå HTTP-anmodninger til at køre MapReduce job. Det fungerer ved hjælp af den WebHCat REST-API (tidligere kendt som Templeton) leveres af din HDInsight klynge.

> [AZURE.NOTE] Hvis du allerede er vant til at bruge Linux-baserede Hadoop-servere, men du er ny bruger af HDInsight skal du se, [Hvad du bør vide om Linux-baserede Hadoop på HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* Et Hadoop på HDInsight klynge (Linux eller Windows-baseret)

* [Krøllet](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Køre MapReduce job med krøllet

> [AZURE.NOTE] Når du bruger krøllet eller en hvilken som helst anden RESTEN kommunikation med WebHCat, skal du godkende anmodningerne ved at indsende den HDInsight klynge administratorens brugernavn og adgangskode. Du skal også bruge klyngenavnet som en del af den URI, der bruges til at sende anmodninger til serveren.
>
> Erstat **brugernavn** for kommandoerne i dette afsnit med brugeren til at godkende klynge og **din adgangskode** med adgangskoden for kontoen. Erstat **CLUSTERNAME** med navnet på din klynge.
>
> REST-API er sikret ved hjælp af [grundlæggende access-godkendelse](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bør altid lave anmodninger ved hjælp af HTTPS til at sikre, at dine legitimationsoplysninger sikkert er sendt til serveren.

1. Brug følgende kommando for at bekræfte, at du kan oprette forbindelse til din HDInsight klynge fra en kommandolinje:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Du skal modtage et svar, der ligner følgende:

        {"status":"ok","version":"v1"}

    De parametre, bruges i denne kommando er som følger:

    * **-u**: Angiver det brugernavn og adgangskode, der bruges til at godkende anmodningen
    * **-G**: Angiver, at dette er en GET-anmodning

    Starten af URI, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, er det samme for alle anmodninger.

2. For at sende et MapReduce job, skal du bruge følgende kommando:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasbs:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasbs:///example/data/gutenberg/davinci.txt -d arg=wasbs:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    Slutningen af URI (/ mapreduce/glas) fortæller WebHCat, denne anmodning starter et MapReduce job fra en klasse i filen glas. De parametre, bruges i denne kommando er som følger:

    * **-d**: `-G` ikke bruges, så anmodningen som standard metoden POST. `-d`Angiver de værdier, der er sendt med anmodningen.

        * **User.Name**: den bruger, der kører kommandoen
        * **glas**: placeringen af filen glas, der indeholder klasse for at være kørte
        * **klasse**: den klasse, der indeholder MapReduce logik
        * **arg**: argumenterne, der skal overføres til MapReduce jobbet. i dette tilfælde, input tekstfilen og den mappe, der bruges til output

    Denne kommando skal returnere et job-ID, der kan bruges til at kontrollere status for jobbet:

        {"id":"job_1415651640909_0026"}

3. Brug følgende kommando for at kontrollere status for jobbet. Erstat **job-id** med den værdi, returneres i det forrige trin. Eksempelvis hvis returværdien blev `{"id":"job_1415651640909_0026"}`, og klik derefter på job-id, der ville være `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Hvis sagen er afsluttet, skal tilstanden være "udført".

    > [AZURE.NOTE] Denne krøllet anmodning returnerer et JSON-dokument med oplysninger om jobbet. jq bruges til at hente værdien tilstand.

4. Når tilstanden for jobbet er blevet ændret til **lykkedes**, kan du hente resultaterne af jobbet fra Azure Blob-lager. Den `statusdir` parameter, der sendes med forespørgslen indeholder placeringen af outputfilen; i dette tilfælde **wasbs: / / / eksempel/krøllet**. Denne adresse gemmer output fra jobbet i mappen **eksempel/krøllet** i standard objektbeholderen til lagring af bruges af din HDInsight klynge.

Du kan vise og hente filerne ved hjælp af [Azure CLI](../xplat-cli-install.md). Brug følgende kommando for at vise filer i **eksempel/krøllet**:

    azure storage blob list <container-name> example/curl

Hvis du vil hente en fil, skal du bruge følgende:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Du skal angive kontonavnet lagerplads, der indeholder blob ved hjælp af den `-a` og `-k` parametre, eller et sæt den **AZURE\_LAGERPLADS\_konto** og **AZURE\_LAGERPLADS\_ACCESS\_nøgle** miljøvariabler. Se, [hvordan du kan overføre data til HDInsight](hdinsight-upload-data.md) kan finde flere oplysninger.

##<a id="summary"></a>Oversigt

Som vist i dette dokument, kan du bruge rå HTTP-anmodning om at køre, overvåge og se resultaterne af Hive job i din HDInsight klynge.

Du kan finde flere oplysninger om grænsefladen RESTEN, der bruges i denne artikel, [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om MapReduce sager i HDInsight:

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)
