<properties 
    pageTitle="Flytte data fra lokale HDFS | Azure Data Factory" 
    description="Få mere at vide om, hvordan til at flytte data fra lokale HDFS ved hjælp af Azure Data Factory." 
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

# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Flytte data fra lokale HDFS ved hjælp af Azure Data Factory
I denne artikel beskrives, hvordan du kan bruge den kopi aktivitet på en Azure data fabrik til at flytte data fra en lokal HDFS til en anden datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , der indeholder en generel oversigt over flytning af data med kopi aktivitet og understøttede data store kombinationer.

Data factory understøtter i øjeblikket kun flytte data fra en lokal HDFS til andre data butikker, men ikke til at flytte data fra andre data butikker til en lokal HDFS.


## <a name="enabling-connectivity"></a>Aktivere connectivity
Factory datatjeneste understøtter oprettelse af forbindelse til lokale HDFS ved hjælp af Datastyringsgateway. Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel for at få mere at vide om Datastyringsgateway og trinvis vejledning til konfiguration af gatewayen. Brug gatewayen til at oprette forbindelse til HDFS, selvom det er placeret i en Azure IaaS VM. 

Mens du kan installere gatewayen på den samme lokale computer eller Azure VM som HDFS, anbefaler vi, at du installerer gatewayen på en separat maskine/Azure IaaS VM. Har du gateway på en anden computer reducerer ressourcekonflikter og forbedrer ydeevnen. Når du installerer gatewayen på en anden computer, skal computeren være adgang til computeren med HDFS. 


## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der blev kopieret data fra lokale HDFS er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempler giver eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). De viser, hvordan du kopierer data fra en lokal HDFS til en Azure Blob-lager. Data kan dog kopieres til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Eksempel: Kopiere data fra lokale HDFS til Azure Blob

Dette eksempel viser, hvordan du kopierer data fra en lokal HDFS til Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

1.  En sammenkædet tjeneste af typen [OnPremisesHdfs](#hdfs-linked-service-properties).
2.  En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.  En input [datasæt](data-factory-create-datasets.md) af typen [filshare, som er](#hdfs-dataset-type-properties).
4.  En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [FileSystemSource](#hdfs-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra en lokal HDFS til en Azure blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

Konfigurere datastyringsgateway som et første trin. Vejledningen i artiklen [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) . 

**HDFS sammenkædet service** I dette eksempel bruges Windows-godkendelse. Se [HDFS sammenkædet service](#hdfs-linked-service-properties) afsnittet for forskellige typer godkendelse, kan du bruge. 

    {
        "name": "HDFSLinkedService",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
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

**HDFS input datasæt** Dette dataset refererer til mappen HDFS DataTransfer/UnitTest /. Pipeline kopierer alle filerne i denne mappe til destinationen. 

Angive "eksterne": "true" informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.
    
    {
        "name": "InputDataset",
        "properties": {
            "type": "FileShare",
            "linkedServiceName": "HDFSLinkedService",
            "typeProperties": {
                "folderPath": "DataTransfer/UnitTest/"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval":  1
            }
        }
    }




**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet.

    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge disse input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **FileSystemSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. SQL-forespørgsel, der er angivet for egenskaben **forespørgsel** markerer dataene i den seneste time kopiere.
    
    {
        "name": "pipeline",
        "properties":
        {
            "activities":
            [
                {
                    "name": "HdfsToBlobCopy",
                    "inputs": [ {"name": "InputDataset"} ],
                    "outputs": [ {"name": "OutputDataset"} ],
                    "type": "Copy",
                    "typeProperties":
                    {
                        "source":
                        {
                            "type": "FileSystemSource"
                        },
                        "sink":
                        {
                            "type": "BlobSink"
                        }
                    },
                    "policy":
                    {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1,
                        "timeout": "00:05:00"
                    }
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="hdfs-linked-service-properties"></a>Egenskaber for HDFS sammenkædede tjenesten

Den følgende tabel indeholder beskrivelse til JSON-elementer, der er specifikke for HDFS sammenkædet tjeneste.

| Egenskaben | Beskrivelse | Påkrævet |
| -------- | ----------- | -------- | 
| type | Typeegenskaben skal være angivet til: **Hdfs** | Ja | 
| URL-adresse | URL-adressen til HDFS | Ja |
| encryptedCredential | [Ny AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) output fra access-legitimationsoplysninger. | Nej |
| Brugernavn | Brugernavn til Windows-godkendelse. | Ja (til Windows-godkendelse)
| adgangskode | Adgangskode til Windows-godkendelse. | Ja (til Windows-godkendelse)
| authenticationType | Windows, eller anonyme. | Ja |
| gatewayName | Navnet på gatewayen, tjenesten Data Factory skal bruge til at oprette forbindelse til HDFS. | Ja |   

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for lokale HDFS.

### <a name="using-anonymous-authentication"></a>Ved hjælp af anonym godkendelse

    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Anonymous",
                "userName": "hadoop",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }


### <a name="using-windows-authentication"></a>Ved hjælp af Windows-godkendelse
    
    {
        "name": "hdfs",
        "properties":
        {
            "type": "Hdfs",
            "typeProperties":
            {
                "authenticationType": "Windows",
                "userName": "Administrator",
                "password": "password",
                "url" : "http://<machine>:50070/webhdfs/v1/",
                "gatewayName": "mygateway"
            }
        }
    }
 


## <a name="hdfs-dataset-type-properties"></a>Egenskaber for HDFS datasæt

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere datasæt, findes i artiklen [oprette datasæt](data-factory-create-datasets.md) . Afsnit som struktur, tilgængelighed og politik for et datasæt JSON er de samme for alle datasæt typer (Azure SQL Azure blob, Azure table osv.).

Afsnittet **typeProperties** er forskellige for hver type datasæt og indeholder oplysninger om placeringen af data i lageret med data. Sektionen typeProperties for dataset af typen **filshare, som er** (som indeholder HDFS datasæt) har følgende egenskaber

Egenskaben | Beskrivelse | Påkrævet
-------- | ----------- | --------
Mappesti | Stien til mappen. Eksempel:`myfolder`<br/><br/>Brug escape-tegn ' \ ' for specialtegn i strengen. For eksempel: angive mappe til folder\subfolder,\\\\undermappe og for d:\samplefolder, angive d:\\\\EksempelMappe.<br/><br/>Du kan kombinere denne egenskab med **partitionBy** have mappesti, der er baseret på udsnit start/slut dato-klokkeslæt. | Ja
Filnavn | Angive navnet på filen i **mappesti** , hvis du vil tabellen til at referere til en bestemt fil i mappen. Hvis du ikke angiver en værdi for denne egenskab, tabellen, der peger på alle filer i mappen.<br/><br/>Når filnavn ikke er angivet for en output datasæt, der ville være navnet på den oprettede fil i følgende dette format: <br/><br/>Data. <Guid>.txt (for eksempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | Nej
partitionedBy | partitionedBy kan bruges til at angive en dynamisk mappesti, filnavn for klokkeslætsdata serie. Eksempel: mappesti parameteriseret for hver time af data. | Nej
fileFilter | Angiv et filter, der bruges til at vælge et undersæt af filer i mappesti i stedet for alle filer. <br/><br/>Tilladte værdier er: `*` (flere tegn) og `?` (enkelt tegn).<br/><br/>Eksempel 1:`"fileFilter": "*.log"`<br/>Eksempel 2:`"fileFilter": 2014-1-?.txt"`<br/><br/>**Bemærk**: fileFilter er tilgængelig for et input filshare, som er datasæt | Nej
| Formatér | Følgende formattyper understøttes: **tekstformat**, **AvroFormat**, **JsonFormat**, **OrcFormat**og **ParquetFormat**. Angiv egenskaben **type** under formater til en af følgende værdier. Se [Angive tekstformat](#specifying-textformat), [Der angiver AvroFormat](#specifying-avroformat), [Angive JsonFormat](#specifying-jsonformat), [Angive OrcFormat](#specifying-orcformat)og [Angive ParquetFormat](#specifying-parquetformat) sektioner få mere at vide. Hvis du vil kopiere filer som-er mellem fil-baserede lagre (binære kopi), kan du springe afsnittet format i begge input- og outputområder datasæt definitioner. | Nej 
| komprimering | Angiv den type og komprimering for dataene. Understøttede datatyper er: **GZip**, **Deflate**og **BZip2** og understøttede niveauer: **Optimal** og **hurtigst**. Komprimeringsindstillinger understøttes i øjeblikket ikke data i **AvroFormat** eller **OrcFormat**. Yderligere oplysninger, se [komprimering support](#compression-support) afsnittet.  | Nej |



> [AZURE.NOTE] filnavnet og fileFilter kan ikke bruges samtidigt.


### <a name="using-partionedby-property"></a>Ved hjælp af partionedBy egenskab

Som nævnt i forrige afsnit, kan du angive en dynamisk mappesti, filnavn for serien klokkeslætsdata med partitionedBy. Du kan gøre det med Data Factory-makroer og systemvariablen SliceStart, SliceEnd, der angiver den logiske periode for en given data udsnit. 

Se [Oprette datasæt](data-factory-create-datasets.md), [planlægning & udførelse](data-factory-scheduling-and-execution.md)og [Oprette rørledninger](data-factory-create-pipelines.md) artikler for at få mere for at vide om tid serie datasæt, planlægning og udsnit. 

#### <a name="sample-1"></a>Eksempel 1:

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

I dette eksempel {udsnit} erstattes med den angivne værdi for Data Factory systemvariabel SliceStart i formatet (YYYYMMDDHH). SliceStart henviser til starttidspunktet for udsnittet. Mappesti er forskellig for hvert udsnit. For eksempel: wikidatagateway/wikisampledataout/2014100103 eller wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Eksempel 2:

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

I dette eksempel er år, måned, dag og klokkeslættet for SliceStart ud i separate variabler, der bruges af egenskaberne mappesti og filnavn.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Egenskaber for HDFS kopi aktivitet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. De varierer afhængigt af typerne datakilder og dræn for kopi aktivitet.

For kopi aktivitet, når kilde er af typen **FileSystemSource** er følgende egenskaber tilgængelige i typeProperties afsnit:

**FileSystemSource** understøtter følgende egenskaber:

| Egenskaben | Beskrivelse | Tilladte værdier | Påkrævet |
| -------- | ----------- | -------------- | -------- |
| rekursive | Angiver, om dataene er læst gælder fra de underordnede mapper eller kun fra den angivne mappe. | SAND, FALSK (standard)| Nej |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

