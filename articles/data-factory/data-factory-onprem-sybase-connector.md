<properties 
    pageTitle="Flytte data fra Sybase | Azure Data Factory" 
    description="Få mere at vide om, hvordan til at flytte data fra Sybase-Database ved hjælp af Azure Data Factory." 
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

# <a name="move-data-from-sybase-using-azure-data-factory"></a>Flytte data fra Sybase ved hjælp af Azure Data Factory 

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra Sybase til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

Factory datatjeneste understøtter oprettelse af forbindelse til lokale Sybase datakilder ved hjælp af Datastyringsgateway. Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway og trinvis vejledning til konfiguration af gatewayen. 

> [AZURE.NOTE]
> Gatewayen er påkrævet, selvom Sybase-databasen er placeret i en Azure IaaS VM. Du kan installere gatewayen på den samme IaaS VM som datalager eller på en anden VM, så længe gatewayen kan oprette forbindelse til databasen. 

Data factory understøtter i øjeblikket kun flytte data fra Sybase til andre data butikker, ikke fra andre data butikker til Sybase.

## <a name="installation"></a>Installation

For Datastyringsgateway til at oprette forbindelse til Sybase-Database, skal du installere [data-provider til Sybase](http://go.microsoft.com/fwlink/?linkid=324846) på det samme system som Datastyringsgatewayen.

> [AZURE.NOTE] Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der kopierer data fra en Sybase-database til en af de understøttede sink data butikker er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra Sybase-database til Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.   

## <a name="sample-copy-data-from-sybase-to-azure-blob"></a>Eksempel: Kopiere data fra Sybase til Azure Blob
Dette eksempel vises, hvordan du kopierer data fra en Sybase-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesSybase](data-factory-onprem-sybase-connector.md#sybase-linked-service-properties).
2.  En liked tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [RelationalTable](data-factory-onprem-sybase-connector.md#sybase-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  [Rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [RelationalSource](data-factory-onprem-sybase-connector.md#sybase-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra et forespørgselsresultat i Sybase-database til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

Konfiguration af datastyringsgateway, som et første skridt. Der er vejledningen i artiklen [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) .

**Sybase sammenkædet service:**

    {
        "name": "OnPremSybaseLinkedService",
        "properties": {
            "type": "OnPremisesSybase",
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


**Sybase input datasæt:**

Eksemplet antages det, du har oprettet en tabel "MinTabel" i Sybase og den indeholder en kolonne med navnet "tidsstempel" for klokkeslætsdata serie.

Angive "eksterne": SAND informerer tjenesten Data Factory, dette dataset er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory. Bemærk, at **typen** sammenkædede tjenesten er indstillet til: **RelationalTable**. 
    
    {
        "name": "SybaseDataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremSybaseLinkedService",
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
        "name": "AzureBlobSybaseDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/sybase/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **RelationalSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer dataene fra Databaseadministratoren. Tabellen Ordrer i databasen.


    {
        "name": "CopySybaseToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from DBA.Orders"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "SybaseDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobSybaseDataSet"
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
                    "name": "SybaseToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="sybase-linked-service-properties"></a>Sybase sammenkædet service egenskaber

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Sybase sammenkædet tjeneste.

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
type | Typeegenskaben skal være angivet til: **OnPremisesSybase** | Ja
Server | Navnet på Sybase-serveren. | Ja
database | Navnet på Sybase-databasen. | Ja 
skema  | Navnet på skemaet i databasen. | Nej
authenticationType | Type godkendelse, der bruges til at oprette forbindelse til Sybase-database. Mulige værdier er: anonym, Basic og Windows. | Ja
brugernavn | Angiv brugernavn, hvis du bruger Basic eller Windows-godkendelse. | Nej
adgangskode | Angiv adgangskoden til den brugerkonto, du har angivet for feltet username. |  Nej
gatewayName | Navnet på gatewayen, som tjenesten Data Factory skal bruge til at oprette forbindelse til den lokale Sybase-database. | Ja 

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal Sybase-datakilde.

## <a name="sybase-dataset-type-properties"></a>Sybase datasæt typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet typeProperties er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen **typeProperties** for dataset af typen **RelationalTable** (som indeholder Sybase datasæt) har følgende egenskaber:

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Tabelnavn | Navnet på tabellen i forekomsten Sybase-Database, som sammenkædede service henviser til. | Ingen (Hvis der er angivet **forespørgsel** med **RelationalSource** )

## <a name="sybase-copy-activity-type-properties"></a>Sybase kopi aktivitet typeegenskaber 

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, kan du [oprette rørledninger](data-factory-create-pipelines.md) til artiklen. Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når kilde er af typen **RelationalSource** (som indeholder Sybase) er følgende egenskaber tilgængelige i **typeProperties** afsnit:

Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet
-------- | ----------- | -------------- | --------
forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. For eksempel: Vælg * fra MinTabel. | Ingen (Hvis der er angivet **tabelnavn** af **datasæt** )

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-sybase"></a>Tilknytning til Sybase

Som nævnt i artiklen [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktiviteten automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Sybase understøtter T-SQL og T-SQL-datatyper. Se [Azure SQL-forbindelse](data-factory-azure-sql-connector.md) til en tabel over tilknytning fra sql datatyper til .NET type, artikel.

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.