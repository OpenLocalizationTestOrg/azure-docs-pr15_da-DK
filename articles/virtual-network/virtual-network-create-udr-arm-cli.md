<properties 
   pageTitle="Styre routing og bruge virtuelle anvendelser i ressourcestyring ved hjælp af Azure CLI | Microsoft Azure"
   description="Lær at styre routing og bruge virtuelle anvendelser ved hjælp af Azure CLI"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>Oprette brugerdefinerede omdirigerer (UDR) i Azure CLI

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [oprette UDRs i modellen Klassisk installation](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Nedenstående eksempel Azure CLI kommandoerne forventer et simpelt allerede har oprettet ud fra ovenstående scenarie-miljø. Hvis du vil køre kommandoer, som de vises i dette dokument, skal først oprette testmiljøet ved at installere [denne skabelon](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), klik på **Implementer til Azure**, erstatte Standardparameterværdier, hvis det er nødvendigt og følge instruktionerne i portalen.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Oprette UDR for front-end-undernettet
Følg nedenstående trin for at oprette ruten tabel og distribuere behov for det front-end-undernet, der er baseret på ovenstående scenarie.

3. Køre den **`azure network route-table create`** kommandoen til at oprette en distribution tabel til front-end-undernettet.

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    Output:

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    Parametre:
    - **-g (eller--ressourcegruppe)**. Navnet på den ressourcegruppe, hvor UDR skal oprettes. I dette scenario *TestRG*.
    - **-l (eller – placering)**. Azure område, hvor den nye UDR skal oprettes. I dette scenario *westus*.
    - **-n (eller -navn)**. Navn til den nye UDR. I dette scenario *UDR FrontEnd*.

4. Køre den **`azure network route-table route create`** kommandoen til at oprette en rute i rutetabellen oprettet ovenfor til at sende al trafik bestemt til back-end-undernet (192.168.2.0/24) til **FW1** VM (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    Output:

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    Parametre:
    - **-r (eller--distribuere tabelnavn)**. Navnet på rutetabellen, hvor ruten skal tilføjes. I dette scenario *UDR FrontEnd*.
    - **-a (eller -adresse-præfiks)**. Adressepræfiks for det undernet, hvor-pakker, der er stilet til. I dette scenario *192.168.2.0/24*.
    - **-y (eller – næste-hop-type)**. Type objekt trafik sendes til. Mulige værdier er *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*eller *ingen*.
    - **-p (eller – næste-hop-ip-adresse**). IP-adressen til næste trin. I dette scenario *192.168.0.4*.

5. Køre den **`azure network vnet subnet set`** kommando for at knytte rutetabellen oprettet ovenfor med **front end** -undernet.

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    Output:

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    Parametre:
    - **-e (eller--vnet navn)**. Navnet på den VNet, hvor undernettet er placeret. I dette scenario *TestVNet*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Oprette UDR for back-end-undernettet
Følg nedenstående trin for at oprette ruten tabel og distribuere behov for det back-end-undernet, der er baseret på ovenstående scenarie.

1. Køre den **`azure network route-table create`** kommandoen til at oprette en distribution tabel til back-end-undernettet.

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Køre den **`azure network route-table route create`** kommandoen til at oprette en rute i rutetabellen oprettet ovenfor til at sende alle trafik bestemt til front end-undernet (192.168.1.0/24) til **FW1** VM (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Køre den **`azure network vnet subnet set`** kommando for at knytte rutetabellen oprettet ovenfor med **back-end** -undernet.

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>Aktivere IP forwarding på FW1
Følg nedenstående trin for at aktivere IP videresendelse i NIC bruges af **FW1**skal.

1. Køre den **`azure network nic show`** kommando, og Bemærk værdien for at **aktivere IP-videresendelse**. Den skal være indstillet til *Falsk*.

        azure network nic show -g TestRG -n NICFW1

    Output:
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. Køre den **`azure network nic set`** kommando for at aktivere IP forwarding.

        azure network nic set -g TestRG -n NICFW1 -f true

    Output:

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    Parametre:

    - **-f (eller – Aktivér-ip-forwarding)**. *Sand* eller *Falsk*.
