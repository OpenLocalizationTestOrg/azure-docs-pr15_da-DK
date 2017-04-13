<properties
   pageTitle="Oprette en via justering af belastning i ressourcestyring ved hjælp af Azure CLI internettet | Microsoft Azure"
   description="Lær, hvordan du opretter en via justering af belastning i ressourcestyring ved hjælp af Azure CLI internettet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Oprette en intern justering af belastning ved hjælp af Azure CLI

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler implementeringsmodel ressourcestyring. Du kan også [se, hvordan du opretter en via justering af belastning ved hjælp af klassisk installation internettet](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementering af løsningen ved hjælp af Azure CLI

Følgende trin viser, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring med CLI internettet. Med Azure ressourcestyring hver ressource er blevet oprettet og konfigureret enkeltvis, derefter læg sammen for at oprette en ressource.

Du skal oprette og konfigurere følgende objekter for at installere en belastningsjustering:

- IP-konfiguration af front end - indeholder offentlige IP-adresser for indgående netværkstrafik.
- Adresse back end-puljen - indeholder netværksgrænseflader (NIC) for de virtuelle maskiner til at modtage netværkstrafik fra justering af belastning.
- Justering af belastning regler - indeholder regler tilknytte en offentlig port på justering af belastning til port i adressegruppen back end.
- Indgående regler for NAT - indeholder regler tilknytte en offentlig port på justering af belastning til en port til en bestemt virtuel maskine i puljen back end-adresse.
- Sonder - indeholder sundhed sonder bruges til at kontrollere tilgængeligheden af forekomster af virtuelle maskiner i puljen back end-adresse.

Du kan finde flere oplysninger [Azure ressourcestyring support til justering af belastning](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Konfigurere CLI til at bruge ressourcestyring

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand, som vist nedenfor.

        azure config mode arm

    Forventet afgang:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Oprette et virtuelt netværk og en offentlig IP-adresse til IP-front end-puljen

1. Oprette et virtuelt netværk (VNet) med navnet *NRPVnet* af US placering ved hjælp af en ressourcegruppe med navnet *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Oprette et undernet med navnet *NRPVnetSubnet* med en CIDR blok med 10.0.0.0/24 i *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Oprette en offentlig IP-adresse med navnet *NRPPublicIP* der skal bruges i en front-end-IP-puljen med DNS-navn *loadbalancernrp.eastus.cloudapp.azure.com*. Kommandoen nedenfor bruges statisk tildeling af typen og inaktiv timeout på 4 minutter.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]Belastning bruge domæne navnet på den offentlige IP-som det fulde Domænenavn. Dette en ændring fra klassisk installation, som bruger skyen-tjeneste som justering af belastning fuldt kvalificeret domæne navn (fulde Domænenavn).
    >I dette eksempel er fulde *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Oprette en justering af belastning

Følgende kommando opretter belastningsjustering med navnet *NRPlb* i gruppen *NRPRG* ressource i *Af US* Azure placering.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Oprette en front end-puljen IP- og en back end-adresse samling

I dette eksempel vises, hvordan du opretter front end-puljen, der modtager indgående netværkstrafikken på justering af belastning IP- og back end-IP-puljen hvor front end-puljen sender netværkstrafik Indlæs afstemmes.

1. Oprette en front-end-IP-gruppe at tilknytte den offentlige IP-adresse, der er oprettet i ovenstående trin og belastning.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Konfigurere en back end-adressegruppe, der bruges til at modtage indgående trafik fra front end IP-puljen.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Oprette regler for kg, NAT-regler og efterprøvning af af

I dette eksempel opretter følgende elementer.

- en NAT regel til at oversætte al indgående trafik på port 21 til port 22<sup>1</sup>
- en NAT regel til at oversætte al indgående trafik på port 23 til port 22
- en Indlæs belastningsjusteringstjenesten regel saldo al indgående trafik på port 80 til port 80 på adresserne i back end-puljen.
- en efterprøvning af af regel til at kontrollere sundhedsstatus på en side med navnet *HealthProbe.aspx*.

<sup>1</sup> NAT-regler er knyttet til en bestemt virtuelt forekomst bag justering af belastning. Netværkstrafikken ankommer på port 21 sendes til en bestemt virtuel maskine på port 22, der er knyttet til denne NAT-regel. Du skal angive et protocol (UDP eller TCP) for en NAT-regel. Begge protokoller kan ikke tildeles til den samme port.

1. Opret regler, NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Oprette en regel for indlæsning af belastning.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Oprette en tilstand efterprøvning af af.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Se dine indstillinger.

        azure network lb show nrprg nrplb

    Forventet afgang:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Oprette netværkskort

Du skal oprette netværkskort (eller redigere eksisterende) og knytte dem til NAT-regler, regler for indlæsning af belastningsjusteringstjenesten og sonder.

1. Oprette en navngivet NIC *kg nic1 være*, og knytte den til *rdp1* NAT reglen og *NRPbackendpool* adresse back end-puljen.

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Forventet afgang:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Oprette en navngivet NIC *kg nic2 være*, og knytte den til *rdp2* NAT reglen og *NRPbackendpool* adresse back end-puljen.

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Oprette et virtuelt (VM) med navnet *web1*og knytte den til NIC med navnet *kg nic1 være*. En lagerplads konto med navnet *web1nrp* blev oprettet før du kører kommandoen nedenfor.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] FOS i belastningsjustering skal være i det samme sæt tilgængelighed. Brug `azure availset create` til at oprette en tilgængelighed angive.

    Output skal ligne følgende:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] Oplysningsmeddelelse **dette er et NIC uden publicIP konfigureret** forventes siden NIC oprettet for den justering af belastning oprette forbindelse til internettet ved hjælp af belastning belastningsjusteringstjenesten offentlige IP-adresse.

    Da den *kg nic1 være* NIC er knyttet til *rdp1* NAT-reglen, du kan oprette forbindelse til *web1* ved hjælp af RDP gennem port 3441 på justering af belastning.

4. Oprette et virtuelt (VM) med navnet *web2*og knytte den til NIC med navnet *kg nic2 være*. En lagerplads konto med navnet *web1nrp* blev oprettet før du kører kommandoen nedenfor.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Opdatere en eksisterende justering af belastning

Du kan tilføje regler, der refererer til en eksisterende justering af belastning. Det næste eksempel er en ny Indlæs belastningsjusteringstjenesten regel føjet til en eksisterende justering af belastning **NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Slette en justering af belastning

Brug følgende kommando til at fjerne en belastningsjustering:

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
