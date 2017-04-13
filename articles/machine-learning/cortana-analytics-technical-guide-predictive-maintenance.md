<properties
    pageTitle="Teknisk vejledning til skabelonen Cortana Intelligence løsning til skønnet vedligeholdelse i aerospace og andre virksomheder | Microsoft Azure"
    description="En teknisk vejledning til skabelonen løsning med Microsoft Cortana Intelligence til skønnet vedligeholdelse i aerospace, funktioner og transport."
    services="cortana-analytics"
    documentationCenter=""
    authors="fboylu"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cortana-analytics"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="fboylu" />

# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace-and-other-businesses"></a>Teknisk vejledning til skabelonen Cortana Intelligence løsning til skønnet vedligeholdelse i aerospace og andre virksomheder

## <a name="acknowledgements"></a>**Godkendelser**
I denne artikel er oprettet af data forskere Yan Zhang Gauher Shaheen, Fidan Boylu Uz og software engineering Dan Grecoe hos Microsoft.

## <a name="overview"></a>**Oversigt**

Løsning skabeloner er designet til at fremskynde processen med at opbygge en E2E demo oven på Cortana Intelligence pakke. En udløst skabelon skal Klargør dit abonnement på nødvendige Cortana Intelligence-komponenter og oprette relationer mellem dem.. Det også forhåndsudfylder data rørledning med eksempeldata, der er dannet ud fra et data generator-program, du vil hente og installere på din lokale computer, når du installerer skabelonen løsning. De data, der er dannet ud fra generatoren vil hydrate data pipeline og begynde at generere machine learning prognoser, kan derefter visualiseres på dashboardet til Power BI. Installationsprocessen vejleder dig gennem flere trin til at konfigurere din løsning legitimationsoplysninger. Kontrollér, at du registrerer disse legitimationsoplysninger som løsningsnavn, brugernavn og adgangskode, du angiver under installationen.  

Formålet med dette dokument er at forklare referencearkitektur og forskellige komponenter, der er klargjort i dit abonnement som en del af denne skabelon til en løsning. Dokumentet også taler om, hvordan du erstatte eksempeldataene med rigtige data af dine egne for at kunne se indsigt og prognoser fra dine egne data. Desuden dokumentet i denne artikel beskrives delene af skabelonen løsning, der skal ændres, hvis du vil tilpasse løsningen med dine egne data. Oplysninger om, hvordan du opretter Power BI-dashboardet for denne skabelon til en løsning er angivet i slutningen.

>[AZURE.TIP] Du kan downloade og udskrive en [PDF-versionen af dette dokument](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).

## <a name="big-picture"></a>**Overblik**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Når løsningen er installeret, aktiveres forskellige Azure tjenester i Cortana Analytics pakke (*det vil sige* begivenhed Hub Stream Analytics, HDInsight, Data Factory, Machine Learning, *osv.*). Arkitektur diagrammet ovenfor viser på højt niveau, hvordan skønnet vedligeholdelsen for Aerospace løsning skabelon er opbygget fra til slut. Du vil kunne undersøge disse tjenester på portalen azure ved at klikke på dem på den løsning skabelon diagram, der er oprettet med installationen af løsningen med undtagelse af HDInsight, som denne tjeneste er klargjort efter behov, når de relaterede pipeline aktiviteter er påkrævet for at køre og slettede bagefter.
Du kan hente en [fuld størrelse version af diagrammet](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

I følgende afsnit beskrives hver enkelt tekststykke.

## <a name="data-source-and-ingestion"></a>**Datakilde og indtagelse**

### <a name="synthetic-data-source"></a>Korte datakilde

For denne skabelon genereres den datakilde, der bruges i en på computeren, som du vil downloade og køre lokalt efter vellykket installation. Du kan finde vejledningen for at hente og installere dette program på værktøjslinjen Egenskaber, når du vælger den første node kaldet skønnet vedligeholdelse Data Generator løsning skabelon diagrammet. Dette program feeds tjenesten [Azure begivenhed Hub](#azure-event-hub) med datapunkter eller begivenheder, der skal bruges i resten af strømmen løsning. Denne datakilde består af eller afledt af offentligt tilgængelige data fra [NASA datalager](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) ved hjælp af [turbofanmotorer program er forringet simulering datasæt](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan).

Generering af programmet begivenhed udfylder Azure begivenhed hubben kun mens det kører på computeren.

### <a name="azure-event-hub"></a>Azure begivenhed hub

Tjenesten [Azure begivenhed Hub](https://azure.microsoft.com/services/event-hubs/) er modtageren af input fra den korte datakilde, der er beskrevet ovenfor.

## <a name="data-preparation-and-analysis"></a>**Forberedelse af data og analyse**


### <a name="azure-stream-analytics"></a>Azure Stream Analytics

Tjenesten [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) bruges til at give i realtid analytics på input strømmen fra tjenesten [Azure begivenhed Hub](#azure-event-hub) og udgive resultaterne til et [Power BI](https://powerbi.microsoft.com) -dashboard, samt arkivering alle rå indgående begivenheder til tjenesten [Azure-lager](https://azure.microsoft.com/services/storage/) til senere behandling af [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -tjenesten.

### <a name="hd-insights-custom-aggregation"></a>HD indsigt brugerdefinerede sammenlægning

Azure HD indsigt tjenesten bruges til at køre [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts (orchestrated af Azure Data Factory) for at give sammenlægninger på de rå hændelser, der blev arkiveret ved hjælp af tjenesten Azure Stream analyser.

### <a name="azure-machine-learning"></a>Azure Machine Learning

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) tjenesten bruges (orchestrated af Azure Data Factory) til at forudsige på den resterende levetid (RUL) på en bestemt fly-program, der er givet modtaget input.

## <a name="data-publishing"></a>**Publicering af data**


### <a name="azure-sql-database-service"></a>Azure SQL Database-tjenesten

Tjenesten [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/) bruges til at gemme (administreres af Azure Data Factory) prognoser modtaget af tjenesten Azure Machine Learning, som skal blive brugt i [Power BI](https://powerbi.microsoft.com) -dashboard.

## <a name="data-consumption"></a>**Data forbrug**

### <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com) -tjenesten bruges til at vise et dashboard, der indeholder sammenlægninger og beskeder, der leveres af tjenesten [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) samt RUL prognoser gemt i [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/) , der blev oprettet ved hjælp af tjenesten [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) . Se afsnittet nedenfor for oplysninger om, hvordan du opretter Power BI-dashboardet for denne skabelon til en løsning.

## <a name="how-to-bring-in-your-own-data"></a>**Hvordan du kan flytte dine egne data**

Dette afsnit beskrives, hvordan du tage dine egne data til Azure, og hvilke områder kræver ændringer for de data, du sætter i denne arkitektur.

Det sandsynligvis, at en hvilken som helst du tage datasæt ville svarer til datasættet bruges af [turbofanmotorer program er forringet simulering stokastisk variabel](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) bruges til denne skabelon til en løsning. Forstå dine data og kravene vil blive afgørende i, hvordan du ændrer denne skabelon til at arbejde med dine egne data. Hvis dette er dit første visning til Azure Machine Learning-tjenesten, kan du få en introduktion til den ved hjælp af eksemplet i [hvordan du kan oprette din første forsøg](machine-learning-create-experiment.md).

De følgende afsnit vil diskutere sektionerne i den skabelon, kræver ændringer, når der introduceres et nyt datasæt.

### <a name="azure-event-hub"></a>Azure begivenhed Hub

Tjenesten Azure begivenhed Hub er meget generisk, så dataene kan sendes til hub i csv- eller JSON-format. Ingen speciel behandling forekommer i Azure begivenhed-Hub, men det er vigtigt, at du forstår de data, der indføres i den.

Dette dokument beskriver ikke, hvordan indtager dataene, men du kan nemt sende begivenheder eller data til en Azure begivenhed Hub ved hjælp af begivenhed Hub API.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

Tjenesten Azure Stream Analytics bruges til at give i realtid analytics ved at læse fra datastreams og skriver data til en hvilken som helst antal kilder.

Skønnet vedligeholdelse til Aerospace løsning skabelon består forespørgslen Azure Stream Analytics af fire underordnede forespørgsler, hver arket begivenheder fra tjenesten Azure begivenhed Hub og har output til fire forskellige placeringer. Disse output består af tre Power BI-datasæt og én placering til Azure-lager.

Azure Stream Analytics-forespørgsel kan findes ved at:

-   Sådan logger du ind på Azure-portalen

-   Finde stream analytics-job ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png) , der blev oprettet, da løsningen blev installeret (*f.eks.*, **maintenancesa02asapbi** og **maintenancesa02asablob** for løsningen skønnet vedligeholdelse)

-   Hvis du vælger

    -   ***Input*** til at få vist forespørgsel input

    -   ***Forespørgsel*** til at få vist selve forespørgslen

    -   ***Output*** til at få vist forskellige output

Oplysninger om Azure Stream Analytics forespørgsel byggeri kan findes i [Stream Analytics forespørgsel Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) på MSDN.

I denne løsning output forespørgsler tre datasæt med nær realtid analytics oplysninger om indgående datastrømmen til et Power BI-dashboard, der er angivet som en del af denne skabelon til en løsning. Da der er implicit viden om indgående dataformat, skal disse forespørgsler ændres afhængigt af din dataformat.

Forespørgslen i den anden stream analytics jobbet **maintenancesa02asablob** blot skriver alle [Begivenhed Hub](https://azure.microsoft.com/services/event-hubs/) begivenheder til [Azure-lager](https://azure.microsoft.com/services/storage/) og derfor kræver ingen ændring uanset din dataformat, som den fulde hændelsesoplysninger streames til lagerplads.

### <a name="azure-data-factory"></a>Azure Data Factory

Tjenesten [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orchestrates flytning og behandling af data. Data factory består af tre [rørledninger](../data-factory/data-factory-create-pipelines.md) , flytte og behandle data ved hjælp af forskellige teknologier i skønnet vedligeholdelse til Aerospace løsning skabelon.  Du kan få adgang til dine data factory ved at åbne den noden Data Factory i bunden af løsning skabelon diagram, der er oprettet med installationen af løsningen. Dette kommer du til data factory på Azure-portalen. Hvis du ser fejl under dit datasæt, kan du ignorere dem, som de er på grund af data factory der blev installeret, inden data generator er blevet startet. Disse fejl forhindrer ikke dine data factory i at fungere.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Dette afsnit beskrives de nødvendige [rørledninger](../data-factory/data-factory-create-pipelines.md) og [aktiviteter](../data-factory/data-factory-create-pipelines.md) , der er indeholdt i [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Nedenfor vises diagramvisning løsningens.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

To rørledninger af denne factory indeholder [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scripts, som bruges til at dele og aggregere dataene. Når angivet, vil scriptene være placeret i kontoen [Azure-lager](https://azure.microsoft.com/services/storage/) , der er oprettet under installationen. Deres placering bliver: maintenancesascript\\\\script\\\\hive\\ \\ (eller https://[Your løsning name].blob.core.windows.net/maintenancesascript).

Svarer til [Azure Stream Analytics](#azure-stream-analytics-1) -forespørgsler, [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scriptene har implicit viden om indgående dataformat, disse forespørgsler skal ændres afhængigt af dine data format og [funktion teknisk](machine-learning-feature-selection-and-engineering.md) krav.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*

Denne [pipeline](../data-factory/data-factory-create-pipelines.md) indeholder en enkelt aktivitet - en [HDInsightHive](../data-factory/data-factory-hive-activity.md) aktivitet ved hjælp af en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , der kører et [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script for at dele de data, der placerer i [Azure-lager](https://azure.microsoft.com/services/storage/) under [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) -job.

[Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scriptet til denne leverandør opgave er ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*

Denne [pipeline](../data-factory/data-factory-create-pipelines.md) indeholder flere aktiviteter, og hvis resultat slutningen er scored prognoser fra [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperimentere, der er knyttet til denne skabelon til en løsning.

De aktiviteter, der er indeholdt i dette er:

-   [HDInsightHive](../data-factory/data-factory-hive-activity.md) aktivitet ved hjælp af en [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) , der kører et [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) script for at udføre sammenlægninger og funktion teknisk nødvendige til [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) forsøget.
    [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) scriptet til denne leverandør opgave er ***PrepareMLInput.hql***.

-   [Kopiér](https://msdn.microsoft.com/library/azure/dn835035.aspx) aktivitet, der flytter resultaterne fra [HDInsightHive](../data-factory/data-factory-hive-activity.md) aktiviteten til en enkelt [Azure-lager](https://azure.microsoft.com/services/storage/) blob, der kan være adgang ved [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet.

-   [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) aktivitet, der kalder [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) eksperimentere, hvilket resulterer i de resultater, der er lagt i en enkelt blob [Azure-lager](https://azure.microsoft.com/services/storage/) .

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*

Denne [pipeline](../data-factory/data-factory-create-pipelines.md) indeholder en enkelt aktivitet - aktivitet, en [kopi](https://msdn.microsoft.com/library/azure/dn835035.aspx) , der flytter resultaterne af [Azure Machine Learning](#azure-machine-learning) eksperimentere fra ***MLScoringPipeline*** til [Azure SQL-Database](https://azure.microsoft.com/services/sql-database/) , der blev klargjort som en del af løsning skabelon installationen.

### <a name="azure-machine-learning"></a>Azure Machine Learning

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) forsøget bruges til denne løsning skabelon indeholder den resterende nyttige levetid (RUL) af en fly-program. Der er specifikke for datasættet consumed og derfor kræver ændres eller erstattes, der er specifikke for de data, der er hentet ind i.

Finde oplysninger om hvordan Azure Machine Learning forsøget er blevet oprettet, [skønnet vedligeholdelse: trin 1 af 3, forberedelse af data og funktion teknisk](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>**Overvåge forløbet**
 Når Data Generator er startet, pipeline begynder at hente hydrated og de forskellige komponenter i din løsning start konkurrencen til handling følgende kommandoerne udstedt af Data Factory. Der er to måder, du kan overvåge pipeline.

1. En af Stream Analytics jobbet skriver indgående rækkedata til blob-lager. Hvis du klikker på Blob-lager komponent i din løsning fra skærmbilledet du er blevet installeret løsningen og klik derefter på Åbn i højre panel, kommer du til [Administration af portal](https://portal.azure.com/). Når der, klikke på BLOB. Du får vist en liste over beholdere i panelet næste. Klik på **maintenancesadata**. I panelet næste vises mappen **RAADATA** . I mappen RAADATA skal du se mapper med navne som time = 17, time = 18 osv. Hvis du får vist disse mapper, indikerer, at den rækkedata er korrekt der oprettes på din computer og gemmes i blob-lager. Du bør se csv-filer, som skal have endelig størrelser i MB i disse mapper.

2. Det sidste trin af rørledninger, er at skrive data (fx prognoser fra machine learning) til SQL-Database. Det være nødvendigt at vente op til tre timer for dataene, der vises i SQL-Database. Der er en metode til at overvåge, hvor meget data findes i dine SQL-Database via [azure portal](https://manage.windowsazure.com/). Find SQL-DATABASER på panelet til venstre ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png) og klikke på den. Find din database **pmaintenancedb** og klik på den derefter. Klik på ADMINISTRER på den næste side nederst

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

    Her kan du klikke på ny forespørgsel og forespørgsel for antallet af rækker (fx Vælg count(*) fra PMResult). Efterhånden som databasen vokser, øge antallet af rækker i tabellen skal.


## <a name="power-bi-dashboard"></a>**Dashboard i Power BI**

### <a name="overview"></a>Oversigt

Dette afsnit beskrives, hvordan konfigurere dashboard i Power BI til at visualisere dine realtid data fra Azure stream analytics (varmt vej) samt batchen forudsigelse resultater fra Azure maskine læringssti (kolde).

### <a name="setup-cold-path-dashboard"></a>Konfiguration af kolde sti dashboard

I kolde sti data pipeline er væsentlige målet at få skønnet RUL (resterende levetid) af hver fly-program, når det er afsluttet flight (cyklus). Forudsigelse resultatet opdateres hver 3 timer for forudsigelser fly-programmer, der er færdig med en flight under de sidste 3 timer.

Power BI opretter forbindelse til en Azure SQL-database som datakilde, hvor resultaterne Tekstfuldførelse er gemt. Bemærk: 1) på anvender din løsning, en reelle forudsigelse vises i databasen i 3 timer.
Filen pbix, der fulgte med Generator overførslen indeholder nogle oprindelsesdata, så du kan oprette Power BI-dashboard det samme. 2) i dette trin er de nødvendige forudsætninger at downloade og installere den gratis software [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

Følgende guider dig om, hvordan du forbinde pbix filen til den SQL-Database, der blev spundet op på tidspunktet for løsningsinstallation, der indeholder data (*f.eks*. forudsigelse resultater) til visualisering.

1.  Få legitimationsoplysningerne database.

    Du skal **databaseservernavnet, databasenavnet, brugernavn og adgangskode** før du går videre til næste trin. Her er trinnene til at hjælpe dig, hvordan du kan finde dem.

    -   Når **Azure SQL Database** på diagrammet løsning skabelon bliver grøn, klikke på den, og klik derefter på **'Åbn'**.

    -   Du får vist et nyt fanen/browservindue som viser siden Azure-portalen. Klik på **ressourcen-grupper** i panelet til venstre.

    -   Vælg det abonnement, du bruger til implementering af løsningen, og vælg derefter **' YourSolutionName\_ResourceGroup'**.

    -   I den nye pop op-panel, skal du klikke på den ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png) ikon for at få adgang til din database. Databasenavnet på din er ud for afkrydsningsfeltet dette ikon (*f.eks.*, **'pmaintenancedb'**), og **databaseservernavnet** står anført under egenskaben Server name og bør se nogenlunde sådan **YourSoutionName.database.windows.net**.

    -   Database **brugernavn** og **adgangskode** er den samme som brugernavnet og adgangskode tidligere optog under installationen af løsningen.

2.  Opdatere datakilden til filen kolde sti rapport med Power BI Desktop.

    -   I mappen på din PC, hvor du har downloadet og pakket filen Generator, skal du dobbeltklikke på den **PowerBI\\PredictiveMaintenanceAerospace.pbix** fil. Hvis du ser en hvilken som helst advarselsmeddelelser, når du åbner filen, skal du ignorere dem. Øverst på fil, skal du klikke på **' redigere forespørgsler '**.

        ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

    -   Du får vist to tabeller, **RemainingUsefulLife** og **PMResult**. Vælg den første tabel, og klik på ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png) ud for **'Kilde'** under **Udførte trin** på panelet højre **' forespørgselsindstillinger '** . Ignorere en advarsel om meddelelser, der vises.

    -   Erstatte **'Server'** og **'Database'** med dine egne server og databasenavne i pop op-vinduet, og klik derefter på **'OK'**. Kontrollér, at du angiver port 1433 (**YourSoutionName.database.windows.net, 1433**) for servernavn. Lad feltet Database som **pmaintenancedb**. Ignorer advarsler, der vises på skærmen.

    -   I den næste pop op-vinduet, får du vist to muligheder i venstre rude (**Windows** og **Database**). Klik på **'Database'**, skal du udfylde dine **'Username'** og **'Adgangskode'** (dette er det brugernavn og adgangskode, du har angivet, når du først udrulles løsningen og oprettet en Azure SQL-database). ***Vælg hvilken niveau for at anvende indstillingerne til***, Markér niveau Databaseindstilling. Klik derefter på **'Opret forbindelse'**.

    -   Klik på den anden tabel **PMResult** og klik derefter på ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png) ud for **'Kilde'** under **Udførte trin** på **' forespørgselsindstillinger '** højre panel og opdatere navnene på den server og database som i ovenstående trin, og klik på OK.

    -   Når du er automatiseret tilbage til den forrige side, kan du lukke vinduet. En meddelelse der pop ud - Klik på **Anvend**. Endelig skal du klikke på knappen **Gem** for at gemme ændringerne. Power BI filen har nu oprettet forbindelse til serveren. Hvis dine visualiseringer er tomme, skal du kontrollere du fjerne markeringen i de visuelle effekter til visualisering af alle dataene ved at klikke på ikonet viskelæder på det øverste højre hjørne af forklaringer. Bruge opdateringsknappen til at afspejle nye data på de visuelle effekter. Først, får du kun vist oprindelsesdata påvirker dine visualiseringer som data factory er planlagt til at opdatere alle 3 timer. Efter 3 timer, vil du se nye prognoser afspejles i dine visualiseringer, når du opdaterer dataene.

3.  (Valgfrit) Udgiv dashboardet kolde sti til [Power BI online](http://www.powerbi.com/). Bemærk, at dette trin skal indeholde et Power BI-konto (eller Office 365-konto).

    -   Klik på **'Udgiv'** og få sekunder senere vises et vindue viser "Publicering til Power BI succes!" med et grønt flueben. Klik på linket under "Åbn PredictiveMaintenanceAerospace.pbix i Power BI". Til at finde detaljerede oplysninger under [Udgiv fra Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Oprette et nyt dashboard: Klik på den **+** log ud for afsnittet **Dashboards** i venstre rude. Skriv navnet "Skønnet vedligeholdelse Demo" til denne nye dashboard.

    -   Når du åbner rapporten, skal du klikke på ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png) til at fastgøre alle visualiseringer til dit dashboard. Detaljerede oplysninger finder du [Fastgør et felt til et Power BI-dashboard fra en rapport](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
    Gå til dashboard-siden og justere størrelsen og placeringen af dine visualiseringer og redigere deres titler. For at finde detaljerede oplysninger om, hvordan du redigerer dine felter skal du se [redigere en side om side - tilpasning af størrelsen, Flyt, Omdøb, pinkode, slette, kan du tilføje link](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Her er et eksempeldashboard med nogle kolde sti visualiseringer fastgjort til den.  Dine tal på de visuelle effekter kan være forskellige afhængigt af hvor længe de kører din data generator.
    <br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png)
<br/>
    -   Planlæg opdatere data, du holder musen over **PredictiveMaintenanceAerospace** datasættet, skal du klikke på ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) og derefter vælge **Opdater tidsplan**.
<br/>
        **Note:** Hvis du får vist en advarsel om massageapparater, klik på **Rediger legitimationsoplysninger** , og Sørg for, at er legitimationsoplysningerne database den samme som dem, der er beskrevet i trin 1.
<br/>
    ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png)
<br/>
    -   Udvid sektionen **Tidsplan opdatere** . Aktivere "holde dataene opdateret".
    <br/>
    -   Planlægge opdateringen baseret på dine behov. Du kan finde flere oplysninger, se [dataopdatering i Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Konfiguration af varmt sti dashboard

Følgende trin vejleder dig, hvordan du visualisere realtid data output fra Stream Analytics-job, som er blevet oprettet på tidspunktet for installationen af løsningen. En [Power BI online](http://www.powerbi.com/) konto skal du udføre følgende trin. Hvis du ikke har en konto, kan du [oprette en](https://powerbi.microsoft.com/pricing).

1.  Tilføje Power BI output i Azure Stream Analytics (ASA).

    -  Skal du følge vejledningen i [Azure Stream analyser og Power BI: et realtid analytics dashboard til realtid synligheden af streaming data](stream-analytics-power-bi-dashboard.md) til konfiguration af output fra Azure Stream Analytics tingene som dit Power BI-dashboard.
    - ASA forespørgsel har tre output, som er **aircraftmonitor**, **aircraftalert**og **flightsbyhour**. Du kan se forespørgslen ved at klikke på forespørgselsfanen. Svarer til hver af disse tabeller, skal du føje output til ASA. Når du tilføjer den første output (*fx* **aircraftmonitor**) kontrollere **Output Alias**, **Datasæt navn** og **Tabelnavnet** er den samme (**aircraftmonitor**). Gentag trinnene for at tilføje afgang for **aircraftalert**og **flightsbyhour**. Når du har tilføjet alle tre output tabeller og ASA jobbet i gang, skal du få en bekræftelsesmeddelelse (*f.eks.*, "Start stream analytics jobbet maintenancesa02asapbi lykkedes").

2. Log på [Power BI online](http://www.powerbi.com)

    -   På panelet til venstre datasæt sektion i min arbejdsområde, skal ***DATASÆT*** navne **aircraftmonitor**, **aircraftalert**og **flightsbyhour** vises. Dette er de streaming data, du har trykket fra Azure Stream Analytics på det forrige trin. Datasæt **flightsbyhour** kan ikke vises på samme tid som de andre to datasæt på grund af SQL-forespørgslen bagved art. Men det skal vises efter en time.
    -   Kontrollér, at ruden ***visuelle effekter*** er åben, og der vises på i højre side af skærmen.

3. Når du har de data, der flyder i Power BI, kan du begynde at visualisere streaming dataene. Under fastgøres et eksempeldashboard med visualiseringer nogle varmt stien til den. Du kan oprette andre dashboardfelter, der er baseret på relevante datasæt. Dine tal på de visuelle effekter kan være forskellige afhængigt af hvor længe de kører din data generator.


    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

4. Her er nogle trin til at oprette et af felterne ovenfor – den "flåden visning af føler 11 kontra grænseværdi 48,26" side om side:

    -   Klik på datasæt **aircraftmonitor** på panelet til venstre datasæt sektion.

    -   Klik på ikonet **Kurvediagram** .

    -   Klik på **behandles** i ruden **felter** , så den vises under "Akse" i ruden **visualiseringer** .

    -   Klik på "s11" og "s11\_besked" så de begge vises under "Værdier". Klik på den lille pil ud for **s11** og **s11\_besked**, ændre "Sum" til "Middel".

    -   Klik på **Gem** øverst, og Giv rapporten navnet "aircraftmonitor". Rapporten med navnet "aircraftmonitor" vises i afsnittet **rapporter** i **navigationsruden i venstre side** .

    -   Klik på ikonet **Pinkode visuelle** på det øverste højre hjørne af dette kurvediagram. Et "Fastgør til Dashboard" vindue viser muligvis for dig at vælge et dashboard. Vælg "Skønnet vedligeholdelse Demo", og derefter klikke på "Fastgør".

    -   Hold musen over dette felt på dashboardet til skal du klikke på ikonet "Rediger" i øverste højre hjørne til at ændre titlen til "Flåden visning af føler 11 kontra grænseværdi 48,26" og undertitel til "Gennemsnit på tværs af flåden over tid".

## <a name="how-to-delete-your-solution"></a>**Sådan sletter du din løsning**
Sørg for, at du stopper data generator, når du bruger ikke aktivt løsningen, som kører data generator vil betale højere omkostninger. Slet løsningen, hvis du ikke bruger den. Slette din løsning, slettes alle komponenter, der er klargjort i dit abonnement, da du installerede løsningen. Slette løsning Klik på løsningsnavnet på din i panelet til venstre på skabelonen, løsning, og klik på Slet.

## <a name="cost-estimation-tools"></a>**Omkostninger skøn værktøjer**

Der findes følgende to værktøjer kan hjælpe dig med bedre at forstå de samlede omkostninger, der er involveret i kører skønnet vedligeholdelse til Aerospace løsning skabelon i dit abonnement:

-   [Microsoft Azure omkostninger-Estimator værktøj (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure omkostninger Estimator værktøj (desktop)](http://www.microsoft.com/download/details.aspx?id=43376)
