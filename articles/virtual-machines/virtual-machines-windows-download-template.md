<properties
    pageTitle="Oprette et VM billede fra en Azure VM | Microsoft Azure"
    description="Lær, hvordan du opretter en generalized VM afbildning fra en eksisterende Azure VM, der er oprettet i implementeringsmodel ressourcestyring"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>


# <a name="download-the-template-for-a-vm"></a>Hente skabelonen for en VM

Når du opretter en VM i Azure ved hjælp af portalen eller PowerShell, oprettes der automatisk en ressourcestyring skabelon for dig. Du kan bruge denne skabelon til hurtigt for at duplikere en installation. Skabelonen indeholder oplysninger om alle ressourcerne i en ressourcegruppe. For et virtuelt betyder skabelon beholdere alt, som er oprettet overholde VM i den ressourcegruppe, herunder de ressourcer, der netværk.

## <a name="download-the-template-using-the-portal"></a>Hente skabelonen ved hjælp af portalen

1. Log på [Azure-portalen](https://portal.azure.com/).
2. En menuen hub skal du markere **virtuelle computere**.
3. Vælg den virtuelle maskine på listen.
5. Vælg **automatiseringsscript**.
6. Klik på **Hent** , og Gem .zip-filen til din lokale computer.
7. Åbn .zip-filen, og udpakke filerne til en mappe. .Zip-filen skal indeholde:
    
    - Deploy.ps1
    - Deploy.sh 
    - deployer.RB
    - DeploymentHelper.cs
    - Parameters.JSON
    - Template.JSON

Filen .json er skabelonen.
    
## <a name="download-the-template-using-powershell"></a>Hente skabelonen ved hjælp af PowerShell

Du kan også hente skabelonfilen .json ved hjælp af [Eksportér AzureRMResourceGroup](https://msdn.microsoft.com/library/mt715427.aspx) cmdlet. Du kan bruge den `-path` parameter til at levere filnavnet og stien til filen .json. I dette eksempel vises, hvordan du kan hente skabelonen for ressourcegruppen med navnet **myResourceGroup** til mappen **C:\users\public\downloads** på din lokale computer.

```powershell
    Export-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om installation af ressourcer ved hjælp af skabeloner, skal du se [ressourcestyring skabelon gennemgang](../resource-manager-template-walkthrough.md).