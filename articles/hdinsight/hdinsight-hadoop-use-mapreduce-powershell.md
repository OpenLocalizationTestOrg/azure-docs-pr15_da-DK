<properties
   pageTitle="Brug MapReduce og PowerShell med Hadoop | Microsoft Azure"
   description="Lær at bruge PowerShell til at køre MapReduce job med Hadoop på HDInsight."
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Køre MapReduce job med Hadoop på HDInsight ved hjælp af PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dette dokument indeholder et eksempel på brug af Azure PowerShell til at køre en MapReduce sag i et Hadoop på HDInsight klynge.

##<a id="prereq"></a>Forudsætninger

For at fuldføre trinnene i denne artikel, skal du følgende:

- **En Azure HDInsight (Hadoop på HDInsight) klynge (Windows-baserede eller Linux-baseret)**

- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Køre et MapReduce job fra Azure PowerShell

Azure PowerShell indeholder *cmdletter* , så du kan køre fra en fjernplacering MapReduce job på HDInsight. Internt opnås dette ved hjælp af RESTEN opkald til [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidligere kaldet Templeton), der kører på HDInsight klyngen.

Følgende cmdletter anvendes, når du kører MapReduce job i en ekstern HDInsight klynge.

* **Logon-AzureRmAccount**: godkender Azure PowerShell til at abonnementet Azure

* **Ny AzureRmHDInsightMapReduceJobDefinition**: opretter en ny *definition af job* ved hjælp af de angivne MapReduce oplysninger

* **Start-AzureRmHDInsightJob**: sender jobbet definitionen til HDInsight, starter jobbet og returnerer et *job* objekt, der kan bruges til at kontrollere status for jobbet

* **Vent-AzureRmHDInsightJob**: bruger jobobjekt til at kontrollere status for jobbet. Det skal vente, indtil jobbet er fuldført eller ventetiden overskrides.

* **Get-AzureRmHDInsightJobOutput**: bruges til at hente output fra jobbet

Følgende trin viser, hvordan du bruger disse cmdlet'er til at køre et job i din HDInsight klynge.

1. Følgende kode ved hjælp af en editor, Gem som **mapreducejob.ps1**. Du skal erstatte **CLUSTERNAME** med navnet på din HDInsight klynge.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Åbn en ny **Azure PowerShell** kommandoprompt. Skifte til placeringen af filen **mapreducejob.ps1** , og derefter bruge følgende kommando til at køre scriptet:

        .\mapreducejob.ps1
    
    Når du kører scriptet, kan du bedt om at godkende til dit Azure-abonnement. Du vil også blive bedt om at angive HTTPS/Admin kontonavn og adgangskoden for HDInsight-klyngen.

3. Når jobbet er fuldført, skal du modtager output, der svarer til følgende:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Dette output angiver, at jobbet blev fuldført.

    > [AZURE.NOTE] Hvis den **ExitCode** er en værdi end 0, se [fejlfinding](#troubleshooting).

    I dette eksempel vil også gemme de hentede filer til en **output.txt** fil i den mappe, som du køre scriptet fra.

###<a name="view-output"></a>Vis output

Åbn filen **output.txt** i et tekstredigeringsprogram for at se de ord og tæller produceret af jobbet.

> [AZURE.NOTE] Outputfiler på en MapReduce sag kan ikke ændres. Hvis du kører igen i dette eksempel, skal du så ændre navnet på outputfilen.

##<a id="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis der returneres ingen oplysninger, når jobbet er fuldført, kan der opstod en fejl under behandling. For at få vist oplysninger om fejlen i sagen, tilføje følgende kommando til slutningen af filen **mapreducejob.ps1** , gemme den og derefter køre den igen.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Dette returnerer de oplysninger, der er skrevet til STDERR på serveren, når du har kørt jobbet, og det kan hjælpe med at finde ud af, hvorfor jobbet går ned.

##<a id="summary"></a>Oversigt

Som du kan se, indeholder Azure PowerShell en nem måde at køre MapReduce job på en HDInsight klynge, overvåge jobstatus og hente output.

##<a id="nextsteps"></a>Næste trin

Du kan finde generelle oplysninger om MapReduce sager i HDInsight:

* [Bruge MapReduce på HDInsight Hadoop](hdinsight-use-mapreduce.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)
