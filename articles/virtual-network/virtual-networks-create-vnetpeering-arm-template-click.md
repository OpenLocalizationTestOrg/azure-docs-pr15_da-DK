<properties
   pageTitle="Oprette VNet Peering ved hjælp af Ressourcestyring skabeloner | Microsoft Azure"
   description="Få mere at vide, hvordan du opretter et virtuelt netværk peering ved hjælp af skabelonerne i ressourcestyring."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
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
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Oprette VNet Peering ved hjælp af Ressourcestyring skabeloner

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Hvis du vil oprette en VNet peering ved hjælp af Ressourcestyring skabeloner, skal du følge nedenstående trin:

1. Hvis du aldrig har brugt Azure PowerShell, se, [hvordan du installere og konfigurere Azure PowerShell](../powershell-install-configure.md) , og følg instruktionerne helt til end for at logge på Azure, og vælg dit abonnement.

    > [AZURE.NOTE] PowerShell-cmdlet til administration af VNet peering leveres med [Azure PowerShell 1,6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Teksten nedenfor viser definitionen af et VNet peering link til VNet1 til VNet2, baseret på ovenstående scenarie. Kopiér indholdet nedenfor og gemme den på en fil med navnet VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. Afsnittet nedenfor viser definitionen af et VNet peering link til VNet2 til VNet1, baseret på ovenstående scenarie.  Kopiér indholdet nedenfor og gemme den på en fil med navnet VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Som det fremgår skabelonen ovenfor, er der et par kan konfigureres egenskaberne for VNet peering:

  	|Indstillingen|Beskrivelse|Standard|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Hvorvidt adresseområde af en peer VNet er inkluderet som en del af mærket virtual_network.|Ja|
  	|AllowForwardedTraffic|Om trafik ikke stammer fra en peered VNet er accepteret eller afvist.|Nej|
  	|AllowGatewayTransit|Gør det muligt for peer VNet at bruge din VNet gateway.|Nej|
  	|UseRemoteGateways|Brug din peer VNet gateway. Peer VNet skal have en gateway, der er konfigureret og AllowGatewayTransit markeret. Du kan ikke bruge denne indstilling, hvis du har en gateway, der er konfigureret.|Nej|

    Hvert link i VNet peering har sæt egenskaber ovenfor. Du kan for eksempel angive AllowVirtualNetworkAccess til Sand for VNet peering link VNet1 for at VNet2 og angive den til False til VNet peering linket i anden retningen.


4. Hvis du vil installere skabelonfilen, kan du køre ny AzureRmResourceGroupDeployment cmdlet for at oprette eller opdatere installationen. Se denne [artikel](../resource-group-template-deploy.md)kan finde flere oplysninger om brug af Ressourcestyring skabeloner.

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Udskift gruppe navn og skabelon ressourcefilen efter behov.

    Under bygger et eksempel på ovenstående scenarie:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Output vises:

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Output vises:

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Når installationen er fuldført, kan du køre nedenfor for at få vist tilstanden peering cmdlet:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Output vises:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Når peering er oprettet i dette scenarie, skal du kunne starte forbindelser fra en hvilken som helst virtuel maskine til en hvilken som helst virtuel maskine i begge VNets. AllowVirtualNetworkAccess er SAND som standard, og VNet peering vil klargøre stort ACLs for at tillade kommunikationen mellem VNets. Du kan stadig anvende netværk sikkerhed gruppe (NSG) regler for at blokere forbindelsen mellem bestemte undernet eller virtuelle maskiner få fin korn kontrol af adgang mellem to virtuelle netværk.  Se denne [artikel](virtual-networks-create-nsg-arm-ps.md)kan finde flere oplysninger i oprette NSG regler.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Hvis du vil oprette en VNet peering på tværs af abonnementer, skal du følge nedenstående trin:

1. Log på Azure med rettigheder bruger A er konto i abonnement-A og køre følgende cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Dette er ikke et krav peering kan oprettes, selvom brugere hæve individuelt peering anmodninger til deres respektive Vnets, som svarer til anmodningerne. Tilføje en bruger med rettigheder af de andre VNet som brugere i den lokale VNet gør det nemmere at udføre konfigurationen.

2. Log på Azure med privilegerede bruger-BS konto til abonnement-B og køre følgende cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Bruger A er i login session, kører denne cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Her er, hvordan JSON-fil er defineret.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. I bruger B login session, kan du køre følgende cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Her er, hvordan filen JSON defineres:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Når peering er oprettet i dette scenarie, skal du kunne starte forbindelser fra en hvilken som helst virtuel maskine til en hvilken som helst virtuelt af begge VNets på tværs af forskellige abonnementer.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. I dette scenarie skal installere du skabelonen eksempel nedenfor for at oprette VNet peering.  Du skal angive egenskaben AllowForwardedTraffic til sand, som gør det muligt for netværket virtuel maskinen i peered VNet til at sende og modtage trafik.

    Her er skabelonen til oprettelse af en VNet peering fra HubVNet VNet1. Bemærk, at AllowForwardedTraffic er indstillet til falsk.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Her er skabelonen til oprettelse af en VNet peering fra VNet1 HubVnet. Bemærk, at AllowForwardedTraffic er indstillet til sand.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Når peering er oprettet, kan du referere til denne [artikel](virtual-network-create-udr-arm-ps.md) for at definere brugerdefinerede routes(UDR) for at omdirigere VNet1 trafik via et virtuelt maskinen at bruge dens egenskaber. Når du angiver den næste hopadresse i omdirigere, kan du konfigurere den virtuelle maskinen i peer VNet HubVNet IP-adresse.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Hvis du vil oprette en peering mellem virtuelle netværk fra forskellige installation modeller, skal du følge nedenstående trin:
1. Teksten nedenfor viser definitionen af et VNet peering link til VNET1 til VNET2 i dette scenarie. Kun én link er påkrævet til peer et klassisk virtuelt netværk til et virtuelt netværk Azure ressource manager.

    Sørg for at anbringe det i dit abonnement-ID for hvor klassisk virtuelt netværk eller VNET2 er placeret, og Skift MyResouceGroup til relevante ressource gruppenavn.

    {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parametre": {}, "variabler": {}, "ressourcer": [{"apiVersion": "2016-06-01", "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings", "navn": "VNET1/LinkToVNET2", "placering": "[resourceGroup () .location]", "Egenskaber": {"allowVirtualNetworkAccess": SAND, "allowForwardedTraffic": falsk "allowGatewayTransit": falsk "useRemoteGateways": falsk "remoteVirtualNetwork": {"-id": "[resourceId (' Microsoft.ClassicNetwork/virtualNetworks',"VNET2")]"}}}]}

2. Hvis du vil installere skabelonfilen, skal du køre følgende cmdlet for at oprette eller opdatere installationen.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Når installationen er oprettet, kan du køre følgende cmdlet for at få vist tilstanden peering:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Når peering er oprettet mellem en klassisk VNet og en ressourceleder VNet, skal du kunne starte forbindelser fra en hvilken som helst virtuel maskine i VNET1 til en hvilken som helst virtuelt i VNET2 og omvendt.
