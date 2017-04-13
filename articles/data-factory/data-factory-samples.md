<properties     
    pageTitle="Azure Data Factory - eksempler" 
    description="Du kan finde oplysninger om eksempler, der leveres med Azure Data Factory-tjenesten." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---samples"></a>Azure Data Factory - eksempler

## <a name="samples-on-github"></a>Eksempler på GitHub
[GitHub Azure-DataFactory lager](https://github.com/azure/azure-datafactory) indeholder flere eksempler, der hjælper dig med hurtigt Rampe op med Azure Data Factory-tjenesten (eller) ændre scriptene og bruge det i eget program. Mappen Samples\JSON indeholder JSON kodestykker for almindelige scenarier.

| Eksempel | Beskrivelse |
| :----- | :---------- | 
| [ADF gennemgang](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | Dette eksempel indeholder en gennemgang af til slut til at behandle logfiler ved hjælp af Azure Data Factory skal bruge data fra logfiler i indsigt. <br/><br/>I denne gennemgang Data Factory pipeline indsamler eksempel logfiler, processer og beriger data fra logfiler med referencedata og transformerer data for at evaluere effektiviteten i forbindelse med en marketingkampagne, der blev åbnet for nylig. |
| [JSON eksempler](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | Dette eksempel indeholder JSON eksempler på almindelige scenarier. | 
| [HTTP-Data Downloader eksempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | Dette eksempel showcases hentning af data fra en HTTP-slutpunkt til Azure Blob-lager ved hjælp af brugerdefinerede .NET aktivitet. |
| [Cross AppDomain prik Net aktivitet eksempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | Dette eksempel kan du redigere en brugerdefineret .NET aktivitet, der ikke er begrænset til samling versioner, der bruges af starteren ADF (For eksempel WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x osv.). |
| [Køre R script](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |  Dette eksempel indeholder de Data Factory tilpassede aktiviteter, der kan bruges til at kalde RScript.exe. Dette eksempel fungerer kun med din egen (ikke efter behov) HDInsight klynge, som allerede indeholder R installeret på den. |
| [Kalde knallertmotor job på HDInsight Hadoop klynge](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | Dette eksempel vises, hvordan du kan bruge MapReduce aktivitet til at starte et styret program. Knallertmotor kopieres lige data fra én Azure Blob-objektbeholder til en anden. |
| [Twitter-analyse ved hjælp af Azure Machine Learning batchen vundne aktivitet](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | Dette eksempel vises, hvordan du kan bruge AzureMLBatchScoringActivity til at starte en Azure Machine Learning-model, som udfører twitter synspunkt analyse vundne forudsigelse osv. |
| [Twitter-analyse ved hjælp af tilpassede aktiviteter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |  Dette eksempel vises, hvordan du kan bruge en brugerdefineret .NET aktivitet til at starte en Azure Machine Learning-model, som udfører twitter synspunkt analyse vundne forudsigelse osv. |
| [Parameteriseret rørledninger til Azure Machine Learning](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | Eksemplet giver en til slut C#-kode for at installere N rørledninger til vundne og omskoling hver med et andet område parameter, hvor listen over områder kommer fra en parameters.txt-fil, som er inkluderet i dette eksempel. | 
| [Reference dataopdatering for Azure Stream Analytics sager](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |  Dette eksempel vises, hvordan du bruger Azure Data Factory og Azure Stream Analytics sammen til at køre forespørgsler med referencedata og konfigurere opdatering for referencedata i en tidsplan. |
| [Hybrid rørledning med lokale Hortonworks Hadoop](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | Stikprøvernes bruger en lokal Hadoop klynge som Beregn destination for at køre job i Data Factory, ligesom skal du tilføje andre Beregn destinationer ud til en HDInsight baseret Hadoop klynge i skyen. |
| [JSON konverteringsværktøj](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | Dette værktøj kan du konvertere JSONs fra version før 2015-07-01-preview til seneste eller 2015-07-01-udskrift (standard). |  
| [U-SQL-input eksempelfil](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |  Denne fil er et eksempel-fil, der bruges af en U-SQL-aktivitet. | 

## <a name="azure-resource-manager-templates"></a>Azure ressourcestyring skabeloner
Du kan finde følgende Azure ressourcestyring skabeloner til Data Factory på Github. 

| Skabelon | Beskrivelse |
| -------- | ----------- | 
| [Kopiere fra Azure Blob-lager til Azure SQL-Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) | Implementere denne skabelon opretter en Azure data factory med en rørledning, der kopierer data fra den angivne Azure blob-lager til Azure SQL-database |    
| [Kopiere fra Salesforce til Azure Blob-lager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) | Implementere denne skabelon opretter en Azure data factory med en rørledning, der kopierer data fra den angivne Salesforce-konto til Azure blob-lager. |    
| [Transformere data ved at køre Hive script på en Azure HDInsight klynge](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) | Implementere denne skabelon opretter en Azure data factory med en rørledning, der transformerer data ved at køre scriptet eksempel Hive på en Azure HDInsight Hadoop-klynge. | 


## <a name="samples-in-azure-portal"></a>Eksempler på Azure-portal
Du kan bruge feltet **eksempel rørledninger** på startsiden for dine data factory skal installeres eksempel rørledninger og deres tilknyttede enheder (datasæt og sammenkædede services) i til dine data factory. 

1. Opret en data fabrik eller Åbn en eksisterende data factory. Se [Introduktion til Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory) til trin til at oprette en data fabrik.
2. Klik på feltet **eksempel rørledninger** i bladet **DATA FACTORY** for data factory.

    ![Eksempel rørledninger felt](./media/data-factory-samples/SamplePipelinesTile.png)

2. Klik på **eksempel** , du vil installere i bladet **eksempel rørledninger** . 
    
    ![Eksempel rørledninger blade](./media/data-factory-samples/SampleTile.png)

3. Angive indstillinger for søgekonfiguration til eksemplet. For eksempel din Azure lagerplads navn og konto kontonøgle, Azure SQL-servernavn, database, bruger-ID, og adgangskode osv. 

    ![Eksempel blade](./media/data-factory-samples/SampleBlade.png)

4. Klik på **Opret** for at oprette/installere eksempel rørledninger og sammenkædede tjenester/tabeller, der anvendes af rørledningerne, når du er færdig med at angive indstillinger for søgekonfiguration.
5. Du kan se status for installation i det eksempel-felt, du klikkede på tidligere på bladet **eksempel rørledninger** .

    ![Status for installation](./media/data-factory-samples/DeploymentStatus.png)

6. Når du ser meddelelsen **installation lykkedes** på feltet for stikprøven, skal du lukke bladet **eksempel rørledninger** .  
5. Under **DATA FACTORY** blade se du, at sammenkædede services, datasæt og rørledninger føjes til dine data factory.  

    ![Data Factory blade](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## <a name="samples-in-visual-studio"></a>Eksemplerne i Visual Studio

### <a name="prerequisites"></a>Forudsætninger

Du skal have følgende installeret på computeren: 

- Visual Studio 2013 eller Visual Studio 2015
- Hent Azure SDK til Visual Studio 2013 eller Visual Studio-2015. Gå til [Azure hente side](https://azure.microsoft.com/downloads/) , og klik på **VS 2013** eller **VS 2015** i sektionen **.NET** .
- Hent den nyeste Azure Data Factory-plug-in til Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) eller [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Hvis du bruger Visual Studio 2013, kan du også opdatere plug-in'en ved at benytte følgende fremgangsmåde: Klik på menuen **Funktioner** -> **Extensions og opdateringer** -> **Online** -> **Visual Studio galleriet** -> **Microsoft Azure Data Factory Tools til Visual Studio** -> **opdatere**.

### <a name="use-data-factory-templates"></a>Brug af Data Factory skabeloner

1. Klik på **filer** i menuen, peg på **Ny**, og klik på **projekt**. 
2. Gør følgende i dialogboksen **Nyt projekt** : 
    1. Vælg **DataFactory** under **skabeloner**. 
    2. Vælg **Data Factory skabeloner** i højre rude. 
    3. Angiv et **navn** for projektet. 
    4. Vælg en **placering** for projektet. 
    5. Klik på **OK**. 

    ![Dialogboksen nyt projekt](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. Vælg skabelonen, eksempel i afsnittet **Use Case skabeloner** i dialogboksen **Data Factory skabeloner** , og klik på **Næste**. Følgende trin fører dig igennem ved hjælp af skabelonen **Kunde profiler** . Trin er de samme for de andre eksempler. 

    ![Dialogboksen data Factory-skabeloner](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. Klik på **Næste** på siden **Data Factory grundlæggende funktioner** i dialogboksen **Data fabrik konfiguration** .
8. På siden **Konfigurer data factory** skal du gøre følgende trin: 
    1. Vælg **Opret ny Data Factory**. Du kan også vælge **Brug eksisterende data factory**.
    2. Angiv et **navn** til fabriksindstillingerne data.
    3. Vælg det **Azure-abonnement** , du vil data factory skal oprettes. 
    4. Vælg den **ressourcegruppe** til fabriksindstillingerne data.
    5. Markér den **Vest USA**, **Af US**eller **North Europe** til et **område**.
    6. Klik på **Næste**. 
9. Angiv en eksisterende **Azure SQL-database** og **Azure lagerplads konto** på siden **Konfigurer data gemmer** (eller) oprette databasen/lagerplads, og klik på Næste. 
10. Vælg standardindstillinger for på siden **Konfigurer beregne** , og klik på **Næste**. 
11. Gennemse alle indstillinger på siden **Oversigt** , og klik på **Næste**. 
12. På siden **Status for installation** skal vente, indtil installationen er fuldført, og klik på **Udfør**.
13. Højreklik på projektet i Solution Explorer, og klik på **Publicer**. 
19. Hvis du får vist dialogboksen **Log på din Microsoft-konto** , indtaste dine legitimationsoplysninger for den konto, der har Azure abonnement, og klik på **Log på**.
20. Du burde se dialogboksen følgende:

    ![Dialogboksen Publicer](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. På siden **Konfigurer data factory** skal du gøre følgende trin: 
    1. Bekræft den **Brug eksisterende data factory** -indstilling.
    2. Markér **data factory** du havde Vælg ved hjælp af skabelonen. 
    6. Klik på **Næste** til at skifte til siden **Publicere elementer** . (Tryk på **TAB** for at flytte fra feltet navn til den **Næste** knap er deaktiveret, hvis). 
23. **Publicere elementer** på siden skal du sikre, at alle Data virksomheder enheder er markeret, og klik på **Næste** til at skifte til siden **Oversigt** .     
24. Gennemse oversigten, og klik på **Næste** for at starte installationsprocessen og få vist **Status for installation**.
25. På siden **Status for installation** , skal du se status for installationsprocessen. Klik på Udfør, når installationen er færdig. 

Se [oprette din første data factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) få mere at vide om brug af Visual Studio til at redigere Data Factory enheder og publicere dem til Azure.          