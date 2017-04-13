<properties
    pageTitle="Sådan bruger du Blob-lager (objekt storage) fra fonetisk | Microsoft Azure"
    description="Gemme ustrukturerede data i skyen med Azure Blob-lager (objekt storage)."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="tamram"/>


# <a name="how-to-use-blob-storage-from-ruby"></a>Sådan bruger du Blob-lager fra fonetisk

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>Oversigt

Azure Blob-lager er en tjeneste, der gemmer ustrukturerede data i skyen som objekter/BLOB. BLOB-lager kan gemme alle typer tekst eller binære data, som et dokument, mediefil eller program installer. BLOB-lager kaldes også objekt lagerplads.

Denne vejledning viser dig, hvordan til at udføre almindelige scenarier ved hjælp af Blob-lager. Eksemplerne skrives ved hjælp af fonetisk API. De scenarier, der er omfattet omfatter **overførsel, listen over, hvis du henter,** og **slette** BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Oprette et fonetisk tjenesteprogram

Oprette et fonetisk program. Flere oplysninger under [fonetisk på gør webprogram på en Azure VM](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)

## <a name="configure-your-application-to-access-storage"></a>Konfigurere dit program tilladelse til at få adgang til lager

Hvis du vil bruge Azure-lager, skal du hente og bruge den fonetisk azure pakke, som indeholder et sæt af nemmere biblioteker, kommunikerer med lagerplads RESTEN tjenester.

### <a name="use-rubygems-to-obtain-the-package"></a>Brug RubyGems til at hente pakken

1. Brug en kommandolinjeparametre som **PowerShell** (Windows), **Terminal** (Mac) eller **Bash** (Unix).

2. Skriv "gem Installer azure" i kommandovinduet til at installere gem og afhængigheder.

### <a name="import-the-package"></a>Importere pakken

Bruger din foretrukne teksteditor, skal du tilføje følgende til toppen af den fonetisk fil, hvor du vil bruge lagerplads:

    require "azure"

## <a name="setup-an-azure-storage-connection"></a>Konfiguration af forbindelse til Azure-lager

Modulet azure læser miljøvariabler **AZURE\_LAGERPLADS\_konto** og **AZURE\_LAGERPLADS\_ACCESS_KEY** oplysninger, der kræves for at oprette forbindelse til kontoen Azure-lager. Hvis variablerne miljø ikke er angivet, skal du angive kontooplysninger inden du bruger **Azure::Blob::BlobService** med følgende kode:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"


Sådan hentes disse værdier fra en klassisk eller Ressourcestyring lagerplads konto i portalen Azure:

1. Log på [Azure-portalen](https://portal.azure.com).
2. Naviger til kontoen lagerplads, du vil bruge.
3. Klik på **Access-taster**i bladet indstillinger til højre.
4. I Access-taster bladet, der vises, får du vist hurtigtast 1 og hurtigtast 2. Du kan bruge en af disse. 
5. Klik på Kopiér for at kopiere nøglen til Udklipsholder. 

Sådan hentes disse værdier fra en klassisk lagerplads konto i klassisk Azure-portalen:

1. Log på [Klassisk Azure portal](https://manage.windowsazure.com).
2. Naviger til kontoen lagerplads, du vil bruge.
3. Klik på **ADMINISTRER HURTIGTASTER** nederst i navigationsruden.
4. I pop op-dialogboksen, får du vist kontonavn lager, primære hurtigtast og sekundær hurtigtast. Du kan bruge den primære liste eller et sekundær for hurtigtast. 
5. Klik på Kopiér for at kopiere nøglen til Udklipsholder.

## <a name="create-a-container"></a>Oprette en objektbeholder

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Objektet **Azure::Blob::BlobService** kan du arbejde med beholdere og BLOB. Hvis du vil oprette en objektbeholder, skal du bruge den **oprette\_container()** metode.

Følgende kodeeksempel opretter en objektbeholder eller Udskriv fejlen, hvis der ikke er nogen.

    azure_blob_service = Azure::Blob::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Hvis du vil gøre filerne i beholderen offentlige, kan du angive den objektbeholder tilladelser.

Du kan kun redigere den <strong>oprette\_container()</strong> opkald til at overføre den **: offentlige\_access\_niveau** indstilling:

    container = azure_blob_service.create_container("test-container",
      :public_access_level => "<public access level>")


Gyldige værdier for den **: offentlige\_access\_niveau** indstilling er:

* **blob:** Angiver fulde offentlige læseadgang for objektbeholder og blob data. Klienter kan optælle BLOB inde i beholderen via anonyme anmodningen, men kan ikke optælles objektbeholdere i kontoen lagerplads.

* **objektbeholder:** Angiver offentlige læseadgang for BLOB. BLOB-data i denne objektbeholder kan læses via anonyme anmodningen, men objektbeholder data er ikke tilgængelig. Klienter kan ikke optælles BLOB inde i beholderen via anonyme anmodninger.

Du kan også ændre det offentlige adgangsniveau for en objektbeholder ved hjælp af **angive\_objektbeholder\_acl()** metode til at angive det offentlige adgangsniveau.

Følgende kodeeksempel ændres niveauet for offentlig adgang til **objektbeholder**:

    azure_blob_service.set_container_acl('test-container', "container")

## <a name="upload-a-blob-into-a-container"></a>Overføre en blob til en objektbeholder

Hvis du vil overføre indhold til en blob, skal du bruge den **oprette\_Bloker\_blob()** metode til at oprette blob, bruge en fil eller en streng som indholdet af blob.

Følgende kode uploader fil **test.png** som en ny blob med navnet "billede-blob" i beholderen.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <a name="list-the-blobs-in-a-container"></a>Liste over BLOB i en objektbeholder

Du kan få vist beholderne, bruge **list_containers()** metode.
Du kan få vist BLOB i en objektbeholder, bruge **listen\_blobs()** metode.

Dette udskriver URL-adresserne på alle BLOB i alle beholdere for kontoen.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <a name="download-blobs"></a>Hente BLOB

Hvis du vil hente blob, skal du bruge den **få\_blob()** metode til at hente indholdet.

Følgende kodeeksempel demonstrerer brugen af **få\_blob()** til at hente indholdet af "billede-blob" og skrive det til en lokal fil.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <a name="delete-a-blob"></a>Slette en Blob
Endelig, hvis du vil slette en blob, bruge den **slette\_blob()** metode. Følgende kodeeksempel viser hvordan du sletter en blob.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <a name="next-steps"></a>Næste trin

Hvis du vil lære mere komplekse lagerplads opgaver, skal du følge disse links:

- [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)
- [Azure SDK til fonetisk](https://github.com/WindowsAzure/azure-sdk-for-ruby) lager på GitHub
- [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
