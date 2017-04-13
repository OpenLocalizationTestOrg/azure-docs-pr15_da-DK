<properties 
    pageTitle="Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af Visual Studio | Microsoft Azure" 
    description="I dette selvstudium oprette du en Azure Data Factory rørledning med en kopi aktivitet ved hjælp af Visual Studio." 
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
    ms.topic="get-started-article" 
    ms.date="10/17/2016" 
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Selvstudium: Oprette en rørledning med kopi aktivitet ved hjælp af Visual Studio
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Kopiere guiden](data-factory-copy-data-wizard-tutorial.md)
- [Azure-portalen](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure ressourcestyring skabelon](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Dette selvstudium viser, hvordan du opretter og overvåge en Azure data factory ved hjælp af Visual Studio. Pipeline i data factory bruger en kopi aktivitet til at kopiere data fra Azure Blob-lager til Azure SQL-Database.

Her er de trin, du udfører som en del af dette selvstudium:

1. Oprette to sammenkædede tjenester: **AzureStorageLinkedService1** og **AzureSqlinkedService1**. 

    AzureStorageLinkedService1 sammenkæder en Azure-lager og AzureSqlLinkedService1 links Azure SQL-database til data factory: **ADFTutorialDataFactoryVS**. Inputdataene for pipelinen er placeret i en blob objektbeholder i Azure blob-lager og outputdata er gemt i en tabel i Azure SQL-database. Derfor skal du tilføje disse to butikker som sammenkædede tjenester til fabriksindstillingerne data.
2. Oprette to datasæt: **InputDataset** og **OutputDataset**, som repræsenterer den input/output-data, der er gemt i datalagre. 

    For InputDataset angiver du objektbeholderen blob, der indeholder en blob med kildedata. For OutputDataset, kan du angive den SQL-tabel, der gemmer outputdataene. Du kan også angive andre egenskaber som struktur, tilgængelighed og politik.
3. Oprette en rørledning med navnet **ADFTutorialPipeline** i ADFTutorialDataFactoryVS. 

    Pipeline har en **Kopi aktivitet** , kopier, der er indtastet data fra Azure blob til outputtabellen Azure SQL. Kopiér aktiviteten udfører på flytning af data i Azure Data Factory. Aktiviteten er drevet af en globalt tilgængelig tjeneste, som kan kopiere data mellem forskellige data butikker i et sikkert, pålideligt og SVG måde. Se [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) artikel for at få mere at vide om den kopi aktivitet. 
4. Oprette en data fabrik med navnet **VSTutorialFactory**. Implementere data fabrik og alle Data Factory-enheder (sammenkædede services, tabeller og pipeline).    

## <a name="prerequisites"></a>Forudsætninger

1. Læse [Selvstudium oversigt](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) artikel, og Fuldfør trinnene **betingelse** . 
2. Du skal være en **administrator af Azure abonnementet** skal kunne publicere Data Factory objekter til Azure Data Factory.  
3. Du skal have følgende installeret på computeren: 
    - Visual Studio 2013 eller Visual Studio 2015
    - Hent Azure SDK til Visual Studio 2013 eller Visual Studio-2015. Gå til [Azure hente side](https://azure.microsoft.com/downloads/) , og klik på **VS 2013** eller **VS 2015** i sektionen **.NET** .
    - Hent den nyeste Azure Data Factory-plug-in til Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Du kan også opdatere plug-in'en ved at benytte følgende fremgangsmåde: Klik på menuen **Funktioner** -> **Extensions og opdateringer** -> **Online** -> **Visual Studio galleriet** -> **Microsoft Azure Data Factory Tools til Visual Studio** -> **opdatere**.

## <a name="create-visual-studio-project"></a>Oprette Visual Studio-projekt 
1. Start **Visual Studio-2013**. Klik på **filer**, peg på **Ny**, og klik på **projekt**. Du bør se dialogboksen **Nyt projekt** .  
2. Vælg skabelonen, **DataFactory** i dialogboksen **Nyt projekt** , og klik på **Tom Data Factory projekt**. Hvis du ikke kan se skabelonen DataFactory, luk Visual Studio, installere Azure SDK til Visual Studio 2013 og genåbn Visual Studio.  

    ![Dialogboksen nyt projekt](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)

3. Angiv et **navn** til projektet, **placering**og et navn til **løsning**, og klik på **OK**.

    ![Solution Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png) 

## <a name="create-linked-services"></a>Oprette sammenkædede tjenester
Sammenkædede services sammenkæde data butikker eller beregne tjenester til en Azure data factory. Se [understøttede data gemmer](data-factory-data-movement-activities.md##supported-data-stores-and-formats) for alle de kilder og dræn understøttes af den kopi aktivitet. Se [beregne sammenkædede tjenester](data-factory-compute-linked-services.md) til listen over Beregn tjenester, der understøttes af Data Factory. I dette selvstudium skal du ikke bruge en hvilken som helst Beregn tjeneste. 

I dette trin skal du oprette to sammenkædede tjenester: **AzureStorageLinkedService1** og **AzureSqlLinkedService1**. AzureStorageLinkedService1 sammenkædet service links kontotypen Azure-lager og AzureSqlLinkedService links Azure SQL-database til data factory: **ADFTutorialDataFactory**. 

### <a name="create-the-azure-storage-linked-service"></a>Oprette tjenesten sammenkædet Azure-lager

4. Højreklik på **Sammenkædede tjenester** i solution explorer, peg på **Tilføj**, og klik på **Nyt element**.      
5. Vælg **Azure-sammenkædede Lagringstjeneste** på listen i dialogboksen **Tilføj nyt element** , og klik på **Tilføj**. 

    ![Ny sammenkædede tjeneste](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
 
3. Erstatte `<accountname>` og `<accountkey>`* med navnet på kontoen Azure-lager og nøglen. 

    ![Azure-lager sammenkædet Service](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)

4. Gem filen **AzureStorageLinkedService1.json** .

> Se [flytte data fra/til Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) få mere at vide om egenskaber for JSON.

### <a name="create-the-azure-sql-linked-service"></a>Oprette Azure SQL sammenkædet service

5. Højreklik på **Sammenkædede Services** node i **Solution Explorer** igen, peg på **Tilføj**, og klik på **Nyt element**. 
6. Denne gang Vælg **Azure SQL sammenkædede tjeneste**, og klik på **Tilføj**. 
7. I feltet **AzureSqlLinkedService1.json fil**skal erstatte `<servername>`, `<databasename>`, `<username@servername>`, og `<password>` med navnene på dine Azure SQL server, database, brugerkonto og adgangskode.    
8.  Gem filen **AzureSqlLinkedService1.json** . 

> [AZURE.NOTE]
> Se [flytte data fra/til Azure SQL-Database](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) for at få oplysninger om JSON egenskaber.

## <a name="create-datasets"></a>Oprette datasæt
I det forrige trin, du har oprettet sammenkædede services **AzureStorageLinkedService1** og **AzureSqlLinkedService1** vil sammenkæde en Azure-lager konto og Azure SQL-database med data factory: **ADFTutorialDataFactory**. I dette trin skal definere du to datasæt-- **InputDataset** og **OutputDataset** --, der repræsenterer de input/output-data, der er gemt i de datalagre henvises i AzureStorageLinkedService1 og AzureSqlLinkedService1 henholdsvis. For InputDataset angiver du objektbeholderen blob, der indeholder en blob med kildedata. For OutputDataset, kan du angive den SQL-tabel, der gemmer outputdataene.

### <a name="create-input-dataset"></a>Oprette input datasæt
I dette trin skal oprette du et datasæt med navnet **InputDataset** , der peger på en blob objektbeholder i Azure-lager, der er repræsenteret af tjenesten **AzureStorageLinkedService1** sammenkædet. En tabel er et rektangulært datasæt og er den eneste type datasæt understøttes lige nu. 

9. Højreklik på **tabeller** i **Solution Explorer**, peg på **Tilføj**, og klik på **Nyt element**.
10. Vælg **Azure Blob**i dialogboksen **Tilføj nyt element** , og klik på **Tilføj**.   
10. Erstat teksten JSON med følgende tekst og gemme filen **AzureBlobLocation1.json** . 

        {
          "name": "InputDataset",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adftutorial/",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "external": true,
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

     Bemærk følgende punkter: 
    
    - datasæt **type** er indstillet til **AzureBlob**.
    - **linkedServiceName** er angivet til **AzureStorageLinkedService**. Du har oprettet denne sammenkædede tjeneste i trin 2.
    - **mappesti** er indstillet til objektbeholderen **adftutorial** . Du kan også angive navnet på en blob i mappen ved hjælp af egenskaben **fileName** . Da du ikke angiver navnet på blob, betragtes som data fra alle BLOB i beholderen som en indtastede data.  
    - Formatér **type** er indstillet til **tekstformat**
    - Der er to felter i den tekstfil – **Fornavn** og **Efternavn** – adskilt af et kommategn (**columnDelimiter**) 
    - **Tilgængelighed** er indstillet til **hver time** (**frekvens** er indstillet til **time** og **interval** er angivet til **1**). Derfor søger Data Factory efter inputdataene hver time i rodmappen på blob objektbeholder (**adftutorial**), du har angivet. 
    
    Hvis du ikke angiver et **filnavn** til en **input** datasæt, der alle filer/BLOB fra mappen input (**mappesti**) betragtes som input. Hvis du angiver et filnavn i JSON, betragtes kun den angivne fil/blob asn input.
 
    Hvis du ikke angiver et **filnavn** til en **outputtabel**, de oprettede filer i **mappesti** navngives i følgende format: Data. &lt;Guid\&BT;. txt (eksempel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    For at angive **mappesti** og **filnavn** dynamisk baseret på tidspunktet, **SliceStart** skal du bruge egenskaben **partitionedBy** . I eksemplet nedenfor mappesti bruger år, måned og dag fra SliceStart (starttidspunktet for det udsnit, behandles) og filnavn bruger time fra SliceStart. Eksempelvis hvis et udsnit føres til 2016-09-20T08:00:00, mappenavn er indstillet til wikidatagateway/wikisampledataout/2016/09/20 og filnavnet er indstillet til 08.csv. 

            "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": 
            [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 

> [AZURE.NOTE]
> Se [flytte data fra/til Azure Blob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) få mere at vide om egenskaber for JSON.

### <a name="create-output-dataset"></a>Oprette output datasæt
I dette trin skal oprette du en output datasæt med navnet **OutputDataset**. Dette dataset peger på en SQL-tabel i den Azure SQL-database, der er repræsenteret af **AzureSqlLinkedService1**. 

11. Højreklik på **tabeller** i **Solution Explorer** igen, peg på **Tilføj**, og klik på **Nyt element**.
12. Vælg **Azure SQL**i dialogboksen **Tilføj nyt element** , og klik på **Tilføj**. 
13. Erstat teksten JSON med følgende JSON, og Gem filen **AzureSqlTableLocation1.json** .

        {
          "name": "OutputDataset",
          "properties": {
            "structure": [
              {
                "name": "FirstName",
                "type": "String"
              },
              {
                "name": "LastName",
                "type": "String"
              }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService1",
            "typeProperties": {
              "tableName": "emp"
            },
            "availability": {
              "frequency": "Hour",
              "interval": 1
            }
          }
        }

     Bemærk følgende punkter: 
    
    - datasæt **type** er indstillet til **AzureSQLTable**.
    - **linkedServiceName** er angivet til **AzureSqlLinkedService** (du oprettede denne sammenkædede tjeneste i trin 2).
    - **tabelnavn** er angivet til **emp**.
    - Der er tre kolonner- **ID**, **Fornavn**og **Efternavn** – i tabellen emp i databasen. ID er en id-kolonne, så du har brug at angive kun **Fornavn** og **Efternavn** her.
    - **Tilgængelighed** er indstillet til **hver time** (**hyppighed** , der er angivet til **hour** og **interval** , der er angivet til **1**).  Tjenesten Data Factory genererer en output data udsnit hver time i tabellen **emp** Azure SQL-database.

> [AZURE.NOTE]
> Se [flytte data fra/til Azure SQL-Database](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) for at få oplysninger om JSON egenskaber.

## <a name="create-pipeline"></a>Oprette pipeline 
Du har oprettet input/output sammenkædede tjenester og tabeller hidtil. Nu skal du oprette en rørledning med en **Kopi aktivitet** til at kopiere data fra Azure blob til Azure SQL-database. 


1. Højreklik på **rørledninger** i **Solution Explorer**, peg på **Tilføj**, og klik på **Nyt element**.  
15. Vælg **Kopiér Data Pipeline** i dialogboksen **Tilføj nyt element** , og klik på **Tilføj**. 
16. Erstatte JSON med følgende JSON, og Gem filen **CopyActivity1.json** .
            
        {
          "name": "ADFTutorialPipeline",
          "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
              {
                "name": "CopyFromBlobToSQL",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "InputDataset"
                  }
                ],
                "outputs": [
                  {
                    "name": "OutputDataset"
                  }
                ],
                "typeProperties": {
                  "source": {
                    "type": "BlobSource"
                  },
                  "sink": {
                    "type": "SqlSink",
                    "writeBatchSize": 10000,
                    "writeBatchTimeout": "60:00:00"
                  }
                },
                "Policy": {
                  "concurrency": 1,
                  "executionPriorityOrder": "NewestFirst",
                  "style": "StartOfInterval",
                  "retry": 0,
                  "timeout": "01:00:00"
                }
              }
            ],
            "start": "2015-07-12T00:00:00Z",
            "end": "2015-07-13T00:00:00Z",
            "isPaused": false
          }
        }

    Bemærk følgende punkter:

    - I sektionen aktiviteter er der kun en aktivitet, hvis **type** er indstillet til **kopi**.
    - Input for aktiviteten er indstillet til **InputDataset** og afgang for aktiviteten er indstillet til **OutputDataset**.
    - I sektionen **typeProperties** **BlobSource** er angivet som kildetypen og **SqlSink** er angivet som typen sink.

    Erstat værdien af egenskaben **starte** med den aktuelle dag og **end** med den næste dag. Du kan angive kun datodel og springe klokkeslætsdelen af dato klokkeslæt. For eksempel "2016-02-03", som svarer til "2016-02-03T00:00:00Z"
    
    Begge start og Afslut dato/klokkeslæt være i [ISO-formatet](http://en.wikipedia.org/wiki/ISO_8601). For eksempel: 2016-10-14T16:32:41Z. **Sluttidspunktet** er valgfrit, men vi bruge det i dette selvstudium. 
    
    Hvis du ikke angiver værdien for egenskaben **slutningen** , er det beregnet som "**start + 48 timer**". For at køre rørledningen på ubestemt tid, skal du angive **9999-09-09** som værdien for egenskaben **slutningen** .
    
    I det foregående eksempel er der 24 dataudsnit, som hvert data udsnit produceret hver time.

## <a name="publishdeploy-data-factory-entities"></a>Publicere/installere Data Factory-enheder
I dette trin skal du publicere Data Factory-objekter (sammenkædede services, datasæt og pipeline) du oprettede tidligere. Du kan også angive navnet på den nye data factory skal have oprettet for at holde disse enheder.  

18. Højreklik på projektet i Solution Explorer, og klik på **Publicer**. 
19. Hvis du får vist dialogboksen **Log på din Microsoft-konto** , indtaste dine legitimationsoplysninger for den konto, der har Azure abonnement, og klik på **Log på**.
20. Du burde se dialogboksen følgende:

    ![Dialogboksen Publicer](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
21. På siden Konfigurer data factory skal du gøre følgende trin: 
    1. Vælg **Opret ny Data Factory** -indstilling.
    2. Angiv **VSTutorialFactory** for **navn**.  
    
        > [AZURE.IMPORTANT]  
        > Navnet på Azure data factory skal være globalt entydige. Hvis du modtager en fejlmeddelelse om navnet på data factory, når du udgiver, kan du ændre navnet på den data factory (for eksempel yournameVSTutorialFactory) og prøve at publicere igen. Se emnet med [Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler for Data Factory elementer.     
    3. Vælg abonnementet Azure for feltet **abonnement** .
     
        > [AZURE.IMPORTANT]Hvis du ikke kan se et abonnement, kan du sikre dig, at du har logget på ved hjælp af en konto, der er en administrator eller co-administrator af abonnementet.  
    4. Vælg **ressourcegruppe** til data factory skal oprettes. 5. Vælg **område** for data factory. Kun de områder, der understøttes af tjenesten Data Factory er vist i på rullelisten.
6. Klik på **Næste** til at skifte til siden **Publicere elementer** .
    
        ![Konfigurere factory dataadgangsside](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
23. **Publicere elementer** på siden skal du sikre, at alle Data virksomheder enheder er markeret, og klik på **Næste** til at skifte til siden **Oversigt** .
    
    ![Publicere elementer side](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
24. Gennemse oversigten, og klik på **Næste** for at starte installationsprocessen og få vist **Status for installation**.

    ![Publicer siden Oversigt](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
25. På siden **Status for installation** , skal du se status for installationsprocessen. Klik på Udfør, når installationen er færdig. 
    ![Statussiden for installation](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png) Bemærk følgende punkter: 

- Hvis du får fejl: "**dette abonnement er ikke registreret til at bruge navneområde Microsoft.DataFactory**", skal du gøre et af følgende og prøv at publicere igen: 

    - Kør følgende kommando for at registrere Data Factory provideren i Azure PowerShell. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Du kan køre følgende kommando for at bekræfte, at den Data Factory udbyder er registreret. 
    
            Get-AzureRmResourceProvider
    - Logon med Azure-abonnement til [Azure-portalen](https://portal.azure.com) , og gå til en Data Factory blade (eller) oprette en data fabrik i portalen Azure. Denne handling registrerer automatisk provideren for dig.
-   Navnet på data factory kan registreres som en DNS-navn på et senere tidspunkt og dermed bliver offentligt synlige.

> [AZURE.IMPORTANT] Hvis du vil oprette Data Factory forekomster, skal du være en administrator/co-administrator af Azure-abonnement

## <a name="summary"></a>Oversigt
I dette selvstudium, du har oprettet en Azure data factory til at kopiere data fra en Azure blob til en Azure SQL-database. Du har brugt Visual Studio til at oprette data factory, sammenkædede services, datasæt og en rørledning. Her er de overordnede trin, du har udført i dette selvstudium:  

1.  Oprettet en Azure **data factory**.
2.  Oprettet **sammenkædede tjenester**:
    1. En **Azure-lager** sammenkædet service sammenkæde kontoen Azure-lager, der indeholder indtastede data.    
    2. En **SQL Azure** sammenkædet service sammenkæde din Azure SQL-database, der indeholder outputdataene. 
3.  Oprettet **datasæt**, der beskriver inputdataene og outputdata for rørledninger.
4.  Oprettet en **rørledning** med en **Kopi aktivitet** med **BlobSource** som kilde- og **SqlSink** som sink. 


## <a name="use-server-explorer-to-view-data-factories"></a>Brug Server Stifinder til at få vist data fabrikker

1. Klik på **Vis** i menuen i **Visual Studio**, og klik på **Server Explorer**.
2. Udvid **Azure** , og udvid **Data Factory**i Server Stifinder-vinduet. Hvis du får vist **logge Visual Studio**, Angiv der er knyttet til abonnementet Azure **konto** , og klik på **Fortsæt**. Angiv **din adgangskode**, og klik på **Log på**. Visual Studio forsøger at få oplysninger om alle Azure data fabrikker i dit abonnement. Du kan se status for denne handling i vinduet **Data Factory-opgaveliste** .
    ![Server Explorer](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. Du kan højreklikke på en data fabrik og vælge Eksportér Data Factory til nyt projekt for at oprette et Visual Studio-projekt, der er baseret på en eksisterende data factory.
    ![Eksportere data factory til et projekt eller-](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Opdatere Data Factory værktøjer til Visual Studio
Hvis du vil opdatere Azure Data Factory værktøjer til Visual Studio, skal du udføre følgende trin:

1. Klik på **Funktioner** i menuen, og vælg **filtypenavne og opdateringer**. 
2. Vælg **opdateringer** i venstre rude, og vælg derefter **Visual Studio galleriet**.
4. Vælg **Azure Data Factory-funktioner til Visual Studio** , og klik på **Opdater**. Hvis du ikke kan se dette element, har du allerede den nyeste version af værktøjerne. 

Se [skærm datasæt og pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) for vejledning i at bruge Azure-portalen til at overvåge pipeline og datasæt, du har oprettet i dette selvstudium.

## <a name="see-also"></a>Se også
| Emne | Beskrivelse |
| :---- | :---- |
| [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) | I denne artikel indeholder detaljerede oplysninger om den kopi aktivitet, du har brugt i selvstudiet. |
| [Planlægning og udførelse af](data-factory-scheduling-and-execution.md) | Denne artikel forklares planlægnings- og udførelse af aspekter af Azure Data Factory programmet model. |
| [Rørledninger](data-factory-create-pipelines.md) | I denne artikel hjælper dig med at forstå rørledninger og aktiviteter i Azure Data Factory |
| [Datasæt](data-factory-create-datasets.md) | I denne artikel hjælper dig med at forstå datasæt i Azure Data Factory.
| [Overvåge og administrere rørledninger ved hjælp af overvågning App](data-factory-monitor-manage-app.md) | I denne artikel beskrives, hvordan du kan overvåge, administrere og foretage fejlfinding rørledninger med kommandoen overvågning og administration af App. 
