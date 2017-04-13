<properties 
   pageTitle="Konfigurere tvunget tunnelføring for at websted forbindelser ved hjælp af Ressourcestyring implementeringsmodel | Microsoft Azure"
   description="Sådan omdirigeres eller 'gennemtvinge' alle Internet-bundne trafik tilbage til din lokale placering."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Konfigurere tvunget tunnelføring ved hjælp af implementeringsmodel Azure ressourcestyring

> [AZURE.SELECTOR]
- [PowerShell - klassisk](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - ressourcestyring](vpn-gateway-forced-tunneling-rm.md)

Tvunget tunnelføring kan du Omdiriger eller "kraft" al trafik til internetbaserede tilbage til din lokale placering via en VPN-websted til websted tunnel til inspektion og revision. Dette er et kritiske sikkerhedskrav til de fleste enterprise IT politikker.

Uden tvunget tunnelføring vil internetbaserede trafik fra din FOS i Azure altid gennemgang fra Azure-netværksinfrastruktur direkte ud til internettet, uden mulighed for at tillade dig om at undersøge eller overvåge trafikken. Uautoriseret adgang til internettet potentielt kan føre til offentliggørelse af oplysninger eller andre typer brud på sikkerhed

I denne artikel vejleder dig gennem konfigurationen af tvunget tunnelføring for virtuelle netværk, der er oprettet ved hjælp af Ressourcestyring implementeringsmodel.

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Installation-modeller og værktøjer til tvunget tunnelføring**

En tvunget tunnelføring forbindelse kan konfigureres til både den klassiske implementeringsmodel og implementeringsmodel ressourcestyring. Se tabellen nedenfor for at få flere oplysninger. Vi opdaterer denne tabel, som nye artikler, nye installation-modeller og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra tabellen.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Om tvunget tunnelføring


I det følgende diagram viser, hvordan tvunget tunnelføring fungerer. 

![Tvungen tunnelføring](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

I det foregående eksempel tunnelført Frontend undernet ikke er tvunget. Arbejdsmængder i front end-undernet kan fortsætte med at acceptere og besvare spørgsmål fra kunder fra internettet direkte. Mellemstore og back end-undernet er tvunget tunnelført. En hvilken som helst udgående forbindelser fra disse to undernet til internettet vil blive tvunget eller omdirigeret tilbage til et lokalt websted via en af S2S VPN-tunneler.

Dette giver dig mulighed at begrænse og undersøge internetadgang fra din virtuelle maskiner eller cloud services i Azure, mens du fortsætter med at aktivere din arkitektur af med flere lag tjenesten påkrævet. Du kan også anvende, tvunget tunnel til hele virtuelle netværk, hvis der ikke er nogen internettet arbejdsmængder i dine virtuelle netværk.

## <a name="requirements-and-considerations"></a>Kravene samt de overvejelser

Tvunget tunnelføring i Azure er konfigureret via virtuelt netværk brugerdefinerede omdirigerer. Omdirigere trafikken til et lokalt websted er udtrykt som en standard rute til Azure VPN gatewayen. Se [brugerdefinerede omdirigerer og IP-videresendelse](../virtual-network/virtual-networks-udr-overview.md)kan finde flere oplysninger om brugerdefinerede routing og virtuelle netværk.

- Hvert virtuelt netværksundernet har en indbygget, routing system-tabel. Systemtabellen routing har følgende tre grupper af omdirigerer:

    - **Lokale VNet dirigerer:** Direkte til destinationen FOS i det samme virtuelle netværk
    
    - **Lokale omdirigerer:** Til gatewayen Azure VPN
    
    - **Standardrute:** Direkte til internettet. Pakker, til de private IP-adresser, der ikke er omfattet af de forrige to omdirigerer fjernes.

-  Denne fremgangsmåde bruger brugerdefinerede omdirigerer (UDR) til at oprette en routing tabel for at tilføje en standardrute, og derefter knytte tabellen routing til din VNet subnet(s) til at aktivere tvunget tunnelføring på disse undernet.

- Tvunget tunnelføring skal være knyttet til en VNet, der indeholder en distribution baseret VPN-gateway. Du skal angive et "standardwebsted" mellem de lokale websteder i tværs lokale forbundet med det virtuelle netværk.

- ExpressRoute tvungen tunnelføring er ikke konfigureret via denne funktion, men i stedet er aktiveret ved at anmelde en standardrute via de ExpressRoute BGP peering sessioner. Se i [Dokumentationen til ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) kan finde flere oplysninger.

## <a name="configuration-overview"></a>Oversigt over konfiguration

Følgende procedure hjælper dig med at oprette en ressourcegruppe og en VNet. Du kan derefter oprette en VPN-gateway og konfigurere tvunget tunnelføring. I denne procedure, og det virtuelle netværk "MultiTier-VNet" har 3 undernet: *Frontend*, *Midtier*og *backend-version*, med 4 tværs lokale forbindelser: *DefaultSiteHQ*og 3 *forgreninger*.

Følgende procedure angive *DefaultSiteHQ* som standard websted forbindelsen til tvunget tunnelføring, og Konfigurer Midtier og back end-undernet bruge tvunget tunnelføring.

    
## <a name="before-you-begin"></a>Inden du går i gang

Kontrollér, at du har følgende elementer, før du starter konfigurationen.

- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).

- Du skal installere den nyeste version af Azure ressourcestyring PowerShell-cmdlet'er (1.0 eller nyere). Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.


## <a name="configure-forced-tunneling"></a>Konfigurere tvunget tunnelføring

1. Log på din Azure-konto i PowerShell-konsollen. Denne cmdlet beder dig om logonoplysninger for din Azure-konto. Når du logger på, henter den dine kontoindstillinger, så de er tilgængelige til Azure PowerShell.

        Login-AzureRmAccount 

2. Få en liste over dine Azure abonnementer.

        Get-AzureRmSubscription

2. Angiv det abonnement, du vil bruge. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Oprette en ressourcegruppe.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Oprette et virtuelt netværk og angive undernet. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Oprette gateways lokale netværk.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Oprette rutetabel og distribuere regel.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Knytte rutetabellen til Midtier og back end-undernet.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Oprette gatewayen med en standard-webstedet. Dette trin tager noget tid at fuldføre nogle gange 45 minutter eller mere, da du opretter og konfigurationen af gatewayen.<br> Den `-GatewayDefaultSite` er parameteren cmdlet, der tillader tvunget routing konfiguration til at arbejde, så sørg for at konfigurere denne indstilling korrekt. Denne parameter er tilgængelig i PowerShell 1.0 eller senere.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Oprette til websted VPN-forbindelser.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



