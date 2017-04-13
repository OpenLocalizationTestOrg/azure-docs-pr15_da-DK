<properties 
    pageTitle="Brug ressourcestyring skabeloner i Data Factory | Microsoft Azure" 
    description="Lær at oprette og bruge Azure ressourcestyring skabeloner til at oprette Data Factory objekter." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Brug skabeloner til at oprette Azure Data Factory objekter

## <a name="overview"></a>Oversigt
Når du bruger Azure Data Factory til dine data integrationsbehov, du kan finde dig selv du genbruge det samme mønster på tværs af forskellige miljøer eller implementering af den samme opgave gentagne gange i den samme løsning. Skabeloner hjælpe dig med at implementere og administrere disse scenarier på en nem måde. Skabelonerne i Azure Data Factory er velegnet til scenarier, der involverer genanvende og gentagelse.
 
Overvej den situation, hvor en organisation har 10 produktionsanlæg planter over hele verden. Logfiler fra hver plante er gemt i en separat lokalt SQL Server-database. Virksomheden ønsker at opbygge et enkelt datawarehouse i skyen til ad hoc-analyser. Det vil også have de samme logik, men forskellige konfigurationer for udvikling, test- og miljøer. 

I dette tilfælde skal en opgave gentages i det samme miljø, men med forskellige værdier på tværs af virksomheder 10 data for hvert produktionsanlæg. **Gentagelse** findes i kraft. Templating giver mulighed for fremstilling af dette generisk flow (det vil sige, rørledninger har de samme aktiviteter i hver data factory), men bruger en separat parameterfil for hvert produktionsanlæg.

Som organisationen ønsker at implementere disse 10 data fabrikker flere gange på tværs af forskellige miljøer, kan skabeloner bruge denne **genanvende** ved at benytte separat parameter filer til udvikling, test- og miljøer.

## <a name="templating-with-azure-resource-manager"></a>Templating med Azure ressourcestyring
[Azure ressourcestyring skabeloner](../azure-resource-manager/resource-group-overview.md#template-deployment) er en god måde at opnå templating i Azure Data Factory. Ressourcestyring skabeloner definere til infrastruktur og konfiguration af din Azure løsning gennem en JSON-fil. Da Azure ressourcestyring skabeloner arbejder med alle/de fleste Azure-tjenester, kan den meget bruges til at nemt administrere alle de ressourcer for dine Azure aktiver. Se [redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md) til at få mere at vide om ressourcestyring skabeloner Generelt. 

## <a name="tutorials"></a>Selvstudier
Se følgende selvstudier til en trinvis vejledning til at oprette Data Factory objekter ved hjælp af Ressourcestyring skabeloner:

- [Selvstudium: Oprette en rørledning for at kopiere data ved hjælp af Azure ressourcestyring skabelon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Selvstudium: Oprette en pipeline til proces data ved hjælp af Azure ressourcestyring skabelon](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Data Factory skabeloner på Github
Se følgende Azure Hurtig start skabeloner på Github: 

- [Oprette en Data fabrik for at kopiere data fra Azure Blob-lager til Azure SQL-Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Oprette en Data fabrik med Hive aktivitet på Azure HDInsight klynge](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Oprette en Data fabrik for at kopiere data fra Salesforce til Azure BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

Velkommen til at dele dine Azure Data Factory-skabeloner på [Azure Hurtig start](https://azure.microsoft.com/documentation/templates/). Referere til [bidrag vejledning](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) under udvikling af skabeloner, der kan deles via denne lager. 

De følgende afsnit indeholder oplysninger om definerer Data Factory ressourcer i en ressourcestyring skabelon. 

## <a name="defining-data-factory-resources-in-templates"></a>Angive Data Factory-ressourcer i skabeloner
Skabelonen på øverste niveau til at definere en data fabrik er:

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Definere data factory

Du definerer en data fabrik i skabelonen Ressourcestyring, som vist i følgende eksempel:

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

DataFactoryName er defineret i "variabler" som:

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Definere sammenkædede tjenester 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Se [Sammenkædede Lagringstjeneste](data-factory-azure-blob-connector.md#azure-storage-linked-service) eller [Beregne sammenkædede tjenester](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) få mere at vide om JSON egenskaberne for den specifikke sammenkædede tjeneste, du vil installere. Parameteren "dependsOn" Angiver navnet på den tilsvarende data factory. Et eksempel på definerer en sammenkædet tjeneste til Azure-lager er vist i følgende JSON definitionen:

### <a name="define-datasets"></a>Definere datasæt

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Referere til [understøttes data butikker](data-factory-data-movement-activities.md#supported-data-stores-and-formats) få mere at vide om JSON egenskaberne for den specifikke datasæt type, du vil installere. Bemærk parameteren "dependsOn" Angiver navnet på den tilsvarende data fabrik og lager sammenkædet tjeneste. Et eksempel på definerer datasæt type Azure blob-lager er vist i følgende JSON definitionen:

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Definere rørledninger

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Se [definere rørledninger](data-factory-create-pipelines.md#pipeline-json) få mere at vide om JSON egenskaberne for definition af bestemte pipeline og aktiviteter, du vil installere. Bemærk parameteren "dependsOn" Angiver navnet på data fabrikken, og alle tilhørende sammenkædet services eller datasæt. Et eksempel på en rørledning, der kopierer data fra Azure Blob-lager til Azure SQL-Database er vist i følgende JSON kodestykket:

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Angive parametre Data Factory-skabelon
For de bedste metoder til at angive parametre, se [bedste fremgangsmåder til oprettelse af Azure ressourcestyring skabeloner](../resource-manager-template-best-practices.md#parameters) artikel. Generelt skal parameter brugen minimeres, især hvis variabler kan bruges i stedet. Give kun parametre i følgende scenarier:

- Indstillinger varierer afhængigt af miljø (eksempel: udvikling, test- og)
- Hemmeligheder (såsom adgangskoder)

Hvis du vil trække hemmeligheder fra [Azure nøgle samling](../key-vault/key-vault-get-started.md) , når du installerer Azure Data Factory objekter ved hjælp af skabeloner skal du angive den **vigtige samling** og **hemmeligt navn** som vist i følgende eksempel:

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Mens eksportere skabeloner til eksisterende data fabrikker ikke understøttes i øjeblikket endnu, er det i works. 


