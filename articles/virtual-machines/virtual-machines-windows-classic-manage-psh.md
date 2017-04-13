<properties
   pageTitle="Administrere din virtuelle maskiner ved hjælp af PowerShell Azure | Microsoft Azure"
   description="Se kommandoer, som du kan bruge til at automatisere opgaver med at administrere din virtuelle computere."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Administrere din virtuelle maskiner ved hjælp af Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Mange opgaver, der udføres hver dag for at administrere din FOS kan automatisk ved hjælp af Azure PowerShell-cmdlet'er. I denne artikel får du eksempel kommandoer til nemmere opgaver og links til artikler, der viser kommandoer til mere komplekse opgaver.

>[AZURE.NOTE] Hvis du ikke har installeret og konfigureret Azure PowerShell endnu, kan du få vejledning i artiklen [Sådan installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Hvordan du kan bruge kommandoerne eksempel
Du skal erstatte noget af teksten på listen kommandoer med tekst, der er relevante for dit miljø. Den < og > symbolerne angiver tekst, du vil erstatte. Når du erstatter teksten, fjerne symbolerne, men lade anførselstegnene på plads.

## <a name="get-a-vm"></a>Få en VM
Dette er en grundlæggende opgave, du skal bruge ofte. Bruge det til at få oplysninger om en VM, udføre opgaver på en VM eller få vist til at gemme i en variabel.

Hvis du vil have oplysninger om VM, kører denne kommando, erstatter alt i anførselstegn, herunder den < og > tegn:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Hvis du vil gemme output i en $vm variabel, skal du køre:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Log på en Windows-baseret VM

Kør følgende kommandoer:

>[AZURE.NOTE] Du kan finde navnet på tjenesten virtuelt og skyen fra visningen af kommandoen **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Stoppe en VM

Kør følgende kommando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Brug denne parameter til at beholde den virtuelle IP-adresse (VIP) af skytjenesten, i tilfælde af, at det er den sidste VM i pågældende skybaseret tjeneste. <br><br> Hvis du bruger parameteren StayProvisioned, faktureres du stadig af VM.

## <a name="start-a-vm"></a>Starte en VM

Kør følgende kommando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Vedhæfte en datadisk
Denne opgave kræver nogle få trin. Først skal du bruge den *** Tilføj-AzureDataDisk *** til at føje disken til objektet $vm. Derefter kan du bruge **Opdatering AzureVM** cmdlet til at opdatere konfigurationen af VM.

Du skal også beslutte, om du vil vedhæfte en ny disk eller en, der indeholder data. Kommandoen opretter .vhd-filen til en ny disk og den knyttes.

Hvis du vil vedhæfte en ny disk, du Kør denne kommando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Hvis du vil vedhæfte en eksisterende datadisk, du Kør denne kommando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Hvis du vil vedhæfte datadisce fra en eksisterende .vhd-fil i blob-lager, du Kør denne kommando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Oprette en Windows-baseret VM

For at oprette en ny Windows-baseret virtuel maskine i Azure, skal du følge vejledningen i [Bruge Azure PowerShell til at oprette og konfigurerer på forhånd virtuelle Windows-baserede computere](virtual-machines-windows-classic-create-powershell.md). Dette emne trin dig gennem oprettelse af en Azure PowerShell kommandoen opsætning, der opretter en Windows-baseret VM, der kan være forudkonfigureret:

- Med medlemskab af Active Directory-domæner.
- Med flere diske.
- Angiv som medlem af en eksisterende netværksbelastningen.
- Med en statisk IP-adresse.
