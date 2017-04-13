<properties
   pageTitle="Hvordan du konfigurerer routing til et ExpressRoute kredsløb | Microsoft Azure"
   description="I denne artikel fører dig gennem trinnene til oprettelse og klargøring privat, offentlige og Microsoft peering af et ExpressRoute kredsløb. Denne artikel kan du også hvordan til at kontrollere status, opdatere eller slette peerings for din kredsløb."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Oprette og redigere distribution til et ExpressRoute kredsløb


> [AZURE.SELECTOR]
[Azure Portal - ressourcestyring](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - ressourcestyring](expressroute-howto-routing-arm.md)
[PowerShell - klassisk](expressroute-howto-routing-classic.md)



I denne artikel fører dig gennem trinnene til at oprette og administrere konfigurationen af routing til en ExpressRoute kredsløb, ved hjælp af PowerShell og implementeringsmodel Azure ressourcestyring.  Nedenstående trin kan du også se Sådan Kontrollér status, Opdater eller Slet og deprovision peerings for en ExpressRoute kredsløb. 


**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Forudsætninger for konfiguration

- Du skal den seneste version af Azure PowerShell-moduler, version 1.0 eller nyere. 
- Sørg for, at du har gennemgået siden [forudsætninger](expressroute-prerequisites.md) , siden [routing krav](expressroute-routing.md) og siden [arbejdsprocesser](expressroute-workflows.md) , inden du starter konfigurationen.
- Du skal have et aktivt ExpressRoute kredsløb. Følg vejledningen til at [oprette et ExpressRoute kredsløb](expressroute-howto-circuit-arm.md) og har det kredsløb, der er aktiveret af din udbyder af connectivity, før du fortsætter. ExpressRoute kredsløb skal være i tilstanden klargjort og aktiveret for dig at kunne køre cmdletter beskrevet nedenfor.

Disse instruktioner gælder kun for kredsløb, der er oprettet med tjenesteudbydere tilbyder Layer 2 forbindelsestjenester. Hvis du bruger en udbyder af tilbyde administrerede Layer 3-tjenester (normalt en IPVPN, som MPLS), skal udbyderen connectivity konfigurere og administrere routing for dig.

>[AZURE.IMPORTANT] Vi annoncere i øjeblikket ikke peerings af tjenesteudbydere via service management-portalen. Vi arbejder på at aktivere denne funktion snart. Spørg din tjenesteudbyder, inden du konfigurerer BGP peerings.

Du kan konfigurere en, to eller alle tre peerings (Azure privat, Azure offentligt og Microsoft) for en ExpressRoute kredsløb. Du kan konfigurere peerings i en hvilken som helst rækkefølge, du vælger. Skal du sikre dig, at du har fuldført konfigurationen af hver peering én ad gangen. 

## <a name="azure-private-peering"></a>Azure privat peering

Dette afsnit indeholder vejledning i at oprette, få, opdatere og slette Azure privat peering konfigurationen for et ExpressRoute kredsløb. 

### <a name="to-create-azure-private-peering"></a>Oprette Azure privat peering

1. Importere af PowerShell-modulet til ExpressRoute.
    
    Du skal installere den nyeste version PowerShell-installer [PowerShell](http://www.powershellgallery.com/) galleriet og importere Azure ressourcestyring moduler til PowerShell-session for at begynde at bruge ExpressRoute-cmdletter. Du skal køre PowerShell som Administrator.

        Install-Module AzureRM

        Install-AzureRM

    Importere alle AzureRM.* modulerne inden for området kendte semantisk version

        Import-AzureRM

    Du kan også bare importere et Vælg modul inden for området kendte semantisk version 
        
        Import-Module AzureRM.Network 

    Logge på din konto

        Login-AzureRmAccount

    Vælg det abonnement, du vil oprette ExpressRoute kredsløb
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Oprette et ExpressRoute kredsløb.
    
    Følg vejledningen for at oprette et [ExpressRoute kredsløb](expressroute-howto-circuit-arm.md) og har den klargjort af provideren connectivity. 

    Hvis din forbindelse-udbyder tilbyder administrerede Layer 3-tjenester, kan du anmode om udbyderen forbindelse for at kunne aktivere Azure privat peering for dig. Det er tilfældet, behøver du ikke at følge instruktionerne, der er angivet i de næste afsnit. Men hvis udbyderen connectivity ikke administrere distribution til dig, når du har oprettet din kredsløb, skal du følge vejledningen nedenfor. 

3. Markér ExpressRoute kredsløbet for at sikre, at den er klargjort.

    Du skal først kontrollere, om ExpressRoute kredsløbet er klargjort og også er aktiveret. Se eksemplet nedenfor.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Svaret bliver noget lignende eksemplet nedenfor:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []


4. Konfigurere Azure privat peering for kredsløbet.

    Sørg for, at du har følgende elementer, før du fortsætter med de næste trin:

    - En /30 undernet til linket primære. Dette må ikke være en del af en hvilken som helst reserveret til virtuelle netværk adresseområde.
    - En /30 undernet til linket sekundær. Dette må ikke være en del af en hvilken som helst reserveret til virtuelle netværk adresseområde.
    - Et gyldigt VLAN ID til at oprette denne peering på. Sikre, at ingen andre peering i kredsløbet bruger det samme VLAN-ID.
    - SOM tal for peering. Du kan bruge både 2-byte og 4 byte som tal. Du kan bruge en privat som tal for denne peering. Sørg for, at du ikke bruger 65515.
    - En MD5 hashværdi, hvis du vælger at bruge en. **Dette er valgfrit**.
    
    Du kan køre følgende cmdlet for at konfigurere Azure privat peering for din kredsløb.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Du kan bruge cmdlet'en til nedenfor, hvis du vælger at bruge en MD5-hash.

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    >[AZURE.IMPORTANT] Sørg for, at du angiver dit nummer, som som peering ASN, ikke kunde ASN.

### <a name="to-view-azure-private-peering-details"></a>At få vist Azure privat peering oplysninger

Du kan finde oplysninger om konfiguration ved hjælp af følgende cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt   


### <a name="to-update-azure-private-peering-configuration"></a>Opdatere Azure privat peering konfiguration

Du kan opdatere en del af konfigurationen med følgende cmdlet. I eksemplet nedenfor er kredsløbet VLAN ID opdateres fra 100 til 500.

    Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-delete-azure-private-peering"></a>Slette Azure privat peering

Du kan fjerne konfigurationen af peering ved at køre følgende cmdlet.

>[AZURE.WARNING] Du skal sikre dig, at alle virtuelle netværk er ikke er sammenkædet fra ExpressRoute kredsløbet, før du kører denne cmdlet. 

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt



## <a name="azure-public-peering"></a>Azure offentlige peering

Dette afsnit indeholder vejledning i at oprette, få, opdatere og slette Azure offentlige peering konfigurationen for et ExpressRoute kredsløb.

### <a name="to-create-azure-public-peering"></a>Oprette Azure offentlige peering

1. Importere af PowerShell-modulet til ExpressRoute.
    
    Du skal installere den nyeste version PowerShell-installer [PowerShell](http://www.powershellgallery.com/) galleriet og importere Azure ressourcestyring moduler til PowerShell-session for at begynde at bruge ExpressRoute-cmdletter. Du skal køre PowerShell som Administrator.

        Install-Module AzureRM

        Install-AzureRM

    Importere alle AzureRM.* modulerne inden for området kendte semantisk version

        Import-AzureRM

    Du kan også bare importere et Vælg modul inden for området kendte semantisk version 
        
        Import-Module AzureRM.Network 

    Logge på din konto

        Login-AzureRmAccount

    Vælg det abonnement, du vil oprette ExpressRoute kredsløb
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Oprette et ExpressRoute kredsløb.
    
    Følg vejledningen for at oprette et [ExpressRoute kredsløb](expressroute-howto-circuit-arm.md) og har den klargjort af provideren connectivity. 

    Hvis din forbindelse-udbyder tilbyder administrerede Layer 3-tjenester, kan du anmode om udbyderen forbindelse for at kunne aktivere Azure offentlige peering for dig. Det er tilfældet, behøver du ikke at følge instruktionerne, der er angivet i de næste afsnit. Men hvis udbyderen connectivity ikke administrere distribution til dig, når du har oprettet din kredsløb, skal du følge vejledningen nedenfor.

3. Se ExpressRoute kredsløb for at sikre, at den er klargjort.

    Du skal først kontrollere, om ExpressRoute kredsløbet er klargjort og også er aktiveret. Se eksemplet nedenfor.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Svaret bliver noget lignende eksemplet nedenfor:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   

4. Konfigurere Azure offentlige peering for kredsløbet.

    Sørg for, at du har følgende oplysninger, inden du fortsætter yderligere.

    - En /30 undernet til linket primære. Det skal være et gyldigt offentlige IPv4 præfiks.
    - En /30 undernet til linket sekundær. Det skal være et gyldigt offentlige IPv4 præfiks.
    - Et gyldigt VLAN ID til at oprette denne peering på. Sikre, at ingen andre peering i kredsløbet bruger det samme VLAN-ID.
    - SOM tal for peering. Du kan bruge både 2-byte og 4 byte som tal.
    - En MD5 hashværdi, hvis du vælger at bruge en. **Dette er valgfrit**.
    
    Du kan køre følgende cmdlet for at konfigurere Azure offentlige peering for din kredsløb

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

    Du kan bruge cmdlet'en til nedenfor, hvis du vælger at bruge en MD5-hash

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


    >[AZURE.IMPORTANT] Sørg for, at du angiver dit nummer, som som peering ASN og ikke kunde ASN.

### <a name="to-view-azure-public-peering-details"></a>At få vist Azure offentlige peering oplysninger

Du kan finde oplysninger om konfiguration ved hjælp af følgende cmdlet

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt


### <a name="to-update-azure-public-peering-configuration"></a>Opdatere Azure offentlige peering konfiguration

Du kan opdatere en del af konfigurationen med følgende cmdlet

    Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600 

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Kredsløbet VLAN ID opdateres fra 200 til 600 i eksemplet ovenfor.

### <a name="to-delete-azure-public-peering"></a>Slette Azure offentlige peering

Du kan fjerne konfigurationen af peering ved at køre følgende cmdlet

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="microsoft-peering"></a>Microsoft peering

Dette afsnit indeholder vejledning i at oprette, få, opdatere og slette peering konfigurationen af Microsoft til et ExpressRoute kredsløb. 

### <a name="to-create-microsoft-peering"></a>Til at oprette Microsoft peering

1. Importere af PowerShell-modulet til ExpressRoute.
    
    Du skal installere den nyeste version PowerShell-installer [PowerShell](http://www.powershellgallery.com/) galleriet og importere Azure ressourcestyring moduler til PowerShell-session for at begynde at bruge ExpressRoute-cmdletter. Du skal køre PowerShell som Administrator.

        Install-Module AzureRM

        Install-AzureRM

    Importere alle AzureRM.* modulerne inden for området kendte semantisk version

        Import-AzureRM

    Du kan også bare importere et Vælg modul inden for området kendte semantisk version 
        
        Import-Module AzureRM.Network 

    Logge på din konto

        Login-AzureRmAccount

    Vælg det abonnement, du vil oprette ExpressRoute kredsløb
        
        Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

2. Oprette et ExpressRoute kredsløb.
    
    Følg vejledningen for at oprette et [ExpressRoute kredsløb](expressroute-howto-circuit-arm.md) og har den klargjort af provideren connectivity. 

    Hvis din forbindelse-udbyder tilbyder administrerede Layer 3-tjenester, kan du anmode om udbyderen forbindelse for at kunne aktivere Azure privat peering for dig. Det er tilfældet, behøver du ikke at følge instruktionerne, der er angivet i de næste afsnit. Men hvis udbyderen connectivity ikke administrere distribution til dig, når du har oprettet din kredsløb, skal du følge vejledningen nedenfor.

3. Se ExpressRoute kredsløb for at sikre, at den er klargjort.

    Du skal først kontrollere, om ExpressRoute kredsløbet er klargjort og også er aktiveret. Se eksemplet nedenfor.

        Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    Svaret bliver noget lignende eksemplet nedenfor:

        Name                             : ExpressRouteARMCircuit
        ResourceGroupName                : ExpressRouteResourceGroup
        Location                         : westus
        Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
        Etag                             : W/"################################"
        ProvisioningState                : Succeeded
        Sku                              : {
                                             "Name": "Standard_MeteredData",
                                             "Tier": "Standard",
                                             "Family": "MeteredData"
                                           }
        CircuitProvisioningState         : Enabled
        ServiceProviderProvisioningState : Provisioned
        ServiceProviderNotes             : 
        ServiceProviderProperties        : {
                                             "ServiceProviderName": "Equinix",
                                             "PeeringLocation": "Silicon Valley",
                                             "BandwidthInMbps": 200
                                           }
        ServiceKey                       : **************************************
        Peerings                         : []   
4. Konfigurere Microsoft peering for kredsløbet.

    Sørg for, at du har følgende oplysninger, inden du fortsætter.

    - En /30 undernet til linket primære. Det skal være et gyldigt offentlige IPv4 præfiks ejes af dig og er registreret i en RIR / IA.
    - En /30 undernet til linket sekundær. Det skal være et gyldigt offentlige IPv4 præfiks ejes af dig og er registreret i en RIR / IA.
    - Et gyldigt VLAN ID til at oprette denne peering på. Sikre, at ingen andre peering i kredsløbet bruger det samme VLAN-ID.
    - SOM tal for peering. Du kan bruge både 2-byte og 4 byte som tal.
    - Annonceret præfikser: skal du angive en liste over alle præfikser du planlægger at annoncere over BGP session. Kun offentlige IP-adressepræfikser accepteres. Hvis du planlægger at sende et sæt af præfikser, kan du sende en kommasepareret liste. Disse præfikser skal være registreret for dig i en RIR / IA.
    - Kunde ASN: Hvis du er reklame præfikser på enheder, der ikke er registreret til peering som tal, kan du angive det som tal, som de er registreret. **Dette er valgfrit**.
    - Routing registreringsdatabasen navn: Du kan angive RIR / IA mod som det som tal og præfikser er registreret.
    - En MD5-hash, hvis du vælger at bruge en. **Dette er valgfrit.**
    
    Du kan køre følgende cmdlet for at konfigurere Microsoft peering for din kredsløb

        Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-get-microsoft-peering-details"></a>At få Microsoft peering oplysninger

Du kan få oplysninger om konfiguration ved hjælp af følgende cmdlet.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

        Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt


### <a name="to-update-microsoft-peering-configuration"></a>Opdatere Microsoft peering konfiguration

Du kan opdatere en del af konfigurationen med følgende cmdlet.

        Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
        

### <a name="to-delete-microsoft-peering"></a>Sådan sletter du Microsoft peering

Du kan fjerne konfigurationen af peering ved at køre følgende cmdlet.

    Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Næste trin

Næste trin, [Link en VNet til en ExpressRoute kredsløb](expressroute-howto-linkvnet-arm.md).

-  Du kan finde flere oplysninger om ExpressRoute arbejdsprocesser, [ExpressRoute arbejdsprocesser](expressroute-workflows.md).

-  Du kan finde flere oplysninger om kredsløb peering, se [ExpressRoute kredsløb og routing domæner](expressroute-circuit-peerings.md).

-  Se [Oversigt over virtuelle netværk](../virtual-network/virtual-networks-overview.md), du kan finde flere oplysninger om at arbejde med virtuelle netværk.

