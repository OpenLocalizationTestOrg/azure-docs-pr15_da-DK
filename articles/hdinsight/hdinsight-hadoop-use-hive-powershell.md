<properties
   pageTitle="Bruge Hadoop Hive med PowerShell i HDInsight | Microsoft Azure"
   description="Bruge PowerShell til at køre Hive forespørgsler i Hadoop på HDInsight."
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

#<a name="run-hive-queries-using-powershell"></a>Køre Hive forespørgsler ved hjælp af PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Dette dokument indeholder et eksempel på brug af Azure PowerShell til at køre Hive forespørgsler i en Hadoop på HDInsight klynge i tilstanden Azure ressourcegruppe.

> [AZURE.NOTE] Dette dokument indeholder ikke en detaljeret beskrivelse af, hvad de HiveQL sætninger, der bruges i eksemplerne gøre. Finde oplysninger om den HiveQL, der bruges i dette eksempel, kan du se [Brug Hive med Hadoop på HDInsight](hdinsight-use-hive.md).


**Forudsætninger**

For at fuldføre trinnene i denne artikel, skal du følgende.

- **En Azure HDInsight (Hadoop på HDInsight) klynge (Windows-baserede eller Linux-baseret)**
- **En arbejdsstation med Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Køre Hive forespørgsler ved hjælp af Azure PowerShell

Azure PowerShell indeholder *cmdletter* , så du kan køre fra en fjernplacering Hive forespørgsler på HDInsight. Internt opnås dette ved hjælp af RESTEN opkald til [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (tidligere kaldet Templeton), der kører på HDInsight klyngen.

Følgende cmdletter bruges, når du kører Hive forespørgsler i en ekstern HDInsight klynge:

* **Tilføj AzureRmAccount**: godkender Azure PowerShell til at abonnementet Azure

* **Ny AzureRmHDInsightHiveJobDefinition**: opretter en ny *definition af job* ved hjælp af de angivne HiveQL sætninger

* **Start-AzureRmHDInsightJob**: sender jobbet definitionen til HDInsight, starter jobbet og returnerer et *job* objekt, der kan bruges til at kontrollere status for jobbet

* **Vent-AzureRmHDInsightJob**: bruger jobobjekt til at kontrollere status for jobbet. Det skal vente, indtil jobbet er fuldført eller ventetiden overskrides.

* **Get-AzureRmHDInsightJobOutput**: bruges til at hente output fra jobbet

* **Aktiver AzureRmHDInsightHiveJob**: bruges til at køre HiveQL sætninger. Dette kan blokere forespørgslen er fuldført og returnerer derefter resultatet

* **Brug AzureRmHDInsightCluster**: Angiver den aktuelle klynge skal bruges til kommandoen **Aktiver AzureRmHDInsightHiveJob**

Nedenstående trin viser, hvordan du bruger disse cmdlet'er til at køre et job i din HDInsight klynge:

1. Følgende kode ved hjælp af en editor, Gem som **hivejob.ps1**. Du skal erstatte **CLUSTERNAME** med navnet på din HDInsight klynge.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Åbn en ny **Azure PowerShell** kommandoprompt. Skifte til placeringen af filen **hivejob.ps1** , og derefter bruge følgende kommando til at køre scriptet:

        .\hivejob.ps1

    Når scriptet kører, bliver du bedt om at angive HTTPS/Admin kontolegitimationsoplysninger for din klynge. Du kan også bedt om at logge på abonnementet Azure.
    
7. Når jobbet er fuldført, at den skal returnere oplysninger som følger:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Som tidligere nævnt kan **Aktiver Hive** bruges til at køre en forespørgsel og vente på svar. Brug følgende kommandoer, og Erstat **CLUSTERNAME** med navnet på din klynge:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    Output ser ud som følger:

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Du kan bruge Azure PowerShell **Her strenge** cmdlet eller HiveQL scriptfiler for længere HiveQL forespørgsler. Følgende kodestykke viser, hvordan du bruger **Aktiver Hive** -cmdlet til at køre en HiveQL scriptfil. Scriptfil HiveQL skal overføres til wasbs: / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Du kan finde flere oplysninger om **Her strenge**, <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Ved hjælp af Windows PowerShell her-strenge</a>.

##<a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis der returneres ingen oplysninger, når jobbet er fuldført, kan der opstod en fejl under behandling. For at få vist oplysninger om fejlen i sagen, skal du tilføje følgende til slutningen af filen **hivejob.ps1** , gemme den og derefter køre den igen.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Dette returnerer de oplysninger, der skrives til STDERR på serveren, når du har kørt jobbet, og det kan hjælpe med at finde ud af, hvorfor jobbet går ned.

##<a name="summary"></a>Oversigt

Som du kan se, indeholder Azure PowerShell en nem måde at køre Hive forespørgsler i en HDInsight klynge, overvåge jobstatus og hente output.

##<a name="next-steps"></a>Næste trin

Du kan finde generelle oplysninger om Hive i HDInsight:

* [Bruge Hive med Hadoop på HDInsight](hdinsight-use-hive.md)

Du kan arbejde med Hadoop på HDInsight oplysninger om andre måder:

* [Brug gris med Hadoop på HDInsight](hdinsight-use-pig.md)

* [Bruge MapReduce med Hadoop på HDInsight](hdinsight-use-mapreduce.md)
