<properties
    pageTitle="Flighting installation (test af betaversion) i Azure App Service"
    description="Lær at flight nye funktioner i din app eller betaversion test dine opdateringer i selvstudiet til slut. Den samler App Service funktioner som sammenhængende udgivelse, pladser, trafik routing og integration af programmet indsigt."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="cephalin"/>
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Flighting installation (test af betaversion) i Azure App Service

Dette selvstudium viser, hvordan du gør *flighting installationer* ved at integrere de forskellige funktioner i [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) og [Azure programmet indsigt](/services/application-insights/). 

*Flighting* er en installation proces, der har valideret en ny funktion eller ændre med et begrænset antal reelle kunder og en overordnet tester i produktionsmiljø. Det kan sammenlignes med beta test og er nogle gange kaldet "kontrolleret test flight". Mange store virksomheder med en web tilstedeværelse Følg denne fremgangsmåde for at finde tidlig validering deres app-opdateringer i deres vane [fleksible udvikling](https://en.wikipedia.org/wiki/Agile_software_development). Azure App Service gør det muligt at integrere test fremstilling med uafbrudt publicering og programmet indsigt at implementere det samme DevOps scenario. Fordelene ved denne fremgangsmåde omfatter:

- **Reelle feedback _før_ opdateringer udgives fremstilling gevinst** - det eneste, der er bedre end at få feedback, så snart du slipper er at få feedback, før du slipper. Du kan teste opdateres med ægte bruger trafik og -funktionsmåder så tidligt som du ønsker i produktets livscyklus.
- **Forbedre [test-driven af konstant forsendelse udvikling (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development) ** - ved at integrere test fremstilling med kontinuert integration og instrumentation med programmet indsigt validering af bruger sker tidlig og automatisk i produktets livscyklus. Dette hjælper med at reducere tid investeringer i udførelse af manuel test.
- **Optimer teste arbejdsproces** - ved at automatisere test fremstilling med kontinuert overvågning instrumentation, du kan potentielt udføre forskellige typer af test i en enkelt proces, som [integration](https://en.wikipedia.org/wiki/Integration_testing), [regression](https://en.wikipedia.org/wiki/Regression_testing), [brugervenligheden](https://en.wikipedia.org/wiki/Usability_testing), hjælp til handicappede, lokalisering, [ydeevne](https://en.wikipedia.org/wiki/Software_performance_testing), [sikkerhed](https://en.wikipedia.org/wiki/Security_testing)og [accept](https://en.wikipedia.org/wiki/Acceptance_testing)mål.

En flighting installation ikke så godt routing-direkte trafik. I Sådan en installation, du vil få indsigt så hurtigt som muligt, om det være en uventet fejl, er forringet ydeevne, bruger oplevelse problemer. Husk, at du arbejder med reelle kunder. Så det kan gøres højre, skal du sikre dig, at du har konfigureret din flighting installation til at indsamle alle de data, du skal bruge til at foretage en informeret beslutning til det næste trin. Dette selvstudium viser, hvordan du indsamle data med programmet viden, men du kan bruge nye Relic eller andre teknologier, der passer til det pågældende scenarie. 

## <a name="what-you-will-do"></a>Hvad du vil gøre

I dette selvstudium lærer du at få de følgende scenarier sammen for at teste din App Service-app i fremstilling:

- [Distribuere fremstilling trafik](app-service-web-test-in-production-get-start.md) til dit beta-app
- [Instrument din app](../application-insights/app-insights-web-track-usage.md) til at hente nyttige målepunkter
- Installer din beta app og spore direkte app målepunkter løbende
- Sammenligne målepunkter mellem fremstilling app og beta-app til at se, hvordan kodeændringer oversættes til resultater

## <a name="what-you-will-need"></a>Du skal bruge:

-   En Azure-konto
-   En [GitHub](https://github.com/) -konto
- Visual Studio-2015 - kan du hente den [Community edition](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).
-   Ciffer Shell (installeret med [GitHub til Windows](https://windows.github.com/)) - Dette gør det muligt at køre både ciffer og PowerShell-kommandoer i den samme session
-   Seneste [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) -bit
-   Grundlæggende forståelse af følgende:
    -   Installation af [Azure ressourcestyring](../azure-resource-manager/resource-group-overview.md) skabelon (se [Implementer en kompleks programmet forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md))
    -   [Ciffer](http://git-scm.com/documentation)
    -   [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [AZURE.NOTE] Du skal bruge en Azure-konto til at udføre dette selvstudium:
> + Du kan [åbne en Azure-konto gratis](/pricing/free-trial/) – du får kredit du kan bruge til at prøve betalt Azure tjenester, og selv efter at de er vant du holder kontoen, og brug ledig Azure tjenester, som Web Apps.
> + Du kan [aktivere Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/) – dit Visual Studio-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.
>
> Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

## <a name="set-up-your-production-web-app"></a>Konfigurere din fremstilling WebApp

>[AZURE.NOTE] Det script, der bruges i dette selvstudium konfigureres automatisk fortløbende udgivelse fra din GitHub lager. Dette kræver, at legitimationsoplysningerne GitHub allerede er gemt i Azure, ellers script installationen mislykkes, når du forsøger at konfigurere indstillinger for versionsstyring for webapps.
>
>For at gemme dine GitHub legitimationsoplysninger i Azure, du Opret en WebApp i [Azure portalen](https://portal.azure.com/) og [konfigurere GitHub installation](app-service-continuous-deployment.md#Step7). Du skal kun gøre det én gang.

I et typisk DevOps scenarie, du har et program, der kører direkte i Azure, og du vil foretage ændringer i den gennem fortløbende udgivelse. I dette scenarie skal vil du installere fremstilling en skabelon, du har udviklet og testet.

1.  Oprette din egen forgreningsovergang [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -lager. Du kan finde oplysninger om oprettelse af din forgreningsovergang [forgreningsovergang en Repo](https://help.github.com/articles/fork-a-repo/). Når din forgreningsovergang er oprettet, kan du se den i din browser.

    ![](./media/app-service-agile-software-development/production-1-private-repo.png)

2.  Åbne en ciffer Shell session. Hvis du endnu ikke har ciffer Shell, [GitHub til Windows](https://windows.github.com/) Installer nu.
3.  Oprette en lokal kopi af din forgreningsovergang ved at udføre følgende kommando:

        git clone https://github.com/<your_fork>/ToDoApp.git

4.  Når du har din lokale Klon, gå til * &lt;repository_root >*\ARMTemplates, og kør deploy.ps1 et script med et entydigt suffiks, som vist nedenfor:

        .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>

4.  Når du bliver bedt om det, kan du skrive i det ønskede brugernavn og adgangskode til database access. Husk legitimationsoplysningerne database, fordi du skal angive dem igen, når du opdaterer ressourcegruppen.

    Du bør se klargøring status for forskellige Azure ressourcer. Når installationen er fuldført, kan scriptet Start programmet i browseren, og giver dig et brugervenligt bip.
    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)

6.  Kør tilbage i sessionen ciffer Shell:

        .\swap –Name ToDoApp<your_suffix>

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)

7.  Når scriptet afsluttes, skal du gå tilbage til at finde frem til den front end-adresse (http://ToDoApp*&lt;your_suffix >*.azurewebsites.net/) til at se det program, der kører i fremstilling.
5.  Log på [Azure-portalen](https://portal.azure.com/) , og se nærmere på Hvad er oprettet.

    Du skal kunne se to webapps i samme ressourcegruppe, hvoraf den ene bruger den `Api` suffiks i navnet. Hvis du ser på gruppen ressourcevisning, kan du også se den SQL-Database og server, App-serviceaftale og de midlertidige pladser til webapps. Gennemse de forskellige ressourcer og sammenligne dem med * &lt;repository_root >*\ARMTemplates\ProdAndStage.json at se, hvordan de konfigureres i skabelonen.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Du har konfigureret fremstilling app.  Nu, Lad os forestille dig, at du modtager feedback, brugervenligheden er dårlig for appen. Så du beslutter dig for at undersøge. Du skal instrumentere din app for at give dig feedback.

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>Undersøge: Instrumentere din klient-app til overvågning/målepunkter

5. Åbn * &lt;repository_root >*\src\MultiChannelToDo.sln i Visual Studio.
6. Gendanne alle Nuget pakker ved at højreklikke på løsning > **Administrere NuGet pakker til løsning** > **gendanne**.
6. Højreklik på **MultiChannelToDo.Web** > **Tilføje programmet indsigt Telemetri** > **Konfigurer indstillinger** > Skift ressourcegruppe til ToDoApp*&lt;your_suffix >* > **Tilføje programmet indsigt til projekt**.
7. Åbn bladet for **MultiChannelToDo.Web** programmet indsigt ressourcen i Azure-portalen. Klik derefter på **Lær at indsamle browser siden Indlæs data** i webdelen **programmet tjenestetilstand** > Kopier kode.
7. Tilføje den kopierede JS instrumentation kode til * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, lige før lukningen `<heading>` mærke. Den skal indeholde entydige instrumentation tast til dit program indsigt ressource.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>

11. Sende brugerdefinerede hændelser programmet indsigt til mus Klik ved at tilføje følgende kode i bunden af brødtekst:

        <script>
            $(document.body).find("*").click(function(event) {

                appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
            });
        </script>

    Denne JavaScript-kodestykket sender en brugerdefineret hændelse til programmet viden, hver gang en bruger klikker på et vilkårligt sted i WebApp.

12. Gem i ciffer Shell, og Overfør dine ændringer til din forgreningsovergang i GitHub. Vent derefter klienter mulighed for at opdatere browseren.

        git add -A :/
        git commit -m "add AI configuration for client app"
        git push origin master

6.  Ombyt fremstilling udløst app ændringerne:

        .\swap –Name ToDoApp<your_suffix>

13. Gå til programmet indsigt ressourcen, du har konfigureret. Klik på brugerdefineret begivenheder.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

    Hvis du ikke kan se måleredskaber for brugerdefinerede hændelser, vent et par minutter, og klik på **Opdater**.

Antag, at du får vist som et diagram under:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

Og begivenhed gitteret nedenfor:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

**Knappen** begivenheden svarer til sendeknappen ifølge din ToDoApp programkode, og hændelsen **INPUT** svarer til tekstfeltet. Så langt mening ting. Men det ser ud til der er en god mængden af Klik og meget få klik på opgaveelementer ( **LI** hændelser).

Baseret på det, du formular forvirret din hypotetisk, der er nogle brugere, hvilken del af Brugergrænsefladen er muligt at klikke, og det skyldes, at markøren er formateret til markeret tekst, når den holdes på listeelementerne og deres ikoner.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Det kan være et contrived eksempel. Alligevel, du vil sikre en forbedring af din app, og udfør derefter en flighting installation for at få brugervenligheden feedback fra direkte kunder.

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>Instrumentere din server-app til overvågning/målepunkter
Dette er en tangens, da dette scenario, der er vist i dette selvstudium omhandler kun klient-app. Dog for fuldstændighed vil du konfigurere serversiden app.

6. Højreklik på **MultiChannelToDo** > **Tilføje programmet indsigt Telemetri** > **Konfigurer indstillinger** > Skift ressourcegruppe til ToDoApp*&lt;your_suffix >* > **Tilføje programmet indsigt til projekt**.
12. Gem i ciffer Shell, og Overfør dine ændringer til din forgreningsovergang i GitHub. Vent derefter klienter mulighed for at opdatere browseren.

        git add -A :/
        git commit -m "add AI configuration for server app"
        git push origin master

6.  Ombyt fremstilling udløst app ændringerne:

        .\swap –Name ToDoApp<your_suffix>

Det var det!

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>Undersøge: Føje slot-specifikke mærker til din klient app målepunkter

I dette afsnit, skal konfigurere du forskellige installation pladser for at sende slot-specifikke telemetri til den samme ressource programmet indsigt. Denne måde, du kan sammenligne telemetridata mellem trafik fra forskellige pladser (installation miljøer) for at nemt se effekten af ændringerne app. Du kan adskille fremstilling trafik fra resten ad gangen, så du kan fortsætte med at overvåge din fremstilling app efter behov.

Da du indsamling af data på klienten funktionalitet, kan du [tilføje en telemetri initialisering til JavaScript-kode](../application-insights/app-insights-api-custom-events-metrics.md#js-initializer) i index.cshtml. Hvis du vil teste serversiden ydeevne, f.eks, du kan også gøre på samme måde i din server-kode (se [Program indsigt API til brugerdefinerede hændelser og målepunkter](../application-insights/app-insights-api-custom-events-metrics.md).

1. Først skal du tilføje kode bewteen to `//` kommentarer under i JavaScript'et blokere, som du har føjet til den `<heading>` mærke tidligere.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Medfører, at denne initialisering kode på `appInsights` objekt til at tilføje de en brugerdefineret egenskab kaldet `Environment` på hver enkelt telemetri, sender programmet.

2. Dernæst skal tilføje denne brugerdefinerede egenskab som en [slot indstilling](web-sites-staged-publishing.md#AboutConfiguration) for din online i Azure. For at gøre dette skal du køre følgende kommandoer i sessionen ciffer Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    Web.config i dit projekt allerede definerer den `environment` app indstilling. Med denne indstilling, når du tester app lokalt, din målepunkter vil blive mærket med `VS Debugger`. Men, når du klikker dine ændringer til Azure, Azure vil finde og bruge den `environment` app i stedet indstillingen i det online konfiguration, og din målepunkter mærket med `Production`.

3. Gem og overføre ændringerne kode til din forgreningsovergang på GitHub, og derefter vente på dine brugere at bruge den nye app (grund til at opdatere browseren). Det tager om 15 minutter, før den nye egenskab, der skal vises i dit program indsigt `MultiChannelToDo.Web` ressource.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master

4. Nu skal du gå til bladet **brugerdefinerede hændelser** igen og filtrere målene på `Environment=Production`. Du bør nu kunne se alle de nye brugerdefinerede hændelser i fremstilling slotten med dette filter.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)

5. Klik på knappen **Favoritter** for at gemme de aktuelle indstillinger for målepunkter Explorer til noget i retning af **brugerdefinerede hændelser: fremstilling**. Du kan nemt skifte mellem denne visning og en installation slot senere.

    > [AZURE.TIP] Overvej at [integrere dit program indsigt ressource med Power BI](../application-insights/app-insights-export-power-bi.md)for endnu mere effektive analyser.

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>Føje slot-specifikke mærker til din server app målepunkter
Igen, for fuldstændighed du vil konfigurere serversiden app. I modsætning til appen klient, der anvendes i JavaScript slot-specifikke koder til appen server er udstyret med .NET kode.

1. Åbn * &lt;repository_root >*\src\MultiChannelToDo\Global.asax.cs. Tilføje kode blokering nedenfor, lige før lukningen navneområde klammeparentes.

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }

2. Rette navn opløsning fejl ved at tilføje den `using` sætninger under til starten af filen:

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;

3. Føj kode under til starten af den `Application_Start()` metode:

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());

3. Gem og overføre ændringerne kode til din forgreningsovergang på GitHub, og derefter vente på dine brugere at bruge den nye app (grund til at opdatere browseren). Det tager om 15 minutter, før den nye egenskab, der skal vises i dit program indsigt `MultiChannelToDo` ressource.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>Opdater: Konfigurere dit beta gren

2. Åbn * &lt;repository_root >*\ARMTemplates\ProdAndStagetest.json og find den `appsettings` ressourcer (søge efter `"name": "appsettings"`). Der er 4 af dem, én for hvert stik. 

2. For hver `appsettings` ressource, tilføje en `"environment": "[parameters('slotName')]"` app indstilling til slutningen af den `properties` matrix. Glem ikke at afslutte den forrige linje med et komma.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)
    
    Du lige har tilføjet den `environment` app indstilling til alle pladser i skabelonen.
    
2. I den samme fil, finde den `slotconfignames` ressourcer (søge efter `"name": "slotconfignames"`). Der er 2 af dem, en for hver app.

2. For hver `slotconfignames` ressource, tilføje `"environment"` til slutningen af den `appSettingNames` matrix. Glem ikke at afslutte den forrige linje med et komma.

    Du lige har foretaget den `environment` app indstille stick til dens respektive installation plads til begge apps.  

3. Kør følgende kommandoer i sessionen ciffer Shell og med det samme gruppe suffiks til ressource, du har brugt før.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta

4. Når du bliver bedt om det, kan du angive de samme SQL-databaselegitimationsoplysninger som før. Når du bliver bedt om at opdatere ressourcegruppen, Skriv `Y`, derefter `ENTER`.

    Når scriptet afsluttes, alle dine ressourcer i den oprindelige ressourcegruppe bevares, men en ny slot med navnet "beta" er oprettet i den med den samme konfiguration som den "Midlertidige" slot, der er oprettet i begyndelsen.

    >[AZURE.NOTE] Denne metode til oprettelse af forskellige installation miljøer er forskellig fra metoden, [fleksible softwareudvikling med Azure App Service](app-service-agile-software-development.md). Her kan oprette du installation miljøer med installation pladser, hvor, som der du oprette installation miljøer med ressourcegrupper. Administrere installation miljøer med ressourcegrupper kan du holde produktionsmiljøet for udviklere, men det er ikke nemt at gøre test i fremstilling, som du let kan gøre med pladser.

Hvis du vil, kan du også oprette en alpha app ved at køre

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

I dette selvstudium, du vil bare fortsætte med at bruge din beta-app.

## <a name="update-push-your-updates-to-the-beta-app"></a>Opdater: Push dine opdateringer til beta-app

Tilbage til din app, du vil forbedre.

1. Sørg for, at du er nu i din beta gren

        git checkout beta

2. I * &lt;repository_root >*\src\MultiChannelToDo.Web\app\Index.cshtml, finde den `<li>` mærke og tilføje den `style="cursor:pointer"` attribut, som vist nedenfor.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)

3. Anvend og opslagsnål til Azure.

4. Kontrollér, at ændringen nu afspejles i beta stikket ved at navigere til http://todoapp*&lt;your_suffix >*-beta.azurewebsites.net/. Hvis du ikke kan se ændringen endnu, kan du opdatere din browser for at få den nye javascript-kode.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Nu hvor du har din ændring, der kører i beta-stik, er du klar til at udføre en flighting installation.

## <a name="validate-route-traffic-to-the-beta-app"></a>Validere: Dirigere trafik til beta-app

I dette afsnit, vil du omdirigere trafikken til beta-app. For sake of klarhed af demonstration vil du omdirigere en betydeligt del af bruger trafikken til den. I virkeligheden afhænger antallet af brugere, du vil omdirigere din bestemte situation. Eksempelvis hvis webstedet er på skalaen på microsoft.com, kan du skal mindre end en procentdel af dit samlede trafik for at få nyttige data.

1. Kør følgende kommandoer til at dirigere halvdelen af fremstilling trafik til beta plads i sessionen ciffer Shell:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

  Den `ReroutePercentage=50` egenskaben angiver, at 50% af fremstilling trafikken bliver dirigeret til appen beta URL-adresse (angivet i den `ActionHostName` egenskaben).

2. Gå til http://ToDoApp*&lt;your_suffix >*. azurewebsites.net. 50% af trafikken skal nu omdirigeres til beta plads.

3. Filtrer målene efter miljø i dit program indsigt ressource = "beta".

    > [AZURE.NOTE] Hvis du gemmer denne filtrerede visning som en anden favorit, kan du nemt spejlvende visningerne metriske explorer mellem fremstilling og beta visninger.

Antag i programmet viden, ser du noget i stil med følgende:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Ikke kun dette viser, at der er mange flere Klik på den `<li>` mærker, men der synes at være en forekomst af klikker på `<li>` mærker. Du kan derefter indgå, personer har opdaget den nye `<li>` mærker er klikbart og nu rydder alle deres tidligere fuldførte opgaver i appen.

Baseret på dataene for din flighting installation, beslutter du, at din nye brugergrænseflade er klar til fremstilling.

## <a name="go-live-move-your-new-code-into-production"></a>Gå direkte: flytte din nye kode til fremstilling

Nu er du klar til at flytte din opdatering til fremstilling. Hvad er velegnet er, at nu du ved, at din opdatering er allerede blevet valideret _før_ den rykkes fremstilling. Du kan nu sikkert installere den. Da du har foretaget en opdatering til appen AngularJS klient, valideret du kun klientsiden koden. Hvis du skulle foretage ændringer i back end-Web API-app, kan du bekræfte dine ændringer på samme måde og nemt.

1. I ciffer Shell, skal du fjerne den trafik routing regel ved at køre følgende kommando:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()

2. Køre ciffer kommandoer:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master

2. Vent et par minutter, før den nye kode skal installeres til den midlertidige plads, og klik derefter Start http://ToDoApp*&lt;your_suffix >*-staging.azurewebsites.net at bekræfte, at den nye opdatering varmet op i den midlertidige slot. Husk, at den din forgreningsovergang master gren er knyttet til den midlertidige plads af din app.

3. Nu Udskift den midlertidige slot til fremstilling

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>Oversigt ##

Azure App Service gør det nemt for små til mellemstore virksomheder at teste deres programmer på computeren med kundekontakt i fremstilling, noget, der er blevet udført traditionelt i store virksomheder. Dette selvstudium har forhåbentlig, givet dig den viden, skal du samler App Service og programmet indsigt for at få mulig flighting installation og endda andre test i fremstilling scenarier i din DevOps verden. 

## <a name="more-resources"></a>Flere ressourcer ##

-   [Fleksible softwareudvikling med Azure App Service](app-service-agile-software-development.md)
-   [Konfigurere arrangere miljøer til webapps i Azure App Service](web-sites-staged-publishing.md)
-   [Installere en kompleks program forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md)
-   [Redigering Azure ressourcestyring skabeloner](../resource-group-authoring-templates.md)
-   [JSONLint - JSON kontrollere](http://jsonlint.com/)
-   [Ciffer forgreninger – grundlæggende forgreninger og flette](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
-   [Azure PowerShell](../powershell-install-configure.md)
-   [Project Kudu Wiki](https://github.com/projectkudu/kudu/wiki)
