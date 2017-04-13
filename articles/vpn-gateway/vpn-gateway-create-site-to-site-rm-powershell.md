<properties
   pageTitle="Oprette et virtuelt netværk med en-til-websted VPN-forbindelse ved hjælp af Azure ressourcestyring og PowerShell | Microsoft Azure"
   description="I denne artikel vejledes du gennem, oprette en VNet ved hjælp af implementeringsmodel ressourcestyring og forbinde den til din lokale lokale netværk ved hjælp af en VPN-S2S gateway-forbindelse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Oprette en VNet med forbindelse til websted ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Klassisk - klassisk Portal](vpn-gateway-site-to-site-create.md)

I denne artikel vejledes du gennem oprettelse af et virtuelt netværk og en VPN-websted til gateway-forbindelse til dit lokale netværk ved hjælp af implementeringsmodel Azure ressourcestyring. Websted til websted forbindelser kan bruges til tværs lokal og hybrid konfigurationer.

![Websted til diagram] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "websted til websted") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Installation-modeller og metoder til at websted forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

I følgende tabel vises de tilgængelige installation modeller og metoder til at websted konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Yderligere konfigurationer

Hvis du vil oprette forbindelse VNets sammen, men ikke opretter forbindelse til en lokal placering, kan du se [konfigurere en VNet-VNet forbindelse](vpn-gateway-vnet-vnet-rm-ps.md). Hvis du vil føje en forbindelse til websted til et VNet, der allerede har en forbindelse, kan du se [tilføje en S2S forbindelse til en VNet med en eksisterende forbindelse for VPN-gateway](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Inden du går i gang

Kontrollér, at du har følgende elementer, før du starter konfigurationen.

- En VPN-kompatibel enhed og en person, der er i stand til at konfigurere den. Se [om VPN-enheder](vpn-gateway-about-vpn-devices.md). Hvis du ikke kender konfiguration af enheden VPN eller ikke har kendskab til den IP-adresse, områder, der er placeret i dine lokale netværkskonfiguration, skal du være koordineret med en person, der kan give disse oplysninger for dig.

- En eksternt modstående offentlige IP-adresse til din VPN-enhed. Denne IP-adresse må ikke være placeret bag en NAT.
    
- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Den seneste version af Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.


## <a name="Login"></a>1. oprette forbindelse til dit abonnement 

Kontrollér, at du skifter til PowerShell til at bruge-cmdletter ressourcestyring. Yderligere oplysninger finder du i [Bruge Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

Åbn din PowerShell console og oprette forbindelse til din konto. Brug følgende eksempel til at hjælpe dig med at oprette forbindelse:

    Login-AzureRmAccount

Markér abonnementer til kontoen.

    Get-AzureRmSubscription 

Angiv det abonnement, du vil bruge.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. Opret et virtuelt netværk og et gateway-undernet

Eksempler på Brug en gateway undernet af /28. Selvom det er muligt at oprette en gateway-undernet så lille som /29, anbefaler vi, at du opretter et større undernet, der indeholder flere adresser ved at vælge mindst /28 eller /27. Dette giver mulighed for nok adresser til at medtage yderligere konfigurationer, der kan du i fremtiden.

Hvis du allerede har et virtuelt netværk med en gateway-undernet, der er/29 eller større, kan du springe frem til at [tilføje dit lokale netværksgateway](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Du kan oprette et virtuelt netværk og et gateway-undernet

Brug følgende eksempel til at oprette et virtuelt netværk og et gateway-undernet. Erstatte værdier for din egen. 

Først skal oprette en ressourcegruppe:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Dernæst skal du oprette netværket virtuel. Kontrollér, at felterne adresse, du angiver ikke overlapper et af felterne adresse, du har på dit lokale netværk.

Følgende eksempelformel opretter et virtuelt netværk med navnet *testvnet* og to undernet, én kaldet *GatewaySubnet* og den anden kaldet *Subnet1*. Det er vigtigt at oprette ét undernet med navnet specifikt *GatewaySubnet*. Hvis du navngive den noget andet, mislykkes konfigurationen af forbindelsen. 

Angive variablerne.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Oprette VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Føje en gateway-undernet til et virtuelt netværk, du allerede har oprettet

Dette trin er påkrævet, kun, hvis du vil føje en gateway-undernet til en VNet, som du tidligere har oprettet.

Du kan oprette din gateway-undernet ved hjælp af i følgende eksempel. Sørg for at navngive gateway undernet 'GatewaySubnet'. Hvis du navngive den noget andet, du opretter et undernet, men Azure behandle ikke den som en gateway-undernet.

Angive variablerne.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Oprette gateway-undernet.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Angive konfigurationen. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>tilføje dit lokale netværksgateway

Et virtuelt netværk og refererer gatewayen lokale netværk typisk til din lokale placering. Giv webstedet et navn, som Azure kan referere til den og også angive præfikset adresse plads til gatewayen lokale netværk. 

Azure bruger præfikset IP-adresse, du angiver for at identificere, hvilke trafik til at sende til din lokale placering. Det betyder, at du har til at angive hver adressepræfiks, der skal være knyttet til dit lokale netværksgateway. Du kan nemt opdatere disse præfikser, hvis dit lokale netværk ændres. 

Når du bruger PowerShell eksemplerne, opmærksom på følgende:
    
- *GatewayIPAddress* er IP-adressen på din lokale VPN-enhed. Din VPN-enhed kan ikke være placeret bag en NAT. 
- *AddressPrefix* er din lokale adresseområde.

Sådan tilføjer du en lokale netværksgateway med et enkelt adressepræfiks:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Sådan tilføjer du en lokale netværksgateway med flere adressepræfikser:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Ændre IP-adressepræfikser for dit lokale netværksgateway

Nogle gange kan ændret dit lokale netværk gateway præfikser. De trin, du tager, i ændre din IP-adressepræfikser afhænger af, om du har oprettet en VPN-gateway-forbindelse. I afsnittet [ændre IP-adressepræfikser for en gateway lokale netværk](#modify) i denne artikel.


## <a name="PublicIP"></a>4. anmode om en offentlig IP-adresse til gatewayen VPN

Derefter skal du anmode om en offentlig IP-adresse skal allokeres til din Azure VNet VPN-gateway. Dette er ikke den samme IP-adresse, der er tildelt din VPN-enhed. hellere er den tildelt til gatewayen Azure VPN sig selv. Du kan ikke angive den IP-adresse, du vil bruge. Det er dynamisk allokeret til din gateway. Du bruger denne IP-adresse, når du konfigurerer din lokale VPN-enhed til at oprette forbindelse til gatewayen.

Gatewayen Azure VPN til Ressourcestyring implementeringsmodel aktuelt understøtter kun offentlige IP-adresser ved hjælp af metoden til dynamisk fordeling. Det betyder dog ikke IP-adressen ændres. Den eneste gang ændres Azure VPN gateway IP-adresse er, når gatewayen slettes og genoprettes. Ikke ændre gatewayen offentlige IP-adressen på tværs af ændre størrelsen på, hvis du nulstiller eller andre interne vedligeholdelse/opgraderinger af din Azure VPN-gateway.

Brug følgende PowerShell-eksempel:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. oprette gatewayen IP-adresse konfiguration

Konfigurationen af gatewayen definerer undernettet og den offentlige IP-adresse til brug. Brug følgende eksempel til at oprette konfigurationen af gatewayen.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. oprette gatewayen virtuelt netværk

I dette trin skal oprette du gatewayen virtuelt netværk. Oprette en gateway kan tage lang tid at fuldføre. Ofte 45 minutter eller mere. 

Brug følgende værdier:

- *-GatewayType* for et websted til websted konfiguration er *Vpn*. Typen gateway er altid specifikke for konfigurationen, som du implementerer. Andre gateway-konfigurationer kræver muligvis - GatewayType ExpressRoute. 

- *-VpnType* kan være *RouteBased* (kaldes en dynamisk Gateway i nogle dokumentation) eller *PolicyBased* (kaldet en statisk Gateway i nogle dokumentation). Du kan finde flere oplysninger om VPN-gateway typer, [Om VPN Gateways](vpn-gateway-about-vpngateways.md#vpntype).
- *-GatewaySku* kan være *grundlæggende*, *Standard*eller *ved anvendelse*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. Konfigurer din VPN-enhed

På dette tidspunkt skal du den offentlige IP-adresse gatewayen virtuelt netværk til at konfigurere din lokale VPN-enhed. Arbejde med producenten af enheden for specifikke konfigurationsoplysninger. Du kan referere til [VPN-enheder](vpn-gateway-about-vpn-devices.md) kan finde flere oplysninger.

For at finde den offentlige IP-adresse på din gateway, virtuelt netværk skal du bruge følgende eksempel:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. oprette VPN-forbindelse

Dernæst skal oprette til websted VPN-forbindelse mellem din gateway, virtuelt netværk og enheden VPN. Sørg for at erstatte værdierne med dine egne. Den delte nøgle skal svare til den værdi, du har brugt til konfigurationen af VPN-enhed. Bemærk, at den `-ConnectionType` for websted til websted er *IPsec*. 

Angive variablerne.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Oprette forbindelsen.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Når du har en kort under forbindelsen fastlægges. 

## <a name="toverify"></a>Kontrollere en VPN-forbindelse

Der er et par forskellige måder at bekræfte din VPN-forbindelse.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Ændre IP-adressepræfikser for en gateway lokale netværk

Hvis du vil ændre præfikser for dit lokale netværksgateway, skal du bruge følgende instruktioner. Der findes to instruktionssæt. De instruktioner, du vælger, afhænger af, om du allerede har oprettet din gateway-forbindelse. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Ændre en lokale netværksgateway gateway IP-adresse

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Næste trin

- Du kan føje virtuelle maskiner til dine virtuelle netværk. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.

- Se oplysninger om BGP [BGP oversigt](vpn-gateway-bgp-overview.md) og [hvordan du konfigurerer BGP](vpn-gateway-bgp-resource-manager-ps.md).

