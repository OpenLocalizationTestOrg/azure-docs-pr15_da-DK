<properties 
    pageTitle="Flytte data fra Cassandra ved hjælp af Data Factory | Microsoft Azure" 
    description="Få mere at vide om, hvordan til at flytte data fra en lokal Cassandra-database ved hjælp af Azure Data Factory." 
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
    ms.date="09/07/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Flytte data fra en lokal Cassandra-database ved hjælp af Azure Data Factory
I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik for at kopiere data fra en lokal Cassandra-database til en hvilken som helst datalager, der er angivet under Sink kolonne i sektionen [understøttede datakilder og dræn](data-factory-data-movement-activities.md#supported-data-stores) . I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

Data factory understøtter i øjeblikket kun flytte data fra en Cassandra database til [understøttes sink data butikker](data-factory-data-movement-activities.md#supported-data-stores), men ikke flytte data fra andre data butikker til en Cassandra database.

## <a name="prerequisites"></a>Forudsætninger
Til Azure Data Factory-tjenesten skal kunne oprette forbindelse til din lokale Cassandra database, skal du installere følgende: 

- Datastyringsgateway 2.0 eller nyere på den samme computer, der er vært databasen eller på en anden computer for at undgå konkurrere for ressourcer med databasen. Datastyringsgateway er en software, der forbinder lokale datakilder til skytjenester på en sikker og administrerede måde. Se [flytte data mellem i det lokale miljø og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway.
  
    Når du installerer gatewayen, installeres automatisk en Microsoft Cassandra ODBC-driver, der bruges til at oprette forbindelse til Cassandra database. 

> [AZURE.NOTE] Se [fejlfinding i forbindelse med gateway problemer](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) tip til fejlfinding i forbindelse med forbindelse/gateway problemer. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der kopierer data fra en Cassandra database til en af de understøttede sink data butikker er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra Cassandra database til Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.   


## <a name="sample-copy-data-from-cassandra-to-blob"></a>Eksempel: Kopiere data fra Cassandra til Blob
Eksemplet kopierer data fra en Cassandra database til en Azure blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. Data kan kopieres direkte til en af de dræn, der er angivet i artiklen [Data bevægelse aktiviteter](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet i Azure Data Factory. 

- En sammenkædet tjeneste af typen [OnPremisesCassandra](#onpremisescassandra-linked-service-properties).
- En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- En input [datasæt](data-factory-create-datasets.md) af typen [CassandraTable](#cassandratable-properties).
- En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [CassandraSource](#cassandrasource-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

**Cassandra sammenkædet service**

I dette eksempel bruger tjenesten **Cassandra** sammenkædet. Se [Cassandra sammenkædet service](#onpremisescassandra-linked-service-properties) afsnittet for de egenskaber, der understøttes af denne sammenkædede tjeneste.  

    {
        "name": "CassandraLinkedService",
        "properties":
        {
            "type": "OnPremisesCassandra",
            "typeProperties":
            {
                "authenticationType": "Basic",
                "host": "mycassandraserver",
                "port": 9042,
                "username": "user",
                "password": "password",
                "gatewayName": "mygateway"
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

**Cassandra input datasæt**

    {
        "name": "CassandraInput",
        "properties": {
            "linkedServiceName": "CassandraLinkedService",
            "type": "CassandraTable",
            "typeProperties": {
                "tableName": "mytable",
                "keySpace": "mykeyspace" 
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

Angive **eksterne** til **Sand** oplyser tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.

**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). 

    {
        "name": "AzureBlobOutput",
        "properties":
        {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties":
            {
                "folderPath": "adfgetstarted/fromcassandra"
            },
            "availability":
            {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Rørledning med kopi aktiviteter**

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **CassandraSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. 

Se [Egenskaber for RelationalSource](#cassandrasource-type-properties) til listen over egenskaber, der understøttes af RelationalSource. 
    
    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2016-06-01T18:00:00",
            "end":"2016-06-01T19:00:00",
            "description":"pipeline with copy activity",
            "activities":[  
            {
                "name": "CassandraToAzureBlob",
                "description": "Copy from Cassandra to an Azure blob",
                "type": "Copy",
                "inputs": [
                {
                    "name": "CassandraInput"
                }
                ],
                "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
                ],
                "typeProperties": {
                    "source": {
                        "type": "CassandraSource",
                        "query": "select id, firstname, lastname from mykeyspace.mytable"
        
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
## <a name="onpremisescassandra-linked-service-properties"></a>Egenskaber for OnPremisesCassandra sammenkædet tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for Cassandra sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **OnPremisesCassandra** | Ja |
| Host | En eller flere IP-adresser eller host navnene på Cassandra servere.<br/><br/>Angive en kommasepareret liste over IP-adresser eller host navne for at oprette forbindelse til alle servere samtidigt. | Ja | 
| port | Den TCP-port, Cassandra-serveren bruger lytte til klientforbindelser. | Nej, standardværdi: 9042 |
| authenticationType | Grundlæggende og anonym | Ja |
| brugernavn |Angiv brugernavnet for brugerkontoen. | Ja, hvis authenticationType er indstillet til Basic. |
| adgangskode | Angiv adgangskoden for kontoen.  | Ja, hvis authenticationType er indstillet til Basic. |
| gatewayName | Navnet på den gateway, der bruges til at oprette forbindelse til den lokale Cassandra database. | Ja |
| encryptedCredential | Legitimationsoplysninger, der er krypteret af gatewayen. | Nej | 

## <a name="cassandratable-properties"></a>CassandraTable egenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **CassandraTable** har følgende egenskaber

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| keyspace | Navnet på det keyspace eller skema i Cassandra database. | Ja (Hvis **forespørgslen** til **CassandraSource** ikke er defineret). |
| Tabelnavn | Navnet på tabellen i Cassandra database. | Ja (Hvis **forespørgslen** til **CassandraSource** ikke er defineret). |


## <a name="cassandrasource-type-properties"></a>Egenskaber for CassandraSource
En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når kilde er af typen **CassandraSource**, er følgende egenskaber tilgængelige i typeProperties afsnit:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-92 forespørgsel eller CQL forespørgsel. Se [reference til CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Når du bruger SQL-forespørgsel, kan du angive **keyspace name.table navn** for at repræsentere den tabel, du vil forespørge. | Vælg Nej (hvis tabelnavn og keyspace på datasættet defineres).  |
| consistencyLevel | Niveauet konsistens angiver, hvor mange replikaer skal svare en kvittering for læsning, før der returneres dataene til klientprogrammet. Cassandra kontrollerer det angivne antal replikaer med data til at udføre Læs anmodningen. | EN, TO, TRE, QUORUM, ALLE LOCAL_QUORUM EACH_QUORUM, LOCAL_ONE. Du kan finde oplysninger i [konfiguration af dataenes konsistens](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) . | Nej. Standardværdien er 1. |  


### <a name="type-mapping-for-cassandra"></a>Tilknytning til Cassandra
Cassandra Type | .NET baseret Type
--------------- | ---------------
ASCII | Streng 
BIGINT | Int64
BLOB | Byte]
BOOLESK VÆRDI | Boolesk værdi
DECIMAL | Decimal
DOBBELT | Dobbelt
FLYDENDE | Enkelt
INET | Streng
HELTAL | Int32
TEKST | Streng
TIDSSTEMPEL | Dato og klokkeslæt
TIMEUUID | GUID
UUID | GUID
VARCHAR | Streng
VARINT | Decimal

> [AZURE.NOTE]  
> Typer (kort, sæt, liste, osv.), se [arbejde med Cassandra samlingstyper ved hjælp af virtuelle tabel](#work-with-collections-using-virtual-table) sektion for af websteder. 
> 
> Brugerdefinerede datatyper understøttes ikke.
> 
> Længden af binær kolonne og strengkolonne længde må ikke være større end 4000. 

## <a name="work-with-collections-using-virtual-table"></a>Arbejde med af websteder ved hjælp af virtuelle tabel
Azure Data Factory bruger en indbygget ODBC-driver til at oprette forbindelse til, og Kopiér data fra databasen Cassandra. Til samling former, herunder kort, konfigurere og liste, driveren igen skal normalisere dataene til tilsvarende virtuelle tabeller. Hvis en tabel indeholder en samling kolonner, opretter driveren specifikt følgende virtuelle tabeller:

-   En **grundlæggende tabel**, som indeholder de samme data, som de reelle tabel med undtagelse af kolonnerne af websteder. Basistabellen bruger det samme navn som den reelle tabel, som den repræsenterer.
-   En **virtuel tabel** for hver samling kolonne, der udvider den indlejrede data. Virtuelle tabeller, der repræsenterer samlinger navngives med navnet på tabellen reelt tal, en separator "_vt_" og navnet på kolonnen.

Virtuelle tabeller referere til data i tabellen reelt tal, så driveren til at få adgang til ikke-normaliserede dataene. Se eksempel afsnittet Yderligere oplysninger. Du kan få adgang til indholdet af Cassandra af websteder ved at forespørge og deltagelse i virtuelle tabeller.

Du kan udnytte [Kopi guiden](data-factory-data-movement-activities.md#data-factory-copy-wizard) for at se intuitivt på listen over tabeller i Cassandra database, herunder virtuelle tabeller og få vist et eksempel af data i. Du kan også oprette en forespørgsel i guiden Kopier og validere for at se resultatet.

### <a name="example"></a>Eksempel
Den følgende "ExampleTable" er for eksempel en Cassandra databasetabel, der indeholder et heltalskolonne for primær nøgle med navnet "pk_int", en kolonne med navnet værdi, en listekolonne, en kort kolonne og en kolonne (kaldet "StringSet").

pk_int | Værdi | Liste | Kort |   StringSet
------ | ----- | ---- | --- | --------
1 | "eksempel værdien 1" | ["1", "2", "3"]  | {"S1": "a", "S2": "b"} | {"A", "B", "C"}
3 | "eksempelværdi 3" | ["100", "101", "102", "105"] | {"S1": "t"} | {"A", "E"}

Driveren vil generere flere virtuelle tabeller for at repræsentere denne enkelt tabel. Fremmed nøgle kolonnerne i virtuelle tabeller referere til de primære nøgle kolonner i tabellen reelt tal, og angive, hvilke reelle tabelrække virtuelle tabelrækken svarer til. 

Den første virtuelle tabel er basistabellen med navnet "ExampleTable" vises i den følgende tabel. Basistabellen indeholder de samme data som den oprindelige databasetabel med undtagelse af samlinger, som er udeladt i denne tabel, og der er udvidet i andre virtuelle tabeller.

pk_int | Værdi
------ | -----
1 | "eksempel værdien 1"
3 | "eksempelværdi 3"

De følgende tabeller viser virtuelle tabeller, der renormalize dataene fra de liste, kort og StringSet kolonner. Kolonner med navne, der slutter med "_index" eller "_key" Angiver placeringen af data i den oprindelige liste eller et kort. Kolonner med navne, der slutter med "_value" indeholder de data, der er udvidet fra samlingen.

#### <a name="table-exampletablevtlist"></a>Tabel "ExampleTable_vt_List":
pk_int | List_index | List_value
------ | ---------- | ----------
1 | 0 | 1
1 | 1 | 2
1 | 2 | 3
3 | 0 | 100
3 | 1 | 101
3 | 2 | 102
3 | 3 | 103

#### <a name="table-exampletablevtmap"></a>Tabel "ExampleTable_vt_Map":
pk_int | Map_key | Map_value
------ | ------- | ---------
1 | S1 | A
1 | S2 | b
3 | S1 | t

#### <a name="table-exampletablevtstringset"></a>Tabel "ExampleTable_vt_StringSet":
pk_int | StringSet_value
------ | ---------------
1 | A
1 | B
1 | C
3 | A
3 | E





[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.
