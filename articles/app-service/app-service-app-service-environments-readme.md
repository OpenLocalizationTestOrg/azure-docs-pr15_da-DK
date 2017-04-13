<properties 
    pageTitle="App Service miljø | Microsoft Azure" 
    description="Hvad er et Azure App Service-miljø? En introduktion til App Service-miljø." 
    keywords="Azure-app service-miljø, virtuelt netværk og sikrer netværk"
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>

# <a name="app-service-environment-documentation"></a>App Service miljø dokumentation

Et App-tjenesten miljø er en [Premium] [ PremiumTier] service plan mulighed for Azure App tjeneste, som indeholder et fuldt isolerede og dedikeret miljø til sikker når Azure App Service apps skaleres til høj, herunder [Onlines][WebApps], [Mobile-Apps][MobileApps], og [API Apps][APIApps].  

App Service miljøer er velegnet til arbejdsbelastninger, der kræver:

- Meget høj skala
- Isolationsniveauet og sikker netværksadgang

Kunder kan oprette flere App Service miljøer inden for et enkelt Azure område og på tværs af flere Azure områder.  Dette gør App Service miljøer ideel til vandret skalering tilstand mindre programmet niveauer overholde høj RPS arbejdsmængder.

App Service miljøer er isolerede køres kun en enkelt kunde programmer, og er altid installeres i et virtuelt netværk.  Kunder har detaljerede kontrol over begge indgående og udgående programmet netværkstrafik ved hjælp af [netværk sikkerhedsgrupper][NetworkSecurityGroups].  Programmer kan også oprette sikre højhastighedsforbindelser via virtuelt Network til lokale virksomhedens ressourcer.

Apps ofte har brug at få adgang til virksomhedens ressourcer som interne databaser og webtjenester.  Apps, der kører på App-tjenesten miljøer kan få adgang til ressourcer, der kan nås via [Til websted] [ SiteToSite] VPN og [Azure ExpressRoute] [ ExpressRoute] forbindelser.

* [Hvad er et App-tjenesten miljø?](../app-service-web/app-service-app-service-environment-intro.md)
* [Oprette en App Service-miljø](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [Oprette Apps i en App Service-miljø](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [Oprette og bruge en intern justering af belastning med App Service miljøer](../app-service-web/app-service-environment-with-internal-load-balancer.md)
* [Konfigurere en App Service-miljø](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [Skalering Apps i en App Service-miljø](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [Netværkssikkerhed og arkitektur](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## <a name="how-tos"></a>Sådans

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## <a name="videos"></a>Videoer
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
