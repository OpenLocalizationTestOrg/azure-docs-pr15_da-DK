<properties 
    pageTitle="Flytte data fra Amazon Simple Lagringstjeneste ved hjælp af Data Factory | Microsoft Azure" 
    description="Få mere at vide om, hvordan du kan flytte data fra Amazon Simple lagerplads Service (S3) ved hjælp af Azure Data Factory." 
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
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Flytte data fra Amazon enkel lagerplads tjenesten ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra Amazon Simple lagerplads Service (S3) til en anden data gemme. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , der giver et overblik over flytning af data, og en liste over understøttede datakilder/sink data butikker kopi aktivitet.  

Data factory understøtter i øjeblikket kun flytte data fra Amazon S3 til andre data butikker, men ikke til at flytte data fra andre data butikker til Amazon S3.

## <a name="required-permissions"></a>Nødvendige tilladelser

For at kopiere data fra Amazon S3, skal du kontrollere, at du har fået under tilladelser:

- **S3:GetObject** og **s3:GetObjectVersion** for Amazon S3 objekt handlinger
- **S3:ListBucket** og **s3:ListAllMyBuckets** (bruges i kun kopiere guiden) for Amazon S3 Malerbøtte handlinger 

Du kan finde den komplette liste over Amazon S3 permisions med detaljeret fra [Angive tilladelser i en politik](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der blev kopieret data fra Amazon S3 er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det viser, hvordan du kopierer data fra Amazon S3 til Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Eksempel: Kopiere data fra Amazon S3 til Azure Blob
Dette eksempel vises, hvordan du kopierer data fra en Amazon S3 til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

- En sammenkædet tjeneste af typen [AwsAccessKey](#linked-service-properties).
- En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- En input [datasæt](data-factory-create-datasets.md) af typen [AmazonS3](#dataset-type-properties).
- En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [FileSystemSource](#copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra Amazon S3 til en Azure blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

**Amazon S3 sammenkædet service**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
            }
        }
    }

**Azure sammenkædet lagringstjeneste**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Amazon S3 input datasæt**

Angive **"eksterne": SAND** informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory. Angive denne egenskab til sand på et input datasæt, der ikke er forårsaget af en aktivitet i pipeline.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }



**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
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
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Rørledning med kopi aktiviteter**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **FileSystemSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Egenskaber for sammenkædede tjenesten

Følgende tabel indeholder en beskrivelse til JSON elementer bestemte til Amazon S3 (**AwsAccessKey**) sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | Hemmeligt hurtigtast ID. | streng | Ja |
| secretAccessKey | Hemmeligt hurtigtast sig selv. | Krypteret hemmeligt streng | Ja | 


## <a name="dataset-type-properties"></a>Egenskaber for datasættet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **AmazonS3** (som indeholder Amazon S3 datasæt) har følgende egenskaber

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------- | ------ | 
| bucketName | S3 Malerbøtte navnet. | Streng | Ja |
| nøgle | Tasten S3 objekt. | Streng | Nej | 
| præfiks | Præfiks for tasten S3 objekt. Objekter, hvis taster starter med præfikset er markeret. Gælder kun, når tasten er tom. | Streng | Nej | 
| version | Versionen af S3 objekt Hvis S3 versionsstyring er aktiveret. | Streng | Nej |  
| Formatér | Følgende formattyper understøttes: **tekstformat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Angiv egenskaben **type** under formater til en af følgende værdier. Se [Angive tekstformat](#specifying-textformat), [Der angiver AvroFormat](#specifying-avroformat), [Angive JsonFormat](#specifying-jsonformat), [Angive OrcFormat](#specifying-orcformat)og [Angive ParquetFormat](#specifying-parquetformat) sektioner få mere at vide. Hvis du vil kopiere filer som-er mellem fil-baserede lagre (binære kopi), kan du springe afsnittet format i begge input- og outputområder datasæt definitioner.| Nej
| komprimering | Angiv den type og komprimering for dataene. Understøttede datatyper er: **GZip**, **Deflate**og **BZip2** og understøttede niveauer: **Optimal** og **hurtigst**. Komprimeringsindstillingerne understøttes i øjeblikket ikke data i **AvroFormat** eller **OrcFormat**. Yderligere oplysninger, se [komprimering support](#compression-support) afsnittet.  | Nej |

> [AZURE.NOTE] bucketName + tast angiver placeringen af objektet S3, hvor Malerbøtte er objektbeholderen rodwebstedet for S3 objekter og nøgle er den fulde sti til S3 objekt.

### <a name="sample-dataset-with-prefix"></a>Eksempel datasæt med præfiks

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Sæt med eksempeldata (med version)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Dynamisk stier til S3

I eksemplet bruge vi faste værdier til nøgle og bucketName egenskaber i Amazon S3 datasættet. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Du kan have Data Factory beregne nøgle og bucketName dynamisk på kørselstidspunktet ved hjælp af systemvariabler som SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Du kan gøre det samme for egenskaben præfiks for et Amazon S3 datasæt. Se [Data Factory-funktioner og systemvariabler](data-factory-functions-variables.md) til en liste over understøttede funktioner og variabler. 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Kopiere aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen **typeProperties** aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når der er kilde i kopi aktivitet af typen **FileSystemSource** (som indeholder Amazon S3), er følgende egenskaber tilgængelige i typeProperties afsnit:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- | 
| rekursive | Angiver, om at gælder listen S3 objekter under mappen. | Sand/falsk | Nej | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

## <a name="next-steps"></a>Næste trin
Se følgende artikler: 
- [Kopiér aktivitet selvstudium](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finde trinvise instruktioner til oprettelse af en rørledning med en kopi aktivitet. 
