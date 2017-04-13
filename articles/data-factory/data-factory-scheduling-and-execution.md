<properties
    pageTitle="Planlægning og udførelse af med Data Factory | Microsoft Azure"
    description="Få mere at vide planlægnings- og udførelse af aspekter af Azure Data Factory programmet model."
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
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="spelluru"/>

# <a name="data-factory-scheduling-and-execution"></a>Data Factory planlægnings- og udførelse af
Denne artikel forklares planlægnings- og udførelse af aspekter af Azure Data Factory programmet modellen. 

## <a name="prerequisites"></a>Forudsætninger
Denne artikel forudsætter, at du forstår grundlæggende om Data Factory programmet model begreber, herunder aktivitet, rørledninger, sammenkædede tjenester og datasæt. Få grundlæggende begreber i Azure Data Factory, i følgende artikler:

- [Introduktion til Data Factory](data-factory-introduction.md)
- [Rørledninger](data-factory-create-pipelines.md)
- [Datasæt](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>Planlægge en aktivitet

Du kan angive en tilbagevendende tidsplan for en aktivitet med afsnittet scheduler aktivitetens JSON. For eksempel kan du planlægge en aktivitet hver time på følgende måde:

    "scheduler": {
        "frequency": "Hour",
        "interval": 1
    },  

![Eksempel på Scheduler](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Som vist i diagrammet, opretter angive en tidsplan for aktiviteten en serie af tumbling windows. Tumbling windows er en serie af fast størrelse, ikke-overlappende, sammenhængende tidsintervaller. Disse logiske tumbling vinduer for aktiviteten kaldes *aktivitet i windows*.

Du kan åbne et tidsinterval, der er knyttet til aktivitetsvinduet med [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) og [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) systemvariabler i aktiviteten JSON for aktivitetsvinduet i øjeblikket. Du kan bruge disse variabler til forskellige formål i din aktivitet JSON. For eksempel kan du bruge dem til at vælge data fra input- og outputområder datasæt, der repræsenterer klokkeslæt seriedata.

Egenskaben **scheduler** understøtter de samme underegenskaber som egenskaben **tilgængelighed** i et datasæt. Du kan finde oplysninger i [Dataset tilgængelighed](data-factory-create-datasets.md#Availability) . Eksempler: planlægge på en bestemt tidsforskydning eller angivelse af tilstand til at justere behandling i starten eller slutningen af intervallet for aktivitetsvinduet.

Du kan angive **scheduler** egenskaberne for en aktivitet, men denne egenskab er **valgfrit**. Hvis du angiver en egenskab, skal den til den cadence, du angiver i output datasæt definition. Output datasæt er i øjeblikket, hvad drev tidsplanen, så du skal oprette et datasæt output, selvom aktiviteten ikke medfører noget output. Hvis aktiviteten ikke tager alt input, kan du springe over oprettelse af input datasættet.

## <a name="time-series-datasets-and-data-slices"></a>Tid serie datasæt og data udsnit

Serie tidsdata er en løbende sekvens af datapunkter, der typisk består af flere på hinanden følgende målinger over et tidsinterval. Almindelige eksempler på serier tidsdata omfatter føler data og programmet telemetridata.

Med Data Factory, kan du behandle gang seriedata i en batchopdelte måde med aktivitet kører. Der er typisk en tilbagevendende cadence hvormed inputdataene ankommer, og Medtag data skal være produceret. Denne cadence er formateret ved at angive **tilgængelighed** i datasættet på følgende måde:

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Hver enhed data consumed og produceret af en aktivitet Kør kaldes et udsnit af data. I det følgende diagram viser et eksempel på en aktivitet med ét input dataset og en udgang datasæt. Disse datasæt har **tilgængelighed** indstillet til en time frekvens.

![Tilgængelighed scheduler](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Det foregående diagram viser de hver time dataudsnit for input- og outputområder datasæt. Diagrammet viser tre input udsnit, der er klar til behandling. 10-11 AM aktivitet er i gang, producerer 10-11 AM output udsnit.

Du kan få adgang til et tidsinterval, der er knyttet til det aktuelle udsnit, oprettet i datasættet JSON med variabler [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) og [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

I øjeblikket, kræver Data Factory, planen angivet i aktiviteten nøjagtigt stemmer overens med planen angivet i **tilgængelighed** af output datasættet. Derfor knyttet **WindowStart**, **WindowEnd**, **SliceStart**og **SliceEnd** altid til den samme tidsperiode og et enkelt output udsnit.

Du kan finde flere oplysninger om forskellige egenskaber, der er tilgængelige for sektionen tilgængelighed, [opretter datasæt](data-factory-create-datasets.md).

## <a name="move-data-from-sql-database-to-blob-storage"></a>Flytte data fra SQL-Database til Blob-lager

Lad os få nogle ting sammen og i aktion ved at oprette en rørledning, der kopierer data fra en datatabel Azure SQL-Database til Azure Blob-lager hver time.

**Input: Azure SQL-Database datasæt**

    {
        "name": "AzureSqlInput",
        "properties": {
            "published": false,
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }


**Frekvens** er indstillet til **time** og **interval** er angivet til **1** i sektionen mulighed.

**Output: Azure Blob storage datasæt**

    {
        "name": "AzureBlobOutput",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
                "format": {
                    "type": "TextFormat"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Frekvens** er indstillet til **time** og **interval** er angivet til **1** i sektionen mulighed.



**Aktivitet: Kopiér aktivitet**

    {
        "name": "SamplePipeline",
        "properties": {
            "description": "copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "name": "AzureSQLtoBlob",
                    "description": "copy activity",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 100000,
                            "writeBatchTimeout": "00:05:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureSQLInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            ],
            "start": "2015-01-01T08:00:00Z",
            "end": "2015-01-01T11:00:00Z"
        }
    }


Eksemplet viser aktivitet tidsplanen og datasæt tilgængelighed sektioner, der er angivet til en time frekvens. Eksemplet viser, hvordan du kan bruge **WindowStart** og **WindowEnd** til at vælge relevante data til en aktivitet køre og kopiere det til en blob med den relevante **mappesti**. **Mappesti** parameteriseret for at få en separat mappe til hver time.

Når tre udsnittene mellem 8 – 11 AM udfører, er dataene i Azure SQL-Database på følgende måde:

![Eksempel input](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Når du installerer rørledningen, udfyldes Azure blob på følgende måde:

-   Filen mypath/2015/1/1/8/Data. &lt;Guid&gt;.txt med data

            10002345,334,2,2015-01-01 08:24:00.3130000
            10002345,347,15,2015-01-01 08:24:00.6570000
            10991568,2,7,2015-01-01 08:56:34.5300000

    > [AZURE.NOTE] &lt;GUID&gt; erstattes med det faktiske guid. Eksempel filnavn: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
-   Filen mypath/2015/1/1/9/Data. &lt;Guid&gt;.txt med data:

            10002345,334,1,2015-01-01 09:13:00.3900000
            24379245,569,23,2015-01-01 09:25:00.3130000
            16777799,21,115,2015-01-01 09:47:34.3130000
-   Filen mypath/2015/1/1/10/Data. &lt;Guid&gt;.txt uden data.


## <a name="active-period-for-pipeline"></a>Aktive periode for pipeline

[Oprette rørledninger](data-factory-create-pipelines.md) introduceret begrebet en aktive periode for en rørledning, der er angivet ved at angive egenskaber for **start** og **Afslut** .

Du kan angive startdatoen for den aktive periode pipeline i fortiden. Data Factory beregnes (tilbage fyld) alle dataudsnit tidligere og begynder at behandle dem automatisk.

## <a name="parallel-processing-of-data-slices"></a>Parallel behandling af dataudsnit
Du kan konfigurere tilbage udfyldt dataudsnit til at blive kørt parallelt ved at angive egenskaben **på dokumentsammenfald** i sektionen politik aktivitetens JSON. Du kan finde flere oplysninger om denne egenskab, [opretter rørledninger](data-factory-create-pipelines.md).

## <a name="rerun-a-failed-data-slice"></a>Kør et mislykkedes data udsnit 
Du kan overvåge udførelse af udsnit på en omfattende og visuelle måde. Finde [overvågnings- og administrere rørledninger ved hjælp af Azure portalen blade](data-factory-monitor-manage-pipelines.md) eller [overvågnings-og administration af](data-factory-monitor-manage-app.md) oplysninger.

Overvej følgende eksempel, der viser to aktiviteter. Activity1 giver et klokkeslæt serie datasæt med udsnit som output, der bruges som input af Activity2 til at producere endelige output tid serie datasæt.

![Mislykkedes udsnit](./media/data-factory-scheduling-and-execution/failed-slice.png)

Diagrammet viser, at af tre seneste udsnit, der opstod en fejl, der producerer 9-10 AM udsnittet for Dataset2. Data Factory registrerer automatisk afhængighed for tid serie datasættet. Derfor kan starter det ikke aktiviteten køre til 9-10 AM efterfølgende udsnittet.

Factory overvågning og administration Dataværktøjer gør det muligt at dykke ned diagnosticeringslogfiler for mislykkedes udsnit til nemt at finde den egentlige årsag til problemet og løse problemet. Når du har løst problemet, kan du nemt begynde aktiviteten køre for at frembringe mislykkedes udsnittet. Se [overvågning og administration af rørledninger ved hjælp af Azure portalen blade](data-factory-monitor-manage-pipelines.md) eller [overvågning og administration app](data-factory-monitor-manage-app.md)for flere oplysninger om, hvordan du kan køre igen og forstå tilstandsovergange for dataudsnit.

Når du kører igen 9-10 AM udsnittet for **Dataset2**, starter Data Factory Kør til 9-10 AM afhængige udsnittet på den endelige datasæt.

![Kør mislykkedes udsnit](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>Køre aktiviteter i en sekvens
Du kan sammenkæde to aktiviteter (køre en aktivitet efter den anden) ved at angive output datasættet med en aktivitet som input datasættet af anden aktiviteten. Aktiviteterne, der kan være i den samme rørledning eller i forskellige rørledninger. Den anden aktivitet udfører kun når den første opgave afsluttes korrekt.

For eksempel kan du overveje følgende tilfælde:

1.  Pipeline P1 har aktivitet A1, der kræver eksterne input datasæt D1, og giver output datasæt D2.
2.  Pipeline P2 har aktivitet A2, der kræver input fra datasæt D2, og giver output datasæt D3.

I dette scenario er aktiviteter A1 og A2 i forskellige rørledninger. Aktiviteten A1 køres, når de eksterne data er tilgængelig og planlagte tilgængelighed hyppigheden er nået. Aktiviteten A2 køres, når de planlagte udsnit fra D2 bliver tilgængelige, og den planlagte tilgængelighed hyppighed nås. Hvis der er fejl i et af udsnittene i dataset D2, kører A2 ikke for udsnittet, indtil det bliver tilgængelig.

Visningen diagrammet vil se ud i det følgende diagram:

![Sammenkædning af aktiviteter i to rørledninger](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Som tidligere nævnt kan aktiviteterne, der være i den samme rørledning. Diagramvisning med begge aktiviteter i den samme rørledning vil se ud i det følgende diagram:

![Sammenkædning af aktiviteter i den samme rørledning](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>Kopiere sekventielt
Det er muligt at køre flere kopier operationer efter hinanden på en fortløbende/bestilt måde. Du kan for eksempel har to kopi aktiviteter i en pipeline (CopyActivity1 og CopyActivity2) med følgende inputdataene output datasæt:   

CopyActivity1

Input: datasæt. Output: Dataset2.

CopyActivity2

Input: Dataset2.  Output: Dataset3.

CopyActivity2 vil kun køre, hvis CopyActivity1 er udført og Dataset2 er tilgængelig.

Her er eksempel rørledningen JSON:

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob1ToBlob2",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset3"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob2ToBlob3",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2016-08-25T01:00:00Z",
            "end": "2016-08-25T01:00:00Z",
            "isPaused": false
        }
    }

Bemærk, at i eksemplet output datasættet af den første kopi aktivitet (Dataset2) er angivet som input til den anden aktivitet. Den anden aktivitet kører derfor kun, når output dataset fra den første aktivitet er klar.  

I eksemplet CopyActivity2 kan have en anden input, såsom Dataset3, men du angiver Dataset2 som input til CopyActivity2, så aktiviteten ikke kører, før CopyActivity1 er afsluttet. Eksempel:

CopyActivity1

Input: Dataset1. Output: Dataset2.

CopyActivity2

Indgange: Dataset3, Dataset2. Output: Dataset4.

    {
        "name": "ChainActivities",
        "properties": {
            "description": "Run activities in sequence",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "copyBehavior": "PreserveHierarchy",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset1"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlobToBlob",
                    "description": "Copy data from a blob to another"
                },
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Dataset3"
                        },
                        {
                            "name": "Dataset2"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "Dataset4"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "CopyFromBlob3ToBlob4",
                    "description": "Copy data from a blob to another"
                }
            ],
            "start": "2017-04-25T01:00:00Z",
            "end": "2017-04-25T01:00:00Z",
            "isPaused": false
        }
    }


Bemærk, at i eksemplet med to input datasæt er angivet for den anden kopi aktivitet. Når der er angivet flere input, kun den første indtastning datasæt bruges til at kopiere data, men andre datasæt bruges som afhængigheder. CopyActivity2 vil starte, når følgende betingelser er opfyldt:

- CopyActivity1 har fuldført og Dataset2 er tilgængelig. Dette dataset bruges ikke, når du kopierer data til Dataset4. Det fungerer kun som en planlægning afhængighed for CopyActivity2.   
- Dataset3 er tilgængelig. Dette dataset repræsenterer de data, der kopieres til destinationen.  



## <a name="model-datasets-with-different-frequencies"></a>Model datasæt med forskellige hyppighed

I eksemplerne var hyppigheden for input- og outputområder datasæt og vinduet aktivitet tidsplan på samme måde. Nogle situationer kræver muligheden for at producere output med en hyppighed, der er anderledes end hyppighed af en eller flere input. Data Factory understøtter modeling disse scenarier.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Eksempel 1: Oprette en rapport over dagligt output til indtastede data, som er tilgængelig, hver time

Overvej et scenarie, hvor du har indtastet mål data fra sensorer tilgængelige hver time i Azure Blob-lager. Du vil oprette en daglig Sammenlæg rapport med statistik som gennemsnit, maksimum og minimum for den pågældende dag med [Data Factory hive aktivitet](data-factory-hive-activity.md).

Her er, hvordan du kan modellere dette scenarie med Data Factory:

**Input-datasæt**

Hver time input filerne udelades i mappen for den angivne dag. Tilgængelighed om input er angivet til **time** (frekvens: time, interval: 1).

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Output datasæt**

Én outputfilen oprettes hver dag i den dag mappe. Tilgængeligheden af output er angivet til **dag** (frekvens: dag og interval: 1).


    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Aktivitet: hive aktivitet i en pipeline**

Scriptet hive modtager *DateTime* oplysninger som parametre, der bruger variablen **WindowStart** , som vist i følgende kodestykket. Hive scriptet bruger denne variabel til at indlæse dataene fra den rigtige mappe for den pågældende dag og køre sammenlægning for at generere output.

        {  
            "name":"SamplePipeline",
            "properties":{  
            "start":"2015-01-01T08:00:00",
            "end":"2015-01-01T11:00:00",
            "description":"hive activity",
            "activities": [
                {
                    "name": "SampleHiveActivity",
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adftutorial\\hivequery.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                            "Month": "$$Text.Format('{0:%M}',WindowStart)",
                            "Day": "$$Text.Format('{0:%d}',WindowStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },          
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 2,
                        "timeout": "01:00:00"
                    }
                 }
             ]
           }
        }

I følgende diagram vises dette scenario fra en data-afhængighed synspunkt.

![Afhængighed af data](./media/data-factory-scheduling-and-execution/data-dependency.png)

Output udsnit for hver dag, afhænger af 24 timer udsnit ud fra et input datasæt. Data Factory beregner disse afhængigheder automatisk ved forståelse af inputdataene udsnit, der falder i den samme tidsperiode som output udsnittet skal være produceret. Hvis et af de 24 input udsnit ikke er tilgængelig, venter Data Factory input udsnittet til at være klar, før du starter den daglige aktiviteter, der kører.


### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Eksempel 2: Angive afhængighed med udtryk og Data Factory-funktioner

Lad os se på et andet scenarie. Antag, at du har en hive aktivitet, der behandler to input datasæt. De indeholder nye data dagligt, men de bliver nye data hver uge. Antag, at du ville gøre en joinforbindelse på tværs af de to input og frembringe output hver dag.

Den enkle fremgangsmåde i hvilken Data Factory automatisk figurer ud højre at angive udsnit til at behandle ved at justere til output data udsnit tid periode ikke virker.

Du skal angive, at for hver enkelt aktivitet, køre, Data Factory skal bruge sidste uge data udsnit for ugentlig input datasættet. Du bruger Azure Data Factory funktioner som vist i følgende kodestykket for at implementere denne funktionsmåde.

**Input1: Azure blob**

Den første input er Azure blob opdateres dagligt.

    {
      "name": "AzureBlobInputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Input2: Azure blob**

Input2 er Azure blob opdateres ugentligt.

    {
      "name": "AzureBlobInputWeekly",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 7
        }
      }
    }

**Output: Azure blob**

Én outputfilen oprettes hver dag i mappen til dag. Tilgængeligheden af output er indstillet til **dag** (frekvens: dag, interval: 1).

    {
      "name": "AzureBlobOutputDaily",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
          "partitionedBy": [
            { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
            { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
            { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
          ],
          "format": {
            "type": "TextFormat"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Aktivitet: hive aktivitet i en pipeline**

Hive aktiviteten tager to input og giver et output udsnit hver dag. Du kan angive hver dag output udsnit for at er afhængige af den forrige uge input udsnit for ugentlig input på følgende måde.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-01-01T08:00:00",
        "end":"2015-01-01T11:00:00",
        "description":"hive activity",
        "activities": [
          {
            "name": "SampleHiveActivity",
            "inputs": [
              {
                "name": "AzureBlobInputDaily"
              },
              {
                "name": "AzureBlobInputWeekly",
                "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
                "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutputDaily"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
              "scriptPath": "adftutorial\\hivequery.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                "Month": "$$Text.Format('{0:%M}',WindowStart)",
                "Day": "$$Text.Format('{0:%d}',WindowStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            },          
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 2,  
              "timeout": "01:00:00"
            }
           }
         ]
       }
    }


## <a name="data-factory-functions-and-system-variables"></a>Data Factory funktioner og systemvariabler   

Se [Data Factory-funktioner og systemvariabler](data-factory-functions-variables.md) til en liste over funktioner og systemvariabler, som Data Factory understøtter.

## <a name="data-dependency-deep-dive"></a>Data afhængighed undersøgelse

Hvis du vil oprette et datasæt udsnit ved at køre en aktivitet, anvender Data Factory følgende *afhængighed model* til at bestemme relationerne mellem datasæt consumed og forårsaget af en aktivitet.

Tidsintervallet for de input datasæt, der kræves for at generere output datasæt udsnittet kaldes *afhængighed periode*.

Kør en aktivitet genererer et datasæt udsnit ud, når data udsnittene i input datasæt i perioden afhængighed er tilgængelige. Med andre ord køre alle input udsnittene omfatter afhængighed perioden skal være i **klar** tilstand for aktiviteten for at frembringe et output datasæt udsnit.

For at generere datasæt udsnittet [**start**, **Slut**], skal en funktion tilknyttes datasæt udsnittet dens afhængighed periode. Denne funktion er grundlæggende en formel, der konverterer starten og slutningen af datasæt udsnittet til starten og slutningen af perioden afhængighed. Mere formelt:

    DatasetSlice = [start, end]
    DependecyPeriod = [f(start, end), g(start, end)]

**F** og **g** knytter funktioner, der beregner starten og slutningen af perioden afhængighed for hver aktivitet, der er indtastet.

Som det fremgår eksempler, er den samme som perioden for dataudsnittet, der er produceret afhængighed perioden. I disse tilfælde beregner Data Factory automatisk input udsnittene, der falder i perioden afhængighed.  

For eksempel i den sammenlægning eksempel, hvor resultatet genererede dagligt og indtastede data er tilgængelig, hver time, er data udsnit perioden 24 timer. Data Factory finder den relevante hver time input udsnit for denne periode, og gør output udsnittet afhænger af det input udsnit.

Du kan også angive din egen tilknytning for perioden afhængighed, som vist i stikprøven, hvor en af input er hver uge, og output udsnittet produceret dagligt.

## <a name="data-dependency-and-validation"></a>Afhængighed af data og validering

Et datasæt kan have en validering-politik defineret, der angiver, hvordan de data, der genereres af en udførelse af udsnit kan valideres, før den er klar til forbrug. Du kan finde oplysninger i [oprette datasæt](data-factory-create-datasets.md) .

Når udsnittet er fuldført, er output udsnit status i så fald ændres til **venter** med en understatus **Validering**. Når udsnit er godkendt, ændres udsnit status til **klar**.

Hvis et udsnit af data er blevet produceret, men ikke består validering, behandles ikke aktivitet kører for efterfølgende udsnit, der afhænger af denne udsnit.

[Skærm og administrere rørledninger](data-factory-monitor-manage-pipelines.md) omhandler de forskellige tilstande på dataudsnit i Data Factory.

## <a name="external-data"></a>Eksterne data

Et datasæt kan markeres som ekstern (som vist i følgende JSON kodestykket), hvilket indebærer det ikke er oprettet med Data Factory. I så fald politikken datasæt har et ekstra sæt af parametre, der beskriver validering og prøv igen politik for datasættet. Du kan finde en beskrivelse af alle egenskaberne i [opretter rørledninger](data-factory-create-pipelines.md) .

Svarer til datasæt, der er oprettet med Data Factory dataudsnit til eksterne data skal være klar før afhængige udsnit kan behandles.

    {
        "name": "AzureSqlInput",
        "properties":
        {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties":
            {
                "tableName": "MyTable"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1     
            },
            "external": true,
            "policy":
            {
                "externalData":
                {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }  
        }
    }


## <a name="onetime-pipeline"></a>Enkelt rørledning
Du kan oprette og planlægge en pipeline til at køre med jævne mellemrum (for eksempel: time- eller dagligt) i de start- og sluttidspunkter, som du angiver i pipeline definition. Du kan finde oplysninger i [Planlægning aktiviteter](#scheduling-and-execution) . Du kan også oprette en rørledning, der kører kun én gang. For at gøre det, du angiver egenskaben **pipelineMode** i definitionen af pipeline til **enkelt** som vist i følgende eksempel på JSON. Standardværdien for denne egenskab er **planlagt**.

    {
        "name": "CopyPipeline",
        "properties": {
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource",
                            "recursive": false
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ]
                    "name": "CopyActivity-0"
                }
            ]
            "pipelineMode": "OneTime"
        }
    }

Bemærk følgende:

- ** **Start** - og** sluttidspunkter for pipeline er ikke angivet.
- **Tilgængelighed** af input- og outputområder datasæt er angivet (**frekvens** og **interval**), selvom Data Factory ikke bruger værdierne.  
- Enkeltstående rørledninger vises ikke i diagramvisning. Det skyldes design.
- Enkeltstående rørledninger kan ikke opdateres. Du kan klone en enkelt rørledning, omdøbe den, opdatere egenskaber og installere den for at oprette en anden.
