<properties
    pageTitle="Sådan bruger du Azure fillagring fra Python | Microsoft Azure"
    description="Lær, hvordan du bruger Azure fillagring fra Python til overførsel, liste, download og slette filer."
    services="storage"
    documentationCenter="python"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robinsh"/>

# <a name="how-to-use-azure-file-storage-from-python"></a>Sådan bruger du Azure fillagring fra Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

## <a name="overview"></a>Oversigt

I denne artikel viser dig, hvordan til at udføre almindelige scenarier ved hjælp af fillagring. Eksemplerne er skrevet i Python og bruge [Microsoft Azure lagerplads SDK for Python]. De scenarier, der er dækket omfatter overførsel af listen over, hente og slette filer.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-share"></a>Oprette en del

Objektet **FileService** kan du arbejde med aktier, mapper og filer. Følgende kode opretter et **FileService** objekt. Tilføj følgende i øverst del af en Python-fil, som du ønsker fra et program adgang til Azure-lager.

    from azure.storage.file import FileService

Følgende kode opretter et **FileService** objekt ved hjælp af lagerplads konto navn og konto nøgle.  Erstat 'min konto' og 'mykey' med dit kontonavn og din nøgle.

    file_service = **FileService** (account_name='myaccount', account_key='mykey')

I følgende eksempel på kode, kan du bruge et **FileService** objekt til at oprette del, hvis den ikke findes.

    file_service.create_share('myshare')

## <a name="upload-a-file-into-a-share"></a>Overføre en fil til en del

Indeholder en Azure filshare lagring i det mindste, en rodmappe, hvor filer kan være placeret. I dette afsnit lærer du, hvordan du overføre en fil fra lokale lager til en del rodmappe.

For at oprette en fil, og overfør data skal du bruge den **oprette\_fil\_fra\_stien**, **oprette\_fil\_fra\_stream**, **oprette\_fil\_fra\_byte** eller **oprette\_fil\_fra\_tekst** metoder. De er overordnet metoder, der udfører den nødvendige samle når dataenes størrelse overstiger 64 MB.

**oprette\_fil\_fra\_sti** overfører indholdet af en fil fra den angivne sti og **oprette\_fil\_fra\_stream** overfører indholdet fra en allerede åben fil/stream. **oprette\_fil\_fra\_byte** overfører en matrix af byte, og **oprette\_fil\_fra\_tekst** overførsler værdien angivne tekst ved hjælp af den angivne kodning (standard UTF-8).

I følgende eksempel uploader indholdet af filen **sunset.png** i filen **myfile** .

    from azure.storage.file import ContentSettings
    file_service.create_file_from_path(
        'myshare',
        None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## <a name="how-to-create-a-directory"></a>Sådan: oprette en mappe

Du kan også organisere lager ved at placere filer i undermapper i stedet for få dem alle i rodmappen. Tjenesten Azure fil storage kan du oprette så mange mapper, som din konto tillader. Nedenstående kode opretter en underordnede mappe med navnet **sampledir** under rodmappen.

    file_service.create_directory('myshare', 'sampledir')

## <a name="how-to-list-files-and-directories-in-a-share"></a>Sådan: liste over filer og mapper i en del

Du kan få vist de filer og mapper i en del, du bruger den **listen\_kataloger\_og\_filer** metode. Denne metode returnerer en generator. Følgende kode skriver **navnet** på de enkelte filer og mapper i en del af konsollen.

    generator = file_service.list_directories_and_files('myshare')
    for file_or_dir in generator:
        print(file_or_dir.name)

## <a name="download-files"></a>Hente filer

For at hente data fra en fil, skal du bruge **få\_fil\_til\_stien**, **få\_fil\_til\_stream**, **få\_fil\_til\_byte**, eller **få\_fil\_til\_tekst**. De er overordnet metoder, der udfører den nødvendige samle når dataenes størrelse overstiger 64 MB.

I følgende eksempel demonstrerer brugen af **få\_fil\_til\_stien** at hente indholdet af filen **myfile** og gemme den til filen **fra sunset.png** .

    file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## <a name="delete-a-file"></a>Slette en fil

Til sidst skal du ringe **delete_file**for at slette en fil.

    file_service.delete_file('myshare', None, 'myfile')

## <a name="next-steps"></a>Næste trin

Nu hvor du har lært grundlæggende fillagring, skal du følge disse links for at få mere at vide.

- [Python Developer Center](/develop/python/)
- [Azure-lager Services REST-API](http://msdn.microsoft.com/library/azure/dd179355)
- [Azure-lager-teamets Blog]
- [Microsoft Azure-lager SDK for Python]

[Azure-lager-teamets Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure-lager SDK for Python]: https://github.com/Azure/azure-storage-python
