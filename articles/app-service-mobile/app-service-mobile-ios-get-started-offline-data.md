<properties
    pageTitle="Aktivere offlinesynkronisering for din Azure Mobile-App (iOS)"
    description="Lær at bruge App Service Mobile-Apps til cache og synkronisere offline data i dit iOS-program"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>Aktivere offlinesynkronisering for din iOS-mobilappen

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Oversigt

Dette selvstudium viser funktionen offlinesynkronisering af Azure Mobile-Apps til iOS. Offlinesynkronisering gør det muligt for slutbrugere til at interagere med en mobilapp&mdash;visning, tilføjelse eller redigering af data&mdash;selv, når der er ingen netværksforbindelse. Ændringer gemmes i en lokal database Når enheden er igen er online, synkroniseres disse ændringer med remote back end.

Hvis dette er din første oplevelse med Azure Mobile-Apps, skal du først gennemførelse af selvstudiet, [oprette en iOS-App]. Hvis du ikke bruger hentede Hurtig start server projektet, skal du tilføje data access lokalnummer pakker til projektet. Du kan finde flere oplysninger om server lokalnummer pakker, under [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Hvis du vil vide mere om funktionen offlinesynkronisering, emnet [Offlinesynkronisering Data i Azure Mobile-Apps].

## <a name="review-sync"></a>Gennemse koden client synkronisering

Klientprojektet, du har hentet i dette selvstudium, [oprette en iOS App] allerede indeholder kode understøttende offlinesynkronisering ved hjælp af en lokal database Core Data ud. Dette afsnit får du en oversigt over, hvad er allerede følger den selvstudium kode. Du kan finde en oversigt over funktionen, [Offline synkronisering af Data i Azure Mobile-Apps].

Offline data synkroniseringsfunktion synkronisering af Azure Mobile-Apps gør det muligt for slutbrugere mulighed for at interagere med en lokal database, når netværket ikke er tilgængelig. Hvis du vil bruge disse funktioner i din app, du har initialiseret konteksten synkronisering af `MSClient` og henviser til en lokal butik. Derefter henvise til tabellen gennem den `MSSyncTable` interface.

1. Bemærk typer medlemmet i **QSTodoService.m** (mål-C) eller **ToDoTableViewController.swift** (Swift), `syncTable` er `MSSyncTable`. Offlinesynkronisering bruger denne Synkroniser tabel grænseflade i stedet for `MSTable`. Når en Synkroniser tabel bruges, gå til det lokale lager alle handlinger, og der kun synkroniseres med remote back end-med eksplicitte push, og træk handlinger.

    For at få en reference til en Synkroniser tabel skal du bruge metoden `syncTableWithName` på `MSClient`. Hvis du vil fjerne offlinesynkronisering funktionalitet, bruge `tableWithName` i stedet.

2. Før en tabel handlinger kan udføres, skal være initialiseret det lokale lager. Her er den relevante kode. 
    
    **Mål-C**:
    
    I den `QSTodoService.init` metode:
    
    
            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
    
    
    **Swift**:
    
    I den `ToDoTableViewController.viewDidLoad` metode:
    
    
            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
    

    Dette opretter en lokal butik hjælp af brugergrænsefladen i `MSCoreDataStore`, som er angivet i Mobile-Apps SDK. Du kan i stedet for en Angiv et andet lokale lager ved at implementere den `MSSyncContextDataSource` protocol. 
    
    Desuden den første parameter i `MSSyncContext` bruges til at angive en konflikt handler. Da vi er gået `nil`, vi får standard konflikt handler, som mislykkes i en konflikt.
    
3. Nu, Lad os udføre handlingen faktisk synkronisering og hente data fra den eksterne backend-version.

    **Mål-C**:
    
    `syncData`først flytter ændringer, kalder derefter `pullData` til at hente data fra den eksterne backend-version. I aktivere metoden `pullData` bliver nye data, der svarer til en forespørgsel:
    
    
            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }
    
            -(void)pullData:(QSCompletionBlock)completion
            {
                MSQuery *query = [self.syncTable query];
    
                // Pulls data from the remote server into the local table.
                // We're pulling all items and filtering in the view
                // query ID is used for incremental sync
                [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
    
                    // Let the caller know that we have finished
                    if (completion != nil) {
                        dispatch_async(dispatch_get_main_queue(), completion);
                    }
                }];
            }
        
        
      **Swift**:
        
        
        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true
            
            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in
                
                UIApplication.sharedApplication().networkActivityIndicatorVisible = false
                
                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")
                    
                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 
    
    
    I mål-C-versionen, i `syncData`, vi kalder først `pushWithCompletion` på konteksten synkronisering. Denne metode er medlem af `MSSyncContext` (i stedet for Synkroniser tabellen selve) fordi det vil overføre ændringer på tværs af alle tabeller. Kun poster, der er blevet ændret i anden måde lokalt (via CUD handlinger) sendes til serveren. Derefter hjælper `pullData` kaldes, hvilke opkald `MSSyncTable.pullWithQuery` til at hente eksterne data og gemme i den lokale database.
    
    I Hurtig-versionen, der er ingen opkald til `pushWithCompletion`. Dette skyldes, at push-handlingen ikke er absolut nødvendigt. Hvis der er ændringer venter i konteksten synkronisere for den tabel, der gør en push-handlingen skal adskille altid problemer med en opslagsnål først. Men hvis du har mere end én tabel til synkronisering, er det bedst eksplicit kalde Tryk for at sikre, at alt er ensartet på tværs af relaterede tabeller.
    
    I både mål-C og problemfrit versioner, metoden `pullWithQuery` kan du angive en forespørgsel for at filtrere posterne, du vil hente. I dette eksempel forespørgslen kun henter alle poster i eksterne `TodoItem` tabel.
    
    Den anden parameter til `pullWithQuery` er en forespørgsel-ID, der bruges til *delvis synkronisering*. Delvis synkronisering henter kun de poster, der er ændret siden seneste synkronisering, ved hjælp af postens `UpdatedAt` tidsstempel (kaldet `updatedAt` i det lokale lager.) Forespørgsels-ID'ET skal være en beskrivende streng, der er entydige for hver logiske forespørgsel i din app. For at fravælge delvis synkronisering, du overfører `nil` som forespørgsel-ID. Bemærk, at det kan være potentielt ineffektiv, da det vil hente alle poster på hver hente handling.

5. Mål-C app synkroniserer, når vi ændre eller tilføje data, en bruger udfører bevægelse opdatering og på Start. Appen hurtig synkroniserer, når en bruger udfører Opdater bevægelse og på Start. 

Da app synkroniserer, når dataene er ændret (mål-C), eller når programmet starter (mål-C og Swift), antages appen, at brugeren er online. Vi vil opdatere appen i et andet afsnit, så brugerne kan redigere, selvom de er offline.

## <a name="review-core-data"></a>Gennemse Core datamodellen

Når du bruger Core Data offlinelageret, skal du definere bestemt tabeller og felter i din datamodel. Eksempel-app indeholder allerede en datamodel med det ønskede format. I dette afsnit vil vi gennemgå disse tabeller, og hvordan de bruges.

- Åbn **QSDataModel.xcdatamodeld**. Der er fire tabeller, der er defineret – tre, der bruges af SDK, og én tabel til opgaveliste elementer sig selv:     *MS_TableOperations: til at finde de elementer, der skal synkroniseres med serveren    * MS_TableOperationErrors: til at spore eventuelle fejl, der sker under offlinesynkronisering     *MS_TableConfig: opdateret tidspunkt for den seneste synkronisering handling for alle hente handlinger til at spore sidst    * TodoItem : Til lagring af opgaveliste elementer. System kolonner **createdAt**, **updatedAt**og **version** er valgfrit systemegenskaber.

>[AZURE.NOTE] Azure Mobile Apps SDK forbeholder sig kolonnenavne, der med "**``**". Du skal ikke bruge præfikset i alt andet end systemkolonner, ellers din kolonnenavne ændres, når du bruger den remote backend-version.

- Når du bruger funktionen offlinesynkronisering, skal du definere systemtabellerne, som vist nedenfor.

    ### <a name="system-tables"></a>Systemtabeller

    **MS_TableOperations**

    ![][defining-core-data-tableoperations-entity]

  	| Attribut  |    Type     |
  	|----------- |   ------    |
  	| id         | Heltal 64  |
  	| element-id     | Streng      |
  	| Egenskaber | Binære Data |
  	| tabel      | Streng      |
  	| tableKind  | Heltal 16  |

    <br>**MS_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

  	| Attribut  |    Type     |
  	|----------- |   ------    |
  	| id         | Streng      |
  	| operationId | Heltal 64 |
  	| Egenskaber | Binære Data |
  	| tableKind  | Heltal 16  |

    <br>**MS_TableConfig**

    ![][defining-core-data-tableconfig-entity]

  	| Attribut  |    Type     |
  	|----------- |   ------    |
  	| id         | Streng      |
  	| nøgle        | Streng      |
  	| keyType    | Heltal 64  |
  	| tabel      | Streng      |
  	| værdi      | Streng      |

    ### <a name="data-table"></a>Datatabel

    **TodoItem**

  	| Attribut    |  Type   | Bemærk!                                                   |
  	|-----------   |  ------ | -------------------------------------------------------|
  	| id           | Streng, der er markeret som obligatorisk  | primær nøgle i remote store                            |
  	| fuldføre     | Boolesk værdi | opgaveliste elementfelt                                        |
  	| tekst         | Streng  | opgaveliste elementfelt                                        |
  	| createdAt | Dato    | (valgfrit) kort til createdAt systemegenskab         |
  	| updatedAt | Dato    | (valgfrit) kort til updatedAt systemegenskab         |
  	| version   | Streng  | (valgfrit) bruges til at finde konflikter, kort til version |


## <a name="setup-sync"></a>Ændre funktionsmåden synkronisering af appen

I dette afsnit, skal redigere du appen, så det ikke synkroniseret på app start, eller når indsættelse og opdatering af elementer, men kun, når der udføres knappen Opdater bevægelse.

**Mål-C**:

1. I **QSTodoListViewController.m**, ændre metoden **viewDidLoad** for at fjerne opkaldet til `[self refresh]` i slutningen af metoden. Vil ikke blive synkroniseret med serveren på app start nu dataene, men i stedet bliver indholdet af lokale lager.

2. Redigere definitionen af i **QSTodoService.m** `addItem` så det ikke synkronisere, når elementet er indsat. Fjerne den `self syncData` blokere og Erstat med følgende:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. Redigere definitionen af `completeItem` som ovenfor; fjerne blokering for `self syncData` og Erstat med følgende:

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. I `viewDidLoad` i **ToDoTableViewController.swift**, skrive en kommentar ud af disse to linjer, stoppe synkroniseringen af på app start. Samtidig med denne artikel skriver opdaterer Swift opgaveliste app ikke tjenesten når nogen tilføjer eller afsluttes et element, kun på app start.

        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)


## <a name="test-app"></a>Teste appen

I dette afsnit, du vil oprette forbindelse til en ugyldig URL-adresse til at simulere en offline scenarie. Når du tilføjer dataelementer, vil de blive opbevares i det lokale lager Core Data, men ikke synkroniseret til den mobile backend-version.

1. Ændre Mobile App URL-adressen i **QSTodoService.m** til en ugyldig URL-adresse, og kør appen igen:

    **Mål-C** i QSTodoService.m:
    
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
    
    **Swift** i ToDoTableViewController.swift:

        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")

2. Tilføje nogle opgaveliste elementer. Afslut simulator (eller gennemtvinge lukker appen), og genstart. Kontrollér, at dine ændringer er blevet bevaret.

3. Få vist indholdet af tabellen remote TodoItem:

    + En Node.js back-end, gå til [Azure-portalen](https://portal.azure.com/), og i Mobile-App backend-version ved at klikke **Nemt tabeller** > **TodoItem** til at få vist indholdet af den `TodoItem` tabel.
    + Få vist indholdsfortegnelse enten med en SQL-værktøjet såsom SQL Server Management Studio eller en RESTEN klient som Fiddler eller Postman for en .NET back-end.

    Kontrollér, at de nye elementer der *ikke* er synkroniseret til serveren:

4. Ændre URL-adressen til de korrekte på i **QSTodoService.m** , og kør igen på app. Udføre Opdater bevægelse ved at trække ned på listen over elementer. Du får vist en gang skalafeltet.

5. Få vist TodoItem dataene igen. Nye og ændrede TodoItems vises nu.

## <a name="summary"></a>Oversigt

For at understøtte funktionen offlinesynkronisering, vi har brugt den `MSSyncTable` brugergrænsefladen og initialiseret `MSClient.syncContext` med en lokal butik. I dette tilfælde er det lokale lager en Core Data-baseret database.

Når du bruger lokale et Core datalager, skal du angive flere tabeller med [Egenskaber for korrekte system](#review-core-data).

Handlingerne normal CRUD til Azure Mobile-Apps fungerer som, hvis programmet stadig er tilsluttet, men alle handlinger sker på det lokale lager.

Når vi vil synkronisere med serveren, det lokale lager, vi har brugt den `MSSyncTable.pullWithQuery`metode.


## <a name="additional-resources"></a>Yderligere ressourcer

* [Synkronisering af offline Data i Azure-Mobilapps]

* [Forside i skyen: offlinesynkronisering i Azure Mobile Services] \(note: videoen er i Mobile-tjenester, men offlinesynkronisering fungerer på samme måde i Azure Mobile-Apps\)

<!-- URLs. -->


[Oprette en iOS-App]: app-service-mobile-ios-get-started.md
[Synkronisering af offline Data i Azure-Mobilapps]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Omslag til skyen: Offlinesynkronisering i Azure Mobile-tjenester]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
