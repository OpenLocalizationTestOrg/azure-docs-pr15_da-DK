<properties
    pageTitle="Flytte Data til og fra Azure Blob-lager ved hjælp af Python | Microsoft Azure"
    description="Flytte Data til og fra Azure Blob-lager ved hjælp af Python"
    services="machine-learning,storage"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="bradsev" />

# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Flytte Data til og fra Azure Blob-lager ved hjælp af Python

Dette emne beskrives det, hvordan listen, uploade og downloade BLOB ved hjælp af Python API. Med Python-API'EN, der findes i Azure SDK, kan du:

- Oprette en objektbeholder
- Overføre en blob til en objektbeholder
- Hente BLOB
- Liste over BLOB i en objektbeholder
- Slette en blob

Se, [hvordan du bruger tjenesten Blob Storage fra Python](../storage/storage-python-how-to-use-blob-storage.md)kan finde flere oplysninger om brug af API Python.

Vejledning i teknologier, der bruges til at flytte data til eller fra Azure Blob-lager er sammenkædet her:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Hvis du bruger VM, der er konfigureret med de scripts, som leveres af [Data videnskabelige virtuelle maskiner i Azure](machine-learning-data-science-virtual-machines.md), derefter installeret AzCopy allerede på VM.

> [AZURE.NOTE] Henvise til [Azure Blob grundlæggende](../storage/storage-dotnet-how-to-use-blobs.md) og til [Azure Blob-tjenesten](https://msdn.microsoft.com/library/azure/dd179376.aspx)for at få en komplet Introduktion til Azure blob-lager.


## <a name="prerequisites"></a>Forudsætninger

Dette dokument forudsætter, at du har et Azure-abonnement, en lagerplads konto og tasten lagerplads for den pågældende konto. Før du overfører/henter data, skal du kende Azure lagerplads konto navn og konto nøglen.

- For at konfigurere et Azure abonnement skal du se [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).

- Vejledning i at oprette en lagerplads konto og for at komme konto og vigtige oplysninger, skal du se [om Azure lagerplads konti](../storage/storage-create-storage-account.md).


## <a name="upload-data-to-blob"></a>Overføre Data til Blob

Tilføj følgende kodestykke i den øverste del af en hvilken som helst Python kode, du ønsker fra et program adgang til Azure-lager:

    from azure.storage.blob import BlobService

Objektet **BlobService** kan du arbejde med beholdere og BLOB. Følgende kode opretter et BlobService objekt ved hjælp af lagerplads konto navn og konto nøgle. Erstat kontonavn og kontonøgle med dit reelle konto og din nøgle.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Brug følgende metoder til at overføre data til en blob:

1. placere\_Bloker\_blob\_fra\_path (uploader indholdet af en fil fra den angivne sti)
2. placere\_block_blob\_fra\_fil (uploader indholdet fra en allerede åben fil/stream)
3. placere\_Bloker\_blob\_fra\_byte (uploads en matrix af byte)
4. placere\_Bloker\_blob\_fra\_tekst (uploader værdien angivne tekst ved hjælp af den angivne kodning)

Følgende eksempelkode uploader en lokal fil til en objektbeholder:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Følgende eksempelkode overfører alle de filer, (undtagen kataloger) i en lokal mappe til blob-lager:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Hente Data fra Blob

Brug følgende metoder til at hente data fra en blob:
1. få\_blob\_til\_sti
2. få\_blob\_til\_fil
3. få\_blob\_til\_byte
4. få\_blob\_til\_tekst

Disse metoder, der udfører den nødvendige samle når dataenes størrelse overstiger 64 MB.

Følgende eksempelkode henter indholdet af en blob i en objektbeholder til en lokal fil:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

Følgende eksempelkode henter alle BLOB fra en objektbeholder. Det bruger listen\_BLOB for at hente en liste over tilgængelige BLOB i beholderen og henter dem til en lokal mappe.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"     

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
