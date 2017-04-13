<properties 
   pageTitle="Styre routing og bruge virtuelle anvendelser ved hjælp af Azure CLI i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær at styre routing i VNets ved hjælp af Azure CLI i modellen Klassisk installation"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Styre routing og bruge virtuelle anvendelser (klassisk) ved hjælp af Azure CLI

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [kontrolelement routing og bruge virtuelle anvendelser i implementeringsmodel ressourcestyring](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Nedenstående eksempel Azure CLI kommandoerne forventer et simpelt allerede har oprettet ud fra ovenstående scenarie-miljø. Hvis du vil køre kommandoer, som de vises i dette dokument, skal du oprette vises i [oprette en VNet (klassisk) ved hjælp af Azure CLI](virtual-networks-create-vnet-classic-cli.md)-miljø.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Oprette UDR for front-end-undernettet
Følg nedenstående trin for at oprette ruten tabel og distribuere behov for det front-end-undernet, der er baseret på ovenstående scenarie.

1. Køre den **`azure config mode`** at skifte til tilstanden Klassisk.

        azure config mode asm

    Output:

        info:    New mode is asm

3. Køre den **`azure network route-table create`** kommandoen til at oprette en distribution tabel til front-end-undernettet.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Output:

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Parametre:
    - **-l (eller – placering)**. Azure område, hvor den nye NSG skal oprettes. I dette scenario *westus*.
    - **-n (eller -navn)**. Navn til den nye NSG. I dette scenario *NSG FrontEnd*.

4. Køre den **`azure network route-table route set`** kommandoen til at oprette en rute i rutetabellen oprettet ovenfor til at sende al trafik bestemt til back-end-undernet (192.168.2.0/24) til **FW1** VM (192.168.0.4).

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Output:

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Parametre:
    - **-r (eller--distribuere tabelnavn)**. Navnet på rutetabellen, hvor ruten skal tilføjes. I dette scenario *UDR FrontEnd*.
    - **-a (eller -adresse-præfiks)**. Adressepræfiks for det undernet, hvor-pakker, der er stilet til. I dette scenario *192.168.2.0/24*.
    - **-t (eller – næste-hop-type)**. Type objekt trafik sendes til. Mulige værdier er *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*eller *ingen*.
    - **-p (eller – næste-hop-ip-adresse**). IP-adressen til næste trin. I dette scenario *192.168.0.4*.

5. Køre den **`azure network vnet subnet route-table add`** kommando for at knytte rutetabellen oprettet ovenfor med **front end** -undernet.

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Output:

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Parametre:
    - **-t (eller--vnet navn)**. Navnet på den VNet, hvor undernettet er placeret. I dette scenario *TestVNet*.
    - **- n (eller - undernet navn**. Navnet på undernettet rutetabellen, føjes til. I dette scenario *front end*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Oprette UDR for back-end-undernettet
Følg nedenstående trin for at oprette ruten tabel og distribuere behov for det back-end-undernet, der er baseret på ovenstående scenarie.

3. Køre den **`azure network route-table create`** kommandoen til at oprette en distribution tabel til back-end-undernettet.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Køre den **`azure network route-table route set`** kommandoen til at oprette en rute i rutetabellen oprettet ovenfor til at sende alle trafik bestemt til front end-undernet (192.168.1.0/24) til **FW1** VM (192.168.0.4).

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Køre den **`azure network vnet subnet route-table add`** kommando for at knytte rutetabellen oprettet ovenfor med **back-end** -undernet.

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

