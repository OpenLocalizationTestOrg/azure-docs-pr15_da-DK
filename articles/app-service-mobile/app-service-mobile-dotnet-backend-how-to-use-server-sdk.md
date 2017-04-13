<properties
    pageTitle="Hvordan du arbejder med .NET back end-serveren SDK til Mobile-Appen | Azure App Service"
    description="Lær, hvordan du arbejder med .NET back end-serveren SDK til Azure App Service Mobile-Apps."
    keywords="App-tjenesten, azure-app-tjenesten, mobilapp, mobile service, skala, SVG, installation, azure app appinstallation"
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

# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Arbejde med .NET back end-serveren SDK til Azure Mobile-Apps

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Dette emne viser, hvordan du bruger .NET back end-serveren SDK i nøglescenarier Azure App Service Mobile-Apps. Azure Mobile Apps SDK hjælper dig med at arbejde med mobilklienter fra ASP.NET-program.

>[AZURE.TIP] [.NET server SDK til Azure Mobile-Apps] [ 2] er åben kilde på GitHub. Lageret indeholder alle kildekode, herunder hele serveren SDK enhed test pakke og nogle eksempel projekter.

## <a name="reference-documentation"></a>Referencedokumentation

Referencedokumentationen til serveren SDK findes her: [Azure Mobile Apps.NET Reference][1].

## <a name="create-app"></a>Sådan: oprette en .NET Mobile-App back-end

Hvis du starter et nyt projekt, kan du oprette en App-tjenesteprogram, ved hjælp af [Azure portal] eller Visual Studio. Du kan køre i App-tjenesteprogrammet lokalt eller publicere projektet til din skybaserede App Service-mobilappen.  

Hvis du føjer mobile-funktioner til et eksisterende projekt, i afsnittet [kan du hente og initialiseret SDK](#install-sdk) .

### <a name="create-a-net-backend-using-the-azure-portal"></a>Oprette en .NET back-end, ved hjælp af portalen Azure

Hvis du vil oprette en mobiltelefon, backend App Service-version, enten følge [Hurtig start selvstudium] [ 3] , eller Følg disse trin:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Tilbage i bladet _komme i gang_ under **Opret en tabel, API**, vælge **C#** som **back end-sprog**. Klik på **Hent**, udtrække komprimeret project-filer til din lokale computer, og Åbn løsningen i Visual Studio.

### <a name="create-a-net-backend-using-visual-studio-2013-and-visual-studio-2015"></a>Oprette en .NET back-end, ved hjælp af Visual Studio 2013 og Visual Studio 2015

Installer [Azure SDK til .NET] [ 4] (version 2.9.0 eller nyere) til at oprette et projekt til Azure Mobile-Apps i Visual Studio. Når du har installeret SDK, kan du oprette en ASP.NET-program, der er at benytte følgende fremgangsmåde:

1. Åbne dialogboksen **Nyt projekt** (fra *fil* > **Ny** > **Project...**).
2. Udvid **skabeloner** > **Visual C#**, og vælg **Web**.
3. Vælg **ASP.NET-webprogram**.
4. Skriv projektnavnet. Klik derefter på **OK**.
5. Under _ASP.NET 4.5.2 skabeloner_, Vælg **Azure Mobile-App**. Markér **Host i skyen** for at oprette en mobil back-end i skyen, kan du udgive projektet.
6. Klik på **OK**.

## <a name="install-sdk"></a>Sådan: hente og initialiseret SDK

SDK er tilgængelig på [NuGet.org]. Denne pakke indeholder de grundlæggende funktioner, der kræves for at komme i gang ved hjælp af SDK. Hvis du vil initialiseret SDK, skal du udføre handlinger på objektet **HttpConfiguration** .

### <a name="install-the-sdk"></a>Installere SDK

Installere SDK, skal du højreklikke på server projektet i Visual Studio, Vælg **Administrer NuGet pakker**, søge efter pakken [Microsoft.Azure.Mobile.Server] og derefter klikke på **Installer**.

###<a name="server-project-setup"></a>Initialiseret server projektet

Et .NET back end-serverprojekt er initialiseret ligner andre ASP.NET-projekter, ved at medtage en OWIN Start klasse. Sørg for, at du har henvist til pakken NuGet `Microsoft.Owin.Host.SystemWeb`. Hvis du vil tilføje denne klasse i Visual Studio, skal du højreklikke på projektet server, og vælg **Tilføj** > 
**Nyt element**, og klik derefter **Web** > **generelle** > **OWIN Start klasse**.  En klasse er oprettet med følgende attributter:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

I den `Configuration()` metode til at klasse OWIN Start Brug et **HttpConfiguration** objekt til at konfigurere miljøet Azure Mobile-Apps.
I følgende eksempel starter server projektet med ingen ekstra funktioner:

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

Hvis du vil aktivere bestemte funktioner, skal du ringe lokalnummer metoder for objektet **MobileAppConfiguration** før du kalder **ApplyTo**. For eksempel følgende kode føjer standard omdirigerer til alle API-enheder, der har attributten `[MobileAppController]` under initialisering:

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Server Hurtig start fra portalen Azure kalder **UseDefaultConfiguration()**. Denne visning, der svarer til opsætningen af følgende:

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

Filtypenavn metoderne er:

* `AddMobileAppHomeController()`indeholder standardstartsiden Azure Mobile-Apps.
* `MapApiControllers()`indeholder brugerdefinerede API-funktioner til WebAPI enheder dekoreret med den `[MobileAppController]` attributter.
* `AddTables()`indeholder en tilknytning af den `/tables` slutpunkter til tabel enheder.
* `AddTablesWithEntityFramework()`er en kort hånd til afbildning på `/tables` slutpunkter ved hjælp af enhed Framework baseret enheder.
* `AddPushNotifications()`indeholder en enkelt metode til at registrere enheder for meddelelse Hubs.
* `MapLegacyCrossDomainController()`indeholder standard CORS sidehoveder til lokale udvikling.

### <a name="sdk-extensions"></a>SDK filtypenavne

Følgende NuGet-baserede lokalnummer-pakker indeholder forskellige mobile funktioner, der kan anvendes af programmet. Du kan aktivere udvidelser under initialisering ved hjælp af objektet **MobileAppConfiguration** .

- [Microsoft.Azure.Mobile.Server.Quickstart] understøtter opsætningen af grundlæggende Mobile-Apps. Føjet til konfigurationen ved at ringe til metoden **UseDefaultConfiguration** lokalnummer under initialisering. Dette lokalnummer omfatter følgende filtypenavne: beskeder, godkendelse, enhed, tabeller, mellem-domæner og hjem pakker. Denne pakke er medtaget i Mobile-Apps Hurtig start tilgængelig på Azure-portalen.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 
   implementerer standard *denne-mobilappen er oppe at køre siden* for roden websted. Føje til konfigurationen ved at ringe til metoden   **AddMobileAppHomeController** filtypenavn.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 
   indeholder klasser til at arbejde med data, og sæt af data pipeline. Føje til konfigurationen ved at ringe til metoden **AddTables** filtypenavn.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) 
   gør det muligt for den enhed, som til access-data i SQL-Database. Føje til konfigurationen ved at ringe til metoden **AddTablesWithEntityFramework** filtypenavn.

- [Microsoft.Azure.Mobile.Server.Authentication] gør det muligt for godkendelse og sæt af den OWIN programmer, der bruges til at validere tokens. Føje til konfigurationen ved at ringe til **AddAppServiceAuthentication**  
   og **IAppBuilder**. **UseAppServiceAuthentication** lokalnummer metoder.

- [Microsoft.Azure.Mobile.Server.Notifications] gør det muligt for push-beskeder og definerer et push registrering slutpunkt. Føje til konfigurationen ved at ringe til metoden **AddPushNotifications** filtypenavn.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 
   opretter en controller, der fungerer data til ældre webbrowsere fra din Mobile-App. Føje til konfigurationen ved at ringe til metoden   **MapLegacyCrossDomainController** filtypenavn.

- [Microsoft.Azure.Mobile.Server.Login] indeholder metoden AppServiceLoginHandler.CreateToken(), som er en statisk metode bruges under brugerdefineret godkendelse scenarier.   

## <a name="publish-server-project"></a>Sådan: publicere projektet server

Dette afsnit viser, hvordan du publicere projektet .NET back end-fra Visual Studio. Du kan også distribuere din back end-projekt, ved hjælp af ciffer eller en af de andre metoder, der er dækket i [Azure App Service installation dokumentation](../app-service-web/web-sites-deploy.md).

1. Genopbygge projektet for at gendanne NuGet pakker i Visual Studio.

2. I Solution Explorer, højreklik på projektet, klik på **Publicer**. Første gang du publicerer, skal du definere en publicering profil. Når du har allerede en profil, der er defineret, kan du markere det og klik på **Publicer**.

2. Hvis du bliver bedt om at vælge en Udgiv destination, skal du klikke på **Microsoft Azure App Service** > **Næste**, og klik derefter (hvis det er nødvendigt) logge på med legitimationsoplysningerne Azure. 
   Visual Studio-overførsler og sikkert gemmer dine indstillinger for direkte fra Azure publicering.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Vælg dit **abonnement**, Vælg **Ressourcetype** fra **visningen**, udvide **Mobile-App**, og klik på din Mobile-App backend-version og derefter skal du klikke på **OK**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Bekræft profiloplysninger Udgiv, og klik på **Publicer**.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    Når din Mobile-App backend-version er udgivet, kan du se en landingsside, der angiver succes.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a>Sådan: definere controller en tabel

Definere en tabel Controller for at få vist en SQL-tabel til mobilklienter.  Konfiguration af en tabel Controller kræver tre trin:

1. Oprette en klasse Data overføre objekt (DTO).
2. Konfigurere en tabelreference i klassen Mobile DbContext.
3. Oprette en tabel controller.

Data overføre objekt (DTO) er et almindeligt C# objekt, der nedarver fra `EntityData`.  Eksempel:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO bruges til at definere tabellen i SQL-databasen.  For at oprette posten database skal du tilføje en `DbSet<>` egenskab, der skal DbContext du bruger.  I project standardskabelonen for Azure Mobile-Apps på DbContext kaldes `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Hvis du har Azure SDK installeret, kan du nu oprette en skabelon tabel controller på følgende måde:

1. Højreklik på mappen enheder og vælg **Tilføj** > **Controller …**.
2. Vælg indstillingen **Azure Mobile Apps tabel Controller** , og derefter klikke på **Tilføj**.
3. I dialogboksen **Tilføj Controller** :
    * Vælg din nye DTO i rullemenuen **Model klasse** .
    * Vælg Mobile Service DbContext klassen i rullemenuen **DbContext** .
    * Der oprettes Controller navn for dig.
4. Klik på **Tilføj**.

Hurtig start server project indeholder et eksempel for en simpel **TodoItemController**.

### <a name="how-to-adjust-the-table-paging-size"></a>Sådan: justere størrelsen på tabellen sideinddeling

Som standard returnerer Azure Mobile-Apps 50 poster per anmodning.  Sideopdeling sikrer, at klienten ikke binder op deres UI-tråd eller server i for lang tid at sikre en god brugeroplevelse. Hvis du vil ændre størrelsen på tabellen sideopdeling, øge serversiden "tilladelse størrelsen af forespørgslen" og siden klientsiden størrelse serversiden "tilladelse størrelsen af forespørgslen" justeres ved hjælp af den `EnableQuery` attribut:

    [EnableQuery(PageSize = 500)]

Sikre PageSize er den samme eller større end den størrelse, der er blevet anmodet af klienten.  Referere til bestemte klienten Sådan dokumentation for at få oplysninger om ændring af sidestørrelsen klient.

## <a name="how-to-define-a-custom-api-controller"></a>Sådan: definere en brugerdefineret API-enhed

Den brugerdefinerede API controller indeholder de mest grundlæggende funktioner til back-end din Mobile-App ved at udsætte et slutpunkt. Du kan registrere en mobile-specifikke API controller, ved hjælp af attributten [MobileAppController]. Den `MobileAppController` attribut registrerer ruten, konfigurerer Mobile Apps JSON serialiseringsfunktion og slår [client version fejlkontrol](app-service-mobile-client-and-server-versioning.md).

1. Højreklik på mappen enheder i Visual Studio, og derefter klikke på **Tilføj** > **Controller**, Vælg **Web API 2 Controller&mdash;tomme** og klikke på **Tilføj**.

2. Angive et **Controller navn**, såsom `CustomController`, og klik på **Tilføj**.

3. Tilføj følgende i den nye controller klassefil ved hjælp af sætning:

        using Microsoft.Azure.Mobile.Server.Config;

4. Anvende attributten **[MobileAppController]** til API controller klassedefinition, som i følgende eksempel:

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }

4. I App_Start/Startup.MobileApp.cs fil skal du tilføje et opkald til metoden **MapApiControllers** filtypenavn som i følgende eksempel:

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

Du kan også bruge den `UseDefaultConfiguration()` lokalnummer metode i stedet for `MapApiControllers()`. En hvilken som helst controller, der ikke har **MobileAppControllerAttribute** anvendt kan stadig åbnes af klienter, men den kan ikke korrekt benyttes fra kunder ved hjælp af enhver Mobile-App-klient SDK.

## <a name="how-to-work-with-authentication"></a>Sådan: arbejde med godkendelse

Azure Mobile-Apps anvender App Service godkendelse / tilladelse til at sikre dine mobile backend-version.  Dette afsnit beskrives, hvordan du udføre følgende godkendelse-relaterede opgaver i projektet .NET back end-server:

+ [Sådan: føje godkendelse til en project server](#add-auth)
+ [Sådan: Brug brugerdefinerede godkendelse for dit program](#custom-auth)
+ [Sådan: Hent godkendte brugeroplysninger](#user-info)
+ [Sådan: begrænse autoriserede brugere adgang til data](#authorize)

### <a name="add-auth"></a>Sådan: føje godkendelse til en project server

Du kan tilføje godkendelse til projektet server ved at forlænge objektet **MobileAppConfiguration** og konfigurere OWIN programmer. Når du har installeret pakken [Microsoft.Azure.Mobile.Server.Quickstart] og kalde metoden **UseDefaultConfiguration** filtypenavn, kan du gå videre til trin 3.

1. I Visual Studio, skal du installere pakken [Microsoft.Azure.Mobile.Server.Authentication] .

2. Tilføj følgende linje af kode i starten af metoden **konfiguration** i projektfil Startup.cs:

        app.UseAppServiceAuthentication(config);

    Denne OWIN program komponent har valideret tokens, der er udstedt af den tilknyttede gateway App Service.

3. Tilføje den `[Authorize]` attribut til en hvilken som helst controller eller metode, der kræver godkendelse. 

For at få mere for at vide om, hvordan du godkende klienter til din Mobile-Apps backend-version, se [Tilføj godkendelse til din app](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Sådan: Brug brugerdefinerede godkendelse for dit program

Hvis du ikke vil bruge en af udbyderne App Service godkendelse/godkendelse, kan du kan implementere login systemet. Installere pakken [Microsoft.Azure.Mobile.Server.Login] til at hjælpe med at token generering af godkendelse.  Angiv din egen programkode til validering af brugerlegitimationsoplysninger. For eksempel kan du se mod saltet og hash'et adgangskoder i en database. I eksemplet nedenfor, den `isValidAssertion()` metode (defineret et andet sted) er ansvarlig for kontrollen.

Brugerdefineret godkendelse der vises ved at oprette en ApiController og afsløre `register` og `login` handlinger. Kunden skal bruge en brugerdefineret brugergrænseflade til at indsamle oplysninger fra brugeren.  Oplysningerne sendes derefter til API med et standard HTTP POST-opkald. Når serveren har valideret program, et token er udstedt ved hjælp af den `AppServiceLoginHandler.CreateToken()` metode.  ApiController **skal ikke** bruge den `[MobileAppController]` attributter. 

Et eksempel `login` handling:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

I det foregående eksempel er LoginResult og LoginResultUser serialiserbar objekter udsætte obligatoriske egenskaber. Klienten forventer login svar skal returneres som JSON-objekter i formularen:

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

Den `AppServiceLoginHandler.CreateToken()` metode omfatter en _Målgruppe_ og en _udsteder_ parameter. Begge af disse parametre er sat til URL-adressen for dit programrod ved hjælp af HTTPS-skemaet. På samme måde skal du indstille _secretKey_ skal være værdien af dit program er logge nøgle. Distribuere ikke den signerende nøgle i en klient, som det kan bruges til at mynte nøgler og repræsentere brugere. Du kan få den signerende nede, mens vært App Service ved at referere til den _websted\_AUTH\_LOGGER\_nøgle_ miljøvariablen. Hvis det er nødvendigt i en lokal fejlfinding kontekst, skal du følge vejledningen i afsnittet [lokale fejlfinding med godkendelse](#local-debug) for at hente den pågældende nøgle og gemme den som en indstilling for programmet på computeren.

Udstedt tokenet kan også indeholde andre krav og en udløbsdato.  Minimalt, skal udstedt tokenet indeholde et emne (**sub**) krav.

Du kan understøtte den standardklient `loginAsync()` metode overbelastning ruten godkendelse af.  Hvis klienten anmoder `client.loginAsync('custom');` for at logge på din adgang skal være `/.auth/login/custom`.  Du kan angive ruten til den brugerdefinerede godkendelse controller ved hjælp af `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] Ved hjælp af den `loginAsync()` tilgang sikrer, at godkendelse Tokenet er knyttet til hver efterfølgende opkald til tjenesten.

###<a name="user-info"></a>Sådan: Hent godkendte brugeroplysninger

Når en bruger er godkendt af App-tjenesten, kan du få adgang til det tildelte bruger-ID og andre oplysninger i din .NET back end-kode. Brugeroplysningerne kan bruges til at oprette godkendelse beslutninger i backend-version. Følgende kode henter det bruger-ID, der er knyttet til en anmodning om:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID er afledt af providerspecifikke bruger-ID og er statisk for en given bruger og login udbyder.  SID er null for ugyldig godkendelsestokens.

App Service kan du anmode om bestemte krav fra udbyderen login. Hver identitetsudbyder kan få flere oplysninger ved hjælp af identitetsudbyder SDK.  Du kan for eksempel bruge Facebook Graph API til venner oplysninger.  Du kan angive krav, der ønskes i bladet udbyder i portalen Azure. Nogle krav kræver yderligere konfiguration med identitetsudbyder.

Følgende kode kalder metoden **GetAppServiceIdentityAsync** udvidelse for at få de logonoplysninger, som omfatter adgangstoken bruges til at gøre anmodninger på Facebook Graph API:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Tilføje en ved hjælp af erklæringen for `System.Security.Principal` til at levere metoden **GetAppServiceIdentityAsync** filtypenavn.

### <a name="authorize"></a>Sådan: begrænse autoriserede brugere adgang til data

Vi viste hvordan du kan hente bruger-ID på en godkendt bruger i det forrige afsnit. Du kan begrænse adgangen til data og andre ressourcer, der er baseret på denne værdi. Tilføje en bruger-id-kolonne til tabeller og filtrering forespørgselsresultaterne ved bruger-ID er en nem måde at begrænse returnerede data kun til autoriserede brugere. Følgende kode returnerer datarækker, hvis SID svarer til værdien i kolonnen bruger-id i tabellen TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Den `Query()` metode returnerer en `IQueryable` , der kan arbejde med LINQ til at håndtere filtrering.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>Sådan: tilføje opslagsnål beskeder til en project server

Føje pushmeddelelser til projektet server ved at forlænge objektet **MobileAppConfiguration** og oprette en meddelelse om Hubs klient.

1. I Visual Studio, skal du højreklikke på server projektet og klikke på **Administrer NuGet-pakker**, søge efter `Microsoft.Azure.Mobile.Server.Notifications`, klik derefter på **Installer**. 

2. Gentag dette trin for at installere den `Microsoft.Azure.NotificationHubs` pakke, som indeholder biblioteket meddelelse Hubs klient.

3. I App_Start/Startup.MobileApp.cs, og Tilføj et opkald til metoden **AddPushNotifications()** lokalnummer under initialisering:

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);

4. Føj følgende kode, der opretter en meddelelse om Hubs klient:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Du kan nu bruge meddelelse Hubs klienten til at sende pushmeddelelser til registrerede enheder. Du kan finde yderligere oplysninger finder [Tilføj pushmeddelelser til din app](app-service-mobile-ios-get-started-push.md). Hvis du vil vide mere om meddelelse Hubs, se [Meddelelse Hubs oversigt](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Sådan: Aktivér rettet opslagsnål ved hjælp af mærker

Meddelelse om Hubs kan du sende målrettede meddelelser til bestemte registreringer ved hjælp af mærker. Flere mærker oprettes automatisk:

* Installations-ID'ET identificerer en bestemt enhed.
* Det bruger-Id, der er baseret på den godkendte SID identificerer en bestemt bruger.

Installations-ID'ET kan åbnes fra egenskaben **offlineaktivering** på **MobileServiceClient**.  I følgende eksempel viser, hvordan du bruger et installations-ID til at føje et mærke til en bestemt installation i meddelelse Hubs:

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

Eventuelle mærker, der leveres af klienten under push meddelelse registrering ignoreres af back-, når du opretter installationen. Hvis du vil aktivere en klient til at føje mærker til installationen, skal du oprette en brugerdefineret API, der tilføjer mærker ved hjælp af den foregående mønster. 

Se [klient tilføjede opslagsnål meddelelse mærker] [ 5] i stikprøven færdige Hurtig start App Service Mobile-Apps til et eksempel.

##<a name="push-user"></a>Sådan: Send pushmeddelelser til en godkendt bruger

Når en godkendt bruger tilmelder sig pushmeddelelser, føjes et bruger-ID mærke automatisk til registrering. Ved hjælp af dette mærke, kan du sende pushmeddelelser til alle enheder, der er registreret af den pågældende person. Følgende kode bliver SID for brugeren, der indsætter anmodningen og sender en skabelon push-besked til enhver enhed registrering for den pågældende person:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Når registrering for pushbeskeder fra en godkendt klient, skal du kontrollere, at godkendelse er fuldført, før du forsøger registrering. Du kan finde flere oplysninger, se [overføre til brugere] [ 6] i stikprøven færdige Hurtig start App Service Mobile-Apps til .NET back end.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>Sådan: fejlfinding og foretage fejlfinding af .NET Server SDK

Azure App Service indeholder flere fejlfinding og fejlfinding teknikker til ASP.NET-programmer:

- [Overvågning af en Azure App-tjeneste](../app-service-web/web-sites-monitor.md)
- [Aktivere logføring af diagnostik i Azure App-tjeneste](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Fejlfinding i forbindelse med en Azure App-tjenesten i Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### <a name="logging"></a>Logføring

Du kan skrive til App Service diagnosticeringslogfiler ved hjælp af standard ASP.NET sporing skrivning. Før du kan skrive til loggene, skal du aktivere diagnosticering i back-end din Mobile-App.

Du aktiverer diagnosticering og skrives til loggene:

1. Følg trinnene i [Sådan aktiveres diagnosticering](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Tilføj følgende ved hjælp af sætning i din kodefil:

        using System.Web.Http.Tracing;

3. Oprette en sporing writer til at skrive fra .NET back-end til diagnosticeringslogfiler, på følgende måde:

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");

4. Udgive projektet server, og få adgang til Mobile-App back-end for at udføre kode stien med logføring.

5. Hente og evaluerer logfiler, som beskrevet i [Sådan: hente logge](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Lokale fejlfinding med godkendelse

Du kan køre programmet lokalt for at teste ændringerne før du udgiver dem til skyen. Tryk på *F5* i Visual Studio for de fleste Azure Mobile-Apps en back-end. Der er dog nogle yderligere overvejelser, når du bruger godkendelse.

Du skal have en skybaseret mobilapp med App Service godkendelse/godkendelse konfigureret, og kunden skal have skyen slutpunktet angivet som den alternative logonvært. Se i dokumentationen til din klient platform til de nødvendige bestemte trin.

Sørg for, at din mobile backend-version har [Microsoft.Azure.Mobile.Server.Authentication] installeret. Tilføj derefter følgende, når dit program OWIN Start klasse `MobileAppConfiguration` er blevet anvendt på din `HttpConfiguration`:

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

I det foregående eksempel, skal du konfigurere programindstillinger _authAudience_ og _authIssuer_ i filen Web.config til hver være URL-adressen for dit programrod ved hjælp af HTTPS-skemaet. På samme måde skal du indstille _authSigningKey_ skal være værdien af dit program er logge nøgle. Til at hente den signerende nøgle:

1. Gå til din app i [Azure-portalen] 
2. Klik på **værktøjer**, **Kudu**, **Gå**.
3. Klik på **miljø**til Kudu Management-webstedet.
4. Finde værdien for _websted\_AUTH\_LOGGER\_nøgle_. 

Brug tasten signerende for parameteren _authSigningKey_ i din lokale programmet config.  Din mobile backend-version er nu udstyret til at validere tokens, når du kører lokalt, som klienten henter tokenet fra det skybaseret slutpunkt.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure-portalen]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

