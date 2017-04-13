<properties 
    pageTitle="Hive aktivitet" 
    description="Få mere at vide, hvordan du kan bruge den Hive aktivitet på en Azure data fabrik til at køre Hive forespørgsler på en i-demand/dine egne HDInsight klynge." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Hive aktivitet
> [AZURE.SELECTOR]
[Hive](data-factory-hive-activity.md)  
[Gris](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
[Machine Learning](data-factory-azure-ml-batch-execution-activity.md) 
[En lagret Procedure](data-factory-stored-proc-activity.md)
[Data sø Analytics U-SQL](data-factory-usql-activity.md)
[.NET brugerdefineret](data-factory-use-custom-activities.md)

HDInsight Hive aktiviteten i en Data Factory [pipeline](data-factory-create-pipelines.md) udfører Hive forespørgsler på [din egen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller [efter behov](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-baserede HDInsight klynge. I denne artikel bygger på artiklen [data transformation aktiviteter](data-factory-data-transformation-activities.md) , som viser en generel oversigt over transformation af data og de understøttede transformation aktiviteter.

## <a name="syntax"></a>Syntaksen for

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
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
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Syntaksen for detaljer

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Navn | Navnet på aktiviteten | Ja
Beskrivelse | En beskrivelse af, hvad aktiviteten skal bruges til | Nej
type | HDinsightHive | Ja
input | Input consumed ved Hive aktiviteten | Nej
output | Output produceret af Hive aktivitet | Ja 
linkedServiceName | Reference til den HDInsight-klynge, der er registreret som en sammenkædet tjeneste i Data Factory | Ja 
script | Angive Hive script indbygget | Nej
Scriptsti | Gemme Hive scriptet i en Azure blob-lager, og Angiv stien til filen. Bruge egenskaben 'script' eller 'scriptPath'. Begge kan ikke bruges sammen. Filnavnet er store og små bogstaver. | Nej 
definerer | Angive parametre som nøgle/værdi-par for henvises til i Hive scriptet ved hjælp af 'hiveconf'  | Nej

## <a name="example"></a>Eksempel

Lad os se et eksempel på game logfiler analytics, hvor du vil identificere den tid, der af brugere spil startet af din virksomhed. 

Følgende loggen er en game eksempel-log, som er komma (`,`) adskilt og indeholder følgende felter – ProfileID, SessionStart, varighed, SrcIPAddress og GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

Den **Hive script** til at behandle disse data:

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

For at udføre dette Hive-script i en Data Factory rørledning, skal du gøre følgende

1. Oprette en sammenkædet tjeneste for at registrere [din egen HDInsight beregne klynge](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurere [efter behov HDInsight beregne klynge](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Lad os ringe sammenkædede tjenesten "HDInsightLinkedService".
2. Oprette en [sammenkædet service](data-factory-azure-blob-connector.md) for at konfigurere forbindelsen til Azure Blob-lager, der er vært for dataene. Lad os ringe sammenkædede tjenesten "StorageLinkedService"
3. Oprette [datasæt](data-factory-create-datasets.md) , der peger på input og outputdata. Lad os ringe input datasættet "HiveSampleIn" og output datasættet "HiveSampleOut"
4. Kopiér Hive forespørgslen som en fil til Azure Blob-lager konfigureret i trin #2. Hvis lagerplads for vært dataene er forskellig fra den, der er vært for denne forespørgselsfil, oprette en separat Azure-lager, der er sammenkædet tjeneste og refererer til det i aktiviteten. Brug **scriptPath **til at angive stien til hive forespørgselsfil og **scriptLinkedService** til at angive den Azure lagerplads, der indeholder script-fil. 

    > [AZURE.NOTE] Du kan også angive Hive script indbygget i definitionen af aktivitet ved hjælp af egenskaben **script** . Vi anbefaler ikke denne fremgangsmåde som alle specialtegn i scriptet i JSON dokument behov escape-tegn og kan give problemer med fejlfinding. Den bedste fremgangsmåde er at følge trin #4.
5.  Oprette en rørledning med HDInsightHive aktiviteter. Aktiviteten processer/transformationer dataene.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Installer rørledningen. Se [oprette rørledninger](data-factory-create-pipelines.md) artikel for at få mere at vide. 
7.  Overvåge pipeline ved hjælp af data factory overvågning og administration visninger. Se [overvågnings og administrere Data Factory rørledninger](data-factory-monitor-manage-pipelines.md) artikel for at få mere at vide. 


## <a name="specifying-parameters-for-a-hive-script"></a>Angive parametre for et Hive-script  
I dette eksempel game logfiler er optagelse dagligt til Azure Blob-lager og gemmes i en mappe, der er opdelt med dato og klokkeslæt. Du vil standardelementet Hive scriptet og overfører input mappeplaceringen dynamisk under kørslen og giver også output opdelt med dato og klokkeslæt.

Hvis du vil bruge parameteriseret Hive-script, skal du gøre følgende

- Definer parametre i **definerer**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- I scriptet Hive referere til parameteren ved hjælp af **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Se også
- [Gris aktivitet](data-factory-pig-activity.md)
- [MapReduce aktivitet](data-factory-map-reduce.md)
- [Hadoop Streaming aktivitet](data-factory-hadoop-streaming-activity.md)
- [Kalde knallertmotor programmer](data-factory-spark.md)
- [Kalde R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









