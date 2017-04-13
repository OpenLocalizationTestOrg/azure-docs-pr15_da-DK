<properties
    pageTitle="Flytte data fra en lokal SQL Server til SQL Azure med Azure Data Factory | Azure"
    description="Konfigurere en ADF rørledning, der opbygger to data overførsel aktiviteter, der flytter sammen data dagligt mellem databaser lokalt og i skyen."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />


# <a name="move-data-from-an-on-premise-sql-server-to-sql-azure-with-azure-data-factory"></a>Flytte data fra en lokal SQL server til SQL Azure med Azure Data Factory

Dette emne viser, hvordan til at flytte data fra en lokal SQL Server-Database til en SQL Azure-Database via Azure Blob-lager ved hjælp af Azure Data Factory (ADF).

**Menuen** følgende links til emner, der beskriver, hvordan indtager data til målrette miljøer, hvor dataene kan gemmes og behandles under Team Data videnskabelige proces.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## <a name="intro"></a>Introduktion: Hvad er ADF, og hvornår skal det bruges til at overføre data?

Azure Data Factory er en fuldt administreret skybaseret data integration tjeneste, som orchestrates og automatisere flytning og transformation af data. Vigtige konceptet i ADF modellen er pipeline. En rørledning er en logisk gruppering af aktiviteter, hver især definerer handlingerne, der skal udføres på dataene i datasæt. Sammenkædede services bruges til at definere de oplysninger, der kræves at oprette forbindelse til dataressourcer Data Factory.

Med ADF, kan eksisterende data behandlingen af tjenesterne bestå i data rørledninger, der er meget tilgængelig og administrerede i skyen. Disse data rørledninger kan planlægges til at indtager, forberede, transformere, analysere og udgive data, og ADF administrerer og orchestrates komplekse data og behandling afhængigheder. Løsninger kan hurtigt indbygget og implementeret i skyen, forbinder et stigende antal i det lokale miljø og skyen datakilder.

Overvej at bruge ADF:

- Når data skal overføres kontinuerligt i et scenarie med hybrid, der har adgang til både lokalt og skyen ressourcer 
- Når dataene er overført eller skal ændres, eller har føjet til den, når du bliver overflyttet forretningslogik. 

ADF giver mulighed for planlægningen og overvågning af job ved hjælp af enkle JSON-scripts, som administrerer flytning af data med jævne mellemrum. ADF har også andre funktioner som understøttelse af komplekse handlinger. Du kan finde flere oplysninger om ADF, på [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).


## <a name="scenario"></a>Dette Scenario

Vi oprette en ADF rørledning, der opbygger to data overførsel aktiviteter. Sammen flytte data dagligt mellem en lokal SQL-database og Azure SQL-Database i skyen De to aktiviteter er:

* Kopiér data fra en lokal SQL Server-database til en Azure Blob-lager-konto
* kopiere data fra Azure Blob-lager-konto til en Azure SQL-Database.

>[AZURE.NOTE] De trin, der vises her er tilpasset fra mere detaljeret selvstudium leveres af ADF teamet: [flytte data mellem lokale datakilder og skyen med Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) referencer til de relevante afsnit i dette emne får, når det er relevant.


## <a name="prereqs"></a>Forudsætninger
Dette selvstudium forudsætter, at du har:

* En **Azure-abonnement**. Hvis du ikke har et abonnement, kan du tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure-lager-konto**. Du bruger en konto til Azure-lager til lagring af data i dette selvstudium. Hvis du ikke har en Azure-lager-konto, kan du se artiklen [Opret en lagerplads konto](storage-create-storage-account.md#create-a-storage-account) . Når du har oprettet kontoen lagerplads, skal du hente den kontonøgle, der bruges til at få adgang til opbevaring. Få [vist, Kopiér og Regenerer lagerplads adgangstaster](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Adgang til en **Azure SQL-Database**. Hvis du skal konfigurere Azure SQL-Database, indeholder tpoic [Introduktion til Microsoft Azure SQL Database](../sql-database/sql-database-get-started.md) oplysninger om, hvordan du klargør en ny forekomst af en Azure SQL-Database.
* Installeret og konfigureret **Azure PowerShell** lokalt. Finde en vejledning, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

> [AZURE.NOTE] Denne fremgangsmåde bruger [Azure-portalen](https://portal.azure.com/).


##<a name="upload-data"></a>Overføre dataene til din lokal SQL Server

Vi bruger [NYC Taxi datasæt](http://chriswhong.com/open-data/foil_nyc_taxi/) til at vise overførselsprocessen. NYC Taxi datasættet er også tilgængeligt som angivet i den valgte indlæg på Azure blob storage [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/). Dataene har to filer, filen trip_data.csv, som indeholder forretningsrejse detaljer, og filen trip_far.csv, som indeholder oplysninger om af prisen betalt for hver forretningsrejse. Et eksempel og en beskrivelse af disse filer er angivet i [NYC Taxi rejser datasæt beskrivelse](machine-learning-data-science-process-sql-walkthrough.md#dataset).


Du kan tilpasse fremgangsmåden her og et sæt af dine egne data eller Følg trinnene beskrevet ved hjælp af NYC Taxi datasættet. Hvis du vil overføre NYC Taxi datasæt til lokal SQL Server-databasen, skal du følge fremgangsmåden i [Flere importere Data til SQL Server-Database](machine-learning-data-science-process-sql-walkthrough.md#dbload). Disse instruktioner er til en SQL Server på en Azure Virtual Machine, men fremgangsmåden til overførsel til lokal SQL Server er den samme.


##<a name="create-adf"></a>Oprette en Azure Data Factory

[Opret en Azure Data Factory](../data-factory/data-factory-build-your-first-pipeline-using-editor.md#step-1-creating-the-data-factory)findes instruktioner til at oprette en ny Azure Data fabrik og en ressourcegruppe i [Azure-portalen](https://portal.azure.com/) . Navnet ny ADF forekomst *adfdsp* og ressource gruppe oprettet *adfdsprg*.


## <a name="install-and-configure-up-the-data-management-gateway"></a>Installation og konfiguration af Datastyringsgateway

Hvis du vil aktivere din rørledninger i en Azure data factory til at fungere sammen med en lokal SQL Server, skal du føje den til fabriksindstillingerne data som en sammenkædet tjeneste. Hvis du vil oprette en sammenkædet tjeneste for en lokal SQL Server, skal du:

- Hent og Installer Microsoft Data Management Gateway på computeren lokalt. 
- konfigurere tjenesten sammenkædede for den lokale datakilde for brug af gatewayen. 

Datastyringsgateway serializes og deserializes kilde- og sink dataene på den computer, hvor det hostes.

Se [flytte data mellem lokale datakilder og skyen med Datastyringsgateway](../data-factory/data-factory-move-data-between-onprem-and-cloud.md) til konfigureres instruktioner og oplysninger om Data Management Gateway


## <a name="adflinkedservices"></a>Oprette sammenkædede services til at oprette forbindelse til dataressourcer

En sammenkædet tjeneste definerer de oplysninger, der bruges til Azure Data Factory til at oprette forbindelse til en ressource, data. Den trinvise procedure til at oprette sammenkædede services findes i [oprette sammenkædede tjenester](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-2-create-linked-services).

Vi har tre ressourcer i dette scenarie, der er behov for sammenkædede serviceydelser.

1. [Sammenkædede-tjeneste til lokal SQL Server](#adf-linked-service-onprem-sql)
2. [Sammenkædede-tjeneste til Azure Blob-lager](#adf-linked-service-blob-store)
3. [Sammenkædede-tjeneste til Azure SQL-database](#adf-linked-service-azure-sql)


###<a name="adf-linked-service-onprem-sql"></a>Sammenkædede-tjeneste til lokal SQL Server-database

Sådan opretter du sammenkædede tjenesten for lokal SQL Server:

- Klik på det **Datalager** i landingssiden ADF på klassisk Azure-portalen 
- Vælg **SQL** , og Angiv legitimationsoplysningerne *brugernavn* og *din adgangskode* for lokal SQL Server. Du skal angive servernavn som et **fuldt kvalificeret servernavn navnet på forekomsten omvendt skråstreg (Servernavn\Forekomstnavn)**. Navngiv sammenkædede service *adfonpremsql*.

###<a name="adf-linked-service-blob-store"></a>Sammenkædede-tjeneste til Blob

Sådan opretter du sammenkædede tjenesten for kontoen Azure Blob-lager:

- Klik på det **Datalager** i landingssiden ADF på klassisk Azure-portalen
- Vælg **Azure lagerplads konto** 
- Indtast et Azure Blob-lager nøgle og objektbeholder kontonavn. Navngiv sammenkædede Service *adfds*.

###<a name="adf-linked-service-azure-sql"></a>Sammenkædede-tjeneste til Azure SQL-database

Sådan opretter du sammenkædede tjenesten for den Azure SQL-Database:

- Klik på det **Datalager** i landingssiden ADF på klassisk Azure-portalen
- Vælg **SQL Azure** , og Angiv legitimationsoplysningerne *brugernavn* og *din adgangskode* for Azure SQL-Database. Feltet *brugernavn* skal være angivet som *user@servername*.   


##<a name="adf-tables"></a>Definere og oprette tabeller for at angive, hvordan du kan få adgang til DataSet

Opret tabeller, der angiver den struktur, placering og tilgængeligheden af DataSet med de følgende script-baserede procedurer. JSON filer bruges til at definere tabellerne. Du kan finde flere oplysninger om strukturen i filerne, [datasæt](../data-factory/data-factory-create-datasets.md).

> [AZURE.NOTE]  Du skal udføre på `Add-AzureAccount` cmdlet før udførelse af [Ny AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet for at bekræfte, at højre Azure abonnementet er markeret til kommandoudførelse. Du kan finde dokumentation for denne cmdlet [Tilføj AzureAccount](https://msdn.microsoft.com/library/azure/dn790372.aspx).

De JSON-baserede definitioner i tabellerne Brug følgende navne:

* **tabelnavn** i lokal SQL server er *nyctaxi_data*
* **navnet på objektbeholderen** i kontoen Azure Blob-lager er *containername*  

Tre tabeldefinitioner du skal bruge til denne ADF pipeline:

1. [SQL on-premise-tabel](#adf-table-onprem-sql)
2. [BLOB tabel](#adf-table-blob-store)
3. [SQL Azure-tabel](#adf-table-azure-sql)

> [AZURE.NOTE]  Disse procedurer bruge Azure PowerShell til at definere og oprette ADF aktiviteter. Men disse opgaver kan også gøres ved hjælp af portalen Azure. Få mere at vide under [oprette input og output datasæt](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-3-create-input-and-output-datasets).

###<a name="adf-table-onprem-sql"></a>SQL on-premise-tabel

Tabeldefinition for lokal SQL Server er angivet i den følgende JSON-filen:

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Kolonnenavnene blev ikke inkluderet her. Du kan underordnede vælge fra kolonnenavne ved at medtage dem her (få mere at vide Kontrollér emnet [ADF dokumentation](../data-factory/data-factory-data-movement-activities.md ) .

Kopiere JSON definitionen af tabellen til en fil kaldet *onpremtabledef.json* filen og gemme den på en kendt placering (her antages at *C:\temp\onpremtabledef.json*). Oprette tabellen i ADF med følgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


###<a name="adf-table-blob-store"></a>BLOB tabel
Definition for tabellen for blob outputplacering findes i følgende (kort oralt dataene fra lokal til Azure blob):

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Kopiere JSON definitionen af tabellen til en fil kaldet *bloboutputtabledef.json* filen og gemme den på en kendt placering (her antages at *C:\temp\bloboutputtabledef.json*). Oprette tabellen i ADF med følgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

###<a name="adf-table-azure-sq"></a>SQL Azure-tabel
Definition for tabellen til SQL Azure output er i det følgende (dette skema tilknyttes dataene kommer fra blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Kopiere JSON definitionen af tabellen til en fil kaldet *AzureSqlTable.json* filen og gemme den på en kendt placering (her antages at *C:\temp\AzureSqlTable.json*). Oprette tabellen i ADF med følgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


##<a name="adf-pipeline"></a>Definere og oprette pipeline

Angiv de aktiviteter, der hører til rørledningen og oprette rørledningen med de følgende script-baserede procedurer. En JSON-fil, der bruges til at definere pipeline yderligere egenskaber.

* Scriptet antager, at den **rørledning navn** er *AMLDSProcessPipeline*.
* Bemærk også, at vi angive intervallerne for pipeline udføres på dagligt og bruge standard kørselstid for sagen (12 am UTC).

> [AZURE.NOTE]Følgende procedurer bruge Azure PowerShell til at definere og oprette ADF pipeline. Men denne opgave kan også gøres ved hjælp af portalen Azure. Få mere at vide under [oprette og køre en rørledning](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#step-4-create-and-run-a-pipeline).

Ved hjælp af tabeldefinitionerne tidligere vises pipeline definitionen for den automatiske Dokumentføder på følgende måde:

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premise SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premise SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",             
                            }           
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Kopiér denne JSON definition af pipeline til en fil kaldet *pipelinedef.json* fil og gemme den på en kendt placering (her antages at *C:\temp\pipelinedef.json*). Oprette en pipeline ADF med følgende Azure PowerShell-cmdlet:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json

Bekræft, at du kan se pipeline på ADF i portalen Azure klassisk vises som følgende (når du klikker på diagrammet)

![](media/machine-learning-data-science-move-sql-azure-adf/DJP1kji.png)


##<a name="adf-pipeline-start"></a>Starte en Pipeline
Pipeline kan nu køres ved hjælp af følgende kommando:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Parameterværdier *startdate* og *enddate* skal erstattes med de faktiske datoer, som du vil imellem pipeline til at køre.

Når rørledningen udfører, skal du kunne se dataene, der vises i objektbeholderen valgt til blob, én fil om dagen.

Bemærk, at vi ikke har opgraderede de funktioner, som ADF pipe data trinvist. Du kan finde flere oplysninger om, hvordan du gør dette og andre funktioner i ADF [ADF dokumentation](https://azure.microsoft.com/services/data-factory/).
