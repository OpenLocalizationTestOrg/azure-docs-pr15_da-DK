<properties 
   pageTitle="Sådan angives en statiske privat IP-Adresser i ARM tilstand ved hjælp af CLI | Microsoft Azure"
   description="Forstå statisk IP'er (fald), og hvordan du administrerer dem i ARM tilstand ved hjælp af CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-set-a-static-private-ip-address-in-azure-cli"></a>Sådan angives en statisk privat IP-adresse i Azure CLI

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [administrere statisk privat IP-adresse i modellen, klassisk installation](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Nedenstående eksempel Azure CLI kommandoerne forventer et enkelt miljø, som allerede har oprettet. Hvis du vil køre kommandoer, som de vises i dette dokument, skal først opbygge testmiljø, der er beskrevet i [oprette en vnet](virtual-networks-create-vnet-arm-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Sådan angives en statisk privat IP-adresse, når du opretter en VM
Følg nedenstående trin for at oprette en navngivet *DNS01* i *front end* -undernet af en VNet med navnet *TestVNet* med en statisk privat IP af *192.168.1.101*VM skal:

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Forventet afgang:

        info:    New mode is arm

3. Køre **azure netværk offentlige ip - Opret** for at oprette en offentlige IP-adresse til VM. Listen vises, når output forklares de parametre, bruges.

        azure network public-ip create -g TestRG -n TestPIP -l centralus
    
    Forventet afgang:

        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK

    - **-g (eller--ressourcegruppe)**. Navnet på ressourcegruppen den offentlige IP-oprettes i.
    - **-n (eller -navn)**. Navnet på den offentlige IP-adresse.
    - **-l (eller – placering)**. Azure område, hvor den offentlige IP-adresse skal oprettes. I dette scenario *centralus*.

3. Kør kommandoen **nic azure network oprette** til at oprette en NIC med en statisk privat IP-adresse. Listen vises, når output forklares de parametre, bruges.

        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

    Forventet afgang:

        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

    - **-a (eller – privat-ip-adresse)**. Statisk privat IP-adressen til NIC.
    - **-m (eller -undernet-vnet-navn)**. Navnet på den VNet, hvor NIC skal oprettes.
    - **-k (eller -undernet navn)**. Navnet på det undernet, hvor NIC skal oprettes.

4. Kør kommandoen **azure vm oprette** til at oprette VM ved hjælp af den offentlige IP- og NIC oprettet ovenfor. Listen vises, når output forklares de parametre, bruges.

        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

    Forventet afgang:

        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK

    - **-y (eller--os-type)**. Typen af operativsystem til VM, *Windows* eller *Linux*.
    - **-f (eller--nic-navn)**. Navnet på NIC VM anvender.
    - **-i (eller--offentlige IP-navn)**. Navnet på den offentlige IP-adresse, hvor VM anvender.
    - **-F (eller--vnet navn)**. Navnet på den VNet, hvor VM skal oprettes.
    - **-j (eller--vnet-undernet-navn)**. Navnet på sted, hvor VM oprettes undernet.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hvordan du kan hente statiske privat IP-adresseoplysninger for en VM

For at få vist statisk privat IP-adresseoplysningerne for det VM, der er oprettet med scriptet ovenfor, du køre følgende kommando for Azure CLI og overholde værdierne for *privat IP-alloc-metoden* og *privat IP-adresse*:

    azure vm show -g TestRG -n DNS01

Forventet afgang:

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Sådan fjernes en statisk privat IP-adresse fra en VM
Du kan ikke fjerne en statisk privat IP-adresse fra en NIC i Azure CLI for ressourcestyring. Du skal oprette en ny NIC, der bruger en dynamisk IP-adresse, fjerne den forrige NIC fra VM og derefter tilføje den nye NIC til VM. Følg nedenstående trin for at ændre NIC for VM bruges int kommandoer ovenfor.
    
1. Kør kommandoen **nic azure network oprette** til at oprette en ny NIC ved hjælp af dynamisk IP-fordeling. Bemærk, hvordan du behøver ikke angive IP-adressen på nuværende tidspunkt.

        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

    Forventet afgang:

        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

2. Kør kommandoen **azure vm set** ændre NIC bruges af VM.

        azure vm set -g TestRG -n DNS01 -N TestNIC2

    Forventet afgang:

        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK

3. Hvis ville, køre **nic azure network slette** kommando for at slette det gamle NIC.

        azure network nic delete -g TestRG -n TestNIC --quiet

    Forventet afgang:

        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Sådan føjes en statisk privat IP-adresse til en eksisterende VM
For at føje en statisk privat IP-adresse til NIC bruges af den VM, der er oprettet ved hjælp af scriptet ovenfor, skal du køre følgende kommando:

    azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Forventet afgang:

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [reserveret offentlige IP-](virtual-networks-reserved-public-ip.md) adresser.
- Få mere at vide om [forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md) adresser.
- Se [reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx).
