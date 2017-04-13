<properties 
    pageTitle="App Service API app udløsere | Microsoft Azure" 
    description="Sådan implementeres udløsere i en API-App i Azure App Service" 
    services="logic-apps" 
    documentationCenter=".net" 
    authors="guangyang"
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="na" 
    ms.tgt_pltfrm="dotnet" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="rachelap"/>

# <a name="azure-app-service-api-app-triggers"></a>Azure App Service API app udløsere

>[AZURE.NOTE] Denne version i denne artikel gælder for API apps 2014-12-01-preview skemaversion.


## <a name="overview"></a>Oversigt

Denne artikel forklares, hvordan du kan implementere API app udløsere og bruge dem fra en logik app.

Alle kodestykker i dette emne er kopieret fra [FileWatcher API App kodeeksempel](http://go.microsoft.com/fwlink/?LinkId=534802). 

Note, skal du hente følgende nuget-pakke til koden i denne artikel for at oprette og køre: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## <a name="what-are-api-app-triggers"></a>Hvad er API app udløsere?

Det er et almindeligt scenarie for en API app forespørgselsreglen en begivenhed, så klienter af API-app kan tage de nødvendige skridt som svar på begivenheden. Den REST-API baseret metode, understøtter dette scenario kaldes en API app udløser. 

Lad os sige for eksempel din klientkode bruger [Twitter-forbindelse API app](../app-service-logic/app-service-logic-connector-twitter.md) og din kode skal udføre en handling, der er baseret på nye tweets, der indeholder bestemte ord. I dette tilfælde kan du konfigurere en afstemning eller push udløser at lette dette behov.

## <a name="poll-trigger-versus-push-trigger"></a>Afstemning udløser kontra push-udløser

To typer udløsere understøttes i øjeblikket:

- Afstemning udløser - klient undersøger API-app til meddelelse om en begivenhed, der har været udløses 
- Push-udløser - klienten besked fra API-app, når en hændelse udløses 

### <a name="poll-trigger"></a>Afstemning udløser

En afstemning udløser er implementeret som en almindelig REST-API og forventer dens klienter (som en logik app) til afstemning Hvis du vil have besked om. Mens klienten kan opretholde tilstand, er afstemning udløseren selve uden tilstand. 

Følgende oplysninger om og svar pakkerne illustrerer nogle vigtige aspekter af afstemning udløser kontrakten:

- Anmode om
    - HTTP-metode: få
    - Parametre
        - triggerState - denne valgfri parameter tillader klienter at angive deres tilstand, så afstemning udløser kan korrekt bestemme, om der skal returneres meddelelse eller ej baseret på den angivne tilstand.
        - API-specifikke parametre
- Svar
    - Status kode **200** - anmodning er gyldig, og der er en meddelelse fra udløseren. Indholdet af meddelelsen bliver svar brødteksten. Overskriften "Forsøg igen efter" i svaret angiver, at yderligere besked om data skal hentes med et efterfølgende anmodninger opkald.
    - Status kode **202** - anmodning er gyldig, men der er ingen besked om ny fra udløseren.
    - Status kode **4xx** - anmodning er ikke gyldige. Klienten skal ikke igen på anmodningen.
    - Status kode **5xx** - anmodning har udløste i en intern serverfejl og/eller midlertidigt problem. Klienten skal forsøg anmodningen igen.

Følgende kodestykke er et eksempel på, hvordan du kan implementere en afstemning udløser.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Hvis du vil teste denne afstemning udløser, skal du følge disse trin:

1. Installer API-App med en indstilling for godkendelse på **offentlige anonyme**.
2. Ring til handlingen **trykke** for at berøring en fil. Følgende billede viser et eksempel på en anmodning om via Postman.
   ![Ringe til Touch handlingen via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Du kan ringe afstemning udløser med parameteren **triggerState** indstillet til et tidsstempel før trin #2. Følgende billede viser eksempel anmodningen via Postman.
   ![Ringe til afstemning udløser via Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### <a name="push-trigger"></a>Push-udløser

En push-udløser er implementeret som en almindelig REST-API, der flytter meddelelser til klienter, der har registreret til at få besked, når bestemte hændelser brand.

Følgende oplysninger om og svar pakkerne illustrerer nogle vigtige aspekter af opslagsnål udløser kontrakt.

- Anmode om
    - HTTP-metode: PLACERE
    - Parametre
        - triggerId: kræves - uigennemsigtig streng (som en GUID), der repræsenterer registrering af en push-udløser.
        - callbackUrl: kræves - URL-adressen for tilbagekaldet kalde når hændelsen udløses. Aktiveringen er et simpelt INDLÆG HTTP-opkald.
        - API-specifikke parametre
- Svar
    - Status kode **200** - anmodning om at registrere klienten vellykket.
    - Status kode **4xx** - anmodning er ikke gyldige. Klienten skal ikke igen på anmodningen.
    - Status kode **5xx** - anmodning har udløste i en intern serverfejl og/eller midlertidigt problem. Klienten skal forsøg anmodningen igen.
- Tilbagekald
    - HTTP-metode: INDLÆG
    - Anmodningsteksten: indhold i meddelelsen.

Følgende kodestykke er et eksempel på, hvordan du kan implementere en push-udløser:

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Hvis du vil teste denne afstemning udløser, skal du følge disse trin:

1. Installer API-App med en indstilling for godkendelse på **offentlige anonyme**.
2. Gå til [http://requestb.in/](http://requestb.in/) til at oprette en RequestBin, der fungerer som din tilbagekald URL-adresse.
3. Ring til push-udløser med et GUID som **triggerId** og RequestBin URL-adressen som **callbackUrl**.
   ![Ringe til Push-udløser via Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Ring til handlingen **trykke** for at berøring en fil. Følgende billede viser et eksempel på en anmodning om via Postman.
   ![Ringe til Touch handlingen via Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Se RequestBin for at bekræfte, at push udløser tilbagekald er aktiveret med egenskaben output.
   ![Ringe til afstemning udløser via Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### <a name="describe-triggers-in-api-definition"></a>Beskriv udløsere i API definition

Gå til bladet **API Definition** i portalen Azure preview efter implementering udløserne og installation af din app API til Azure, og du får vist udløsere genkendes automatisk i Brugergrænsefladen, som er baseret på Swagger 2.0 API definitionen af API-app.

![API Definition Blade](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Hvis du klikker på knappen **Hent Swagger** og Åbn filen JSON, får du vist svarende til følgende resultater:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

Filtypenavn egenskaben **x-ms-schedular-udløser** er, hvordan udløsere er beskrevet i API definition, og tilføjes automatisk af gatewayen API app, når du anmoder om API definitionen via gatewayen, hvis anmodningen til en af følgende kriterier. (Du kan også tilføje denne egenskab manuelt).

- Afstemning udløser
    - Hvis metoden HTTP er **få**.
    - Hvis egenskaben **operationId** indeholder streng **udløser**.
    - Hvis egenskaben **parameters** indeholder en parameter med en **name** -egenskab, der er angivet til **triggerState**.
- Push-udløser
    - Hvis metoden HTTP er **PLACERE**.
    - Hvis egenskaben **operationId** indeholder streng **udløser**.
    - Hvis egenskaben **parameters** indeholder en parameter med en **name** -egenskab, der er angivet til **triggerId**.

## <a name="use-api-app-triggers-in-logic-apps"></a>Bruge API app udløsere i logik apps

### <a name="list-and-configure-api-app-triggers-in-the-logic-apps-designer"></a>Vise og konfigurere API app udløsere i logik apps designer

Hvis du opretter en logik app i samme ressourcegruppe som API-app, vil du kunne føje den til Forespørgselsdesigner lærredet ved blot at klikke på den. Følgende billeder viser, at dette:

![Udløsere i logik App Designer](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Konfigurere afstemning udløser i logik App Designer](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Konfigurere Push-udløser i logik App Designer](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## <a name="optimize-api-app-triggers-for-logic-apps"></a>Optimere API app udløsere til logik apps

Når du føjer udløsere til en API-app, er der et par ting, du kan gøre for at forbedre oplevelsen, når du bruger appen API i en logik app.

For eksempel parameteren **triggerState** for afstemning udløsere skal du vælge følgende udtryk i appen logik. Dette udtryk skal evaluere sidste aktiveringen af udløser fra logik app og returnere denne værdi.  

    @coalesce(triggers()?.outputs?.body?['triggerState'], '')

BEMÆRKNING: Indeholder en beskrivelse af de funktioner, der bruges i ovenstående udtryk, finder du i dokumentationen til [Logik App arbejdsproces Definition Language](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Logik app brugere har brug at give udtryk over for parameteren **triggerState** når du bruger udløseren. Det er muligt at have denne værdi forudindstillede af logik app designer gennem lokalnummer egenskaben **x-ms-scheduler-anbefaling**.  Egenskaben **x-ms-synlighed** lokalnummer kan angives til en værdi på *interne* , så parameteren selve ikke vises på designer.  Følgende kodestykke illustrerer, at der.

    "/api/Messages/poll": {
      "get": {
        "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

For push udløsere identificerer parameteren **triggerId** entydigt logik app. Anbefalede den bedste fremgangsmåde er at angive denne egenskab til navnet på arbejdsprocessen ved hjælp af følgende udtryk:

    @workflow().name

Ved hjælp af **x-ms-scheduler-anbefaling** og **x-ms-synlighed** lokalnummer egenskaber i dets API defineres, kan formidle API-app til app logik designer til at konfigurere automatisk dette udtryk for brugeren.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### <a name="add-extension-properties-in-api-defintion"></a>Tilføj lokalnummer egenskaber i API defintion

Metadataoplysninger om yderligere - såsom lokalnummer egenskaber **x-ms-scheduler-anbefaling** og **x-ms-synlighed** - kan tilføjes i API defintion på en af to måder: fast eller dynamisk.

Til statisk metadata, kan du direkte redigere filen */metadata/apiDefinition.swagger.json* i projektet og tilføje egenskaberne manuelt.

For API-apps ved hjælp af dynamiske metadata, kan du redigere filen SwaggerConfig.cs for at tilføje et filter til handlingen som kan føje disse filtyper.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Følgende er et eksempel på hvordan klassen kan implementeres for at lette scenariet dynamiske metadata.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 
