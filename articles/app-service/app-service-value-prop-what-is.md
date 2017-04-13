<properties
    pageTitle="Azure-App-tjeneste til web, mobile og API apps | Microsoft Azure"
    description="Få mere at vide, hvordan Azure App Service hjælper dig med at udvikle, installere og administrere internettet og mobilapps."
    keywords="App-tjenesten, azure-app-tjenesten, app service omkostninger, skala, SVG, appinstallation, azure appinstallation, paas, platform som en tjeneste, websted, websted, internettet, azure mobile"
    services="app-service"
    documentationCenter=""
    authors="omarkmsft"
    manager="erikre"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/26/2016"
    ms.author="omark"/>

# <a name="what-is-azure-app-service"></a>Hvad er Azure App Service?

*App* er en [platform som en tjeneste](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) med tilbud om af Microsoft Azure. Oprette web og mobilapps til en hvilken som helst platform eller enhed. Integrere dine apps med SaaS løsninger, oprette forbindelse til lokale programmer og automatisere dine forretningsprocesser. Azure kører dine apps på fuldt administreret virtuelle maskiner (VM'er), hvor du kan vælge delte VM ressourcer eller dedikeret FOS.

App Service omfatter internettet og mobile-funktioner, som vi tidligere leveret separat som Azure websteder og Azure Mobile-tjenester. Den indeholder også nye funktioner til automatisere forretningsprocesser og vært skyen API'er. Som en enkelt integreret tjeneste kan App Service du oprette forskellige komponenter – websteder, -mobilappen tilbage ender, RESTful API'er og forretningsprocesser – i en enkelt løsning.

Følgende 4-minutters video giver en kort beskrivelse af hvordan App Service relaterer til tidligere Azure tilbud og nyheder i den.

+[AZURE.VIDEO app-service-history-lesson]

## <a name="why-use-app-service"></a>Hvorfor bruge App Service?

Her er nogle af de vigtigste funktioner og egenskaber App-tjenesten:

- **Flere sprog og rammer** - App Service understøtter førsteklasses til ASP.NET, Node.js, Java, PHP og Python. Du kan også køre [Windows PowerShell og andre scripts eller eksekverbare filer](../app-service-web/web-sites-create-web-jobs.md) på App-tjenesten FOS.

- **Optimering af DevOps** - konfigurere [fortløbende integration og implementering](../app-service-web/app-service-continuous-deployment.md) med Visual Studio Team Services, GitHub eller BitBucket. Hæve opdateringer via [test og arrangere miljøer](../app-service-web/web-sites-staged-publishing.md). Udføre [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Administrere dine apps i App-tjenesten ved hjælp af [Azure PowerShell](../powershell-install-configure.md) eller [på tværs af platforme kommandolinjen (CLI)](../xplat-cli-install.md).

- **Global skalere med høj tilgængelighed** - skala [op](../app-service-web/web-sites-scale.md) eller [ud](../monitoring-and-diagnostics/insights-how-to-scale.md) manuelt eller automatisk. Være vært for dine apps overalt i Microsoft-global datacenter infrastruktur og tjenesten App [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) vil medføre høj tilgængelighed.

- **Forbindelser til SaaS platforme og lokale data** - vælge mere end 50 [forbindelser](../connectors/apis-list.md) til enterprise-systemer (som SAP, Siebel og Oracle), SaaS services (såsom Salesforce og Office 365) og internet services (som Facebook og Twitter). Access lokale data ved hjælp af [Hybrid forbindelser](../biztalk-services/integration-hybrid-connection-overview.md) og [Azure virtuelle netværk](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sikkerhed og overholdelse** - App tjenesten er [ISO, SOC, og PCI kompatibel](https://www.microsoft.com/TrustCenter/).

- **Programskabeloner** – Vælg fra en omfattende liste over programskabeloner på [Azure Marketplace](https://azure.microsoft.com/marketplace/) , som du kan bruge en guide til at installere populære open source-software som WordPress, Joomla og Drupal.

- **Integration af visual Studio** - dedikeret værktøjer i Visual Studio strømline arbejdet for oprettelse, implementering og fejlfinding.

## <a name="app-types-in-app-service"></a>App typer i App-tjeneste

App Service indeholder flere *typer app*, hver især er beregnet til at være vært for en bestemt type arbejdsbelastningen:

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - som vært for websteder og web-programmer.

- [**Mobilapps**](../app-service-mobile/app-service-mobile-value-prop.md) Tilbage til at være vært mobilapp ender.

- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) - til vært RESTful API'er.

- [**Logik Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - til automatisere forretningsprocesser og integration af systemer og data på tværs af skyer uden at skrive kode.

Word *app* her refererer til de hosting ressourcer, der er angivet til at køre en arbejdsbyrde. Hvis vi tager "Online" som et eksempel, du er sandsynligvis vant til at tænker på en web-app som både Beregn ressourcer og programkode, der sammen levere funktionalitet til en browser. Men i App Service en *Online* er de Beregn ressourcer, der Azure leverer som vært for din programkode. Hvis dit program består af en web front end- og en RESTful API tilbage slutningen, du kan installere begge dele til en WebApp eller du kan installere din front end-kode til en WebApp og back end-kode til en API-app. Programmet kan bestå af flere App Service-apps på forskellige typer.

## <a name="app-service-plans"></a>App Service-planer

[App Service plan](azure-web-sites-web-hosting-plans-in-depth-overview.md) angive typen Beregn ressourcer, som dine apps køre på. Hvis du forventer light trafik indlæse, kan du bruge delte FOS (**gratis** og **delt** priser niveauer). Til højere indlæse, kan du vælge mellem forskellige størrelser dedikeret FOS (**grundlæggende**, **Standard**og **Premium** niveauer). Flere App Service-apps deler de samme plan, og de skalere op og ned priser niveauer sammen i planen.

Hvis du har brug for mere skalerbarhed og netværkets isolation, kan du køre dine apps i en [App Service-miljø](../app-service-web/app-service-app-service-environment-intro.md).

## <a name="pricing"></a>Priser

Finde oplysninger om omkostninger hvor meget App Service, [App servicepriser](https://azure.microsoft.com/pricing/details/app-service/).

## <a name="test-drive-app-service"></a>Prøvekør App Service

[Opret en stikprøve WebApp, mobilappen eller logik app](http://go.microsoft.com/fwlink/?LinkId=523751) og afspilning med den til 1 time, med ingen kreditkort er påkrævet, ingen forpligtelser uden besvær med.

Eller Åbn en [gratis Azure-konto](https://azure.microsoft.com/pricing/free-trial/), og prøv en af vores-introduktion selvstudier:

* [Selvstudium: Oprette en web-app](../app-service-web/app-service-web-get-started.md)
* [Selvstudium: Oprette en mobilapp](../app-service-mobile/app-service-mobile-android-get-started.md)
* [Selvstudium: Oprette en API-app](../app-service-api/app-service-api-dotnet-get-started.md)
* [Selvstudium: Oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md)
