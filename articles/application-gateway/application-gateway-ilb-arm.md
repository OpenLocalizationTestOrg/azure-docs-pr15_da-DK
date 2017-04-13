<properties
   pageTitle="Oprette og konfigurere et program-gateway med en intern justering af belastning (ILB) ved hjælp af Azure ressourcestyring | Microsoft Azure"
   description="Denne side indeholder en vejledning til at oprette, konfigurere, starte og slette en Azure-program gateway med interne justering af belastning (ILB) til Azure ressourcestyring"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Oprette et program-gateway med en intern justering af belastning (ILB) ved hjælp af Azure ressourcestyring

> [AZURE.SELECTOR]
- [Azure klassiske trin](application-gateway-ilb.md)
- [Ressourcestyring PowerShell trin](application-gateway-ilb-arm.md)

Azure Application Gateway kan konfigureres med en forbindelse til internettet VIP eller med et internt slutpunkt, der ikke vises på internettet, også kaldet interne Indlæs belastningsjusteringstjenesten (ILB) ark. Konfigurationen af gatewayen med en ILB er nyttig til interne line of business-applikationer, der ikke vises på internettet. Det er også nyttigt for tjenester og niveauer inden for et program med flere lag, der sidde i rammen sikkerhed, der ikke vises på internettet, men stadig kræver, at round robin-Indlæs fordeling, session klæbrighed eller opsigelse før Secure Sockets Layer (SSL).

I denne artikel fører dig gennem trinnene til at konfigurere et program-gateway med en ILB.

## <a name="before-you-begin"></a>Inden du går i gang

1. Installere den nyeste version af Azure PowerShell-cmdlet'er ved hjælp af Web Platform installationsprogrammet. Du kan hente og installere den nyeste version fra sektionen **Windows PowerShell** på [downloadsiden](https://azure.microsoft.com/downloads/).
2. Du opretter et virtuelt netværk og et undernet for Application Gateway. Sørg for, at ingen virtuelle maskiner eller skyen installationer bruger undernettet. Application Gateway være alene i et virtuelt netværksundernet.
3. Servere, der du konfigurere til at bruge programmet gatewayen skal findes, eller har tildelt deres slutpunkter, der er oprettet i det virtuelle netværk eller med en offentlige IP-/ VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Hvad er påkrævet for at oprette et program-gateway?


- **Back end-server programgruppen:** Listen over IP-adresserne på back-end-servere. De viste IP-adresser skal enten hører til det virtuelle netværk, men i et andet undernet af programmet gatewayen eller skal være en offentlige IP-adresse/VIP.
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-port:** Denne port er den offentlige port, der er åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back-end-servere.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https, er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload).
- **Regel:** Reglen binder lytteren og back-end-serveren puljen og definerer hvilken back end-server puljen trafikken skal sendes til, når det rammer en bestemt lytter. I øjeblikket, understøttes kun den *grundlæggende* regel. *Grundlæggende* reglen er round robin-indlæsning fordeling.



## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Forskellen mellem at bruge Azure klassisk og Azure ressourcestyring er den rækkefølge, hvori du oprette gatewayen programmet og de elementer, der skal konfigureres.
Med ressourcestyring alle elementer, som gør et program-gateway er konfigureret enkeltvis, og Placer derefter sammen for at oprette programmet gateway ressourcen.


Her er de trin, som er nødvendige for at oprette et program-gateway:

1. Oprette en ressourcegruppe til Ressourcestyring
2. Oprette et virtuelt netværk og et undernet til gatewayen program
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

Oprette en ny ressourcegruppe (Spring dette trin, hvis du bruger en eksisterende ressourcegruppe).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Det bruges som standardplacering til ressourcer i denne ressourcegruppe. Sørg for, at alle kommandoer til at oprette et program-gateway bruger samme ressourcegruppe.

I eksemplet ovenfor oprettede vi en ressourcegruppe med navnet "appgw-indbyggede rg" og en placering "Vest os".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Oprette et virtuelt netværk og et undernet til gatewayen program

I følgende eksempel viser, hvordan du opretter et virtuelt netværk ved hjælp af Ressourcestyring:

### <a name="step-1"></a>Trin 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Dette tildeler adresse område 10.0.0.0/24 til en undernet variabel skal bruges til at oprette et virtuelt netværk.

### <a name="step-2"></a>Trin 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Dette opretter et virtuelt netværk med navnet "appgwvnet" i ressource gruppe "appgw-indbyggede rg" til området Vest USA til brug af præfikset 10.0.0.0/16 med undernet 10.0.0.0/24.

### <a name="step-3"></a>Trin 3

    $subnet = $vnet.subnets[0]

Dette giver undernetobjektet variablen $subnet for de næste trin.

## <a name="create-an-application-gateway-configuration-object"></a>Oprette et program gateway konfigurationsobjekt

### <a name="step-1"></a>Trin 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Dette opretter en gateway IP-programkonfiguration med navnet "gatewayIP01". Når Application Gateway starter, henter en IP-adresse fra det undernet, der er konfigureret og omdirigere netværkstrafik til IP-adresser i back end-IP-puljen. Husk på, at hver forekomst tager én IP-adresse.


### <a name="step-2"></a>Trin 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Dette konfigurerer back end-IP-adressegruppe med navnet "pool01" med IP-adresser "134.170.185.46, 134.170.188.221,134.170.185.50". Dette er de IP-adresser, modtager den netværkstrafik, som kommer fra front end IP-slutpunktet. Du erstatte de ovenfor for at føje dine egne programmet IP-adresse slutpunkter IP-adresser.

### <a name="step-3"></a>Trin 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Dette konfigurerer programmet gateway indstillingen "poolsetting01" for afkrydsningsfeltet Indlæs afstemmes netværkstrafik i back end-puljen.

### <a name="step-4"></a>Trin 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Dette konfigurerer navnet "frontendport01" for ILB front end IP-port.

### <a name="step-5"></a>Trin 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Dette opretter front end IP-konfigurationen kaldet "fipconfig01" og knytter den til en privat IP fra det aktuelle virtuelle netværksundernet.

### <a name="step-6"></a>Trin 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Dette opretter lytteren kaldet "listener01" og knytter den front end-port til front end IP-konfigurationen.

### <a name="step-7"></a>Trin 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Dette opretter Indlæs belastningsjusteringstjenesten routing reglen kaldet "rule01", der konfigurerer Indlæs belastningsjusteringstjenesten funktionsmåden.

### <a name="step-8"></a>Trin 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Dette konfigurerer forekomst størrelsen af gatewayen programmet på computeren.

>[AZURE.NOTE]  Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Du kan vælge mellem Standard_Small, Standard_Medium og Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Oprette et program-gateway ved hjælp af ny AzureApplicationGateway

Opretter et program-gateway med alle elementer til konfiguration af fra ovenstående trin. I dette eksempel er gatewayen program kaldet "appgwtest".


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Det opretter et program-gateway med alle elementer til konfiguration af fra ovenstående trin. I eksemplet hedder gatewayen programmet "appgwtest".


## <a name="delete-an-application-gateway"></a>Slette et program-gateway

Hvis du vil slette et program-gateway, skal du gøre følgende i rækkefølge:

1. Brug cmdlet'en **Stop AzureRmApplicationGateway** til at stoppe gatewayen.
2. Brug cmdlet'en **Fjern AzureRmApplicationGateway** til at fjerne gatewayen.
3. Kontrollér, at gatewayen er blevet fjernet ved hjælp af cmdlet'en **Get-AzureApplicationGateway** til.


### <a name="step-1"></a>Trin 1

Få application gateway-objektet, og knytte den til en variabel "$getgw".

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Trin 2

Brug **Stop AzureRmApplicationGateway** til at stoppe gatewayen programmet på computeren. Dette eksempel viser Cmdletten **Stop AzureRmApplicationGateway** på den første linje, efterfulgt af output.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Når gatewayen programmet er i en standset tilstand, kan du bruge **Fjern AzureRmApplicationGateway** cmdlet til at fjerne tjenesten.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Den **-tvinge** parameter kan benyttes til at skjule bekræftelsesmeddelelsen Fjern.


For at bekræfte, at tjenesten er blevet fjernet, kan du bruge cmdlet'en **Get-AzureRmApplicationGateway** til. Dette trin er ikke påkrævet.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Næste trin

Hvis du vil konfigurere SSL aflastning, skal du se [konfigurere et program-gateway til SSL offload](application-gateway-ssl.md).

Hvis du vil konfigurere et program-gateway til brug med en ILB, skal du se [oprette et program-gateway med en intern justering af belastning (ILB)](application-gateway-ilb.md).

Hvis du vil finde flere oplysninger om indlæse indstillinger for justering af belastning Generelt, skal du se:

- [Azure justering af belastning](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
