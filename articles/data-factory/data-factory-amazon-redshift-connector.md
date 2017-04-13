<properties 
    pageTitle="Flytte data fra Amazon Redshift ved hjælp af Data Factory | Microsoft Azure" 
    description="Få mere at vide om, hvordan til at flytte data fra Amazon Redshift ved hjælp af Azure Data Factory." 
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
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Flytte data fra Amazon Redshift ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra Amazon Redshift til en anden data gemme. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og en liste over kilde/sink data butikker.  

Data factory understøtter i øjeblikket kun flytte data fra Amazon Redshift til andre data butikker, men ikke til at flytte data fra andre data butikker til Amazon Redshift.

## <a name="prerequisites"></a>Forudsætninger

- Hvis du vil flytte data til et lokalt datalager, Giv Datastyringsgateway (Brug IP-adressen på maskinen) adgang til Amazon Redshift klynge. Du kan finde instruktioner i [godkende adgang til klyngen](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) . 
- Hvis du vil flytte data i en Azure datalager, kan du se [Azure Data Center IP-intervaller](https://www.microsoft.com/download/details.aspx?id=41653) til de beregne IP-adresseområder (herunder SQL områder) bruges af Microsoft Azure-datacentre. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der blev kopieret data fra Amazon Redshift er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Det viser, hvordan du kopierer data fra Amazon Redshift til Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Eksempel: Kopiere data fra Amazon Redshift til Azure Blob
Dette eksempel vises, hvordan du kopierer data fra en Amazon Redshift-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

- En sammenkædet tjeneste af typen [AmazonRedshift](#linked-service-properties).
- En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- En input [datasæt](data-factory-create-datasets.md) af typen [RelationalTable](#dataset-type-properties).
- En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [RelationalSource](#copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra et forespørgselsresultat i Amazon Redshift til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

**Amazon Redshift sammenkædet service**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
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

**Amazon Redshift input datasæt**

Angive **"eksterne": SAND** informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory. Angive denne egenskab til sand på et input datasæt, der ikke er forårsaget af en aktivitet i pipeline.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
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
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **RelationalSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer dataene i den seneste time kopiere.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Egenskaber for sammenkædede tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Amazon Redshift sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **AmazonRedshift**. | Ja |
| Server | IP-adresse eller værtsnavn navnet på serveren, Amazon Redshift. | Ja |
| port | Antallet af den TCP-port, Amazon Redshift serveren bruger lytte til klientforbindelser. | Nej, standardværdi: 5439 |
| database | Navnet på Amazon Redshift databasen. | Ja |
| brugernavn | Navnet på brugeren, der har adgang til databasen.| Ja |
| adgangskode | Adgangskode til kontoen.| Ja |


## <a name="dataset-type-properties"></a>Egenskaber for datasættet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **RelationalTable** (som indeholder Amazon Redshift datasæt) har følgende egenskaber

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| Tabelnavn | Navnet på tabellen i Amazon Redshift databasen, som sammenkædede service henviser til. | Ingen (Hvis der er angivet **forespørgsel** med **RelationalSource** ) | 

## <a name="copy-activity-type-properties"></a>Kopiere aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen **typeProperties** aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når der er kilde kopi aktivitet af typen **RelationalSource** (som indeholder Amazon Redshift) er følgende egenskaber tilgængelige i typeProperties afsnit:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. For eksempel: Vælg * fra MinTabel. | Ingen (Hvis der er angivet **tabelnavn** af **datasæt** ) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Tilknytning til Amazon Redshift

Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere typer med to-trins følgende fremgangsmåde:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data til Amazon Redshift, bruges følgende tilknytninger fra Amazon Redshift typer til .NET datatyper.

Amazon Redshift Type | .NET baseret Type
-------------------- | ---------------
SMALLINT | Int16
HELTAL | Int32
BIGINT | Int64
DECIMAL | Decimal
REELT TAL | Enkelt
DOBBELT PRÆCISION | Dobbelt
BOOLESK VÆRDI | Streng
TEGN | Streng
VARCHAR | Streng
DATO | Dato og klokkeslæt
TIDSSTEMPEL | Dato og klokkeslæt
TEKST | Streng



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

## <a name="next-steps"></a>Næste trin
Se følgende artikler: 
- [Kopiér aktivitet selvstudium](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finde trinvise instruktioner til oprettelse af en rørledning med en kopi aktivitet. 