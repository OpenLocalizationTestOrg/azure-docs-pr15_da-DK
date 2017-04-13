<properties
    pageTitle="Azure App Service: Skalering App tjenesteprogrammer"
    description="Få mere at vide på Sådan fungerer skalering programmet i App-tjeneste."
    keywords="App, azure app tjeneste, skala, SVG, app-serviceaftale app serviceomkostninger"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-scaling-app-service-applications"></a>Azure App Service: Skalering App tjenesteprogrammer

Programmer, der er hostet i Azure App Service kan [opnå massive skala](https://azure.microsoft.com/blog/canadian-broadcasting-corporation-radio-canada-leverage-azure-for-smooth-election-coverage/).
Skalere et program er dog komplekse problemer, der ikke har en "én størrelse passer alle" løsning. Korrekt skala dit program der er 3 nøgleområder, der bidrager til din programmer succes:

1. Om dit programarkitektur og dens svagheder.
    * Er dit program med høj sikkerhed? Uden status?
    * Hvad er alle komponenter i dit program?
        * Hvor er flaskehalse i programmet på computeren?
    * Når Indlæs anvendes til din app, skifter det første?
2. Forstå de forventede krav til indlæsning og ydeevne.
    * Skal programmet at kunne levere et tusinde brugere? eller en million?
    * Kommer trafik fra en enkelt geografiske placering eller globalt?
    * Er der sæsonudsving? trafik spidser?
    * Hvor hurtigt skal appen svare? 1 sekund? 1 millisekunder?
3. Forstå og korrekt Udnyt den platform, der er vært for din app.
    * Hvilke funktioner skal jeg anvende for at opnå min skala mål?

Dette afsnit hjælper dig med at forstå alle faktorer og Hjælp du udtænke en strategi, der tager fordel af de nødvendige App Service-funktioner til at nå dine mål skalerbarhed.

[AZURE.INCLUDE [app-service-blueprint-scaling-app-service-applications](../../includes/app-service-blueprint-scaling-app-service-applications.md)]
