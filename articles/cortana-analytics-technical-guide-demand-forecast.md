<properties
    pageTitle="Kræve prognose i energi tekniske vejledning | Microsoft Azure"
    description="En teknisk vejledning til skabelonen løsning med Microsoft Cortana Intelligence til behov forecast i energi."
    services="cortana-analytics"
    documentationCenter=""
    authors="yijichen"
    manager="ilanr9"
    editor="yijichen"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="inqiu;yijichen;ilanr9"/>

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Teknisk vejledning til skabelonen Cortana Intelligence løsning til behov forecast i energi

## <a name="overview"></a>**Oversigt**

Løsning skabeloner er designet til at fremskynde processen med at opbygge en E2E demo oven på Cortana Intelligence pakke. En udløst skabelon skal klargøre abonnementet med det er nødvendigt Cortana Intelligence-komponent og oprette relationer mellem. Det også forhåndsudfylder data rørledning med eksempeldata få dannet ud fra et data simulering program. Hente data simulator fra det link og installere den på din lokale computer skal du henvise til filen readme.txt for instruktion på ved hjælp af simulator. Data, der er dannet ud fra simulator vil hydrate data pipeline og begynde at generere machine learning forudsigelse, kan derefter visualiseres på dashboardet til Power BI.

Skabelonen løsning kan findes [her](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1) 

Installationsprocessen vejleder dig gennem flere trin til at konfigurere din løsning legitimationsoplysninger. Kontrollér, at du registrerer disse legitimationsoplysninger som løsningsnavn, brugernavn og adgangskode, du angiver under installationen.

Formålet med dette dokument er at forklare referencearkitektur og forskellige komponenter, der er klargjort i dit abonnement som en del af denne skabelon til en løsning. Dokumentet også taler om, hvordan du Udskift eksempeldataene, med rigtige data af dine egne for at kunne se indsigt/prognoser fra du har vundet data. Desuden på dokument taler om delene i skabelonen løsning, der skal ændres, hvis du vil tilpasse løsningen med dine egne data. Oplysninger om, hvordan du opretter Power BI-dashboardet for denne skabelon til en løsning er angivet i slutningen.

## <a name="big-picture"></a>**Overblik**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arkitektur forklaring
Når løsningen er installeret, aktiveres forskellige Azure tjenester i Cortana Analytics pakke (*det vil sige* begivenhed Hub Stream Analytics, HDInsight, Data Factory, Machine Learning, *osv.*). Arkitektur diagrammet ovenfor viser på højt niveau, hvordan Demand prognoser for energi løsning skabelon er opbygget fra til slut. Du vil kunne undersøge disse tjenester ved at klikke på dem på den løsning skabelon diagram, der er oprettet med installationen af løsningen. I følgende afsnit beskrives hver enkelt tekststykke.

## <a name="data-source-and-ingestion"></a>**Datakilde og indtagelse**

### <a name="synthetic-data-source"></a>Korte datakilde

For denne skabelon genereres den datakilde, der bruges i en på computeren, som du vil downloade og køre lokalt efter vellykket installation. Du kan finde vejledningen for at hente og installere dette program på værktøjslinjen Egenskaber, når du vælger den første node kaldet energi prognoser Data Simulator løsning skabelon diagrammet. Dette program feeds tjenesten [Azure begivenhed Hub](#azure-event-hub) med datapunkter eller begivenheder, der skal bruges i resten af strømmen løsning.

Generering af programmet begivenhed udfylder Azure begivenhed hubben kun mens det kører på computeren.

### <a name="azure-event-hub"></a>Azure begivenhed Hub

Tjenesten [Azure begivenhed Hub](https://azure.microsoft.com/services/event-hubs/) er modtageren af input fra den korte datakilde, der er beskrevet ovenfor.

## <a name="data-preparation-and-analysis"></a>**Forberedelse af data og analyse**


### <a name="azure-stream-analytics"></a>Azure Stream Analytics

Tjenesten [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) bruges til at give i realtid analytics på input strømmen fra tjenesten [Azure begivenhed Hub](#azure-event-hub) og udgive resultaterne til et [Power BI](https://powerbi.microsoft.com) -dashboard, samt arkivering alle rå indgående begivenheder til tjenesten [Azure-lager](https://azure.microsoft.com/services/storage/) til senere behandling af [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -tjenesten.

### <a name="hd-insights-custom-aggregation"></a>HD indsigt brugerdefinerede sammenlægning

Azure HD indsigt tjenesten bruges til at køre [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts (orchestrated af Azure Data Factory) for at give sammenlægninger på de rå hændelser, der blev arkiveret ved hjælp af tjenesten Azure Stream analyser.

### <a name="azure-machine-learning"></a>Azure Machine Learning

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) tjenesten bruges (orchestrated af Azure Data Factory) til at gøre prognose på fremtidige strømforbruget på et bestemt område, der er givet modtaget input.

## <a name="data-publishing"></a>**Publicering af data**


### <a name="azure-sql-database-service"></a>Azure SQL Database-tjenesten

Tjenesten [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/) bruges til at gemme (administreres af Azure Data Factory) prognoser modtaget af tjenesten Azure Machine Learning, som skal blive brugt i [Power BI](https://powerbi.microsoft.com) -dashboard.

## <a name="data-consumption"></a>**Data forbrug**

### <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com) -tjenesten bruges til at vise et dashboard, der indeholder sammenlægninger, forudsat ved [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -tjenesten samt demand forecast resultater, der er gemt i [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/) , der blev oprettet ved hjælp af tjenesten [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) . Se afsnittet nedenfor for oplysninger om, hvordan du opretter Power BI-dashboardet for denne skabelon til en løsning.

## <a name="how-to-bring-in-your-own-data"></a>**Hvordan du kan flytte dine egne data**

Dette afsnit beskrives, hvordan du tage dine egne data til Azure, og hvilke områder kræver ændringer for de data, du sætter i denne arkitektur.

Det sandsynligvis, at en hvilken som helst du tage datasæt ville svarer til datasættet bruges til denne skabelon til en løsning. Forstå dine data og kravene vil blive afgørende i, hvordan du ændrer denne skabelon til at arbejde med dine egne data. Hvis dette er dit første visning til Azure Machine Learning-tjenesten, kan du få en introduktion til den ved hjælp af eksemplet i [hvordan du kan oprette din første forsøg](machine-learning\machine-learning-create-experiment.md).

De følgende afsnit vil diskutere sektionerne i den skabelon, kræver ændringer, når der introduceres et nyt datasæt.

### <a name="azure-event-hub"></a>Azure begivenhed Hub

Tjenesten [Azure begivenhed Hub](https://azure.microsoft.com/services/event-hubs/) er meget generisk, så dataene kan sendes til hubben i csv- eller JSON-format. Ingen speciel behandling forekommer i Azure begivenhed-Hub, men det er vigtigt du forstår de data, der indføres i den.

Dette dokument beskriver ikke, hvordan indtager dataene, men en kan nemt sende begivenheder eller data til en Azure begivenhed-Hub, ved hjælp af [Begivenhed Hub API](event-hubs\event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

Tjenesten [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) bruges til at give i realtid analytics ved at læse fra datastreams og skriver data til en hvilken som helst antal kilder.

Til den Demand prognose for energi løsning skabelon består forespørgslen Azure Stream Analytics af to underordnede forespørgsler, hver forbrug begivenheder fra tjenesten Azure begivenhed Hub som input og output skulle to forskellige steder. Disse output består af en Power BI-datasæt og én placering til Azure-lager.

[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -forespørgsel kan findes ved at:

-   Sådan logger du ind på [Azure management-portalen](https://manage.windowsazure.com/)

-   Finde stream analytics-job ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png) , der blev oprettet, da løsningen blev installeret. En er for at placere data til blob storage (fx mytest1streaming432822asablob), og det andet er for at placere data til Power BI (fx mytest1streaming432822asapbi).


-   Hvis du vælger

    -   ***Input*** til at få vist forespørgsel input

    -   ***Forespørgsel*** til at få vist selve forespørgslen

    -   ***Output*** til at få vist forskellige output

Oplysninger om Azure Stream Analytics forespørgsel byggeri kan findes i [Stream Analytics forespørgsel Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I denne løsning leveres Azure Stream Analytics jobbet som producerer datasæt med nær realtid analytics oplysninger om indgående datastrømmen til et Power BI-dashboard som en del af denne skabelon til en løsning. Da der er implicit viden om indgående dataformat, skal disse forespørgsler ændres afhængigt af din dataformat.

Andre Azure Stream Analytics jobbet skriver alle [Begivenhed Hub](https://azure.microsoft.com/services/event-hubs/) begivenheder til [Azure-lager](https://azure.microsoft.com/services/storage/) og derfor kræver ingen ændring uanset din dataformat, som den fulde hændelsesoplysninger streames til lagerplads.

### <a name="azure-data-factory"></a>Azure Data Factory

Tjenesten [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestrates flytning og behandling af data. Data factory består af tolv [rørledninger](data-factory\data-factory-create-pipelines.md) , flytte og behandle data ved hjælp af forskellige teknologier i Demand prognoser for energi løsning skabelon.

  Du kan få adgang til dine data factory ved at åbne noden Data Factory i bunden af løsning skabelon diagrammet oprettet med installationen af løsningen. Dette kommer du til data factory på Azure management-portalen. Hvis du ser fejl under dit datasæt, kan du ignorere dem, som de er på grund af data factory der blev installeret, inden data generator er blevet startet. Disse fejl forhindrer ikke dine data factory i at fungere.

Dette afsnit beskrives de nødvendige [rørledninger](data-factory\data-factory-create-pipelines.md) og [aktiviteter](data-factory\data-factory-create-pipelines.md) , der er indeholdt i [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Nedenfor vises diagramvisning løsningens.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Fem af rørledninger af denne factory indeholder [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts, som bruges til at dele og aggregere dataene. Når angivet, vil scriptene være placeret i kontoen [Azure-lager](https://azure.microsoft.com/services/storage/) , der er oprettet under installationen. Deres placering bliver: demandforecasting\\\\script\\\\hive\\ \\ (eller https://[Your løsning name].blob.core.windows.net/demandforecasting).

Svarer til [Azure Stream Analytics](#azure-stream-analytics-1) -forespørgsler, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scriptene har implicit viden om indgående dataformat, disse forespørgsler skal ændres afhængigt af dine data format og [funktion teknisk](machine-learning\machine-learning-feature-selection-and-engineering.md) krav.

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*

Denne [pipeline](data-factory\data-factory-create-pipelines.md) pipeline indeholder en enkelt aktivitet – en [HDInsightHive](data-factory\data-factory-hive-activity.md) aktivitet ved hjælp af en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , der kører et [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script skal aggregeres hvert 10 streamet i demand data i understation niveau til hver time område niveau og placerer i [Azure-lager](https://azure.microsoft.com/services/storage/) via Azure Stream Analytics-job.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scriptet til denne leverandør opgave er ***AggregateDemandRegion1Hr.hql***


#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*

Denne [pipeline](data-factory\data-factory-create-pipelines.md) indeholder to aktiviteter:
- [HDInsightHive](data-factory\data-factory-hive-activity.md) aktivitet ved hjælp af en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , der kører et Hive script for at sammenlægge hver time historik demand dataene i understation niveau til hver time område niveau og placere i Azure-lager under Azure Stream Analytics-job

- [Kopiér](https://msdn.microsoft.com/library/azure/dn835035.aspx) en aktivitet, flyttes de aggregerede data fra Azure-lager blob til Azure SQL-Database, der blev klargjort som en del af løsning skabelon installationen.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scriptet til denne opgave er ***AggregateDemandHistoryRegion.hql***.


#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*

Disse [rørledninger](data-factory\data-factory-create-pipelines.md) indeholder flere aktiviteter, og hvis resultat slutningen er scored prognoser fra Azure Machine Learning forsøget der er knyttet til denne skabelon til en løsning. De er næsten ens, bortset fra hver af dem kun håndterer det andet område, der udføres af forskellige RegionID overføres i ADF pipeline og hive scriptet for hvert område.  
De aktiviteter, der er indeholdt i dette er:
-   [HDInsightHive](data-factory\data-factory-hive-activity.md) aktivitet ved hjælp af en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , der kører et Hive script for at udføre sammenlægninger og funktion teknisk nødvendige til Azure Machine Learning forsøget. Hive-scripts til denne opgave er respektive ***PrepareMLInputRegionX.hql***.

-   [Kopiér](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet, der flytter resultaterne fra [HDInsightHive](data-factory\data-factory-hive-activity.md) aktiviteten til en enkelt Azure-lager blob, der kan være adgang ved [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet.

-   [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet, der kalder Azure maskine læring eksperimentere, hvilket giver de resultater, der er lagt i en enkelt Azure-lager blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Disse [rørledninger](data-factory\data-factory-create-pipelines.md) indeholder en enkelt aktivitet - aktivitet, en [kopi](https://msdn.microsoft.com/library/azure/dn835035.aspx) , der flytter resultaterne af Azure Machine Learning forsøget fra de respektive ***MLScoringRegionXPipeline*** til Azure SQL-Database, der blev klargjort som en del af løsning skabelon installationen.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Denne [rørledninger](data-factory\data-factory-create-pipelines.md) indeholder en enkelt aktivitet - aktivitet, en [kopi](https://msdn.microsoft.com/library/azure/dn835035.aspx) , der flytter aggregeret igangværende demand data fra ***LoadHistoryDemandDataPipeline*** til Azure SQL-Database, der blev klargjort som en del af løsning skabelon installationen.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Disse [rørledninger](data-factory\data-factory-create-pipelines.md) indeholder en enkelt aktivitet - aktivitet, en [kopi](https://msdn.microsoft.com/library/azure/dn835035.aspx) , der flytter referencedataene for Region/understation/Topologygeo, der overføres til Azure-lager blob som en del af løsning skabelon installationen til Azure SQL-Database, der blev klargjort som en del af løsning skabelon installationen.

### <a name="azure-machine-learning"></a>Azure Machine Learning
[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) forsøget bruges til denne løsning skabelon indeholder forudsigelse af demand af område. Der er specifikke for datasættet consumed og derfor kræver ændres eller erstattes, der er specifikke for de data, der er hentet ind i.

## <a name="monitor-progress"></a>**Overvåge forløbet**
Når Data Generator er startet, pipeline begynder at hente hydrated og de forskellige komponenter i din løsning start konkurrencen til handling følgende kommandoerne udstedt af Data Factory. Der er to måder, du kan overvåge pipeline.

1. Markér feltet data fra Azure Blob-lager.

    En af Stream Analytics jobbet skriver indgående rækkedata til blob-lager. Hvis du klikker på **Azure Blob-lager** komponent i din løsning fra skærmbilledet du er blevet installeret løsningen og klik derefter på **Åbn** i højre panel, kommer du til [Azure management portal](https://portal.azure.com). Når der, klikke på **BLOB**. Du får vist en liste over beholdere i panelet næste. Klik på **"energysadata"**. I panelet næste vises mappen **"demandongoing"** . I mappen RAADATA, får du vist mapper med navne som dato = 2016-01-28 osv. Hvis du får vist disse mapper, indikerer, at den rækkedata er korrekt der oprettes på din computer og gemmes i blob-lager. Du bør se filer, som skal have endelig størrelser i MB i disse mapper.

2. Markér feltet data fra Azure SQL-Database.

    Det sidste trin af rørledninger, er at skrive data (fx prognoser fra machine learning) til SQL-Database. Det være nødvendigt at vente op til to timer for dataene, der skal vises i SQL-Database. Der er en metode til at overvåge, hvor meget data findes i dine SQL-Database via [Azure management portal](https://manage.windowsazure.com/). Find SQL-DATABASER på panelet til venstre![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png) og klikke på den. Find din database (det vil sige demo123456db) og klik på den derefter. Klik på **"Kør Transact-SQL-forespørgsler med SQL-database"**på den næste side under **"Opret forbindelse til databasen"** sektion.

    Her kan du klikke på ny forespørgsel og forespørgsel for antallet af rækker (fx "Vælg count(*) fra DemandRealHourly)" som databasen vokser, antallet af rækker i tabellen skal øge.)

3. Markér feltet data fra dashboard i Power BI.

    Du kan konfigurere Power BI varmt sti dashboard til at overvåge indgående rækkedata. Følg instruktionerne i afsnittet "Dashboard i Power BI".



## <a name="power-bi-dashboard"></a>**Dashboard i Power BI**

### <a name="overview"></a>Oversigt

I dette afsnit beskrives, hvordan du konfigurerer dashboard i Power BI til at visualisere dine realtid data fra Azure stream analytics (varmt vej) samt forecast-resultater fra Azure maskine læringssti (kolde).


### <a name="setup-hot-path-dashboard"></a>Konfiguration af varmt sti Dashboard

Følgende trin vejleder dig, hvordan du visualisere realtid data output fra Stream Analytics-job, som er blevet oprettet på tidspunktet for installationen af løsningen. En [Power BI online](http://www.powerbi.com/) konto skal du udføre følgende trin. Hvis du ikke har en konto, kan du [oprette en](https://powerbi.microsoft.com/pricing).

1.  Tilføje Power BI output i Azure Stream Analytics (ASA).

    -  Skal du følge vejledningen i [Azure Stream analyser og Power BI: et realtid analytics dashboard til realtid synligheden af streaming data](stream-analytics-power-bi-dashboard.md) til konfiguration af output fra Azure Stream Analytics tingene som dit Power BI-dashboard.

    - Find stream analytics jobbet i din [Azure management portal](https://manage.windowsazure.com). Navnet på jobbet skal være: YourSolutionName + "streamingjob" + vilkårlige tal + "asapbi" (det vil sige demostreamingjob123456asapbi).

    - Tilføje en PowerBI output til ASA jobbet. Angive **Output Alias** som **'PBIoutput'**. Angiv dit **Navn på datasæt** og **tabelnavn** som **'EnergyStreamData'**. Når du har tilføjet output skal du klikke på **"Start"** nederst på siden for at starte Stream Analytics-job. Du skal have en bekræftelsesmeddelelse (*f.eks.*, "Start stream analytics jobbet myteststreamingjob12345asablob lykkedes").

2. Log på [Power BI online](http://www.powerbi.com)

    -   På panelet til venstre datasæt sektion i min arbejdsområde, skal du kunne se et nyt datasæt, der viser på panelet til venstre på Power BI. Dette er de streaming data, du har trykket fra Azure Stream Analytics på det forrige trin.

    -   Kontrollér, at ruden ***visuelle effekter*** er åben, og der vises på i højre side af skærmen.

3. Oprette "Demand ved tidsstempel" felt:
    -   Klik på datasættet **'EnergyStreamData'** i panelet til venstre datasæt sektion.

    -   Klik på **"kurvediagram"** ikonet ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

    -   Klik på 'EnergyStreamData' i **felterne** panel.

    -   Klik på **"Tidsstempel"** , og Sørg for, at den vises under "Akse". Klik på **"Indlæs"** , og Sørg for, at den vises under "Værdier".

    -   Klik på **Gem** øverst, og navngiv rapporten som "EnergyStreamDataReport". Rapporten med navnet "EnergyStreamDataReport" vises i rapporter afsnit i Navigator-ruden til venstre.

    -   Klik på **"Fastgør visuel"** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) ikonet i øverste højre hjørne af dette kurvediagram, et "Fastgør til Dashboard" vindue kan vise til dig om at vælge et dashboard. Markere "EnergyStreamDataReport" og derefter klikke på "Fastgør".

    -   Hold musen over dette felt på dashboardet til, klik på "redigere" ikonet i øverste højre hjørne for at ændre titlen som "Demand ved tidsstempel"

4.  Oprette andre dashboardfelter, der er baseret på relevante datasæt. Den endelige dashboardvisning er vist nedenfor.
        ![](media\cortana-analytics-technical-guide-demand-forecast\PBIFullScreen.png)


### <a name="setup-cold-path-dashboard"></a>Konfiguration af kolde sti Dashboard
I kolde sti data pipeline er væsentlige målet at få demand prognosen på hvert område. Power BI opretter forbindelse til en Azure SQL-database som datakilde, hvor resultaterne Tekstfuldførelse er gemt.

> [AZURE.NOTE] 1) det tager par timer at indsamle nok prognose resultater for dashboard. Vi anbefaler, at du starter denne proces 2-3 timer efter at du lunch data generator. 2) i dette trin er de nødvendige forudsætninger at downloade og installere den gratis software [Power BI desktop](https://powerbi.microsoft.com/desktop).



1.  Få legitimationsoplysningerne database.

    Du skal **databaseservernavnet, databasenavnet, brugernavn og adgangskode** før du går videre til næste trin. Her er trinnene til at hjælpe dig, hvordan du kan finde dem.

    -   Når **"Azure SQL-Database"** på diagrammet løsning skabelon bliver grøn, klikke på den, og klik derefter på **"Åbn"**. Bliver du vejledt Azure management-portalen, og din database oplysningsside åbnes samt.

    -   Du kan finde en "Database" sektion på siden. Det viser ud af den database, du har oprettet. Navnet på databasen skal være **"Din løsningsnavn + tilfældigt nummer + 'db'"** (f.eks. "mytest12345db").

    -   Klik på databasen, i den nye pop op-pannel, du kan finde navnet på databaseserveren øverst. Din database server navn navnet skal være **"Din løsningsnavn + tilfældigt nummer + 'database.windows.net,1433'"** (f.eks. "mytest12345.database.windows.net,1433").

    -   Database **brugernavn** og **adgangskode** er den samme som brugernavnet og adgangskode tidligere optog under installationen af løsningen.

2.  Opdatere datakilden til kolde sti Power BI-fil
    -  Sørg for, at du har installeret den nyeste version af [Power BI desktop](https://powerbi.microsoft.com/desktop).

    -   Dobbeltklik på filen **' Power BI Template\DemandForecastPowerBI.pbix'** i mappen **"DemandForecastingDataGeneratorv1.0"** du har hentet. De indledende visuelle effekter er baseret på dummy data. **Note:** Hvis du får en fejlmeddelelse Massage med, Kontrollér, at har du installeret den nyeste version af Power BI Desktop.

        Når du åbner det, øverst på fil, skal du klikke på **' redigere forespørgsler '**. Dobbeltklik på i pop op-vindue, **'Kilde'** i højre panel.
    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

    -   Erstat **"Server"** og **"Database"** med dine egne server og databasenavne i pop op-vinduet, og klik derefter på **"OK"**. Kontrollér, at du angiver port 1433 (**YourSolutionName.database.windows.net, 1433**) for servernavn. Ignorer advarsler, der vises på skærmen.

    -   I den næste pop op-vinduet, får du vist to muligheder i venstre rude (**Windows** og **Database**). Klik på **"Database"**, skal du udfylde **"Brugernavn"** og **"Adgangskode"** (dette er det brugernavn og adgangskode, du har angivet, når du først udrulles løsningen og oprettet en Azure SQL-database). ***Vælg hvilken niveau for at anvende indstillingerne til***, Markér niveau Databaseindstilling. Klik derefter på **"Tilslut"**.

    -   Når du er automatiseret tilbage til den forrige side, kan du lukke vinduet. En meddelelse der pop ud - Klik på **Anvend**. Endelig skal du klikke på knappen **Gem** for at gemme ændringerne. Power BI filen har nu oprettet forbindelse til serveren. Hvis dine visualiseringer er tomme, skal du kontrollere du fjerne markeringen i de visuelle effekter til visualisering af alle dataene ved at klikke på ikonet viskelæder på det øverste højre hjørne af forklaringer. Bruge opdateringsknappen til at afspejle nye data på de visuelle effekter. Først, får du kun vist oprindelsesdata påvirker dine visualiseringer som data factory er planlagt til at opdatere alle 3 timer. Efter 3 timer, vil du se nye prognoser afspejles i dine visualiseringer, når du opdaterer dataene.

3. (Valgfrit) Udgiv dashboardet kolde sti til [Power BI online](http://www.powerbi.com/). Bemærk, at dette trin skal indeholde et Power BI-konto (eller Office 365-konto).

    -   Klik på **"Udgiv"** og få sekunder senere vises et vindue viser "Publicering til Power BI succes!" med et grønt flueben. Klik på linket under "Åbn demoprediction.pbix i Power BI". Til at finde detaljerede oplysninger under [Udgiv fra Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Oprette et nyt dashboard: Klik på den **+** log ud for afsnittet **Dashboards** i venstre rude. Skriv navnet "Demand prognoser Demo" til denne nye dashboard.

    -   Når du åbner rapporten, skal du klikke på ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png) til at fastgøre alle visualiseringer til dit dashboard. Detaljerede oplysninger finder du [Fastgør et felt til et Power BI-dashboard fra en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
        Gå til dashboard-siden og justere størrelsen og placeringen af dine visualiseringer og redigere deres titler. For at finde detaljerede oplysninger om, hvordan du redigerer dine felter skal du se [redigere en side om side - tilpasning af størrelsen, Flyt, Omdøb, pinkode, slette, kan du tilføje link](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Her er et eksempeldashboard med nogle kolde sti visualiseringer fastgjort til den.

        ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Valgfrit) Planlægge opdatering af datakilden.
    -     Tidsplan opdatere data, du holder musen over datasættet **EnergyBPI færdig** , skal du klikke på ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png) og derefter vælge **Opdater tidsplan**.
    **Note:** Hvis du får vist en advarsel om massageapparater, klik på **Rediger legitimationsoplysninger** , og Sørg for, at er legitimationsoplysningerne database den samme som dem, der er beskrevet i trin 1.

    ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

    -   Udvid sektionen **Tidsplan opdatere** . Aktivere "holde dataene opdateret".

    -   Planlægge opdateringen baseret på dine behov. Du kan finde flere oplysninger, se [dataopdatering i Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).


## <a name="how-to-delete-your-solution"></a>**Sådan sletter du din løsning**
Sørg for, at du stopper data generator, når du bruger ikke aktivt løsningen, som kører data generator vil betale højere omkostninger. Slet løsningen, hvis du ikke bruger den. Slette din løsning, slettes alle komponenter, der er klargjort i dit abonnement, da du installerede løsningen. Slette løsning Klik på løsningsnavnet på din i panelet til venstre på skabelonen, løsning, og klik på Slet.

## <a name="cost-estimation-tools"></a>**Omkostning skøn værktøjer**

Der findes følgende to værktøjer kan hjælpe dig med bedre at forstå de samlede omkostninger, der er involveret i kører Demand prognoser for energi løsning skabelon i dit abonnement:

-   [Microsoft Azure omkostninger-Estimator værktøj (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure omkostninger Estimator værktøj (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Godkendelser**
I denne artikel er oprettet af data scientist Yijing Chen og software engineering Qiu Min hos Microsoft.
