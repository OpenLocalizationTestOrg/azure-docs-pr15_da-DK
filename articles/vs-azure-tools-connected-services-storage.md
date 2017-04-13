<properties 
   pageTitle="Tilføje Azure-lager ved hjælp af forbundne tjenester i Visual Studio | Microsoft Azure"
   description="Føje Azure-lager til din app ved hjælp af dialogboksen Visual Studio tilføje forbundne tjenester"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>Tilføje Azure-lager ved hjælp af Visual Studio forbundne tjenester

## <a name="overview"></a>Oversigt

Med Visual Studio-2015, kan du slutter en hvilken som helst C# skybaseret tjeneste, .NET back end-mobile service, ASP.NET websted eller tjeneste, ASP.NET 5 service eller Azure WebJob tjeneste til Azure-lager ved hjælp af dialogboksen **Tilføj forbundne tjenester** . Funktionen forbundne service tilføjer alle de nødvendige referencer og forbindelse kode og ændrer dine konfigurationsfiler korrekt. Dialogboksen fører dig også dokumentation, der fortæller dig, hvad de næste trin er at starte blob-lager, forespørgsler og tabeller.

## <a name="supported-project-types"></a>Understøttede projekttyper

Du kan bruge dialogboksen forbundne tjenester til at oprette forbindelse til Azure-lager i følgende projekttyper.

- ASP.NET Web projekter

- ASP.NET 5 projekter

- .NET skyen Service Web rolle og arbejder rolle projekter

- .NET mobile Services-projekter

- Azure WebJob projekter


## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>Oprette forbindelse til Azure-lager ved hjælp af dialogboksen forbundne tjenester

1. Sørg for, at du har en Azure-konto. Hvis du ikke har en Azure-konto, kan du tilmelde dig en [gratis prøveversion](http://go.microsoft.com/fwlink/?LinkId=518146). Når du har en Azure-konto, kan du oprette lagerplads konti, oprette mobile-tjenester og konfigurere Azure Active Directory.

1. Åbn dit projekt i Visual Studio, åbne genvejsmenuen for noden **referencer** i Solution Explorer, og vælg derefter **Tilføj forbindelse tjeneste**.

    ![Tilføje en forbundne tjeneste](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Vælg **Azure-lager**i dialogboksen **Tilføj forbindelse tjeneste** , og vælg derefter knappen **Konfigurer** . Du muligvis bedt om at logge på Azure, hvis du ikke allerede har gjort det.

    ![Tilføje dialogboksen forbindelse tjeneste - lagerplads](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Vælg en eksisterende lagerplads konto i dialogboksen **Azure-lager** , og vælg **Tilføj**.

    Hvis du vil oprette en ny firmapost lagerplads, skal du gå til næste trin. Ellers skal du gå videre til trin 6.

    ![Azure-lager dialogboks](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Sådan oprettes en ny lagerplads konto: 

    1. Vælg knappen **Opret en ny lagerplads konto** nederst i dialogboksen Azure-lager.

    1. Udfyld dialogboksen **Opret lagerplads konto** , og klik på knappen **Opret** .
    
        ![Azure-lager dialogboks](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Når du er tilbage i dialogboksen **Azure-lager** , vises den nye lagerplads på listen.

    1. Vælg den nye lagerplads på listen, og vælg **Tilføj**.

1. Tjenesten storage forbindelse vises under noden Service referencer i projektet WebJob.

    ![Azure-lageret i web job project](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Gennemgå siden Introduktion, der vises, og find ud af, hvordan projektet blev ændret. En introduktion side vises i din browser, når du tilføjer en forbundne tjeneste. Du kan gennemgå de foreslåede næste trin og kodeeksempler, eller du kan skifte til siden Hvad skete der med at se, hvilke referencer er blevet føjet til projektet, og hvordan filerne kode og konfiguration er blevet ændret.

## <a name="how-your-project-is-modified"></a>Hvordan dit projekt er ændret

Når du er færdig dialogboksen Visual Studio tilføjer referencer og ændrer bestemte konfigurationsfiler. De ønskede ændringer, afhænger af projekttypen. 

 - Se [Hvad er der sket – ASP.NET projekter](http://go.microsoft.com/fwlink/p/?LinkId=513126)for ASP.NET projekter. 
 - Se [Hvad er der sket – ASP.NET 5 projekter](http://go.microsoft.com/fwlink/p/?LinkId=513124)for ASP.NET 5 projekter. 
 - Tjenesteprojekter (web roller og arbejder roller), se [Hvad er der sket – skybaseret tjeneste projekter](http://go.microsoft.com/fwlink/p/?LinkId=516965)til skyen. 
 - Se [Hvad er der sket - WebJob projekter](./storage/vs-storage-webjobs-what-happened.md)for WebJob projekter.

## <a name="next-steps"></a>Næste trin

1. Ved hjælp af kodeeksempler Kom godt i gang som vejledning, oprette typen lagerplads, du vil, og begynd at skrive kode for at få adgang til kontoen lagerplads!

1. Stille spørgsmål og få hjælp
     - [MSDN-Forum: Azure lagerplads](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Azure-lager-teamets Blog](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Lagre i azure.microsoft.com](https://azure.microsoft.com/services/storage/)

     - [Lagerplads dokumentation på azure.microsoft.com](https://azure.microsoft.com/documentation/services/storage/)

