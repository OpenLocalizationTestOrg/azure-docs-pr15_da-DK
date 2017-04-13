<properties 
    pageTitle="Kalde MapReduce Program fra Azure Data Factory" 
    description="Lær at behandle data ved at køre MapReduce programmer på en Azure HDInsight klynge fra en Azure data fabrik." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Kalde MapReduce programmer fra Data Factory
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)

HDInsight MapReduce aktiviteten i en Data Factory [pipeline](data-factory-create-pipelines.md) udfører MapReduce programmer på [din egen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [efter behov](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserede HDInsight klynge. I denne artikel bygger på artiklen [data transformation aktiviteter](data-factory-data-transformation-activities.md) , som viser en generel oversigt over transformation af data og de understøttede transformation aktiviteter.

## <a name="introduction"></a>Introduktion 
En rørledning i en Azure data factory behandler data i sammenkædede lagerplads services ved hjælp af sammenkædede Beregn services. Den indeholder en sekvens af aktiviteter, hvor hver aktivitet udfører bestemte behandling. I denne artikel beskrives ved hjælp af den HDInsight MapReduce aktivitet.
 
Se [gris](data-factory-pig-activity.md) og [Hive](data-factory-hive-activity.md) få mere at vide om at køre gris/Hive scripts på en Windows/Linux-baserede HDInsight klynge fra en rørledning ved hjælp af HDInsight gris og Hive aktiviteter. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON for HDInsight MapReduce aktivitet 

I JSON definitionen for HDInsight aktiviteten: 
 
1. Angiv **typen** **aktivitet** til **HDInsight**.
3. Angiv navnet på klassen for **Klassenavn** egenskab.
4. Angiv stien til filen glas, inklusive filnavnet for **jarFilePath** egenskab.
5. Angiv den sammenkædede tjeneste, der refererer til Azure Blob-lager, der indeholder filen glas for **jarLinkedService** egenskab.   
6. Angive argumenter til programmet MapReduce i afsnittet **argumenter** . På kørselstidspunktet, du ser et par ekstra argumenter (for eksempel: mapreduce.job.tags) fra MapReduce framework. For at skelne din argumenter med argumenterne MapReduce, bør du overveje at bruge både indstillingen og værdi som argumenter, som vist i følgende eksempel (- s - input, – output osv., er indstillingerne for efterfulgt af deres værdier).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

Du kan bruge den HDInsight MapReduce aktivitet til at køre en MapReduce glas fil på en HDInsight klynge. I følgende eksempel JSON definitionen af en rørledning, er den HDInsight aktivitet konfigureret til at køre en Mahout JAR fil.

## <a name="sample-on-github"></a>Eksempel på GitHub
Du kan hente en stikprøve til brug af den HDInsight MapReduce aktivitet fra: [Data Factory eksempler på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Køre programmet Ordoptælling
Pipeline i dette eksempel køres programmet Ordoptælling kort/reducere på din klynge Azure HDInsight.   

### <a name="linked-services"></a>Sammenkædede tjenester
Først skal oprette du en sammenkædet tjeneste for at knytte Azure-lager, der bruges af Azure HDInsight klynge til Azure data factory. Hvis du Kopiér og Indsæt følgende kode, ikke har glemt erstatte **kontonavn** og **kontonøgle** med navn og nøgle Azure-lager. 

#### <a name="azure-storage-linked-service"></a>Azure sammenkædet lagringstjeneste

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
Derefter skal oprette du en sammenkædet tjeneste for at knytte din Azure HDInsight klynge til Azure data factory. Hvis du Kopiér og Indsæt følgende kode, udskifte **HDInsight klyngenavn** med navnet på din HDInsight klynge og ændre brugernavn og en adgangskode for værdier.   

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
Pipeline i dette eksempel træder ikke i en hvilken som helst input. Du kan angive et output datasæt for den HDInsight MapReduce aktivitet. Dette dataset er et dummy datasæt, der kræves til styrer pipeline tidsplanen.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Pipeline
Pipeline i dette eksempel er kun én aktivitet, der er af typen: HDInsightMapReduce. Nogle af de vigtige egenskaber i JSON er: 

Egenskaben | Noter
:-------- | :-----
type | Typen skal angives til **HDInsightMapReduce**. 
Klassenavn | Navnet på klassen er: **wordcount**
jarFilePath | Stien til filen glas, der indeholder klassen. Hvis du Kopiér og Indsæt følgende kode, Glem ikke at ændre navnet på klyngen. 
jarLinkedService | Azure-lager sammenkædet tjeneste, som indeholder fil, glas. Denne sammenkædede tjeneste refererer til opbevaring, der er knyttet til HDInsight klyngen. 
argumenter | Programmet wordcount kræver to argumenter, et input og output. Filen er filen davinci.txt.
frekvens/interval | Værdierne for disse egenskaber er ens output datasættet. 
linkedServiceName | refererer til tjenesten HDInsight sammenkædet, du oprettede tidligere.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Køre knallertmotor programmer
Du kan bruge MapReduce aktivitet for at køre knallertmotor programmer på din HDInsight Spark klynge. Du kan finde oplysninger i [kalde knallertmotor programmer fra Azure Data Factory](data-factory-spark.md) .  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Se også
- [Hive aktivitet](data-factory-hive-activity.md)
- [Gris aktivitet](data-factory-pig-activity.md)
- [Hadoop Streaming aktivitet](data-factory-hadoop-streaming-activity.md)
- [Kalde knallertmotor programmer](data-factory-spark.md)
- [Kalde R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
