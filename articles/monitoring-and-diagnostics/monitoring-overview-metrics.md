<properties
    pageTitle="Oversigt over målepunkter i Microsoft Azure | Microsoft Azure"
    description="Oversigt over målepunkter og deres brug i Microsoft Azure"
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="ashwink"/>

# <a name="overview-of-metrics-in-microsoft-azure"></a>Oversigt over målepunkter i Microsoft Azure 

I denne artikel beskrives målepunkter fremgår Microsoft Azure deres fordele, og hvordan du kommer i gang med at bruge dem.  

## <a name="what-are-metrics"></a>Hvad er målepunkter?

Azure skærm gør det muligt at bruge telemetri for at få indsigt i den ydeevne og tilstand for dit arbejdsbelastningen på Azure. De vigtigste typer Azure telemetridata er målene (også kaldet tællere i ydeevne) fra mest Azure ressourcer. Azure skærm indeholder flere måder at konfigurere og bruge disse målepunkter til overvågning og fejlfinding.


## <a name="what-can-you-do-with-metrics"></a>Hvad kan du gøre med målepunkter?

Målepunkter er et værdifuldt kilde til telemetri, så du kan gøre følgende:

- **Registrer ydeevnen** for din ressource (som en VM, websted eller logik App) efter afbildning af dens målepunkter i et portalen diagram og Fastgør diagrammet til et dashboard.
- **Få besked om et problem** påvirke ydeevnen for din ressource, når en metrikværdi krydser en bestemt grænse.
- **Konfigurer automatisk handlinger**, som Autoskalering en ressource eller en runbook kraft, når en metrikværdi krydser en bestemt grænse.
- **Udfør avancerede analytics** eller rapportering om tendenser for ydeevnen eller brugen af dine ressourcer.
- **Arkiv** ydeevnen eller sundhed oversigten over dine ressourcer **for overholdelse af angivne standarder/overvågning** formål.

##  <a name="metric-characteristics"></a>Metriske egenskaber
Målepunkter har følgende egenskaber:

- Alle målepunkter har **1 minutters frekvens**. Du modtager en metrikværdi hvert minut fra din ressource, hvorved du nær realtid indsigt i status og tilstand for ressourcen.
- Målepunkter er **tilgængelige fra af-i uden at skulle tilmelder dig** eller til at konfigurere yderligere diagnosticering.
- Du kan få adgang til **30 dage efter historik** for hver metrikværdi. Du kan hurtigt se de seneste og månedlige tendenser i ydeevne eller tilstanden for ressourcen.

Du kan:

- Nemt finde, access, og **få vist alle målepunkter** via portalen Azure, når du markerer en ressource og afbilde dem på et diagram. 
- Konfigurere en metriske **reglen, der sender en besked eller tager automatiseret handling** , når metrikværdien krydser i grænseværdi, du har angivet. Autoskalering er en speciel automatiseret handling, der gør det muligt at skalere ud ressource til at opfylde indgående anmodninger eller indlæse på dit websted eller beregne ressourcer. Du kan konfigurere en regel for Autoskalering indstilling at skalere op/i baseret på en metrikværdi krydser en grænseværdi.
- **Arkiv** måleredskaber for længere eller bruge dem til offline-rapportering. Du kan distribuere din målepunkter for at blob storage, når du konfigurerer diagnosticering indstillinger for ressourcen.
- **Stream** målepunkter til en begivenhed-Hub, så du kan sende dem til Azure Stream Analytics eller brugerdefinerede apps til nær realtid analyse. Du kan gøre det ved hjælp af indstillinger for diagnosticering.
- **Distribuere** alle målepunkter til OMS Log Analytics låse op Chat analytics, Søg og brugerdefinerede advarer på målepunkter data fra dine ressourcer.
- **Forbrug** målepunkter via Azure skærm REST API'er.
- **Forespørgsel** målepunkter ved hjælp af PowerShell Cmdlets eller på tværs af platforme REST-API.

 ![Routing af målepunkter i Azure skærm](./media/monitoring-overview-metrics/MetricsOverview0.png)

## <a name="access-metrics-via-portal"></a>Access målepunkter via portalen
Her er en hurtig gennemgang af oprettelse af et metriske diagram via Azure-portalen

### <a name="view-metrics-after-creating-a-resource"></a>Få vist målepunkter efter oprettelse af en ressource
1. Åbn Azure portal
2. Oprette en ny tjeneste i App - websted.
3. Når du opretter et websted, kan du gå til bladet oversigt på et websted.
4. Du kan se nye målepunkter som et 'Overvågnings' felt. Du kan redigere feltet og vælge flere målepunkter

 ![Målepunkter på en ressource i Azure skærm](./media/monitoring-overview-metrics/MetricsOverview1.png)    

### <a name="access-all-metrics-in-a-single-place"></a>Få adgang til alle målepunkter på ét sted
1. Åbn Azure-portalen 
2. Gå til fanen ny skærm og vælge indstillingen målepunkter under det 
3. Du kan vælge dit abonnement, ressourcegruppe og navnet på ressourcen fra på rullelisten. 
4. Du kan nu få vist listen over tilgængelige målepunkter. 
5. Vælg metrisk du er interesseret i, og udlæg den. 
6. Du kan fastgøre den til dashboard ved at klikke på PIN-koden på det øverste højre hjørne.

 ![Få adgang til alle målepunkter på ét sted i Azure skærm](./media/monitoring-overview-metrics/MetricsOverview2.png) 


>[AZURE.NOTE] Du kan få adgang til host niveau målepunkter fra FOS (Azure ressourcestyring baseret) og VM skala sæt uden eventuelle yderligere diagnosticering opsætning. Denne nye host niveau statistik fås til Windows og Linux forekomster. Disse målepunkter må ikke forveksles med gæst OS niveau målene, som du har adgang til, når du slår Azure Diagnostics på din FOS eller VMSS. Hvis du vil vide mere om at konfigurere Azure diagnosticering, skal du se [Hvad er Microsoft Azure diagnosticering](../azure-diagnostics.md).

## <a name="access-metrics-via-rest-api"></a>Access målepunkter via REST-API
Azure målepunkter skal være adgang via Azure skærm API'er. Der er to API'er, der hjælper dig med at registrere og få adgang til målepunkter. Brug den: 

- [Azure skærm metrisk definitioner REST-API](https://msdn.microsoft.com/library/mt743621.aspx) til at få adgang til listen over målepunkter tilgængelig for en tjeneste.
- [Azure skærm målepunkter REST-API](https://msdn.microsoft.com/library/mt743622.aspx) til at få adgang til de faktiske målepunkter data

>[AZURE.NOTE] Denne artikel omhandler målepunkter via [nye API til målepunkter](https://msdn.microsoft.com/library/dn931930.aspx) for Azure ressourcer. API-version til de nye metriske definitioner API er 2016-03-01 og version på målepunkter API er 2016-09-01. Ældre metriske definitioner og målepunkter kan åbnes med api-versionen 2014-04-01.

Du kan finde en mere detaljeret gennemgang ved hjælp af Azure skærm REST API'er, [Azure skærm RESTEN API gennemgang](monitoring-rest-api-walkthrough.md).

## <a name="export-options-for-metrics"></a>Eksportindstillinger på målepunkter
Du kan gå til diagnosticering logfiler blade under fanen skærm og få vist eksportindstillinger på målepunkter. Du kan vælge målepunkter (og diagnosticeringslogfiler) skal dirigeres til Blob-lager, begivenhed Hubs eller til OMS Log Analytics for brug-tilfælde nævnt tidligere i denne artikel. 

 ![Eksportindstillinger på målepunkter i Azure skærm](./media/monitoring-overview-metrics/MetricsOverview3.png)   

Du kan konfigurere via ressourcestyring skabeloner, [PowerShell](insights-powershell-samples.md), [CLI](insights-cli-samples.md) eller [REST API'er](https://msdn.microsoft.com/library/dn931943.aspx). 

## <a name="take-action-on-metrics"></a>Udføre handlinger på målepunkter
Du kan modtage beskeder eller tage automatiske handlinger på metriske data. Du kan konfigurere regler for påmindelser eller Autoskalering indstillinger for at gøre dette.

### <a name="alert-rules"></a>Regler for påmindelser
Du kan konfigurere regler for påmindelser på målepunkter. Disse regler for påmindelser kan se, hvis en metrikværdi har krydses en bestemt grænse og give dig besked via mail, eller brand en webhook, der kan derefter bruges til at udføre en brugerdefineret script. Du kan også bruge webhook til at konfigurere 3 produkt integrationer.

 ![Målepunkter og regler for påmindelser i Azure skærm](./media/monitoring-overview-metrics/MetricsOverview4.png)

### <a name="autoscale"></a>Autoskalering
Nogle Azure ressourcer understøtter flere forekomster til at skalere eller tilmeldt sig til at håndtere dine arbejdsmængder. Autoskalering gælder for App-tjenester (online), virtuelt skala sæt (VMSS) og klassisk Skytjenester. Du kan konfigurere Autoskalering regler til at skalere eller tilmeldt sig, når en bestemt metrikværdi, som påvirker beskyttelsen arbejdsbelastningen krydser en grænse, du angiver. Se [Oversigt over Autoskalering](monitoring-overview-autoscale.md)kan finde flere oplysninger.

 ![Målepunkter og Autoskalering i Azure skærm](./media/monitoring-overview-metrics/MetricsOverview5.png)

## <a name="supported-services-and-metrics"></a>Understøttede tjenester og -målinger
Azure skærm er en ny målepunkter infrastruktur. Giver mulighed for følgende Azure tjenester i portalen Azure og den nye version af Azure skærm API:

- FOS (Azure ressourcestyring baseret)
- VM skala sæt
- Batchen
- Begivenhed Hub navneområde 
- Service Bus navneområde (kun premium SKU)
- SQL (version 12)
- Elastiske SQL puljen
- Websteder
- Webserver-farme
- Logik Apps
- IoT Hubs
- Redis Cache
- Netværk: Programmet Gateways
- Søgning

Du kan få vist en en detaljeret liste over alle de understøttede tjenester og deres målepunkter på [Azure skærm målepunkter - understøttet måleredskaber per ressourcetype](monitoring-supported-metrics.md). 


## <a name="next-steps"></a>Næste trin

Se linkene i denne artikel. Desuden kan få mere at vide:  

- om [almindelige statistik for Autoskalering](insights-autoscale-common-metrics.md)
- hvordan du [opretter regler for påmindelser](insights-alerts-portal.md)




