## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Installere skabelonen ARM ved hjælp af Azure CLI

Følg nedenstående trin for at installere den ARM skabelon, du har hentet ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
2. Køre den **`azure config mode`** kommandoen til at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Her er den forventede afgang for kommandoen ovenfor:

        info:    New mode is arm

3. Hvis det er nødvendigt, kan du køre den **`azure group create`** til at oprette en ny ressourcegruppe, som vist nedenfor. Bemærk output fra kommandoen. Listen vises, når output forklares de parametre, bruges. Du kan finde flere oplysninger om ressourcegrupper ved at besøge [Azure ressourcestyring oversigt](../articles/resource-group-overview.md).

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

4. Køre den **`azure group deployment create`** til at installere den nye VNet ved hjælp af skabelonen og parameter-filer, du har downloadet og ændret ovenfor. Listen vises, når output forklares de parametre, bruges.

        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

    Her er den forventede afgang for kommandoen ovenfor:

        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK

    - **-g (eller--ressourcegruppe)**. Navnet på ressourcegruppen nye VNet oprettes i.
    - **f-(eller -skabelonfil)**. Stien til filen ARM skabelon.
    - **-e (eller -parametre-fil)**. Stien til filen ARM parametre.

5. Køre den **`azure network vnet show`** kommandoen til at få vist egenskaberne for den nye vnet, som vist nedenfor.

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
