<properties 
    pageTitle="Flyt data til/fra Azure-tabel | Microsoft Azure" 
    description="Lær at flytte data til/fra Azure Table Storage ved hjælp af Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Flytte data til og fra Azure-tabel ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data til/fra Azure-tabel fra/til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data og understøttede data store kombinationer med kopi aktivitet.

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der kopierer data til/fra Azure-Tabellager er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempler giver eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data til og fra Azure Table Storage og Azure Blob-Database. Data kan dog kopierede **direkte** fra enhver af kilder til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Eksempel: Kopiere data fra Azure-tabel til Azure Blob

I følgende eksempel viser:

1.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (bruges til både tabel & blob).
2.  En input [datasæt](data-factory-create-datasets.md) af typen [Azure](#azure-table-dataset-type-properties).
3.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  [Rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [AzureTableSource](#azure-table-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

Eksemplet kopierer data, der hører til standardpartition i en Azure-tabel til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner.

**Azure-lager sammenkædet service:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory understøtter to typer Azure-lager, der er sammenkædet tjenester: **AzureStorage** og **AzureStorageSas**. Til den første opgave, du angiver forbindelsesstrengen, der indeholder kontonøgle og efter den senere, du angiver den delte Access signatur (SAS) Uri. Se [Sammenkædede Services](#linked-services) afsnittet Yderligere oplysninger.  

**Azure tabel input datasæt:**

Eksemplet antages, du har oprettet en tabel "MinTabel" i Azure-tabel.
 
Angive "eksterne": "true" informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
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

**Azure Blob output datasæt:**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Rørledning med kopi aktiviteten:**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **AzureTableSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgslen angivet med **AzureTableSourceQuery** egenskaben markerer dataene fra standardpartitionen hver time kopiere.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Eksempel: Kopiere data fra Azure Blob til Azure-tabel

I følgende eksempel viser:

1.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (bruges til både tabel & blob)
3.  En input [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [Azure](#azure-table-dataset-type-properties). 
4.  [Rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) og [AzureTableSink](#azure-table-copy-activity-type-properties). 


Eksempel kopierne tidsserie data fra en Azure blob til en Azure tabellen hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner.

**Azure-lager (for både Azure-tabel og Blob) sammenkædet service:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure Data Factory understøtter to typer Azure-lager, der er sammenkædet tjenester: **AzureStorage** og **AzureStorageSas**. Til den første opgave, du angiver forbindelsesstrengen, der indeholder kontonøgle og efter den senere, du angiver den delte Access signatur (SAS) Uri. Se [Sammenkædede Services](#linked-services) afsnittet Yderligere oplysninger. 

**Azure Blob input datasæt:**

Data er valgte fra en ny blob hver time (frekvens: time, interval: 1). Mappe stien og navnet på blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned og dag en del af starttidspunktet og filnavn bruger time en del af starttidspunktet. "eksterne": "true" indstilling informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
                "format": "MM"
              }
            },
            {
              "name": "Day",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "dd"
              }
            },
            {
              "name": "Hour",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HH"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
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

**Azure Table output datasæt:**

Eksemplet kopierer data til en tabel med navnet "MinTabel" i Azure-tabel. Oprette en Azure-tabel med det samme antal kolonner, som du forventer at Blob csv-filen indeholder. Nye rækker føjes til tabellen hver time. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Rørledning med kopi aktiviteten:**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **BlobSource** i pipeline JSON definition, og **sink** type er indstillet til **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>Sammenkædede tjenester
Der findes to typer af sammenkædede tjenester, du kan bruge til at sammenkæde en Azure data factory en Azure blob-lager. De er: **AzureStorage** sammenkædet og tjenesten **AzureStorageSas** sammenkædet. Tjenesten Azure-lager, der er sammenkædet indeholder data factory med global adgang til Azure-lager. Mens feltet Azure lagerplads SAS (delt Access underskrift) sammenkædet indeholder service data factory med begrænsede/klokkeslæt-bundne adgang til Azure-lager. Der er ingen andre forskelle mellem disse to sammenkædede tjenester. Vælg den sammenkædede tjeneste, der passer til dine behov. De følgende afsnit indeholder flere detaljer om disse to sammenkædede services.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Azure egenskaber for tabel datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet typeProperties er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen **typeProperties** for datasættet af typen **Azure** har følgende egenskaber.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| Tabelnavn | Navnet på tabellen i den Azure tabel Database-forekomst, som sammenkædede service henviser til. | Ja. Når der er angivet et tabelnavn uden en azureTableSourceQuery, kopieres alle poster fra tabellen til destinationen. Hvis en azureTableSourceQuery angives også, kopieres poster fra den tabel, der opfylder forespørgslen til destinationen. |

### <a name="schema-by-data-factory"></a>Skema af Data Factory
For skema ledig data butikker som Azure-tabel indstiller tjenesten Data Factory skemaet i en af følgende måder:

1.  Hvis du angiver strukturen af data ved hjælp af egenskaben **strukturen** i definitionen af datasæt, respekterer tjenesten Data Factory denne struktur som i skemaet. Hvis en række ikke indeholder en værdi for en kolonne, angivet i dette tilfælde en null-værdi for den.
2. Hvis du ikke angiver strukturen af data ved hjælp af egenskaben **strukturen** i definitionen af datasæt, indstiller Data Factory skemaet ved hjælp af den første række i dataene. I dette tilfælde, hvis den første række ikke indeholder det hele skema, mistes nogle kolonner i resultatet af kopieringen.

Derfor for skema ledig datakilder er den bedste fremgangsmåde at angive strukturen af data ved hjælp af egenskaben **struktur** .

## <a name="azure-table-copy-activity-type-properties"></a>Azure egenskaber for tabel kopi aktivitet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output datasæt og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

**AzureTableSource** understøtter følgende egenskaber i typeProperties afsnit:

Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Brug den brugerdefinerede forespørgsel til at læse data. | Azure tabel forespørgselsstreng. Se eksemplerne i næste afsnit. | Nej. Når der er angivet et tabelnavn uden en azureTableSourceQuery, kopieres alle poster fra tabellen til destinationen. Hvis en azureTableSourceQuery angives også, kopieres poster fra den tabel, der opfylder forespørgslen til destinationen.
azureTableSourceIgnoreTableNotFound | Angiv, om swallow bortset fra tabellen ikke findes. | SAND<br/>FALSK | Nej |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery eksempler

Hvis der er Azure tabelkolonne af strengtypen: 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Hvis der er Azure-tabel kolonne af typen Dato og klokkeslæt: 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** understøtter følgende egenskaber i typeProperties afsnit:


Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Standard partition nøgleværdi, der kan anvendes af sink. | En strengværdi. | Nej 
azureTablePartitionKeyName | Angiv navnet på den kolonne, hvis værdier bruges som partitionsnøgler. Hvis ikke angives, bruges AzureTableDefaultPartitionKeyValue som tasten partition. | Et kolonnenavn. | Nej |
azureTableRowKeyName | Angiv navnet på den kolonne, hvis kolonneværdier bruges som række nøgle. Hvis ikke er angivet, kan du bruge en GUID for hver række. | Et kolonnenavn. | Nej  
azureTableInsertType | Tilstand for at indsætte data i Azure table.<br/><br/>Denne egenskab kontrollerer, om eksisterende rækker i outputtabellen med identiske partition og række taster har deres værdier erstattet eller flettede. <br/><br/>Se emner i [Indsæt eller Flet enhed](https://msdn.microsoft.com/library/azure/hh452241.aspx) , og [Indsæt eller erstatte enhed](https://msdn.microsoft.com/library/azure/hh452242.aspx) for at få mere for at vide om, hvordan disse indstillinger (Flet og Erstat) fungerer. <br/><br> Denne indstilling gælder på niveauet række ikke tabel niveauet, og hverken indstilling sletter rækker i outputtabellen, der ikke findes i input. | flette (standard)<br/>Erstat | Nej 
writeBatchSize | Indsætter data i tabellen Azure, når den writeBatchSize eller writeBatchTimeout rammer. | Heltal (antallet af rækker)| Ikke (standard: 10000) 
writeBatchTimeout | Indsætter data i tabellen Azure, når writeBatchSize eller writeBatchTimeout ramme | TimeSpan<br/><br/>Eksempel: "00:20:00" (20 minutter) | Ikke (standard for at lagerplads klient Standardtimeout værdien 90 sek.)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Knytte en kildekolonne til en destination kolonne ved hjælp af oversætter JSON egenskab, før du kan bruge destinationskolonnen som azureTablePartitionKeyName.

I eksemplet nedenfor kildekolonne DivisionID er knyttet til destinationskolonnen: DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

DivisionID er angivet som tasten partition. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Tilknytning til Azure Table

Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende to-trins tilgang.

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data til og fra Azure-tabel, bruges på følgende [tilknytninger defineret af Azure-tabel service](https://msdn.microsoft.com/library/azure/dd179338.aspx) fra Azure tabel OData typer til .NET type og omvendt. 

| OData-datatype | .NET type | Detaljer |
| --------------- | --------- | ------- |
| Edm.Binary | byte] | En matrix af byte op til 64 KB. |
| Edm.Boolean | Boolesk | En boolesk værdi. |
| Edm.DateTime | Dato og klokkeslæt | En 64-bit-værdi, udtrykt som UTC (Coordinated Universal Time). Understøttede DateTime området begynder fra 12:00 midnat, januar 1, 1601 e.kr. (C.E.), UTC. Området, der slutter på 31 December 9999. |
| Edm.Double | dobbelt | En 64-bit flydende tal-værdi. |
| Edm.Guid | GUID | Et 128-bit globalt entydigt id. |
| Edm.Int32 | Int32 eller heltal | En 32-bit heltal. |
| Edm.Int64 | Int64 eller lang | Et 64-bit heltal. |
| Edm.String | Streng | En UTF-16-kodet værdi. Strengværdier kan være op til 64 KB. |

### <a name="type-conversion-sample"></a>Type konvertering eksempel

I følgende eksempel er kopierer data fra en Azure Blob til Azure-tabel med typekonverteringer. 

Antag Blob datasættet er i CSV-format og indeholder tre kolonner. En af dem er en datetime-kolonne med en brugerdefineret datetime-format, ved hjælp af forkortet franske navne for dag i ugen. 

Definere Blob kilde datasættet således sammen med definitioner af afhængighedstyper for kolonnerne.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
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

Givet type tilknytning fra Azure tabel OData type til .NET type, vil du definerer tabellen i Azure-tabel med følgende skemaet. 

**Azure tabel-skema:**

Kolonnenavn | Type
----------- | --------
bruger-id | Edm.Int64
Navn | Edm.String 
lastlogindate | Edm.DateTime

Derefter skal du angive datasættet Azure-tabel på følgende måde. Du behøver ikke at angive "struktur" afsnit med typeoplysninger, da typeoplysninger er allerede angivet i den underliggende datalager.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

I dette tilfælde Data Factory automatisk datatypekonvertering herunder feltet Dato/klokkeslæt med den brugerdefinerede datetime-format, ved hjælp af kultur "fransk-fransk", når du flytter data fra Blob til Azure-tabel.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
For at få mere for at vide om vigtige faktorer, virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den, skal du se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md).







