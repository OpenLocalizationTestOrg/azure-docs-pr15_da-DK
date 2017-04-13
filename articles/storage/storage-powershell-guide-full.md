<properties
    pageTitle="Brug af Azure PowerShell med Azure lagerplads | Microsoft Azure"
    description="Lær, hvordan du bruger Azure PowerShell-cmdlet'er til Azure-lager til at oprette og administrere lagerplads konti; arbejde med blob, tabeller, køer og filer. konfigurere og forespørgsel lagerplads analyser og oprette delt adgang signaturer."
    services="storage"
    documentationCenter="na"
    authors="robinsh"
    manager="carmonm"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="using-azure-powershell-with-azure-storage"></a>Brug af Azure PowerShell med Azure-lager

## <a name="overview"></a>Oversigt

Azure PowerShell er et modul, der indeholder cmdletter for at administrere Azure via Windows PowerShell. Det er en opgavebaseret kommandolinje-shell og scriptsproget udviklet specielt til systemadministration af. Med PowerShell, er det nemt at styre og automatisere administrationen af Azure tjenester og -programmer. Du kan for eksempel bruge cmdlet'erne til at udføre de samme opgaver, som du kan udføre via [Azure-portalen](https://portal.azure.com).

I denne vejledning, ser vi nærmere Sådan bruges [Azure lagerplads cmdlet'er](https://msdn.microsoft.com/library/azure/mt269418.aspx) til at udføre en række udvikling og administration af opgaver med Azure-lager.

Denne vejledning forudsætter, at du har tidligere oplevelse ved hjælp af [Azure-lager](https://azure.microsoft.com/documentation/services/storage/) og [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). Guiden giver et antal scripts til at vise brugen af PowerShell til Azure-lager. Du skal opdatere scriptvariablerne, der er baseret på din konfiguration før du kører hvert script.

Den første sektion i denne vejledning giver et hurtigt overblik Azure-lager og PowerShell. Detaljerede oplysninger og vejledninger, starte fra de [nødvendige for at Azure PowerShell med Azure-lager](#prerequisites-for-using-azure-powershell-with-azure-storage).


## <a name="getting-started-with-azure-storage-and-powershell-in-5-minutes"></a>Introduktion til Azure-lager og PowerShell i 5 minutter

Dette afsnit viser, hvordan du få adgang til Azure-lager via PowerShell i fem minutter.

**Ny bruger af Azure:** Få en Microsoft Azure-abonnement og en Microsoft-konto, der er knyttet til dette abonnement. Se [Gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/), [Købsmuligheder](https://azure.microsoft.com/pricing/purchase-options/)og [Medlem indeholder](https://azure.microsoft.com/pricing/member-offers/) oplysninger om Azure Købsmuligheder (for medlemmer af MSDN, Microsoft Partner Network og BizSpark og andre Microsoft-programmer).

Du kan finde flere oplysninger om Azure abonnementer i [tildele administratorroller i Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Når du har oprettet en Microsoft Azure-abonnement og konto:**

1.  Hent og Installer [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.  Start Windows PowerShell integreret Scripting miljø (ISE): I din lokale computer skal du gå til menuen **Start** . Skriv **Administration** og klikke på for at køre den. I vinduet **Administration** skal du højreklikke på **Windows PowerShell ISE**skal du klikke på **Kør som Administrator**.
3.  Klik på **filer**i **Windows PowerShell ISE** > **Ny** for at oprette en ny scriptfil.
4.  Nu, får du et simpelt script, der viser grundlæggende PowerShell-kommandoer til at få adgang til Azure-lager. Scriptet skal du bede din Azure konto logonoplysninger for at tilføje din Azure-konto i det lokale miljø PowerShell. Derefter kan scriptet Angiver standardsproget til Azure-abonnement, og oprette en ny firmapost lagerplads i Azure. Derefter scriptet vil oprette en ny objektbeholder i denne nye lagerplads-konto, og Overfør en eksisterende billedfil (blob) til objektbeholderen. Når scriptet viser en liste over alle BLOB i objektbeholderen, oprettes en ny destinationsmappe i din lokale computer, og der hente billedfilen.
5.  Vælg script mellem bemærkninger i kodeafsnittet følgende **#begin** og **#end**. Tryk på CTRL + C for at kopiere den til Udklipsholder.

        #begin
        # Update with the name of your subscription.
        $SubscriptionName = "YourSubscriptionName"

        # Give a name to your new storage account. It must be lowercase!
        $StorageAccountName = "yourstorageaccountname"

        # Choose "West US" as an example.
        $Location = "West US"

        # Give a name to your new container.
        $ContainerName = "imagecontainer"

        # Have an image file and a source directory in your local computer.
        $ImageToUpload = "C:\Images\HelloWorld.png"

        # A destination directory in your local computer.
        $DestinationFolder = "C:\DownloadImages"

        # Add your Azure account to the local PowerShell environment.
        Add-AzureAccount

        # Set a default Azure subscription.
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

        # Create a new storage account.
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

        # Set a default storage account.
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

        # Create a new container.
        New-AzureStorageContainer -Name $ContainerName -Permission Off

        # Upload a blob into a container.
        Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

        # List all blobs in a container.
        Get-AzureStorageBlob -Container $ContainerName

        # Download blobs from the container:
        # Get a reference to a list of all blobs in a container.
        $blobs = Get-AzureStorageBlob -Container $ContainerName

        # Create the destination directory.
        New-Item -Path $DestinationFolder -ItemType Directory -Force  

        # Download blobs into the local destination directory.
        $blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
        #end

5.  Tryk på CTRL + V for at kopiere scriptet i **Windows PowerShell ISE**. Klik på **filer** > **Gem**. Skriv navnet på scriptfilen, såsom "mystoragescript" i dialogboksen **Gem som** . Klik på **Gem**.

6.  Nu skal du opdatere scriptvariablerne, der er baseret på dine konfigurationsindstillinger. Du skal opdatere variablen **$SubscriptionName** med dine egne abonnement. Du kan bevare de andre variabler som angivet i scriptet eller opdatere dem, som du ønsker.

    - **$SubscriptionName:** Du skal opdatere denne variabel med dit eget navn på abonnement. Følg et af følgende tre måder at finde navnet på dit abonnement:

        en. Klik på **filer**i **Windows PowerShell ISE** > **Ny** for at oprette en ny scriptfil. Kopiere følgende script til den nye scriptfil og klikke på **fejlfinding af** > **køre**. Følgende script skal du bede din Azure konto logonoplysninger for at tilføje din Azure konto i det lokale miljø PowerShell og derefter få vist alle de abonnementer, der er forbundet til den lokale PowerShell-session. Notere navnet på det abonnement, du vil bruge, mens du følge dette selvstudium:

            Add-AzureAccount
                Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName

        b. Klik på **abonnementer**for at finde og kopiere abonnementets navn på [Azure Portal](https://portal.azure.com)i menuen Hub i venstre side. Kopiere og indsætte navnet på abonnement, du vil bruge, mens du kører scripts i denne vejledning.

        ![Azure-portalen][Image2]

        c. Rul ned for at finde og kopiere abonnementets navn på [Klassisk Azure-portalen](https://manage.windowsazure.com/), og klik på **Indstillinger** i venstre side af portalen. Klik på **abonnementer** for at få vist en liste over dine abonnementer. Kopiere og indsætte navnet på abonnement, du vil bruge, mens du kører scripts, der er angivet i denne vejledning.

        ![Azure klassisk Portal][Image1]

    - **$StorageAccountName:** Brug det angivne navn i scriptet eller skriv et nyt navn til kontoen lagerplads. **Vigtige:** Navnet på lagerplads konto skal være entydigt i Azure. Det skal skrives med småt, for!

    - **$Location:** Bruge den angivne "Vest USA" i scriptet eller vælge andre Azure placeringer, som indtastning af østasiatiske USA, nord Europa, og så videre.

    - **$ContainerName:** Brug det angivne navn i scriptet eller skriv et nyt navn til din objektbeholder.

    - **$ImageToUpload:** Angiv en sti til et billede på din lokale computer, f.eks.: "C:\Images\HelloWorld.png".

    - **$DestinationFolder:** Angiv en sti til en lokal mappe til lagring af filer, der er hentet fra Azure-lager, f.eks.: "C:\DownloadImages".

7.  Klik på **filer**, når du har opdateret scriptvariabler i filen "mystoragescript.ps1", > **Gem**. Klik derefter på **fejlfinding af** > **køre** eller tryk på **F5** for at køre scriptet.  

Når du har kørt scriptet, bør du have en lokal destinationsmappe, der indeholder den hentede billedfil. Følgende skærmbillede viser et eksempel på output:

![Hente BLOB][Image3]


> [AZURE.NOTE] Afsnittet "Introduktion til Azure lager og PowerShell i 5 minutter" angivet en hurtig introduktion til, hvordan du bruger Azure PowerShell med Azure-lager. Detaljerede oplysninger og vejledninger opfordrer vi dig at læse de følgende afsnit.

## <a name="prerequisites-for-using-azure-powershell-with-azure-storage"></a>Forudsætninger for Azure PowerShell med Azure-lager
Du har brug for et Azure-abonnement og konto for at køre den PowerShell-cmdletter, der er givet i denne vejledning, som beskrevet ovenfor.

Azure PowerShell er et modul, der indeholder cmdletter for at administrere Azure via Windows PowerShell. Finde oplysninger om installation og konfiguration af Azure PowerShell, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md). Vi anbefaler, at hente og installere eller opgradere til det seneste Azure PowerShell-modulet før du bruger denne vejledning.

Du kan køre cmdlet'erne i standard Windows PowerShell-konsollen eller på Windows PowerShell ISE Integrated Scripting-miljø (). For eksempel for at åbne **Windows PowerShell ISE**, gå til menuen Start skal du skrive administration og klikke på at køre den. I vinduet administration skal du højreklikke på Windows PowerShell ISE skal du klikke på Kør som Administrator.

## <a name="how-to-manage-storage-accounts-in-azure"></a>Hvordan du administrerer lager konti i Azure

### <a name="how-to-set-a-default-azure-subscription"></a>Sådan angives standardindstillinger Azure-abonnement
Hvis du vil administrere Azure-lager, med Azure PowerShell, skal du godkende dit klientmiljø med Azure via Azure Active Directory-godkendelse eller certifikat-baseret godkendelse. Du kan finde detaljerede oplysninger om, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md) selvstudium. Denne vejledning anvender Azure Active Directory-godkendelse.

1.  Skriv følgende kommando for at tilføje din Azure i Windows PowerShell ISE konto i det lokale miljø PowerShell:

    `Add-AzureAccount`

2.  Skriv den mailadresse og adgangskode er knyttet til din konto i vinduet "Log i til Microsoft Azure". Azure godkender og gemmer legitimationsoplysninger for oplysningerne, og derefter lukker vinduet.

3.  Derefter skal du køre følgende kommando for at få vist de Azure konti i dit lokale miljø, PowerShell og bekræfte, at din konto er angivet:

    `Get-AzureAccount`

4.  Derefter køre følgende cmdlet for at få vist alle de abonnementer, der er forbundet til den lokale PowerShell-session, og Kontrollér, at dit abonnement står på listen:

    `Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.  Hvis du vil angive en standardindstilling Azure-abonnement, skal du køre Vælg AzureSubscription cmdlet:

        $SubscriptionName = 'Your subscription Name'
        Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.  Kontrollere navnet på standard-abonnementet ved at køre cmdlet'en Get-AzureSubscription til:

    `Get-AzureSubscription -Default`

7.  Hvis du vil se alle tilgængelige PowerShell-cmdlet'er til Azure-lager, skal du køre:

    `Get-Command -Module Azure -Noun *Storage*`

### <a name="how-to-create-a-new-azure-storage-account"></a>Sådan oprettes en ny konto Azure-lager
Hvis du vil bruge Azure lagerplads, skal du en lagerplads konto. Når du har konfigureret din computer for at oprette forbindelse til dit abonnement, kan du oprette en ny konto Azure-lager.

1.  Kør Get-AzureLocation cmdlet for at finde alle de tilgængelige datacenter placeringer:

    `Get-AzureLocation | Format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.  Dernæst skal køre ny AzureStorageAccount cmdlet for at oprette en ny firmapost lagerplads. I følgende eksempel oprettes en ny lagerplads konto i "Vest USA" datacenteret.

        $location = "West US"
        $StorageAccountName = "yourstorageaccount"
        New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] Navnet på din lagerplads konto skal være entydig i Azure og skal skrives med småt. Se [Om Azure lagerplads konti](storage-create-storage-account.md) og [Naming og henvisninger til beholdere, blob, og Metadata](http://msdn.microsoft.com/library/azure/dd135715.aspx)til navngivningskonventioner og begrænsninger.

### <a name="how-to-set-a-default-azure-storage-account"></a>Sådan angives en standardkonto Azure-lager
Du kan have flere lagerplads konti i dit abonnement. Du kan vælge en af dem og angive den som lager standardkontoen for alle storage-kommandoer i den samme PowerShell-session. Dette gør det muligt at køre kommandoerne Azure PowerShell lagerplads uden at angive konteksten lagerplads eksplicit.

1.  Hvis du vil angive en standardkonto lagerplads for dit abonnement, kan du køre cmdlet'en Set-AzureSubscription til.

        $SubscriptionName = "Your subscription name"
        $StorageAccountName = "yourstorageaccount"  
        Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.  Derefter skal du køre følgende Get-AzureSubscription cmdlet for at bekræfte, at kontoen lagerplads er knyttet til din standardkonto abonnement. Denne kommando returnerer egenskaber for abonnement på dit aktuelle abonnement, herunder dens aktuelle lagerplads-konto.

        Get-AzureSubscription –Current

### <a name="how-to-list-all-azure-storage-accounts-in-a-subscription"></a>Sådan liste over alle Azure lagerplads konti i et abonnement
Hvert Azure abonnement kan have op til 100 lagerplads konti. Se [Azure-abonnement og grænser, kvoter, og begrænsninger](../azure-subscription-service-limits.md)for de mest opdaterede oplysninger om begrænsninger ved.

Kør følgende cmdlet til at finde ud af navnet og status for lagerplads konti i det aktuelle abonnement:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="how-to-create-an-azure-storage-context"></a>Sådan oprettes en Azure lagerplads kontekst
Azure-lager kontekst er et objekt i PowerShell til at omfatter lagerplads legitimationsoplysninger. Ved hjælp af en lagerplads kontekst, mens du kører en hvilken som helst efterfølgende cmdlet gør det muligt at godkende anmodningen uden at angive lagerplads kontoen og dens hurtigtast eksplicit. Du kan oprette en lagerplads kontekst på mange måder, som bruger lagerplads navn og access kontonøgle, delte signatur (SAS) adgangstoken, forbindelsesstreng, eller anonyme. Du kan finde flere oplysninger [Ny AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).  

Benyt en af de følgende tre måder at oprette en lagerplads kontekst:

- Køre cmdlet'en [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) til at finde ud af tasten primære lagerplads adgang til kontoen Azure-lager. Dernæst skal ringe til [Ny AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) cmdlet for at oprette en lagerplads kontekst:

        $StorageAccountName = "yourstorageaccount"
        $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
        $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Oprette en delt adgang signaturtoken for en objektbeholder til Azure lagring og bruge det til at oprette en lagerplads kontekst:

        $sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
        $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

    Få mere at vide under [Ny AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) og [Ved hjælp af delt Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md).

- I nogle tilfælde vil du måske Angiv service slutpunkterne, når du opretter en ny lagerplads kontekst. Det kan være nødvendigt, når du har registreret et brugerdefineret domænenavn til din lagerplads konto med Blob-tjenesten, eller du vil bruge en delt adgang signatur til at få adgang til lagerplads ressourcer. Angive service slutpunkterne i en forbindelsesstreng og bruge det til at oprette en ny lagerplads kontekst, som vist nedenfor:

        $ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
        $Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Du kan finde flere oplysninger om hvordan du konfigurerer en lagerplads forbindelsesstreng, [Konfigurere forbindelsesstrenge](storage-configure-connection-string.md).

Nu, hvor du har konfigureret din computer og lært at administrere abonnementer og lagerplads konti med Azure PowerShell, skal du gå til næste afsnit for at se, hvordan du administrere Azure BLOB og blob snapshots.

### <a name="how-to-retrieve-and-regenerate-azure-storage-keys"></a>Sådan kan du hente og genoprette Azure lagerplads taster

En Azure-lager konto leveres med to konto nøgler. Du kan bruge følgende cmdlet til at hente dine nøgler.

    Get-AzureStorageKey -StorageAccountName "yourstorageaccount"

Du kan bruge følgende cmdlet til at hente en bestemt nøgle. Gyldige værdier er primære og sekundære.  

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary

    (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Secondary

Hvis du vil gerne genoprette dine nøgler, kan du bruge følgende cmdlet. Gyldige værdier for - KeyType er "Primære" og "Sekundære"

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Primary”

    New-AzureStorageKey -StorageAccountName $StorageAccountName -KeyType “Secondary”

## <a name="how-to-manage-azure-blobs"></a>Sådan administreres Azure BLOB
Azure Blob-lager er en tjeneste til lagring af store mængder ustrukturerede data, som tekst eller binære data, der kan åbnes fra et vilkårligt sted i verden via HTTP eller HTTPS. Dette afsnit antages det, at du allerede har kendskab til Azure Blob Storage Service begreberne. Oplysninger, skal du se [komme i gang med Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md) og [Blob Service begreber](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="how-to-create-a-container"></a>Sådan oprettes en objektbeholder
Alle blob i Azure lagerplads skal være i en beholder. Du kan oprette en privat objektbeholder ved hjælp af ny AzureStorageContainer cmdlet:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Der er tre niveauer af anonym læseadgang: **fra**, **Blob**og **objektbeholderen**. For at forhindre anonym adgang i blob, skal du indstille parameteren tilladelse til **fra**. Som standard objektbeholderen nye er privat, og kan kun få adgang til ejeren af kontoen. Tillade anonyme offentlige læseadgang blob ressourcer, men ikke til objektbeholder metadata eller listen over BLOB i beholderen, skal du indstille parameteren tilladelse til **Blob**. Hvis du vil tillade fuldstændig, offentlig læseadgang til blob ressourcer, angive objektbeholder metadata og listen over BLOB i beholderen, parameteren tilladelse til **objektbeholder**. Se [administrere anonym adgang til beholdere og BLOB](storage-manage-access-to-resources.md)kan finde flere oplysninger.

### <a name="how-to-upload-a-blob-into-a-container"></a>Sådan overfører du et blob til en objektbeholder
Azure Blob-lager understøtter Bloker BLOB og siden BLOB. Se [forstå Bloker blob, tilføje BLOB og siden BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx)kan finde flere oplysninger.

Hvis du vil overføre BLOB i til en objektbeholder, kan du bruge cmdlet'en [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) til. Som standard uploader denne kommando de lokale filer til en blok blob. Hvis du vil angive typen for blob, kan du bruge parameteren - BlobType.

I følgende eksempel kører cmdlet'en [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) til for at få alle filerne i den angivne mappe, og overfører dem derefter til den næste cmdlet ved hjælp af operatoren pipeline. Cmdletten [Sæt AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) uploader de lokale filer til din objektbeholder:

    Get-ChildItem –Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="how-to-download-blobs-from-a-container"></a>Sådan kan du hente BLOB fra en objektbeholder
I følgende eksempel viser, hvordan du henter BLOB fra en objektbeholder. Eksemplet først opretter forbindelse til Azure-lager ved hjælp af konteksten lagerplads konto, som omfatter kontonavn lager og den primære access-nøgle. I eksempel henter derefter en blob reference ved hjælp af cmdlet'en [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) til. I eksemplet bruges derefter cmdlet'en [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) til at hente BLOB til lokale destinationsmappen.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="how-to-copy-blobs-from-one-storage-container-to-another"></a>Sådan kopieres BLOB fra en objektbeholder til lagring til en anden
Du kan kopiere BLOB asynkront på tværs af lagerplads konti og områder. I følgende eksempel viser, hvordan du kopierer BLOB fra en objektbeholder til lagring til en anden inden for to forskellige lagerplads konti. Eksemplet angiver først variabler for kilde- og destinationstabellerne lagerplads konti og opretter derefter en lagerplads kontekst for hver konto. Derefter kopierer eksemplet BLOB fra objektbeholderen kilde til objektbeholderen destination ved hjælp af [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) cmdlet. Det antages, at de kilde- og destinationstabellerne lagerplads konti og beholdere allerede findes.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Bemærk, at dette eksempel udfører en asynkron kopi. Du kan overvåge statussen for hver enkelt kopi ved at køre Cmdletten [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) .

### <a name="how-to-copy-blobs-from-a-secondary-location"></a>Sådan kopieres BLOB fra en sekundær placering
Du kan kopiere BLOB fra sekundær placeringen af en RA-GRS-aktiveret konto.

    #define secondary storage context using a connection string constructed from secondary endpoints.
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### <a name="how-to-delete-a-blob"></a>Sådan sletter du en blob
For at slette en blob skal først hente en blob reference, og kald derefter Fjern AzureStorageBlob cmdlet på den. I følgende eksempel sletter alle BLOB i en given objektbeholder. Eksemplet angiver først variabler for en lagerplads konto og opretter derefter en lagerplads kontekst. Derefter eksemplet henter en blob reference ved hjælp af cmdlet'en [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) til og kører [Fjern AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) cmdlet for at fjerne BLOB fra en objektbeholder i Azure-lager.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## <a name="how-to-manage-azure-blob-snapshots"></a>Sådan administreres Azure blob snapshots
Azure kan du oprette et øjebliksbillede af en blob. Et øjebliksbillede er en skrivebeskyttet version af en blob, der er taget på et sted i gang. Når et øjebliksbillede er blevet oprettet, kan det være læse, kopieret, eller slettet, men ikke ændret. Snapshots ikke en metode til at sikkerhedskopiere en blob som den vises på et tidspunkt, hvor tidspunkt. Du kan finde flere oplysninger i [oprette et øjebliksbillede af en Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### <a name="how-to-create-a-blob-snapshot"></a>Sådan oprettes et snapshot af blob
For at oprette en snaphot af en blob skal først hente en blob reference, og kald derefter den `ICloudBlob.CreateSnapshot` metode på den. I følgende eksempel angiver først variabler for en lagerplads konto og opretter derefter en lagerplads kontekst. Derefter eksemplet henter en blob reference ved hjælp af cmdlet'en [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) til og kører metoden [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) for at oprette et øjebliksbillede.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### <a name="how-to-list-a-blobs-snapshots"></a>Hvordan til at få vist en blob snapshots
Du kan oprette så mange snapshots, som du vil bruge til en blob. Du kan få vist de snapshots, der er knyttet til din blob til at registrere din aktuelle snapshots. I følgende eksempel bruger en foruddefineret blob og cmdlet'en [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) til for at få vist snapshot af pågældende blob-opkald.  

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="how-to-copy-a-snapshot-of-a-blob"></a>Sådan kopieres et øjebliksbillede af en blob
Du kan kopiere et øjebliksbillede af en blob gendanne snapshot. Detaljerede oplysninger og begrænsninger i [oprette et øjebliksbillede af en Blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). I følgende eksempel angiver først variabler for en lagerplads konto og opretter derefter en lagerplads kontekst. Derefter definerer eksemplet objektbeholder og blob navn variablerne. I eksempel henter en blob reference ved hjælp af cmdlet'en [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) til og kører metoden [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) for at oprette et øjebliksbillede. Eksemplet kører derefter [Start AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) cmdlet for at kopiere øjebliksbillede af en blob ved hjælp af objektet ICloudBlob til kilde-blob. Sørg for at opdatere de variabler, der er baseret på din konfiguration før du kører eksemplet. Bemærk, at i følgende eksempel antager, at kilden og destination beholdere og kilde blob allerede findes.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Nu hvor du har lært at administrere Azure BLOB og blob snapshots med Azure PowerShell, gå til næste afsnit for at se, hvordan du administrerer tabeller, køer og filer.

## <a name="how-to-manage-azure-tables-and-table-entities"></a>Sådan administreres Azure tabeller og tabel enheder
Azure tabel lagringstjeneste er en NoSQL datastore, som du kan bruge til at gemme og forespørge store datasæt strukturerede, ikke-relationelle. Hovedelementerne i tjenesten er tabeller, -objekter og egenskaber. En tabel er en samling af objekter. Et objekt er et sæt af egenskaber. De enkelte objekter kan have op til 252 egenskaber, som er alle navn / værdi-par. Dette afsnit antages det, du allerede har kendskab til Azure tabel Lagringstjeneste begreber. Oplysninger, skal du se [om tabel Service-datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx) og [komme i gang med Azure Table storage ved hjælp af .NET](storage-dotnet-how-to-use-tables.md).

I de følgende underafsnit lærer du, hvordan du administrerer Azure-tabel ved hjælp af Azure PowerShell-lagringstjeneste. De scenarier, der er omfattet omfatter **oprette**, **slette**og **hentning af** **tabeller**, såvel som **tilføjelse**, **forespørgsler**og **slette tabel enheder**.

### <a name="how-to-create-a-table"></a>Sådan oprettes en tabel
Hver tabel skal placeres i en Azure-lager-konto. I følgende eksempel viser, hvordan du opretter en tabel i Azure-lager. Eksemplet først opretter forbindelse til Azure-lager ved hjælp af konteksten lagerplads konto, som omfatter kontonavn lager og access-nøglen. Derefter bruges [Ny AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) cmdlet til at oprette en tabel i Azure-lager.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-retrieve-a-table"></a>Hvordan du kan hente en tabel
Du kan forespørge og hente et eller alle tabeller i en lagerplads konto. I følgende eksempel viser, hvordan til at hente en bestemt tabel ved hjælp af cmdlet'en [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) til.

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Hvis du kalder Cmdletten Get-AzureStorageTable uden parametre, bliver alle lagerplads tabeller til en lagerplads konto.

### <a name="how-to-delete-a-table"></a>Sådan sletter du en tabel
Du kan slette en tabel fra en lagerplads konto ved hjælp af [Fjern AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) cmdlet.  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### <a name="how-to-manage-table-entities"></a>Sådan administreres tabel enheder
Azure PowerShell giver i øjeblikket ikke cmdletter for at administrere tabel enheder direkte. For at udføre handlinger på tabel enheder, kan du bruge de klasser, der er angivet i [Azure lagerplads klientbibliotek til .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### <a name="how-to-add-table-entities"></a>Sådan føjer du tabel enheder
Hvis du vil tilføje et objekt til en tabel, skal du først oprette et objekt, der definerer egenskaber for din enhed. Et objekt kan have op til 255 egenskaber, herunder 3 egenskaber for system: **PartitionKey**, **RowKey**og **tidsstempel**. Du er ansvarlig for at indsætte og opdatere værdierne af **PartitionKey** og **RowKey**. Serveren, der styrer værdien af **tidsstempel**, som ikke kan ændres. Sammen identificerer **PartitionKey** og **RowKey** entydigt hver enhed i en tabel.

-   **PartitionKey**: bestemmer den partition, der er gemt objektet i.
-   **RowKey**: identificerer entydigt objektet i partitionen.

Du kan definere op til 252 brugerdefinerede egenskaber for et objekt. Du kan finde flere oplysninger, kan du se [om tabel Service datamodellen](http://msdn.microsoft.com/library/azure/dd179338.aspx).

I følgende eksempel viser, hvordan du kan føje objekter til en tabel. Eksemplet viser, hvordan til at hente medarbejdertabellen og tilføje flere objekter i den. Først skal opretter den forbindelse til Azure-lager ved hjælp af konteksten lagerplads konto, som omfatter kontonavn lager og access-nøglen. Dernæst skal hentes den angivne tabel ved hjælp af cmdlet'en [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) til. Hvis tabellen ikke findes, bruges [Ny AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) cmdlet til at oprette en tabel i Azure-lager. Dernæst skal definerer eksemplet en brugerdefineret funktion Tilføj-objektet for at føje objekter til tabellen ved at angive de enkelte objekter partition og række nøgle. Funktionen Tilføj-enhed videoopkald Cmdletten [Ny-objekt](http://technet.microsoft.com/library/hh849885.aspx) fra klassen [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) til at oprette et objekt. Senere, kalder eksemplet metoden [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) på dette objekt at føje den til en tabel.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### <a name="how-to-query-table-entities"></a>Sådan forespørgsel tabel enheder
Hvis du vil forespørge en tabel, skal du bruge klassen [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) . I følgende eksempel antages det, at du allerede har kørt scriptet givet i artiklen Sådan føje objekter sektion i denne vejledning. Eksemplet først opretter forbindelse til Azure-lager ved hjælp af konteksten lager, hvilket omfatter kontonavn lager og access-nøglen. Derefter forsøger den at hente den tidligere oprettet "Medarbejdere"-tabel ved hjælp af cmdlet'en [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) til. Ringe til Cmdletten [Ny objekt](http://technet.microsoft.com/library/hh849885.aspx) i klassen Microsoft.WindowsAzure.Storage.Table.TableQuery opretter et nyt forespørgselsobjekt. I eksempel søger efter de enheder, har kolonnen 'ID', hvis værdien er 1 som angivet i filteret streng. Oplysninger, skal du se [forespørgsler tabeller og objekter](http://msdn.microsoft.com/library/azure/dd894031.aspx). Når du kører forespørgslen, returnerer alle objekter, der opfylder filterkriterierne.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize

#### <a name="how-to-delete-table-entities"></a>Sådan sletter du tabel enheder
Du kan slette et objekt med dens partition og række taster. I følgende eksempel antages det, at du allerede har kørt scriptet givet i artiklen Sådan føje objekter sektion i denne vejledning. Eksemplet først opretter forbindelse til Azure-lager ved hjælp af konteksten lager, hvilket omfatter kontonavn lager og access-nøglen. Derefter forsøger den at hente den tidligere oprettet "Medarbejdere"-tabel ved hjælp af cmdlet'en [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) til. Hvis tabellen findes, kalder eksemplet metoden [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) for at hente et objekt, der er baseret på dens partition og række nøgleværdier. Overføre derefter objektet til [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) metoden til at slette.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result
    if ($entity -ne $null)
    {
       #Delete the entity.
       $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="how-to-manage-azure-queues-and-queue-messages"></a>Sådan administreres Azure køer og beskeder i kø
Azure kø lagerplads er en tjeneste til lagring af stort antal meddelelser, der kan åbnes fra et vilkårligt sted i verden via godkendte opkald ved hjælp af HTTP eller HTTPS. Dette afsnit antages det, at du allerede har kendskab til Azure kø Lagringstjeneste begreber. Oplysninger, skal du se [Introduktion til Azure kø lagerplads ved hjælp af .NET](storage-dotnet-how-to-use-queues.md).

Dette afsnit viser dig, hvordan du administrerer Azure kø lagringstjeneste ved hjælp af Azure PowerShell. De scenarier, der er omfattet omfatter **indsættelse** og **sletning af** beskeder i kø, samt **oprette**, **slette**og **hentning af køer**.

### <a name="how-to-create-a-queue"></a>Sådan oprettes en kø
I følgende eksempel først opretter forbindelse til Azure-lager ved hjælp af konteksten lagerplads konto, som omfatter kontonavn lager og access-nøglen. Derefter kalder [Ny AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) cmdlet for at oprette en kø med navnet 'queuename'.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Se flere oplysninger om navngivningskonventioner til Azure kø Service, [navngive køer og Metadata](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### <a name="how-to-retrieve-a-queue"></a>Hvordan du kan hente en kø
Du kan forespørge og hente en bestemt kø eller en liste over alle køer med en konto med lagerplads. I følgende eksempel viser, hvordan til at hente en bestemt kø ved hjælp af cmdlet'en [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) til.

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Hvis du kalder Cmdletten [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) uden parametre, får liste over alle køer.

### <a name="how-to-delete-a-queue"></a>Sådan sletter du en kø
Hvis du vil slette en kø og alle de meddelelser, der er indeholdt i den, skal du ringe til Cmdletten Fjern AzureStorageQueue. I følgende eksempel vises, hvordan du kan slette en bestemt kø ved hjælp af fjern AzureStorageQueue cmdlet.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### <a name="how-to-insert-a-message-into-a-queue"></a>Hvordan du indsætter en meddelelse i en kø
Hvis du vil indsætte en meddelelse i en eksisterende kø, skal du først oprette en ny forekomst af klassen [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Derefter skal du ringe til metoden [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Du kan oprette en CloudQueueMessage fra en streng (i formatet UTF-8) eller en bytematrix af.

I følgende eksempel viser, hvordan du føjer meddelelse til en kø. Eksemplet først opretter forbindelse til Azure-lager ved hjælp af konteksten lagerplads konto, som omfatter kontonavn lager og access-nøglen. Dernæst skal hentes den angivne kø ved hjælp af cmdlet'en [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) til. Hvis køen findes, bruges Cmdletten [Ny objekt](http://technet.microsoft.com/library/hh849885.aspx) til at oprette en forekomst af klassen [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) . Senere, kalder eksemplet metoden [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) på denne meddelelsesobjekt til at føje den til en kø. Her er kode, der henter en kø og indsætter meddelelsen 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### <a name="how-to-de-queue-at-the-next-message"></a>Sådan deaktiveres kø på den næste meddelelse
Din kode kø deaktivere en meddelelse fra en kø i to trin. Når du kalder metoden [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) , får du den næste meddelelse i en kø. En meddelelse, der returneres fra **GetMessage** ikke er synligt for andre kode, der læser meddelelser fra denne kø. Fjerner meddelelsen fra køen skal afsluttes, skal du også kalde metoden [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) . Denne proces for at fjerne en meddelelse sikrer, at hvis din kode ikke behandle en meddelelse på grund af hardware og software fejl, kan en anden forekomst af din kode få den samme meddelelse og prøve igen. Din kode kalder **DeleteMessage** højre, når meddelelsen er blevet behandlet.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## <a name="how-to-manage-azure-file-shares-and-files"></a>Sådan administreres Azure filshares og filer
Azure fillagring indeholder delt storage for programmer ved hjælp af standard små og mellemstore virksomheder-protokollen. Virtuelle Microsoft Azure-computere og skytjenester kan dele data fra en fil på tværs af programkomponenter via tilsluttede aktier, og lokale programmer kan få adgang til data fra en fil i en del via funktionen fil storage API eller Azure PowerShell.

Se [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md) og [Fil Service REST-API](http://msdn.microsoft.com/library/azure/dn167006.aspx)kan finde flere oplysninger om Azure fillagring.

## <a name="how-to-set-and-query-storage-analytics"></a>Sådan angives og forespørgsel lagerplads analytics
Du kan bruge [Azure lagerplads Analytics](storage-analytics.md) for at indsamle måleredskaber for dine Azure lagerplads konti og logdata om anmodninger sendes til kontoen lagerplads. Du kan bruge lagringsmål til at overvåge tilstanden for en lagerplads konto og lagerplads logføring til at finde og løse eventuelle problemer med kontoen lagerplads.
Lagringsmål er som standard ikke aktiveret for din lagerplads tjenester. Du kan aktivere overvågning ved hjælp af Azure Portal eller Windows PowerShell, eller fra et program ved hjælp af biblioteket lagerplads klient. Lagerplads logføring sker serversiden og gør det muligt at registrere oplysninger for både vellykkede og mislykkede anmodninger i kontoen lagerplads. Disse logfiler gør det muligt at se oplysninger i læse, skrive og slette handlinger mod dine tabeller, køer og BLOB samt grundene til mislykkede anmodninger.

Hvis du vil se, hvordan du aktiverer og få vist Lagringsmål data ved hjælp af PowerShell, se, [hvordan du aktiverer Lagringsmål ved hjælp af PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

For at se, hvordan du aktiverer og hente lagerplads logføring data ved hjælp af PowerShell, skal du se [Sådan aktiveres lagerplads logføring ved hjælp af PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) og [finde dataene lagerplads logføring log](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata).
Detaljerede oplysninger om brug af Lagringsmål og lagerplads logføring til fejlfinding i lagerplads, skal du se [overvågnings, diagnosticere, og fejlfinding i forbindelse med Microsoft Azure-lager](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="how-to-manage-shared-access-signature-sas-and-stored-access-policy"></a>Sådan administreres delt Access signatur (SAS) og gemt Access-politik
Delt adgang signaturer er en vigtig del af sikkerhedsmodel for et program ved hjælp af Azure-lager. De kan bruges til at levere begrænsede tilladelser til kontoen lagerplads til kunder, der ikke bør have tasten konto. Som standard er kun ejeren af kontoen lagerplads kan få adgang til BLOB, tabeller og køer inden for kontoen. Hvis din tjeneste eller et program du skal gøre disse ressourcer tilgængelige for andre klienter uden at dele din access-nøgle, har du tre muligheder:

- Angiv en objektbeholder tilladelser til at tillade anonyme læseadgang på beholderen og dens BLOB. Dette er ikke tilladt for tabeller eller køer.
- Bruge en delt adgang signatur, giver begrænset adgang til bestemte beholdere, blob, forespørgsler og tabeller til et bestemt tidsinterval.
- Bruge en politik for lagrede access til at få et ekstra niveau af kontrol over delt adgang signaturer til en objektbeholder eller dens BLOB for en kø eller for en tabel. Den lagrede access-politik gør det muligt at ændre starttidspunktet, udløbstid eller tilladelser for en signatur, eller hvis du vil ophæve efter den er udstedt.

En delt adgang signatur kan være på en af to måder:

- **Ad hoc-SAS**: Når du opretter et ad hoc-SAS, starttidspunkt, udløbet klokkeslæt og tilladelser til Sikkerhedstilknytningerne all er angivet på SAS URI. Denne type SAS kan oprettes på en objektbeholder, blob, tabel eller kø, og det er ikke-revokable.
- **SAS med lagrede access-politik**: en lagrede access-politik er defineret på en ressource objektbeholder en blob objektbeholder, tabel eller kø - og du kan bruge den til at administrere begrænsninger til én eller flere delt adgang signaturer. Når du knytter en SAS til en politik for lagrede access, arver Sikkerhedstilknytningerne begrænsninger - starttidspunkt, udløbstid og tilladelser - defineret for den lagrede access-politik. Denne type SAS er revokable.

Se [Brug af delte Access signaturer (SAS)](storage-dotnet-shared-access-signature-part-1.md) og [administrere anonym adgang til beholdere og BLOB](storage-manage-access-to-resources.md)kan finde flere oplysninger.

I de næste afsnit lærer du, hvordan du kan oprette en delt adgang signatur token og lagrede access-politik for Azure tabeller. Azure PowerShell indeholder lignende cmdletter til beholdere, BLOB og køer samt. Hvis du vil køre scripts i dette afsnit, skal du hente [Azure PowerShell version 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) eller nyere.

### <a name="how-to-create-a-policy-based-shared-access-signature-token"></a>Sådan oprettes en politik-baseret delt Access signaturen token
Brug cmdlet'en ny AzureStorageTableStoredAccessPolicy til at oprette en ny politik for lagrede access. Derefter ringe [Ny AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) cmdlet for at oprette en ny politik-baserede delte signatur adgangstoken for en tabel med Azure-lager.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="how-to-create-an-ad-hoc-non-revokable-shared-access-signature-token"></a>Sådan oprettes et ad hoc-(ikke-revokable) delt Access signatur token
Brug cmdlet'en [Ny AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) til at oprette et nyt ad hoc-(ikke-revokable) delt Access signatur id for en Azure-lager tabel:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="how-to-create-a-stored-access-policy"></a>Sådan oprettes en lagrede access-politik
Brug cmdlet'en ny AzureStorageTableStoredAccessPolicy til at oprette en ny adgangspolitik for lagrede for en Azure-lager tabel:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="how-to-update-a-stored-access-policy"></a>Hvordan du opdaterer en lagrede access-politik
Brug cmdlet'en Set-AzureStorageTableStoredAccessPolicy til at opdatere en eksisterende lagrede access-politik for en Azure-lager tabel:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="how-to-delete-a-stored-access-policy"></a>Sådan sletter du en lagrede access-politik
Brug cmdlet'en Fjern AzureStorageTableStoredAccessPolicy til at slette en lagrede access-politik på en tabel, der Azure-lager:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="how-to-use-azure-storage-for-us-government-and-azure-china"></a>Sådan bruges Azure-lager til Azure Kina og amerikanske regering
Et Azure miljø er en uafhængig installation af Microsoft Azure, som [Azure Government til den amerikanske regering](https://azure.microsoft.com/features/gov/), [AzureCloud for global Azure](https://portal.azure.com)og [AzureChinaCloud til Azure drevet af 21Vianet i Kina](http://www.windowsazure.cn/). Du kan installere nye Azure miljøer til amerikansk government og Azure Kina.

Hvis du vil bruge Azure-lager med AzureChinaCloud, skal du oprette en lagerplads kontekst, der er knyttet til AzureChinaCloud. Følg disse trin for at komme i gang:

1.  Kør [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) cmdlet for at få vist de tilgængelige Azure miljøer:

    `Get-AzureEnvironment`

2.  Tilføje en Azure Kina konto i Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.  Oprette en lagerplads kontekst for en AzureChinaCloud-konto:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Hvis du vil bruge Azure-lager med [Azure amerikanske](https://azure.microsoft.com/features/gov/), skal du definere et nyt miljø og derefter oprette en ny lagerplads kontekst med dette miljø:

1.  Kør [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) cmdlet for at få vist de tilgængelige Azure miljøer:

    `Get-AzureEnvironment`

2.  Tilføje en Azure os Government konto i Windows PowerShell:

    `Add-AzureAccount –Environment AzureUSGovernment`

3.  Oprette en lagerplads kontekst for en AzureUSGovernment-konto:

        $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureUSGovernment

Du kan finde flere oplysninger i:

- [Microsoft Azure Government Developer Guide](../azure-government-developer-guide.md).
- [Oversigt over forskelle, når du opretter et program på Kina Service](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## <a name="next-steps"></a>Næste trin
I denne vejledning, har du lært Sådan administreres Azure-lager med Azure PowerShell. Her er nogle relaterede artikler og ressourcer for at lære mere om dem.

- [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)
- [Azure-lager PowerShell-cmdletter](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Vejledning til Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
