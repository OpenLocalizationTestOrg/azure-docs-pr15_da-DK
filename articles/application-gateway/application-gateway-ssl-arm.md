<properties
   pageTitle="Konfigurere et program-gateway for SSL aflastning ved hjælp af Azure ressourcestyring | Microsoft Azure"
   description="Denne side indeholder instruktioner til at oprette et program-gateway med SSL offload ved hjælp af Azure ressourcestyring"
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
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Konfigurere et program-gateway for SSL aflastning ved hjælp af Azure ressourcestyring

> [AZURE.SELECTOR]
-[Azure Portal](application-gateway-ssl-portal.md)
-[Azure ressourcestyring PowerShell](application-gateway-ssl-arm.md)
-[Azure klassisk PowerShell](application-gateway-ssl.md)

 Azure Application Gateway kan konfigureres til at afslutte sessionen Secure Sockets Layer (SSL) på din gateway for at undgå dyrt SSL dekryptering opgaver for at opstå på webfarmen. SSL aflastning forenkler også front end-server-konfiguration og administration af webprogrammet.

## <a name="before-you-begin"></a>Inden du går i gang

1. Installere den nyeste version af Azure PowerShell-cmdlet'er ved hjælp af Web Platform installationsprogrammet. Du kan hente og installere den nyeste version fra sektionen **Windows PowerShell** på [downloadsiden](https://azure.microsoft.com/downloads/).
2. Du opretter et virtuelt netværk og et undernet til gatewayen programmet på computeren. Sørg for, at ingen virtuelle maskiner eller skyen installationer bruger undernettet. Application Gateway være alene i et virtuelt netværksundernet.
3. De servere, du konfigurerer for at bruge programmet gatewayen skal findes, eller har tildelt deres slutpunkter, der er oprettet i det virtuelle netværk eller med en offentlige IP-/ VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Hvad er påkrævet for at oprette et program-gateway?


- **Back end-server programgruppen:** Listen over IP-adresserne på back-end-servere. De viste IP-adresser skal enten tilhører det virtuelle netværksundernet eller skal være en offentlige IP-adresse/VIP.
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-port:** Denne port er den offentlige port, der er åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back-end-servere.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https disse indstillinger er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload).
- **Regel:** Reglen binder lytteren og back-end-serveren puljen og definerer hvilken back end-server puljen trafikken skal sendes til, når det rammer en bestemt lytter. I øjeblikket, understøttes kun den *grundlæggende* regel. *Grundlæggende* reglen er round robin-indlæsning fordeling.

**Yderligere konfiguration noter**

Konfiguration af SSL-certifikater, opnår protocol i **HttpListener** ændre til *Https* (store og små bogstaver). Elementet **SslCertificate** føjes til **HttpListener** med den variable værdi, der er konfigureret til SSL-certifikat. Front end-port skal opdateres til 443.

**Aktivere cookie-baseret forbindelse**: et program-gateway kan konfigureres for at sikre, at en anmodning om fra en klientsession altid bedt om at den samme VM i webfarmen. Dette scenario er udført af indsættelse af en sessionscookie, der gør det muligt for gateway til at dirigere trafik korrekt. For at aktivere cookie-baseret forbindelse skal du angive **CookieBasedAffinity** til *aktiveret* i elementet **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Forskellen mellem at bruge Azure klassisk implementeringsmodel og Azure ressourcestyring er den rækkefølge, som du opretter et program-gateway og de elementer, der skal konfigureres.

Med ressourcestyring alle komponenter i et program-gateway er konfigureret enkeltvis, og angiv derefter sammen for at oprette en programmet gateway ressource.


Her er de trin, der bruges til at oprette et program-gateway:

1. Oprette en ressourcegruppe til Ressourcestyring
2. Oprette virtuelle netværk, undernet og offentlige IP-adresse til gatewayen program
3. Oprette et program gateway konfigurationsobjekt
4. Oprette en programmet gateway ressource


## <a name="create-a-resource-group-for-resource-manager"></a>Oprette en ressourcegruppe til Ressourcestyring

Sørg for, at du har skiftet PowerShell tilstand for at bruge-cmdletter Azure ressourcestyring. Flere oplysninger er tilgængelige ved [Hjælp af Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Trin 1

    Login-AzureRmAccount

### <a name="step-2"></a>Trin 2

Markér abonnementer til kontoen.

    Get-AzureRmSubscription

Du bliver bedt om at godkende med dine legitimationsoplysninger.<BR>

### <a name="step-3"></a>Trin 3

Vælg, hvilke af dine Azure abonnementer til brug. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Trin 4

Oprette en ressourcegruppe (Spring dette trin, hvis du bruger en eksisterende ressourcegruppe).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Denne indstilling bruges som standardplacering til ressourcer i denne ressourcegruppe. Sørg for, at alle kommandoer til at oprette et program-gateway bruger samme ressourcegruppe.

I eksemplet ovenfor oprettede vi en ressourcegruppe med navnet "appgw-indbyggede RG" og en placering "Vest os".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Oprette et virtuelt netværk og et undernet til gatewayen program

I følgende eksempel viser, hvordan du opretter et virtuelt netværk ved hjælp af Ressourcestyring:

### <a name="step-1"></a>Trin 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Dette eksempel tildeles adresse område 10.0.0.0/24 til en undernet variabel skal bruges til at oprette et virtuelt netværk.

### <a name="step-2"></a>Trin 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Dette eksempel opretter et virtuelt netværk med navnet "appgwvnet" i ressource gruppe "appgw-indbyggede rg" til området Vest USA til brug af præfikset 10.0.0.0/16 med undernet 10.0.0.0/24.

### <a name="step-3"></a>Trin 3

    $subnet = $vnet.Subnets[0]

Dette eksempel tildeles undernetobjektet til variabel $subnet for de næste trin.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Oprette en offentlig IP-adresse til front end-konfiguration

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Dette eksempel opretter en offentlige IP-ressource "publicIP01" i ressource gruppe "appgw-indbyggede rg" for området, vest USA.


## <a name="create-an-application-gateway-configuration-object"></a>Oprette et program gateway konfigurationsobjekt

### <a name="step-1"></a>Trin 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Dette eksempel opretter en gateway IP-programkonfiguration med navnet "gatewayIP01". Når Application Gateway starter, henter en IP-adresse fra det undernet, der er konfigureret og omdirigere netværkstrafik til IP-adresser i back end-IP-puljen. Husk på, at hver forekomst tager én IP-adresse.

### <a name="step-2"></a>Trin 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Dette eksempel konfigurerer back end-IP-adressegruppe med navnet "pool01" med IP-adresser "134.170.185.46, 134.170.188.221,134.170.185.50." Disse værdier er de IP-adresser, modtager den netværkstrafik, som kommer fra front end IP-slutpunktet. Erstat IP-adresser fra det foregående eksempel med web application slutpunkterne IP-adresser.

### <a name="step-3"></a>Trin 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Dette eksempel konfigurerer programmet gateway indstillingen "poolsetting01" til at indlæse afstemmes netværkstrafik i back end-puljen.

### <a name="step-4"></a>Trin 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Dette eksempel konfigurerer front end IP-port med navnet "frontendport01" til den offentlige IP-slutpunkt.

### <a name="step-5"></a>Trin 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Dette eksempel konfigurerer certifikatet, der bruges til SSL-forbindelse. Certifikatet, der skal være i .pfx-format, og adgangskoden, skal være mellem tegn med 4 til 12.

### <a name="step-6"></a>Trin 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Dette eksempel opretter den front end IP-konfiguration med navnet "fipconfig01" og knytter den offentlige IP-adresse til front end IP-konfigurationen.

### <a name="step-7"></a>Trin 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Dette eksempel opretter lytteren "listener01" og knytter den front end-port til front end IP-konfiguration og certifikat.

### <a name="step-8"></a>Trin 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Dette eksempel opretter Indlæs belastningsjusteringstjenesten routing reglen med navnet "rule01", der konfigurerer Indlæs belastningsjusteringstjenesten funktionsmåden.

### <a name="step-9"></a>Trin 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Dette eksempel konfigurerer forekomst størrelsen af gatewayen programmet på computeren.

>[AZURE.NOTE]  Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Du kan vælge mellem Standard_Small, Standard_Medium og Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Oprette et program-gateway ved hjælp af ny AzureApplicationGateway

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Dette eksempel opretter et program-gateway med alle elementer fra de foregående trin til konfiguration. I eksemplet hedder gatewayen programmet "appgwtest".

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

Hvis du vil konfigurere et program-gateway til brug med en intern justering af belastning (ILB), skal du se [oprette et program-gateway med en intern justering af belastning (ILB)](application-gateway-ilb.md).

Hvis du vil finde flere oplysninger om indlæse indstillinger for justering af belastning Generelt, skal du se:

- [Azure justering af belastning](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
