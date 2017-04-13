<properties
    pageTitle="Geografisk indhegnet pushmeddelelser med Azure meddelelse Hubs og Bing geografiske Data | Microsoft Azure"
    description="I dette selvstudium lærer du at levere placering-baseret pushmeddelelser med Azure meddelelse Hubs og Bing geografiske Data."
    services="notification-hubs"
    documentationCenter="windows"
    keywords="Push-meddelelse, push-meddelelse"
    authors="dend"
    manager="yuaxu"
    editor="dend"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-phone"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dendeli"/>
    
# <a name="geo-fenced-push-notifications-with-azure-notification-hubs-and-bing-spatial-data"></a>Geografisk indhegnet pushmeddelelser med Azure meddelelse Hubs og Bing geografiske Data
 
 > [AZURE.NOTE] For at fuldføre dette selvstudium skal skal du have en active Azure-konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto på blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

I dette selvstudium lærer du at levere placering-baseret pushmeddelelser med Azure meddelelse Hubs og Bing geografiske Data, opgraderede fra den i et program Universal Windows-platforme.

##<a name="prerequisites"></a>Forudsætninger
Først og fremmest skal du kontrollere, at du har alle software og tjenester forudsætninger:

* [Visual Studio 2015 opdatering 1](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) eller nyere ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) gør samt). 
* Seneste version af [Azure SDK](https://azure.microsoft.com/downloads/). 
* [Bing Maps-Udviklercenter konto](https://www.bingmapsportal.com/) (du kan oprette en gratis og knytte den til din Microsoft-konto). 

##<a name="getting-started"></a>Kom godt i gang

Lad os starte med at oprette projektet. I Visual Studio, skal du starte et nyt projekt af typen **Tom App (Universal Windows)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Når projektoprettelsen er fuldført, bør du have enhedens til selve appen. Nu Lad os oprette alt for geografisk indhegning infrastrukturen. Da vi skal bruge Bing tjenester til dette, er der en offentlig REST-API slutpunkt, der gør det muligt for os til at forespørge bestemte placering rammer:

    http://spatial.virtualearth.net/REST/v1/data/
    
Du skal angive følgende parametre for at få det til at fungere:

* **Datakilde-ID** og **Datakildenavn** – i Bing Maps API-datakilder, der indeholder forskellige bucketed metadata, som placeringer og business åbningstider. Du kan læse mere om disse her. 
* **Objektnavn** – den enhed, du vil bruge som reference for meddelelsen. 
* **Bing Maps API-nøgle** – dette er den nøgle, som du hentede tidligere, når du har oprettet Bing Udviklercenter kontoen.
 
Lad os gøre en gå i dybden på konfigurationen for hver af de elementer, der er ovenfor.

##<a name="setting-up-the-data-source"></a>Konfiguration af datakilden

Du kan gøre det i Bing Maps-Udviklercenter. Blot klikke på **datakilder** i den øverste navigationslinje, og vælg **Administrer datakilder**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Hvis du ikke har arbejdet med Bing Maps API før, højst sandsynligt der ikke nogen datakilder, der findes, så du kan kun oprette en ny ved at klikke på Overfør data til en datakilde. Sørg for, at du udfylde alle de obligatoriske felter:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Du tænker muligvis – Hvad er datafilen, og hvad skal du overførsel? Til at udføre denne test, kan vi blot bruge stikprøvernes pipe-baserede, der indeholder rammer et område af San Francisco havnefront:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Ovenstående repræsenterer denne enhed:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Blot kopiere og indsætte ovenstående streng i en ny fil og gemme den som **NotificationHubsGeofence.pipe**, og Overfør det i Bing-Udviklercenter.

>[AZURE.NOTE]Du muligvis bedt om at angive en ny nøgle til **Master nøgle** , der er forskellig fra den **Forespørgsel nøgle**. Oprette en ny nøgle gennem dashboardet blot, og Opdater overførselssiden datakilder.

Når du overfører datafilen, skal du kontrollere, at du publicerer datakilden. 

Gå til **Administrere datakilder**, ligesom vi har ovenfor, finde datakilden på listen, og klik på **Publicer** i kolonnen **Actions** . I en smule, skal du se afsnittet din datakilde under fanen **Publicerede datakilder** :

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Hvis du klikker på **Rediger**, vil du kunne se overblik hvad vi introduceret i den placeringer:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

På dette tidspunkt viser portalen ikke du grænserne for geofence, vi oprettede – vi skal blot en bekræftelse, som den angivne placering er i nærheden højre.

Nu har du alle betingelser for datakilden. Klik på **datakilder** for at få oplysninger om anmodningen URL-adressen til opkaldet, skal du API i Bing Maps-Udviklercenter, og vælg **Oplysninger om datakilden**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

**Forespørgsel URL-adresse** er, hvad vi taler her. Dette er det slutpunkt, som vi kan udføre forespørgsler for at kontrollere, om enheden er i øjeblikket i grænserne for en placering eller ej. For at udføre denne kontrol, skal vi blot udføre et få opkald mod URL-adressen forespørgsel med følgende parametre føjet:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

Peg på denne måde, du angiver en destination, som vi indhente fra enheden og Bing Maps udfører automatisk beregninger for at se, om det er i geofence. Når du udfører anmodningen via en browser (eller krøllet), får du standard et JSON-svar:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Dette svar sker kun, når punktet er faktisk inden for angivne grænser. Hvis det ikke er, får du en tom **resultater** Malerbøtte:

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##<a name="setting-up-the-uwp-application"></a>Konfiguration af programmet UWP

Nu hvor vi har den datakilde, der er klar, kan vi begynde at arbejde på UWP programmet, som vi initialiseres fra tidligere.

Først og fremmest skal vi aktivere placering tjenester til vores program. For at gøre dette, skal du dobbeltklikke på `Package.appxmanifest` fil i **Solution Explorer**.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

Klik på **Funktioner** i pakke egenskabsfanen, der lige er åbnet, og Sørg for, at du markerer **placering**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Som er erklæret muligheden for placering, kan du oprette en ny mappe i din løsning med navnet `Core`, og Tilføj en ny fil i det, kaldet `LocationHelper.cs`:

![](./media/notification-hubs-geofence/vs-location-helper.png)

Den `LocationHelper` selve klassen er ret grundlæggende på dette tidspunkt – alt det er tilfældet, er giver os mulighed for få brugerplacering hele systemet API:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Du kan læse mere om at få brugerens placering i UWP apps i officielle [MSDN-dokumentet](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).

For at kontrollere, at placering acquisition rent faktisk fungerer, åbne siden kode på din primære side (`MainPage.xaml.cs`). Oprette en ny hændelseshandler for den `Loaded` begivenhed i den `MainPage` parametre:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

Implementeringen af hændelseshandleren er som følger:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Bemærk, at vi erklæret handleren som asynkron, fordi `GetCurrentLocation` er awaitable, og derfor kræver, at der skal udføres i en asynkron kontekst. Da under visse omstændigheder vi ender muligvis med en null placering (fx placeringen services er deaktiveret eller programmet blev nægtet tilladelse til at få adgang til placering), skal vi også Sørg for, at den er korrekt håndteret med null Tjek.

Kør programmet. Sørg for, at du tillader adgang placering:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Når programmet startes, du skal kunne se koordinaterne i vinduet **Output** :

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Nu ved du, at placering acquisition fungerer – Velkommen til at fjerne hændelseshandler test til indlæst, fordi vi ikke skal bruge den længere.

Næste trin er at registrere placering ændringer. Til det, skal du gå tilbage til den `LocationHelper` klasse og tilføje hændelseshandler for `PositionChanged`:

    geolocator.PositionChanged += Geolocator_PositionChanged;

Implementeringen vil vise koordinaterne placering i vinduet **Output** :

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##<a name="setting-up-the-backend"></a>Konfiguration af backend-version

Hente [.NET back end-eksempel fra GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Når overførslen er fuldført, skal du åbne den `NotifyUsers` mappe, og derefter – den `NotifyUsers.sln` fil.

Angiv den `AppBackend` project som **Start Project** og start den.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Projektet er allerede konfigureret til at sende pushmeddelelser til destinationsenheder, så vi skal gøre kun to ting – udskifte den rette forbindelse streng til hubben meddelelse og tilføje rammen identifikation for at sende meddelelsen, kun, når brugeren er inden for geofence.

Du kan konfigurere forbindelsesstrengen, i den `Models` mappe Åbn `Notifications.cs`. Den `NotificationHubClient.CreateClientFromConnectionString` funktionen skal indeholde oplysninger om din meddelelse-hub, som du kan komme på [Azure-portalen](https://portal.azure.com) (se i bladet **Access politikker** i **Indstillinger**). Gemme den opdaterede konfigurationsfil.

Nu skal vi oprette en model til Bing kort API resultatet. Er den nemmeste måde at gøre det, skal du højreklikke på den `Models` mappe, **Tilføj** > **klasse**. Kald den `GeofenceBoundary.cs`. Når færdig, skal du kopiere JSON fra API svaret, som vi beskrevet i den første sektion og i Visual Studio Brug **redigere** > **Indsæt speciel** > **Indsæt JSON som klasser**. 

På denne måde, vi sikre, at objektet bliver deserialiseret nøjagtigt, som det er beregnet. Din klasse resultatsættet skal ligne dette:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Derefter skal du åbne `Controllers`  >  `NotificationsController.cs`. Vi vil tilpasse indlæg opkaldet kontoen for target længde- og længde. I dette tilfælde skal du blot tilføje to strenge til funktionen signatur – `latitude` og `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Oprette en ny klasse i projektet kaldet `ApiHelper.cs` – vi skal bruge til at oprette forbindelse til Bing til at kontrollere Peg rammen skæringspunkterne. Implementere en `IsPointWithinBounds` funktion, som dette:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Sørg for at erstatte API slutpunktet med forespørgsel URL-adressen, som du tidligere har hentet fra Bing-Udviklingscenter Center (det samme gælder for API-nøglen). 

Hvis der er resultater til forespørgslen, betyder det, er det angivne punkt inden for kanten af geofence, så vi gå tilbage `true`. Hvis der er ingen resultater, Bing fortælle os, er punktet uden for ruden opslag, så vi returnere `false`.

Tilbage i `NotificationsController.cs`, opretter en check lige før switch-sætning:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

På denne måde meddelelsen sendes kun når punktet er inden for grænserne.

##<a name="testing-push-notifications-in-the-uwp-app"></a>Test pushmeddelelser i appen UWP

Gå tilbage til appen UWP, bør vi nu kunne teste beskeder. I den `LocationHelper` klasse, skal du oprette en ny funktion – `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Udskifte det `POST_URL` til placeringen for udløst webprogrammet, vi oprettede i forrige afsnit. Nu er det OK at køre den lokalt, men når du arbejder på at implementere en offentlig version, skal du placere den med en ekstern udbyder.

Lad os nu Sørg for, at vi registrerer UWP app til pushmeddelelser. Klik på **projekt**i Visual Studio, > **gemme** > **knytte app med store**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Når du logger på kontoen udvikler, Sørg for, at du markerer en eksisterende app eller oprette en ny og knytte pakken med den. 

Gå til den Udviklercenter, og Åbn den app, du lige har oprettet. Klik på **tjenester** > **Push-beskeder** > **Live Services-websted**.

![](./media/notification-hubs-geofence/ms-live-services.png)

På webstedet, skal du bemærke af **Programmet hemmeligt** og **Pakke SID**. Du skal både i portalen Azure – Åbn din meddelelse-hub skal du klikke på **Indstillinger for** > **Meddelelse Services** > **Windows (WNS)** og angive oplysninger i de påkrævede felter.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Klik på **Gem**.

Højreklik på **referencer** i **Solution Explorer** , og vælg **Administrer NuGet pakker**. Vi skal tilføje en reference til **Microsoft Azure Service Bus administrerede bibliotek** – blot søge efter `WindowsAzure.Messaging.Managed` og tildele den til dit projekt.

![](./media/notification-hubs-geofence/vs-nuget.png)

Til testformål, kan vi oprette den `MainPage_Loaded` hændelseshandler igen, og Føj denne kodestykke til det:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Ovenstående registrerer appen med besked om hub. Du er klar til at gå! 

I `LocationHelper`, indvendig den `Geolocator_PositionChanged` handler, kan du tilføje en del af en test kode, som vil tvungen lægge sted i geofence:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Da vi ikke overfører reelle koordinaterne (som ikke muligvis i grænserne i øjeblikket) og bruger foruddefinerede test værdier, ser vi en meddelelse, der vises på opdatering:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##<a name="whats-next"></a>Hvad skal der ske nu?

Der er et par trin, du skal muligvis følge ud over ovenstående for at sikre, at løsningen er klar til fremstilling.

Først og fremmest, kan det være nødvendigt at sikre, at geofences er dynamisk. Dette kræver noget ekstra arbejde med Bing API for at kunne overføre nye grænser inden for den eksisterende datakilde. Se i [dokumentationen til Bing geografiske Data Services API](https://msdn.microsoft.com/library/ff701734.aspx) få mere at vide om emnet.

Som du arbejder for at sikre, at leveringen sker til de rigtige deltagere, kan du vil målrette dem via [mærkning](notification-hubs-tags-segment-push-message.md).

Løsningen ovenstående beskriver et scenarie, hvor du muligvis en lang række målplatforme, så vi ikke begrænse geofencing til system-specifikke egenskaber. Dog Universal Windows-platformen indeholder funktioner til at [registrere geofences højre ud af-i](https://msdn.microsoft.com/windows/uwp/maps-and-location/set-up-a-geofence).

Se vores [dokumentation portal](https://azure.microsoft.com/documentation/services/notification-hubs/)for flere oplysninger om meddelelse Hubs funktioner.
