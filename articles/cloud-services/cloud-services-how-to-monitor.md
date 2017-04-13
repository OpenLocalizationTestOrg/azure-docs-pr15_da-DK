<properties 
    pageTitle="Hvordan du kan overvåge en skybaseret tjeneste | Microsoft Azure" 
    description="Lær at overvåge skytjenester ved hjælp af portalen Azure klassisk." 
    services="cloud-services" 
    documentationCenter="" 
    authors="rboucher" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/04/2015" 
    ms.author="robb"/>


# <a name="how-to-monitor-cloud-services"></a>Hvordan du kan overvåge Cloud Services

[AZURE.INCLUDE [disclaimer](../../includes/disclaimer.md)]

Du kan overvåge `key` ydeevne omfanget af din skytjenester på portalen Azure klassisk. Du kan angive kontrolniveau minimale og detaljeret for hver tjeneste rolle, og du kan tilpasse den overvågning, vises. Detaljeret overvågningsdata er gemt i en lagerplads konto, som du kan få adgang til uden for portalen. 

Overvågning vises i portalen Azure klassisk er nemme at konfigurere. Du kan vælge den målepunkter, du vil overvåge på listen målepunkter på siden **skærm** , og du kan vælge, hvilke målepunkter afbilde i målepunkter diagrammer på siden **skærm** og dashboard. 

## <a name="concepts"></a>Begreber

Minimale overvågning er som standard angivet til en ny skybaseret tjeneste ved hjælp af tællere i ydeevne indsamlede fra værtsoperativsystemet efter roller forekomster (virtuelle maskiner). De minimale målepunkter er begrænset til CPU procentdel, Data i, Data ud, Disk læst overførselshastighed, og Disk skrive overførselshastighed. Ved at konfigurere detaljeret overvågning, kan du modtage flere statistikker baseret på ydelsesdata i virtuelle maskiner (rolle forekomster). Detaljeret målepunkter aktivere nærmere analyse af problemer, der opstår under programmet handlinger.

Som standard er tæller ydeevnedata fra rolle forekomster prøver og overføres fra den rolle forekomst med 3 minutters intervaller. Når du aktiverer detaljeret overvågning, sammenlægges tæller rå ydelsesdata for hver rolle forekomst og på tværs af rolle forekomster for hver rolle med intervaller på fem minutter, 1 time og 12 timer. De aggregerede data fjernes efter 10 dage.

Når du aktiverer detaljeret overvågning, er aggregeret overvågningsdata gemt i tabeller i kontoen lagerplads. Hvis du vil aktivere detaljeret overvågning for en rolle, skal du konfigurere en diagnosticering forbindelsesstreng, der er knyttet til kontoen lagerplads. Du kan bruge forskellige lagerplads konti til forskellige roller.

Bemærk, at aktivere detaljeret overvågning, øger din lagerplads omkostninger, der er relateret til lagring af data, dataoverførsel og lagerplads transaktioner. Minimale overvågning kræver ikke en lagerplads konto. Dataene på den målepunkter, der vises på niveauet for minimale overvågning gemmes ikke i kontoen lagerplads, selvom du angiver niveauet for overvågning til detaljeret.


## <a name="how-to-configure-monitoring-for-cloud-services"></a>Sådan: Konfigurere overvågning til skytjenester

Brug følgende procedurer til at konfigurere detaljeret eller minimale overvågning på portalen Azure klassisk. 

### <a name="before-you-begin"></a>Inden du går i gang

- Oprette en lagerplads konto for at gemme den overvågningsdata. Du kan bruge forskellige lagerplads konti til forskellige roller. Kan finde flere oplysninger i Hjælp til **Lagerplads konti**, eller se, [hvordan du oprette en lagerplads konto](/manage/services/storage/how-to-create-a-storage-account/).

- Aktivere Azure diagnosticering for dine skyen service roller. Se [konfiguration af diagnosticering til Skytjenester](https://msdn.microsoft.com/library/azure/dn186185.aspx#BK_EnableBefore).

Sikre, at der findes i rollekonfigurationen forbindelsesstrengen diagnosticering. Du kan ikke aktivere detaljeret overvågning, indtil du aktiverer Azure diagnosticering og medtage en diagnosticering forbindelsesstreng i konfigurationen af rollen.   

> [AZURE.NOTE] Projekter målretning af Azure SDK 2,5 omfattede ikke automatisk diagnosticering forbindelsesstrengen i projektskabelon. For disse projekter skal du manuelt føje forbindelsesstrengen diagnosticering til rollekonfigurationen.

**Sådan tilføjes manuelt diagnosticering forbindelsesstreng til rolle konfiguration**

1. Åbne projektet skybaseret tjeneste i Visual Studio
2. Dobbeltklik på den **rolle** til at åbne rolle designer, og vælg fanen **Indstillinger**
3. Se efter en indstilling med navnet **Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString**. 
4. Hvis denne indstilling ikke er til stede derefter klikke på knappen **Tilføj indstilling** til at føje det til konfigurationen og ændre typen for den nye indstilling til **ConnectionString**
5. Angive værdien for forbindelsesstreng den ved at klikke på knappen **...** . Der åbnes en dialogboks, så du kan vælge en lagerplads konto.

    ![Visual Studio indstillinger](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioDiagnosticsConnectionString.png)

### <a name="to-change-the-monitoring-level-to-verbose-or-minimal"></a>Ændre niveauet for overvågning detaljeret eller minimale

1. Åbn siden **Konfigurer** til skyen service installation i [Azure klassisk portal](https://manage.windowsazure.com/).

2. Klik på **Vis** eller **minimale**i **niveau**. 

3. Klik på **Gem**.

Når du har aktiveret detaljeret overvågning, skal du begynde at se overvågningsdata i portalen Azure klassisk inden for en time.

Rå ydeevne tællerdata og aggregeret overvågningsdata er gemt i kontoen lagerplads i tabeller, der er kvalificeret efter installation ID for rollerne. 

## <a name="how-to-receive-alerts-for-cloud-service-metrics"></a>Sådan: modtage beskeder for skyen service målepunkter

Du kan modtage beskeder, der er baseret på dine skybaseret tjeneste, overvågning målepunkter. På siden **Management Services** i portalen Azure klassisk, kan du oprette en regel til at udløse en besked, når den metrikværdi, du vælger en værdi, du angiver. Du kan også vælge at have en e-mail, der sendes, når påmindelsen udløses. Du kan finde flere oplysninger, se [Sådan: modtage påmindelser og administrere regler for påmindelser i Azure](http://go.microsoft.com/fwlink/?LinkId=309356).

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Sådan: føje målepunkter til tabellen målepunkter

1. Åbn siden **skærm** for skytjenesten i [Azure klassisk portal](http://manage.windowsazure.com/).

    Som standard viser tabellen målepunkter et undersæt af de tilgængelige målepunkter. Illustrationen viser standard detaljeret omfanget af en skybaseret tjeneste, som er begrænset til tælleren Hukommelse\Tilgængelige MB ydeevne med data aggregeret på niveauet for rolle. Brug **Tilføje målepunkter** for at vælge flere aggregerede og rolle niveau statistikker til at overvåge i portalen Azure klassisk.

    ![Detaljeret visning](./media/cloud-services-how-to-monitor/CloudServices_DefaultVerboseDisplay.png)
 
2. Sådan føjes målepunkter til tabellen målepunkter:

    1. Klik på **Tilføj målepunkter** for at åbne **Vælg målepunkter**, som er vist nedenfor.

        Den første tilgængelige metrikværdi er udvidet for at få vist indstillinger, der er tilgængelige. For hver metrikværdi vises indstillingen øverste aggregeret overvågningsdata for alle roller. Desuden kan du vælge individuelle roller for at få vist data.

        ![Tilføje målepunkter](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics.png)

    2. Markere målepunkter skal vises

        - Klik på pil ned ved metrisk at udvide indstillingerne for overvågning.
        - Markér afkrydsningsfeltet for hver overvågning indstilling, du vil have vist.

        Du kan vise op til 50 målepunkter i tabellen målepunkter.

        > [AZURE.TIP] Listen målepunkter kan indeholde massevis af målepunkter i detaljeret overvågning. Hold markøren over højre side af dialogboksen for at få vist et rullepanel. Hvis du vil filtrere på listen, klik på søgeikonet, og skriv tekst i søgefeltet, som vist nedenfor.
    
        ![Tilføje målepunkter søgning](./media/cloud-services-how-to-monitor/CloudServices_AddMetrics_Search.png)


3. Klik på OK (markering), når du er færdig med at vælge målepunkter.

    De valgte målepunkter føjes til tabellen målepunkter, som vist nedenfor.

    ![overvåge målepunkter](./media/cloud-services-how-to-monitor/CloudServices_Monitor_UpdatedMetrics.png)

 
4. Hvis du vil slette en metrikværdi fra tabellen målepunkter, skal du klikke på metrikværdien for at markere den, og klik **Slette metrisk**. (Du kun se **Slette metrisk** når du har en metrikværdi, der er markeret).

### <a name="to-add-custom-metrics-to-the-metrics-table"></a>Tilføje brugerdefinerede målepunkter til tabellen målepunkter

**Detaljeret** overvågning niveau indeholder en liste over standard mål, som du kan overvåge på portalen. Ud over dette kan du overvåge eventuelle brugerdefinerede målepunkter eller tællere i ydeevne defineret af dit program via portalen.

Følgende trin forudsætter, at du har aktiveret **detaljeret** overvågning niveau og har konfiguration af dit program til at samle og overføre brugerdefinerede ydeevne tællere. 

Du skal opdatere konfigurationen i wad-kontrolelement-objektbeholder for at få vist tællerne i brugerdefineret ydeevne i portalen:
 
1. Åbn wad-kontrolelement-objektbeholder blob i kontoen diagnosticering lagerplads. Du kan bruge Visual Studio eller eventuelle andre lagerplads Stifinder til at gøre dette.

    ![Visual Studio Server Explorer](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioBlobExplorer.png)

2. Gå blob stien ved hjælp af mønster **DeploymentId/RoleName/RoleInstance** til at finde sektionen konfiguration for din rolle forekomst. 

    ![Visual Studio Storage Explorer](./media/cloud-services-how-to-monitor/CloudServices_Monitor_VisualStudioStorage.png)
3. Hente konfigurationsfil for din rolle forekomst og opdatere den for at medtage alle tællere i brugerdefineret ydeevne. Tilføj følgende under **PerformanceCounters\Subscriptions** node for eksempel til at overvåge *Disk skrive byte/sekund* for *C-drevet*

    ```xml
    <PerformanceCounterConfiguration>
    <CounterSpecifier>\LogicalDisk(C:)\Disk Write Bytes/sec</CounterSpecifier>
    <SampleRateInSeconds>180</SampleRateInSeconds>
    </PerformanceCounterConfiguration>
    ```
4. Gemme ændringerne, og Overfør konfigurationsfilen tilbage til den samme placering overskrive den eksisterende fil BLOB.
5. Skift til detaljeret tilstand i Azure klassisk portalen konfigurationen. Hvis du allerede var i detaljeret tilstand har du skifte til minimale og tilbage til detaljeret.
6. Tælleren brugerdefinerede ydeevne vil nu være tilgængelige i dialogboksen **Tilføj målepunkter** . 

## <a name="how-to-customize-the-metrics-chart"></a>Sådan: tilpasse målepunkter diagrammet

1. Vælg op til 6 målepunkter afbilde på målepunkter diagrammet i tabellen målepunkter. Hvis du vil markere en metrikværdi, skal du markere feltet på den venstre side. Hvis du vil fjerne en metrikværdi fra målepunkter diagrammet, afkrydsningen dens i tabellen målepunkter.

    Når du vælger målepunkter i tabellen målepunkter, føjes før mailadvarslen sendes til målepunkter diagrammet. På en smal skærm indeholder en rulleliste **n flere** metriske sidehoveder, der ikke passer til visningen.

 
2. For at skifte mellem at få vist relative værdier (sidste værdi kun for hver metrisk) og absolutte værdier (Y-aksen vises), skal du vælge relativ eller absolut øverst i diagrammet.

    ![Relativ eller absolut](./media/cloud-services-how-to-monitor/CloudServices_Monitor_RelativeAbsolute.png)

3. Hvis du vil ændre tidsintervallet Vælg målepunkter diagram vises 1 hour, 24 timer eller 7 dage øverst i diagrammet.

    ![Overvåge Vis periode](./media/cloud-services-how-to-monitor/CloudServices_Monitor_DisplayPeriod.png)

    Metoden til at angive målepunkter er anderledes i dashboard målepunkter diagrammet. Et standardsæt af målepunkter er tilgængelig, og målepunkter tilføjes eller fjernes ved at vælge metriske sidehovedet.

### <a name="to-customize-the-metrics-chart-on-the-dashboard"></a>At tilpasse diagrammet målepunkter på dashboardet

1. Åbn dashboardet for skytjenesten.

2. Tilføje eller fjerne målepunkter fra diagrammet:

    - Hvis du vil afbilde en ny metrikværdi, skal du markere afkrydsningsfeltet for metrikværdien i diagrammet overskrifterne. Klik på pil ned ved ** *n*??metrics** afbilde en metrikværdi i sidehovedområdet diagram ikke kan vises på en lille skærm.

    - Hvis du vil slette en metrikværdi, der er afbildet i diagrammet, skal du fjerne markeringen i afkrydsningsfeltet efter dens sidehoved.

3. Skifte mellem **Relative** og **absolutte** vises.

4. Vælg 1 hour, 24 timer eller 7 dage af data for at få vist.

## <a name="how-to-access-verbose-monitoring-data-outside-the-azure-classic-portal"></a>Sådan: Access detaljeret overvågningsdata uden for portalen Azure klassisk

Detaljeret overvågningsdata er gemt i tabeller i kontiene lagerplads, du angiver for hver rolle. Seks tabeller oprettes for hver skyen service installation for rollen. To tabeller oprettes for hver (5 minutter, 1 time og 12 timer). En af disse tabeller gemmer rolle niveau sammenlægninger; anden tabellen gemmer sammenlægninger efter rolle forekomster. 

Tabelnavnene har følgende format:

```
WAD*deploymentID*PT*aggregation_interval*[R|RI]Table
```

hvor:

- *deploymentID* er tildelt til skyen service installationen GUID

- *aggregation_interval* = 5 M, 1t eller 12 H

- rolle-niveau sammenlægninger = R

- sammenlægninger efter rolle forekomster = RI

De følgende tabeller lagrer for eksempel detaljeret overvågningsdata sammenlagt med 1-timers mellemrum:

```
WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRTable (hourly aggregations for the role)

WAD8b7c4233802442b494d0cc9eb9d8dd9fPT1HRITable (hourly aggregations for role instances)
```
