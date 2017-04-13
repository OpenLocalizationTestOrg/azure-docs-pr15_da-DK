<properties 
    pageTitle="Flytte data fra MongoDB ved hjælp af Data Factory | Microsoft Azure" 
    description="Få mere at vide om, hvordan til at flytte data fra MongoDB database ved hjælp af Azure Data Factory." 
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
    ms.date="08/04/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Flytte data fra MongoDB ved hjælp af Azure Data Factory

I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra en lokal MongoDB-database til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , som indeholder en generel oversigt over flytning af data med kopi aktivitet og kilde/sink data store kombinationer, der understøtter kopi aktiviteten.

Factory datatjeneste understøtter oprettelse af forbindelse til lokale MongoDB datakilder ved hjælp af Datastyringsgateway. Se [Datastyringsgateway](data-factory-data-management-gateway.md) artikel for at få mere at vide om Datastyringsgateway og [flytte data fra lokalt til skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få en trinvis vejledning om konfiguration af gatewayen en data pipeline til at flytte data. 

> [AZURE.NOTE] Du skal bruge gatewayen til at oprette forbindelse til MongoDB, selvom de ligger i Azure IaaS FOS. Hvis du prøver at oprette forbindelse til en forekomst af MongoDB hostet i skyen, kan du også installere forekomsten gateway i IaaS VM.

Data Factory understøtter i øjeblikket kun flytte data fra MongoDB til andre data butikker, men ikke til at flytte data fra andre data butikker til MongoDB.

## <a name="prerequisites"></a>Forudsætninger
Til Azure Data Factory-tjenesten skal kunne oprette forbindelse til din lokale MongoDB database, skal du installere følgende komponenter: 

- Datastyringsgateway 2.0 eller nyere på den samme computer, der er vært databasen eller på en anden computer for at undgå konkurrere for ressourcer med databasen. Datastyringsgateway er en software, der forbinder lokale datakilder til skytjenester på en sikker og administrerede måde. Se [Datastyringsgateway](data-factory-data-management-gateway.md) artikel for at få mere at vide om Datastyringsgateway.
  
    Når du installerer gatewayen, installeres automatisk en Microsoft MongoDB ODBC-driver, der bruges til at oprette forbindelse til MongoDB. 

## <a name="copy-data-wizard"></a>Kopiere guiden Data
Den nemmeste måde at oprette en rørledning, der kopierer data fra en MongoDB database til en af de understøttede sink data butikker er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempel indeholder eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra MongoDB database til Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.

## <a name="sample-copy-data-from-mongodb-to-azure-blob"></a>Eksempel: Kopiere data fra MongoDB til Azure Blob
Dette eksempel vises, hvordan du kopierer data fra en lokal MongoDB-database til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesMongoDb](#linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [MongoDbCollection](#dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [MongoDbSource](#copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra et forespørgselsresultat i MongoDB database til en blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

Konfigurere datastyringsgateway ifølge instruktionerne i artiklen [Datastyringsgateway](data-factory-data-management-gateway.md) , som et første trin. 

**MongoDB sammenkædet service**

    { 
        "name": "OnPremisesMongoDbLinkedService", 
        "properties": 
        { 
            "type": "OnPremisesMongoDb", 
            "typeProperties": 
            { 
                "authenticationType": "<Basic or Anonymous>", 
                "server": "< The IP address or host name of the MongoDB server >",  
                "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>", 
                "username": "<username>", 
                "password": "<password>",
               "authSource": "< The database that you want to use to check your credentials for authentication. >",
                "databaseName": "<database name>",
                "gatewayName": "<mygateway>"
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

**MongoDB input datasæt** Angive "eksterne": "true" informerer tjenesten Data Factory, tabellen er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.
    
    {
         "name":  "MongoDbInputDataset",
        "properties": { 
            "type": "MongoDbCollection", 
            "linkedServiceName": "OnPremisesMongoDbLinkedService", 
            "typeProperties": { 
                "collectionName": "<Collection name>"   
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
                "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                            "format": "%M"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%d"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "%H"
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

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge den ovenstående input og output datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **MongoDbSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer dataene i den seneste time kopiere.
    
    {
        "name": "CopyMongoDBToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "MongoDbSource",
                            "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MongoDbInputDataset"
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
                    "name": "MongoDBToAzureBlob"
                }
            ],
            "start": "2016-06-01T18:00:00Z",
            "end": "2016-06-01T19:00:00Z"
        }
    }


## <a name="linked-service-properties"></a>Egenskaber for sammenkædede tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for **OnPremisesMongoDB** sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **OnPremisesMongoDb** | Ja |
| Server | IP-adresse eller værtsnavn navnet på serveren, MongoDB. | Ja | 
| port | TCP-port, MongoDB-serveren bruger lytte til klientforbindelser. | Valgfri, standardværdien: 27017 |
| authenticationType | Grundlæggende og anonym. | Ja | 
| brugernavn | Bruger for at få adgang til MongoDB. | Ja (hvis basisgodkendelse bruges).|
| adgangskode | Adgangskode til brugeren. |   Ja (hvis basisgodkendelse bruges). | 
| authSource | Navnet på den MongoDB database, du vil bruge til at kontrollere dine legitimationsoplysninger til godkendelse. | Valgfrit (hvis basisgodkendelse bruges). standard: bruger kontoen Administrator og den database, der er angivet ved hjælp af databasenavn egenskab. |  
| Databasenavn | Navnet på den MongoDB database, du vil have adgang til. | Ja |
| gatewayName | Navnet på den gateway, der har adgang til datalageret. | Ja | 
| encryptedCredential | Legitimationsoplysninger, der er krypteret af gatewayen. | Valgfri |


Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal MongoDB datakilde.

## <a name="dataset-type-properties"></a>Egenskaber for datasættet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **MongoDbCollection** har følgende egenskaber:

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- |
| collectionName | Navnet på samlingen i MongoDB database. | Ja | 

## <a name="copy-activity-type-properties"></a>Kopiere aktivitet typeegenskaber

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politik er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen **typeProperties** aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

Når kilden er af typen **MongoDbSource** findes følgende egenskaber i typeProperties afsnit:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| forespørgsel | Brug den brugerdefinerede forespørgsel til at læse data. | SQL-92 forespørgselsstreng. For eksempel: Vælg * fra MinTabel. | Ingen (Hvis der er angivet **collectionName** af **datasæt** ) | 

## <a name="schema-by-data-factory"></a>Skema af Data Factory
Azure Data Factory-tjenesten indstiller skemaet fra en MongoDB af websteder ved hjælp af de nyeste 100 dokumenter i gruppen af websteder. Hvis disse 100 dokumenter ikke indeholder fuldstændigt skema, kan nogle kolonner er ignoreres under kopieringen. 

## <a name="type-mapping-for-mongodb"></a>Tilknytning til MongoDB

Som nævnt i artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , udfører kopi aktivitet automatisk typekonverteringer fra kildetyper skal synkronisere datatyper med den følgende fremgangsmåde i trin 2:

1. Konvertere fra oprindelige kildetyper til .NET type
2. Konvertere fra .NET type til oprindelige sink type

Når du flytter data til MongoDB anvendes følgende tilknytninger fra MongoDB typer til .NET datatyper.

| MongoDB type | .NET framework type |
| ------------------- | ------------------- | 
| Binær | Byte] |
| Boolesk værdi | Boolesk værdi |
| Dato | Dato og klokkeslæt |
| NumberDouble | Dobbelt |
| NumberInt | Int32 |
| NumberLong | Int64 |
| ObjectID | Streng |
| Streng | Streng |
| UUID | GUID | 
| Objekt | Renormalized til udjævne kolonner med "_" som indlejrede separator |

> [AZURE.NOTE]  
> For at få mere for at vide om understøttelse af matrixer ved hjælp af virtuelle tabeller, du referere til [Support til komplekse typer ved hjælp af virtuelle tabeller](#support-for-complex-types-using-virtual-tables) nedenfor. 

I øjeblikket MongoDB følgende datatyper understøttes ikke: DBPointer, JavaScript, Maks/Min nøgle, regulære udtryk, Symbol, tidsstempel, Udefineret

## <a name="support-for-complex-types-using-virtual-tables"></a>Understøttelse af komplekse typer ved hjælp af virtuelle tabeller
Azure Data Factory bruger en indbygget ODBC-driver til at oprette forbindelse til, og Kopiér data fra databasen MongoDB. Til komplekse typer som matrixer eller objekter med forskellige typer på tværs af dokumenterne, driveren igen skal normalisere data i tilsvarende virtuelle tabeller. Hvis en tabel indeholder disse kolonner, opretter driveren specifikt følgende virtuelle tabeller:

-   En **grundlæggende tabel**, som indeholder de samme data, som de reelle tabel med undtagelse af kolonnerne kompleks type. Basistabellen bruger det samme navn som den reelle tabel, som den repræsenterer.
-   En **virtuel tabel** for hver kompleks type kolonne, der udvider den indlejrede data. Virtuelle tabeller navngives med navnet på tabellen reelt tal, en separator "_" og navnet på den matrix eller det objekt.

Virtuelle tabeller referere til data i tabellen reelt tal, så driveren til at få adgang til ikke-normaliserede dataene. Se eksempel afsnittet under detaljer. Du kan få adgang til indholdet af MongoDB matrixer ved at forespørge og deltagelse i virtuelle tabeller.

Du kan bruge [Guiden kopi](data-factory-data-movement-activities.md#data-factory-copy-wizard) til intuitivt få vist listen over tabeller i MongoDB database, herunder virtuelle tabeller og få vist et eksempel af data i. Du kan også oprette en forespørgsel i guiden Kopier og validere for at se resultatet.

### <a name="example"></a>Eksempel

"ExampleTable" under er for eksempel en MongoDB tabel, der indeholder en kolonne med en matrix af objekter i hver celle – fakturaer og én kolonne med en matrix med skalær typer – bedømmelser. 

_id | Kundenavn | Fakturaer | Tjenesten niveau | Bedømmelser
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice_id: "123" element: "toaster", pris: "456" rabat: "0,2 eller"}, {invoice_id: "124" element: "ovn", pris: "1235" DISKONTO: "0,2 eller"}] | Silver | [5,6]
2222 | XYZ | [{invoice_id: "135" element: "køleskabet", pris: "12543", rabat: "0,0"}] | Guld | [1,2]

Driveren vil generere flere virtuelle tabeller for at repræsentere denne enkelt tabel. Den første virtuelle tabel er basistabellen med navnet "ExampleTable", som er vist nedenfor. Basistabellen indeholder alle dataene på den oprindelige tabel, men dataene fra matrixer er udeladt og er udvidet i virtuelle tabeller.

_id | Kundenavn | Tjenesten niveau
--- | ------------- | -------------
1111 | ABC | Silver
2222 | XYZ | Guld

De følgende tabeller viser virtuelle tabeller, der repræsenterer de oprindelige matrixer i eksemplet. Disse tabeller indeholder følgende: 

- En reference tilbage til den oprindelige primære nøglekolonne, der svarer til rækken af den oprindelige matrix (via kolonnen _id)
- Angivelse af placeringen af data i den oprindelige matrix 
- Udvidet dataene for hvert element i matrixen

Tabel "ExampleTable_Invoices":

_id | ExampleTable_Invoices_dim1_idx | invoice_id | element | pris | Rabat
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | toaster | 456 | 0,2
1111 | 1 | 124 | ovn | 1235 | 0,2
2222 | 0 | 135 | køleskabet | 12543 | 0,0

Tabel "ExampleTable_Ratings":

_id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

## <a name="next-steps"></a>Næste trin
Se [flytte data mellem i det lokale miljø og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel finde trinvise instruktioner til oprettelse af en data rørledning, der flytter data fra et lokalt datalager til et Azure datalager. 

