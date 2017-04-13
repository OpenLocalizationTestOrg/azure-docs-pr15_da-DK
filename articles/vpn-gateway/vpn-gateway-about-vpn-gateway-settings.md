<properties 
   pageTitle="Om VPN-Gateway, indstillinger for virtuelt netværk gateways | Microsoft Azure"
   description="Få mere at vide om VPN-Gateway, indstillinger for Azure virtuelt netværk."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>Om VPN-Gateway-indstillinger

En VPN-gateway forbindelse løsning er afhængig af konfigurationen af flere ressourcer for at kunne sende netværkstrafik mellem virtuelle netværk og lokale placeringer. Hver ressource indeholder konfigurerbar indstillinger. Kombination af ressourcer og indstillinger for afgør forbindelse resultatet.

I afsnit i denne artikel beskrives de ressourcer og indstillinger, der er relateret til en VPN-gateway i implementeringsmodel **Ressourcestyring** . Det kan være praktisk at få vist de tilgængelige konfigurationer ved hjælp af forbindelse topologi diagrammer. Du kan finde beskrivelser og topologi for diagrammer for hver forbindelse løsning i artiklen [Om VPN-Gateway](vpn-gateway-about-vpngateways.md) . 

## <a name="gwtype"></a>Gateway-datatyper

Hver virtuelt netværk kan kun have én virtuelt netværksgateway af hver type. Når du opretter en virtuel netværksgateway, skal du sikre dig, at gatewayen typen er korrekte for din konfiguration.

De tilgængelige værdier for - GatewayType er: 

- VPN
- ExpressRoute

En VPN-gateway, kræver det `-GatewayType` *Vpn*.  

Eksempel:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>Gatewayen lagerenheder


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Konfigurationen af gatewayen SKU

**Angive gatewayen SKU i portalen Azure**

Hvis du bruger portalen Azure til at oprette en ressourcestyring virtuelt netværksgateway, kan du vælge gatewayen SKU ved hjælp af på rullelisten. De indstillinger, får du vist svarer til gatewayen type og VPN-type, du vælger.

Eksempelvis hvis du vælger typen gateway 'VPN' og den VPN-type 'baseret på politik', vises der kun den 'Grundlæggende' SKU fordi det er den eneste SKU, der er tilgængelige for PolicyBased VPN. Hvis du vælger 'Distribution baseret', kan du vælge mellem Basic, som Standard og ved anvendelse lagerenheder. 


**Angive gatewayen SKU ved hjælp af PowerShell**


I følgende PowerShell-eksempel angiver den `-GatewaySku` som *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Ændre en gateway SKU**

Hvis du vil opgradere din gateway SKU til en mere effektiv SKU (fra Basic/Standard til ved anvendelse) kan du bruge den `Resize-AzureRmVirtualNetworkGateway` PowerShell-cmdlet. Du kan også nedgradere gatewayen SKU størrelse ved hjælp af denne cmdlet.

I følgende PowerShell-eksempel viser en gateway SKU, der ændres størrelse til ved anvendelse.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Anslået Sammenlæg overførselshastighed ved gateway SKU og type

Den følgende tabel viser typerne gateway og anslåede Sammenlæg overførselshastigheden. I denne tabel gælder for både ressourcestyring og klassisk installation modeller.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Forbindelsestyper af

I implementeringsmodel ressourceleder, der kræver hver konfiguration en bestemt virtuelt netværk gateway forbindelsestype. De tilgængelige ressourcestyring PowerShell værdier for `-ConnectionType` er:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

I eksemplet nedenfor PowerShell oprette vi en S2S forbindelse, der kræver forbindelsestype *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>VPN-datatyper

Når du opretter gatewayen virtuelt netværk for en VPN-gateway-konfiguration, skal du angive en VPN-type. Typen VPN, som du vælger, afhænger af den forbindelse topologi, som du vil oprette. For eksempel kræver en P2S forbindelse en RouteBased VPN-type. En VPN-type kan også afhænger af den hardware, som du vil bruge. S2S konfigurationer kræver en VPN-enhed. Nogle VPN-enheder understøtter kun en bestemt VPN-type.

Den VPN-type, du vælger skal opfylder alle de forbindelse løsningen du vil oprette. Eksempelvis hvis du vil oprette en VPN-S2S gateway forbindelse og en VPN-P2S gateway-forbindelse til det samme virtuelle netværk, vil du bruge VPN type *RouteBased* , fordi P2S kræver en RouteBased VPN-type. Du har også brug at bekræfte, at enheden VPN understøttes en RouteBased VPN-forbindelse. 

Når et virtuelt netværk gatewayen er blevet oprettet, kan du ikke ændre typen VPN. Du skal slette gatewayen virtuelt netværk og oprette en ny. Der er to typer af VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


I følgende PowerShell-eksempel angiver den `-VpnType` som *RouteBased*. Når du opretter en gateway, skal du sikre dig, at VpnType - er korrekte for din konfiguration. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Gatewayen krav

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Gateway-undernet

Hvis du vil konfigurere et virtuelt netværksgateway, skal du først oprette en gateway-undernet til din VNet. Gateway-undernet skal navngives *GatewaySubnet* kan fungere korrekt. Dette navn kan Azure vide, at dette undernet skal bruges af gatewayen.

Minimumstørrelse af dit gateway-undernet afhænger helt af konfigurationen, du vil oprette. Selvom det er muligt at oprette en gateway-undernet så lille som /29, anbefaler vi, at du opretter en gateway undernet af /28 eller større (/ 28, /27, /26, osv.). 

Oprette en gateway større forhindrer dig i kører mod gateway størrelsesbegrænsninger. For eksempel, har du oprettet en gateway virtuelt netværk med en gateway undernet størrelse /29 for en S2S forbindelse. Du nu ønsker at konfigurere en S2S/ExpressRoute fungere konfiguration. Denne konfiguration kræver en gateway undernet minimumstørrelse /28. For at oprette din konfiguration, vil det være nødvendigt at redigere gateway-undernet for at imødekomme minimumkrav for forbindelsen, som er /28.

I følgende eksempel ressourcestyring PowerShell viser en gateway-undernet med navnet GatewaySubnet. Du kan se CIDR notation angiver en /27, som giver mulighed for nok IP-adresser for de fleste konfigurationer, der findes i øjeblikket.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Lokale netværk gateways

Når du opretter en VPN-gateway-konfiguration, er din lokale placering beholdere for gatewayen lokale netværk. I den klassiske implementeringsmodel blev lokale netværk gatewayen kaldes et lokalt websted. 

Du give gatewayen lokale netværk et navn, den offentlige IP-adressen på den lokale VPN-enhed, og Angiv, hvilke adressepræfikser, der er placeret på den lokale placering. Azure kigger på destination adressepræfikser for netværkstrafik, hører den konfiguration, som du har angivet for dit lokale netværksgateway og sender pakker i overensstemmelse hermed. Du kan også angive lokale netværk gateways VNet-VNet konfigurationer, der bruger en VPN-gateway-forbindelse.

I følgende PowerShell-eksempel oprettes en ny lokale netværksgateway:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Du vil ændre indstillingerne for gateway lokale netværk. For eksempel, når du tilføjer eller ændrer adresseområdet, eller hvis ændrer IP-adressen på VPN-enheden. Du kan ændre disse indstillinger i klassisk portal på siden lokalnetværk til en klassisk VNet. For ressourcestyring se [Rediger lokale netværk gateway indstillinger ved hjælp af PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST API'er og PowerShell-cmdletter

Flere tekniske ressourcer og bestemte syntakskrav, når du bruger REST API'er og PowerShell-cmdlet'er til VPN-Gateway konfigurationer, finde de følgende sider:

|**Klassisk** | **Ressourcestyring**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST-API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST-API](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Næste trin

Se [Om VPN-Gateway,](vpn-gateway-about-vpngateways.md) du kan finde flere oplysninger om konfiguration af tilgængelige forbindelse. 







 
