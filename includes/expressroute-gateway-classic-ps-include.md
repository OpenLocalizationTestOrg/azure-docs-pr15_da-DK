Du skal oprette en VNet og en gateway-undernet først, før du arbejder på følgende opgaver. Se artiklen [konfigurere et virtuelt netværk ved hjælp af portalen klassisk](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) kan finde flere oplysninger.   

## <a name="add-a-gateway"></a>Tilføje en gateway

Bruge kommandoen nedenfor til at oprette en gateway. Sørg for at erstatte alle værdier for din egen.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Kontrollere gatewayen blev oprettet

Brug kommandoen nedenfor til at bekræfte, at gatewayen er blevet oprettet. Denne kommando henter også gateway-ID, som du har brug for for andre handlinger.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Ændre størrelsen på en gateway

Der findes et antal [Gateway lagerenheder](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan bruge følgende kommando til at ændre den Gateway SKU når som helst.

>[AZURE.IMPORTANT] Denne kommando virker UltraPerformance gatewayen. Hvis du vil ændre din gateway til en UltraPerformance gateway, først fjerne den eksisterende ExpressRoute gateway, og derefter oprette en ny UltraPerformance gateway. Hvis du vil nedgradere din gateway fra en UltraPerformance gateway, først fjerne gatewayen UltraPerformance, og derefter oprette en ny gateway. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Fjerne en gateway

Bruge kommandoen nedenfor til at fjerne en gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>