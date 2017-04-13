<properties
    pageTitle="Administrere Hadoop klynger i HDInsight med PowerShell | Microsoft Azure"
    description="Lær at udføre administrative opgaver for Hadoop klynger i ved hjælp af PowerShell Azure HDInsight."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Administrere Hadoop klynger i HDInsight ved hjælp af Azure PowerShell

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell er en effektiv scripting-miljø, som du kan bruge til at styre og automatisere installation og administration af din arbejdsmængder i Azure. I denne artikel lærer du, hvordan du administrerer Hadoop klynger i Azure HDInsight ved hjælp af en lokal Azure PowerShell console ved hjælp af Windows PowerShell. Se [reference til HDInsight-cmdlet]til listen over HDInsight PowerShell-cmdletterne[hdinsight-powershell-reference].



**Forudsætninger**

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="install-azure-powershell"></a>Installere Azure PowerShell

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Hvis du har installeret Azure PowerShell version 0.9 x, skal du fjerne den før du installerer en nyere version.

Sådan kontrolleres versionen af de installerede PowerShell:

    Get-Module *azure*
    
For at fjerne den gamle version, skal du køre programmer og funktioner i Kontrolpanel. 


##<a name="create-clusters"></a>Oprette klynger

Se [oprette Linux-baserede klynger i ved hjælp af PowerShell Azure HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>Listen klynger
Brug følgende kommando til at få vist alle klynger i det aktuelle abonnement:

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>Vis klynge

Brug følgende kommando til at vise oplysninger om en bestemt klynge i det aktuelle abonnement:

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>Slette klynger

Brug følgende kommando til at slette en klynge:

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

Du kan også slette en klynge ved at fjerne ressourcegruppen, der indeholder klyngen. Vær opmærksom på, slettes alle ressourcerne i gruppen herunder lagerplads standardkontoen.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>Skala klynger
Klynge skalering funktion gør det muligt at ændre antallet af knuder, som bruges af en klynge, der kører på Azure HDInsight uden at skulle oprette klyngen.

>[AZURE.NOTE] Kun clusters med HDInsight version 3.1.3 eller højere understøttes. Hvis du er usikker på, hvilken version af din klynge, kan du se siden Egenskaber.  Se [liste og Vis klynger](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

Virkningen af at ændre antallet af dataknuder for hver type af klynge, der understøttes af HDInsight:

- Hadoop

    Du kan øge antallet af knuder arbejder i en Hadoop-klynge, der kører uden at påvirke alle andre ventende eller kører job problemfrit. Nyt job kan også sendes, når handlingen er i gang. Fejl i handlingen skalering håndteres problemfrit, så klyngen er altid tilbage i en funktionel tilstand.

    Når en Hadoop klynge skaleres ved at reducere antallet af dataknuder,, er nogle af tjenesterne i klyngen genstartet. Derved alle, der kører og ventende job mislykkes ved afslutningen af handlingen skalering. Du kan dog indsender job, når handlingen er fuldført.

- HBase

    Du kan problemfrit tilføjer eller fjerner noder til din HBase klynge, mens den kører. Regionale servere afstemmes automatisk i løbet af et par minutter skalering er udført. Du kan også manuelt saldo de regionale servere, ved at logge på headnode af klynge og køre følgende kommandoer fra et kommandopromptvindue:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Storm

    Du kan problemfrit tilføjer eller fjerner data noder til din Storm klynge, mens den kører. Men når installationen er fuldført af handlingen skalering, skal du genoprette topologien.

    Skulle genoprette balancen kan gøres på to måder:

    * Storm web brugergrænseflade
    * Værktøj til kommandolinjen (CLI)

    Se [Apache Storm dokumentation](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) for at få flere oplysninger.

    Webdelen Storm brugergrænseflade findes på HDInsight klyngen:

    ![HDInsight storm skala rebalance](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Her er et eksempel, hvordan du kan bruge kommandoen CLI for at genoprette Storm topologien:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

For at ændre Hadoop klyngestørrelse ved hjælp af Azure PowerShell, skal du køre følgende kommando fra en klientcomputer:

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>Giv/revoke access

HDInsight klynger har de følgende HTTP-webtjenester (alle disse tjenester har RESTful slutpunkter):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Disse tjenester er som standard tildelt til access. Du kan revoke/give adgang til. Ophæve:

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Give:

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Ved at give/tilbagekalde adgang, kan du nulstille klynge brugernavn og din adgangskode.

Dette kan gøres via portalen. Se [Administrere HDInsight ved hjælp af portalen Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Opdatere HTTP brugerlegitimationsoplysninger

Det er den samme fremgangsmåde som [Giv/revoke HTTP adgang](#grant/revoke-access). Hvis klyngen har fået tildelt HTTP-adgang, skal du først fjerne den.  Og derefter tildele adgang med nye HTTP brugerens legitimationsoplysninger.


##<a name="find-the-default-storage-account"></a>Finde lagerplads standardkontoen

Følgende Powershell-script viser, hvordan du få kontonavn standard lager og standard lagerplads kontonøgle for en klynge.

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>Find ressourcegruppen

I tilstanden ressourceleder, der tilhører hver HDInsight klynge en Azure ressourcegruppe.  Sådan finder du ressourcegruppen:

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>Sende job

**Sende MapReduce job**

Se [køre Hadoop MapReduce eksempler i Windows-baseret HDInsight](hdinsight-run-samples.md).

**Sende Hive job** 

Se [køre Hive forespørgsler ved hjælp af PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Sende gris job**

Få vist [køre gris sager ved hjælp af PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Sende Sqoop job**

Se [Brug Sqoop med HDInsight](hdinsight-use-sqoop.md).

**Sende Oozie job**

Se [Brug Oozie med Hadoop til at definere og køre en arbejdsproces i HDInsight](hdinsight-use-oozie.md).

##<a name="upload-data-to-azure-blob-storage"></a>Overføre data til Azure Blob-lager
Se [overføre data til HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Se også
* [HDInsight cmdlet referencedokumentation][hdinsight-powershell-reference]
* [Administrere HDInsight ved hjælp af portalen Azure][hdinsight-admin-portal]
* [Administrere HDInsight ved hjælp af en kommandolinjen][hdinsight-admin-cli]
* [Oprette HDInsight klynger][hdinsight-provision]
* [Overføre data til HDInsight][hdinsight-upload-data]
* [Sende Hadoop-job fra et program][hdinsight-submit-jobs]
* [Introduktion til Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
