<properties
   pageTitle="Oprette en intern justering af belastning til skytjenester i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær at oprette en intern justering af belastning ved hjælp af PowerShell i modellen Klassisk installation"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Få en introduktion til en intern justering af belastning (klassisk) til skytjenester

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Konfigurere interne justering af belastning til skytjenester

Interne justering af belastning understøttes til både virtuelle maskiner og skytjenester. Et internt Indlæs belastningsjusteringstjenesten slutpunkt, der er oprettet i en skybaseret tjeneste, som er uden for et internationale virtuelt netværk vil være tilgængelige kun inden for skytjenesten.

Interne Indlæs belastningsjusteringstjenesten konfigurationen indeholder lagres under oprettelse af den første installation i skybaseret tjeneste, som vist i eksemplet nedenfor.

>[AZURE.IMPORTANT] En betingelse til at køre nedenstående trin er at have et virtuelt netværk, der allerede har oprettet til skyen installation. Du skal bruge den virtuelle netværksnavn og undernet navn for at oprette den interne belastning.

### <a name="step-1"></a>Trin 1

Åbn service konfigurationsfil (.cscfg) til skyen installationen i Visual Studio og tilføje følgende afsnit for at oprette den interne belastning under sidst "`</Role>`" element til netværkskonfigurationen.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Lad os tilføje værdierne for konfigurationsfil netværk til at vise, hvordan det ser ud. I eksemplet forudsætter du har oprettet et undernet, kaldet "test_vnet" med et undernet 10.0.0.0/24 kaldet test_subnet og en statisk IP-10.0.0.4. Justering af belastning navngives testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Du kan finde flere oplysninger om indlæsning belastningsjusteringstjenesten skema, [Tilføj justering af belastning](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Trin 2


Ændre definition (.csdef) servicefil for at føje slutpunkter til den interne belastning. Det tidspunkt, hvor der oprettes en forekomst af rolle, føjer definitionsfil service rolle forekomster til den interne belastning.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Følge de samme værdier fra eksemplet ovenfor, Lad os tilføje værdierne i filen service definition.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

Netværkstrafikken bliver Indlæs afstemmes med den testLB justering af belastning ved hjælp af port 80 til indgående anmodninger, sende til arbejder rolle forekomster også på port 80.


## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstanden for ved hjælp af kilde IP-forbindelse](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)