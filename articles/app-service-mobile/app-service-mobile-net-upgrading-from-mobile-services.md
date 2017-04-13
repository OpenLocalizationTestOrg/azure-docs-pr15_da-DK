<properties
    pageTitle="Opgradere fra Mobile-tjenester til Azure App Service"
    description="Lær, hvordan du nemt at opgradere din Mobile Services-program til en App-tjenesten Mobile-App"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Opgradere din eksisterende .NET Azure Mobile-tjeneste til App Service

App Service Mobile er en ny måde at opbygge mobilprogrammer ved hjælp af Microsoft Azure. Du kan få flere [Hvad er Mobile-Apps?].

Dette emne beskrives, hvordan du opgraderer en eksisterende .NET back end-program fra Azure Mobile-tjenester til en ny App Service Mobile-Apps. Mens du udfører denne opgradering, kan din eksisterende Mobile Services-program kan fortsætte med at fungere.   Hvis du vil opgradere et Node.js back end-program, skal du henvise til [opgradering Node.js Mobile-tjenester](./app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Når en mobil back-end er opgraderet til Azure App Service, har adgang til alle App Service-funktioner og er faktureret efter [App Service priser], ikke Mobile tjenester priser.

##<a name="migrate-vs-upgrade"></a>Overføre kontra opgradering

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Det anbefales, du [udføre en overførsel](app-service-mobile-migrating-from-mobile-services.md) før du gennemgår en opgradering. Denne måde, som du kan flytte begge versioner af programmet til den samme App Service planlægge og afholde gratis.

###<a name="improvements-in-mobile-apps-net-server-sdk"></a>Forbedringer i Mobile-Apps .NET server SDK

Opgradere til den nye [Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) indeholder følgende fordele:

- Større fleksibilitet på NuGet afhængigheder. Den værtsmiljø indeholder ikke længere sin egen versioner af NuGet-pakker, så du kan bruge alternative kompatible versioner. Men hvis der er nye kritiske bugfixes eller sikkerhedsopdateringer til Mobile Server SDK eller afhængigheder, skal du opdatere din tjeneste manuelt.

- Større fleksibilitet i mobile SDK. Du kan eksplicit styre, hvilke funktioner og omdirigerer er konfigureret, som godkendelse, tabel API'er og opslagsnål registrering slutpunkt. Hvis du vil vide mere, se, [hvordan du bruger .NET server SDK til Azure Mobile-Apps](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Understøttelse af andre ASP.NET projekttyper og omdirigerer. Nu kan du hoste MVC og Web API enheder i det samme projekt som dit mobile back end-projekt.

- Understøttelse af nye App Service godkendelsesfunktioner, som gør det muligt at bruge en almindelig godkendelse konfiguration på tværs af din web og mobilapps.

##<a name="overview"></a>Grundlæggende opgradering oversigt

I mange situationer bliver opgradering sværere end skifte til den nye Mobile-Apps server SDK og genudgive din kode til en ny forekomst af Mobile-App. Der er dog nogle scenarier, der kræver yderligere konfiguration, som Avanceret godkendelse scenarier og arbejde med planlagt job. Hver af disse er beskrevet i afsnittene senere.

>[AZURE.TIP] Det anbefales, at du læse og forstå resten af dette emne helt, før du starter en opgradering. Noter funktioner du bruger, som er vist nedenfor.

Mobile-tjenester klienten SDK'er er **ikke** kompatibel med den nye Mobile-Apps server SDK. For at give løbende-tjenesten for din app, skal du ikke publicere ændringer til et websted, der aktuelt tjener publicerede klienter. I stedet skal du oprette en ny mobilapp, der fungerer som en dublet. Du kan placere dette program i den samme App serviceaftale vil undgå af foretage yderligere finansielle omkostninger.

Du kan derefter har to versioner af programmet: en, der forbliver den samme og fungerer udgivet apps i vilde, og den anden, kan du derefter opgradere og målliste med en ny version af klienten. Du kan flytte og teste din kode i dit tempo, men du skal kontrollere, at en hvilken som helst fejlrettelser, du foretager få anvendes på begge. Når du mener, at et ønskede antal klient apps vilde har opdateret til den nyeste version, kan du slette den oprindelige overførte app Hvis du ønsker.

Fuld dispositionen for opgraderingsprocessen er som følger:

1. Oprette en ny Mobile-App
2. Opdatere projektet for at bruge den nye Server SDK'er
3. Slip en ny version af klientprogrammet
4. (Valgfrit) Slette din oprindelige overførte forekomst

##<a name="mobile-app-version"></a>Oprette en anden forekomst af programmet
Det første trin i forbindelse med opgradering er at oprette ressourcen Mobile-App, skal være vært for den nye version af programmet. Hvis du allerede har opgraderet en eksisterende mobile tjeneste, vil du oprette denne version på den samme hosting plan. Åbn [Azure-portalen] , og gå til dine overførte programmet. Noter på App-tjenesten planlægge den kører på.

Dernæst skal oprette den anden forekomst af programmet ved at følge [.NET back end-oprettelse af instruktioner](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Når du bliver bedt om at vælge du Vælg App-serviceaftale eller "vært plan" planen for dit overførte program.

Du vil højst sandsynligt at bruge den samme database og meddelelse-Hub, som du gjorde i Mobile-tjenester. Du kan kopiere disse værdier ved at åbne [Azure portal] og navigere til det oprindelige program og derefter klikke på **Indstillinger for** > **Programindstillinger**. Under **Strenge**, kopiere `MS_NotificationHubConnectionString` og `MS_TableConnectionString`. Gå til dit nye websted, opgradering, og sæt dem ind i, overskrive eventuelle eksisterende værdier. Gentag denne proces for eventuelle andre programindstillinger dine app behov. Hvis du ikke bruger en overført tjeneste, kan du læse strenge og app-indstillinger fra fanen **Konfigurer** i sektionen Mobile-tjenester på [Azure klassisk portal].

Oprette en kopi af ASP.NET projektet for dit program og publicere den til dit nye websted. Ved hjælp af en kopi af klientprogrammet opdateret med den nye URL-adresse, validere, at alt fungerer som forventet.

## <a name="updating-the-server-project"></a>Opdatering af server projektet

Mobile-Apps giver en ny [Mobile App Server SDK] som indeholder mange af de samme funktioner som Mobile Services runtime. Først skal du fjerne alle henvisninger til Mobile Services-pakker. I NuGet pakke manager skal du søge efter `WindowsAzure.MobileServices.Backend`. De fleste apps vil se flere pakker her, herunder `WindowsAzure.MobileServices.Backend.Tables` og `WindowsAzure.MobileServices.Backend.Entity`. I så fald starter med den laveste pakke i afhængighedstræet såsom `Entity`, og fjern den. Når du bliver bedt om det, Fjern ikke alle afhængige pakker. Gentag denne proces, indtil du har fjernet `WindowsAzure.MobileServices.Backend` sig selv.

På dette tidspunkt har du et projekt, der ikke længere henviser Mobile tjenester SDK'er.

Dernæst skal du tilføje referencer i Mobile-Apps SDK. Til denne opgradering, vil de fleste udviklere at hente og installere den `Microsoft.Azure.Mobile.Server.Quickstart` pakke, som dette vil trække i hele det nødvendige sæt.

Der vil være en hel kompilerfejl som følge af forskelle mellem SDK'er, men de er nemme at adresse og behandles i resten af dette afsnit.

### <a name="base-configuration"></a>Grundlæggende konfiguration

Derefter i WebApiConfig.cs, kan du erstatte:

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

med

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

>[AZURE.NOTE] Hvis du vil vide mere om den nye .NET server SDK og Sådan Tilføj/fjern funktioner fra din app, skal du se [Sådan bruger du .NET server SDK] emne.

Hvis din app gør brug af funktionerne til godkendelse, skal du også til at registrere en OWIN programmer. I dette tilfælde skal du flytte ovenstående konfigurationskode til en ny OWIN Start-klasse.

1. Tilføje pakken NuGet `Microsoft.Owin.Host.SystemWeb` Hvis den ikke allerede er inkluderet i projektet.
2. Højreklik på dit projekt i Visual Studio, og vælg **Tilføj** -> **Nyt element**. Vælg **Web** -> **generelle** -> **OWIN Start klasse**.
3. Flytte ovenstående kode for MobileAppConfiguration fra `WebApiConfig.Register()` til den `Configuration()` metode til den nye start-klasse.

Sørg for, at den `Configuration()` metode slutter med:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Der er relateret til godkendelse yderligere ændringer, der er omfattet i sektionen fuld godkendelse.

### <a name="working-with-data"></a>Arbejde med Data

Mobile appnavnet fungeret som skema standardnavnet i opsætningen af enhed Framework i Mobile-tjenester.

At sikre, at du har det samme skema, der refereres til som før, Brug følgende fremgangsmåde til at angive skemaet i DbContext for dit program:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Kontrollér, at du har angivet, hvis du ikke gør ovenstående MS_MobileServiceName. Du kan også angive et andet skemanavn, hvis dit program tidligere har tilpasset dette.

### <a name="system-properties"></a>Egenskaber for system

#### <a name="naming"></a>Navngive

Egenskaber for system indeholder altid en dobbelt understregning i server Azure Mobile Services SDK (`__`) præfiks for egenskaberne:

- __createdAt
- __updatedAt
- __deleted
- __version

Mobile-tjenester klienten SDK'er har særlig logik til fortolkning af egenskaber for system i dette format.

Egenskaber for system ikke længere har et specielt format i Azure Mobile-Apps og har følgende navne:

- createdAt
- updatedAt
- slettet
- version

Mobile-Apps klienten SDK'er bruge de nye system egenskaber navne, så der kræves til klienten kode ingen ændringer. Men hvis du laver direkte RESTEN opkald til din tjeneste skal du ændre dine forespørgsler i overensstemmelse hermed.

#### <a name="local-store"></a>Lokale lager

Ændringer til navnene på Egenskaber for system betyder, at en lokal database offlinesynkronisering til Mobile-tjenester ikke er kompatibel med Mobile-Apps. Hvis det er muligt, skal du undgå at opgradering af klienten apps fra Mobile-tjenester med Mobile Apps indtil efter ventende ændringer der er sendt til serveren. Opgraderet app skal derefter fortsætte med et nyt filnavn i databasen.

Hvis en klient app er opgraderet fra Mobile-tjenester til Mobile-Apps, mens der er ventende offline ændringer i køen handling, skal systemdatabasen opdateres for at bruge de nye kolonnenavne. På iOS, kan dette opnås ved hjælp af optimerede overførsler ændre kolonnenavnene. På Android og .NET administrerede klienten, skal du skrive brugerdefinerede SQL for at omdøbe kolonnerne til dine data objekt tabeller.

På iOS, skal du ændre dit Core Data skema for dine data-enheder, så det svarer til følgende. Bemærk, at egenskaberne `createdAt`, `updatedAt` og `version` ikke længere har en `ms_` præfiks:

| Attribut |  Type   | Bemærk!                                                 |
|---------- |  ------ | -----------------------------------------------------|
| id        | Streng, der er markeret som obligatorisk  | primær nøgle i remote store         |
| createdAt | Dato    | (valgfrit) kort til createdAt systemegenskab         |
| updatedAt | Dato    | (valgfrit) kort til updatedAt systemegenskab         |
| version   | Streng  | (valgfrit) bruges til at finde konflikter, kort til version |

#### <a name="querying-system-properties"></a>Forespørgsler egenskaber for system

Azure Mobile tjenesterne, egenskaber for system ikke sendes som standard, men kun, når der anmodes om ved hjælp af forespørgselsstrengen `__systemProperties`. Derimod i Azure Mobile-Apps system er egenskaber **altid markeret** fordi de er en del af objektmodellen server SDK.

Denne ændring påvirker hovedsageligt brugerdefinerede installationer domæne ledere, som er udvidelser af `MappedEntityDomainManager`. I Mobile-tjenester, hvis en klient aldrig anmoder om en hvilken som helst egenskaber for system, er det muligt at bruge en `MappedEntityDomainManager` , der ikke faktisk knytte alle egenskaber. Dog kan disse ikke-tilknyttede egenskaber i Azure Mobile-Apps medfører en fejl i få forespørgsler.

Den nemmeste måde at løse problemet er at ændre din DTOs, så de nedarver fra `ITableData` i stedet for `EntityData`. Tilføj derefter den `[NotMapped]` attribut til de felter, der skal udelades.

For eksempel følgende definerer `TodoItem` uden system egenskaber:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Bemærk: Hvis du får fejl `NotMapped`, tilføje en reference til samlingen `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS

Nogle understøttelse af CORS inkluderet som ombrydning løsningen ASP.NET CORS Mobile-tjenester. Dette ombrydning lag er blevet fjernet for at give udvikleren mere kontrol, så du kan udnytte [ASP.NET CORS understøtter](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)direkte.

Til hovedområderne i vedrører Hvis bruger CORS er, at den `eTag` og `Location` sidehoveder må i rækkefølge for klienten SDK'er kan fungere korrekt.

### <a name="push-notifications"></a>Push-beskeder
For push er det primære element, du kan finde mangler fra Server SDK klassen PushRegistrationHandler. Registreringer skal håndteres lidt anderledes i Mobile-Apps, og tagless registreringer er aktiveret som standard. Administrere mærker kan gøres ved hjælp af brugerdefinerede API'er. Se vejledningen [registrering efter mærker](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) for at få flere oplysninger.

### <a name="scheduled-jobs"></a>Planlagte opgaver
Planlagte opgaver ikke er indbygget i Mobile-Apps, så alle eksisterende sager, som du har i din .NET back-end skal opgraderes enkeltvis. En af mulighederne er at oprette et planlagt [Web tingene] på webstedet Mobile-App-kode. Du kan også angive en controller, der indeholder din sag kode og konfigurere [Azure Scheduler] for at der påløber pågældende slutpunkt på den forventede tidsplan.

### <a name="miscellaneous-changes"></a>Diverse ændringer
Alle ApiControllers som vil blive brugt i en mobilklient nu skal have den `[MobileAppController]` attributter. Dette er ikke længere som standard inkluderes så, at andre ApiControllers til at blive sendt påvirket af mobile formatters.

Den `ApiServices` objektet er ikke længere del af SDK. For at få adgang til indstillinger for Mobile-App, kan du bruge følgende:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

Logføring opnås på samme måde, nu, ved hjælp af standard ASP.NET sporing skrivning:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Overvejelser i forbindelse med godkendelse

Godkendelse komponenterne i Mobile-tjenester blevet nu flyttet til funktionen App Service godkendelse/godkendelse. Du kan få mere at vide om aktivering dette til dit websted ved at læse emnet [Tilføj godkendelse til din mobile-app](app-service-mobile-ios-get-started-users.md) .

For nogle udbydere, som AAD, Facebook og Google, skal du kunne udnytte den eksisterende registrering fra e-kopi. Du skal blot gå til den identitetsudbyder portal og tilføje en ny Omdiriger URL-adresse til registrering. Konfigurer derefter App Service godkendelse/godkendelse med klient-ID og hemmeligt.

### <a name="controller-action-authorization"></a>Controller handling godkendelse
Alle forekomster af det `[AuthorizeLevel(AuthorizationLevel.User)]` attribut skal nu ændres for at bruge den standard ASP.NET `[Authorize]` attributter. Desuden er enheder nu anonym som standard, som andre ASP.NET-programmer.
Hvis du bruger en af de andre AuthorizeLevel indstillinger, som administrator eller program, Vær opmærksom på, at dette er fjernet. I stedet kan du konfigurere AuthorizationFilters til delte hemmeligheder eller konfigurere en AAD Service hovedstolen for at aktivere-tjenester opkald sikkert.

### <a name="getting-additional-user-information"></a>Få flere brugeroplysninger

Du kan finde yderligere brugeroplysninger, herunder access tokens gennem den `GetAppServiceIdentityAsync()` metode:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

Desuden, hvis dit program tager afhængigheder på bruger-id'er, som gemme dem i en database, er det vigtigt at bemærke, at bruger-id'er mellem Mobile-tjenester og App-tjenesten Mobile-Apps er forskellige. Du kan stadig åbne Mobile Services bruger-ID, via. Alle ProviderCredentials underklasserne har et bruger-id-egenskaben. Så fortsætter fra eksemplet før:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Hvis din app tager afhængigheder på bruger-id'er, er det vigtigt, at du udnytte den samme registrering til en identitetsudbyder, hvis det er muligt. Bruger-id'er er typisk fastsat til registreringen program, der blev brugt, så Introduktion til en ny registrering kan give problemer med identiske brugere til deres data.

### <a name="custom-authentication"></a>Brugerdefineret godkendelse

Hvis din app bruger en tilpasset godkendelsesløsning, vil du kontrollere, at det opgraderede websted har adgang til systemet. Følg de nye instruktioner til brugerdefineret godkendelse i [.NET server SDK oversigt] til at integrere din løsning. Bemærk, at de brugerdefinerede godkendelse komponenter er stadig i Vis udskrift.

##<a name="updating-clients"></a>Opdaterer klienter
Når du har en funktionsdygtige Mobile-App backend-version, kan du arbejde på en ny version af klientprogrammet der forbruger den. Mobile-Apps indeholder også en ny version af klienten SDK'er og svarende til at serveren er opgraderet ovenfor, skal du fjerne alle henvisninger til Mobile tjenester SDK'er før du installerer Mobile-Apps versioner.

En af de vigtigste ændringer mellem versionerne er, at konstruktører ikke længere kræver en programtasten. Du nu sende blot i URL-adressen på din Mobile-App. For eksempel på .NET-klienter, den `MobileServiceClient` parametre er nu:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Du kan læse om installation af de nye SDK'er og bruge den nye struktur via linkene nedenfor:

- [iOS version 3.0.0 eller nyere](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 eller nyere](app-service-mobile-dotnet-how-to-use-client-library.md)

Hvis dit program gør brug af pushmeddelelser, skal du notere de bestemte registrering instruktioner til hver platform, som der er nogle ændringer der også.

Når du har den klar ny klientversion, prøv det mod projektet opgraderede server. Efter validering af, at den fungerer, kan du slipper en ny version af programmet til kunder. Til sidst, når dine kunder har haft mulighed for at modtage disse opdateringer, kan du slette Mobile-tjenester versionen af din app. Du har på dette tidspunkt helt opgraderet til en App-tjenesten Mobile-App, der bruger den seneste Mobile-Apps server SDK.

<!-- URLs. -->

[Azure-portalen]: https://portal.azure.com/
[Azure klassisk portal]: https://manage.windowsazure.com/
[Hvad er Mobile-Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile-App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web-Job]: ../app-service-web/websites-webjobs-resources.md
[Sådan bruger du .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service priser]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Oversigt over .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
