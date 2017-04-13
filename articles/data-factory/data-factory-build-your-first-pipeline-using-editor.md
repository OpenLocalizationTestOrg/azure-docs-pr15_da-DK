<properties
    pageTitle="Oprette din første data factory (Azure portal) | Microsoft Azure"
    description="I dette selvstudium, kan du oprette en eksempel Azure Data Factory-rørledning med Data Factory redigeringsprogram i portalen Azure."
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
    ms.date="09/14/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-portal"></a>Selvstudium: Oprette din første Azure data factory Azure portalen
> [AZURE.SELECTOR]
- [Oversigt og forudsætninger](data-factory-build-your-first-pipeline.md)
- [Azure-portalen](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Ressourcestyring skabelon](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

I denne artikel kan lære du at bruge [Azure portal](https://portal.azure.com/) til at oprette din første Azure data factory. 

## <a name="prerequisites"></a>Forudsætninger        
1. Læse [Selvstudium oversigt](data-factory-build-your-first-pipeline.md) artikel, og Fuldfør trinnene **betingelse** .
2. I denne artikel indeholder ikke en oversigt over Azure Data Factory-tjenesten. Vi anbefaler, at du gennemfører [Introduktion til Azure Data Factory](data-factory-introduction.md) -artikel for at få et detaljeret overblik til tjenesten.  

## <a name="create-data-factory"></a>Oprette data factory
En data fabrik kan have en eller flere rørledninger. En rørledning kan have en eller flere aktiviteter i den. En kopi aktivitet til at kopiere data fra en kilde til en destination datalager og en HDInsight Hive aktivitet til at køre Hive script for at transformere inputdataene til produkt får dataene. Lad os starte med at oprette data factory i dette trin. 

1.  Log på [Azure-portalen](https://portal.azure.com/).
2.  Klik på **Ny** i menuen til venstre, skal du klikke på **Data + analyser**og klik på **Data Factory**.
        
    ![Oprette blade](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.  Angiv **GetStartedDF** for navnet i bladet **nye data factory** .

    ![Nye data factory blade](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

    > [AZURE.IMPORTANT] 
    > Navnet på Azure data factory skal være **globalt entydige**. Hvis du får fejl: **Data factory navnet "GetStartedDF" er ikke tilgængelig**. Ændre navnet på data factory (for eksempel yournameGetStartedDF), og prøv at oprette igen. Se emnet med [Data Factory - navngivning af regler](data-factory-naming-rules.md) for navngivning regler for Data Factory elementer.
    > 
    > Navnet på data factory kan registreres som en **DNS-** navn på et senere tidspunkt og dermed bliver offentligt synlige.

3.  Vælg det **Azure-abonnement** , hvor du vil data factory skal oprettes. 
4.  Vælg eksisterende **ressourcegruppe** eller Opret en ressourcegruppe. I dette selvstudium skal du oprette en ressourcegruppe med navnet: **ADFGetStartedRG**. 
5.  Klik på **Opret** på bladet **nye data factory** .

    > [AZURE.IMPORTANT] For at oprette Data Factory forekomster, skal du være medlem af rollen [Data Factory bidragyder](../active-directory/role-based-access-built-in-roles.md/#data-factory-contributor) på gruppeniveau abonnement/ressource. 
6.  Du får vist den data factory, der oprettes i **Startboard** af portalen Azure på følgende måde:   

    ![Oprette data factory status](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Tillykke! Du har oprettet din første data factory. Når data factory er blevet oprettet, kan du se siden fabrik, som viser indholdet af data factory.   

    ![Data Factory blade](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Før du opretter en rørledning i data factory, skal du først oprette nogle Data Factory-enheder. Du først oprette sammenkædede services for at sammenkæde data butikker/beregner med dit datalager, definere input og output datasæt for at repræsentere input/output data i sammenkædede data butikker, og opret derefter rørledningen med en aktivitet, der bruger disse datasæt. 

## <a name="create-linked-services"></a>Oprette sammenkædede tjenester
I dette trin skal knytte du kontoen Azure-lager og en efter behov Azure HDInsight klynge til dine data factory. Kontoen Azure-lager indeholder input- og outputområder dataene for rørledningen i dette eksempel. Tjenesten HDInsight sammenkædet bruges til at køre Hive-script, der er angivet i aktiviteten af rørledning i dette eksempel. Identificere, hvilke [data gemme](data-factory-data-movement-activities.md)/[beregne services](data-factory-compute-linked-services.md) bruges i scenariet og sammenkæde disse tjenester til fabriksindstillingerne data ved at oprette sammenkædede tjenester.  

### <a name="create-azure-storage-linked-service"></a>Oprette Azure-lager, der er sammenkædet service
I dette trin kan sammenkæde du kontoen Azure-lager til dine data factory. I dette selvstudium bruger du den samme konto Azure-lager til lagring input/output data og HQL script-fil. 

1.  Klik på **forfatter og installere** på bladet **DATA FACTORY** for **GetStartedDF**. Du bør se Data Factory-Editor. 
     
    ![Redigere og installere side om side](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.  Klik på **nye data gemmes** , og vælg **Azure-lager**.

    ![Nye data store - Azure-lager - menu](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

3.  Du bør se JSON scriptet til at oprette en sammenkædet Azure-lager tjeneste i editoren. 
    
    ![Azure sammenkædet lagringstjeneste](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
     
4. Erstat **kontonavn** med navnet på din konto Azure-lager og **kontonøgle** hurtigtast konto, Azure-lager. For at lære at få din lagerplads hurtigtast, få [vist, Kopiér og Regenerer lagerplads access-taster](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
5. Klik på **Implementer** på kommandolinjen til at udrulle sammenkædede tjenesten.

    ![Installere knappen](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Når sammenkædede tjenesten er installeret korrekt, bør forsvinde vinduet **kladde-1** , og du får vist **AzureStorageLinkedService** i trævisningen i venstre side. 
    ![Sammenkædede Lagringstjeneste i menuen](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)   

 
### <a name="create-azure-hdinsight-linked-service"></a>Oprette Azure HDInsight sammenkædet service
I dette trin kan sammenkæde du en efter behov HDInsight klynge til dine data factory. HDInsight klynge oprettes på kørselstidspunktet og slettet, når det er gjort kataloger og inaktiv for det angivne tidsrum automatisk. 

1. I **Data Factory Editor**skal du klikke på **... Flere**, skal du klikke på **Ny beregne**, og vælg **efter behov HDInsight klynge**.

    ![Ny beregne](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Kopiere og indsætte følgende kodestykke til vinduet **kladde-1** . JSON kodestykke beskrives de egenskaber, der bruges til at oprette den HDInsight klynge efter behov. 

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
    
    Den følgende tabel indeholder en beskrivelse til de JSON egenskaber, der bruges i kodestykket:
    
  	| Egenskaben | Beskrivelse |
  	| :------- | :---------- |
  	| Version | Angiver, at versionen af HDInsight oprettede til at være 3,2. | 
  	| ClusterSize | Angiver størrelsen på HDInsight klyngen. | 
  	| TimeToLive | Angiver, at indstillingen Inaktivitetstid for HDInsight-klyngen, før det bliver slettet. |
  	| linkedServiceName | Angiver lagerplads-konto, der bruges til at gemme logfiler, der genereres af HDInsight. |

    Bemærk følgende punkter: 
    
    - Data Factory opretter en **Windows-baseret** HDInsight klynge for dig med JSON. Du kan også få dem oprette en **Linux-baserede** HDInsight klynge. Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . 
    - Du kan bruge **din egen HDInsight klynge** i stedet for ved hjælp af en efter behov HDInsight klynge. Du kan finde oplysninger i [HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
    - HDInsight klynge opretter en **standard objektbeholder** i den blob-lager, du har angivet i JSON (**linkedServiceName**). HDInsight sletter ikke denne beholder, når klyngen slettes. Det skyldes design. Med efter behov HDInsight sammenkædet tjeneste oprettes en HDInsight klynge, hver gang et udsnit behandles, medmindre der er en eksisterende direkte klynge (**timeToLive**). Klyngen slettes automatisk, når behandlingen er færdig.
    
        Efterhånden som flere udsnit er behandlet, kan du se mange beholdere i Azure blob-lager. Hvis du ikke skal bruge dem til fejlfinding af job, kan du vil slette dem for at reducere omkostningerne lagerplads. Navnene på disse beholdere følge et mønster: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Bruge funktioner såsom [Microsoft lagerplads Explorer](http://storageexplorer.com/) til at slette beholdere i Azure blob-lager.

    Du kan finde oplysninger i [Efter behov HDInsight sammenkædede tjeneste](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
3. Klik på **Implementer** på kommandolinjen til at udrulle sammenkædede tjenesten. 

    ![Installere efter behov HDInsight sammenkædet service](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

4. Bekræft, at du kan se både **AzureStorageLinkedService** og **HDInsightOnDemandLinkedService** i træet få vist i venstre side.

    ![Visning som træstruktur med sammenkædede tjenester](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Oprette datasæt
I dette trin skal oprette du datasæt for at repræsentere input og eksportere data til Hive behandling. Disse datasæt referere til **AzureStorageLinkedService** , du har oprettet tidligere i dette selvstudium. Den sammenkædede service for peger på en Azure-lager konto og datasæt angive objektbeholder, mappe, filnavn i opbevaring, der indeholder input og output-data.   

### <a name="create-input-dataset"></a>Oprette input datasæt

1. I **Data Factory Editor**skal du klikke på **... Flere** på kommandolinjen, skal du klikke på **nyt datasæt**, og vælg **Azure Blob-lager**.

    ![Nye datasæt](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Kopiere og indsætte følgende kodestykke til vinduet kladde-1. I JSON kodestykket opretter du et datasæt, kaldet **AzureBlobInput** , der repræsenterer indtastede data for en aktivitet i pipeline. Desuden kan du angive, at inputdataene er placeret i objektbeholderen blob kaldet **adfgetstarted** og den mappe med navnet **inputdata**.
        
        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "AzureStorageLinkedService",
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
  	| linkedServiceName | refererer til den AzureStorageLinkedService, du oprettede tidligere. |
  	| Filnavn | Denne egenskab er valgfrit. Hvis du udelader denne egenskab, er alle filerne fra mappesti valgte. I dette tilfælde behandles kun input.log. |
  	| type | Logfilerne er i tekstformat, så vi bruge tekstformat. | 
  	| columnDelimiter | kolonner i logfilerne er adskilt af kommategn () |
  	| frekvens/interval | hyppighed, der er angivet til måned og interval er 1, hvilket betyder, at de input udsnit er tilgængelige hver måned. | 
  	| eksterne | Denne egenskab er indstillet til sand, hvis de indtastede data ikke er oprettet af tjenesten Data Factory. | 
        
3. Klik på **Implementer** på kommandolinjen til at udrulle nyoprettede datasættet. Du bør se datasættet i trævisningen i venstre side. 


### <a name="create-output-dataset"></a>Oprette output datasæt
Nu skal oprette du output datasæt for at repræsentere den outputdata, der er gemt i Azure Blob-lager. 

1. I **Data Factory Editor**skal du klikke på **... Flere** på kommandolinjen, skal du klikke på **nyt datasæt**, og vælg **Azure Blob-lager**.  
2. Kopiere og indsætte følgende kodestykke til vinduet kladde-1. I JSON kodestykket opretter du et datasæt, kaldet **AzureBlobOutput**og angive strukturen af de data, der oprettes af scriptet Hive. Derudover kan angive du, gemmes resultaterne i objektbeholderen blob kaldet **adfgetstarted** og den mappe med navnet **partitioneddata**. Afsnittet **tilgængelighed** angiver, at output datasættet produceret på månedsbasis.
    
        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
3. Klik på **Implementer** på kommandolinjen til at udrulle nyoprettede datasættet.
4. Kontrollér, at datasættet er oprettet.

    ![Visning som træstruktur med sammenkædede tjenester](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-pipeline"></a>Oprette pipeline
I dette trin skal oprette du din første pipeline med en **HDInsightHive** aktivitet. Indtast udsnit findes månedligt (frekvens: måned, interval: 1), output udsnit produceret månedligt og egenskaben scheduler for aktiviteten også er indstillet til månedlig. Indstillingerne for output datasættet og aktivitet planlæggeren skal svare til. Output datasæt er i øjeblikket, hvad drev tidsplanen, så du skal oprette et datasæt output, selvom aktiviteten ikke medfører noget output. Hvis aktiviteten ikke tager alt input, kan du springe over oprettelse af input datasættet. De egenskaber, der bruges i de følgende JSON gennemgås i slutningen af dette afsnit. 

1. I **Data Factory Editor**skal du klikke på **ellipsen (...) Flere kommandoer** og derefter klikke på **ny pipeline**.
    
    ![knappen ny pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Kopiere og indsætte følgende kodestykke til vinduet kladde-1.

    > [AZURE.IMPORTANT] Erstat **storageaccountname** med navnet på din lagerplads konto i JSON.
        
        {
            "name": "MyFirstPipeline",
            "properties": {
                "description": "My first Azure Data Factory pipeline",
                "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                            "scriptLinkedService": "AzureStorageLinkedService",
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
    
    Hive script-fil **partitionweblogs.hql**, er gemt i kontoen Azure-lager (angivet af scriptLinkedService, kaldet **AzureStorageLinkedService**) og **script** mappe i beholderen **adfgetstarted**.

    Sektionen **definerer** bruges til at angive runtime-indstillinger, der sendes til hive scriptet som Hive konfigurationsværdier (f.eks. ${hiveconf: inputtable}, ${hiveconf:partitionedtable}).

    **Start** og **Stop** egenskaberne for rørledningen angiver den aktive periode af rørledninger.

    I aktiviteten JSON angiver du, at Hive scriptet kører på Beregn angivet af **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Du kan finde oplysninger om JSON egenskaber, der bruges i eksemplet i [Anatomien af en rørledning](data-factory-create-pipelines.md#anatomy-of-a-pipeline) . 

3. Ved at kontrollere følgende: 
    1. **input.log** filen findes i mappen **inputdata** i objektbeholderen **adfgetstarted** i Azure blob-lager
    2. **partitionweblogs.hql** fil findes i mappen **script** for objektbeholderen **adfgetstarted** i Azure blob-lager. Fuldført påkrævede trin i [Selvstudium oversigt](data-factory-build-your-first-pipeline.md) Hvis du ikke kan se disse filer. 
    3. Bekræft, at du erstattet **storageaccountname** med navnet på kontoen lagerplads i pipeline JSON. 
2. Klik på **Implementer** på kommandolinjen til at udrulle pipeline. Da ** **start** - og** sluttidspunkter angives tidligere og **isPaused** er indstillet til falsk, kører pipeline (aktivitet i pipeline) umiddelbart efter at du installerer. 
4. Bekræft, at du ser rørledningen i trævisningen.

    ![Visning som træstruktur med rørledning](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Tillykke, du har oprettet din første pipeline!

## <a name="monitor-pipeline"></a>Overvåge pipeline

### <a name="monitor-pipeline-using-diagram-view"></a>Overvåge rørledning med diagramvisning

6. Klik på **X** for at lukke Data Factory Editor blade og gå tilbage til bladet Data Factory, og klik på **Diagram**.
  
    ![Diagram felt](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. I visningen Diagram skal se du en oversigt over rørledninger og datasæt, der bruges i dette selvstudium.
    
    ![Diagramvisning](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Højreklik på pipeline i diagrammet for at få vist alle aktiviteter i pipeline, og klik på Åbn Pipeline. 

    ![Åbn pipeline menu](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Bekræft, at du ser HDInsightHive aktiviteten i pipeline. 
  
    ![Åbn pipeline visning](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Hvis du vil gå tilbage til den forrige visning, skal du klikke på **Data factory** i brødkrummemenuen øverst. 
10. Dobbeltklik på datasættet **AzureBlobInput**i **Diagramvisning**. Kontrollér, at udsnittet er i **klar** tilstand. Det kan tage et par minutter, før udsnittet skal vises i klar tilstand. Hvis den indtræffer efter at du venter på et tidspunkt, kan du se, om du har input filen (input.log) placeret i det rigtige objektbeholder (adfgetstarted) og den mappe (inputdata).

    ![Indtast udsnit i klar tilstand](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Klik på **X** for at lukke **AzureBlobInput** blade. 
12. Dobbeltklik på datasættet **AzureBlobOutput**i **Diagramvisning**. Du kan se, at det udsnit, behandles i øjeblikket.

    ![Datasæt](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Når behandling er færdig, skal se du udsnittet i **klar** tilstand.
    
>[AZURE.IMPORTANT] Oprettelse af en efter behov HDInsight klynge tager normalt et tidspunkt (cirka 20 minutter). Derfor Forvent pipeline til at tage **cirka 30 minutter** at behandle udsnittet.    

    ![Dataset](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png) 
    
10. Når udsnittet er i **klar** tilstand, skal du kontrollere mappen **partitioneddata** i objektbeholderen **adfgetstarted** i din blob storage for datatypen output.  
 
    ![outputdata](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)
11. Klik på udsnittet for at se oplysninger om det i en **Data udsnit** blade.

    ![Detaljer om udsnit af data](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)  
12. Klik på en aktivitet køre **aktivitet kører liste** for at se oplysninger om en aktivitet, kører (Hive aktivitet i vores scenarie) i et vindue med **aktivitet køre detaljer** .   

    ![Aktivitet køre detaljer](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)  
    
    Du kan se Hive forespørgsel, der blev udført og statusoplysninger fra logfilerne. Disse logfiler er nyttige til at løse eventuelle problemer.
Se [skærm og administrere rørledninger ved hjælp af Azure portalen blade](data-factory-monitor-manage-pipelines.md) artikel for at få mere at vide. 

> [AZURE.IMPORTANT] Indtast filen bliver slettet, når udsnittet er behandlet. Derfor, hvis du vil køre udsnittet eller gøre selvstudiet igen, overføre input filen (input.log) til mappen inputdata for objektbeholderen adfgetstarted.

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Overvåge pipeline ved hjælp af skærm og administrer App
Du kan også bruge skærm og administrere program tilladelse til at overvåge dine rørledninger. Finde detaljerede oplysninger om brug af dette program, [skærm og administrere Azure Data Factory rørledninger ved hjælp af overvågning og administration af App](data-factory-monitor-manage-app.md).

1. Klik på **skærm og administrer** felt på startsiden for dine data factory.

    ![Overvåge og administrere felt](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png) 
2. Du bør se **skærm og Administrer programmer**. Ændre **Starttidspunkt** og **Sluttidspunkt** for at matche start (04-01-2016 12:00 AM)- og sluttidspunkter (04-02-2016 12:00 AM) af din pipeline, og klik på **Anvend**.

    ![Overvåge og administrere-App](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png) 
3. Vælg en aktivitet vindue i listen **Aktivitet Windows** for at se oplysninger om den. 
    ![Aktivitet vinduet detaljer](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)


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

  

