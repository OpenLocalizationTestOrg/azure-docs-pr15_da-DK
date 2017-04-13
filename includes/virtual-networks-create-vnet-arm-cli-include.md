## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Sådan oprettes en VNet ved hjælp af Azure CLI

Du kan bruge Azure CLI til at administrere dine Azure ressourcer fra kommandoprompten fra en hvilken som helst computer, der kører Windows, Linux eller OSX. Følg nedenstående trin for at oprette en VNet ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Her er den forventede afgang for kommandoen ovenfor:

        info:    New mode is arm

3. Hvis det er nødvendigt, køre **oprettelse af azure gruppe** for at oprette en ny ressourcegruppe, som vist nedenfor. Bemærk output fra kommandoen. Listen vises, når output forklares de parametre, bruges. Du kan finde flere oplysninger om ressourcegrupper ved at besøge [Azure ressourcestyring oversigt](../articles/virtual-network/resource-group-overview.md#resource-groups).

        azure group create -n TestRG -l centralus

    Her er den forventede afgang for kommandoen ovenfor:

        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK

    - **-n (eller -navn)**. Navn til den nye ressourcegruppe. I dette scenario *TestRG*.
    - **-l (eller – placering)**. Azure område, hvor den nye ressourcegruppe skal oprettes. I dette scenario *centralus*.

4. Kør kommandoen **azure netværk vnet oprette** til at oprette en VNet og et undernet, som vist nedenfor. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    Her er den forventede afgang for kommandoen ovenfor:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (eller--ressourcegruppe)**. Navnet på den ressourcegruppe, hvor VNet skal oprettes. I dette scenario *TestRG*.
    - **-n (eller -navn)**. Navnet på VNet skal oprettes. I dette scenario *TestVNet*
    - **-a (eller -adresse-præfikser)**. Liste over CIDR blokke, der bruges til VNet-adresseområde. I vores scenario *192.168.0.0/16*
    - **-l (eller – placering)**. Azure område, hvor VNet skal oprettes. I dette scenario *centralus*.

5. Kør kommandoen **azure netværks vnet undernet oprette** til at oprette et undernet, som vist nedenfor. Bemærk output fra kommandoen. Listen vises, når output forklares de parametre, bruges.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    Her er den forventede afgang for kommandoen ovenfor:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (eller--vnet navn**. Navnet på den VNet, hvor undernettet skal oprettes. I vores scenario *TestVNet*.
    - **-n (eller -navn)**. Navnet på det nye undernet. I dette scenario *front end*.
    - **-a (eller -adresse-præfiks)**. Undernet CIDR blok. Fire vores scenario, *192.168.1.0/24*.

6. Gentag trin 5 ovenfor for at oprette andre undernet, hvis det er nødvendigt. Kør kommandoen nedenfor til at oprette *back-end* -undernettet i vores scenario.

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Kør kommandoen **azure netværk vnet Vis** til at få vist egenskaberne for den nye vnet, som vist nedenfor.

        azure network vnet show -g TestRG -n TestVNet

    Her er den forventede afgang for kommandoen ovenfor:

        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK
