Trin til denne opgave bruges en VNet, der er baseret på værdierne nedenfor. Flere indstillinger og navnene er også beskrevet i denne liste. Vi ikke kan bruge denne liste direkte i nogen af trinnene, selvom vi tilføjer variabler, der er baseret på værdierne i denne liste. Du kan kopiere på listen for at bruge som reference, erstatter værdierne med dine egne.

Konfiguration referenceliste:
    
- Virtuel netværksnavn = "TestVNet"
- Virtuelt netværk adresseområde = 192.168.0.0/16
- Ressourcegruppe = "TestRG"
- Subnet1 navn = "Front end" 
- Subnet1 adresseområde = "192.168.0.0/16"
- Undernet gatewaynavn: "GatewaySubnet" du skal altid navngive en gateway undernet *GatewaySubnet*.
- Gatewayen undernet adresseområde = "192.168.200.0/26"
- Område = "Indtastning af østasiatiske USA"
- Gatewaynavn = "GW"
- IP-gatewaynavn = "GWIP"
- Gatewayen IP-konfiguration navn = "gwipconf"
-  Type = "ExpressRoute" denne type er påkrævet til en ExpressRoute konfiguration.
- Offentlige IP-gatewaynavn = "gwpip"


## <a name="add-a-gateway"></a>Tilføje en gateway

1. Oprette forbindelse til dit Azure-abonnement. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Definere dine variabler for denne opgave. I dette eksempel bruger Brug variablerne i eksemplet nedenfor. Sørg for at redigere dette afspejler de indstillinger, du vil bruge. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Gemme objektet virtuelt netværk som en variabel.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Føje en gateway-undernet til netværket virtuel. Gateway-undernet skal navngives "GatewaySubnet". Du skal oprette en gateway, der er /27 eller større (/ 26/25, osv.).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Angive konfigurationen.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Gemme gateway-undernettet som en variabel.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Anmode om en offentlig IP-adresse. IP-adressen der anmodes om før du opretter gatewayen. Du kan ikke angive den IP-adresse, du vil bruge. Det fordeles dynamisk. Du skal bruge denne IP-adresse i sektionen næste konfiguration. AllocationMethod skal være dynamisk.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Oprette konfigurationen af din gateway. Konfigurationen af gatewayen definerer undernettet og den offentlige IP-adresse til brug. I dette trin angiver du den konfiguration, der skal bruges, når du opretter gatewayen. Dette trin opretter ikke faktisk objektet gateway. Brug eksemplet nedenfor til at oprette konfigurationen af gatewayen. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Oprette gatewayen. I dette trin skal er **-GatewayType** især vigtigt. Du skal bruge værdien **ExpressRoute**. Bemærk, at når du har kørt disse cmdletter, gatewayen kan tage 20 minutter eller mere til at oprette.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Kontrollere gatewayen blev oprettet

Brug kommandoen nedenfor til at bekræfte, at gatewayen er blevet oprettet.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Ændre størrelsen på en gateway

Der findes et antal [Gateway lagerenheder](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Du kan bruge følgende kommando til at ændre den Gateway SKU når som helst.

>[AZURE.IMPORTANT] Denne kommando virker UltraPerformance gatewayen. Hvis du vil ændre din gateway til en UltraPerformance gateway, først fjerne den eksisterende ExpressRoute gateway, og derefter oprette en ny UltraPerformance gateway. Hvis du vil nedgradere din gateway fra en UltraPerformance gateway, først fjerne gatewayen UltraPerformance, og derefter oprette en ny gateway.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Fjerne en gateway

Bruge kommandoen nedenfor til at fjerne en gateway

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
