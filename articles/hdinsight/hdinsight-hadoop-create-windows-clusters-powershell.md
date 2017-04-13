<properties
   pageTitle="Oprette Windows-baseret Hadoop klynger i ved hjælp af PowerShell Azure HDInsight | Microsoft Azure"
    description="Lær at oprette klynger til Azure HDInsight ved hjælp af Azure PowerShell."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Oprette Windows-baseret Hadoop klynger i ved hjælp af PowerShell Azure HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Lær at oprette HDInsight klynger ved hjælp af Azure PowerShell. Azure PowerShell er et modul, der indeholder cmdletter for at administrere Azure med Windows PowerShell. Andre klynge oprettelse af værktøjer og funktioner, klik på fanen Vælg øverst på denne side eller se [metoder til oprettelse af klynge](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Forudsætninger for:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Før du begynder vejledningen i denne artikel, skal du have følgende:

- Azure-abonnement. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Oprette klynger
Azure PowerShell er en effektiv scripting-miljø, som du kan bruge til at styre og automatisere installation og administration af din arbejdsmængder i Azure. Dette afsnit indeholder en vejledning til, hvordan du opretter en HDInsight klynge ved hjælp af Azure PowerShell. Finde oplysninger om konfiguration af en arbejdsstation for at køre HDInsight Windows PowerShell-cmdlet'er [installere og konfigurere Azure PowerShell](../powershell-install-configure.md). Du kan finde flere oplysninger om brug af Azure PowerShell med HDInsight, kan du se [administrere HDInsight ved hjælp af PowerShell](hdinsight-administer-use-powershell.md). Liste over HDInsight Windows PowerShell-cmdletterne, skal du se [HDInsight cmdlet reference](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Følgende procedurer er nødvendige for at oprette en HDInsight klynge ved hjælp af Azure PowerShell:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Oprette klynger ved hjælp af ARM skabelon

Du kan bruge Azure PowerShell til at installere en ARM-skabelon, som opretter en HDInsight klynge.  Se [ringe skabeloner ved hjælp af Azure PowerShell](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Tilpasse klynger

- Se [tilpasse HDInsight klynger ved hjælp af Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Se [tilpasse Windows-baseret HDInsight klynger ved hjælp af Script handlingen](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Næste trin
Du har lært flere måder at oprette en HDInsight klynge i denne artikel. Hvis du vil vide mere, skal du se følgende artikler:

* [Introduktion til Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Lær, hvordan du begynde at arbejde med din HDInsight klynge
* [Sende Hadoop job fra et program](hdinsight-submit-hadoop-jobs-programmatically.md) – Lær, hvordan du automatisk sende job til HDInsight
* [Administrere Hadoop klynger i HDInsight ved hjælp af PowerShell](hdinsight-administer-use-powershell.md) - få mere at vide om at arbejde med HDInsight ved hjælp af Azure PowerShell
* [Azure HDInsight SDK dokumentation]  [ hdinsight-sdk-documentation] -opdage HDInsight SDK




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
