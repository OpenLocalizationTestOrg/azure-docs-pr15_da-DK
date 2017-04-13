
For at konfigurere din VPN-enhed, skal du den offentlige IP-adresse gatewayen virtuelt netværk til at konfigurere din lokale VPN-enhed. Arbejde med producenten af enheden for specifikke konfigurationsoplysninger og konfigurere din enhed. Se de [VPN-enheder](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) kan finde flere oplysninger om VPN-enheder, der fungerer fint med Azure.

For at finde den offentlige IP-adresse for din virtuelt netværksgateway ved hjælp af PowerShell, skal du bruge følgende eksempel:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Du kan også få vist den offentlige IP-adresse for din gateway, virtuelt netværk ved hjælp af portalen Azure. Gå til **virtuelle netværk gateways**og derefter klikke på navnet på din gateway.