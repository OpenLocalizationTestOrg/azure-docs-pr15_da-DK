<properties
    pageTitle="Administrere Hadoop klynger ved hjælp af Azure CLI | Microsoft Azure"
    description="Sådan bruges Azure CLI til at administrere Hadoop klynger i HDIsight"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="mumian"
    tags="azure-portal"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Administrere Hadoop klynger i HDInsight ved hjælp af Azure CLI

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Lær at bruge [Azure kommandolinjen](../xplat-cli-install.md) til at administrere Hadoop klynger i Azure HDInsight. Azure CLI er implementeret i Node.js. Det kan bruges på alle platforme, der understøtter Node.js, herunder Windows, Mac og Linux.

Denne artikel omhandler kun ved hjælp af Azure CLI med HDInsight. Finde en generel vejledning om, hvordan du bruger Azure CLI [installere og konfigurere Azure CLI][azure-command-line-tools].

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="prerequisites"></a>Forudsætninger

Før du begynder i denne artikel, skal du have følgende:

- **En Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI** - se [installere og konfigurere Azure CLI](../xplat-cli-install.md) oplysninger om installation og konfiguration.
- **Opret forbindelse til Azure**, som bruger følgende kommando:

        azure login

    Du kan finde flere oplysninger om godkendelse af med en arbejds- eller skolekonto, [Opret forbindelse til et Azure-abonnement fra Azure CLI](xplat-cli-connect.md).
    
- **Skift til Azure ressourcestyring-tilstand**, som med følgende kommando:

        azure config mode arm

Hvis du vil have hjælp, skal du bruge parameteren **-h** .  Eksempel:

    azure hdinsight cluster create -h
    
##<a name="create-clusters"></a>Oprette klynger

Se [oprette Linux-baserede klynger i HDInsight ved hjælp af Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

##<a name="list-and-show-cluster-details"></a>Listen, og vise klynge detaljer
Brug følgende kommandoer til listen, og vise klynge detaljer:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI. CLIListCluster][image-cli-clusterlisting]


##<a name="delete-clusters"></a>Slette klynger

Brug følgende kommando til at slette en klynge:

    azure hdinsight cluster delete <Cluster Name>

Du kan også slette en klynge ved at slette ressourcegruppen, der indeholder klyngen. Vær opmærksom på, slettes alle ressourcerne i gruppen herunder lagerplads standardkontoen.

    azure group delete <Resource Group Name>

##<a name="scale-clusters"></a>Skala klynger

Sådan ændrer du Hadoop klyngestørrelse:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Aktivere/deaktivere HTTP-adgang til en klynge

    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Aktivere/deaktivere RDP adgang til en klynge

    azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
    azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##<a name="next-steps"></a>Næste trin
I denne artikel, har du lært at udføre forskellige HDInsight klynge administrative opgaver. Hvis du vil vide mere, skal du se følgende artikler:

* [Administrere HDInsight ved hjælp af portalen Azure] [hdinsight-admin-portal]
* [Administrere HDInsight ved hjælp af Azure PowerShell] [hdinsight-admin-powershell]
* [Introduktion til Azure HDInsight] [hdinsight-get-started]
* [Sådan bruger du Azure CLI] [azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Liste og Vis klynger"
