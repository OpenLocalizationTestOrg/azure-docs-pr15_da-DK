Hvis du vil ændre gateway IP-adressen skal bruge den `New-AzureRmVirtualNetworkGatewayConnection` cmdlet. Så længe du beholder navnet på gatewayen lokale netværk præcis det samme som det eksisterende navn, overskriver indstillingerne. Cmdletten "Angiv" understøtter på nuværende tidspunkt ikke ændre gatewayen IP-adresse.

### <a name="gwipnoconnection"></a>Hvordan du ændrer gateway IP-adresse - nogen gateway-forbindelse

For at opdatere gateway IP-adressen til dit lokale netværksgateway, der endnu ikke har en forbindelse, skal du bruge eksemplet nedenfor. Du kan også opdatere adressepræfikser på samme tid. De indstillinger, du angiver overskriver de eksisterende indstillinger. Sørg for at bruge det eksisterende navn for gateway dit lokale netværk. Hvis du ikke, kan du oprette en ny lokale netværksgateway ikke overskrive den eksisterende kalender.

Brug følgende eksempel erstatter værdierne for din egen.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Hvordan du ændrer gateway IP-adresse - eksisterende gateway-forbindelse

Hvis der findes allerede en gateway-forbindelse, skal du først fjerne forbindelsen. Derefter kan du redigere gateway IP-adresse og genskabe en ny forbindelse. Dette kan medføre nogle nedetid for din VPN-forbindelse.


>[AZURE.IMPORTANT] Slet ikke VPN gatewayen. Hvis du gør det, du er nødt til at gå tilbage gennem trinnene for at genoprette den samt omkonfigurere routeren lokalt med den IP-adresse, der skal tildeles til nyoprettede gatewayen.
 

1. Fjerne forbindelsen. Du kan finde navnet på din forbindelse ved hjælp af den `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Ændre værdien GatewayIpAddress. Du kan også redigere din adressepræfikser på nuværende tidspunkt, hvis det er nødvendigt. Bemærk, at dette overskriver de eksisterende indstillinger for gateway lokale netværk. Brug det eksisterende navn på dit lokale netværk gatewayen, når du ændrer så indstillingerne overskriver. Hvis du ikke, kan du oprette en ny lokale netværksgateway ikke at ændre den eksisterende kalender.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Oprette forbindelsen. I dette eksempel skal konfigurerer vi en IPsec-forbindelsestype. Når du genoprette forbindelsen, kan du bruge den forbindelsestype, der er angivet til din konfiguration. Du kan finde yderligere forbindelsestype, siden [PowerShell-cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) .  For at få VirtualNetworkGateway navn, du kan køre den `Get-AzureRmVirtualNetworkGateway` cmdlet.

    Angive variablerne:

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Oprette forbindelsen:
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

