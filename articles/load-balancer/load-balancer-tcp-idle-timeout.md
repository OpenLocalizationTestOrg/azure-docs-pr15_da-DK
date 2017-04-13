<properties
   pageTitle="Konfigurere Indlæs belastningsjusteringstjenesten TCP inaktiv timeout | Microsoft Azure"
   description="Konfigurere Indlæs belastningsjusteringstjenesten TCP inaktiv timeout"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurere TCP timeout for inaktiv indstillinger for Azure justering af belastning

I standardkonfigurationen har Azure justering af belastning en inaktiv timeoutindstilling af 4 minutter. Hvis et tidsrum uden aktivitet er længere end timeoutværdien, er der ingen garanti, som TCP- eller HTTP-sessionen vedligeholdes mellem klienten og skybaseret tjeneste.

Når forbindelsen er lukket, klientprogrammet muligvis følgende fejlmeddelelse: "underliggende forbindelsen blev afbrudt: en forbindelse, der blev forventet at være aktiv blev lukket af serveren."

En almindelig praksis er at bruge en heller TCP. Denne øvelse holder forbindelsen aktiv i en længere periode. Se disse [.NET eksempler](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)kan finde flere oplysninger. Med heller aktiveret, -pakker, der sendes i perioder uden aktivitet på forbindelsen. Disse heller pakker sikre, at inaktive timeoutværdien nås aldrig og vedligeholdes forbindelsen til en længere periode.

Denne indstilling virker for indgående forbindelser. For at undgå at miste forbindelsen, skal du konfigurere TCP heller med et interval, der er mindre end indstillingen inaktiv timeout eller øge inaktiv timeoutværdien. Vi har tilføjet understøttelse af en konfigurerbar inaktiv timeout for at understøtte disse scenarier. Du kan nu angive den til en varighed på 4 til 30 minutter.

TCP heller fungerer godt til scenarier, hvor levetid ikke er en begrænsning. Det anbefales ikke for mobilprogrammer. Ved hjælp af en TCP heller i et mobile computeren kan batteriet enhed hurtigere.

![TCP-timeout](./media/load-balancer-tcp-idle-timeout/image1.png)

I nedenstående afsnit beskrives, hvordan du kan ændre indstillingerne for timeout for inaktiv i virtuelle computere og tjenester i skyen.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Konfigurere TCP-timeout for din forekomst niveau offentlige IP-adresse til 15 minutter

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes`er valgfrit. Hvis det ikke er angivet, er standardtimeouten 4 minutter. Området acceptable timeout er 4 til 30 minutter.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Angiv inaktiv timeout, når du opretter et Azure slutpunkt på en virtuel maskine

Hvis du vil ændre indstillingen Timeout for et slutpunkt, skal du bruge følgende:

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

For at hente din konfiguration af inaktiv timeout, skal du bruge følgende kommando:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Angive TCP-timeout på et netværksbelastningen slutpunkt, vises

Hvis slutpunkter er en del af en Indlæs afstemmes slutpunkt sæt, skal være angivet TCP-timeout på sættet Indlæs afstemmes slutpunkt. Eksempel:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## <a name="change-timeout-settings-for-cloud-services"></a>Ændre indstillingerne for timeout til skytjenester

Du kan bruge Azure SDK opdatere skybaseret tjeneste. Du kan gøre slutpunkt indstillinger for skytjenester i filen .csdef. Opdatering af TCP-timeout for installation af en skybaseret tjeneste kræver en opgradering af installationen. En undtagelse er, hvis der er angivet TCP-timeout kun til en offentlig IP-Adresser. Offentlige IP-indstillinger er i filen .cscfg, og du kan opdatere dem via installation update og opgraderingen.

.Csdef ændringer til slutpunkt indstillinger er:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

.Cscfg ændringer for indstillingen timeout på offentlige IP-adresser er:

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
        <PublicIPs>
          <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
        </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="rest-api-example"></a>Eksempel på REST-API

Du kan konfigurere TCP-inaktiv timeout ved hjælp af service management API. Sørg for, at den `x-ms-version` sidehoved er indstillet til version `2014-06-01` eller nyere. Opdater konfigurationen af de angivne netværksbelastningen input slutpunkterne på alle virtuelle maskiner i en installation.

### <a name="request"></a>Anmode om

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Svar

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

## <a name="next-steps"></a>Næste trin

[Interne Indlæs belastningsjusteringstjenesten oversigt](load-balancer-internal-overview.md)

[Introduktion til konfiguration af en forbindelse til internettet justering af belastning](load-balancer-get-started-internet-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)
