<properties
   pageTitle="Konfigurere et program-gateway for SSL aflastning ved hjælp af klassisk installation | Microsoft Azure"
   description="Denne artikel indeholder instruktioner til at oprette et program-gateway med SSL offload ved hjælp af Azure klassisk implementeringsmodel."
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Konfigurere et program-gateway for SSL aflastning ved hjælp af den klassiske implementeringsmodel

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure ressourcestyring PowerShell](application-gateway-ssl-arm.md)
-[Azure klassisk PowerShell](application-gateway-ssl.md)

Azure Application Gateway kan konfigureres til at afslutte sessionen Secure Sockets Layer (SSL) på din gateway for at undgå dyrt SSL dekryptering opgaver for at opstå på webfarmen. SSL aflastning forenkler også front end-server-konfiguration og administration af webprogrammet.

## <a name="before-you-begin"></a>Inden du går i gang

1. Installere den nyeste version af Azure PowerShell-cmdlet'er ved hjælp af Web Platform installationsprogrammet. Du kan hente og installere den nyeste version fra sektionen **Windows PowerShell** på [downloadsiden](https://azure.microsoft.com/downloads/).
2. Kontrollér, at du har et virtuelt netværk med et gyldigt undernet. Sørg for, at ingen virtuelle maskiner eller skyen installationer bruger undernettet. Gatewayen programmet skal være alene i et virtuelt netværksundernet.
3. Servere, der du konfigurere til at bruge programmet gatewayen skal findes, eller har tildelt deres slutpunkter, der er oprettet i det virtuelle netværk eller med en offentlige IP-/ VIP.

For at konfigurere SSL aflastning på et program-gateway skal du udføre følgende trin i den viste rækkefølge:

1. [Oprette et program-gateway](#create-an-application-gateway)
2. [Overføre SSL-certifikater](#upload-ssl-certificates)
3. [Konfigurere gatewayen](#configure-the-gateway)
4. [Konfiguration gateway](#set-the-gateway-configuration)
5. [Starte gateway](#start-the-gateway)
6. [Kontrollere gatewaystatus](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>Oprette et program-gateway

Brug Cmdletten **Ny AzureApplicationGateway** , erstatter værdierne med dine egne til at oprette gatewayen. Fakturering for gatewayen starter ikke på nuværende tidspunkt. Fakturering begynder på et senere tidspunkt, når gatewayen er blevet startet.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Du kan bruge cmdlet'en **Get-AzureApplicationGateway** til at validere, gatewayen blev oprettet.

Eksempel på, *beskrivelsen*, *InstanceCount*og *GatewaySize* er valgfrie parametre. Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Små og store er andre tilgængelige værdier. *VirtualIPs* og *DnsName* vises som tomme fordi gatewayen ikke er startet endnu. Disse værdier oprettes, når gatewayen er i tilstanden, der kører.

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>Overføre SSL-certifikater

Bruge **Tilføj AzureApplicationGatewaySslCertificate** til at overføre servercertifikat i *pfx* format til gatewayen programmet på computeren. Navnet på certifikatet er et bruger-valgte navn og skal være entydig i programmet gatewayen. Dette certifikat kaldes med dette navn i alle certifikat management handlinger på gatewayen programmet på computeren.

Det følgende eksempel viser Cmdletten, erstatte værdier i stikprøven med dine egne.

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

Dernæst skal validere certifikat overførslen. Brug cmdlet'en **Get-AzureApplicationGatewayCertificate** .

Dette eksempel viser cmdlet på den første linje, efterfulgt af output.

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] Adgangskoden certifikat skal være mellem 4 til 12 tegn, bogstaver eller tal. Specialtegn accepteres ikke.

## <a name="configure-the-gateway"></a>Konfigurere gatewayen

En gateway programkonfiguration består af flere værdier. Værdierne, der kan være bundet sammen for at opbygge konfigurationen.

Værdierne er:

- **Back end-server programgruppen:** Listen over IP-adresserne på back-end-servere. De viste IP-adresser skal enten tilhører det virtuelle netværksundernet eller skal være en offentlige IP-adresse/VIP.
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-port:** Denne port er den offentlige port, der er åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back-end-servere.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https disse værdier er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload).
- **Regel:** Reglen binder lytteren og back-end-serveren puljen og definerer hvilken back end-server puljen trafikken skal sendes til, når det rammer en bestemt lytter. I øjeblikket, understøttes kun den *grundlæggende* regel. *Grundlæggende* reglen er round robin-indlæsning fordeling.

**Yderligere konfiguration noter**

Konfiguration af SSL-certifikater, opnår protocol i **HttpListener** ændre til *Https* (store og små bogstaver). Elementet **SslCert** føjes til **HttpListener** med den værdi, der er angivet til det samme navn som anvendt i at overføre forud SSL certifikater sektionen. Front end-port skal opdateres til 443.

**Aktivere cookie-baseret forbindelse**: et program-gateway kan konfigureres for at sikre, at en anmodning om fra en klientsession altid bedt om at den samme VM i webfarmen. Dette scenario er udført af indsættelse af en sessionscookie, der gør det muligt for gateway til at dirigere trafik korrekt. For at aktivere cookie-baseret forbindelse skal du angive **CookieBasedAffinity** til *aktiveret* i elementet **BackendHttpSettings** .



Du kan opbygge din konfiguration, enten ved at oprette et konfigurationsobjekt eller ved hjælp af en XML-konfigurationsfil.
Hvis du vil oprette din konfiguration ved hjælp af en XML-fil, du bruge følgende eksempel:

**Eksempel på XML-konfigurationsfil**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
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
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
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


## <a name="set-the-gateway-configuration"></a>Konfiguration gateway

Derefter skal angive du gatewayen programmet på computeren. Du kan bruge cmdlet'en **Set-AzureApplicationGatewayConfig** til med enten en konfigurationsobjekt eller med en XML-konfigurationsfil.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>Starte gateway

Når gatewayen er blevet konfigureret, kan du bruge **Start AzureApplicationGateway** cmdlet til at starte gatewayen. Fakturering for et program-gateway begynder efter gatewayen er blevet startet.


**Note:** **Start AzureApplicationGateway** cmdlet kan tage op til 15-20 minutter at afslutte.

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>Kontrollere gatewaystatus

Brug cmdlet'en **Get-AzureApplicationGateway** til at kontrollere status for gatewayen. Hvis **Start-AzureApplicationGateway** lykkedes i det forrige trin, skal køre *tilstand* og *VirtualIPs* og *DnsName* skal have gyldige indtastninger.

I dette eksempel vises et program-gateway, der er oprettet, kører, og er klar til at tage trafik.

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>Næste trin


Hvis du vil finde flere oplysninger om indlæse indstillinger for justering af belastning Generelt, skal du se:

- [Azure justering af belastning](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)