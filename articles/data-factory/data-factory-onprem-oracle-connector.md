<properties 
    pageTitle="Flyt data til/fra Oracle ved hjælp af Data Factory | Microsoft Azure" 
    description="Lær at flytte data til/fra Oracle-database, der er i det lokale miljø ved hjælp af Azure Data Factory." 
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

# <a name="move-data-tofrom-on-premises-oracle-using-azure-data-factory"></a>Flytte data til/fra lokale Oracle ved hjælp af Azure Data Factory 

I denne artikel beskrives, hvordan du kan bruge data factory kopi aktivitet til at flytte data til/fra Oracle fra/til en anden data gemme. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

## <a name="installation"></a>Installation 
Til Azure Data Factory-tjenesten skal kunne oprette forbindelse til din lokale Oracle-database, skal du installere følgende komponenter: 

- Datastyringsgateway på den samme computer, der er vært databasen eller på en anden computer for at undgå konkurrere for ressourcer med databasen. Datastyringsgateway er en agent for klient, der forbinder lokale datakilder til skytjenester på en sikker og administrerede måde. Se [flytte data mellem i det lokale miljø og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway. 
- Oracle-dataprovider til .NET. Denne komponent er inkluderet i [Oracle Data Access komponenter til Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/). Installere den relevante version (32/64-bit) på værtsmaskinen hvor gatewayen er installeret. [Oracle Data udbyder .NET 12.1](http://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) kan få adgang til Oracle-Database 10 g Release 2 eller nyere.

    Hvis du vælger "XCopy Installation", skal du følge trinnene i vigtigt.htm. Vi anbefaler, at du vælger installer med Brugergrænsefladen (ikke-XCopy en). 
 
    Når du har installeret udbyderen, genstart tjenesten Data Management Gateway vært på computeren ved hjælp af tjenesterne applet (eller) Data Konfigurationsstyring til Datastyringsgateway.  

> [AZURE.NOTE] Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der kopierer data fra/til en Oracle-database til en af de understøttede sink data butikker er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra/til en Oracle-database til/fra Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.   

## <a name="sample-copy-data-from-oracle-to-azure-blob"></a>Eksempel: Kopiere data fra Oracle til Azure Blob
Dette eksempel viser, hvordan du kopierer data fra en lokal Oracle-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) som kilde- og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) som sink.

Eksemplet kopierer data fra en tabel i en lokal Oracle-database til en blob hver time. Du kan finde flere oplysninger om forskellige egenskaber, der bruges i stikprøven, dokumentation i sektioner, der følger eksemplerne.

**Oracle sammenkædet service:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob-lager sammenkædet service:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Oracle input datasæt:**

Eksemplet antages det, du har oprettet en tabel "MinTabel" i Oracle og den indeholder en kolonne med navnet "timestampcolumn" for klokkeslætsdata serie. 

Angive "eksterne": "true" informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

    {
        "name": "OracleInput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
               "external": true,
            "availability": {
                "offset": "01:00:00",
                "interval": "1",
                "anchorDateTime": "2014-02-27T12:00:00",
                "frequency": "Hour"
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

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Mappe stien og navnet på blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet.
    
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


**Rørledning med kopi aktiviteter:**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **OracleSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**.  SQL-forespørgslen angivet med **oracleReaderQuery** egenskaben markerer dataene i den seneste time kopiere.

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": " OracleInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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


Du har brug at justere forespørgselsstreng, der er baseret på, hvordan datoer er konfigureret i din Oracle-database. Hvis du får vist følgende fejlmeddelelse: 

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

Du skal muligvis ændre forespørgslen, som vist i følgende eksempel på (ved hjælp af funktionen to_date):

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\')  AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"

## <a name="sample-copy-data-from-azure-blob-to-oracle"></a>Eksempel: Kopiere data fra Azure Blob til Oracle
Dette eksempel viser, hvordan du kopierer data fra en Azure Blob-lager til en lokal Oracle-database. Data kan dog kopierede **direkte** fra enhver af de kilder angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
5.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) som datakilde [OracleSink](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) som sink.

Stikprøvernes kopierer data fra en blob til en tabel i en lokal Oracle-database i timen. Du kan finde flere oplysninger om forskellige egenskaber, der bruges i stikprøven, dokumentation i sektioner, der følger eksemplerne.

**Oracle sammenkædet service:**

    {
      "name": "OnPremisesOracleLinkedService",
      "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
          "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
          "gatewayName": "<gateway name>"
        }
      }
    }

**Azure Blob-lager sammenkædet service:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
        }
      }
    }

**Azure Blob input datasæt**

Data er valgte fra en ny blob hver time (frekvens: time, interval: 1). Mappe stien og navnet på blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned og dag en del af starttidspunktet og filnavn bruger time en del af starttidspunktet. "eksterne": "true" indstilling informerer tjenesten Data Factory, denne tabel er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

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

**Oracle output datasæt:**

Eksemplet antages, du har oprettet en tabel "MinTabel" i Oracle. Oprette tabellen i Oracle med det samme antal kolonner, som du forventer at Blob csv-filen indeholder. Nye rækker føjes til tabellen hver time.

    {
        "name": "OracleOutput",
        "properties": {
            "type": "OracleTable",
            "linkedServiceName": "OnPremisesOracleLinkedService",
            "typeProperties": {
                "tableName": "MyTable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": "1"
            }
        }
    }


**Rørledning med kopi aktiviteter:**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **BlobSource** i pipeline JSON definition, og **sink** typen er indstillet til **OracleSink**.  

    
    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "OracleOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "OracleSink"
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


## <a name="oracle-linked-service-properties"></a>Egenskaber for Oracle sammenkædet tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Oracle sammenkædet tjeneste. 

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
type | Typeegenskaben skal være angivet til: **OnPremisesOracle** | Ja
connectionString | Angive oplysninger, der er behov for at oprette forbindelse til Oracle-Database forekomst for egenskaben connectionString. | Ja 
gatewayName | Navnet på gatewayen, som bruges til at oprette forbindelse til den lokale Oracle-server | Ja

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal Oracle-datakilde.
## <a name="oracle-dataset-type-properties"></a>Egenskaber for Oracle-datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Oracle, Azure blob, Azure table osv.).
 
Afsnittet typeProperties er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for datasættet af typen OracleTable har følgende egenskaber:

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Tabelnavn | Navnet på tabellen i den Oracle-Database, som tjenesten sammenkædede henviser til. | Ingen (Hvis der er angivet **oracleReaderQuery** af **OracleSource** )

## <a name="oracle-copy-activity-type-properties"></a>Oracle kopi aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter. 

> [AZURE.NOTE]
Kopiér aktiviteten tager kun én input og producerer kun én output.

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

### <a name="oraclesource"></a>OracleSource
Kopiér aktivitet, når kilden er af typen **OracleSource** findes følgende egenskaber i **typeProperties** afsnit:

Egenskaben | Beskrivelse |Tilladte værdier | Påkrævet
-------- | ----------- | ------------- | --------
oracleReaderQuery | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-forespørgselsstreng. For eksempel: Vælg *fra MinTabel <br/> <br/>Hvis ikke angives, den SQL-sætning, der udføres: Vælg* fra MinTabel | Ingen (Hvis der er angivet **tabelnavn** af **datasæt** )

### <a name="oraclesink"></a>OracleSink
**OracleSink** understøtter følgende egenskaber:

Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet
-------- | ----------- | -------------- | --------
writeBatchTimeout | Ventetid for handlingen batchen Indsæt at fuldføre, før det udløber. | TimeSpan<br/><br/> Eksempel: 00:30:00 (30 minutter). | Nej
writeBatchSize | Indsætter data i en SQL-tabel, når størrelsen på bufferen writeBatchSize.   | Heltal (antallet af rækker)| Ikke (standard: 10000)  
sqlWriterCleanupScript | Angive en forespørgsel for kopi aktiviteten udføres så data fra et bestemt udsnit ryddes. | En forespørgselssætning. | Nej
sliceIdentifierColumnName | Angiv kolonnenavn for kopi aktivitet udfylde med automatisk genereret udsnit id, som bruges til at rydde op i data i et bestemt udsnit, når Kør igen. | Kolonnenavn for en kolonne med datatypen for binary(32). | Nej


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-oracle"></a>Tilknytning til Oracle

Som nævnt i udfører [data bevægelse aktiviteter](data-factory-data-movement-activities.md) artikel kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data fra Oracle, anvendes følgende tilknytninger fra Oracle-datatype til .NET type og omvendt.

Oracle-datatype | .NET framework-datatype
---------------- | ------------------------
BFILER | Byte]
BLOB | Byte]
TEGN | Streng
CLOB | Streng
DATO | Dato og klokkeslæt
FLYDENDE | Decimal
HELTAL | Decimal
INTERVAL ÅR TIL MÅNED | Int32
INTERVAL DAG TIL SEKUND | TimeSpan
LANG | Streng
LÆNGE RÅ | Byte]
NCHAR | Streng
NCLOB | Streng
TAL | Decimal
NVARCHAR2 | Streng
RÅ | Byte]
RÆKKE-ID | Streng
TIDSSTEMPEL | Dato og klokkeslæt
TIDSSTEMPEL MED LOKALE TIDSZONE | Dato og klokkeslæt
TIDSSTEMPEL MED TIDSZONE | Dato og klokkeslæt
HELTAL UDEN FORTEGN | Tal
VARCHAR2 | Streng
XML | Streng

## <a name="troubleshooting-tips"></a>Tip til fejlfinding

**Problem:** Du får vist følgende **fejlmeddelelse**: Kopiér aktivitet opfyldt ugyldige parametre: 'UnknownParameterName', detaljeret meddelelse: der blev ikke fundet den anmodede .net Framework Data-Provider. Det er muligvis ikke installeret".  

**Mulige årsager:**

1. .NET Framework Data-Provider til Oracle blev ikke installeret.
2. .NET Framework Data-Provider til Oracle er blevet installeret til .NET Framework 2.0 og findes ikke i mapperne .NET Framework 4.0. 

**Opløsning/løsning:**

1. Hvis du ikke har installeret .NET-Provider til Oracle, [installere det,](http://www.oracle.com/technetwork/topics/dotnet/downloads/) og prøv igen dette scenario. 
2. Hvis du får en fejlmeddelelse om selv når du har installeret udbyderen, kan du gøre følgende: 
    1. Åbn maskine config af .NET 2.0 fra mappen: <system disk>: \Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Søgning for **Oracle-dataprovider til .NET**, og du skal være i stand til at finde en post, som vist i det følgende eksempel unwn i følgende eksempel på FNder **system.data** -> **DbProviderFactories**:
            “<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />”
2.  Kopiér denne post til filen machine.config i mappen følgende v4.0: <system disk>: \Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config, og Skift version til 4.xxx.x.x.
3.  Installere "< ODP.NET installerede sti > \11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll" i den globale assemblycache (GAC) ved at køre `gacutil /i [provider path]`.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.
