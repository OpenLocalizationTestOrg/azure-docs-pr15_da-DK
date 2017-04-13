<properties
    pageTitle="Føje funktioner til din første online"
    description="Føje smarte funktioner til din første online i et par minutter."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/12/2016"
    ms.author="cephalin"
/>

# <a name="add-functionality-to-your-first-web-app"></a>Føje funktioner til din første online

I [Implementer din første online til Azure i fem minutter](app-service-web-get-started.md), du har installeret en stikprøve web-app til [Azure App-tjenesten](../app-service/app-service-value-prop-what-is.md). I denne artikel får du hurtigt tilføje nogle fantastiske funktioner i din udløst online. I et par minutter skal du:

- gennemtvinge godkendelse til brugerne
- tilpasse din app automatisk
- modtage påmindelser på ydeevnen for din app

Uanset hvilken eksempel-app, du har installeret i den forrige artikel, kan du følge med i selvstudiet.

De tre aktiviteter i dette selvstudium er kun nogle få eksempler på de mange nyttige funktioner, du får, når du placerer din online i App-tjeneste. Mange af funktionerne er tilgængelige i **gratis** niveau (som er, at det dit første online der kører på), og du kan bruge din prøveversion kredit til at teste funktioner, der kræver højere priser niveauer. Kan være sikker på, at din online forbliver i **Free** lag, medmindre du eksplicit ændres det til en anden priser niveau.

>[AZURE.NOTE] Den WebApp, du har oprettet med Azure CLI kører i **Free** lag, som kun tillader én delt VM forekomst med kvoter for serverforbrug. Du kan finde flere oplysninger om, hvad du får med **gratis** niveau, [App Service begrænsninger](../azure-subscription-service-limits.md#app-service-limits).

## <a name="authenticate-your-users"></a>Godkende dine brugere

Nu, Lad os se, hvor nemt det er at føje godkendelse til din app (yderligere læsning på [App Service godkendelse/godkendelse](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/)).

1. I portalen blade for din app, som du lige har åbnet skal du klikke på **Indstillinger for** > **godkendelse / godkendelse**.  
    ![Godkende - indstillinger blade](./media/app-service-web-get-started/aad-login-settings.png)

2. Klik **på** slå godkendelse.  

4. Klik på **Azure Active Directory**i **Godkendelsesprovidere**.  
    ![Godkende – Vælg Azure Active Directory](./media/app-service-web-get-started/aad-login-config.png)

5. Klik på **Express**i bladet **Azure Active Directory-indstillinger** og derefter klikke på **OK**. Oprette en ny standardindstillingerne Azure AD-programmet i din standard-mappe.  
 ![Godkende - express konfiguration](./media/app-service-web-get-started/aad-login-express.png)

6. Klik på **Gem**.  
    ![Godkende - Gem konfiguration](./media/app-service-web-get-started/aad-login-save.png)

    Når ændringen er gået igennem, kan du se klokken meddelelse bliver grøn, sammen med en venlig meddelelse.

7. Klik på den **URL-** hyperlink (eller **Gå** i menulinjen) tilbage i bladet portalen af din app. Linket er en HTTP-adresse.  
    ![Godkende - gå til URL-adresse](./media/app-service-web-get-started/aad-login-browse-click.png)  
    Men når den åbnes appen på en ny fane, URL-adressen feltet omdirigeringer flere gange og afsluttes på din app med en HTTPS-adresse. Hvad du ser er, at du allerede er logget på abonnementet Azure, og du er automatisk godkendelse i appen.  
    ![Godkende - logget på](./media/app-service-web-get-started/aad-login-browse-http-postclick.png)  
    Så hvis du nu åbner en ikke-godkendte session i en anden browser, får du vist en logonskærmen, når du navigerer til den samme URL-adresse.  
    <!-- ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png)  -->
   Hvis du aldrig har gjort noget med Azure Active Directory, har din standardmappen muligvis ikke alle Azure AD-brugere. I så fald er sandsynligvis den eneste konto der Microsoft-kontoen til din Azure-abonnement. Det er grunden du automatisk er logget på app i den samme browser tidligere.
   Du kan bruge den samme Microsoft-konto kan logge på denne logonside.

Tillykke, du godkender al trafik til din online.

Du har muligvis bemærket i den **godkendelse / godkendelse** blade, som du kan gøre meget mere, f.eks.:

- Aktivere sociale logon
- Aktivere flere logonindstillinger for
- Ændre standardfunktionsmåden, når personer først gå til din app

App Service indeholder en slå nøgle løsning til nogle af de almindelige godkendelse skal, så du ikke behøver at angive godkendelse logik dig selv.
Du kan finde yderligere oplysninger finder [App Service godkendelse/godkendelse](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

## <a name="scale-your-app-automatically-based-on-demand"></a>Tilpasse din app automatisk på baggrund af efterspørgsel

Næste, Lad os Autoskalering din app så den automatisk vil justere det kapacitet til at reagere på brugernes behov (yderligere læsning på [skala af din app i Azure](web-sites-scale.md) og [skalere forekomst Tæl manuelt eller automatisk](../monitoring-and-diagnostics/insights-how-to-scale.md)).

En kort, du skalere din online på to måder:

- [Skalere op](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): få flere CPU, hukommelse, diskplads og ekstra funktioner som dedikeret FOS, brugerdefinerede domæner og certifikater, arrangere pladser, autoskalering og meget mere. Du kan skalere ved at ændre det priser niveau i den App serviceaftale din app tilhører.
- [Skaler ud](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): øge antallet af VM forekomster, der kører din app.
Du kan skalere ud med op til 50 forekomster, afhængigt af din priser niveau.

Uden yderligere ado, Lad os oprette Autoskalering.

1. Først skal Lad os skalere op til at aktivere Autoskalering. Klik på **Indstillinger**i bladet portalen af din app > **Skala op (App planlægge Service)**.  
    ![Skalere op - indstillinger blade](./media/app-service-web-get-started/scale-up-settings.png)

2. Rul ned, og vælg **S1 Standard** niveauet, det laveste niveau, der understøtter Autoskalering (med cirkel på skærmbillede), og klik derefter på **Vælg**.  
    ![Skalere op – Vælg niveau](./media/app-service-web-get-started/scale-up-select.png)

    Du er færdig skalering op.

    >[AZURE.IMPORTANT] Dette niveau expends din gratis prøveversion kredit. Hvis du har en konto løn i brug, medfører gebyrer til din konto.

3. Næste, Lad os konfigurere Autoskalering. Klik på **Indstillinger**i bladet portalen af din app > **Skala ud (App planlægge Service)**.  
    ![Skaler ud - indstillinger blade](./media/app-service-web-get-started/scale-out-settings.png)

4. Ændre **skaleringen af** **CPU**procentdel. Skyderne under rullemenuen opdatere i overensstemmelse hermed. Derefter definere et **forekomster** område mellem **1** og **2** og et **målområde** mellem **40** og **80**. Gøre det ved at skrive i felterne eller ved at flytte skyderne.  
 ![Skalere ud - konfigurere Autoskalering](./media/app-service-web-get-started/scale-out-configure.png)

    Baseret på denne konfiguration, skaleres din app automatisk ud af, når CPU-forbrug er større end 80% og skalering i, når CPU-forbrug er under 40%.

5. Klik på **Gem** i menulinjen.

Tillykke, din app er Autoskalering.

Du har muligvis bemærket i bladet **Indstillinger for skalering** , som du kan gøre meget mere, f.eks.:

- Skalere til et bestemt antal forekomster manuelt
- Skalere med andre ydeevne for statistik, som hukommelse procent eller en disk kø
- Tilpasse skaleringen, når en regel for ydeevnen udløses
- Autoskalering på en tidsplan
- Angive Autoskalering funktionsmåden for en fremtidig begivenhed

Se [skalere op din app i Azure](../app-service-web/web-sites-scale.md)kan finde flere oplysninger om skalering af din app. Se [skalere forekomst Tæl manuelt eller automatisk](../monitoring-and-diagnostics/insights-how-to-scale.md)kan finde flere oplysninger om skalering af.

## <a name="receive-alerts-for-your-app"></a>Modtage beskeder for din app

Nu, hvor din app er Autoskalering, hvad sker der, når den maksimale forekomst TÆL (2) og CPU er over ønskede udnyttelse (80%)?
Du kan konfigurere en besked (yderligere læsning på [Modtag påmindelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)) til at informere dig om problemet, så du kan yderligere tilpasse op/ud din app, f.eks. Lad os hurtigt at oprette en besked for dette scenario.

1. Klik på **Funktioner**i bladet portalen af din app > **beskeder**.  
    ![Påmindelser - indstillinger blade](./media/app-service-web-get-started/alert-settings.png)

2. Klik på **Tilføj påmindelse**. Vælg derefter den ressource, der slutter med **(serverfarms)**i feltet **ressource** . Det er din App-serviceaftale.  
    ![Påmindelser - tilføje besked for App-serviceaftale](./media/app-service-web-get-started/alert-add.png)

3. Angiv **navn** som `CPU Maxed`, **metrisk** som **CPU procentdel**og **grænseværdi** som `90`, vælg derefter **mail ejere, bidragydere, og læsere**, og klik derefter på **OK**.   
 ![Konfigurere påmindelser - besked](./media/app-service-web-get-started/alert-configure.png)

    Når Azure er færdig med at oprette beskeden, skal du se den i bladet **beskeder** .  
    ![Påmindelser - færdig visning](./media/app-service-web-get-started/alert-done.png)

Tillykke, du får nu beskeder.

Beskeder om indstillingen kontrollerer CPU-forbrug hver fem minutter. Hvis dette nummer går over 90%, modtager du en besked via mail, sammen med alle, der er godkendt. Gå tilbage til portalen blade af din app for at få vist alle, der har tilladelse til at modtage beskeder, og klik på knappen **tilgængelighed** .  
![Se, hvem der får beskeder](./media/app-service-web-get-started/alert-rbac.png)

Du skal se, at **abonnement administratorer** allerede **ejer** af appen. Denne gruppe skal du medtage, hvis du er kontoadministratoren af abonnementet Azure (fx dit prøveabonnement). Du kan finde flere oplysninger om Azure rollebaseret adgangskontrol, [Azure Role-Based adgangskontrol](../active-directory/role-based-access-control-configure.md).

> [AZURE.NOTE] Regler for påmindelser er en Azure funktion. Du kan finde yderligere oplysninger finder [Modtag påmindelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

## <a name="next-steps"></a>Næste trin

På din egen måde til at konfigurere beskeden, kan du har bemærket et bredt udvalg af funktioner i bladet **værktøjer** . Her kan du kan foretage fejlfinding af problemer, overvåge ydeevnen teste for svagheder, administrere ressourcer, interagere med konsollen VM og Tilføj nyttige udvidelser. Vi inviterer dig til at klikke på hver enkelt af disse værktøjer til at finde de enkle og effektive værktøjer på din finger tip.

Finde ud af, hvordan du gør mere med din udløst app. Her er en delvis liste:

- [Købe og konfigurere et brugerdefineret domænenavn](custom-dns-web-site-buydomains-web-app.md) - købe en flot domæne til din online i stedet for den *. azurewebsites.net domæne. Eller brug et domæne, du allerede har.
- [Konfigurere arrangere miljøer](web-sites-staged-publishing.md) - Installer din app til en midlertidig URL før ibrugtagning fremstilling. Opdater din direkte online uden bekymringer. Konfigurere en udførlige DevOps-løsning med flere installation pladser.
- [Konfigurere fortløbende installation](app-service-continuous-deployment.md) - integrere appinstallation i systemet kilde kontrolelement. Installer Azure med hver bekræftelse.
- [Access lokale ressourcer](web-sites-hybrid-connection-get-started.md) - adgang til en eksisterende lokal database eller CRM-systemet.
- [Sikkerhedskopiere din app](web-sites-backup.md) - konfigurere tilbage og Gendan for din online. Forberede uventet fejl og gendanne fra dem.
- [Aktivere diagnosticeringslogfiler](web-sites-enable-diagnostic-log.md) - Læs IIS-logfilerne fra Azure eller program sporinger. Læse dem i en stream, hente dem eller port dem i [Programmet indsigt](../application-insights/app-insights-overview.md) til slå nøgle analyse.
- [Scanne din app til sikkerhedsrisici](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) -
scanne din online mod moderne trusler ved hjælp af tjeneste fra [Tinfoil sikkerhed](https://www.tinfoilsecurity.com/).
- [Køre baggrundsjob](../azure-functions/functions-overview.md) - Kør job for databehandling reporting osv.
- [Få mere at vide, hvordan fungerer App Service](../app-service/app-service-how-works-readme.md)
