<properties
   pageTitle="Oprette og redigere et ExpressRoute kredsløb ved hjælp af Ressourcestyring og PowerShell | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du opretter, klargøre, bekræfte, opdatere, slette og deprovision et ExpressRoute kredsløb."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>


# <a name="create-and-modify-an-expressroute-circuit"></a>Oprette og redigere et ExpressRoute kredsløb

> [AZURE.SELECTOR]
[Azure Portal - ressourcestyring](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - ressourcestyring](expressroute-howto-circuit-arm.md)
[PowerShell - klassisk](expressroute-howto-circuit-classic.md)


I denne artikel beskrives, hvordan du opretter et Azure ExpressRoute kredsløb ved hjælp af Windows PowerShell-cmdlet'er og implementeringsmodel Azure ressourcestyring. I denne artikel kan du også se Sådan kontrollere status for kredsløbet, opdatere, eller slette og deprovision den.

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Inden du går i gang


- Hente den nyeste version af Azure PowerShell-moduler (mindst version 1.0). Finde en trinvis vejledning til, hvordan du konfigurerer din computer for at bruge PowerShell-moduler, skal du følge vejledningen i [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

- Gennemse [forudsætninger](expressroute-prerequisites.md) og [arbejdsprocesser](expressroute-workflows.md) , inden du starter konfigurationen.

## <a name="create-and-provision-an-expressroute-circuit"></a>Oprette og klargøre et ExpressRoute kredsløb

### <a name="1-sign-in-to-your-azure-account-and-select-your-subscription"></a>1. log på din Azure-konto, og vælg dit abonnement

Log på din Azure-konto for at starte din konfiguration. Du kan finde flere oplysninger om PowerShell, [Ved hjælp af Windows PowerShell med ressourcestyring](../powershell-azure-resource-manager.md). Brug eksemplerne nedenfor til at hjælpe dig med at oprette forbindelse:

    Login-AzureRmAccount

Kontrollere abonnementer til kontoen:

    Get-AzureRmSubscription

Markere det abonnement, du vil oprette et ExpressRoute kredsløb for:

    Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. hente en liste over understøttede udbydere, steder og båndbredder

Før du opretter et ExpressRoute kredsløb, skal du på listen over understøttede connectivity udbydere, steder og båndbredde indstillinger.

PowerShell-cmdlet `Get-AzureRmExpressRouteServiceProvider` returnerer disse oplysninger, som du skal bruge i efterfølgende trin:

    Get-AzureRmExpressRouteServiceProvider

Kontrollér, om udbyderen connectivity står der. Foretage en note følgende oplysninger, fordi du skal bruge den senere, når du opretter et kredsløb:

- Navn

- PeeringLocations

- BandwidthsOffered

Nu er du klar til at oprette et ExpressRoute kredsløb.   

### <a name="3-create-an-expressroute-circuit"></a>3. Opret et ExpressRoute kredsløb

Hvis du ikke allerede har en ressourcegruppe, skal du oprette én, før du opretter din ExpressRoute kredsløb. Du kan gøre det ved at køre følgende kommando:


    New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


I følgende eksempel viser, hvordan du opretter en 200 Mbps ExpressRoute kredsløb gennem Equinix i Silicon Valley. Hvis du bruger en anden udbyder og forskellige indstillinger, skal du erstatte disse oplysninger, når du foretager din anmodning. Følgende er et eksempel anmodning om en ny tjeneste nøgle:

    New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Sørg for, at du angive den korrekte SKU niveau og SKU familie:

- SKU niveau bestemmer, om en ExpressRoute standard eller et ExpressRoute premium tilføjelsesprogram er aktiveret. Du kan angive *Standard* for at hente standard SKU eller *Premium* til premium-tilføjelsesprogrammet.

- SKU familie bestemmer typen fakturering. Du kan angive *Metereddata* for en forbrugsafregnet dataabonnement og *Unlimiteddata* for en ubegrænset dataabonnement. Bemærk, at du kan ændre typen billing fra *Metereddata* til *Unlimiteddata*, men du kan ikke ændre typen fra *Unlimiteddata* til *Metereddata*.


>[AZURE.IMPORTANT] Din ExpressRoute kredsløb vil blive faktureret fra det tidspunkt, hvor en tjeneste nøgle er udstedt. Sørg for, at du udfører denne handling, når provideren connectivity er klar til at klargøre kredsløbet.

Svaret indeholder tasten tjeneste. Du kan få detaljerede beskrivelser af alle parametre ved at køre følgende:


    get-help New-AzureRmExpressRouteCircuit -detailed


### <a name="4-list-all-expressroute-circuits"></a>4. liste over alle ExpressRoute kredsløb

For at få en liste over alle de ExpressRoute kredsløb, du har oprettet skal køre den `Get-AzureRmExpressRouteCircuit` kommandoen:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Svaret ser nogenlunde sådan i følgende eksempel:


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
    CircuitProvisioningState          : Enabled
    ServiceProviderProvisioningState  : NotProvisioned
    ServiceProviderNotes              :
    ServiceProviderProperties         : {
                                          "ServiceProviderName": "Equinix",
                                          "PeeringLocation": "Silicon Valley",
                                          "BandwidthInMbps": 200
                                        }
    ServiceKey                        : **************************************
    Peerings                          : []

Du kan hente disse oplysninger til enhver tid ved hjælp af den `Get-AzureRmExpressRouteCircuit` cmdlet. Foretager opkald uden parametre viser en liste over alle kredsløb. Produktnøglen tjeneste vises i feltet *ServiceKey* :


    Get-AzureRmExpressRouteCircuit


Svaret ser nogenlunde sådan i følgende eksempel:


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
    ServiceProviderProvisioningState : NotProvisioned
    ServiceProviderNotes             :
    ServiceProviderProperties        : {
                                         "ServiceProviderName": "Equinix",
                                         "PeeringLocation": "Silicon Valley",
                                         "BandwidthInMbps": 200
                                       }
    ServiceKey                       : **************************************
    Peerings                         : []


Du kan få detaljerede beskrivelser af alle parametre ved at køre følgende:


    get-help Get-AzureRmExpressRouteCircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. sende tasten tjeneste til udbyderen af forbindelse til klargøring

*ServiceProviderProvisioningState* indeholder oplysninger om den aktuelle tilstand for klargøring i tjenesteudbyder side. Status indeholder tilstanden i Microsoft-side. Finde flere oplysninger om kredsløb klargøring stater, i artiklen i [arbejdsprocesser](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Når du opretter et nyt ExpressRoute kredsløb, bliver kredsløbet i tilstanden følgende:


    ServiceProviderProvisioningState : NotProvisioned
    CircuitProvisioningState         : Enabled



Kredsløbet ændres til tilstanden følgende, når provideren connectivity er ved at aktivere det for dig:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Du kan bruge et ExpressRoute kredsløb, skal det være i tilstanden følgende:

    ServiceProviderProvisioningState : Provisioned
    CircuitProvisioningState         : Enabled

### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. regelmæssigt kontrollere status og tilstanden for tasten kredsløb

Kontrollere status og tilstanden for tasten kredsløb giver dig besked, når din udbyder har aktiveret dit kredsløb. Efter kredsløbet er konfigureret, *ServiceProviderProvisioningState* vises som *Provisioned*, som vist i følgende eksempel:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


Svaret ser nogenlunde sådan i følgende eksempel:


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

### <a name="7-create-your-routing-configuration"></a>7. Opret din routing konfiguration

Finde en trinvis vejledning i artiklen [konfigurationen af ExpressRoute kredsløb routing](expressroute-howto-routing-arm.md) til at oprette og redigere kredsløb peerings.


>[AZURE.IMPORTANT] Disse instruktioner gælder kun for kredsløb, der er oprettet med tjenesteudbydere, der tilbyder lag 2 forbindelsestjenester. Hvis du bruger en tjenesteudbyder, der indeholder administreret lag 3 tjenester (normalt en IP VPN, som MPLS), udbyderen connectivity skal konfigurere og administrere routing for dig.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. sammenkæde et virtuelt netværk med et ExpressRoute kredsløb

Dernæst skal sammenkæde et virtuelt netværk til din ExpressRoute kredsløb. Brug [sammenkædning virtuelle netværk til ExpressRoute kredsløb](expressroute-howto-linkvnet-arm.md) artiklen, når du arbejder med implementeringsmodel ressourcestyring.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hentning af status for et ExpressRoute kredsløb

Du kan hente disse oplysninger til enhver tid ved hjælp af den `Get-AzureRmExpressRouteCircuit` cmdlet. Foretager opkald uden parametre viser en liste over alle kredsløb.

    Get-AzureRmExpressRouteCircuit


Svaret bliver ligner det følgende eksempel:


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


Du kan få oplysninger om et bestemt ExpressRoute kredsløb ved at overføre ressource gruppenavn og kredsløb navn som en parameter til opkaldet:


    Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


Svaret ser nogenlunde sådan i følgende eksempel:


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


Du kan få detaljerede beskrivelser af alle parametre ved at køre følgende:

    get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Ændre et ExpressRoute kredsløb

Du kan redigere visse egenskaber for et ExpressRoute kredsløb uden at påvirke connectivity.

Du kan gøre følgende med ingen nedetid:

- Aktivere eller deaktivere et ExpressRoute premium tilføjelsesprogram for din ExpressRoute kredsløb.
- Øge båndbredden for din ExpressRoute kredsløb. Bemærk, at nedgradere båndbredden for et kredsløb ikke understøttes.
- Ændre vha planen fra Forbrugsafregnede Data til ubegrænset Data. Bemærk, at ændre den måling planlægge fra ubegrænset Data til Forbrugsafregnede Data understøttes ikke.
-  Du kan aktivere og deaktivere *Tillad klassisk handlinger*.

Se [ExpressRoute ofte stillede spørgsmål om](expressroute-faqs.md)kan finde flere oplysninger om grænser og begrænsninger.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aktivere tilføjelsesprogrammet ExpressRoute premium

Du kan aktivere tilføjelsesprogrammet ExpressRoute premium for din eksisterende kredsløb ved hjælp af de følgende PowerShell-kodestykket:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Premium"
    $ckt.sku.Name = "Premium_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Kredsløbet har nu de ExpressRoute premium tilføjelsesprogram-funktioner, der er aktiveret. Bemærk, at vi begynder fakturering for premium tilføjelsesprogram egenskaben, så snart kommandoen har køre.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Sådan deaktiverer du tilføjelsesprogrammet ExpressRoute premium

>[AZURE.IMPORTANT] Denne handling kan mislykkes, hvis du bruger ressourcer, der er større end det er tilladt for det standard kredsløb.

Bemærk følgende:

- Før du nedgradere fra premium til standard, skal du sikre dig, at antallet af virtuelle netværk, der er knyttet til kredsløbet er mindre end 10. Hvis du ikke gør dette, din anmodning om opdatering mislykkes, og vi sender dig en regning til premium rente.

- Du skal fjerne sammenkædningen alle virtuelle netværk i andre geopolitiske områder. Hvis du ikke gør dette, din anmodning om opdatering mislykkes, og vi sender dig en regning til premium rente.

- Distribuere tabellen skal være mindre end 4.000 omdirigerer til private peering. Hvis distribuere tabellens størrelse er større end 4.000 omdirigerer, BGP sessionen slipper og reenabled ikke, indtil antallet annoncerede præfikser går under 4.000.

Du kan deaktivere tilføjelsesprogrammet premium ExpressRoute til det eksisterende kredsløb ved hjælp af følgende PowerShell-cmdlet:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Tier = "Standard"
    $ckt.sku.Name = "Standard_MeteredData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Opdatere ExpressRoute kredsløb båndbredde

Du kan finde indstillinger for understøttede båndbredde for din udbyder [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md). Du kan vælge en hvilken som helst størrelse, der er større end størrelsen af din eksisterende kredsløb.

>[AZURE.IMPORTANT] Du kan reducere båndbredden for et ExpressRoute kredsløb uden afbrydelser. Nedgradere båndbredde, skal du deprovision ExpressRoute kredsløb og derefter genklargøre et nyt ExpressRoute kredsløb.

Når du beslutter, hvilken størrelse, du skal bruge, kan du bruge følgende kommando til at tilpasse din kredsløb:


    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.ServiceProviderProperties.BandwidthInMbps = 1000

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Din kredsløb kan tilpasses i Microsoft-side. Derefter skal du kontakte din connectivity udbyder for at opdatere konfigurationer på deres side til at matche denne ændring. Når du har foretaget denne meddelelse, kan vi begynder fakturering du indstillingen for opdaterede båndbredde.


### <a name="to-move-the-sku-from-metered-to-unlimited"></a>Hvis du vil flytte SKU fra forbrugsafregnede til ubegrænset

Du kan ændre SKU af et ExpressRoute kredsløb ved hjælp af de følgende PowerShell-kodestykket:

    $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

    $ckt.Sku.Family = "UnlimitedData"
    $ckt.sku.Name = "Premium_UnlimitedData"

    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>At styre adgangen til klassiske og ressourcestyring miljøer  

Gennemse vejledningen i [flytte ExpressRoute kredsløb fra klassiske til implementeringsmodel ressourcestyring](expressroute-howto-move-arm.md).  


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Ophævelse af klargøring og sletning af et ExpressRoute kredsløb

Bemærk følgende:

- Du skal fjerne sammenkædningen alle virtuelle netværk fra ExpressRoute kredsløbet. Hvis handlingen mislykkes, skal du kontrollere for at se, hvis nogen virtuelle netværk er sammenkædet med kredsløbet.

- Hvis ExpressRoute kredsløb-udbyder klargøring tjenestetilstand er **artikel Provisioning** eller **Provisioned** skal du arbejde med din udbyder og deprovision kredsløb på deres side. Vi fortsætter med at reservere ressourcer, som du faktureres før udbyderen af er fuldført ophævelse af klargøring kredsløbet og giver os.

- Du kan derefter slette kredsløbet, hvis udbyderen af har fjernet det kredsløb (-udbyder klargøring tjenestetilstand er indstillet til **ikke klargjort**). Dette standser fakturering for kredsløbet

Du kan slette din ExpressRoute kredsløb ved at køre følgende kommando:

    Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## <a name="next-steps"></a>Næste trin

Når du opretter din kredsløb, skal du sørge for, at du gøre følgende:

- [Oprette og redigere distribution til din ExpressRoute kredsløb](expressroute-howto-routing-arm.md)
- [Sammenkæde netværket virtuel til din ExpressRoute kredsløb](expressroute-howto-linkvnet-arm.md)
