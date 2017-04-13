<properties 
    pageTitle="Flytte data fra MySQL | Azure Data Factory" 
    description="Få mere at vide om, hvordan til at flytte data fra MySQL-database ved hjælp af Azure Data Factory." 
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

# <a name="move-data-from-mysql-using-azure-data-factory"></a>Flytte data fra MySQL ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra MySQL til en anden data gemme. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

Factory datatjeneste understøtter oprettelse af forbindelse til lokale MySQL datakilder ved hjælp af Datastyringsgateway. Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway og trinvis vejledning til konfiguration af gatewayen. 

> [AZURE.NOTE] Gatewayen er påkrævet, selvom MySQL-databasen er placeret i en Azure IaaS virtual machine (VM). Du kan installere gatewayen på den samme VM som datalager eller på en anden VM, så længe gatewayen kan oprette forbindelse til databasen.  

Data factory understøtter i øjeblikket kun flytte data fra MySQL til andre data butikker, men ikke til at flytte data fra andre data butikker til MySQL.

## <a name="installation"></a>Installation 
For Datastyringsgateway til at oprette forbindelse til MySQL-Database, skal du installere [MySQL forbindelse/redskaber 6.6.5 til Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) på det samme system som Datastyringsgatewayen.

> [AZURE.NOTE] Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der blev kopieret data fra en MySQL-database til en af de understøttede sink data butikker er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en pipeline. Finde en komplet gennemgang med en trinvis vejledning i artiklen [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) . Data kan kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Eksempel: Kopiere data fra MySQL til Azure Blob
Dette eksempel vises, hvordan du kopierer data fra en lokal MySQL-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  

> [AZURE.IMPORTANT] Dette eksempel indeholder JSON kodestykker. Det omfatter ikke trinvise instruktioner til oprettelse af data factory. Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artiklen finde en trinvis vejledning. 
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra et forespørgselsresultat i MySQL-database til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

Konfiguration af datastyringsgateway, som et første skridt. Der er vejledningen i artiklen [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) . 

**Sammenkædet MySQL-tjenesten**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**MySQL input datasæt**

Eksemplet antages det, du har oprettet en tabel "MinTabel" i MySQL og den indeholder en kolonne med navnet "timestampcolumn" for klokkeslætsdata serie.

Angive "eksterne": "true" informerer tjenesten Data Factory, tabellen er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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



**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Egenskaber for MySQL sammenkædet tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for MySQL sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **OnPremisesMySql** | Ja |
| Server | Navnet på MySQL-serveren. | Ja |
| database | Navnet på MySQL-databasen. | Ja | 
| skema  | Navnet på skemaet i databasen. | Nej | 
| authenticationType | Type godkendelse, der bruges til at oprette forbindelse til MySQL-database. Mulige værdier er: anonym, Basic og Windows. | Ja | 
| brugernavn | Angiv brugernavn, hvis du bruger Basic eller Windows-godkendelse. | Nej | 
| adgangskode | Angiv adgangskoden til den brugerkonto, du har angivet for feltet username. | Nej | 
| gatewayName | Navnet på gatewayen, som tjenesten Data Factory skal bruge til at oprette forbindelse til den lokale MySQL-database. | Ja |

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal MySQL-datakilde.

## <a name="mysql-dataset-type-properties"></a>Egenskaber for MySQL-datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **RelationalTable** (som indeholder MySQL datasæt) har følgende egenskaber

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| Tabelnavn | Navnet på tabellen i den MySQL-Database-forekomst, som sammenkædede service henviser til. | Ingen (Hvis der er angivet **forespørgsel** med **RelationalSource** ) | 

## <a name="mysql-copy-activity-type-properties"></a>MySQL kopi aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input- og tabeller, er politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen **typeProperties** aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når der er kilde i kopi aktivitet af typen **RelationalSource** (som indeholder MySQL) er følgende egenskaber tilgængelige i typeProperties afsnit:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. For eksempel: Vælg * fra MinTabel. | Ingen (Hvis der er angivet **tabelnavn** af **datasæt** ) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Tilknytning til MySQL

Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere typer med to-trins følgende fremgangsmåde:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data til MySQL, anvendes følgende tilknytninger fra MySQL typer til .NET datatyper.

| Skriv MySQL-Database | .NET framework type |
| ------------------- | ------------------- | 
| bigint usigneret | Decimal |
| bigint | Int64 |
| bit | Decimal |
| BLOB | Byte] |
| Boolesk |  Boolesk værdi | 
| tegn | Streng |
| dato | Dato og klokkeslæt |
| dato og klokkeslæt | Dato og klokkeslæt |
| decimal | Decimal |
| dobbelt præcision | Dobbelt |
| dobbelt | Dobbelt |
| Optæl | Streng |
| slæk | Enkelt |
| heltal uden fortegn | Int64 |
| heltal | Int32 |
| heltal uden fortegn | Int64 |
| heltal | Int32 | 
| lang varbinary | Byte] |
| lang varchar | Streng |
| longblob | Byte] |
| LONGTEXT | Streng | 
| mediumblob |  Byte] | 
| mediumint usigneret | Int64 |
| mediumint | Int32 | 
| mediumtext | Streng |
| numeriske | Decimal |
| reelt tal |  Dobbelt |
| angive | Streng |
| smallint usigneret | Int32 |
| smallint | Int16 |
| tekst | Streng |
| tid | TimeSpan |
| tidsstempel | Dato og klokkeslæt |
| tinyblob | Byte] |
| tinyint usigneret |  Int16 | 
| tinyint | Int16 |
| tinytext | Streng | 
| varchar | Streng | 
| år | Heltal | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

