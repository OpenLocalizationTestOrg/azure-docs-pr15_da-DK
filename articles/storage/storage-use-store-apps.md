<properties
    pageTitle="Bruge Azure lagerplads i Windows Store-apps | Microsoft Azure"
    description="Lær at oprette en Windows Store-app, der bruger Azure Blob, kø, tabel eller fil lagerplads."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Sådan bruges Azure-lager i Windows Store-apps

## <a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du kommer i gang med at udvikle en Windows Store-app, der gør brug af Azure-lager.

## <a name="download-required-tools"></a>Hent nødvendige værktøjer

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) gør det nemt at opbygge, fejlfinding, lokalisere, pakke, og anvende Windows Store-apps. Visual Studio 2012 eller senere er påkrævet.
- [Azure lagerplads klientbibliotek](https://www.nuget.org/packages/WindowsAzure.Storage) indeholder en Windows Runtime klassebibliotek til at arbejde med Azure-lager.
- [WCF Data Services værktøjer til Windows Store Apps](http://www.microsoft.com/download/details.aspx?id=30714) udvider Tilføj Service Reference oplevelse med klientsiden OData support til Windows Store-apps i Visual Studio.

## <a name="develop-apps"></a>Udvikle apps

### <a name="getting-ready"></a>Gør klar

Oprette et nyt Windows Store app projekt i Visual Studio 2012 eller nyere:

![store-apps-lager-vs-projekt][store-apps-storage-vs-project]

Dernæst skal du tilføje en reference til biblioteket Azure lagerplads klienten ved at højreklikke på **referencer**, klikke på **Tilføj Reference**og derefter gennemse til lagerplads klient bibliotek for Windows Runtime, du har hentet:

![store-apps-lager-Vælg-bibliotek][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>Brug af biblioteket med tjenesterne Blob og kø

På dette tidspunkt er din app klar til at ringe til tjenesterne Azure Blob og kø. Tilføje følgende **ved hjælp af** sætninger, så Azure-lager typer kan benyttes direkte:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Derefter skal du tilføje en knap på siden. Føj følgende kode til hændelsen **skal du klikke på** og redigere din begivenhed handler metode ved hjælp af [asynkrone nøgleord](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Denne koden antages, at du har to strengvariabler, *kontonavn* og *accountKey*. De repræsenterer navnet på kontoen lager og den kontonøgle, der er knyttet til den pågældende konto.

Oprette og køre programmet. Klik på knappen kontrollere, om en objektbeholder med navnet *container1* findes på din konto, og derefter oprette den, hvis den ikke.

### <a name="using-the-library-with-the-table-service"></a>Brug af biblioteket med tjenesten tabel

Datatyper, der bruges til at kommunikere med tjenesten Azure-tabel, afhænger af WCF Data Services til biblioteket Windows Store app. Dernæst skal du tilføje en reference til de påkrævede WCF-biblioteker ved hjælp af konsollen Package Manager:

![store-apps-storage-pakke – manager][store-apps-storage-package-manager]

Brug følgende kommando for at punkt Package Manager til placeringen på computeren:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Denne kommando føjer automatisk alle nødvendige referencer til projektet. Hvis du ikke vil bruge konsollen Package Manager, kan du føje WCF Data Services NuGet mappe på din lokale computer til listen over pakke kilder og derefter tilføje henvisninger via Brugergrænsefladen, som beskrevet i [Administration af NuGet pakker ved hjælp af dialogboksen](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Når du har henvist til pakken WCF Data Services NuGet, kan du ændre koden i din knap **skal du klikke på** begivenhed:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Denne kode kontrollerer, om en tabel med navnet *Tabel1* findes i din konto, og derefter opretter den, hvis den ikke.

Du kan også tilføje en reference til Microsoft.WindowsAzure.Storage.Table.dll, som er tilgængelig i den samme pakke, du har hentet. Dette bibliotek indeholder flere funktioner, som refleksion-baserede serialisering og generisk forespørgsler. Bemærk, at dette bibliotek ikke understøtter JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
