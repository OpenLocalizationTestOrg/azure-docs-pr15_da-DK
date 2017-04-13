<properties 
   pageTitle="Konfigurere tvunget tunnelføring for at websted forbindelser ved hjælp af den klassiske implementeringsmodel | Microsoft Azure"
   description="Sådan omdirigeres eller 'gennemtvinge' al trafik til internetbaserede tilbage til din lokale placering."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurere tvunget tunnelføring ved hjælp af den klassiske implementeringsmodel

> [AZURE.SELECTOR]
- [PowerShell - klassisk](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - ressourcestyring](vpn-gateway-forced-tunneling-rm.md)

Tvunget tunnelføring kan du Omdiriger eller "kraft" al trafik til internetbaserede tilbage til din lokale placering via en VPN-websted til websted tunnel til inspektion og revision. Dette er et kritiske sikkerhedskrav til de fleste enterprise IT politikker. 

Uden tvunget tunnelføring vil internetbaserede trafik fra din FOS i Azure altid gennemgang fra Azure-netværksinfrastruktur direkte ud til internettet, uden mulighed for at gør det muligt at undersøge eller overvåge trafikken. Uautoriseret adgang til internettet kan potentielt føre til offentliggørelse af oplysninger eller andre typer brud på sikkerhed.

I denne artikel fører dig gennem konfigurationen af tvunget tunnelføring for virtuelle netværk, der er oprettet ved hjælp af den klassiske implementeringsmodel. 

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Installation-modeller og værktøjer til tvunget tunnelføring**

En tvunget tunnelføring forbindelse kan konfigureres til både den klassiske implementeringsmodel og implementeringsmodel ressourcestyring. Se tabellen nedenfor for at få flere oplysninger. Vi opdaterer denne tabel, som nye artikler, nye installation-modeller og yderligere værktøjer bliver tilgængelige for denne konfiguration. Når en artikel, der er tilgængelige, link vi direkte til den fra tabellen.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="requirements-and-considerations"></a>Kravene samt de overvejelser

Tvunget tunnelføring i Azure er konfigureret via virtuelt netværk brugerdefinerede omdirigerer (UDR). Omdirigere trafikken til et lokalt websted er udtrykt som en standard rute til Azure VPN gatewayen. Følgende afsnit vises den aktuelle begrænsning af routing tabel og omdirigerer for et virtuelt netværk Azure:


-  Hvert virtuelt netværksundernet har en indbygget, routing system-tabel. Systemtabellen routing har følgende tre grupper af omdirigerer:

    - **Lokale VNet dirigerer:** Direkte til destinationen FOS i det samme virtuelle netværk
    
    - **På lokale omdirigerer:** Til gatewayen Azure VPN
    
    - **Standardrute:** Direkte til internettet. Pakker, til de private IP-adresser, der ikke er omfattet af de forrige to omdirigerer fjernes.


-  Du kan oprette en routing tabel for at tilføje en standardrute og derefter knytte tabellen routing til din VNet subnet(s) til at aktivere tvunget tunnelføring på disse undernet med versionen af brugerdefinerede omdirigerer.

- Du skal angive et "standardwebsted" mellem de lokale websteder i tværs lokale forbundet med det virtuelle netværk.

- Tvunget tunnelføring skal være knyttet til en VNet, der har en dynamisk routing VPN-gateway (ikke en statisk gateway).
 
- ExpressRoute tvungen tunnelføring er ikke konfigureret via denne funktion, men i stedet er aktiveret ved at anmelde en standardrute via de ExpressRoute BGP peering sessioner. Se i [Dokumentationen til ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) kan finde flere oplysninger.



## <a name="configuration-overview"></a>Oversigt over konfiguration

I eksemplet nedenfor tunnelført Frontend undernet ikke er tvunget. Arbejdsmængder i front end-undernet kan fortsætte med at acceptere og besvare spørgsmål fra kunder fra internettet direkte. Mellemstore og back end-undernet er tvunget tunnelført. En hvilken som helst udgående forbindelser fra disse to undernet til internettet vil blive tvunget eller omdirigeret tilbage til et lokalt websted via en af S2S VPN-tunneler.

Dette giver dig mulighed at begrænse og undersøge internetadgang fra din virtuelle maskiner eller cloud services i Azure, mens du fortsætter med at aktivere din arkitektur af med flere lag tjenesten påkrævet. Du kan også anvende, tvunget tunnel til hele virtuelle netværk, hvis der ikke er nogen internettet arbejdsmængder i dine virtuelle netværk.


![Tvungen tunnelføring](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## <a name="before-you-begin"></a>Inden du går i gang

Kontrollér, at du har følgende elementer, før du starter konfigurationen.

- Et Azure-abonnement. Hvis du ikke allerede har et Azure-abonnement, kan du aktivere din [MSDN abonnement fordele](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller Log op for at få [gratis konto](https://azure.microsoft.com/pricing/free-trial/).

- Et konfigureret virtuelt netværk. 

- Den seneste version af Azure PowerShell-cmdlet'er. Se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) kan finde flere oplysninger om installation af PowerShell-cmdlet'er.


## <a name="configure-forced-tunneling"></a>Konfigurere tvunget tunnelføring

Følgende procedure hjælper dig med at angive tvunget tunneling i et virtuelt netværk. Konfigurationstrinnene svarer til VNet netværk konfigurationsfil.



    <VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>

I dette eksempel det virtuelle netværk "MultiTier-VNet" har tre undernet: *Frontend*, *Midtier*og *back-end* undernet, med fire cross lokale forbindelser: *DefaultSiteHQ*og tre *grene*. 

Trinnene skal angive *DefaultSiteHQ* som standard websted forbindelsen til tvungen tunnelføring, og Konfigurer Midtier og back end-undernet bruge tvunget tunnelføring.


1. Oprette en routing tabel. Brug følgende cmdlet til at oprette indholdsfortegnelsen distribution.

        New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Føje en standardrute til tabellen routing. 

    I følgende eksempel tilføjer en standardrute til tabellen routing oprettede i trin 1. Bemærk, at kun ruten understøttes er præfikset destination af "0.0.0.0/0" til det næste hop "VPNGateway".
 
        Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Knytte tabellen routing til undernet. 

    Når der oprettes en routing tabel og en rute tilføjet, kan du bruge i følgende eksempel til at tilføje eller knytte rutetabellen til en VNet undernet. I eksempel adderes rutetabellen "MyRouteTable" til Midtier og back end-undernet af VNet MultiTier-VNet.

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

        Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Tildele en standard-webstedet for tvunget tunnelføring. 

    I det forrige trin, eksempel cmdlet scripts oprettet tabellen routing og tilhørende rutetabellen til to af VNet undernet. Det sidste trin er at vælge et lokalt websted mellem flere websted forbindelser i det virtuelle netværk som standardwebstedet eller tunnel.

        $DefaultSite = @("DefaultSiteHQ")
        Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## <a name="additional-powershell-cmdlets"></a>Yderligere PowerShell-cmdletter

### <a name="to-delete-a-route-table"></a>Slette en tabel til distribution

    Remove-AzureRouteTable -Name <routeTableName>

### <a name="to-list-a-route-table"></a>Til at få vist en distribution tabel

    Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### <a name="to-delete-a-route-from-a-route-table"></a>Slette en rute fra en tabel til distribution

    Remove-AzureRouteTable –Name <routeTableName>

### <a name="to-remove-a-route-from-a-subnet"></a>Fjerne en rute fra et undernet

    Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Med listen over rutetabellen, der er knyttet til et undernet
    
    Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Sådan fjernes et standardwebsted fra en VNet VPN-gateway

    Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>






