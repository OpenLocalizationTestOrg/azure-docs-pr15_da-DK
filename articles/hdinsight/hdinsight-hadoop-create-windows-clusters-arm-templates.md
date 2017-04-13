<properties
   pageTitle="Oprette Windows-baseret Hadoop klynger i HDInsight ved hjælp af Azure ressourcestyring skabeloner | Microsoft Azure"
    description="Lær at oprette klynger til Azure HDInsight ved hjælp af Azure ressourcestyring skabeloner."
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
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Oprette Windows-baseret Hadoop klynger i HDInsight ved hjælp af Azure ressourcestyring skabeloner

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Lær at oprette HDInsight klynger ved hjælp af Azure ressourcestyring skabeloner. Du kan finde yderligere oplysninger finder [Implementer et program med Azure ressourcestyring skabelon](../resource-group-template-deploy.md). Andre klynge oprettelse af værktøjer og funktioner, klik på fanen Vælg øverst på denne side eller se [metoder til oprettelse af klynge](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Forudsætninger for:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Før du begynder vejledningen i denne artikel, skal du have følgende:

- [Azure-abonnement](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell eller Azure CLI

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>Krav til Access

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Ressourcestyring skabeloner

Ressourcestyring skabelon gør det nemt at oprette HDInsight klynger, ressourcerne afhængige (såsom lagerplads standardkontoen) og andre ressourcer (såsom Azure SQL-Database du bruger Apache Sqoop) for dit program i en enkelt, koordineret handling. I skabelonen, du definerer de ressourcer, der er behov for programmet og angive installation parametre for at angive værdier for forskellige miljøer. Skabelonen består af JSON og udtryk, som du kan bruge til at oprette værdier til din installation.

Du kan finde en ressourcestyring skabelon til oprettelse af en HDInsight klynge og kontoen afhængige Azure-lager i [Tillæg A](#appx-a-arm-template). Brug en teksteditor til at gemme skabelonen til en fil på computeren. Du lære, hvordan du ringe til skabelonen ved hjælp af forskellige værktøjer.

Finde flere oplysninger om ressourcestyring skabelon

- [Forfatter Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)
- [Installere et program med Azure ressourcestyring skabelon](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>Installere med PowerShell

Den følgende procedure opretter en HDInsight klynge.

**Installere en klynge ved hjælp af Ressourcestyring skabelon**

1. Gem filen json i [tillæg A](#appx-a-arm-template) til computeren.
2. Angiv parametrene, hvis det er nødvendigt.
3. Køre skabelonen ved hjælp af følgende PowerShell-script:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    PowerShell-script konfigurerer kun klyngenavn og kontonavn lager.  Du kan angive andre værdier i skabelonen ressourcestyring. 
    
Du kan finde yderligere oplysninger finder [Implementer med PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Installere med Azure CLI

I følgende eksempel oprettes en klynge og dens afhængige lagerplads konto og objektbeholder ved at ringe til en ressourcestyring skabelon:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Installere med REST API'ER

Se [installere med REST-API](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## <a name="deploy-with-visual-studio"></a>Installere med Visual Studio

Med Visual Studio, kan du oprette gruppeprojekt, hvor en ressource og installerer det til Azure via brugergrænsefladen. Du vælger typen ressourcer til at medtage i projektet, og disse ressourcer føjes automatisk til Ressourcestyring skabelon. Projektet indeholder også en PowerShell-script for at installere skabelonen.

Se [oprette og implementere Azure ressourcegrupper via Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)for at få en introduktion til brug af Visual Studio med ressourcegrupper.

##<a name="next-steps"></a>Næste trin
Du har lært flere måder at oprette en HDInsight klynge i denne artikel. Hvis du vil vide mere, skal du se følgende artikler:


- Se et eksempel med at udrulle ressourcer via biblioteket .NET klient [Implementer ressourcer ved hjælp af .NET biblioteker og en skabelon](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Finde et detaljeret eksempel med at udrulle et program under [klargøring og installere microservices forudsigeligt i Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Vejledning i installation af din løsning til forskellige miljøer, under [udvikling og testmiljøer i Microsoft Azure](../solution-dev-test-environments.md).
- Se [redigering skabeloner](../resource-group-authoring-templates.md)for at få mere for at vide om sektionerne i skabelonen Azure ressourcestyring.
- Du kan finde en liste over de funktioner, du kan bruge i en skabelon til Azure Ressourcestyring, [skabelon funktioner](../resource-group-template-functions.md).



##<a name="appx-a-resource-manager-template"></a>Appx A: ressourcestyring skabelon

Følgende Azure Resource Manager skabelon opretter en Windows-baseret Hadoop klynge med kontoen afhængige Azure-lager.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

