<properties
   pageTitle="Oprette, starte eller slette et program-gateway ved hjælp af Azure ressourcestyring | Microsoft Azure"
   description="Denne side indeholder en vejledning til at oprette, konfigurere, starte og slette en gateway Azure-program ved hjælp af Azure ressourcestyring"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Oprette, starte eller slette et program-gateway ved hjælp af Azure ressourcestyring

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-gateway-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-gateway-arm.md)
- [Azure klassisk PowerShell](application-gateway-create-gateway.md)
- [Azure ressourcestyring skabelon](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway er lag-7 belastningsjustering. Den indeholder failover, ydeevne-routing HTTP-anmodninger mellem forskellige servere, uanset om de er i skyen eller i det lokale miljø. Application Gateway indeholder mange programmet levering Controller (ADC) funktioner, herunder HTTP belastning, cookie-baserede session forbindelse, Secure Sockets Layer (SSL) offload, brugerdefinerede sundhed sonder, understøttelse af flere websted og mange andre. For at finde en komplet liste over understøttede funktioner skal du besøge [Programmet Gateway oversigt](application-gateway-introduction.md)

I denne artikel fører dig gennem trinnene til at oprette, konfigurere, starte og slette et program-gateway.

>[AZURE.IMPORTANT] Før du arbejder med Azure ressourcer, er det vigtigt at forstå, Azure aktuelt har to installation modeller: ressourcestyring og klassisk. Sørg for, at du forstår [installation modeller og værktøjer,](../azure-classic-rm.md) før du arbejder med en hvilken som helst Azure ressource. Du kan se i dokumentationen til forskellige værktøjer ved at klikke på fanerne øverst i denne artikel. Dette dokument omhandler oprettelse af et program-gateway ved hjælp af Azure ressourcestyring. For at bruge den klassiske version skal du gå til [oprette en programmet gateway klassisk installation ved hjælp af PowerShell](application-gateway-create-gateway.md).


## <a name="before-you-begin"></a>Inden du går i gang

1. Installere den nyeste version af Azure PowerShell-cmdlet'er ved hjælp af Web Platform installationsprogrammet. Du kan hente og installere den nyeste version fra sektionen **Windows PowerShell** på [downloadsiden](https://azure.microsoft.com/downloads/).
2. Hvis du har en eksisterende virtuelt netværk og enten vælge et eksisterende tomme undernet eller oprette et undernet i din eksisterende virtuelt netværk udelukkende til brug af gatewayen programmet. Du kan ikke installere programmet gatewayen til et andet virtuelle netværk end de ressourcer, du vil installere bag gatewayen programmet på computeren.
3. Servere, der du konfigurere til at bruge programmet gatewayen skal findes, eller har tildelt deres slutpunkter, der er oprettet i det virtuelle netværk eller med en offentlige IP-/ VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Hvad er påkrævet for at oprette et program-gateway?

- **Back end-server programgruppen:** Listen over IP-adresserne på back-end-servere. De viste IP-adresser skal enten tilhører det virtuelle netværksundernet eller skal være en offentlige IP-adresse/VIP.
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-port:** Denne port er den offentlige port, der er åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back-end-servere.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https disse værdier er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload).
- **Regel:** Reglen binder lytteren, puljen back end-server og definerer hvilken back end-server puljen trafikken skal sendes til, når det rammer en bestemt lytter.

## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Forskellen mellem at bruge Azure klassisk og Azure ressourcestyring er den rækkefølge, hvori du oprette gatewayen programmet og de elementer, der skal konfigureres.

Med ressourcestyring alle elementer, som gør et program-gateway er konfigureret enkeltvis, og angiv derefter sammen for at oprette programmet gateway ressourcen.

Følgende er de trin, der skal bruges til at oprette et program-gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Oprette en ressourcegruppe til Ressourcestyring

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

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Trin 4

Oprette en ressourcegruppe (Spring dette trin, hvis du bruger en eksisterende ressourcegruppe).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Denne placering bruges som standardplacering til ressourcer i denne ressourcegruppe. Sørg for, at alle kommandoer til at oprette et program-gateway bruger samme ressourcegruppe.

I eksemplet ovenfor oprettede vi en ressourcegruppe med navnet "appgw-indbyggede RG" og en placering "Vest os".

>[AZURE.NOTE] Hvis du vil konfigurere et brugerdefineret efterprøvning af af dit program gatewayen skal du se [oprette et program-gateway med brugerdefinerede sonder ved hjælp af PowerShell](application-gateway-create-probe-ps.md). Se [brugerdefinerede sonder og sundhedsovervågning](application-gateway-probe-overview.md) kan finde flere oplysninger.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Oprette et virtuelt netværk og et undernet til gatewayen program

I følgende eksempel viser, hvordan du opretter et virtuelt netværk ved hjælp af Ressourcestyring.

### <a name="step-1"></a>Trin 1

Tildele adresse område 10.0.0.0/24 til variablen undernet skal bruges til at oprette et virtuelt netværk.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Trin 2

Oprette et virtuelt netværk med navnet "appgwvnet" i ressource gruppe "appgw-indbyggede rg" til et Vest USA-område, der bruger præfiks 10.0.0.0/16 med undernet 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>Trin 3

Tildele en undernet variabel for de næste trin, som opretter et program-gateway.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Oprette en offentlig IP-adresse til front end-konfiguration

Opret en offentlig IP-ressource "publicIP01" i ressource gruppe "appgw-indbyggede rg" for området, vest USA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>Oprette et program gateway konfigurationsobjekt

Du skal konfigurere elementer til konfiguration af alle før du opretter gatewayen programmet på computeren. Følgende trin Opret de konfigurationselementer, der skal bruges for et program gateway ressource.

### <a name="step-1"></a>Trin 1

Oprette en gateway IP-programkonfiguration med navnet "gatewayIP01". Når Application Gateway starter, henter en IP-adresse fra det undernet, der er konfigureret og dirigerer netværkstrafik til IP-adresser i back end-IP-puljen. Husk på, at hver forekomst tager én IP-adresse.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>Trin 2

Konfigurere back end-IP-adressegruppe med navnet "pool01" med IP-adresser "134.170.185.46, 134.170.188.221,134.170.185.50". Disse IP-adresser er de IP-adresser, modtager den netværkstrafik, som kommer fra front end IP-slutpunktet. Du erstatte de foregående IP-adresser for at tilføje dine egne slutpunkter for programmet IP-adresse.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>Trin 3

Konfigurere programmet gateway indstillingen "poolsetting01" til netværkstrafik netværksbelastningen i back end-puljen.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Trin 4

Konfigurere den front end IP-port med navnet "frontendport01" til den offentlige IP-slutpunkt.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>Trin 5

Opret den front end IP-konfiguration med navnet "fipconfig01" og knytte den offentlige IP-adresse til front end IP-konfigurationen.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>Trin 6

Oprette lytteren navnet "listener01" og knytte den front end-port til front end IP-konfigurationen.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>Trin 7

Oprette Indlæs belastningsjusteringstjenesten routing reglen med navnet "rule01", der konfigurerer Indlæs belastningsjusteringstjenesten funktionsmåden.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>Trin 8

Konfigurere forekomst størrelsen af gatewayen programmet på computeren.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Du kan vælge mellem Standard_Small, Standard_Medium og Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Oprette et program-gateway ved hjælp af ny AzureRmApplicationGateway

Oprette et program-gateway med alle elementer fra de foregående trin til konfiguration. I dette eksempel er gatewayen program kaldet "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>Trin 9

Hente oplysninger om DNS og VIP af gatewayen programmet fra den offentlige IP-ressource, der er knyttet til gatewayen programmet på computeren.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>Slette et program-gateway

Hvis du vil slette et program-gateway, skal du følge disse trin:

### <a name="step-1"></a>Trin 1

Få application gateway-objektet, og knytte den til en variabel "$getgw".

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Trin 2

Brug **Stop AzureRmApplicationGateway** til at stoppe gatewayen programmet på computeren.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

Når gatewayen programmet er i en standset tilstand, kan du bruge **Fjern AzureRmApplicationGateway** cmdlet til at fjerne tjenesten.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] Den **-tvinge** parameter kan benyttes til at skjule bekræftelsesmeddelelsen Fjern.

For at bekræfte, at tjenesten er blevet fjernet, kan du bruge cmdlet'en **Get-AzureRmApplicationGateway** til. Dette trin er ikke påkrævet.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

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

Hvis du vil konfigurere SSL aflastning, skal du se [konfigurere et program-gateway til SSL offload](application-gateway-ssl.md).

Hvis du vil konfigurere et program-gateway til brug med en intern justering af belastning, skal du se [oprette et program-gateway med en intern justering af belastning (ILB)](application-gateway-ilb.md).

Hvis du vil finde flere oplysninger om indlæse indstillinger for justering af belastning Generelt, skal du se:

- [Azure justering af belastning](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
