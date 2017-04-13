<properties
    pageTitle="Oprette din første data factory (ressourcestyring skabelon) | Microsoft Azure"
    description="I dette selvstudium, kan du oprette en stikprøve Azure Data Factory rørledning, ved hjælp af en skabelon til Azure ressourcestyring."
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
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Selvstudium: Oprette din første Azure data factory ved hjælp af Azure ressourcestyring skabelon
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-build-your-first-pipeline.md)
- [Azure-portalen](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

I denne artikel kan du bruge en skabelon til Azure ressourcestyring til at oprette din første Azure data factory.

## <a name="prerequisites"></a>Forudsætninger
- Læse [Selvstudium oversigt](data-factory-build-your-first-pipeline.md) artikel, og Fuldfør trinnene **betingelse** .
- Følg vejledningen i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) artikel for at installere seneste version af Azure PowerShell på din computer.
- Se [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md) til at få mere at vide om Azure ressourcestyring skabeloner. 

## <a name="in-this-tutorial"></a>I dette selvstudium
Enhed | Beskrivelse  
------ | ----------- 
Azure sammenkædet lagringstjeneste | Link til kontoen Azure-lager til fabriksindstillingerne data. Kontoen Azure-lager indeholder input- og outputområder dataene for rørledningen i dette eksempel. 
HDInsight efter behov sammenkædede service| Links en efter behov HDInsight klynge data factory. Klyngen oprettes automatisk for dig at behandle data og er blevet slettet, når behandlingen er færdig.
Azure Blob input datasæt | Refererer til tjenesten sammenkædet Azure-lager. Tjenesten sammenkædede refererer til en Azure-lager-konto og Azure Blob datasættet angiver objektbeholder, mappe og filnavn i opbevaring, der indeholder de indtastede data. 
Azure Blob output datasæt | Refererer til tjenesten sammenkædet Azure-lager. Tjenesten sammenkædede refererer til en Azure-lager-konto og Azure Blob datasættet angiver objektbeholder, mappe og filnavn i opbevaring, der indeholder outputdataene. 
Data pipeline | Pipeline har en aktivitet af typen HDInsightHive forbruger input datasættet og giver output datasættet.   

En data fabrik kan have en eller flere rørledninger. En rørledning kan have en eller flere aktiviteter i den. Der findes to typer aktiviteter: [data bevægelse aktiviteter](data-factory-data-movement-activities.md) og [data transformation aktiviteter](data-factory-data-transformation-activities.md). I dette selvstudium, kan du oprette en rørledning med én aktivitet (kopi aktivitet).

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

Oprette en JSON-fil med navnet **ADFTutorialARM.json** i **C:\ADFGetStarted** mappe med følgende indhold:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
            "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
            "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
            "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
            "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
            "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
            "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
            "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
            "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
        },
        "variables": {
            "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
            "azureStorageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDatasetName": "AzureBlobInput",
            "blobOutputDatasetName": "AzureBlobOutput",
            "pipelineName": "HiveTransformPipeline"
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
                "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "typeProperties": {
                        "clusterSize": 1,
                        "version": "3.2",
                        "timeToLive": "00:05:00",
                        "osType": "windows",
                        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
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
                    "typeProperties": {
                        "fileName": "[parameters('inputBlobName')]",
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "external": true
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobOutputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
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
                    "[variables('hdInsightOnDemandLinkedServiceName')]",
                    "[variables('blobInputDatasetName')]",
                    "[variables('blobOutputDatasetName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "description": "Pipeline that transforms data using Hive script.",
                    "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                            "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                            "defines": {
                                "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                                "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                            }
                        },
                        "inputs": [
                            {
                                "name": "[variables('blobInputDatasetName')]"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "[variables('blobOutputDatasetName')]"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                    }
                    ],
                    "start": "2016-10-01T00:00:00Z",
                    "end": "2016-10-02T00:00:00Z",
                    "isPaused": false
                }
            }
            ]
        }
        ]
    }

> [AZURE.NOTE] Du kan finde et andet eksempel på ressourcestyring skabelon til oprettelse af en Azure data factory på [Selvstudium: oprette en rørledning med kopi aktivitet ved hjælp af en skabelon til Azure ressourcestyring](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  

## <a name="parameters-json"></a>Parametre JSON 
Oprette en JSON-fil med navnet **ADFTutorialARM Parameters.json** , der indeholder parametre for skabelonen Azure ressourcestyring.  

> [AZURE.IMPORTANT] Angiv navn og tast til kontoen Azure-lager for **storageAccountName** og **storageAccountKey** parametre i denne parameterfil. 

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "storageAccountName": {
                "value": "<Name of your Azure Storage account>"
            },
            "storageAccountKey": {
                "value": "<Key of your Azure Storage account>"
            },
            "blobContainer": {
                "value": "adfgetstarted"
            },
            "inputBlobFolder": {
                "value": "inputdata"
            },
            "inputBlobName": {
                "value": "input.log"
            },
            "outputBlobFolder": {
                "value": "partitioneddata"
            },
            "hiveScriptFolder": {
                "value": "script"
            },
            "hiveScriptFile": {
                "value": "partitionweblogs.hql"
            }
        }
    }

> [AZURE.IMPORTANT] Du skal muligvis separat parameter JSON filer til udvikling, test og fremstilling miljøer, der kan bruges med de samme Data Factory JSON-skabelon. Ved hjælp af en Power Shell script, kan du automatisere implementering Data Factory-objekter i disse miljøer. 

## <a name="create-data-factory"></a>Oprette data factory

1. Starte **Azure PowerShell** og køre følgende kommando: 
    - Køre `Login-AzureRmAccount` og angive det brugernavn og adgangskode, du bruger til at logge på portalen Azure.  
    - Køre `Get-AzureRmSubscription` til at få vist alle abonnementer til denne konto.
    - Køre `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` til at markere det abonnement, du vil arbejde med. Dette abonnement skal være den samme som den, du brugte på portalen Azure.
1. Kør følgende kommando for at installere Data Factory objekter ved hjælp af skabelonen Ressourcestyring, du oprettede i trin 1. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Overvåge pipeline
 
1.  Når du logger på [Azure-portalen](https://portal.azure.com/), klik på **Find** og vælg **Data fabrikker**.
        ![Gennemse -> Data fabrikker](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  Klik på den data factory (**TutorialFactoryARM**), du har oprettet i bladet **Data fabrikker** .   
2.  Klik på **Diagram**i bladet **Data Factory** til dine data factory.
        ![Diagram felt](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  Du kan se en oversigt over rørledninger og datasæt, der bruges i dette selvstudium i **Diagramvisning**.
    
    ![Diagramvisning](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Dobbeltklik på datasættet **AzureBlobOutput**i visningen Diagram. Du kan se, at det udsnit, behandles i øjeblikket.

    ![Datasæt](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Når behandling er færdig, skal se du udsnittet i **klar** tilstand. Oprettelse af en efter behov HDInsight klynge tager normalt et tidspunkt (cirka 20 minutter). Derfor Forvent pipeline til at tage **cirka 30 minutter** at behandle udsnittet.

    ![Datasæt](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Når udsnittet er i **klar** tilstand, skal du kontrollere mappen **partitioneddata** i objektbeholderen **adfgetstarted** i din blob storage for datatypen output.  

Se [skærm datasæt og pipeline](data-factory-monitor-manage-pipelines.md) for oplysninger om, hvordan du bruger Azure portalen blade til at overvåge pipeline og datasæt, du har oprettet i dette selvstudium.

Du kan også bruge skærm og administrere App til at overvåge dine data rørledninger. Se [skærm og administrere Azure Data Factory rørledninger ved hjælp af overvågning App](data-factory-monitor-manage-app.md) oplysninger om brug af programmet. 

> [AZURE.IMPORTANT] Indtast filen bliver slettet, når udsnittet er behandlet. Derfor, hvis du vil køre udsnittet eller gøre selvstudiet igen, overføre input filen (input.log) til mappen inputdata for objektbeholderen adfgetstarted.

## <a name="data-factory-entities-in-the-template"></a>Data Factory objekter i skabelonen
### <a name="define-data-factory"></a>Definere data factory
Du definerer en data fabrik i skabelonen Ressourcestyring, som vist i følgende eksempel:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

DataFactoryName defineres som: 
      
      "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",

Det er en entydig streng, der er baseret på ressource gruppe-ID.  

### <a name="defining-data-factory-entities"></a>Angive Data Factory-enheder
De følgende Data Factory-enheder er defineret i skabelonen JSON: 

- [Azure sammenkædet lagringstjeneste](#azure-storage-linked-service)
- [HDInsight efter behov sammenkædede service](#hdinsight-on-demand-linked-service)
- [Azure blob input datasæt](#azure-blob-input-dataset)
- [Azure blob output datasæt](#azure-blob-output-dataset)
- [Data rørledning med en kopi aktivitet](#data-pipeline)

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

**ConnectionString** bruger parametrene storageAccountName og storageAccountKey. Værdierne for disse parametre ved hjælp af en konfigurationsfil. Definitionen også bruger variabler: azureStroageLinkedService og dataFactoryName, der er defineret i skabelonen. 
    
#### <a name="hdinsight-on-demand-linked-service"></a>HDInsight efter behov sammenkædede service
Se [beregne sammenkædede services](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) artikel for at få mere at vide om JSON egenskaber, der bruges til at definere en sammenkædet tjeneste HDInsight efter behov.  

      {
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "HDInsightOnDemand",
          "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
          }
        }
      }

Bemærk følgende punkter: 

- Data Factory opretter en **Windows-baseret** HDInsight klynge for dig med det ovenfor JSON. Du kan også få dem oprette en **Linux-baserede** HDInsight klynge. Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
- Du kan bruge **din egen HDInsight klynge** i stedet for ved hjælp af en efter behov HDInsight klynge. Du kan finde oplysninger i [HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
- HDInsight klynge opretter en **standard objektbeholder** i den blob-lager, du har angivet i JSON (**linkedServiceName**). HDInsight sletter ikke denne beholder, når klyngen slettes. Det skyldes design. Med efter behov HDInsight sammenkædet tjeneste, en HDInsight klynge oprettes, hver gang et udsnit skal behandles, medmindre der er en eksisterende live klynge (**timeToLive**) og slettes, når behandlingen er færdig.

    Efterhånden som flere udsnit er behandlet, kan du se mange beholdere i Azure blob-lager. Hvis du ikke skal bruge dem til fejlfinding af job, kan du vil slette dem for at reducere omkostningerne lagerplads. Navnene på disse beholdere følge et mønster: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Bruge funktioner såsom [Microsoft lagerplads Explorer](http://storageexplorer.com/) til at slette beholdere i Azure blob-lager.

Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .



#### <a name="azure-blob-input-dataset"></a>Azure blob input datasæt
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
          "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          },
          "external": true
        }
      }

Denne definition bruger de følgende parametre, der er angivet i parameter skabelon: blobContainer, inputBlobFolder, og inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Azure Blob output datasæt
Du angiver navnene på blob objektbeholder og en mappe, der indeholder outputdataene. Se [Azure Blob datasæt egenskaber](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) for at få oplysninger om JSON egenskaber, der bruges til at definere et Azure Blob-datasæt.  

      {
        "type": "datasets",
        "name": "[variables('blobOutputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          }
        }
      }

Denne definition bruger de følgende parametre, der er angivet i skabelonen parameter: blobContainer og outputBlobFolder. 

#### <a name="data-pipeline"></a>Data pipeline
Du definerer en rørledning, der transformere data ved at køre Hive script på en efter behov Azure HDInsight klynge. Du kan finde beskrivelser af JSON elementer, der bruges til at definere en rørledning i dette eksempel i [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) . 

    {
        "type": "datapipelines",
        "name": "[variables('pipelineName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('hdInsightOnDemandLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('blobOutputDatasetName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "description": "Pipeline that transforms data using Hive script.",
          "activities": [
            {
              "type": "HDInsightHive",
              "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                  "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                  "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
              },
              "inputs": [
                {
                  "name": "[variables('blobInputDatasetName')]"
                }
              ],
              "outputs": [
                {
                  "name": "[variables('blobOutputDatasetName')]"
                }
              ],
              "policy": {
                "concurrency": 1,
                "retry": 3
              },
              "scheduler": {
                "frequency": "Month",
                "interval": 1
              },
              "name": "RunSampleHiveActivity",
              "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
          ],
          "start": "2016-10-01T00:00:00Z",
          "end": "2016-10-02T00:00:00Z",
          "isPaused": false
        }
      }

## <a name="reuse-the-template"></a>Genbruge skabelonen 
I dette selvstudium skal oprettet du en skabelon til at definere Data Factory-objekter og en skabelon for sende værdier for parametre. Hvis du vil bruge den samme skabelon skal installeres Data Factory enheder til forskellige miljøer, du opretter en parameterfil for hver miljø og bruge den, når du installerer til det pågældende miljø.     

Eksempel:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json

Bemærk, at den første kommando bruger parameterfil til udviklingsmiljøet, anden, i testmiljøet og den tredje, til produktionsmiljøet.  

Du kan også genbruge skabelonen for at udføre gentagne opgaver. For eksempel skal du oprette mange data fabrikker med en eller flere rørledninger, der implementerer den samme logik, men hver data factory bruger forskellige Azure lager og Azure SQL-Database konti. I dette scenarie skal bruge du den samme skabelon i det samme miljø (Udviklingscenter, test eller fremstilling) med forskellige parameter filer til at oprette data fabrikker. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Ressourcestyring skabelon til at oprette en gateway
Her er et eksempel på ressourcestyring en skabelon til oprettelse af en logisk gateway i baggrunden. Installere en gateway på din lokale computer eller Azure IaaS VM og registrere gatewayen med Data Factory-tjenesten ved hjælp af en nøgle. Se [flytte data mellem i det lokale miljø og skyen](data-factory-move-data-between-onprem-and-cloud.md) få mere at vide.

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Denne skabelon opretter en data fabrik med navnet GatewayUsingArmDF med en gateway med navnet: GatewayUsingARM. 

## <a name="see-also"></a>Se også
| Emne | Beskrivelse |
| :---- | :---- |
| [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) | Denne artikel indeholder en liste over data transformation aktiviteter (såsom HDInsight Hive transformation, du har brugt i dette selvstudium) understøttes af Azure Data Factory. |
| [Planlægning og udførelse af](data-factory-scheduling-and-execution.md) | Denne artikel forklares planlægnings- og udførelse af aspekter af Azure Data Factory programmet model. |
| [Rørledninger](data-factory-create-pipelines.md) | I denne artikel hjælper dig med at forstå rørledninger og aktiviteter i Azure Data Factory og hvordan du kan bruge dem til at oprette til slut datadrevne arbejdsprocesser for scenarie eller business. |
| [Datasæt](data-factory-create-datasets.md) | I denne artikel hjælper dig med at forstå datasæt i Azure Data Factory.
| [Overvåge og administrere rørledninger ved hjælp af overvågning App](data-factory-monitor-manage-app.md) | I denne artikel beskrives, hvordan du kan overvåge, administrere og foretage fejlfinding rørledninger med kommandoen overvågning og administration af App. 

  

