<properties
    pageTitle="Overvåge Apps i Azure App-tjeneste"
    description="Lær at overvåge Apps i Azure App Service ved hjælp af portalen Azure."
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>Sådan: overvåge Apps i Azure App-tjeneste

[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) indeholder indbygget overvågning funktionalitet i [Azure-portalen](https://portal.azure.com).
Dette omfatter muligheden for at gennemse **kvotaer** og **målepunkter** for en app samt App Service-plan, skal konfigurere **beskeder** og lige **Skalering** automatisk på baggrund af disse målepunkter.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>Forstå kvotaer og målepunkter

### <a name="quotas"></a>Kvoter

Programmer, der er hostet i App-tjeneste er underlagt visse *begrænsninger* på de ressourcer, de kan bruge. Begrænsningerne er defineret af den **App-serviceaftale** , der er knyttet til appen.

Hvis programmet er placeret i en **gratis** eller **delt** plan, er begrænsninger for de ressourcer, der kan bruge appen defineret af **kvoter**.

Hvis programmet er placeret i en **grundlæggende**, **Standard** eller **Premium** , og begrænsningerne på angives de ressourcer, de kan bruge af **størrelse** (lille, Medium, stor) og **forekomst Tæl** (1, 2, 3,...) i **App-serviceaftale**.

**Kvoter** for **ledig** eller **delte** apps er:

* **CPU(Short)**
   * Mængde CPU, der er tilladt for dette program i et 3 minutters interval. Denne kvote angiver igen hver 3 minutter.
* **CPU(Day)**
   * Samlede mængde CPU, der er tilladt for dette program i en dag. Denne kvote angiver igen døgnet ved midnat UTC.
* **Hukommelse**
   * Samlede mængde hukommelse, der er tilladt for dette program.
* **Båndbredde**
   * Samlede mængde af udgående båndbredde, der er tilladt for dette program i en dag.
   Denne kvote angiver igen døgnet ved midnat UTC.
* **Filsystemet**
   * Samlede mængde lagerplads, der er tilladt.

Kvoten kun gælder for apps hostes på **grundlæggende**, **Standard** og **Premium** planer er **filsystemet**.

Flere oplysninger om bestemte kvoter, begrænsninger og funktioner, der er tilgængelige for forskellige App Service lagerenheder kan findes her: [Azure abonnement Service begrænsninger](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>Kvote for aktivering

Hvis et program i brugen overskrider **CPU (kort)**, stoppes **CPU (dag)**eller **båndbredde** kvote derefter programmet, indtil kvoten angiver igen. I denne periode, vil alle indgående anmodninger resultere i en **HTTP 403**.
![][http403]

Hvis programmet **hukommelse** kvoten er overskredet, vil programmet være igen Introduktion.

Hvis kvoten **filsystemet** overskrides, derefter skrive en handlingen mislykkes, dette omfatter skrivning til logfiler.

Kvoter kan øget eller fjernet fra din app ved at opgradere din App-serviceaftale.

### <a name="metrics"></a>Målepunkter

**Målepunkter** indeholder oplysninger om appen eller App-serviceaftale funktionsmåde.

For et **program**er de tilgængelige målepunkter:

* **Gennemsnitlig svartid**
   * Den gennemsnitlige tid at kunne levere anmodninger i ms-appen.
* **Gennemsnitlig hukommelse arbejdssæt**
   * Den gennemsnitlige mængde hukommelse i MIB'er bruges af appen.
* **CPU-tid**
   * Mængden af CPU i sekunder, der bruges af appen. Du kan finde flere oplysninger om dette metriske: [CPU tid vs CPU procentdel](#cpu-time-vs-cpu-percentage)
* **Data i**
   * Mængden af indgående båndbredde efter app i MIB'er.
* **Data ud**
   * Mængden af udgående båndbredde efter app i MIB'er.
* **HTTP-2xx**
   * Antallet af anmodninger, hvilket resulterer i en HTTP-statuskode > = 200 men < 300.
* **HTTP-3xx**
   * Antallet af anmodninger, hvilket resulterer i en HTTP-statuskode > = 300, men < 400.
* **HTTP 401**
   * Antallet af anmodninger, hvilket resulterer i HTTP 401 statuskode.
* **HTTP 403**
   * Antallet af anmodninger, hvilket resulterer i HTTP 403 statuskode.
* **HTTP 404**
   * Antallet af anmodninger, hvilket resulterer i HTTP 404 statuskode.
* **HTTP 406**
   * Antallet af anmodninger, hvilket resulterer i HTTP 406 statuskode.
* **HTTP-4xx**
   * Antallet af anmodninger, hvilket resulterer i en HTTP-statuskode > = 400, men < 500.
* **HTTP-serverfejl**
   * Antallet af anmodninger, hvilket resulterer i en HTTP-statuskode > = 500, men < 600.
* **Arbejdssæt til hukommelse**
   * Aktuelle mængde hukommelse, der bruges af appen i MIB'er.
* **Anmodninger om**
   * Samlet antal anmodninger uanset deres resulterende HTTP-statuskode.

For en **App-serviceaftale**er tilgængelige målepunkter:

>[AZURE.NOTE] App Service plan målepunkter er kun tilgængelige for planer i **grundlæggende**, **Standard** og **Premium** SKU.

* **CPU procentdel**
   * Den gennemsnitlige CPU, der bruges på tværs af alle forekomster af planen.
* **Procent af hukommelsen**
   * Den gennemsnitlige hukommelse, der bruges på tværs af alle forekomster af planen.
* **Data i**
   * Den gennemsnitlige indgående båndbredde, der bruges på tværs af alle forekomster af planen.
* **Data ud**
   * Gennemsnittet udgående båndbredde, der bruges på tværs af alle forekomster af planen.
* **Længde på disk kø**
   * Det gennemsnitlige antal læse og skrive anmodninger, der er i kø på lagerplads. Længde på en høj disk kø er angivelse af et program, der kan langsommere på grund af for mange disk I/O.
* **Længde på http-kø**
   * Det gennemsnitlige antal HTTP-anmodninger, der skulle sidde i køen før er opfyldt. En høj eller stigende HTTP kø længde er et symptom på en plan under belastet.

### <a name="cpu-time-vs-cpu-percentage"></a>CPU tid vs CPU procentdel
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

Der er 2 målepunkter, der viser CPU-brug. **CPU-tid** og **CPU procentdel**

**CPU-tid** er til apps, der er hostet i **ledig** eller **delt** planer, da et af deres kvoter er defineret i CPU minutter, der bruges af appen.

**CPU procentdel** er på den anden side til apps, der er hostet i **grundlæggende**, **standard** og **premium** -planer, da de kan skaleres ud af, og denne metrikværdi er en god angivelse af det overordnede forbrug på tværs af alle forekomster.

##<a name="metrics-granularity-and-retention-policy"></a>Målepunkter granularitet og opbevaringspolitik

Målene for et program og app-serviceaftale er logget og sammenlagt af tjenesten med følgende granulariteter og opbevaringspolitikker:

 * **Minut** granularitet målepunkter bevares til **48 timer**
 * **Time** granularitet målepunkter bevares i **30 dage**
 * **Dag** granularitet målepunkter bevares til **90 dage**

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Overvåge kvoter og målepunkter i portalen Azure.

Du kan kontrollere status for de forskellige **kvoter** og - **metrik** påvirker et program i [Azure-portalen](https://portal.azure.com).

![][quotas]
**Kvoter** kan findes under Indstillinger >**kvoter**. UX gør det muligt at gennemse: navn (1) på kvoter, (2) dens nulstillingsinterval, (3) dens aktuelle grænse og (4) aktuelle værdi.

![][metrics]
**Målepunkter** kan være adgang direkte fra bladet ressource. Du kan også tilpasse diagrammet ved at: (1) **Klik** på den, og vælg (2) **Rediger diagram**.
Her kan du ændre (3) **tidsinterval**, (4) **diagramtype**og (5) **målepunkter** skal vises.  

Du kan læse mere om målepunkter her: [overvåge tjenesten målepunkter](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

## <a name="alerts-and-autoscale"></a>Beskeder og Autoskalering
Målepunkter for en App eller tjeneste App plan kan benyttes op til vigtige beskeder, du lære mere om dette skal du se [modtage påmindelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

App Service apps vært grundlæggende, standard eller førsteklasses App Service-planer understøtter **Autoskalering**. Dette kan du konfigurere regler, overvåge App Service plan målepunkter og kan øge eller mindske forekomst antallet give yderligere ressourcer efter behov, eller gemme penge når programmet er overliggende klargøring. Du kan læse mere om automatisk skala her: [Sådan skala](../monitoring-and-diagnostics/insights-how-to-scale.md) , og her [bedste fremgangsmåder til Azure skærm Autoskalering](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

>[AZURE.NOTE] Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="whats-changed"></a>Hvad er ændret
* Finde en vejledning til ændring fra websteder til App-tjenesten: [Azure App Service og dets indvirkning på eksisterende Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
