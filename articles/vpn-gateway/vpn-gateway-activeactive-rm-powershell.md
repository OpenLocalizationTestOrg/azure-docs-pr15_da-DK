<properties
   pageTitle="Sådan konfigureres aktiv-aktiv S2S VPN-forbindelser med Azure VPN Gateways ved hjælp af Azure ressourcestyring og PowerShell | Microsoft Azure"
   description="I denne artikel vejleder dig gennem konfigurationen af aktiv-aktiv forbindelser med Azure VPN Gateways ved hjælp af Azure ressourcestyring og PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Konfigurere aktiv-aktiv S2S VPN-forbindelser med Azure VPN Gateways ved hjælp af Azure ressourcestyring og PowerShell

I denne artikel fører dig gennem trinnene til at oprette aktiv-aktiv tværs lokale og VNet-VNet forbindelser ved hjælp af Ressourcestyring implementeringsmodel og PowerShell.


**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>Om meget tilgængelige tværs lokale forbindelser

For at opnå høj tilgængelighed til tværs det lokale miljø og VNet-VNet forbindelse, skal du installere flere VPN gateways og oprette flere parallelle forbindelser mellem dit netværk og Azure. Se [meget tilgængelige tværs det lokale miljø og VNet-VNet forbindelse](./vpn-gateway-highlyavailable.md) til en oversigt over indstillinger for forbindelse og topologi.

I denne artikel indeholder en vejledning til konfiguration af en aktiv-aktiv tværs lokale VPN-forbindelse og aktiv-aktiv forbindelse mellem to virtuelle netværk:

- [Del 1 – oprette og konfigurere din Azure VPN-gateway i aktiv-aktiv tilstand](#aagateway)

- [Del 2 – oprette aktiv-aktiv tværs lokale forbindelser](#aacrossprem)

- [Del 3 – oprette aktiv-aktiv VNet-VNet forbindelser](#aav2v)

- [Del 4 – Opdater eksisterende gateway mellem aktiv-aktiv og aktiv standby](#aaupdate)

Du kan kombinere disse sammen for at opbygge en mere kompleks, meget tilgængelige netværkstopologi, der opfylder dine behov.

>[AZURE.IMPORTANT] Bemærk, at den aktive aktive tilstand kun fungerer i HighPerformance SKU


## <a name ="aagateway"></a>Del 1 – oprette og konfigurere aktiv-aktiv VPN gateways

Følgende trin konfigurere din Azure VPN-gateway i aktiv-aktiv funktionsmåder. De tre vigtigste forskelle mellem aktiv-aktiv og aktiv standby gateways:

- Du skal oprette to IP-Gateway-konfigurationer med to offentlige IP-adresser
- Du skal angive flaget EnableActiveActiveFeature
- Gatewayen SKU skal være ved anvendelse

De andre egenskaber er den samme som ikke-aktiv-aktiv gateways. 

### <a name="before-you-begin"></a>Inden du går i gang

- Kontrollér, at du har et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Du skal installere Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

### <a name="step-1---create-and-configure-vnet1"></a>Trin 1 – oprette og konfigurere VNet1

#### <a name="1-declare-your-variables"></a>1. erklære dine variabler

I denne opgave skal vi starter med erklære vores variabler. Eksemplet herunder erklærer variabler ved hjælp af værdierne i denne opgave. Sørg for at erstatte værdierne med dine egne, når du konfigurerer til fremstilling. Du kan bruge disse variabler, hvis du kører gennemgå trinnene til at blive fortrolig med denne type konfiguration. Ændre variablerne, og derefter kopiere og sætte ind i din PowerShell console.

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. oprette forbindelse til dit abonnement og oprette en ny ressourcegruppe

Kontrollér, at du skifter til PowerShell til at bruge-cmdletter ressourcestyring. Yderligere oplysninger finder du i [Bruge Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md).

Åbn din PowerShell console og oprette forbindelse til din konto. Brug følgende eksempel til at hjælpe dig med at oprette forbindelse:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. Opret TestVNet1

Eksemplet nedenfor opretter et virtuelt netværk med navnet TestVNet1 og tre undernet, én kaldet GatewaySubnet, én kaldet FrontEnd og én kaldet back end. Ved erstatning med værdier, er det vigtigt, at du altid navngive din gateway-undernet specifikt GatewaySubnet. Hvis du navngive den noget andet, mislykkes din gateway oprettelse. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Trin 2 – oprette gatewayen VPN til TestVNet1 med aktiv-aktiv tilstand

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. oprette den offentlige IP-adresser og gateway IP-konfigurationer

Anmode om to offentlige IP-adresser skal allokeres til gatewayen, du vil oprette for din VNet. Du kan også definere undernet og IP-konfigurationer, der er påkrævet. 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. oprette gatewayen VPN med aktiv aktiv konfiguration

Oprette gatewayen virtuelt netværk for TestVNet1. Bemærk, at der er to GatewayIpConfig poster, og flaget EnableActiveActiveFeature er indstillet. Aktiv-aktiv tilstand kræver en rute-baserede VPN-gateway af HighPerformance SKU. Oprette en gateway kan tage et stykke tid (30 minutter eller mere for at fuldføre).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. få gateway offentlige IP-adresser og BGP peer-IP-adresse

Når gatewayen er oprettet, skal du hente BGP peer-IP-adressen på gatewayen Azure VPN. Denne adresse er nødvendig for at konfigurere gatewayen Azure VPN som en BGP Peer til din lokale VPN-enheder.

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Brug følgende cmdletter til at vise de to offentlige IP-adresser allokeret for din VPN-gateway, og deres tilsvarende BGP peer-IP-adresser for hver forekomst af gatewayen:

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

Rækkefølgen af den offentlige IP-adresser til gatewayforekomster, og de tilsvarende BGP Peering adresser er den samme. I dette eksempel gatewayen VM med offentlige IP-af 40.112.190.5 anvender 10.12.255.4 som BGP Peering adressen og gatewayen med 138.91.156.129 anvender 10.12.255.5. Disse oplysninger bruges, når du konfigurerer dine til lokale VPN-enheder, der er forbindelse til gatewayen aktiv-aktiv. Gatewayen vises i diagrammet nedenfor med alle adresser:

![aktiv-aktiv gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Når gatewayen er oprettet, kan du bruge denne gateway til at oprette aktiv-aktiv tværs lokale eller VNet-VNet forbindelse. De følgende afsnit fører gennem trinnene for at fuldføre øvelsen.


## <a name ="aacrossprem"></a>Del 2 – oprette en aktiv-aktiv tværs lokale forbindelse

Hvis du vil oprette en forbindelse i tværs det lokale miljø, skal du oprette en lokal netværksgateway til at repræsentere din lokale VPN-enhed, og en forbindelse til at oprette forbindelse gatewayen Azure VPN med gatewayen lokale netværk. I dette eksempel er Azure VPN gatewayen i aktiv-aktiv tilstand. Som et resultat, selvom der er kun en lokal VPN-enhed (lokale netværksgateway) og én forbindelse ressource, fastlægger begge Azure VPN gatewayforekomster S2S VPN tunneler med kameraet, i det lokale miljø.

Før du fortsætter, Kontroller, at du har fuldført [del 1](#aagateway) af denne opgave.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Trin 1 – oprette og konfigurere gatewayen lokale netværk

#### <a name="1-declare-your-variables"></a>1. erklære dine variabler

Denne øvelse fortsætter med at opbygge konfigurationen vises i diagrammet. Sørg for at erstatte værdierne med dem, du vil bruge til din konfiguration.

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

Et par ting at være opmærksom på angående lokale netværk gateway parametre:

- Gatewayen lokale netværk kan være i samme eller en anden placering og ressourcegruppe som VPN gatewayen. I dette eksempel vises dem i forskellige grupper, men i den samme Azure placering.

- Hvis der er kun én lokale VPN-enhed, som vist ovenfor, kan forbindelsen aktiv-aktiv arbejde med eller uden BGP protocol. I dette eksempel bruges BGP for tværs lokale forbindelsen.

- Hvis BGP er aktiveret, er det præfiks, du vil erklære af lokale netværk gatewayen host adressen på din BGP peer-IP-adresse på enheden VPN. I dette tilfælde er det en /32 præfikset "10.52.255.253/32".

- Som en påmindelse, skal du bruge forskellige BGP ASN'er mellem dit lokale netværk og Azure VNet. Hvis det er den samme, skal du ændre din VNet ASN, hvis din lokale VPN-enhed allerede bruger ASN til peer med andre BGP tilstødende netværk.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. oprette gatewayen lokale netværk til Site5
    
Kontrollér, at du stadig har forbindelse til abonnement 1, før du fortsætter. Oprette ressourcegruppen, hvis det ikke er endnu har oprettet.

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Trin 2 – forbinde VNet gateway og lokale netværksgateway

#### <a name="1-get-the-two-gateways"></a>1. få to gateways

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Opret TestVNet1 til Site5 forbindelse

I dette trin skal oprette du forbindelse fra TestVNet1 til Site5_1 med "EnableBGP", som er angivet til $True.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN og BGP parametre til din lokale VPN-enhed

I eksemplet nedenfor viser, hvilke parametre, du træder i sektionen BGP konfiguration på din lokale VPN-enhed til denne opgave:

    - Site5 ASN: 65050
    - IP-Site5 BGP: 10.52.255.253
    - Præfikser, der skal oplyse: (for eksempel) 10.51.0.0/16 og 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 for tunnel til 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 for tunnel til 138.91.156.129
    - Statisk omdirigerer: Destination 10.12.255.4/32, nexthop VPN-tunnel brugergrænseflade til 40.112.190.5 Destination 10.12.255.5/32, nexthop VPN-tunnel brugergrænseflade til 138.91.156.129
    - eBGP Multispring blev: sikre, at indstillingen "Multispring blev" af eBGP er aktiveret på din enhed, hvis det er nødvendigt

Forbindelsen skal oprettes efter et par minutter, og BGP peering sessionen starter, når der er oprettet forbindelse IPsec. I dette eksempel har indtil videre konfigureret kun én lokale VPN-enhed, hvilket resulterer i et diagram, der er vist nedenfor:

![aktiv-aktiv-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Trin 3 – tilsluttes gatewayen aktiv-aktiv VPN to lokale VPN-enheder

Hvis du har to VPN-enheder på det samme lokale netværk, kan du opnå dobbelt redundans ved at oprette forbindelse Azure VPN gatewayen til den anden VPN-enhed.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. oprette gatewayen anden lokale netværk til Site5

Bemærk, at gatewayen IP-adresse, adressepræfiks og BGP peering adressen til den anden lokale netværksgateway ikke skal overlapper med forrige lokale netværksgateway for det samme lokale netværk. 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. forbinde gatewayen VNet og den anden lokale netværksgateway

Oprette forbindelse fra TestVNet1 til Site5_2 med "EnableBGP", som er angivet til $True

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN og BGP parametre for din anden lokale VPN-enhed

På samme måde, under lister parametrene du træder i den anden VPN-enhed:

    - Site5 ASN: 65050
    - IP-Site5 BGP: 10.52.255.254
    - Præfikser, der skal oplyse: (for eksempel) 10.51.0.0/16 og 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP 1: 10.12.255.4 for tunnel til 40.112.190.5
    - Azure VNet BGP IP 2: 10.12.255.5 for tunnel til 138.91.156.129
    - Statisk omdirigerer: Destination 10.12.255.4/32, nexthop VPN-tunnel brugergrænseflade til 40.112.190.5 Destination 10.12.255.5/32, nexthop VPN-tunnel brugergrænseflade til 138.91.156.129
    - eBGP Multispring blev: sikre, at indstillingen "Multispring blev" af eBGP er aktiveret på din enhed, hvis det er nødvendigt

Når der er oprettet forbindelse (tunneler), får du to overflødige VPN-enheder og tunneler forbinder dit lokale netværk og Azure:

![to-redundans-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Del 3 – oprette en aktiv-aktiv VNet-VNet forbindelse

Dette afsnit opretter en aktiv-aktiv VNet-VNet forbindelse med BGP. 

Vejledningen nedenfor for fortsætter fra de forrige trin, der er nævnt ovenfor. Du skal fuldføre [del 1](#aagateway) for at oprette og konfigurere TestVNet1 og gatewayen VPN med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Trin 1 – oprette TestVNet2 og gatewayen VPN

Det er vigtigt at sikre dig, at IP-adresseområder for det nye virtuelle netværk og TestVNet2, ikke overlapper med en af dine VNet områder.

I dette eksempel tilhører virtuelle netværk samme abonnement. Du kan konfigurere VNet-VNet forbindelser mellem forskellige abonnementer Se [konfigurere en VNet-VNet forbindelse](./vpn-gateway-vnet-vnet-rm-ps.md) for at få mere at vide. Sørg for, at du har tilføjet den "-EnableBgp $True" ved oprettelse af forbindelser for at aktivere BGP.

#### <a name="1-declare-your-variables"></a>1. erklære dine variabler

Sørg for at erstatte værdierne med dem, du vil bruge til din konfiguration.

    $RG2           = "TestAARG2"
    $Location2     = "East US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Opret TestVNet2 i den nye ressourcegruppe

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. oprette gatewayen aktiv-aktiv VPN til TestVNet2

Anmode om to offentlige IP-adresser skal allokeres til gatewayen, du vil oprette for din VNet. Du kan også definere undernet og IP-konfigurationer, der er påkrævet. 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Oprette gatewayen VPN med som tal og flaget "EnableActiveActiveFeature". Bemærk, at du skal tilsidesætte standard ASN på din Azure VPN gateways. ASN'er for den forbundne VNets skal være forskellige til at aktivere BGP og distribution af overførsel.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Trin 2 – forbinde TestVNet1 og TestVNet2 gateways

I dette eksempel er begge gateways i samme-abonnement. Du kan udføre dette trin i den samme PowerShell-session.

#### <a name="1-get-both-gateways"></a>1. få begge gateways

Kontrollér, at du logge på og oprette forbindelse til abonnement 1.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. Opret begge forbindelser

I dette trin skal oprette du forbindelse mellem TestVNet1 og TestVNet2, og forbindelsen fra TestVNet2 til TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Sørg for at aktivere BGP for begge forbindelser.

Når du har fuldført disse trin, forbindelsen fastlægger i et par minutter, og BGP bliver peering session op, når VNet-VNet forbindelsen er færdig med to redundans:

![aktiv-aktiv-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Del 4 – Opdater eksisterende gateway mellem aktiv-aktiv og aktiv standby

Det sidste afsnit beskriver, hvordan du kan konfigurere en eksisterende Azure VPN-gateway fra aktiv standby til aktiv-aktiv tilstand eller omvendt.

>[AZURE.IMPORTANT] Bemærk, at den aktive aktive tilstand kun fungerer i HighPerformance SKU

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Konfigurere en aktiv standby gateway til aktiv-aktiv gateway

#### <a name="1-gateway-parameters"></a>1. gateway parametre

I følgende eksempel konverterer en aktiv standby gateway til en aktiv-aktiv gateway. Du skal oprette en anden offentlige IP-adresse og derefter tilføje en anden Gateway IP-konfiguration. Viser de parametre, bruges nedenfor:

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Opret den offentlige IP-adresse og derefter tilføje den anden gateway IP-konfiguration

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. aktivere aktiv-aktiv tilstand og Opdater gatewayen

Du skal indstille objektet gateway i PowerShell til at udløse den faktiske opdatering. SKU for objektet gateway skal også ændres til ved anvendelse, siden den blev oprettet tidligere som Standard.

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

Denne opdatering kan tage 30-45 minutter.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Konfigurere en aktiv-aktiv gateway til aktiv standby gateway

#### <a name="1-gateway-parameters"></a>1. gateway parametre

Bruge de samme parametre som ovenfor, finder du navnet på den IP-konfiguration, du vil fjerne.

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. fjerne gateway IP-konfiguration og deaktivere tilstanden aktiv-aktiv

På samme måde, skal du angive objektet gateway i PowerShell til at udløse den faktiske opdatering.

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

Denne opdatering kan tage op til 30 og 45 minutter.


## <a name="next-steps"></a>Næste trin

Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.

