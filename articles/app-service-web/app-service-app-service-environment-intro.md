<properties 
    pageTitle="Introduktion til App Service-miljø" 
    description="Få mere at vide om funktionen App Service-miljø, som giver sikker, VNet, der er joinforbundne, dedikeret skalaenheder til kørsel af alle dine apps." 
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

# <a name="introduction-to-app-service-environment"></a>Introduktion til App Service-miljø

## <a name="overview"></a>Oversigt ##
Et App-tjenesten miljø er en [Premium] [ PremiumTier] service plan mulighed for Azure App tjeneste, som indeholder et fuldt isolerede og dedikeret miljø til sikker når Azure App Service apps skaleres til høj, herunder [Onlines][WebApps], [Mobile-Apps][MobileApps], og [API Apps][APIApps].  

App Service miljøer er velegnet til arbejdsbelastninger, der kræver:

- Meget høj skala
- Isolationsniveauet og sikker netværksadgang

Kunder kan oprette flere App Service miljøer inden for et enkelt Azure område og på tværs af flere Azure områder.  Dette gør App Service miljøer ideel til vandret skalering tilstand mindre programmet niveauer overholde høj RPS arbejdsmængder.

App Service miljøer er isolerede køres kun en enkelt kunde programmer, og er altid installeres i et virtuelt netværk.  Kunder har detaljerede kontrol over begge indgående og udgående programmet netværkstrafik, og programmer kan etablere sikre højhastighedsforbindelser via virtuelt Network til lokale virksomhedens ressourcer.

Alle artikler og hvordan-handler om at App Service miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

Have et overblik over, hvordan App Service miljøer aktivere høj skala og sikker adgang til netværket, skal du se [AzureCon undersøgelse] [ AzureConDeepDive] på App-tjenesten miljøer!

En gå i dybden på vandret skalering ved hjælp af flere App Service miljøer artiklen om, hvordan du konfigurerer et [geografisk distribueret app miljøet][GeodistributedAppFootprint].

For at se, hvordan vises i den AzureCon undersøgelse sikkerhedsarkitekturen er konfigureret, skal du se artiklen om implementering af en [lagt sikkerhedsarkitekturen](app-service-app-service-environment-layered-security.md) i App-tjenesten miljøer.

Apps, der kører på App-tjenesten miljøer kan have deres access gated ved før enheder som web application firewalls (WAF).  I artiklen om [konfiguration af en WAF til App Service miljøer](app-service-app-service-environment-web-application-firewall.md) dækker dette scenario. 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="dedicated-compute-resources"></a>Dedikeret Beregn ressourcer ##
Alle Beregn ressourcerne i en App-tjenesten miljø er dedikeret eksklusivt til et enkelt abonnement, og et App Service-miljø kan konfigureres med op til 50 (50) Beregn ressourcer med udelt adgang af et enkelt program.

Et App-tjenesten miljø består af en front end Beregn ressourcepulje samt en til tre arbejder Beregn ressourcepuljer. 

Front end-puljen indeholder Beregn ressourcer, der er ansvarlig for opsigelse før SSL som også automatisk belastning på app-anmodninger i en App Service-miljø. 

Hver arbejder puljen indeholder Beregn ressourcer, der er allokeret til [App Service plan][AppServicePlan], som også indeholder en eller flere Azure App Service-apps.  Da der kan være op til tre forskellige arbejder grupper i en App Service-miljø, har du mulighed for at vælge forskellige Beregn ressourcer for hver arbejder puljen.  

For eksempel kan dette du oprette én arbejder puljen med mindre effektiv Beregn ressourcer til App-tjenesten planer beregnet til udvikling eller test apps.  En anden (eller endda tredje) arbejder pulje kunne bruge mere effektiv Beregn ressourcer, der er beregnet til App-tjenesten planer kører fremstilling apps.

Se, [hvordan du konfigurere en App Service-miljø]kan finde flere oplysninger om antallet af Beregn ressourcer tilgængelige for front end- og arbejder grupper[HowToConfigureanAppServiceEnvironment].  

Få at vide om de tilgængelige beregne ressource størrelser understøttes i et App-tjenesten miljø, kan du se den [App servicepriser] [ AppServicePricing] side og gennemse de tilgængelige indstillinger for App-tjenesten miljøer i Premium priser niveau.

## <a name="virtual-network-support"></a>Understøttelse af virtuelle netværk ##
Kan oprettes et App-tjenesten miljø i **enten** en Azure ressourcestyring virtuelt netværk, **eller** en klassisk installation modellere virtuelt netværk ([flere oplysninger om virtuelle netværk][MoreInfoOnVirtualNetworks]).  Da et App-tjenesten miljø findes altid i et virtuelt netværk og mere præcist inden for et undernet af et virtuelt netværk, kan du udnytte sikkerhedsfunktioner i virtuelle netværk til at styre begge indgående og udgående netværkskommunikation.  

Et App Service-miljø kan være enten via med en offentlig IP-adresse eller interne modstående med kun en Azure interne Indlæs belastningsjusteringstjenesten (ILB) adresse internettet.

Du kan bruge [netværk sikkerhedsgrupper] [ NetworkSecurityGroups] til at begrænse indgående netværkskommunikation til det undernet, hvor et App-tjenesten miljø er placeret.  Dette giver dig mulighed at køre apps bag mod strømmen enheder og tjenester som web application firewalls og netværk SaaS udbydere.

Apps også ofte har brug at få adgang til virksomhedens ressourcer som interne databaser og webtjenester.  Et almindelige tilgang er til rådighed disse slutpunkter kun for interne netværkstrafik, der flyder i en Azure virtuelt netværk.  Når et App-tjenesten miljø er tilsluttet det samme virtuelle netværk som interne tjenesterne, apps, der kører i miljøet kan få adgang til dem, herunder slutpunkter kan nås via [Til websted] [ SiteToSite] og [Azure ExpressRoute] [ ExpressRoute] forbindelser.

For flere oplysninger om, hvordan App Service miljøer sammen med virtuelle netværk og lokale netværk se følgende artikler på [Netværksarkitektur][NetworkArchitectureOverview], [Styre indgående trafik][ControllingInboundTraffic], og [Sikkert at oprette forbindelse til en back-end][SecurelyConnectingToBackends]. 

## <a name="getting-started"></a>Kom godt i gang

For at komme i gang med App-tjenesten miljøer skal du se [Hvordan til at oprette en App Service-miljø][HowToCreateAnAppServiceEnvironment]

Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

Få vist [Netværk arkitektur oversigt] for en oversigt over App servicemiljø netværksarkitektur[ NetworkArchitectureOverview] artikel.

Yderligere oplysninger om brug af et App-tjenesten miljø med ExpressRoute, finder du i følgende artikel på [Express distribuere og App Service miljøer][NetworkConfigDetailsForExpressRoute].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[AzureConDeepDive]:  https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/
[GeodistributedAppFootprint]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[ControllingInboundTraffic]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SecurelyConnectingToBackends]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NetworkArchitectureOverview]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkConfigDetailsForExpressRoute]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 

<!-- IMAGES -->

 
