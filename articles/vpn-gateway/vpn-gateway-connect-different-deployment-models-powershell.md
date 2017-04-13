<properties 
   pageTitle="Sådan oprettes forbindelse klassisk virtuelle netværk til Ressourcestyring virtuelle netværk ved hjælp af PowerShell | Microsoft Azure"
   description="Lær, hvordan du opretter en VPN-forbindelse mellem klassisk VNets og ressourcestyring VNets ved hjælp af VPN-Gateway og PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Forbind virtuelle netværk fra forskellige installation modeller ved hjælp af PowerShell

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Azure aktuelt har to management-modeller: Klassisk og ressource Manager (Ressourcestyring). Hvis du har brugt Azure i et stykke tid, har du sandsynligvis Azure FOS og forekomst roller, der kører i et klassisk VNet. Din nyere FOS og rolle forekomster kører muligvis i en VNet, der er oprettet i ressourcestyring. I denne artikel vejledes du gennem forbindelse klassisk VNets til Ressourcestyring VNets tillade de ressourcer, der er placeret i separate installation modellerne til at kommunikere med hinanden via en gateway-forbindelse.

Du kan oprette en forbindelse mellem VNets, der er i forskellige abonnementer og i forskellige områder. Du kan også forbinde VNets, der allerede har forbindelser til lokale netværk, så længe den gateway, der er konfigureret med er dynamiske eller distribuere-baserede. Du kan finde flere oplysninger om VNet-VNet forbindelser, [VNet-VNet ofte stillede spørgsmål](#faq) i slutningen af denne artikel.

### <a name="deployment-models-and-methods-for-connecting-vnets-in-different-deployment-models"></a>Installation-modeller og metoder til at oprette forbindelse VNets i forskellige installation modeller

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Vi opdaterer den følgende tabel som nye artikler og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra tabellen.<br><br>

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="before-beginning"></a>Før du begynder

Følgende trin fører dig gennem de indstillinger, der er nødvendige for at konfigurere en dynamisk eller distribuere-baserede gateway for hver VNet og oprette en VPN-forbindelse mellem gateways. Denne konfiguration understøtter ikke statisk eller baseret på politik gateways.

### <a name="prerequisites"></a>Forudsætninger

 - Begge VNets er allerede blevet oprettet.
 - Adresseområder for VNets ikke overlapper med hinanden eller overlapper med en af de områder for andre forbindelser, der kan være tilsluttet gateways.
 - Du har installeret de nyeste PowerShell-cmdletter (1.0.2 eller nyere). Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger. Kontrollér, at du har installeret både Service Management (SM) og cmdlet'erne ressource Manager (Ressourcestyring). 

### <a name="exampleref"></a>Eksempelindstillinger for

Du kan bruge indstillingerne for eksempel som en reference.

**Klassisk VNet indstillinger**

VNet navn = ClassicVNet <br>
Placering = Vest US <br>
Virtuelt netværk adresse mellemrum = 10.0.0.0/24 <br>
Undernet 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Lokale Network Name = RMVNetLocal <br>
GatewayType = DynamicRouting

**Ressourcestyring VNet indstillinger**

VNet navn = RMVNet <br>
Ressourcegruppe = RG1 <br>
Virtuelt netværk IP-adresseområder = 192.168.0.0/16 <br>
Undernet 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Placering = af US <br>
Offentlige IP-gatewaynavn = gwpip <br>
Lokale netværksgateway = ClassicVNetLocal <br>
Virtuelt netværk gatewaynavn = RMGateway <br>
Gatewayen IP-adresse konfiguration = gwipconfig


## <a name="createsmgw"></a>Afsnit 1 - konfigurere klassisk VNet

### <a name="part-1---download-your-network-configuration-file"></a>Del 1 – Download konfigurationsfil dit netværk

1. Log på din Azure-konto i PowerShell console med administratorrettigheder. Følgende cmdlet beder dig om logonoplysninger for din Azure-konto. Når du logger på, henter den dine kontoindstillinger, så de er tilgængelige til Azure PowerShell. Du skal bruge SM PowerShell-cmdlet'er til at udføre denne del af konfigurationen.

        Add-AzureAccount

2. Eksportere dine Azure netværk konfigurationsfil ved at køre følgende kommando. Du kan ændre placeringen af filen for at eksportere til en anden placering, hvis det er nødvendigt. Du kan redigere filen og derefter importere den til Azure.

        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml

3. Åbn den .xml-fil, du har hentet for at redigere den. Du kan finde et eksempel på konfigurationsfil netværk, [Netværk konfiguration skemaet](https://msdn.microsoft.com/library/jj157100.aspx).
 

### <a name="part-2--verify-the-gateway-subnet"></a>Del 2 - bekræfte gateway-undernet

Tilføj en gateway-undernet til din VNet i elementet **VirtualNetworkSites** , hvis der ikke allerede er blevet oprettet. Når du arbejder med konfigurationsfil netværk, gateway-undernet skal navngives "GatewaySubnet" eller Azure ikke kan genkende og bruge det som en gateway-undernet.

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Eksempel:**
    
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### <a name="part-3---add-the-local-network-site"></a>Del 3 – Tilføj webstedet lokale netværk

Webstedet lokale netværk, du har tilføjet repræsenterer den Ressourcestyring VNet, du vil oprette forbindelse. Du kan være nødvendigt at tilføje et **LocalNetworkSites** element til fil, hvis den ikke allerede findes. På dette tidspunkt i sektionen konfiguration kan på VPNGatewayAddress skyldes en hvilken som helst gyldigt offentlige IP-adresse vi endnu ikke har oprettet gatewayen for ressourcestyring VNet. Når vi oprette gatewayen, Erstat vi denne pladsholder IP-adresse med den korrekte offentlige IP-adresse, der er tildelt til Ressourcestyring gatewayen.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="part-4---associate-the-vnet-with-the-local-network-site"></a>Del 4 – Tilknyt VNet med lokale netværk-webstedet

I dette afsnit skal Angiv vi det lokale netværk websted, du vil forbinde VNet til. I dette tilfælde er det det ressourcestyring VNet, der refereres til tidligere. Sørg for, at navnene svarer. Dette trin oprette ikke en gateway. Det angiver det lokale netværk, som gatewayen opretter forbindelse til.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="part-5---save-the-file-and-upload"></a>Del 5 – gemme filen, og Overfør

Gem filen, og derefter importere den til Azure ved at køre følgende kommando. Sørg for, at du ændrer stien til filen efter behov for dit miljø.

        Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml

Du bør se noget lignende dette resultat, der viser, at importen blev fuldført.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="part-6---create-the-gateway"></a>Del 6 – Opret en gateway 

Du kan oprette gatewayen VNet, enten ved hjælp af portalen klassisk eller ved hjælp af PowerShell.

Brug eksemplet nedenfor til at oprette en dynamisk routing gateway:

    New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting

Du kan kontrollere status for gateway ved hjælp af den `Get-AzureVNetGateway` cmdlet.


## <a name="creatermgw"></a>Afsnit 2: Konfigurere gatewayen Ressourcestyring VNet

For at oprette en VPN-gateway til Ressourcestyring VNet, skal du følge nedenstående vejledning. Trinnene, indtil ikke starte, når du har hentet klassisk VNet gateway offentlige IP-adresse. 

1. **Log på din Azure-konto** i PowerShell console. Følgende cmdlet beder dig om logonoplysninger for din Azure-konto. Når du logger på, hentes dine kontoindstillinger, så de er tilgængelige til Azure PowerShell.

        Login-AzureRmAccount 

    Få en liste over dine Azure abonnementer, hvis du har mere end ét abonnement.

        Get-AzureRmSubscription

    Angiv det abonnement, du vil bruge. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Opret en gateway lokale netværk**. Et virtuelt netværk og refererer gatewayen lokale netværk typisk til din lokale placering. I dette tilfælde henviser gatewayen lokale netværk til din klassisk VNet. Give den et navn, som Azure kan referere til den og også angive præfikset adresse mellemrum. Azure bruger præfikset IP-adresse, du angiver for at identificere, hvilke trafik til at sende til din lokale placering. Hvis du vil justere oplysningerne her senere, før du opretter din gateway, kan du ændre værdierne og køre eksemplet igen.<br><br>
    - `-Name`er det navn, du vil tildele til at referere til gatewayen lokale netværk.<br>
    - `-AddressPrefix`er det adresseområde for din klassisk VNet.<br>
    - `-GatewayIpAddress`er den offentlige IP-adresse på klassisk VNet gateway. Sørg for at ændre i følgende eksempel afspejler den korrekte IP-adresse.
    
            New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
            -Location "West US" -AddressPrefix "10.0.0.0/24" `
            -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Anmode om en offentlig IP-adresse** skal allokeres til gatewayen virtuelt netværk for ressourcestyring VNet. Du kan ikke angive den IP-adresse, du vil bruge. IP-adressen er dynamisk allokeret til gatewayen virtuelt netværk. Det betyder dog ikke IP-adressen ændres. Den eneste gang ændres virtuelt netværk gateway IP-adresse er, når gatewayen er slettet og genoprettet. Den ændrer ikke på tværs af ændre størrelsen på, hvis du nulstiller eller andre interne vedligeholdelse/opgraderinger af gatewayen.<br>I dette trin skal angive vi også en variabel, der bruges på et senere tidspunkt.


        $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
        -ResourceGroupName RG1 -Location 'EastUS' `
        -AllocationMethod Dynamic

4. **Bekræft, at netværket virtuel har en gateway-undernet**. Hvis der findes ingen gateway-undernet, kan du tilføje en. Kontrollér, at gateway-undernettet hedder *GatewaySubnet*.

5. **Hente undernettet** bruges til gatewayen ved at køre følgende kommando. I dette trin skal angive vi også en variabel skal bruges i næste trin.
    - `-Name`er navnet på din ressourcestyring VNet.
    - `-ResourceGroupName`er den ressourcegruppe, som er tilknyttet VNet. Gateway-undernet skal allerede findes for denne VNet og skal navngives *GatewaySubnet* kan fungere korrekt.


            $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
            -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Opret gateway IP-adresse konfigurationen**. Konfigurationen af gatewayen definerer undernettet og den offentlige IP-adresse til brug. Brug følgende eksempel til at oprette konfigurationen af gatewayen.<br>I dette trin skal den `-SubnetId` og `-PublicIpAddressId` parametre skal overføres egenskaben id fra undernet og IP-adresse objekter, henholdsvis. Du kan ikke bruge en simpel streng. Disse variabler er angivet i trin til at anmode om en offentlige IP- og trinnet til at hente undernettet.

        $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
        -Name gwipconfig -SubnetId $subnet.id `
        -PublicIpAddressId $ipaddress.id
    
7. **Oprette gatewayen ressourcestyring virtuelt netværk** ved at køre følgende kommando. Den `-VpnType` skal være *RouteBased*. Det kan tage 45 minutter eller mere at fuldføre.

        New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
        -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
        -IpConfigurations $gwipconfig `
        -EnableBgp $false -VpnType RouteBased

8. **Kopiere den offentlige IP-adresse** én gang VPN gatewayen er blevet oprettet. Du kan bruge, når du konfigurerer indstillingerne for din klassisk VNet lokale netværk. Du kan bruge følgende cmdlet til at hente den offentlige IP-adresse. Den offentlige IP-adresse er angivet i afkastet som *IP-adresse*.

        Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Afsnit 3: Ændre det lokale netværk for klassisk VNet

Du kan udføre dette trin, enten ved at eksportere konfigurationsfil netværk, redigere den, og derefter gemme og importere filen tilbage til Azure. Eller du kan ændre denne indstilling i portalen klassisk. 

### <a name="to-modify-in-the-portal"></a>Ændre i portalen

1. Åbn [Klassisk portal](https://manage.windowsazure.com).

2. Rul ned til venstre i klassisk portal, og klik på **netværk**. Klik på **Lokalnetværk** øverst på siden, på siden **netværk** . 

3. Klik på det lokale netværk, du konfigureret i del 1, og derefter gå til bunden af siden, og klik på **Rediger**på siden **Lokalnetværk** .

4. Erstat pladsholder IP-adressen på siden **Angiv dit lokale netværk oplysninger** med den offentlige IP-adressen til gatewayen Ressourcestyring, du oprettede i forrige afsnit. Klik på pilen til at flytte til næste afsnit. Bekræft, at **Adresseområde** er korrekte, og klik derefter på markering for at acceptere ændringerne.

### <a name="to-modify-using-the-network-configuration-file"></a>Ændre ved hjælp af netværk konfigurationsfil

1. Eksportere konfigurationsfil netværk.
2. Ændre VPN gateway IP-adressen i et tekstredigeringsprogram.

        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Gemme ændringerne og importere den redigerede fil tilbage til Azure.


## <a name="connect"></a>Del 4: Oprette en forbindelse mellem gateways

Oprette en forbindelse mellem gateways kræver PowerShell. Du muligvis tilføje din Azure-konto for at bruge de klassiske PowerShell-cmdletter. For at gøre det, kan du bruge følgende cmdlet: 
        
    Add-AzureAccount

1. **Angive produktnøglen på delt** ved at køre følgende kommando. I dette eksempel `-VNetName` er navnet på den klassisk VNet og `-LocalNetworkSiteName` er det navn, du har angivet for det lokale netværk, når du har konfigureret det i portalen klassisk. Den `-SharedKey` er en værdi, du kan oprette og angive. Den værdi, du angiver her, skal være den samme værdi, du angiver i næste trin, når du opretter din forbindelse. Vend tilbage til dette eksempel bør vise **Status: vellykket**.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Oprette VPN-forbindelse ved at køre følgende kommandoer.

    **Angive variablerne**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Oprette forbindelse**<br> Bemærk, at den `-ConnectionType` er IPsec, ikke Vnet2Vnet.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Du kan få vist din forbindelse, i enten portal eller ved at bruge den `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>VNet-VNet ofte stillede spørgsmål

Se ofte stillede spørgsmål om detaljerne for yderligere oplysninger om VNet-VNet forbindelser.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


