<properties
    pageTitle="Arbejde med biblioteket App Service Mobile-Apps administreret klient (Windows | Xamarin) | Microsoft Azure"
    description="Lær, hvordan du bruger en .NET-klienten til Azure App Service Mobile-Apps til Windows og Xamarin apps."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Sådan bruges administrerede klienten til Azure Mobile-Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##<a name="overview"></a>Oversigt

Denne vejledning viser, hvordan du udfører almindelige scenarier, der bruger administreret klient biblioteket til Azure App Service Mobile-Apps til Windows og Xamarin apps. Hvis du er ny bruger af Mobile-Apps, du bør overveje første Fuldfør [Azure Mobile-Apps Hurtig start] [ 1] selvstudium. I denne vejledning der, fokuserer vi på klientsiden administrerede SDK. Hvis du vil vide mere om serversiden SDK'er til Mobile-Apps, i dokumentationen til [.NET Server SDK] [ 2] eller [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Referencedokumentation

Referencedokumentationen til klient-SDK findes her: [Azure Mobile Apps .NET klient reference][4].
Du kan også finde flere klient eksempler i [Azure-eksempler GitHub lager][5].

## <a name="supported-platforms"></a>Understøttede platforme

.NET-Platform understøtter følgende platforme:

* Xamarin Android versioner for API 19 til 24 (kitkat-operativsystem gennem Nougat)
* Xamarin iOS versioner til iOS 8.0 og senere versioner
* Universal Windows-Platform
* Windows Phone 8.1
* Windows Phone 8.0 med undtagelse af Silverlight-programmer

"Server-flow" godkendelse bruger en webvisning for præsenteret Brugergrænsefladen.  Hvis enheden ikke er stand til at præsentere en WebView UI, bruges der andre metoder til godkendelse.  Dette SDK er derfor ikke egnet til type af værdikontrol eller på samme måde begrænset enheder.

##<a name="setup"></a>Installation og forudsætninger

Vi antager, at du allerede har oprettet og udgivet din Mobile-App back end-projekt, som indeholder mindst én tabel.  I den kode, der bruges i dette emne, skal tabellen hedder `TodoItem` og den har følgende kolonner: `Id`, `Text`, og `Complete`. Denne tabel er den samme tabel, der oprettes, når du har fuldført [Azure Mobile-Apps Hurtig start].

Den tilsvarende indtastede klientside type i C# er klassen følgende:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }
    }

[JsonPropertyAttribute] [ 6] bruges til at definere *egenskabsnavn* tilknytningen mellem klienttype og tabellen.

For at lære at oprette tabeller i din Mobile-Apps backend-version, skal du se oplysningerne i [.NET Server SDK emne] [ 7] eller [Node.js Server SDK emne][8]. Hvis du har oprettet din Mobile-App backend-version i portalen Azure ved hjælp af på Hurtig start, kan du også bruge indstillingen **nemt tabeller** i [Azure-portalen].

###<a name="how-to-install-the-managed-client-sdk-package"></a>Sådan: installere administreret klient SDK-pakken

Bruge en af følgende metoder til at installere administreret klient SDK-pakken til Mobile-Apps fra [NuGet][9]:

+ **Visual Studio** Højreklik på dit projekt, skal du klikke på **Administrer NuGet-pakker**, søge efter den `Microsoft.Azure.Mobile.Client` pakke og derefter klikke på **Installer**.

+ **Xamarin Studio** Højreklik på dit projekt, skal du klikke på **Tilføj** > **Tilføje NuGet-pakker**, søge efter den `Microsoft.Azure.Mobile.Client `pakke, og klik derefter på **Tilføj pakke**.

I din primære aktivitet fil skal du huske at føje sætningen **ved hjælp af** følgende:

    using Microsoft.WindowsAzure.MobileServices;

###<a name="symbolsource"></a>Sådan: arbejde med fejlfinding symboler i Visual Studio

Symboler for navneområdet Microsoft.Azure.Mobile er tilgængelige på [SymbolSource][10].  Refererer til [SymbolSource instruktioner] [ 11] til at integrere SymbolSource med Visual Studio.

##<a name="create-client"></a>Oprette klienten Mobile-Apps

Følgende kode opretter [MobileServiceClient] [ 12] objekt, der bruges til at få adgang til din Mobile-App backend-version.

    var client = new MobileServiceClient("MOBILE_APP_URL");

Erstat teksten i den foregående kode `MOBILE_APP_URL` med URL-adressen på Mobile-App backend-version, som findes i bladet for din Mobile-App backend-version i [Azure-portalen]. Objektet MobileServiceClient skal være en separat.

## <a name="work-with-tables"></a>Arbejde med tabeller

Følgende afsnit indeholder oplysninger om hvordan du kan søge efter og hente poster og ændre dataene i tabellen.  De følgende emner:

* [Oprette en tabelreference](#instantiating)
* [Forespørgselsdata](#querying)
* [Filtrering af data skal returneres](#filtering)
* [Sortér returnerede data](#sorting)
* [Returnere data i sider](#paging)
* [Markere bestemte kolonner](#selecting)
* [Søge efter en post efter Id](#lookingup)
* [Håndtere uspecificerede forespørgsler](#untypedqueries)
* [Indsætte data](#inserting)
* [Opdatering af data](#updating)
* [Slette data](#deleting)
* [Konfliktløsning og optimistisk](#optimisticconcurrency)
* [Binding til en Windows-brugergrænseflade](#binding)
* [Ændre sidestørrelsen](#pagesize)

###<a name="instantiating"></a>Sådan: oprette en tabelreference

Den kode, der har adgang til eller ændrer data i en back end-tabel kalder funktioner på den `MobileServiceTable` objekt. Hente en reference til tabellen ved at ringe til metoden [GetTable] på følgende måde:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

Det returnerede objekt bruger indtastede serialisering modellen. En uspecificerede serialisering model understøttes også. I følgende eksempel [opretter en reference til en uspecificerede tabel]:

    // Get an untyped table reference
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

I den uspecificerede forespørgsler, skal du angive den underliggende OData-forespørgselsstreng.

###<a name="querying"></a>Sådan: forespørgsel dataene fra din Mobile-App

Dette afsnit beskrives, hvordan til at stille spørgsmål til den Mobile-App backend-version, som omfatter følgende funktioner:

- [Filtrering af data skal returneres](#filtering)
- [Sortér returnerede data](#sorting)
- [Returnere data i sider](#paging)
- [Markere bestemte kolonner](#selecting)
- [Søge efter data efter ID](#lookingup)

>[AZURE.NOTE]En server-driven sidestørrelse gennemtvinges for at forhindre, at alle rækker returneres.  Sideopdeling beholder standard anmodninger om store datasæt fra påvirke tjenesten.  Hvis du vil returnere mere end 50 rækker, skal du bruge den `Skip` og `Take` metode, som beskrevet i [returnerede data på sider].

###<a name="filtering"></a>Sådan: Filter returnerede data

Følgende kode viser, hvordan til at filtrere data ved at medtage en `Where` delsætning i en forespørgsel. Returnerer den alle elementer fra `todoTable` Hvis `Complete` egenskab er lig med `false`. Funktionen [hvor] anvender en række filtrering hurtigere forespørgslen mod en tabel.

    // This query filters out completed TodoItems and items without a timestamp.
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Du kan få vist URI for din anmodning, der er sendt til back-end ved hjælp af meddelelse inspektion software, som browseren udviklerværktøjer eller [Fiddler]. Bemærk, at forespørgselsstrengen er ændret, hvis du ser på anmodningen URI:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Denne OData-anmodning oversættes til en SQL-forespørgsel ved Server SDK:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0

Den funktion, der sendes til den `Where` metode kan have et vilkårligt antal betingelser.

    // This query filters out completed TodoItems where Text isn't null
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
       .ToListAsync();

I dette eksempel skal oversættes til en SQL-forespørgsel ved Server SDK:

    SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Denne forespørgsel kan også opdeles i flere delsætninger:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

De to metoder er ækvivalente og kan bruges i flæng.  Indstillingen tidligere&mdash;af sammenkæde flere prædikaterne i en forespørgsel&mdash;er mere kompakt og anbefalede.

Den `Where` delsætningen understøtter handlinger, som skal oversættes til OData-undersæt. Handlinger omfatter:

* Relationelle operatorer (==,! =, <, <, >, > =),
* Aritmetiske operatorer (+, -, /, *, %),
* Nummerere præcision (Math.Floor, Math.Ceiling)
* Strengfunktioner (længde, understreng, Erstat, IndexOf, StartsWith, EndsWith)
* Egenskaber for dato (år, måned, dag, time, minut, sekund)
* Få adgang til egenskaberne for et objekt, og
* Udtryk, der kombinerer disse handlinger.

Når du beslutter, hvad Server SDK understøtter, kan du overveje at [OData v3 dokumentation].

###<a name="sorting"></a>Sådan: Sortér returnerede data

Følgende kode viser, hvordan til at sortere data ved at medtage en [SorterEfter] eller [OrderByDescending] funktion i forespørgslen. Returnerer den elementer fra `todoTable` sorteret i faldende rækkefølge efter den `Text` felt.

    // Sort items in ascending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

    // Sort items in descending order by Text field
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)
    List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Sådan: returnere data i sider

Som standard returnerer back-end kun de første 50 rækker. Du kan øge antallet af returnerede rækker ved at ringe til metoden [tage] . Brug `Take` sammen med [Spring] metoden til at anmode om en bestemt "side" af det samlede dataset returneres i forespørgslen. Følgende forespørgsel, når køres, returnerer de tre vigtigste elementer i tabellen.

    // Define a filtered query that returns the top 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

Følgende reviderede forespørgsel springer de første tre resultater og returnerer de næste tre resultater. Denne forespørgsel giver den anden "side" af data, hvor sidestørrelsen er tre elementer.

    // Define a filtered query that skips the top 3 items and returns the next 3 items.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);
    List<TodoItem> items = await query.ToListAsync();

Metoden [IncludeTotalCount] , som det samlede antal for _alle_ de poster, vil der blev returneret, ignorerer alle sideopdeling/grænse-delsætning, der er angivet:

    query = query.IncludeTotalCount();

I en virkelige app, kan du bruge forespørgsler, der svarer til det foregående eksempel med en personsøger kontrolelement eller en tilsvarende brugergrænseflade til at navigere mellem sider.

>[AZURE.NOTE]Hvis du vil tilsidesætte grænsen 50 række i en back-end til Mobile-App, skal du også anvende [EnableQueryAttribute] på offentlige GET-metoden og angive sideopdeling funktionsmåden. Når du har anvendt til metoden, angiver følgende det maksimale antal returnerede rækker til 1000:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Sådan: markere bestemte kolonner

Du kan angive, hvilket sæt egenskaber vil medtage i resultaterne ved at tilføje en [Vælg] delsætning til din forespørgsel. For eksempel viser følgende kode, hvordan du vælger et enkelt felt, og hvordan du kan vælge og formatere flere felter:

    // Select one field -- just the Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Select multiple fields -- both Complete and Text info
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => string.Format("{0} -- {1}",
                        todoItem.Text.PadRight(30), todoItem.Complete ?
                        "Now complete!" : "Incomplete!"));
    List<string> items = await query.ToListAsync();

Alle de funktioner, der er beskrevet hidtil er additive, dvs vi kan bevare sammenkædning af dem. Hver sammenkædede opkald påvirker flere af forespørgslen. Ét eksempel:

    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Where(todoItem => todoItem.Complete == false)
                    .Select(todoItem => todoItem.Text)
                    .Skip(3).
                    .Take(3);
    List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Sådan: søge efter data efter ID

Funktionen [LookupAsync] kan bruges til at søge efter objekter fra databasen med en bestemt-ID.

    // This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Sådan: udføre uspecificerede forespørgsler

Når du kører en forespørgsel med en uspecificerede table-objekt, skal du angive forespørgselsstreng OData ved at ringe til [ReadAsync], som i følgende eksempel:

    // Lookup untyped data using OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Du kommer tilbage JSON-værdier, du kan bruge som en egenskab pose. Du kan finde flere oplysninger om JToken og Newtonsoft Json.NET webstedet [Json.NET] .

### <a name="inserting"></a>Sådan: indsætte data i en Mobile-App back-end

Alle klienttyper skal indeholde et medlem, der hedder **Id**, som er som standard en streng. Dette **Id** er påkrævet til at udføre CRUD handlinger og offlinesynkronisering. Følgende kode viser, hvordan du bruger [InsertAsync] metoden til at indsætte nye rækker i en tabel. Parameteren indeholder dataene, der skal indsættes som en .NET-objekt.

    await todoTable.InsertAsync(todoItem);

Hvis en entydig værdi for brugerdefineret ID ikke medtages i den `todoItem` under en Indsæt et GUID genereres af serveren.
Du kan hente det genererede Id ved at undersøge objektet, når opkaldet returnerer.

Hvis du vil indsætte den uspecificerede data, kan du drage fordel af Json.NET:

    JObject jo = new JObject();
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Her er et eksempel med en mailadresse som en entydig streng-id:

    JObject jo = new JObject();
    jo.Add("id", "myemail@emaildomain.com");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

### <a name="working-with-id-values"></a>Arbejde med id-værdier

Mobile-Apps understøtter entydige brugerdefinerede strengværdier til tabellens **id-** kolonnen. En strengværdi gør det muligt for programmer til at bruge brugerdefinerede værdier som e-mail-adresser eller brugernavne for-ID.  Streng id'er giver dig følgende fordele:

* Id'er genereres uden at foretage en returkørsel til databasen.
* Poster er nemmere at flette fra forskellige tabeller eller databaser.
* Id'er værdier kan integrere bedre med et programs logik.

Når en strengværdi-ID ikke er angivet for en indsat post, genererer Mobile-App back-end en entydig værdi af-id'et. Du kan bruge metoden [Guid.NewGuid] til at oprette din egen ID-værdier, enten på klienten eller i back end.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Sådan: ændre data i en Mobile-App back-end

Følgende kode viser, hvordan du bruger metoden [UpdateAsync] til at opdatere en eksisterende post med det samme ID med nye oplysninger. Parameteren indeholder dataene, der opdateres som en .NET-objekt.

    await todoTable.UpdateAsync(todoItem);

Hvis du vil opdatere den uspecificerede data, kan du drage fordel af [Json.NET] på følgende måde:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World");
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

En `id` felt skal angives, når du foretager en opdatering. Back-end bruger den `id` felt for at identificere, hvilke række til at opdatere. Den `id` felt kan fås fra resultatet af den `InsertAsync` opkald. En `ArgumentException` opstår, hvis du forsøger at opdatere et element uden at give den `id` værdi.

###<a name="deleting"></a>Sådan: slette data i en Mobile-App back-end

Følgende kode viser, hvordan du bruger metoden [DeleteAsync] til at slette en eksisterende forekomst. Forekomsten, der er identificeret med den `id` feltet sæt på den `todoItem`.

    await todoTable.DeleteAsync(todoItem);

Hvis du vil slette den uspecificerede data, kan du drage fordel af Json.NET på følgende måde:

    JObject jo = new JObject();
    jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Når du har foretaget Sletteanmodningen, skal være angivet et ID. Andre egenskaber er ikke overføres til tjenesten eller ignoreres i tjenesten. Resultatet af en `DeleteAsync` opkald er som regel `null`. ID'ET til at videregive i kan hentes fra resultatet af den `InsertAsync` opkald. A `MobileServiceInvalidOperationException` er opstået, når du forsøger at slette et element uden at angive den `id` felt.

###<a name="optimisticconcurrency"></a>Sådan: Brug optimistisk på Dokumentsammenfald for konfliktløsning

To eller flere kunder kan skrive ændringer til det samme element ad gangen. Uden at finde konflikter, vil den sidste Skriv overskrive eventuelle tidligere opdateringer. **Optimistisk kontrolelementet** antager, at hver transaktion kan bekræfte og derfor bruger ikke en ressource låsning.  Før du gemmer en transaktion, kontrollerer optimistisk kontrolelement, at ingen andre transaktion har ændret dataene. Hvis dataene er blevet ændret, de udfører transaktion er annulleret.

Mobile-Apps understøtter optimistisk kontrolelementet ved at spore ændringer til hvert element ved hjælp af den `version` system egenskabskolonne, der er defineret for hver tabel i din Mobile-App backend-version. Hver gang en post er opdateret, Mobile-Apps angiver den `version` egenskab for den pågældende post til en ny værdi. Under hver anmodning om opdatering af `version` egenskab i den post, der er inkluderet i anmodningen, er sammenlignet med den samme egenskab for posten på serveren. Hvis versionen overføres med anmodningen ikke svarer til back-end og derefter biblioteket klient viser en `MobileServicePreconditionFailedException<T>` undtagelse. Den type, der følger med undtagelsen er en post fra backend-version, der indeholder den servere version af posten. Programmet kan derefter bruge disse oplysninger til at beslutte, om skal udføre opdateringsforespørgsel igen med den korrekte `version` værdi fra back-end at acceptere ændringer.

Oprette en kolonne i klassen tabel til den `version` system egenskaben til at aktivere optimistisk. Eksempel:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

        // *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public string Version { set; get; }
    }


Programmer, der bruger den uspecificerede tabeller aktivere optimistisk ved at angive den `Version` Markér med flag ved det `SystemProperties` i tabellen på følgende måde.

    //Enable optimistic concurrency by retrieving version
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Ud over at aktivere optimistisk, skal du også se den `MobileServicePreconditionFailedException<T>` undtagelse i din kode, når du ringer til [UpdateAsync].  Løse konflikten ved at anvende den korrekte `version` til de opdaterede post og opkaldet [UpdateAsync] med den løste post. Følgende kode viser, hvordan du løser en Skrivekonflikt fundet én gang:

    private async void UpdateToDoItem(TodoItem item)
    {
        MobileServicePreconditionFailedException<TodoItem> exception = null;

        try
        {
            //update at the remote table
            await todoTable.UpdateAsync(item);
        }
        catch (MobileServicePreconditionFailedException<TodoItem> writeException)
        {
            exception = writeException;
        }

        if (exception != null)
        {
            // Conflict detected, the item has changed since the last query
            // Resolve the conflict between the local and server item
            await ResolveConflict(item, exception.Item);
        }
    }


    private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
    {
        //Ask user to choose the resoltion between versions
        MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

        UICommand localBtn = new UICommand("Commit Local Text");
        UICommand ServerBtn = new UICommand("Leave Server Text");
        msgDialog.Commands.Add(localBtn);
        msgDialog.Commands.Add(ServerBtn);

        localBtn.Invoked = async (IUICommand command) =>
        {
            // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
            // catching a MobileServicePreConditionFailedException.
            localItem.Version = serverItem.Version;

            // Updating recursively here just in case another change happened while the user was making a decision
            UpdateToDoItem(localItem);
        };

        ServerBtn.Invoked = async (IUICommand command) =>
        {
            RefreshTodoItems();
        };

        await msgDialog.ShowAsync();
    }

Du kan få mere at vide under emnet [Offline synkronisering af Data i Azure Mobile-Apps] .

###<a name="binding"></a>Sådan: Bind Mobile-Apps data til en Windows-brugergrænseflade

Dette afsnit viser, hvordan du får vist returnerede dataobjekter ved hjælp af brugergrænsefladeelementer i en Windows-app.  Følgende eksempelkode bindes til kilden til listen med en forespørgsel for ufuldstændig elementer. [MobileServiceCollection] opretter en Mobile-Apps-aware binding af websteder.

    // This query filters out completed TodoItems.
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl is an IEnumerable that could be bound to a UI list control
    IEnumerable itemsControl  = items;

    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Nogle kontrolelementer i den administrerede runtime understøtter en brugergrænseflade kaldet [ISupportIncrementalLoading]. Denne grænseflade kan kontrolelementer til at anmode om ekstra data, når brugeren ruller. Der er indbygget understøttelse af denne grænseflade til universal Windows apps via [MobileServiceIncrementalLoadingCollection], som automatisk håndterer opkald fra kontrolelementerne. Brug `MobileServiceIncrementalLoadingCollection` i Windows-apps på følgende måde:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Hvis du vil bruge den nye websteder på Windows Phone 8 og "Silverlight" apps, skal du bruge den `ToCollection` lokalnummer metoder på `IMobileServiceTableQuery<T>` og `IMobileServiceTable<T>`. Hvis du vil indlæse data, skal du ringe `LoadMoreItemsAsync()`.

    MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
    await items.LoadMoreItemsAsync();

Når du bruger samlingen oprettet ved at ringe til `ToCollectionAsync` eller `ToCollection`, får du en samling, der kan være bundet til Brugergrænsefladen kontrolelementer.  Denne samling er sideopdeling-aware.  Da samlingen indlæser data fra netværket, mislykkes indlæsning af nogle gange. Hvis du vil håndtere disse fejl, tilsidesætter den `OnException` metode på `MobileServiceIncrementalLoadingCollection` til at håndtere undtagelser, der er resultatet af opkald til `LoadMoreItemsAsync`.

Overvej Hvis tabellen har mange felter, men du kun vil have vist nogle af dem i din kontrolelement. Du kan bruge vejledningen, der i det forrige afsnit "[markere bestemte kolonner](#selecting)" markere bestemte kolonner skal vises i Brugergrænsefladen.

###<a name="pagesize"></a>Ændre sidestørrelsen

Azure Mobile-Apps returnerer maksimalt 50 varer per anmodning som standard.  Du kan ændre sideopdeling størrelsen ved at øge den maksimale sidestørrelse på både klienten og serveren.  Hvis du vil øge den ønskede sidestørrelse, angive `PullOptions` når du bruger `PullAsync()`:

    PullOptions pullOptions = new PullOptions
        {
            MaxPageSize = 100
        };

Hvis du har foretaget den `PageSize` lig med eller større end 100 inden for serveren, en anmodning om returnerer op til 100 elementer.

##<a name="#offlinesync"></a>Arbejde med Offline tabeller

Offline tabeller Brug en lokal SQLite store til lagring af data til brug, når du er offline.  Alle tabel, der foregår mod lokalt SQLite store i stedet for i fjernserver store.  For at oprette en offline tabel skal du først forberede dit projekt:

1. Højreklik på løsningen i Visual Studio > **Administrere NuGet pakker til løsning...**, og derefter søge efter og installere pakken **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet for alle projekter i løsningen.

2. (Valgfrit) Installere en af følgende SQLite runtime-pakker for at understøtte Windows enheder:

    * **Windows 8.1 Runtime:** Installere [SQLite til Windows 8.1][3].
    * **Windows Phone 8.1:** Installere [SQLite til Windows Phone 8.1][4].
    * **Universal Windows-Platform** Installere [SQLite for Universal Windows Universal][5].

3. (Valgfrit). Klik på **referencer**til Windows-enheder, > **Tilføj Reference**, Udvid mappen **Windows** > **filtypenavne**, og Aktivér den relevante **SQLite til Windows** SDK sammen med **Visual C++ 2013 Runtime til Windows** SDK.
    SQLite SDK navnene kan variere en smule med hver Windows-platform.

Før du kan oprette en tabelreference, skal være forberedt det lokale lager:

    var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
    store.DefineTable<TodoItem>();

    //Initializes the SyncContext using the default IMobileServiceSyncHandler.
    await this.client.SyncContext.InitializeAsync(store);

Store initialisering gøres normalt med det samme, når klienten er blevet oprettet.  **OfflineDbPath** skal være et filnavn, der er velegnet til brug på alle platforme, du yder support.  Hvis stien er en fuldt kvalificeret sti (den starter det vil sige, med en skråstreg), og derefter denne sti bruges.  Hvis stien ikke er fuldt kvalificeret, er filen placeret i en platform-specifikke placering.

* Til iOS og Android-enheder er standardstien mappen "Personlige filer".
* Til Windows-enheder er standardstien mappen program-specifikke "AppData".

En tabelreference kan opnås ved hjælp af den `GetSyncTable<>` metode:

    var table = client.GetSyncTable<TodoItem>();

Du behøver ikke at godkende for at bruge en offline-tabel.  Du skal kun godkende, når du kommunikerer med back end-tjenesten.

###<a name="syncoffline"></a>Synkronisere en Offline-tabel

Offline tabeller er ikke synkroniseret med back end-som standard.  Synkronisering er opdelt i to dele.  Du kan overføre ændringer separat i at hente nye elementer.  Her er en typisk synkroniseringsmetode:

    public async Task SyncAsync()
    {
        ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

        try
        {
            await this.client.SyncContext.PushAsync();

            await this.todoTable.PullAsync(
                //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
                //Use a different query name for each unique query in your program
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

        // Simple error/conflict handling. A real application would handle the various errors like network conditions,
        // server conflicts and others via the IMobileServiceSyncHandler.
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

                Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
            }
        }
    }

Hvis det første argument til `PullAsync` er null, og derefter delvis synkronisering ikke bruges.  Hver synkroniseringen henter alle poster.

SDK udfører et implicit `PushAsync()` før at trække poster.

Håndtering af konflikt sker på en `PullAsync()` metode.  Du kan løse konflikter på samme måde som online tabeller.  Konflikten produceret når `PullAsync()` hedder i stedet for i løbet af Indsæt, Opdater eller Slet. Hvis flere konflikter, er de samlet i en enkelt MobileServicePushFailedException.  Håndtere de enkelte fejl separat.

##<a name="#customapi"></a>Arbejde med en brugerdefineret API

En brugerdefineret API gør det muligt at definere brugerdefinerede slutpunkter, der viser server-funktionalitet, der ikke tilknyttes en insert, opdatere, slette, eller Læs handling. Ved hjælp af en brugerdefineret API, kan du have mere kontrol over messaging, herunder læsning og indstille HTTP brevhoveder for meddelelser og definere formatet for en meddelelsens brødtekst end JSON.

Du ringe til en brugerdefineret API ved at ringe til en af [InvokeApiAsync] metoderne på klienten. For eksempel sender følgende linje af kode en anmodning om INDLÆG til **completeAll** API på back-end:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Denne formular er et indtastede metode opkald og kræver, at **MarkAllResult** returtypen er defineret. Både indtastede og uspecificerede metoder understøttes.

##<a name="authentication"></a>Godkende brugere

Mobile-Apps understøtter kontrollere og godkende app brugere ved hjælp af forskellige eksterne identitetsudbydere: Facebook, Google, Microsoft-Account, Twitter og Azure Active Directory. Du kan angive tilladelser på tabeller for at begrænse adgangen til bestemte operationer til kun godkendte brugere. Du kan også bruge identiteten af godkendte brugere for at implementere reglerne i serverscripts. Du kan finde yderligere oplysninger finder selvstudiet [Tilføj godkendelse til din app].

To godkendelse flyder understøttes: _klient-administreret_ og _server-administreret_ . Server-administreret strømmen yder den nemmeste godkendelse oplevelse, som den er afhængig af brugergrænsefladen udbyderens internettet godkendelse. Klient-administreret strømmen giver mulighed for bedre integration med enhed-specifikke funktioner, som den er afhængig af providerspecifikke enhed-specifikke SDK'er.

>[AZURE.NOTE] Det anbefales at bruge en klient-administreret flow i dine fremstilling apps.

Hvis du vil konfigurere godkendelse, skal du registrere din app med en eller flere id-udbydere.  Identitetsudbyder genererer en klient-ID og en klient hemmeligt for din app.  Disse værdier angives derefter i din backend-version til at aktivere Azure App Service godkendelse/godkendelse.  Følg den detaljerede vejledning kan finde flere oplysninger i selvstudium [Tilføj godkendelse til din app].

De følgende emner behandles i dette afsnit:

+ [Klient-administreret godkendelse](#clientflow)
+ [Server-administreret godkendelse](#serverflow)
+ [Cachelagring tokenet godkendelse](#caching)

###<a name="clientflow"></a>Klient-administreret godkendelse

Din app kan uafhængigt Kontakt udbyderen af identitet og derefter angive det returnerede token ved logon med din back end. Dette klient flow gør det muligt at give en enkelt sign-on – oplevelse for brugere eller til at hente yderligere brugerdata fra identitetsudbyder. Klient flow godkendelse er foretrukne til at bruge en server flow som identitetsudbyder SDK indeholder en mere oprindelige UX funktionalitet og giver mulighed for yderligere tilpasning.

Eksempler er beregnet til følgende klient-flow godkendelse mønstre:

+ [Active Directory Authentication Library](#adal)
+ [Facebook eller Google](#client-facebook)
+ [Live SDK](#client-livesdk)

#### <a name="adal"></a>Godkende brugere med Active Directory Authentication Library

Du kan bruge Active Directory Authentication Library (ADAL) til Påbegynd brugergodkendelse fra klienten på computeren ved hjælp af Azure Active Directory-godkendelse.

1. Konfigurere din mobilapp backend-version til AAD sign-on ved at følge [Sådan konfigureres App-tjeneste til logon til Active Directory] -selvstudium. Sørg for at fuldføre valgfrit til registrering af et native client-program.
2. I Visual Studio eller Xamarin Studio skal du åbne projektet og tilføje en reference til den `Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet pakke. Når du søger, kan du medtage foreløbige versioner.
3. Tilføj følgende kode i dit program, efter den platform, du bruger. I hver skal du foretage de følgende erstatninger:

    * Erstat **Indsæt-NØGLECENTER-her** med navnet på den lejer, hvori du klargjort dit program. Formatet skal være https://login.windows.net/contoso.onmicrosoft.com. Denne værdi kan kopieres fra fanen domæne i din Azure Active Directory i [Azure klassisk portal].
    * Erstat **Indsæt-ressource-ID-her** med klient-ID for din back end-til-mobilappen. Du kan få klient-ID fra fanen **Avanceret** under **Azure Active Directory-indstillinger** i portalen.
    * Erstat **Indsæt-klient-ID-her** med brugerens klient-ID, du har kopieret fra den oprindelige klientprogrammet.
    * Erstat **Indsæt-REDIRECT-URI-her** med webstedets _/.auth/login/done_ slutpunkt, ved hjælp af HTTPS-skemaet. Denne værdi skal ligne _https://contoso.azurewebsites.net/.auth/login/done_.

    Den kode, der er behov for hver platform følger:

    **Windows:**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            while (user == null)
            {
                string message;
                try
                {
                    AuthenticationContext ac = new AuthenticationContext(authority);
                    AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                        new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                    JObject payload = new JObject();
                    payload["access_token"] = ar.AccessToken;
                    user = await App.MobileService.LoginAsync(
                        MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                    message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    **Xamarin.iOS**

        private MobileServiceUser user;
        private async Task AuthenticateAsync(UIViewController view)
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(view));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

    **Xamarin.Android**

        private MobileServiceUser user;
        private async Task AuthenticateAsync()
        {
            string authority = "INSERT-AUTHORITY-HERE";
            string resourceId = "INSERT-RESOURCE-ID-HERE";
            string clientId = "INSERT-CLIENT-ID-HERE";
            string redirectUri = "INSERT-REDIRECT-URI-HERE";
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(this));
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await client.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
            }
            catch (Exception ex)
            {
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.SetMessage(ex.Message);
                builder.SetTitle("You must log in. Login Required");
                builder.Create().Show();
            }
        }
        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);
            AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
        }

####<a name="client-facebook"></a>Single Sign-On ved hjælp af et token fra Facebook eller Google

Du kan bruge strømmen klient, som vist i denne kodestykke til Facebook eller Google.

    var token = new JObject();
    // Replace access_token_value with actual value of your access token obtained
    // using the Facebook or Google SDK.
    token.Add("access_token", "access_token_value");

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Change MobileServiceAuthenticationProvider.Facebook
                // to MobileServiceAuthenticationProvider.Google if using Google auth.
                user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

####<a name="client-livesdk"></a>Enkelt logon ved hjælp af Microsoft Account med Live SDK

Hvis du vil godkende brugere, skal du registrere din app på Microsoft-kontoen Developer Center. Konfigurere registreringsoplysninger på din Mobile-App back-end. Hvis du vil oprette en Microsoft-kontoregistrering og forbinde den til din Mobile-App backend-version, Fuldfør trinnene i [registrere din app for at bruge et Microsoft-konto logon]. Hvis du har både Windows Store og Windows Phone 8/Silverlight-versionen af din app, skal du registrere versionen af Windows Store først.

Følgende kode godkender ved hjælp af Live SDK og bruger returnerede tokenet til at logge på din Mobile-App backend-version.

    private LiveConnectSession session;
    //private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
            // The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Du kan finde flere oplysninger i dokumentationen til [Windows Live SDK] .

###<a name="serverflow"></a>Server-administreret godkendelse

Når du har registreret dit identitetsudbyder, kalde metoden [LoginAsync] på [MobileServiceClient] med [MobileServiceAuthenticationProvider] værdien af din udbyder. Følgende kode starter for eksempel en server flow logge på ved hjælp af Facebook.

    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                message =
                    string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Hvis du bruger en identitetsudbyder end Facebook, kan du ændre værdien af [MobileServiceAuthenticationProvider] til værdien for din udbyder.

I et server flow administrerer Azure App Service OAuth godkendelse strømmen ved at vise logonside for den valgte provider.  Når identitet udbyder returnerer, Azure App Service genererer en App Service godkendelse token. Metoden [LoginAsync] returnerer en [MobileServiceUser], som indeholder både [bruger-id] for den godkendte bruger og [MobileServiceAuthenticationToken]som et JSON web token (JWT). Dette token kan cachelagret og genbruges, før det udløber. Se [cachelagring godkendelse tokenet](#caching)kan finde flere oplysninger.

###<a name="caching"></a>Cachelagring tokenet godkendelse

I nogle tilfælde kan opkaldet til metoden login undgås efter den første vellykkede godkendelse ved at gemme det godkendelse token fra provideren.  Windows Store- og UWP apps kan bruge [PasswordVault] til cache aktuelle godkendelse tokenet efter en vellykket logon, på følgende måde:

    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
        client.currentUser.MobileServiceAuthenticationToken));

Værdien, der bruger-id er gemt som brugernavnet for legitimationsoplysninger og Tokenet er gemt som adgangskoden. Du kan kontrollere **PasswordVault** for cachelagrede legitimationsoplysninger under efterfølgende opstart. I følgende eksempel bruges cachelagrede legitimationsoplysninger, når de findes, og ellers forsøger at godkende igen med back end:

    // Try to retrieve stored credentials.
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        // Create the current user from the stored credentials.
        client.currentUser = new MobileServiceUser(creds.UserName);
        client.currentUser.MobileServiceAuthenticationToken =
            vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Regular login flow and cache the token as shown above.
    }

Når du logger af en bruger, skal du også fjerne den gemte legitimationsoplysninger, som følger:

    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));

Xamarin apps Brug [Xamarin.Auth] API'er til at lagre legitimationsoplysninger sikkert i et **konto** -objekt. Du kan finde et eksempel på brug af disse API'er filen [AuthStore.cs] kode i [ContosoMoments fotodeling eksempel].

Når du bruger klient-administreret godkendelse, kan du også cache adgangstoken, som fås fra din udbyder som Facebook- eller Twitter. Dette token kan leveres for at anmode om en ny godkendelse token fra backend-version, som følger:

    var token = new JObject();
    // Replace <your_access_token_value> with actual value of your access token
    token.Add("access_token", "<your_access_token_value>");

    // Authenticate using the access token.
    await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);

##<a name="pushnotifications"></a>Push-beskeder

Push-beskeder ind på de følgende emner:

* [Tilmelding til Pushmeddelelser](#register-for-push)
* [Få en Windows Store-pakke SID](#package-sid)
* [Registrere med på tværs af platforme skabeloner](#register-xplat)

###<a name="register-for-push"></a>Sådan: Tilmeld dig Pushmeddelelser

Mobile-Apps klienten gør det muligt at registrere til pushmeddelelser med Azure meddelelse Hubs. Når der registreres, kan du få styr, får du fra den platform-specifikke Push meddelelse Service (PNS). Du kan derefter angive denne værdi sammen med eventuelle mærker, når du opretter registreringen. Følgende kode registrerer din Windows-app til pushmeddelelser med Windows besked om Service (WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Hvis du bevæger til WNS, derefter skal du [få en Windows Store-pakke SID](#package-sid).  Du kan finde flere oplysninger om Windows apps, herunder hvordan du tilmelde dig skabelon registreringer, [Tilføj pushmeddelelser til din app].

Anmoder om mærker fra klienten understøttes ikke.  Mærke anmodninger udelades uovervåget fra registrering.
Hvis du vil registrere din enhed med mærker, skal du oprette et brugerdefineret API, der bruger meddelelse Hubs API til at udføre registreringen på dine vegne.  [Ringe til brugerdefineret API](#customapi) i stedet for den `RegisterNativeAsync()` metode.

###<a name="package-sid"></a>Sådan: få en Windows Store-pakke SID

En pakken SID er nødvendig for at aktivere pushmeddelelser i Windows Store-apps.  Hvis du vil modtage en pakke SID, du registrere dit program med Windows Store.

Sådan hentes denne værdi:

1. Højreklik på Windows Store app projektet i Visual Studio Solution Explorer, skal du klikke på **Store** > **Knytte App med Store …**.
2. Klik på **Næste**i guiden, logge på med din Microsoft-konto, Skriv et navn for din app i **Reserver en ny app-navn**og derefter klikke på **Reserver**.
3. Når app registrering er blevet oprettet, Vælg app-navnet, klik på **Næste**, og klik **knytte**.
4. Log på [Windows-Udviklercenter] ved hjælp af din Microsoft-Account. Klik på den app registrering, du oprettede under **Mine apps**.
5. Klik på **Administration af App** > **App identitet**, og derefter Rul ned for at finde din **Pakke SID**.

Mange anvendelsesområder pakken SID behandle den som en URI, hvorefter du skal bruge _ms-app: / /_ som skemaet. Versionen af din pakke SID udformet ved at sammenkæde denne værdi som et præfiks noter.

Xamarin apps kræver ekstra kode skal kunne registrere en app, der kører på iOS eller Android-platforme. Du kan få mere at vide under emnet til din platform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Sådan: Registrer opslagsnål skabeloner at sende meddelelser på tværs af platforme

For at registrere skabeloner skal du bruge den `RegisterAsync()` metode med skabelonerne, på følgende måde:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Dine skabeloner skal være `JObject` filtyper og kan indeholde flere skabeloner i den følgende JSON-format:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

Metoden **RegisterAsync()** accepterer også sekundær felter:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Alle mærker fjernes ikke til stede under registrering for sikkerhed. For at føje mærker til installationer eller skabeloner i installationer, skal du se [arbejde med .NET back end-serveren SDK til Azure Mobile-Apps].

For at sende meddelelser ved hjælp af disse registrerede skabeloner skal du henvise til [Meddelelse Hubs API'er].

##<a name="misc"></a>Diverse emner

###<a name="errors"></a>Sådan: håndtere fejl

Når der opstår en fejl i backend-version, klienten SDK hæver en `MobileServiceInvalidOperationException`.  I følgende eksempel viser Sådan håndteres en undtagelse, der returneres af back:

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // This code inserts a new TodoItem into the database. When the operation completes
        // and App Service has assigned an Id, the item is added to the CollectionView
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Handle error
        }
    }

Et andet eksempel på håndtering af fejltilstande kan findes i [Mobile Apps filer eksempel]. [LoggingHandler] eksemplet indeholder en logføring stedfortræder handler (følgende) for at logge på anmodninger om henvisninger til backend-version.

###<a name="headers"></a>Sådan: tilpasse anmode om sidehoveder

For at understøtte scenariet bestemt app, skal du muligvis tilpasse kommunikation med Mobile-App back end. Du vil muligvis føje et brugerdefineret sidehoved til alle udgående anmodning eller endda ændre svar statuskoder. Du kan bruge en brugerdefineret [DelegatingHandler], som i følgende eksempel:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/en-us/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: http://www.symbolsource.org/
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Føje godkendelse til din app]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Synkronisering af offline Data i Azure-Mobilapps]: app-service-mobile-offline-data-sync.md
[Føje pushmeddelelser til din app]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrere din app for at bruge en Microsoft-konto-logon]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Sådan konfigureres App Service til logon til Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/en-us/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/en-us/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/en-us/library/azure/jj554275(v=azure.10).aspx
[opretter en reference til en uspecificerede tabel]: https://msdn.microsoft.com/en-us/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/en-us/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/en-us/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/en-us/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/en-us/library/azure/jj871654(v=azure.10).aspx
[SorterEfter]: https://msdn.microsoft.com/en-us/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/en-us/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/en-us/library/azure/mt691741(v=azure.10).aspx
[Tage]: https://msdn.microsoft.com/en-us/library/azure/dn250574(v=azure.10).aspx
[Vælg]: https://msdn.microsoft.com/en-us/library/azure/dn250569(v=azure.10).aspx
[Spring over]: https://msdn.microsoft.com/en-us/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/en-us/library/azure/dn250536.(v=azure.10)aspx
[Bruger-id]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Hvor]: https://msdn.microsoft.com/en-us/library/azure/dn250579(v=azure.10).aspx
[Azure-portalen]: https://portal.azure.com/
[Azure klassisk portal]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/en-us/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows-Udviklercenter]: https://dev.windows.com/en-us/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/en-us/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Meddelelse om Hubs API'er]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobilapps filer eksempel]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 dokumentation]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: (https://github.com/azure-appservice-samples/ContosoMoments/blob/dev/src/Mobile/ContosoMoments/Helpers/AuthStore.cs)
[ContosoMoments foto deling eksempel]: https://github.com/azure-appservice-samples/ContosoMoments