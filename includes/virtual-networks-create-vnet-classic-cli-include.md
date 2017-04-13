## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Sådan oprettes en klassisk VNet ved hjælp af Azure CLI

Du kan bruge Azure CLI til at administrere dine Azure ressourcer fra kommandoprompten fra en hvilken som helst computer, der kører Windows, Linux eller OSX. Følg nedenstående trin for at oprette en VNet ved hjælp af Azure CLI.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
2. Kør kommandoen **azure netværk vnet oprette** til at oprette en VNet og et undernet, som vist nedenfor. Listen vises, når output forklares de parametre, bruges.

            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    
    Forventet afgang:

            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    - **– vnet**. Navnet på VNet skal oprettes. I dette scenario *TestVNet*
    - **-e (eller -adresseområde)**. VNet-adresseområde. I dette scenario *192.168.0.0*
    - **-i (eller - cidr)**. Netværk inputmaske i CIDR format. I dette scenario *16*.
    - **- n (eller - undernet navn**). Navnet på det første undernet. I dette scenario *front end*.
    - **-p (eller -undernet-start-IP-)**. Første IP-adresse for undernet eller undernet-adresseområde. I dette scenario *192.168.1.0*.
    - **-r (eller -undernet cidr)**. Netværk inputmaske i CIDR format for undernet. I dette scenario *24*.
    - **-l (eller – placering)**. Azure område, hvor VNet skal oprettes. I dette scenario *Centrale USA*.

3. Kør kommandoen **azure netværks vnet undernet oprette** til at oprette et undernet, som vist nedenfor. Listen vises, når output forklares de parametre, bruges.

            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    
    Her er den forventede afgang for kommandoen ovenfor:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    - **-t (eller--vnet navn**. Navnet på den VNet, hvor undernettet skal oprettes. I vores scenario *TestVNet*.
    - **-n (eller -navn)**. Navnet på det nye undernet. I vores scenario *backend-version*.
    - **-a (eller -adresse-præfiks)**. Undernet CIDR blok. Fire vores scenario, *192.168.2.0/24*.

4. Kør kommandoen **azure netværk vnet Vis** til at få vist egenskaberne for den nye vnet, som vist nedenfor.

            azure network vnet show

    Her er den forventede afgang for kommandoen ovenfor:

            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK
