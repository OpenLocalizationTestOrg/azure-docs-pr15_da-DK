<properties
   pageTitle="Oprette en intern justering af belastning ved hjælp af Azure CLI i den klassiske implementeringsmodel | Microsoft Azure"
   description="Lær at oprette en intern justering af belastning ved hjælp af Azure CLI i modellen Klassisk installation"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Få en introduktion til en intern belastningsjustering (klassisk) ved hjælp af Azure CLI

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Lær, hvordan du kan [udføre disse trin, ved hjælp af Ressourcestyring modellen](load-balancer-get-started-ilb-arm-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Hvis du vil oprette en intern justering af belastning, der er angivet for virtuelle maskiner

Hvis du vil oprette et internt Indlæs belastningsjusteringstjenesten sæt og servere, der sender deres trafik til den, skal du gøre følgende:

1. Oprette en forekomst af interne indlæse justering, der skal være slutpunkt for indgående trafik skal være på tværs af servere i et netværksbelastningen sæt.

1. Tilføj slutpunkter, der svarer til de virtuelle maskiner, modtager den indgående trafik.

1. Konfigurere servere, der sender trafik skal være Indlæs afstemmes for at sende deres trafik til den virtuelle IP-adresse (VIP) adresse på den interne indlæse justering forekomst.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Oprette en intern justering af belastning ved hjælp af CLI Step by step

Denne vejledning viser, hvordan du opretter en intern justering af belastning baseret på ovenstående scenarie.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Kør kommandoen **azure config tilstand** til at skifte til tilstanden Klassisk, som vist nedenfor.

        azure config mode asm

    Forventet afgang:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Oprette slutpunkt og indlæse belastningsjusteringstjenesten sæt

Dette scenario antages det virtuelle-maskiner "Saldo 1" og "DB2" i en skybaseret tjeneste, kaldet "mytestcloud". Begge virtuelle maskiner bruger et virtuelt netværk kaldet min "testvnet" med undernet "undernet-1".

Denne vejledning opretter en intern Indlæs belastningsjusteringstjenesten sæt, ved hjælp af port 1433 som privat port og 1433 som lokal port.

Dette er et almindelige scenarie, hvor du har SQL virtuelle maskiner på back-end ved hjælp af en intern justering af belastning til en databaseservere ikke vises direkte ved hjælp af en offentlig IP-adresse.


### <a name="step-1"></a>Trin 1

Oprette en intern justering af belastning sæt med `azure network service internal-load-balancer add`.

     azure service internal-load-balancer add -r mytestcloud -n ilbset -t subnet-1 -a 192.168.2.7

Parametre bruges:

**-r** - sky service navn<BR>
**-n** - interne Indlæs belastningsjusteringstjenesten navn<BR>
**-t** - undernet navn (samme undernet ved de virtuelle maskiner, du vil tilføje til den interne justering af belastning)<BR>
**-en** - (valgfrit) Tilføj en statisk privat IP-adresse<BR>

Se `azure service internal-load-balancer --help` kan finde flere oplysninger.

Du kan kontrollere interne Indlæs belastningsjusteringstjenesten egenskaber ved hjælp af kommandoen `azure service internal-load-balancer list` *skyen service navn*.

Her følger et eksempel på output:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


## <a name="step-2"></a>Trin 2

Du kan konfigurere interne Indlæs belastningsjusteringstjenesten sæt, når du føjer det første slutpunkt. Du vil knytte slutpunkt, virtuelt og efterprøvning af af port til de interne Indlæs belastningsjusteringstjenesten i dette trin.

    azure vm endpoint create db1 1433 -k 1433 tcp -t 1433 -r tcp -e 300 -f 600 -i ilbset

Parametre bruges:

**-k** - lokale virtuelt port<BR>
**-t** - efterprøvning af af port<BR>
**-r** - efterprøvning af af protokol<BR>
**-e** - efterprøvning af af intervallet i sekunder<BR>
**-f** - interval for timeout i sekunder <BR>
**-i** - interne Indlæs belastningsjusteringstjenesten navn <BR>


## <a name="step-3"></a>Trin 3

Bekræft, at indlæse belastningsjusteringstjenesten konfiguration bruger `azure vm show` *virtuelt navn*

    azure vm show DB1

Output bliver:

        azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK


## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Oprette et remote desktop slutpunkt for en virtuel maskine

Du kan oprette et remote desktop slutpunkt for at videresende netværkstrafik fra en offentlig port til en lokal port til en bestemt virtuelt ved hjælp af `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Fjerne virtual machine fra justering af belastning

Du kan fjerne en virtuel maskine fra en intern justering af belastning angive ved at slette det tilknyttede slutpunkt. Når slutpunktet er fjernet, tilhører ikke den virtuelle maskine den justering af belastning angive længere.

 I eksemplet ovenfor, kan du fjerne det slutpunkt, der er oprettet for virtuelt "saldo 1" fra interne justering af belastning "ilbset" ved hjælp af kommandoen `azure vm endpoint delete`.

    azure vm endpoint delete DB1 tcp-1433-1433


Se `azure vm endpoint --help` kan finde flere oplysninger.


## <a name="next-steps"></a>Næste trin

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstanden for ved hjælp af kilde IP-forbindelse](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)