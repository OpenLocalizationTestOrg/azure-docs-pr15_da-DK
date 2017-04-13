### <a name="noconnection"></a>Sådan tilføjer eller fjerner præfikser - nogen gateway-forbindelse

- **Tilføje** ekstra adressepræfikser på et lokalt netværk gateway, du har oprettet, men, der ikke, men har en gateway-forbindelse, bruge eksemplet nedenfor. Sørg for at ændre værdierne til din egen.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Fjerne** en adressepræfiks fra et lokalt netværksgateway, der ikke har en VPN-forbindelse, bruge eksemplet nedenfor. Udelade de præfikser på enheder, du ikke længere har brug for. I dette eksempel skal vi ikke længere har brug for præfiks 20.0.0.0/24 (fra det forrige eksempel), så vi kan opdatere lokalt netværk gateway og udelade præfikset.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Sådan tilføjer eller fjerner præfikser - eksisterende gateway-forbindelse

Hvis du har oprettet din gateway-forbindelse og vil tilføje eller fjerne de IP-adressepræfikser, der er indeholdt i dit lokale netværksgateway, skal du gøre følgende trin i rækkefølge. Dette kan medføre nogle nedetid for din VPN-forbindelse. Når du opdaterer din præfikser på enheder, skal du først fjerne forbindelsen, ændre præfikserne og derefter oprette en ny forbindelse. Sørg for at ændre værdierne til din egen i eksemplerne nedenfor.

>[AZURE.IMPORTANT] Slet ikke VPN gatewayen. Hvis du gør dette, skal du skal gå tilbage gennem trinnene for at genoprette den samt omkonfigurere routeren lokalt med de nye indstillinger.
 
1. Fjerne forbindelsen.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Rediger adressepræfikser for gateway dit lokale netværk.

    Angive variablen for LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Ændre præfikserne.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Oprette forbindelsen. I dette eksempel skal konfigurerer vi en IPsec-forbindelsestype. Når du genoprette forbindelsen, kan du bruge den forbindelsestype, der er angivet til din konfiguration. Du kan finde yderligere forbindelsestype, siden [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) .

    Angive variablen for VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Oprette forbindelsen. Bemærk, at dette eksempel bruger variablen $local, der konfigureres i det foregående trin.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
