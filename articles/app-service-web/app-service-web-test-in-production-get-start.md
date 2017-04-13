<properties
    pageTitle="Introduktion til test i produktionsmiljø i Web Apps"
    description="Få mere at vide om Test i fremstilling (TiP) funktion i Azure App Service Web Apps."
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
    ms.date="01/13/2016"
    ms.author="cephalin"/>

# <a name="get-started-with-test-in-production-for-web-apps"></a>Introduktion til test i produktionsmiljø i Web Apps

Test fremstilling eller live-test din online ved hjælp af direkte kunde trafik, er en strategi for test, der stadig integrerer app udviklere i deres [fleksible udvikling](https://en.wikipedia.org/wiki/Agile_software_development) metode. Det gør det muligt at teste kvaliteten af dine apps med live bruger trafik i din produktionsmiljø, i modsætning til elektronisk data i et testmiljø. Ved at give adgang til din nye app til reelle brugere, kan du blive informeret om de reelle problemer med din app kan vises, når den er installeret. Du kan kontrollere den funktionalitet, ydeevne og værdien af din app opdateringer mod lydstyrken, overførselshastighed og række reelle bruger trafik, som du kan aldrig omtrentlig i et testmiljø.

## <a name="traffic-routing-in-app-service-web-apps"></a>Trafik-Routing i App Service Webapps

Med funktionen trafik Routing i [Azure App-tjeneste](http://go.microsoft.com/fwlink/?LinkId=529714), kan du dirigere en del af live bruger trafik til en eller flere [installation pladser](web-sites-staged-publishing.md)og derefter analysere din app med [Azure programmet indsigt](/services/application-insights/) eller [Azure HDInsight](/services/hdinsight/)eller en tredjepart værktøj som [Ny Relic](/marketplace/partners/newrelic/newrelic/) til at validere din ændring. Du kan for eksempel implementere følgende scenarier med App-tjenesten:

- Opdag funktionelle fejl eller få vist ydeevnen flaskehalse i dine opdateringer før hele webstedet installation
- Udføre "kontrolleret test fly, flyver" af dine ændringer ved at måle usibility målepunkter på beta-app
- Gradvist sætte produktiviteten i vejret op til en ny opdatering og problemfrit igen til den aktuelle version Hvis der opstår fejl 
- Optimere din app business resultater ved at køre [A / B tester](https://en.wikipedia.org/wiki/A/B_testing) eller [multivariate test](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) i flere installation stik

### <a name="requirements-for-using-traffic-routing-in-web-apps"></a>Krav til brug af trafik Routing i Web Apps

- Din online skal køre i **Standard** eller **Premium** lag, som det er nødvendigt for flere installation pladser.
- For at kunne fungere korrekt, kræver trafik Routing cookies skal være aktivt i brugernes webbrowser. Trafik Routing bruger cookies til at fastgøre en klient-browser til en installation plads til levetid klientsession.
- Trafik Routing understøtter avancerede TiP-scenarier via Azure PowerShell-cmdlet'er.

## <a name="route-traffic-segment-to-a-deployment-slot"></a>Omdirigere trafik segmentet til en installation slot

På det grundlæggende niveau i hver TiP scenarie, kan du distribuere en foruddefineret procentdel af din live trafik til et ikke-produktionsmiljø slot. Følg nedenstående trin for at gøre dette:

>[AZURE.NOTE] Disse trin her antages det, at du allerede har en [ikke - produktionsmiljø slot](web-sites-staged-publishing.md) og, ønskede web app-indhold er allerede [installeret](web-sites-deploy.md) på den.

1. Log på [Azure-portalen](https://portal.azure.com/).
2. Klik på **Indstillinger**i din online blade > **Trafik Routing**.
  ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Vælg den slot, du vil omdirigere trafik til og procentdelen af den samlede trafik, du ønsker, og klik derefter på **Gem**.

    ![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Gå til den installation slot blade. Du bør nu se direkte trafik dirigeres til den.

    ![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Når trafik distributionen er konfigureret, vil den angivne procentdel af klienter sendes tilfældigt til din ikke-produktiv slot. Men det er vigtigt at være opmærksom på, når en klient sendes automatisk til en bestemt slot, det vil være "fastgjort" til stikket til aktivets levetid klient sessionen. Dette er færdig med en cookie til at fastgøre brugersessionen. Hvis du undersøge HTTP-anmodninger, kan du finde en `TipMix` cookie i alle efterfølgende anmodninger.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## <a name="force-client-requests-to-a-specific-slot"></a>Tving klient-anmodninger til et bestemt slot

Ud over automatisk trafik routing er App Service kan distribuere anmodninger om til en bestemt slot. Dette er nyttigt, når du vil brugerne skal kunne vælge til eller framelding af din beta-app. For at gøre dette skal du bruge den `x-ms-routing-name` forespørgsel parameter.

Omdirigere brugere til en bestemt slot ved hjælp af `x-ms-routing-name`, skal du sikre dig, at slotten allerede er føjet til listen trafik Routing. Da du vil omdirigere til en slot eksplicit, skal er du angive faktiske routing procent ligegyldig. Hvis du vil, kan du udforme en "beta" brugerne kan klikke på linket for at få adgang til appen beta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### <a name="opt-users-out-of-beta-app"></a>Vælge brugere af beta-app'en

Hvis du vil lade brugere fravælge din beta-app, f.eks, kan du anbringe dette link i din webside:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

Strengen `x-ms-routing-name=self` angiver, hvilken fremstilling slot. Når klient-browseren adgang til linket, ikke kun omdirigeres den til fremstilling plads, men alle efterfølgende anmodninger skal indeholde den `x-ms-routing-name=self` cookie, fastlåser sessionen til fremstilling plads.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### <a name="opt-users-in-to-beta-app"></a>Vælge brugerne i at beta app

Lade brugerne tilmelde sig din beta-app, skal du angive de samme forespørgselsparameter til navnet på den ikke-produktiv slot, for eksempel:

        <webappname>.azurewebsites.net/?x-ms-routing-name=staging

## <a name="more-resources"></a>Flere ressourcer ##

-   [Konfigurere arrangere miljøer til webapps i Azure App Service](web-sites-staged-publishing.md)
-   [Installere en kompleks program forudsigeligt i Azure](app-service-deploy-complex-application-predictably.md)
-   [Fleksible softwareudvikling med Azure App Service](app-service-agile-software-development.md)
-   [Bruge DevOps miljøer effektivt til din online](app-service-web-staged-publishing-realworld-scenarios.md)