<properties
    pageTitle="Aktivere offlinesynkronisering for din Azure Mobile-App (Android)"
    description="Lær at bruge App Service Mobile-Apps til cache og synkronisere offline data i programmet på Android"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Aktivere offlinesynkronisering for din Android-mobilappen

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium viser funktionen offlinesynkronisering af Azure Mobile-Apps til Android. Offlinesynkronisering gør det muligt for slutbrugere mulighed for at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse. Ændringer gemmes i en lokal database. Når enheden er igen er online, synkroniseres disse ændringer med remote back end.

Hvis dette er din første oplevelse med Azure Mobile-Apps, skal du først gennemførelse af selvstudiet, [oprette en Android-App]. Hvis du ikke bruger hentede Hurtig start server projektet, skal du tilføje data access lokalnummer pakker til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Hvis du vil vide mere om funktionen offlinesynkronisering, emnet [Offlinesynkronisering Data i Azure Mobile-Apps].

## <a name="update-the-app-to-support-offline-sync"></a>Opdatere appen for at understøtte offlinesynkronisering

Med offlinesynkronisering, kan du læse til og skrive fra en *Synkroniser tabel* (ved hjælp af *IMobileServiceSyncTable* interface), som er en del af en **SQLite** database på din enhed.

Hvis du vil push og trække ændringer mellem enheden og Azure Mobile-tjenester, skal bruge du en *synkroniseringskontekst* (*MobileServiceClient.SyncContext*), som du har initialiseret med den lokale database til at gemme data lokalt.

1. I `TodoActivity.java`, kommentar ud af den eksisterende definition af `mToDoTable` og fjern kommentar fra Synkroniser tabel versionen:

        private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. I den `onCreate` metode, kommentar ud eksisterende initialiseringen af `mToDoTable` og fjern kommentar fra denne definition:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. I `refreshItemsFromTable` kommentar ud definitionen af `results` og fjern kommentar fra denne definition:

        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Fjerne definitionen af `refreshItemsFromMobileServiceTable`.

5. Fjern kommentar fra definitionen af `refreshItemsFromMobileServiceTableSyncTable`:

        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }

6. Fjern kommentar fra definitionen af `sync`:

        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Teste appen

I dette afsnit, du tester funktionsmåden med WiFi på og deaktiver derefter Wi-Fi til at oprette en offline scenarie.

Når du tilføjer dataelementer, er de opbevares i det lokale lager SQLite, men ikke synkroniseret til mobile service, indtil du trykker på knappen **Opdater** . Andre apps kan have forskellige krav om når data skal synkroniseres, men henblik demo selvstudiet har brugeren eksplicit anmoder om det.

Når du trykker på knappen, starter en ny opgave i baggrunden. Først flytter alle ændringer i det lokale lager ved hjælp af synkroniseringskontekst og derefter trækker alle ændret data fra Azure til den lokale tabel.

### <a name="offline-testing"></a>Test af offline

1. Placer enhed eller simulator i *Fly tilstand*. Dette opretter en offline scenarie.

2. Tilføje nogle *Opgaveliste* elementer eller markere nogle elementer som fuldført. Afslut enhed eller simulator (eller gennemtvinge lukker appen), og genstart. Kontrollér, at dine ændringer på enheden er blevet bevares, fordi de afholdes på det lokale lager SQLite.

3. Få vist indholdet af tabellen Azure *TodoItem* enten med en SQL-værktøjet som *SQL Server Management Studio*eller en RESTEN klient som *Fiddler* eller *Postman*. Kontrollér, at de nye elementer der _ikke_ er synkroniseret til serveren

    + En Node.js back-end, gå til [Azure-portalen](https://portal.azure.com/), og i Mobile-App backend-version ved at klikke **Nemt tabeller** > **TodoItem** til at få vist indholdet af den `TodoItem` tabel.
    + Få vist indholdsfortegnelse enten med en SQL-værktøjet såsom *SQL Server Management Studio*eller en RESTEN klient som *Fiddler* eller *Postman*for en .NET back-end.

4. Aktivere WiFi i enhed eller simulator. Derefter skal du trykke på knappen **Opdater** .

5. Få vist dataene TodoItem igen på portalen Azure. Nye og ændrede TodoItems vises nu.

## <a name="additional-resources"></a>Yderligere ressourcer

* [Synkronisering af offline Data i Azure-Mobilapps]

* [Forside i skyen: offlinesynkronisering i Azure Mobile Services] \(note: videoen er i Mobile-tjenester, men offlinesynkronisering fungerer på samme måde i Azure Mobile-Apps\)


<!-- URLs. -->

[Synkronisering af offline Data i Azure-Mobilapps]: app-service-mobile-offline-data-sync.md

[Oprette en Android-App]: app-service-mobile-android-get-started.md

[Omslag til skyen: Offlinesynkronisering i Azure Mobile-tjenester]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

