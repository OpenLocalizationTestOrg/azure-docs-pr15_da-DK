<properties
   pageTitle="Oprette og redigere et ExpressRoute kredsløb ved hjælp af klassisk implementeringsmodel og PowerShell | Microsoft Azure"
   description="I denne artikel fører dig gennem trinnene til oprettelse og klargør et ExpressRoute kredsløb. I denne artikel også viser, hvordan du kan tjekke status, Opdater eller Slet og deprovision din kredsløb."
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
   ms.author="ganesr;cherylmc"/>

# <a name="create-and-modify-an-expressroute-circuit"></a>Oprette og redigere et ExpressRoute kredsløb

> [AZURE.SELECTOR]
[Azure Portal - ressourcestyring](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - ressourcestyring](expressroute-howto-circuit-arm.md)
[PowerShell - klassisk](expressroute-howto-circuit-classic.md)

I denne artikel fører dig gennem trinnene til at oprette et Azure ExpressRoute kredsløb ved hjælp af PowerShell-cmdletter og klassisk implementeringsmodel. I denne artikel kan du også se Sådan Kontrollér status, Opdater eller Slet og deprovision et ExpressRoute kredsløb.

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="before-you-begin"></a>Inden du går i gang

### <a name="1-review-the-prerequisites-and-workflow-articles"></a>1. Gennemse forudsætninger og arbejdsproces artikler

Sørg for, at du har gennemgået [forudsætninger](expressroute-prerequisites.md) og [arbejdsprocesser](expressroute-workflows.md) , inden du starter konfigurationen.  


### <a name="2-install-the-latest-versions-of-the-azure-powershell-modules"></a>2. Installer den seneste version af Azure PowerShell-moduler 

Følg vejledningen i, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) finde en trinvis vejledning til, hvordan du konfigurerer din computer for at bruge Azure PowerShell-moduler.

### <a name="3-log-in-to-your-azure-account-and-select-a-subscription"></a>3. log på din Azure-konto, og vælg et abonnement

1. Kør følgende cmdlet ved hjælp af en øgede Windows PowerShell-prompt:

        Add-AzureAccount
2. På Log på skærmbilledet, der vises, skal du logge på din konto.

3. Få en liste over dine abonnementer.

        Get-AzureSubscription
4. Vælg det abonnement, du vil bruge.
    
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Oprette og klargøre et ExpressRoute kredsløb

### <a name="1-import-the-powershell-modules-for-expressroute"></a>1. importere PowerShell-moduler til ExpressRoute

 Hvis du ikke allerede har gjort det, skal du importere Azure og ExpressRoute modulerne til PowerShell-session for at begynde at bruge ExpressRoute-cmdletter. Du kan importere modulerne fra den placering, som de blev installeret til på din lokale computer. Afhængigt af den metode, du brugte til at installere modulerne, placeringen muligvis anderledes end i følgende eksempel vises. Ændre eksemplet, hvis det er nødvendigt.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>2. hente en liste over understøttede udbydere, steder og båndbredder

Før du opretter et ExpressRoute kredsløb, skal du på listen over understøttede connectivity udbydere, steder og båndbredde indstillinger.

PowerShell-cmdlet `Get-AzureDedicatedCircuitServiceProvider` returnerer disse oplysninger, som du skal bruge i efterfølgende trin:

    Get-AzureDedicatedCircuitServiceProvider

Kontrollér, om udbyderen connectivity står der. Foretage en note følgende oplysninger, fordi du skal bruge den senere, når du opretter et kredsløb:

- Navn

- PeeringLocations

- BandwidthsOffered

Nu er du klar til at oprette et ExpressRoute kredsløb.         

### <a name="3-create-an-expressroute-circuit"></a>3. Opret et ExpressRoute kredsløb

I følgende eksempel viser, hvordan du opretter en 200 Mbps ExpressRoute kredsløb gennem Equinix i Silicon Valley. Hvis du bruger en anden udbyder og forskellige indstillinger, skal du erstatte disse oplysninger, når du foretager din anmodning.

>[AZURE.IMPORTANT] Din ExpressRoute kredsløb vil blive faktureret fra det tidspunkt, hvor en tjeneste nøgle er udstedt. Sørg for, at du udfører denne handling, når provideren connectivity er klar til at klargøre kredsløbet.


Følgende er et eksempel anmodning om en ny tjeneste nøgle:

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Eller hvis du vil oprette et ExpressRoute kredsløb med tilføjelsesprogrammet premium, skal du bruge følgende eksempel. Se [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md) få mere at vide om tilføjelsesprogrammet premium.

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


Svaret skal indeholde service-nøglen. Du kan få detaljerede beskrivelser af alle parametre ved at køre følgende:

    get-help new-azurededicatedcircuit -detailed

### <a name="4-list-all-the-expressroute-circuits"></a>4. liste over alle ExpressRoute kredsløb

Du kan køre den `Get-AzureDedicatedCircuit` kommando for at få en liste over alle de ExpressRoute kredsløb, du har oprettet:


    Get-AzureDedicatedCircuit

Svaret bliver noget lignende i følgende eksempel:

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Du kan hente disse oplysninger til enhver tid ved hjælp af den `Get-AzureDedicatedCircuit` cmdlet. Foretager opkald uden parametre viser en liste over alle kredsløb. Produktnøglen tjeneste vises i feltet *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Du kan få detaljerede beskrivelser af alle parametre ved at køre følgende:

    get-help get-azurededicatedcircuit -detailed

### <a name="5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>5. sende tasten tjeneste til udbyderen af forbindelse til klargøring


*ServiceProviderProvisioningState* indeholder oplysninger om den aktuelle tilstand for klargøring i tjenesteudbyder side. *Status* indeholder tilstanden i Microsoft-side. Finde flere oplysninger om kredsløb klargøring stater, i artiklen i [arbejdsprocesser](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Når du opretter et nyt ExpressRoute kredsløb, bliver kredsløbet i tilstanden følgende:

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Kredsløbet går til tilstanden følgende, når provideren connectivity er ved at aktivere det for dig:

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Et ExpressRoute kredsløb skal være i den følgende tilstand for dig at kunne bruge den:

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>6. regelmæssigt kontrollere status og tilstanden for tasten kredsløb

Dette giver dig besked, når din udbyder har aktiveret dit kredsløb. Når kredsløbet er konfigureret, vises *ServiceProviderProvisioningState* som *Provisioned* , som vist i følgende eksempel:

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="7-create-your-routing-configuration"></a>7. Opret din routing konfiguration

Refererer til den [ExpressRoute kredsløb konfigurationen routing (oprette og redigere kredsløb peerings)](expressroute-howto-routing-classic.md) artikel for at få en trinvis vejledning.

>[AZURE.IMPORTANT] Disse instruktioner gælder kun for kredsløb, der er oprettet med tjenesteudbydere, der tilbyder lag 2 forbindelsestjenester. Hvis du bruger en tjenesteudbyder, der indeholder administreret lag 3 tjenester (normalt en IP VPN, som MPLS), udbyderen connectivity skal konfigurere og administrere routing for dig.

### <a name="8-link-a-virtual-network-to-an-expressroute-circuit"></a>8. sammenkæde et virtuelt netværk med et ExpressRoute kredsløb

Dernæst skal sammenkæde et virtuelt netværk til din ExpressRoute kredsløb. Referere til [sammenkædning ExpressRoute kredsløb til virtuelle netværk](expressroute-howto-linkvnet-classic.md) til en trinvis vejledning. Hvis du vil oprette et virtuelt netværk ved hjælp af den klassiske implementeringsmodel for ExpressRoute skal du se [oprette et virtuelt netværk for ExpressRoute](expressroute-howto-vnet-portal-classic.md) vejledning.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Hentning af status for et ExpressRoute kredsløb

Du kan hente disse oplysninger til enhver tid ved hjælp af den `Get-AzureCircuit` cmdlet. Foretager opkald uden parametre viser en liste over alle kredsløb.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Du kan få oplysninger om et bestemt ExpressRoute kredsløb ved at overføre tasten service som en parameter til opkaldet:

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Du kan få detaljerede beskrivelser af alle parametre ved at køre følgende:

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Ændre et ExpressRoute kredsløb

Du kan redigere visse egenskaber for et ExpressRoute kredsløb uden at påvirke connectivity.

Du kan gøre følgende med ingen nedetid:

- Aktivere eller deaktivere et ExpressRoute premium tilføjelsesprogram for din ExpressRoute kredsløb.
- Øge båndbredden for din ExpressRoute kredsløb. Bemærk, at nedgradere båndbredden for et kredsløb ikke understøttes.
- Ændre vha planen fra Forbrugsafregnede Data til ubegrænset Data. Bemærk, at ændre den måling planlægge fra ubegrænset Data til Forbrugsafregnede Data understøttes ikke.
- Du kan aktivere og deaktivere *Tillad klassisk handlinger*.

Se [Ofte stillede spørgsmål om ExpressRoute](expressroute-faqs.md) til flere oplysninger om grænser og begrænsninger.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Aktivere tilføjelsesprogrammet ExpressRoute premium

Du kan aktivere tilføjelsesprogrammet ExpressRoute premium for din eksisterende kredsløb ved hjælp af følgende PowerShell-cmdlet:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Din kredsløb har nu de ExpressRoute premium tilføjelsesprogram-funktioner, der er aktiveret. Bemærk, at vi begynder fakturering for premium tilføjelsesprogram egenskaben, så snart kommandoen har køre.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Sådan deaktiverer du tilføjelsesprogrammet ExpressRoute premium

>[AZURE.IMPORTANT] Denne handling kan mislykkes, hvis du bruger ressourcer, der er større end det er tilladt for det standard kredsløb.

Bemærk følgende:

- Du skal sikre, at antallet af virtuelle netværk, der er knyttet til kredsløbet er mindre end 10, før du nedgradere fra premium til standard. Hvis du ikke gør dette, din anmodning om opdatering mislykkes, og du kan faktureres premium satser.

- Du skal fjerne sammenkædningen alle virtuelle netværk i andre geopolitiske områder. Hvis du ikke gør dette, din anmodning om opdatering mislykkes, og du kan faktureres premium satser.

- Distribuere tabellen skal være mindre end 4.000 omdirigerer til private peering. Hvis dirigere tabellens størrelse er større end 4.000 omdirigerer, BGP sessionen vil slippe og reenabled ikke, indtil antallet annoncerede præfikser går under 4.000.

Du kan deaktivere tilføjelsesprogrammet ExpressRoute premium for din eksisterende kredsløb ved hjælp af følgende PowerShell-cmdlet:

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Opdatere ExpressRoute kredsløb båndbredde

Se [ExpressRoute ofte stillede spørgsmål om](expressroute-faqs.md) understøttede båndbredde af indstillingerne for din udbyder. Du kan vælge en hvilken som helst størrelse, der er større end størrelsen af din eksisterende kredsløb, som giver mulighed for den fysiske port (som din kredsløb er oprettet).

>[AZURE.IMPORTANT] Du kan reducere båndbredden for et ExpressRoute kredsløb uden afbrydelser. Nedgradere båndbredde kræver, at du deprovision ExpressRoute kredsløb og derefter genklargøre et nyt ExpressRoute kredsløb.

Når du beslutter, hvilken størrelse, du skal bruge, kan du bruge følgende kommando til at tilpasse din kredsløb:

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Din kredsløb vil har blevet tilpasset i Microsoft-side. Du skal kontakte udbyderen forbindelse for at opdatere konfigurationer på deres side til at matche denne ændring. Bemærk, at vi begynder fakturering du indstillingen for opdaterede båndbredde fra dette punkt på.

Hvis du får vist følgende fejlmeddelelse, når øge kredsløb båndbredde, betyder det der er ingen tilstrækkelig båndbredde til venstre på den fysiske port, hvor din eksisterende kredsløb er oprettet. Du skal slette dette kredsløb og oprette et nyt kredsløb af den ønskede størrelse. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
    

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Ophævelse af klargøring og sletning af et ExpressRoute kredsløb

Bemærk følgende:

- Du skal fjerne sammenkædningen alle virtuelle netværk fra ExpressRoute kredsløbet for denne handling kan udføres. Kontrollér, om du har nogen virtuelle netværk, der er knyttet til kredsløbet, hvis handlingen mislykkes.

- Hvis ExpressRoute kredsløb-udbyder klargøring tjenestetilstand er **artikel Provisioning** eller **Provisioned** skal du arbejde med din udbyder og deprovision kredsløb på deres side. Vi fortsætter med at reservere ressourcer, som du faktureres før udbyderen af er fuldført ophævelse af klargøring kredsløbet og giver os.

- Du kan derefter slette kredsløbet, hvis udbyderen af har fjernet det kredsløb (-udbyder klargøring tjenestetilstand er indstillet til **ikke klargjort**). Dette standser fakturering for kredsløbet.

Du kan slette din ExpressRoute kredsløb ved at køre følgende kommando:

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Næste trin

Når du opretter din kredsløb, skal du sørge for, at du gøre følgende:

- [Oprette og redigere distribution til din ExpressRoute kredsløb](expressroute-howto-routing-classic.md)
- [Sammenkæde netværket virtuel til din ExpressRoute kredsløb](expressroute-howto-linkvnet-classic.md)
