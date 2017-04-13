<properties 
    pageTitle="Flyt data til/fra Azure SQL-Database | Microsoft Azure" 
    description="Lær at flytte data til/fra Azure SQL-Database ved hjælp af Azure Data Factory." 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Flytte data til og fra Azure SQL-Database ved hjælp af Azure Data Factory
I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data til/fra Azure SQL-Database fra/til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer. 

## <a name="supported-sources-and-sinks"></a>Understøttede datakilder og dræn
Se [understøttede data butikker](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabel til en liste over data butikker understøttes som kilder eller dræn af kopi aktiviteten. Du kan flytte data fra en hvilken som helst understøttet datakilde datalager til Azure SQL-Database eller fra Azure SQL-Database til en hvilken som helst understøttede sink datalager.

## <a name="create-pipeline"></a>Oprette pipeline
Du kan oprette en rørledning med en kopi aktiviteter, der flytter data til/fra Azure SQL-database ved hjælp af forskellige værktøjer/API'er.  

- Kopiere guiden
- Azure-portalen
- Visual Studio
- Azure PowerShell
- .NET API
- REST-API

Du kan finde trinvise instruktioner til oprettelse af en rørledning med en kopi aktivitet på forskellige måder i [kopi aktivitet selvstudium](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .   

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der kopierer data til/fra Azure SQL-Database er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempler giver eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data til og fra Azure SQL-Database og Azure Blob-lager. Data kan dog kopierede **direkte** fra enhver af kilder til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.

## <a name="sample-copy-data-from-azure-sql-database-to-azure-blob"></a>Eksempel: Kopiere data fra Azure SQL-Database til Azure Blob

Den samme definerer de følgende Data Factory-enheder:

1. En sammenkædet tjeneste af typen [AzureSqlDatabase](#azure-sql-linked-service-properties).
2. En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. En input [datasæt](data-factory-create-datasets.md) af typen [AzureSqlTable](#azure-sql-dataset-type-properties). 
4. En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [SqlSource](#azure-sql-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer tidsserie data (dagligt, hver time, osv.) fra en tabel i Azure SQL-database til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner.  

**Azure SQL sammenkædet service**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

I afsnittet [Azure SQL sammenkædede Service](#azure-sql-linked-service-properties) for listen over egenskaber, der understøttes af denne sammenkædede tjeneste. 

**Azure Blob storage sammenkædet service**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Artiklen [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) for listen over egenskaber, der understøttes af denne sammenkædede tjeneste. 

**Azure SQL input datasæt**

Eksemplet antages det, du har oprettet en tabel "MinTabel" i SQL Azure og den indeholder en kolonne med navnet "timestampcolumn" for klokkeslætsdata serie. 

Angive "eksterne": "true" informerer tjenesten Azure Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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

I afsnittet [Azure SQL-datasæt typeegenskaber](#azure-sql-dataset-type-properties) for listen af egenskaber, der understøttes af denne type datasæt.  

**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

I afsnittet [Azure Blob datasæt typeegenskaber](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) for listen af egenskaber, der understøttes af denne type datasæt.  

**Rørledning med kopi aktiviteter**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **SqlSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **SqlReaderQuery** markerer dataene i den seneste time kopiere.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
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
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

I eksemplet er **sqlReaderQuery** angivet for SqlSource. Kopiér aktiviteten kører denne forespørgsel mod kilden Azure SQL-Database til at få vist data. Du kan også angive en lagret procedure ved at angive **sqlReaderStoredProcedureName** og **storedProcedureParameters** (hvis den lagrede procedure tager parametre).

Hvis du ikke angiver sqlReaderQuery eller sqlReaderStoredProcedureName, bruges de kolonner, der er defineret i sektionen struktur af datasættet JSON til at oprette en forespørgsel til at køre mod Azure SQL-Database. For eksempel: `select column1, column2 from mytable`. Hvis definitionen datasæt ikke har strukturen, markeres alle kolonner fra tabellen. 


Se afsnittet [Sql kilde](#sqlsource) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) til listen over egenskaber, der understøttes af SqlSource og BlobSink. 


## <a name="sample-copy-data-from-azure-blob-to-azure-sql-database"></a>Eksempel: Kopiere data fra Azure Blob til Azure SQL-Database

Eksemplet definerer de følgende Data Factory-enheder:  

1.  En sammenkædet tjeneste af typen [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) og [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

Eksempel kopierne tidsserie data (dagligt, hver time, osv.) fra Azure blob til en tabel i Azure SQL database hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 


**Azure SQL sammenkædet service**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

I afsnittet [Azure SQL sammenkædede Service](#azure-sql-linked-service-properties) for listen over egenskaber, der understøttes af denne sammenkædede tjeneste. 

**Azure Blob storage sammenkædet service**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Artiklen [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) for listen over egenskaber, der understøttes af denne sammenkædede tjeneste.

**Azure Blob input datasæt**

Data er valgte fra en ny blob hver time (frekvens: time, interval: 1). Mappe stien og navnet på blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned og dag en del af starttidspunktet og filnavn bruger time en del af starttidspunktet. "eksterne": "true" indstilling informerer tjenesten Data Factory, denne tabel er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

I afsnittet [Azure Blob datasæt typeegenskaber](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) for listen af egenskaber, der understøttes af denne type datasæt.

**Azure SQL output datasæt**

Eksemplet kopierer data til en tabel med navnet "MinTabel" i SQL Azure. Oprette tabellen i SQL Azure med det samme antal kolonner, som du forventer at Blob csv-filen indeholder. Nye rækker føjes til tabellen hver time. 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

I afsnittet [Azure SQL-datasæt typeegenskaber](#azure-sql-dataset-type-properties) for listen af egenskaber, der understøttes af denne type datasæt.

**Rørledning med kopi aktiviteter**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **BlobSource** i pipeline JSON definition, og **sink** type er indstillet til **SqlSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

Se [Sql Sink](#sqlsink) sektion og [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) til listen over egenskaber, der understøttes af SqlSink og BlobSource. 


## <a name="azure-sql-linked-service-properties"></a>Azure SQL sammenkædet service egenskaber
I eksemplerne, har du brugt en sammenkædet tjeneste af typen **AzureSqlDatabase** linke en Azure SQL-database til en data fabrik. Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Azure SQL sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| type | Typeegenskaben skal være angivet til: **AzureSqlDatabase** | Ja |
| connectionString | Angive oplysninger, der er behov for at oprette forbindelse til Azure SQL-Database forekomst for egenskaben connectionString. | Ja |

> [AZURE.NOTE] Konfigurere [Azure SQL Database-firewallen](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) databaseserveren mulighed for at [tillade Azure Services til at få adgang til serveren](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Desuden, hvis du kopierer data til Azure SQL-Database fra eksterne Azure herunder fra lokale datakilder med data factory gateway, konfigurere relevante IP-adresseområder for den computer, der bruges til at sende data til Azure SQL-Database. 

## <a name="azure-sql-dataset-type-properties"></a>Egenskaber for Azure SQL-datasæt
I eksemplerne, har du brugt et datasæt af typen **AzureSqlTable** til at repræsentere en tabel i en Azure SQL-database. 

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.). 

Afsnittet typeProperties er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen **typeProperties** for datasættet af typen **AzureSqlTable** har følgende egenskaber:

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| Tabelnavn | Navnet på tabellen i forekomsten Azure SQL-Database, som sammenkædede service henviser til. | Ja |

## <a name="azure-sql-copy-activity-type-properties"></a>Azure SQL kopi aktivitet typeegenskaber
En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter.

> [AZURE.NOTE] Kopiér aktiviteten tager kun én input og producerer kun én output.

Tilgængelige egenskaber i sektionen **typeProperties** aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet. 

Hvis du vil flytte data fra en Azure SQL-database, kan du angive kildetypen i kopi aktiviteten til **SqlSource**. På samme måde, hvis du vil flytte data til en Azure SQL-database, du angiver typen sink i kopi aktiviteten til **SqlSink**. Dette afsnit indeholder en liste over egenskaber, der understøttes af SqlSource og SqlSink. 

### <a name="sqlsource"></a>SqlSource

Kopiér aktivitet, når kilden er af typen **SqlSource**findes følgende egenskaber i **typeProperties** afsnit:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. Eksempel: `select * from MyTable`.  | Nej |
| sqlReaderStoredProcedureName | Navnet på den lagrede procedure, der læser data fra kildetabellen. | Navnet på den lagrede procedure. | Nej |
| storedProcedureParameters | Parametre for den lagrede procedure. | Navn/værdi-par. Navne og hus af parametre skal svare til navne og hus af de lagrede procedureparametre. | Nej |

Hvis **sqlReaderQuery** er angivet for SqlSource, kører kopi aktiviteten denne forespørgsel mod kilden Azure SQL-Database til at få vist data. Du kan også angive en lagret procedure ved at angive **sqlReaderStoredProcedureName** og **storedProcedureParameters** (hvis den lagrede procedure tager parametre). 

Hvis du ikke angiver sqlReaderQuery eller sqlReaderStoredProcedureName, de kolonner, der er defineret i sektionen struktur af datasættet JSON bruges til at oprette en forespørgsel (`select column1, column2 from mytable`) til at køre mod Azure SQL-Database. Hvis definitionen datasæt ikke har strukturen, markeres alle kolonner fra tabellen. 

> [AZURE.NOTE] Når du bruger **sqlReaderStoredProcedureName**, nødt du stadig til at angive en værdi for egenskaben **tabelnavn** i datasættet JSON. Der findes ingen valideringer, der udføres via mod denne tabel. 

### <a name="sqlsource-example"></a>Eksempel på SqlSource

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Den lagrede procedure definition:** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### <a name="sqlsink"></a>SqlSink 

**SqlSink** understøtter følgende egenskaber:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Ventetid for handlingen batchen Indsæt at fuldføre, før det udløber. | TimeSpan<br/><br/> Eksempel: "00: 30:00" (30 minutter). | Nej | 
| writeBatchSize | Indsætter data i en SQL-tabel, når størrelsen på bufferen writeBatchSize. | Heltal (antallet af rækker)| Ikke (standard: 10000)
| sqlWriterCleanupScript | Angive en forespørgsel for kopi aktiviteten udføres så data fra et bestemt udsnit ryddes. Få mere at vide afsnittet [repeterbarhed](#repeatability-during-copy). | En forespørgselssætning.  | Nej |
| sliceIdentifierColumnName | Angiv et kolonnenavn for kopi aktivitet udfylde med automatisk genereret udsnit id, som bruges til at rydde op i data i et bestemt udsnit, når Kør igen. Få mere at vide afsnittet [repeterbarhed](#repeatability-during-copy). | Kolonnenavn for en kolonne med datatypen for binary(32). | Nej |
| sqlWriterStoredProcedureName | Navnet på den lagrede procedure upserts (opdateringer/indsætter) dataene i måltabellen. | Navnet på den lagrede procedure. | Nej |
| storedProcedureParameters | Parametre for den lagrede procedure. | Navn/værdi-par. Navne og hus af parametre skal svare til navne og hus af de lagrede procedureparametre. | Nej | 
| sqlWriterTableType | Angive et tabelnavn type skal bruges i den lagrede procedure. Kopiér aktivitet gør de data, der flyttes tilgængelige i en midlertidig tabel med denne tabel. Lagret procedurekode kan derefter flette de data, der kopieres med eksisterende data. | Navnet på en tabel type. | Nej |

#### <a name="sqlsink-example"></a>Eksempel på SqlSink

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }

## <a name="identity-columns-in-the-target-database"></a>Id-kolonner i destinationsdatabasen
Dette afsnit indeholder et eksempel for at kopiere data fra en kildetabel uden en id-kolonne til en destinationstabel med en id-kolonne. 

**Kildetabellen:** 

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**Destinationstabellen:**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


Bemærk, at måltabellen har en id-kolonne. 

**Kilde datasæt JSON definition**

    {
        "name": "SampleSource",
        "properties": {
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**Destination datasæt JSON definition**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }   
    }


Bemærk, som kilde- og destinationswebsteder tabellen har forskellige skema (mål har en ekstra kolonne med identitet). I dette scenarie skal du angive egenskaben **struktur** i target datasæt definition, som ikke indeholder id-kolonnen. 

Derefter skal knytte du kolonner fra kilden datasæt til kolonner i destinationsdatasættet. Se [eksempler på kolonnetilknytning](#column-mapping-samples) afsnittet for et eksempel. 

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server--azure-sql-database"></a>Skriv tilknytning til SQL Server & Azure SQL-Database

Som nævnt i udfører [data bevægelse aktiviteter](data-factory-data-movement-activities.md) artikel kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når flytning af data til og fra SQL Azure, SQL server, Sybase følgende tilknytninger anvendes fra SQL type til .NET type og omvendt.

Tilknytningen er den samme som den SQL Server datatilknytning til ADO.NET.

| SQL Server-Database Engine type | .NET framework type |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binært tal | Byte] |
| bit | Boolesk værdi |
| tegn | Streng, Char] |
| dato | Dato og klokkeslæt |
| Dato og klokkeslæt | Dato og klokkeslæt |
| datetime2 | Dato og klokkeslæt |
| DateTimeOffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM attribut (varbinary(max)) | Byte] |
| Flydende | Dobbelt |
| Billede af | Byte] | 
| heltal | Int32 | 
| penge | Decimal |
| nchar | Streng, Char] |
| ntext | Streng, Char] |
| numeriske | Decimal |
| nvarchar | Streng, Char] |
| reelt tal | Enkelt |
| med navnet ROWVERSION | Byte] |
| smalldatetime | Dato og klokkeslæt |
| smallint | Int16 |
| Access | Decimal | 
| sql_variant | Objekt * |
| tekst | Streng, Char] |
| tid | TimeSpan |
| tidsstempel | Byte] |
| tinyint | Byte |
| Entydigt id | GUID |
| varbinary |  Byte] |
| varchar | Streng, Char] |
| XML | XML |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.