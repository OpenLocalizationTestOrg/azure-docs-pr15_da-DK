<properties
    pageTitle="Oprette og overføre en VM billede ved hjælp af Powershell | Microsoft Azure"
    description="Lær at oprette og overføre en generalized afbildning af Windows Server (Virtuelle) ved hjælp af klassisk implementeringsmodel og Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Oprette og overføre en Windows Server Virtuelle til Azure

I denne artikel beskrives, hvordan at overføre dine egne generalized VM billede som en virtuel harddisk (Virtuelle), så du kan bruge den til at oprette virtuelle computere. Du kan finde flere oplysninger om og virtuelle harddiske i Microsoft Azure, skal du se [om og virtuelle harddiske til virtuelle maskiner](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Du kan også [overføre](virtual-machines-windows-upload-image.md) en virtuel maskine, ved hjælp af Ressourcestyring modellen. 

## <a name="prerequisites"></a>Forudsætninger

I denne artiklen antages, du har:

- **En Azure abonnement** - Hvis du ikke har en, du kan [åbne en Azure-konto gratis](/pricing/free-trial/?WT.mc_id=A261C142F).

- **[Microsoft Azure PowerShell](../powershell-install-configure.md)** - du har Microsoft Azure PowerShell-modulet installeret og konfigureret til at bruge dit abonnement. 

- **A . Virtuelle fil** - understøttede Windows-operativsystem, der er gemt i en .vhd-fil og knyttet til en virtuel maskine. Kontrollér, hvis de serverroller, der kører på den virtuelle harddisk, der understøttes af Sysprep. Du kan finde yderligere oplysninger finder [Sysprep-understøttelse af serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [AZURE.IMPORTANT] Formatet VHDX understøttes ikke i Microsoft Azure. Du kan konvertere disken Virtuelle formatet ved hjælp af Hyper-V Manager eller [Konverter Virtuelle cmdlet](http://technet.microsoft.com/library/hh848454.aspx). Yderligere oplysninger finder du denne [blogpost](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Trin 1: Forberede den virtuelle harddisk 

Før du overfører den virtuelle harddisk til Azure, skal den være generalized ved hjælp af værktøjet Sysprep. Dette forbereder Virtuelle som skal bruges som et billede. Få mere at vide om Sysprep se [Sådan Brug Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx). Sikkerhedskopiere VM, før du kører Sysprep.

Fra den virtuelle maskine, som operativsystemet er installeret på, at udføre følgende procedure:

1. Log på operativsystemet.

2. Åbn et kommandopromptvindue som administrator. Ændre mappen til **%windir%\system32\sysprep**og derefter køre `sysprep.exe`.

    ![Åbn et kommandopromptvindue](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.  Dialogboksen **Værktøj til forberedelse af System** vises.

    ![Start Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Vælg **Angiv System ud af OOBE Box Experience ()** i **Værktøj til forberedelse af System**, og Sørg for, at **generalisere** er markeret.

5.  Vælg **Luk computeren**i **Indstillinger for lukning**.

6.  Klik på **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Trin 2: Oprette en lagerplads konto og en objektbeholder

Du skal bruge en lagerplads konto i Azure, så du har et sted at overføre .vhd-filen. Dette trin viser, hvordan du opretter en konto, eller få de oplysninger, du har brug for fra en eksisterende konto. Erstatte variablerne i &lsaquo; parenteser &rsaquo; med dine egne oplysninger.

1. Logon

        Add-AzureAccount

1. Angiv dit Azure-abonnement.

        Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Oprette en ny firmapost lagerplads. Navnet på kontoen, lagerplads skal være entydige, 3-24 tegn. Navnet kan være en kombination af bogstaver og tal. Du skal også angive en placering som "Øst os"
        
        New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Angiv den nye lagerplads konto som standard.
        
        Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Oprette en ny objektbeholder.

        New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## <a name="step-3-upload-the-vhd-file"></a>Trin 3: Overføre .vhd-filen

Bruge [Tilføj AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) til at overføre den virtuelle harddisk.

Skriv følgende kommando fra Azure PowerShell-vinduet, du har brugt i det forrige trin, og Erstat variabler i &lsaquo; parenteser &rsaquo; med dine egne oplysninger.

        Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Trin 4: Tilføje billedet til din liste over brugerdefinerede billeder

Brug cmdlet'en [Tilføj AzureVMImage](https://msdn.microsoft.com/library/mt589167.aspx) til at tilføje billedet til listen over dine brugerdefinerede billeder.

        Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## <a name="next-steps"></a>Næste trin

Du kan nu [oprette en brugerdefineret VM](virtual-machines-windows-classic-createportal.md) ved hjælp af det overførte billede.

