<properties 
   pageTitle="Sådan angives en statiske privat IP-Adresser i tilstanden Klassisk ausing CLI | Microsoft Azure"
   description="Forstå statisk privat IP'er (fald), og hvordan du administrerer dem i tilstanden Klassisk ved hjælp af CLI"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-azure-cli"></a>Sådan angives en statisk privat IP-adresse (klassisk) i Azure CLI

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Denne artikel omhandler klassisk implementeringsmodel. Du kan også [administrere en statisk privat IP-adresse i implementeringsmodel ressourcestyring](virtual-networks-static-private-ip-arm-cli.md).

Nedenstående eksempel Azure CLI kommandoerne forventer et enkelt miljø, som allerede har oprettet. Hvis du vil køre kommandoer, som de vises i dette dokument, skal først opbygge testmiljø, der er beskrevet i [oprette en vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Sådan angives en statisk privat IP-adresse, når du opretter en VM
Hvis du vil oprette en ny VM med navnet *DNS01* i en ny skybaseret tjeneste med navnet *TestService* baseret på ovenstående scenarie, skal du følge disse trin:

1. Hvis du aldrig har brugt Azure CLI, se [installere og konfigurere Azure CLI](../xplat-cli-install.md) , og følg vejledningen op til det sted, hvor du kan vælge din Azure-konto og abonnement.
1. Kør kommandoen **azure service oprette** til at oprette skytjenesten.

        azure service create TestService --location uscentral

    Forventet afgang:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. Kør kommandoen **azure oprette vm** til at oprette VM. Læg mærke til værdien for en statisk privat IP-adresse. Listen vises, når output forklares de parametre, bruges.

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    Forventet afgang:

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l (eller – placering)**. Azure område, hvor VM skal oprettes. I dette scenario *centralus*.
    - **-n (eller--vm navn)**. Navnet på VM skal oprettes.
    - **-w (eller--virtuelle netværksnavn)**. Navnet på den VNet, hvor VM skal oprettes. 
    - **-S (eller--statiske ip-)**. Statisk privat IP-adresse til VM.
    - **TestService**. Navnet på den skybaseret tjeneste, hvor VM skal oprettes.
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Billede, der bruges til at oprette VM.
    - **adminuser**. Lokal administrator for Windows VM.
    - **AdminP@ssw0rd**. Lokale administratoradgangskode til Windows-VM.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hvordan du kan hente statiske privat IP-adresseoplysninger for en VM
For at få vist statisk privat IP-adresseoplysningerne for det VM, der er oprettet med scriptet ovenfor, du køre følgende kommando for Azure CLI og overholde værdien for *Netværk StaticIP*:

    azure vm static-ip show DNS01

Forventet afgang:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Sådan fjernes en statisk privat IP-adresse fra en VM
Fjerne den statiske private IP-adresse tilføjet til VM i scriptet ovenfor, køre følgende kommando for Azure CLI:
    
    azure vm static-ip remove DNS01

Forventet afgang:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Sådan føjes en statisk privat IP-adresse til en eksisterende VM
Tilføje en statisk privat IP-adresse til VM oprettet ved hjælp af script over runt følgende kommando:

    azure vm static-ip set DNS01 192.168.1.101

Forventet afgang:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [reserveret offentlige IP-](virtual-networks-reserved-public-ip.md) adresser.
- Få mere at vide om [forekomst niveau offentlige IP-adresse (ILPIP)](virtual-networks-instance-level-public-ip.md) adresser.
- Se [reserverede IP-REST API'er](https://msdn.microsoft.com/library/azure/dn722420.aspx).
