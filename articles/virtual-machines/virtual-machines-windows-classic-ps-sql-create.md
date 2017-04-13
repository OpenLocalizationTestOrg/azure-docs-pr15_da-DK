<properties
    pageTitle="Oprette en SQL Server virtuel maskine i Azure PowerShell (klassisk) | Microsoft Azure"
    description="Indeholder trin og PowerShell-scripts til at oprette en Azure VM med SQL Server virtuelt galleriet billeder. Dette emne bruges tilstanden Klassisk installation."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/07/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Klargøre en SQL Server virtuel maskine, ved hjælp af Azure PowerShell (klassisk)

## <a name="overview"></a>Oversigt

Denne artikel indeholder vejledning til at oprette en SQL Server virtuel maskine i Azure ved hjælp af PowerShell-cmdlet'er.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Du kan finde ressourcestyring version af dette emne, [klargøring en SQL Server virtuel maskine, ved hjælp af Azure PowerShell ressourcestyring](virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installer og Konfigurer PowerShell:

1. Hvis du ikke har en Azure-konto, kan du besøge [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).

2. [Downloade og installere de nyeste Azure PowerShell-kommandoer](../powershell-install-configure.md).

3. Start Windows PowerShell, og forbinde den til abonnementet Azure med kommandoen **Tilføj AzureAccount** .

        Add-AzureAccount

## <a name="determine-your-target-azure-region"></a>Finde ud af dine mål Azure område

Din SQL Server virtuelle maskine skal placeres i en skybaseret tjeneste, der findes et bestemt Azure område. Følgende trin hjælpe dig med at finde ud af din region, lager og skybaseret tjeneste, der skal bruges for resten af selvstudiet.

1. Find ud af det datacenter, som du vil bruge til at hoste din SQL Server VM. Følgende PowerShell-kommandoer vises de tilgængelige områder i detaljer med en oversigt i slutningen.

        Get-AzureLocation
        (Get-AzureLocation).Name

2.  Når du har identificeret din foretrukne placering, kan du angive en variabel med navnet **$dcLocation** til dette område.

        $dcLocation = "<region name>"

## <a name="set-your-subscription-and-storage-account"></a>Angiv dit abonnement og lager konto

1. Find ud af det Azure abonnement, du vil bruge til den nye virtuelle maskine.

        (Get-AzureSubscription).SubscriptionName

1. Tildele dine mål Azure abonnement til variablen **$subscr** . Angiv derefter det som dit aktuelle abonnement, Azure.

        $subscr="<subscription name>"
        Select-AzureSubscription -SubscriptionName $subscr –Current

1. Markér derefter afkrydsningsfeltet for eksisterende lagerplads konti. Følgende script viser alle lagerplads konti, der findes i dit valgte område:

        (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

    >[AZURE.NOTE] Hvis du kræver en ny konto lagerplads, først oprette en kontonavn til alle små lager med kommandoen ny AzureStorageAccount som i følgende eksempel: **Ny AzureStorageAccount - StorageAccountName "<storage account name>"-placering $dcLocation**

1. Tildele kontonavn target lager til **$staccount**. Brug derefter **Sæt AzureSubscription** til at angive abonnement og aktuelle lagerplads konto.

        $staccount="<storage account name>"
        Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## <a name="select-a-sql-server-virtual-machine-image"></a>Vælge et virtuelt billede af SQL Server

1. Find ud af på listen over tilgængelige SQL Server virtuelle maskiner billeder fra galleriet. Disse billeder alle har en **ImageFamily** egenskab, der starter med "SQL". Følgende forespørgsel viser billede familien tilgængelige for dig, der har SQL Server forudinstalleret.

        Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Når du har fundet virtuel maskine billede familie, kan der være flere publicerede billeder i denne serie. Brug følgende script til at finde det seneste publicerede virtuelt billede navn til familiens valgte billede (såsom **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2**):

        $family="<ImageFamily value>"
        $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

        echo "Selected SQL Server image name:"
        echo "   $image"

## <a name="create-the-virtual-machine"></a>Oprette den virtuelle maskine

Til sidst skal oprette den virtuelle maskine med PowerShell:

1. Oprette en skybaseret tjeneste, hvis du vil placere den nye VM. Bemærk, at det er også muligt at bruge en eksisterende skytjeneste i stedet. Opret en ny variabel **$svcname** med det korte navn på skytjenesten.

        $svcname = "<cloud service name>"
        New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Angiv navnet på den virtuelle computer og en størrelse. Du kan finde flere oplysninger om virtuelt størrelser, [Virtuelt størrelser til Azure](virtual-machines-windows-sizes.md).

        $vmname="<machine name>"
        $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
        $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Angiv den lokale administratorkonto og adgangskode.

        $cred=Get-Credential -Message "Type the name and password of the local administrator account."
        $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Kør følgende script for at oprette den virtuelle maskine.

        New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] I afsnittet **opbygge din kommandosæt** i [Bruge Azure PowerShell til at oprette og konfigurerer på forhånd Windows-baserede virtuelle maskiner](virtual-machines-windows-classic-create-powershell.md)yderligere forklaring og -indstillinger.

## <a name="example-powershell-script"></a>Eksempel PowerShell-script

Følgende script giver og eksempel på et komplet script, der opretter en **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2** virtuel maskine. Hvis du bruger dette script, skal du tilpasse de indledende variabler, der er baseret på de forrige trin i dette emne.

    # Customize these variables based on your settings and requirements:
    $dcLocation = "East US"
    $subscr="mysubscription"
    $staccount="mystorageaccount"
    $family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
    $svcname = "mycloudservice"
    $vmname="myvirtualmachine"
    $vmsize="A5"

    # Set the current subscription and storage account
    # Comment out the New-AzureStorageAccount line if the account already exists
    Select-AzureSubscription -SubscriptionName $subscr –Current
    New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

    # Select the most recent VM image in this image family:
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    # Create the new cloud service; comment out this line if cloud service exists already:
    New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

    # Create the VM config:
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    # Set administrator credentials:
    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    # Create the SQL Server VM:
    New-AzureVM –ServiceName $svcname -VMs $vm1


## <a name="connect-with-remote-desktop"></a>Oprette forbindelse til Fjernskrivebord

1. Oprette den. RDP-filer i den aktuelle bruger dokumentmappe til Start disse virtuelle computere at fuldføre installationen:

        $documentspath = [environment]::getfolderpath("mydocuments")
        Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Start RDP-filen i dokumentmappen. Oprette forbindelse med administratorens brugernavn og adgangskode, der er angivet tidligere (for eksempel hvis dit brugernavn var VMAdmin, angive "\VMAdmin" som brugeren og angive adgangskoden).

        cd $documentspath
        .\vm1.rdp

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Fuldføre konfigurationen af SQL Server-computer til fjernadgang

Når du har logget på maskinen med Fjernskrivebord, konfiguration af SQL Server, der er baseret på instruktionerne i [trin til konfiguration af SQL Server-forbindelse i en Azure VM](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Næste trin

Du kan finde yderligere instruktioner til klargøring af virtuelle maskiner med PowerShell i [virtuelle maskiner dokumentation](virtual-machines-windows-classic-create-powershell.md). Du kan finde yderligere scripts, der er relateret til SQL Server og Premium lager, [Brug Azure Premium lager med SQL Server på virtuelle maskiner](virtual-machines-windows-classic-sql-server-premium-storage.md).

I mange situationer er næste trin til at overføre dine databaser til denne nye SQL Server VM. Vejledning til database overførsel finder du under [overførsel af en Database til SQL Server på en Azure VM](virtual-machines-windows-migrate-sql.md).

Hvis du er også interesseret i portalen Azure til at oprette SQL virtuelle maskiner, kan du se [klargøring af en SQL Server virtuel maskine på Azure](virtual-machines-windows-portal-sql-server-provision.md). Bemærk, at det selvstudium, der vejleder dig gennem portalen opretter FOS bruger anbefalede ressourcestyring modellen i stedet for den klassiske model, der bruges i dette emne PowerShell.

Ud over disse ressourcer anbefaler vi, at du gennemser [andre emner, der er relateret til kører SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).
