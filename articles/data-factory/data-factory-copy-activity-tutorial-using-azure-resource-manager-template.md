<properties
    pageTitle="Selvstudium: Oprette en rørledning, ved hjælp af Ressourcestyring skabelon | Microsoft Azure"
    description="I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af Azure ressourcestyring skabelon."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af Azure ressourcestyring skabelon
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Kopiere guiden](data-factory-copy-data-wizard-tutorial.md)
- [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure ressourcestyring skabelon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Dette selvstudium viser, hvordan du opretter og overvåge en Azure data factory ved hjælp af en skabelon til Azure ressourcestyring. Pipeline i data factory kopierer data fra Azure Blob-lager til Azure SQL-Database.

## <a name="prerequisites"></a>Forudsætninger
- Gennemgå [selvstudium oversigt og forudsætninger](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , og Fuldfør trinnene **betingelse** .
- Følg vejledningen i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) artikel for at installere seneste version af Azure PowerShell på din computer. I dette selvstudium bruger du PowerShell til at udrulle Data Factory enheder. 
- (valgfrit) Se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md) til at få mere at vide om Azure ressourcestyring skabeloner.


## <a name="in-this-tutorial"></a>I dette selvstudium

I dette selvstudium, kan du oprette en data fabrik med følgende Data Factory objekter:

Enhed | Beskrivelse  
------ | ----------- 
Azure sammenkædet lagringstjeneste | Link til kontoen Azure-lager til fabriksindstillingerne data. Azure-lager er datalager kilde og Azure SQL-database er sink datalager for kopi aktiviteten i selvstudiet. Det angiver kontoen lagerplads, der indeholder den indtastede data til kopi aktiviteten. 
Azure SQL-Database, der er sammenkædet service| Link til Azure SQL-database til fabriksindstillingerne data. Det angiver den Azure SQL-database, der indeholder outputdata til kopi aktiviteten. 
Azure Blob input datasæt | Refererer til tjenesten sammenkædet Azure-lager. Tjenesten sammenkædede refererer til en Azure-lager-konto og Azure Blob datasættet angiver objektbeholder, mappe og filnavn i opbevaring, der indeholder de indtastede data. 
Azure SQL output datasæt | Refererer til Azure SQL sammenkædet service. Tjenesten Azure SQL sammenkædet refererer til en Azure SQL server og Azure SQL-datasæt angiver navnet på den tabel, der indeholder outputdataene. 
Data pipeline | Pipeline har en aktivitet af typen kopier, der tager Azure blob datasættet som input og Azure SQL-datasæt som output. Kopiér aktiviteten kopierer data fra en Azure blob til en tabel i Azure SQL-database.  

En data fabrik kan have en eller flere rørledninger. En rørledning kan have en eller flere aktiviteter i den. Der findes to typer aktiviteter: [data bevægelse aktiviteter](data-factory-data-movement-activities.md) og [data transformation aktiviteter](data-factory-data-transformation-activities.md). I dette selvstudium, kan du oprette en rørledning med én aktivitet (kopi aktivitet).

![Kopiere Azure Blob til Azure SQL-Database](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

Følgende afsnit indeholder fuldført ressourcestyring skabelonen til at definere Data Factory enheder, så du hurtigt kan køre via selvstudiet og teste skabelonen. For at forstå, hvordan hvert Data Factory-objekt er defineret, se [Data Factory-objekter i skabelonen](#data-factory-entities-in-the-template) afsnittet.

## <a name="data-factory-json-template"></a>Factory JSON dataskabelon
Skabelonen websted på øverste ressourcestyring til at definere en data fabrik er: 

    {
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
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Oprette en JSON-fil med navnet **ADFCopyTutorialARM.json** i **C:\ADFGetStarted** mappe med følgende indhold:


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
          "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
          "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
          "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
          "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
          "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
          "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
          } 
        },
        "variables": {
          "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
          "azureSqlLinkedServiceName": "AzureSqlLinkedService",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "blobInputDatasetName": "BlobInputDataset",
          "sqlOutputDatasetName": "SQLOutputDataset",
          "pipelineName": "Blob2SQLPipeline"
        },
        "resources": [
          {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
              {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
                }
              },
              {
                "type": "linkedservices",
                "name": "[variables('azureSqlLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlDatabase",
                  "description": "Azure SQL linked service",
                  "typeProperties": {
                    "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
                  }
                }
              },
              {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "Column0",
                      "type": "String"
                    },
                    {
                      "name": "Column1",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                    "fileName": "[parameters('sourceBlobName')]",
                    "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  },
                  "external": true
                }
              },
              {
                "type": "datasets",
                "name": "[variables('sqlOutputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureSqlLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlTable",
                  "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "FirstName",
                      "type": "String"
                    },
                    {
                      "name": "LastName",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "tableName": "[parameters('targetSQLTable')]"
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  }
                }
              },
              {
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
                  "start": "2016-10-02T00:00:00Z",
                  "end": "2016-10-03T00:00:00Z"
                }
              }
            ]
          }
        ]
      }

## <a name="parameters-json"></a>Parametre JSON 
Oprette en JSON-fil med navnet **ADFCopyTutorialARM Parameters.json** , der indeholder parametre for skabelonen Azure ressourcestyring. 

> [AZURE.IMPORTANT] Angiv navn og tast til kontoen Azure-lager for **storageAccountName** og **storageAccountKey** parametre.  

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { 
            "storageAccountName": { "value": "<Name of the Azure storage account>"  },
            "storageAccountKey": {
                "value": "<Key for the Azure storage account>"
            },
            "sourceBlobContainer": { "value": "adftutorial" },
            "sourceBlobName": { "value": "emp.txt" },
            "sqlServerName": { "value": "<Name of the Azure SQL server>" },
            "databaseName": { "value": "<Name of the Azure SQL database>" },
            "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
            "sqlServerPassword": { "value": "<password for the user>" },
            "targetSQLTable": { "value": "emp" }
        }
    }

> [AZURE.IMPORTANT] Du skal muligvis separat parameter JSON filer til udvikling, test og fremstilling miljøer, der kan bruges med de samme Data Factory JSON-skabelon. Ved hjælp af en Power Shell script, kan du automatisere implementering Data Factory-objekter i disse miljøer.  

## <a name="create-data-factory"></a>Oprette data factory
1. Starte **Azure PowerShell** og køre følgende kommando:
    - Køre `Login-AzureRmAccount` og angive det brugernavn og adgangskode, du bruger til at logge på portalen Azure.  
    - Køre `Get-AzureRmSubscription` til at få vist alle abonnementer til denne konto.
    - Køre `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` til at markere det abonnement, du vil arbejde med. 
2. Kør følgende kommando for at installere Data Factory objekter ved hjælp af skabelonen Ressourcestyring, du oprettede i trin 1.

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Overvåge pipeline
1. Log på [Azure-portalen](https://portal.azure.com) ved hjælp af din Azure-konto.
2. Klik på **Data fabrikker** på menuen til venstre (eller) Klik på **flere tjenester** og **Data fabrikker** under **INTELLIGENCE + ANALYTICS** kategori.

    ![Menuen data fabrikker](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Søge efter **Data fabrikker** på siden, og find dine data factory. 

    ![Søge efter data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Klik på din Azure data factory. Du kan se startsiden for data factory.

    ![Startsiden for data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. Klik på **diagrammet** for at få vist diagrammet oversigt over dine data factory.

    ![Diagramvisning af data factory](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. Dobbeltklik på datasættet **SQLOutputDataset**i diagramvisningen. Du får denne status for udsnittet. Når kopieringen er færdig, skal angiver du status **klar**.

    ![Output udsnit i klar tilstand](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. Når udsnittet er i **klar** tilstand, skal du kontrollere, at dataene er kopieret til tabellen **emp** i Azure SQL-database.

Se [skærm datasæt og pipeline](data-factory-monitor-manage-pipelines.md) for oplysninger om, hvordan du bruger Azure portalen blade til at overvåge pipeline og datasæt, du har oprettet i dette selvstudium.

Du kan også bruge skærm og administrere App til at overvåge dine data rørledninger. Se [skærm og administrere Azure Data Factory rørledninger ved hjælp af overvågning App](data-factory-monitor-manage-app.md) oplysninger om brug af programmet.


## <a name="data-factory-entities-in-the-template"></a>Data Factory objekter i skabelonen

### <a name="define-data-factory"></a>Definere data factory
Du definerer en data fabrik i skabelonen ressource manager som vist i følgende eksempel:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

DataFactoryName defineres som: 
      
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"

Det er en entydig streng, der er baseret på ressource gruppe-ID.  

### <a name="defining-data-factory-entities"></a>Angive Data Factory-enheder
De følgende Data Factory-enheder er defineret i skabelonen JSON: 

1. [Azure sammenkædet lagringstjeneste](#azure-storage-linked-service)
2. [Azure SQL sammenkædet service](#azure-sql-database-linked-service)
3. [Azure blob-datasæt](#azure-blob-dataset)
4. [Azure SQL-datasæt](#azure-sql-dataset)
5. [Data rørledning med en kopi aktivitet](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Azure sammenkædet lagringstjeneste
Du kan angive navn og tast til kontoen Azure-lager i dette afsnit. Se [Azure-lager sammenkædet service](data-factory-azure-blob-connector.md#azure-storage-linked-service) få mere at vide om JSON egenskaber, der bruges til at definere en Azure-lager, der er sammenkædet tjeneste. 

    {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureStorage",
            "description": "Azure Storage linked service",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
            }
        }
    }

ConnectionString bruger parametrene storageAccountName og storageAccountKey. Værdierne for disse parametre ved hjælp af en konfigurationsfil. Definitionen også bruger variabler: azureStroageLinkedService og dataFactoryName, der er defineret i skabelonen. 
    
#### <a name="azure-sql-database-linked-service"></a>Azure SQL-Database, der er sammenkædet service
Du angiver den Azure SQL-servernavnet, databasenavnet, brugernavn og brugeradgangskode i dette afsnit. Se [Azure SQL sammenkædet service](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) for at få oplysninger om JSON egenskaber, der bruges til at definere en Azure SQL sammenkædet tjeneste.  

    {
        "type": "linkedservices",
        "name": "[variables('azureSqlLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "Azure SQL linked service",
            "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
            }
        }
    }

ConnectionString bruger sqlServerName, databasenavn, sqlServerUserName og sqlServerPassword parametre, der overføres hvis værdier ved hjælp af en konfigurationsfil. Definitionen også bruger følgende variabler fra skabelonen: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Azure blob-datasæt
Du angiver navnene på blob objektbeholder, mappe og fil, der indeholder de indtastede data. Se [Azure Blob datasæt egenskaber](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) for at få oplysninger om JSON egenskaber, der bruges til at definere et Azure Blob-datasæt. 


    {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
            "structure": [
            {
                "name": "Column0",
                "type": "String"
            },
            {
                "name": "Column1",
                "type": "String"
            }
            ],
            "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            },
            "external": true
        }
    }

#### <a name="azure-sql-dataset"></a>Azure SQL-datasæt
Du kan angive navnet på tabellen i den Azure SQL-database, der indeholder de kopierede data fra Azure Blob-lager. Se [Azure SQL-datasæt egenskaber](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) for at få oplysninger om JSON egenskaber, der bruges til at definere et Azure SQL-datasæt. 

    {
        "type": "datasets",
        "name": "[variables('sqlOutputDatasetName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
            "[variables('azureSqlLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
            "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
            ],
            "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

#### <a name="data-pipeline"></a>Data pipeline
Du definerer en rørledning, der kopierer data fra Azure blob-datasæt til Azure SQL-datasæt. Du kan finde beskrivelser af JSON elementer, der bruges til at definere en rørledning i dette eksempel i [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) . 

    {
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
            "start": "2016-10-02T00:00:00Z",
            "end": "2016-10-03T00:00:00Z"
        }
    }

## <a name="reuse-the-template"></a>Genbruge skabelonen 
I dette selvstudium skal oprettet du en skabelon til at definere Data Factory-objekter og en skabelon for sende værdier for parametre. Pipeline kopierer data fra en Azure-lager-konto til en Azure SQL-database, der angives ved hjælp af parametre. Hvis du vil bruge den samme skabelon skal installeres Data Factory enheder til forskellige miljøer, du opretter en parameterfil for hver miljø og bruge den, når du installerer til det pågældende miljø.     

Eksempel:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json

Bemærk, at den første kommando bruger parameterfil til udviklingsmiljøet, anden, i testmiljøet og den tredje, til produktionsmiljøet.  

Du kan også genbruge skabelonen for at udføre gentagne opgaver. For eksempel skal du oprette mange data fabrikker med en eller flere rørledninger, der implementerer den samme logik, men hver data factory bruger forskellige Azure lager og Azure SQL-Database konti. I dette scenarie skal bruge du den samme skabelon i det samme miljø (Udviklingscenter, test eller fremstilling) med forskellige parameter filer til at oprette data fabrikker.   

