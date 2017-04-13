<properties 
    pageTitle="Flytte data fra DB2 | Azure Data Factory" 
    description="Få mere at vide om, hvordan flytte data fra DB2-Database ved hjælp af Azure Data Factory" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Flytte data fra DB2 ved hjælp af Azure Data Factory
I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra DB2 til en anden data gemme. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

Data factory understøtter oprettelse af forbindelse til lokale DB2 datakilder ved hjælp af Datastyringsgateway. Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway og trinvis vejledning til konfiguration af gatewayen. 

> [AZURE.NOTE]
Brug gatewayen til at oprette forbindelse til DB2, selvom de ligger i Azure IaaS FOS. Hvis du prøver at oprette forbindelse til en forekomst af DB2 hostet i skyen, kan du også installere forekomsten gateway i IaaS VM.

Data factory understøtter i øjeblikket kun flytte data fra DB2 til andre data butikker, ikke fra andre data butikker til DB2. 

## <a name="installation"></a>Installation 

Datastyringsgateway indeholder en indbygget DB2-driver, der understøtter følgende: 

- SQLAM 9 / 10 / 11
- DB2 for LUW (Linux, Unix, Windows)
- DB2 til z/OS og DB2 for jeg (også kendt som / 400)

Derfor skal du ikke længere manuelt installere drivere, når du kopierer data fra DB2.

> [AZURE.NOTE] Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer. 

## <a name="copy-data-wizard"></a>Kopiere guiden Data
Den nemmeste måde at oprette en rørledning, der kopierer data fra en DB2-Database er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempler giver eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra DB2-database og Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Eksempel: Kopiere data fra DB2 til Azure Blob

Dette eksempel viser, hvordan du kopierer data fra en lokal DB2-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3.  En input [datasæt](data-factory-create-datasets.md) af typen [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

Eksemplet kopierer data fra et forespørgselsresultat i en DB2-database til en Azure blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

Som en første trin skal du installere og konfigurere en datastyringsgateway. Instruktioner er i artiklen [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) .

**DB2 sammenkædet service:**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
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

**DB2 input datasæt:**

Eksemplet antages det, du har oprettet en tabel "MinTabel" i DB2 og den indeholder en kolonne med navnet "tidsstempel" for klokkeslætsdata serie.

Angive "eksterne": SAND informerer tjenesten Data Factory, dette dataset er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory. Bemærk, at **typen** er indstillet til **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
            "typeProperties": {},
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
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Rørledning med kopi aktiviteter:**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **RelationalSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer dataene fra tabellen Ordrer.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Egenskaber for DB2 sammenkædet tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for DB2 sammenkædet tjeneste. 

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **OnPremisesDB2** | Ja |
| Server | Navnet på DB2-serveren. | Ja |
| database | Navnet på DB2-databasen. | Ja |
| skema | Navnet på skemaet i databasen. Skema hedder store og små bogstaver. | Nej |
| authenticationType | Type godkendelse, der bruges til at oprette forbindelse til DB2-database. Mulige værdier er: anonym, Basic og Windows. | Ja |
| brugernavn | Angiv brugernavn, hvis du bruger Basic eller Windows-godkendelse. | Nej |
| adgangskode | Angiv adgangskoden til den brugerkonto, du har angivet for feltet username. | Nej |
| gatewayName | Navnet på gatewayen, som tjenesten Data Factory skal bruge til at oprette forbindelse til den lokale DB2-database. | Ja |

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal DB2 datakilde. 


## <a name="db2-dataset-type-properties"></a>Egenskaber for DB2 datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet typeProperties er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen RelationalTable (som indeholder DB2 datasæt) har følgende egenskaber.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| Tabelnavn | Navnet på tabellen i forekomsten DB2-Database, som sammenkædede service henviser til. Tabelnavn er store og små bogstaver. | Ingen (Hvis der er angivet **forespørgsel** med **RelationalSource** ) |

## <a name="db2-copy-activity-type-properties"></a>DB2 kopi aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

For kopi aktivitet, når kilde er af typen **RelationalSource** (som indeholder DB2) er følgende egenskaber tilgængelige i typeProperties afsnit:


| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------- | -------------- |
| forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. For eksempel: `"query": "select * from "MySchema"."MyTable""`. | Ingen (Hvis der er angivet **tabelnavn** af **datasæt** )|

> [AZURE.NOTE] Der skelnes mellem skemaet og tabelnavne. Omslutte navnene i "" (dobbelte anførselstegn) i forespørgslen.  

**Eksempel:**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Tilknytning til DB2
Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktiviteten automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data til DB2, anvendes følgende tilknytninger fra DB2 type til .NET type.

Skriv DB2-Database | .NET framework type 
----------------- | ------------------- 
SmallInt | Int16
Heltal | Int32
BigInt | Int64
Reelt tal | Enkelt
Dobbelt | Dobbelt
Flydende | Dobbelt
Decimal | Decimal
DecimalFloat | Decimal
Numerisk | Decimal
Dato | Dato og klokkeslæt
Tid | TimeSpan
Tidsstempel | Dato og klokkeslæt
XML | Byte]
Tegn | Streng
VarChar | Streng
LongVarChar | Streng
DB2DynArray | Streng
Binær | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
Grafik | Streng
VarGraphic | Streng
LongVarGraphic | Streng
CLOB | Streng
BLOB | Byte]
DbClob | Streng
SmallInt | Int16
Heltal | Int32
BigInt | Int64
Reelt tal | Enkelt
Dobbelt | Dobbelt
Flydende | Dobbelt
Decimal | Decimal
DecimalFloat | Decimal
Numerisk | Decimal
Dato | Dato og klokkeslæt
Tid | TimeSpan
Tidsstempel | Dato og klokkeslæt
XML | Byte]
Tegn | Streng


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.


