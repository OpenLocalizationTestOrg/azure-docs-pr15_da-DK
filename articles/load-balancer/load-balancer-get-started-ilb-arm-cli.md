<properties
   pageTitle="Oprette en intern justering af belastning ved hjælp af Azure CLI i ressourcestyring | Microsoft Azure"
   description="Lær, hvordan du opretter en intern justering af belastning ved hjælp af Azure CLI i ressourcestyring."
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Oprette en intern justering af belastning ved hjælp af Azure CLI

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][Klassisk implementeringsmodel](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Installere løsningen ved hjælp af Azure CLI

Følgende trin viser, hvordan du opretter en forbindelse til internettet justering af belastning ved hjælp af Azure ressourcestyring med CLI. Med Azure Resource Manager hver ressource er oprettet og konfigureret enkeltvis, og angiv derefter sammen for at oprette en ressource.

Du har brug at oprette og konfigurere følgende objekter for at installere en belastningsjustering:

- **Front-end-IP-konfiguration**: indeholder offentlige IP-adresser for indgående netværkstrafik
- **Back end - adresse puljen**: indeholder netværksgrænseflader (NIC), der aktiverer de virtuelle maskiner til at modtage netværkstrafik fra justering af belastning
- **Justering af belastning regler**: indeholder regler, der er tilknyttet en offentlig port på justering af belastning port i adressegruppen back end-
- **Indgående NAT regler**: indeholder regler, der er tilknyttet en port til en bestemt virtuel maskine i back end-adresse puljen en offentlig port på justering af belastning
- **Sonder**: indeholder sundhed sonder, der bruges til at kontrollere tilgængeligheden af forekomster af virtuelle maskiner i puljen back end-adresse

Du kan finde yderligere oplysninger finder [Azure ressourcestyring support til justering af belastning](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Konfigurere CLI til at bruge ressourcestyring

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../../articles/xplat-cli-install.md). Følg vejledningen for op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Kør kommandoen **azure config tilstand** for at skifte til Ressourcestyring tilstand på følgende måde:

        azure config mode arm

    Forventet afgang:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Oprette en intern justering af belastning trin for trin

1. Log på Azure.

        azure login

    Når du bliver bedt om det, Angiv dine Azure legitimationsoplysninger.

2. Ændre kommandoen værktøjerne til Azure ressourcestyring tilstand.

        azure config mode arm

## <a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Alle de ressourcer i Azure ressourceleder, der er knyttet til en ressourcegruppe. Hvis du ikke har gjort det endnu, kan du oprette en ressourcegruppe.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Oprette en intern Indlæs belastningsjusteringstjenesten sæt

1. Oprette en intern justering af belastning

    I følgende eksempel oprettes en ressourcegruppe med navnet nrprg i af US område.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Alle ressourcerne for en intern Indlæs balancere som virtuelle netværk og virtuelt netværksundernet, skal være i samme ressourcegruppe og i det samme område.

2. Oprette en front-end-IP-adressen til den interne justering af belastning.

    IP-adressen, som du bruger skal være inden for området undernet til netværket virtuel.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Oprette adresse back end-puljen.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Når du definerer en front end IP-adresse og en adresse til back end-puljen, kan du oprette regler for indlæsning af belastningsjusteringstjenesten, regler for indgående NAT og tilpasset sundhed sonder.

4. Oprette en regel til Indlæs belastningsjusteringstjenesten for den interne justering af belastning.

    Når du har fulgt trinnene herover, opretter kommandoen en regel for justering af belastning for at lytte til port 1433 i front end-puljen og sender netværksbelastningen netværkstrafik til back end-adresse puljen skal også bruge port 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Oprette indgående NAT regler.

    Indgående NAT regler, der bruges til at oprette slutpunkter i belastningsjustering, gå til en bestemt virtuelt forekomst. De forrige trin, der er oprettet to NAT-regler for Fjernskrivebord.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Oprette sundhed sonder for justering af belastning.

    En tilstand efterprøvning af af kontrollerer alle forekomster af virtuelt at sikre, at de kan sende netværkstrafik. Virtuelt forekomst med mislykkedes efterprøvning af af Kontroller fjernes fra justering af belastning, indtil det går igen er online og efterprøvning af af Tjek bestemmer, at det er sund.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]Microsoft Azure-platform bruger en statisk, offentligt distribueret IPv4-adresse til en række forskellige administrative scenarier. IP-adressen er 168.63.129.16. Denne IP-adresse skal ikke blokeres af enhver firewalls, da det kan medføre uventede funktionsmåde.
    >Denne IP-adresse bruges i forhold til Azure interne justering af belastning, ved at overvåge sonder fra justering af belastning til at bestemme tilstanden for virtuelle maskiner i et netværksbelastningen sæt. Hvis en sikkerhedsgruppe netværk bruges til at begrænse trafik til Azure virtuelle maskiner i et internt netværksbelastningen sæt eller anvendes på et virtuelt netværksundernet, kan du sikre dig, at en netværk sikkerhedsregel føjes til tillade trafik fra 168.63.129.16.

## <a name="create-nics"></a>Oprette netværkskort

Du skal oprette netværkskort (eller redigere eksisterende) og knytte dem til NAT-regler, regler for indlæsning af belastningsjusteringstjenesten og sonder.

1. Oprette en navngivet NIC *kg nic1 være*, og knytte den til *rdp1* NAT reglen og *beilb* adresse back end-puljen.

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Oprette en navngivet NIC *kg nic2 være*, og knytte den til *rdp2* NAT reglen og *beilb* adresse back end-puljen.

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Oprette en virtuel maskine, der hedder *Saldo 1*og knytte den til NIC med navnet *kg nic1 være*. Lagerplads der kaldet *web1nrp* oprettes en før følgende kommando kører:

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] FOS i belastningsjustering skal være i det samme sæt tilgængelighed. Brug `azure availset create` til at oprette en tilgængelighed angive.

4. Oprette et virtuelt (VM) med navnet *DB2*og knytte den til NIC med navnet *kg nic2 være*. En lagerplads konto med navnet *web1nrp* blev oprettet før køre følgende kommando.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Slette en justering af belastning

Hvis du vil fjerne en belastningsjustering, skal du bruge følgende kommando:

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand ved hjælp af kilde IP-forbindelse](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)
