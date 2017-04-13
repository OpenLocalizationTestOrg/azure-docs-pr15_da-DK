<properties
    pageTitle="Analysere flight forsinkelse data med Hadoop i HDInsight | Microsoft Azure"
    description="Lær, hvordan du bruger én Windows PowerShell-script til at oprette en HDInsight klynge, kørslen Hive, kørslen Sqoop og slette klyngen."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analysere flight forsinkelse data ved hjælp af Hive i HDInsight

Hive giver mulighed for at køre Hadoop MapReduce job via en SQL-lignende scriptsproget kaldet * [HiveQL][hadoop-hiveql]*, som kan anvendes mod summering af forespørgsler og analysere store datamængder.

> [AZURE.NOTE] Trinnene i dette dokument kræver en Windows-baseret HDInsight klynge. Få en vejledning, der arbejder med en Linux-baserede klynge i [analysér flight forsinkelse data ved hjælp af Hive i HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

En af de vigtigste fordele ved Azure HDInsight er adskillelse af datalagring og Beregn. HDInsight bruger Azure Blob-lager til lagring af data. Et typisk job omfatter tre dele:

1. **Gemme data i Azure Blob-lager.**  For eksempel vejrudsigter data, føler data, web logge, og i dette tilfælde flight forsinkelse data er gemt til Azure Blob-lager.
2. **Køre job.** Når det er tid til at behandle dataene, du kører et Windows PowerShell-script (eller en klientprogrammet) for at oprette en HDInsight klynge køre job, og Slet klyngen. Job Gem outputdata til Azure Blob-lager. Outputdataene bevares, selv efter at klyngen slettes. Denne måde, du betaler for kun hvad, du har brugt.
3. **Hente output fra Azure Blob-lager**, eller i dette selvstudium eksportere dataene til en Azure SQL-database.

I følgende diagram vises dette scenario og strukturen af dette selvstudium:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

**Bemærk**: tallene i diagrammet, der svarer til afsnittene. **M** står for den primære proces. **A** står for indholdet i tillægget.

Den primære del af selvstudiet viser, hvordan du bruger en Windows PowerShell-script til at udføre følgende opgaver:

- Oprette en HDInsight klynge.
- Køre et Hive-job på klynge til at beregne gennemsnitlige forsinkelser i lufthavne. Flight forsinkelse data er gemt i en Azure Blob storage konto.
- Køre et Sqoop job for at eksportere Hive job output til Azure SQL-database.
- Slet HDInsight klyngen.

I appendikser, kan du finde instruktionerne til overførsel af flight forsinkelse data, oprette/overførsel af en Hive-forespørgselsstreng og forberede Azure SQL-database til Sqoop jobbet.

> [AZURE.NOTE] Trinnene i dette dokument er specifikke for Windows-baseret HDInsight klynger. Få en vejledning, der arbejder med en Linux-baserede klynge i [analysér flight forsinkelse data ved hjælp af Hive i HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>Forudsætninger

Inden du starter selvstudiet, skal du have følgende elementer:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**Filer, der bruges i dette selvstudium**

Dette selvstudium bruger til tiden ydeevnen for flydata fra [forskning og Administration af Innovative teknologi, Bureau af transport statistik eller RITA] [rita-website]. En kopi af dataene er blevet overført til en objektbeholder til lagring af Azure Blob med tilladelsen offentlige Blob access. En del af dit PowerShell-script kopierer data fra offentlige blob beholderen til objektbeholderen standard blob af din klynge. Scriptet HiveQL også kopieret til den samme Blob beholder. Hvis du vil lære at få/Overfør data til din egen lagerplads konto, og hvordan du kan oprette/Overfør scriptfil HiveQL, skal du se [tillæg A](#appendix-a) og [B tillæg](#appendix-b).

I følgende tabel vises de filer, der bruges i dette selvstudium:

<table border="1">
<tr><th>Filer</th><th>Beskrivelse</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Bruges af kørslen Hive HiveQL-scriptfil. Dette script er blevet overført til en Azure Blob storage konto med offentlig adgang. <a href="#appendix-b">Tillæg B</a> har instruktioner til, hvordan du forbereder og overføre filen til din egen Azure Blob storage konto.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Indtast data til Hive jobbet. Dataene er blevet overført til en Azure Blob storage konto med offentlig adgang. <a href="#appendix-a">Tillæg A</a> har vejledning i at hente dataene og overføre data til din egen Azure Blob storage konto.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Outputstien til Hive jobbet. Objektbeholderen standard bruges til at lagre outputdata.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Hive job status mappen på objektbeholderen standard.</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>Oprette klynge og køre Hive/Sqoop job

Hadoop MapReduce er batchbehandling. Den mest økonomiske måde at køre et Hive-job er at oprette en klynge til opgaven, og slette jobbet efter jobbet er fuldført. Følgende script dækker hele processen. Du kan finde flere oplysninger om at oprette en HDInsight klynge og kører Hive job se [oprette Hadoop klynger i HDInsight] [ hdinsight-provision] og [Brug Hive med HDInsight][hdinsight-use-hive].

**Køre Hive forespørgsler ved Azure PowerShell**

1. Oprette en Azure SQL-database og tabel Sqoop job output til ved at følge vejledningen i [Tillæg C](#appendix-c).
3. Åbn Windows PowerShell ISE, og Kør følgende script:

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
        # Create the HDInsight cluster
        $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
        $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
        New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -Location $location `
            -ClusterType Hadoop `
            -OSType Windows `
            -ClusterSizeInNodes 2 `
            -HttpCredential $httpCredential `
            -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Oprette forbindelse til din SQL-database, og se gennemsnitlige flyforsinkelser efter by i tabellen AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Tillæg A - Overfør flight forsinkelse data til Azure Blob-lager
Overførsel af datafilen og de HiveQL script-filer (se [Tillæg B](#appendix-b)) kræver nogle planlægning. Ideen er at gemme datafilerne og filen HiveQL før oprette en HDInsight klynge og køre Hive jobbet. Har du to muligheder:

- **Brug den samme Azure-lager-konto, der skal bruges ved HDInsight klynge som standard-filsystemet.** Fordi HDInsight klynge får hurtigtast lagerplads konto, kan du ikke behøver at foretage eventuelle yderligere ændringer.
- **Bruge en anden konto Azure-lager fra HDInsight klynge standard-filsystemet.** Hvis det er tilfældet, skal du redigere oprettelse af en del af den Windows PowerShell-script, der findes i [oprette HDInsight klynge og køre Hive/Sqoop job](#runjob) at linke lagerplads konto som en ekstra lagerplads-konto. Flere oplysninger under [oprette Hadoop klynger i HDInsight][hdinsight-provision]. HDInsight klynge ved derefter hurtigtast for kontoen lagerplads.

>[AZURE.NOTE] Blob storage sti til datafilen er svært kodet i HiveQL script-fil. Du skal opdatere den tilsvarende.

**Hente flight data**

1. Gå til [Opslag og Administration af Innovative teknologi Bureau transport statistik][rita-website].
2. På siden skal du vælge følgende værdier:

    <table border="1">
    <tr><th>Navn</th><th>Værdi</th></tr>
    <tr><td>Filtrere år</td><td>2013 </td></tr>
    <tr><td>Filtrere periode</td><td>Januar</td></tr>
    <tr><td>Felter</td><td>*År*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (Ryd alle andre felter)</td></tr>
    </table>

3. Klik på **Hent**.
4. Udpakke filen til mappen **C:\Tutorials\FlightDelay\2013Data** . Hver fil er en CSV-fil og cirka 60GB i størrelse.
5.  Omdøb filen til navnet på den måned, den indeholder data til. For eksempel vil den fil, der indeholder data, januar kan navngives *January.csv*.
6. Gentag trin 2 og 5 for at hente en fil for hver af 12 månederne i 2013. Du skal mindst én fil til at køre selvstudiet.  

**Overføre flight forsinkelse dataene til Azure Blob-lager**

1. Forberede parametrene:

    <table border="1">
    <tr><th>Variabelnavn</th><th>Noter</th></tr>
    <tr><td>$storageAccountName</td><td>Den Azure-lager-konto, hvor du vil overføre data til.</td></tr>
    <tr><td>$blobContainerName</td><td>Blob objektbeholderen, hvor du vil overføre data til.</td></tr>
    </table>
2. Åbn Azure PowerShell ISE.
3. Indsæt følgende script i ruden script:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. Tryk på **F5** for at køre scriptet.

Hvis du vælger at bruge en anden metode til overførsel af filerne, skal du kontrollere, at stien til filen er flightdelay-selvstudier/data. Syntaksen for at få adgang til filerne er:

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Data om sti selvstudier/flightdelay/er den virtuelle mappe, du har oprettet, når du har overført filerne. Kontrollér, at der er 12 filer, én for hver måned.

>[AZURE.NOTE] Du skal opdatere Hive forespørgslen til at læse fra den nye placering.

> Enten skal du konfigurere adgangsrettigheder objektbeholder til at være offentlig eller binde kontoen lagerplads til HDInsight klynge. Ellers kan forespørgselsstreng Hive ikke få adgang til datafilerne.

---
##<a id="appendix-b"></a>Tillæg B – oprette og overføre et HiveQL script

Ved hjælp af Azure PowerShell, kan du køre flere HiveQL sætninger én ad gangen eller pakke sætningen HiveQL til en scriptfil. Dette afsnit viser, hvordan du opretter et HiveQL script, og Overfør scriptet til Azure Blob-lager ved hjælp af Azure PowerShell. Hive kræver HiveQL-scripts kan være gemt på Azure Blob-lager.

Scriptet HiveQL skal udføre følgende:

1. **Slip tabellen delays_raw**, findes i tilfælde tabellen allerede.
2. **Oprette tabellen delays_raw eksterne Hive** peger på Blob storage placering med flight forsinkelse filer. Denne forespørgsel angiver, at felterne er adskilt af "," og, linjer er afsluttet af "\n". Dette udgør et problem, når feltværdier indeholder kommaer, fordi Hive kan skelne mellem et komma, der er en feltafgrænser og en, der er en del af et feltværdi (som er tilfældet i feltværdier for ORIGIN\_by\_navn og DEST\_by\_navn). Du kan løse dette ved opretter forespørgslen TEMP kolonner for at holde data, der er forkert opdelt i kolonner.  
3. **Slip tabellen forsinkelser**, findes i tilfælde tabellen allerede.
4. **Opret tabellen forsinkelser**. Det er praktisk at rydde op i dataene, før videre behandling. Denne forespørgsel opretter en ny tabel, *forsinkelser*fra tabellen delays_raw. Bemærk, at der ikke er kopieret TEMP kolonnerne (som tidligere nævnt), og, funktionen **understreng** bruges til at fjerne anførselstegn fra dataene.
5. **Beregne den gennemsnitlige vejrudsigt forsinkelse og grupper resultaterne efter by navn.** Det kan også udskrive resultaterne til Blob-lager. Bemærk, at forespørgslen, fjerner apostroffer fra dataene, og vil udelukke de rækker, hvor værdien for **weather_delay** er null. Det er nødvendigt, fordi Sqoop, bruges senere i dette selvstudium problemfrit ikke håndtere disse værdier som standard.

Se en komplet liste over kommandoerne HiveQL [Hive Data Definition Language][hadoop-hiveql]. Hver HiveQL kommando skal afsluttes med et semikolon.

**Oprette en HiveQL script-fil**

1. Forberede parametrene:

    <table border="1">
    <tr><th>Variabelnavn</th><th>Noter</th></tr>
    <tr><td>$storageAccountName</td><td>Hvor du vil overføre HiveQL scriptet til kontoen Azure-lager.</td></tr>
    <tr><td>$blobContainerName</td><td>Hvor du vil overføre HiveQL scriptet til objektbeholderen Blob.</td></tr>
    </table>
2. Åbn Azure PowerShell ISE.

3. Kopiere og indsætte følgende script i ruden script:

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Her er de variabler, der anvendes i scriptet:

    - **$hqlLocalFileName** - scriptet gemmer scriptfil HiveQL lokalt før du overfører til Blob-lager. Dette er filnavnet. Standardværdien er <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
    - **$hqlBlobName** - dette er HiveQL script blob filnavnet bruges i Azure Blob-lager. Standardværdien er tutorials/flightdelay/flightdelays.hql. Da filen skrives direkte til Azure Blob-lager, er der ikke en "/" i starten af blob-navn. Hvis du vil have adgang til filen fra Blob-lager, skal du tilføje en "-" i starten af filnavnet.
    - **$srcDataFolder** og **$dstDataFolder** - = "selvstudier/flightdelay /-data" = "selvstudier/flightdelay/output"


---
##<a id="appendix-c"></a>Tillæg C - forberede en Azure SQL-database til Sqoop job output
**Sådan forbereder du dig SQL-databasen (flette dette med scriptet Sqoop)**

1. Forberede parametrene:

    <table border="1">
    <tr><th>Variabelnavn</th><th>Noter</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>Navnet på Azure SQL-databaseserveren. Angiv dig noget for at oprette en ny server.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>Logonnavn for Azure SQL-databaseserveren. Hvis $sqlDatabaseServerName er en eksisterende server, bruges logon og logonadgangskode til at godkende med serveren. Ellers bruges de til at oprette en ny server.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>Logonadgangskode for Azure SQL-databaseserveren.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Denne værdi bruges kun, når du opretter en ny Azure-databaseserver.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>SQL-database, der bruges til at oprette tabellen AvgDelays for Sqoop sag. Så det er tomt, oprettes en database, kaldet HDISqoop. Tabelnavnet Sqoop job output til er AvgDelays. </td></tr>
    </table>
2. Åbn Azure PowerShell ISE.
3. Kopiere og indsætte følgende script i ruden script:

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] Scriptet bruger en kunne tilstand overførsel (RESTEN) tjeneste, http://bot.whatismyipaddress.com, til at hente din eksterne IP-adresse. IP-adresse bruges til at oprette en regel for SQL-databaseserveren.  

    Her er nogle variabler, som bruges i script:

    - **$ipAddressRestService** - standardværdien er http://bot.whatismyipaddress.com. Det er en offentlig IP-adresse REST-webtjeneste til at få din eksterne IP-adresse. Hvis du vil, kan du bruge andre tjenester. Den eksterne IP-adresse, der er hentet via tjenesten bruges til at oprette en firewallregel for din Azure SQL-databaseserver, så du kan få adgang til databasen fra computeren (ved hjælp af en Windows PowerShell-script).
    - **$fireWallRuleName** - dette er navnet på reglen for Azure SQL-databaseserveren. Standardnavnet er <u>FlightDelay</u>. Du kan omdøbe den, hvis du vil have.
    - **$sqlDatabaseMaxSizeGB** - denne værdi bruges kun, når du opretter en ny Azure SQL-databaseserver. Standardværdien er 10GB. 10GB er tilstrækkelige til dette selvstudium.
    - **$sqlDatabaseName** - denne værdi bruges kun, når du opretter en ny Azure SQL-database. Standardværdien er HDISqoop. Hvis du omdøber den, skal du opdatere Sqoop Windows PowerShell-script i overensstemmelse hermed.

4. Tryk på **F5** for at køre scriptet.
5. Validere script output. Sørg for, at scriptet blev afsluttet.

##<a id="nextsteps"></a>Næste trin
Nu kan du forstå, hvordan du kan overføre en fil til Azure Blob-lager, hvordan du kan udfylde en Hive-tabel ved hjælp af data fra Azure Blob-lager, hvordan du kører Hive forespørgsler og Sådan bruges Sqoop til at eksportere data fra HDFS til Azure SQL-database. Hvis du vil vide mere, skal du se følgende artikler:

* [Introduktion til HDInsight][hdinsight-get-started]
* [Bruge Hive med HDInsight][hdinsight-use-hive]
* [Bruge Oozie med HDInsight][hdinsight-use-oozie]
* [Bruge Sqoop med HDInsight][hdinsight-use-sqoop]
* [Brug gris med HDInsight][hdinsight-use-pig]
* [Udvikle Java MapReduce programmer til HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
