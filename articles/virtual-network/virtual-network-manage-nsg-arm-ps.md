<properties 
   pageTitle="Administrere NSGs ved hjælp af PowerShell i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du administrerer exising NSGs ved hjælp af PowerShell i ressourcestyring."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Administrere NSGs ved hjælp af PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisk implementeringsmodel.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Hente oplysninger

Du kan se din eksisterende NSGs, hente regler til en eksisterende NSG og finde ud af, hvilke ressourcer en NSG er knyttet til den.

### <a name="view-existing-nsgs"></a>Få vist eksisterende NSGs
Hvis du vil vise alle eksisterende NSGs i et abonnement, skal du køre den `Get-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor.

    Get-AzureRmNetworkSecurityGroup

Forventet resultat:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


For at se listen over NSGs i en bestemt ressourcegruppe skal køre den `Get-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Forventet afgang:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Liste over alle regler for en NSG

For at få vist regler for en NSG med navnet **NSG FrontEnd**køre den `Get-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Forventet afgang:
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] Du kan også bruge `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` til en liste over standardreglerne fra **NSG FrontEnd** NSG.

### <a name="view-nsgs-associations"></a>Få vist NSGs tilknytninger

Hvis du vil se, hvad ressourcer **NSG FrontEnd** NSG er knyttes til, skal du køre den `Get-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Se efter egenskaberne **NetworkInterfaces** og **undernet** som vist nedenfor:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

NSG er ikke knyttet til en hvilken som helst netværksgrænseflader (NIC) i det foregående eksempel, og den er knyttet til et navngivet **front end**-undernet.

## <a name="manage-rules"></a>Administrere regler

Du kan føje regler til en eksisterende NSG, redigere eksisterende regler og fjerne regler.

### <a name="add-a-rule"></a>Tilføje en regel

Følg nedenstående trin for at tilføje en regel for at tillade **indgående** trafik til port **443** fra en hvilken som helst computer til **NSG FrontEnd** NSG skal.

1. Køre den `Get-AzureRmNetworkSecurityGroup` til at hente den eksisterende NSG og gemme dem i en variabel, som vist nedenfor.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Køre den `Add-AzureRmNetworkSecurityRuleConfig` cmdlet, som vist nedenfor.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. For at gemme ændringer i NSG skal køre den `Set-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Forventet afgang, der viser kun sikkerhed reglerne:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Ændre en regel

Følg nedenstående trin for at ændre den regel, der er oprettet ovenfor til at tillade indgående trafik fra **internettet** kun skal.

1. Køre den `Get-AzureRmNetworkSecurityGroup` til at hente den eksisterende NSG og gemme dem i en variabel, som vist nedenfor.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Køre den `Set-AzureRmNetworkSecurityRuleConfig` cmdlet, som vist nedenfor.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. For at gemme ændringer i NSG skal køre den `Set-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Forventet afgang, der viser kun sikkerhed reglerne:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Slette en regel

1. Køre den `Get-AzureRmNetworkSecurityGroup` til at hente den eksisterende NSG og gemme dem i en variabel, som vist nedenfor.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Køre den `Remove-AzureRmNetworkSecurityRuleConfig` cmdlet, som vist nedenfor.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. For at gemme ændringer i NSG skal køre den `Set-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Forventet output, der viser kun sikkerhed reglerne, meddelelse om **https-regel** ikke længere vises:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Administrere tilknytninger

Du kan knytte en NSG til undernet og netværkskort. Du kan også fjerne forbindelsen mellem en NSG fra alle de ressourcer, der er knyttet til.

### <a name="associate-an-nsg-to-a-nic"></a>Knytte en NSG til et NIC

Følg nedenstående trin for at knytte **NSG FrontEnd** NSG til **TestNICWeb1** NIC.

1. Køre den `Get-AzureRmNetworkSecurityGroup` til at hente den eksisterende NSG og gemme dem i en variabel, som vist nedenfor.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Køre den `Get-AzureRmNetworkInterface` til at hente den eksisterende NIC og gemme dem i en variabel, som vist nedenfor.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Angive egenskaben **NetworkSecurityGroup** af variablen **NIC** til værdien af variablen **NSG** , som vist nedenfor.

        $nic.NetworkSecurityGroup = $nsg

4. For at gemme ændringer i NIC skal køre den `Set-AzureRmNetworkInterface` cmdlet som vist nedenfor.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Forventet afgang, hvor egenskaben **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>Fjerne forbindelsen mellem en NSG fra et NIC

Følg nedenstående trin for at fjerne tilknytningen **NSG FrontEnd** NSG fra **TestNICWeb1** NIC.

1. Køre den `Get-AzureRmNetworkSecurityGroup` til at hente den eksisterende NSG og gemme dem i en variabel, som vist nedenfor.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Køre den `Get-AzureRmNetworkInterface` til at hente den eksisterende NIC og gemme dem i en variabel, som vist nedenfor.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Angive egenskaben **NetworkSecurityGroup** af variablen **NIC** til **$null**, som vist nedenfor.

        $nic.NetworkSecurityGroup = $null

4. For at gemme ændringer i NIC skal køre den `Set-AzureRmNetworkInterface` cmdlet som vist nedenfor.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Forventet afgang, hvor egenskaben **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>Fjerne forbindelsen mellem en NSG fra et undernet

Følg nedenstående trin for at fjerne tilknytningen **NSG FrontEnd** NSG fra **front end** -undernet.

1. Køre den `Get-AzureRmVirtualNetwork` til at hente den eksisterende VNet og gemme dem i en variabel, som vist nedenfor.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Køre den `Get-AzureRmVirtualNetworkSubnetConfig` til at hente **front end** -undernettet og gemme dem i en variabel, som vist nedenfor.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. Angive egenskaben **NetworkSecurityGroup** af variablen **undernet** til **$null**, som vist nedenfor.

        $subnet.NetworkSecurityGroup = $null

4. For at gemme ændringer i undernettet skal køre den `Set-AzureRmVirtualNetwork` cmdlet som vist nedenfor.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Forventet afgang, der viser kun egenskaberne for **front end** -undernettet. Bemærk, at der ikke er en egenskab for **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Knytte en NSG til et undernet

Hvis du vil knytte **NSG FrontEnd** NSG til **FronEnd** undernet igen, skal du følge nedenstående trin.

1. Køre den `Get-AzureRmVirtualNetwork` til at hente den eksisterende VNet og gemme dem i en variabel, som vist nedenfor.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Køre den `Get-AzureRmVirtualNetworkSubnetConfig` til at hente **front end** -undernettet og gemme dem i en variabel, som vist nedenfor.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Køre den `Get-AzureRmNetworkSecurityGroup` til at hente den eksisterende NSG og gemme dem i en variabel, som vist nedenfor.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. Angive egenskaben **NetworkSecurityGroup** af variablen **undernet** til **$null**, som vist nedenfor.

        $subnet.NetworkSecurityGroup = $nsg

4. For at gemme ændringer i undernettet skal køre den `Set-AzureRmVirtualNetwork` cmdlet som vist nedenfor.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Forventet output, der viser kun egenskaben **NetworkSecurityGroup** for **front end** -undernet:

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Slette en NSG

Du kan kun slette en NSG, hvis det ikke er knyttet til en ressource. Følg nedenstående trin for at slette en NSG.

1. Hvis du vil se de ressourcer, der er knyttet til en NSG skal køre den `azure network nsg show` som vist i [visningen NSGs tilknytninger](#View-NSGs-associations).
2. Hvis NSG er knyttet til en hvilken som helst netværkskort, skal du køre den `azure network nic set` som vist i [Dissociate en NSG fra et NIC](#Dissociate-an-NSG-from-a-NIC) for hver NIC. 
3. Hvis NSG er knyttet til en hvilken som helst undernet, skal du køre den `azure network vnet subnet set` som vist i [Dissociate en NSG fra et undernet](#Dissociate-an-NSG-from-a-subnet) for hvert undernet.
4. For at slette NSG skal køre den `Remove-AzureRmNetworkSecurityGroup` cmdlet som vist nedenfor.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] Den **-kraft** parameter sikrer, at du ikke behøver at bekræfte sletningen.

## <a name="next-steps"></a>Næste trin

- [Aktivere logføring af](virtual-network-nsg-manage-log.md) NSGs.