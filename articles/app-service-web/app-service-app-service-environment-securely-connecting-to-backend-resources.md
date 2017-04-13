<properties 
    pageTitle="Sikkert at oprette forbindelse til back end-ressourcer fra en App Service-miljø" 
    description="Få mere at vide om, hvordan du sikkert oprette forbindelse til back end-ressourcer fra et App Service-miljø." 
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

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>Sikkert at oprette forbindelse til back end-ressourcer fra en App Service-miljø #

## <a name="overview"></a>Oversigt ##
Da oprettes et App-tjenesten miljø altid i **enten** en Azure ressourcestyring virtuelt netværk, **eller** en klassisk installation modellere [virtuelt netværk][virtualnetwork], udgående forbindelser fra et App-tjenesten miljø til andre back end-ressourcer kan flyde udelukkende over det virtuelle netværk.  Med en seneste ændring, der har foretaget i juni 2016, kan også være installeret ASEs til virtuelle netværk, der bruger enten offentlige adresseområder eller RFC1918 adresse mellemrum (dvs. private adresser).  

Der kan for eksempel være en SQL Server, der kører på en klynge af virtuelle maskiner med port 1433 låst.  Slutpunktet kan være ACLd til kun at tillade adgang fra andre ressourcer på det samme virtuelle netværk.  

Som et andet eksempel følsomme slutpunkter kan køre i det lokale miljø og være forbundet med Azure via et [Websted til websted] [ SiteToSite] eller [Azure ExpressRoute] [ ExpressRoute] forbindelser.  Derfor kan kunne kun ressourcer i virtuelle netværk, der er tilsluttet til websted eller ExpressRoute tunneler få adgang til lokale slutpunkter.

For alle disse scenarier kunne apps, der kører på et App-tjenesten miljø oprette en sikker forbindelse til forskellige servere og ressourcer.  Udgående trafik fra apps, der kører i et App-tjenesten miljø til private slutpunkter i det samme virtuelle netværk (eller forbundet med det samme virtuelle netværk), der kun flow over det virtuelle netværk.  Udgående trafik til private slutpunkter teksten går ikke via offentlige internettet.

Én ting gælder for udgående trafik fra et App-tjenesten miljø til slutpunkter i et virtuelt netværk.  App Service miljøer ikke kan nå slutpunkter af virtuelle maskiner, der er placeret i det **samme** undernet som App Service-miljø.  Dette må normalt ikke være et problem så længe App Service miljøer installeres i et undernet reserveret kun App Service miljøet med udelt adgang.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Udgående forbindelse og DNS-krav ##
For en App Service-miljø kan fungere korrekt, kræver udgående adgang til forskellige slutpunkter. En komplet liste over de eksterne slutpunkter, der bruges af en ASE er i afsnittet "Påkrævet netværksforbindelsen" i artiklen [Netværkskonfiguration for ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service miljøer kræver en gyldig DNS-infrastrukturen, der er konfigureret til det virtuelle netværk.  Hvis DNS-konfigurationen ændres til en eller anden grund efter et App-tjenesten miljø er blevet oprettet, kan udviklere tvinge en App-tjenesten miljø for at fortsætte efter den nye DNS-konfiguration.  Udløse en rullende miljø genstart ved hjælp af ikonet "Genstart" findes i øverst del af bladet App servicemiljø management i portalen medfører miljø at fortsætte efter den nye DNS-konfiguration.

Det anbefales også, at eventuelle brugerdefinerede DNS-servere på vnet indstilles forvejen før du opretter en App Service-miljø.  Hvis et virtuelt netværk DNS-konfiguration ændres, mens der oprettes et App-tjenesten miljø, der medfører App servicemiljø oprettelse af processen fejlbehæftede.  I en lignende vein, hvis en brugerdefineret DNS-server findes på anden ende af en VPN-gateway, og DNS-serveren er ikke tilgængelig eller ikke tilgængelig, mislykkes oprettelsen af App servicemiljø også.

## <a name="connecting-to-a-sql-server"></a>Oprette forbindelse til en SQL Server
En almindelig SQL Server-konfiguration har lytte på port 1433 ark:

![SQL Server slutpunkt][SqlServerEndpoint]

Der er to metoder til at begrænse trafik til dette slutpunkt:


- [Netværk adgangskontrollister] [ NetworkAccessControlLists] (hjemmenetværk ACLs)

- [Netværk sikkerhedsgrupper][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Begrænse adgang med et netværk ACL

Port 1433 kan være fastgjort ved hjælp af et netværk adgangslisten kontrolelement.  Eksemplet herunder whitelists klient løser kommer fra inde i et virtuelt netværk og blokerer adgang til alle andre klienter.

![Eksempel på netværk Access kontrolelementet liste][NetworkAccessControlListExample]

Alle programmer, der kører i App-tjenesten miljø i det samme virtuelle netværk som SQL Server vil kunne oprette forbindelse til SQL Server-forekomsten ved hjælp af **VNet interne** IP-adressen til SQL Server virtual machine.  

Forbindelsesstrengen eksempel nedenfor henviser til SQL Server ved hjælp af dens privat IP-adresse.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Selvom den virtuelle maskine har et offentligt slutpunkt, afvises forsøg på forbindelse ved hjælp af den offentlige IP-adresse på grund af netværket ACL. 

## <a name="restricting-access-with-a-network-security-group"></a>Begrænse adgang med en netværk sikkerhedsgruppe
Der er en alternativ fremgangsmåde for at sikre adgang med en netværk sikkerhedsgruppe.  Netværk sikkerhedsgrupper kan anvendes til individuelle virtuelle maskiner eller til et undernet, der indeholder virtuelle computere.

En sikkerhedsgruppe netværk skal først oprettes:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Begrænse adgangen til kun VNet intern trafik er meget simpel med en netværk sikkerhedsgruppe.  Standardreglerne i en netværk sikkerhedsgruppe Tillad kun adgang fra andre netværksklienter i det samme virtuelle netværk.

Låse adgang til SQL Server som et resultat er så enkelt som at anvende en netværk sikkerhedsgruppe med dens standardregler på enten de virtuelle maskiner, der kører SQL Server eller det undernet, der indeholder de virtuelle computere.

Eksemplet nedenfor gælder sikkerhedsgruppe netværk for det med undernet:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Afslut resultatet er et regelsæt for sikkerhed, der forhindrer ekstern adgang, mens VNet interne adgang:

![Standard netværk sikkerhedsregler][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Kom godt i gang
Alle artikler og hvordan-til er for App-tjenesten miljøer er tilgængelige i [vigtige oplysninger om programmet Service miljøer](../app-service/app-service-app-service-environments-readme.md).

For at komme i gang med App-tjenesten miljøer skal du se [Introduktion til App Service-miljø][IntroToAppServiceEnvironment]

Yderligere oplysninger omkring styre indgående trafik til dit App servicemiljø finder du [styre indgående trafik til et App Service-miljø][ControlInboundASE]

Du kan finde flere oplysninger om Azure App Service platformen, se [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
