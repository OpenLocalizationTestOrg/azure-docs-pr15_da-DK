<properties
   pageTitle="Konfigurere Firewall til webprogrammer på ny eller eksisterende Application Gateway | Microsoft Azure"
   description="I denne artikel indeholder vejledning i at begynde at bruge firewall til webprogrammer på et eksisterende eller nye program-gateway."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Konfigurere Firewall til webprogrammer på en ny eller eksisterende Application Gateway

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-web-application-firewall-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-web-application-firewall-powershell.md)

Web application firewallen (WAF) i Azure Application Gateway beskytter webprogrammer fra fælles webbaserede angreb som SQL-indsættelse, angreb via scripts på tværs af websteder og session hijacks.

Azure Application Gateway er lag-7 belastningsjustering. Den indeholder failover, ydeevne-routing HTTP-anmodninger mellem forskellige servere, uanset om de er i skyen eller i det lokale miljø. Programmet indeholder mange programmet levering Controller (ADC) funktioner, herunder HTTP belastning, cookie-baserede session forbindelse, Secure Sockets Layer (SSL) offload, brugerdefinerede sundhed sonder, understøttelse af flere websted og mange andre. For at finde en komplet liste over understøttede funktioner skal du besøge programmet Gateway oversigt

Følgende artikel viser hvordan du [tilføjer firewall til webprogrammer til et eksisterende program-gateway](#add-web-application-firewall-to-an-existing-application-gateway) og [oprette et program-gateway, der bruger firewall til webprogrammer](#create-an-application-gateway-with-web-application-firewall).

![scenarie billede][scenario]

## <a name="waf-configuration-differences"></a>WAF konfigurationsforskelle

Hvis du har læst [Opret et program-Gateway med PowerShell](application-gateway-create-gateway-arm.md), forstå SKU indstillingerne til at konfigurere, når du opretter et program-gateway. WAF giver yderligere indstillinger til at definere, når du konfigurerer SKU til et program-gateway. Der er ingen yderligere ændringer, du foretager i programmet gatewayen sig selv.

**SKU** - en normal programmet gateway uden WAF understøtter **Standard\_lille**, **Standard\_mellem**, og **Standard\_stor** størrelser. Med introduktionen af WAF, der er to ekstra lagerenheder **WAF\_mellem** og **WAF\_stor**. WAF understøttes ikke på lille Programgateways.

**Niveau** - de tilgængelige værdier er **Standard** eller **WAF**. Når du bruger Firewall til webprogrammer, skal **WAF** vælges.

**Tilstand** - denne indstilling er WAF-tilstand. tilladte værdier er **registrering** og **forebyggelse**. Når WAF er konfigureret i tilstanden registrering, gemmes alle trusler i en logfil. Hændelser, der stadig er logget i forebyggelse tilstand, men hackeren modtager et 403 uautoriseret svar fra programmet gatewayen.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Føje firewall til webprogrammer til et eksisterende program-gateway

Sørg for, at du bruger den nyeste version af Azure PowerShell. Flere oplysninger er tilgængelige ved [Hjælp af Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Trin 1

Log på din Azure-konto.

    Login-AzureRmAccount

### <a name="step-2"></a>Trin 2

Vælg abonnementet, der skal bruges til dette scenario.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Trin 3

Hent den gateway, du tilføjer Firewall til webprogrammer til.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Trin 4

Konfigurere web application firewall sku. De tilgængelige størrelser er **WAF\_stor** og **WAF\_mellem**. Når firewall til webprogrammer bruges niveauet, skal være **WAF**, kapaciteten skal bekræftes, når du angiver sku.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Trin 5

Konfigurere indstillingerne for WAF, som defineret i følgende eksempel:

For at indstillingen **WafMode** er de tilgængelige værdier forebyggelse og registrering.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Trin 6

Opdater gatewayen program med de indstillinger, der er defineret i det foregående trin.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Denne kommando opdaterer gatewayen program med Firewall til webprogrammer. Det anbefales at få vist [Programmet Gateway diagnosticering](application-gateway-diagnostics.md) for at forstå, hvordan du kan få vist logfiler for dit program gateway. På grund af WAF sikkerhed art skal logfiler revideres regelmæssigt for at forstå sikkerhed stilling i dine webprogrammer.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Oprette et program-Gateway med Firewall til webprogrammer

Følgende trin fører dig gennem hele processen fra start til slut til at oprette et program-Gateway med Firewall til webprogrammer.

Sørg for, at du bruger den nyeste version af Azure PowerShell. Flere oplysninger er tilgængelige ved [Hjælp af Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Trin 1

Log på Azure

    Login-AzureRmAccount

Du bliver bedt om at godkende med dine legitimationsoplysninger.

### <a name="step-2"></a>Trin 2

Markér abonnementer til kontoen.

    Get-AzureRmSubscription

### <a name="step-3"></a>Trin 3

Vælg, hvilke af dine Azure abonnementer til brug.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Trin 4

Oprette en ressourcegruppe (Spring dette trin, hvis du bruger en eksisterende ressourcegruppe).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Denne placering bruges som standardplacering til ressourcer i denne ressourcegruppe. Sørg for, at alle kommandoer til at oprette et program-gateway bruger samme ressourcegruppe.

I det foregående eksempel oprettede vi en ressourcegruppe med navnet "appgw-indbyggede RG" og en placering "Vest os".

>[AZURE.NOTE] Hvis du vil konfigurere et brugerdefineret efterprøvning af af dit program gatewayen skal du se [oprette et program-gateway med brugerdefinerede sonder ved hjælp af PowerShell](application-gateway-create-probe-ps.md). Se [brugerdefinerede sonder og sundhedsovervågning](application-gateway-probe-overview.md) kan finde flere oplysninger.

### <a name="step-5"></a>Trin 5

Tildele et adresseområde til undernettet anvendes af programmet gatewayen sig selv.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Et undernet for et program skal have mindst 28 inputmaske bit. Denne værdi lader 10 tilgængelige adresser i undernet til programmet gatewayforekomster. Med en mindre undernet, kan du muligvis ikke tilføje flere forekomst af dit program gateway i fremtiden.

### <a name="step-6"></a>Trin 6

Tildele et adresseområde skal bruges til adresse back end-puljen.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Trin 7

Oprette et virtuelt netværk med de foregående undernet i ressourcegruppen oprettede i trin: [Opret ressourcegruppen](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Trin 8

Hente de virtuelt netværksressource og undernet ressourcer, der kan bruges i de følgende trin:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Trin 9

Opret en offentlig IP-ressource skal bruges til gatewayen programmet på computeren. Denne offentlige IP-adresse bruges i en af følgende trin:

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Application Gateway understøtter ikke brug for en offentlig IP-adresse, der er oprettet med en domæne etiket, der er defineret. Kun en offentlig IP-adresse med en dynamisk oprettede domæne etiket understøttes. Hvis du kræver et fuldt DNS-navn for gatewayen programmet, er det anbefales at bruge en cname-post som et alias.

### <a name="step-10"></a>Trin 10

Du skal konfigurere elementer til konfiguration af alle før du opretter gatewayen programmet på computeren. Følgende trin Opret de konfigurationselementer, der skal bruges for et program gateway ressource.

Opret en gateway-programkonfiguration til IP-adresse, dette konfigurerer hvilke undernet gatewayen program bruger. Når Application Gateway starter, henter en IP-adresse fra det undernet, der er konfigureret og dirigerer netværkstrafik til IP-adresser i back end-IP-puljen. Husk på, at hver forekomst tager én IP-adresse.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Trin 11

Konfigurere back end-IP-adressegruppe med back end-webserverne IP-adresser. Disse IP-adresser er de IP-adresser, modtager den netværkstrafik, som kommer fra front end IP-slutpunktet. Du erstatte følgende IP-adresser for at tilføje dine egne slutpunkter for programmet IP-adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Trin 12

Upload certifikatet, der skal bruges på ssl aktiveret back end-puljen ressourcer.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Trin 13

Konfigurere programindstillinger back end-http gateway. Tildele det certifikat, der er overført i det foregående trin til http-indstillinger.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Trin 14

Konfigurere den front end IP-port for den offentlige IP-slutpunkt. Denne port er den port, slutbrugere oprette forbindelse til.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Trin 15

Oprette en front-end-IP-konfiguration, denne indstilling tilknyttes en privat eller offentlig IP-adresse til front end gatewayen programmet på computeren. Følgende trin knytter den offentlige IP-adresse i det foregående trin til front end IP-konfigurationen.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Trin 16

Konfigurere certifikat til gatewayen programmet på computeren. Dette certifikat bruges til at dekryptere og kryptere trafik på gatewayen programmet igen.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Trin 17

Oprette HTTP lytteren af programmet gatewayen. Tildele front end IP-konfiguration, port og ssl certifikat til brug.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Trin 18

Oprette en Indlæs belastningsjusteringstjenesten routing regel, der konfigurerer Indlæs belastningsjusteringstjenesten funktionsmåden. I dette eksempel oprettes et grundlæggende round robin reglen.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Trin 19

Konfigurere forekomst størrelsen af gatewayen programmet på computeren.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Du kan vælge mellem **WAF\_mellem** og **WAF\_stor**, niveauet, når ved hjælp af WAF er altid **WAF**. Kapacitet er et vilkårligt tal mellem 1 og 10.

### <a name="step-20"></a>Trin 20

Konfigurere tilstanden for WAF, acceptable værdier er **at forebygge** og **opdage**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Trin 21

Oprette et program-gateway med alle elementer fra de foregående trin til konfiguration. I dette eksempel er gatewayen program kaldet "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

## <a name="get-application-gateway-dns-name"></a>Få programmet gateway DNS-navn

Når gatewayen er oprettet, er næste trin at konfigurere front end til kommunikation. Når du bruger en offentlige IP-adresse, kræver programmet gateway et dynamisk tildelt DNS-navn, som ikke er fuldt. For at sikre slutbrugere kan ramme gatewayen programmet en CNAME-post kan bruges til at pege på det offentlige slutpunkt for programmet gateway. [Konfigurere et brugerdefineret domænenavn til i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Hente oplysninger om programmet gatewayen og dens tilhørende IP-/ DNS-oplysninger om navn ved hjælp af det PublicIPAddress element, der er knyttet til gatewayen programmet for at gøre dette. Gatewayen programmet DNS-navn skal bruges til at oprette en CNAME-post, der peger på to webprogrammer til denne DNS-navn. Brug af A-poster anbefales ikke, da VIP kan ændre på Genstart for programmet gateway.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Næste trin

Lær, hvordan du kan konfigurere logføring af diagnostik, hvis du vil logge de hændelser, der er fundet eller forhindret med Firewall til webprogrammer ved at besøge [Programmet Gateway diagnosticering](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png