<properties 
    pageTitle="Flytte data fra FTP-server | Microsoft Azure" 
    description="Få mere at vide om, hvordan til at flytte data fra en FTP-server ved hjælp af Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/20/2016" 
    ms.author="spelluru"/>

# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Flytte data fra en FTP-server ved hjælp af Azure Data Factory
I denne artikel beskrives, hvordan du bruger den kopi aktivitet i Azure Data Factory til at flytte data fra en FTP-server til en understøttet sink datalager. I denne artikel bygger på artiklen [data bevægelse aktiviteter](data-factory-data-movement-activities.md) , der indeholder en generel oversigt over flytning af data med kopi aktivitet og på listen over data butikker understøttes som kilder/dræn. 

Data factory understøtter i øjeblikket kun flytte data fra en FTP-server til andre data butikker, men ikke til at flytte data fra andre data butikker til en FTP-server. Understøtter både lokalt og FTP-servere i skyen. 

Hvis du vil flytte data fra en **lokal** FTP-server, til en skybaseret datalager (eksempel: Azure Blob-lager), installere og bruge Datastyringsgateway. Datastyringsgateway er en agent for klient, der er installeret på din lokale computer, som giver mulighed for skytjenester for at oprette forbindelse til lokal ressource. Du kan finde oplysninger på gatewayen i [Datastyringsgateway](data-factory-data-management-gateway.md) . Se [flytte data mellem lokale steder og skyen](data-factory-move-data-between-onprem-and-cloud.md) artikel til en trinvis vejledning om konfiguration af gatewayen og bruge den. Du kan bruge gatewayen til at oprette forbindelse til en FTP-server, selv hvis serveren er på en Azure IaaS virtuel maskine (VM). 

Du kan installere gatewayen på den samme lokale computer eller Azure IaaS VM som FTP-serveren. Dog anbefaler vi, at du installerer gatewayen på en anden computer eller en separat Azure IaaS VM for at undgå ressourcekonflikter og for at forbedre ydeevnen. Når du installerer gatewayen på en anden computer, skal computeren være adgang til FTP-serveren. 

## <a name="copy-data-wizard"></a>Kopiere guiden data
Den nemmeste måde at oprette en rørledning, der blev kopieret data fra en FTP-server er at bruge guiden kopi. Se [Selvstudium: oprette en kopi guiden pipeline](data-factory-copy-data-wizard-tutorial.md) for en hurtig gennemgang på at oprette en rørledning, ved hjælp af guiden Kopier data. 

I følgende eksempler giver eksempel JSON definitioner, som du kan bruge til at oprette en rørledning med [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) eller [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) eller [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). 

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Eksempel: Kopiere data fra FTP-server til Azure blob

Dette eksempel viser, hvordan du kopierer data fra en FTP-server til en Azure Blob-lager. Data kan dog kopierede **direkte** til en af de dræn angivet [her](data-factory-data-movement-activities.md#supported-data-stores) ved hjælp af den kopi aktivitet på Azure Data fabrik.  
 
Stikprøvernes har følgende data factory objekter:

- En sammenkædet tjeneste af typen [FtpServer](#ftp-linked-service-properties).
- En sammenkædet tjeneste af typen [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- En input [datasæt](data-factory-create-datasets.md) af typen [filshare, som er](#fileshare-dataset-type-properties).
- En output [datasæt](data-factory-create-datasets.md) af typen [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- En [rørledning](data-factory-create-pipelines.md) med kopi aktiviteter, der bruger [FileSystemSource](#ftp-copy-activity-type-properties) og [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Eksemplet kopierer data fra en FTP-server til en Azure blob hver time. De JSON egenskaber, der bruges i disse eksempler er beskrevet i følge eksemplerne sektioner. 

**FTP sammenkædet service** I dette eksempel bruges den basisgodkendelse med brugernavn og din adgangskode i almindelig tekst. Du kan også bruge en af følgende måder: 

- Anonym godkendelse 
- Basisgodkendelse med krypterede legitimationsoplysninger
- FTP-over SSL/TLS (FTPS)

Se [FTP sammenkædet service](#ftp-linked-service-properties) afsnittet for forskellige typer godkendelse, kan du bruge. 

    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",          
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
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

**FTP-input datasæt** Dette dataset refererer til mappen FTP- `mysharedfolder` og `test.csv`. Pipeline kopierer filen til destinationen. 

Angive "eksterne": "true" informerer tjenesten Data Factory, datasættet er ekstern i forhold til data fabrik og er ikke oprettet med en aktivitet i data factory.
    
    {
      "name": "FTPFileInput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
          "folderPath": "mysharedfolder",
          "fileName": "test.csv",
          "useBinaryTransfer": true
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Azure Blob output datasæt**

Data skrives til en ny blob hver time (frekvens: time, interval: 1). Stien til mappen for blob evalueres dynamisk baseret på starttidspunktet for det udsnit, der behandles. Stien til mappen bruger år, måned, dag og timer dele af starttidspunktet.

    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Pipeline indeholder en kopi aktivitet, der er konfigureret til at bruge input- og outputområder datasæt og er planlagt til at køre hver time. **Kildetypen** er indstillet til **FileSystemSource** i pipeline JSON definition, og **sink** type er indstillet til **BlobSink**. 
    
    {
        "name": "pipeline",
        "properties": {
            "activities": [{
                "name": "FTPToBlobCopy",
                "inputs": [{
                    "name": "FtpFileInput"
                }],
                "outputs": [{
                    "name": "AzureBlobOutput"
                }],
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "FileSystemSource"
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
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }],
            "start": "2016-08-24T18:00:00Z",
            "end": "2016-08-24T19:00:00Z"
        }
    }

## <a name="ftp-linked-service-properties"></a>Egenskaber for FTP-sammenkædede tjenesten

Den følgende tabel indeholder en beskrivelse til JSON-elementer, der er specifikke for FTP-sammenkædede tjeneste.

| Egenskaben | Beskrivelse | Påkrævet | Standard |
| -------- | ----------- | -------- | ------- | 
| type | Typeegenskaben skal angives til FtpServer | Ja | &nbsp;
| Host | Navn eller IP-adressen på den FTP-Server | Ja | &nbsp;
| authenticationType | Angive godkendelsestype | Ja | Grundlæggende og anonym |
| brugernavn | Bruger, der har adgang til FTP-serveren | Nej | &nbsp;
| adgangskode | Adgangskode til brugeren (brugernavn) | Nej | &nbsp;
| encryptedCredential | Krypteret legitimationsoplysninger for at få adgang til FTP-serveren | Nej | &nbsp;
| gatewayName | Navnet på gatewayen Datastyringsgateway til at oprette forbindelse til en lokal FTP-server | Nej    | &nbsp;
| port | Port, hvor FTP-serveren lytter | Nej | 21 |
| enableSsl | Angiv, om du vil bruge FTP over SSL/TLS-kanal | Nej | SAND | 
| enableServerCertificateValidation | Angiv, om du vil aktivere server SSL-certifikat datavalidering, når du bruger FTP over SSL/TLS-kanal | Nej | SAND | 

### <a name="using-anonymous-authentication"></a>Ved hjælp af anonym godkendelse

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {     
                "authenticationType": "Anonymous",
                "host": "myftpserver.com"
            }
        }
    }

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Brug af brugernavn og din adgangskode i almindelig tekst til basisgodkendelse
    
    {
        "name": "FTPLinkedService",
        "properties": {
        "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "username": "Admin",
                "password": "123456"
            }
        }
    }


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Brug af port, enableSsl, enableServerCertificateValidation

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",  
                "username": "Admin",
                "password": "123456",
                "port": "21",
                "enableSsl": true,
                "enableServerCertificateValidation": true
            }
        }
    }

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Bruge encryptedCredential til godkendelse og gateway

    {
        "name": "FTPLinkedService",
        "properties": {
            "type": "FtpServer",
            "typeProperties": {
                "host": "myftpserver.com",
                "authenticationType": "Basic",
                "encryptedCredential": "xxxxxxxxxxxxxxxxx",
                "gatewayName": "mygateway"
            }
        }
    }

Se [angive legitimationsoplysningerne og sikkerhed](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) for at få oplysninger om at angive legitimationsoplysninger for en lokal FTP-datakilde.

[AZURE.INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]   
[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]   
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Egenskaber for FTP-kopien aktivitet

En komplet liste over sektioner og egenskaber, der er tilgængelige til at definere aktiviteter, findes i artiklen [Oprette rørledninger](data-factory-create-pipelines.md) . Egenskaber som navn, beskrivelse, input og output tabeller og politikker er tilgængelige for alle typer aktiviteter. 

Tilgængelige egenskaber i sektionen typeProperties aktivitetens afhænger på den anden side af hver aktivitetstype. For kopi aktivitet varierer egenskaberne type afhængigt af typerne datakilder og dræn.

[AZURE.INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]   

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Ydeevne og tilpasning  
Se [kopi aktivitet ydeevne og justering af vejledning](data-factory-copy-activity-performance.md) til at få mere at vide om vigtige faktorer, der virkning ydeevnen for flytning af data (kopi aktivitet) i Azure Data Factory og forskellige måder at optimere den.

## <a name="next-steps"></a>Næste trin
Se følgende artikler: 

- [Kopiér aktivitet selvstudium](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finde trinvise instruktioner til oprettelse af en rørledning med en kopi aktivitet. 
