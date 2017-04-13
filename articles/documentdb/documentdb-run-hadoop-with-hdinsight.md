<properties
    pageTitle="Køre et Hadoop-job ved hjælp af DocumentDB og HDInsight | Microsoft Azure"
    description="Lær at køre et simpelt Hive, gris og MapReduce job med DocumentDB og Azure HDInsight."
    services="documentdb"
    authors="dennyglee"
    manager="jhubbard"
    editor="mimig"
    documentationCenter=""/>


<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="denlee"/>

#<a name="DocumentDB-HDInsight"></a>Køre et Hadoop-job ved hjælp af DocumentDB og HDInsight

Dette selvstudium viser, hvordan du kører [Apache Hive][apache-hive], [Apache gris][apache-pig], og [Apache Hadoop] [ apache-hadoop] MapReduce sager på Azure HDInsight med Documentdb's Hadoop-forbindelse. Documentdb's Hadoop connector lader DocumentDB skal fungere som både kilde- og sink for Hive, gris og MapReduce sager. Dette selvstudium bruger DocumentDB som både datakilde og destination for Hadoop-job.

Når du har fuldført dette selvstudium, vil du kunne besvare spørgsmål, der er følgende:

- Hvordan kan jeg indlæse data fra DocumentDB ved hjælp af et Hive, gris eller MapReduce job?
- Hvordan gemmer jeg data i DocumentDB ved hjælp af et Hive, gris eller MapReduce job?

Vi anbefaler, at komme i gang ved at se den følgende video, hvor vi køre via et Hive-job ved hjælp af DocumentDB og HDInsight.

> [AZURE.VIDEO use-azure-documentdb-hadoop-connector-with-azure-hdinsight]

Derefter gå tilbage til denne artikel, hvor du vil modtage de fulde oplysninger om hvordan du kan køre analytics job på dine DocumentDB data.

> [AZURE.TIP] Dette selvstudium antages det, at du har tidligere oplevelse ved hjælp af Apache Hadoop, Hive og/eller gris. Hvis du er ny bruger af Apache Hadoop, Hive og gris, anbefaler vi at besøge [Apache Hadoop dokumentation][apache-hadoop-doc]. Dette selvstudium antages det, at du har tidligere oplevelse med DocumentDB og har en DocumentDB-konto. Hvis du er ny bruger af DocumentDB, eller du ikke har en DocumentDB-konto, skal du se vores [Introduktion] [ getting-started] side.

Ikke har tid til at fuldføre selvstudiet og blot ønsker at få fuld eksempel PowerShell-scripts til Hive, gris og MapReduce? Ikke noget problem, få dem [her][documentdb-hdinsight-samples]. Overførslen indeholder også hql, gris og java filerne til disse eksempler.

## <a name="NewestVersion"></a>Nyeste Version

<table border='1'>
    <tr><th>Hadoop Connector-versionen</th>
        <td>1.2.0</td></tr>
    <tr><th>Script Uri</th>
        <td>https://portalcontent.BLOB.Core.Windows.NET/scriptaction/documentdb-hadoop-Installer-v04.ps1</td></tr>
    <tr><th>Dato for ændring</th>
        <td>04-26/2016</td></tr>
    <tr><th>Understøttede HDInsight versioner</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Ændringslog</th>
        <td>Opdaterede DocumentDB Java SDK til 1.6.0</br>
            Understøttelse af partitioneret samlinger som både kilde- og sink tilføjet</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Forudsætninger
Før du følge vejledningen i dette selvstudium, kan du sikre dig, at du har følgende:

- En DocumentDB-konto, en database eller en samling med dokumenter i. Du kan finde flere oplysninger, se [Introduktion til DocumentDB][getting-started]. Importere eksempeldata til din DocumentDB konto med den [DocumentDB importere værktøjet][documentdb-import-data].
- Overførselshastighed. Læser og skriver fra HDInsight, der skal tælles med i din anmodning om tildelte enheder på din af websteder. Du kan finde flere oplysninger, se [Provisioned overførselshastighed, anmodning om enheder, og Databasefunktioner][documentdb-manage-throughput].
- Kapacitet til en ekstra lagret procedure inden for hver output af websteder. De lagrede procedurer bruges til overførsel af resulterende dokumenter. Du kan finde flere oplysninger, se [af websteder og klargjort overførselshastighed][documentdb-manage-document-storage].
- Kapacitet for de resulterende dokumenter fra Hive, gris eller MapReduce job. Få mere at vide under [administrere DocumentDB kapacitet og ydeevne][documentdb-manage-collections].
- [*Valgfrit*] Kapacitet til en af flere websteder. Du kan finde flere oplysninger, se [Provisioned dokument lager og indeks indirekte][documentdb-manage-document-storage].

> [AZURE.WARNING] For at undgå oprettelse af en ny samling under en af job, kan du enten udskrive resultatet af til stdout, gemme outputtet til din WASB objektbeholder eller angive en allerede eksisterende af websteder. I forbindelse med angivelse af en eksisterende af websteder, der oprettes nye dokumenter i gruppen af websteder og allerede eksisterende dokumenter påvirkes kun, hvis der opstår en konflikt i *id'er*. **Forbindelsen automatisk overskrive eksisterende dokumenter med id-konflikter**. Du kan deaktivere denne funktion ved at angive indstillingen upsert til falsk. Hvis upsert er falsk, og der opstår en konflikt, mislykkes Hadoop jobbet; rapportering af fejl en id-konflikt.


## <a name="ProvisionHDInsight"></a>Trin 1: Oprette en ny HDInsight klynge
Dette selvstudium bruger scripthandling fra Azure-portalen til at tilpasse din HDInsight klynge. I dette selvstudium bruger vi portalen Azure til at oprette din HDInsight klynge. Oplysninger om, hvordan du kan bruge PowerShell-cmdlet'er eller HDInsight .NET SDK, se [tilpasse HDInsight klynger ved hjælp af Script handlingen] [ hdinsight-custom-provision] artikel.

1. Log på [Azure Portal][azure-portal].

2. Klik på **+ Ny** øverst på den venstre navigationsrude skal søge efter **HDInsight** i den øverste Søgelinje på den nye blade.

3. **HDInsight** udgivet af **Microsoft** vises øverst i resultaterne. Klik på den, og klik derefter på **Opret**.

4. Oprette blade på den nye HDInsight klynge, skrive dit **Navn** , og vælg det **abonnement** , du vil tildele denne ressource under.

    <table border='1'>
        <tr><td>Klyngenavn</td><td>Navnet på klynge.<br/>
   DNS-navnet skal starte og afslutte med et alfanumeriske tegn og kan indeholde bindestreger.<br/>
   Feltet skal være en streng mellem 3 og 63 tegn.</td></tr>
        <tr><td>Navn på abonnement</td>
            <td>Hvis du har mere end ét Azure-abonnement, skal du markere det abonnement, der er vært for din HDInsight klynge. </td></tr>
    </table>

5. Klik på **Vælg typen af klynge** og angive følgende egenskaber til de angivne værdier.

    <table border='1'>
        <tr><td>Klynge type</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Klynge niveau</td><td><strong>Standard</strong></td></tr>
        <tr><td>Operativsystem</td><td><strong>Windows</strong></td></tr>
        <tr><td>Version</td><td>seneste version</td></tr>
    </table>

    Klik nu på **Vælg**.

    ![Angiv Hadoop HDInsight indledende klynge oplysninger][image-customprovision-page1]

6. Klik på **legitimationsoplysninger** til at angive dit login og fjernadgang til legitimationsoplysninger. Vælge din **klynge Login brugernavn** og **din adgangskode klynge Login**.

    Hvis du vil remote til din klynge, skal du vælge *Ja* i bunden af bladet og angive et brugernavn og adgangskode.

7. Klik på **Datakilden** for at angive din primære placering for dataadgang. Vælg den **Markerede metode** og angive en allerede eksisterende lagerplads-konto eller oprette en ny.

8. Angiv en **Standard objektbeholder** og en **placering**på den samme blade. Og klikke på **Vælg**.

    > [AZURE.NOTE] Vælg en placering tæt på dit DocumentDB konto område for at forbedre ydeevnen

8. Klik på **priser** til at vælge antallet og typen af knuder. Du kan holde standardkonfiguration og skalere antallet af knuder, arbejder senere.

9. Klik på **valgfri konfiguration**, og klik derefter **scripthandlinger** i bladet valgfri konfiguration.

    Angiv følgende oplysninger for at tilpasse din HDInsight klynge i scripthandlinger.

    <table border='1'>
        <tr><th>Egenskaben</th><th>Værdi</th></tr>
        <tr><td>Navn</td>
            <td>Angiv et navn for handlingen script.</td></tr>
        <tr><td>Script URI</td>
            <td>Angiv URI til det script, der er aktiveret for at tilpasse klyngen.</br></br>
   Angiv: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
        <tr><td>I afsnit</td>
            <td>Klik på afkrydsningsfeltet for at køre PowerShell-scriptet til noden i afsnit.</br></br>
            <strong>Markér dette afkrydsningsfelt</strong>.</td></tr>
        <tr><td>Arbejder</td>
            <td>Klik på afkrydsningsfeltet for at køre PowerShell-scriptet til noden arbejder.</br></br>
            <strong>Markér dette afkrydsningsfelt</strong>.</td></tr>
        <tr><td>Zookeeper</td>
            <td>Klik på afkrydsningsfeltet for at køre PowerShell-scriptet på Zookeeper.</br></br>
   Der          <strong>ikke er nødvendigt</strong>.
            </td></tr>
        <tr><td>Parametre</td>
            <td>Angiv parametrene, hvis det er nødvendigt ved scriptet.</br></br>
            <strong>Ingen parametre det er nødvendigt</strong>.</td></tr>
    </table>

10. Opret enten en ny **Ressourcegruppe** eller brug en eksisterende ressourcegruppe under abonnementet Azure.

11. Nu se **Fastgør til dashboard** for at registrere installationen, og klik på **Opret**!

## <a name="InstallCmdlets"></a>Trin 2: Installere og konfigurere Azure PowerShell

1. Installer Azure PowerShell. Du kan finde instruktioner [her][powershell-install-configure].

    > [AZURE.NOTE] Du kan også bruge Hdinsight's online Hive Editor lige for Hive forespørgsler. Log på [Azure Portal]for at gøre det,[azure-portal], klikke på **HDInsight** i venstre rude for at få vist en liste over dine HDInsight klynger. Klik på den klynge, du vil køre Hive forespørgsler, og derefter klikke på **Forespørgsel Console**.

2. Åbn det integrerede Scripting Azure PowerShell-miljø:
    - På en computer, der kører Windows 8 eller Windows Server 2012 eller nyere, kan du bruge den indbyggede søgning. Skriv **powershell ise** fra startskærmen, og klik på **Enter**.
    - På en computer med en version tidligere end Windows 8 eller Windows Server 2012 kan du bruge menuen Start. Fra menuen Start, skriver **kommandoprompt** i søgefeltet, og klik derefter på listen over resultater skal du klikke på **kommandoprompt**. Skriv **powershell_ise** kommandoprompten, og klik på **Enter**.

3. Tilføje din Azure-konto.
    1. Skriv **Tilføj AzureAccount** konsoltræet detaljeruden, og klik på **Enter**.
    2. Skriv den mailadresse, der er knyttet til abonnementet Azure, og klik på **Fortsæt**.
    3. Skriv i feltet adgangskode til dit Azure-abonnement.
    4. Klik på **Log på**.

4. I følgende diagram identificeres de vigtigste dele af dit Azure PowerShell Scripting miljø.

    ![Diagram til Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Trin 3: Køre et Hive-job ved hjælp af DocumentDB og HDInsight

> [AZURE.IMPORTANT] Alle variabler, der er angivet med sammen skal angives på ved hjælp af indstillingerne for konfigurationen.

1. Angiv følgende variabler i din PowerShell-Script rude.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"

2. <p>Lad os starte bygning af din forespørgselsstreng. Vi vil skrive en Hive-forespørgsel, der tager alle dokumenter genereres af systemet tidsstempler (_ts) og entydige id'er (_rid) fra en DocumentDB samling, sammenlægges alle dokumenter ved minuttet, og derefter gemmer resultaterne tilbage til en ny DocumentDB samling.</p>

    <p>Først skal oprette en Hive-tabel i vores DocumentDB samling. Tilføj følgende kodestykke til PowerShell-Script ruden <strong>Når</strong> kodestykket fra #1. Sørg for, at du kan medtage valgfrit DocumentDB.query parameter t trim vores dokumenter til kun _ts og _rid.</p>

    > [AZURE.NOTE]**Navngivning af DocumentDB.inputCollections er ikke en fejl.** Ja, vi giver mulighed for at tilføje flere websteder som input: </br>

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.


        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Dernæst skal oprette en Hive-tabel til samlingen output. Dokumentegenskaber output bliver måned, dag, time, minut og det samlede antal forekomster.

    > [AZURE.NOTE]**Endnu en gang, navngive DocumentDB.outputCollections ikke var en fejl.** Ja, vi giver mulighed for at tilføje flere websteder som output: </br>
'*DocumentDB.outputCollections*'='*\<DocumentDB Output samlingsnavnet 1\>*,*\<DocumentDB Output samlingsnavnet 2\>*' </br> Samling navnene adskilles uden mellemrum, ved hjælp af et enkelt komma. </br></br>
Dokumenter bliver fordelt round robin-på tværs af flere websteder. En mængde dokumenter gemmes i én af websteder, og derefter en anden mængde dokumenter gemmes i næste indsamling og så videre.

        # Create a Hive table for the output data to DocumentDB.
        $queryStringPart2 = "drop table DocumentDB_analytics; " +
                              "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                              "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                              "tblproperties ( " +
                                  "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                  "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                  "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                  "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "

4. Til sidst skal Lad os stemmer overens dokumenterne ved måned, dag, time og minut og indsætte resultaterne tilbage i output Hive-tabellen.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "

5. Tilføj de følgende script kodestykke for at oprette en definition af Hive job fra den forrige forespørgsel.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Du kan også bruge parameteren - fil parameter, der angiver en HiveQL scriptfil på HDFS.

6. Tilføj følgende kodestykke for at gemme starttidspunktet og sende Hive jobbet.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7. Tilføj følgende for at vente på kørslen Hive at fuldføre.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8. Tilføj følgende for at udskrive standard output, og start- og sluttidspunkter.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Køre** scriptet nyt! **Klik på** knappen grønne execute.

10. Kontrollere resultaterne. Log på [Azure Portal][azure-portal].
    1. Klik på <strong>Gennemse</strong> i panelet i venstre side. </br>
    2. Klik på <strong>det hele</strong> på øverst til højre i panelet Gennemse. </br>
    3. Find og klik på <strong>DocumentDB konti</strong>. </br>
    4. Find derefter din <strong>DocumentDB konto</strong>, og derefter <strong>DocumentDB Database</strong> og din <strong>DocumentDB af websteder</strong> , der er knyttet til den output af websteder, der er angivet i forespørgslen Hive.</br>
    5. Klik til sidst på <strong>Dokument Explorer</strong> under <strong>Udviklerværktøjer</strong>.</br></p>

    Du får vist resultaterne af forespørgslen Hive.

    ![Hive forespørgselsresultater][image-hive-query-results]

## <a name="RunPig"></a>Trin 4: Køre et gris job ved hjælp af DocumentDB og HDInsight

> [AZURE.IMPORTANT] Alle variabler, der er angivet med sammen skal angives på ved hjælp af indstillingerne for konfigurationen.

1. Angiv følgende variabler i din PowerShell-Script rude.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"

2. <p>Lad os starte bygning af din forespørgselsstreng. Vi vil skrive en gris forespørgsel, der tager alle dokumenter genereres af systemet tidsstempler (_ts) og entydige id'er (_rid) fra en DocumentDB samling, sammenlægges alle dokumenter ved minuttet, og derefter gemmer resultaterne tilbage til en ny DocumentDB samling.</p>
    <p>Først skal du indlæse dokumenter fra DocumentDB i HDInsight. Tilføj følgende kodestykke til PowerShell-Script ruden <strong>Når</strong> kodestykket fra #1. Sørg for at tilføje en DocumentDB forespørgsel til det valgfrit DocumentDB forespørgselsparameter trimme vores dokumenter til kun _ts og _rid.</p>

    > [AZURE.NOTE]Ja, vi giver mulighed for at tilføje flere websteder som input: </br>
'*\<DocumentDB Input samlingsnavnet 1\>*,*\<DocumentDB Input samlingsnavnet 2\>*'</br> Samling navnene adskilles uden mellemrum, ved hjælp af et enkelt komma. </b>

    Dokumenter bliver fordelt round robin-på tværs af flere websteder. En mængde dokumenter gemmes i én af websteder, og derefter en anden mængde dokumenter gemmes i næste indsamling og så videre.

        # Load data from DocumentDB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

3.  Næste, Lad os stemmer overens dokumenterne ved måneden, dag, time, minut og det samlede antal forekomster.

        # GROUP BY minute and COUNT entries for each.
        $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                            "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                            "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "

4. Til sidst, Lad os gemme resultaterne i vores nye output af websteder.

    > [AZURE.NOTE]Ja, vi giver mulighed for at tilføje flere websteder som output: </br>
'\<DocumentDB Output samlingsnavnet 1\>,\<DocumentDB Output samlingsnavnet 2\>'</br> Samling navnene adskilles uden mellemrum, ved hjælp af et enkelt komma.</br>
Dokumenter bliver fordelt round robin-på tværs af flere websteder. En mængde dokumenter gemmes i én af websteder, og derefter en anden mængde dokumenter gemmes i næste indsamling og så videre.

        # Store output data to DocumentDB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "

5. Tilføj de følgende script kodestykke for at oprette en definition af gris job fra den forrige forespørgsel.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Du kan også bruge parameteren - fil parameter, der angiver en gris scriptfil på HDFS.

6. Tilføj følgende kodestykke for at gemme starttidspunktet og sende gris jobbet.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

7. Tilføj følgende for at vente på kørslen gris at fuldføre.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8. Tilføj følgende for at udskrive standard output, og start- og sluttidspunkter.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

9. **Køre** scriptet nyt! **Klik på** knappen grønne execute.

10. Kontrollere resultaterne. Log på [Azure Portal][azure-portal].
    1. Klik på <strong>Gennemse</strong> i panelet i venstre side. </br>
    2. Klik på <strong>det hele</strong> på øverst til højre i panelet Gennemse. </br>
    3. Find og klik på <strong>DocumentDB konti</strong>. </br>
    4. Find derefter din <strong>DocumentDB konto</strong>, og derefter <strong>DocumentDB Database</strong> og din <strong>DocumentDB af websteder</strong> , der er knyttet til den output af websteder, der er angivet i forespørgslen gris.</br>
    5. Klik til sidst på <strong>Dokument Explorer</strong> under <strong>Udviklerværktøjer</strong>.</br></p>

    Du får vist resultaterne af forespørgslen gris.

    ![Gris forespørgselsresultater][image-pig-query-results]

## <a name="RunMapReduce"></a>Trin 5: Køre en MapReduce sag ved hjælp af DocumentDB og HDInsight

1. Angiv følgende variabler i din PowerShell-Script rude.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

2. Vi vil udføre en MapReduce job, der tæller antallet af forekomster for hver dokumentegenskab fra din DocumentDB samling. Tilføje denne script kodestykke **efter** den kodestykke ovenfor.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

    > [AZURE.NOTE] TallyProperties v01.jar leveres med den tilpassede installation af DocumentDB Hadoop-forbindelse.

3. Tilføj følgende kommando for at sende MapReduce jobbet.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Ud over MapReduce jobdefinition, kan du også angive den HDInsight klyngenavn, hvor du vil køre kørslen MapReduce og legitimationsoplysningerne. Start-AzureHDInsightJob er et asynkrone opkald. Brug cmdlet'en *Vent AzureHDInsightJob* for at kontrollere udførelse af opgave.

4. Tilføj følgende kommando for at kontrollere fejl med kørslen MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green

5. **Køre** scriptet nyt! **Klik på** knappen grønne execute.

6. Kontrollere resultaterne. Log på [Azure Portal][azure-portal].
    1. Klik på <strong>Gennemse</strong> i panelet i venstre side.
    2. Klik på <strong>det hele</strong> på øverst til højre i panelet Gennemse.
    3. Find og klik på <strong>DocumentDB konti</strong>.
    4. Find derefter din <strong>DocumentDB konto</strong>, og derefter <strong>DocumentDB Database</strong> og din <strong>DocumentDB af websteder</strong> , der er knyttet til den output af websteder, der er angivet i MapReduce tingene.
    5. Klik til sidst på <strong>Dokument Explorer</strong> under <strong>Udviklerværktøjer</strong>.

    Du får vist resultaterne af MapReduce tingene.

    ![MapReduce forespørgselsresultater][image-mapreduce-query-results]

## <a name="NextSteps"></a>Næste trin

Tillykke! Du lige har kørt dine første Hive, gris og MapReduce sager ved hjælp af Azure DocumentDB og HDInsight.

Vi har Åbn leveres vores Hadoop-forbindelse. Hvis du er interesseret, du kan bidrage på [GitHub][documentdb-github].

Hvis du vil vide mere, skal du se følgende artikler:

- [Udvikle en Java-program med Documentdb][documentdb-java-application]
- [Udvikle Java MapReduce programmer til Hadoop i HDInsight][hdinsight-develop-deploy-java-mapreduce]
- [Introduktion til brug af Hadoop med Hive i HDInsight til at analysere mobile håndsæt brug][hdinsight-get-started]
- [Bruge MapReduce med HDInsight][hdinsight-use-mapreduce]
- [Bruge Hive med HDInsight][hdinsight-use-hive]
- [Brug gris med HDInsight][hdinsight-use-pig]
- [Tilpasse HDInsight klynger ved hjælp af scripthandling][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/documentdb-run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[documentdb-hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[documentdb-github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[documentdb-manage-collections]: documentdb-manage.md#Collections
[documentdb-manage-document-storage]: documentdb-manage.md#IndexOverhead
[documentdb-manage-throughput]: documentdb-manage.md#ProvThroughput
[documentdb-import-data]: documentdb-import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md#powershell
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/documentdb-run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/documentdb-run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: ../powershell-install-configure.md
