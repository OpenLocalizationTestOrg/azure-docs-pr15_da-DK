<properties 
    pageTitle="Flytte data fra Teradata | Azure Data Factory" 
    description="Få mere at vide om Teradata forbindelse til Data Factory-tjenesten, hvor du kan flytte data fra Teradata-Database" 
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

# <a name="move-data-from-teradata-using-azure-data-factory"></a>Flytte data fra Teradata ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra Teradata til en anden data gemme. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

Data factory understøtter oprettelse af forbindelse til lokale Teradata kilder via af Datastyringsgateway. Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway og trinvis vejledning til konfiguration af gatewayen. 

> [AZURE.NOTE]
Gatewayen er påkrævet, selvom Teradata er placeret i en Azure IaaS VM. Du kan installere gatewayen på den samme IaaS VM som datalager eller på en anden VM, så længe gatewayen kan oprette forbindelse til databasen. 

Data factory understøtter kun flytte data fra Teradata til andre data butikker, ikke fra andre data butikker til Teradata.

## <a name="installation"></a>Installation 

For Datastyringsgateway til at oprette forbindelse til Teradata-Database, skal du installere [.NET-dataudbyder for Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) på det samme system som Datastyringsgatewayen.

> [AZURE.NOTE] Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der blev kopieret data fra Teradata til en af de understøttede sink data butikker er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra Teradata til Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.   

### <a name="sample-copy-data-from-teradata-to-azure-blob"></a>Eksempel: Kopiere data fra Teradata til Azure Blob

Dette eksempel viser, hvordan du kopierer data fra en Teradata-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.  [Rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra et forespørgselsresultat i Teradata-database til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

Konfiguration af datastyringsgateway, som et første skridt. Der er vejledningen i artiklen [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) .

**Teradata sammenkædet service:**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Azure Blob-lager sammenkædet service:**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }


**Teradata input datasæt:**

Eksemplet antages det, du har oprettet en tabel "MinTabel" i Teradata og den indeholder en kolonne med navnet "tidsstempel" for klokkeslætsdata serie.

Angive "eksterne": SAND informerer tjenesten Data Factory, tabellen er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ],
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Rørledning med kopi aktiviteter:**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **RelationalSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer dataene i den seneste time kopiere.

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
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
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## <a name="teradata-linked-service-properties"></a>Egenskaber for Teradata sammenkædet tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Teradata sammenkædet tjeneste. 

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
type | Typeegenskaben skal være angivet til: **OnPremisesTeradata** | Ja
Server | Navnet på Teradata-serveren. | Ja
authenticationType | Type godkendelse, der bruges til at oprette forbindelse til Teradata-database. Mulige værdier er: anonym, Basic og Windows. | Ja
brugernavn | Angiv brugernavn, hvis du bruger Basic eller Windows-godkendelse. | Nej 
adgangskode | Angiv adgangskoden til den brugerkonto, du har angivet for feltet username. | Nej 
gatewayName | Navnet på gatewayen, som tjenesten Data Factory skal bruge til at oprette forbindelse til den lokale Teradata-database. | Ja

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal Teradata-datakilde.

## <a name="teradata-dataset-type-properties"></a>Egenskaber for Teradata-datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Der er i øjeblikket ingen typeegenskaber, der understøttes i forbindelse med Teradata datasættet. 


## <a name="teradata-copy-activity-type-properties"></a>Teradata kopi aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når kilde er af typen **RelationalSource** (som indeholder Teradata) er følgende egenskaber tilgængelige i **typeProperties** afsnit:

Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet
-------- | ----------- | -------------- | --------
forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. For eksempel: Vælg * fra MinTabel. | Ja

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-teradata"></a>Tilknytning til Teradata

Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktiviteten automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data til Teradata, anvendes følgende tilknytninger fra Teradata type til .NET type.

Teradata-Database type | .NET framework type
----------------- | ---------------------------
Tegn | Streng
CLOB | Streng
Grafik | Streng
VarChar | Streng
VarGraphic | Streng
BLOB | Byte]
Byte | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Dobbelt | Dobbelt
Heltal | Int32
Tal | Dobbelt
SmallInt | Int16
Dato | Dato og klokkeslæt
Tid | TimeSpan
Tiden med tidszone | Streng
Tidsstempel | Dato og klokkeslæt
Tidsstempel med tidszone | DateTimeOffset
Interval dag | TimeSpan
Interval dag til time | TimeSpan
Interval dag til minut | TimeSpan
Interval dag til sekund | TimeSpan
Interval time | TimeSpan
Interval time, minut | TimeSpan
Interval time til sekund | TimeSpan
Interval minut | TimeSpan
Interval minut til sekund | TimeSpan
Interval anden | TimeSpan
Interval år | Streng
Interval år til måned | Streng
Interval måned | Streng
Period(date) | Streng
Period(time) | Streng
Periode (tid med tidszone) | Streng
Period(Timestamp) | Streng
Periode (tidsstempel med tidszone) | Streng
XML | Streng

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.