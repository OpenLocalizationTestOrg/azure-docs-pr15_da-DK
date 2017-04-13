<properties
    pageTitle="Brug af Azure CLI med Azure lagerplads | Microsoft Azure"
    description="Lær at bruge kommandolinjen Azure (Azure CLI) med Azure-lager til at oprette og administrere lagerplads konti og arbejde med Azure BLOB og filer. Azure CLI er et værktøj i tværs af platforme "
    services="storage"
    documentationCenter="na"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="micurd"/>

# <a name="using-the-azure-cli-with-azure-storage"></a>Brug af Azure CLI med Azure-lager

## <a name="overview"></a>Oversigt

Azure CLI indeholder et sæt af Åbn kilde på tværs af platforme kommandoer til at arbejde med Azure Platform. Den indeholder meget af de samme funktioner, der findes i [Azure portal](https://portal.azure.com) samt omfattende data funktionaliteten i access.

I denne vejledning, ser vi nærmere Sådan bruges [Azure kommandolinjen (Azure CLI)](../xplat-cli-install.md) til at udføre en række udvikling og administration af opgaver med Azure-lager. Vi anbefaler, at du hente og installere eller opgradere til den seneste Azure CLI, før du bruger denne vejledning.

Denne vejledning forudsætter, at du forstår de grundlæggende begreber i Azure-lager. Vejledningen indeholder en række scripts til at vise brugen af Azure CLI med Azure-lager. Sørg for at opdatere scriptvariablerne, der er baseret på din konfiguration før du kører hvert script.

> [AZURE.NOTE] Vejledningen indeholder Azure CLI kommandoen og script eksempler på klassisk lagerplads konti. Se [Brug af Azure CLI til Mac, Linux, og Windows med Azure ressourcestyring](../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) til Azure CLI kommandoer for ressourcestyring lagerplads konti.

## <a name="get-started-with-azure-storage-and-the-azure-cli-in-5-minutes"></a>Introduktion til Azure-lager og Azure CLI 5 minutter

Denne vejledning bruger Ubuntu eksempler, men andre OS platforme skal udføre på samme måde.

**Ny bruger af Azure:** Få en Microsoft Azure-abonnement og en Microsoft-konto, der er knyttet til dette abonnement. Se [Gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/), [Købsmuligheder](https://azure.microsoft.com/pricing/purchase-options/)og [Medlem indeholder](https://azure.microsoft.com/pricing/member-offers/) oplysninger om Azure Købsmuligheder (for medlemmer af MSDN, Microsoft Partner Network og BizSpark og andre Microsoft-programmer).

Du kan finde flere oplysninger om Azure abonnementer i [tildele administratorroller i Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx) .

**Når du har oprettet en Microsoft Azure-abonnement og konto:**

1. Hent og installer det Azure CLI følge instruktionerne i [installere Azure CLI](../xplat-cli-install.md).
2. Når Azure CLI er blevet installeret, vil du kunne bruge kommandoen azure fra din kommandolinjen (fest, Terminal, kommandoprompt) til at få adgang til kommandoerne Azure CLI. Skriv `azure` kommandoen, og du bør se følgende output.

    ![Azure kommandooutput][Image1]

3. Skriv i grænsefladen kommandoen linje `azure storage` til listen af alle kommandoerne azure-lager, og få et førstehåndsindtryk af funktionaliteten Azure CLI giver. Du kan skrive kommandonavnet med **-h** for (for eksempel `azure storage share create -h`) til at få vist detaljer om kommandosyntaksen for.
4. Nu, får du et simpelt script, der viser grundlæggende Azure CLI-kommandoer til at få adgang til Azure-lager. Scriptet bliver først bedt om at angive to variabler for din lagerplads konto og nøgle. Derefter opretter en ny objektbeholder i denne nye lagerplads konto scriptet og overføre en eksisterende billedfil (blob) til objektbeholderen. Når scriptet viser en liste over alle BLOB i objektbeholderen, henter den billedfilen til destinationsmappen som findes på den lokale computer.

        #!/bin/bash
        # A simple Azure storage example

        export AZURE_STORAGE_ACCOUNT=<storage_account_name>
        export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

        export container_name=<container_name>
        export blob_name=<blob_name>
        export image_to_upload=<image_to_upload>
        export destination_folder=<destination_folder>

        echo "Creating the container..."
        azure storage container create $container_name

        echo "Uploading the image..."
        azure storage blob upload $image_to_upload $container_name $blob_name

        echo "Listing the blobs..."
        azure storage blob list $container_name

        echo "Downloading the image..."
        azure storage blob download $container_name $blob_name $destination_folder

        echo "Done"

5. Åbn din foretrukne teksteditor (vim for eksempel) i din lokale computer. Skriv ovenfor scriptet i tekstredigeringsprogrammet.

6. Nu skal du opdatere scriptvariablerne, der er baseret på dine konfigurationsindstillinger.

    - **< storage_account_name >** Brug det angivne navn i scriptet eller skriv et nyt navn til kontoen lagerplads. **Vigtige:** Navnet på kontoen, lagerplads skal være entydigt i Azure. Det skal skrives med småt, for!

    - **< storage_account_key >** Hurtigtast af kontoen lagerplads.

    - **< container_name >** Brug det angivne navn i scriptet eller skriv et nyt navn til din objektbeholder.

    - **< image_to_upload >** Angiv en sti til et billede på din lokale computer, f.eks.: "~ / images/HelloWorld.png".

    - **< destination_folder >** Angiv en sti til en lokal mappe til lagring af filer, der er hentet fra Azure-lager, f.eks.: "~/downloadImages".

7. Når du har opdateret de nødvendige variabler i vim, skal du trykke på tastekombinationer "Esc,:, QW!" for at gemme scriptet.

8. For at køre dette script, nøjes med at skrive scriptfilnavnet i konsollen fest. Når du har kørt dette script, bør du have en lokal destinationsmappe, der indeholder den hentede billedfil. Følgende skærmbillede viser et eksempel på output:

Når du har kørt scriptet, bør du have en lokal destinationsmappe, der indeholder den hentede billedfil.

## <a name="manage-storage-accounts-with-the-azure-cli"></a>Administrere lager konti med Azure CLI

### <a name="connect-to-your-azure-subscription"></a>Oprette forbindelse til abonnementet Azure

Selvom de fleste af kommandoerne lagerplads fungerer uden et Azure-abonnement, anbefaler vi at oprette forbindelse til dit abonnement fra Azure CLI. For at konfigurere Azure CLI til at arbejde med dit abonnement skal du følge trinnene i [oprette forbindelse til et Azure-abonnement fra Azure CLI](../xplat-cli-connect.md).

### <a name="create-a-new-storage-account"></a>Oprette en ny firmapost lagerplads

Hvis du vil bruge Azure lagerplads, skal du en lagerplads konto. Når du har konfigureret din computer for at oprette forbindelse til dit abonnement, kan du oprette en ny konto Azure-lager.

        azure storage account create <account_name>

Navnet på din lagerplads konto skal være mellem 3 og 24 tegn og bruge tal og små bogstaver.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Konfigurere en standardkonto Azure-lager i miljøvariabler

Du kan have flere lagerplads konti i dit abonnement. Du kan vælge en af dem og angive den i miljøvariabler for alle storage-kommandoer i samme session. Dette gør det muligt at køre kommandoerne Azure CLI lagerplads uden at angive kontoen lager og centrale eksplicit.

        export AZURE_STORAGE_ACCOUNT=<account_name>
        export AZURE_STORAGE_ACCESS_KEY=<key>

En anden måde at konfigurere en standardkonto lagerplads bruger forbindelsesstreng. Få først forbindelsesstrengen ved kommando:

        azure storage account connectionstring show <account_name>

Derefter Kopiér forbindelsesstrengen output og angive den til miljøvariablen:

        export AZURE_STORAGE_CONNECTION_STRING=<connection_string>

## <a name="create-and-manage-blobs"></a>Oprette og administrere BLOB

Azure Blob-lager er en tjeneste til lagring af store mængder ustrukturerede data, som tekst eller binære data, der kan åbnes fra et vilkårligt sted i verden via HTTP eller HTTPS. Dette afsnit antages det, at du allerede har kendskab til Azure Blob storage begreber. Oplysninger, skal du se [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md) og [Blob Service begreber](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Oprette en objektbeholder

Alle blob i Azure lagerplads skal være i en beholder. Du kan oprette en private beholder ved hjælp af den `azure storage container create` kommandoen:

        azure storage container create mycontainer

> [AZURE.NOTE] Der er tre niveauer af anonyme læseadgang: **fra**, **Blob**og **beholder**. For at forhindre anonym adgang i blob, skal du indstille parameteren tilladelse til **fra**. Som standard nye beholderen er privat, og kan kun få adgang til ejeren af kontoen. Tillade anonyme offentlige læseadgang blob ressourcer, men ikke til objektbeholder metadata eller listen over BLOB i beholderen, skal du indstille parameteren tilladelse til **Blob**. Hvis du vil tillade fulde offentlige læseadgang til blob ressourcer, angive objektbeholder metadata og listen over BLOB i beholderen, parameteren tilladelse til **objektbeholder**. Se [administrere anonym adgang til beholdere og BLOB](storage-manage-access-to-resources.md)kan finde flere oplysninger.

### <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Azure Blob-lager understøtter Bloker BLOB og siden BLOB. Se [forstå Bloker blob, tilføje BLOB og siden BLOB](http://msdn.microsoft.com/library/azure/ee691964.aspx)kan finde flere oplysninger.

Hvis du vil overføre BLOB i til en objektbeholder, kan du bruge den `azure storage blob upload`. Som standard uploader denne kommando de lokale filer til en blok blob. Hvis du vil angive typen for blob, kan du bruge den `--blobtype` parameter.

        azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob

### <a name="download-blobs-from-a-container"></a>Hente BLOB fra en objektbeholder

I følgende eksempel viser, hvordan du henter BLOB fra en objektbeholder.

        azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'

### <a name="copy-blobs"></a>Kopiere BLOB

Du kan kopiere BLOB i eller på tværs af lagerplads konti og områder asynkront.

I følgende eksempel viser, hvordan kopiere BLOB fra den ene lagerplads konto til en anden. I dette eksempel vi oprette en objektbeholder, hvor BLOB er offentligt, anonymt tilgængelige.

    azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

    azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

    azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer

Dette eksempel udfører en asynkron kopi. Du kan overvåge statussen for hver kopieringen ved at køre den `azure storage blob copy show` handling.

Bemærk, at kildens URL-adresse, der er knyttet til kopieringen skal enten være offentligt tilgængelig, eller omfatter et token SAS (delt adgang signatur).

### <a name="delete-a-blob"></a>Slette en blob

Hvis du vil slette en blob, skal du bruge den under kommandoen:

        azure storage blob delete mycontainer myBlockBlob2

## <a name="create-and-manage-file-shares"></a>Oprette og administrere filshares

Azure fillagring indeholder delt storage for programmer ved hjælp af standard små og mellemstore virksomheder-protokollen. Virtuelle Microsoft Azure-computere og skytjenester samt lokale programmer, kan dele data fra en fil via tilsluttede aktier. Du kan administrere filshares og data fra en fil via Azure CLI. Se [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md) eller [hvordan du bruger Azure fillagring med Linux](storage-how-to-use-files-linux.md)kan finde flere oplysninger om Azure fillagring.

### <a name="create-a-file-share"></a>Oprette et filshare

En Azure filshare er et filshare til små og mellemstore virksomheder i Azure. Alle mapper og filer, der skal oprettes i et filshare. Et firma kan indeholde et ubegrænset antal aktier, og en del kan gemme et ubegrænset antal filer, op til kapacitetsgrænser for lagerplads kontoen. I følgende eksempel oprettes et filshare, med navnet **mitshare**.

        azure storage share create myshare

### <a name="create-a-directory"></a>Oprette en mappe

En mappe indeholder en valgfri hierarkiske struktur til en Azure filshare. I følgende eksempel oprettes en mappe med navnet **myDir** i det pågældende filshare.

        azure storage directory create myshare myDir

Bemærk Denne mappesti kan indeholde flere niveauer, *f.eks.*, **en / b**. Skal du kontrollere, at alle overordnede mapper findes. For eksempel for stien **en / b**, du skal oprette directory **en** først og derefter oprette directory **b**.

### <a name="upload-a-local-file-to-directory"></a>Overføre en lokal fil til mappe

I følgende eksempel overfører en fil fra **~/temp/samplefile.txt** til mappen **myDir** . Redigere stien til filen, så den peger på en gyldig fil på din lokale computer:

        azure storage file upload '~/temp/samplefile.txt' myshare myDir

Bemærk, at en fil i dialogboksen del kan være op til 1 TB i størrelse.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Liste over filer i den del rod eller mappe

Du kan få vist filer og undermapper i en del rod eller et bibliotek med følgende kommando:

        azure storage file list myshare myDir

Bemærk, at navnet på den mappe er valgfrit for handlingen oversigt. Hvis den udelades, viser kommandoen indholdet af dialogboksen del rodmappe.

### <a name="copy-files"></a>Kopiere filer

Begynder med version 0.9.8 af Azure CLI, kan du kopiere en fil til en anden fil, en fil til en blob eller en blob til en fil. Nedenfor viser vi, hvordan du kan udføre disse kopi handlinger ved hjælp af CLI-kommandoer. Kopiere en fil til den nye mappe:

    azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

Sådan kopieres et blob til en filmappe:

    azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString

## <a name="next-steps"></a>Næste trin

Her er nogle relaterede artikler og ressourcer for at lære mere om Azure-lager.

- [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)
- [Azure-lager RESTEN API Reference](https://msdn.microsoft.com/library/azure/dd179355.aspx)


[Image1]: ./media/storage-azure-cli/azure_command.png
