<properties 
   pageTitle="Oprette og konfigurere et program-Gateway med interne justering af belastning (ILB) i et virtuelt netværk | Microsoft Azure"
   description="Denne side indeholder en vejledning til at konfigurere en Azure Application Gateway med interne Indlæs afstemmes ark"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Oprette et program-Gateway med en intern justering af belastning (ILB)

> [AZURE.SELECTOR]
- [Azure klassiske trin](application-gateway-ilb.md)
- [Ressourcestyring Powershell trin](application-gateway-ilb-arm.md)

Application Gateway kan konfigureres med en via virtuelle IP internettet eller med et internt slutpunkt ikke være tilgængelig på internettet, også kaldet interne Indlæs belastningsjusteringstjenesten (ILB) slutpunkt. Konfigurationen af gatewayen med en ILB er nyttig til interne line of business-applikationer ikke være tilgængelig på internettet. Det er også nyttig til tjenester/niveauer inden for et program, med flere lag, som er placeret i rammen sikkerhed ikke være tilgængelig internettet, men stadig kræver round robin Indlæs fordeling, session klæbrighed eller opsigelse før SSL. I denne artikel fører dig gennem trinnene til at konfigurere et program-gateway med en ILB.

## <a name="before-you-begin"></a>Inden du går i gang

1. Installere nyeste version af Azure PowerShell-cmdletter ved hjælp af Web Platform installationsprogrammet. Du kan hente og installere den nyeste version fra sektionen **Windows PowerShell** på [overførselssiden](https://azure.microsoft.com/downloads/).
2. Kontrollér, at du har et virtuelt netværk med gyldige undernet.
3. Kontrollér, at du har back end-servere i det virtuelle netværk eller med en offentlige IP-/ VIP tildelt.


Hvis du vil oprette et program-gateway, skal du udføre følgende trin i den viste rækkefølge. 

1. [Oprette et program-gateway](#create-a-new-application-gateway)
2. [Konfigurere gatewayen](#configure-the-gateway)
3. [Konfiguration gateway](#set-the-gateway-configuration)
4. [Starte gateway](#start-the-gateway)
4. [Bekræfte gatewayen](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Oprette et program-gateway:

**Til at oprette gatewayen**, Brug den `New-AzureApplicationGateway` cmdlet, erstatter værdierne med dine egne. Bemærk, at fakturering for gatewayen ikke starter på dette tidspunkt. Fakturering begynder på et senere tidspunkt, når gatewayen er blevet startet.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Til at validere** , gatewayen blev oprettet, kan du bruge den `Get-AzureApplicationGateway` cmdlet. 

Eksempel på, *beskrivelsen*, *InstanceCount*og *GatewaySize* er valgfrie parametre. Standardværdien for *InstanceCount* er 2 med den største værdi af 10. Standardværdien for *GatewaySize* er mellem. Små og store er andre tilgængelige værdier. *VIP* og *DnsName* vises som tomme fordi gatewayen ikke er startet endnu. Disse oprettes, når gatewayen er i tilstanden, der kører. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Konfigurere gatewayen

En gateway programkonfiguration består af flere værdier. Værdierne, der kan være bundet sammen for at opbygge konfigurationen.
 
Værdierne er:

- **Back end-server programgruppen:** Listen over IP-adresserne på back end-serverne. De viste IP-adresser skal enten tilhører VNet undernet, eller skal være en offentlige IP-adresse/VIP. 
- **Back end-puljen serverindstillinger:** Hver puljen har indstillinger som port, protocol og cookie-baseret forbindelse. Disse indstillinger er knyttet til en gruppe og er anvendt på alle servere i gruppen.
- **Front end-Port:** Denne port er den offentlige port åbnet på gatewayen programmet på computeren. Trafik rammer denne port, og klik derefter viderestilles til en af back end-serverne.
- **Lytteren:** Lytteren har en front end-port, en protokol (Http eller Https, er store og små bogstaver), og SSL-certifikatnavnet (hvis konfiguration af SSL offload). 
- **Regel:** Reglen binder lytteren og back end-server puljen og definerer hvilken back end-server-programgruppen trafikken skal sendes til, når det rammer en bestemt lytter. I øjeblikket, understøttes kun den *grundlæggende* regel. *Grundlæggende* reglen er round robin-indlæsning fordeling.

Du kan opbygge din konfiguration, enten ved at oprette et konfigurationsobjekt eller ved hjælp af en XML-konfigurationsfil. Hvis du vil oprette din konfiguration ved hjælp af en XML-konfigurationsfil, kan bruge eksemplet nedenfor.



Bemærk følgende:


- Elementet *FrontendIPConfigurations* beskriver de relevante til konfiguration af Application Gateway med en ILB ILB oplysninger. 

- Frontend IP- *Type* skal du vælge "Privat"

- *StaticIPAddress* skal du vælge den ønskede interne IP som gatewayen modtager trafik. Bemærk, at elementet *StaticIPAddress* valgfrit. Hvis du ikke sæt, en tilgængelige interne IP fra det udløst undernet er valgt. 

- Værdien af elementet *navn* , der er angivet i *FrontendIPConfiguration* skal bruges til at referere til FrontendIPConfiguration i den HTTPListener *FrontendIP* element.

 **Eksempel på XML-konfigurationsfil**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Konfiguration gateway

Derefter skal du konfigurere gatewayen programmet på computeren. Du kan bruge den `Set-AzureApplicationGatewayConfig` cmdlet med et konfigurationsobjekt eller med en XML-konfigurationsfil. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Starte gateway

Når gatewayen er blevet konfigureret, kan du bruge den `Start-AzureApplicationGateway` til at starte gatewayen. Fakturering for et program-gateway begynder efter gatewayen er blevet startet. 


> [AZURE.NOTE] Den `Start-AzureApplicationGateway` cmdlet kan tage op til 15-20 minutter at gennemføre. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Kontrollere gatewaystatus

Brug den `Get-AzureApplicationGateway` til at kontrollere status for gateway. Hvis *Start-AzureApplicationGateway* lykkedes i det forrige trin, tilstanden skal være *kører*og Vip og DnsName bør have gyldige indtastninger. Dette eksempel viser cmdlet på den første linje, efterfulgt af output. I dette eksempel gateway kører, og er klar til at tage trafik. 

> [AZURE.NOTE] Gatewayen programmet på computeren er konfigureret til at acceptere trafik på 10.0.0.10 i dette eksempel konfigurerede ILB slutpunkt.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Næste trin


Hvis du vil finde flere oplysninger om indlæse indstillinger for justering af belastning Generelt, skal du se:

- [Azure justering af belastning](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure trafik Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)
