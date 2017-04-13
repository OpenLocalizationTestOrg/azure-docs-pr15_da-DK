<properties
    pageTitle="Opgradere fra Mobile-tjenester til Azure App Service - Node.js"
    description="Lær, hvordan du nemt at opgradere din Mobile Services-program til en App-tjenesten Mobile-App"
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Opgradere din eksisterende Node.js Azure Mobile-tjeneste til App Service

App Service Mobile er en ny måde at opbygge mobilprogrammer ved hjælp af Microsoft Azure. Du kan få flere [Hvad er Mobile-Apps?].

Dette emne beskrives, hvordan du opgraderer en eksisterende Node.js back end-program fra Azure Mobile-tjenester til en ny App Service Mobile-Apps. Mens du udfører denne opgradering, kan din eksisterende Mobile Services-program kan fortsætte med at fungere.  Hvis du vil opgradere et Node.js back end-program skal du henvise til [opgradering af .NET Mobile-tjenester](./app-service-mobile-net-upgrading-from-mobile-services.md).

Når en mobil back-end er opgraderet til Azure App Service, har adgang til alle App Service-funktioner og er faktureret efter [App Service priser], ikke Mobile tjenester priser.

## <a name="migrate-vs-upgrade"></a>Overføre kontra opgradering

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP] Det anbefales, du [udføre en overførsel](app-service-mobile-migrating-from-mobile-services.md) før du gennemgår en opgradering. Denne måde, som du kan flytte begge versioner af programmet til den samme App Service planlægge og afholde gratis.

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Forbedringer i Mobile-Apps Node.js server SDK

Opgradere til den nye [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) indeholder mange forbedringer, herunder:

- Baseret på [Express framework](http://expressjs.com/en/index.html), den nye Node SDK er nedtonet og udviklet til at holde med Node versioner, som de kommer. Du kan tilpasse programfunktioner med Express programmer.

- Betydeligt forbedring af ydeevnen sammenlignet med Mobile Services SDK.

- Du kan nu være vært for et websted sammen med din mobile back-end; på samme måde, det er nemt at føje Azure Mobile SDK til et eksisterende express.v4-program.

- Bygget til på tværs af platforme og lokale udvikling, kan i Mobile-Apps SDK udviklet og køre den lokalt på Windows, Linux og OSX platforme. Det er nu nemt at bruge almindelige Node udviklingsteknikker som kører [Mocha](https://mochajs.org/) test før installation.

## <a name="overview"></a>Grundlæggende opgradering oversigt

For at hjælpe i forbindelse med opgradering af en Node.js back-end, har Azure App Service angivet en kompatibilitet pakke.  Efter opgradering får du et niew websted, der kan installeres på et nyt websted for App-tjenesten.

Mobile-tjenester klienten SDK'er er **ikke** kompatibel med den nye Mobile-Apps server SDK. For at give løbende-tjenesten for din app, skal du ikke publicere ændringer til et websted, der aktuelt tjener publicerede klienter. I stedet skal du oprette en ny mobilapp, der fungerer som en dublet. Du kan placere dette program i den samme App serviceaftale vil undgå af foretage yderligere finansielle omkostninger.

Du kan derefter har to versioner af programmet:, forbliver den samme og fungerer udgivet apps i vilde, og den anden, kan du derefter opgradere og målliste med en ny version af klienten. Du kan flytte og teste din kode i dit tempo, men du skal kontrollere, at en hvilken som helst fejlrettelser, du foretager få anvendes på begge. Når du mener, at et ønskede antal klient apps vilde har opdateret til den nyeste version, kan du slette den oprindelige overførte app Hvis du ønsker. Den betale ikke eventuelle yderligere pengeværdier omkostninger, hvis den samme App Service plan som din Mobile-App som vært.

Fuld dispositionen for opgraderingsprocessen er som følger:

1. Hent dine eksisterende (overførte) Azure Mobile Service.
2. Konvertere projektet til en Azure Mobile-App ved hjælp af pakken kompatibilitet.
3. Ret eventuelle forskelle (såsom godkendelsesindstillinger).
4. Installere projektet konverterede Azure Mobile-App i en ny App-tjeneste.
4. Slip en ny version af klientprogrammet, der bruger den nye Mobile-App.
5. (Valgfrit) Slette din oprindelige overførte mobile service-app.

Sletningen kan opstå, når du ikke kan se al trafik på din oprindelige overførte mobile tjeneste.

## <a name="install-npm-package"></a>Installere på forudsætninger

Du skal installere [Node] på din lokale computer.  Du skal også installere pakken kompatibilitet.  Når Node er installeret, kan du køre følgende kommando fra en ny cmd eller PowerShell-prompt:

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a>Få dine Azure Mobile tjenester Scripts

- Log på [Azure-portalen].
- Bruger **Alle de ressourcer** eller **App Services**, finde webstedet Mobile-tjenester.
- Klik på **Funktioner**på webstedet -> **Kudu** -> **Gå** til at åbne Kudu websted.
- Klik på **Fejlfinding af konsol** -> **PowerShell** til at åbne konsollen fejlfinding.
- Gå til `site/wwwroot/App_Data/config` ved at klikke på hver mappe tur.
- Klik på ikonet Hent ud for den `scripts` directory.

Dette vil hente scriptene i ZIP-format.  Oprette en ny mappe på din lokale computer og Pak den `scripts.ZIP` fil i mappen.  Dette vil oprette en `scripts` directory.

## <a name="scaffold-app"></a>Scaffold nye Azure Mobile-Apps backend-version

Kør følgende kommando fra den mappe, der indeholder mappen scripts:

```scaffold-mobile-app scripts out```

Dette vil oprette en scaffolded back-end på en Azure Mobile-Apps i den `out` directory.  Selvom det ikke nødvendigt, det er en god ide at kontrollere den `out` mappe til en kilde kode lager efter eget valg.

## <a name="deploy-ama-app"></a>Installere din Azure Mobile-Apps backend-version

Under installationen skal du gøre følgende:

1. Oprette en ny Mobile-App i [Azure-portalen].
2. Køre den `createViews.sql` script på din forbundne database.
3. Sammenkæd den database, der er knyttet til din Mobile-tjeneste til din nye App-tjeneste.
4. Knytte andre ressourcer (såsom meddelelse Hubs) til den nye App-tjeneste.
5. Installer den genererede kode til dit nye websted.

### <a name="create-a-new-mobile-app"></a>Oprette en ny Mobile-App

1. Log på på [Azure-portalen].

2. Klik på **+ Ny** > **Web + Mobile** > **Mobile-App**, Angiv derefter et navn på din Mobile-App backend-version.

3. Vælg en eksisterende ressourcegruppe til **Ressourcegruppe**, eller Opret en ny (med det samme navn som din app). 
 
    Du kan vælge en anden App Service-plan eller oprette en ny. Få mere at vide om App Services planer og hvordan du opretter en ny plan i en anden priser klasse og se [Azure App Service planer dybden oversigt](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)på den ønskede placering.

4. **App-serviceaftale**til er standardplanen (i [Standard niveau](https://azure.microsoft.com/pricing/details/app-service/)) markeret. Du kan også vælge en anden plan, eller [Opret en ny](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Indstillinger for den App-serviceaftale bestemmer [placering, funktioner, omkostninger og beregne ressourcer, der](https://azure.microsoft.com/pricing/details/app-service/) er knyttet til din app. 

    Når du beslutter dig for planen, skal du klikke på **Opret**. Dette opretter Mobile-App backend-version. 


### <a name="run-createviewssql"></a>Køre CreateViews.SQL

Appen scaffolded indeholder en fil med navnet `createViews.sql`.  Dette script skal udføres på destinationsdatabasen.  Forbindelsesstrengen for destinationsdatabasen kan fås fra dine overførte mobile service fra bladet **Indstillinger** under **Strenge**.  Det hedder `MS_TableConnectionString`.

Du kan køre dette script fra SQL Server Management Studio eller Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Sammenkæde databasen med din App-tjeneste

Sammenkæd den eksisterende database til din App-tjenesten:

- Åbn din App-tjeneste i [Azure-portalen].
- Vælg **Indstillinger for alle** -> **dataforbindelser**.
- Klik på **+ Tilføj**.
- I rullemenuen skal du vælge **SQL-Database**
- Vælg din eksisterende database under **SQL-Database**, og derefter klikke på **Vælg**.
- Angiv brugernavn og din adgangskode til databasen under **forbindelsesstreng**, og derefter klikke på **OK**.
- Klik på **OK**i bladet **Tilføj dataforbindelser** .

Brugernavnet og adgangskoden, kan findes ved at få vist forbindelsesstrengen for destinationsdatabasen i din overførte Mobile-tjeneste.


### <a name="set-up-authentication"></a>Konfigurere godkendelse

Azure Mobile-Apps kan du konfigurere Azure Active Directory, Facebook, Google, Microsoft og Twitter godkendelse i tjenesten.  Brugerdefineret godkendelse skal udvikles separat.  Se i dokumentationen til [Godkendelse begreber] og [Godkendelse Hurtig start] dokumentation kan finde flere oplysninger.  

## <a name="updating-clients"></a>Opdatere mobilklienter

Når du har en funktionsdygtige Mobile-App backend-version, kan du arbejde på en ny version af klientprogrammet der forbruger den. Mobile-Apps indeholder også en ny version af klienten SDK'er, og svarer til at serveren er opgraderet ovenfor, skal du fjerne alle henvisninger til Mobile tjenester SDK'er før du installerer Mobile-Apps versioner.

En af de vigtigste ændringer mellem versionerne er, at konstruktører ikke længere kræver en programtasten. Du nu sende blot i URL-adressen på din Mobile-App. For eksempel på .NET-klienter, den `MobileServiceClient` parametre er nu:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Du kan læse om installation af de nye SDK'er og bruge den nye struktur via linkene nedenfor:

- [Android version 2,2 eller nyere](app-service-mobile-android-how-to-use-client-library.md)
- [iOS version 3.0.0 eller nyere](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) version 2.0.0 eller nyere](app-service-mobile-dotnet-how-to-use-client-library.md)
- [Apache Cordova version 2.0 eller nyere](app-service-mobile-cordova-how-to-use-client-library.md)

Hvis dit program gør brug af pushmeddelelser, skal du notere de bestemte registrering instruktioner til hver platform, som der er nogle ændringer der også.

Når du har den klar ny klientversion, prøv det mod projektet opgraderede server. Efter validering af, at den fungerer, kan du slipper en ny version af programmet til kunder. Til sidst, når dine kunder har haft mulighed for at modtage disse opdateringer, kan du slette Mobile-tjenester versionen af din app. Du har på dette tidspunkt helt opgraderet til en App-tjenesten Mobile-App, der bruger den seneste Mobile-Apps server SDK.

<!-- URLs. -->

[Azure-portalen]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Hvad er Mobile-Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service priser]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Godkendelse begreber]: ../app-service/app-service-authentication-overview.md
[Hurtig start godkendelse]: app-service-mobile-auth.md

[Azure-portalen]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
