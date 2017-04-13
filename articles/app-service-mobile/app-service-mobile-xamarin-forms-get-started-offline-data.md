<properties
    pageTitle="Aktivere offlinesynkronisering for din Azure Mobile-App (Xamarin.Forms) | Microsoft Azure"
    description="Lær at bruge App Service Mobile-App til cache og synkronisere offline data i dit Xamarin.Forms program"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Aktivere offlinesynkronisering for din Xamarin.Forms-mobilappen

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Oversigt
Dette selvstudium introducerer funktionen offlinesynkronisering af Azure Mobile-Apps til Xamarin.Forms. Offlinesynkronisering gør det muligt for slutbrugere mulighed for at interagere med en mobilapp - visning, tilføjer eller ændrer data –, selv når der er ingen netværksforbindelse. Ændringer gemmes i en lokal database. Når enheden er igen er online, synkroniseres disse ændringer med tjenesten remote.

Dette selvstudium er baseret på Xamarin.Forms Hurtig start løsningen til Mobile-Apps, som du opretter, når du udfører selvstudiet [Opret en Xamarin iOS-app]. Hurtig start løsningen til Xamarin.Forms indeholder koden for at understøtte offlinesynkronisering, som kun skal være aktiveret. I dette selvstudium skal du opdatere løsningen Hurtig start for at slå funktionerne offline under Azure Mobile-Apps. Vi også fremhæve offline-specifikke koden i appen. Hvis du ikke bruge løsningen hentede Hurtig start, skal du tilføje data access lokalnummer pakker til projektet. Se [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps]kan finde flere oplysninger om server lokalnummer pakker[1].

Hvis du vil vide mere om funktionen offlinesynkronisering skal du i emnet [Offlinesynkronisering Data i Azure Mobile-Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Aktivere offlinesynkronisering funktionaliteten i Hurtig start løsningen

Offlinesynkronisering-kode er inkluderet i projektet ved hjælp af C# preprocessor-direktiver. Når den **OFFLINE\_SYNKRONISER\_aktiveret** symbol er defineret, disse kodestier er inkluderet i den nyeste version. Til Windows-apps, skal du også installere SQLite platform.

1. Højreklik på løsningen i Visual Studio > **Administrere NuGet pakker til løsning...**, og derefter søge efter og installere pakken **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet for alle projekter i løsningen.

2. I Solution Explorer Åbn filen TodoItemManager.cs fra projektet med **bærbare** i det navn, som er flytbare klassebibliotek projekt, derefter Fjern kommentar fra følgende preprocessor-direktiv:

        #define OFFLINE_SYNC_ENABLED

4. (Valgfrit) Installere en af følgende SQLite runtime-pakker for at understøtte Windows enheder:

    * **Windows 8.1 Runtime:** Installere [SQLite til Windows 8.1][3].
    * **Windows Phone 8.1:** Installere [SQLite til Windows Phone 8.1][4].
    * **Universal Windows-Platform** Installere [SQLite for Universal Windows Universal][5].

    Selvom på Hurtig start ikke indeholder et Universal Windows-projekt, understøttes Universal Windows-platform med Xamarin formularer.

5. (Valgfrit) Højreklik på **referencer**i hvert Windows app-projekt, > **Tilføj Reference**, Udvid mappen **Windows** > **filtypenavne**.
    Aktivere den relevante **SQLite til Windows** SDK sammen med **Visual C++ 2013 Runtime til Windows** SDK.
    SQLite SDK navnene kan variere en smule med hver Windows-platform.

## <a name="review-the-client-sync-code"></a>Gennemse koden client synkronisering

Her er en kort oversigt over, hvad er allerede følger den selvstudium kode i den `#if OFFLINE_SYNC_ENABLED` direktiver. Funktionen offlinesynkronisering er i projektfilen TodoItemManager.cs i bærbare klassebibliotek projektet. En oversigt over funktionen, under [Offline Data synkronisere i Azure Mobile-Apps][2].

* Før en tabel handlinger kan udføres, skal være initialiseret det lokale lager. Lokale lager databasen er initialiseret i klassekonstruktør **TodoItemManager** ved hjælp af følgende kode:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Denne kode opretter en ny lokal SQLite database ved hjælp af klassen **MobileServiceSQLiteStore** .

    Metoden **DefineTable** opretter en tabel i det lokale lager, der svarer til felterne i den angivne type.  Typen behøver ikke at medtage alle de kolonner, der findes i den eksterne database. Det er muligt at gemme et undersæt af kolonner.

* Feltet **todoTable** i **TodoItemManager** er af typen **IMobileServiceSyncTable** i stedet for **IMobileServiceTable**. Denne klasse, der bruger den lokale database for alle oprette, læse, opdatere og slette (CRUD) tabel handlinger. Du beslutter, at når disse ændringer publiceres i Mobile-App back-end ved at ringe til **PushAsync** på **IMobileServiceSyncContext**. Konteksten Synkroniser hjælper med at bevare tabelrelationer ved registrering og trykke på ændringer i alle tabeller, der er en klient app er ændret, når **PushAsync** kaldes.

    Den følgende **SyncAsync** metode kaldes at synkronisere med Mobile-App back end:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Dette eksempel bruger simple fejlhåndtering med standardprogrammet Synkroniser håndtering. Et reelt tal program vil håndtere de forskellige fejl som netværk betingelser og server konflikter ved hjælp af en brugerdefineret **IMobileServiceSyncHandler** implementering.

## <a name="offline-sync-considerations"></a>Overvejelser i forbindelse med offlinesynkronisering

I eksemplet kaldes metoden **SyncAsync** kun på Start, og når der anmodes om en synkronisering.  For at starte en synkronisering i en Android- eller iOS-app, skal du trække på listen elementer. for Windows skal du bruge knappen **Synkroniser** . I et reale til computeren, kan du også gøre synkronisering udløser, når tilstanden netværk ændres.

Når en hente køres i forhold til en tabel, der indeholder afventende registreres lokale opdateringer i den kontekst, adskille handlingen automatisk udløsere en foregående kontekst opslagsnål. Når opdatere, tilføje, og Fuldfør elementer i dette eksempel, kan du udelade eksplicitte **PushAsync** opkaldet.

Alle poster i tabellen remote TodoItem spørges i den angivne kode, men det er også muligt at filtrere poster ved at overføre et forespørgsel-id og -forespørgsel til **PushAsync**. Du kan finde flere oplysninger i afsnittet *Delvis synkronisering* i [Offline synkronisering af Data i Azure Mobile-Apps][2].

## <a name="run-the-client-app"></a>Køre appen klient

Køre klientprogrammet mindst én gang på hver platform til at udfylde lokale lager databasen med offlinesynkronisering nu aktiveret. Senere simulere en offline scenarie og ændre dataene i det lokale lager, mens appen er offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Opdatere Synkroniser funktionsmåden for klient-app

Ændre klientprojektet for at efterligne et offline scenarie ved hjælp af en ugyldig programmet URL-adresse til dit backend-version i dette afsnit. Alternativt kan du slå fra netværksforbindelser ved at flytte din enhed til "Fly mode."  Når du tilføjer eller ændrer dataelementer, som findes i det lokale lager disse ændringer, men ikke synkroniseres til back end-datalager, indtil forbindelsen er genoprettet.

1. Åbn filen Constants.cs project fra **bærbare** projektet i Solution Explorer, og Skift værdien for `ApplicationURL` til at pege på en ugyldig URL-adresse:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";

2. Åbn filen TodoItemManager.cs fra **bærbare** projektet, og derefter føje en **fange** for klassen base **undtagelse** til blokering **prøve... produkter** i **SyncAsync**. Denne **fange** blok skriver Undtagelsesmeddelelsen til konsollen, på følgende måde:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }

3. Oprette og køre klient-app.  Tilføj nogle nye elementer. Bemærk, at en undtagelse er logget på konsollen for hvert forsøg på at synkronisere med back end. Disse nye elementer findes kun i det lokale lager, indtil de kan overføres til den mobile backend-version. Klient-app fungerer som om den er tilsluttet til back end-, understøtter alle oprette, læse, opdatere, sletninger (CRUD).

4. Luk app, og genstart den for at bekræfte, at de nye elementer, du har oprettet er bevaret til det lokale lager.

5. (Valgfrit) Brug Visual Studio til at få vist indholdsfortegnelsen Azure SQL-Database for at se, at dataene i back end-databasen ikke er ændret.

    I Visual Studio, skal du åbne **Server Explorer**. Gå til din database i **Azure**->**SQL-databaser**. Højreklik på databasen, og vælg **Åbn i SQL Server Object Explorer**. Nu kan du gå til din SQL-databasetabeller og dens indhold.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Opdatere appen klienten på computeren for at forbinde dine mobile backend-version

Genopret appen til den mobile backend-version, som viser appen kommer tilbage til onlinetilstand i dette afsnit. Når du udfører Opdater bevægelse, synkroniseres data til din mobile backend-version.

1. Genåbn Constants.cs. Rette den `applicationURL` til at pege på den korrekte URL-adresse.

2. Genopbygge og køre klient-app. Programmet forsøger at synkronisere med mobilapp back end-der er blevet startet. Kontrollér, at ingen undtagelser er logget på konsollen fejlfinding.

3. (Valgfrit) Få vist de opdaterede data ved hjælp af SQL Server Object Explorer eller et RESTEN værktøj som Fiddler eller [Postman][6]. Bemærk, at dataene er blevet synkroniseret mellem back end-databasen og det lokale lager.

    Bemærk, at dataene er blevet synkroniseret mellem databasen og det lokale lager og den indeholder de elementer, du har tilføjet, mens din app blev afbrudt.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Synkronisering af offline Data i Azure-Mobilapps][2]
* [Azure-Mobilapps .NET SDK Sådan][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md