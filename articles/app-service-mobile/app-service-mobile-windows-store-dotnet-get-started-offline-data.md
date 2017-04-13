<properties
    pageTitle="Aktivere offlinesynkronisering for din Universal Windows Platform (UWP) app med Mobile Apps | Azure App Service"
    description="Lær, hvordan du bruger en Azure Mobile-App til cache og synkronisere offline data i din app Universal Windows Platform (UWP)."
    documentationCenter="windows"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-windows-app"></a>Aktivere offlinesynkronisering for din Windows-app

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium viser, hvordan du føjer offline-understøttelse til en Universal Windows Platform (UWP)-app, ved hjælp af en Azure Mobile-App backend. Offlinesynkronisering gør det muligt for slutbrugere mulighed for at interagere med en mobilapp - visning, tilføjer eller ændrer data -, selv når der er ingen netværksforbindelse. Ændringer gemmes i en lokal database. Når enheden er igen er online, synkroniseres disse ændringer med remote back end.

I dette selvstudium skal du opdatere UWP app projektet fra selvstudium [oprette en Windows-app] til at understøtte funktionerne offline under Azure Mobile-Apps. Hvis du ikke bruger hentede Hurtig start server projektet, skal du tilføje data access lokalnummer pakker til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Hvis du vil vide mere om funktionen offlinesynkronisering, emnet [Offlinesynkronisering Data i Azure Mobile-Apps].

## <a name="requirements"></a>Krav

Dette selvstudium kræver følgende forudsætninger:

* Visual Studio 2013 i Windows 8.1 eller nyere.
* [Oprette en Windows-app],[oprette en windows-app]er afsluttet.
* [Azure Mobile tjenester SQLite Store][sqlite store nuget]
* [SQLite for Universal Windows-Platform udvikling](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Opdater klient-app til at understøtte offline funktioner

Azure offline Mobile-App-funktioner gør det muligt at interagere med en lokal database, når du er i et scenarie med offline. Hvis du vil bruge disse funktioner i din app, du har initialiseret [SyncContext] [ synccontext] til en lokal butik. Derefter referere til tabellen via brugergrænsefladen på [IMobileServiceSyncTable][IMobileServiceSyncTable] . SQLite bruges som det lokale lager på enheden.

1. Installere [SQLite runtime til Universal Windows-Platform](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).

2. Åbn pakke NuGet manager for det UWP app-projekt, du har udført i [oprette en Windows-app] selvstudiet i Visual Studio.
    Søge efter og installere pakken **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.

4. Højreklik på **referencer**i Solution Explorer > **Tilføj Reference**  >  **Universal Windows** > **filtypenavne**, derefter aktivere både **SQLite til Universal Windows-Platform** og **Visual C++ 2015 Runtime til Universal Windows-Platform apps**.

    ![Føje SQLite UWP][1]

5. Åbn filen MainPage.xaml.cs og fjern kommentar fra den `#define OFFLINE_SYNC_ENABLED` definition.

6. Tryk på **F5** for at genopbygge og køre klient-app i Visual Studio. Appen fungerer på samme måde som før du har aktiveret offlinesynkronisering. Den lokale database udfyldes nu med data, der kan bruges i et scenarie med offline.

## <a name="update-sync"></a>Opdatere appen til at afbryde forbindelsen fra backend-version

I dette afsnit, skal afbryde du forbindelsen til din Mobile-App backend-version til at simulere en offline situation. Når du tilføjer dataelementer, kan du din undtagelsesbehandler se, at programmet er i offlinetilstand. I denne tilstand nye elementer tilføjes i det lokale lager og vil blive synkroniseret til mobilapp back-end, når opslagsnål næste køres i tilstanden forbundne.

1. Rediger App.xaml.cs i det delte projekt. Skrive en kommentar ud initialiseringen af **MobileServiceClient** og tilføje følgende linje, som bruger en ugyldig mobilapp URL-adresse:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Du kan også vise offline funktionsmåde ved at deaktivere wifi og mobilnetværk på enheden eller bruger fly tilstand.

2. Tryk på **F5** for at oprette og køre appen. Bemærk din synkroniseringen mislykkedes ved opdatering, når appen startet.

3. Angive nye elementer, og Bemærk, at push mislykkes med statussen [CancelledByNetworkError] , hver gang du klikker på **Gem**. De nye opgaveliste elementer findes dog i det lokale lager, indtil de kan installeres mobilapp back-end.  Hvis du skjule disse undtagelser i en fremstilling app fungerer klient-app som om den stadig er tilsluttet mobilapp back-end.

4. Luk app, og genstart den for at bekræfte, at de nye elementer, du har oprettet er bevaret til det lokale lager.

5. (Valgfrit) I Visual Studio, skal du åbne **Server Explorer**. Gå til din database i **Azure**->**SQL-databaser**. Højreklik på databasen, og vælg **Åbn i SQL Server Object Explorer**. Nu kan du gå til din SQL-databasetabeller og dens indhold. Kontrollér, at dataene i back end-databasen ikke er ændret.

6. (Valgfrit) Bruge et RESTEN værktøj som Fiddler eller Postman for at forespørge på din mobile backend-version, ved hjælp af en GET-forespørgsel i formen `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Opdatere appen for at forbinde dine Mobile-App backend-version

I dette afsnit, skal du genoprette forbindelsen appen til mobilapp back-end. Disse ændringer simulere en netværk genoprettelse på appen.

Når du første gang programmet, den `OnNavigatedTo` begivenhed handler opkald `InitLocalStoreAsync`. Denne metode kalder `SyncAsync` til at synkronisere dine lokale lager med back end-databasen. Programmet forsøger at synkronisere ved start.

1. Åbn App.xaml.cs i det delte projekt, og fjern kommentar fra din tidligere initialisering af `MobileServiceClient` at bruge den korrekte mobilapp URL-adressen.

2. Tryk på **F5** for at genopbygge og køre appen. Appen synkroniserer dine lokale ændringer med Azure Mobile-App back end-og ved hjælp af handlingerne opslagsnål og hente når den `OnNavigatedTo` hændelseshandler udfører.

3. (Valgfrit) Få vist de opdaterede data ved hjælp af SQL Server Object Explorer eller et RESTEN værktøj som Fiddler. Meddelelse om dataene er blevet synkroniseret mellem Azure Mobile-App back end-database og det lokale lager.

4. I appen skal du klikke på afkrydsningsfeltet ud for nogle elementer, der skal fuldføres i det lokale lager.

  `UpdateCheckedTodoItem`opkald `SyncAsync` til synkroniseringen hvert element med Mobile-App back end. `SyncAsync`kalder både opslagsnål og hente. Men, **Når du udfører en hente mod en tabel, som kunden har foretaget ændringer, en opslagsnål altid køres automatisk**. Denne funktionalitet sikrer, at alle tabeller i det lokale lager sammen med relationer forbliver ensartet. Dette problem kan medføre en uventet opslagsnål.  Du kan finde flere oplysninger om problemet, [Offline synkronisering af Data i Azure Mobile-Apps].


##<a name="api-summary"></a>Oversigt over API

For at understøtte offline funktionerne i mobile-tjenester, vi bruges grænsefladen [IMobileServiceSyncTable] og initialiseret [MobileServiceClient.SyncContext] [ synccontext] med en lokal SQLite database. Når du er offline, fungerer handlingerne normal CRUD til Mobile-Apps, som hvis appen er stadig forbundet, mens handlingerne sker på det lokale lager. De følgende metoder bruges til at synkronisere det lokale lager med serveren:

*  **[PushAsync]** Da denne metode er medlem af [IMobileServicesSyncContext], publiceres ændringer på tværs af alle tabeller i backend-version. Der sendes kun poster med lokale ændringer til serveren.

* **[PullAsync] ** 
   en hente startes fra en [IMobileServiceSyncTable]. Når der er registrerede ændringer i tabellen, kører en implicit push for at sikre, at alle tabeller i det lokale lager sammen med relationer forbliver ensartet. Parameteren *pushOtherTables* kontrollerer, om andre tabeller i konteksten publiceres i en implicit opslagsnål. *Forespørgselsparameteren* tager en [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] 
   eller OData-forespørgselsstreng til at filtrere de returnerede data. Parameteren *queryId* bruges til at definere delvis synkronisering. Du kan finde yderligere oplysninger finder  [Offline synkronisering af Data i Azure Mobile-Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

* **[PurgeAsync]** Denne metode til at fjerne forældede data fra det lokale lager skal med jævne mellemrum ringe til din app. Bruge parameteren *tvinge* , når du har brug at slette de ændringer, der endnu ikke er synkroniseret.

Du kan finde flere oplysninger om disse begreber, [Offline synkronisering af Data i Azure Mobile-Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Flere oplysninger

Følgende emner indeholder flere baggrundsoplysninger om funktionen offlinesynkronisering af Mobile-Apps:

* [Synkronisering af offline Data i Azure-Mobilapps]
* [Azure-Mobilapps .NET SDK Sådan][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Synkronisering af offline Data i Azure-Mobilapps]: app-service-mobile-offline-data-sync.md
[oprette en windows-app]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
