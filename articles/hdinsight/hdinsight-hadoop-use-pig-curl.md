<properties
   pageTitle="Bruge Hadoop gris med krøllet i HDInsight | Microsoft Azure"
   description="Lær at bruge krøllet til at køre gris latinsk job på en Hadoop klynge i Azure HDInsight."
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

#<a name="run-pig-jobs-with-hadoop-on-hdinsight-by-using-curl"></a>Køre gris job med Hadoop på HDInsight ved hjælp af krøllet

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

I dette dokument lære du, hvordan du bruger krøllet til at køre gris latinsk job på en Azure HDInsight klynge. Programmeringssproget gris latinsk giver dig mulighed at beskrive transformationer, der gælder for det indtastede data til at producere den ønskede output.

Krøllet bruges til at vise, hvordan du kan arbejde med HDInsight ved hjælp af rå HTTP-anmodninger til at køre, overvåge og hente resultaterne af gris job. Det fungerer ved hjælp af den WebHCat REST-API (tidligere kendt som Templeton), som leveres af din HDInsight klynge.

> [AZURE.NOTE] Hvis du allerede er vant til at bruge Linux-baserede Hadoop-servere, men du er ny bruger af HDInsight skal du se [Linux-baserede HDInsight tip](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* En Azure HDInsight (Hadoop på HDInsight) klynge (Linux-baserede eller Windows-baseret)

* [Krøllet](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Køre gris job ved hjælp af krøllet

> [AZURE.NOTE] Når du bruger krøllet eller en hvilken som helst anden RESTEN kommunikation med WebHCat, skal du godkende anmodningerne ved at indsende den administratorens brugernavn og adgangskode for HDInsight-klyngen. Du skal også bruge klyngenavnet som en del af URI Uniform Resource Identifier (), der bruges til at sende anmodningerne til serveren.
>
> Erstatte **brugernavn** med brugeren til at godkende til klyngen for kommandoerne i dette afsnit, og Erstat **adgangskode** med adgangskoden for kontoen. Erstat **CLUSTERNAME** med navnet på din klynge.
>
> REST-API er sikret via [grundlæggende access-godkendelse](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bør altid lave anmodninger ved hjælp af Secure HTTP (HTTPS) for at sikre, at dine legitimationsoplysninger sikkert er sendt til serveren.

1. Brug følgende kommando for at bekræfte, at du kan oprette forbindelse til din HDInsight klynge fra en kommandolinje:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Du skal modtage et svar, der ligner følgende:

        {"status":"ok","version":"v1"}

    De parametre, bruges i denne kommando er som følger:

    * **-u**: brugernavn og adgangskode, der bruges til at godkende anmodningen
    * **-G**: Angiver, at dette er en GET-anmodning

    Starten af URL-adressen, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, vil være den samme alle anmodninger om. Stien, **/status/status**angiver, at forespørgslen er til at returnere status for WebHCat (også kaldet Templeton) til serveren til.

2. Du kan bruge følgende kode til at sende et gris latinsk job til klyngen:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasbs:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasbs:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    De parametre, bruges i denne kommando er som følger:

    * **-d**: fordi `-G` ikke bruges, anmodningen som standard metoden POST. `-d`Angiver de værdier, der er sendt med anmodningen.

        * **User.Name**: den bruger, der kører kommandoen
        * **udføre**: feltet gris latinsk sætninger for at udføre
        * **statusdir**: den mappe, der skal skrives status for dette job til

    > [AZURE.NOTE] Bemærk, at mellemrummene i gris latinsk sætninger erstattes med den `+` tegn, når den bruges med krøllet.

    Denne kommando skal returnere et job-ID, der kan bruges til at kontrollere status for jobbet, f.eks.:

        {"id":"job_1415651640909_0026"}

3. Brug følgende kommando for at kontrollere status for jobbet. Erstat **job-id** med den værdi, returneres i det forrige trin. Eksempelvis hvis returværdien blev `{"id":"job_1415651640909_0026"}`, og derefter **job-id** , der ville være `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Hvis jobbet er afsluttet, bliver tilstanden **lykkedes**.

    > [AZURE.NOTE] Denne krøllet anmodning returnerer et JavaScript Object Notation (JSON) dokument med oplysninger om opgaven, og jq bruges til at hente værdien tilstand.

##<a id="results"></a>Få vist resultater

Når tilstanden for jobbet er blevet ændret til **lykkedes**, kan du hente resultaterne af jobbet fra Azure Blob-lager. Den `statusdir` parameter overføres med forespørgslen indeholder placeringen af outputfilen; i dette tilfælde **wasbs: / / / eksempel/pigcurl**. Denne adresse gemmer output fra jobbet i mappen **eksempel/pigcurl** i beholderen standard lagerplads, der anvendes af din HDInsight klynge.

Du kan vise og hente filerne ved hjælp af [Azure CLI](../xplat-cli-install.md). Brug følgende kommando for at vise filer i **eksempel/pigcurl**:

    azure storage blob list <container-name> example/pigcurl

Hvis du vil hente en fil, skal du bruge følgende:

    azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Du skal angive kontonavnet lagerplads, der indeholder blob ved hjælp af den `-a` og `-k` parametre, eller et sæt den **AZURE\_LAGERPLADS\_konto** og **AZURE\_LAGERPLADS\_ACCESS\_nøgle** miljøvariabler.

##<a id="summary"></a>Oversigt

Som vist i dette dokument, kan du bruge en rå HTTP-anmodning om at køre, overvåge og se resultaterne af gris job på din HDInsight klynge.

Du kan finde flere oplysninger om RESTEN grænsefladen, der bruges i denne artikel, [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Næste trin

Generelle oplysninger om gris på HDInsight:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)
