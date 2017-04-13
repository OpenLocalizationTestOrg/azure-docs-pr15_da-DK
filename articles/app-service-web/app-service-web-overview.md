<properties
    pageTitle="Web Apps oversigt | Microsoft Azure"
    description="Få mere at vide, hvordan Azure App Service hjælper dig med at udvikle og host webprogrammer"
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="cephalin"/>

# <a name="web-apps-overview"></a>Oversigt over Web Apps

*App Service Web Apps* er en fuldt administreret Beregn platform, der er optimeret til vært for websteder og web-programmer. Tilbud denne [platform som en tjeneste](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) af Microsoft Azure kan du fokusere på dine forretningslogik mens Azure tager sig af infrastruktur til at køre og tilpasse dine apps.

Den følgende 5-minutters video introducerer Azure App Service Web Apps.

[AZURE.VIDEO azure-app-service-web-apps-with-yochay-kiriaty]

>[AZURE.INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

## <a name="what-is-a-web-app-in-app-service"></a>Hvad er en WebApp i App-tjenesten?

I App-tjeneste er en *Online* de Beregn ressourcer, der Azure leverer som vært for et websted eller web-program.  

Beregn ressourcer kan være på delte eller dedikeret virtuelle-maskiner (VM'er), afhængigt af det priser niveau, du vælger. Din programkode kører i en administreret VM, der er adskilt fra andre kunder.

Din kode kan være i en hvilken som helst sprog eller struktur, der understøttes af [Azure App tjeneste](../app-service/app-service-value-prop-what-is.md), såsom ASP.NET, Node.js, Java, PHP eller Python. Du kan også køre scripts, der bruger [PowerShell og andre scripting sprog](web-sites-create-web-jobs.md#acceptablefiles) i en WebApp.

Eksempler på typisk programscenarier, som du kan bruge Web Apps til, at se [Web app scenarier](https://azure.microsoft.com/documentation/scenarios/web-app/) og afsnittet **scenarier og anbefalinger** i [Azure App Service, virtuelle maskiner, Service-strukturen og Cloud Services sammenligning](choose-web-site-cloud-service-vm.md#scenarios).

## <a name="why-use-web-apps"></a>Hvorfor bruge Web Apps?

Her er nogle vigtige funktioner i App Service, der gælder for Web Apps:

- **Flere sprog og rammer** - App Service understøtter førsteklasses til ASP.NET, Node.js, Java, PHP og Python. Du kan også køre [PowerShell og andre scripts eller eksekverbare filer](../app-service-web/web-sites-create-web-jobs.md) på App-tjenesten FOS.

- **Optimering af DevOps** - konfigurere [fortløbende integration og implementering](../app-service-web/app-service-continuous-deployment.md) med Visual Studio Team Services, GitHub eller BitBucket. Hæve opdateringer via [test og arrangere miljøer](../app-service-web/web-sites-staged-publishing.md). Udføre [A / B test](../app-service-web/app-service-web-test-in-production-get-start.md). Administrere dine apps i App-tjenesten ved hjælp af [Azure PowerShell](../powershell-install-configure.md) eller [på tværs af platforme kommandolinjen (CLI)](../xplat-cli-install.md).

- **Global skalere med høj tilgængelighed** - skala [op](../app-service-web/web-sites-scale.md) eller [ud](../monitoring-and-diagnostics/insights-how-to-scale.md) manuelt eller automatisk. Være vært for dine apps overalt i Microsoft-global datacenter infrastruktur og tjenesten App [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) vil medføre høj tilgængelighed.

- **Forbindelser til SaaS platforme og lokale data** - vælge mere end 50 [forbindelser](../connectors/apis-list.md) til enterprise-systemer (som SAP, Siebel og Oracle), SaaS services (såsom Salesforce og Office 365) og internet services (som Facebook og Twitter). Access lokale data ved hjælp af [Hybrid forbindelser](../biztalk-services/integration-hybrid-connection-overview.md) og [Azure virtuelle netværk](../app-service-web/web-sites-integrate-with-vnet.md).

- **Sikkerhed og overholdelse** - App tjenesten er [ISO, SOC, og PCI kompatibel](https://www.microsoft.com/TrustCenter/).

- **Programskabeloner** – Vælg fra en omfattende liste over programskabeloner på [Azure Marketplace](https://azure.microsoft.com/marketplace/) , som du kan bruge en guide til at installere populære open source-software som WordPress, Joomla og Drupal.

- **Integration af visual Studio** - dedikeret værktøjer i Visual Studio strømline arbejdet for oprettelse, implementering og fejlfinding.

Desuden kan en WebApp drage fordel af funktioner, som [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) (f.eks, der understøtter CORS) og [Mobile-Apps](../app-service-mobile/app-service-mobile-value-prop.md) (såsom pushmeddelelser). Se [Oversigt over Azure App Service](../app-service/app-service-value-prop-what-is.md)kan finde flere oplysninger om app typer i App-tjeneste.

Ud over Web Apps i App-tjeneste indeholder Azure andre tjenester, der kan bruges som vært for websteder og web-programmer. Til de fleste scenarier er Web Apps det bedste valg.  Overvej [Service-strukturen](https://azure.microsoft.com/documentation/services/service-fabric)for microservice arkitektur, og hvis du har brug for mere kontrol over VM'er, der kører din kode på, kan du overveje at [virtuelle Azure-computere](https://azure.microsoft.com/documentation/services/virtual-machines/). Kan finde flere oplysninger om, hvordan du kan vælge mellem disse Azure tjenester i [Azure App Service, virtuelle maskiner, Service-strukturen og sammenligning af Skytjenester](choose-web-site-cloud-service-vm.md).

## <a name="getting-started"></a>Kom godt i gang

For at komme i gang ved at installere eksempelkode til en ny WebApp i App-tjeneste skal du følge selvstudiet [Implementer din første online til Azure 5 minutter](app-service-web-get-started.md) . Du skal bruge en gratis Azure-konto.

Hvis du vil komme i gang med Azure App Service før tilmelding til en Azure-konto, skal du gå til [Prøve App Service](http://go.microsoft.com/fwlink/?LinkId=523751), hvor du straks kan oprette en forbigående starter WebApp i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.
