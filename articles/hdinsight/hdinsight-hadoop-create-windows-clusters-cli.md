<properties
   pageTitle="Oprette Windows-baseret Hadoop klynger i HDInsight ved hjælp af Azure CLI"
    description="Lær at oprette klynger til Azure HDInsight ved hjælp af Azure CLI."
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
   ms.date="09/02/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Oprette Windows-baseret Hadoop klynger i HDInsight ved hjælp af Azure CLI

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Lær at oprette HDInsight klynger ved hjælp af Azure CLI. Andre klynge oprettelse af værktøjer og funktioner, klik på fanen Vælg øverst på denne side eller se [metoder til oprettelse af klynge](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Forudsætninger for:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Før du begynder vejledningen i denne artikel, skal du have følgende:

- **Azure-abonnement**. Se [få Azure gratis prøveversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Azure CLI**.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

##<a name="connect-to-azure"></a>Oprette forbindelse til Azure

Brug følgende kommando til at oprette forbindelse til Azure:

    azure login

Du kan finde flere oplysninger om godkendelse af med en arbejds- eller skolekonto, [Opret forbindelse til et Azure-abonnement fra Azure CLI](../xplat-cli-connect.md).

Brug følgende kommando til at skifte til ARM tilstand:

    azure config mode arm

Hvis du vil have hjælp, skal du bruge parameteren **-h** .  Eksempel:

    azure hdinsight cluster create -h

##<a name="create-clusters"></a>Oprette klynger

Du skal have en Azure ressource Management (ARM) og en Azure Blob-lager-konto, før du kan oprette en HDInsight klynge. Hvis du vil oprette en HDInsight klynge, skal du angive følgende:

- **Azure ressourcegruppe**: A Data sø Analytics konto skal oprettes i en Azure ressourcegruppe. Azure ressourcestyring gør det muligt at arbejde med ressourcer i dit program som en gruppe. Du kan installere, opdatere eller slette alle ressourcerne for dit program i en enkelt, koordineret handling.

    Liste over ressourcegrupperne i dit abonnement:

        azure group list

    Sådan oprettes en ny ressourcegruppe:

        azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **HDInsight klyngenavn**

- **Placering**: en af de Azure datacentre, der understøtter HDInsight klynger. Se [HDInsight priser](https://azure.microsoft.com/pricing/details/hdinsight/)for en liste over understøttede placeringer.

- **Lagerplads standardkonto**: HDInsight bruger en Azure Blob storage beholder som standard-filsystemet. Der kræves en Azure-lager-konto, før du kan oprette en HDInsight klynge.

    Sådan oprettes en ny Azure-lager-konto:

        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

    > [AZURE.NOTE]Kontoen lagerplads skal samhuses med HDInsight i et datacenter.
    > Kontotype lagerplads kan ikke ZRS, fordi ZRS ikke understøtter tabel.

    Finde oplysninger om oprettelse af en Azure-lager-konto ved hjælp af portalen Azure, under [oprette, administrere eller slette en lagerplads konto] [azure-oprette-storageaccount].

    Hvis du allerede har en lagerplads-konto, men du ikke kender det kontonavn og kontonøgle, kan du bruge følgende kommandoer til at hente oplysninger:

        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

    Få mere at vide om at få oplysninger ved hjælp af portalen Azure i afsnittet "Administrere kontoen lagerplads" i [om Azure lagerplads konti](../storage/storage-create-storage-account#manage-your-storage-account).

- **(Valgfrit) standard Blob objektbeholder**: kommandoen **azure hdinsight klynge create** opretter objektbeholderen, hvis den ikke findes. Hvis du vælger at oprette objektbeholderen på forhånd, kan du bruge følgende kommando:

    oprette objektbeholderen til Azure lagring – kontonavn "<Storage Account Name>"--kontonøgle <Storage Account Key> [ContainerName]

Når du har forberedt kontoen lagerplads, er du klar til at oprette en klynge:


    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


##<a name="create-clusters-using-configuration-files"></a>Oprette klynger ved hjælp af konfigurationsfiler
Typisk, du opretter en HDInsight klynge, du køre job på den og derefter slette klynge for at reducere omkostningerne. Kommandolinjen giver dig mulighed for at gemme konfigurationerne til en fil, så du kan genbruge den, hver gang du opretter en klynge.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Eksempel: Oprette en konfigurationsfil, der indeholder handlingen script til at køre, når du opretter en klynge.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

##<a name="create-clusters-with-script-action"></a>Oprette klynger med scripthandling

Oprette en konfigurationsfil, der indeholder handlingen script til at køre, når du opretter en klynge.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Oprette en klynge med en scripthandling

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Du kan finde oplysninger om generelle script, [tilpasse HDInsight klynger ved hjælp af Script-handlingen (Linux)](hdinsight-hadoop-customize-cluster.md).


## <a name="create-clusters-using-arm-templates"></a>Oprette klynger ved hjælp af ARM skabeloner

Du kan bruge CLI til at oprette klynger ved at ringe til ARM skabeloner. Se [installere med Azure CLI](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Se også

- [Introduktion til Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Lær, hvordan du begynde at arbejde med din HDInsight klynge
- [Sende Hadoop job fra et program](hdinsight-submit-hadoop-jobs-programmatically.md) – Lær, hvordan du automatisk sende job til HDInsight
- [Administrere Hadoop klynger i HDInsight ved hjælp af Azure CLI](hdinsight-administer-use-command-line.md)
- [Bruge Azure CLI til Mac, Linux og Windows med Azure Service Management](../virtual-machines-command-line-tools.md)
