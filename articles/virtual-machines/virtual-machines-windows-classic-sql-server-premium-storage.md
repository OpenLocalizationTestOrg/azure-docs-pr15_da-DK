<properties
    pageTitle="Bruge Azure Premium lagerplads med SQL Server | Microsoft Azure"
    description="I denne artikel bruger ressourcer, der er oprettet med den klassiske implementeringsmodel og giver vejledning i brug af Azure Premium lager med SQL Server, der kører på virtuelle Azure-computere."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="danielsollondon"
    manager="jhubbard"
    editor="monicar"    
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth"/>

# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Bruge Azure Premium lagerplads med SQL Server på virtuelle maskiner


## <a name="overview"></a>Oversigt

[Azure Premium lager](../storage/storage-premium-storage.md) er det næste generering af lagerplads, som indeholder et kort ventetid og høj overførselshastighed EY. Det fungerer bedst med vigtige EY intensivt arbejdsbelastninger, såsom SQL Server på IaaS [virtuelle computere](https://azure.microsoft.com/services/virtual-machines/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Denne artikel indeholder planlægning og vejledning for at overføre en virtuel maskine, der kører SQL Server til at bruge Premium-lagerplads. Dette omfatter Azure infrastruktur (netværk, storage) og Gæst Windows VM trin. Eksemplet i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) viser en fulde fyldestgørende til en komplet migrering af hvordan du flytter større FOS at drage fordel af forbedrede lokale SSD lagerplads med PowerShell.

Det er vigtigt at forstå processen til slut ved at benytte Azure Premium storage med SQL Server på IAAS FOS. Dette omfatter:

- Identifikation af forudsætninger for at kunne bruge Premium-lagerplads.
- Eksempler på installation af SQL Server på IaaS til Premium-lager til nye installationer.
- Eksempler på overføre eksisterende installationer, både separate servere og brug af SQL altid på tilgængelighed grupper-installationer.
- Mulige overførsel fremgangsmåder.
- Fuldstændig til en komplet eksempel, der viser Azure, Windows og SQL Server trin til overførslen af en eksisterende altid på implementering.

Du kan finde flere baggrundsoplysninger om SQL Server på virtuelle Azure-computere, [SQL Server på virtuelle Azure-computere](virtual-machines-windows-sql-server-iaas-overview.md).

**Forfatter:** Daniel Sol **tekniske korrekturlæsere:** Mikkel Carlos Vargas sild Sanjay Mishra Pravin Mital, Juergen Thomas, og Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Forudsætninger for Premium-lagerplads

Der er flere forudsætninger for at bruge Premium-lager.

### <a name="machine-size"></a>Maskine størrelse

Du skal bruge DS serie virtuelle maskiner (VM) til brug af Premium lagerplads. Hvis du ikke har brugt DS serie computere i din skybaseret tjeneste før, skal du slette den eksisterende VM, holde vedhæftede diskene og derefter oprette en ny tjeneste i skyen før genoprette VM som DS * rolle størrelse. Se [virtuelt og skyen Service størrelser til Azure](virtual-machines-linux-sizes.md)kan finde flere oplysninger om virtuelt størrelser.

### <a name="cloud-services"></a>Skytjenester

Du kan kun bruge DS * FOS med Premium lagerplads, når de er oprettet i en ny tjeneste i skyen. Hvis du bruger SQL Server altid på i Azure, refererer altid på lytteren til den Azure interne og eksterne Indlæs belastningsjusteringstjenesten IP-adresse, der er knyttet til en skybaseret tjeneste. I denne artikel fokuserer på, hvordan til at overføre og samtidig bevare tilgængelighed i dette scenarie.

> [AZURE.NOTE] En DS * serie skal være det første VM, der er installeret på den nye tjeneste i skyen.

### <a name="regional-vnets"></a>Internationale VNETS

Du skal konfigurere den virtuelle netværk (VNET) vært for din FOS for at være internationale for DS * FOS. Dette "udvider" på VNET bruges til at tillade større FOS klargøres i andre klynger og tillade kommunikation mellem dem. På følgende skærmbillede viser den fremhævede placering internationale VNETs, mens det første resultat viser en "smal" VNET.

![RegionalVNET][1]

Du kan hæve en Microsoft-supportbilletter for at overføre til en internationale VNET, Microsoft vil foretage en ændring, for at gennemføre overflytningen til internationale VNETs, og skift derefter egenskaben AffinityGroup i netværkskonfigurationen. Først eksportere netværkskonfiguration i PowerShell, og derefter erstatte egenskaben **AffinityGroup** i elementet **VirtualNetworkSite** til en egenskab for **placering** . Angive `Location = XXXX` hvor `XXXX` er et Azure område. Derefter importere den nye konfiguration.

For eksempel at overveje følgende VNET konfiguration:

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

For at flytte det til en internationale VNET i Vest Europa, skal du ændre konfigurationen til følgende:

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Lagerplads konti

Du skal oprette en ny lagerplads-konto, der er konfigureret til Premium-lagerplads. Bemærk, at afkrydsningsfeltet Brug Premium-lager er angivet til kontoen lagerplads, ikke på individuelle virtuelle harddiske, men når du bruger en DS * serie VM kan du vedhæfte Virtuelles fra Premium og Standard lager konti. Du kan overveje, hvis du ikke vil placere den OS virtuelle harddisk på kontoen, Premium lagerplads.

Følgende kommando for **Ny AzureStorageAccountPowerShell** med "Premium_LRS" **Type** opretter en Premium lagerplads konto:

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Virtuelle harddiske cacheindstillinger

Primære forskellen mellem oprette diske, der er en del af en Premium lagerplads konto er indstillingen disk cache. Det anbefales, at du bruger '**Læst cachelagring**' om en SQL Server Data lydstyrken disk. For transaktion log enheder, skal du vælge indstillingen disk cache til '**ingen**'. Dette er anderledes end anbefalinger til Standard lagerplads konti.

Når de virtuelle harddiske er blevet vedhæftet, kan ikke ændres indstillingen cache. Du har brug at fjerne og Tilslut den virtuelle harddisk med en opdateret cacheindstilling.

### <a name="windows-storage-spaces"></a>Windows-lagerplads mellemrum

Du kan bruge [Windows lagerplads mellemrum](https://technet.microsoft.com/library/hh831739.aspx) , som du gjorde med forrige Standard lagerplads, dette gør det muligt at overføre en VM, der allerede ved hjælp af lagerplads mellemrum. Eksemplet i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) (trin 9 og frem) demonstrerer Powershell-koden for at udtrække og importere en VM med flere vedhæftede virtuelle harddiske.

Lagerplads grupper blev brugt med Standard Azure-lager-konto til at forbedre overførselshastighed og reducere ventetid. Du kan finde værdien i test lagerplads grupper med Premium lagerplads til nye installationer, men de kan føje yderligere kompleksitet med lagerplads konfiguration.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Sådan finder du hvilke Azure virtuelle disk knyttes til lagerplads grupper

Når der er forskellige cache indstilling anbefalinger vedhæftede virtuelle, kan du beslutter at kopiere de virtuelle harddiske til en Premium lagerplads konto. Når du Tilslut dem til den nye DS serie VM, er du muligvis skal til at ændre cacheindstillingerne. Det er nemmere at anvende Premium opbevaring anbefalede cacheindstillinger, når du har en separat virtuelle harddiske for SQL-datafiler og logfiler (i stedet for en enkelt virtuel harddisk, der indeholder begge).

> [AZURE.NOTE] Hvis du har SQL Server data og logfiler filer på samme lydstyrken, afhænger af den indstilling for cachelagring du vælger EY access mønstre for din database arbejdsmængder. Kun test kan viser, hvilken indstilling for cachelagring der passer bedst til dette scenario.

Hvis du bruger Windows lagerplads rum, som består af flere virtuelle harddiske skal du se på dine oprindelige scripts til at identificere som tilknyttet virtuelle harddiske, i hvilke specifikke puljen, så kan du derefter angive cacheindstillingerne tilsvarende for hver disk.

Hvis du ikke har oprindelige script, der er tilgængelige til at vise dig som virtuelle harddiske overfører til lagerpulje, kan du bruge følgende trin til at bestemme/lagerplads på disken puljen tilknytning.

Følge nedenstående trin for hver disk:

1. Få liste over diske, der er knyttet til VM med kommandoen **Get-AzureVM** :

    Get-AzureVM - ServiceName <servicename> -navn <vmname> | Get-AzureDataDisk

1. Bemærk Diskname og LUN.

    ![DisknameAndLUN][2]

1. Fjernskrivebord til VM. Gå derefter til **Computer Management** | **Enhedshåndtering** | **diskdrev**. Se egenskaberne for hver af de 'Microsoft virtuelle disk'

    ![VirtualDiskProperties][3]

1. LUN tallet her er en reference til LUN antallet du angive, hvornår vedhæfte den virtuelle harddisk til VM.
1. For den 'Microsoft virtuelle Disk' Gå til fanen **Detaljer** , på listen **egenskab** gå derefter til **Driver nøgle**. **Værdien**, noter i **forskydningen**, hvilket er 0002 på følgende skærmbillede. 0002 angiver den PhysicalDisk2, der refererer til lagerpulje.

    ![VirtualDiskPropertyDetails][4]

2. Lagring af de tilknyttede diske for hver lagerpulje:

    Get-StoragePool - FriendlyName AMS1pooldata | Get-fysisk disk

    ![GetStoragePool][5]

Nu kan du bruge tilknyttet disse oplysninger til knytte virtuelle harddiske fysiske diske i lagerplads grupper.

Når du har knyttet virtuelle harddiske til fysiske diske i lagerplads grupper kan du fjerne og kopiere dem til en Premium lagerplads konto derefter vedhæfte dem med indstillingen korrekte cache. Se eksemplet i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), trin 8 til 12. Disse trin viser, hvordan du udtrækker en VM vedhæftet Virtuelle diskkonfiguration til en CSV-fil, kopiere de virtuelle harddiske, ændre disk konfiguration cacheindstillinger og til sidst Geninstaller VM som en DS serie VM med alle tilsluttede diske.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM lagerplads båndbredde og Virtuelle lagerplads overførselshastighed

Mængden lagerplads ydeevnen afhænger af den angivne DS * VM størrelse og Virtuelle størrelsen. FOS har forskellige kvoter for antallet af virtuelle harddiske, som kan knyttes og den maksimale båndbredde, de understøtter (MB/s). Se [virtuelt og skyen Service størrelser til Azure](virtual-machines-linux-sizes.md)for tal bestemte båndbredde.

Øget IOP'ER opnås med disk større. Du bør overveje, når du overvejer din migreringsstien. Få mere at vide [i tabellen for IOP'ER og disktyper](../storage-premium-storage.md#scalability-and-performance-targets-when-using-premium-storage).

Til sidst skal du overveje, FOS har forskellige maksimale disk båndbredder de understøtter om en alle disk vedhæftet. Høj belastning, kan du mætning den maksimale disk båndbredde, der er tilgængelige for den pågældende VM rolle størrelse. For eksempel understøtter en Standard_DS14 op til 512 MB/s; Du kan derfor mætning disk båndbredde VM med tre P30 diske. Men i dette eksempel kan overførselshastighed grænse overskrides afhængigt af kombination af læse- og skrivetilladelser IOs.

## <a name="new-deployments"></a>Nye installationer

De næste to afsnit viser, hvordan du kan installere SQL Server FOS til Premium-lager. Som nævnt før, behøver du behøver at være ikke at lægge OS cd'en til Premium-lager. Du kan vælge at gøre dette, hvis du er har til hensigt at placere en hvilken som helst intensivt EY arbejdsbelastninger på OS Virtuelle.

Det første eksempel demonstrerer ved hjælp af eksisterende Azure galleriet billeder. Det andet eksempel viser hvordan du bruger et brugerdefineret VM billede, du har i en eksisterende Standard lagerplads-konto.

> [AZURE.NOTE] Disse eksempler forudsætter, at du allerede har oprettet en internationale VNET.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Oprette en ny VM med Premium Storage med galleriet Hurtige

I eksemplet nedenfor viser, hvordan placere OS Virtuelle til premium-lager og vedhæfte Premium lagerplads virtuelle harddiske. Du kan også lægge OS cd'en med en konto med Standard-lager og derefter vedhæfte virtuelle harddiske, der er placeret på en Premium lagerplads konto. Begge scenarier er vist.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Trin 1: Oprette en Premium lagerplads konto


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Trin 2: Oprette en ny tjeneste i skyen

    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Trin 3: Reserve en skybaseret tjeneste VIP (valgfrit)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Trin 4: Oprette en VM objektbeholder
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Trin 5: Placere OS Virtuelle på Standard eller Premium lagerplads
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Trin 6: Oprette VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Oprette en ny VM for at kunne bruge Premium-lager med et brugerdefineret billede

Dette scenarie viser, hvor du har eksisterende brugerdefinerede billeder, der er placeret på en Standard-lager-konto. Som nævnt, hvis du vil placere den virtuelle harddisk OS på Premium lagerplads skal du kopiere det billede, der findes i kontoen Standard lagerplads og overføre dem til en Premium-lager, før den kan bruges. Hvis du har et billede på en lokal, kan du også bruge denne metode til at kopiere, direkte til kontoen Premium lagerplads.

#### <a name="step-1-create-storage-account"></a>Trin 1: Oprette lagerplads konto
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Trin 2 Opret skybaseret tjeneste
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Trin 3: Brug eksisterende afbildning
Du kan bruge en eksisterende afbildning. Eller du kan [tage et billede af en eksisterende computer](virtual-machines-windows-classic-capture-image.md). Bemærk, at den maskine, billede du ikke behøver at være DS* computer. Når du har billedet, vejledningen nedenfor viser, hvordan at kopiere den til Premium lagerplads konto med den * *Start AzureStorageBlobCopy** PowerShell cmdlet accepterer.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Trin 4: Kopiér Blob mellem lagerplads konti
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Trin 5: Kig regelmæssigt Kopiér status:
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Trin 6: Tilføje billede disk til Azure disk lager i abonnement
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [AZURE.NOTE] Du kan finde, selvom statusrapporter som succes, du kan stadig få en bortlease fejl. I dette tilfælde skal du vente 10 minutter.

#### <a name="step-7--build-the-vm"></a>Trin 7: Opbygge VM
Her bygger du VM fra dit billede og vedhæfte to lagerplads virtuelle harddiske Premium:

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Eksisterende installationer, der ikke bruger altid på tilgængelighed-grupper

> [AZURE.NOTE] Eksisterende installationer først se afsnittet [forudsætninger](#prerequisites-for-premium-storage) i dette emne.

Der findes forskellige overvejelser i forbindelse med SQL Server-installationer, der ikke bruger altid på tilgængelighed-grupper og de, der gør. Hvis du ikke bruger altid på og har en eksisterende enkeltstående SQL Server, kan du opgradere til Premium-lager ved hjælp af en ny cloud-tjenesten og lager konto. Overvej følgende indstillinger:

- **Opret en ny SQL Server VM**. Du kan oprette en ny SQL Server VM, der bruger en Premium lagerplads-konto, som beskrevet i nye installationer. Derefter sikkerhedskopiere og gendanne dine SQL Server-konfiguration og bruger databaser. Programmet skal opdateres, hvis du vil indsætte den nye SQL Server, hvis den har adgang til internt eller eksternt. Du har brug at kopiere alle 'af db' objekter, som om du var i gang en Side om Side (SxS) SQL Server-migrering. Dette omfatter objekter som logon, certifikater og sammenkædede servere.
- **Overføre en eksisterende SQL Server VM**. Dette kræver, at den SQL Server VM offline, og derefter overføre den til en ny skybaseret tjeneste, som indeholder alle dens vedhæftede virtuelle harddiske kopieres til kontoen Premium lagerplads. Når VM er online, henviser programmet værtsnavn server som før. Vær opmærksom på, at størrelsen på den eksisterende disk vil påvirke ydeevnen egenskaber. For eksempel bliver en 400 GB disk rundet op til en r20 =. Hvis du ved, at du ikke kræver pågældende disk ydeevne, så du kan genskabe VM som en DS serie VM, og vedhæft Premium lagerplads virtuelle harddiske af specifikationen størrelse og ydeevne, du har brug for. Derefter kan du fjerne og Tilslut SQL DB filer.

> [AZURE.NOTE] Når kopiere de Virtuelle diske, skal du være opmærksom på størrelsen, afhængigt af størrelsen vil betyde, hvilken type Premium lagerplads Disk de falder inden for, dette bestemmer disk ydeevne specifikation. Azure afrunder op til nærmeste disken størrelse, så hvis du har en disk 400 GB, dette vil blive rundet op til en r20 =. Afhængigt af dine eksisterende EY krav på den OS virtuelle harddisk nødvendigvis du ikke til at overføre det til en Premium lagerplads konto.

Hvis din SQL Server er adgang til eksternt, ændres skyen service VIP. Du får også at opdatere slutpunkter, ACLs og DNS-indstillinger.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Eksisterende installationer, der bruger altid på tilgængelighed-grupper

> [AZURE.NOTE] Eksisterende installationer først se afsnittet [forudsætninger](#prerequisites-for-premium-storage) i dette emne.

I dette afsnit vil vi først se på hvordan arbejde sammen med Azure netværk altid på. Vi vil Opdel overførsler i til to scenarier derefter: overførsler, hvor nogle nedetid kan accepteres og overførsler, hvor du skal nå minimale nedetid.

Lokal SQL Server altid på tilgængelighed grupper kan bruge en lytteren i det lokale miljø, der registrerer et virtuelt DNS-navn sammen med en IP-adresse, som er delt mellem en eller flere SQL-servere. Når klienter opretter forbindelse dirigeres de gennem lytteren IP til den primære SQL Server. Dette er den server, der ejer altid på IP-ressourcen på det pågældende tidspunkt.

![DeploymentsUseAlways på][6]

I Microsoft Azure, får du kun én IP-adresse, der er tildelt til en NIC på VM, så anvender for at opnå det samme lag i fremstilling som lokalt, Azure den IP-adresse, der er tildelt intern/ekstern Indlæs balancere (ILB/ELB). Den IP-ressource, der er delt mellem servere er indstillet til den samme IP-som ILB/ELB. Dette er blevet publiceret i DNS, og klienttrafik er passeret ILB/ELB til den primære SQL Server-replika. ILB/ELB kender som SQL Server er primære, da sonder bruges til at sende forespørgsler altid på IP-ressourcen. Det sonder hver node, der indeholder et slutpunkt, der refereres til af ELB/ILB i det forrige eksempel, afhængigt af hvad der reagerer er den primære SQL Server.

> [AZURE.NOTE] ILB og ELB tildeles alle til en bestemt Azure skybaseret tjeneste, og derfor enhver skyen migrering i Azure højst sandsynligt betyder, at indlæse belastningsjusteringstjenesten IP vil ændre.

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Overføre altid på installationer, der kan tillade, at nogle nedetid

Der er to strategier til at overføre altid på installationer, som giver mulighed for nogle nedetid:

1. **Føje flere sekundære replikaer til en eksisterende altid på klynge**
1. **Overføre til en ny altid på klynge**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. føje flere sekundære replikaer til en eksisterende altid på klynge

En strategi er at tilføje flere secondaries til altid på tilgængelighed gruppen. Du skal tilføje nøgleordene i en ny tjeneste i skyen og opdatere lytteren med nye Indlæs belastningsjusteringstjenesten IP.

##### <a name="points-of-downtime"></a>Points nedetid:

- Validering af.
- Test altid på failover for nye Secondaries.

Hvis du bruger Windows lagerplads grupper i VM til højere EY overførselshastighed, derefter føres disse offline under en fuld klynge validering. Valideringstest er påkrævet, når du tilføjer noder til klyngen. Den tid, det tager for at køre en test kan variere, så skal du teste i din repræsentant testmiljø for at få en tilnærmet klokkeslættet for hvor lang tid det tager.

Du skal klargøre klokkeslæt, hvor du kan udføre manuel failover og kaos test på de nyligt tilføjede noder til at sikre altid på høj tilgængelighed fungerer som forventet.

![DeploymentUseAlways On2][7]

> [AZURE.NOTE] Du skal stoppe alle forekomster af SQL Server, hvor lagerplads grupper bruges før validering kørt.
##### <a name="high-level-steps"></a>Overordnede trin

1. Oprette to nye SQL-servere i nye skytjeneste med vedhæftede Premium lagerplads.
1. Kopiere over fuld sikkerhedskopier og Gendan med **ingen genoprettelse**.
1. Kopiere over 'af bruger DB' afhængige objekter, som logon osv.
1. Opret et nyt en ny interne Indlæs belastningsjusteringstjenesten (ILB) eller brug en ekstern Indlæs belastningsjusteringstjenesten (ELB), og derefter konfigurere Indlæs afstemmes slutpunkter på begge nye noder.
> [AZURE.NOTE] Kontrollere alle noder har den korrekte slutpunktskonfiguration, før du fortsætter

1. Stoppe bruger/Application adgang til SQL Server (hvis ved hjælp af lagerplads grupper).
1. Stop SQL Server-program Services på alle noder (hvis ved hjælp af lagerplads grupper).
1. Tilføje nye noder for at samle og køre fuld validering.
1. Når validering er gået igennem, start alle SQL Server-tjenester.
1. Sikkerhedskopiér transaktionslog og gendanne brugerdatabaser.
1. Tilføje nye noder til altid på tilgængelighed gruppen, og Placer gentagelse til **synkron**.
1. Tilføj IP-adresseressource af den nye skyen Service ILB/ELB via PowerShell for altid på baseret på flere websted eksemplet i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage). I Windows indstille klynge, **mulige ejere** på ressourcen, **IP-adresse** til noderne nye gamle. I afsnittet 'Tilføje IP-adresseressource på samme undernet' i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
1. Failover til en af de nye noder.
1. Kontrollér de nye noder automatisk Failover partnere og test failover.
1. Fjerne oprindelige knuder fra tilgængelighed gruppe.

##### <a name="advantages"></a>Fordele

- Nye SQL-servere kan være testes (SQL Server og program), før de er føjet til altid på.
- Du kan ændre VM størrelse og tilpasse lagerplads præcist til virksomhedens behov. Det vil dog være en fordel at bevare alle SQL filstier det samme.
- Du kan styre, hvor overførslen af DB sikkerhedskopier til sekundær replikaerne er startet. Dette er forskellig fra bruger cmdlet accepterer Azure **Start AzureStorageBlobCopy** til at kopiere virtuelle harddiske, fordi det er en asynkron kopi.

##### <a name="disadvantages"></a>Ulemper
- Når du bruger Windows lagerplads grupper, er der klynge nedetid under fuld klynge validering af nye yderligere noder.
- Afhængigt af den Version af SQL Server og det eksisterende antal sekundær replikaer, kan du ikke kunne tilføje flere sekundære replikaer uden at fjerne eksisterende secondaries.
- Der kan være lang tid på filoverførsel SQL under konfiguration af secondaries.
- Der er flere omkostninger under overflytningen, mens du har nye maskiner, der kører parallelt.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. overføre til en ny altid på klynge

En anden strategi er at oprette en helt ny altid på klynge med helt nyt noder i nye skytjeneste og derefter omdirigere klienter for at bruge den.

##### <a name="points-of-downtime"></a>Punkter nedetid

Der er nedetid, når du overfører programmer og brugere til den nye altid på lytteren. Nedetiden afhænger:

- Den tid, det tager at gendanne endelige transaction logsikkerhedskopier til databaser på nye servere.
- Den tid, det tager at opdatere klientprogrammer bruge nye altid på lytteren.

##### <a name="advantages"></a>Fordele

- Du kan teste de faktiske produktionsmiljø, SQL Server, og OS opbygge ændringer.
- Har du mulighed for at tilpasse opbevaring og potentielt reducere størrelsen på VM. Dette kan medføre omkostninger reduktion.
- Du kan opdatere din version eller SQL Server build under denne proces. Du kan også opgradere operativsystemet.
- Den forrige altid på klynge kan fungere som et dækkende rollback mål.

##### <a name="disadvantages"></a>Ulemper

- Du skal ændre lytteren DNS-navn, hvis du vil have begge klynger, kører samtidigt altid på. Dette tilføjer administration spild under overførslen, som klienten programmet strenge skal afspejle det nye lytter navn.
- Du skal implementere en synkroniseringsfunktion mellem de to miljøer til at holde dem så tæt som muligt for at minimere endelige synkronisering kravene før overførslen.
- Der er tilføjet omkostninger under overflytningen, mens du har det nye miljø, der kører.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Overføre altid på installationer for minimale nedetid

Der er to strategier for at overføre altid på installationer af minimale nedetid:

1. **Bruge en eksisterende sekundær: enkelt websted**
1. **Anvende eksisterende sekundær Replica(s): flere websted**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. anvender en eksisterende sekundær: enkelt websted

Der er en strategi for minimale nedetid til at tage en eksisterende skyen sekundær og fjerne det fra den aktuelle skybaseret tjeneste. Derefter kopiere de virtuelle harddiske til den nye konto Premium lagerplads, og oprette VM i den nye tjeneste i skyen. Derefter opdatere lytteren i klynge og belastningsjustering.

##### <a name="points-of-downtime"></a>Punkter nedetid

- Der er nedetid, når du opdaterer den endelige node med slutpunktet Indlæs afstemmes.
- Din klient Genoprettelse kan forsinkes afhængigt af din klient/DNS-konfiguration.
- Der er flere nedetid, hvis du vælger at udføre gruppen altid på klynge offline for at udskifte IP-adresser. Du kan undgå dette ved hjælp af en eller afhængighed og mulige ejere for den tilføjede ressource IP-adresse. I afsnittet 'Tilføje IP-adresseressource på samme undernet' i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

> [AZURE.NOTE] Når du vil de tilføjede knude til partake i som altid på Failover-Partner, skal du tilføje et Azure slutpunkt med en reference til det Indlæs afstemmes sæt. Når du kører kommandoen **Tilføj AzureEndpoint** du gør dette, kan aktuelle forbindelser skal være åben, men nye forbindelser til lytteren kan ikke oprettes, før justering af belastning er opdateret. Afprøve dette blev set til sidste 90-120seconds, det skal bruges til test.

##### <a name="advantages"></a>Fordele

- Uden ekstra omkostninger, der er påløbet under overflytningen.
- En en til en overførsel.
- Reduceret kompleksitet.
- Giver mulighed for øget IOP'ER fra Premium lagerplads lagerenheder. Når de er fjernet fra VM og kopieret til den nye tjeneste i skyen, der er en 3 deltager kan værktøj bruges til at øge størrelsen Virtuelle, som indeholder højere gennemløb. Se denne [forum diskussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)for at øge Virtuelle størrelser.

##### <a name="disadvantages"></a>Ulemper

- Er der et midlertidige tab af HA og DR under overførsel.
- Som det er en 1:1 migrering, er du nødt til at bruge en VM-minimumstørrelse, der understøtter din antallet af virtuelle harddiske, så du kan ikke muligvis downsize din FOS.
- Dette scenarie skal bruge cmdlet accepterer Azure **Start AzureStorageBlobCopy** , som er asynkron. Der er ingen SLA på Kopiér fuldførelse. Tidspunktet, hvor kopierne varierer, mens dette afhænger af Vent i kø det også, afhænger af mængden data til at overføre. Kopiér klokkeslættet øges, hvis overførslen gå til en anden Azure datacenter, der understøtter Premium lagring i et andet område. Hvis du blot har 2 noder, kan du overveje en mulige afhjælpning, i tilfælde af kopien tager længere tid end i test. Dette kan omfatte følgende ideer.
    - Tilføje en midlertidig 3 SQL Server-node for HA før overførslen med godkendte nedetid.
    - Køre overførslen uden for Azure planlagt vedligeholdelse.
    - Sikre, at du har konfigureret din klynge quorum korrekt.  

##### <a name="high-level-steps"></a>Overordnede trin

Dette dokument viser ikke en komplet start til slut eksempel, men [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) indeholder oplysninger, der kan bruges til at udføre dette.

![MinimalDowntime][8]

- Indsamle konfigurationen af disk, og fjern noden (Slet ikke vedhæftede virtuelle harddiske).
- Oprette en Premium lagerplads konto og kopiere virtuelle harddiske fra kontoen Standard lagerplads
- Oprette nye skybaseret tjeneste, og geninstaller SQL2 VM i pågældende skybaseret tjeneste. Oprette VM ved hjælp af den kopierede oprindelige OS virtuelle harddisk og vedhæfte de kopierede virtuelle harddiske.
- Konfigurere ILB / ELB og tilføje slutpunkter.
- Opdatere lytteren ved enten:
    - Tage altid på gruppen offline og opdaterer altid på lytteren med nye ILB / ELB IP-adresse.
    - Eller føje IP-adresse ressourcen af nye skyen Service ILB/ELB via PowerShell i Windows klynge. Derefter angive de mulige ejere på ressourcen, IP-adresse til noden overførte, SQL2, og angive dette som eller afhængighed i Network Name. I afsnittet 'Tilføje IP-adresseressource på samme undernet' i [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).
- Se DNS-konfiguration/overførselsopgaver til klienter.
- Overføre SQL1 VM, og gå til trin 2-4.
- Hvis ved hjælp af trinnene 5ii, skal du tilføje SQL1 som ejer af et muligt for den tilføjede IP-adresseressource
- Teste failover.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. udnytte eksisterende sekundær replica(s): flere websted

Hvis du har noder i mere end ét Azure datacenter (DC), eller hvis du har et hybridmiljø, kan du bruge en altid på konfiguration i dette miljø for at minimere nedetid.

Den metode egner sig til at ændre altid på synkroniseringen til synkron til lokale eller sekundær Azure DC og derefter failover over på den SQL Server. Kopiere de virtuelle harddiske til en Premium lagerplads konto og derefter geninstallere maskinen til en ny tjeneste i skyen. Opdater lytteren, og derefter mislykkes tilbage.

##### <a name="points-of-downtime"></a>Punkter nedetid

Nedetiden består af klokkeslættet Sådan flytter du til alternative DC og tilbage. Det afhænger også klient/DNS-konfigurationen og din klient Genoprettelse kan udskydes.
Overvej følgende eksempel på en hybrid altid på konfiguration:

![MultiSite1][9]

##### <a name="advantages"></a>Fordele

- Du kan bruge eksisterende infrastruktur.
- Du har mulighed for at før opgradering Azure opbevaring på DR Azure DC først.
- DR Azure DC lagerplads kan konfigureres.
- Der er mindst to failover under overflytningen, undtagen test failover.
- Du behøver ikke at flytte SQL Server-data med sikkerhedskopiering og gendannelse.

##### <a name="disadvantages"></a>Ulemper

- Afhængigt af klientens adgang til SQL Server, kan der være øget ventetid når SQL Server kører i en alternativ DC til programmet.
- Kopiér tidspunktet for virtuelle harddiske til Premium-lager kan være lang. Dette kan påvirke din beslutning om at holde noden i gruppen tilgængelighed. Overvej dette for, hvornår log intensivt arbejde indlæse kører under overførslen er påkrævet, da den primære node nødt til at holde unreplicated transaktioner i dets transaktionslog. Derfor kan dette vokse betydeligt.
- Dette scenarie skal bruge cmdlet accepterer Azure **Start AzureStorageBlobCopy** , som er asynkron. Der er ingen SLA på fuldførelse. Tidspunktet, hvor kopierne varierer, mens dette afhænger af Vent i kø, det også, afhænger af mængden data til at overføre. Derfor du har blot en node i dit 2. datacenter, skal du tage afhjælpning sikkerhedsforanstaltninger i tilfælde af kopien tager længere tid end i test. Dette kan omfatte følgende ideer.
    - Tilføje en midlertidig 2. SQL node for HA før overførslen med godkendte nedetid.
    - Køre overførslen uden for Azure planlagt vedligeholdelse.
    - Sikre, at du har konfigureret din klynge quorum korrekt.

Dette scenarie antager, at du har dokumenteret din installation og ved, hvordan opbevaring er tilknyttet for at foretage ændringer til optimal disk cacheindstillinger.

##### <a name="high-level-steps"></a>Overordnede trin
![Multisite2][10]

- Foretag lokale / alternative Azure DC SQL Server primære, og gøre det på andre automatisk Failover Partner (AFP).
- Indsamle oplysninger om konfiguration fra SQL2, og fjern noden (Slet ikke vedhæftede virtuelle harddiske).
- Oprette en Premium lagerplads-konto, og Kopiér virtuelle harddiske fra kontoen Standard lagerplads.
- Oprette en ny tjeneste i skyen og SQL2 VM med dens knyttet tillæg lagerplads diske.
- Konfigurere ILB / ELB og tilføje slutpunkter.
- Opdatere altid på lytteren med nye ILB / ELB IP-adresse og test failover.
- Se DNS-konfigurationen.
- Ændre AFP til SQL2, og overføre SQL1 og gå til trin 2 – 5.
- Teste failover.
- Skifte AFP tilbage til SQL1 og SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Tillæg: Overføre en flere lokationer altid på klynge til Premium-lager

Resten af dette emne indeholder et detaljeret eksempel til at konvertere en med flere websted altid på klynge til Premium-lager. Det også konverterer lytteren fra ved hjælp af en ekstern justering af belastning (ELB) til en intern justering af belastning (ILB).

### <a name="environment"></a>Miljø

- Windows 2k 12 / SQL 2k 12
- 1 DB filer på SP
- 2 x lagerplads grupper per Node

![Appendix1][11]

### <a name="vm"></a>VM:

I dette eksempel vi til at vise, flyttes fra en ELB til ILB. ELB var tilgængelig før ILB, så det viser hvordan du skifter til denne under overførslen.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Før trinnene: Oprette forbindelse til abonnement

    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Trin 1: Oprette ny lagerplads konto og tjeneste i skyen
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Trin 2: Øge de tilladte mislykkede forsøg på ressourcer<Optional>
På bestemte ressourcer, der hører til din altid på tilgængelighed gruppe er der begrænsninger på antallet af mislykkede forsøg, der kan forekomme i en periode, hvor klyngetjenesten vil forsøge at genstarte ressourcegruppen. Det anbefales, at du øge dette, mens du gennemgang af denne procedure, og siden Hvis du ikke manuelt failover og udløser failover ved at lukke maskiner, kan du få tæt denne grænse.

Det ville være forsigtig at fordoble tilladelsen fejl ved at gøre dette i failoverklyngestyring, gå til egenskaberne for ressourcegruppe altid på:

![Appendix3][13]

Ændre de maksimale mislykkede til 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Trin 3: Tilføjelse IP-adresse ressourcen for klynge gruppe<Optional>

Hvis du har kun én IP-adresse til gruppen klynge og dette er justeret til skyen undernet, Vær opmærksom på, hvis du ved et uheld tager offline alle klyngenoder i skyen på netværket derefter klynge IP-ressourcen og klynge netværksnavn ikke er online. I tilfælde af dette forhindrer værktøjet opdateringer til andre klyngeressourcer.

#### <a name="step-4-dns-configuration"></a>Trin 4: DNS-konfiguration

For at implementere en blød overgang afhænger af, hvordan DNS der anvendes og opdateret.
Når altid er installeret, oprettes der en Windows-klynge ressourcegruppe, hvis du åbner failoverklyngestyring, får du vist, at den skal som minimum have tre ressourcer, de to, som dokumentet refererer til:

- Virtuel Network Name (VNN) – dette er DNS-navn, der klienten oprette forbindelse til når du vil oprette forbindelse til SQL-servere via altid på.
- IP-adresseressource – dette er den IP-adresse, der er knyttet til VNN, kan du har mere end én eller i en af flere lokationer konfiguration har du en IP-adresse per websted/undernet.

Når du opretter forbindelse til SQL Server, SQL Server Client driver hentes de DNS-poster, der er knyttet til lytteren og forsøger at oprette forbindelse til hver altid på tilknyttede IP-adresse, gennemgås under nogle faktorer, der kan påvirke dette.

Antallet af samtidige DNS-poster, der er knyttet til lytteren navnet afhænger ikke kun antallet af IP-adresser, der er knyttet til, men den ' RegisterAllIpProviders'setting i Failoverklyngedannelse for ressourcen altid på VNN.

Når du installerer altid på i Azure der er forskellige trin til at oprette lytteren og IP-adresser, du skal konfigurere manuelt 'RegisterAllIpProviders' 1, dette er forskellige til en til en lokal installation altid på, hvor det allerede er angivet til 1.

Hvis 'RegisterAllIpProviders' er 0, så du kun vil se en DNS-posten i DNS, der er knyttet til lytteren:

![Appendix4][14]

Hvis 'RegisterAllIpProviders' er 1:

![Appendix5][15]

Nedenstående kode skal lagring af indstillingerne for VNN og angive den for dig, Bemærk, før ændringen træder i kraft, skal du gøre VNN offline og slå det tilbage online denne tager det lytteren offline forårsager klient connectivity afbrydelser.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

I en nyere overførselstrinnet skal du opdatere lytteren altid på med en opdateret IP-adresse, der skal referere til en belastningsjustering dette omfatter en IP-adresse ressource fjernelse og tilføjelse. Når IP-opdateringen skal du sikre, at den nye IP-adresse er blevet opdateret i DNS-Zone, og klienterne, der er ved at opdatere deres lokale DNS-cache.

Hvis dine kunder, der er placeret i et andet netværkssegment og henviser til en anden DNS-server, skal du overveje, hvad sker der, om DNS-Zone overførsel under overførslen som programmet forbinde tidspunkt vil være begrænset af mindst Zone overføre tidspunktet for alle nye IP-adresser for lytteren. Hvis du er under tidsbegrænsning her, skal du diskutere og teste at tvinge en trinvis zoneoverførsel med din Windows-grupper og også placerer DNS-værtspost til en lavere tid til Live (TTL), så klienterne opdatere. Se [Trinvise zoneoverførsler](https://technet.microsoft.com/library/cc958973.aspx) og [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx)kan finde flere oplysninger.

Som standard TTL for DNS-post, der er knyttet til lytteren altid på i Azure er 1200 sekunder. Du kan eventuelt at reducere dette, hvis du er under gang begrænsning under din migrering til at sikre, at klienterne, der opdaterer deres DNS med den opdaterede IP-adresse for lytteren. Du kan se og redigere konfigurationen af dumping af konfigurationen af VNN:

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Vær opmærksom på, jo lavere på 'HostRecordTTL', opstår der en højere mængde DNS-trafik.

##### <a name="client-application-settings"></a>Programmet klientindstillinger

Hvis din SQL-klientprogrammet understøtter .net 4.5 SQLClient, og du kan bruge ' MULTISUBNETFAILOVER = TRUE' nøgleord, det anbefales at anvendes som det giver mulighed for hurtigere forbindelse til SQL altid på tilgængelighed gruppe under failover. IT-optælles gennem alle IP-adresser, der er knyttet til lytteren altid på parallelt og udfører en mere tilstanden TCP forsøg forbindelseshastighed under en failover.

Få flere oplysninger om indstillingerne ovenfor, skal du se [MultiSubnetFailover nøgleord og funktioner, der er knyttet](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Se også [SqlClient understøttelse af høj tilgængelighed, nedbrud](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Trin 5: Klynge quorum indstillinger

Når du går at finde ud af mindst én SQL Server ned ad gangen, skal du ændre indstillingen klynge quorum, hvis bruger fil del bekræftelse (FSW) med 2 noder, skal du indstille quorum at tillade node størstedelen og bruge dynamiske afstemning, og dette er giver mulighed for en enkelt node skal være stående.


    Set-ClusterQuorum -NodeMajority  

Du kan finde flere oplysninger om administration af og konfiguration af klynge quorum, skal du se [konfigurere og administrere Quorum i en Windows Server 2012 sekundær klynge](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Trin 6: Udtrække eksisterende slutpunkter og ACLs
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Gemme ændringerne i en tekstfil.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Trin 7: Ændre Failover partnere og gentagelse tilstande

Hvis du har mere end 2 SQL-servere, skal du ændre sekundære af en anden sekundær lokal eller i en anden DC til 'Synkron' og gøre det en automatisk Failover Partner (AFP), er så du vedligeholde HA, mens du foretager ændringer. Du kan gøre dette gennem TSQL af ændre via SSMS:

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Trin 8: Fjerne sekundære VM fra en skytjeneste

Du skal planlægger at overføre en sekundær node skyen først, hvis det er i øjeblikket primære, skal du starte en manuel failover.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Trin 9: Ændre disk cachelagring af indstillinger i CSV-fil og gemme

Disse skal være angivet til SKRIVEBESKYTTET for datamængder.

For TLOG enheder disse skal du vælge ingen.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Trin 10: Kopiér virtuelle harddiske
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Du kan kontrollere den kopi af de virtuelle harddiske til kontoen Premium lagerplads:

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Vent, indtil alle dette er registreret som succes.

Oplysninger til individuelle BLOB:

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Trin 11: Registrer OS disk

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Trin 12: Importere sekundær til nye skytjeneste

Nedenstående kode også bruger indstillingen tilføjede her kan du importere maskinen og bruge retainable VIP.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Trin 13: Oprette ILB på nye skyen Svc, tilføje Indlæs afstemmes slutpunkter og ACLs
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####<a name="step-14-update-always-on"></a>Trin 14: Opdatere altid på
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Nu fjerne gamle skytjenesten IP-adresse.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Trin 15: Søgning efter DNS opdateringer

Nu skal du kontrollere DNS-servere på dine SQL Server-klienten netværk og kontrollere, at klynge har føjet ekstra host posten for den tilføjede IP-adresse. Hvis disse DNS-servere ikke er opdateret, kan du overveje at tvinge en DNS-Zone overførsel og sikre, at klienter i der undernet er i stand til at løse til begge altid på IP-adresser, dette er, så du ikke behøver at vente automatisk DNS gentagelse.

#### <a name="step-16-reconfigure-always-on"></a>Trin 16: Omkonfigurere altid på

På dette tidspunkt vente du på sekundært pågældende node, der blev overført for at synkronisere fuldt ud med den lokal node og skifte til synkron gentagelse node og gøre det på AFP.  

#### <a name="step-17-migrate-second-node"></a>Trin 17: Overføre anden knude
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Trin 18: Ændre disk cachelagring af indstillinger i CSV-fil og gemme

Disse skal være angivet til SKRIVEBESKYTTET for datamængder.

For TLOG enheder disse skal du vælge ingen.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Trin 19: Oprette ny uafhængige lagerplads konto til sekundære Node
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Trin 20: Kopiér virtuelle harddiske
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Du kan kontrollere Virtuelle Kopiér status alle virtuelle: ForEach ($disk i $diskobjects) {$lun = $disk. LUN $vhdname = $disk.vhdname $cacheoption = $disk. HostCaching $disklabel = $disk. DiskLabel $diskName = $disk. DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Vent, indtil alle dette er registreret som succes.

Få at vide om individuelle BLOB: #Check induvidual blob status Get-AzureStorageBlobCopyState-Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd"-objektbeholder $containerName-kontekst $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Trin 21: Registrer OS disk
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Trin 22: Føje Indlæs afstemmes slutpunkter og ACLs
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Trin 23: Test failover

Du bør nu give noden overførte synkronisere med den lokal altid på knude, placere det i til synkron gentagelse tilstand og vente, indtil den er synkroniseret. Derefter overflyttet failover fra lokalt til den første node, som er AFP. Når, der har arbejdet, kan du ændre den sidste overførte knude til AFP.

Du bør teste failover mellem alle noder og køre selvom kaos tests for at sikre failover arbejde som forventet, og i en tiden manor.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Trin 24: Placere tilbage klynge quorum indstillinger / DNS TTL / Failover Pntrs / synkroniseringsindstillinger
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Tilføje IP-adresseressource på samme undernet

Hvis du har kun 2 SQL-servere og ønsker at overføre dem til en ny tjeneste i skyen, men vil beholde dem på samme undernet, kan du undgå at tage lytteren offline til at slette den oprindelige altid på IP-adresse og tilføje den nye IP-adresse. Hvis du vil overføre FOS til en anden undernet skal du ikke gør dette, som der vil være et ekstra klyngenetværk, der skal referere til pågældende undernet.

Når du har ajourføres overførte sekundær og tilføjet i den nye ressource IP-adressen til den nye tjeneste i skyen før failover den eksisterende primære, skal du udføre følgende trin i klynge Failover Manager:

For at tilføje i IP-adresse skal du se [tillæg](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), trin 14.

1. Ændre mulige ejeren til 'Eksisterende primære SQL Server' i eksemplet nedenfor, 'dansqlams4' for den aktuelle IP-adresse ressource:

    ![Appendix13][23]

1. Ændre ejeren af mulige til 'Migrated sekundær SQL Server' i eksemplet nedenfor, 'dansqlams5' for den nye IP-adresse ressource:

    ![Appendix14][24]

1. Når denne indstilling er angivet du kan skifte, og når den sidste node overføres mulige ejere skal redigeres, så denne node er tilføjet som ejer af et mulige:

    ![Appendix15][25]

## <a name="additional-resources"></a>Yderligere ressourcer
- [Azure Premium lagerplads](../storage/storage-premium-storage.md)
- [Virtuelle maskiner](https://azure.microsoft.com/services/virtual-machines/)
- [SQL Server på Azure virtuelle computere](virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
