<properties
    pageTitle="Aktivere offlinesynkronisering for din Azure Mobile-App (Xamarin Android)"
    description="Lær at bruge App Service Mobile-App til cache og synkronisere offline data i dit Xamarin Android-program"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Aktivere offlinesynkronisering for din Xamarin.Android-mobilappen

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium introducerer funktionen offlinesynkronisering af Azure Mobile-Apps til Xamarin.Android. Offlinesynkronisering gør det muligt for slutbrugere mulighed for at interagere med en mobilapp - visning, tilføjer eller ændrer data –, selv når der er ingen netværksforbindelse. Ændringer gemmes i en lokal database.
Når enheden er igen er online, synkroniseres disse ændringer med tjenesten remote.

I dette selvstudium skal du opdatere klientprojektet fra selvstudium [oprette en Xamarin Android-app] til at understøtte funktionerne offline under Azure Mobile-Apps. Hvis du ikke bruger hentede Startvejledning server projektet, skal du tilføje data access lokalnummer pakker til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Hvis du vil vide mere om funktionen offlinesynkronisering, emnet [Offlinesynkronisering Data i Azure Mobile-Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Opdater klient-app til at understøtte offline funktioner

Azure offline Mobile-App-funktioner gør det muligt at interagere med en lokal database, når du er i et scenarie med offline. Hvis du vil bruge disse funktioner i din app, du har initialiseret en [SyncContext] til en lokal butik. Derefter referere til tabellen via brugergrænsefladen på [IMobileServiceSyncTable] [IMobileServiceSyncTable]. SQLite bruges som det lokale lager på enheden.

1. Åbn pakke NuGet manager i det projekt, du har udført i [oprette en Xamarin Android app] selvstudiet i Visual Studio.  Søge efter og installere pakken **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet.

2. Åbn filen ToDoActivity.cs og fjern kommentar fra den `#define OFFLINE_SYNC_ENABLED` definition.

3. Tryk på **F5** for at genopbygge og køre klient-app i Visual Studio. Appen fungerer på samme måde som før du har aktiveret offlinesynkronisering. Den lokale database udfyldes nu med data, der kan bruges i et scenarie med offline.

## <a name="update-sync"></a>Opdatere appen til at afbryde forbindelsen fra backend-version

I dette afsnit, skal afbryde du forbindelsen til din Mobile-App backend-version til at simulere en offline situation. Når du tilføjer dataelementer, kan du din undtagelsesbehandler se, at programmet er i offlinetilstand. I denne tilstand nye elementer tilføjes i det lokale lager og er synkroniseret til mobilapp backend-version, når en opslagsnål køres i tilstanden forbundne.

1. Rediger ToDoActivity.cs i det delte projekt. Ændre **applicationURL** til at pege på en ugyldig URL-adresse:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Du kan også vise offline funktionsmåde ved at deaktivere wifi og mobilnetværk på enheden eller ved hjælp af fly tilstand.

2. Tryk på **F5** for at oprette og køre appen. Bemærk din synkroniseringen mislykkedes ved opdatering, når appen startet.

3. Angive nye elementer, og Bemærk, at push mislykkes med statussen [CancelledByNetworkError], hver gang du klikker på **Gem**. De nye opgaveliste elementer findes dog i det lokale lager, indtil de kan installeres mobilapp back-end.  Hvis du skjule disse undtagelser i en fremstilling app fungerer klient-app som om den stadig er tilsluttet mobilapp back-end.

4. Luk app, og genstart den for at bekræfte, at de nye elementer, du har oprettet er bevaret til det lokale lager.

5. (Valgfrit) I Visual Studio, skal du åbne **Server Explorer**. Gå til din database i **Azure**->**SQL-databaser**. Højreklik på databasen, og vælg **Åbn i SQL Server Object Explorer**. Nu kan du gå til din SQL-databasetabeller og dens indhold. Kontrollér, at dataene i back end-databasen ikke er ændret.

6. (Valgfrit) Bruge et RESTEN værktøj som Fiddler eller Postman for at forespørge på din mobile backend-version, ved hjælp af en GET-forespørgsel i formularen `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Opdatere appen for at forbinde dine Mobile-App backend-version

Genopret appen til mobilapp back-end i dette afsnit. Når du første gang programmet, den `OnCreate` begivenhed handler opkald `OnRefreshItemsSelected`. Denne metode kalder `SyncAsync` til at synkronisere dine lokale lager med back end-databasen.

1. Åbn ToDoActivity.cs i det delte projekt, og gendanne din ændring af egenskaben **applicationURL** .

2. Tryk på **F5** for at genopbygge og køre appen. Appen synkroniserer dine lokale ændringer med Azure Mobile-App back end-og ved hjælp af handlingerne opslagsnål og hente når den `OnRefreshItemsSelected` metode udfører.

3. (Valgfrit) Få vist de opdaterede data ved hjælp af SQL Server Object Explorer eller et RESTEN værktøj som Fiddler. Meddelelse om dataene er blevet synkroniseret mellem Azure Mobile-App back end-database og det lokale lager.

4. I appen skal du klikke på afkrydsningsfeltet ud for nogle elementer, der skal fuldføres i det lokale lager.

  `CheckItem`opkald `SyncAsync` til synkroniseringen hvert element med Mobile-App back end. `SyncAsync`kalder både opslagsnål og hente. **Når du udfører en hente mod en tabel, som kunden har foretaget ændringer, en opslagsnål altid køres automatisk**. Dette sikrer, at alle tabeller i det lokale lager sammen med relationer forbliver ensartet. Dette problem kan medføre en uventet opslagsnål. Du kan finde flere oplysninger om problemet, [Offline synkronisering af Data i Azure Mobile-Apps].

## <a name="review-the-client-sync-code"></a>Gennemse koden client synkronisering

Xamarin klientprojektet, som du hentede, når du har fuldført selvstudiet [oprette en Xamarin Android app] allerede indeholder kode understøttende offlinesynkronisering ved hjælp af en lokal SQLite database. Her er en kort oversigt over, hvad findes allerede på selvstudium c ode. Du kan finde en oversigt over funktionen, [Offline synkronisering af Data i Azure Mobile-Apps].

* Før en tabel handlinger kan udføres, skal være initialiseret det lokale lager. Lokale lager databasen er initialiseret når `ToDoActivity.OnCreate()` udfører `ToDoActivity.InitLocalStoreAsync()`. Denne metode opretter en lokal SQLite database ved hjælp af den `MobileServiceSQLiteStore` klasse, der er angivet af Azure Mobile-Apps klienten SDK.

    Den `DefineTable` metode opretter en tabel i det lokale lager, der svarer til felterne i den angivne type `ToDoItem` i dette tilfælde. Typen behøver ikke at medtage alle de kolonner, der findes i den eksterne database. Det er muligt at gemme blot et undersæt af kolonner.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }


* Den `toDoTable` medlem af `ToDoActivity` , er den `IMobileServiceSyncTable` Skriv i stedet for `IMobileServiceTable`. IMobileServiceSyncTable omdirigerer alle oprette, læse, opdatere og slette (CRUD) tabel handlinger til den lokale lager database.

    Du beslutter, at når ændringer der overføres til Azure Mobile-App back-end ved at ringe til `IMobileServiceSyncContext.PushAsync()`. Konteksten Synkroniser hjælper med at bevare tabelrelationer ved registrering og trykke på ændringer i alle tabeller, der er en klient-app har ændret hvornår `PushAsync` hedder.

    De angivne kode opkald `ToDoActivity.SyncAsync()` til synkronisering, når listen todoitem opdateres eller en todoitem tilføjes eller afsluttet. Kode synkroniserer efter hver lokal ændring.

    I den angivne kode alle poster i eksterne `TodoItem` tabel spørges, men det er også muligt at filtrere poster ved at overføre en forespørgsels-id'et og forespørge til `PushAsync`. Yderligere oplysninger finder du i afsnittet *Delvis synkronisering* i [Offline synkronisering af Data i Azure Mobile-Apps].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Yderligere ressourcer

* [Synkronisering af offline Data i Azure-Mobilapps]
* [Azure-Mobilapps .NET SDK Sådan][8]

<!-- URLs. -->
[Oprette en Xamarin Android-app]: ../app-service-mobile-xamarin-android-get-started.md
[Synkronisering af offline Data i Azure-Mobilapps]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Oprette en Xamarin Android-app]: app-service-mobile-xamarin-android-get-started.md
[Synkronisering af offline Data i Azure-Mobilapps]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
