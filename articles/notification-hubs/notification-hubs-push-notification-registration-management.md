<properties
    pageTitle="Administration af registrering"
    description="Dette emne beskriver, hvordan du registrere enheder med besked om hubs for at modtage pushmeddelelser."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="registration-management"></a>Administration af registrering

##<a name="overview"></a>Oversigt

Dette emne beskriver, hvordan du registrere enheder med besked om hubs for at modtage pushmeddelelser. Emnet beskrives registreringer på højt niveau og derefter introducerer de to primære mønstre for din registrering enheder: registrere fra enheden direkte til meddelelse hubben og registrere gennem et program backend-version. 


##<a name="what-is-device-registration"></a>Hvad er enhed registrering

Registrering af enhed med en meddelelse om Hub opnås ved hjælp af en **registrering** eller **Installation**.

#### <a name="registrations"></a>Registreringer
En registrering knytter håndtaget Platform meddelelse Service (PNS) til en enhed med mærker og eventuelt en skabelon. Håndtaget PNS kan være en ChannelURI, enhed token eller GCM registrering-id. Mærker bruges til at sende meddelelser til det korrekte sæt af markeringshåndtagene for enhed. Få mere at vide under [Routing og mærke udtryk](notification-hubs-tags-segment-push-message.md). Skabeloner bruges til at implementere per registrering transformation. Du kan finde flere oplysninger under [skabeloner](notification-hubs-templates-cross-platform-push-messages.md).

#### <a name="installations"></a>Installationer
En Installation er en forbedret registrering, der indeholder en pose med opslagsnål relateret egenskaber. Det er den seneste og bedste metode til at registrere dine enheder. Det er dog ikke understøttes af klientsiden .NET SDK ([Besked om Hub SDK til back end-handlinger](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) og endnu.  Det betyder, at hvis du registrerer fra klientenhed selve, vil du skal bruge [Meddelelse Hubs REST-API](https://msdn.microsoft.com/library/mt621153.aspx) metode til at understøtte installationer. Hvis du bruger en back end-tjeneste, skal du kunne bruge [Meddelelse Hub SDK til back end-handlinger](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Følgende er nogle vigtige fordele ved at bruge installationer:

* Oprette eller opdatere en installation er fuldt idempotent. Så kan du prøve det uden at en hvilken som helst bekymre sig om dublerede registreringer.
* Installation modellen gør det nemt at gøre individuelle skubber - målretning af bestemte enhed. Mærket system **"$InstallationId: [installationId]"** føjes automatisk til hver installation baseret registrering. Så kan du ringe til en send til dette mærke målrette en bestemt enhed uden at skulle gøre noget ekstra om koder.
* Brug af installationer kan du også gøre delvis registrering opdateringer. Delvis opdatering af en installation, der anmodes om med en programrettelse metode ved hjælp af [JSON-programrettelse standard](https://tools.ietf.org/html/rfc6902). Dette er især nyttig, når du vil opdatere mærker på registreringen. Du behøver at rullemenu med hele registreringen og derefter sende igen alle de forrige mærker igen.

En installation kan indeholde de følgende egenskaber. Finde en komplet oversigt over installationen egenskaber under [Opret eller Overskriv en Installation med REST-API](https://msdn.microsoft.com/library/azure/mt621153.aspx) eller [Installationsegenskaber](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx) for den.

    // Example installation format to show some supported properties
    {
        installationId: "",
        expirationTime: "",
        tags: [],
        platform: "",
        pushChannel: "",
        ………
        templates: {
            "templateName1" : {
                body: "",
                tags: [] },
            "templateName2" : {
                body: "",
                // Headers are for Windows Store only
                headers: {
                    "X-WNS-Type": "wns/tile" }
                tags: [] }
        },
        secondaryTiles: {
            "tileId1": {
                pushChannel: "",
                tags: [],
                templates: {
                    "otherTemplate": {
                        bodyTemplate: "",
                        headers: {
                            ... }
                        tags: [] }
                }
            }
        }
    }

 

Det er vigtigt at være opmærksom på, at registreringer og installationer som standard ikke længere udløber.

Registreringer og installationer skal indeholde et gyldigt PNS håndtag for hver enhed/kanal. Da PNS håndtag kan kun fås i en klient app på enheden, er et mønster til at registrere direkte på enheden med klient-app. På den anden side overvejelser om sikkerheden og forretningslogik, der er relateret til mærker kræver muligvis dig med at administrere enhed registrering i app-back-end. 

#### <a name="templates"></a>Skabeloner

Hvis du vil bruge [skabeloner](notification-hubs-templates-cross-platform-push-messages.md), holde installationen af enheden også alle de skabeloner, der er forbundet med den pågældende enhed i en JSON filformat (se eksemplet ovenfor). Navne på de hjælpe target forskellige skabeloner til den samme enhed.

Bemærk, at hver skabelonnavn kort med helhed skabelon og et valgfrit sæt over mærker. Desuden kan hver platform have ekstra skabelonegenskaber. Til Windows Store (ved hjælp af WNS) og Windows Phone 8 (ved hjælp af MPNS), kan et ekstra sæt af sidehoveder være en del af skabelonen. Hvis det er APNs, kan du indstille egenskaben et udløbet til enten en konstant eller til en skabelon udtryk. For en komplet oversigt over installationen egenskaber i emnet, [Opret eller Overskriv en Installation med RESTEN](https://msdn.microsoft.com/library/azure/mt621153.aspx) .

#### <a name="secondary-tiles-for-windows-store-apps"></a>Sekundær felter til Windows Store-Apps

Til Windows Store-klientprogrammer, sende beskeder til sekundær felter er den samme som at sende dem til den primære. Dette er også understøttet i installationer. Bemærk, at sekundær felter har en anden ChannelUri, som SDK på din klient app håndterer transparent.

SecondaryTiles ordbogen bruger den samme TileId, der bruges til at oprette objektet SecondaryTiles i Windows Store app.
Som i den primære ChannelUri kan ændre ChannelUris af sekundær felter på et tidspunkt. For at holde installationerne i meddelelse hubben opdateret, skal enheden opdatere dem med den aktuelle ChannelUris af de sekundære felter.


##<a name="registration-management-from-the-device"></a>Registrering Administration fra enheden

Når du administrerer enhed registrering fra klient apps, er backend-version er kun ansvarlig for at sende meddelelser. Klient apps holde PNS håndtag opdateret, og Registrer mærker. Billedet nedenfor illustrerer dette mønster.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Enheden først henter håndtaget PNS fra PNS og derefter registrerer med besked om hub direkte. Når registreringen er fuldført, kan app back-end sende en meddelelse om målretning af, at registreringen. Du kan finde flere oplysninger om, hvordan du vil sende beskeder, skal du se [Routing og mærke udtryk](notification-hubs-tags-segment-push-message.md).
Bemærk, at det er tilfældet, du vil bruge lytter kun rettigheder til at få adgang til din meddelelse hubs fra enheden. Du kan finde flere oplysninger under [sikkerhed](notification-hubs-push-notification-security.md).

Registrere fra enheden er den nemmeste metode, men der er visse ulemper.
Den første ulempe er, at en klient app kan kun opdatere dens mærker, når appen er aktiv. Eksempelvis hvis en bruger har to enheder, der registrerer mærker, der er relateret til sportsgren teams, når den første enhed tilmelder sig en ekstra mærke (for eksempel Seahawks), modtager den anden enhed ikke meddelelser om Seahawks indtil app på den anden enhed er udført endnu en gang. Når mærker påvirkes af flere enheder, er administrere mærker fra back-end mere generelt en bør indstilling.
Den anden ulempe af registrering Administration fra klient app er, at da apps kan være hacket, sikring af registreringen til bestemte koder kræver ekstra omtanke, som beskrevet i afsnittet "mærke sikkerhed på brugerniveau."



#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Eksempler på kode til at registrere med en meddelelse om hub fra en enhed med en installation 

På nuværende tidspunkt handling denne understøttes kun ved hjælp af [Besked om Hubs REST-API](https://msdn.microsoft.com/library/mt621153.aspx).

Du kan også bruge metoden programrettelse ved hjælp af [JSON-programrettelse standard](https://tools.ietf.org/html/rfc6902) for at opdatere installationen.

    class DeviceInstallation
    {
        public string installationId { get; set; }
        public string platform { get; set; }
        public string pushChannel { get; set; }
        public string[] tags { get; set; }
    }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
         string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See, https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                        "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Eksempler på kode til at registrere med en meddelelse om hub fra en enhed med en registrering


Disse metoder oprette eller opdatere en registrering for den enhed, som de hedder. Det betyder, at for at opdatere håndtaget eller mærkerne, du skal for at overskrive den hele registrering. Husk, at registreringer er forbigående, så du altid skal have en pålidelig store med de aktuelle koder, der kræver en bestemt enhed.


    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
    // storage. Then when the app starts, you can check if a registration id already exists or not before
    // creating.
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a registration id in application data, store in application data.
    if (!settings.ContainsKey("__NHRegistrationId"))
    {
        // make sure there are no existing registrations for this push handle (used for iOS and Android)    
        string newRegistrationId = null;
        var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
        foreach (RegistrationDescription registration in registrations)
        {
            if (newRegistrationId == null)
            {
                newRegistrationId = registration.RegistrationId;
            }
            else
            {
                await hub.DeleteRegistrationAsync(registration);
            }
        }

        newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
    }
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

    try
    {
        await hub.CreateOrUpdateRegistrationAsync(registration);
    }
    catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
    {
        settings.Remove("__NHRegistrationId");
    }


## <a name="registration-management-from-a-backend"></a>Administration af registrering fra en back-end

Administrere registreringer fra backend-version, skal du skrive yderligere kode. Appen fra enheden skal angive de opdaterede PNS håndtere til backend-version, hver gang appen startes (sammen med mærker og skabeloner), og back-end skal opdatere denne handle på meddelelsen hubben. Billedet nedenfor illustrerer dette design.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Fordelene ved at administrere registreringer fra backend-version er muligheden for at redigere mærker til registreringer, selvom den tilsvarende app på enheden er inaktiv og til at godkende klienten app før du tilføjer et mærke til dens registrering.


#### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Eksempler på kode til at registrere med en meddelelse om hub fra en back-end ved hjælp af en installation

Klientenhed stadig får dens PNS fyldhåndtag samt relevante installationsegenskaber som før og kalder en brugerdefineret API på back-end, der kan udføre registreringen og Godkend mærker osv. Backend-version kan udnytte [Meddelelse Hub SDK til back end-handlinger](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Du kan også bruge metoden programrettelse ved hjælp af [JSON-programrettelse standard](https://tools.ietf.org/html/rfc6902) for at opdatere installationen.
 

    // Initialize the Notification Hub
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Eksempler på kode til at registrere med en meddelelse om hub fra en enhed med en registrering-id

Fra din app backend-version, kan du udføre grundlæggende CRUDS handlinger på registreringer. Eksempel:

    var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
    // create a registration description object of the correct type, e.g.
    var reg = new WindowsRegistrationDescription(channelUri, tags);

    // Create
    await hub.CreateRegistrationAsync(reg);

    // Get by id
    var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

    // update
    r.Tags.Add("myTag");

    // update on hub
    await hub.UpdateRegistrationAsync(r);

    // delete
    await hub.DeleteRegistrationAsync(r);


Back-end skal håndtere på dokumentsammenfald mellem registrering opdateringer. Service Bus indeholder optimistisk kontrolelementet til administration af registrering. Dette er implementeret med anvendelse af ETag på registrering management handlinger på niveauet HTTP. Denne funktion bruges transparent af Microsoft SDKs, som Udløs en undtagelse, hvis en opdatering afvises på dokumentsammenfald årsager. App back-end er ansvarlig for håndtering af disse undtagelser og prøver igen opdateringen, hvis det er nødvendigt.