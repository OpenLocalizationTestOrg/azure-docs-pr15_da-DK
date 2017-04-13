<properties
   pageTitle="Oprette VNet Peering ved hjælp af Powershell-cmdlet'er | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter et virtuelt netværk ved hjælp af portalen Azure i ressourcestyring."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Oprette VNet Peering ved hjælp af Powershell-cmdlet'er

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Hvis du vil oprette en VNet peering ved hjælp af PowerShell, skal du følge nedenstående trin:

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../powershell-install-configure.md) , og følg instruktionerne helt til end for at logge på Azure, og vælg dit abonnement.

> [AZURE.NOTE] PowerShell-cmdlet til administration af VNet peering leveres med [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Læs virtuelt netværksobjekter:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. For at oprette VNet peering, skal du oprette to links, en for hver retning. Følgende trin kan oprette et VNet peering link til VNet1 til VNet2 først:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Output vises:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Dette trin opretter et VNet peering link til VNet2 til VNet1:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Output vises:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Når linket VNet peering er oprettet, kan du se tilstanden link nedenfor:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Output vises:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Der er et par kan konfigureres egenskaberne for VNet peering:

  	|Indstillingen|Beskrivelse|Standard|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Om en adresse for Peer VNet der skal medtages som en del af mærket Virtual_network|Ja|
  	|AllowForwardedTraffic|Om trafik ikke stammer fra en peered VNet accepteret eller afvist|Nej|
  	|AllowGatewayTransit|Gør det muligt for peer VNet at bruge din VNet gateway|Nej|
  	|UseRemoteGateways|Brug din peer VNet gateway. Peer VNet skal have en gateway, der er konfigureret og AllowGatewayTransit markeret. Du kan ikke bruge denne indstilling, hvis du har en gateway, der er konfigureret|Nej|

    Hvert link i VNet peering har sæt egenskaber ovenfor. Du kan for eksempel angive AllowVirtualNetworkAccess til Sand for VNet peering link VNet1 for at VNet2 og angive den til False til VNet peering linket i anden retningen.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Du kan køre Get-AzureRmVirtualNetworkPeering til dobbelte Kontrollér egenskabsværdien efter ændringen. Fra outputtet, kan du se AllowForwardedTraffic ændres sæt til sand når du har kørt cmdlet'erne ovenfor.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Når peering er oprettet i dette scenarie, skal du kunne starte forbindelser fra en hvilken som helst virtuel maskine til en hvilken som helst virtuelt af begge VNets. AllowVirtualNetworkAccess er SAND som standard, og VNet peering vil klargøre stort ACLs for at tillade kommunikationen mellem VNets. Du kan stadig anvende netværk sikkerhed gruppe (NSG) regler for at blokere forbindelsen mellem bestemte undernet eller virtuelle maskiner få fin korn kontrol af adgang mellem to virtuelle netværk.  Se denne [artikel](virtual-networks-create-nsg-arm-ps.md)kan finde flere oplysninger om oprettelse af NSG regler.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Hvis du vil oprette VNet peering på tværs af abonnementer ved hjælp af PowerShell, skal du følge nedenstående trin:

1. Log på Azure med rettigheder bruger A er kontoen for abonnement A og køre følgende cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Dette er ikke et krav peering kan oprettes, selvom brugere hæve individuelt peering anmodninger til deres respektive VNets, som svarer til anmodningerne. Tilføje en bruger med rettigheder af de andre VNet som en bruger i det lokale VNet gør det nemmere at udføre konfigurationen.

2. Log på Azure med privilegerede bruger-BS konto til abonnement-B og køre følgende cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. Bruger A er i login session, du køre følgende cmdlet nedenfor:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. I bruger B login session, du køre følgende cmdlet nedenfor:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Når peering er oprettet, skal der være en hvilken som helst virtuel maskine i VNet3 kan kommunikere med en hvilken som helst virtuel maskine i VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. I dette scenarie skal køre du den PowerShell-cmdletter nedenfor for at oprette VNet peering.  Du har brug at angive egenskaben AllowForwardedTraffic til SAND og sammenkæde VNET1 med HubVNet, som giver mulighed for den indgående trafik fra uden for peering VNet-adresseområde.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Når peering er oprettet, kan du referere til denne [artikel](virtual-network-create-udr-arm-ps.md) og definere en brugerdefineret rute (UDR) til at omdirigere VNet1 trafik via et virtuelt maskinen at bruge dens egenskaber. Når du angiver den næste hopadresse i ruten, kan du konfigurere den virtuelle maskinen i peer VNet HubVNet IP-adresse. Nedenfor er et eksempel:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Hvis du vil oprette en VNet peering mellem et klassisk virtuelt netværk og et virtuelt netværk med Azure ressourcestyring i PowerShell, skal du følge nedenstående trin:

1. Læs virtuelt netværk objekt for **VNET1**, det virtuelle netværk Azure Ressourcestyring på følgende måde:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. For at oprette VNet peering i dette scenarie, der er behov for kun én link, specifikt et link fra **VNET1** til **VNET2**. Dette trin kræver, at kende dine klassisk VNet ressource-ID. Formatet for ressource-ID ser sådan ud:

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Sørg for at erstatte SubscriptionID, ResourceGroupName og VirtualNetworkName med de relevante navne.

    Dette kan gøres ved følgende:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Én gang på VNet peering link er oprettet, kan du se tilstanden link som vist i nedenstående output:

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Fjerne VNet Peering

1.  Hvis du vil fjerne VNet peering, skal du køre følgende cmdlet:

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Når du fjerner et link i en VNET peering, går tilstanden peer link til afbrudt. I denne tilstand, ikke kan du genoprette linket indtil tilstanden peer link ændres til startet. Vi anbefaler, at du fjerner begge links, før du genskabe VNet peering.
