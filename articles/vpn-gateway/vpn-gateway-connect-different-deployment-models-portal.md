<properties 
   pageTitle="Sådan oprettes forbindelse klassisk virtuelle netværk til Ressourcestyring virtuelle netværk i portalen | Microsoft Azure"
   description="Lær, hvordan du opretter en VPN-forbindelse mellem klassisk VNets og ressourcestyring VNets ved hjælp af VPN-Gateway og portalen"
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
   ms.date="10/03/2016"
   ms.author="cherylmc" />

# <a name="connect-virtual-networks-from-different-deployment-models-in-the-portal"></a>Forbind virtuelle netværk fra forskellige installation modeller i portalen

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Azure aktuelt har to management-modeller: Klassisk og ressource Manager (Ressourcestyring). Hvis du har brugt Azure i et stykke tid, har du sandsynligvis Azure FOS og forekomst roller, der kører i et klassisk VNet. Din nyere FOS og rolle forekomster kører muligvis i en VNet, der er oprettet i ressourcestyring. I denne artikel vejledes du gennem forbindelse klassisk VNets til Ressourcestyring VNets tillade de ressourcer, der er placeret i separate installation modellerne til at kommunikere med hinanden via en gateway-forbindelse. 

Du kan oprette en forbindelse mellem VNets, der er i forskellige abonnementer og i forskellige områder. Du kan også forbinde VNets, der allerede har forbindelser til lokale netværk, så længe den gateway, der er konfigureret med er dynamiske eller distribuere-baserede. Du kan finde flere oplysninger om VNet-VNet forbindelser, [VNet-VNet ofte stillede spørgsmål](#faq) i slutningen af denne artikel.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Installation-modeller og metoder til VNet-VNet forbindelser

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Vi opdaterer den følgende tabel som nye artikler og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra tabellen.<br><br>

**VNet-VNet**

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 

#### <a name="vnet-peering"></a>VNet peering

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="before-beginning"></a>Før du begynder

Følgende trin fører dig gennem de indstillinger, der er nødvendige for at konfigurere en dynamisk eller distribuere-baserede gateway for hver VNet og oprette en VPN-forbindelse mellem gateways. Denne konfiguration understøtter ikke statisk eller baseret på politik gateways. 

I denne artikel bruge vi portalen klassisk, Azure portal og PowerShell. Det er i øjeblikket ikke muligt at oprette denne konfiguration ved hjælp af kun portalen Azure.

### <a name="prerequisites"></a>Forudsætninger

 - Begge VNets er allerede blevet oprettet.
 - Adresseområder for VNets ikke overlapper med hinanden eller overlapper med en af de områder for andre forbindelser, der kan være tilsluttet gateways.
 - Du har installeret de nyeste PowerShell-cmdletter (1.0.2 eller nyere). Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger. Kontrollér, at du har installeret både Service Management (SM) og cmdlet'erne ressource Manager (Ressourcestyring). 

### <a name="values"></a>Eksempelindstillinger for

Du kan bruge indstillingerne for eksempel som reference.

**Klassisk VNet indstillinger**

VNet navn = ClassicVNet <br>
Placering = Vest US <br>
Virtuelt netværk adresse mellemrum = 10.0.0.0/24 <br>
Undernet 1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Lokale Network Name = RMVNetLocal <br>

**Ressourcestyring VNet indstillinger**

VNet navn = RMVNet <br>
Ressourcegruppe = RG1 <br>
Virtuelt netværk IP-adresseområder = 192.168.0.0/16 <br>
Undernet 1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Placering = af US <br>
Virtuelt netværk gatewaynavn = RMGateway <br>
Offentlige IP-gatewaynavn = gwpip <br>
Gatewayen type = VPN <br>
VPN-type = rute-baseret <br>
Lokale netværksgateway = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Sektion 1: Konfigurere indstillinger for klassisk VNet

I dette afsnit skal oprette vi det lokale netværk og gateways til din klassisk VNet. Vejledningen i dette afsnit bruge klassisk portal. Portalen Azure tilbyder i øjeblikket ikke alle de indstillinger, der vedrører et klassisk VNet.

### <a name="part-1---create-a-new-local-network"></a>Del 1 – Opret et nyt lokale netværk

Åbn [Klassisk portalen](https://manage.windowsazure.com) og logge på med din Azure-konto.

1. Klik på **Ny**under det nederste venstre hjørne af skærmen, > **Netværk Services** > **Virtuelt netværk** > **Tilføj lokale netværk**.

2. Skriv et navn til den Ressourcestyring VNet, du vil oprette forbindelse til i vinduet **Angiv detaljerne for dit lokale netværk** . Skriv en gyldig offentlige IP-adresse i feltet **VPN-enhed IP-adresse (valgfrit)** . Dette er en midlertidig pladsholder. Du kan ændre denne IP-adresse senere. Klik på pileknappen nederst til højre i vinduet.
 
3. På siden **Angiv adresseområder** i tekstfeltet **Første IP-** Skriv netværkspræfiks og CIDR blokere for den ressourcestyring VNet du vil oprette forbindelse til. Denne indstilling bruges til at angive adresseområde for at omdirigere til Ressourcestyring VNet.

### <a name="part-2---associate-the-local-network-to-your-vnet"></a>Del 2 - Tilknyt det lokale netværk til din VNet

1. Klik **Virtuelle netværk** øverst på siden til at skifte til skærmbilledet virtuelle netværk, og klik for at vælge din klassisk VNet. Klik på **Konfigurer** for at gå til konfigurationssiden på siden for din VNet.

2. Markér afkrydsningsfeltet **Opret forbindelse til det lokale netværk** under sektionen **til websted connectivity** forbindelse. Vælg derefter det lokale netværk, du har oprettet. Hvis du har flere lokale netværk, du har oprettet, skal du sørge for at markere den, du har oprettet for at repræsentere din ressourcestyring VNet på rullelisten.

3. Klik på **Gem** nederst på siden.

### <a name="part-3---create-the-gateway"></a>Del 3 – Opret en gateway

1. Når du har gemt indstillingerne, klik på **Dashboard** øverst på siden for at ændre til Dashboard-siden. Nederst på siden Dashboard, skal du klikke på **Opret Gateway**, og klik derefter på **Dynamiske Routing**. Klik på **Ja** for at starte oprettelsen af din gateway. En dynamisk Routing gateway er påkrævet for denne konfiguration.

2. Vent gatewayen skal oprettes. Det kan tage nogle gange, 45 minutter eller mere for at fuldføre.

### <a name="ip"></a>Del 4 – få vist den gateway offentlige IP-adresse

Når gatewayen er blevet oprettet, kan du se gateway IP-adressen på siden **Dashboard** . Dette er den offentlige IP-adresse for din gateway. Notér eller Kopiér den offentlige IP-adresse. Du bruger den i efterfølgende trin, når du opretter det lokale netværk til din ressourcestyring VNet konfiguration.


## <a name="creatermgw"></a>Afsnit 2: Konfigurere ressourcestyring VNet indstillinger

I dette afsnit skal oprette vi gatewayen virtuelt netværk og det lokale netværk for din ressourcestyring VNet. Følgende trin indtil ikke starte, når du har hentet klassisk VNet gateway offentlige IP-adresse.

Skærmbillederne, der er angivet som eksempler. Sørg for at erstatte værdierne med dine egne. Hvis du opretter denne konfiguration som en opgave, kan du se disse [værdier](#values).


### <a name="part-1---create-a-gateway-subnet"></a>Del 1 – Opret en gateway-undernet

Før der oprettes forbindelse til netværket virtuel til en gateway, skal du først oprette gatewayen undernet for det virtuelle netværk, du vil oprette forbindelse. Oprette en gateway-undernet med CIDR antallet af /28 eller større (/ 27/26, osv.)

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Gå til [Azure-portalen](http://portal.azure.com) i en browser, og log på med din Azure-konto.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Del 2 – Opret en virtuelt netværksgateway


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### <a name="part-3---create-a-local-network-gateway"></a>Del 3 – Opret en lokale netværksgateway

'Lokale netværk gatewayen' typisk refererer til din lokale placering. Det fortæller Azure, hvilke IP-adresser til at distribuere placeringen, og den offentlige IP-adresse enhed til den nye placering. Men i dette tilfælde, det henviser til adresseområde og offentlige IP-adresse, der er knyttet til din klassisk VNet og virtuelt netværk gatewayen.

Navngive gatewayen lokale netværk, som Azure kan referere til den. Mens din gateway, virtuelt netværk oprettes, kan du oprette gatewayen dit lokale netværk. Du kan bruge den offentlige IP-adresse, der er tildelt til din klassisk VNet gatewayen i [forrige afsnit](#ip)for at denne konfiguration.

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### <a name="part-4---copy-the-public-ip-address"></a>Del 4 – Kopiér den offentlige IP-adresse

Når gatewayen virtuelt netværk er færdig med at oprette, kan du kopiere den offentlige IP-adresse, der er knyttet til gatewayen. Du kan bruge, når du konfigurerer indstillingerne for din klassisk VNet lokale netværk. 


## <a name="section-3-modify-the-local-network-for-the-classic-vnet"></a>Afsnit 3: Ændre det lokale netværk for klassisk VNet

Åbn [Klassisk portal](https://manage.windowsazure.com).

2. Rul ned til venstre i klassisk portal, og klik på **netværk**. Klik på **Lokalnetværk** øverst på siden, på siden **netværk** . 

3. Klik på det lokale netværk, du konfigurerede i del 1. Klik på **Rediger**nederst på siden.

4. Erstat pladsholder IP-adressen på siden **Angiv dit lokale netværk oplysninger** med den offentlige IP-adressen til gatewayen Ressourcestyring, du oprettede i forrige afsnit. Klik på pilen til at flytte til næste afsnit. Bekræft, at **Adresseområde** er korrekte, og klik derefter på markering for at acceptere ændringerne.

## <a name="connect"></a>Del 4: Oprette forbindelsen

I dette afsnit skal oprette vi forbindelse mellem VNets. Fremgangsmåden for dette kræver PowerShell. Du kan ikke oprette denne forbindelse på en af portaler. Kontrollér, at du har downloadet og installeret både klassisk (SM) og ressource Manager (Ressourcestyring) PowerShell-cmdlet'er.


1. Log på din Azure-konto i PowerShell console. Følgende cmdlet beder dig om logonoplysninger for din Azure-konto. Når du logger på, hentes dine kontoindstillinger, så de er tilgængelige til Azure PowerShell.

        Login-AzureRmAccount 

    Få en liste over dine Azure abonnementer, hvis du har mere end ét abonnement.

        Get-AzureRmSubscription

    Angiv det abonnement, du vil bruge. 

        Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Tilføj din Azure-konto for at bruge klassisk PowerShell-cmdletter. For at gøre det, kan du bruge følgende kommando:

        Add-AzureAccount

3. Angive produktnøglen på delt ved at køre i følgende eksempel. I dette eksempel `-VNetName` er navnet på den klassisk VNet og `-LocalNetworkSiteName` er det navn, du har angivet for det lokale netværk, når du har konfigureret det i portalen klassisk. Den `-SharedKey` er en værdi, du kan oprette og angive. Den værdi, du angiver her, skal være den samme værdi, du angiver i næste trin, når du opretter din forbindelse.

        Set-AzureVNetGatewayKey -VNetName ClassicVNet `
        -LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Oprette VPN-forbindelse ved at køre følgende kommandoer:
    
    **Angive variablerne**

        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

    **Oprette forbindelse**<br> Bemærk, at den `-ConnectionType` er 'IPsec', ikke 'Vnet2Vnet'. I dette eksempel `-Name` er det navn, du vil ringe til din forbindelse. I følgende eksempel oprettes en forbindelse med navnet '*ressourcestyring-til-klassisk-forbindelse*'.
        
        New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="verify-your-connection"></a>Kontrollér forbindelsen

Du kan kontrollere din forbindelse ved hjælp af portalen klassisk, Azure portal eller PowerShell. Du kan bruge følgende trin til at bekræfte din forbindelse. Erstat værdier med dine egne.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)] 

## <a name="faq"></a>VNet-VNet ofte stillede spørgsmål

Se ofte stillede spørgsmål om detaljerne for yderligere oplysninger om VNet-VNet forbindelser.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


