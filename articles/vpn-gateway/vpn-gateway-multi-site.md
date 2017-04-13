<properties 
   pageTitle="Forbinde et virtuelt netværk til flere websteder ved hjælp af VPN-Gateway og PowerShell | Microsoft Azure"
   description="I denne artikel fører dig gennem forbindelse flere lokale lokalt websteder til et virtuelt netværk ved hjælp af en VPN-Gateway til klassisk implementeringsmodel."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Føje en forbindelse til websted til et VNet med en eksisterende forbindelse for VPN-gateway

> [AZURE.SELECTOR]
- [Ressourcestyring - Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
- [Klassisk - PowerShell](vpn-gateway-multi-site.md)

I denne artikel vejledes du gennem ved hjælp af PowerShell til at føje-til-websted (S2S) forbindelser til en VPN-gateway, der har en eksisterende forbindelse. Denne type forbindelse der ofte kaldes en "med flere websted" konfiguration. 

I denne artikel gælder for virtuelle netværk, der er oprettet ved hjælp af den klassiske implementeringsmodel (også kaldet Service Management). Disse trin gælder ikke for ExpressRoute /-til-websted fungerer sammen forbindelse konfigurationer. Du kan finde oplysninger om fungerer sammen forbindelser i [ExpressRoute/S2S fungerer sammen forbindelser](../expressroute/expressroute-howto-coexist-classic.md) .

### <a name="deployment-models-and-methods"></a>Installation-modeller og metoder

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

Vi opdaterer denne tabel som nye artikler og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra denne tabel.

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)] 



## <a name="about-connecting"></a>Om at oprette forbindelse

Du kan oprette forbindelse flere lokale websteder til et enkelt virtuelt netværk. Dette er især flot til opbygning af hybrid skyen løsninger. Oprette forbindelse til din gateway, Azure virtuelt netværk med flere websted er meget svarer til at oprette andre forbindelser til websted. Faktisk, du kan bruge en eksisterende Azure VPN-gateway, så længe gatewayen er dynamiske (rute-baseret).

Hvis du allerede har en statisk gateway, der er tilsluttet netværket virtuel, kan du ændre typen gateway til en dynamisk uden at skulle genopbygge det virtuelle netværk for at tage højde for flere websted. Før du ændrer typen routing, Sørg for, at din lokale VPN-gateway understøtter distribution baseret konfigurationer for VPN. 

![flere websted diagram] (./media/vpn-gateway-multi-site/multisite.png "flere websted")

## <a name="points-to-consider"></a>Punkter du bør overveje

**Du kan ikke bruge Azure klassisk Portal til at foretage ændringer til denne virtuelle netværk.** I denne udgave skal du foretage ændringer i konfigurationsfil netværk i stedet for ved hjælp af portalen Azure klassisk. Hvis du foretager ændringer i portalen Azure klassisk, får de overskrive indstillingerne med flere websted reference for denne virtuelle netværk. 

Du bør er ret tryg ved hjælp af konfigurationsfil netværk, når du har fuldført proceduren med flere websted. Men hvis du har flere personer arbejder på din netværkskonfiguration, skal du at sikre dig, at alle ved, om denne begrænsning. Dette betyder ikke, at du ikke kan bruge portalen Azure klassisk. Du kan bruge den til alt andet, undtagen foretager ændringer i konfigurationen til dette bestemt virtuelle netværk.

## <a name="before-you-begin"></a>Inden du går i gang

Før du starter konfigurationen, skal du kontrollere, at du har følgende:

- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).

- Kompatible VPN hardware for hver lokal placering. Kontrollere, [Om VPN-enheder til virtuelle netværksforbindelsen](vpn-gateway-about-vpn-devices.md) for at bekræfte, hvis den enhed, du vil bruge er noget, der kendes ikke kompatibelt.

- En eksternt modstående offentlige IPv4 IP-adresse for hver VPN-enhed. IP-adressen kan ikke være placeret bag en NAT. Dette er krav.

- Du skal installere den nyeste version af Azure PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.

- Nogen, som er opnå på konfigurerer din VPN-hardware. Du kan ikke bruge de automatisk oprettede VPN-scripts fra portalen Azure klassisk til at konfigurere dine VPN-enheder. Det betyder, at du skal have en stærk forståelse af, hvordan du konfigurerer din VPN-enhed eller arbejde med en person, som.

- De IP-adresseområder, du vil bruge til dit virtuelle netværk (Hvis du ikke allerede har oprettet en). 

- IP-adresseområder for hver af de lokale netværk websteder, du vil oprette forbindelse til. Du skal kontrollere, at IP-adressen intervaller for hver af de lokale netværk websteder, du vil oprette forbindelse for at der ikke overlapper. Ellers afviser portalen Azure klassisk eller REST-API konfigurationen overføres. 

    Eksempelvis hvis du har to lokale netværk websteder, at begge indeholder IP-adresse område 10.2.3.0/24, og du har en pakke med en destinationsadresse 10.2.3.3, vil ikke kunne Azure ved hvilket websted, du vil sende pakken til, fordi adresseområder overlappende. Hvis du vil forhindre, at dirigere problemer, tillader Azure ikke, at du kan overføre en konfigurationsfil, der indeholder overlappende områder.



## <a name="1-create-a-site-to-site-vpn"></a>1. Opret en VPN til-websted

Hvis du allerede har en-til-websted VPN-forbindelse til en dynamisk routing gateway godt! Du kan fortsætte med at [eksportere konfigurationsindstillinger virtuelt netværk](#export). Hvis ikke, skal du gøre følgende:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Hvis du allerede har et websted til websted virtuelt netværk, men det har en statisk (politik-baseret)-gateway til routing:

1. Ændre din gateway til dynamisk routing. En VPN-forbindelse til flere websted kræver en dynamisk (også kendt som rute-baseret)-gateway til routing. Hvis du vil ændre din gateway-type, skal du først slette eksisterende gatewayen og derefter oprette en ny. Finde en vejledning, se, [hvordan du ændre typen VPN routing til din gateway](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md#how-to-change-the-vpn-routing-type-for-your-gateway).  

2. Konfigurere din ny gateway og oprette VPN-tunnel. Flere oplysninger under [konfigurere en VPN-Gateway i portalen Azure klassisk](vpn-gateway-configure-vpn-gateway-mp.md). Først skal du ændre din gateway af typen til dynamisk routing. 

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Hvis du ikke har et virtuelt netværk til-websted:

1. Oprette dit websted til websted virtuelle netværk ved hjælp af disse instruktioner: [oprette et virtuelt netværk med en - til-websted VPN-forbindelse i portalen Azure klassisk](vpn-gateway-site-to-site-create.md).  

2. Konfigurere en dynamisk routing gateway ved hjælp af disse instruktioner: [konfigurere en VPN-Gateway](vpn-gateway-configure-vpn-gateway-mp.md). Sørg for at vælge **dynamisk routing** til din gateway.

## <a name="export"></a>2. eksportere konfigurationsfil netværk 

Eksportere dit netværk konfigurationsfil. Den fil, du har eksporteret bruges til at konfigurere din nye med flere webstedsindstillinger. Hvis du har brug for oplysninger om, hvordan du kan eksportere en fil, kan du se afsnittet i artiklen: [Sådan opretter du en VNet ved hjælp af et netværk konfigurationsfil i portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="3-open-the-network-configuration-file"></a>3. Åbn konfigurationsfil netværk

Åbn filen netværk konfiguration, som du har hentet i det sidste trin. Brug en XML-editor, som du have. Filen skal ligne følgende:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. tilføje flere referencer til websteder

Når du tilføjer eller fjerner websted referenceoplysninger, foretager du ændringer i konfigurationen til ConnectionsToLocalNetwork/LocalNetworkSiteRef. Tilføje en ny lokal websted reference udløsere Azure til at oprette en ny tunnel. I eksemplet herunder er netværkskonfigurationen for en enkelt lokalitet forbindelse. Gem filen, når du har foretaget dine ændringer.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

    To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## <a name="5-import-the-network-configuration-file"></a>5. importere konfigurationsfil netværk

Importere konfigurationsfil netværk. Når du importerer denne fil med ændringerne, tilføjes nye tunneler. Tunneler anvender dynamiske gatewayen, du oprettede tidligere. Hvis du har brug for oplysninger om, hvordan du kan importere fil, kan du se afsnittet i artiklen: [Sådan opretter du en VNet ved hjælp af et netværk konfigurationsfil i portalen Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). 

## <a name="6-download-keys"></a>6. Hent taster

Når din nye tunneler er blevet tilføjet, kan du bruge PowerShell-cmdlet `Get-AzureVNetGatewayKey` at få IPsec/IKE allerede delt taster til hver tunnel.

Eksempel:

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

    Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Hvis du foretrækker, kan du også bruge *Få virtuelle netværk Gateway delt nøgle* REST-API til at få tasterne allerede delt.

## <a name="7-verify-your-connections"></a>7. bekræfte dine forbindelser

Kontrollér status med flere websted tunnel. Når du overfører taster til hver tunnel, vil du bekræfte forbindelser. Brug `Get-AzureVnetConnection` til at få en liste over virtuelt netværk tunneler, som vist i eksemplet nedenfor. VNet1 er navnet på VNet.

    Get-AzureVnetConnection -VNetName VNET1
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
        
    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om VPN Gateways skal du se [Om VPN Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md).

