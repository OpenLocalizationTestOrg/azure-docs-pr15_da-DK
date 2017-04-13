<properties
    pageTitle="Overføre en Windows Virtuelle for ressourcestyring | Microsoft Azure"
    description="Lær at overføre en Windows virtuel maskine Virtuelle fra det lokale til Azure, ved hjælp af Ressourcestyring implementeringsmodel. Du kan overføre en virtuel harddisk fra enten en generalized eller en særlig VM."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Overføre en Windows Virtuelle fra en lokal VM til Azure 


I denne artikel beskrives, hvordan til at oprette og overføre en Windows virtuel harddisk (Virtuelle) der skal bruges i at oprette en Azure Vm. Du kan overføre en virtuel harddisk fra en generalized VM eller en særlig VM. 

Få mere at vide om og virtuelle harddiske i Azure, kan du se [om og virtuelle harddiske til virtuelle computere](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Forberede VM 

Du kan overføre både generalized og specialiserede virtuelle harddiske til Azure. Hver type kræver, at du har forberedt VM før du starter.

- **Generalized Virtuelle** - en generalized Virtuelle har haft alle dine personlige oplysninger fjernes ved hjælp af Sysprep. Hvis du vil bruge den virtuelle harddisk som et billede til at oprette nye FOS fra, skal du gøre følgende:
    - [Forberede en virtuel harddisk Windows til at overføre til Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [Generalisere den virtuelle maskine ved hjælp af Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **Specialiserede Virtuelle** - en særlig Virtuelle fører brugerkonti, programmer og andre tilstand data fra din oprindelige VM. Hvis du vil bruge den virtuelle harddisk som-er at oprette en ny VM, sikre følgende trin er fuldført. 
    - [Forberede en virtuel harddisk Windows til at overføre til Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **Send ikke** generalize VM ved hjælp af Sysprep.
    - Fjerne alle gæst virtualization værktøjer og supportmedarbejdere, der er installeret på VM (det vil sige VMware funktioner).
    - Sørg for VM er konfigureret til at trække dens IP-adresse og DNS-indstillingerne via DHCP. Dette sikrer, at serveren får en IP-adresse i VNet, når det starter. 

## <a name="log-in-to-azure"></a>Log på Azure

Hvis du ikke allerede har PowerShell version 1.4 eller nyere installeret, Læs [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

1. Åbne Azure PowerShell og logge på din Azure-konto. Der åbnes et pop op-vindue for dig at angive dine legitimationsoplysninger Azure-konto.

    ```powershell
    Login-AzureRmAccount
    ```


2. Få abonnementet id'er for dine tilgængelige abonnementer.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Angive den korrekte abonnement via abonnement-ID. Erstatte `<subscriptionID>` med ID'ET for det korrekte abonnement.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Få lagerplads-konto

Du har brug for en lagerplads konto i Azure gemme overførte VM billedet. Du kan bruge en eksisterende lagerplads-konto eller oprette en ny. 

For at vise de tilgængelige lagerplads konti skal du skrive:

```powershell
Get-AzureRmStorageAccount
```

Hvis du vil bruge en eksisterende konto lagerplads, skal du gå til afsnittet [overføre VM billede](#upload-the-vm-vhd-to-your-storage-account) .

Hvis du vil oprette en lagerplads-konto, skal du følge disse trin:

1. Du har brug for navnet på den ressourcegruppe, hvor kontoen lagerplads skal der oprettes. For at finde ud af alle ressourcegrupper, der findes i dit abonnement, skal du skrive:

    ```powershell
    Get-AzureRmResourceGroup
    ```

For at oprette en ressourcegruppe med navnet **myResourceGroup** i **Vest USA** område skal du skrive:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Oprette en lagerplads konto med navnet **mystorageaccount** i denne ressourcegruppe ved hjælp af [Ny AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet:

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Gyldige værdier for - SkuName er:

    - **Standard_LRS** - lokalt overflødige lagerplads. 
    - **Standard_ZRS** - Zone overflødige lagerplads.
    - **Standard_GRS** - geografisk overflødige lagerplads. 
    - **Standard_RAGRS** - læseadgang geografisk overflødige lagerplads. 
    - **Premium_LRS** - Premium lokalt overflødige lagerplads. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Overføre den virtuelle harddisk til kontoen lagerplads

Brug cmdlet'en [Tilføj AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) til at overføre billedet til en objektbeholder i kontoen lagerplads. I dette eksempel overfører filer **myVHD.vhd** fra `"C:\Users\Public\Documents\Virtual hard disks\"` til et lager konto med navnet **mystorageaccount** i gruppen **myResourceGroup** ressource. Filen skal placeres i beholderen **mycontainer** og det nye filnavn bliver **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Hvis det lykkes, får du et svar, der ser nogenlunde sådan ud:

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Afhængigt af din netværksforbindelse og størrelsen på filen Virtuelle kan denne kommando tage et stykke tid at fuldføre


## <a name="next-steps"></a>Næste trin

- [Oprette en VM i Azure fra en generalized virtuel harddisk](virtual-machines-windows-create-vm-generalized.md)
- [Opret en VM i Azure fra en særlig Virtuelle](virtual-machines-windows-create-vm-specialized.md) ved at vedhæfte den som en OS disk, når du opretter en ny VM.


