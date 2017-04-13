<properties 
    pageTitle="Flyt data til/fra DocumentDB | Microsoft Azure" 
    description="Få mere at vide hvordan flytte data til/fra Azure DocumentDB af websteder ved hjælp af Azure Data Factory" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Flytte data til og fra DocumentDB ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data til Azure DocumentDB fra en anden data gemmes og flytte data fra DocumentDB til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

I følgende eksempler viser, hvordan du kopierer data til og fra Azure DocumentDB og Azure Blob-lager. Data kan dog kopierede **direkte** fra enhver af kilder til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  

> [AZURE.NOTE] Kopierer data fra on-lokale/Azure IaaS data butikker til Azure DocumentDB og understøttes omvendt, med Data Management Gateway version 2.1 og over.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Eksempel: Kopiere data fra DocumentDB til Azure Blob

Eksemplet nedenfor viser:

1. En sammenkædet tjeneste af typen [DocumentDb](#azure-documentdb-linked-service-properties).
2. En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. En input [datasæt](data-factory-create-datasets.md) af typen [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data i Azure DocumentDB til Azure Blob. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner.

**Azure DocumentDB sammenkædet service:**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob-lager sammenkædet service:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure dokument DB input datasæt:**

Stikprøvernes forudsætter, at du har en samling med navnet **Person** i en Azure DocumentDB database.
 
Angive "eksterne": "sand" og angive externalData politikoplysninger Azure Data Factory-tjenesten, tabellen er ekstern i forhold til data fabrik og ikke oprettet med en aktivitet i data factory.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Azure Blob output datasæt:**

Data er kopieret til en ny blob hver time med stien til den afspejler den bestemte datetime med time granularitet blob.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

JSON eksempeldokument i samlingen Person i en DocumentDB database: 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB understøtter forespørgsel dokumenter ved hjælp af en SQL som syntaksen over hierarkiske JSON-dokumenter. 

Eksempel: SELECT Person.PersonId, Person.Name.First som Fornavn, Person.Name.Middle som MiddleName, Person.Name.Last AS efternavn fra Person

Den følgende rørledning kopierer data fra samlingen Person i DocumentDB database til en Azure blob. Som en del af kopi aktiviteten input og output har datasæt angivet.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Eksempel: Kopiere data fra Azure Blob til Azure DocumentDB

Eksemplet nedenfor viser:

1. En sammenkædet tjeneste af typen [DocumentDb](#azure-documentdb-linked-service-properties).
2. En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. En input [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. En output [datasæt](data-factory-create-datasets.md) af typen [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) og [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


Eksemplet kopierer data fra Azure blob til Azure DocumentDB. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner.

**Azure Blob-lager sammenkædet service:**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure DocumentDB sammenkædet service:**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Azure Blob input datasæt:**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Azure DocumentDB output datasæt:**

Eksemplet kopierer data til en af websteder med navnet "Person".

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Den følgende rørledning kopierer data fra Azure Blob til samlingen Person i DocumentDB. Som en del af kopi aktiviteten input og output har datasæt angivet. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Hvis eksempel blob input er som 

    1,John,,Doe

Derefter bliver output JSON i DocumentDB som:

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB er en NoSQL store for JSON dokumenter, hvor indlejrede strukturer er tilladt. Azure Data Factory gør det muligt for brugeren til angivelse af hierarki via **nestingSeparator**, som er "." i dette eksempel. Med separatoren, Kopiér aktiviteten genererer objektet "Navn" med tre underordnede elementer først mellemnavn og efternavn, afhængigt af "Name.First", "Name.Middle" og "Name.Last" i tabeldefinitionen.

## <a name="azure-documentdb-linked-service-properties"></a>Egenskaber for Azure DocumentDB sammenkædede tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Azure DocumentDB sammenkædet tjeneste. 

| **Egenskaben** | **Beskrivelse** | **Påkrævet** |
| -------- | ----------- | --------- |
| type | Typeegenskaben skal være angivet til: **DocumentDb** | Ja |
| connectionString | Angive oplysninger, der er behov for at oprette forbindelse til Azure DocumentDB database. | Ja |

## <a name="azure-documentdb-dataset-type-properties"></a>Azure DocumentDB Dataset egenskaber

Se [oprette datasæt](data-factory-create-datasets.md) artiklen til en komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt. Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).
 
Afsnittet typeProperties er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for datasættet af typen **DocumentDbCollection** har følgende egenskaber.

| **Egenskaben** | **Beskrivelse** | **Påkrævet** |
| -------- | ----------- | -------- |
| collectionName | Navnet på samlingen DocumentDB dokument. | Ja |


Eksempel:

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Skema af Data Factory
For skema ledig data butikker som DocumentDB indstiller tjenesten Data Factory skemaet i en af følgende måder:  

1.  Hvis du angiver strukturen af data ved hjælp af egenskaben **strukturen** i definitionen af datasæt, respekterer tjenesten Data Factory denne struktur som i skemaet. I dette tilfælde, hvis en række ikke indeholder en værdi for en kolonne, leveres en null-værdi for den.
2.  Hvis du ikke angiver strukturen af data ved hjælp af egenskaben **strukturen** i definitionen af datasæt, indstiller tjenesten Data Factory skemaet ved hjælp af den første række i dataene. Hvis den første række ikke indeholder det hele skema, vil nogle af kolonnerne i dette tilfælde der mangle i resultatet af kopieringen.

Derfor for skema ledig datakilder er den bedste fremgangsmåde at angive strukturen af data ved hjælp af egenskaben **struktur** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Azure DocumentDB kopi aktivitet egenskaber

Se [Oprette rørledninger](data-factory-create-pipelines.md) artiklen til en komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter. Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter.
 
**Note:** Kopiér aktiviteten tager kun én input og producerer kun én output.

Tilgængelige egenskaber i sektionen typeProperties aktivitetens dels varierer i forhold til hver aktivitetstype og i tilfælde af kopi aktivitet de varierer afhængigt af typerne datakilder og dræn.

I tilfælde af kopi aktivitet når kilde er af typen **DocumentDbCollectionSource** er følgende egenskaber tilgængelige i **typeProperties** afsnit:

| **Egenskaben** | **Beskrivelse** | **Tilladte værdier** | **Påkrævet** |
| ------------ | --------------- | ------------------ | ------------ |
| forespørgsel | Angive forespørgslen for at læse dataene. | Forespørge streng, der understøttes af DocumentDB. <br/><br/>Eksempel:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | Nej <br/><br/>Hvis ikke angives, den SQL-sætning, der udføres:`select <columns defined in structure> from mycollection` 
| nestingSeparator | Specialtegn til at angive, at dokumentet er indlejret | Et tegn. <br/><br/>DocumentDB er en NoSQL store for JSON dokumenter, hvor indlejrede strukturer er tilladt. Azure Data Factory gør det muligt for brugeren til angivelse af hierarki via nestingSeparator, som er "." i ovenstående eksempler. Med separatoren, Kopiér aktiviteten genererer objektet "Navn" med tre underordnede elementer først mellemnavn og efternavn, afhængigt af "Name.First", "Name.Middle" og "Name.Last" i tabeldefinitionen. | Nej

**DocumentDbCollectionSink** understøtter følgende egenskaber:

| **Egenskaben** | **Beskrivelse** | **Tilladte værdier** | **Påkrævet** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Der er brug for et specialtegn i navnet på kildekolonnen til at angive indlejrede dokumentet. <br/><br/>For eksempel ovenfor: `Name.First` i outputtet tabel giver følgende JSON strukturen i DocumentDB dokumentet:<br/><br/>"Navn": {}<br/>  "Første": "John"<br/>}, | Tegn, der bruges til at adskille indlejringsniveauer.<br/><br/>Standardværdien er `.` (punktum). | Tegn, der bruges til at adskille indlejringsniveauer. <br/><br/>Standardværdien er `.` (punktum). | Nej | 
| writeBatchSize | Antallet af parallelle forespørgsler til DocumentDB webtjenesten til at oprette dokumenter.<br/><br/>Du kan finjustere ydeevnen, når du kopierer data til/fra DocumentDB ved hjælp af denne egenskab. Du kan forvente en bedre ydeevne, når du øger writeBatchSize, da flere parallelle anmodninger til DocumentDB sendes. Men du skal undgå (throttling), der kan udløse fejlmeddelelsen: "Anmode om rente er stort".<br/><br/>(Throttling) bestemmes af en række faktorer, herunder størrelsen på dokumenter, antallet af ord i dokumenter, indeksering politik af destinationswebsteder osv. Kopiere noget, du kan bruge en bedre samling (fx S3) skal have mest overførsel (2.500 anmodning enheder/sekund). | Heltal | Ikke (standard: 10000) |
| writeBatchTimeout | Ventetid for handlingen er fuldført, før det udløber. | TimeSpan<br/><br/> Eksempel: "00: 30:00" (30 minutter). | Nej |
 
## <a name="appendix"></a>Tillæg
1. **Spørgsmål:**  
    betyder kopi aktivitet support opdatering af eksisterende poster?

    **Svar:**  
    Nej.

2. **Spørgsmål:**  
    hvordan ved et nyt forsøg af en kopi til DocumentDB omhandler allerede kopieret poster?

    **Svar:**  
    Hvis poster har en "-ID" felt og kopieringen forsøger at indsætte en post med det samme ID, kopieringen opstår en fejl.  
 
3. **Spørgsmål:** 
    Data Factory understøtter [område eller hash-baserede data partitionering](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Svar:** 
    Nej. 
4. **Spørgsmål:** 
    kan jeg angive mere end én DocumentDB samling for en tabel?
    
    **Svar:** 
    Nej. Kun én af websteder kan angives på nuværende tidspunkt.
     
## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.
