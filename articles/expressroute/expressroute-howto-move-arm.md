<properties
   pageTitle="Flytte ExpressRoute kredsløb fra klassisk til Ressourcestyring | Microsoft Azure"
   description="Denne side beskrives det, hvordan du flytter et klassisk kredsløb til implementeringsmodel ressourcestyring."
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


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Flytte ExpressRoute kredsløb fra klassiske til implementeringsmodel ressourcestyring

## <a name="configuration-prerequisites"></a>Forudsætninger for konfiguration

- Du har brug for den seneste version af Azure PowerShell-moduler (mindst version 1.0).
- Sørg for, at du har gennemgået [forudsætninger](expressroute-prerequisites.md), [routing krav](expressroute-routing.md)og [arbejdsprocesser](expressroute-workflows.md) , inden du starter konfigurationen.
- Gennemse oplysninger, der findes under [flytning af et ExpressRoute kredsløb fra klassisk til Ressourcestyring](expressroute-move.md)før før yderligere. Sørg for, at du fuldt har forstået grænser og begrænsninger i hvad der er muligt.
- Hvis du vil flytte et Azure ExpressRoute kredsløb fra den klassiske implementeringsmodel til Azure ressourcestyring implementeringsmodel, skal du have kredsløb fuldt konfigureret og drift i modellen Klassisk installation.
- Sørg for, at du har en ressourcegruppe, der er oprettet i implementeringsmodel ressourcestyring.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Flytte ExpressRoute kredsløbet til implementeringsmodel ressourcestyring

Du skal flytte et ExpressRoute kredsløb til implementeringsmodel Ressourcestyring, så du kan bruge den på tværs af både klassiske og ressourcestyring installation modeller. Du kan gøre dette ved at køre følgende PowerShell-kommandoer.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Trin 1: Indsaml kredsløb oplysninger fra den klassiske implementeringsmodel

Du har brug at indsamle oplysninger om din ExpressRoute kredsløb først.

Log på Azure klassisk miljøet, og Indsaml tasten tjeneste. Du kan bruge følgende PowerShell-kodestykke til at indsamle oplysninger:

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Kopiér den **tjenestens nøgle** i det kredsløb, du vil flytte til implementeringsmodel ressourcestyring.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Trin 2: Log på ressourcestyring-miljø, og oprette en ny ressourcegruppe

Du kan oprette en ny ressourcegruppe ved hjælp af følgende kodestykke:

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Du kan også bruge en eksisterende ressourcegruppe, hvis du allerede har en.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Trin 3: Flytte ExpressRoute kredsløb til implementeringsmodel ressourcestyring

Du er nu klar til at flytte over din ExpressRoute kredsløb fra klassiske til implementeringsmodel ressourcestyring. Gennemse oplysningerne i afsnittet [flytte et ExpressRoute kredsløb fra klassiske til implementeringsmodel ressourcestyring](expressroute-move.md) , før du går videre.

Du kan gøre dette ved at køre følgende kodestykke:

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Når Flyt er afsluttet, som det nye navn, der er angivet i den forrige cmdlet skal bruges til en adresse til ressourcen. Kredsløbet grundlæggende omdøbes.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Aktivere et ExpressRoute kredsløb for begge installation modeller

Før kontrol af adgang til implementeringsmodel, skal du flytte dine ExpressRoute kredsløb til implementeringsmodel ressourcestyring.

Kør følgende cmdlet for at få adgang til begge installation modeller:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Når handlingen blev fuldført, vil du kunne se kredsløbet i modellen Klassisk installation.

Kør følgende for at få vist flere detaljer i ExpressRoute kredsløbet:

    get-azurededicatedcircuit

Du skal kunne se tasten tjeneste vises. Du kan nu administrere links til de ExpressRoute kredsløb, ved hjælp af din standard klassisk installation model kommandoer til klassisk VNets og din standard ARM kommandoer til ARM VNETs. I følgende artikler fører dig gennem Sådan administreres links til ExpressRoute kredsløb:

- [Sammenkæde netværket virtuel til din ExpressRoute kredsløb i implementeringsmodel ressourcestyring](expressroute-howto-linkvnet-arm.md)
- [Sammenkæde netværket virtuel til din ExpressRoute kredsløb i modellen Klassisk installation](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Deaktivere ExpressRoute kredsløb modellen med klassisk installation

Kør følgende cmdlet for at deaktivere adgang til den klassiske implementeringsmodel:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Når handlingen blev fuldført, kan du ikke kan se kredsløbet i modellen Klassisk installation.

## <a name="next-steps"></a>Næste trin

Når du opretter din kredsløb, skal du sørge for, at du gøre følgende:

- [Oprette og redigere distribution til din ExpressRoute kredsløb](expressroute-howto-routing-arm.md)
- [Sammenkæde netværket virtuel til din ExpressRoute kredsløb](expressroute-howto-linkvnet-arm.md)
