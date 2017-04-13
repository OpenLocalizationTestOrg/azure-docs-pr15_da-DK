<properties
   pageTitle="Bruge Hadoop Hive med krøllet i HDInsight | Microsoft Azure"
   description="Lær at sende fra en fjernplacering gris job til HDInsight ved hjælp af krøllet."
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
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-with-hadoop-in-hdinsight-with-curl"></a>Køre Hive-forespørgsler med Hadoop i HDInsight med krøllet

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

I dette dokument lære du, hvordan du bruger krøllet til at køre Hive forespørgsler på et Hadoop på Azure HDInsight klynge.

Krøllet bruges til at vise, hvordan du kan arbejde med HDInsight ved hjælp af rå HTTP-anmodninger til at køre, overvåge og hente resultaterne af Hive forespørgsler. Det fungerer ved hjælp af den WebHCat REST-API (tidligere kendt som Templeton) leveres af din HDInsight klynge.

> [AZURE.NOTE] Hvis du allerede er vant til at bruge Linux-baserede Hadoop-servere, men du er ny bruger af HDInsight skal du se, [Hvad du bør vide om Hadoop på Linux-baserede HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

* Et Hadoop på HDInsight klynge (Linux eller Windows-baseret)

* [Krøllet](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Køre Hive forespørgsler ved hjælp af krøllet

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

    Starten af URL-adressen, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, vil være den samme alle anmodninger om. Stien, **/status/status**angiver, at forespørgslen er til at returnere en status for WebHCat (også kaldet Templeton) til serveren til. Du kan også anmode om versionen af Hive ved hjælp af følgende kommando:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Dette skal returnere et svar, der er som følger:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Du kan bruge følgende til at oprette en ny tabel med navnet **log4jLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasbs:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    De parametre, bruges i denne kommando er som følger:

    * **-d** - da `-G` ikke bruges, anmodningen som standard metoden POST. `-d`Angiver de værdier, der er sendt med anmodningen.

        * **user.name** - den bruger, der kører kommandoen.

        * **udføre** - feltet HiveQL sætninger for at udføre.

        * **statusdir** - mappen, der skal skrives status for dette job til.

    Disse sætninger udføre følgende handlinger:

    * **DROP TABLE** - sletter tabellen og datafilen, hvis der allerede findes i tabellen.

    * **Opret eksterne tabel** - opretter en ny 'ekstern' tabel i Hive. Eksterne tabeller indeholder kun tabeldefinition i Hive. Data, der er tilbage i den oprindelige placering.

        > [AZURE.NOTE] Eksterne tabeller skal bruges, når du forventer, at de underliggende data opdateres ved en ekstern kilde, som et automatiseret data overførslen eller af en anden MapReduce handling, men vil altid Hive forespørgsler til at bruge de nyeste data.
        >
        > Slippe en ekstern tabel betyder **ikke** slette dataene, kun tabeldefinition.

    * **Række FORMAT** – som fortæller Hive hvordan dataene skal formateres. I dette tilfælde er felterne i hver log adskilt af et mellemrum.

    * **GEMT AS TEXTFILE placering** - fortæller Hive hvor dataene er gemt (mappen eksempeldataene /), og at det er gemt som tekst.

    * **Vælg** - markerer en optælling af alle rækker, hvor kolonne **t4** indeholder værdien **[ERROR]**. Dette skal returnere en værdi på **3** , når der er tre rækker, der indeholder denne værdi.

    > [AZURE.NOTE] Bemærk, at mellemrum mellem HiveQL sætninger erstattes med den `+` tegn, når den bruges med krøllet. Anførselstegn værdier, der indeholder et mellemrum, som afgrænseren, ikke kan erstattes med `+`.

    * **INPUT__FILE__NAME som '% 25.log'** - denne søgningen til kun bruge filer, der ender med begrænsninger. log. Hvis dette ikke findes, vil Hive forsøge at søge i alle filer i denne mappe og dens undermapper, herunder filer, der ikke matcher kolonne skemaet defineret for denne tabel.

    > [AZURE.NOTE] Bemærk, at % 25 er URL-adressen kodet form af %, så den faktiske tilstand er `like '%.log'`. Feltet % har skal URL-kodes, som fortolkes det som et specialtegn i URL-adresser.

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

6. Brug følgende udtalelser til at oprette en ny 'interne' tabel med navnet **errorLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="set+hive.execution.engine=tez;CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasbs:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Disse sætninger udføre følgende handlinger:

    * **Oprette tabel Hvis ikke findes** - opretter en tabel, hvis den ikke allerede findes. Da nøgleordet **eksterne** ikke bruges, er dette en intern tabel, som er gemt i et Hive datalager og administreres helt af Hive.

        > [AZURE.NOTE] I modsætning til eksterne tabeller slippe en intern tabel, slettes de underliggende data.

    * **GEMT AS ORC** - gemmer dataene i optimeret række kolonne (ORC)-format. Dette er et meget optimerede og effektivt format til lagring af Hive data.
    * **Indsæt OVERSKRIV... Vælg** - markerer rækker fra tabellen **log4jLogs** , der indeholder **[ERROR]**og derefter indsætter dataene i tabellen **errorLogs** .
    * **Vælg** - markerer alle rækker fra den nye **errorLogs** tabel.

7. Brug det job-ID returneres til at kontrollere status for jobbet. Når den er fuldført, bruge Azure CLI til Mac, Linux og vinduer, som det er beskrevet ovenfor for at hente og få vist resultaterne. Output skal indeholde tre linjer, som alle indeholder **[ERROR]**.


##<a id="summary"></a>Oversigt

Som vist i dette dokument, kan du bruge en rå HTTP-anmodning om at køre, overvåge og se resultaterne af Hive job på din HDInsight klynge.

Du kan finde flere oplysninger om RESTEN grænsefladen, der bruges i denne artikel, <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat Reference</a>.

##<a id="nextsteps"></a>Næste trin

Generelle oplysninger om Hive med HDInsight:

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




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


