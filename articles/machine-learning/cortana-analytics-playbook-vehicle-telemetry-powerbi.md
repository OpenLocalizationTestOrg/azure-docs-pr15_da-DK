<properties 
    pageTitle="Biltype telemetri analytics løsning skabelon PowerBI Dashboard konfigurationsvejledning | Microsoft Azure" 
    description="Bruge funktionerne i Cortana Intelligence til at få realtid og skønnet viden på biltype tilstand og bil vaner." 
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
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-template-powerbi-dashboard-setup-instructions"></a>Biltype telemetri analytics løsning skabelon PowerBI Dashboard konfigurationsvejledning

I denne **menu** links til kapitlerne i denne playbook. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


Løsningen biltype Telemetri Analytics viser, hvordan bil forhandlere, bil producenter og forsikringsselskaber kan udnytte mulighederne i Cortana Intelligence til at få realtid og skønnet viden på biltype sundhed og fører vaner til drev forbedringer i området af kunde oplever, R & D og marketingkampagner. Dette dokument indeholder trinvise instruktioner til, hvordan du kan konfigurere PowerBI rapporter og dashboard når løsningen er installeret i dit abonnement. 


## <a name="prerequisites"></a>Forudsætninger
1.  Installere biltype Telemetri Analytics løsningen ved at navigere til [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.  [Installere Microsoft Power BI Desktop](http://www.microsoft.com/download/details.aspx?id=45331)
3.  En [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/). Hvis du ikke har et Azure-abonnement, Introduktion til Azure gratis abonnement
4.  Microsoft PowerBI konto
    

## <a name="cortana-intelligence-suite-components"></a>Cortana Intelligence Suite-komponenter
Følgende Cortana Intelligence tjenester installeres som en del af skabelonen biltype Telemetri Analytics løsning i dit abonnement.

- **Begivenhed Hubs** for ingesting millioner af biltype telemetri begivenheder til Azure.
- **Stream analytisk**s for at få realtid indsigt på biltype sundhed og fortsætter disse data til længerevarende opbevaring for bedre batchen analyser.
- **Machine Learning** til anomali registrering i realtid og batchbehandling til at få skønnet indsigt.
- **HDInsight** opgraderede for at transformere data på skala
- **Data Factory** håndterer organisering, planlægning, ressourcestyring og overvågning af batchbehandling rørledning.

**Power BI** giver denne løsning et omfattende dashboard for realtidsdata og skønnet analytics visualiseringer. 

Løsningen anvender to forskellige datakilder: **simuleret biltype signaler og diagnosticering dataset** og **biltype katalog**.

Biltype telematik simulator er inkluderet som en del af denne løsning. Det udsender diagnostiske oplysninger og signaler svarer til tilstanden for biltype og bil mønster på et bestemt tidspunkt. 

Biltype kataloget er en reference datasæt med VIN til model-tilknytning


## <a name="powerbi-dashboard-preparation"></a>PowerBI Dashboard forberedelse

### <a name="deployment"></a>Installation

Når installationen er fuldført, skal du se i det følgende diagram med alle disse komponenter, der er markeret med grøn. 

- Klik på pilen øverst til højre for grøn knuderne for at gå til de tilsvarende tjenester vil validere, om alle disse har installeret.
- For at hente data simulator-pakke, skal du klikke på pilen øverst til højre på noden **Biltype telematik Simulator** . Gem og udtrække filer lokalt på din computer. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Nu er du klar til at konfigurere PowerBI dashboard med omfattende visualiseringer til at få realtid og skønnet viden om biltype tilstand og bil vaner. Det tager om 45 minutter til at oprette alle rapporter og konfigurere dashboardet i timen. 


### <a name="setup-power-bi-real-time-dashboard"></a>Konfigurere realtid Dashboard i Power BI

**Oprette simuleret data**

1. På din lokale computer, gå til den mappe, hvor du udpakkede pakken biltype telematik Simulator![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.  Køre programmet ***CarEventGenerator.exe***.
3.  Det udsender diagnostiske oplysninger og signaler svarer til tilstanden for biltype og bil mønster på et bestemt tidspunkt. Dette er blevet publiceret på en Azure begivenhed Hub-forekomst, der er konfigureret som en del af din installation.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
     
**Starte programmet, realtid dashboard**

Løsningen indeholder et program, der genererer en realtid dashboard i PowerBI. Dette program lytter til en begivenhed Hub-forekomst, hvorfra Stream Analytics publicerer begivenheder løbende. For hver begivenhed, der modtager dette program, behandler det data med et svar med Machine Learning anmodning vurdering slutpunkt. Den resulterende datasæt er udgivet på PowerBI opslagsnål API'er til visualisering. 

Sådan henter du programmet:

1.  Klik på noden PowerBI på diagramvisning og klikke på **Hent realtid Dashboard programmet**' link i egenskabsruden.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.  Udtrække og gemme programmet lokalt![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.  Køre programmet **RealtimeDashboardApp.exe**
4.  Gyldige legitimationsoplysninger til Power BI, logge på og klik på **Acceptér**
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### <a name="configure-powerbi-reports"></a>Konfigurere PowerBI rapporter
Realtid rapporter og dashboardet tage om 30-45 minutter for at gennemføre. Gå til [http://powerbi.com](http://powerbi.com) og logon.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Der oprettes et nyt datasæt i Power BI. Klik på **ConnectedCarsRealtime** datasættet.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Gem rapporten tom ved hjælp af **Ctrl + s**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Angiv rapportnavn *biltype Telemetri Analytics realtid - rapporter*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Realtid rapporter
Der er tre realtid rapporter i denne løsning:

1.  Køretøjer i handlingen
2.  Køretøjer, der kræver vedligeholdelse
3.  Køretøjer sundhed statistik

Du kan vælge at konfigurere alle tre realtid rapporter eller stoppe efter en hvilken som helst fase, og gå videre til næste afsnit for at konfigurere batchen rapporter. Vi anbefaler, at du kan oprette alle de tre rapporter til visualisering af de fulde indsigt i realtid stien til løsningen.  

### <a name="1-vehicles-in-operation"></a>1. køretøjer i handlingen
  
Dobbeltklik på **side 1** , og Omdøb den til "Køretøjer i handlingen"  
    ![Forbindelse biler - køretøjer i handlingen](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Vælg **vin** felt fra **felter,** og vælg visualiseringstype som **"Kort"**.  

Kortvisualisering oprettes, som vist i figur.  
    ![Forbundne biler - Vælg vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Klik på det tomme område for at tilføje ny visualisering.  

Vælg **By** og **vin** felter. Ændre visualisering til **"Kort"**. Træk **vin** i værdiområdet. Træk **By** fra felter til **forklaringsområde** .   
    ![Forbindelse biler - Kortvisualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Vælge afsnittet **format** fra **visualiseringer**, skal du klikke på **Titel** og ændre **tekst** til **"Køretøjer i handlingen efter by"**.  
    ![Forbindelse biler - køretøjer i handlingen efter by](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

Endelige visualisering ser ud, som vist i figur.    
    ![Forbundne biler - endelige visualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Klik på det tomme område for at tilføje ny visualisering.  

Vælg **By** og **vin**, Skift visualiseringstype til **Grupperet søjlediagram**. Sikre, at feltet **By** i **akse område** og **vin** i **værdi område**  

Sortere diagram ved at **"Antal vin"**  
    ![Forbundne biler - antallet af vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Ændre diagrammet **titlen** til **"Køretøjer i handlingen efter by"**  

Klik på sektionen **Formater** og derefter vælge **Data farver**, skal du klikke på **"På"** til **Vis alle**  
    ![Forbundne biler - Vis alle Data farver](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Ændre farven på individuelle by ved at klikke på farveikon.  
    ![Forbindelse biler - Skift farver](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Klik på det tomme område for at tilføje ny visualisering.  

Vælg **Grupperet søjlediagram** visualisering visualiseringer, skal du trække feltet **By** i **akse** område skal **Model** i **forklaringsområde** og **vin** i **værdi** område.  
    ![Forbundne biler - grupperet søjlediagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Forbundne biler - gengivelse](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Omarrangere alle visualisering på denne side, som vist i figur.  
    ![Forbundne biler - visualiseringer](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Rapporten "Køretøjer i handlingen" realtid er konfigureret korrekt. Du kan fortsætte med at oprette den næste realtid rapport eller stoppe her og konfigurere dashboard. 

### <a name="2-vehicles-requiring-maintenance"></a>2. kræve vedligeholdelse køretøjer
  
Klik på ![Tilføj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) for at tilføje en ny rapport skal du omdøbe den til **"køretøjer kræve vedligeholdelse"**

![Forbundne biler - køretøjer, der kræver vedligeholdelse](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Vælg **vin** felt, og Skift visualiseringstype til **kort**.  
    ![Forbundne biler - Vin Kortvisualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Vi har et felt med navnet "MaintenanceLabel" i datasættet. Dette felt kan have værdien "0" eller "1"." Det er angivet af Azure Machine Learning modellen klargjort som en del af løsningen og integreret med realtid stien. Værdien "1" Angiver en biltype kræver vedligeholdelse. 

Sådan tilføjer du et **Siden niveau** filter til at vise køretøjer data, der kræver vedligeholdelse: 

1. Træk feltet **"MaintenanceLabel"** til **Siden niveau filtre**.  
![Forbundne biler - siden niveau filtre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Klik på **Standardfiltrering** menuen Præsenter i bunden af MaintenanceLabel siden niveau Filter.  
![Forbindelse biler - Standardfiltrering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  

3.  Angive filterværdien til **"1"**    
![Forbundne biler - filterværdien](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  


Klik på det tomme område for at tilføje ny visualisering.  

Vælg **Grupperet søjlediagram** visualiseringer  
![Forbundne biler - Vind Kortvisualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Forbundne biler - grupperet søjlediagram](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Træk felt **Model** til **akse** område **Vin** til **værdi** område. Derefter sortere visualisering af **antallet af vin**.  Ændre diagrammet **Titel** til **"Køretøjer kræve vedligeholdelse af model"**  

Træk **vin** felter til **Farvemætning** Præsenter på **felter** ![felter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) afsnittet **visualisering** under fanen  
![Forbundne biler - farvemætning](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Ændre **Farver Data** i visualiseringer fra afsnittet **Format**  
Ændre farven på Minimum til: **F2C812**  
Ændre farven på maksimum til: **FF6300**  
![Forbindelse biler - farve ændres](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Forbindelse biler - nye visualisering farver](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Klik på det tomme område for at tilføje ny visualisering.  

Vælg **grupperet søjlediagram** visualiseringer, træk **vin** felt til **værdi** område, skal du trække feltet **By** i **akse** område. Sortere diagram ved at **"Antal vin"**. Ændre diagrammet **titlen** til **"Køretøjer kræve vedligeholdelse efter by"**   
![Forbundne biler - køretøjer, der kræver vedligeholdelse efter by](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Klik på det tomme område for at tilføje ny visualisering.  

Vælg **Flere række** kortvisualisering visualiseringer, skal du trække **Model** og **vin** til området **felter** .  
![Forbundne biler - kort med flere række](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

Omarrangere alle Visualiseringen, ser den endelige rapport således:  
![Forbundne biler - kort med flere række](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Rapporten "Køretøjer kræver reparation" realtid er konfigureret korrekt. Du kan fortsætte med at oprette den næste realtid rapport eller stoppe her og konfigurere dashboard. 

### <a name="3-vehicles-health-statistics"></a>3. køretøjer sundhed statistik
  
Klik på ![Tilføj](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) for at tilføje ny rapport skal du omdøbe den til **"køretøjer sundhed statistik"**  

Vælg **måler** visualisering visualiseringer, og derefter trække feltet **hastighed** til **værdi, minimumværdi, maksimumværdi** områder.  
![Forbundne biler - kort med flere række](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Ændre standardsammenlægningen i **hastighed** i **værdi område** til **gennemsnit** 

Ændre standardsammenlægningen i **hastighed** i **Minimum område** til **Minimum**

Ændre standardsammenlægningen i **hastighed** i **Maksimum område** til **Maksimum**

![Forbundne biler - kort med flere række](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Omdøb **Måler titel** til **"Gennemsnitlige hastighed"** 
 
![Forbundne biler - måler](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Klik på det tomme område for at tilføje ny visualisering.  

Tilføje en **måler** til **gennemsnitlige program oil**, **gennemsnitlige brændstof**og **gennemsnitlige program tempereret**på samme måde.  

Ændre standardaggregeringen af felter i hver måler som per over trin i **"Gennemsnitlige hastighed"** måle.

![Forbundne biler - målere](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Klik på det tomme område for at tilføje ny visualisering.

Vælg **kurvediagram og grupperet søjlediagram** visualiseringer, og derefter trække feltet **By** i **Delt akse**, træk **hastighed**, **tirepressure og engineoil felter** i området **Værdier i kolonnen** , ændre deres diagramtypen til **gennemsnit**. 

Træk feltet **motortemperatur** til området **Værdier for linje** , skal du ændre sammenlægning til **gennemsnit**. 

![Forbundne biler - visualiseringer felter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Ændre diagrammet **titlen** til **"gennemsnitlige hastighed, dæk tryk, program oil og program temperatur"**.  

![Forbundne biler - visualiseringer felter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Klik på det tomme område for at tilføje ny visualisering.

Vælg **træstruktur** visualisering visualiseringer skal du trække feltet **Model** til området **gruppe** , og træk feltet **MaintenanceProbability** til området **værdier** .

Ændre diagrammet **titlen** til **"Biltype modeller kræver vedligeholdelse"**.

![Forbindelse biler - Skift diagramtitel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Klik på det tomme område for at tilføje ny visualisering.

Vælg **100% stablet liggende søjlediagram** visualisering, træk feltet **By** til området **akse** , og træk **MaintenanceProbability** **RecallProbability** felter til området **værdi** .

![Forbundne biler - Tilføj ny visualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Klik på **Formatér**, Vælg **Data farver**og angive farven **MaintenanceProbability** til værdien **"F2C80F"**.

Ændre **Titel** på diagrammet til **"sandsynlighed af biltype vedligeholdelse og tilbagekalde efter by"**.

![Forbundne biler - Tilføj ny visualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Klik på det tomme område for at tilføje ny visualisering.

Vælg **Områdediagram** visualisering fra visualiseringer skal du trække feltet **Model** til området **akse** , og træk felterne **engineOil, tirepressure, hastighed og MaintenanceProbability** til området **værdier** . Ændre deres sammenlægning til **"Middel"**. 

![Forbindelse biler - sammenlægning ændringstype](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Ændre titlen på diagrammet til **"Gennemsnitlig program oil, træt tryk, hastighed og vedligeholdelse sandsynlighed af datamodel"**.

![Forbindelse biler - Skift diagramtitel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Klik på det tomme område for at tilføje ny visualisering:

1. Vælg **Punktdiagram** visualisering visualiseringer.
2. Træk feltet **Model** til området **Detaljer** og **forklaring** .
3. Træk feltet **brændstof** til området **x-aksen** , ændre sammenlægning til **gennemsnit**.
4. Træk **engineTemparature** på **y-aksen område**, ændre sammenlægning til **gennemsnit**
5. Træk feltet **vin** til området **størrelse** .


![Forbundne biler - Tilføj ny visualisering](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Ændre diagrammet **Titel** til **"Gennemsnit af brændstof, program temperatur af datamodel"**.

![Forbindelse biler - Skift diagramtitel](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Den endelige rapport ser ud, som vist nedenfor.

![Forbundne biler færdig rapport](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Fastgør visualiseringer fra rapporterne til realtid dashboard
  
Oprette en tom dashboard ved at klikke på ikonet plus ud for Dashboards. Du kan navngive den "Telemetri Analytics instrumentbrættet"

![Forbundne biler-Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Fastgøre Visualiseringen fra de ovenstående rapporter til dashboard. 
 
![Forbundne biler-Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Dashboard ser således, når der oprettes alle tre rapporter og de tilsvarende visuelle effekter er fastgjort til dashboard. Hvis du ikke har oprettet alle rapporter, kan dit dashboard ser anderledes. 

![Forbundne biler-Dashboard](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Tillykke! Du har oprettet dashboardet i realtid. Når du fortsætter med at udføre CarEventGenerator.exe og RealtimeDashboardApp.exe, skal du se live opdateringer på dashboardet. Det skal tage om 10 til 15 minutter for at benytte følgende fremgangsmåde.

 
##  <a name="setup-power-bi-batch-processing-dashboard"></a>Konfigurere Power BI batchen behandling dashboard

>[AZURE.NOTE] Det tager om to timer (fra installationen er fuldført) for start til slut batchbehandling pipeline afslutte udførelse af og behandle et år værd genererede data. Vente så behandling til afsluttes, før du fortsætter med de næste trin. 

**Hente filen PowerBI Forespørgselsdesigner**

-   En fil til forudkonfigurerede PowerBI designer er inkluderet som en del af installationen
-   Klik på noden PowerBI på diagramvisning og klikke på **Hent filen PowerBI Forespørgselsdesigner** link i egenskabsruden![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

-   Gemme lokalt

**Konfigurere PowerBI rapporter**

-   Åbn filen Forespørgselsdesigner 'VehicleTelemetryAnalytics - skrivebord Report.pbix' ved hjælp af PowerBI skrivebord. Hvis du ikke allerede har, kan du installere PowerBI skrivebordet fra [PowerBI skrivebord installere](http://www.microsoft.com/download/details.aspx?id=45331). 

-   Klik på **Rediger forespørgsler**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- Dobbeltklik på den **kilde**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Opdater Server forbindelsesstreng med Azure SQL server, der har klargjort som en del af installationen. Klik på noden Azure SQL i diagrammet, og få vist servernavnet i egenskabsruden.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- Lad **Database** som *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Klik på **OK**.
- Du får vist **Windows-legitimationsoplysninger** under fanen valgt som standard, ændre den til **databasen legitimationsoplysninger** ved at klikke på fanen **Database** til højre.
- Angiv **brugernavn** og **adgangskode** til din Azure SQL-Database, der blev angivet under installationen af dens installation.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Klik på **Opret forbindelse**
- Gentage ovenstående trin for hvert af de tre resterende forespørgsler, der findes i højre rude, og derefter opdatere forbindelsesoplysningerne datakilde.
- Klik på **Luk og Indlæs**. Power BI Desktop fil datasæt har forbindelse til SQL Azure-databasetabeller.
- **Luk** Power BI Desktop-fil.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Klik på **Gem** for at gemme ændringerne. 
 
Du har nu konfigureret alle rapporter, der svarer til batchen behandling stien i løsningen. 


## <a name="upload-to-powerbicom"></a>Overføre til *powerbi.com*
 
1.  Gå til portalen PowerBI web på http://powerbi.com og logge.
2.  Klik på **Hent Data**  
3.  Overfør Power BI Desktop-fil.  
4.  Hvis du vil overføre, skal du klikke på **-Hent Data > filer få lokale filer ->**  
5.  Gå til **"VehicleTelemetryAnalytics – pc Report.pbix"**  
6.  Når filen er overført, føres du tilbage til dit Power BI-arbejdsplads.  

Et datasæt, rapport og et tomt dashboard oprettes for dig.  
 

Fastgøre diagrammer til eksisterende dashboard **Telemetri Analytics instrumentbrættet** i **Power BI**. Klik på den tomme dashboard, der er oprettet over og gå derefter til **rapporter** afsnit Klik på den overførte rapporten.  

![Biltype Telemetri PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 


**Note rapporten har seks sider:**  
Side 1: Biltype tæthed  
Side 2: Realtid biltype tilstand  
Side 3: Indsatsbaserede aggressivt køretøjer   
Side 4: Tilbagekaldte køretøjer  
Siden 5: Brændstof effektivt indsatsbaserede køretøjer  
Siden 6: Contoso-Logo  

![Forbundne biler PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**Fra side 3**, pinkode følgende:  

1.  Antallet af VIN  
    ![Forbundne biler PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 

2.  Aggressivt indsatsbaserede køretøjer af model – vandfaldsdiagram  
    ![Biltype Telemetri - pinkode diagrammer 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**Fra siden 5**, pinkode følgende: 
 
1.  Antallet af vin    
    ![Biltype Telemetri - pinkode diagrammer 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2.  Brændstof effektiv køretøjer efter model: grupperet søjlediagram  
    ![Biltype Telemetri - pinkode diagrammer 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**Fra side 4**, pinkode følgende:  

1.  Antallet af vin  
    ![Biltype Telemetri - pinkode diagrammer 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.  Tilbagekaldte køretøjer efter by, model: træstruktur  
    ![Biltype Telemetri - fastgøre diagrammer, 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**Fra siden 6**, pinkode følgende:  

1.  Contoso konstruktion logo  
    ![Biltype Telemetri - pinkode diagrammer 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organisere dashboard**  

1.  Gå til dashboard
2.  Hold markøren over hver diagram og Omdøb den baseret på den navngivning fremgår fuldført dashboard billedet nedenfor. Flytte også diagrammerne rundt til at ligne dashboardet nedenfor.  
    ![Biltype Telemetri - organisere Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
    ![Biltype Telemetri PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3.  Hvis du har oprettet alle rapporter, som nævnes i dette dokument, ligner den endelige udfyldte dashboard i følgende figur. 

![Biltype Telemetri - organisere Dashboard 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Tillykke! Du har oprettet rapporterne og dashboardet for at få realtid, skønnet og batch viden om biltype tilstand og bil vaner.  
