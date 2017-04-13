<properties 
    pageTitle="Gris aktivitet" 
    description="Få mere at vide, hvordan du kan bruge den gris aktivitet på en Azure data fabrik til at køre gris scripts på en i-demand/dine egne HDInsight klynge." 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Gris aktivitet
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)

HDInsight gris aktivitet i en Data Factory [pipeline](data-factory-create-pipelines.md) udfører gris forespørgsler på [din egen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [efter behov](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserede HDInsight klynge. I denne artikel bygger på artiklen [data transformation aktiviteter](data-factory-data-transformation-activities.md) , som viser en generel oversigt over transformation af data og de understøttede transformation aktiviteter.

## <a name="syntax"></a>Syntaksen for

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
                "inputs": [
                    {
                        "name": "input tables"
                    }
                ],
                "outputs": [
                    {
                        "name": "output tables"
                    }
                ],
                "linkedServiceName": "MyHDInsightLinkedService",
                "typeProperties": {
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Syntaksen for detaljer

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Navn | Navnet på aktiviteten | Ja
Beskrivelse | En beskrivelse af, hvad aktiviteten skal bruges til | Nej
type | HDinsightPig | Ja
input | En eller flere input consumed ved gris aktivitet | Nej
output | En eller flere output produceret af gris aktivitet | Ja
linkedServiceName | Reference til den HDInsight-klynge, der er registreret som en sammenkædet tjeneste i Data Factory | Ja
script | Angive gris script indbygget | Nej
Scriptsti | Gemme gris scriptet i en Azure blob-lager, og Angiv stien til filen. Bruge egenskaben 'script' eller 'scriptPath'. Begge kan ikke bruges sammen. Filnavnet er store og små bogstaver. | Nej
definerer | Angive parametre som nøgle/værdi-par for henvises til i scriptet gris | Nej

## <a name="example"></a>Eksempel

Lad os se et eksempel på game logfiler analytics, hvor du vil identificere den tid, der ved spillere spil startet af din virksomhed.
 
Følgende eksempel game loggen er et komma (,) adskilt fil. Den indeholder følgende felter – ProfileID, SessionStart, varighed, SrcIPAddress og GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

**Gris script** til at behandle disse data:

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

For at udføre dette gris script i en Data Factory pipeline, skal du gøre følgende:

1. Oprette en sammenkædet tjeneste for at registrere [din egen HDInsight beregne klynge](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurere [efter behov HDInsight beregne klynge](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Lad os ringe sammenkædede tjenesten **HDInsightLinkedService**.
2.  Oprette en [sammenkædet service](data-factory-azure-blob-connector.md) for at konfigurere forbindelsen til Azure Blob-lager, der er vært for dataene. Lad os ringe sammenkædede tjenesten **StorageLinkedService**.
3.  Oprette [datasæt](data-factory-create-datasets.md) , der peger på input og outputdata. Lad os ringe input datasættet **PigSampleIn** og output datasættet **PigSampleOut**.
4.  Kopiere gris forespørgslen i en fil på Azure Blob-lager, der er konfigureret i trin #2. Hvis den Azure lagerplads, der er vært dataene er forskellig fra dét, der er vært for forespørgselsfilen, kan du oprette en separat Azure-lager, der er sammenkædet tjeneste. Referere til tjenesten sammenkædede i aktivitet konfigurationen. Brug **scriptPath **til at angive stien til gris scriptfil og **scriptLinkedService**. 
    
    > [AZURE.NOTE] Du kan også angive gris script indbygget i definitionen af aktivitet ved hjælp af egenskaben **script** . Men vi anbefaler ikke denne fremgangsmåde som alle specialtegn i script behov escape-tegn og kan give problemer med fejlfinding. Den bedste fremgangsmåde er at følge trin #4.
5. Opret rørledningen med HDInsightPig aktivitet. Denne aktivitet behandler inputdataene ved at køre gris script på HDInsight klynge.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Installer rørledningen. Se [oprette rørledninger](data-factory-create-pipelines.md) artikel for at få mere at vide. 
7. Overvåge pipeline ved hjælp af data factory overvågning og administration visninger. Se [overvågnings og administrere Data Factory rørledninger](data-factory-monitor-manage-pipelines.md) artikel for at få mere at vide.

## <a name="specifying-parameters-for-a-pig-script"></a>Angive parametre for et gris script 

Overvej følgende eksempel: game logfiler optagelse dagligt til Azure Blob-lager og gemmes i en mappe partitioneret baseret på dato og klokkeslæt. Du vil standardelementet gris scriptet og overfører input mappeplaceringen dynamisk under kørslen og giver også output opdelt med dato og klokkeslæt.
 
Hvis du vil bruge parameteriseret gris script, skal du gøre følgende:

- Definer parametre i **definerer**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- I scriptet gris referere til de parametre, ved hjælp af '**$parameterName**', som vist i følgende eksempel:

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Se også
- [Hive aktivitet](data-factory-hive-activity.md)
- [MapReduce aktivitet](data-factory-map-reduce.md)
- [Hadoop Streaming aktivitet](data-factory-hadoop-streaming-activity.md)
- [Kalde knallertmotor programmer](data-factory-spark.md)
- [Kalde R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


