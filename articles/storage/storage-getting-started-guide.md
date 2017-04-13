<properties
    pageTitle="Introduktion til Azure-lager i fem minutter | Microsoft Azure"
    description="Hurtigt sætte produktiviteten i vejret op på Microsoft Azure blob, tabel og køer ved hjælp af Azure lagerplads hurtig starter Visual Studio og emulatoren Azure-lager. Køre dit første Azure-lager-program i fem minutter."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Introduktion til Azure-lager i fem minutter

## <a name="overview"></a>Oversigt

Det er nemt at komme i gang udvikling med Azure-lager. Dette selvstudium viser, hvordan du får et Azure-lager program op at køre hurtigt. Du skal bruge Hurtig Start skabelonerne inkluderet med Azure SDK til .NET. Disse hurtig starter indeholder klar og kør-kode, der viser nogle grundlæggende programmering scenarier med Azure-lager.

Hvis du vil vide mere om Azure-lager før dykning i koden, skal du se [Næste trin](#next-steps).

## <a name="prerequisites"></a>Forudsætninger

Før du går i gang, skal du bruge følgende forudsætninger:

1. For at samle og oprette programmet, skal du en version af [Visual Studio](https://www.visualstudio.com/) installeret på computeren.

2. Installere den nyeste version [Azure SDK til .NET](https://azure.microsoft.com/downloads/). SDK indeholder Azure Hurtig start eksempel projekter, emulatoren Azure-lager og [Azure lagerplads klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Sørg for, at du har [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) installeret på computeren, som det kræves af Hurtig start Azure eksempel-projekter, som vi bruger i dette selvstudium.

    Hvis du ikke er sikker på, hvilken version af .NET Framework er installeret på computeren, kan du se [Sådan: finde ud af, hvor .NET Framework versioner er installeret](https://msdn.microsoft.com/vstudio/hh925568.aspx). Eller tryk på knappen **Start** eller på Windows-tasten, skriver **Kontrolpanel**. Klik derefter på **programmer** > **programmer og funktioner**, og finde ud af om .NET Framework 4.5 vises blandt de installerede programmer.

4. Du skal bruge et Azure-abonnement og en Azure-lager-konto.

    - For at få et Azure abonnement skal du se [Gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/), [Købsmuligheder](https://azure.microsoft.com/pricing/purchase-options/)og [Medlem tilbyder](https://azure.microsoft.com/pricing/member-offers/) (for medlemmer af MSDN, Microsoft Partner Network og BizSpark og andre Microsoft-programmer).
    - For at oprette et lager konto i Azure, se, [hvordan du opretter en lagerplads konto](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Kør dit første Azure-lager-program i forhold til Azure-lager i skyen

Når du har en konto, kan du oprette en simpel Azure-lager-program ved hjælp af en af Azure hurtig starter eksempel projekterne i Visual Studio. Dette selvstudium fokuserer på eksempel projekterne til Azure-lager: **Azure-lager: BLOB**, **Azure-lager: filer**, **Azure-lager: køer**, og **Azure-lager: tabeller**:

1. Start Visual Studio.
2. Klik på **Nyt projekt**fra menuen **filer** .
3. I dialogboksen **Nyt projekt** , skal du klikke på **installerede** > **skabeloner** > **Visual C#** > **skyen** > **Hurtig start-guider** > **Data Services**.
    en. Vælg en af følgende skabeloner: **Azure-lager: BLOB**, **Azure-lager: filer**, **Azure-lager: køer**, eller **Azure-lager: tabeller**.
    b. Sørg for, at **.NET Framework 4.5** er markeret som destination framework.
    - 3.c. Angiv et navn til dit projekt, og oprette en ny løsning i Visual Studio, som vist:

    ![Azure Hurtig start][Image1]

Du muligvis vil gennemgå koden, før du kører programmet. For at gennemse koden, skal du vælge **Solution Explorer** i menuen **Vis** i Visual Studio. Klik derefter dobbeltklikke på filen Program.cs.

Derefter skal du køre eksempelprogrammet:

1.  Vælg **Solution Explorer** i menuen **Vis** i Visual Studio. Åbne App.config-filen, og fjerne forbindelsesstrengen til Azure lagerplads emulatoren:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Fjern kommentar fra forbindelsesstrengen for tjenesten Azure-lager og give lagerplads konto navn og access nøglen i App.config-filen:`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    For at hente din lagerplads kontonøgle access skal du se [administrere lagerplads access-taster](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Når du angiver kontonavn lager og hurtigtast i App.config-filen, i menuen **filer** , klik på **Gem alt** for at gemme alle project-filer.
4.  Klik på menuen **Build** **Opbygge løsning**.
5.  Tryk på **F11** for at køre løsningen trin for trin, eller tryk på **F5** for at køre løsningen på menuen **fejlfinding** .


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Kør dit første Azure-lager program lokalt i forhold til Azure-lager emulatoren

[Azure lagerplads Emulator](storage-use-emulator.md) indeholder et lokalt miljø, der emulerer Azure Blob, kø og tabel tjenesterne til udviklingsformål. Du kan bruge lagerplads emulatoren for at teste din lagerplads programmet lokalt, uden at oprette et Azure-abonnement eller lagerplads konto og uden at der påløber nogen omkostninger.

Hvis du vil prøve det, Lad os oprette en simpel Azure-lager-program ved hjælp af en af Azure hurtig starter eksempel projekterne i Visual Studio. Dette selvstudium fokuserer på **Azure Blob-lager**, **Azure Table Storage**og **Azure kø lager** eksempel projekter:

1. Start Visual Studio.
2. Klik på **Nyt projekt**fra menuen **filer** .
3. I dialogboksen **Nyt projekt** , skal du klikke på **installerede** > **skabeloner** > **Visual C#** > **skyen** > **Hurtig start-guider** > **Data Services**.
    en. Vælg en af følgende skabeloner: **Azure-lager: BLOB**, **Azure-lager: filer**, **Azure-lager: køer**, eller **Azure-lager: tabeller**.
    b. Sørg for, at **.NET Framework 4.5** er markeret som destination framework.
    c. Angiv et navn til dit projekt, og oprette en ny løsning i Visual Studio, som vist:

    ![Azure Hurtig start][Image1]

4.  Vælg **Solution Explorer** i menuen **Vis** i Visual Studio. Åbn App.config-filen, og fjerne forbindelsesstrengen for kontoen Azure-lager, hvis du allerede har tilføjet en. Derefter Fjern kommentar fra forbindelsesstrengen til Azure lagerplads emulatoren:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Du muligvis vil gennemgå koden, før du kører programmet. For at gennemse koden, skal du vælge **Solution Explorer** i menuen **Vis** i Visual Studio. Klik derefter dobbeltklikke på filen Program.cs.

Derefter skal du køre eksempelprogrammet i Azure lagerplads emulatoren:

1.  Tryk på knappen **Start** eller på Windows-tasten, Søg efter *Microsoft Azure-lager emulator*, og starte programmet. Når emulatoren starter, får du vist et ikon og en besked i området Windows opgavevisning.
2.  Klik på **Opret løsning** i menuen **Build** i Visual Studio.
3.  Tryk på **F11** for at køre løsningen trinvise menuen **fejlfinding** , eller tryk på **F5** for at køre løsningen fra start til slut.

## <a name="next-steps"></a>Næste trin

Se disse ressourcer mere at vide om Azure-lager:

* [Introduktion til Microsoft Azure-lager](storage-introduction.md)
* [Introduktion til Azure-lager Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Introduktion til Azure Blob-lager ved hjælp af .NET](storage-dotnet-how-to-use-blobs.md)
* [Introduktion til Azure Table storage ved hjælp af .NET](storage-dotnet-how-to-use-tables.md)
* [Introduktion til Azure kø lager ved hjælp af .NET](storage-dotnet-how-to-use-queues.md)
* [Introduktion til Azure fillagring i Windows](storage-dotnet-how-to-use-files.md)
* [Overføre data med AzCopy kommandolinjeparametre Utility](storage-use-azcopy.md)
* [Azure-lager dokumentation](https://azure.microsoft.com/documentation/services/storage/)
* [Microsoft Azure-lager klientbibliotek til .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Azure-lager Services REST-API](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
