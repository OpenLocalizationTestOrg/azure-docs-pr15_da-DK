<properties
    pageTitle="Konfigurere SSL politik og til slut SSL med Application Gateway | Microsoft Azure"
    description="I denne artikel beskriver, hvordan du konfigurerer start til slut SSL med Application Gateway ved hjælp af Azure ressourcestyring PowerShell"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Konfigurere SSL politik og til slut SSL med Application Gateway ved hjælp af PowerShell

## <a name="overview"></a>Oversigt

Application Gateway understøtter start til slut kryptering af trafik. Application Gateway gør dette ved at afbryde SSL-forbindelse på programmet gateway. Gatewayen derefter gælder routing reglerne for trafikken, igen krypterer pakken eller videresender pakken til de relevante back-end, der er baseret på de routing regler, der er defineret. Eventuelle svar fra webserveren går gennem den samme fremgangsmåde tilbage til slutbrugeren.

En anden funktion, der programmet gateway understøtter deaktivering af visse SSL-protokollen versioner. Application Gateway understøtter deaktivere den følgende protocol-version TLSv1.0, TLSv1.1 eller TLSv1.2.

> [AZURE.NOTE] SSL 2.0 og SSL 3.0 er som standard deaktiveret og kan ikke aktiveres. De betragtes som usikker og kan ikke bruges med Application Gateway

![scenarie billede][scenario]

## <a name="scenario"></a>Scenarie

I dette scenarie skal du få mere at vide, hvordan du opretter et program-gateway ved hjælp af start til slut SSL ved hjælp af PowerShell.

Dette scenarie skal:

- Oprette en ressourcegruppe med navnet "appgw-indbyggede rg"
- Oprette et virtuelt netværk med navnet "appgwvnet" med en reserveret CIDR blok med 10.0.0.0/16.
- Opret to undernet titlen "appgwsubnet" og "appsubnet".
- Oprette en lille program gateway understøttende start til slut SSL-kryptering, der deaktiverer bestemte SSL-protokoller.

## <a name="before-you-begin"></a>Inden du går i gang

Hvis du vil konfigurere start til slut SSL med et program-gateway, et certifikat er påkrævet for gateway, og der kræves certifikater til back end-servere. Gatewayens certifikat bruges til at kryptere og dekryptere trafikken sendes til den ved hjælp af SSL. Gatewayens certifikat skal være i formatet Personal Information Exchange (pfx). I dette filformat giver mulighed for privat nøgle der skal eksporteres som kræves af gatewayen programmet på computeren til at udføre kryptering og dekryptering af trafik.

Start til slut ssl-kryptering være back-end whitelisted med application gateway. Det gør du ved at uploade det offentlige certifikat i en back-end til gatewayen programmet på computeren. Dette sikrer, at gatewayen programmet kun kommunikerer med kendte back end-forekomster. Dette sikrer yderligere start til slut kommunikation.

Denne proces er beskrevet i følgende trin:

## <a name="create-the-resource-group"></a>Oprette ressourcegruppen

Dette afsnit vejleder dig gennem oprettelse af en ressourcegruppe, der indeholder gatewayen programmet på computeren.

### <a name="step-1"></a>Trin 1

Log på din Azure-konto.

    Login-AzureRmAccount

### <a name="step-2"></a>Trin 2

Vælg abonnementet, der skal bruges til dette scenario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Trin 3

Oprette en ressourcegruppe (Spring dette trin, hvis du bruger en eksisterende ressourcegruppe).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Oprette et virtuelt netværk og et undernet til gatewayen program

I følgende eksempel oprettes et virtuelt netværk og to undernet. Ét undernet bruges til at holde gatewayen programmet på computeren. Andet undernettet bruges til en back-end vært webprogrammet.

### <a name="step-1"></a>Trin 1

Tildele et adresseområde til undernettet anvendes af programmet gatewayen sig selv.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Undernet konfigureret programmet gatewayen skal tilpasses korrekt. Et program-gateway kan konfigureres op til 10 forekomster. Hver forekomst tager 1 IP-adresse fra undernettet. For små af et undernet kan det påvirke skalering af et program-gateway.

### <a name="step-2"></a>Trin 2

Tildele et adresseområde skal bruges til adresse back end-puljen.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Trin 3

Oprette et virtuelt netværk med de undernet, der er defineret i de forrige trin.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Trin 4

Hente de virtuelt netværksressource og undernet ressourcer, der kan bruges i de følgende trin:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Oprette en offentlig IP-adresse til front end-konfiguration

Opret en offentlig IP-ressource skal bruges til gatewayen programmet på computeren. Denne offentlige IP-adresse bruges et følgende trin.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Application Gateway understøtter ikke brug for en offentlig IP-adresse, der er oprettet med en domæne etiket, der er defineret. Kun en offentlig IP-adresse med en dynamisk oprettede domæne etiket understøttes. Hvis du kræver et fuldt DNS-navn for gatewayen programmet, er det anbefales at bruge en cname-post som et alias.

## <a name="create-an-application-gateway-configuration-object"></a>Oprette et program gateway konfigurationsobjekt

Du skal konfigurere elementer til konfiguration af alle før du opretter gatewayen programmet på computeren. Følgende trin Opret de konfigurationselementer, der skal bruges for et program gateway ressource.

### <a name="step-1"></a>Trin 1

Opret en gateway-programkonfiguration til IP-adresse, denne indstilling konfigurerer hvilke undernet gatewayen program bruger. Når programmet gateway starter, henter en IP-adresse fra det undernet, der er konfigureret og dirigerer netværkstrafik til IP-adresser i back end-IP-puljen. Husk på, at hver forekomst tager én IP-adresse.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Trin 2

Oprette en front-end-IP-konfiguration, denne indstilling tilknyttes en privat eller offentlig IP-adresse til front end gatewayen programmet på computeren. Følgende trin knytter den offentlige IP-adresse i det foregående trin til front end IP-konfigurationen.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Trin 3

Konfigurere back end-IP-adressegruppe med back end-webserverne IP-adresser. Disse IP-adresser er de IP-adresser, modtager den netværkstrafik, som kommer fra front end IP-slutpunktet. Du erstatte følgende IP-adresser for at tilføje dine egne slutpunkter for programmet IP-adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Et fuldt domænenavn (fulde Domænenavn) er også en gyldig værdi i stedet for en IP-adresse til back end-servere ved hjælp af parameteren - BackendFqdns.

### <a name="step-4"></a>Trin 4

Konfigurere den front end IP-port for den offentlige IP-slutpunkt. Denne port er den port, slutbrugere oprette forbindelse til.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Trin 5

Konfigurere certifikat til gatewayen programmet på computeren. Dette certifikat bruges til at dekryptere og kryptere trafik på gatewayen programmet igen.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Dette eksempel konfigurerer certifikatet, der bruges til SSL-forbindelse. Certifikatet, der skal være i .pfx-format, og adgangskoden skal være mellem tegn med 4 til 12.

### <a name="step-6"></a>Trin 6

Oprette HTTP lytteren af programmet gatewayen. Tildele front end IP-konfiguration, port og ssl certifikat til brug.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Trin 7

Upload certifikatet, der skal bruges på ssl aktiveret back end-puljen ressourcer.

> [AZURE.NOTE] Standard efterprøvning af af bliver offentlig nøgle fra **standard** -SSL-binding på den back end-'s IP-adresse og sammenligner den offentlige nøgleværdi den modtager til den offentlige nøgleværdi, du angiver her. Det hentede offentlig nøgle muligvis ikke nødvendigvis det pågældende område, hvor trafikken går **Hvis** du bruger host sidehoveder og SNI på back end. Hvis du er i tvivl, ved at besøge https://127.0.0.1/ på tilbage-enderne for at bekræfte, hvilket certifikat bruges til **standard** -SSL-binding. Brug den offentlig nøgle fra denne anmodning i dette afsnit. Hvis du bruger host sidehoveder og SNI på HTTPS bindinger, og du ikke modtager en svar og certifikat fra en manuel browseranmodning til https://127.0.0.1/ på tilbage-enderne, skal du konfigurere en standard-SSL binding i back-ender. Hvis du ikke gør det, sonder mislykkes, og back end-bliver ikke være whitelisted.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] Det certifikat, der er angivet i dette trin skal være offentlig nøgle af pfx certifikatet findes på back-end. Eksportere certifikat (ikke rodcertifikat), der er installeret på back end-serveren i. Virksomheden formatere og bruge det i dette trin. Dette trin whitelists back end-med application gateway.

### <a name="step-8"></a>Trin 8

Konfigurere programindstillinger back end-http gateway. Tildele det certifikat, der er overført i det foregående trin til http-indstillinger.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Trin 9

Oprette en Indlæs belastningsjusteringstjenesten routing regel, der konfigurerer Indlæs belastningsjusteringstjenesten funktionsmåden. I dette eksempel oprettes et grundlæggende round robin reglen.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Trin 10

Konfigurere forekomst størrelsen af gatewayen programmet på computeren.  De tilgængelige størrelser er **Standard\_lille**, **Standard\_mellem**, og **Standard\_stor**.  For kapacitet er de tilgængelige værdier mellem 1 og 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] En forekomst antallet af 1 kan vælges til testformål. Det er vigtigt at vide, at en hvilken som helst forekomst Tæl under to forekomster ikke er omfattet af SERVICEAFTALEN og derfor anbefales ikke. Lille gateways, der skal bruges til Udviklingscenter test og ikke til fremstilling formål.

### <a name="step-11"></a>Trin 11

Konfigurere SSL politikken til at blive brugt på gatewayen programmet på computeren. Application Gateway understøtter muligheden for at deaktivere visse SSL-protokollen versioner.

Følgende værdier er en liste over protocol versioner, der kan være deaktiveret.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

I følgende eksempel deaktiverer TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Oprette gatewayen program

Med alle de foregående trin kan du oprette gatewayen programmet på computeren. Oprettelse af gatewayen er en længerevarende proces.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Deaktivere SSL-protokollen versioner på en eksisterende Application Gateway

De foregående trin fører dig gennem oprettelse af et program med start til slut ssl og deaktivering af visse SSL-protokollen versioner. I følgende eksempel deaktiverer bestemte SSL politikker på et eksisterende program-gateway.

### <a name="step-1"></a>Trin 1

Hent gatewayen programmet på computeren til at opdatere.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Trin 2

Definere en SSL-politik. I eksemplet nedenfor er TLSv1.0 og TLSv1.1 deaktiveret.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Trin 3

Til sidst skal Opdater gatewayen. Det er vigtigt at være opmærksom på, at det sidste trin er en længerevarende opgave. Når guiden er fuldført, er Start til slut ssl konfigureret på gatewayen programmet på computeren.

    $gw | Set-AzureRmApplicationGateway

## <a name="get-application-gateway-dns-name"></a>Få programmet gateway DNS-navn

Når gatewayen er oprettet, er næste trin at konfigurere front end til kommunikation. Når du bruger en offentlige IP-adresse, kræver programmet gateway et dynamisk tildelt DNS-navn, som ikke er fuldt. For at sikre slutbrugere kan ramme gatewayen programmet en CNAME-post kan bruges til at pege på det offentlige slutpunkt for programmet gateway. [Konfigurere et brugerdefineret domænenavn til i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Hente oplysninger om programmet gatewayen og dens tilknyttede IP-/ DNS-navn ved hjælp af det PublicIPAddress element, der er knyttet til gatewayen programmet for at gøre dette. Gatewayen programmet DNS-navn skal bruges til at oprette en CNAME-post, der peger på to webprogrammer til denne DNS-navn. Brug af A-poster anbefales ikke, da VIP kan ændre på Genstart for programmet gateway.
    
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

Få mere at vide om hardening sikkerheden for dine webprogrammer med Firewall til webprogrammer gennem Application Gateway ved at besøge [Web Application Firewall oversigt](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
