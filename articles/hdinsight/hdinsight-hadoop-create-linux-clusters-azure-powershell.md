<properties
    pageTitle="Oprette Hadoop, HBase, Storm eller knallertmotor klynger på Linux i ved hjælp af PowerShell Azure HDInsight | Microsoft Azure"
    description="Lær at oprette Hadoop, HBase, Storm eller knallertmotor klynger på Linux til HDInsight ved hjælp af Azure PowerShell."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Oprette Linux-baserede klynger i HDInsight ved hjælp af Azure PowerShell

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell er en effektiv scripting-miljø, som du kan bruge til at styre og automatisere installation og administration af din arbejdsmængder i Microsoft Azure. Dette dokument indeholder oplysninger om, hvordan du opretter en Linux-baserede HDInsight klynge ved hjælp af Azure PowerShell. Den indeholder også et eksempel på et script.

> [AZURE.NOTE] Azure PowerShell er kun tilgængelig på Windows-klienter. Hvis du bruger en Linux, Unix eller Mac OS X-klienten, skal du se [oprette en Linux-baserede HDInsight klynge, ved hjælp af Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md) oplysninger om brug af Azure CLI til at oprette en klynge.

## <a name="prerequisites"></a>Forudsætninger
Du skal have følgende, før du starter denne fremgangsmåde:

- Et Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- Azure PowerShell.
    Du kan finde flere oplysninger om at bruge Azure PowerShell med HDInsight, kan du se [administrere HDInsight ved hjælp af PowerShell](hdinsight-administer-use-powershell.md). Se [reference til HDInsight-cmdlet](https://msdn.microsoft.com/library/azure/dn858087.aspx)til listen over HDInsight Windows PowerShell-cmdlet'er.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Oprette klynger

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Hvis du vil oprette en HDInsight klynge ved hjælp af Azure PowerShell, skal du benytte følgende fremgangsmåde:

- Oprette en Azure ressourcegruppe
- Oprette en Azure-lager-konto
- Oprette en Azure Blob-objektbeholder
- Oprette en HDInsight klynge

De to vigtigste parametre, du skal angive for at oprette Linux klynger er dem, der angiver typen OS og SSH brugeroplysninger:

- Kontrollér, at du angiver parameteren **- OSType** som **Linux**.
- Hvis du vil bruge SSH for remote sessioner på klynger, kan du angive SSH brugeradgangskode eller SSH offentlige nede. Hvis du angiver både SSH brugeradgangskode og den SSH offentlig nøgle, ignoreres tasten. Hvis du vil bruge tasten SSH for remote sessioner, skal du angive en tom SSH adgangskode, når du bliver bedt om en. Du kan finde flere oplysninger om at bruge SSH med HDInsight du se en af følgende artikler:

    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Linux, Unix eller OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Bruge SSH med Linux-baserede Hadoop på HDInsight fra Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Følgende script viser, hvordan du opretter en ny klynge:

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

De værdier, du angiver for **$clusterCredentials** bruges til at oprette Hadoop-brugerkonto for-klyngen. Brug denne konto til at oprette forbindelse til klyngen.

De værdier, du angiver for **$sshCredentials** bruges til at oprette SSH brugeren for-klyngen. Brug denne konto til at starte en fjernsession SSH på klyngen og køre job.

> [AZURE.IMPORTANT] I dette script, skal du angive antallet af knuder, arbejder, der skal være i klyngen. Hvis du planlægger at bruge mere end 32 arbejder noder (enten på klynge oprettelse eller ved at skalere klyngen efter oprettelse af), skal du også angive en hoved node størrelse med mindst 8 kerner og 14 GB RAM.
>
> Se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/)kan finde flere oplysninger om node størrelser og det tilknyttede omkostninger.

Det kan tage op til 20 minutter til at oprette en klynge.

I følgende eksempel viser, hvordan du kan tilføje en ekstra lagerplads konto:

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Tilpasse klynger

- Se [tilpasse HDInsight klynger ved hjælp af Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Se [tilpasse Windows-baseret HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Slette klyngen

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Næste trin

Nu hvor du har oprettet en HDInsight klynge, kan du bruge følgende ressourcer til at få mere for at vide om at arbejde med din klynge.

### <a name="hadoop-clusters"></a>Hadoop klynger

* [Bruge Hive med HDInsight](hdinsight-use-hive.md)
* [Brug gris med HDInsight](hdinsight-use-pig.md)
* [Bruge MapReduce med HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase klynger

* [Komme i gang med HBase på HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Udvikle Java-programmer til HBase på HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm klynger

* [Udvikle Java topologier for Storm på HDInsight](hdinsight-storm-develop-java-topology.md)
* [Bruge Python komponenter i Storm på HDInsight](hdinsight-storm-develop-python-topology.md)
* [Installere og overvåge topologier med Storm på HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Knallertmotor klynger

* [Oprette en enkeltstående program, ved hjælp af Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Køre job fra en fjernplacering på en knallertmotor klynge, ved hjælp af Livius](hdinsight-apache-spark-livy-rest-interface.md)
* [Knallertmotor med BI: analyse af interaktive data ved hjælp af knallertmotor i HDInsight med BI-værktøjer](hdinsight-apache-spark-use-bi-tools.md)
* [Knallertmotor med Machine Learning: Brug knallertmotor i HDInsight til at forudsige mad undersøgelsesresultaterne](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Knallertmotor Streaming: Brug knallertmotor i HDInsight til udvikling af realtid streaming programmer](hdinsight-apache-spark-eventhub-streaming.md)
