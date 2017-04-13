<properties
    pageTitle="Oprette din første data factory (Visual Studio) | Microsoft Azure"
    description="I dette selvstudium, kan du oprette en stikprøve Azure Data Factory rørledning, ved hjælp af Visual Studio."
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
    ms.topic="hero-article" 
    ms.date="10/17/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-azure-first-data-factory-using-microsoft-visual-studio"></a>Selvstudium: Opbygge din Azure første data factory ved hjælp af Microsoft Visual Studio
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-build-your-first-pipeline.md)
- [Azure-portalen](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

I denne artikel kan bruge du Microsoft Visual Studio til at oprette din første Azure data factory.

## <a name="prerequisites"></a>Forudsætninger
1. Læse [Selvstudium oversigt](data-factory-build-your-first-pipeline.md) artikel, og Fuldfør trinnene **betingelse** .
2. Du skal være en **administrator af Azure abonnementet** skal kunne publicere Data Factory-objekter fra Visual Studio til Azure Data Factory.
3. Du skal have følgende installeret på computeren: 
    - Visual Studio 2013 eller Visual Studio 2015
    - Hent Azure SDK til Visual Studio 2013 eller Visual Studio-2015. Gå til [Azure hente side](https://azure.microsoft.com/downloads/) , og klik på **VS 2013** eller **VS 2015** i sektionen **.NET** .
    - Hent den nyeste Azure Data Factory-plug-in til Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Du kan også opdatere plug-in'en ved at gøre følgende: Klik på menuen **Funktioner** -> **Extensions og opdateringer** -> **Online** -> **Visual Studio galleriet** -> **Microsoft Azure Data Factory Tools til Visual Studio** -> **opdatere**. 
 
Nu skal vi bruge Visual Studio til at oprette en Azure data factory. 


## <a name="create-visual-studio-project"></a>Oprette Visual Studio-projekt 
1. Start **Visual Studio 2013** eller **Visual Studio-2015**. Klik på **filer**, peg på **Ny**, og klik på **projekt**. Du bør se dialogboksen **Nyt projekt** .  
2. Vælg skabelonen, **DataFactory** i dialogboksen **Nyt projekt** , og klik på **Tom Data Factory projekt**.   

    ![Dialogboksen nyt projekt](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)

3. Angiv et **navn** til projektet, **placering**og et navn til **løsning**, og klik på **OK**.

    ![Solution Explorer](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

## <a name="create-linked-services"></a>Oprette sammenkædede tjenester
En data fabrik kan have en eller flere rørledninger. En rørledning kan have en eller flere aktiviteter i den. For eksempel en kopi aktivitet at kopiere data fra en kilde til en destination datalager og en HDInsight Hive aktivitet til at køre Hive script for at transformere indtastede data. Se [understøttede data gemmer](data-factory-data-movement-activities.md##supported-data-stores-and-formats) for alle de kilder og dræn understøttes af den kopi aktivitet. Se [beregne sammenkædede tjenester](data-factory-compute-linked-services.md) til listen over Beregn tjenester, der understøttes af Data Factory. 

I dette trin skal knytte du kontoen Azure-lager og en efter behov Azure HDInsight klynge til dine data factory. Kontoen Azure-lager indeholder input- og outputområder dataene for rørledningen i dette eksempel. Tjenesten HDInsight sammenkædet bruges til at køre Hive-script, der er angivet i aktiviteten af rørledning i dette eksempel. Identificere, hvilke data store/Beregn services bruges i scenariet og sammenkæde disse tjenester til fabriksindstillingerne data ved at oprette sammenkædede tjenester.  

Du skal angive navn og indstillinger for data factory senere, når du publicerer din Data Factory-løsning.

#### <a name="create-azure-storage-linked-service"></a>Oprette Azure-lager, der er sammenkædet service
I dette trin kan sammenkæde du kontoen Azure-lager til dine data factory. Dette selvstudium skal bruge du den samme konto Azure-lager til at gemme input/output data og scriptfil HQL. 

4. Højreklik på **Sammenkædede tjenester** i solution explorer, peg på **Tilføj**, og klik på **Nyt element**.      
5. Vælg **Azure-sammenkædede Lagringstjeneste** på listen i dialogboksen **Tilføj nyt element** , og klik på **Tilføj**. 
3. Erstatte **kontonavn** og **accountkey** med navnet på kontoen Azure-lager og nøglen. For at lære at få din lagerplads hurtigtast, få [vist, Kopiér og Regenerer lagerplads access-taster](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)

    ![Azure-lager sammenkædet Service](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)

4. Gem filen **AzureStorageLinkedService1.json** .

#### <a name="create-azure-hdinsight-linked-service"></a>Oprette Azure HDInsight sammenkædet service
I dette trin kan sammenkæde du en efter behov HDInsight klynge til dine data factory. HDInsight klynge oprettes på kørselstidspunktet og slettet, når det er gjort kataloger og inaktiv for det angivne tidsrum automatisk. Du kan bruge din egen HDInsight klynge i stedet for ved hjælp af en efter behov HDInsight klynge. Du kan finde oplysninger i [Beregne sammenkædede tjenester](data-factory-compute-linked-services.md) . 

1. I **Solution Explorer**, højreklik på **Sammenkædede Services**, peg på **Føj**, og klik på **Nyt element**.
2. Vælg **HDInsight på Demand sammenkædede tjeneste**, og klik på **Tilføj**. 
3. Erstat **JSON** med følgende:

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService1"
            }
          }
        }
    
    Den følgende tabel indeholder en beskrivelse til de JSON egenskaber, der bruges i kodestykket:
    
    Egenskaben | Beskrivelse
    -------- | -----------
    Version | Angiver, at versionen af HDInsight oprettede til at være 3,2. 
    ClusterSize | Angiver størrelsen på HDInsight klyngen. 
    TimeToLive | Angiver, at indstillingen Inaktivitetstid for HDInsight-klyngen, før det bliver slettet.
    linkedServiceName | Angiver lagerplads-konto, der bruges til at gemme logfiler, der genereres af HDInsight

    Bemærk følgende: 
    
    - Data Factory opretter en **Windows-baseret** HDInsight klynge for dig med den foregående JSON. Du kan også få dem oprette en **Linux-baserede** HDInsight klynge. Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
    - Du kan bruge **din egen HDInsight klynge** i stedet for ved hjælp af en efter behov HDInsight klynge. Du kan finde oplysninger i [HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
    - HDInsight klynge opretter en **standard objektbeholder** i den blob-lager, du har angivet i JSON (**linkedServiceName**). HDInsight sletter ikke denne beholder, når klyngen slettes. Det skyldes design. Med efter behov HDInsight sammenkædet tjeneste oprettes en HDInsight klynge, hver gang et udsnit behandles, medmindre der er en eksisterende direkte klynge (**timeToLive**). Klyngen slettes automatisk, når behandlingen er færdig.
    
        Efterhånden som flere udsnit er behandlet, kan du se mange beholdere i Azure blob-lager. Hvis du ikke skal bruge dem til fejlfinding af job, kan du vil slette dem for at reducere omkostningerne lagerplads. Navnene på disse beholdere følge et mønster: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Bruge funktioner såsom [Microsoft lagerplads Explorer](http://storageexplorer.com/) til at slette beholdere i Azure blob-lager.

    Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
4. Gem filen **HDInsightOnDemandLinkedService1.json** .

## <a name="create-datasets"></a>Oprette datasæt
I dette trin skal oprette du datasæt for at repræsentere input og eksportere data til Hive behandling. Disse datasæt referere til **AzureStorageLinkedService1** , du har oprettet tidligere i dette selvstudium. Den sammenkædede service for peger på en Azure-lager konto og datasæt angive objektbeholder, mappe, filnavn i opbevaring, der indeholder input og output-data.   

#### <a name="create-input-dataset"></a>Oprette input datasæt

1. Højreklik på **tabeller**i **Solution Explorer**, peg på **Tilføj**, og klik på **Nyt element**. 
2. Vælg **Azure Blob** på listen, ændre navnet på filen til **InputDataSet.json**, og klik på **Tilføj**.
3. Erstat **JSON** i editoren med følgende: 

    I JSON kodestykket opretter du et datasæt, kaldet **AzureBlobInput** , der repræsenterer indtastede data for en aktivitet i pipeline. Derudover kan angive du, at de indtastede data er placeret i objektbeholderen blob kaldet **adfgetstarted** og den mappe med navnet **inputdata**
        
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService1",
                "typeProperties": {
                    "fileName": "input.log",
                    "folderPath": "adfgetstarted/inputdata",
                    "format": {
                        "type": "TextFormat",
                        "columnDelimiter": ","
                    }
                },
                "availability": {
                    "frequency": "Month",
                    "interval": 1
                },
                "external": true,
                "policy": {}
            }
        } 

    Den følgende tabel indeholder en beskrivelse til de JSON egenskaber, der bruges i kodestykket:

  	| Egenskaben | Beskrivelse |
  	| :------- | :---------- |
  	| type | Typeegenskaben er indstillet til AzureBlob, fordi dataene opbevares i Azure blob-lager. |  
  	| linkedServiceName | refererer til den AzureStorageLinkedService1, du oprettede tidligere. |
  	| Filnavn | Denne egenskab er valgfrit. Hvis du udelader denne egenskab, er alle filerne fra mappesti valgte. I dette tilfælde behandles kun input.log. |
  	| type | Logfilerne er i tekstformat, så vi bruge tekstformat. | 
  	| columnDelimiter | kolonner i logfilerne er adskilt af kommategn () |
  	| frekvens/interval | hyppighed, der er angivet til måned og interval er 1, hvilket betyder, at de input udsnit er tilgængelige hver måned. | 
  	| eksterne | Denne egenskab er indstillet til sand, hvis de indtastede data ikke er oprettet af tjenesten Data Factory. | 
      
    
3. Gem filen **InputDataset.json** . 

 
#### <a name="create-output-dataset"></a>Oprette output datasæt
Nu skal oprette du output datasæt for at repræsentere den outputdata, der er gemt i Azure Blob-lager. 

1. Højreklik på **tabeller**i **Solution Explorer**, peg på **Tilføj**, og klik på **Nyt element**. 
2. Vælg **Azure Blob** på listen, ændre navnet på filen til **OutputDataset.json**, og klik på **Tilføj**. 
3. Erstat **JSON** i editoren med følgende: 

    I JSON kodestykket opretter du et datasæt, kaldet **AzureBlobOutput**og angive strukturen af de data, der oprettes af scriptet Hive. Derudover kan angive du, gemmes resultaterne i objektbeholderen blob kaldet **adfgetstarted** og den mappe med navnet **partitioneddata**. Afsnittet **tilgængelighed** angiver, at output datasættet produceret på månedsbasis.
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
            "typeProperties": {
              "folderPath": "adfgetstarted/partitioneddata",
              "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
              }
            },
            "availability": {
              "frequency": "Month",
              "interval": 1
            }
          }
        }

    Se **oprette input datasættet** afsnittet beskrivelser af disse egenskaber. Du angiver ikke egenskaben eksterne på en output datasættet, mens datasættet er oprettet med tjenesten Data Factory.

4. Gem filen **OutputDataset.json** .


### <a name="create-pipeline"></a>Oprette pipeline
I dette trin skal oprette du din første pipeline med en **HDInsightHive** aktivitet. Indtast udsnittet findes månedligt (frekvens: måned, interval: 1), output udsnit produceret månedligt og egenskaben scheduler for aktiviteten også er indstillet til månedlig. Indstillingerne for output datasættet og aktivitet planlæggeren skal svare til. Output datasæt er i øjeblikket, hvad drev tidsplanen, så du skal oprette et datasæt output, selvom aktiviteten ikke medfører noget output. Hvis aktiviteten ikke tager alt input, kan du springe over oprettelse af input datasættet. De egenskaber, der bruges i de følgende JSON gennemgås i slutningen af dette afsnit.

1. I **Solution Explorer**, højreklik på **rørledninger**, peg på **Føj**, og klik på **nyt element.** 
2. Vælg **Hive Transformation Pipeline** på listen, og klik på **Tilføj**. 
3. Erstat **JSON** med følgende kodestykke.

    > [AZURE.IMPORTANT] Erstat **storageaccountname** med navnet på din konto, lagerplads.

        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService1",
                            "defines": {
                                "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                                "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                            }
                        },
                        "inputs": [
                            {
                                "name": "AzureBlobInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "AzureBlobOutput"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                    }
                ],
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }

    I JSON kodestykket opretter du en rørledning, der består af en enkelt aktivitet, der bruger Hive til proces Data på en HDInsight klynge.
    
    I JSON kodestykket opretter du en rørledning, der består af en enkelt aktivitet, der bruger Hive til proces Data på en HDInsight klynge.
    
    Hive script-fil **partitionweblogs.hql**, er gemt i kontoen Azure-lager (angivet af scriptLinkedService, kaldet **AzureStorageLinkedService1**) og **script** mappe i beholderen **adfgetstarted**.

    Sektionen **definerer** bruges til at angive runtime-indstillinger, der sendes til hive scriptet som Hive konfigurationsværdier (f.eks. ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

    **Start** og **Stop** egenskaberne for rørledningen angiver den aktive periode af rørledninger.

    I aktiviteten JSON angiver du, at Hive scriptet kører på Beregn angivet af **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Du kan finde oplysninger om JSON egenskaber, der bruges i eksemplet i [Anatomien af en rørledning](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 
3. Gem filen **HiveActivity1.json** .

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>Tilføje partitionweblogs.hql og input.log som en afhængighed 

1. Højreklik på **afhængigheder** i **Solution Explorer** -vinduet, peg på **Tilføj**, og klik på **Eksisterende element**.  
2. Gå til **C:\ADFGettingStarted** og vælge **partitionweblogs.hql**, **input.log** filer, og klik på **Tilføj**. Du har oprettet disse to filer som en del af forudsætninger fra [Selvstudium oversigt](data-factory-build-your-first-pipeline.md).

Når du udgiver løsningen i næste trin, er **partitionweblogs.hql** filen overført til mappen scripts i beholderen **adfgetstarted** blob.   

### <a name="publishdeploy-data-factory-entities"></a>Publicere/installere Data Factory-enheder

18. Højreklik på projektet i Solution Explorer, og klik på **Publicer**. 
19. Hvis du får vist dialogboksen **Log på din Microsoft-konto** , indtaste dine legitimationsoplysninger for den konto, der har Azure abonnement, og klik på **Log på**.
20. Du burde se dialogboksen følgende:

    ![Dialogboksen Publicer](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. På siden Konfigurer data factory skal du gøre følgende: 
    1. Vælg **Opret ny Data Factory** -indstilling.
    2. Angiv et entydigt **navn** til fabriksindstillingerne data. For eksempel: **FirstDataFactoryUsingVS09152016**. Navnet skal være globalt entydig.  
    
    
        > [AZURE.IMPORTANT] Hvis du modtager fejlen **Data factory navnet "FirstDataFactoryUsingVS" er ikke tilgængelig** , når du udgiver, kan du ændre navnet (for eksempel yournameFirstDataFactoryUsingVS). Se emnet med [Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler for Data Factory elementer.
3. Vælg det rigtige abonnement for feltet **abonnement** .
     
     
        > [AZURE.IMPORTANT] Hvis du ikke kan se et abonnement, kan du sikre dig, at du har logget på ved hjælp af en konto, der er en administrator eller co-administrator af abonnementet.  
        
    4. Vælg den **ressourcegruppe** til data factory skal oprettes. 
    5. Vælg **område** for data factory. 
    6. Klik på **Næste** til at skifte til siden **Publicere elementer** . (Tryk på **TAB** for at flytte fra feltet navn til den **Næste** knap er deaktiveret, hvis). 
23. **Publicere elementer** på siden skal du sikre, at alle Data virksomheder enheder er markeret, og klik på **Næste** til at skifte til siden **Oversigt** .     
24. Gennemse oversigten, og klik på **Næste** for at starte installationsprocessen og få vist **Status for installation**.
25. På siden **Status for installation** , skal du se status for installationsprocessen. Klik på Udfør, når installationen er færdig. 

 
Vigtige punkter at være opmærksom på: 

- Hvis du får fejl: "**dette abonnement er ikke registreret til at bruge navneområde Microsoft.DataFactory**", skal du gøre et af følgende og prøv at publicere igen: 

    - Kør følgende kommando for at registrere Data Factory provideren i Azure PowerShell. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Du kan køre følgende kommando for at bekræfte, at den Data Factory udbyder er registreret. 
    
            Get-AzureRmResourceProvider
    - Logon ved hjælp af Azure abonnementet i [Azure-portalen](https://portal.azure.com) , og gå til en Data Factory blade (eller) oprette en data fabrik i portalen Azure. Denne handling registrerer automatisk provideren for dig.
-   Navnet på data factory kan registreres som en DNS-navn på et senere tidspunkt og dermed bliver offentligt synlige.
-   Hvis du vil oprette Data Factory forekomster, skal du være en administrator eller co-administrator af Azure-abonnement

 
## <a name="monitor-pipeline"></a>Overvåge pipeline

### <a name="monitor-pipeline-using-diagram-view"></a>Overvåge rørledning med diagramvisning
6. Log på [Azure-portalen](https://portal.azure.com/)skal du gøre følgende:
    1. Klik på **flere tjenester** og **Data fabrikker**.
        ![Gennemse data fabrikker](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png) 
    2. Vælg navnet på din data factory (for eksempel: **FirstDataFactoryUsingVS09152016**) på listen over data fabrikker. 
        ![Vælg dine data factory](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
7. Klik på **Diagram**på siden hjem til dine data factory.
  
    ![Diagram felt](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
7. I visningen Diagram skal se du en oversigt over rørledninger og datasæt, der bruges i dette selvstudium.
    
    ![Diagramvisning](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png) 
8. Højreklik på pipeline i diagrammet for at få vist alle aktiviteter i pipeline, og klik på Åbn Pipeline. 

    ![Åbn pipeline menu](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
9. Bekræft, at du ser HDInsightHive aktiviteten i pipeline. 
  
    ![Åbn pipeline visning](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Hvis du vil gå tilbage til den forrige visning, skal du klikke på **Data factory** i brødkrummemenuen øverst. 
10. Dobbeltklik på datasættet **AzureBlobInput**i **Diagramvisning**. Kontrollér, at udsnittet er i **klar** tilstand. Det kan tage et par minutter, før udsnittet skal vises i klar tilstand. Hvis den indtræffer efter at du venter på et tidspunkt, kan du se, om du har input filen (input.log) placeret i det rigtige objektbeholder (adfgetstarted) og den mappe (inputdata).

    ![Indtast udsnit i klar tilstand](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
11. Klik på **X** for at lukke **AzureBlobInput** blade. 
12. Dobbeltklik på datasættet **AzureBlobOutput**i **Diagramvisning**. Du kan se, at det udsnit, behandles i øjeblikket.

    ![Datasæt](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. Når behandling er færdig, skal se du udsnittet i **klar** tilstand.

    > [AZURE.IMPORTANT] Oprettelse af en efter behov HDInsight klynge tager normalt et tidspunkt (cirka 20 minutter). Derfor Forvent pipeline til at tage **cirka 30 minutter** at behandle udsnittet.  

    ![Datasæt](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png) 
    
10. Når udsnittet er i **klar** tilstand, skal du kontrollere mappen **partitioneddata** i objektbeholderen **adfgetstarted** i din blob storage for datatypen output.  
 
    ![outputdata](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Klik på udsnittet for at se oplysninger om det i en **Data udsnit** blade.

    ![Detaljer om udsnit af data](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Klik på en aktivitet køre **aktivitet kører liste** for at se oplysninger om en aktivitet, kører (Hive aktivitet i vores scenarie) i et vindue med **aktivitet køre detaljer** .   
    ![Aktivitet køre detaljer](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)  
    
    Du kan se Hive forespørgsel, der blev udført og statusoplysninger fra logfilerne. Disse logfiler er nyttige til at løse eventuelle problemer.  
 

Se [skærm datasæt og pipeline](data-factory-monitor-manage-pipelines.md) for vejledning i at bruge Azure-portalen til at overvåge pipeline og datasæt, du har oprettet i dette selvstudium.

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Overvåge pipeline ved hjælp af skærm og administrer App
Du kan også bruge skærm og administrere program tilladelse til at overvåge dine rørledninger. Finde detaljerede oplysninger om brug af dette program, [skærm og administrere Azure Data Factory rørledninger ved hjælp af overvågning og administration af App](data-factory-monitor-manage-app.md).

1. Klik på skærm og administrere felt.

    ![Overvåge og administrere felt](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png) 
2. Du bør se skærm og administrere-programmet. Ændre **Starttidspunkt** og **Sluttidspunkt** for at matche start (04-01-2016 12:00 AM)- og sluttidspunkter (04-02-2016 12:00 AM) af din pipeline, og klik på **Anvend**.

    ![Overvåge og administrere-App](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png) 
3. Vælg en aktivitet vindue i listen aktivitet Windows for at se oplysninger om den. 
    ![Aktivitet vinduet detaljer](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)


> [AZURE.IMPORTANT] Indtast filen bliver slettet, når udsnittet er behandlet. Derfor, hvis du vil køre udsnittet eller gøre selvstudiet igen, overføre input filen (input.log) til mappen inputdata for objektbeholderen adfgetstarted.
 

## <a name="use-server-explorer-to-view-data-factories"></a>Brug Server Stifinder til at få vist data fabrikker

1. Klik på **Vis** i menuen i **Visual Studio**, og klik på **Server Explorer**.
2. Udvid **Azure** , og udvid **Data Factory**i Server Stifinder-vinduet. Hvis du får vist **logge Visual Studio**, Angiv der er knyttet til abonnementet Azure **konto** , og klik på **Fortsæt**. Angiv **din adgangskode**, og klik på **Log på**. Visual Studio forsøger at få oplysninger om alle Azure data fabrikker i dit abonnement. Du kan se status for denne handling i vinduet **Data Factory-opgaveliste** .

    ![Server Explorer](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. Du kan højreklikke på en data fabrik, og vælg **Eksportere Data Factory til nye projekt** til at oprette et Visual Studio-projekt, der er baseret på en eksisterende data factory.

    ![Eksportere data factory](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Opdatere Data Factory værktøjer til Visual Studio

Hvis du vil opdatere Azure Data Factory værktøjer til Visual Studio, skal du gøre følgende:

1. Klik på **Funktioner** i menuen, og vælg **filtypenavne og opdateringer**.
2. Vælg **opdateringer** i venstre rude, og vælg derefter **Visual Studio galleriet**.
3. Vælg **Azure Data Factory-funktioner til Visual Studio** , og klik på **Opdater**. Hvis du ikke kan se dette element, har du allerede den nyeste version af værktøjerne. 

## <a name="use-configuration-files"></a>Brug konfigurationsfiler
Du kan bruge konfigurationsfiler i Visual Studio til at konfigurere egenskaber for sammenkædede tjenester/tabeller/rørledninger anderledes for hver-miljø. 

Overvej følgende JSON definitionen for en sammenkædet Azure-lager-tjeneste. Du vil angive **connectionString** med forskellige værdier for kontonavn og accountkey baseret på miljøet (Udviklingscenter/Test/fremstilling), du installerer Data Factory enheder. Du kan opnå dette problem ved hjælp af separat konfigurationsfil for hver-miljø. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    } 

### <a name="add-a-configuration-file"></a>Tilføje en konfigurationsfil
Tilføje en konfigurationsfil for hver-miljø ved at benytte følgende fremgangsmåde:   

1. Højreklik på Data Factory projektet i Visual Studio-løsning, peg på **Føj**, og klik på **nyt element**.
2. Vælg **Config** på listen over installerede skabeloner til venstre, vælge **Konfigurationsfil**, Angiv et **navn** til konfigurationsfilen, og klik på **Tilføj**.

    ![Tilføje konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Tilføje konfiguration af parametre og deres værdier i følgende format.

        {
            "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
            "AzureStorageLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            ],
            "AzureSqlLinkedService1": [
                {
                    "name": "$.properties.typeProperties.connectionString",
                    "value":  "Server=tcp:spsqlserver.database.windows.net,1433;Database=spsqldb;User ID=spelluru;Password=Sowmya123;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            ]
        }

    I dette eksempel konfigurerer connectionString egenskab for en sammenkædet Azure-lager og tjenesten en Azure SQL sammenkædet. Bemærk, at syntaksen for at angive navn er [JsonPath](http://goessner.net/articles/JsonPath/).   

    Hvis JSON har en egenskab har, der en matrix med værdier som vist i følgende kode:  

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
    
    Konfigurere egenskaber, som vist i følgende konfigurationsfil (Brug baseret på nul indeksering): 
        
        {
            "name": "$.properties.structure[0].name",
            "value": "FirstName"
        }
        {
            "name": "$.properties.structure[0].type",
            "value": "String"
        }
        {
            "name": "$.properties.structure[1].name",
            "value": "LastName"
        }
        {
            "name": "$.properties.structure[1].type",
            "value": "String"
        }

### <a name="property-names-with-spaces"></a>Egenskabsnavne med mellemrum
Hvis egenskabsnavnet på en er mellemrum, kan du bruge kantede parenteser som vist i følgende eksempel (databasenavn server): 

     {
         "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
         "value": "MyAsqlServer.database.windows.net"
     }


### <a name="deploy-solution-using-a-configuration"></a>Installere løsning ved hjælp af en konfiguration
Når du udgiver Azure Data Factory objekter i VS, kan du angive den konfiguration, som du vil bruge til publicering handlingen. 

Udgive objekter i et Azure Data Factory-projekt ved hjælp af konfigurationsfil:   

1. Højreklik på Data Factory project, og klik på **Publicer** for at få vist dialogboksen **Publicere elementer** . 
2. Vælg en eksisterende data factory eller Angiv værdier for at oprette en data fabrik på siden **Konfigurer data factory** , og klik på **Næste**.   
3. På siden **Publicere elementer** : Du kan se en rulleliste med tilgængelige konfigurationer for feltet **Vælg installation Config** .

    ![Vælg konfigurationsfil](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)

4. Vælg den **konfigurationsfil** , du vil bruge, og klik på **Næste**. 
5. Bekræft, at du ser navnet på JSON-fil på siden **Oversigt** , og klik på **Næste**. 
6. Klik på **Udfør** , når handlingen installationen er fuldført. 

Når du installerer, er værdierne fra konfigurationsfilen bruges til at angive værdier for egenskaber i JSON-filer for Data Factory objekter, før enheder, der er installeret til Azure Data Factory-tjenesten.   

## <a name="summary"></a>Oversigt 
I dette selvstudium, du har oprettet en Azure data factory til proces data ved at køre Hive script på en HDInsight hadoop klynge. Du har brugt Data Factory-Editor i portalen Azure til at gøre følgende:  

1.  Oprettet en Azure **data factory**.
2.  Oprettet to **sammenkædede tjenester**:
    1.  **Azure-lager** sammenkædet service sammenkæde din Azure blob-lager, der indeholder input/output filer til fabriksindstillingerne data.
    2.  **Azure HDInsight** efter behov sammenkædede service en efter behov HDInsight Hadoop klynge sammenkædes data factory. Azure Data Factory opretter en HDInsight Hadoop klynge just-in-time for at behandle inputdataene og frembringe outputdata. 
3.  Oprettet to **datasæt**, der beskriver input- og data for HDInsight Hive aktivitet i pipeline. 
4.  Oprettet en **rørledning** med en **HDInsight Hive** aktivitet.  


## <a name="next-steps"></a>Næste trin
Du har oprettet en rørledning med en transformation aktivitet (HDInsight aktivitet), der kører et Hive-script på en efter behov HDInsight klynge i denne artikel. Hvis du vil se, hvordan du bruger en kopi aktivitet til at kopiere data fra en Azure Blob til Azure SQL, se [Selvstudium: Kopiér data fra en Azure blob til Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
  
## <a name="see-also"></a>Se også
| Emne | Beskrivelse |
| :---- | :---- |
| [Data Transformation aktiviteter](data-factory-data-transformation-activities.md) | Denne artikel indeholder en liste over data transformation aktiviteter (såsom HDInsight Hive transformation, du har brugt i dette selvstudium) understøttes af Azure Data Factory. | 
| [Planlægning og udførelse af](data-factory-scheduling-and-execution.md) | Denne artikel forklares planlægnings- og udførelse af aspekter af Azure Data Factory programmet model. |
| [Rørledninger](data-factory-create-pipelines.md) | I denne artikel hjælper dig med at forstå rørledninger og aktiviteter i Azure Data Factory og hvordan du kan bruge dem til at oprette til slut datadrevne arbejdsprocesser for scenarie eller business. |
| [Datasæt](data-factory-create-datasets.md) | I denne artikel hjælper dig med at forstå datasæt i Azure Data Factory.
| [Overvåge og administrere rørledninger ved hjælp af overvågning App](data-factory-monitor-manage-app.md) | I denne artikel beskrives, hvordan du kan overvåge, administrere og foretage fejlfinding rørledninger med kommandoen overvågning og administration af App. 
