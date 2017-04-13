<properties
   pageTitle="Få en introduktion til en via justering af belastning i klassisk implementeringsmodel ved hjælp af Azure CLI internettet | Microsoft Azure"
   description="Lær, hvordan du opretter en via justering af belastning i klassisk implementeringsmodel ved hjælp af Azure CLI internettet"
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

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-cli"></a>Få en introduktion til en via justering af belastning (klassisk) i Azure CLI internettet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [se, hvordan du opretter en via justering af belastning ved hjælp af Azure ressourcestyring internettet](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## <a name="step-by-step-creating-an-internet-facing-load-balancer-using-cli"></a>Step by step oprettelse af en via justering af belastning ved hjælp af CLI internettet

Denne vejledning viser, hvordan du opretter en Internet justering af belastning baseret på ovenstående scenarie.

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../../articles/xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.

2. Kør kommandoen **azure config tilstand** til at skifte til tilstanden Klassisk, som vist nedenfor.

        azure config mode asm

    Forventet afgang:

        info:    New mode is asm


## <a name="create-endpoint-and-load-balancer-set"></a>Oprette slutpunkt og indlæse belastningsjusteringstjenesten sæt

Dette scenario antages det virtuelle maskiner "web1" og "web2" blev oprettet.
Denne vejledning opretter en Indlæs belastningsjusteringstjenesten sæt, ved hjælp af port 80 som offentlige port og port 80 som lokal port. En efterprøvning af af port er også konfigureret på port 80 og navngivne Indlæs belastningsjusteringstjenesten sæt "lbset".


### <a name="step-1"></a>Trin 1

Oprette det første slutpunkt og justering af belastning sæt med `azure network vm endpoint create` for virtuel maskine "web1".

    azure vm endpoint create web1 80 -k 80 -o tcp -t 80 -b lbset

Parametre bruges:

**-k** - lokale virtuelt port<br>
**-o** - protokol<BR>
**-t** - efterprøvning af af port<BR>
**-b** - indlæsning belastningsjusteringstjenesten navn<BR>

## <a name="step-2"></a>Trin 2

Føje en anden virtuel maskine "web2" til sættet Indlæs belastningsjusteringstjenesten.

    azure vm endpoint create web2 80 -k 80 -o tcp -t 80 -b lbset

## <a name="step-3"></a>Trin 3

Bekræft, at indlæse belastningsjusteringstjenesten konfiguration bruger `azure vm show` .

    azure vm show web1

Output bliver:

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Oprette et remote desktop slutpunkt for en virtuel maskine

Du kan oprette et remote desktop slutpunkt for at videresende netværkstrafik fra en offentlig port til en lokal port til en bestemt virtuelt ved hjælp af `azure vm endpoint create`.

    azure vm endpoint create web1 54580 -k 3389


## <a name="remove-virtual-machine-from-load-balancer"></a>Fjerne virtual machine fra justering af belastning

Du skal slette det slutpunkt, der er knyttet til den fra den virtuelle maskine justering af belastning. Når slutpunktet er fjernet, tilhører ikke den virtuelle maskine den justering af belastning angive længere.

 I eksemplet ovenfor, kan du fjerne det slutpunkt, der er oprettet for virtuel maskine "web1" fra justering af belastning "lbset" ved hjælp af kommandoen `azure vm endpoint delete`.

    azure vm endpoint delete web1 tcp-80-80


>[AZURE.NOTE] Du kan se flere muligheder til at administrere slutpunkter ved hjælp af kommandoen`azure vm endpoint --help`


## <a name="next-steps"></a>Næste trin

[Introduktion til konfiguration af en intern justering af belastning](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurere en Indlæs belastningsjusteringstjenesten fordeling tilstand](load-balancer-distribution-mode.md)

[Konfigurere inaktive TCP timeoutindstillinger for din justering af belastning](load-balancer-tcp-idle-timeout.md)

