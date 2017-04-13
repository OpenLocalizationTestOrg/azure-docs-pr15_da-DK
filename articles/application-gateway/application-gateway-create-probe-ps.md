<properties
   pageTitle="Oprette en brugerdefineret efterprøvning af af til Application Gateway ved hjælp af PowerShell i ressourcestyring | Microsoft Azure"
   description="Lær at oprette en brugerdefineret efterprøvning af af til Application Gateway ved hjælp af PowerShell i ressourcestyring."
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Oprette en brugerdefineret efterprøvning af af til Azure Application Gateway ved hjælp af PowerShell til Azure ressourcestyring

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-probe-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-probe-ps.md)
- [Azure klassisk PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](application-gateway-create-probe-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Trin 1

Bruge logon-AzureRmAccount til at godkende.

    Login-AzureRmAccount

### <a name="step-2"></a>Trin 2

Markér abonnementer til kontoen.

    Get-AzureRmSubscription

### <a name="step-3"></a>Trin 3

Vælg, hvilke af dine Azure abonnementer til brug. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Trin 4

Oprette en ressourcegruppe (Spring dette trin, hvis du bruger en eksisterende ressourcegruppe).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Denne placering bruges som standardplacering til ressourcer i denne ressourcegruppe. Sørg for, at alle kommandoer til at oprette et program-gateway bruger samme ressourcegruppe.

I eksemplet ovenfor oprettede vi en ressourcegruppe med navnet "appgw-indbyggede RG" og en placering "Vest os".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Oprette et virtuelt netværk og et undernet til gatewayen program

Følgende trin oprette et virtuelt netværk og et undernet til gatewayen programmet på computeren.

### <a name="step-1"></a>Trin 1


Tildele adresse område 10.0.0.0/24 til en undernet variabel skal bruges til at oprette et virtuelt netværk.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Trin 2

Oprette et virtuelt netværk med navnet "appgwvnet" i ressource gruppe "appgw-indbyggede rg" til området Vest USA til brug af præfikset 10.0.0.0/16 med undernet 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Trin 3

Tildele en undernet variabel for de næste trin, som opretter et program-gateway.

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Oprette en offentlig IP-adresse til front end-konfiguration


Opret en offentlig IP-ressource "publicIP01" i ressource gruppe "appgw-indbyggede rg" for området, vest USA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Oprette et program gateway konfigurationsobjekt med en brugerdefineret efterprøvning af af

Du kan konfigurere elementer til konfiguration af alle før du opretter gatewayen programmet på computeren. Følgende trin Opret de konfigurationselementer, der skal bruges for et program gateway ressource.

### <a name="step-1"></a>Trin 1

Oprette en gateway IP-programkonfiguration med navnet "gatewayIP01". Når Application Gateway starter, henter en IP-adresse fra det undernet, der er konfigureret og omdirigere netværkstrafik til IP-adresser i back end-IP-puljen. Husk på, at hver forekomst tager én IP-adresse.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Trin 2


Konfigurere back end-IP-adressegruppe med navnet "pool01" med IP-adresser "134.170.185.46, 134.170.188.221,134.170.185.50". Disse værdier er de IP-adresser, modtager den netværkstrafik, som kommer fra front end IP-slutpunktet. Du erstatte de ovenfor for at føje dine egne programmet IP-adresse slutpunkter IP-adresser.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>Trin 3


Den brugerdefinerede efterprøvning af af er konfigureret i dette trin.

De parametre, bruges er:

- **Interval** - konfigurerer efterprøvning af af interval Kontroller i sekunder.
- **Timeout** – definerer efterprøvning af af timeout for en HTTP-svar check.
- **-Hostname og sti** - fulde URL-sti, som er gældende af Application Gateway til at bestemme tilstanden for forekomsten. Eksempelvis hvis du har et websted http://contoso.com/, kan derefter på brugerdefineret efterprøvning af af konfigureres til "http://contoso.com/path/custompath.htm" for efterprøvning af af Kontroller skal have en vellykket HTTP-svar.
- **UnhealthyThreshold** - antallet af mislykkede HTTP-svar, der er behov for at markere den forekomst af back end-som *beskadiget*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>Trin 4

Konfigurere programmet gateway indstillingen "poolsetting01" for trafikken i back end-puljen. Dette trin har også en timeout konfiguration, som er til back end-puljen svaret til en anmodning om anvendelse af gatewayen. Når et back end-svar rammer en timeout grænse, annullerer Application Gateway anmodningen. Denne værdi er forskellig fra en efterprøvning af af timeout, som er kun til back end-svaret forespørgsler trykkeri.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>Trin 5

Konfigurere den front end IP-port med navnet "frontendport01" til den offentlige IP-slutpunkt.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>Trin 6

Opret den front end IP-konfiguration med navnet "fipconfig01" og knytte den offentlige IP-adresse til front end IP-konfigurationen.


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>Trin 7

Oprette lytteren navnet "listener01" og knytte den front end-port til front end IP-konfigurationen.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>Trin 8

Oprette Indlæs belastningsjusteringstjenesten routing reglen med navnet "rule01", der konfigurerer Indlæs belastningsjusteringstjenesten funktionsmåden.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>Trin 9

Konfigurere forekomst størrelsen af gatewayen programmet på computeren.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Du kan vælge mellem Standard_Small, Standard_Medium og Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Oprette et program-gateway ved hjælp af ny AzureRmApplicationGateway

Oprette et program-gateway med alle elementer til konfiguration af fra ovenstående trin. I dette eksempel er gatewayen program kaldet "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Føje en efterprøvning af af til et eksisterende program-gateway

Du har fire trin at føje en brugerdefineret efterprøvning af af til et eksisterende program-gateway.

### <a name="step-1"></a>Trin 1

Indlæse programmet gateway ressource i en PowerShell-variabel ved hjælp af **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Trin 2

Føje en efterprøvning af af til den eksisterende gateway-konfiguration.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


Den brugerdefinerede efterprøvning af af i eksemplet er konfigureret til at kontrollere, om URL-sti contoso.com/path/custompath.htm hvert 30. En timeout grænseværdi for 120 sekunder er konfigureret med et maksimalt antal 8 mislykkede forespørgsler.

### <a name="step-3"></a>Trin 3

Tilføje efterprøvning af af til back end-puljen indstilling konfiguration og timeout ved hjælp af **-angive-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>Trin 4

Gemme konfigurationen af gatewayen program ved hjælp af **Sæt AzureRmApplicationGateway**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Fjerne en efterprøvning af af fra et eksisterende program-gateway

Her er trinnene for at fjerne en brugerdefineret efterprøvning af af fra et eksisterende program-gateway.

### <a name="step-1"></a>Trin 1

Indlæse programmet gateway ressource i en PowerShell-variabel ved hjælp af **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>Trin 2

Fjern efterprøvning af af konfigurationen fra programmet gateway ved hjælp af **Fjern AzureRmApplicationGatewayProbeConfig**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>Trin 3

Opdatere back end-puljen indstilling til at fjerne efterprøvning af af og indstillingen for timeout for ved hjælp af **-angive-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Trin 4

Gemme konfigurationen af gatewayen program ved hjælp af **Sæt AzureRmApplicationGateway**. 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

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

Lær, hvordan du konfigurerer SSL overføre ved at besøge [Konfigurere SSL Offload](application-gateway-ssl-arm.md)

