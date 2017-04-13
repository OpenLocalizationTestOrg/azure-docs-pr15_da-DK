<properties
    pageTitle="Azure stak Web Apps oversigt | Microsoft Azure"
    description="Oversigt over Webapps i Azure stak"
    services="azure-stack"
    documentationCenter=""
    authors="apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>
    
# <a name="azure-stack-web-apps-overview"></a>Oversigt over Azure stak Web Apps
    
> [AZURE.NOTE] Følgende oplysninger gælder kun for Azure stak TP1 installationer.

Azure stak Web Apps er det første element i den Azure App-servicetilbud, der er gjort Azure stak. Azure stak Web Apps installationsprogrammet opretter en forekomst af hver af de fem påkrævede rolle kategorier, og du kan også oprette en filserver. Selvom du kan tilføje flere forekomster for hver af typerne rolle, du huske, at der ikke er meget plads til FOS i Technical Preview-1. De aktuelle funktioner til Azure stak Web Apps er primært foundation-funktioner, der skal bruges til at administrere system og host web apps.

![Azure stak App Service Webapps i Azure stable Portal][1]

## <a name="limitations-of-the-technical-preview"></a>Begrænsninger af Technical Preview

Der er ingen understøttelse for Azure stak App Service preview-versioner. Sæt ikke fremstilling arbejdsbelastninger på denne preview-versionen. Der er også ingen opgradering mellem Azure stak App Service preview versioner. De primære formål med disse preview-versioner er at vise hvad vi tilbyder og for at få tilbagemeldinger. 

## <a name="what-is-an-app-service-plan"></a>Hvad er en Plan til App-tjenesten?

Azure stak Web Apps ressource udbyder bruger den samme kode, der bruger funktionen Azure Web Apps i tjenesten Azure-App. Som et resultat er nogle almindelige koncepter værd der beskriver. I Web Apps kaldes objektbeholderen priser for Onlines App-serviceaftale. Repræsenterer det sæt af dedikeret virtuelle maskiner, der bruges til at holde dine apps. Du kan have flere App tjenesteplanerne inden for et bestemt abonnement. Dette gælder også i Azure stak Web Apps. 

I Azure er der delt og dedikeret medarbejdere. En delt arbejder understøtter vært for kompakte multiprofiler web app, og der er kun ét sæt delte medarbejdere. Dedikerede servere er kun bruges af en lejer og findes i tre størrelser: lille, mellemstore og store. Behovene hos lokale kunder beskrives ikke altid ved hjælp af disse udtryk. Ressource udbyder administratorer kan definere de arbejder lag, de vil gøre tilgængelige, så de har flere sæt af delte medarbejdere eller forskellige typer dedikeret medarbejdere, der er baseret på deres entydige web vært behov i Azure stak Web Apps. Ved hjælp af disse arbejder niveau definitioner, kan de derefter definere deres egne priser lagerenheder.

## <a name="portal-features"></a>Portalen funktioner

Som det er også tilfældet med back-end, bruger Azure stak Web Apps i samme brugergrænseflade, der bruger Azure Web Apps. Nogle funktioner er deaktiveret og ikke endnu funktionelle Azure stablede. Dette skyldes, at Azure-specifikke forventninger eller tjenester, der kræver disse funktioner er endnu ikke tilgængelige i Azure stak. 

## <a name="next-steps"></a>Næste trin

- [Før du går i gang med Azure stak Web Apps](azure-stack-webapps-before-you-get-started.md)
- [Installere provideren Web Apps ressource](azure-stack-webapps-deploy.md)

Du kan også prøve andre [platform som en tjeneste (PaaS)-tjeneste](azure-stack-tools-paas-services.md), såsom [SQL Server ressource udbyder](azure-stack-sql-rp-deploy-short.md) og [MySQL ressource udbyder](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-overview/AppService_Portal.png
