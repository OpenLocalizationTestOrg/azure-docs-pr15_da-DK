<properties
   pageTitle="Bruge Hadoop gris med PowerShell i HDInsight | Microsoft Azure"
   description="Lær at sende gris job til et Hadoop-klynge på ved hjælp af PowerShell Azure HDInsight."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Køre gris job ved hjælp af PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Dette dokument indeholder et eksempel på brug af Azure PowerShell til at sende gris job til et Hadoop på HDInsight klynge. Gris kan du skrive MapReduce job ved hjælp af et sprog (gris latinsk), der viser datatransformationer, i stedet for at tilknytte og reducere funktioner.

> [AZURE.NOTE] Dette dokument indeholder ikke en detaljeret beskrivelse af, hvad gris latinsk sætninger, der bruges i eksemplerne gøre. Du kan finde oplysninger om gris latinsk i dette eksempel bruges [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende.

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Køre gris job ved hjælp af PowerShell

Azure PowerShell indeholder *cmdletter* , så du kan køre fra en fjernplacering gris job på HDInsight. Internt opnås dette ved hjælp af RESTEN opkald til [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidligere kaldet Templeton), der kører på HDInsight klyngen.

Følgende cmdletter bruges, når der benyttes gris job en remote HDInsight klynge:

* **Logon-AzureRmAccount**: godkender Azure PowerShell til at abonnementet Azure

* **Ny AzureRmHDInsightPigJobDefinition**: opretter en ny *definition af job* ved hjælp af de angivne gris latinsk sætninger

* **Start-AzureRmHDInsightJob**: sender jobbet definitionen til HDInsight, starter jobbet og returnerer et *job* objekt, der kan bruges til at kontrollere status for jobbet

* **Vent-AzureRmHDInsightJob**: bruger jobobjekt til at kontrollere status for jobbet. Det skal vente, indtil jobbet er fuldført eller ventetiden er overskredet.

* **Get-AzureRmHDInsightJobOutput**: bruges til at hente output fra jobbet

Følgende trin viser, hvordan du bruger disse cmdlet'er til at køre et job på din HDInsight klynge.

1. Følgende kode ved hjælp af en editor, Gem som **pigjob.ps1**. Du skal erstatte **CLUSTERNAME** med navnet på din HDInsight klynge.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Åbn en ny Windows PowerShell-kommandoprompt. Skifte til placeringen af filen **pigjob.ps1** , og derefter bruge følgende kommando til at køre scriptet:

        .\pigjob.ps1
        
    Du skal først bedt om at logge på abonnementet Azure. Derefter bliver du spurgt til HTTPs/Admin kontonavnet og adgangskoden for HDInsight-klyngen.

7. Når jobbet er fuldført, at den skal returnere oplysninger som følger:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis der returneres ingen oplysninger, når jobbet er fuldført, kan der opstod en fejl under behandling. For at få vist oplysninger om fejlen i sagen, tilføje følgende kommando til slutningen af filen **pigjob.ps1** , gemme den og derefter køre den igen.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Dette vil returnere de oplysninger, der er skrevet til STDERR på serveren, når du har kørt jobbet, og det kan hjælpe med at finde ud af, hvorfor jobbet går ned.

##<a id="summary"></a>Oversigt

Som du kan se, indeholder Azure PowerShell en nem måde at køre gris job på en HDInsight klynge, overvåge jobstatus og hente output.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om gris i HDInsight:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)
