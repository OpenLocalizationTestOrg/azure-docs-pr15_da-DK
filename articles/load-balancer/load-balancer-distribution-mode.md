<properties
   pageTitle="Konfigurere tilstanden for justering af belastning fordeling | Microsoft Azure"
   description="Sådan konfigureres Azure Indlæs belastningsjusteringstjenesten fordeling tilstand for at understøtte kilde IP-forbindelse"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>Konfigurere tilstanden for fordeling for justering af belastning

## <a name="hash-based-distribution-mode"></a>Hash-baseret fordeling tilstand

Algoritmen standard fordeling er en 5-tupel (kilde-IP, kildeport, destination IP-adresse, destinationsport, protokol type) hash tilknytte trafik til tilgængelige servere. Klæbrighed kan kun inden for en transportsession med. Pakker i den samme session, dirigeret til den samme datacenter IP-adresse (DIP) forekomst bag Indlæs afstemmes slutpunkt. Når klienten starter en ny session fra samme kilde-IP, kildeporten ændres og får trafikken at gå til et andet DIP slutpunkt.

![hash-baseret justering af belastning](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figur 1-5-tupel fordeling

## <a name="source-ip-affinity-mode"></a>Kilde-IP-forbindelse tilstand

Vi har en anden fordeling tilstand kaldet kilde IP-forbindelse (også kaldet session forbindelse eller klient IP-forbindelse). Azure justering af belastning kan være konfigureret til at bruge et 2-tupel (kilde-IP, IP-Destination) eller en 3-tupel (kilde-IP, Destination IP Protocol) til at knytte trafik til de tilgængelige servere. Ved hjælp af kilde-IP-forbindelse, forbindelser, der er startet fra samme klientcomputeren går til det samme DIP slutpunkt.

I følgende diagram vises en 2-tupel konfiguration. Bemærk, hvordan 2-tuplen kører gennem den justering af belastning til virtuelt 1 (VM1) som derefter sikkerhedskopieres ved VM2 og VM3.

![session forbindelse](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figur 2-2-tupel fordeling

Kilde-IP-forbindelsen mellem processorer løser inkompatibilitet mellem Azure justering af belastning og Remote Desktop (RD) Gateway. Nu kan du oprette en RD gateway-farm i en enkelt skybaseret tjeneste.

En anden brug-fald-scenario er medier Overfør hvor data overførslen sker via UDP, men kontrolelement planet opnås gennem TCP:

- En klient først starter en TCP-sessioner lov til at indlæse afstemmes offentlige adressen, bliver ført til et bestemt DIP denne kanal forbliver aktiv for at overvåge forbindelse tilstand
- En ny UDP-session fra den samme klientcomputeren startes til samme Indlæs afstemmes offentlige slutpunktet, forventningen her er, at denne forbindelse også ført til det samme DIP slutpunkt, som tidligere TCP-forbindelsen så medier overføre kan udføres på høj overførselshastighed også samtidig en kontrolelement kanal via TCP.

>[AZURE.NOTE] Når en netværksbelastningen sæt ændres er (fjerne eller tilføje en virtuel maskine) fordelingen af klient-anmodninger genberegnes. Du kan ikke afhænger af nye forbindelser fra eksisterende klienter, der slutter på den samme server. Desuden kan ved hjælp af kilde-IP forbindelse fordeling tilstand medføre en ulige fordelingen af trafikken. Klienter, der kører bag proxyer kan ses som et entydigt klientprogrammet.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Konfiguration af kilde-IP forbindelse indstillingerne for justering af belastning

Til virtuelle maskiner, kan du bruge PowerShell til at ændre indstillingerne for timeout:

Føje et Azure slutpunkt til en virtuel maskine og Indlæs belastningsjusteringstjenesten fordeling tilstand

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

LoadBalancerDistribution kan indstilles til sourceIP for 2-tupel (kilde-IP, IP-Destination) justering af belastning, sourceIPProtocol til justering af belastning 3-tupel (kilde-IP, Destination IP protocol) eller ingen eventuelt standardfunktionsmåden for 5 tupel belastning.

Brug følgende for at hente en slutpunkt Indlæs belastningsjusteringstjenesten fordeling tilstand konfiguration:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Hvis elementet LoadBalancerDistribution ikke findes bruger Azure justering af belastning algoritmen standard 5 tupel.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Angiv fordeling-tilstand på et Indlæs afstemmes slutpunkt, vises

Hvis slutpunkter er en del af en Indlæs afstemmes slutpunkt sæt, skal være angivet tilstanden fordeling på sættet Indlæs afstemmes slutpunkt:

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Tjenestekonfiguration for at ændre fordelingen tilstand i skyen

Hvis du vil opdatere din skybaseret tjeneste, kan du udnytte Azure SDK til .NET 2,5 (vil blive udgivet i November). Indstillinger for slutpunkt for Cloud Services er foretaget i .csdef. En opgradering af installationen er påkrævet for at opdatere Indlæs belastningsjusteringstjenesten fordeling tilstanden for en Cloud Services-installation.
Her er et eksempel på .csdef ændringer af slutpunkt indstillinger:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
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

## <a name="api-example"></a>Eksempel på API

Du kan konfigurere Indlæs belastningsjusteringstjenesten fordelingen ved hjælp af service management API. Sørg for at tilføje den `x-ms-version` sidehoved er indstillet til version `2014-09-01` eller nyere.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Opdatere konfigurationen af det angivne Indlæs afstemmes sæt i en installation

#### <a name="request-example"></a>Anmode om eksempel

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Værdien af LoadBalancerDistribution kan være sourceIP for 2 tupel forbindelse, sourceIPProtocol for 3-tupel forbindelse eller ingen (for forbindelsen mellem processorer. Det vil sige 5-tupel)

#### <a name="response"></a>Svar

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Næste trin

[Interne Indlæs belastningsjusteringstjenesten oversigt](load-balancer-internal-overview.md)

[Introduktion til konfiguration af en via justering af belastning internettet](load-balancer-get-started-internet-arm-ps.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
