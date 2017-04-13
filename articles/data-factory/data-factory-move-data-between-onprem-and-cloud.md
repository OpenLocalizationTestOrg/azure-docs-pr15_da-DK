<properties 
    pageTitle="Flytte data - Datastyringsgateway | Microsoft Azure"
    description="Konfigurere en datastyringsgateway til at flytte data mellem lokale miljø og skyen. Brug af Datastyringsgateway i Azure Data Factory til at flytte dine data." 
    keywords="datastyringsgateway, dataintegration af, flytte data, gateway legitimationsoplysninger"
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
    ms.date="10/18/2016" 
    ms.author="jingwang"/>

# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Flytte data mellem lokale datakilder og skyen med Data Management Gateway
I denne artikel indeholder en oversigt over dataintegration af mellem lokale data butikker og skyen data butikker, der bruger Data Factory. Det er baseret på artiklen [Data bevægelse aktiviteter](data-factory-data-movement-activities.md) og andre data factory grundlæggende begreber artikler: [datasæt](data-factory-create-datasets.md) og [rørledninger](data-factory-create-pipelines.md). 

## <a name="data-management-gateway"></a>Datastyringsgateway
Du skal installere Datastyringsgateway på din lokale computer til at aktivere flytte data til/fra et lokalt datalager. Gatewayen kan installeres på samme computer som datalageret eller på en anden computer, så længe gatewayen kan oprette forbindelse til data store. 

> [AZURE.IMPORTANT] Se [Datastyringsgateway](data-factory-data-management-gateway.md) artikel for at få mere at vide om Datastyringsgateway.   

Følgende gennemgang viser, hvordan du opretter en fabrik data med en rørledning, der flytter data fra en lokal **SQL Server** -database til en Azure blob-lager. Som en del af denne gennemgang skal du installere og konfigurere Datastyringsgateway på din computer. 

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Gennemgang: kopiere lokale data til skyen
  
## <a name="create-data-factory"></a>Oprette data factory
I dette trin skal bruge du Azure portal til at oprette en Azure Data Factory-forekomst med navnet **ADFTutorialOnPremDF**. 

1.  Log på [Azure-portalen](https://portal.azure.com). 
2.  Klik på **+ Ny**, skal du klikke på **Intelligence + analytics**, og på **Data Factory**.

    ![Ny -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
2. Angiv **ADFTutorialOnPremDF** for navnet i bladet **nye data factory** .

    ![Føje til Startboard](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

    > [AZURE.IMPORTANT] 
    > Navnet på Azure data factory skal være globalt entydige. Hvis du får fejl: **Data factory navnet "ADFTutorialOnPremDF" er ikke tilgængelig**, ændre navnet på data factory (for eksempel yournameADFTutorialOnPremDF) og prøve at oprette igen. Brug dette navn i stedet for ADFTutorialOnPremDF under udførelse af resterende trin i dette selvstudium.
    > 
    > Navnet på data factory kan registreres som en **DNS-** navn på et senere tidspunkt og dermed bliver offentligt synlige.
3. Vælg det **Azure-abonnement** , hvor du vil data factory skal oprettes. 
4.  Vælg eksisterende **ressourcegruppe** eller Opret en ressourcegruppe. I dette selvstudium skal du oprette en ressourcegruppe med navnet: **ADFTutorialResourceGroup**. 
5.  Klik på **Opret** på bladet **nye data factory** .

    > [AZURE.IMPORTANT] For at oprette Data Factory forekomster, skal du være medlem af rollen [Data Factory bidragyder](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) på gruppeniveau abonnement/ressource. 
11. Efter oprettelse af er fuldført, skal se du bladet **Data Factory** , som vist på følgende billede:

    ![Data Factory-startsiden](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Oprette gateway
5. Klik på i bladet **Data Factory** **forfatter og installere** felt for at starte **Editor** til fabriksindstillingerne data.

    ![Redigere og installere side om side](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.  I Data Factory Editor, skal du klikke på **... Flere** på værktøjslinjen og derefter klikke på **Ny datastyringsgateway**. Du kan også højreklikke **Data Gateways** i trævisningen og klikke på **Ny datastyringsgateway**. 

    ![Ny datastyringsgateway på værktøjslinjen](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Angiv **adftutorialgateway** for **navn**bladet **Opret** , og klik på **OK**.    

    ![Oprette gatewayen blade](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)
3. Klik på **Installer direkte på denne computer**i bladet **Konfigurer** . Denne handling installationspakken for gateway-overførsler, installerer, konfigurerer og registrerer gatewayen på computeren.  

    > [AZURE.NOTE] 
    > Bruge Internet Explorer eller en Microsoft ClickOnce kompatible webbrowser.
    > 
    > Hvis du bruger Chrome, gå til [Chrome web store](https://chrome.google.com/webstore/), søge med "ClickOnce" nøgleord, vælge et af ClickOnce filtypenavne og installere den. 
    >  
    > Gør det samme for Firefox (Installer tilføjelsesprogram). Klik på **Menuen Åbn** knap på værktøjslinjen (**tre vandrette linjer** i øverste højre hjørne), skal du klikke på **tilføjelsesprogrammer**, søge med "ClickOnce" nøgleord, Vælg en af ClickOnce udvidelserne og installere den.    

    ![Gateway - konfigurere blade](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Denne metode er den nemmeste måde (et enkelt klik) at hente, installere, konfigurere og registrere gatewayen i ét enkelt trin. Du kan se programmet **Microsoft Data Management Gateway Configuration Manager** er installeret på computeren. Du kan også finde den eksekverbare **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    Du kan også hente og installere gateway manuelt ved at bruge linkene i denne blade og registrere den ved hjælp af tasten vises i feltet **Ny nøgle** tekst.
    
    Se [Datastyringsgateway](data-factory-data-management-gateway.md) artikel for at få alle oplysninger om gatewayen.

    >[AZURE.NOTE] Du skal være administrator på den lokale computer for at kunne installere og konfigurere Datastyringsgateway korrekt. Du kan tilføje flere brugere til gruppen **Data Management Gateway brugere** lokale Windows. Medlemmer af denne gruppe kan bruge værktøjet Konfigurationsstyring til Datastyringsgateway Data til at konfigurere gatewayen. 

5. Vent et par minutter, eller vente, indtil du ser meddelelsen om opgaven:

    ![Gatewayen installationen lykkedes](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png) 
6. Åbne **Konfigurationsstyring til Datastyringsgateway Data** -program på computeren. Skriv **Datastyringsgateway** til at få adgang til denne funktion i vinduet **Søg** . Du kan også finde den eksekverbare **ConfigManager.exe** i mappen: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared** 

    ![Konfigurationsstyring til datastyringsgateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
6. Bekræft, at du ser `adftutorialgateway is connected to the cloud service` meddelelse. Statuslinjen i bunden viser **tilsluttet til skytjenesten** sammen med et **grønt flueben**.

    Under fanen **Startside** , kan du også gøre følgende handlinger: 
    - **Registrere** en gateway med en nøgle fra Azure-portalen ved hjælp af knappen Register. 
    - **Stoppe** tjenesten Data Management Gateway Host kører på gatewaycomputeren. 
    - **Planlæg opdateringer** installeres på et bestemt tidspunkt på dagen. 
    - Få vist, hvornår gatewayen blev **senest opdateret**.
    - Angiv tid, som kan være installeret en opdatering til gatewayen. 

8. Skifte til fanen **Indstillinger** . Det certifikat, der er angivet i sektionen **certifikat** bruges til at kryptere/dekryptere legitimationsoplysninger for den lokale datalager, du angiver på portalen. (valgfrit) Klik på **Skift** for at bruge dit eget certifikat i stedet. Som standard bruger gatewayen det certifikat, der genereres automatisk af Data Factory-tjenesten.

    ![Konfiguration af gatewayen certifikater](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    Du kan også gøre følgende handlinger under fanen **Indstillinger** : 
    - Få vist eller eksportere det certifikat, der bruges af gatewayen.
    - Ændre HTTPS slutpunktet bruges af gatewayen.    
    - Angive en HTTP-proxy, der skal bruges i gatewayen.   
9. (valgfrit) Skifte til fanen **diagnosticering** , skal du kontrollere indstillingen **Aktiver logføring** , hvis du vil aktivere logføring, som du kan bruge til at foretage fejlfinding af problemer med gatewayen. Oplysninger om logføring kan findes i **Logbog** under **tjenester logfiler for programmer og** -> **Datastyringsgateway** node. 

    ![Fanen Diagnosticering](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    Du kan også udføre følgende handlinger i fanen **diagnosticering** : 
    
    - Brug **Testforbindelsen** sektion til en lokal datakilde ved hjælp af gatewayen.
    - Klik på **Vis logfiler** for at få vist Data Management Gateway loggen i vinduet Logbog. 
    - Klik på **Send logfiler** til at overføre en zip-fil med logfiler over seneste syv dage til Microsoft for at lette fejlfinding af din problemer. 
10. I sektionen **Afprøv forbindelse** under fanen **diagnosticering** vælge **SQL Server** for typen datalager, Skriv navnet på databaseserveren og navnet på databasen, angive godkendelsestype, angive brugernavn og adgangskode og klikke på **Test** for at teste, om gatewayen kan oprette forbindelse til databasen. 
11. Skift til webbrowseren, og i **Azure-portalen**skal du klikke på **OK** på bladet **Konfigurer** og derefter klikke på bladet **nye datastyringsgateway** .
6. Du bør se **adftutorialgateway** under **Data Gateways** i trævisningen i venstre side.  Hvis du klikker på det, skal du se den tilknyttede JSON. 
    

## <a name="create-linked-services"></a>Oprette sammenkædede tjenester 
I dette trin skal du oprette to sammenkædede tjenester: **AzureStorageLinkedService** og **SqlServerLinkedService**. **SqlServerLinkedService** linker til en lokal SQL Server-database og gemme en Azure blob **AzureStorageLinkedService** sammenkædede service linkene til fabriksindstillingerne data. Du kan oprette en rørledning senere i denne gennemgang, som kopieres data fra lokale SQL Server-database til Azure blob-lager. 

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Føje en sammenkædet tjeneste til en lokal SQL Server-database
1.  Klik på **nye data gemmes** på værktøjslinjen i **Data Factory Editor**, og vælg **SQL Server**. 

    ![Ny SQL Server, der er sammenkædet tjeneste](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.  I **JSON editor** i højre side skal du gøre følgende trin: 
    1. Angiv **adftutorialgateway**for **gatewayName**. 
    2. Gør følgende i **connectionString**: 
        1. Skriv navnet på den server, hvor SQL Server-databasen for **servernavn**.
        2. Skriv navnet på databasen til **Databasenavn**.
        3. Klik på **Krypter** knap på værktøjslinjen. Dette overførsler og starter programmet styring af legitimationsoplysninger.
        
            ![Legitimationsoplysninger Manager-program](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
        5. Angiv godkendelsestype, brugernavn og adgangskode i dialogboksen **Indstilling legitimationsoplysninger** , og klik på **OK**. Hvis forbindelsen er oprettet, de krypterede legitimationsoplysninger er gemt i JSON og dialogboksen lukkes. 
        6. Luk fanen tomme browser, der startes i dialogboksen, hvis det ikke automatisk lukkes, og gå tilbage til fanen ved hjælp af Azure portal. 
  
            På gatewaycomputeren er disse legitimationsoplysninger **krypteret** ved hjælp af et certifikat, der ejer Data Factory-tjenesten. Hvis du vil bruge det certifikat, der er knyttet til Datastyringsgateway i stedet, skal du se [angive legitimationsoplysninger sikkert](#set-credentials-and-security).    
    1.  Klik på **Implementer** på kommandolinjen til at installere SQL Server, der er sammenkædet-tjenesten. Du bør se tjenesten sammenkædede i trævisningen. 
        
        ![Tjenesten SQL Server, der er sammenkædet i trævisningen](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)  

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Tilføje en sammenkædet tjeneste for en Azure-lager-konto
 
1. Klik på **nye data gemmes** på kommandolinjen **Data Factory Editor**, og klik på **Azure-lager**.
2. Skriv navnet på kontoen Azure lagerplads for **kontonavn**.
3. Angiv nøgle til kontoen Azure lagerplads for **kontonøgle**.
4. Klik på **Installer** for at installere **AzureStorageLinkedService**.
   
 
## <a name="create-datasets"></a>Oprette datasæt
I dette trin skal du oprette input og afgang datasæt, der repræsenterer input- og outputområder data til kopieringen (lokal SQL Server-database = > Azure blob-lager). Gør følgende for (detaljeret vejledning følger listen), før du opretter datasæt:

- Opret en tabel med navnet **emp** i SQL Server-databasen du tilføjes som en sammenkædet tjeneste i data fabrik og Indsæt et par eksempel poster i tabellen.
- Oprette en blob beholder med navnet **adftutorial** i kontoen Azure blob-lager, du har tilføjet som en sammenkædet tjeneste til data factory.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Forberede selvstudiet lokal SQL Server

1. I den database, du har angivet for den lokale SQL Server sammenkædede service (**SqlServerLinkedService**), skal du bruge følgende SQL-script til at oprette tabellen **emp** i databasen.

        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL, 
            FirstName varchar(50),
            LastName varchar(50),
            CONSTRAINT PK_emp PRIMARY KEY (ID)
        )
        GO 
2. Indsætte nogle eksempel i tabellen: 

        INSERT INTO emp VALUES ('John', 'Doe')
        INSERT INTO emp VALUES ('Jane', 'Doe')

### <a name="create-input-dataset"></a>Oprette input datasæt

1. I **Data Factory Editor**skal du klikke på **... Flere**, klik på **nyt datasæt** på kommandolinjen, og klik på **SQL Server-tabel**. 
2.  Erstat JSON i højre rude med følgende tekst:
        
            {       
                "name": "EmpOnPremSQLTable",
                "properties": {
                    "type": "SqlServerTable",
                    "linkedServiceName": "SqlServerLinkedService",
                    "typeProperties": {
                        "tableName": "emp"
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

    Bemærk følgende punkter: 

    - **type** er indstillet til **SqlServerTable**.
    - **tabelnavn** er angivet til **emp**.
    - **linkedServiceName** er angivet til **SqlServerLinkedService** (du oprettede sammenkædede tjenesten tidligere i denne gennemgang.).
    - For en input datasæt, der ikke er oprettet af en anden rørledning i Azure Data Factory, skal du angive **eksterne** til **Sand**. Det angiver de indtastede data er produceret eksterne til Azure Data Factory-tjenesten. Du kan eventuelt angive politikker eksterne data ved hjælp af elementet **externalData** i sektionen **politik** .    

    Se [flytte data til/fra SQL Server](data-factory-sqlserver-connector.md) få mere at vide om egenskaber for JSON.
2. Klik på **Implementer** på kommandolinjen til at udrulle datasættet.  


### <a name="create-output-dataset"></a>Oprette output datasæt

1.  Klik på **nyt datasæt** på kommandolinjen **Data Factory Editor**, og klik på **Azure Blob-lager**.
2.  Erstat JSON i højre rude med følgende tekst: 

            {
                "name": "OutputBlobTable",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "AzureStorageLinkedService",
                    "typeProperties": {
                        "folderPath": "adftutorial/outfromonpremdf",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            }
  
    Bemærk følgende punkter: 
    
    - **type** er indstillet til **AzureBlob**.
    - **linkedServiceName** er angivet til **AzureStorageLinkedService** (du oprettede denne sammenkædede tjeneste i trin 2).
    - **mappesti** er angivet til **adftutorial/outfromonpremdf** , hvor outfromonpremdf er mappen i objektbeholderen adftutorial. Oprette objektbeholderen **adftutorial** , hvis den ikke allerede findes. 
    - **Tilgængelighed** er indstillet til **hver time** (**hyppighed** , der er angivet til **hour** og **interval** , der er angivet til **1**).  Tjenesten Data Factory genererer en output data udsnit hver time i tabellen **emp** Azure SQL-Database. 

    Hvis du ikke angiver et **filnavn** til en **outputtabel**, de oprettede filer i **mappesti** navngives i følgende format: Data. <Guid>.txt (for eksempel:: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    For at angive **mappesti** og **filnavn** dynamisk baseret på tidspunktet, **SliceStart** skal du bruge egenskaben partitionedBy. I eksemplet nedenfor mappesti bruger år, måned og dag fra SliceStart (starttidspunktet for det udsnit, behandles) og filnavn bruger time fra SliceStart. Eksempelvis hvis et udsnit genereres for 2014-10-20T08:00:00, mappenavn er indstillet til wikidatagateway/wikisampledataout/2014/10/20 og filnavnet er indstillet til 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 
    Se [flytte data til/fra Azure Blob-lager](data-factory-azure-blob-connector.md) få mere at vide om egenskaber for JSON.
2.  Klik på **Implementer** på kommandolinjen til at udrulle datasættet. Bekræft, at du ser begge datasæt i trævisningen.  

## <a name="create-pipeline"></a>Oprette pipeline
I dette trin skal du oprette en **rørledning** med en **Kopi aktivitet** , der bruger **EmpOnPremSQLTable** som input og **OutputBlobTable** som output.

1.  I Data Factory Editor, skal du klikke på **... Flere**, og klik på **ny pipeline**. 
2.  Erstat JSON i højre rude med følgende tekst: 
    
            {
                "name": "ADFTutorialPipelineOnPrem",
                "properties": {
                "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
                "activities": [
                {
                    "name": "CopyFromSQLtoBlob",
                    "description": "Copy data from on-prem SQL server to blob",
                    "type": "Copy",
                    "inputs": [
                    {
                        "name": "EmpOnPremSQLTable"
                    }
                    ],
                    "outputs": [
                    {
                        "name": "OutputBlobTable"
                      }
                    ],
                    "typeProperties": {
                      "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from emp"
                      },
                      "sink": {
                        "type": "BlobSink"
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
                "start": "2016-07-05T00:00:00Z",
                "end": "2016-07-06T00:00:00Z",
                "isPaused": false
              }
            }

    > [AZURE.IMPORTANT]
    > Erstat værdien af egenskaben **starte** med den aktuelle dag og **end** med den næste dag.

    Bemærk følgende punkter:
 
    - I sektionen aktiviteter er der kun aktivitet, hvis **type** er indstillet til **kopi**.
    - **Input** for aktiviteten er indstillet til **EmpOnPremSQLTable** og **output** for aktiviteten er indstillet til **OutputBlobTable**.
    - I sektionen **typeProperties** **SqlSource** er angivet som **Kildetype** og **BlobSink **er angivet som **sink type**.
    - SQL-forespørgsel `select * from emp` er angivet for egenskaben **sqlReaderQuery** for **SqlSource**.

     Begge start og Afslut dato/klokkeslæt være i [ISO-formatet](http://en.wikipedia.org/wiki/ISO_8601). For eksempel: 2014-10-14T16:32:41Z. **Sluttidspunktet** er valgfrit, men vi bruge det i dette selvstudium. 
    
    Hvis du ikke angiver værdien for egenskaben **slutningen** , er det beregnet som "**start + 48 timer**". For at køre rørledningen på ubestemt tid, skal du angive **9/9/9999** som værdien for egenskaben **slutningen** . 
    
    Du definerer den varighed, som data udsnittene behandles baseret på egenskaberne **tilgængelighed** , der er defineret for hver Azure Data Factory-datasæt.
    
    I eksemplet er der 24 dataudsnit, som hvert data udsnit produceret hver time.     
2. Klik på **Implementer** på kommandolinjen til at udrulle datasættet (tabel er et rektangulært datasæt). Bekræft, at rørledningen vises i trævisningen under **rørledninger** node.  
5. Nu skal du klikke på **X** to gange for at blade at komme tilbage til bladet **Data Factory** for **ADFTutorialOnPremDF**.

**Tillykke!** Du har oprettet en Azure data factory, sammenkædede services, datasæt og en rørledning og planlagt pipeline.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Få vist data factory i en diagramvisning 
1. Klik på **Diagram** -feltet på startsiden for **ADFTutorialOnPremDF** data factory i **Azure-portalen**. :

    ![Diagram Link](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Du burde se diagrammet svarende til følgende billede:

    ![Diagramvisning](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    Du kan zoome ind, zoome ud og zoom til 100%, zoom til at passe, placere rørledninger og datasæt automatisk og vise oplysninger om datakildeafstamning (fremhæver foregående og efterfølgende elementer for de markerede elementer).  Du kan dobbeltklikke på et objekt (input/output datasæt eller pipeline) for at se egenskaberne for den. 

## <a name="monitor-pipeline"></a>Overvåge pipeline
I dette trin skal bruge du Azure portal til at overvåge, hvad der foregår på en Azure data fabrik. Du kan også bruge PowerShell-cmdlet'er til at overvåge datasæt og rørledninger. Se Få mere at vide om overvågning af [skærmen og administrere rørledninger](data-factory-monitor-manage-pipelines.md).

5. Dobbeltklik på **EmpOnPremSQLTable**i diagrammet.  

    ![EmpOnPremSQLTable udsnit](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Bemærk, at alle dataene udsnit op er i **klar** tilstand, fordi pipeline varighed (starttidspunktet til sluttidspunkt) er passeret. Det er også, fordi du har indsat dataene i SQL Server-databasen, og det er der hele tiden. Bekræft, at ingen udsnit vises i sektionen **Problem udsnit** nederst. For at få vist alle udsnittene, skal du klikke på **Se mere** i bunden af listen over udsnit. 
7. Klik nu på **OutputBlobTable**i bladet **datasæt** .

    ![OputputBlobTable udsnit](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
9. Klik på en hvilken som helst data udsnit på listen, og skal du se afsnittet bladet **Data udsnit** . Du kan se aktivitet kører for udsnittet. Du kan se kun én aktivitet køre normalt.  

    ![Data udsnit Blade](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Hvis udsnittet ikke er i tilstanden **klar** , kan du se de tidligere udsnit, der ikke er klar og blokering af det aktuelle udsnit fra udførelse af på listen **før udsnit, der er ikke klar** .

10. Klik på **Kør aktivitet** fra listen i bunden for at se **aktivitet køre detaljer**.

    ![Aktivitet køre detaljer blade](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

    Du vil se oplysninger som overførselshastighed, varighed og den gateway, der bruges til at overføre data. 
11. Klik på **X** for at lukke alle blade indtil du 
12. Gå tilbage til bladet privat for **ADFTutorialOnPremDF**.
14. (valgfrit) Klik på **rørledninger**, skal du klikke på **ADFTutorialOnPremDF**og detaljeadgang input tabeller (**forbrugt**) eller output datasæt (**fremstillede**).
15. Brug funktioner som Brug funktioner som [Microsoft lagerplads Explorer](http://storageexplorer.com/) til at bekræfte, at der oprettes en blob/fil for hver time.

    ![Azure-lager Explorer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Næste trin

- Se [Datastyringsgateway](data-factory-data-management-gateway.md) artikel for at få alle oplysninger om Datastyringsgateway.
- Se [Kopiér data fra Azure Blob til Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) for at få mere at vide om, hvordan du bruger kopi aktivitet til at flytte data fra en kilde datalager til et sink datalager. 
