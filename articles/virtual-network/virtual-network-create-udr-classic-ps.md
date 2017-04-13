<properties 
   pageTitle="Styre routing og bruge virtuelle anvendelser ved hjælp af PowerShell i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær at styre routing i VNets ved hjælp af PowerShell i modellen Klassisk installation"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Styre routing og bruge virtuelle anvendelser (klassisk) ved hjælp af PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Eksemplet Azure PowerShell kommandoer nedenfor forventer et enkelt miljø, som allerede har oprettet på basis af ovenstående scenarie. Hvis du vil køre kommandoer, som de vises i dette dokument, skal du oprette vises i [oprette en VNet (klassisk) ved hjælp af PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md)-miljø.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Oprette UDR for front-end-undernettet
Følg nedenstående trin for at oprette ruten tabel og distribuere behov for det front-end-undernet, der er baseret på ovenstående scenarie.

3. Køre den **`New-AzureRouteTable`** til at oprette en distribution tabel til front-end-undernettet.

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    Output:

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. Køre den **`Set-AzureRoute`** til at oprette en rute i rutetabellen oprettet ovenfor til at sende al trafik bestemt til back-end-undernet (192.168.2.0/24) til **FW1** VM (192.168.0.4).
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    Output:

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Køre den **`Set-AzureSubnetRouteTable`** til at knytte rutetabellen oprettet ovenfor med **front end** -undernet.

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Oprette UDR for back-end-undernettet
Følg nedenstående trin for at oprette ruten tabel og distribuere behov for det back-end-undernet, der er baseret på ovenstående scenarie.

3. Køre den **`New-AzureRouteTable`** til at oprette en distribution tabel til back-end-undernettet.

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. Køre den **`Set-AzureRoute`** til at oprette en rute i rutetabellen oprettet ovenfor til at sende alle trafik bestemt til front end-undernet (192.168.1.0/24) til **FW1** VM (192.168.0.4).

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. Køre den **`Set-AzureSubnetRouteTable`** til at knytte rutetabellen oprettet ovenfor med **back-end** -undernet.

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Aktivere IP forwarding på FW1 VM
Følg nedenstående trin for at aktivere IP videresendelse i FW1 VM skal.

1. Køre den **`Get-AzureIPForwarding`** -cmdlet til k status for IP-videresendelse

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Output:

        Disabled

2. Køre den **`Set-AzureIPForwarding`** kommando for at aktivere IP forwarding for *FW1* VM.

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
