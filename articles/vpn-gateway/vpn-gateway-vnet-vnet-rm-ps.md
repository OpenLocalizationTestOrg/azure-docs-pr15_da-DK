<properties
   pageTitle="Oprette forbindelse Azure VNets med VPN-Gateway og PowerShell | Microsoft Azure"
   description="I denne artikel vejledes du gennem opretter forbindelse til virtuelle netværk sammen ved hjælp af Azure ressourcestyring og PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-vnet-to-vnet-connection-for-resource-manager-using-powershell"></a>Konfigurere en VNet-VNet forbindelse til Ressourcestyring ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Ressourcestyring - Azure-portalen](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Ressourcestyring - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Klassisk - klassisk Portal](virtual-networks-configure-vnet-to-vnet-connection.md)

I denne artikel fører dig gennem trinnene til at oprette en forbindelse mellem VNets i ressourcestyring implementeringsmodel ved hjælp af VPN-Gateway. De virtuelle netværk kan være i samme eller forskellige områder og fra de samme eller forskellige abonnementer.


![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Installation-modeller og metoder til VNet-VNet forbindelser

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Følgende tabel viser de tilgængelige installation modeller og metoder til VNet-VNet konfigurationer. Når en artikel med konfigurationstrinnene er tilgængelig, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="about-vnet-to-vnet-connections"></a>Om VNet-VNet forbindelser

Oprette forbindelse et virtuelt netværk til en anden virtuelt netværk minder (VNet til VNet) om forbindelse en VNet til en lokal placering. Begge typer connectivity bruges en VPN-Azure gateway for at give en sikker tunnel ved hjælp af IPsec/IKE. Den VNets, du opretter forbindelse kan være i forskellige områder. Eller i forskellige abonnementer. Du kan også kombinere VNet-VNet kommunikation med flere websted konfigurationer. På denne måde kan du oprette netværkstopologier, der kombinerer tværs lokale-forbindelse med mellem virtuelle netværksforbindelse, som vist i følgende diagram:


![Om forbindelser](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### <a name="why-connect-virtual-networks"></a>Hvorfor oprette forbindelse virtuelle netværk?

Vil du måske at oprette forbindelse virtuelle netværk af følgende årsager:

- **Cross geografisk område-redundans og geografisk tilstedeværelse**
    - Du kan konfigurere din egen geografisk gentagelse eller synkronisering med sikre forbindelser uden at gå over internettet slutpunkter.
    - Med Azure trafik overordnet og justering af belastning, kan du konfigurere meget tilgængelige arbejdsprocess med geografisk redundans på tværs af flere Azure områder. Et vigtigt eksempel er til konfiguration af SQL altid på med tilgængelighed grupper, der strækker sig over flere Azure områder.

- **Internationale med flere lag programmer med isolationsniveauet eller administrative rammen**
    - I det samme region, kan du konfigurere med flere lag programmer med flere virtuelle netværk, der er forbundet med hinanden på grund af isolationsniveauet eller administrative krav.


### <a name="vnet-to-vnet-faq"></a>VNet-VNet ofte stillede spørgsmål

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


## <a name="which-set-of-steps-should-i-use"></a>Hvilke trin skal jeg bruge?

I denne artikel kan se du to forskellige typer trin. En række trin til [VNets, der er placeret i samme abonnement](#samesub)og en anden til [VNets, der er placeret forskellige abonnementer](#difsub). Vigtige forskellen mellem sæt er, om du kan oprette og konfigurere alle virtuelt netværk og gateway ressourcer i den samme PowerShell-session.

Trinnene i denne artikel bruge variabler, som er defineret i begyndelsen af hver sektion. Hvis du allerede arbejder med eksisterende VNets, kan du ændre variabler afspejler indstillingerne i dit eget miljø. 

![Begge forbindelser](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


## <a name="samesub"></a>Sådan opretter du forbindelse VNets, der er i samme-abonnement

![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Inden du går i gang
    
Før du begynder skal du installere Azure ressourcestyring PowerShell-cmdletterne. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

### <a name="Step1"></a>Trin 1 – planlægge din IP-adresseområder


I de følgende trin, kan vi oprette to virtuelle netværk sammen med deres respektive gateway undernet og konfigurationer. Vi derefter oprette en VPN-forbindelse mellem de to VNets. Det er vigtigt at planlægge IP-adresseområder for din netværkskonfiguration. Husk på, skal du sikre dig, at ingen af din VNet områder eller lokale netværk områder overlapper på nogen måde.

Vi bruger følgende værdier i eksempler:

**Værdier for TestVNet1:**

- VNet navn: TestVNet1
- Ressourcegruppe: TestRG1
- Placering: Af USA
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- Front end: 10.11.0.0/24
- Backend-version: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- DNS-Server: 8.8.8.8
- GatewayName: VNet1GW
- Offentlige IP-adresse: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Værdier for TestVNet4:**

- VNet navn: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- Front end: 10.41.0.0/24
- Backend-version: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- Ressourcegruppe: TestRG4
- Placering: Vest USA
- DNS-Server: 8.8.8.8
- GatewayName: VNet4GW
- Offentlige IP-adresse: VNet4GWIP
- VPNType: RouteBased
- Forbindelse: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name="Step2"></a>Trin 2 – oprette og konfigurere TestVNet1

1. Definere dine variabler

    Starte med at erklære variabler. I dette eksempel erklærer variabler ved hjælp af værdierne i denne opgave. I de fleste tilfælde, skal du erstatte værdierne med dine egne. Du kan bruge disse variabler, hvis du kører gennemgå trinnene til at blive fortrolig med denne type konfiguration. Ændre variablerne, hvis det er nødvendigt, og derefter kopiere og indsætte dem i din PowerShell console.

        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"

2. Oprette forbindelse til dit abonnement

    Skifte til PowerShell tilstand at bruge-cmdletter ressourcestyring. Åbn din PowerShell console og oprette forbindelse til din konto. Bruge eksemplet nedenfor til at hjælpe dig med at oprette forbindelse:

        Login-AzureRmAccount

    Markér abonnementer til kontoen.

        Get-AzureRmSubscription 

    Angiv det abonnement, du vil bruge.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Oprette en ny ressourcegruppe

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Oprette undernet konfigurationer for TestVNet1

    I dette eksempel opretter et virtuelt netværk med navnet TestVNet1 og tre undernet, én kaldet GatewaySubnet, én kaldet FrontEnd og én kaldet back end. Ved erstatning med værdier, er det vigtigt, at du altid navngive din gateway-undernet specifikt GatewaySubnet. Hvis du navngive den noget andet, mislykkes din gateway oprettelse. 

    I følgende eksempel bruges de variabler, du tidligere har angivet. I dette eksempel bruger gateway-undernet en /27. Selvom det er muligt at oprette en gateway-undernet så lille som /29, anbefaler vi, at du opretter et større undernet, der indeholder flere adresser ved at vælge mindst /28 eller /27. Dette giver mulighed for nok adresser til at medtage yderligere konfigurationer, der kan du i fremtiden. 

        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Oprette TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Anmode om en offentlig IP-adresse

    Anmode om en offentlig IP-adresse skal allokeres til gatewayen, du vil oprette for din VNet. Bemærk, at AllocationMethod dynamisk. Du kan ikke angive den IP-adresse, du vil bruge. Det er dynamisk allokeret til din gateway. 

        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic

7. Oprette gateway-konfiguration

    Konfigurationen af gatewayen definerer undernettet og den offentlige IP-adresse til brug. Bruge eksemplet til at oprette konfigurationen af gatewayen. 

        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Oprette gatewayen til TestVNet1

    I dette trin skal oprette du gatewayen virtuelt netværk for din TestVNet1. VNet-VNet konfigurationer kræver en RouteBased VpnType. Oprette en gateway kan tage et stykke tid (45 minutter eller mere for at fuldføre).

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>Trin 3 – oprette og konfigurere TestVNet4

Når du har konfigureret TestVNet1, kan du oprette TestVNet4. Følg trinnene nedenfor, erstatter værdierne med dine egne, når det er nødvendigt. Dette trin kan udføres i den samme PowerShell-session, fordi det er i samme-abonnement.

1. Definere dine variabler

    Sørg for at erstatte værdierne med dem, du vil bruge til din konfiguration.

        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"

    Før du fortsætter, skal du kontrollere du stadig har forbindelse til abonnement 1.

2. Oprette en ny ressourcegruppe

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Oprette undernet konfigurationer for TestVNet4

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Oprette TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Anmode om en offentlig IP-adresse

        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic

6. Oprette gateway-konfiguration

        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Oprette gatewayen TestVNet4

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>Trin 4 – forbinde gateways

1. Få begge virtuelt netværk gateways

    I dette eksempel, fordi begge gateways er i samme abonnement kan dette trin udføres i den samme PowerShell-session.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Oprette TestVNet1 til TestVNet4 forbindelse

    I dette trin skal oprette du forbindelse fra TestVNet1 til TestVNet4. Du får vist en delt nøgle, der refereres til i eksemplerne. Du kan bruge dit eget værdier til den delte nøgle. Det vigtigste er, at den delte nøgle skal til for begge forbindelser. Oprette en forbindelse, kan det tage et øjeblik at fuldføre.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Oprette TestVNet4 til TestVNet1 forbindelse

    Dette trin er svarer til den, ovenfor, undtagen du opretter forbindelsen fra TestVNet4 til TestVNet1. Kontrollere de delte nøgler svarer.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    Forbindelsen skal oprettes efter et par minutter.

4. Kontrollér din forbindelse. I afsnittet [Sådan kontrolleres forbindelsen](#verify).


## <a name="difsub"></a>Sådan opretter du forbindelse VNets, der findes i forskellige-abonnementer


![v2v diagram](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

I dette scenarie skal vi oprette forbindelse TestVNet1 og TestVNet5. TestVNet1 og TestVNet5 er placeret i et andet abonnement. Fremgangsmåden for denne konfiguration Føj en ekstra VNet-VNet forbindelse for at oprette forbindelse TestVNet1 til TestVNet5. 

Forskellen er, at nogle af trinnene til konfiguration skal udføres i en separat PowerShell-session i forbindelse med det andet abonnement. Især når de to abonnementer tilhører forskellige organisationer. 

Vejledningen fortsætter fra de forrige trin, der er nævnt ovenfor. Du skal fuldføre [trin 1](#Step1) og [trin 2](#Step2) for at oprette og konfigurere TestVNet1 og gatewayen VPN til TestVNet1. Når du udfører trin 1 og 2 for trin, skal du fortsætte med trin 5 for at oprette TestVNet5.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>Trin 5 – bekræfte yderligere IP-adresseområder

Det er vigtigt at sikre dig, at IP-adresseområder for det nye virtuelle netværk og TestVNet5, ikke overlapper med en af dine VNet områder eller lokale netværk gateway områder. 

I dette eksempel kan de virtuelle netværk tilhører forskellige organisationer. Du kan bruge følgende værdier for TestVNet5 for denne opgave:

**Værdier for TestVNet5:**

- VNet navn: TestVNet5
- Ressourcegruppe: TestRG5
- Placering: Japan øst
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- Front end: 10.51.0.0/24
- Backend-version: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- DNS-Server: 8.8.8.8
- GatewayName: VNet5GW
- Offentlige IP-adresse: VNet5GWIP
- VPNType: RouteBased
- Forbindelse: VNet5toVNet1
- ConnectionType: VNet2VNet

**Flere værdier for TestVNet1:**

- Forbindelse: VNet1toVNet5


### <a name="step-6---create-and-configure-testvnet5"></a>Trin 6 - oprette og konfigurere TestVNet5

Dette trin skal udføres i forbindelse med det nye abonnement. Denne del kan udføres af administratoren i en anden organisation, der ejer abonnementet.

1. Definere dine variabler

    Sørg for at erstatte værdierne med dem, du vil bruge til din konfiguration.

        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"

2. Oprette forbindelse til abonnement 5

    Åbn din PowerShell console og oprette forbindelse til din konto. Brug følgende eksempel til at hjælpe dig med at oprette forbindelse:

        Login-AzureRmAccount

    Markér abonnementer til kontoen.

        Get-AzureRmSubscription 

    Angiv det abonnement, du vil bruge.

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. Oprette en ny ressourcegruppe

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Oprette undernet konfigurationer for TestVNet4
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Oprette TestVNet5

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Anmode om en offentlig IP-adresse

        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic


7. Oprette gateway-konfiguration

        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Oprette gatewayen TestVNet5

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>Trin 7 - forbindelse gateways

I dette eksempel, fordi gateways er i de forskellige abonnementer, har vi Opdel dette trin i to PowerShell-sessioner er markeret som [abonnement 1] og [abonnement 5].

1. **[Abonnement 1]** Få gatewayen virtuelt netværk til abonnement 1

    Kontrollér, at du logge på og oprette forbindelse til abonnement 1.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    Kopiere output fra følgende elementer og sende dem til administratoren af abonnement 5 via mail eller en anden metode.

        $vnet1gw.Name
        $vnet1gw.Id

    Disse to elementer har værdier, der ligner følgende eksempel output:

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Abonnement 5]** Få gatewayen virtuelt netværk til abonnement 5

    Kontrollér, at du logge på og oprette forbindelse til abonnement 5.

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    Kopiere output fra følgende elementer og sende dem til administratoren af abonnement 1 via mail eller en anden metode.

        $vnet5gw.Name
        $vnet5gw.Id

    Disse to elementer har værdier, der ligner følgende eksempel output:

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Abonnement 1]** Oprette TestVNet1 til TestVNet5 forbindelse

    I dette trin skal oprette du forbindelse fra TestVNet1 til TestVNet5. Forskellen er, $vnet5gw ikke kunne hentes direkte, fordi det er i et andet abonnement. Du skal oprette et nyt PowerShell-objekt med de værdier, der har kommunikeret fra abonnement 1 i ovenstående trin. Brug eksemplet nedenfor. Erstatte det navn, Id og delte nøgle med dine egne værdier. Det vigtigste er, at den delte nøgle skal til for begge forbindelser. Oprette en forbindelse, kan det tage et øjeblik at fuldføre.

    Kontrollér, at du opretter forbindelse til abonnement 1. 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Abonnement 5]** Oprette TestVNet5 til TestVNet1 forbindelse

    Dette trin er svarer til den, ovenfor, undtagen du opretter forbindelsen fra TestVNet5 til TestVNet1. Gælder her samt den samme fremgangsmåde for at oprette en PowerShell-objekt, der er baseret på de værdier, der fås fra abonnement 1. I dette trin skal du sørge for, at de delte nøgler svarer til.

    Kontrollér, at du opretter forbindelse til abonnement 5.

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Sådan kontrolleres en forbindelse


[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="next-steps"></a>Næste trin

- Når forbindelsen er fuldført, kan du føje virtuelle maskiner til dine virtuelle netværk. Se [oprette en virtuel maskine](../virtual-machines/virtual-machines-windows-hero-tutorial.md) for trin.
- Se oplysninger om BGP [BGP oversigt](vpn-gateway-bgp-overview.md) og [hvordan du konfigurerer BGP](vpn-gateway-bgp-resource-manager-ps.md). 

