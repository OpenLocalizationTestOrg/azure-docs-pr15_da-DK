<properties 
    pageTitle="Hadoop Streaming aktivitet" 
    description="Få mere at vide, hvordan du kan bruge den Hadoop Streaming aktivitet på en Azure data fabrik for at køre Hadoop Streaming programmer på en i-demand/dine egne HDInsight klynge." 
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
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Hadoop Streaming aktivitet
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)

Du kan bruge den HDInsightStreamingActivity aktivitet kalde et Hadoop Streaming job fra en Azure Data Factory pipeline. Den følgende JSON kodestykke viser syntaksen til brug af HDInsightStreamingActivity i en pipeline JSON-fil. 

Den HDInsight Streaming aktivitet i en Data Factory [pipeline](data-factory-create-pipelines.md) udfører Hadoop Streaming programmer på [din egen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [efter behov](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserede HDInsight klynge. I denne artikel bygger på artiklen [data transformation aktiviteter](data-factory-data-transformation-activities.md) , som viser en generel oversigt over transformation af data og de understøttede transformation aktiviteter.

## <a name="json-sample"></a>JSON eksempel
HDInsight klynge udfyldes automatisk med eksempel programmer (wc.exe og cat.exe) og data (davinci.txt). Som standard er navnet på objektbeholderen, der bruges af HDInsight klynge navnet på klyngen sig selv. Hvis klyngenavnet på din er myhdicluster, vil navnet på objektbeholderen blob, der er knyttet for eksempel være myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Bemærk følgende punkter:

1. Angive **linkedServiceName** til navnet på den sammenkædede tjeneste, der peger på din HDInsight klynge, som køres streaming mapreduce jobbet.
2. Angiv typen aktivitetens til **HDInsightStreaming**.
3. Angiv navnet på mapper eksekverbare for egenskaben **mapper** . I eksemplet er cat.exe den eksekverbare mapper.
4. Angiv navnet på reduktionsrør eksekverbare for egenskaben **reduktionsrør** . I eksemplet er wc.exe den eksekverbare reduktionsrør.
5. Angiv input filen (herunder placeringen) mapperen for egenskaben **input** type. I eksemplet: "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt ": adfsample er objektbeholderen blob, data-eksempel/Gutenberg er mappen, og davinci.txt er blob.
6. Angiv outputfilen (herunder placeringen) på reduktionsrør for egenskaben **output** type. Output fra jobbet Hadoop Streaming skrives til den placering, der er angivet for denne egenskab.
7. Angiv stierne til mapper og reduktionsrør eksekverbare filer i afsnittet **filePaths** . I eksemplet: "adfsample/example/apps/wc.exe" adfsample er objektbeholderen blob, eksempel/apps er mappen, og wc.exe er den eksekverbare fil.
8. Angiv tjenesten Azure-lager, der er sammenkædet, der repræsenterer den Azure lagerplads, der indeholder de filer, der er angivet i sektionen filePaths for egenskaben **fileLinkedService** .
9. Angiv argumenterne for streaming jobbet for egenskaben **argumenter** .
10. Egenskaben **getDebugInfo** er et valgfrit element. Når den er indstillet til fejl, hentes loggene kun ved fejl. Når den er indstillet til alle, hentes logfiler altid uanset udførelse af status.

> [AZURE.NOTE] Som vist i eksemplet, kan du angive et output datasæt for Hadoop Streaming aktiviteten for egenskaben **skriver** . Dette dataset er et dummy datasæt, der kræves til styrer pipeline tidsplanen. Du behøver ikke at angive en hvilken som helst input datasæt for aktiviteten for egenskaben **input** .  

    
## <a name="example"></a>Eksempel
Pipeline i denne gennemgang kører Ordoptælling streaming kort/reducere programmet på din klynge Azure HDInsight. 

### <a name="linked-services"></a>Sammenkædede tjenester

#### <a name="azure-storage-linked-service"></a>Azure sammenkædet lagringstjeneste
Først skal oprette du en sammenkædet tjeneste for at knytte Azure-lager, der bruges af Azure HDInsight klynge til Azure data factory. Hvis du Kopiér og Indsæt følgende kode, ikke har glemt erstatte kontonavn og kontonøgle med navn og nøgle Azure-lager. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight sammenkædet service
Derefter skal oprette du en sammenkædet tjeneste for at knytte din Azure HDInsight klynge til Azure data factory. Hvis du Kopiér og Indsæt følgende kode, udskifte HDInsight klyngenavn med navnet på din HDInsight klynge og ændre brugernavn og en adgangskode for værdier. 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Datasæt

#### <a name="output-dataset"></a>Output datasæt
Pipeline i dette eksempel træder ikke i en hvilken som helst input. Du kan angive et output datasæt for den HDInsight Streaming aktivitet. Dette dataset er et dummy datasæt, der kræves til styrer pipeline tidsplanen. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline

Pipeline i dette eksempel er kun én aktivitet, der er af typen: **HDInsightStreaming**. 

HDInsight klynge udfyldes automatisk med eksempel programmer (wc.exe og cat.exe) og data (davinci.txt). Som standard er navnet på objektbeholderen, der bruges af HDInsight klynge navnet på klyngen sig selv. Hvis klyngenavnet på din er myhdicluster, vil navnet på objektbeholderen blob, der er knyttet for eksempel være myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Se også
- [Hive aktivitet](data-factory-hive-activity.md)
- [Gris aktivitet](data-factory-pig-activity.md)
- [MapReduce aktivitet](data-factory-map-reduce.md)
- [Kalde knallertmotor programmer](data-factory-spark.md)
- [Kalde R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

