<properties
   pageTitle="Sådan oprettes NSGs i tilstanden Klassisk ved hjælp af Azure CLI | Microsoft Azure"
   description="Lær, hvordan du opretter og installerer NSGs i tilstanden Klassisk ved hjælp af Azure CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
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

# <a name="how-to-create-nsgs-classic-in-the-azure-cli"></a>Sådan oprettes NSGs (klassisk) i Azure CLI

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [oprette NSGs i implementeringsmodel ressourcestyring](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Nedenstående eksempel Azure CLI kommandoerne forventer et simpelt allerede har oprettet ud fra ovenstående scenarie-miljø. Hvis du vil køre kommandoer, som de vises i dette dokument, skal først oprette testmiljøet ved at [oprette en VNet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Sådan oprettes NSG for front-end-undernet
Følg nedenstående trin for at oprette en navngivet navngivet **NSG FrontEnd** baseret på ovenstående scenarie NSG.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Køre den **`azure config mode`** kommandoen til at skifte til tilstanden Klassisk, som vist nedenfor.

        azure config mode asm

    Forventet afgang:

        info:    New mode is asm

3. Køre den **`azure network nsg create`** kommandoen til at oprette en NSG.

        azure network nsg create -l uswest -n NSG-FrontEnd

    Forventet afgang:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-FrontEnd"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : NSG-FrontEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parametre:

    - **-l (eller – placering)**. Azure område, hvor den nye NSG skal oprettes. I vores scenario *westus*.
    - **-n (eller -navn)**. Navn til den nye NSG. I dette scenario *NSG FrontEnd*.

4. Køre den **`azure network nsg rule create`** kommandoen til at oprette en regel, der giver adgang til port 3389 (RDP) fra internettet.

        azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

    Forventet afgang:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "rdp-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : rdp-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 3389
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK

    Parametre:

    - **-a (eller--nsg-navn)**. Navnet på den NSG, hvor reglen oprettes. I vores scenario *NSG FrontEnd*.
    - **-n (eller -navn)**. Navn på ny regel. I dette scenario *rdp-regel*.
    - **-c (eller -handling)**. Adgangsniveauet for reglen (Afvis eller Tillad).
    - **-p (eller--protocol)**. Protocol (Tcp, Udp, eller *) for reglen.
    - **-r (eller -type)**. Retning af forbindelse (indgående eller udgående).
    - **-y (eller--prioritet)**. Prioritet for reglen.
    - **-f (eller -kilde-adresse-præfiks)**. Kilde adressepræfiks i CIDR eller ved hjælp af standardmærker.
    - **-o (eller – - port-kildeområde)**. Kildeport eller portområde.
    - **-e (eller -destination-adresse-præfiks)**. Destination adressepræfiks i CIDR eller ved hjælp af standardmærker.
    - **-u (eller -destination-port-område)**. Destinationsport eller portområde.

5. Køre den **`azure network nsg rule create`** kommandoen til at oprette en regel, der giver adgang til port 80 (HTTP) fra internettet.

        azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

    Forventede putput:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-FrontEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : INTERNET
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Køre den **`azure network nsg subnet add`** kommandoen til at oprette et link på NSG til front-end-undernet.

        azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

    Forventet afgang:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-FrontEnd"
        info:    network nsg subnet add command OK

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Sådan oprettes NSG for back-end-undernet
Følg nedenstående trin for at oprette en NSG kaldet navngivne *Back-end NSG* baseret på ovenstående scenarie.

3. Køre den **`azure network nsg create`** kommandoen til at oprette en NSG.

        azure network nsg create -l uswest -n NSG-BackEnd

    Forventet afgang:

        info:    Executing command network nsg create
        info:    Creating a network security group "NSG-BackEnd"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : NSG-BackEnd
        data:    Location                        : West US
        data:    Security group rules:
        data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
        ity  Default
        data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
        ---  -------
        data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
             true   
        data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
             true   
        data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
             true   
        data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
             true   
        data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
             true   
        data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
             true   
        info:    network nsg create command OK

    Parametre:

    - **-l (eller – placering)**. Azure område, hvor den nye NSG skal oprettes. I dette scenario *westus*.
    - **-n (eller -navn)**. Navn til den nye NSG. I dette scenario *NSG FrontEnd*.

4. Køre den **`azure network nsg rule create`** kommandoen til at oprette en regel, der giver adgang til port 1433 (SQL) fra front end-undernet.

        azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

    Forventet afgang:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "sql-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : sql-rule
        data:    Source address prefix           : 192.168.1.0/24
        data:    Source Port                     : *
        data:    Destination address prefix      : *
        data:    Destination Port                : 1433
        data:    Protocol                        : TCP
        data:    Type                            : Inbound
        data:    Action                          : Allow
        data:    Priority                        : 100
        info:    network nsg rule create command OK


5. Køre den **`azure network nsg rule create`** kommandoen til at oprette en regel, der afviser adgang til internettet.

        azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

    Forventede putput:

        info:    Executing command network nsg rule create
        info:    Looking up the network security group "NSG-BackEnd"
        info:    Creating a network security rule "web-rule"
        info:    Looking up the network security group "NSG-BackEnd"
        data:    Name                            : web-rule
        data:    Source address prefix           : *
        data:    Source Port                     : *
        data:    Destination address prefix      : INTERNET
        data:    Destination Port                : 80
        data:    Protocol                        : TCP
        data:    Type                            : Outbound
        data:    Action                          : Deny
        data:    Priority                        : 200
        info:    network nsg rule create command OK

6. Køre den **`azure network nsg subnet add`** kommandoen til at oprette et link på NSG til back-end-undernet.

        azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

    Forventet afgang:

        info:    Executing command network nsg subnet add
        info:    Looking up the network security group "NSG-BackEndX"
        info:    Looking up the subnet "BackEnd"
        info:    Looking up network configuration
        info:    Creating a network security group "NSG-BackEndX"
        info:    network nsg subnet add command OK
