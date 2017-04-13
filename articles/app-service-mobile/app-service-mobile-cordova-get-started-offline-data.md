<properties
    pageTitle="Aktivere offlinesynkronisering for din Azure Mobile-App (Cordova) | Microsoft Azure"
    description="Lær at bruge App Service Mobile-App til cache og synkronisere offline data i dit Cordova program"
    documentationCenter="cordova"
    authors="adrianhall"
    manager="erikre"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-cordova-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Aktivere offlinesynkronisering for din Cordova-mobilappen

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Dette selvstudium introducerer funktionen offlinesynkronisering af Azure Mobile-Apps til Cordova. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse. Ændringer gemmes i en lokal database Når enheden er igen er online, synkroniseres disse ændringer med tjenesten remote.

Dette selvstudium er baseret på Cordova Hurtig start løsningen til Mobile-Apps, som du opretter, når du udfører selvstudiet [Apache Cordova Hurtig start]. I dette selvstudium, vil du opdatere løsningen Hurtig start for at tilføje offline funktioner i Azure Mobile-Apps. Vi også fremhæver offline-specifikke koden i appen.

Hvis du vil vide mere om funktionen offlinesynkronisering, emnet [Offlinesynkronisering Data i Azure Mobile-Apps]. Du kan finde oplysninger om brug af API, skal filen [med vigtige oplysninger] i plug-in'en.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Føje offlinesynkronisering til løsningen Hurtig start

Offlinesynkronisering koden skal føjes til appen. Offlinesynkronisering kræver plug-in'et cordova-sqlite-lager, som automatisk bliver føjet til din app, når plug-in'en til Azure Mobile-Apps er inkluderet i projektet. Hurtig start projektet indeholder begge af disse plug-ins.

1. I Visual Studio Solution Explorer skal du åbne index.js og erstatte følgende kode

        var client,            // Connection to the Azure Mobile App backend
          todoItemTable;      // Reference to a table endpoint on backend

    med denne kode:

        var client,             // Connection to the Azure Mobile App backend
          todoItemTable, syncContext; // Reference to table and sync context

2. Derefter skal du erstatte følgende kode:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    med denne kode:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

        // Note: Requires at least version 2.0.0-beta6 of the Azure Mobile Apps plugin
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              deleted: 'boolean',
              complete: 'boolean'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    De foregående kode tilføjelser initialiseret det lokale lager og definere en lokal tabel, der svarer til de kolonneværdier, der bruges i din Azure tilbage slutningen. (Du ikke behøver at medtage alle kolonneværdier i denne kode).

    Du får en reference til konteksten synkronisering ved at ringe til **getSyncContext**. Konteksten Synkroniser hjælper med at bevare tabelrelationer ved registrering og trykke på ændringer i alle tabeller, der er en klient app er ændret, når **opslagsnål** kaldes.

3. Opdater programmet URL-adressen til din URL-adresse til Mobile-App-programmet på computeren.

4. Derefter skal du erstatte denne kode:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    med denne kode:

        // todoItemTable = client.getTable('todoitem');

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (serverRecord, clientRecord, pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Den foregående kode starter konteksten synkronisering og kalder derefter getSyncTable (i stedet for getTable) for at få en reference til den lokale tabel.

    Denne kode, der bruger den lokale database for alle oprette, læse, opdatere og slette (CRUD) tabel handlinger.

    Dette eksempel udfører enkle fejlhåndtering på synkroniseringskonflikter. Et reelt tal program vil håndtere de forskellige fejl som netværk betingelser, konflikter server og andre. Du kan finde kodeeksempler, [offlinesynkronisering eksempel].

5. Dernæst skal tilføje denne funktion til at udføre den faktiske synkronisering.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Du har besluttet, hvornår til at overføre ændringerne til Mobile-App back-end ved at ringe **push** for **syncContext** objektet bruges af klienten. For eksempel kan du tilføje et opkald til **syncBackend** til en knap hændelseshandler i app som en ny synkroniseringsknappen, eller du kan tilføje opkaldet til funktionen **addItemHandler** til at synkronisere, når der føjes et nyt element.

##<a name="offline-sync-considerations"></a>Overvejelser i forbindelse med offlinesynkronisering

**Push** -metoden af **syncContext** kaldes i stikprøven, kun på app Start i Tilbagekaldsfunktionen til at logge på.  I et reale til computeren, kan du også gøre denne Synkroniser funktionalitet, der udløste manuelt, eller når tilstand af netværk ændres.

Når en hente køres i forhold til en tabel, der afventer lokale opdateringer sporet efter konteksten, udløse hente handlingen automatisk en foregående kontekst opslagsnål. Når du opdaterer, tilføje og fuldfører elementer i dette eksempel kan du udelade eksplicitte **opslagsnål** opkaldet, da det kan være overflødige (første kontrol [vigtigt] for aktuelle funktionsstatus).

Alle poster i tabellen remote todoItem spørges i den angivne kode, men det er også muligt at filtrere poster ved at overføre et forespørgsel-id og -forespørgsel til **opslagsnål**. Yderligere oplysninger finder du i afsnittet *Delvis synkronisering* i [Offline synkronisering af Data i Azure Mobile-Apps].

## <a name="optional-disable-authentication"></a>(Valgfrit) Deaktivere godkendelse

Hvis du ikke allerede har konfigureret godkendelse og ikke vil konfigurere godkendelse før test offlinesynkronisering, skrive en kommentar ud af Tilbagekaldsfunktionen til logon, men lade koden i uncommented Tilbagekaldsfunktionen.

Koden ser sådan ud efter kommentering af linjerne login.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Nu, synkroniseres appen med Azure back end-, når du kører app i stedet for når du logger på.

## <a name="run-the-client-app"></a>Køre appen klient

Med offlinesynkronisering nu aktiveret, kan du nu køre klientprogrammet mindst én gang på hver platform til at udfylde den lokale lager database. Senere, kan du simulere en offline scenarie og ændre dataene i det lokale lager, mens appen er offline.

## <a name="optional-test-the-sync-behavior"></a>(Valgfrit) Teste Synkroniser funktionsmåden

I dette afsnit, skal redigere du klientprojektet for at efterligne et offline scenarie ved hjælp af en ugyldig programmet URL-adresse til dit backend-version. Når du tilføjer eller ændrer dataelementer, vil ændringerne blive opbevares i det lokale lager, men ikke synkroniseres til back end-datalager, indtil forbindelsen er genoprettet.

1. Åbn filen index.js project i Solution Explorer og ændre programmet URL-adressen til at pege på en ugyldig URL-adresse, som følger:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Opdatere Konferencetjenesteudbyderen i index.html, `<meta>` element med det samme ugyldige URL-adresse.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Oprette og køre klient-app, og Bemærk, at en undtagelse er logget på konsollen, når programmet forsøger at synkronisere med back end-efter logon. De nye elementer, du har tilføjet findes kun i det lokale lager, indtil de kan overføres til den mobile backend-version. Klient-app fungerer som om er forbundet til back end-, understøtter alle oprette, læse, opdatere, sletninger (CRUD).

4. Luk app, og genstart den for at bekræfte, at de nye elementer, du har oprettet er bevaret til det lokale lager.

5. (Valgfrit) Brug Visual Studio til at få vist indholdsfortegnelsen Azure SQL-Database for at se, at dataene i back end-databasen ikke er ændret.

    I Visual Studio, skal du åbne **Server Explorer**. Gå til din database i **Azure**->**SQL-databaser**. Højreklik på databasen, og vælg **Åbn i SQL Server Object Explorer**. Nu kan du gå til din SQL-databasetabeller og dens indhold.


## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Valgfrit) Teste genoprettelse til din mobile backend-version

I dette afsnit kan du forbinde appen til den mobile backend-version, som viser appen kommer tilbage til onlinetilstand. Når du logger på, vil data blive synkroniseret til din mobile backend-version.

1. Genåbn index.js og rette programmet URL-adressen til at pege på den korrekte URL-adresse.

2. Genåbn index.html og rette programmet URL-adressen i Konferencetjenesteudbyderen `<meta>` element.

3. Genopbygge og køre klient-app. Programmet forsøger at synkronisere med mobilapp back end-efter logon. Kontrollér, at ingen undtagelser er logget på konsollen fejlfinding.

4. (Valgfrit) Få vist de opdaterede data ved hjælp af SQL Server Object Explorer eller et RESTEN værktøj som Fiddler. Bemærk, at dataene er blevet synkroniseret mellem back end-databasen og det lokale lager.

    Bemærk, at dataene er blevet synkroniseret mellem databasen og det lokale lager og den indeholder de elementer, du har tilføjet, mens din app blev afbrudt.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Synkronisering af offline Data i Azure-Mobilapps]

* [Forside i skyen: offlinesynkronisering i Azure Mobile Services] \(note: videoen er i Mobile-tjenester, men offlinesynkronisering fungerer på samme måde i Azure Mobile-Apps\)

* [Visual Studio-værktøjer til Apache Cordova]

## <a name="next-steps"></a>Næste trin

* Se mere avancerede offlinesynkronisering funktioner som konfliktløsning i [offlinesynkronisering eksempel]
* Se på offlinesynkronisering API reference i [vigtige oplysninger]

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova Hurtig start]: app-service-mobile-cordova-get-started.md
[VIGTIGE OPLYSNINGER]: https://github.com/Azure/azure-mobile-apps-js-client#offline-data-sync-preview
[offlinesynkronisering eksempel]: https://github.com/shrishrirang/azure-mobile-apps-quickstarts/tree/samples/client/cordova/ZUMOAPPNAME
[Synkronisering af offline Data i Azure-Mobilapps]: app-service-mobile-offline-data-sync.md
[Omslag til skyen: Offlinesynkronisering i Azure Mobile-tjenester]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio-værktøjer til Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
