<properties
    pageTitle="Brug Hadoop Sqoop i HDInsight | Microsoft Azure"
    description="Lær at bruge Azure PowerShell fra en arbejdsstation til at køre Sqoop importere og eksportere mellem en Hadoop-klynge og Azure SQL-database."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight"></a>Bruge Sqoop med Hadoop i HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Lær, hvordan du bruger Sqoop i HDInsight til at importere og eksportere mellem HDInsight klynge og Azure SQL-database eller SQL Server-database.

Selvom Hadoop er et neutralt valg til at behandle ustrukturerede og semistructured data, som logfiler og filer, kan der også være behov for at behandle strukturerede data, der er gemt i relationsdatabaser.

[Sqoop] [ sqoop-user-guide-1.4.4] er et værktøj, der er udviklet til at overføre data mellem Hadoop klynger og relationsdatabaser. Du kan bruge det til at importere data fra en relationsdatabase administrationssystem (RDBMS), såsom SQL Server, MySQL eller Oracle til Hadoop-distribueret filsystem (HDFS), transformere dataene i Hadoop med MapReduce eller Hive, og Eksportér derefter dataene i en RDBMS igen. I dette selvstudium bruger du en SQL Server-database til din relationsdatabase.

Sqoop versioner, der understøttes på HDInsight klynger, se [Nyheder i de klynge versioner, der leveres af HDInsight?] [hdinsight-versions].

##<a name="understand-the-scenario"></a>Forstå dette scenario

HDInsight klynge følger med nogle eksempeldata. Du kan bruge følgende to eksempler:

- En log4j logfil, der er placeret */example/data/sample.log*. Følgende loggene udtrækkes fra filen:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

- En Hive-tabel med navnet *hivesampletable*, der henviser til den datafil, findes i */hive/warehouse/hivesampletable*. Tabellen indeholder nogle mobilenhed data. 

  	| Felt | Datatype |
  	| ----- | --------- |
  	| clientid | streng |
  	| querytime | streng |
  	| marked | streng |
  	| deviceplatform | streng |
  	| devicemake | streng |
  	| devicemodel | streng |
  	| stat | streng |
  	| land | streng |
  	| querydwelltime | dobbelt |
  	| Session-id | bigint |
  	| sessionpagevieworder | bigint |

Du kan eksportere først *sample.log* og *hivesampletable* til Azure SQL-database eller til SQL Server, og derefter importere den tabel, der indeholder dataene, mobilenhed tilbage til HDInsight ved hjælp af følgende sti:

    /tutorials/usesqoop/importeddata

## <a name="create-cluster-and-sql-database"></a>Oprette klynge og SQL-database

Dette afsnit viser, hvordan du opretter en klynge og SQL-databaseskemaer til at køre selvstudiet ved hjælp af portalen Azure og en Azure ressourcestyring skabelon.  Hvis du foretrækker at bruge Azure PowerShell, kan du se [tillæg A](#appendix-a---a-powershell-sample).

1. Klik på følgende billede for at åbne en ressource manager skabelon i portalen Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Skabelonen ressource manager er placeret i en offentlig blob beholder, *https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*. 
    
    Skabelonen ressource manager kalder en bacpac pakke for at installere tabel skemaerne til SQL-database.  Pakken bacpac findes også i en offentlig blob beholder, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Hvis du vil bruge en privat objektbeholder til bacpac-filer, kan du bruge følgende værdier i skabelonen:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. Angiv følgende oplysninger fra bladet parametre:

    - **ClusterName**: Angiv et navn til den Hadoop-klynge, du vil oprette.
    - **Klynge login-navn og din adgangskode**: login standardnavnet er administrator.
    - **SSH brugernavn og din adgangskode**.
    - **SQL-database server login-navn og din adgangskode**.

    Følgende værdier er hårdt i sektionen variabler:
    
  	|Standard kontonavn til lager|<CluterName>store|
  	|----------------------------|-----------------|
  	|Azure SQL server-databasenavn|<ClusterName>dbserver|
  	|Azure SQL-databasenavn|<ClusterName>DB|
    
    Skriv disse værdier.  Du skal bruge dem senere i selvstudiet.
    
3. Klik på **OK** for at gemme parametrene.

4. Klik på rullelisten **ressourcegruppe** fra bladet **brugerdefineret installation** , og klik derefter på **Ny** for at oprette en ny ressourcegruppe. Ressourcegruppen er en objektbeholder, der grupperer klyngen, kontoen afhængige lagerplads og andre sammenkædede ressource.

5. skal du klikke på **juridiske begreber**, og klik derefter på **Opret**.

6. Klik på **Opret**. Du får vist et nyt felt med titlen Submitting installation til installation af skabelon. Det tager om omkring 20 minutter til at oprette klynge og SQL-database.

Hvis du vælger at bruge eksisterende Azure SQL-database eller Microsoft SQL Server

- **Azure SQL-database**: skal du konfigurere en firewallregel for Azure SQL-databaseserveren mulighed for at tillade adgang fra computeren. Oplysninger om oprettelse af en Azure SQL database og konfiguration af firewall, se [Introduktion til brug af Azure SQL-database][sqldatabase-get-started]. 

    > [AZURE.NOTE] Som standard kan en Azure SQL-database forbindelser fra Azure tjenester, som Azure HDInsight. Hvis denne indstilling af firewall er deaktiveret, skal du har aktiveret den fra Azure-portalen. Anvisninger om oprettelse af en Azure SQL-database og konfiguration af firewallregler, skal du se [oprette og konfigurere SQL-Database][sqldatabase-create-configue].

- **SQL Server**: Hvis din HDInsight klynge er på det samme virtuelle netværk i Azure som SQL Server, kan du bruge trinnene i denne artikel for at importere og eksportere data til en SQL Server-database.

    > [AZURE.NOTE] HDInsight understøtter placering-baserede kun virtuelle netværk, og den virker ikke i øjeblikket med forbindelse gruppe-baserede virtuelle netværk.

    * For at oprette og konfigurere et virtuelt netværk skal du se [oprette et virtuelt netværk ved hjælp af portalen Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

        * Når du bruger SQL Server i dit datacenter, skal du konfigurere det virtuelle netværk som *- til-websted* eller *punkt-til-websted*.

            > [AZURE.NOTE] Til **punkt-til-site** virtuelle netværk, skal SQL Server køre VPN-klient konfigurationsprogrammet, som er tilgængelig fra **Dashboard** af din Azure virtuelle netværkskonfiguration.

        * Når du bruger SQL Server på en Azure virtuel maskine, kan et virtuelt netværkskonfiguration bruges, hvis den virtuelle maskine, der er vært for SQL Server er medlem af det samme virtuelle netværk som HDInsight.

    * For at oprette en HDInsight klynge i et virtuelt netværk skal du se [oprette Hadoop klynger i HDInsight ved hjælp af brugerdefinerede indstillinger](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server skal også tillade godkendelse. Du skal bruge en SQL Server-logon til at fuldføre trinnene i denne artikel.


## <a name="run-sqoop-jobs"></a>Køre Sqoop job

HDInsight kan køre Sqoop job ved hjælp af en række forskellige metoder. Brug tabellen nedenfor til at afgøre, hvilken metode der passer til dine og derefter følge linket for en gennemgang.

| **Brug denne indstilling** , hvis du vil...                                   | .. .an **interaktive** shell | ... **batchbehandling** | .. .with **klynge operativsystem** | .. .from **klientens operativsystem** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md)                        |              ✔              |            ✔            | Linux                                     | Linux, Unix, Mac OS X eller Windows        |
| [.NET SDK for Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux eller Windows                          | Windows (for nu)                        |
| [Azure PowerShell](hdinsight-hadoop-use-sqoop-powershell.md)  |           &nbsp;            |            ✔            | Linux eller Windows                          | Windows                                  |

##<a name="limitations"></a>Begrænsninger

* Flere eksport - med Linux-baserede HDInsight, Sqoop forbindelsen bruges til at eksportere data til Microsoft SQL Server eller Azure SQL-Database understøtter i øjeblikket ikke flere indsættes.

* "Samling"-med Linux-baserede HDInsight, når du bruger den `-batch` skifte, når du udfører indsættes, Sqoop udfører flere indsættes i stedet for "samling" Indsæt handlinger.

##<a name="next-steps"></a>Næste trin

Nu har du lært, hvordan du bruger Sqoop. Hvis du vil vide mere, skal du se:

- [Bruge Hive med HDInsight](hdinsight-use-hive.md)
- [Brug gris med HDInsight](hdinsight-use-pig.md)
- [Bruge Oozie med HDInsight][hdinsight-use-oozie]: Brug Sqoop handling i en arbejdsproces for Oozie.
- [Analysere flight forsinkelse data ved hjælp af HDInsight][hdinsight-analyze-flight-data]: Brug Hive til at analysere flight forsinke data, og brug derefter Sqoop til at eksportere data til en Azure SQL-database.
- [Overføre data til HDInsight][hdinsight-upload-data]: finde andre metoder til at overføre data til HDInsight/Azure Blob-lager.


## <a name="appendix-a---a-powershell-sample"></a>Tillæg A - en PowerShell-eksempel

Eksemplet PowerShell udfører følgende trin:

1. Oprette forbindelse til Azure.
2. Opret en Azure ressourcegruppe. Du kan finde flere oplysninger i [Bruge PowerShell for Azure med Azure ressourcestyring](../powershell-azure-resource-manager.md)
3. Oprette en Azure SQL-databaseserver, Azure SQL-database og to tabeller. 

    Hvis du bruger SQL Server i stedet skal du bruge følgende udtalelser til at oprette tabellerne:
    
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))

        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])

    Den nemmeste måde at undersøge database og tabeller er at bruge Visual Studio. Databaseserveren og databasen kan undersøges ved hjælp af portalen Azure.

4. Oprette en HDInsight klynge.

    For at undersøge klyngen, kan du bruge Azure portal eller Azure PowerShell.

5. Udfyldt behandle data kildefilen.

    I dette selvstudium skal du eksportere en log4j logfil (en tabulatorsepareret fil) og en Hive-tabel til en Azure SQL-database. Afgrænset fil kaldes */example/data/sample.log*. Tidligere i selvstudiet så du et par eksempler på log4j logfiler. I logfilen er der nogle tomme linjer og nogle linjer som disse:
    
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
    
    Det fungerer fint i andre eksempler, der bruger disse data, men vi skal fjerne disse undtagelser, før vi kan importere til Azure SQL-database eller SQL Server. Sqoop Eksportér mislykkes, hvis der er en tom streng eller en linje med en færre antal elementer end antallet af felter, der er defineret i tabellen Azure SQL-database. Tabellen log4jlogs indeholder 7 strengtypen felter.

    Denne procedure opretter en ny fil på klyngen: tutorials/usesqoop/data/sample.log. Hvis du vil undersøge filen ændrede data, kan du bruge portalen Azure, et værktøj til Azure-lager explorer eller Azure PowerShell. [Komme i gang med HDInsight] [ hdinsight-get-started] har en eksempel-kode til brug af Azure PowerShell til at hente en fil og vise indholdet af filen.

6. Eksportere en datafil til Azure SQL-database.

    Filen er tutorials/usesqoop/data/sample.log. Den tabel, hvor dataene er eksporteret til kaldes log4jlogs.
    
    > [AZURE.NOTE] End oplysningerne om forbindelsesstrengen, skal trinnene i dette afsnit bruges til Azure SQL-database eller til SQL Server. Disse trin er testet ved hjælp af følgende konfiguration:
    >
    > * **Azure virtuelle punkt-til-site netværkskonfiguration**: et virtuelt netværk forbundet HDInsight klynge til en SQL Server i en privat datacenter. Du kan se [konfigurere et punkt-til-Site VPN i portalen Management](../vpn-gateway/vpn-gateway-point-to-site-create.md) kan finde flere oplysninger.
    > * **Azure HDInsight 3.1**: Du kan finde oplysninger om oprettelse af en klynge i et virtuelt netværk [oprette Hadoop klynger i HDInsight ved hjælp af brugerdefinerede indstillinger](hdinsight-provision-clusters.md) .
    > * **SQL Server 2014**: konfigureret til at tillade godkendelse og kører VPN-klient konfigurationspakke til at oprette sikker forbindelse til det virtuelle netværk.

7. Eksportere en Hive-tabellen til Azure SQL-database.

8. Importere tabellen mobiledata til HDInsight klynge.

    Hvis du vil undersøge filen ændrede data, kan du bruge portalen Azure, et værktøj til Azure-lager explorer eller Azure PowerShell.  [Komme i gang med HDInsight] [ hdinsight-get-started] er et eksempel på kode om brug af Azure PowerShell til at hente en fil og vise indholdet af filen.


### <a name="the-powershell-sample"></a>Eksemplet PowerShell

    # Prepare an Azure SQL database to be used by the Sqoop tutorial
    
    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure Subscription ID>"
    
    $sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
    $sqlDatabasePassword = "<Enter a Password>"
    
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    
    # used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # Used for creating tables and clustered indexes
    $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
        [t1] [nvarchar](50),
        [t2] [nvarchar](50),
        [t3] [nvarchar](50),
        [t4] [nvarchar](50),
        [t5] [nvarchar](50),
        [t6] [nvarchar](50),
        [t7] [nvarchar](50))"
    
    $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
    
    $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder][bigint])"
    
    $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $credential `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    
    #endregion
    
    #region - Create tables
    Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jTable
    $ret = $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateLog4jClusteredIndex
    $cmd.ExecuteNonQuery()
    
    # Create the mobiledata table and index
    $cmd.CommandText = $cmdCreateMobileTable
    $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    
    #endregion
    
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
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
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - pre-process the source file
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
    
    # Define the connection string
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
    
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
    
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
    
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
    
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
    
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
    
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
    
    #endregion
    
    #region - export a log file from the cluster to the SQL database
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    $tableName_log4j = "log4jlogs"
    
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $exportDir_log4j = "/tutorials/usesqoop/data"
    
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - export a Hive table
    
    $tableName_mobile = "mobiledata"
    $exportDir_mobile = "/hive/warehouse/hivesampletable"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - import a database
    
    $targetDir_mobile = "/tutorials/usesqoop/importeddata/"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
    
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
