<properties 
   pageTitle="Tilføje Mobile-tjenester ved hjælp af forbundne tjenester i Visual Studio | Microsoft Azure"
   description="Tilføje Mobile-tjenester ved hjælp af dialogboksen Visual Studio tilføje forbundne tjenester"
   services="visual-studio-online"
   documentationCenter="na"
   authors="mlhoop"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="mlearned" />

# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Tilføje Mobile-tjenester ved hjælp af Visual Studio forbundne tjenester

Med Visual Studio-2015, kan du oprette forbindelse til Azure Mobile-tjenester ved hjælp af dialogboksen **Tilføj forbindelse tjeneste** . Du kan oprette forbindelse fra en hvilken som helst C#-klienten app, en hvilken som helst JavaScript app eller på tværs af platforme Cordova app. Når du opretter forbindelse, kan du oprette og få adgang til data, oprette brugerdefinerede API'er og planlagte opgaver eller tilføje understøttelse af pushmeddelelser.  Handlingen forbundne tjenester tilføjer alle relevante referencer og forbindelse kode. Du kan også drage fordel af indbygget understøttelse af godkendelse med en række populære identitet farveskemaer, som Azure AD Facebook, Twitter og Microsoft Accounts.

## <a name="supported-project-types"></a>Understøttede projekttyper

>[AZURE.NOTE] I Visual Studio-2015 understøttes tilføje Azure Mobile tjenester til en Windows Universal (Windows 10) projekter ved hjælp af dialogboksen Tilføj forbundne tjenester ikke. Du kan tilføje Azure Mobile-tjenester ved at installere de relevante pakker ved hjælp af NuGet pakke Manager for dit projekt.

Du kan bruge dialogboksen forbundne tjenester for at oprette forbindelse til Azure Mobile Services i følgende projekttyper.

- .NET Windows 8.1 Store, telefon og Universal App projekter

- JavaScript Windows 8.1 Store, telefon og Universal App projekter

- Projekter, der er oprettet ved hjælp af Visual Studio Tools til Apache Cordova


## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Oprette forbindelse til Azure Mobile-tjenester ved hjælp af dialogboksen Tilføj forbundne tjenester

1. Sørg for, at du har en Azure-konto. Hvis du ikke har en Azure-konto, kan du tilmelde dig en [gratis prøveversion](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Åbne dialogboksen **Tilføj forbundne tjenester** .
 - Åbn dit projekt i Visual Studio .NET Apps, skal, åbne genvejsmenuen for noden **referencer** i Solution Explorer, og vælg derefter **Tilføj forbindelse tjeneste**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Åbne projektet i Visual Studio for Apache Cordova app projekter, Åbn genvejsmenuen for projektnoden i Solution Explorer, og klik på **Tilføj forbindelse tjeneste**.

1. Vælg **Azure Mobile-tjenester**i dialogboksen **Tilføj forbindelse tjeneste** , og vælg derefter knappen **Konfigurer** . Du muligvis bedt om at logge på Azure, hvis du ikke allerede har gjort det.

    ![Tilføje en Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. Vælg en eksisterende mobile tjeneste i dialogboksen **Azure Mobile-tjenester** , hvis du har en. Hvis du vil oprette en ny Azure mobile tjeneste, skal du følge fremgangsmåden nedenfor for at gøre dette. Ellers skal du gå til næste trin.

    Sådan oprettes en ny mobile service-konto:
    1. Vælg **Opret Service **linket nederst i dialogboksen.
        ![Tilføjelse af nye mobile forbundne tjenester](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. I dialogboksen **Opret Mobile Service** kan du vælge en JavaScript back end-mobile service, eller en .NET back end-mobile service fra rullelisten **Runtime** . 
  
        ![Oprette en mobile service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        En JavaScript back end-tjeneste er enkle og effektive. Hvis du opretter en JavaScript back end-mobile service, serversiden JavaScript-kode er gemt i skyen, men du kan redigere serverscripts ved hjælp af Server Explorer eller administrationsportalen Azure. 

        .NET back end-mobile service får du den fulde styrke og fleksibilitet Web API og enhed Framework. Hvis du opretter en .NET back end-mobile service, er et projekt oprettet for dig og føjes til din løsning. 

    1. Vælg det **område** , hvor du vil tjenesten mobile, og derefter angive et brugernavn og adgangskode til serveren.
 
    1. Når du har angivet alle de nødvendige oplysninger, skal du vælge knappen **Opret** til at oprette den mobile service.
    2. Den nye mobile tjeneste skal vises på listen tjeneste i dialogboksen **Azure Mobile tjenester** . Vælg den nye mobile tjeneste på listen, og vælg derefter knappen **Tilføj** for at føje tjenesten til projektet.
    

1. Gennemse den komme i gang side, der vises, og find ud af, hvordan projektet blev ændret. En introduktion side vises i din browser, når du tilføjer en forbundne tjeneste. Du kan gennemgå de foreslåede næste trin og kodeeksempler, eller du kan skifte til siden Hvad skete der med at se, hvilke referencer er blevet føjet til projektet, og hvordan filerne kode og konfiguration er blevet ændret.

1. Brug af eksemplerne som en vejledning, Begynd at skrive-koden for at få adgang til din mobile service!

## <a name="how-your-project-is-modified"></a>Hvordan dit projekt er ændret

Hvordan Visual Studio ændrer dit projekt, afhænger af projekttypen. C#-klienten Apps, skal du se [Hvad er der sket – C# projekter](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript-klienten Apps, skal du se [Hvad er der sket – JavaScript projekter](http://go.microsoft.com/fwlink/p/?LinkId=513120). Cordova Apps, skal du se [Hvad er der sket – Cordova projekter](http://go.microsoft.com/fwlink/p/?LinkId=513116).


##<a name="next-steps"></a>Næste trin

Stille spørgsmål og få hjælp: 

 - [MSDN-Forum: Azure Mobile-tjenester](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Azure Mobile tjenester på Microsoft Azure-teamets Blog](https://azure.microsoft.com/blog/topics/mobile/)

 - [Azure Mobile tjenester på azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)

 - [Azure Mobile tjenester dokumentationen på azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)



