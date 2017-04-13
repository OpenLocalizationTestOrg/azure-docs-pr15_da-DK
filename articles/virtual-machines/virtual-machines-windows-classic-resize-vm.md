<properties
    pageTitle="Ændre størrelsen på en klassisk Windows VM | Microsoft Azure"
    description="Ændre størrelsen på en Windows virtuel maskine, der er oprettet i klassisk implementeringsmodel ved hjælp af Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Ændre størrelsen på en Windows-VM, der er oprettet i den klassiske implementeringsmodel

I denne artikel beskrives, hvordan ændre størrelsen på en Windows-VM, der er oprettet i den klassiske implementeringsmodel ved hjælp af Azure Powershell.

Når du beslutter, at muligheden for at ændre størrelsen på en VM, er der to begreber, som styrer udvalg af størrelser, der er tilgængelige til at tilpasse den virtuelle maskine. Første konceptet er det område, hvor din VM er installeret. På listen over VM størrelser tilgængelige område findes under fanen tjenester på websiden Azure områder. Anden konceptet er den fysiske hardware, der er i øjeblikket er vært din VM. De fysiske servere, der er vært for FOS grupperes i klynger almindelige fysisk hardware. Metoden til at ændre en VM størrelse varierer afhængigt af, hvis den ønskede størrelse for nye VM understøttes af den hardware klynge i øjeblikket er vært VM.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Du kan også [ændre størrelsen på en VM, der er oprettet i implementeringsmodel ressourcestyring](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Tilføje din konto

Du skal konfigurere Azure PowerShell til at arbejde med klassisk Azure ressourcer. Følg nedenstående trin for at konfigurere Azure PowerShell for at administrere klassisk ressourcer.

1. Skriv kommandoprompten PowerShell `Add-AzureAccount` , og klik på **Enter**. 
2. Skriv den mailadresse, der er knyttet til abonnementet Azure, og klik på **Fortsæt**. 
3. Skriv i feltet adgangskode for kontoen. 
4. Klik på **Log på**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Ændre størrelsen på i den samme hardware klynge

Hvis du vil ændre størrelsen på en VM til en størrelse, der er tilgængelige i den hardware klynge vært VM skal du udføre følgende trin.

1. Køre følgende PowerShell-kommando til at få vist de tilgængelige i den hardware klynge værtstjeneste skyen som indeholder VM VM størrelser.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Kør følgende kommandoer for at ændre størrelsen på VM.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Ændre størrelsen på en ny hardware klynge

Ændre størrelsen på en VM til en størrelse, der er ikke tilgængelig i den hardware klynge vært VM, skyen tjeneste og alle FOS i skytjenesten skal gendannes. Hver skybaseret tjeneste er hostet på en enkelt hardware klynge, så alle FOS i skytjenesten skal have en størrelse, der understøttes på en hardware-klynge. Følgende trin beskrives det, hvordan ændre størrelsen på en VM ved at slette og derefter genoprette skytjenesten.

1. Køre følgende kommando for PowerShell til at få vist de VM tilgængelige størrelser i området. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Noter alle konfigurationsindstillinger for hver VM i skytjenesten, som indeholder VM til at blive tilpasset. 
3. Slette alle FOS i skytjenesten valg af muligheden for at bevare diskene for hver VM.
4. Genopret VM til at blive tilpasset ved hjælp af den ønskede VM størrelse.
5. Genopret alle andre FOS der var i skyen-tjenesten ved hjælp af en tilgængelig i den hardware klynge nu vært skytjenesten VM størrelse.

Et eksempel på script for at slette og gendanne en skybaseret tjeneste, ved hjælp af en ny VM størrelse kan findes [her](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Næste trin

- [Ændre størrelsen på en VM, der er oprettet i implementeringsmodel ressourcestyring](virtual-machines-windows-resize-vm.md).