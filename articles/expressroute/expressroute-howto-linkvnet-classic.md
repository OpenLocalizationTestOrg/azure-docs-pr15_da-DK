<properties
   pageTitle="Sammenkæde et virtuelt netværk med et ExpressRoute kredsløb ved hjælp af klassisk implementeringsmodel og PowerShell | Microsoft Azure"
   description="Dette dokument indeholder en oversigt over, hvordan du kan sammenkæde virtuelle netværk (VNets) til ExpressRoute kredsløb ved hjælp af klassisk implementeringsmodel og PowerShell."
   services="expressroute"
   documentationCenter="na"
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
   ms.author="ganesr" />

# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Sammenkæde et virtuelt netværk med et ExpressRoute kredsløb

> [AZURE.SELECTOR]
- [Azure Portal - ressourcestyring](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - ressourcestyring](expressroute-howto-linkvnet-arm.md)
- [PowerShell - klassisk](expressroute-howto-linkvnet-classic.md)



I denne artikel kan du sammenkæde virtuelle netværk (VNets) med Azure ExpressRoute kredsløb ved hjælp af klassisk implementeringsmodel og PowerShell. Virtuelt Network kan enten være i samme abonnement eller kan være en del af et andet abonnement.

**Om Azure-installation modeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Forudsætninger for konfiguration

1. Du har brug for den seneste version af Azure PowerShell-moduler. Du kan hente de seneste PowerShell-moduler fra PowerShell sektionen på [siden Azure overførsler](https://azure.microsoft.com/downloads/). Følg vejledningen i, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) finde en trinvis vejledning til, hvordan du konfigurerer din computer for at bruge Azure PowerShell-moduler.
2. Du skal gennemgå [forudsætninger](expressroute-prerequisites.md), [routing krav](expressroute-routing.md)og [arbejdsprocesser](expressroute-workflows.md) , inden du starter konfigurationen.
3. Du skal have et aktivt ExpressRoute kredsløb.
    - Følg vejledningen for at [oprette et ExpressRoute kredsløb](expressroute-howto-circuit-classic.md) og har udbyderen connectivity aktivere kredsløbet.
    - Sørg for, at du har Azure privat peering konfigureret for din kredsløb. Se artiklen [konfigurere routing](expressroute-howto-routing-classic.md) routing vejledning.
    - Sikre, at Azure private peering er konfigureret og BGP peering mellem dit netværk og Microsoft er oprettet, så du kan aktivere til slut connectivity.
    - Du skal have et virtuelt netværk og et virtuelt netværk gatewayen oprettet og fuldt klargjort. Følg vejledningen for at [konfigurere et virtuelt netværk for ExpressRoute](expressroute-howto-vnet-portal-classic.md).

Du kan sammenkæde op til 10 virtuelle netværk til en ExpressRoute kredsløb. Alle virtuelle netværk skal være i samme geopolitiske område. Du kan sammenkæde et større antal virtuelle netværk til din ExpressRoute kredsløb eller link virtuelle netværk, der er i andre geopolitiske områder, hvis du har aktiveret ExpressRoute premium tilføjelsesprogrammet. Se [ofte stillede spørgsmål](expressroute-faqs.md) til flere oplysninger om tilføjelsesprogrammet premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Forbinde et virtuelt netværk i samme abonnement til et kredsløb

Du kan sammenkæde et virtuelt netværk til en ExpressRoute kredsløb ved hjælp af følgende cmdlet. Sørg for, at gatewayen virtuelt netværk oprettes og er klar til at sammenkæde, før du køre følgende cmdlet.

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
    Provisioned

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Forbinde et virtuelt netværk i et andet abonnement til et kredsløb

Du kan dele et ExpressRoute kredsløb på tværs af flere abonnementer. I følgende figur vises en enkel skematiske af hvordan deling fungerer for ExpressRoute kredsløb på tværs af flere abonnementer.

Hver af de mindre skyer i store skyen bruges til at repræsentere abonnementer, der tilhører forskellige afdelinger i en organisation. Hver af afdelinger i organisationen kan bruge deres egen abonnement til implementering af deres services – men afdelingerne kan dele et enkelt ExpressRoute kredsløb til at oprette forbindelse tilbage til dit lokale netværk. En enkelt afdeling (i dette eksempel: IT) kan ejer ExpressRoute kredsløb. Andre abonnementer i organisationen kan bruge ExpressRoute kredsløbet.

>[AZURE.NOTE] Forbindelsen og båndbredde gebyrer for dedikeret kredsløb anvendes til ExpressRoute kredsløb ejer. Alle virtuelle netværk dele den samme båndbredde.

![Forbindelse i tværs-abonnement](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Administration

*Kredsløb ejer* er administrator/coadministrator af det abonnement, som ExpressRoute kredsløbet oprettes. Ejeren af kredsløb kan Godkend administratorer/coadministrators af andre abonnementer, kaldes *kredsløb brugere*, hvis du vil bruge det dedikerede kredsløb, de ejer. Kredsløb brugere, der er autoriseret til at bruge organisationens ExpressRoute kredsløb kan sammenkæde det virtuelle netværk i deres abonnement med ExpressRoute kredsløbet, når de har tilladelse.

Kredsløb ejeren har mulighed for at redigere og ophæve tilladelser når som helst. Tilbagekalde en tilladelse medfører alle kæder, blive slettet fra det abonnement, hvis adgang blev tilbagekaldt.

### <a name="circuit-owner-operations"></a>Kredsløb ejer handlinger

#### <a name="creating-an-authorization"></a>Oprette en tilladelse

Ejeren af kredsløb godkender administratorer af andre abonnementer til at bruge den angivne kredsløb. I eksemplet nedenfor kan administratoren af kredsløb (Contoso IT) administratoren af et andet abonnement (Udviklingscenter-Test) sammenkæde op til to virtuelle netværk med kredsløbet. Contoso IT-administratoren kan dette ved at angive Udviklingscenter-Test Microsoft-ID. Cmdletten sende ikke mails til det angivne Microsoft-ID. Ejeren af kredsløb skal eksplicit besked om andre ejeren af abonnementet, tilladelsen er fuldført.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

#### <a name="reviewing-authorizations"></a>Gennemgå tilladelser

Ejeren af kredsløb kan gennemse alle de tilladelser, der er udstedt på et bestemt kredsløb ved at køre følgende cmdlet:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

    Description         : EngineeringTeam
    Limit               : 3
    LinkAuthorizationId : ####################################
    MicrosoftIds        : engadmin@contoso.com
    Used                : 1

    Description         : MarketingTeam
    Limit               : 1
    LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
    MicrosoftIds        : marketingadmin@contoso.com
    Used                : 0

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : salesadmin@contoso.com
    Used                : 2


#### <a name="updating-authorizations"></a>Opdatering af tilladelser

Kredsløb ejeren kan ændre tilladelser ved hjælp af følgende cmdlet:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


#### <a name="deleting-authorizations"></a>Sletning af tilladelser

Ejeren af kredsløb kan revoke/Slet tilladelser til brugeren, ved at køre følgende cmdlet:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Kredsløb bruger handlinger

#### <a name="reviewing-authorizations"></a>Gennemgå tilladelser

Kredsløb brugeren kan gennemse tilladelser ved hjælp af følgende cmdlet:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

#### <a name="redeeming-link-authorizations"></a>Indløse link tilladelser

Kredsløb brugeren kan køre følgende cmdlet for at indløse en link tilladelse:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om ExpressRoute, [ExpressRoute ofte stillede spørgsmål](expressroute-faqs.md).
