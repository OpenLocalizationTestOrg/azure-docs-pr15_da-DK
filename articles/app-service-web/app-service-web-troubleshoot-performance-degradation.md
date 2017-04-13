<properties
    pageTitle="Hastigheden web app i App Service | Microsoft Azure"
    description="I denne artikel hjælper dig med at foretage fejlfinding af problemer med ydeevnen langsom web app i Azure App-tjeneste."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="Web app-ydeevne, langsom app app langsom"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Foretage fejlfinding af problemer med ydeevnen langsom web app i Azure App Service

I denne artikel hjælper dig med at foretage fejlfinding af problemer med ydeevnen langsom web app i [Azure App-tjeneste](http://go.microsoft.com/fwlink/?LinkId=529714).

Hvis du har brug for mere hjælp på en hvilken som helst sted i denne artikel, kan du kontakte de Azure eksperter på [MSDN Azure og forummerne stakoverløb](https://azure.microsoft.com/support/forums/). Alternativt kan du også sende en Azure support-hændelse. Gå til det [Azure supportwebsted](https://azure.microsoft.com/support/options/) , og klik på **Få Support**.

## <a name="symptom"></a>Symptom

Når du navigerer online, sider Indlæs langsomt og nogle gange timeout.

## <a name="cause"></a>Årsag

Problemet skyldes ofte niveau problemer, f.eks.:

-   anmodninger om tager lang tid
-   program ved hjælp af høj hukommelse/CPU
-   programmet ned på grund af en undtagelse.

## <a name="troubleshooting-steps"></a>Trin til fejlfinding

Fejlfinding i forbindelse med kan opdeles i tre forskellige opgaver i rækkefølge:

1.  [Se og overvåge programmets funktionsmåde](#observe)
2.  [Indsamle data](#collect)
3.  [Reducere problemet](#mitigate)

[App Service Web Apps](/services/app-service/web/) giver forskellige indstillinger på hvert trin.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. se og overvåge programmets funktionsmåde

#### <a name="track-service-health"></a>Registrer tjenestetilstand

Microsoft Azure publicizes hver gang der er en tjenesten blev afbrudt eller ydeevne er forringet. Du kan spore tilstanden for tjenesten på [Azure-portalen](https://portal.azure.com/). Du kan finde flere oplysninger [Registrer tjenestetilstand](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Overvåge din online

Denne indstilling giver dig mulighed at finde ud af, hvis dit program er problemer eventuelle problemer. Klik på feltet **anmodninger og fejl** i din online blade. Bladet **metrisk** viser dig alle målene, kan du tilføje.

Nogle af de mål, som du muligvis vil overvåge for din online er

-   Gennemsnitlig hukommelse arbejdssæt
-   Gennemsnitlig svartid
-   CPU-tid
-   Arbejdssæt til hukommelse
-   Anmodninger om

![overvåge ydeevnen i web app](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Du kan finde flere oplysninger i:

-   [Overvåge Webapps i Azure App-tjeneste](web-sites-monitor.md)
-   [Modtage påmindelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Overvåge web slutpunkt status

Hvis du kører din online i **Standard** priser niveau, kan du overvåge 2 slutpunkter fra 3 geografiske placeringer Web Apps.

Slutpunkt overvågning konfigurerer web test fra geografisk distribueret placeringer, der tester svartid og oppetid på URL-webadresser. Testen udfører en HTTP GET operation på URL-adressen til at bestemme svartid og oppetid fra hver placering. Hver konfigureret placering kører en test alle fem minutter.

Oppetid overvåges ved hjælp af HTTP-Svarkoder, og svartid måles i millisekunder. En overvågning test mislykkes, hvis koden, HTTP-svar er større end eller lig med 400, eller hvis svaret tager mere end 30 sekunder. Et slutpunkt betragtes som tilgængelig, hvis dens overvågning test lykkes fra de angivne placeringer.

Hvis du vil konfigurere det, se [Sådan: overvåge web slutpunkt status](web-sites-monitor.md#webendpointstatus).

Se også [holde styr på Azure af websteder op plus slutpunkt overvågning - med Stefans Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) en video om slutpunkt overvågning.

#### <a name="application-performance-monitoring-using-extensions"></a>Programmet overvågning af ydeevnen ved hjælp af filtypenavne

Du kan også overvåge dit program ydeevne ved at udnytte _websted filtypenavne_.

Hver App Service WebApp indeholder en extensible management slutpunkt, kan du benytte en effektiv række værktøjer, der er installeret som websted filtypenavne. Disse funktioner går fra kilde kode redaktører som [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) til administrationsværktøjer for forbundne ressourcer, som en MySQL-database, der er knyttet til en WebApp.

[Azure programmet indsigt](/services/application-insights/) og [Nye Relic](/marketplace/partners/newrelic/newrelic/) er to af ydeevnen overvågning websted filtypenavne, der er tilgængelige. Hvis du vil bruge nye Relic, skal installere du en agent på kørselstidspunktet. Hvis du vil bruge Azure programmet viden, du genopbygger din kode med en SDK, og du kan også installere et filtypenavn, der giver adgang til yderligere data. SDK kan du skrive programkode for at overvåge brugen af og ydeevnen af din app mere detaljeret.

Hvis du vil bruge programmet viden, se [overvåge ydeevnen i webprogrammer](../application-insights/app-insights-web-monitor-performance.md).

For at bruge nye Relic skal du se [Nye Relic ydeevne programadministration på Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />
### <a name="2-collect-data"></a>2. indsamle data

####    <a name="enable-diagnostics-logging-for-your-web-app"></a>Aktivere logføring af diagnosticering til din online

Web Apps-miljø indeholder diagnosticering funktioner for oplysninger om logføring fra både Webserveren og webprogrammet. Disse er logisk adskilt i web server diagnosticerings- og programmet diagnosticering.

##### <a name="web-server-diagnostics"></a>Web server diagnosticering

Du kan aktivere eller deaktivere følgende typer af logfiler:

-   **Detaljeret logføring af fejl** – detaljerede fejloplysninger for HTTP statuskoder, der angiver en fejl (statuskode 400 eller nyere). Dette kan indeholde oplysninger, der kan hjælpe med at finde ud af, hvorfor serveren returnerede fejlkoden.
-   **Mislykkedes sporing af anmodning** - detaljerede oplysninger om mislykkede anmodninger, herunder en sporing af de IIS-komponenter, der bruges til at behandle anmodningen og den tid i hver komponent. Det kan være nyttigt, hvis du forsøger at forbedre ydeevnen i web app eller isolere, hvad der forårsager en bestemt HTTP-fejl.
-   **Web Server logføring** - oplysninger om HTTP transaktioner i W3C udvidet logon-filformatet. Dette er nyttigt, når du bestemmer overordnede web app statistik, som antallet af anmodninger om håndteres, eller hvor mange anmodninger er fra en bestemt IP-adresse.

##### <a name="application-diagnostics"></a>Programmet diagnosticering

Programmet diagnosticering gør det muligt at registrere oplysninger, der er oprettet med et webprogram. ASP.NET-programmer kan bruge den `System.Diagnostics.Trace` klasse til at logge oplysninger programmets diagnosticering logfil.

Finde detaljerede oplysninger om, hvordan du konfigurerer dit program til logføring, kan du se [aktivere logføring for webapps i Azure App Service diagnosticering](web-sites-enable-diagnostic-log.md).

#### <a name="use-remote-profiling"></a>Brug af Remote profiler

I Azure App Service kan Web Apps, API Apps og WebJobs blive fra en fjernplacering præsenteret. Hvis processen kører langsommere end forventet, eller forsinkelse på HTTP-anmodninger er højere end normal og processens CPU-brug er også høj, kan du fra en fjernplacering profil processen og får CPU udvalg opkald stakke til at analysere procesaktivitet og kode varmt stier.

Du kan finde flere oplysninger om, [Remote profiler support i Azure App-tjeneste](/blog/remote-profiling-support-in-azure-app-service).


#### <a name="use-the-azure-app-service-support-portal"></a>Bruge Azure App Service Support-portalen

Web Apps giver dig mulighed for at foretage fejlfinding af problemer i forbindelse med din online ved at kigge på http-logfiler, hændelseslogfiler, gemmer proces og mere. Du kan få adgang til alle disse oplysninger ved hjælp af vores Support-portalen på **http://&lt;appnavnet på din >.scm.azurewebsites.net/Support**

Portalen Azure App tjeneste understøtter giver dig tre separate faner til at understøtte de tre trin af et almindelige scenarie med fejlfinding:

1.  Overhold aktuelle funktionsmåde
2.  Analysere ved at indsamle diagnosticeringsoplysninger og køre de indbyggede programmer til analyse
3.  Reducere

Hvis problemet sker lige nu, skal du klikke på **analysér** > **diagnosticering** > **Diagnosticere nu** til at oprette en diagnose-session for dig, som indsamler HTTP logger, Fremviser hændelseslogfiler, gemmer, PHP fejllogge og PHP behandle rapport hukommelse.

Når dataene er indsamlet, den også køre en analyse af data og giver dig en HTML-rapport.

I tilfælde af, at du vil hente data, som standard, vil det være gemt i mappen D:\home\data\DaaS.

Du kan finde flere oplysninger om understøttelse af Azure App Service-portalen, [Nye opdateringer Support websted udvidelse til Azure websteder](/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Brug konsollen Kudu fejlfinding

Web Apps leveres med en fejlfinding konsol, der kan bruges til fejlfinding, udforske, overførsel af filer samt JSON slutpunkter for oplysninger om dit miljø. Dette kaldes _Kudu Console_ eller _SCM Dashboard_ for din online.

Du kan få adgang til dette dashboard ved at gå til linket **https://&lt;appnavnet på din >.scm.azurewebsites.net/**.

Nogle af de ting, som indeholder et Kudu er:

-   miljøindstillinger for dit program
-   Log værdistrøm
-   lagring af diagnostik
-   foretage fejlfinding af konsol, hvor du kan køre Powershell-cmdletter og grundlæggende DOS-kommandoer.


En anden nyttig funktion af Kudu er, at i tilfælde af, at dit program er aktiverende første undtagelser, du kan bruge Kudu og værktøjet SysInternals Procdump til at oprette hukommelse gemmer. Disse hukommelse gemmer er øjebliksbillede af processen og ofte kan hjælpe dig med at foretage fejlfinding af mere kompliceret problemer med din online.

Du kan finde flere oplysninger om funktionerne i Kudu, [Azure websteder Team Services-værktøjer, du bør vide om](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. reducere problemet

####    <a name="scale-the-web-app"></a>Skalere WebApp

Du kan justere skalaen, hvor du kører dit program i Azure App-tjeneste til forbedret ydeevne og overførselshastighed. Skalering af en WebApp omfatter to relaterede handlinger: ændring af din App Service plan til et højere priser trin og konfiguration af visse indstillinger, når du har skiftet til det nyere priser niveau.

Du kan finde flere oplysninger om skalering, [skala en WebApp i Azure App Service](web-sites-scale.md).

Desuden kan du vælge at køre dit program på mere end én forekomst. Dette ikke kun giver dig mulighed for flere behandling, men også giver dig nogle mængde fejltolerance. Hvis processen går ned på én forekomst, fortsat anden forekomsten stadig fungerer anmodninger.

Du kan angive skalering til manuel eller automatisk.

####    <a name="use-autoheal"></a>Brug AutoHeal

AutoHeal genbruger arbejdsprocessen for din app, der er baseret på indstillinger, du vælger (som ændringer i konfigurationen, anmodninger, hukommelse-baserede begrænsninger eller den tid, der er behov for at udføre en anmodning om). De fleste tilfælde, er Papirkurv processen den hurtigste måde til at gendanne fra et problem. Selvom du kan altid genstarter online fra direkte i portalen Azure, AutoHeal skal det gøres automatisk for dig. Alt, du skal gøre, er tilføje nogle udløsere i roden web.config for din online. Bemærk, at disse indstillinger vil fungerer på samme måde, selvom dit program ikke er en .net en.

Du kan finde yderligere oplysninger finder [automatisk reparation Azure-websteder](/blog/auto-healing-windows-azure-web-sites/).

####    <a name="restart-the-web-app"></a>Genstart af WebApp

Dette er ofte den nemmeste måde at gendanne fra enkeltstående problemer. [Azure-portalen](https://portal.azure.com/)har på din online blade, du indstillinger for at stoppe eller genstarte din app.

 ![Genstart online for at løse problemer med ydeevnen](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Du kan også administrere din online ved hjælp af Azure Powershell. Se [Bruge Azure PowerShell med Azure ressourcestyring](../powershell-azure-resource-manager.md)kan finde flere oplysninger.
