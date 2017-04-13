<properties 
    pageTitle="Flytte data fra OData kilder | Azure Data Factory" 
    description="Få mere at vide om, hvordan til at flytte data fra OData kilder ved hjælp af Azure Data Factory." 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Flytte data fra et OData datakilde ved hjælp af Azure Data Factory
I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra et OData-kilde til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

> [AZURE.NOTE] Denne OData-forbindelsen understøttelse kopiere data fra begge skyen OData og lokal OData kilder. For disse skal du installere af Datastyringsgateway. Se [flytte data mellem i det lokale miljø og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway.

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der kopierer data fra et OData-kilde er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempler giver eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra et OData-kilde til en Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Eksempel: Kopiere data fra OData-kilde til Azure Blob

Dette eksempel vises, hvordan du kopierer data fra et OData-kilde til Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OData](#odata-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [ODataResource](#odata-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [RelationalSource](#odata-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra en OData kilde til en Azure blob forespørgsel hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

**OData sammenkædet service** I dette eksempel bruges den basisgodkendelse. Se [OData sammenkædet service](#odata-linked-service-properties) afsnittet for forskellige typer godkendelse, kan du bruge. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**OData input datasæt**

Angive "eksterne": "true" informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

Angive **sti** i datasættet er definition valgfrit. 


**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **RelationalSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer de seneste (nyeste) data fra OData-kilde.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


Angive **forespørgsel** i pipeline definitionen er valgfrit. Den **URL-adresse** , som tjenesten Data Factory bruger til at hente data er: URL-adressen er angivet i tjenesten sammenkædede (påkrævet) + sti er angivet i datasættet (valgfrit) + forespørgsel i pipeline (valgfrit). 

## <a name="odata-linked-service-properties"></a>OData sammenkædet egenskaber for tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for OData-sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **OData** | Ja |
| URL-adresse| URL-adressen til OData-tjenesten. | Ja |
| authenticationType | Type godkendelse, der bruges til at oprette forbindelse til OData-kilde. <br/><br/> For skyen OData er mulige værdier anonym og grundlæggende. For OData-lokalt er mulige værdier anonym, Basic og Windows. | Ja | 
| brugernavn | Angiv brugernavn, hvis du bruger basisgodkendelse. | Ja (kun hvis du bruger basisgodkendelse) | 
| adgangskode | Angiv adgangskoden til den brugerkonto, du har angivet for feltet username. | Ja (kun hvis du bruger basisgodkendelse) | 
| gatewayName | Navnet på gatewayen, som tjenesten Data Factory skal bruge til at oprette forbindelse til lokale OData-tjenesten. Angiv kun, hvis du kopierer data fra lokalt OData kilde. | Nej |

### <a name="using-basic-authentication"></a>Ved hjælp af grundlæggende godkendelse

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Ved hjælp af anonym godkendelse
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Ved hjælp af Windows-godkendelse, få adgang til lokale OData-kilde

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Egenskaber for OData-datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **ODataResource** (som indeholder OData datasæt) har følgende egenskaber

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| sti | Stien til OData ressourcen | Nej | 

## <a name="odata-copy-activity-type-properties"></a>Egenskaber for OData kopi aktivitet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når kilde er af typen **RelationalSource** (som indeholder OData) er følgende egenskaber tilgængelige i typeProperties afsnit:

| Egenskaben | Beskrivelse | Eksempel | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | "? $select = navn, beskrivelse og $top = 5" | Nej | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Tilknytning til OData

Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du gemmer flytte data fra OData-data, er OData-datatyper knyttet til .NET datatyper.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

