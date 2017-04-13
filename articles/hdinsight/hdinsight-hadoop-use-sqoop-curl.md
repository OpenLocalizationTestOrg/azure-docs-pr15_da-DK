<properties
   pageTitle="Bruge Hadoop Sqoop med krøllet i HDInsight | Microsoft Azure"
   description="Lær at sende fra en fjernplacering Sqoop job til HDInsight ved hjælp af krøllet."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/21/2016"
   ms.author="jgao"/>

#<a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Køre Sqoop job med Hadoop i HDInsight med krøllet

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Lær at bruge krøllet til at køre Sqoop job på en Hadoop klynge i HDInsight.

Krøllet bruges til at vise, hvordan du kan arbejde med HDInsight ved hjælp af rå HTTP-anmodninger til at køre, overvåge og hente resultaterne af Sqoop job. Det fungerer ved hjælp af den WebHCat REST-API (tidligere kendt som Templeton) leveres af din HDInsight klynge.

> [AZURE.NOTE] Hvis du allerede er vant til at bruge Linux-baserede Hadoop-servere, men du er ny bruger af HDInsight skal du se [oplysninger om brug af HDInsight på Linux](hdinsight-hadoop-linux-information.md).

##<a name="prerequisites"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* Et Hadoop på HDInsight klynge (Linux eller Windows-baseret)

* [Krøllet](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a name="submit-sqoop-jobs-by-using-curl"></a>Sende Sqoop job ved hjælp af krøllet

> [AZURE.NOTE] Når du bruger krøllet eller en hvilken som helst anden RESTEN kommunikation med WebHCat, skal du godkende anmodningerne ved at angive det brugernavn og adgangskode til HDInsight klyngeadministratoren. Du skal også bruge klyngenavnet som en del af den URI Uniform Resource Identifier () bruges til at sende anmodninger til serveren.
>
> Erstatte **brugernavn** med brugeren til at godkende til klyngen for kommandoerne i dette afsnit, og Erstat **adgangskode** med adgangskoden for kontoen. Erstat **CLUSTERNAME** med navnet på din klynge.
>
> REST-API er sikret via [basisgodkendelse](http://en.wikipedia.org/wiki/Basic_access_authentication). Du bør altid lave anmodninger ved hjælp af Secure HTTP (HTTPS) for at sikre, at dine legitimationsoplysninger sikkert er sendt til serveren.

1. Brug følgende kommando for at bekræfte, at du kan oprette forbindelse til din HDInsight klynge fra en kommandolinje:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Du skal modtage et svar, der ligner følgende:

        {"status":"ok","version":"v1"}

    De parametre, bruges i denne kommando er som følger:

    * **-u** - brugernavn og adgangskode, der bruges til at godkende anmodningen.
    * **-G** - angiver, at dette er en GET-anmodning.

    Starten af URL-adressen, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, vil være den samme alle anmodninger om. Stien, **/status/status**angiver, at forespørgslen er til at returnere en status for WebHCat (også kaldet Templeton) til serveren til. 

2. Du kan bruge følgende til at sende en sqoop sag:


        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /tutorials/usesqoop/data --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop

    De parametre, bruges i denne kommando er som følger:

    * **-d** - da `-G` ikke bruges, anmodningen som standard metoden POST. `-d`Angiver de værdier, der er sendt med anmodningen.

        * **user.name** - den bruger, der kører kommandoen.

        * **kommandoen** - feltet Sqoop kommandoen til at udføre.

        * **statusdir** - mappen, der skal skrives status for dette job til.

    Denne kommando skal returnere et job-ID, der kan bruges til at kontrollere status for jobbet.

        {"id":"job_1415651640909_0026"}

3. Brug følgende kommando for at kontrollere status for jobbet. Erstat **job-id** med den værdi, returneres i det forrige trin. Eksempelvis hvis returværdien blev `{"id":"job_1415651640909_0026"}`, og derefter **job-id** , der ville være `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

    Hvis jobbet er afsluttet, bliver tilstanden **lykkedes**.

    > [AZURE.NOTE] Denne krøllet anmodning returnerer et JavaScript Object Notation (JSON) dokument med oplysninger om jobbet. jq bruges til at hente værdien tilstand.

4. Når tilstanden for jobbet er blevet ændret til **lykkedes**, kan du hente resultaterne af jobbet fra Azure Blob-lager. Den `statusdir` parameter overføres med forespørgslen indeholder placeringen af outputfilen; i dette tilfælde **wasbs: / / / eksempel/krøllet**. Denne adresse gemmer output fra jobbet i mappen **eksempel/krøllet** på standard objektbeholderen til lagring af bruges af din HDInsight klynge.

    Du kan vise og hente filerne ved hjælp af [Azure CLI](../xplat-cli-install.md). Brug følgende kommando for at vise filer i **eksempel/krøllet**:

        azure storage blob list <container-name> example/curl

    Hvis du vil hente en fil, skal du bruge følgende:

        azure storage blob download <container-name> <blob-name> <destination-file>

    > [AZURE.NOTE] Skal du enten angive kontonavnet lagerplads, der indeholder blob ved hjælp af den `-a` og `-k` parametre eller angive den **AZURE\_LAGERPLADS\_konto** og **AZURE\_LAGERPLADS\_ACCESS\_nøgle** miljøvariabler. Se < en linkreference = "hdinsight-Overfør-data.md" target = "_blank" kan finde flere oplysninger.

##<a name="limitations"></a>Begrænsninger

* Flere eksport - med Linux-baserede HDInsight, Sqoop forbindelsen bruges til at eksportere data til Microsoft SQL Server eller Azure SQL-Database understøtter i øjeblikket ikke flere indsættes.

* "Samling"-med Linux-baserede HDInsight, når du bruger den `-batch` skifte, når du udfører indsættes, Sqoop udfører flere indsættes i stedet for "samling" Indsæt handlinger.

##<a name="summary"></a>Oversigt

Som vist i dette dokument, kan du bruge en rå HTTP-anmodning om at køre, overvåge og se resultaterne af Sqoop job på din HDInsight klynge.

Du kan finde flere oplysninger om RESTEN grænsefladen, der bruges i denne artikel, <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST-API-vejledning</a>.

##<a name="next-steps"></a>Næste trin

Generelle oplysninger om Hive med HDInsight:

* [Bruge Sqoop med Hadoop på HDInsight](hdinsight-use-sqoop.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)

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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


