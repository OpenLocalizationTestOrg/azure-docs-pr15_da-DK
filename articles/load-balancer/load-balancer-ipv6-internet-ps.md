<properties
    pageTitle="Oprette en via justering af belastning, IPv6 ved hjælp af PowerShell til Ressourcestyring internettet | Microsoft Azure"
    description="Lær, hvordan du opretter en via justering af belastning, IPv6 ved hjælp af PowerShell til Ressourcestyring internettet"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6-azure justering af belastning dobbelt stak, offentlige IP-adresse, oprindelige ipv6, mobile, og iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Få en introduktion til en via justering af belastning, IPv6 ved hjælp af PowerShell til Ressourcestyring internettet

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Azure CLI](./load-balancer-ipv6-internet-cli.md)
- [Skabelon](./load-balancer-ipv6-internet-template.md)

En Azure justering af belastning er belastningsjustering Layer-4 (TCP, UDP). Justering af belastning giver høj tilgængelighed ved at distribuere indgående trafik mellem forekomster af sund tjenesten i skytjenester eller virtuelle maskiner i et Indlæs belastningsjusteringstjenesten sæt. Azure justering af belastning kan også give disse tjenester på flere porte, flere IP-adresser eller begge dele.

## <a name="example-deployment-scenario"></a>Eksempel på installation scenarie

I følgende diagram vises belastningsjustering løsning, der er implementeret i denne artikel.

![Indlæse belastningsjusteringstjenesten scenarie](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

I dette scenarie skal du oprette følgende Azure ressourcer:

- en forbindelse til internettet justering af belastning med en IPv4 og IPv6 offentlige IP-adressen
- to indlæse justering af regler for at tilknytte de offentlige VIPs til private slutpunkterne
- en tilgængelighed, der er angivet til, der indeholder to FOS
- to virtuelle maskiner (VM'er)
- et virtuelt netværksgrænsefladen til hver VM med både IPv4 og IPv6-adresser, der er tildelt

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Implementering af løsningen ved hjælp af Azure PowerShell

Følgende trin viser, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring med PowerShell internettet. Med Azure Resource Manager hver ressource er blevet oprettet og konfigureret enkeltvis, derefter læg sammen for at oprette en ressource.

Hvis du vil installere belastningsjustering, skal du oprette og konfigurere følgende objekter:

- IP-konfiguration af front end - indeholder offentlige IP-adresser for indgående netværkstrafik.
- Adresse back end-puljen - indeholder netværksgrænseflader (NIC) for de virtuelle maskiner til at modtage netværkstrafik fra justering af belastning.
- Justering af belastning regler - indeholder regler tilknytte en offentlig port på justering af belastning til port i adressegruppen back end.
- Indgående regler for NAT - indeholder regler tilknytte en offentlig port på justering af belastning til en port til en bestemt virtuel maskine i puljen back end-adresse.
- Sonder - indeholder sundhed sonder bruges til at kontrollere tilgængeligheden af forekomster af virtuelle maskiner i puljen back end-adresse.

Du kan finde yderligere oplysninger finder [Azure ressourcestyring support til justering af belastning](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Konfigurere PowerShell til at bruge ressourcestyring

Sørg for, at du har den nyeste version af modulet Azure ressourcestyring til PowerShell.

1. Log på Azure

        Login-AzureRmAccount

    Angiv dine legitimationsoplysninger, når du bliver bedt om.

2. Markér abonnementer til kontoen

        Get-AzureRmSubscription

3. Vælg, hvilke af dine Azure abonnementer til brug.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Oprette en ressourcegruppe (Spring dette trin, hvis ved hjælp af en eksisterende ressourcegruppe)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Oprette et virtuelt netværk og en offentlig IP-adresse til IP-front end-puljen

1. Oprette et virtuelt netværk med et undernet.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Oprette Azure offentlige IP-adresse (PIP) ressourcer til front end IP-adressegruppe.

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] Justering af belastning bruger domæne navnet på den offentlige IP-som præfiks for det fulde Domænenavn. I dette eksempel er FQDN'er *lbnrpipv4.westus.cloudapp.azure.com* og *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Oprette en front-end-IP-konfigurationer og en Back End-adresse samling

1. Oprette adressekonfiguration af front end-, der bruger de offentlige IP-adresser, du har oprettet.

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. Oprette grupper i back end-adresse.

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Oprette kg regler, NAT-regler, en efterprøvning af af og belastningsjustering

I dette eksempel oprettes følgende elementer:

- en NAT regel til at oversætte al indgående trafik på port 443 til port 4443
- en Indlæs belastningsjusteringstjenesten regel saldo al indgående trafik på port 80 til port 80 på adresserne i back end-puljen.
- en Indlæs belastningsjusteringstjenesten regel til at tillade RDP-forbindelse til FOS på port 3389.
- en efterprøvning af af regel til at kontrollere sundhedsstatus på en side med navnet *HealthProbe.aspx* eller en tjeneste på port 8080
- belastningsjustering, der bruger disse objekter

1. Opret regler, NAT.

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. Oprette en tilstand efterprøvning af af. Der er to måder at konfigurere en efterprøvning af af:

    Efterprøvning af af HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    eller TCP efterprøvning af af

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
        $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2


    I dette eksempel skal vi bruge TCP sonder.

3. Oprette en regel for indlæsning af belastning.

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389

4. Oprette den justering af belastning ved hjælp af de tidligere oprettet objekter.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule

## <a name="create-nics-for-the-back-end-vms"></a>Oprette netværkskort til back end-FOS

1. Få virtuelt netværk og virtuelle netværks-undernet, hvor netværkskortene skal oprettes.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Oprette IP-konfigurationer og netværkskort for FOS.

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Oprette virtuelle maskiner og tildele de nyoprettede netværkskort

Finde flere oplysninger om oprettelse af en VM [Opret og konfigurerer på forhånd en Windows virtuel maskine med ressourcestyring og Azure PowerShell](..\virtual-machines\virtual-machines-windows-ps-create.md)

1. Oprette en tilgængelighed angive og lager konto

        New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'

2. Oprette hver VM og tildele den tidligere oprettet netværkskort

        $mySecureCredentials= Get-Credential -Message “Type the username and password of the local administrator account.”

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
