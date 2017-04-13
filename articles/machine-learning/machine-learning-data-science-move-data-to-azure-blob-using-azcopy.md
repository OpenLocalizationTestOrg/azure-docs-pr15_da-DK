<properties
    pageTitle="Flytte Data til og fra Azure Blob-lager ved hjælp af AzCopy | Microsoft Azure"
    description="Flytte Data til og fra Azure Blob-lager ved hjælp af AzCopy"
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

# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Flytte Data til og fra Azure Blob-lager ved hjælp af AzCopy

AzCopy er et kommandolinjen værktøj til overførsel, hente og kopierer data til og fra Microsoft Azure blob, filer og -tabellager.

Finde instruktioner til installation af AzCopy og yderligere oplysninger om at bruge det med Azure-platformen, kan du se [Introduktion til AzCopy kommandolinjeparametre Utility](../storage/storage-use-azcopy.md).

Vejledning i teknologier, der bruges til at flytte data til eller fra Azure Blob-lager er sammenkædet her:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Hvis du bruger VM, der er konfigureret med de scripts, som leveres af [Data videnskabelige virtuelle maskiner i Azure](machine-learning-data-science-virtual-machines.md), derefter installeret AzCopy allerede på VM.

> [AZURE.NOTE] Henvise til [Azure Blob grundlæggende](../storage/storage-dotnet-how-to-use-blobs.md) og til [Azure Blob-tjenesten](https://msdn.microsoft.com/library/azure/dd179376.aspx)for at få en komplet Introduktion til Azure blob-lager.


## <a name="prerequisites"></a>Forudsætninger

Dette dokument forudsætter, at du har et Azure-abonnement, en lagerplads konto og tasten lagerplads for den pågældende konto. Før du overfører/henter data, skal du kende Azure lagerplads konto navn og konto nøglen.

- For at konfigurere et Azure abonnement skal du se [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).

- Vejledning i at oprette en lagerplads konto og for at komme konto og vigtige oplysninger, skal du se [om Azure lagerplads konti](../storage/storage-create-storage-account.md).


## <a name="run-azcopy-commands"></a>Køre AzCopy kommandoer

Åbne et kommandovindue for at køre AzCopy kommandoer, og gå til mappen AzCopy installation på din computer, hvor den eksekverbare AzCopy.exe er placeret. 

Den grundlæggende syntaks for AzCopy kommandoer er:

    AzCopy /Source:<source> /Dest:<destination> [Options]

>[AZURE.NOTE] Du kan føje installationsplacering AzCopy til en sti og derefter køre kommandoerne fra en anden mappe. Som standard installeres AzCopy *% ProgramFiles(x86) %\Microsoft SDKs\Azure\AzCopy* eller *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.

## <a name="upload-files-to-an-azure-blob"></a>Overføre filer til en Azure blob

Hvis du vil overføre en fil, skal du bruge følgende kommando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Hente filer fra en Azure blob

Hvis du vil hente en fil fra en Azure blob, skal du bruge følgende kommando:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Overføre BLOB mellem Azure objektbeholdere

For at overføre BLOB mellem Azure beholdere, skal du bruge følgende kommando:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Tip til brug af AzCopy

> [AZURE.TIP]   
> 1. Når **overførsel af** filer, */S* overfører filer gælder. Der ikke overføres filer i undermapper uden denne parameter.  
> 2. Når **hente** filen, */S* søger objektbeholder gælder indtil alle filer i den angivne mappe og dens undermapper eller alle filer, der svarer til det angivne mønster i den angivne mappe og dens undermapper, hentes.  
> 3.  Du kan ikke angive en **bestemt blob-fil** til overførsel ved hjælp af parameteren */Source* . Angiv filnavnet blob for at hente ved hjælp af parameteren */Pattern* for at hente en bestemt fil. **/S** parameter kan bruges til at have AzCopy se efter en fil navn mønster gælder. Uden parameteren mønster henter AzCopy alle filerne i mappen.
