<properties
   pageTitle="Hvordan du konfigurerer routing til et ExpressRoute kredsløb for den klassiske implementeringsmodel ved hjælp af PowerShell | Microsoft Azure"
   description="I denne artikel fører dig gennem trinnene til oprettelse og klargøring privat, offentlige og Microsoft peering af et ExpressRoute kredsløb. Denne artikel kan du også hvordan til at kontrollere status, opdatere eller slette peerings for din kredsløb."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>

# <a name="create-and-modify-routing-for-an-expressroute-circuit"></a>Oprette og redigere distribution til et ExpressRoute kredsløb

> [AZURE.SELECTOR]
[Azure Portal - ressourcestyring](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - ressourcestyring](expressroute-howto-routing-arm.md)
[PowerShell - klassisk](expressroute-howto-routing-classic.md)



I denne artikel fører dig gennem trinnene til at oprette og administrere konfigurationen af routing til en ExpressRoute kredsløb, ved hjælp af PowerShell og klassisk implementeringsmodel. Nedenstående trin kan du også se Sådan Kontrollér status, Opdater eller Slet og deprovision peerings for en ExpressRoute kredsløb.


**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="configuration-prerequisites"></a>Forudsætninger for konfiguration

- Du skal den seneste version af Azure PowerShell-moduler. Du kan hente det seneste PowerShell-modulet i afsnittet PowerShell på [siden Azure overførsler](https://azure.microsoft.com/downloads/). Følg vejledningen på siden [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) finde en trinvis vejledning til, hvordan du konfigurerer din computer for at bruge Azure PowerShell-moduler. 
- Sørg for, at du har gennemgået siden [forudsætninger](expressroute-prerequisites.md) , siden [routing krav](expressroute-routing.md) og siden [arbejdsprocesser](expressroute-workflows.md) , inden du starter konfigurationen.
- Du skal have et aktivt ExpressRoute kredsløb. Følg vejledningen for at [oprette et ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) og har det kredsløb, der er aktiveret af din udbyder af connectivity, før du fortsætter. ExpressRoute kredsløb skal være i tilstanden klargjort og aktiveret for dig at kunne køre cmdletter beskrevet nedenfor.

>[AZURE.IMPORTANT] Disse instruktioner gælder kun for kredsløb, der er oprettet med tjenesteudbydere tilbyder Layer 2 forbindelsestjenester. Hvis du bruger en udbyder af tilbyde administrerede Layer 3-tjenester (normalt en IPVPN, som MPLS), skal udbyderen connectivity konfigurere og administrere routing for dig.

Du kan konfigurere en, to eller alle tre peerings (Azure privat, Azure offentligt og Microsoft) for en ExpressRoute kredsløb. Du kan konfigurere peerings i en hvilken som helst rækkefølge, du vælger. Skal du sikre dig, at du har fuldført konfigurationen af hver peering én ad gangen. 

## <a name="azure-private-peering"></a>Azure privat peering

Dette afsnit indeholder vejledning i at oprette, få, opdatere og slette Azure privat peering konfigurationen for et ExpressRoute kredsløb. 

### <a name="to-create-azure-private-peering"></a>Oprette Azure privat peering

1. **Importere af PowerShell-modulet til ExpressRoute.**
    
    For at begynde at bruge ExpressRoute-cmdletter, skal du importere Azure og ExpressRoute modulerne til PowerShell-session. Kør følgende kommandoer til at importere Azure og ExpressRoute modulerne til PowerShell-session.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Oprette et ExpressRoute kredsløb.**
    
    Følg vejledningen for at oprette et [ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) og har den klargjort af provideren connectivity. Hvis din forbindelse-udbyder tilbyder administrerede Layer 3-tjenester, kan du anmode om udbyderen forbindelse for at kunne aktivere Azure privat peering for dig. Det er tilfældet, behøver du ikke at følge instruktionerne, der er angivet i de næste afsnit. Men hvis udbyderen connectivity ikke administrere distribution til dig, når du har oprettet din kredsløb, skal du følge vejledningen nedenfor. 

3. **Markér ExpressRoute kredsløbet for at sikre, at den er klargjort.**

    Du skal først kontrollere, om ExpressRoute kredsløbet er klargjort og også er aktiveret. Se eksemplet nedenfor.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Sørg for, at kredsløbet viser som Provisioned og aktiveret. Hvis det ikke er tilfældet, kan du arbejde med din connectivity udbyder for at få din kredsløb til den påkrævede tilstand og status.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Konfigurere Azure privat peering for kredsløbet.**

    Sørg for, at du har følgende elementer, før du fortsætter med de næste trin:

    - En /30 undernet til linket primære. Dette må ikke være en del af en hvilken som helst reserveret til virtuelle netværk adresseområde.
    - En /30 undernet til linket sekundær. Dette må ikke være en del af en hvilken som helst reserveret til virtuelle netværk adresseområde.
    - Et gyldigt VLAN ID til at oprette denne peering på. Sikre, at ingen andre peering i kredsløbet bruger det samme VLAN-ID.
    - SOM tal for peering. Du kan bruge både 2-byte og 4 byte som tal. Du kan bruge en privat som tal for denne peering. Sørg for, at du ikke bruger 65515.
    - En MD5 hashværdi, hvis du vælger at bruge en. **Dette er valgfrit**.
    
    Du kan køre følgende cmdlet for at konfigurere Azure privat peering for din kredsløb.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100

    Du kan bruge cmdlet'en til nedenfor, hvis du vælger at bruge en MD5-hash.

        New-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 100 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Sørg for, at du angiver dit nummer, som som peering ASN, ikke kunde ASN.

### <a name="to-view-azure-private-peering-details"></a>At få vist Azure privat peering oplysninger

Du kan finde oplysninger om konfiguration ved hjælp af følgende cmdlet

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100


### <a name="to-update-azure-private-peering-configuration"></a>Opdatere Azure privat peering konfiguration

Du kan opdatere en del af konfigurationen med følgende cmdlet. I eksemplet nedenfor er kredsløbet VLAN ID opdateres fra 100 til 500.

    Set-AzureBGPPeering -AccessType Private -ServiceKey "*********************************" -PrimaryPeerSubnet "10.0.0.0/30" -SecondaryPeerSubnet "10.0.0.4/30" -PeerAsn 1234 -VlanId 500 -SharedKey "A1B2C3D4"

### <a name="to-delete-azure-private-peering"></a>Slette Azure privat peering

Du kan fjerne konfigurationen af peering ved at køre følgende cmdlet.

>[AZURE.WARNING] Du skal sikre dig, at alle virtuelle netværk er ikke er sammenkædet fra ExpressRoute kredsløbet, før du kører denne cmdlet. 

    Remove-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"


## <a name="azure-public-peering"></a>Azure offentlige peering

Dette afsnit indeholder vejledning i at oprette, få, opdatere og slette Azure offentlige peering konfigurationen for et ExpressRoute kredsløb.

### <a name="to-create-azure-public-peering"></a>Oprette Azure offentlige peering

1. **Importere af PowerShell-modulet til ExpressRoute.**
    
    For at begynde at bruge ExpressRoute-cmdletter, skal du importere Azure og ExpressRoute modulerne til PowerShell-session. Kør følgende kommandoer til at importere Azure og ExpressRoute modulerne til PowerShell-session. 

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Oprette et ExpressRoute kredsløb**
    
    Følg vejledningen for at oprette et [ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) og har den klargjort af provideren connectivity. Hvis din forbindelse-udbyder tilbyder administrerede Layer 3-tjenester, kan du anmode om udbyderen forbindelse for at kunne aktivere Azure offentlige peering for dig. Det er tilfældet, behøver du ikke at følge instruktionerne, der er angivet i de næste afsnit. Men hvis udbyderen connectivity ikke administrere distribution til dig, når du har oprettet din kredsløb, skal du følge vejledningen nedenfor.

3. **Kontrollere ExpressRoute kredsløb for at sikre, at den er klargjort**

    Du skal først kontrollere, om ExpressRoute kredsløbet er klargjort og også er aktiveret. Se eksemplet nedenfor.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Sørg for, at kredsløbet viser som Provisioned og aktiveret. Hvis det ikke er tilfældet, kan du arbejde med din connectivity udbyder for at få din kredsløb til den påkrævede tilstand og status.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled

    

4. **Konfigurere Azure offentlige peering for kredsløbet**

    Sørg for, at du har følgende oplysninger, inden du fortsætter yderligere.

    - En /30 undernet til linket primære. Det skal være et gyldigt offentlige IPv4 præfiks.
    - En /30 undernet til linket sekundær. Det skal være et gyldigt offentlige IPv4 præfiks.
    - Et gyldigt VLAN ID til at oprette denne peering på. Sikre, at ingen andre peering i kredsløbet bruger det samme VLAN-ID.
    - SOM tal for peering. Du kan bruge både 2-byte og 4 byte som tal.
    - En MD5 hashværdi, hvis du vælger at bruge en. **Dette er valgfrit**.
    
    Du kan køre følgende cmdlet for at konfigurere Azure offentlige peering for din kredsløb

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200

    Du kan bruge cmdlet'en til nedenfor, hvis du vælger at bruge en MD5-hash

        New-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 200 -SharedKey "A1B2C3D4"

    >[AZURE.IMPORTANT] Sørg for, at du angiver dit nummer, som som peering ASN og ikke kunde ASN.

### <a name="to-view-azure-public-peering-details"></a>At få vist Azure offentlige peering oplysninger

Du kan finde oplysninger om konfiguration ved hjælp af følgende cmdlet

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 131.107.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 131.107.0.4/30
    State                          : Enabled
    VlanId                         : 200


### <a name="to-update-azure-public-peering-configuration"></a>Opdatere Azure offentlige peering konfiguration

Du kan opdatere en del af konfigurationen med følgende cmdlet

    Set-AzureBGPPeering -AccessType Public -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -PeerAsn 1234 -VlanId 600 -SharedKey "A1B2C3D4"

Kredsløbet VLAN ID opdateres fra 200 til 600 i eksemplet ovenfor.

### <a name="to-delete-azure-public-peering"></a>Slette Azure offentlige peering

Du kan fjerne konfigurationen af peering ved at køre følgende cmdlet

    Remove-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

## <a name="microsoft-peering"></a>Microsoft peering

Dette afsnit indeholder vejledning i at oprette, få, opdatere og slette peering konfigurationen af Microsoft til et ExpressRoute kredsløb. 

### <a name="to-create-microsoft-peering"></a>Til at oprette Microsoft peering

1. **Importere af PowerShell-modulet til ExpressRoute.**
    
    For at begynde at bruge ExpressRoute-cmdletter, skal du importere Azure og ExpressRoute modulerne til PowerShell-session. Kør følgende kommandoer til at importere Azure og ExpressRoute modulerne til PowerShell-session.  

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Oprette et ExpressRoute kredsløb**
    
    Følg vejledningen for at oprette et [ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) og har den klargjort af provideren connectivity. Hvis din forbindelse-udbyder tilbyder administrerede Layer 3-tjenester, kan du anmode om udbyderen forbindelse for at kunne aktivere Azure privat peering for dig. Det er tilfældet, behøver du ikke at følge instruktionerne, der er angivet i de næste afsnit. Men hvis udbyderen connectivity ikke administrere distribution til dig, når du har oprettet din kredsløb, skal du følge vejledningen nedenfor.

3. **Kontrollere ExpressRoute kredsløb for at sikre, at den er klargjort**

    Du skal først tjekke for at se, om ExpressRoute kredsløbet er i Provisioned og aktiveret tilstand.

        PS C:\> Get-AzureDedicatedCircuit -ServiceKey "*********************************"

        Bandwidth                        : 200
        CircuitName                      : MyTestCircuit
        Location                         : Silicon Valley
        ServiceKey                       : *********************************
        ServiceProviderName              : equinix
        ServiceProviderProvisioningState : Provisioned
        Sku                              : Standard
        Status                           : Enabled

    Sørg for, at kredsløbet viser som Provisioned og aktiveret. Hvis det ikke er tilfældet, kan du arbejde med din connectivity udbyder for at få din kredsløb til den påkrævede tilstand og status.

        ServiceProviderProvisioningState : Provisioned
        Status                           : Enabled


4. **Konfigurere Microsoft peering for kredsløbet**

    Sørg for, at du har følgende oplysninger, inden du fortsætter.

    - En /30 undernet til linket primære. Det skal være et gyldigt offentlige IPv4 præfiks ejes af dig og er registreret i en RIR / IA.
    - En /30 undernet til linket sekundær. Det skal være et gyldigt offentlige IPv4 præfiks ejes af dig og er registreret i en RIR / IA.
    - Et gyldigt VLAN ID til at oprette denne peering på. Sikre, at ingen andre peering i kredsløbet bruger det samme VLAN-ID.
    - SOM tal for peering. Du kan bruge både 2-byte og 4 byte som tal.
    - Annonceret præfikser: skal du angive en liste over alle præfikser du planlægger at annoncere over BGP session. Kun offentlige IP-adressepræfikser accepteres. Hvis du planlægger at sende et sæt af præfikser, kan du sende en kommasepareret liste. Disse præfikser skal være registreret for dig i en RIR / IA.
    - Kunde ASN: Hvis du er reklame præfikser på enheder, der ikke er registreret til peering som tal, kan du angive det som tal, som de er registreret. **Dette er valgfrit**.
    - Routing registreringsdatabasen navn: Du kan angive RIR / IA mod som det som tal og præfikser er registreret.
    - En MD5-hash, hvis du vælger at bruge en. **Dette er valgfrit.**
    
    Du kan køre følgende cmdlet for at konfigurere Microsoft pering for din kredsløb

        New-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"


### <a name="to-view-microsoft-peering-details"></a>At få vist Microsoft peering oplysninger

Du kan få oplysninger om konfiguration ved hjælp af følgende cmdlet.

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"
    
    AdvertisedPublicPrefixes       : 123.0.0.0/30
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 2245
    PeerAsn                        : 1234
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : ARIN
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 300


### <a name="to-update-microsoft-peering-configuration"></a>Opdatere Microsoft peering konfiguration

Du kan opdatere en del af konfigurationen med følgende cmdlet.

        Set-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************" -PrimaryPeerSubnet "131.107.0.0/30" -SecondaryPeerSubnet "131.107.0.4/30" -VlanId 300 -PeerAsn 1234 -CustomerAsn 2245 -AdvertisedPublicPrefixes "123.0.0.0/30" -RoutingRegistryName "ARIN" -SharedKey "A1B2C3D4"

### <a name="to-delete-microsoft-peering"></a>Sådan sletter du Microsoft peering

Du kan fjerne konfigurationen af peering ved at køre følgende cmdlet.

    Remove-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

## <a name="next-steps"></a>Næste trin

Næste [Link en VNet til en ExpressRoute kredsløb](expressroute-howto-linkvnet-classic.md).


-  Du kan finde flere oplysninger om arbejdsgange [ExpressRoute arbejdsprocesser](expressroute-workflows.md).
-  Du kan finde flere oplysninger om kredsløb peering, se [ExpressRoute kredsløb og routing domæner](expressroute-circuit-peerings.md).

