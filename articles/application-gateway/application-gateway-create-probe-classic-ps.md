<properties
   pageTitle="Oprette en brugerdefineret efterprøvning af af til Application Gateway ved hjælp af PowerShell i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær at oprette en brugerdefineret efterprøvning af af til Application Gateway ved hjælp af PowerShell i modellen Klassisk installation"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Oprette en brugerdefineret efterprøvning af af til Azure Application Gateway (klassisk) ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Azure-portalen](application-gateway-create-probe-portal.md)
- [Azure ressourcestyring PowerShell](application-gateway-create-probe-ps.md)
- [Azure klassisk PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Sådan oprettes et program-gateway:

1. Oprette en programmet gateway ressource.
2. Oprette en XML-fil eller et konfigurationsobjekt.
3. Udfør konfigurationen nyoprettede programmet gateway ressourcen.

### <a name="create-an-application-gateway-resource"></a>Oprette en programmet gateway ressource

Brug Cmdletten **Ny AzureApplicationGateway** , erstatter værdierne med dine egne til at oprette gatewayen. Fakturering for gatewayen starter ikke på nuværende tidspunkt. Fakturering begynder på et senere tidspunkt, når gatewayen er blevet startet.

I følgende eksempel oprettes et program-gateway ved hjælp af et virtuelt netværk, der hedder "testvnet1" og et undernet, kaldet "undernet-1".

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Du kan bruge cmdlet'en **Get-AzureApplicationGateway** til at validere, gatewayen blev oprettet.

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Du kan vælge mellem lille, Medium og stor.

 *VirtualIPs* og *DnsName* vises som tomme fordi gatewayen ikke er startet endnu. Disse oprettes, når gatewayen er i tilstanden, der kører.

## <a name="configure-an-application-gateway"></a>Konfigurere et program-gateway

Du kan konfigurere gatewayen program ved hjælp af XML- eller et konfigurationsobjekt.

## <a name="configure-an-application-gateway-by-using-xml"></a>Konfigurere et program-gateway ved hjælp af XML

I eksemplet nedenfor kan du bruge en XML-fil til at konfigurere alle indstillinger for gateway og acceptere dem programmet gateway ressourcen.  

### <a name="step-1"></a>Trin 1

Kopier følgende tekst i Notesblok.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Rediger værdierne mellem parenteserne for elementerne, konfiguration. Gem filen med filtypenavnet .xml.

I følgende eksempel viser, hvordan du bruger en konfigurationsfil til at konfigurere programmet gatewayen, at indlæse balance HTTP-trafik på offentlige port 80 og sende netværkstrafik til back end-port 80 mellem to IP-adresser ved hjælp af en brugerdefineret efterprøvning af af.

>[AZURE.IMPORTANT] Elementet protocol Http eller Https er store og små bogstaver.

Et nyt konfigurationselement af \<forespørgsler\> føjes til at konfigurere brugerdefinerede sonder.

Konfigurationsparametrene er:

- **Navn** - referencenavn til brugerdefineret efterprøvning af af.
- **Protocol** - protokol, der bruges (mulige værdier er HTTP eller HTTPS).
- **Host** og **sti** - fulde URL-sti, som er gældende af gatewayen programmet på computeren til at bestemme tilstanden for forekomsten. Eksempelvis hvis du har et websted http://contoso.com/, kan derefter på brugerdefineret efterprøvning af af konfigureres til "http://contoso.com/path/custompath.htm" for efterprøvning af af Kontroller skal have en vellykket HTTP-svar.
- **Interval** - konfigurerer efterprøvning af af interval Kontroller i sekunder.
- **Timeout** – definerer efterprøvning af af timeout for en HTTP-svar check.
- **UnhealthyThreshold** - antallet af mislykkede HTTP-svar, der er behov for at markere den forekomst af back end-som *beskadiget*.

Efterprøvning af af navnet der refereres til i den <BackendHttpSettings> konfiguration til at tildele hvilken back end-puljen bruger brugerdefinerede efterprøvning af af indstillinger.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Tilføje en brugerdefineret efterprøvning af af konfiguration til et eksisterende program-gateway

Ændre den aktuelle konfiguration af et program-gateway kræver tre trin: få den aktuelle XML-konfigurationsfil, ændre for at få en brugerdefineret efterprøvning af af og konfigurere gatewayen program med de nye XML-indstillinger.

### <a name="step-1"></a>Trin 1

Få XML-fil ved hjælp af get-AzureApplicationGatewayConfig. Denne handling eksporterer konfigurationen XML ændres for at tilføje en efterprøvning af af indstilling.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Trin 2

Åbn XML-fil i et tekstredigeringsprogram. Tilføje en `<probe>` afsnit efter `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Tilføj efterprøvning af af navnet i afsnittet backendHttpSettings i XML-filen, som vist i følgende eksempel:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Gemme XML-filen.

### <a name="step-3"></a>Trin 3

Opdater gatewayen programkonfiguration med den nye XML-fil ved hjælp af **Sæt AzureApplicationGatewayConfig**. Dette opdaterer din gateway, programmet på computeren med den nye konfiguration.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Næste trin

Hvis du vil konfigurere Secure Sockets Layer (SSL) aflastning, skal du se [konfigurere et program-gateway til SSL offload](application-gateway-ssl.md).

Hvis du vil konfigurere et program-gateway til brug med en intern justering af belastning, skal du se [oprette et program-gateway med en intern justering af belastning (ILB)](application-gateway-ilb.md).
