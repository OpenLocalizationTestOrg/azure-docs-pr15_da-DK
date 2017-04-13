<properties
    pageTitle="Afbryde forbindelsen til en datadisk, fra en Windows-VM | Microsoft Azure"
    description="Lær at afbryde forbindelsen til en datadisk, fra en virtuel maskine i Azure ved hjælp af Ressourcestyring implementeringsmodel."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Hvordan du kan afbryde forbindelsen til en datadisk, fra en Windows virtuel maskine


Når du ikke længere har brug for en datadisk, der er knyttet til en virtuel maskine, kan du nemt fjerne den. Dette fjerner disken fra den virtuelle maskine, men fjerne ikke det fra lagerplads. 

> [AZURE.WARNING] Hvis du fjerner en disk, ikke slettes den automatisk. Hvis du har et abonnement til Premium-lager, fortsætter du med at betale lagerplads gebyrer for disken. Se [priser og fakturering, når du bruger Premium lagerplads](../storage/storage-premium-storage.md#pricing-and-billing)kan finde flere oplysninger. 

Hvis du vil bruge de eksisterende data på disken igen, kan du vedhæfte filen igen til den samme virtuelle maskine eller en anden plan.  


## <a name="detach-a-data-disk-using-the-portal"></a>Fjerne en datadisk ved hjælp af portalen

1. I portalen-hubben skal du vælge **virtuelle computere**.

2. Vælg den virtuelle maskine, der indeholder data disken, du vil fjerne, og klik derefter på **alle indstillinger**.

3. Vælg **diske**i bladet **Indstillinger** .

4. Vælg disken data, du vil fjerne, i bladet **diske** .

5. Klik på **Afbryd**i bladet for disken data.


    ![Skærmbillede med knappen Afbryd.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

Disken forbliver på lager, men er ikke længere er knyttet til en virtuel maskine.


## <a name="detach-a-data-disk-using-powershell"></a>Fjerne en datadisk ved hjælp af PowerShell

I dette eksempel henter den første kommando den virtuelle maskine med navnet **MyVM07** i gruppen **RG11** ressource ved hjælp af cmdlet'en Get-AzureRmVM til. Kommandoen gemmer den virtuelle maskine i variablen **$VirtualMachine** . 

Den anden kommando fjerner data disken med navnet DataDisk3 fra den virtuelle maskine. 

Kommandoen endelige opdaterer tilstanden for den virtuelle maskine til at fuldføre processen med at fjerne data disken.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Du kan finde flere oplysninger, se [Fjern AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Næste trin

Hvis du vil genbruge data disken, kan du kun [Vedhæft den til en anden VM](virtual-machines-windows-attach-disk-portal.md)
