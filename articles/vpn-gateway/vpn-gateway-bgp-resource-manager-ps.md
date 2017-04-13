<properties
   pageTitle="Hvordan du konfigurerer BGP på Azure VPN Gateways ved hjælp af Azure ressourcestyring og PowerShell | Microsoft Azure"
   description="I denne artikel vejleder dig gennem konfigurationen af BGP med Azure VPN Gateways ved hjælp af Azure ressourcestyring og PowerShell."
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
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Hvordan du konfigurerer BGP på Azure VPN Gateways ved hjælp af Azure ressourcestyring og PowerShell

I denne artikel fører dig gennem trinnene til at aktivere BGP på en tværs lokale websted-til-websted (S2S) VPN-forbindelse og en VNet-VNet forbindelse ved hjælp af Ressourcestyring implementeringsmodel og PowerShell.


**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>Om BGP

BGP er den standard routing protokol, der bruges i internettet til at udveksle routing- og reachability oplysninger mellem to eller flere netværk. BGP aktiverer Azure VPN Gateways og dine lokale VPN-enheder, der kaldes BGP peers eller tilstødende netværk, til at udveksle "dirigerer", der oplyser begge gateways på tilgængelighed og adgangen til disse præfikser at gå gennem gateways eller routere involveret. BGP kan også aktivere overførsel routing mellem flere netværk på overførsel af omdirigerer en BGP gateway lærer fra én BGP peer til alle andre BGP peers.

Se [Oversigt over BGP med Azure VPN Gateways](./vpn-gateway-bgp-overview.md) til flere diskussion på fordele af BGP og at forstå de tekniske krav og overvejelser ved at bruge BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introduktion til BGP på Azure VPN gateways

I denne artikel fører dig gennem trinnene til at udføre følgende opgaver:

- [Del 1 – Aktivér BGP på din Azure VPN-gateway](#enablebgp)

- [Del 2 – oprette en tværs lokale forbindelse med BGP](#crossprembgp)

- [Del 3 – oprette en VNet-VNet forbindelse med BGP](#v2vbgp)

Hver del af vejledningen udgør en grundlæggende byggesten for aktivering BGP i din netværksforbindelse. Hvis du har fuldført alle tre dele, kan du oprette topologien, som vist i følgende diagram:

![BGP topologi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Du kan kombinere disse sammen for at opbygge en mere kompleks, med flere håb overførsel netværk, der opfylder dine behov.

## <a name ="enablebgp"></a>Del 1 – konfigurere BGP på gatewayen Azure VPN

De følgende trin til konfiguration installationen BGP parametrene for gatewayen Azure VPN, som vist i følgende diagram:

![BGP Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Inden du går i gang

- Kontrollér, at du har et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).
    
- Du skal installere Azure ressourcestyring PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

### <a name="step-1---create-and-configure-vnet1"></a>Trin 1 – oprette og konfigurere VNet1 

#### <a name="1-declare-your-variables"></a>1. erklære dine variabler

I denne opgave skal vi starter med erklære vores variabler. Eksemplet herunder erklærer variabler ved hjælp af værdierne i denne opgave. Sørg for at erstatte værdierne med dine egne, når du konfigurerer til fremstilling. Du kan bruge disse variabler, hvis du kører gennemgå trinnene til at blive fortrolig med denne type konfiguration. Ændre variablerne, og derefter kopiere og sætte ind i din PowerShell console.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
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
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Trin 2 – oprette gatewayen VPN til TestVNet1 med BGP parametre

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. oprette IP- og undernet konfigurationerne

Anmode om en offentlig IP-adresse skal allokeres til gatewayen, du vil oprette for din VNet. Du kan også definere undernet og IP-konfigurationer, der er påkrævet. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Opret gatewayen VPN med tal, som

Oprette gatewayen virtuelt netværk for TestVNet1. Bemærk, at BGP kræver en distribution baseret VPN-gateway, og parameteren tilføjelse, - Asn, til at angive ASN (som tal) for TestVNet1. Oprette en gateway kan tage et stykke tid (30 minutter eller mere for at fuldføre).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. få Azure BGP peer-IP-adresse

Når gatewayen er oprettet, skal du hente BGP peer-IP-adressen på gatewayen Azure VPN. Denne adresse er nødvendig for at konfigurere gatewayen Azure VPN som en BGP Peer til din lokale VPN-enheder.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

Den sidste kommando viser de tilsvarende BGP konfigurationer på Azure VPN-gatewayen. Eksempel:

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Når gatewayen er oprettet, kan du bruge denne gateway til at oprette tværs lokale eller VNet-VNet forbindelse med BGP. De følgende afsnit fører gennem trinnene for at fuldføre øvelsen.

## <a name ="crossprembbgp"></a>Del 2 – oprette en tværs lokale forbindelse med BGP

Hvis du vil oprette en forbindelse i tværs det lokale miljø, skal du oprette en lokal netværksgateway til at repræsentere din lokale VPN-enhed, og en forbindelse til at oprette forbindelse gatewayen Azure VPN med gatewayen lokale netværk. Forskellen mellem vejledningen i denne artikel er for yderligere egenskaber, der er påkrævet for at angive BGP konfiguration af parametrene.

![BGP til lokal kryds-installation](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Før du fortsætter, Kontroller, at du har fuldført [del 1](#enablebgp) af denne opgave.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Trin 1 – oprette og konfigurere gatewayen lokale netværk

#### <a name="1-declare-your-variables"></a>1. erklære dine variabler

Denne øvelse fortsætter med at opbygge konfigurationen vises i diagrammet. Sørg for at erstatte værdierne med dem, du vil bruge til din konfiguration.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Et par ting at være opmærksom på angående lokale netværk gateway parametre:

- Gatewayen lokale netværk kan være i samme eller en anden placering og ressourcegruppe som VPN gatewayen. I dette eksempel viser dem i forskellige grupper på forskellige placeringer.

- Det mindste præfiks, du vil erklære for gatewayen lokale netværk er host adressen på din BGP peer-IP-adresse på enheden VPN. I dette tilfælde er det en /32 præfikset "10.52.255.254/32".

- Som en påmindelse, skal du bruge forskellige BGP ASN'er mellem dit lokale netværk og Azure VNet. Hvis det er den samme, skal du ændre din VNet ASN, hvis din lokale VPN-enhed allerede bruger ASN til peer med andre BGP tilstødende netværk.
    
Kontrollér, at du stadig har forbindelse til abonnement 1, før du fortsætter.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. oprette gatewayen lokale netværk til Site5

Sørg for at oprette ressourcegruppen, hvis det ikke er oprettet, inden du opretter gatewayen lokale netværk. Bemærk de to yderligere parametre for gatewayen lokale netværk: Asn og BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Trin 2 – forbinde VNet gateway og lokale netværksgateway

#### <a name="1-get-the-two-gateways"></a>1. få to gateways

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Opret TestVNet1 til Site5 forbindelse

I dette trin skal oprette du forbindelse fra TestVNet1 til Site5. Du skal angive "-EnableBGP $True" til at aktivere BGP for denne forbindelse. Som beskrevet tidligere, er det muligt at have både BGP og ikke BGP forbindelser til den samme Azure VPN-Gateway. Medmindre BGP er aktiveret i egenskaben forbindelse, aktivere Azure ikke BGP for denne forbindelse selvom BGP parametre er allerede konfigureret på begge gateways.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


I eksemplet nedenfor viser, hvilke parametre, du træder i sektionen BGP konfiguration på din lokale VPN-enhed til denne opgave:

    - Site5 ASN: 65050
    - IP-Site5 BGP: 10.52.255.254
    - Præfikser, der skal oplyse: (for eksempel) 10.51.0.0/16 og 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP-adresse: 10.12.255.30
    - Statisk rute: tilføje en rute for 10.12.255.30/32, med nexthop der grænsefladen VPN tunnel på din enhed
    - eBGP Multispring blev: sikre, at indstillingen "Multispring blev" af eBGP er aktiveret på din enhed, hvis det er nødvendigt

Forbindelsen skal oprettes efter et par minutter, og BGP peering sessionen starter, når der er oprettet forbindelse IPsec.
 
## <a name ="v2vbgp"></a>Del 3 – oprette en VNet-VNet forbindelse med BGP

Dette afsnit tilføjer en VNet-VNet forbindelse med BGP, som vist i diagrammet nedenfor. 

![BGP for VNet-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Vejledningen nedenfor for fortsætter fra de forrige trin, der er nævnt ovenfor. Du skal fuldføre [del I](#enablebgp) for at oprette og konfigurere TestVNet1 og gatewayen VPN med BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Trin 1 – oprette TestVNet2 og gatewayen VPN

Det er vigtigt at sikre dig, at IP-adresseområder for det nye virtuelle netværk og TestVNet2, ikke overlapper med en af dine VNet områder.

I dette eksempel tilhører virtuelle netværk samme abonnement. Du kan konfigurere VNet-VNet forbindelser mellem forskellige abonnementer Se [konfigurere en VNet-VNet forbindelse](./vpn-gateway-vnet-vnet-rm-ps.md) for at få mere at vide. Sørg for, at du har tilføjet den "-EnableBgp $True" ved oprettelse af forbindelser for at aktivere BGP.

#### <a name="1-declare-your-variables"></a>1. erklære dine variabler

Sørg for at erstatte værdierne med dem, du vil bruge til din konfiguration.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
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
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Opret TestVNet2 i den nye ressourcegruppe

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. oprette gatewayen VPN til TestVNet2 med BGP parametre

Anmode om en offentlig IP-adresse skal allokeres til gatewayen, du vil oprette for din VNet. Du kan også definere undernet og IP-konfigurationer, der er påkrævet. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Oprette gatewayen VPN med tal, som. Bemærk, at du skal tilsidesætte standard ASN på din Azure VPN gateways. ASN'er for den forbundne VNets skal være forskellige til at aktivere BGP og distribution af overførsel.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Trin 2 – forbinde TestVNet1 og TestVNet2 gateways

I dette eksempel er begge gateways i samme-abonnement. Du kan udføre dette trin i den samme PowerShell-session.

#### <a name="1-get-both-gateways"></a>1. få begge gateways

Kontrollér, at du logger på og oprette forbindelse til abonnement 1.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. Opret begge forbindelser

I dette trin skal oprette du forbindelse mellem TestVNet1 og TestVNet2, og forbindelsen fra TestVNet2 til TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Sørg for at aktivere BGP for begge forbindelser.

Efter at udføre disse trin, forbindelsen være fastlægger i et par minutter, og BGP peering sessionen bliver op én gang er VNet-VNet forbindelsen færdig.

Hvis du har fuldført alle tre dele af denne opgave, vil du har oprettet en netværkstopologi som vist nedenfor:

![BGP for VNet-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Næste trin

Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.

