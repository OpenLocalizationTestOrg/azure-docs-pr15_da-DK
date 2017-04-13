<properties
   pageTitle="Oprette, starte eller slette et program-gateway | Microsoft Azure"
   description="Denne side indeholder en vejledning til at oprette, konfigurere, starte og slette en gateway Azure-program"
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

# <a name="create-start-or-delete-an-application-gateway"></a>Oprette, starte eller slette et program-gateway

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-gateway-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-gateway-arm.md)
- [Azure klassisk PowerShell](application-gateway-create-gateway.md)
- [Azure ressourcestyring skabelon](application-gateway-create-gateway-arm-template.md)
- [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway er lag-7 belastningsjustering. Den indeholder failover, ydeevne-routing HTTP-anmodninger mellem forskellige servere, uanset om de er i skyen eller i det lokale miljø. Application Gateway indeholder mange programmet levering Controller (ADC) funktioner, herunder HTTP belastning, cookie-baserede session forbindelse, Secure Sockets Layer (SSL) offload, brugerdefinerede sundhed sonder, understøttelse af flere websted og mange andre. For at finde en komplet liste over understøttede funktioner skal du besøge [Programmet Gateway oversigt](application-gateway-introduction.md)

I denne artikel fører dig gennem trinnene til at oprette, konfigurere, starte og slette et program-gateway.

## <a name="before-you-begin"></a>Inden du går i gang

1. Installere den nyeste version af Azure PowerShell-cmdlet'er ved hjælp af Web Platform installationsprogrammet. Du kan hente og installere den nyeste version fra sektionen **Windows PowerShell** på [downloadsiden](https://azure.microsoft.com/downloads/).
2. Hvis du har en eksisterende virtuelt netværk og enten vælge et eksisterende tomme undernet eller oprette et nyt undernet i din eksisterende virtuelt netværk udelukkende til brug ved gatewayen programmet på computeren. Du kan ikke installere programmet gatewayen til et andet virtuelle netværk end de ressourcer, du vil installere bag gatewayen program, medmindre vnet peering bruges. Hvis du vil vide besøg mere [Vnet Peering](../virtual-network/virtual-network-peering-overview.md)
3. Kontrollér, at du har et virtuelt netværk med et gyldigt undernet. Sørg for, at ingen virtuelle maskiner eller skyen installationer bruger undernettet. Gatewayen programmet skal være alene i et virtuelt netværksundernet.
3. Servere, der du konfigurere til at bruge programmet gatewayen skal findes, eller har tildelt deres slutpunkter, der er oprettet i det virtuelle netværk eller med en offentlige IP-/ VIP.

## <a name="what-is-required-to-create-an-application-gateway"></a>Hvad er påkrævet for at oprette et program-gateway?

Når du bruger kommandoen **Ny AzureApplicationGateway** til at oprette gatewayen programmet, ingen konfiguration er angivet på dette tidspunkt og nyoprettede ressourcen er konfigureret til enten ved hjælp af XML- eller et konfigurationsobjekt.

Værdierne er:

- **Back end-server programgruppen:** Listen over IP-adresserne på back-end-servere. De viste IP-adresser skal enten tilhører det virtuelle netværksundernet eller skal være en offentlige IP-adresse/VIP.
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-port:** Denne port er den offentlige port, der er åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back-end-servere.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https disse værdier er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload).
- **Regel:** Reglen binder lytteren og back-end-serveren puljen og definerer hvilken back end-server puljen trafikken skal sendes til, når det rammer en bestemt lytter.

## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Sådan oprettes et program-gateway:

1. Oprette en programmet gateway ressource.
2. Oprette en XML-fil eller et konfigurationsobjekt.
3. Udfør konfigurationen nyoprettede programmet gateway ressourcen.

>[AZURE.NOTE] Hvis du vil konfigurere et brugerdefineret efterprøvning af af dit program gatewayen skal du se [oprette et program-gateway med brugerdefinerede sonder ved hjælp af PowerShell](application-gateway-create-probe-classic-ps.md). Se [brugerdefinerede sonder og sundhedsovervågning](application-gateway-probe-overview.md) kan finde flere oplysninger.

![Scenarieeksempel][scenario]

### <a name="create-an-application-gateway-resource"></a>Oprette en programmet gateway ressource

Brug Cmdletten **Ny AzureApplicationGateway** , erstatter værdierne med dine egne til at oprette gatewayen. Fakturering for gatewayen starter ikke på nuværende tidspunkt. Fakturering begynder på et senere tidspunkt, når gatewayen er blevet startet.

I følgende eksempel oprettes et program-gateway ved hjælp af et virtuelt netværk, der hedder "testvnet1" og et undernet, kaldet "undernet-1".


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Beskrivelse*, *InstanceCount*og *GatewaySize* er valgfrie parametre.

Du kan bruge cmdlet'en **Get-AzureApplicationGateway** til at validere, gatewayen blev oprettet.

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Du kan vælge mellem lille, Medium og stor.

*VirtualIPs* og *DnsName* vises som tomme fordi gatewayen ikke er startet endnu. Disse oprettes, når gatewayen er i tilstanden, der kører.

## <a name="configure-the-application-gateway"></a>Konfigurere gatewayen program

Du kan konfigurere gatewayen program ved hjælp af XML- eller et konfigurationsobjekt.

## <a name="configure-the-application-gateway-by-using-xml"></a>Konfigurere gatewayen program ved hjælp af XML

I eksemplet nedenfor kan du bruge en XML-fil til at konfigurere alle indstillinger for gateway og acceptere dem programmet gateway ressourcen.  

### <a name="step-1"></a>Trin 1  

Kopier følgende tekst i Notesblok.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Rediger værdierne mellem parenteserne for elementerne, konfiguration. Gem filen med filtypenavnet .xml.

>[AZURE.IMPORTANT] Elementet protocol Http eller Https er store og små bogstaver.

I følgende eksempel viser, hvordan du bruger en konfigurationsfil til at konfigurere gatewayen programmet på computeren. Eksempel Indlæs afbalancerer HTTP-trafik på offentlige port 80 og sender netværkstrafik til back end-port 80 mellem to IP-adresser.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Trin 2

Næste, Angiv gatewayen programmet på computeren. Brug cmdlet'en **Set-AzureApplicationGatewayConfig** med en XML-konfigurationsfil.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Konfigurere gatewayen program ved hjælp af et konfigurationsobjekt

I følgende eksempel viser, hvordan du konfigurerer gatewayen program ved hjælp af konfigurationsobjekter. Elementer til konfiguration af alle skal være konfigureret individuelt og lægges derefter til et program gateway konfigurationsobjekt. Når du har oprettet objektet configuration, kan du bruge kommandoen **Sæt AzureApplicationGateway** for at udføre konfigurationen tidligere oprettet programmet gateway ressourcen.

>[AZURE.NOTE] Før du tildeler en værdi for alle objekter, konfiguration, skal du angive, hvilken type objekt PowerShell bruger til lagring. Den første linje for at oprette de individuelle elementer definerer hvad Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (objektnavn) bruges.

### <a name="step-1"></a>Trin 1

Oprette elementer til individuelle konfiguration af alle.

Oprette den front end IP-adresse, som vist i følgende eksempel.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Oprette den front end-port, som vist i følgende eksempel.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Oprette server back end-puljen.

 Definer de IP-adresser, der er føjet til puljen back end-server som vist i det næste eksempel.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Bruge objektet $server til at addere værdierne til back end-puljen objektet ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Oprette serverindstillingen back end-puljen.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Oprette lytteren.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Oprette reglen.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Trin 2

Tildele alle elementer til individuelle konfiguration til et program gateway konfigurationsobjekt ($appgwconfig).

Føje den front end IP-adresse til konfigurationen.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Tilføj den front end-port af konfigurationen.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Tilføj server back end-puljen af konfigurationen.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Tilføj indstillingen back end-puljen af konfigurationen.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Føje lytteren til konfigurationen.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Føje reglen til konfigurationen.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Trin 3

Udfør objektet configuration programmet gateway ressourcen ved hjælp af **Sæt AzureApplicationGatewayConfig**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Starte gateway

Når gatewayen er blevet konfigureret, kan du bruge **Start AzureApplicationGateway** cmdlet til at starte gatewayen. Fakturering for et program-gateway begynder efter gatewayen er blevet startet.

> [AZURE.NOTE] **Start AzureApplicationGateway** cmdlet kan tage op til 15-20 minutter at afslutte.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Kontrollere gatewaystatus

Brug cmdlet'en **Get-AzureApplicationGateway** til at kontrollere status for gatewayen. Hvis **Start-AzureApplicationGateway** lykkedes i det forrige trin, skal køre *tilstand* og *Vip* og *DnsName* skal have gyldige indtastninger.

I følgende eksempel vises et program-gateway, der er op, køre, og klar til at tage trafik tiltænkt `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Slette et program-gateway

Sådan sletter du et program-gateway:

1. Brug cmdlet'en **Stop AzureApplicationGateway** til at stoppe gatewayen.
2. Brug cmdlet'en **Fjern AzureApplicationGateway** til at fjerne gatewayen.
3. Kontrollér, at gatewayen er blevet fjernet ved hjælp af cmdlet'en **Get-AzureApplicationGateway** til.

I følgende eksempel viser Cmdletten **Stop AzureApplicationGateway** på den første linje, efterfulgt af output.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Når gatewayen programmet er i en standset tilstand, kan du bruge **Fjern AzureApplicationGateway** cmdlet til at fjerne tjenesten.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

For at bekræfte, at tjenesten er blevet fjernet, kan du bruge cmdlet'en **Get-AzureApplicationGateway** til. Dette trin er ikke påkrævet.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Næste trin

Hvis du vil konfigurere SSL aflastning, skal du se [konfigurere et program-gateway til SSL offload](application-gateway-ssl.md).

Hvis du vil konfigurere et program-gateway til brug med en intern justering af belastning, skal du se [oprette et program-gateway med en intern justering af belastning (ILB)](application-gateway-ilb.md).

Hvis du vil finde flere oplysninger om indlæse indstillinger for justering af belastning Generelt, skal du se:

- [Azure justering af belastning](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png