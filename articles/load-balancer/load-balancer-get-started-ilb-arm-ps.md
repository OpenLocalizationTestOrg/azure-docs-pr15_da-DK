<properties
   pageTitle="Oprette en intern justering af belastning ved hjælp af PowerShell i ressourcestyring | Microsoft Azure"
   description="Lær at oprette en intern justering af belastning ved hjælp af PowerShell i ressourcestyring."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-powershell"></a>Oprette en intern justering af belastning ved hjælp af PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Følgende fremgangsmåde beskriver, hvordan du opretter en intern justering af belastning ved hjælp af Azure ressourcestyring med PowerShell. Med Azure Resource Manager elementer til at oprette en intern belastningsjustering konfigureres enkeltvis og derefter kombineres for at oprette en justering af belastning.

Du har brug at oprette og konfigurere følgende objekter for at installere en belastningsjustering:

- Front end IP-konfiguration - vil konfigurere indgående netværkstrafik privat IP-adresse
- Back end-adressegruppe - vil konfigurere, hvilke netværksgrænseflader modtager Indlæs afstemmes trafikken kommer fra front end-IP-puljen
- Justering af belastning regler - kilde- og lokal portkonfiguration for justering af belastning.
- Sonder - konfigurerer tilstand status efterprøvning af af efter virtuelt forekomster.
- Indgående regler for NAT - konfigurerer portreglerne direkte adgang til en af virtuelt forekomsterne.

Du kan få mere at vide om Indlæs belastningsjusteringstjenesten komponenter med Azure Ressourcestyring på [Azure ressourcestyring support til justering af belastning](load-balancer-arm.md).

Følgende fremgangsmåde beskriver, hvordan du konfigurerer en belastningsjustering mellem to virtuelle computere.


## <a name="setup-powershell-to-use-resource-manager"></a>Konfiguration af PowerShell til at bruge ressourcestyring

Kontrollér, at du har den seneste version af Azure-modulet til PowerShell og har PowerShell konfigureret korrekt til adgang til Azure abonnementet.

### <a name="step-1"></a>Trin 1

        Login-AzureRmAccount

### <a name="step-2"></a>Trin 2

Markér abonnementer til kontoen

        Get-AzureRmSubscription

Du vil blive bedt om at Godkend med dine legitimationsoplysninger.<BR>

### <a name="step-3"></a>Trin 3

Vælg, hvilke af dine Azure abonnementer til brug. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Oprette ressourcegruppe til justering af belastning

### <a name="step-4"></a>Trin 4

Oprette en ny ressourcegruppe (Spring dette trin, hvis ved hjælp af en eksisterende ressourcegruppe)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Azure ressourcestyring kræver, at alle ressourcegrupper angive en placering. Det bruges som standardplacering til ressourcer i denne ressourcegruppe. Kontrollér, at alle kommandoer til at oprette en belastningsjustering anvender den samme ressourcegruppe.

Vi har oprettet en ressourcegruppe med navnet "NRP-indbyggede RG" og en placering "Vest os" i det foregående eksempel.

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Oprette virtuelle Netværks- og en privat IP-adresse til IP-front end-puljen


### <a name="step-1"></a>Trin 1

Opretter et undernet til det virtuelle netværk og giver variablen $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Oprette et virtuelt netværk:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Opretter det virtuelle netværk og tilføjer undernet kg undernet være til det virtuelle netværk NRPVNet og giver variablen $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Oprette Front end-puljen for IP- og back end-adresse puljen

Konfiguration af en IP-front end-puljen, for indgående Indlæs belastningsjusteringstjenesten netværk trafik og back end-adresse puljen modtage afkrydsningsfeltet Indlæs fordeles trafik.

### <a name="step-1"></a>Trin 1

Oprette en front-end IP-gruppe, ved hjælp af private IP-adressen 10.0.2.5 for den undernet 10.0.2.0/24 som skal være indgående netværk trafik slutpunkt.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>trin 2

Konfigurere en back-end adresse puljen, der bruges til at modtage indgående trafik fra front-end IP-gruppe:

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Oprette kg, NAT-regler, efterprøvning af af og justering af belastning

Når du har oprettet gruppen front-end IP- og back end-adresse puljen, skal du oprette de regler, der skal tilhøre Indlæs belastningsjusteringstjenesten ressource:

### <a name="step-1"></a>Trin 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


Ovenstående eksempel opretter følgende elementer:

- Regel for NAT, går al indgående trafik til port 3441 til port 3389.
- en anden NAT-regel, går al indgående trafik til port 3442 til port 3389.
- en regel, Indlæs belastningsjusteringstjenesten hvilket indlæser saldo alle indgående trafik på offentlige port 80 til lokal port 80 i back-end adresse puljen.
- en regel for efterprøvning af af som kontrollerer tilstand for sti "HealthProbe.aspx"



### <a name="step-2"></a>Trin 2

Oprette den justering af belastning sammenlægning af alle objekter (NAT, Indlæs belastningsjusteringstjenesten regler, efterprøvning af af konfigurationer):

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Oprette netværksgrænseflader

Når du har oprettet den interne justering af belastning, skal du definere, hvilke netværksgrænseflader der modtager indgående Indlæs afstemmes netværkstrafik, NAT regler og efterprøvning af af. Netværksgrænsefladen i dette tilfælde er konfigureret individuelt og kan tildeles til en virtuel maskine senere.


### <a name="step-1"></a>Trin 1


Får den ressource virtuelt netværk og undernet til at oprette netværksgrænseflader:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Dette trin opretter en netværksgrænseflade for, der hører til Indlæs belastningsjusteringstjenesten back-end-puljen og knytte den første regel NAT til RDP til dette netværksgrænseflade:

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Trin 2

Oprette et andet netværk brugergrænseflade kaldet kg Nic2 være:

Dette trin opretter en anden netværksgrænseflade, tildele til samme Indlæs belastningsjusteringstjenesten back-end-puljen og knytte den anden NAT-regel, der er oprettet for RDP:

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

Betyder vil vise følgende:

    $backendnic1

Forventet afgang:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Trin 3

Brug kommandoen Tilføj AzureRmVMNetworkInterface skal tildeles en virtuel maskine NIC.

Du kan finde trinvise instruktioner til at oprette en virtuel maskine og tildele en NIC, følge dokumentationen: [Opret en Azure VM ved hjælp af PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Hvis du allerede har en virtuel maskine, der er oprettet, kan du tilføje netværksgrænsefladen med følgende trin:

#### <a name="step-1"></a>Trin 1

Indlæse Indlæs belastningsjusteringstjenesten ressource i en variabel (Hvis du ikke har gjort det, der). Variablen bruges kaldes $lb og bruge de samme navne fra Indlæs belastningsjusteringstjenesten ressourcen oprettet ovenfor.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Trin 2

Indlæse back end-konfiguration til en variabel.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Trin 3

Indlæse netværksgrænsefladen allerede oprettede i en variabel. variabelnavn bruges er $nic. Netværksnavn interface bruges er den samme fra eksemplet ovenfor.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Trin 4

Ændre back end-konfigurationen på netværksgrænsefladen.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Trin 5

Gemme objektet netværk interface.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Når en netværksgrænseflade er føjet til Indlæs belastningsjusteringstjenesten back end-puljen, starter den modtager netværkstrafik baseret på regler for indlæsning belastningsjusteringstjenesten ressourcen af belastning.

## <a name="update-an-existing-load-balancer"></a>Opdatere en eksisterende justering af belastning


### <a name="step-1"></a>Trin 1

Ved hjælp af justering af belastning fra eksemplet ovenfor, tildele Indlæs belastningsjusteringstjenesten objekt til variabel $slb ved hjælp af Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Trin 2

I følgende eksempel føjer du en ny regel for indgående NAT med 81 i front end-port og port 8181 til back-end-puljen til en eksisterende justering af belastning

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Trin 3

Gemme den nye konfiguration ved hjælp af sæt AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Fjerne en justering af belastning

Brug kommandoen Fjern AzureRmLoadBalancer til at slette en tidligere oprettet belastningsjustering med navnet "NRP kg" i en ressourcegruppe kaldet "NRP-indbyggede RG"

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Du kan bruge det valgfri skifte - kraft for at undgå prompten til sletning.



## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)