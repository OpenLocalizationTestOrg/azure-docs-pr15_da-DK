<properties
    pageTitle="Oprette en web-app fra Azure Marketplace | Microsoft Azure"
    description="Lær at oprette en ny WordPress web-app fra Azure Marketplace ved hjælp af portalen Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

<!-- Note: This article replaces web-sites-php-web-site-gallery.md -->

# <a name="create-a-web-app-from-the-azure-marketplace"></a>Oprette en web-app fra Azure Marketplace

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Azure Marketplace gør tilgængelige et bredt udvalg af populære Onlines udviklet af Microsoft, fra tredjepart firmaer, og Åbn kilde software initiativer. Eksempel: WordPress, Umbraco CMS, Drupal, osv. Disse webapps er bygget på et bredt udvalg af populære strukturer, som [PHP] i denne WordPress eksempel, [.NET], [Node.js], [Java]og [Python], f.eks. Er den browser, du bruger til [Azure Portal]for at oprette en web-app fra Azure Marketplace den kun software, du skal bruge.

I dette selvstudium lærer du, hvordan du:

* Finde og oprette online i Azure App-tjeneste, der er baseret på en skabelon til Azure Marketplace.
* Konfigurere Azure App Service indstillinger for den nye WebApp.
* Start og administrere din online.

Til dette selvstudium, vil du installere blogwebsted WordPress fra Azure Marketplace. Når du har fuldført trinnene i dette selvstudium, har du dit eget WordPress websted op og kører i skyen.

![Eksempel WordPress wep app dashboard][WordPressDashboard1]

Webstedet WordPress, som du vil installere i dette selvstudium bruger MySQL for databasen. Hvis du vil bruge i stedet for SQL-Database til databasen, skal du se [Project Nami], som er også tilgængelige via Azure Marketplace.

> [AZURE.NOTE]
> For at fuldføre dette selvstudium skal bruge du en Microsoft Azure-konto. Hvis du ikke har en konto, kan du [aktivere dit Visual Studio abonnement fordele] [ activate] eller [Tilmeld dig en gratis prøveversion][free trial].
>
> Hvis du vil Introduktion til Azure App Service, før du tilmelder dig en Azure-konto, skal du gå til [Prøve App Service]. Derfra kan du straks oprette en forbigående starter WebApp i App Service – ingen kreditkort er påkrævet, og der er ingen forpligtelser.

## <a name="find-and-create-a-web-app-in-azure-app-service"></a>Finde og oprette en WebApp i Azure App-tjeneste

1. Log på [Azure-portalen].

1. Klik på **Ny**.
    
    ![Oprette en ny Azure ressource][MarketplaceStart]
    
1. Søge efter **WordPress**, og klik derefter på **WordPress**. (Hvis du vil bruge SQL-Database i stedet for MySQL, skal du søge efter **Project Nami**).

    ![Søge efter WordPress på markedet][MarketplaceSearch]
    
1. Når du har læst beskrivelsen af WordPress-app, skal du klikke på **Opret**.

    ![Oprette WordPress WebApp][MarketplaceCreate]

## <a name="configure-azure-app-service-settings-for-your-new-web-app"></a>Konfigurere Azure App Tjenesteindstillinger for din nye WebApp

1. Når du har oprettet en ny WebApp, vises bladet WordPress indstillinger, som du skal bruge til at udføre følgende trin:

    ![Konfigurere indstillinger for WordPress web app][ConfigStart]

1. Angiv et navn til WebApp i feltet **WebApp** .

    Dette navn skal være entydige i azurewebsites.net domæne, fordi URL-adressen til WebApp bliver *{name}*. azurewebsites.net. Hvis det navn, du angiver ikke er entydige, vises et rødt udråbstegn i tekstfeltet.

    ![Konfigurere WordPress web app-navn][ConfigAppName]

1. Hvis du har mere end ét abonnement, skal du vælge det, du vil bruge. 

    ![Konfigurere abonnementet for WebApp][ConfigSubscription]

1. Vælg en **Ressourcegruppe** eller Opret en ny.

    Du kan finde flere oplysninger om ressourcegrupper se [Oversigt over Azure ressourcestyring][ResourceGroups].

    ![Konfigurere ressourcegruppen for WebApp][ConfigResourceGroup]

1. Vælg en **App Service plan/placering** eller Opret en ny.

    Du kan finde flere oplysninger om App Service planer, se [Oversigt over Azure App Service planer][AzureAppServicePlans]. 

    ![Konfigurere tjenesten planen for WebApp][ConfigServicePlan]

1. Klik på **Database**, og angiv derefter de ønskede værdier til konfiguration af din MySQL-database i bladet **Nye MySQL-Database** .

    en. Skriv et nyt navn, eller lad standardnavnet.

    b. Lad **Database Type** er angivet til **delt**.

    c. Vælg den samme placering, som den, du har valgt til WebApp.

    d. Vælg et priser trin. **Kviksølv** - som er gratis med minimale forbindelser og diskplads - er fint til dette selvstudium.

    e. Acceptere juridiske betingelser i bladet **Nye MySQL-Database** , og klik derefter på **OK**. 

    ![Konfigurere databaseindstillingerne web App][ConfigDatabase]

1. Acceptere juridiske betingelser i bladet **WordPress** , og klik derefter på **Opret**. 

    ![Afslutte web app-indstillinger og klikke på OK][ConfigFinished]

    Azure App Service opretter online, typisk i mindre end et minut. Du kan se status ved at klikke på klokkeikonet øverst på siden med portalen.

    ![Statusindikator][ConfigProgress]

## <a name="launch-and-manage-your-wordpress-web-app"></a>Start og administrere din WordPress online
    
1. Når web app oprettelse er afsluttet, gå på portalen Azure ressourcegruppen, hvor du har oprettet programmet, og du kan se WebApp og databasen.

    Ekstra ressourcen med ikonet pære er [Programmet indsigt][ApplicationInsights], som indeholder overvågning tjenester til din online.

1. Klik på web app-linjen i bladet **ressourcegruppe** .

    ![Vælg din WordPress WebApp][WordPressSelect]

1. Klik på **Gennemse**i bladet Web app.

    ![Gå til din WordPress online][WordPressBrowse]

1. Hvis du bliver bedt om at vælge sproget for bloggen WordPress, Vælg det ønskede sprog, og klik derefter på **Fortsæt**.

    ![Konfigurere sprog for din WordPress online][WordPressLanguage]

1. Angiv de konfigurationsoplysninger, der kræves, før WordPress WordPress **Velkommen til** siden, og klik derefter på **Installere WordPress**.

    ![Konfigurere indstillingerne for din WordPress online][WordPressConfigure]

1. Log på med legitimationsoplysningerne, som du oprettede på siden **Velkommen** .  

1. Dit Dashboard webstedsside åbnes og viser de oplysninger, som du angav.    

    ![Få vist dit WordPress dashboard][WordPressDashboard2]

## <a name="next-steps"></a>Næste trin

I dette selvstudium har du set hvordan du kan oprette og installere en eksempel-WebApp fra Azure Marketplace.

Du kan finde flere oplysninger om at arbejde med App-tjenesten Web Apps, kan du se linkene i venstre side af siden (for bred browservinduer) eller øverst på siden (for smal browservinduer).

Du kan finde flere oplysninger om udvikling af WordPress webapps på Azure, se [Udvikling af WordPress på Azure App Service][WordPressOnAzure]. 

<!-- URL List -->

[PHP]: https://azure.microsoft.com/develop/php/
[.NET]: https://azure.microsoft.com/develop/net/
[Node.js]: https://azure.microsoft.com/develop/nodejs/
[Java]: https://azure.microsoft.com/develop/java/
[Python]: https://azure.microsoft.com/develop/python/
[activate]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[free trial]: https://azure.microsoft.com/pricing/free-trial/
[Prøv App Service]: http://go.microsoft.com/fwlink/?LinkId=523751
[ResourceGroups]: ../resource-group-overview.md
[AzureAppServicePlans]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[ApplicationInsights]: https://azure.microsoft.com/services/application-insights/
[Azure-portalen]: https://portal.azure.com/
[Project Nami]: http://projectnami.org/
[WordPressOnAzure]: ./develop-wordpress-on-app-service-web-apps.md

<!-- IMG List -->

[MarketplaceStart]: ./media/app-service-web-create-web-app-from-marketplace/marketplacestart.png
[MarketplaceSearch]: ./media/app-service-web-create-web-app-from-marketplace/marketplacesearch.png
[MarketplaceCreate]: ./media/app-service-web-create-web-app-from-marketplace/marketplacecreate.png
[ConfigStart]: ./media/app-service-web-create-web-app-from-marketplace/configstart.png
[ConfigAppName]: ./media/app-service-web-create-web-app-from-marketplace/configappname.png
[ConfigSubscription]: ./media/app-service-web-create-web-app-from-marketplace/configsubscription.png
[ConfigResourceGroup]: ./media/app-service-web-create-web-app-from-marketplace/configresourcegroup.png
[ConfigServicePlan]: ./media/app-service-web-create-web-app-from-marketplace/configserviceplan.png
[ConfigDatabase]: ./media/app-service-web-create-web-app-from-marketplace/configdatabase.png
[ConfigFinished]: ./media/app-service-web-create-web-app-from-marketplace/configfinished.png
[ConfigProgress]: ./media/app-service-web-create-web-app-from-marketplace/configprogress.png
[WordPressSelect]: ./media/app-service-web-create-web-app-from-marketplace/wpselect.png
[WordPressBrowse]: ./media/app-service-web-create-web-app-from-marketplace/wpbrowse.png
[WordPressLanguage]: ./media/app-service-web-create-web-app-from-marketplace/wplanguage.png
[WordPressDashboard1]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard1.png
[WordPressDashboard2]: ./media/app-service-web-create-web-app-from-marketplace/wpdashboard2.png
[WordPressConfigure]: ./media/app-service-web-create-web-app-from-marketplace/wpconfigure.png
