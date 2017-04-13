<properties
    pageTitle="Oprette en SQL Server virtuel maskine i Azure PowerShell (ressourcestyring) | Microsoft Azure"
    description="Indeholder trin og PowerShell-scripts til at oprette en Azure VM med SQL Server virtuelt galleriet billeder."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Klargøre en SQL Server virtuel maskine, ved hjælp af Azure PowerShell (ressourcestyring)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## <a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du opretter en enkelt Azure virtuelt ved hjælp af implementeringsmodel **Azure ressourcestyring** , ved hjælp af Azure PowerShell-cmdlet'er. I dette selvstudium skal du oprette en enkelt virtuel maskine ved hjælp af et enkelt diskdrev fra et billede i galleriet SQL. Vi opretter nye udbydere for lagerplads-, Netværks- og Beregn ressourcer, der skal bruges ved den virtuelle maskine. Hvis du har eksisterende udbydere på grund af disse ressourcer, kan du bruge disse udbydere i stedet.

Hvis du har brug for den klassiske version af dette emne, kan du se [klargøring en SQL Server virtuel maskine, ved hjælp af Azure PowerShell klassisk](virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Forudsætninger

I dette selvstudium skal du:

- En Azure-konto og abonnement, før du går i gang. Hvis du ikke har en tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell)](../powershell-install-configure.md), minimumversion af 1.4.0 eller nyere (dette selvstudium, der er skrevet ved hjælp af version 1.5.0).
    - For at hente din version skal du skrive **Get-modul Azure-ListAvailable**.

## <a name="configure-your-subscription"></a>Konfigurere dit abonnement

Åbn Windows PowerShell og oprette adgang til kontoen Azure ved at køre følgende cmdlet. Du vil få vist en logonskærm angive dine legitimationsoplysninger. Brug af samme mail og adgangskode, du bruger til at logge på portalen Azure.

    Add-AzureRmAccount

Efter du har logget korrekt vises nogle oplysninger på skærmen, der indeholder den SubscriptionId, som du er logget på. Dette er det abonnement, hvori ressourcerne til dette selvstudium oprettes, medmindre du ændrer til et andet abonnement. Hvis du har flere SubscriptionIds, skal du køre følgende cmdlet for at få vist en liste over alle dine SubscriptionIds:

    Get-AzureRmSubscription

Hvis du vil ændre til en anden SubscriptionID, skal du køre følgende cmdlet med dit ønskede SubscriptionId.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Definere billede variabler

For at forenkle brugervenligheden og forståelse af det færdige script fra dette selvstudium, starter vi ved at definere et antal variabler. Ændre parameterværdier, som du behov, men vær opmærksom på navngive begrænsninger, der er relateret til navnet længde og specialtegn, når du ændrer de værdier, der er angivet.

### <a name="location-and-resource-group"></a>Placering og ressourcegruppe
Brug to variabler til at definere dataområdet og ressourcegruppen, hvor du vil oprette de andre ressourcer til den virtuelle maskine.

Redigere efter behov, og udfør derefter følgende cmdletter for at initialiseret variablerne.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Egenskaber for storage

Brug følgende variabler til at definere kontoen lager og typen lagerplads, der skal anvendes af den virtuelle maskine.

Redigere efter behov og derefter køre følgende cmdlet for at initialiseret variablerne. Bemærk, at vi i dette eksempel bruger [Premium lagerplads](../storage/storage-premium-storage.md), der anbefales til fremstilling arbejdsmængder. Du kan finde oplysninger om denne vejledning og andre anbefalinger, [ydeevne bedste fremgangsmåder til SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Egenskaber for netværk

Brug følgende variabler til at definere grænsefladen for det netværk, metode til TCP/IP-fordeling, navnet på det virtuelle netværk, virtuelle undernet navn, de område af IP-adresser for det virtuelle netværk, de område af IP-adresser for undernettet og etiketten offentligt tilgængelige navn der skal bruges i netværket i den virtuelle maskine.

Redigere efter behov og derefter køre følgende cmdlet for at initialiseret variablerne.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### <a name="virtual-machine-properties"></a>Virtuelt egenskaber

Brug følgende variabler til at definere virtuelt navnet, navnet på den computer, virtuelt størrelse og operativsystem disk navn til den virtuelle maskine.

Redigere efter behov og derefter køre følgende cmdlet for at initialiseret variablerne.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Egenskaber for billede

Brug følgende variabler til at definere billedet for at bruge til den virtuelle maskine. I dette eksempel bruges SQL Server 2016 Enterprise billedet.

Redigere efter behov og derefter køre følgende cmdlet for at initialiseret variablerne.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Bemærk, at du kan få en komplet liste over SQL Server billede tilbud med kommandoen Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Og du kan se den tilgængelig for en tilbyder med kommandoen Get-AzureRmVMImageSku lagerenheder. Følgende kommando viser alle SKU'er tilgængelig for **SQL2014SP1 WS2012R2** tilbyder.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Oprette en ressourcegruppe

Med implementeringsmodel ressourcestyring er det første objekt, du opretter ressourcegruppen. Vi bruger [Ny AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) cmdlet til at oprette en Azure ressourcegruppe og dens ressourcer med ressource gruppenavn og placering, der er defineret af variablerne, du tidligere har initialiseret.

Udføre følgende cmdlet for at oprette en ny ressourcegruppe.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Oprette en lagerplads-konto

Den virtuelle maskine kræver lagerplads ressourcer til operativsystem disken og for de SQL Server data og logfiler. For enkel, skal du oprette en enkelt disk for begge. Du kan vedhæfte flere diske senere ved hjælp af [Tilføj Azure Disk](https://msdn.microsoft.com/library/azure/dn495252.aspx) cmdlet for at placere dine SQL Server-data og logfiler på dedikeret diske. Vi bruger [Ny AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) cmdlet til at oprette en standard-lager-konto i din nye ressourcegruppe og med kontonavn lager, lagerplads Sku navn og placering, der er defineret ved hjælp af de variabler, som du tidligere har initialiseret.

Udføre følgende cmdlet for at oprette kontoen nye lagerplads.  

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Oprette netværksressourcer

Den virtuelle maskine kræver et antal netværksressourcer til netværksforbindelse.

- Hver virtuelt kræver et virtuelt netværk.
- Et virtuelt netværk skal have mindst én undernet, der er defineret.
- En netværkskort skal være defineret med en offentlig eller en privat IP-adresse.

### <a name="create-a-virtual-network-subnet-configuration"></a>Oprette en virtuel undernet netværkskonfiguration

Vi starter ved at oprette en undernet konfiguration til vores virtuelt netværk. Vores selvstudium, skal du oprette et standard-undernet, ved hjælp af [Ny AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) cmdlet. Vi opretter vores virtuelle undernet netværkskonfiguration med undernet navn og adresse præfikset defineret ved hjælp af de variabler, som du tidligere har initialiseret.

>[AZURE.NOTE] Du kan angive yderligere egenskaber for den virtuelle undernet netværkskonfiguration ved hjælp af denne cmdlet, men det er ikke medtaget i dette selvstudium.

Udføre følgende cmdlet for at oprette virtuelle undernet konfigurationen.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Oprette et virtuelt netværk

Derefter skal du oprette vores virtuelle netværk ved hjælp af [Ny AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) cmdlet. Vi opretter vores virtuelt netværk i din nye ressourcegruppe, med det navn, placering og adresse præfiks defineret ved hjælp af de variabler, som du tidligere har initialiseret, og den undernet konfiguration, som du angav i ovenstående trin.

Udføre følgende cmdlet for at oprette dit virtuelle netværk.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Oprette den offentlige IP-adresse

Nu hvor vi har vores virtuelt netværk, der er defineret, skal vi konfigurere en forbindelse til den virtuelle maskine IP-adresse. I dette selvstudium opretter vi en offentlig IP-adresse, der bruger dynamiske IP-adresser for at understøtte forbindelse til internettet. Vi bruger [Ny AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) cmdlet til at oprette den offentlige IP-adresse i ressourcegruppen oprettet prevously og med det navn, placering, fordelingsmetoden og DNS-domæne navn defineret ved hjælp af de variabler, som du tidligere har initialiseret.

>[AZURE.NOTE] Du kan angive yderligere egenskaber for den offentlige IP-adresse ved hjælp af denne cmdlet, men det er ikke medtaget i selvstudiet indledende. Du kan også oprette en privat adresse eller en adresse med en statisk adresse, men det er også ud over omfanget af dette selvstudium.

Udføre følgende cmdlet for at oprette din offentlige IP-adresse.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Oprette netværksgrænsefladen

Vi er nu klar til at oprette grænsefladen for netværk, som vores virtuelt skal bruge. Vi bruger [Ny AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) cmdlet til at oprette vores netværksgrænseflade i ressourcegruppen, der er oprettet tidligere og med det navn, placering, undernet og offentlige IP-adresse, der tidligere er defineret.

Udføre følgende cmdlet for at oprette dit netværksgrænseflade.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Konfigurere et VM objekt

Nu hvor vi har lager og netværk ressourcer, der er defineret, er vi klar til at definere Beregn ressourcer til den virtuelle maskine. Vores selvstudium, vil vi angive virtuelt størrelse og egenskaber for forskellige operativsystemet skal du angive grænsefladen for det netværk, som vi tidligere har oprettet, definere blob-lager, og angiv derefter operativsystem disken.

### <a name="create-the-vm-object"></a>Oprette objektet VM

Vi starter ved at angive den virtuelle maskine størrelse. I dette selvstudium, vi angiver en DS13. Vi bruger [Ny AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) cmdlet til at oprette en konfigurerbar virtuelt objekt med navn og størrelse, der er defineret ved hjælp af de variabler, som du tidligere har initialiseret.

Udføre følgende cmdlet for at oprette virtuelle Maskinobjekt.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Oprette en legitimationsoplysninger for objektet for at holde brugernavn og adgangskode til de lokale administratorrettigheder

Før vi kan angive operativsystem egenskaberne for den virtuelle maskine, har vi brug at angive legitimationsoplysninger for den lokale administratorkonto som en sikker streng. For at opnå dette, skal bruge vi Cmdletten [Get-legitimationsoplysninger](https://technet.microsoft.com/library/hh849815.aspx) .

Udføre følgende cmdlet, og Skriv brugernavn og adgangskode skal bruges til den lokale administratorkonto i Windows virtual machine i vinduet Windows PowerShell legitimationsoplysninger anmodning.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Angive egenskaber for den virtuelle maskine operativsystem

Vi er nu klar til at angive den virtuelle maskine operativsystem egenskaber. Vi bruger cmdlet'en [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) til at angive typen operativsystem som Windows, kræver [virtuelt agent](virtual-machines-windows-classic-agents-and-extensions.md) til at være installeret, angive, at cmdlet aktiverer automatisk opdatering og angive navnet på den virtuelle computer, navn på den computer og de legitimationsoplysninger, ved hjælp af de variabler, som du tidligere har initialiseret.

Udføre følgende cmdlet for at angive egenskaber for din virtuelle maskine operativsystem.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Føje netværksgrænsefladen til den virtuelle maskine

Dernæst skal tilføje vi grænsefladen for det netværk, du oprettede tidligere til den virtuelle maskine. Vi bruger [Tilføj AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) cmdlet til at tilføje netværksgrænsefladen ved hjælp af variablen netværk grænseflade, du definerede tidligere.

Udføre følgende cmdlet for at angive netværksgrænsefladen for din virtuelle maskine.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Angive blob storage placering til cd'en, der skal bruges i den virtuelle maskine

Vi sæt derefter lagerplacering blob for disken der skal bruges i den virtuelle maskine ved hjælp af variablerne, du definerede tidligere.

Udføre følgende cmdlet for at angive placeringen af blob-lager.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Indstille operativsystemet disk egenskaberne for den virtuelle maskine

Dernæst skal indstiller vi operativsystemet disk egenskaberne for den virtuelle maskine. Vi bruger cmdlet'en [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) til at angive, operativsystemet til den virtuelle maskine kan komme fra et billede til at angive cachelagring for at læse kun (fordi SQL Server er blevet installeret på den samme disk) og definere navnet på den virtuelle computer og operativsystem disken defineret ved hjælp af de variabler, som vi definerede tidligere.

Udføre følgende cmdlet for at angive operativsystemet diskegenskaber for din virtuelle maskine.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Angive platform billedet til den virtuelle maskine

Vores sidste trin i konfigurationen er at angive platform billedet til vores virtual machine. Vores selvstudium bruger vi den nyeste SQL Server 2016 Le billede. Vi vil bruge cmdlet'en [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) til at bruge dette billede, som defineret af variablerne, du definerede tidligere.

Udføre følgende cmdlet for at angive platform billedet til din virtuelle maskine.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Oprette SQL-VM

Nu hvor du er færdig med konfigurationstrinnene, er du klar til at oprette den virtuelle maskine. Vi bruger [Ny AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) cmdlet til at oprette den virtuelle maskine ved hjælp af de variabler, som vi har defineret.

Udføre følgende cmdlet for at oprette din virtuelle maskine.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

Den virtuelle maskine oprettes. Bemærk, at en standard-lagerplads konto oprettes for Start diagnosticering, fordi den angivne lagerplads konto til den virtuelle maskine disk er en premium lagerplads konto.

Du kan nu få vist denne computer i Azure portalen for at se [den offentlige IP-adresse og dens fulde domænenavn](virtual-machines-windows-portal-sql-server-provision.md#Connect).  

## <a name="example-script"></a>Eksempel på et script

Følgende script indeholder den komplette PowerShell-script til dette selvstudium. Det antages det, du har allerede konfigureret Azure abonnementet til brug med kommandoerne **Tilføj AzureRmAccount** og **Vælg AzureRmSubscription** .


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Næste trin
Når den virtuelle maskine er oprettet, er du klar til at oprette forbindelse til den virtuelle maskine ved hjælp af RDP og konfiguration af forbindelse. Yderligere oplysninger finder du se [forbinde til en SQL Server virtuel maskine på Azure (ressourcestyring)](virtual-machines-windows-sql-connect.md).
