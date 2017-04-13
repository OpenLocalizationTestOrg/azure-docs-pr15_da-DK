<properties 
    pageTitle="Bruge Machine Learning aktiviteter | Microsoft Azure" 
    description="Beskriver, hvordan du opretter oprette skønnet rørledninger ved hjælp af Azure Data Factory og Azure maskine læring" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Oprette skønnet rørledninger ved hjælp af Azure Machine Learning aktiviteter   
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduktion

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) gør det muligt at oprette, teste og implementere skønnet analytics løsninger. Fra et overordnet synspunkt, er det gjort med tre trin: 

1. **Opret et kursus forsøg**. Du kan udføre dette trin ved hjælp af Azure ML Studio. ML studio er et samarbejde, hvor visuelle udviklingsmiljø, som du kan bruge til at undervise og teste en skønnet analytics-model ved hjælp af kursus data.
2. **Konvertere den til en skønnet forsøg**. Når modellen er blevet uddannelse med eksisterende data, og du er klar til at bruge det til at give nye data, skal du forberede og strømline din forsøg resultatværdier.
3. **Installere det som en webtjeneste**. Du kan publicere din vurdering forsøg som en Azure-webtjeneste. Du kan sende data til din model via denne web service slutpunkt og modtage resultatet prognoser fra modellen.  

Azure Data Factory kan du nemt oprette rørledninger, der bruger en publiceret [Azure Machine Learning] [ azure-machine-learning] -webtjeneste for skønnet analyser. Se [Introduktion til Azure Data Factory](data-factory-introduction.md) og [oprette din første pipeline](data-factory-build-your-first-pipeline.md) artikler for at komme hurtigt i gang med Azure Data Factory-tjenesten. 

Bruger **Batchen udførelse af aktivitet** i en Azure Data Factory pipeline, kan du aktivere en Azure ML webtjeneste for at gøre prognoser på dataene i batchen. Se [aktivering af en Azure ML webtjeneste bruger batchen udførelse af aktiviteten](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) afsnittet Yderligere oplysninger.

I tidens løb skal skønnet-modeller i de Azure ML vundne forsøg være retrained ved hjælp af nye input datasæt. Du kan henblik på omstilling en Azure ML model fra en Data Factory rørledning ved at benytte følgende fremgangsmåde: 

1. Publicere kursus forsøget (ikke skønnet forsøg) som en webtjeneste. Du kan udføre dette trin i Azure ML Studio, som du gjorde for at eksponere skønnet forsøg som en webtjeneste i det forrige scenario.
2. Brug Azure ML batchen udførelse af aktivitet til at kalde webtjenesten til uddannelse forsøget. Grundlæggende, kan du bruge den Azure ML batchen udførelse af aktivitet til at aktivere både kursus webtjeneste og vurdering webtjeneste. 
  
Når du er færdig med omskoling, vil du opdatere vurdering webtjenesten (skønnet forsøg vises som en webtjeneste) med den nyligt erfaren model. Her er trinnene: 

1. Tilføje en ikke-standard slutpunkt til vurdering webtjenesten. Standardslutpunkt for webtjenesten kan ikke opdateres, så du har brug at oprette et ikke-standard slutpunkt ved hjælp af portalen Azure. Artiklen [Oprette slutpunkter](../machine-learning/machine-learning-create-endpoint.md) for både grundlæggende oplysninger og trinvise fremgangsmåde.
2. Opdater eksisterende Azure ML sammenkædet tjenester resultatværdier for at bruge det ikke-standard slutpunkt. Begynde at bruge det nye slutpunkt til at bruge den webtjeneste, der er opdateret.
3. Brug **Azure ML opdatere ressource aktivitet** til at opdatere webtjenesten med den nyligt erfaren model.  

Se [opdatere Azure ML modellerne ved hjælp af den opdatering ressource aktivitet](#updating-azure-ml-models-using-the-update-resource-activity) afsnittet Yderligere oplysninger. 

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>Aktivering af en webtjeneste via batchen udførelse af aktivitet

Du bruger Azure Data Factory til dirigerer flytning af data og behandling, og udfør derefter batchen udførelse af ved hjælp af Azure Machine Learning. Her er de øverste trin:

1. Oprette en Azure Machine Learning sammenkædet tjeneste. Du skal bruge følgende:
    1. **Anmode om URI** om batchen udførelse af API. Du kan finde den Request URI ved at klikke på linket **BATCHEN udførelse af** web services på siden.
    1. **API-nøgle** for publicerede Azure Machine Learning-webtjenesten. Du kan finde API-nøglen ved at klikke på den webtjeneste, som du har udgivet. 
 2. Brug **AzureMLBatchExecution** aktivitet.

    ![Machine Learning Dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

    ![Batchen URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>Scenarie: Forsøg ved hjælp af Web service input/output, der refererer til data i Azure Blob-lager
I dette scenarie skal Azure Machine Learning webtjenesten gør prognoser ud fra data i en fil i en Azure blob-lager og gemmer forudsigelse resultaterne i blob-lager. Følgende JSON definerer en Data Factory rørledning med en AzureMLBatchExecution aktivitet. Aktiviteten har datasættet **DecisionTreeInputBlob** som input og **DecisionTreeResultBlob** som output. **DecisionTreeInputBlob** overføres som input til webtjenesten ved hjælp af egenskaben **webServiceInput** JSON. **DecisionTreeResultBlob** der overføres som output til webtjenesten ved hjælp af egenskaben **webServiceOutputs** JSON.  

> [AZURE.IMPORTANT] 
> Hvis webtjenesten tager flere input, kan du bruge egenskaben **webServiceInputs** i stedet for ved hjælp af **webServiceInput**. Se afsnittet [webtjeneste kræver flere oplysninger](#web-service-requires-multiple-inputs) om et eksempel på brug af egenskaben webServiceInputs.
>  
> Datasæt, der refereres til ved **webServiceInput**/**webServiceInputs** og **webServiceOutputs** egenskaber (i **typeProperties**) også skal medtages i aktivitet **input** og **skriver**.
> 
> I din Azure ML forsøg har web service input og output porte og globale parametre standardnavne ("input1", "input2"), som du kan tilpasse. De navne, du bruger til webServiceInputs, webServiceOutputs og globalParameters indstillinger skal svare nøjagtigt til navnene i forsøg. Du kan få vist eksempel anmodning om data på siden batchen udførelse af hjælp til din Azure ML slutpunkt at bekræfte den forventede tilknytning. 


    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "DecisionTreeInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "DecisionTreeResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob"
                }                
            },
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

> [AZURE.NOTE] Kun input og output AzureMLBatchExecution aktivitetens kan overføres som parametre til webtjenesten. I det ovenstående JSON-kodestykket er DecisionTreeInputBlob for eksempel en input til AzureMLBatchExecution aktivitet, som der overføres som input til webtjenesten via webServiceInput parameter.   

### <a name="example"></a>Eksempel

I dette eksempel bruger Azure-lager til at holde både input- og outputområder data. 

Vi anbefaler, at du gennemfører [oprette din første rørledning med Data Factory] [ adf-build-1st-pipeline] selvstudium før du gennemgår dette eksempel. Brug Data Factory-Editor til at oprette Data Factory elementer (sammenkædede tjenester, datasæt, pipeline) i dette eksempel.   
 

1. Oprette en **sammenkædet tjenesten** for din **Azure-lager**. Hvis input- og outputområder filerne er på forskellige lagerplads konti, skal du to sammenkædede tjenester. Her er en JSON-eksempel:

        {
          "name": "StorageLinkedService",
          "properties": {
            "type": "AzureStorage",
            "typeProperties": {
              "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
            }
          }
        }

2. Oprette **input** Azure Data Factory **datasæt**. I modsætning til nogle andre Data Factory datasæt skal disse datasæt indeholde både **mappesti** og **filnavn** værdier. Du kan bruge partitionering årsagen hver batchen afvikling (hver data udsnit) til at behandle eller landbrugsprodukter entydige input og output filer til. Du skal muligvis medtage nogle mod strømmen aktivitet for at transformere input i CSV-filformatet og placere det i lagerplads konto for hvert udsnit. Det er tilfældet, du ikke vil medtage de indstillinger for **ekstern** og **externalData** , der er vist i følgende eksempel, og din DecisionTreeInputBlob ville være output datasættet i en anden aktivitet.

        {
          "name": "DecisionTreeInputBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/input",
              "fileName": "in.csv",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Day",
              "interval": 1
            },
            "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
            }
          }
        }
    
    Indtast csv-filen skal have rækken med kolonneoverskrifter. Hvis du bruger den **Kopi aktivitet** til at oprette/Flyt csv i blob-lager, skal du indstille sink egenskaben **blobWriterAddHeader** til **Sand**. Eksempel:
    
         sink: 
         {
             "type": "BlobSink",     
             "blobWriterAddHeader": true 
         }
     
    Hvis CSV-filen ikke har kolonneoverskriften, kan du se følgende fejl: **fejl i aktivitet: fejl ved læsning af strengen. Uventet token: StartObject. Stien ", linjen 1, skal du placere 1**.
3. Oprette **output** Azure Data Factory **datasæt**. I dette eksempel bruges partitionering til at oprette et entydigt outputsti for hver udførelse af udsnit. Uden partitionering, vil aktiviteten overskrive filen.

        {
          "name": "DecisionTreeResultBlob",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
              "folderPath": "azuremltesting/scored/{folderpart}/",
              "fileName": "{filepart}result.csv",
              "partitionedBy": [
                {
                  "name": "folderpart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "yyyyMMdd"
                  }
                },
                {
                  "name": "filepart",
                  "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HHmmss"
                  }
                }
              ],
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Day",
              "interval": 15
            }
          }
        }

4. Oprette en **sammenkædet tjenesten** af typen: **AzureMLLinkedService**, give API centrale og modellere batchen udførelse af URL-adresse.
        
        {
          "name": "MyAzureMLLinkedService",
          "properties": {
            "type": "AzureML",
            "typeProperties": {
              "mlEndpoint": "https://[batch execution endpoint]/jobs",
              "apiKey": "[apikey]"
            }
          }
        }
5. Til sidst skal oprette en rørledning, der indeholder en **AzureMLBatchExecution** aktivitet. På kørselstidspunktet udfører pipeline følgende trin:
    1. Henter placeringen af filen input fra din Skriv datasæt.
    2. Aktiverer Azure Machine Learning batchen udførelse af API
    3. Kopierer batchen udførelse af output til blob ud i dit output datasæt. 

    > [AZURE.NOTE] AzureMLBatchExecution aktivitet kan have nul eller flere input og en eller flere output.

        {
          "name": "PredictivePipeline",
          "properties": {
            "description": "use AzureML model",
            "activities": [
              {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                  {
                    "name": "DecisionTreeInputBlob"
                  }
                ],
                "outputs": [
                  {
                    "name": "DecisionTreeResultBlob"
                  }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                  "concurrency": 3,
                  "executionPriorityOrder": "NewestFirst",
                  "retry": 1,
                  "timeout": "02:00:00"
                }
              }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
          }
        }

    **Start** og **Stop** dato/klokkeslæt være i [ISO-formatet](http://en.wikipedia.org/wiki/ISO_8601). For eksempel: 2014-10-14T16:32:41Z. **Sluttidspunktet** er valgfrit. Hvis du ikke angiver værdien for egenskaben **slutningen** , beregnes som "**start + 48 timer.**" For at køre rørledningen på ubestemt tid, skal du angive **9999-09-09** som værdien for egenskaben **slutningen** . Du kan finde oplysninger om JSON egenskaber i [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) .

    > [AZURE.NOTE] Angive input til AzureMLBatchExecution er aktivitet valgfrit. 

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>Scenarie: Forsøg ved hjælp af læser/skriver moduler til at referere til data i forskellige lagre

En anden almindelige scenarie, når du opretter Azure ML forsøg er at bruge læser og Writer moduler. Modulet læser bruges til at lægge data i et forsøg og modulet writer er at gemme data fra din forsøg. Få mere at vide om læser og writer moduler, kan du se [læser](https://msdn.microsoft.com/library/azure/dn905997.aspx) og [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) emnerne MSDN-biblioteket.     

Når du bruger skærmlæsere og writer moduler, er det god praksis at bruge en Web service-parameter for hver egenskab for disse læser/skriver moduler. Disse web parametre gør det muligt at konfigurere værdierne under runtime. For eksempel du kan oprette et forsøg med et læser modul, der bruger en Azure SQL-Database: XXX.database.windows.net. Når webtjenesten er blevet installeret, vil du aktivere forbrugere af webtjenesten til at angive en anden Azure SQL Server kaldet YYY.database.windows.net. Du kan bruge parameteren Web service tillade denne værdi, der skal konfigureres.

> [AZURE.NOTE] Web service input og output er forskellige fra Web Serviceparametre. I det første scenario, har du set hvordan et input og output kan angives for en webtjeneste til Azure ML. I dette scenarie skal overføre du parametre til en webtjeneste, der svarer til egenskaberne for læser/skriver moduler. 

Lad os se på et scenario for ved hjælp af Web Serviceparametre. Du har en udløst Azure Machine Learning-webtjeneste, der bruger et læser modul til at læse data fra en af de datakilder, der understøttes af Azure Machine Learning (for eksempel: Azure SQL-Database). Når batchen udførelsen er udført, skal skrives resultaterne ved hjælp af et Writer-modul (Azure SQL-Database).  Ingen web service input og output defineres forsøg. I dette tilfælde anbefaler vi, at du konfigurerer relevante web Serviceparametre for læser og writer moduler. Denne konfiguration kan læser/skriver moduler skal konfigureres, når du bruger AzureMLBatchExecution aktiviteten. Du angiver Web Serviceparametre i sektionen **globalParameters** i aktivitet JSON på følgende måde. 


    "typeProperties": {
        "globalParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }

Du kan også bruge [Data Factory-funktioner](https://msdn.microsoft.com/library/dn835056.aspx) i sende værdier for parametrene Web service som vist i følgende eksempel:

    "typeProperties": {
        "globalParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Parametrene Web-tjenesten er store og små bogstaver, så sørg for, at de navne, du angiver i aktiviteten JSON svarer til dem, der vises af webtjenesten. 

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Brug af et læser modul til at læse data fra flere filer i Azure Blob
Stor data rørledninger med aktiviteter som gris og Hive kan give en eller flere output filer uden filtypenavn. Når du angiver en ekstern Hive-tabel, kan der eksempelvis gemmes data for den eksterne Hive-tabel i Azure blob-lager med den følgende navn 000000_0. Du kan bruge modulet reader i et forsøg for at læse flere filer, og du kan bruge dem til prognoser. 

Når du bruger modulet læser i Azure Machine Learning forsøg, kan du angive Azure Blob som input. Filerne i Azure blob-lager kan være outputfiler (eksempel: 000000_0), der er oprettet med et gris og Hive script, der kører på HDInsight. Modulet læser gør det muligt at læse filer (med uden filtypenavn) ved at konfigurere den **stien til objektbeholder til directory/blob**. **Stien til objektbeholder** peger på objektbeholder og **directory/blob** peger på mappe, der indeholder filerne, som vist på følgende billede. Stjernen det vil sige, \*) **Angiver, at alle filerne i beholderen/mappe (det vil sige, data/aggregateddata/år = måned-2014-6 /\*)** læses som en del af forsøget.

![Azure Blob-egenskaber](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### <a name="example"></a>Eksempel 
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>Rørledning med AzureMLBatchExecution aktiviteter med Web Serviceparametre

    {
      "name": "MLWithSqlReaderSqlWriter",
      "properties": {
        "description": "Azure ML model with sql azure reader/writer",
        "activities": [
          {
            "name": "MLSqlReaderSqlWriterActivity",
            "type": "AzureMLBatchExecution",
            "description": "test",
            "inputs": [
              {
                "name": "MLSqlInput"
              }
            ],
            "outputs": [
              {
                "name": "MLSqlOutput"
              }
            ],
            "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
                "globalParameters": {
                    "Database server name": "<myserver>.database.windows.net",
                    "Database name": "<database>",
                    "Server user account name": "<user name>",
                    "Server user account password": "<password>"
                }              
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            },
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }
 
I eksemplet ovenfor JSON:

- Tjenesten udløst Azure Machine Learning Web bruger en læser og et writer modul til at læse-og skriveadgang data fra/til Azure SQL-Database. Denne webtjeneste Fremviser følgende fire parametre: servernavn, databasenavnet, Server-konto brugernavn og adgangskode til brugerkonto Server-Database.  
- **Start** og **Stop** dato/klokkeslæt være i [ISO-formatet](http://en.wikipedia.org/wiki/ISO_8601). For eksempel: 2014-10-14T16:32:41Z. **Sluttidspunktet** er valgfrit. Hvis du ikke angiver værdien for egenskaben **slutningen** , beregnes som "**start + 48 timer.**" For at køre rørledningen på ubestemt tid, skal du angive **9999-09-09** som værdien for egenskaben **slutningen** . Du kan finde oplysninger om JSON egenskaber i [JSON Scripting Reference](https://msdn.microsoft.com/library/dn835050.aspx) .

### <a name="other-scenarios"></a>Andre scenarier

#### <a name="web-service-requires-multiple-inputs"></a>Webtjeneste kræver flere input
Hvis webtjenesten tager flere input, kan du bruge egenskaben **webServiceInputs** i stedet for ved hjælp af **webServiceInput**. Datasæt, der refereres til ved **webServiceInputs** skal også medtages i aktivitet **input**.
 
I din Azure ML forsøg har web service input og output porte og globale parametre standardnavne ("input1", "input2"), som du kan tilpasse. De navne, du bruger til webServiceInputs, webServiceOutputs og globalParameters indstillinger skal svare nøjagtigt til navnene i forsøg. Du kan få vist eksempel anmodning om data på siden batchen udførelse af hjælp til din Azure ML slutpunkt at bekræfte den forventede tilknytning.


    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [{
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [{
                    "name": "inputDataset1"
                }, {
                    "name": "inputDataset2"
                }],
                "outputs": [{
                    "name": "outputDataset"
                }],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties": {
                    "webServiceInputs": {
                        "input1": "inputDataset1",
                        "input2": "inputDataset2"
                    },
                    "webServiceOutputs": {
                        "output1": "outputDataset"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }

#### <a name="web-service-does-not-require-an-input"></a>Webtjeneste kræver ikke en input

Azure ML batchen udførelse af web-tjenester kan bruges til at køre arbejdsprocesser, for eksempel R eller Python scripts, som ikke formentlig kræver en hvilken som helst input. Eller forsøget kan være konfigureret med en læser modul, der ikke vist nogen GlobalParameters. Det er tilfældet, vil den AzureMLBatchExecution aktivitet konfigureres på følgende måde:

    {
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### <a name="web-service-does-not-require-an-inputoutput"></a>Webtjeneste kræver ikke input/output
Azure ML batchen udførelse af webtjenesten har muligvis ikke noget webtjeneste output, der er konfigureret. I dette eksempel, der er ingen webtjeneste input eller output og er konfigureret til en hvilken som helst GlobalParameters. Der er stadig konfigureret på aktiviteten selve output, men det er ikke angivet som en webServiceOutput.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web-tjenesten bruger læsere og forfattere og aktivitet kørt kun, når andre aktiviteter har lykkedes

Azure ML web service læser og writer modulerne kan være konfigureret til at køre med eller uden en hvilken som helst GlobalParameters. Dog kan du integrere serviceopkald i en rørledning, der bruger datasæt afhængigheder til at starte tjenesten kun, når nogle før behandling er afsluttet. Du kan også udløse en anden handling, når batchen udførelsen er fuldført ved hjælp af denne metode. Det er tilfældet, kan du express afhængigheder ved hjælp af aktivitet input og output, uden at navngive enhver af dem som webtjeneste input eller output.

    {
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "inputs": [
            {
                "name": "upstreamData1"
            },
            {
                "name": "upstreamData2"
            }
        ],
        "outputs": [
            {
                "name": "downstreamData"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

**Takeaways** er:

-   Hvis din forsøg slutpunkt bruger en webServiceInput: det er repræsenteret af en blob datasæt og er inkluderet i aktivitet input og egenskaben webServiceInput. Ellers skal udelades egenskaben webServiceInput. 
-   Hvis din forsøg slutpunkt bruger webServiceOutput(s): de er repræsenteret af blob datasæt og er inkluderet i aktivitet output og egenskaben webServiceOutputs. Udskriver aktiviteten og webServiceOutputs er tilknyttet af navnet på hver output i forsøget. Ellers skal udelades egenskaben webServiceOutputs.
-   Hvis din forsøg slutpunkt Fremviser globalParameter(s), er de angivet i egenskaben aktivitet globalParameters som nøgle, værdi-par. Ellers skal udelades egenskaben globalParameters. Der skelnes mellem de pågældende taster. [Azure Data Factory-funktioner](data-factory-scheduling-and-execution.md#data-factory-functions-reference) kan anvendes i feltet værdier. 
- Yderligere datasæt kan medtages i aktivitet input og output egenskaberne, uden at der refereres til i aktivitet typeProperties. Disse datasæt styrer udførelse af ved hjælp af udsnit afhængigheder, men ignoreres ellers ved AzureMLBatchExecution aktivitet. 


## <a name="updating-models-using-update-resource-activity"></a>Opdatere modellerne ved hjælp af Update ressource aktivitet
I tidens løb skal skønnet-modeller i de Azure ML vundne forsøg være retrained ved hjælp af nye input datasæt. Når du er færdig med omskoling, vil du opdatere vurdering webtjenesten med retrained ML modellen. Typisk trinnene for at aktivere omskoling og opdaterer Azure ML modeller via webtjenester er: 

1. Oprette et forsøg i [Azure ML Studio](https://studio.azureml.net). 
2. Når du er tilfreds med modellen, bruge Azure ML Studio til at publicere webtjenester til begge de **kursus eksperimentere** og vundne /**skønnet forsøg**.

I følgende tabel beskrives de webtjenester i dette eksempel bruges.  Se [henblik på omstilling Machine Learning modeller fra et program](../machine-learning/machine-learning-retrain-models-programmatically.md) til detaljer.

| Type af webtjeneste | Beskrivelse 
| :------------------ | :---------- 
| **Kursus-webtjeneste** | Modtager kursus data og producerer erfaren modeller. Output fra den omskoling er en .ilearner fil i en Azure Blob-lager.  **Standardslutpunkt** oprettes automatisk for dig, når du publicerer kursus forsøget som en webtjeneste. Du kan oprette flere slutpunkter, men i eksempel bruges kun standardslutpunktet |
| **Vundne webtjeneste** | Modtager umærkede data eksempler, og gør prognoser. Output fra forudsigelse kan have forskellige formularer, som en .csv-fil eller rækker i en Azure SQL-database, afhængigt af konfigurationen af forsøget. Standardslutpunktet oprettes automatisk for dig, når du publicerer skønnet forsøget som en webtjeneste. Oprette det andet **ikke-standard og kan opdateres slutpunkt** ved hjælp af [Azure-portalen](https://manage.windowsazure.com). Du kan oprette flere slutpunkter, men i dette eksempel bruges kun én ikke-standard kan opdateres slutpunkt. Artiklen [Oprette slutpunkter](../machine-learning/machine-learning-create-endpoint.md) for trin.       
 
Følgende billede viser forholdet mellem kursus og pointsystemet slutpunkter i Azure ML. 

![Webtjenester](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Du kan aktivere **kursus webtjeneste** ved hjælp af **Azure ML batchen udførelse af aktivitet**. Aktivering af et kursus webtjeneste er den samme som aktivering en Azure ML webtjeneste (vundne webtjeneste) til vurdering data. De forudgående afsnit dækker Sådan kalde en Azure ML webtjeneste fra en Azure Data Factory pipeline detaljeret. 
  
Du kan aktivere **vundne webtjeneste** ved hjælp af **Azure ML opdatere ressource aktivitet** opdatere webtjenesten med den nyligt erfaren model. Som nævnt i ovenstående tabel, skal du oprette og bruge det ikke-standard kan opdaterbare slutpunkt. Derudover kan opdatere en eksisterende sammenkædet tjenester i dine data factory bruge ikke-standard-slutpunkt, så de altid bruge den nyeste retrained model. 

Du kan finde flere oplysninger i følgende scenario. Der er et eksempel på omskoling og opdatering af Azure ML modeller fra en Azure Data Factory pipeline. 
 
### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>Scenarie: omskoling og opdatere en Azure ML model
Dette afsnit indeholder en stikprøve rørledning, der bruger **Azure ML batchen udførelse af aktivitet** for at omskole en model. Pipeline bruger også **Azure ML opdatere ressource aktivitet** til at opdatere modellen i vurdering webtjenesten. Afsnittet indeholder også JSON kodestykker for alle sammenkædede services, datasæt og pipeline i eksemplet. 

Her er diagramvisning for eksempel rørledning. Som du kan se, Azure ML batchen udførelse af aktivitet tager kursus input og giver et kursus output (iLearner-fil). Azure ML opdatering ressource aktivitet tager dette kursus output og opdaterer modellen i vurdering web tjenesteslutpunkt. Opdater ressource aktivitet medfører ikke noget output. PlaceholderBlob er bare et dummy output datasæt, der kræves af Azure Data Factory-tjenesten for at køre rørledningen. 

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### <a name="azure-blob-storage-linked-service"></a>Azure Blob-lager sammenkædet service:
Azure-lager indeholder følgende data:

- kursus data. Indtast data for Azure ML kursus-webtjenesten.  
- iLearner-fil. Output fra Azure ML kursus-webtjenesten. Denne fil er også input til ressource til opdatering af aktiviteten.  
   
Her er eksempel JSON definitionen af tjenesten sammenkædede: 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
            }
        }
    }


#### <a name="training-input-dataset"></a>Kursus input datasæt:
Følgende datasættet repræsenterer input kursus dataene til webtjenesten Azure ML kursus. Azure ML batchen udførelse af aktiviteten tager dette dataset som input. 

    {
        "name": "trainingData",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "labeledexamples",
                "fileName": "labeledexamples.arff",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "policy": {          
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

#### <a name="training-output-dataset"></a>Kursus output datasæt:
Følgende datasættet repræsenterer iLearner outputfilen fra Azure ML kursus-webtjenesten. Azure ML batchen udførelse af aktivitet giver dette dataset. Dette dataset er også input til Azure ML opdatering ressource aktivitet.

    {
        "name": "trainedModelBlob",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "trainingoutput",
                "fileName": "model.ilearner",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Week",
                "interval": 1
            }
        }
    }

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Sammenkædede-tjeneste til Azure ML kursus slutpunkt 
Den følgende JSON kodestykke definerer en Azure Machine Learning sammenkædet tjeneste, der peger på webtjenesten kursus standardslutpunkt. 

    {   
        "name": "trainingEndpoint",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
                "apiKey": "myKey"
            }
        }
    }

I **Azure ML Studio**, skal du gøre følgende for at hente værdier til **mlEndpoint** og **apiKey**:

1. Klik på **WEB-tjenester** i menuen til venstre.
2. Klik på den **kursus webtjeneste** på listen over webtjenester. 
3. Klik på Kopiér ud for tekstfeltet **API-nøgle** . Indsæt tasten i Udklipsholder i Data Factory JSON-editor.
4. Klik på **Azure ML studio** **BATCHEN udførelse af** kæde.
5. Kopiér **Request URI** i afsnittet **anmode om** og sætte det ind i Data Factory JSON-editoren.   


#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Sammenkædede tjeneste for Azure ML kan opdateres vurdering slutpunkt:
Den følgende JSON kodestykke definerer en Azure Machine Learning sammenkædet tjeneste, der peger på ikke-standard kan opdateres slutpunkt for den vurdering webtjeneste.  

    {
        "name": "updatableScoringEndpoint2",
        "properties": {
            "type": "AzureML",
            "typeProperties": {
                "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
                "apiKey": "endpoint2Key",
                "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
            }
        }
    }


Før oprettelse og implementere en Azure ML sammenkædet service, skal du følge trinnene i [Oprette slutpunkter](../machine-learning/machine-learning-create-endpoint.md) artikel for at oprette en anden (ikke-standard og kan opdateres) slutpunkt for vurdering-webtjenesten.

Når du opretter ikke-standard kan opdateres slutpunktet, kan du gøre følgende:

- Klik på **BATCHEN udførelse af** for at få værdien URI for egenskaben **mlEndpoint** JSON.
- Klik på **Ressource til opdatering af** link for at få værdien URI for egenskaben **updateResourceEndpoint** JSON. API-nøgle er på siden slutpunkt selve (i nederste højre hjørne). 

![kan opdateres slutpunkt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### <a name="placeholder-output-dataset"></a>Pladsholder output datasæt:
Azure ML opdatering ressource aktiviteten genererer ikke noget output. Azure Data Factory kræver dog et output datasæt til styrer tidsplanen for en rørledning. Derfor kan bruge vi et dukke/pladsholder datasæt i dette eksempel.  

    {
        "name": "placeholderBlob",
        "properties": {
            "availability": {
                "frequency": "Week",
                "interval": 1
            },
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "any",
                "format": {
                    "type": "TextFormat"
                }
            }
        }
    }


#### <a name="pipeline"></a>Pipeline
Pipeline har to aktiviteter: **AzureMLBatchExecution** og **AzureMLUpdateResource**. Azure ML batchen udførelse af aktiviteten tager kursus data som input og giver en iLearner fil som output. Aktiviteten kalder webtjenesten kursus (kursus forsøg vises som en webtjeneste) med dataene, indtast kursus og modtager filen ilearner fra webtjeneste. PlaceholderBlob er bare et dummy output datasæt, der kræves af Azure Data Factory-tjenesten for at køre rørledningen. 

![pipeline diagram](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


    {
        "name": "pipeline",
        "properties": {
            "activities": [
                {
                    "name": "retraining",
                    "type": "AzureMLBatchExecution",
                    "inputs": [
                        {
                            "name": "trainingData"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "typeProperties": {
                        "webServiceInput": "trainingData",
                        "webServiceOutputs": {
                            "output1": "trainedModelBlob"
                        }              
                     },
                    "linkedServiceName": "trainingEndpoint",
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "02:00:00"
                    }
                },
                {
                    "type": "AzureMLUpdateResource",
                    "typeProperties": {
                        "trainedModelName": "Training Exp for ADF ML [trained model]",
                        "trainedModelDatasetName" :  "trainedModelBlob"
                    },
                    "inputs": [
                        {
                            "name": "trainedModelBlob"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "placeholderBlob"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "name": "AzureML Update Resource",
                    "linkedServiceName": "updatableScoringEndpoint2"
                }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }


### <a name="reader-and-writer-modules"></a>Læser og Writer moduler

Et almindeligt scenarie til brug af parametre for Web er brugen af Azure SQL læsere og forfattere. Modulet læser bruges til at lægge data i et forsøg fra data management services uden for Azure Machine Learning Studio. Modulet writer er at gemme data fra din forsøg til administration af datatjenester uden for Azure Machine Learning Studio.  

Få mere at vide om Azure Blob/Azure SQL-læser/skriver du se emner i [læser](https://msdn.microsoft.com/library/azure/dn905997.aspx) og [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) på MSDN-biblioteket. Eksemplet ovenfor bruges Azure Blob læser og Azure Blob writer. Dette afsnit beskrives ved hjælp af Azure SQL-læser og Azure SQL writer.


## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

**Q:** Jeg har flere filer, der genereres af min stor data rørledninger. Kan jeg bruge den AzureMLBatchExecution aktivitet til at arbejde på alle filerne?

**A:** Ja. I afsnittet **ved hjælp af et læser modul for at læse data fra flere filer i Azure Blob** få mere at vide. 

## <a name="azure-ml-batch-scoring-activity"></a>Azure ML batchen vundne aktivitet
Hvis du bruger **AzureMLBatchScoring** aktiviteten du integrere systemet med Azure Machine Learning, anbefaler vi, at du bruger den seneste **AzureMLBatchExecution** aktivitet. 

AzureMLBatchExecution aktiviteten introduceret i August 2015 version af Azure SDK og Azure PowerShell.

Hvis du vil fortsætte med at bruge AzureMLBatchScoring aktiviteten, skal du fortsætte læse gennem i dette afsnit.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure ML batchen vundne aktivitet med Azure-lager til input/output 

    {
      "name": "PredictivePipeline",
      "properties": {
        "description": "use AzureML model",
        "activities": [
          {
            "name": "MLActivity",
            "type": "AzureMLBatchScoring",
            "description": "prediction analysis on batch input",
            "inputs": [
              {
                "name": "ScoringInputBlob"
              }
            ],
            "outputs": [
              {
                "name": "ScoringResultBlob"
              }
            ],
            "linkedServiceName": "MyAzureMLLinkedService",
            "policy": {
              "concurrency": 3,
              "executionPriorityOrder": "NewestFirst",
              "retry": 1,
              "timeout": "02:00:00"
            }
          }
        ],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
      }
    }

### <a name="web-service-parameters"></a>Web Serviceparametre
For at angive værdier for Web Serviceparametre, skal du tilføje et **typeProperties** afsnit i sektionen **AzureMLBatchScoringActivty** i pipeline JSON som vist i følgende eksempel: 

    "typeProperties": {
        "webServiceParameters": {
            "Param 1": "Value 1",
            "Param 2": "Value 2"
        }
    }


Du kan også bruge [Data Factory-funktioner](https://msdn.microsoft.com/library/dn835056.aspx) i sende værdier for parametrene Web service som vist i følgende eksempel:

    "typeProperties": {
        "webServiceParameters": {
           "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
        }
    }
 
> [AZURE.NOTE] Parametrene Web-tjenesten er store og små bogstaver, så sørg for, at de navne, du angiver i aktiviteten JSON svarer til dem, der vises af webtjenesten. 

## <a name="see-also"></a>Se også

- [Azure blogindlæg: Introduktion til Azure Data Factory og Azure maskine læring](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 
