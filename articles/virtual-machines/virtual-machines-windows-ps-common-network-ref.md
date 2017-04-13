<properties
    pageTitle="Fælles netværk PowerShell kommandoer til FOS | Microsoft Azure"
    description="Almindelige PowerShell-kommandoer til at komme i gang med at oprette et virtuelt netværk og dets tilknyttede ressourcer for FOS."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Almindelige netværk Azure PowerShell-kommandoer til FOS

Hvis du vil oprette en virtuel maskine, skal du oprette et [virtuelt netværk](../virtual-network/virtual-networks-overview.md) eller ikke kender til et eksisterende virtuelt netværk, hvor VM kan tilføjes. Typisk, når du opretter en VM, du skal også overveje at oprette de ressourcer, der er beskrevet i denne artikel.

Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) for at få oplysninger om at installere den nyeste version af Azure PowerShell, vælge dit abonnement og logge på din konto.

## <a name="create-network-resources"></a>Oprette netværksressourcer

Opgave | Kommandoen 
-------------- | -------------------------
Oprette undernet konfigurationer | $subnet1 = [Ny AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -navn "subnet_name" - AddressPrefix XX. X.X.X/XX<BR>$subnet2 = ny AzureRmVirtualNetworkSubnetConfig-navn "subnet_name" - AddressPrefix XX. X.X.X/XX<BR><BR>Et typisk netværk kan have et undernet for en [internet modstående justering af belastning](../load-balancer/load-balancer-internet-overview.md) og et separat undernet til en [intern justering af belastning](../load-balancer/load-balancer-internal-overview.md). |
Oprette et virtuelt netværk | $vnet = [Ny AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -resource_group_name"navn"virtual_network_name"- ResourceGroupName"-placering "location_name" - AddressPrefix XX. X.X.X/XX-undernet $subnet1, $subnet2
Teste for et entydigt domænenavn | [Test AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName "domænenavn"-placering "location_name"<BR><BR>Du kan angive en DNS-domænenavn til en [offentlig IP-ressource](../virtual-network/virtual-network-ip-addresses-overview-arm.md), der opretter en tilknytning til domainname.location.cloudapp.azure.com til den offentlige IP-adresse i Azure-administreret DNS-serverne. Navnet kan indeholde kun bogstaver, tal og bindestreger. Det første og sidste tegn skal være et bogstav eller tal og domænenavnet skal være entydig i dens Azure placering. Hvis **Sand** returneres, er navnet på din foreslåede globalt entydigt.
Oprette en offentlig IP-adresse | $pip = [Ny AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -navn "ip_address_name" - ResourceGroupName "resource_group_name" - DomainNameLabel "domænenavn"-placering "location_name" - AllocationMethod dynamisk<BR><BR>Den offentlige IP-adresse bruger det domænenavn, du tidligere har testet og bruges af front end-konfigurationen af justering af belastning.
Oprette en front end IP-konfiguration | $frontendIP = [Ny AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -navn "frontend_ip_name" - PublicIpAddress $pip<BR><BR>Front end-konfigurationen omfatter den offentlige IP-adresse, du tidligere har oprettet for indgående netværkstrafik.
Oprette en back end-adressegruppe | $beAddressPool = [Ny AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -navn "backend_pool_name"<BR><BR>Indeholder interne adresser til backend-version af den justering af belastning, er åbnet via et netværksgrænseflade.
Oprette en efterprøvning af af | $healthProbe = [Ny AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -navn "probe_name" - RequestPath 'HealthProbe.aspx'-protokollen http-Port 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Indeholder sundhed sonder bruges til at kontrollere tilgængeligheden af forekomster af virtuelle maskiner i adresse back end-puljen.
Oprette en regel til justering af belastning | $lbRule = [Ny AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -navn HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-forespørgsler $healthProbe-protokollen Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Indeholder regler, der tildeles en offentlig port på justering af belastning til en port i gruppen back end-adresse.
Oprette en indgående NAT-regel | $inboundNATRule = [Ny AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -navn "rule_name" - FrontendIpConfiguration $frontendIP-protokollen TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Indeholder regler tilknytte en offentlig port på justering af belastning til en port til en bestemt virtuel maskine i adresse back end-puljen.
Oprette en justering af belastning | $loadBalancer = [Ny AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName "resource_group_name"-navn "load_balancer_name"-placering "location_name" - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-forespørgsler $healthProbe
Oprette et netværksgrænseflade | $nic1 = [Ny AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName "resource_group_name"-navn "network_interface_name"-placering "location_name" - PrivateIpAddress XX. X.X.X-undernet subnet2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Oprette en netværksgrænseflade ved hjælp af den offentlige IP-adresse og virtuelt netværksundernet, som du tidligere har oprettet.
    
## <a name="get-information-about-network-resources"></a>Få oplysninger om netværksressourcer

Opgave | Kommandoen 
-------------- | -------------------------
Listen virtuelle netværk | [Get-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Viser alle de virtuelle netværk i ressourcegruppen.
Få oplysninger om et virtuelt netværk | Get-AzureRmVirtualNetwork-resource_group_name"navn"virtual_network_name"- ResourceGroupName"
Listen undernet i et virtuelt netværk | Get-AzureRmVirtualNetwork-navn "virtual_network_name" - ResourceGroupName "resource_group_name" & #124; Vælg undernet
Få oplysninger om et undernet | [Get-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -navn "subnet_name" - VirtualNetwork $vnet<BR><BR>Får oplysninger om undernettet, i det angivne virtuelle netværk. Værdien $vnet repræsenterer det objekt, der returneres af Get-AzureRmVirtualNetwork.
Listen IP-adresser | [Get-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Viser de offentlige IP-adresser i ressourcegruppen.
Listen Indlæs balancere | [Get-AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Oversigt over alle Indlæs balancere i ressourcegruppen.
Angiv netværksgrænseflader | [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Oversigt over alle netværksgrænseflader i ressourcegruppen.
Få oplysninger om en netværksgrænseflade | Get-AzureRmNetworkInterface-resource_group_name"navn"network_interface_name"- ResourceGroupName"<BR><BR>Henter oplysninger om en bestemt netværk-grænseflade.
Få IP-konfigurationen af et netværk brugergrænseflade | [Get-AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -navn "ipconfiguration_name" - NetworkInterface $nic<BR><BR>Henter oplysninger om IP-konfigurationen af grænsefladen for det angivne netværk. Værdien $nic repræsenterer det objekt, der returneres af Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Administrere netværksressourcer

Opgave | Kommandoen 
-------------- | -------------------------
Føje et undernet til et virtuelt netværk | [Tilføje AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-navn "subnet_name" - VirtualNetwork $vnet<BR><BR>Tilføjer et undernet til et eksisterende virtuelt netværk. Værdien $vnet repræsenterer det objekt, der returneres af Get-AzureRmVirtualNetwork.
Slette et virtuelt netværk | [Fjern AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -resource_group_name"navn"virtual_network_name"- ResourceGroupName"<BR><BR>Fjerner det angivne virtuelle netværk fra ressourcegruppen.
Slette en netværksgrænseflade | [Fjern AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -resource_group_name"navn"network_interface_name"- ResourceGroupName"<BR><BR>Fjerner grænsefladen for det angivne netværk fra ressourcegruppen.
Slette en justering af belastning | [Fjern AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -resource_group_name"navn"load_balancer_name"- ResourceGroupName"<BR><BR>Fjerner den angivne justering af belastning fra ressourcegruppen.
Slette en offentlig IP-adresse | [Fjern AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-resource_group_name"navn"ip_address_name"- ResourceGroupName"<BR><BR>Fjerner den angivne offentlige IP-adresse fra ressourcegruppen.

## <a name="next-steps"></a>Næste trin

- Brug grænsefladen for det netværk, du lige har oprettet når du [opretter en VM](virtual-machines-windows-ps-create.md).
- Få mere at vide om, hvordan du kan [oprette en VM med flere netværkgrænseflader](../virtual-network/virtual-networks-multiple-nics.md).
