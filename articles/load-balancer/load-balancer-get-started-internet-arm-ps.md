<properties
   pageTitle="Oprette en forbindelse til internettet justering af belastning i ressourcestyring ved hjælp af PowerShell | Microsoft Azure"
   description="Lær at oprette en forbindelse til internettet justering af belastning i ressourcestyring ved hjælp af PowerShell"
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

# <a name="get-started"></a>Oprette en forbindelse til internettet justering af belastning i ressourcestyring ved hjælp af PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [se, hvordan du opretter en forbindelse til internettet justering af belastning ved hjælp af den klassiske implementeringsmodel](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implementering af løsningen ved hjælp af Azure PowerShell

I det følgende forklares, hvordan du opretter en forbindelse til internettet justering af belastning ved hjælp af Azure ressourcestyring med PowerShell. Med Azure Resource Manager hver ressource er oprettet og konfigureret enkeltvis, og angiv derefter sammen for at oprette en justering af belastning.

Du skal oprette og konfigurere følgende objekter for at installere en belastningsjustering:

- Front end IP-konfiguration: indeholder offentlige IP-adresse (PIP)-adresser for indgående netværkstrafik.
- Adresse back end-puljen: indeholder netværksgrænseflader (NIC) for de virtuelle maskiner til at modtage netværkstrafik fra justering af belastning.
- Justering af belastning regler: indeholder regler, der er tilknyttet en port i back end-adresse puljen en offentlig port på justering af belastning.
- Indgående regler NAT: indeholder regler, der er tilknyttet en port til en bestemt virtuel maskine i back end-adresse puljen en offentlig port på justering af belastning.
- Sonder: indeholder sundhed sonder bruges til at kontrollere tilgængeligheden af virtuelt forekomster i adressegruppen back end.

Du kan finde yderligere oplysninger finder [Azure ressourcestyring support til justering af belastning](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurere PowerShell til at bruge ressourcestyring

Sørg for, at du har den nyeste version af modulet Azure ressourcestyring til PowerShell:

1. Log på Azure.

        Login-AzureRmAccount

    Angiv dine legitimationsoplysninger, når du bliver bedt om.

2. Markér abonnementer til kontoen.

        Get-AzureRmSubscription

3. Vælg, hvilke af dine Azure abonnementer til brug.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Oprette en ressourcegruppe. (Springe dette trin, hvis du bruger en eksisterende ressourcegruppe.)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Oprette et virtuelt netværk og en offentlig IP-adresse til IP-front end-puljen

1. Oprette et undernet og et virtuelt netværk.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Oprette en Azure offentlige IP-adresseressource, med navnet **PublicIP**, der skal bruges i en front-end-IP-puljen med DNS-navn **loadbalancernrp.westus.cloudapp.azure.com**. Følgende kommando bruger typen statisk allokering.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]Justering af belastning bruger domæne navnet på den offentlige IP-som et præfiks for det fulde Domænenavn. Dette er forskellig fra den klassiske implementeringsmodel, som bruger skytjenesten som justering af belastning fulde Domænenavn.
    >I dette eksempel er fulde **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Oprette en front end-puljen IP- og en adresse til back end-puljen

1. Oprette en front-end-IP-gruppe med navnet **Kg Frontend** , der bruger **PublicIp** ressourcen.

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Oprette en back end-adressegruppe med navnet **kg-backend-version**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Oprette NAT regler, en Indlæs belastningsjusteringstjenesten regel, en efterprøvning af af og belastningsjustering

I dette eksempel oprettes følgende elementer:

- En NAT regel til at oversætte al indgående trafik på port 3441 til port 3389
- En NAT regel til at oversætte al indgående trafik på port 3442 til port 3389
- En efterprøvning af af regel til at kontrollere sundhedsstatus på en side med navnet **HealthProbe.aspx**
- En regel for indlæsning belastningsjusteringstjenesten at saldo al indgående trafik på port 80 til port 80 på adresserne i back end-puljen
- Belastningsjustering, der bruger disse objekter

Følg disse trin:

1. Opret regler, NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Oprette en tilstand efterprøvning af af. Der er to måder at konfigurere en efterprøvning af af:

    Efterprøvning af af HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Efterprøvning af af TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Oprette en regel for indlæsning af belastning.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Oprette justering af belastning ved hjælp af de tidligere oprettet objekter.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Oprette netværkskort

Oprette netværksgrænseflader (eller redigere eksisterende) og knytte dem til NAT-regler, regler for indlæsning af belastningsjusteringstjenesten og sonder:

1. Få det virtuelle netværk og et virtuelt netværksundernet, hvor netværkskortene skal oprettes.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Oprette en navngivet NIC **kg nic1 være**, og knytte den til den første regel NAT og første (og kun) adresse til back end-puljen.

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Oprette en navngivet NIC **kg nic2 være**, og knytte den til den anden regel NAT og første (og kun) adresse til back end-puljen.

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Markér netværkskortene.

        $backendnic1

    Forventet afgang:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
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
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Brug den `Add-AzureRmVMNetworkInterface` til at tildele netværkskortene til forskellige FOS.

## <a name="create-a-virtual-machine"></a>Oprette en virtuel maskine

For at vide om at oprette en virtuel maskine og tildele en NIC, skal du se [oprette en Azure VM ved hjælp af PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Føje netværksgrænsefladen til justering af belastning

1. Hent justering af belastning fra Azure.

    Indlæse Indlæs belastningsjusteringstjenesten ressource i en variabel (Hvis du ikke har gjort det, der). Variablen kaldes **$lb**. Bruge de samme navne fra Indlæs belastningsjusteringstjenesten ressource, som du oprettede tidligere.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Indlæse back end-konfiguration til en variabel.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Indlæse netværksgrænsefladen allerede oprettede i en variabel. Den variabelnavn er **$nic**. Interface netværksnavn er den samme, fra det tidligere eksempel.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Ændre den back end-konfiguration på netværksgrænsefladen.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Gemme objektet netværk interface.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Når en netværksgrænseflade er føjet til Indlæs belastningsjusteringstjenesten back end-puljen, starter den modtager netværkstrafik baseret på de justering af belastning regler for indlæsning belastningsjusteringstjenesten ressourcen.

## <a name="update-an-existing-load-balancer"></a>Opdatere en eksisterende justering af belastning

1. Ved hjælp af justering af belastning fra det tidligere eksempel, tildele et Indlæs belastningsjusteringstjenesten objekt til variable **$slb** ved hjælp af `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. I eksemplet nedenfor kan du tilføje en indgående NAT regel--ved hjælp af port 81 i front end-puljen og port 8181 til back end-puljen – til en eksisterende justering af belastning.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Gemme den nye konfiguration ved hjælp af `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Fjerne en justering af belastning

Brug kommandoen `Remove-AzureLoadBalancer` **NRP-indbyggede RG**for at slette en tidligere oprettet belastningsjustering med navnet **NRP kg** i en ressourcegruppe navnet.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Du kan bruge den valgfri parameter **-kraft** for at undgå prompten til sletning.

## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
