<properties 
    pageTitle="Konfiguration af en Firewall til webprogrammer (WAF) til App Service-miljø" 
    description="Lær, hvordan du konfigurerer en firewall til webprogrammer foran dit App Service-miljø." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Konfiguration af en Firewall til webprogrammer (WAF) til App Service-miljø

## <a name="overview"></a>Oversigt ##
Web application firewalls som [Barracuda WAF til Azure](https://www.barracuda.com/programs/azure) , der er tilgængelige på [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) hjælper med at sikre dine webprogrammer ved at undersøge indgående webtrafik for at blokere SQL-injektion, Scripting på tværs af websteder, malware uploads og programmet DDoS og andre angreb. Den undersøger også svar fra back end-webserverne for Data tab forebyggelse (DLP). Kombineres med isolationsniveauet og yderligere skalering fra App Service-miljøer, er dette et ideelt miljø til host business kritiske webprogrammer, der skal klare ondsindede anmodninger og store mængder trafik.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Konfiguration ##
For dette dokument, vi vil konfigurere afstemmes vores App servicemiljø bag flere Indlæs forekomster af Barracuda WAF, så kun trafik fra WAF kan få fat i App-tjenesten miljø og den vil ikke være tilgængelige i DMZ. Vi har også Azure trafik Manager foran vores Barracuda WAF forekomster til justering af belastning på tværs af Azure datacentre og områder. Et højt niveau diagram over konfigurationen vil ligne Hvad er vist nedenfor.

![Arkitektur][Architecture] 

> Bemærk: Med introduktionen af [ILB support til App Service-miljø](app-service-environment-with-internal-load-balancer.md), kan du konfigurere ASE for at være utilgængelige fra DMZ og kun være tilgængelige for det private netværk. 

## <a name="configuring-your-app-service-environment"></a>Konfigurere din App Service-miljø ##
For at konfigurere refererer et App-tjenesten miljø til [vores dokumentation](app-service-web-how-to-create-an-app-service-environment.md) om emnet. Når du har et App-tjenesten miljø, der er oprettet, kan du oprette [Web Apps](app-service-web-overview.md), [API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) og [Mobile-Apps](../app-service-mobile/app-service-mobile-value-prop.md) i dette miljø, vil alle være beskyttede bag WAF vi konfigurerer i næste afsnit.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Konfigurere din Barracuda WAF skytjeneste ##
Barracuda har en [detaljeret artikel](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) om installation af dens WAF på en virtuel maskine i Azure. Men da vi vil redundans og ikke introducere for fejl fra ét sted, du vil installere mindst 2 WAF forekomst FOS i samme Skytjenesten, når du følger disse instruktioner.

### <a name="adding-endpoints-to-cloud-service"></a>Tilføje slutpunkter til tjeneste i skyen ###
Når du har 2 eller flere forekomster af WAF VM i din skytjeneste kan du bruge [Azure portal](https://portal.azure.com/) tilføje HTTP og HTTPS slutpunkter, der bruges af dit program, som vist i billedet nedenfor.

![Konfigurer slutpunkt][ConfigureEndpoint]

Hvis dine programmer bruger andre slutpunkter, skal du sørge for at føje dem til denne liste samt. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Konfiguration af Barracuda WAF via dens Management-Portal ###
Barracuda WAF bruger TCP Port 8000 for konfiguration via dens management portal. Da vi har flere forekomster af WAF FOS skal du gentage trinnene her for hver VM forekomst. 


> Bemærk: Når du er færdig med WAF konfiguration, skal du fjerne TCP/8000 slutpunktet fra alle dine WAF FOS til at beskytte din WAF.

Tilføj management slutpunkt, som vist i billedet nedenfor til at konfigurere din Barracuda WAF.

![Tilføje Management slutpunkt][AddManagementEndpoint]
 
Bruge en browser til at gå til administration af slutpunktet på skybaseret tjeneste. Hvis din skytjeneste kaldes test.cloudapp.net, vil du få adgang til dette slutpunkt ved at gå til http://test.cloudapp.net:8000. Skal du se afsnittet en logonside som nedenfor, kan du logger på ved hjælp af legitimationsoplysninger, du har angivet i fasen WAF VM konfiguration.

![Administration af logonside][ManagementLoginPage]

Når du login du bør se et dashboard som et i billedet nedenfor, der skal vises grundlæggende statistik om WAF beskyttelse.

![Administrationsdashboard][ManagementDashboard]

At klikke på fanen tjenester, kan du konfigurere din WAF for det beskytter-tjenester. Få mere at vide om konfiguration af din Barracuda WAF kan du se [deres dokumentationen](https://techlib.barracuda.com/waf/getstarted1). I eksemplet herunder en Azure-WebApp som fungerer trafik på http- og HTTPS er blevet konfigureret.

![Administration af tilføje tjenester][ManagementAddServices]

> Bemærk: Afhængigt af hvordan dine programmer er konfigureret, og hvilke funktioner der bruges i dit miljø, App-tjenesten, skal du videresende trafik til TCP porte end 80 og 443, f.eks. Hvis du har IP SSL konfiguration til en WebApp. Se [indgående trafik dokumentation](app-service-app-service-environment-control-inbound-traffic.md) netværk porte sektion til en liste over netværksporte, der bruges i App-tjenesten miljøer.

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Konfiguration af Microsoft Azure trafik Manager (VALGFRIT) ##
Hvis dit program er tilgængelig i flere områder og derefter en god ide at indlæse saldo dem bag [Azure trafik Manager](../traffic-manager/traffic-manager-overview.md). Du kan tilføje et slutpunkt i [Azure klassisk portal](https://manage.azure.com) ved hjælp af skytjeneste navn til din WAF i profilen trafik Manager, som vist i billedet nedenfor til at gøre dette. 

![Trafik Manager slutpunkt][TrafficManagerEndpoint]

Hvis dit program kræver godkendelse, sikre, at du har nogle ressourcer, der ikke kræver en hvilken som helst godkendelse til trafik Manager ping til tilgængeligheden af dit program. Du kan konfigurere URL-adressen under afsnittet konfiguration på [Azure klassisk portal](https://manage.azure.com) , som vist nedenfor.

![Konfigurere trafik Manager][ConfigureTrafficManager]

Hvis du vil videresende trafik Manager pinger fra din WAF til dit program, skal du konfiguration websted oversættelser på din Barracuda WAF for at videresende trafik til dit program, som vist i eksemplet nedenfor.

![Websted oversættelser][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Sikring af trafik til App Service miljø ved hjælp af netværk sikkerhedsgrupper (NSG)##
Følg i [kontrolelementet indgående trafik dokumentation](app-service-app-service-environment-control-inbound-traffic.md) for oplysninger på begrænse trafik til dit miljø til App-tjenesten fra WAF kun ved hjælp af VIP-adressen skybaseret tjeneste. Her er et eksempel Powershell-kommando for at udføre denne opgave for TCP-port 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Erstat SourceAddressPrefix med den virtuelle IP-adresse (VIP) af din WAF skybaseret tjeneste.

> Bemærk: VIP af din skytjeneste ændres, når du sletter og genskabe Skytjenesten. Sørg for at opdatere IP-adressen i gruppen netværksressource, når du har gjort. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
