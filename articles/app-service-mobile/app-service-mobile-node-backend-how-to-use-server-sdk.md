<properties
    pageTitle="Hvordan du arbejder med Node.js back end-serveren SDK til Mobile-Appen | Azure App Service"
    description="Lær, hvordan du arbejder med Node.js back end-serveren SDK til Azure App Service Mobile-Apps."
    services="app-service\mobile"
    documentationCenter=""
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="node"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Sådan bruger du Azure Mobile Apps Node.js SDK

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

I denne artikel indeholder detaljerede oplysninger og eksempler viser, hvordan du arbejder med en Node.js back-end i Azure App Service Mobile-Apps.

## <a name="Introduction"></a>Introduktion

Azure App Service Mobile-Apps giver mulighed for at føje en mobile-optimeret dataadgang Web API til et webprogram.  Azure App Service Mobile Apps SDK er beregnet til ASP.NET og Node.js webprogrammer.  SDK indeholder følgende handlinger:

- Tabel operationer (Læs, Indsæt, opdatere, slette) for dataadgang
- Brugerdefineret API handlinger

Begge handlinger giver til godkendelse på tværs af alle identitetsudbydere tillader Azure App Service, herunder sociale identitetsudbydere som Facebook, Twitter, Google og Microsoft samt Azure Active Directory for enterprise identitet.

Du kan finde eksempler for hver use case i [eksempler mappe på GitHub].

## <a name="supported-platforms"></a>Understøttede platforme

Azure Mobile Apps Node SDK understøtter den aktuelle udgave af LTS af Node og nyere.  Og du skriver er den nyeste version af LTS Node v4.5.0.  Andre versioner af Node fungerer muligvis, men understøttes ikke.

Azure Mobile Apps Node SDK understøtter to databasedrivere - node mssql driveren understøtter SQL Azure og lokale forekomster af SQL Server.  Sqlite3 driver understøtter SQLite databaser på en enkelt forekomst.

### <a name="howto-cmdline-basicapp"></a>Sådan: oprette en grundlæggende Node.js back-end, ved hjælp af kommandolinjen

Hver Azure App Service Mobile App Node.js back-end starter som et ExpressJS program.  ExpressJS er den mest populære web service struktur tilgængelig for Node.js.  Du kan oprette en grundlæggende [Express] program på følgende måde:

1. Opret en mappe for dit projekt i en kommando eller PowerShell-vinduet.

        mkdir basicapp

2. Køre npm initialisering for at initialiseret pakke struktur.

        cd basicapp
        npm init

    Kommandoen npm initialisering beder et sæt spørgsmål initialiseret projektet.  Se eksempel output:

    ![Npm initialisering output][0]

3. Du kan installere de biblioteker, der udtrykkelig og azure-mobile-apps fra npm-lager.

        npm install --save express azure-mobile-apps

4. Oprette en app.js fil for at implementere grundlæggende mobile serveren.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Dette program opretter en mobile-optimeret WebAPI med et enkelt slutpunkt (`/tables/TodoItem`), der indeholder ikke-godkendte adgang til et underliggende SQL datalager ved hjælp af en dynamisk skema.  Det er egnet til følge klient bibliotek Hurtig start:

- [Android-klienten Hurtig start]
- [Apache Cordova klient Hurtig start]
- [iOS-klienten Hurtig start]
- [Hurtig start Windows Store-klient]
- [Xamarin.iOS klient Hurtig start]
- [Xamarin.Android klient Hurtig start]
- [Xamarin.Forms klient Hurtig start]

Du kan finde koden for denne grundlæggende program i [basicapp eksempel på GitHub].

### <a name="howto-vs2015-basicapp"></a>Sådan: oprette en Node back-end med Visual Studio 2015

Visual Studio 2015 kræver en udvidelse til udvikling af Node.js programmer i IDE.  For at starte skal du installere [Node.js Tools 1.1 til Visual Studio].  Når Node.js værktøjer til Visual Studio er installeret, skal du oprette et Express 4.x program:

1. Åbne dialogboksen **Nyt projekt** (fra **fil** > **Ny** > **Project...**).

2. Udvid **skabeloner** > **JavaScript** > **Node.js**.

3. Vælg det **grundlæggende Azure Node.js Express 4-program**.

4. Skriv projektnavnet.  Klik på *OK*.

    ![Visual Studio 2015 nyt projekt][1]

5. Højreklik på noden **npm** , og vælg **Installer nye npm pakker …**.

6. Du skal muligvis opdatere kataloget npm på at oprette dit første Node.js-program.  Klik på **Opdater** , hvis det er nødvendigt.

7. Angiv _azure-mobile-apps_ i søgefeltet.  Klik på **azure-mobile-apps 2.0.0** -pakken, og klik derefter på **Installere pakke**.

    ![Installere nye npm-pakker][2]

8. Klik på **Luk**.

9. Åbn filen _app.js_ for at tilføje understøttelse af Azure Mobile Apps SDK.  På linjen 6 at gå til bunden af biblioteket kræver erklæringer, skal du tilføje følgende kode:

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Tilføj følgende kode hos cirka streg 27 efter de øvrige app.use oplysninger:

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Gem filen.

10. Enten at køre programmet lokalt (API vises på http://localhost:3000) eller publicere til Azure.

### <a name="create-node-backend-portal"></a>Sådan: oprette en Node.js back-end, ved hjælp af portalen Azure

Du kan oprette en Mobile-App back end-direkte i [Azure-portalen]. Du kan enten skal du følge nedenstående trin, eller du kan oprette en klient og serveren sammen ved at følge selvstudiet [oprette en mobilapp](app-service-mobile-ios-get-started.md) . Selvstudiet indeholder en forenklet version af disse instruktioner og fungerer bedst til bevis for konceptet projekter.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Vælg **Node.js** tilbage i bladet _komme i gang_ under **Opret en tabel, API**, som din **back end-sprog**. Markér afkrydsningsfeltet ud for "**jeg bekræfte, at dette overskriver alle websted indholdet.**", og derefter klikke på **Opret TodoItem tabel**.

### <a name="download-quickstart"></a>Sådan: hente Node.js back end-Hurtig start Kodeprojektet ved hjælp af ciffer

Når du opretter en Node.js Mobile-App back-end ved hjælp af portalen **Hurtig start** blade, er en Node.js project oprettes til dig og installeres på dit websted. Du kan tilføje tabeller og API'er og redigere kodefiler til Node.js backend-version i portalen. Du kan også bruge forskellige installationsværktøjer til at hente back end-projektet, så du kan tilføje eller ændre tabeller og API'er og derefter genudgive projektet. Se [Installationsvejledning til Azure App Service]kan finde flere oplysninger. følgende fremgangsmåde bruger et ciffer lager til at hente Hurtig start project koden.

1. Hvis du ikke allerede har gjort det, skal du installere ciffer. De trin, der kræves for at installere ciffer varierer mellem operativsystemer. Se [Installere ciffer](http://git-scm.com/book/en/Getting-Started-Installing-Git) til operativsystem-specifikke salgsdistributioner og installation vejledning.

2. Følg trinnene i [aktivere App Service app lager](../app-service-web/app-service-deploy-local-git.md#Step3) til at aktivere ciffer lager for webstedet back end-, foretage en note for installation brugernavn og din adgangskode.

3. I bladet for din Mobile-App backend-version, skal du notere af indstillingen **ciffer Klon URL-adresse** .

4. Udføre den `git clone` kommandoen ved hjælp af ciffer klone URL-adresse, at angive din adgangskode, når det er påkrævet, som i følgende eksempel:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Gå til lokal mappe, som i det foregående eksempel er /todolist, og Bemærk, at projektfiler har hentet. Find den `todoitem.json` filer i den `/tables` directory.  Denne fil definerer tilladelser på tabellen.  Også finde den `todoitem.js` fil i den samme mappe, der definerer CRUD-handlingen scripts til tabellen.

6. Når du har lavet ændringer i project-filer, kan du udføre følgende kommandoer for at tilføje, bekræfte og derefter overføre ændringerne til webstedet:

        $ git commit -m "updated the table script"
        $ git push origin master

    Når du tilføjer nye filer til projektet, skal du først at udføre de `git add .` kommandoen.

Webstedet udgives, hver gang en ny række anvendelser rykkes til webstedet.

### <a name="howto-publish-to-azure"></a>Sådan: publicere din Node.js backend-version til Azure

Microsoft Azure indeholder mange funktioner til at publicere din Azure App Service Mobile Apps Node.js backend-version til Azure tjenesten.  Dette omfatter udnytter installationsværktøjer, der er integreret i Visual Studio, kommandolinjen værktøjer og fortløbende installationsindstillinger baseret på versionsstyring.  Du kan finde flere oplysninger om dette emne, [Installationsvejledning til Azure App tjeneste].

Azure App Service har bestemt råd til Node.js program, du skal gennemgå før du anvender:

- Sådan [angives den Node Version]
- Sådan [bruger du Node moduler]

### <a name="howto-enable-homepage"></a>Sådan: aktivere en startside for dit program

Mange programmer er en kombination af internettet og mobilapps, og ExpressJS, som gør det muligt at kombinere to facetter.  Nogle gange kan dog kan du kun implementere en mobile grænseflade.  Det er nyttigt at give en landingssiden for at sikre, at tjenesten app er oppe at køre.  Du kan angive dine egne startsiden eller aktivere en midlertidig startside.  For at aktivere en midlertidig startside skal du bruge følgende til at oprette Azure Mobile-Apps:

    var mobile = azureMobileApps({ homePage: true });

Hvis du kun vil denne indstilling er tilgængelig, når udvikler lokalt, kan du tilføje denne indstilling til din `azureMobile.js` fil.

## <a name="TableOperations"></a>Tabel handlinger 

Azure-mobile-apps Node.js Server SDK indeholder funktioner til at få vist datatabeller, der er gemt i Azure SQL-Database som en WebAPI.  Der findes fem handlinger.

| Handling | Beskrivelse |
| --------- | ----------- |
| FÅ /tables/_tabelnavn_ | Få alle poster i tabellen |
| FÅ /tables/_tabelnavn_/:id | Få en bestemt post i tabellen |
| INDLÆG /tables/_tabelnavn_ | Oprette en post i tabellen |
| PROGRAMRETTELSE /tables/_tabelnavn_/:id | Opdatere en post i tabellen |
| SLETTE /tables/_tabelnavn_/:id | Slette en post i tabellen |

Denne WebAPI understøtter [OData] og udvider tabellen skemaet for at understøtte [offline data synkronisering].

### <a name="howto-dynamicschema"></a>Sådan: definere tabeller ved hjælp af et dynamisk skema

Før en tabel kan bruges, skal den være defineret.  Tabeller kan defineres med et statisk skema (hvor udvikleren definerer kolonnerne i skemaet) eller dynamisk (hvor SDK styrer skemaet baseret på indgående anmodninger). Desuden kan udvikleren styre bestemte aspekter af WebAPI ved at føje Javascript-kode til definitionen.

Som en bedste fremgangsmåde, skal du definere hver tabel i en Javascript-fil i mappen tabeller og derefter bruge metoden tables.import() til at importere tabellerne.  Hvis du vil udvide basic-app, skal filen app.js justeres:

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Definere tabellen. / tables/TodoItem.js:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Tabeller bruge dynamiske skema som standard.  For at deaktivere dynamiske skema globalt skal du angive App indstilling **MS_DynamicSchema** til falsk i Azure portal.

Du kan finde en komplet eksempel i [Opgaveliste eksempel på GitHub].

### <a name="howto-staticschema"></a>Sådan: definere tabeller ved hjælp af et statisk skema

Du kan definere kolonnerne til at fremvise via WebAPI eksplicit.  Azure-mobile-apps Node.js SDK lægger automatisk alle flere kolonner, der kræves til synkronisering af offline data til den liste, du angiver.  For eksempel klientprogrammerne Hurtig start kræver en tabel med to kolonner: tekst (streng), og Fuldfør (en boolesk).  
Tabellen kan defineres i den tabel JavaScript definitionsfil (findes i mappen tabeller) på følgende måde:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Hvis du definerer tabeller statisk, skal du også kalde metoden tables.initialize() for at oprette databaseskemaet ved start.  Metoden tables.initialize() returnerer en [løftet] , så webtjenesten ikke betjene anmodninger før den database, der initialiseret.

### <a name="howto-sqlexpress-setup"></a>Sådan: Brug SQL Express som et udvikling datalager på din lokale computer

I Azure Mobile-Apps i AzureMobile Apps Node SDK har du tre muligheder for fungerer data af feltet: SDK har du tre muligheder for fungerer data af feltet:

- Bruge **hukommelse** driver til at give et lager til ikke-fast eksempel
- Bruge **mssql** driver til at give et SQL Express datalager til udvikling
- Bruge **mssql** driver til at give et datalager Azure SQL-Database til fremstilling

Azure Mobile Apps Node.js SDK bruger [mssql Node.js pakke] til at oprette og bruge en forbindelse til SQL Express og SQL-Database.  Denne pakke kræver, at du aktiverer TCP-forbindelser på din SQL Express forekomst.

> [AZURE.TIP]Hukommelse driveren indeholder ikke et komplet sæt af facilitet til test.  Hvis du vil teste din back-end lokalt, anbefaler vi brug af et SQL Express datalager og mssql driveren.

1. Hent og Installer [Microsoft SQL Server 2014 Express].  Kontrollér, at du installerer SQL Server 2014 Express med værktøjer edition.  Medmindre du eksplicit kræver 64-bit-understøttelse, bruger 32-bit-versionen mindre hukommelse, når du kører.

2. Køre Konfigurationsstyring til SQL Server 2014.

  1. Udvid noden **SQL Server-konfiguration** i den venstre træmenuen.
  2. Klik på **protokoller for SQLEXPRESS**.
  3. Højreklikke på **TCP/IP** og vælge **Aktivér**.  Klik på **OK** i dialogboksen pop op.
  4. Højreklikke på **TCP/IP** , og vælg **Egenskaber**.
  5. Klik på fanen **IP-adresser** .
  6. Find noden **IPAll** .  Angiv **1433**i feltet **TCP-Port** .

         ![Configure SQL Express for TCP/IP][3]

  7. Klik på **OK**.  Klik på **OK** i dialogboksen pop op.
  8. Klik på **SQL servertjenester** i menuen venstre trævisningen.
  9. Højreklik på **SQL Server (SQLEXPRESS)** og vælg **Genstart**
  10. Luk SQL Server 2014 Configuration Manager.

3. Kør SQL Server 2014 Management Studio og oprette forbindelse til din lokale forekomst af SQL Express

  1. Højreklik på din forekomst i Object Explorer, og vælg **Egenskaber**
  2. Vælg siden **sikkerhed** .
  3. Sørg for **SQL Server og Windows-godkendelsestilstand** er valgt
  4. Klik på **OK**

        ![Konfigurere hurtig SQL-godkendelse][4]

  5. Udvid **sikkerhed** > **logon** i Explorer objekt
  6. Højreklik på **logon** , og vælg **Nyt logon...**
  7. Skriv et brugernavn.  Vælg **SQL Server-godkendelse**.  Angiv en adgangskode, og derefter angive den samme adgangskode i feltet **Bekræft adgangskode**.  Adgangskoden skal opfylde Windows kompleksitetskrav.
  8. Klik på **OK**

        ![Tilføje en ny bruger til SQL Express][5]

  9. Højreklik på dit nye logon, og vælg **Egenskaber**
  10. Vælg siden **Serverroller**
  11. Markér afkrydsningsfeltet ud for rollen **dbcreator** server
  12. Klik på **OK**
  13. Luk SQL Server 2015 Management Studio

Kontrollér, at du registrerer det brugernavn og adgangskode, du valgte.  Du kan være nødvendigt at tildele tilladelserne afhængigt af dine krav til bestemte database eller yderligere servertyper roller.

Programmet Node.js læser miljøvariablen **SQLCONNSTR_MS_TableConnectionString** for forbindelsesstrengen for denne database.  Du kan angive denne variabel i dit miljø.  For eksempel kan du bruge PowerShell til at indstille denne miljøvariablen:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Få adgang til databasen via en TCP/IP-forbindelse og angive et brugernavn og adgangskode for forbindelsen.

### <a name="howto-config-localdev"></a>Sådan: konfigurere dit projekt til lokale udvikling

Azure Mobile-Apps læser en JavaScript-fil, der hedder _azureMobile.js_ fra det lokale filsystem.  Brug ikke denne fil til at konfigurere Azure Mobile Apps SDK i fremstilling - du kan bruge indstillinger i [Azure portal] i stedet.  Filen _azureMobile.js_ skal eksportere en konfigurationsobjekt.  De mest almindelige indstillinger er:

- Databaseindstillinger
- Indstillinger for logføring af diagnostik
- Alternative CORS indstillinger

_AzureMobile.js_ eksempelfil implementering af de foregående databaseindstillinger følger:

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Vi anbefaler, at du tilføjer _azureMobile.js_ til filen _.gitignore_ (eller andre versionsstyring Ignorer fil) for at forhindre adgangskoder i at blive gemt i skyen.  Konfigurere altid fremstilling indstillinger i App-indstillinger i [Azure-portalen].

### <a name="howto-appsettings"></a>Hvordan: Konfigurere App indstillinger for din-Mobilappen

De fleste indstillinger i filen _azureMobile.js_ har en tilsvarende App indstilling i [Azure-portalen].  Brug listen nedenfor til at konfigurere din app i App-indstillinger:

| App-indstilling                 | _azureMobile.js_ indstilling  | Beskrivelse                               | Gyldige værdier                                |
| :-------------------------- | :------------------------ | :---------------------------------------- | :------------------------------------------ |
| **MS_MobileAppName**        | Navn                      | Navnet på app'en                       | streng                                      |
| **MS_MobileLoggingLevel**   | Logging.level             | Mindste logføringsniveau af meddelelser for at logge      | fejl, advarsel, oplysninger, detaljeret, fejlfinding, fjollet |
| **MS_DebugMode**            | fejlfinding af                     | Aktivér eller Deaktiver fejlsikret tilstand              | SAND, FALSK                                 |
| **MS_TableSchema**          | data.Schema               | Skema standardnavnet for SQL-tabeller        | streng (standard: dbo)                       |
| **MS_DynamicSchema**        | data.dynamicSchema        | Aktivér eller Deaktiver fejlsikret tilstand              | SAND, FALSK                                 |
| **MS_DisableVersionHeader** | version (angivet til ikke defineret)| Deaktiverer overskriften X-ZUMO-Server-Version | SAND, FALSK                                 |
| **MS_SkipVersionCheck**     | skipversioncheck          | Deaktiverer kontrollen klient API version     | SAND, FALSK                                 |

Sådan angives en App indstilling:

1. Log på [Azure-portalen].
2. Vælg **alle de ressourcer** eller **App tjenester** , og klik derefter på navnet på din Mobile-App.
3. Bladet indstillinger åbnes som standard. Hvis det ikke, klik på **Indstillinger**.
4. Klik på **Indstillinger** i menuen Generelt.
5. Rul ned til sektionen Indstillinger.
6. Hvis din app, der angiver, allerede findes, skal du klikke på værdien af indstillingen app til at redigere værdien.
7. Hvis indstillingen app ikke findes, kan du angive indstillingen App i feltet nøgle og værdien i feltet værdi.
8. Når du er færdig, skal du klikke på **Gem**.

Ændre indstillinger for de fleste kræver en tjeneste genstart.

### <a name="howto-use-sqlazure"></a>Sådan: Brug SQL-Database som dit fremstilling datalager

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Brug af Azure SQL-Database som et datalager er identiske på tværs af alle typer af Azure App Service-program. Hvis du allerede ikke har gjort det, skal du følge disse trin for at oprette en Mobile-App back-end.

1. Log på [Azure-portalen].

2. I øverste venstre side af vinduet, skal du klikke på knappen **+ Ny** > **Web + Mobile** > **Mobile-App**, Angiv derefter et navn på din Mobile-App backend-version.

3. I feltet **Ressourcegruppe** skal du angive det samme navn som din app.

4. Standard App serviceaftale er markeret.  Hvis du vil ændre din App Service-plan, kan du gøre det ved at klikke på App-serviceaftale > **+ Opret et nyt**.  Angiv et navn for den nye App Service-plan, og vælg en passende placering.  Klik på priser niveauet, og vælg en passende priser niveau for tjenesten. Vælg **Vis alle** for at få vist flere indstillinger, priser, såsom **gratis** og **delt**.  Når du har valgt det priser niveau, skal du klikke på knappen **Vælg** .  Klik på **OK**igen i bladet **App-serviceaftale** .

5. Klik på **Opret**. Klargøring af en Mobile-App back-end kan tage et par minutter.  Når Mobile-App backend-version er klargjort, åbnes bladet **Indstillinger** til Mobile-App back end-portalen.

Når Mobile-App backend-version er oprettet, kan du vælge enten at knytte en eksisterende SQL-database til din Mobile-App backend-version eller oprette en ny SQL-database.  I dette afsnit skal oprette vi en SQL-database.

> [AZURE.NOTE]Hvis du allerede har en database på samme placering, som mobilapp backend-version, kan du i stedet vælge **Brug en eksisterende database** og derefter vælge databasen. Brug af en database på en anden placering anbefales ikke på grund af højere latenstider.

6. Klik på **Indstillinger**i den nye Mobile-App backend-version, > **Mobile-App** > **Data** > **+ Tilføj**.

7. Klik på **SQL-Database - konfigurere nødvendige indstillinger**i bladet **Tilføj dataforbindelse**  > **Opret en ny database**.  Skriv navnet på den nye database i feltet **navn** .

8. Klik på **Server**.  Angiv et entydigt servernavn i feltet **servernavn** i bladet **nye server** , og angive en passende **Server administrator logon** og en **adgangskode**.  Sikre, at **Tillad azure services til at få adgang til server** er markeret.  Klik på **OK**.

    ![Oprette en Azure SQL-Database][6]

9. Klik på **OK**på bladet **Ny database** .

10. Vælg **forbindelsesstreng**tilbage på bladet **Tilføj dataforbindelse** , indtast logon og adgangskode, som du angav, når du opretter databasen.  Hvis du bruger en eksisterende database, kan du give logonoplysninger for den pågældende database.  Når du har angivet, skal du klikke på **OK**.

11. Igen på bladet **Tilføj dataforbindelse** igen skal du klikke på **OK** for at oprette databasen.

<!--- END OF ALTERNATE INCLUDE -->

Oprettelse af databasen kan tage et par minutter.  Brug **meddelelsesområdet** til at overvåge statussen for installationen.  Gennemgå ikke indtil databasen er blevet installeret korrekt.  Når installeret, oprettes en forbindelsesstreng for forekomsten SQL-Database i din Mobile back end-App-indstillinger.  Du kan se denne app indstilling på listen **Indstillinger** > **Programindstillinger** > **forbindelsesstrenge**.

### <a name="howto-tables-auth"></a>Sådan: kræver godkendelse for at få adgang til tabeller

Hvis du ønsker at bruge App Service godkendelse med slutpunktet, tabeller, skal du konfigurere App Service godkendelse i [Azure portal] først.  Gennemgå guiden Konfiguration for den identitetsudbyder, du vil bruge kan finde flere oplysninger om konfiguration af godkendelse i en Azure App Service:

- [Sådan konfigureres Azure Active Directory-godkendelse]
- [Sådan konfigureres Facebook-godkendelse]
- [Sådan konfigureres Google-godkendelse]
- [Sådan konfigureres Microsoft Authentication]
- [Sådan konfigureres Twitter-godkendelse]

Hver tabel har en access-egenskab, der kan bruges til at styre adgangen til tabellen.  I følgende eksempel viser en statisk definerede tabel med godkendelse kræves.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Egenskaben access kan have en af tre værdier

  - *anonym* angiver, at klientprogrammet har tilladelse til at læse data uden godkendelse
  - *godkendt* , betyder det, at klientprogrammet skal sendes en gyldig godkendelse token med anmodningen
  - *deaktiveret* angiver, at denne tabel er deaktiveret

Hvis egenskaben access er defineret, adgang ikke-godkendte.

### <a name="howto-tables-getidentity"></a>Sådan: bruge godkendelse af krav med dine tabeller

Du kan konfigurere forskellige krav, der skal bruges, når godkendelse er konfigureret.  Disse krav er ikke normalt tilgængelige via den `context.user` objekt.  Men de kan hentes ved hjælp af den `context.user.getIdentity()` metode.  Den `getIdentity()` metode returnerer løftet en, der oversættes til et objekt.  Objektet er indtastes ved godkendelsesmetoden (facebook, google, twitter, microsoftaccount eller aad).

Hvis du har konfigureret Microsoft Account godkendelses- og anmodning om gøre krav på e-mail-adresser, kan du tilføje mailadressen til posten med den følgende tabel controller:

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Hvis du vil se, hvilke krav er tilgængelige, skal du bruge en webbrowser til at få vist den `/.auth/me` slutpunkt for webstedet.

### <a name="howto-tables-disabled"></a>Sådan: deaktivere adgang til bestemte tabel handlinger

Ud over vises på tabellen, skal kan egenskaben access bruges til at styre individuelle operationer.  Der er fire handlinger:

  - *Læs* er handlingen RESTful få på tabellen
  - *indsætte* er handlingen RESTful INDLÆG på tabellen
  - *opdatere* er handlingen RESTful programrettelse på tabellen
  - *slette* er handlingen RESTful DELETE i tabellen

For eksempel kan du angive en skrivebeskyttet ikke-godkendte tabel:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Sådan: justere forespørgslen, der bruges med handlinger i tabel

En almindelig krav til tabel handlinger er at tilvejebringe et begrænset visning af dataene.  For eksempel kan du angive en tabel, der er mærket med den godkendte bruger-ID, så du kan kun læse eller opdatere dine egne poster.  Den følgende tabeldefinition indeholder denne funktion:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Handlinger, der normalt udføre en forespørgsel har en forespørgsel egenskab, du kan justere med en where delsætningen. Forespørgselsegenskaben er et [QueryJS] objekt, der bruges til at konvertere en OData-forespørgsel til noget, som kan behandle data back-end.  Et kort kan bruges til enkel lige tilfælde (som det foregående). Du kan også tilføje bestemte SQL-delsætninger:

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Sådan: konfigurere bløde Slet på en tabel

Bløde Slet slette ikke faktisk poster.  I stedet for markerer det dem som slettet i databasen ved at angive den slettede kolonne til sand.  Azure Mobile Apps SDK fjernes automatisk bløde slettet poster fra resultater, medmindre i Mobile Client SDK bruger IncludeDeleted().  For at konfigurere en tabel til bløde Slet skal du angive den `softDelete` egenskab i definitionsfil tabel:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Du skal oprette en funktion til sletning af poster – enten fra et program via en WebJob, Azure funktionen eller en brugerdefineret API.

### <a name="howto-tables-seeding"></a>Sådan: podes din database med data

Når du opretter et nyt program, kan du podes en tabel med data.  Dette kan gøres i tabel definition JavaScript-filen på følgende måde:

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Forhåndsudfyldning af data sker kun, når tabellen er oprettet af Azure Mobile Apps SDK.  Hvis tabellen allerede findes i databasen, tilføjet ingen data i tabellen.  Hvis dynamisk skema er slået til, udledes skemaet fra seeded data.

Vi anbefaler, at du eksplicit ringe på `tables.initialize()` metode til at oprette en tabel, når tjenesten begynder at køre.

### <a name="Swagger"></a>Sådan: aktivere understøttelse af Swagger

Azure App Service Mobile-Apps leveres med indbygget [Swagger] support.  For at aktivere understøttelse af Swagger skal du først installere swagger-brugergrænsefladen som en afhængighed:

    npm install --save swagger-ui

Når installeret, kan du aktivere understøttelse af Swagger i Azure Mobile-Apps parametre:

    var mobile = azureMobileApps({ swagger: true });

Du sandsynligvis kun vil aktivere understøttelse af Swagger i udvikling udgaver.  Du kan gøre dette ved at benytte de `NODE_ENV` app indstilling:

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Swagger slutpunkt er placeret i http://_yoursite_.azurewebsites.net/swagger.  Du kan få adgang til Brugergrænsefladen Swagger via den `/swagger/ui` slutpunkt.  Hvis du vælger at kræve godkendelse på tværs af hele programmet, giver Swagger en fejl.  Du opnår de bedste resultater, du vælger at tillade ikke-godkendte anmodninger gennem i Azure App Service godkendelse / godkendelse indstillinger, derefter styre godkendelse ved hjælp af den `table.access` egenskab.

Du kan også tilføje indstillingen Swagger til din `azureMobile.js` filen, hvis du kun vil Swagger support, når udvikler lokalt.

## <a name="a-namepushpush-notifications"></a><a name="push">Push-beskeder

Mobile-Apps integrerer med Azure meddelelse Hubs til giver dig mulighed at sende målrettede pushmeddelelser til millioner af enheder på tværs af alle store platforme. Ved hjælp af besked om Hubs, kan du sende pushmeddelelser til iOS, Android- og Windows-enheder. Mere at vide om alt, hvad du kan gøre med besked om Hubs, skal du se [Oversigt over Hubs meddelelse](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Sådan: Send pushmeddelelser

Følgende kode viser, hvordan du kan bruge objektet opslagsnål til at sende en meddelelse om broadcast opslagsnål til registrerede iOS-enheder:

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Ved at oprette en skabelon opslagsnål registrering fra klienten på computeren, kan du i stedet sende meddelelsen skabelon opslagsnål til enheder på alle understøttede platforme. Følgende kode viser, hvordan du kan sende en meddelelse om skabelon:

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


###<a name="push-user"></a>Sådan: Send pushmeddelelser til en godkendt bruger, der bruger mærker

Når en godkendt bruger tilmelder sig pushmeddelelser, føjes et bruger-ID mærke automatisk til registrering. Ved hjælp af dette mærke, kan du sende pushmeddelelser til alle enheder, der er registreret af en bestemt bruger. Følgende kode bliver SID for brugeren, der indsætter anmodningen og sender en skabelon push-besked til enhver enhed registrering for den pågældende bruger:

    // Only do the push if configured
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Når registrering for pushbeskeder fra en godkendt klient, skal du kontrollere, at godkendelse er fuldført, før du forsøger registrering.

## <a name="CustomAPI"></a>Brugerdefineret API'er

###  <a name="howto-customapi-basic"></a>Sådan: definere en brugerdefineret API


Ud over dataadgang API via /tables slutpunkt, kan Azure Mobile-Apps angive brugerdefinerede API dækning.  Brugerdefineret API'er er defineret i en lignende måde at tabeldefinitioner og har adgang til alle de samme faciliteter, herunder godkendelse.

Hvis du ønsker at bruge App Service godkendelse med en brugerdefineret API, skal du konfigurere App Service godkendelse i [Azure portal] først.  Gennemgå guiden Konfiguration for den identitetsudbyder, du vil bruge kan finde flere oplysninger om konfiguration af godkendelse i en Azure App Service:

- [Sådan konfigureres Azure Active Directory-godkendelse]
- [Sådan konfigureres Facebook-godkendelse]
- [Sådan konfigureres Google-godkendelse]
- [Sådan konfigureres Microsoft Authentication]
- [Sådan konfigureres Twitter-godkendelse]

Brugerdefineret API'er er defineret på samme måde som API'EN tabeller.

1. Oprette en **api** -mappe
2. Oprette en API definition JavaScript-fil i mappen **api** .
3. Bruge importmetoden til at importere mappen **api** .

Her er baseret på den basic-app eksempel brugte vi tidligere prototyper api definitionen.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Lad os tage et eksempel API, der returnerer datoen server ved hjælp af metoden _Date.now()_ .  Her er api/date.js fil:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Hver parameter er et af de almindelige RESTful verber - Hent, INDLÆG, programrettelse eller Slet.  Metoden er et standard [ExpressJS program] -funktion, der sender påkrævet output.

### <a name="howto-customapi-auth"></a>Sådan: kræver godkendelse for at få adgang til en brugerdefineret API

Azure Mobile Apps SDK implementerer godkendelse på samme måde for både tabeller slutpunkt og brugerdefinerede API'er.  Hvis du vil tilføje godkendelse til API udviklet i forrige afsnit, du Tilføj en **access** -egenskab:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Du kan også angive godkendelse på bestemte handlinger:

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Det samme token, der bruges til tabeller slutpunktet skal bruges til brugerdefineret API'er, der kræver godkendelse.

### <a name="howto-customapi-auth"></a>Sådan: håndtere store filoverførsler

Azure Mobile Apps SDK bruger [brødtekst-parseren programmer](https://github.com/expressjs/body-parser) til at acceptere og afkode brødtekst indhold i dit bidrag.  Udfyldt kan du konfigurere brødteksten-parseren for at acceptere større fil overfører:

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Filen er base-64-kodet før overførslen.  Størrelsen af den faktiske Overfør øges (og dermed størrelsen du skal tage højde).

### <a name="howto-customapi-sql"></a>Sådan: udføre brugerdefinerede SQL-sætninger

Azure Mobile Apps SDK giver adgang til konteksten hele gennem request-objektet, så du kan udføre parameteriseret SQL-sætninger til definerede dataprovideren nemt:

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Fejlfinding, nemt tabeller og nemt API'er

### <a name="howto-diagnostic-logs"></a>Sådan: fejlfinding, diagnosticere og foretage fejlfinding af Azure Mobile-apps

Tjenesten Azure App giver flere fejlfinding og fejlfinding teknikker til Node.js programmer.
Se følgende artikler for at komme i gang med at foretage fejlfinding af din Node.js Mobile backend-version:

- [Overvågning af en Azure App-tjeneste]
- [Aktivere logføring af diagnostik i Azure App-tjeneste]
- [Fejlfinding i forbindelse med en Azure App-tjenesten i Visual Studio]

Node.js programmer har adgang til en bred vifte af diagnosticeringsloggene værktøjer.  Azure Mobile Apps Node.js SDK bruger internt, [Winston] til logføring af diagnostik.  Logføring er automatisk aktiveret, ved at aktivere fejlsikret tilstand eller ved at angive indstillingen **MS_DebugMode** app til sand på [Azure-portalen]. Oprettede logfiler vises i diagnosticeringslogfiler på [Azure-portalen].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Sådan: arbejde med nemt tabeller i portalen Azure

Nemt tabeller i portalen kan du oprette og arbejde med tabeller højre på portalen. Du kan endda redigere tabel handlinger ved hjælp af App Service Editor.

Når du klikker på **nemt tabeller** i indstillingerne for dit back end-websted, kan du tilføje, redigere eller slette en tabel. Du kan også få vist dataene i tabellen.

![Arbejde med nemt tabeller](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Følgende kommandoer er tilgængelige på kommandolinjen for en tabel:

+ **Ændring af tilladelser** - ændre tilladelsen for læsning, indsætte, opdatere og slette handlinger på tabellen. 
  Indstillinger er at tillade anonym adgang, så der kræves godkendelse eller for at deaktivere alle adgang til handlingen. 
+ **Redigere script** - scriptfil for tabellen åbnes i App Service Editor.
+ **Administrer skema** - tilføje eller slette kolonner eller ændre indekset tabel.
+ **Ryd tabel** - afkorter en eksisterende tabel er at slette alle datarækker, men at forlade skemaet uændret.
+ **Slette rækker** - Slet separate rækker af data.
+ **Få vist streaming logfiler** - opretter forbindelse til tjenesten streaming log på dit websted.

###<a name="work-easy-apis"></a>Sådan: arbejde med nemt API'er i portalen Azure

Nemt API'er i portalen kan du oprette og arbejde med brugerdefinerede API'er højre på portalen. Du kan redigere API scripts ved hjælp af App Service Editor.

Når du klikker på **Nemt API'er** i indstillingerne for dit back end-websted, kan du tilføje, redigere eller slette et brugerdefineret API slutpunkt.

![Arbejde med nemt API'er](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

I portalen, kan du ændre adgangstilladelser for en given HTTP-handling, Rediger scriptfil API i App Service Editor eller få vist loggene streaming.

###<a name="online-editor"></a>Sådan: redigere koden i App Service Editor

Portalen Azure kan du redigere dine Node.js back end-script-filer i App Service Editor uden at skulle hente projektet til din lokale computer. Sådan redigerer du script-filer i editoren online:

1. Klik på **Indstillinger for alle** i din Mobile-App back end-blade > **nemt tabeller** eller **Nemt API'er**, skal du klikke på en tabel eller API og derefter klikke på **Rediger script**. Script-filen åbnes i App Service Editor.

    ![App Service Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)

2. Foretag dine ændringer til filen kode i editoren online. Ændringer gemmes automatisk, mens du skriver.


<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android-klienten Hurtig start]: app-service-mobile-android-get-started.md
[Apache Cordova klient Hurtig start]: app-service-mobile-cordova-get-started.md
[iOS-klienten Hurtig start]: app-service-mobile-ios-get-started.md
[Xamarin.iOS klient Hurtig start]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android klient Hurtig start]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms klient Hurtig start]: app-service-mobile-xamarin-forms-get-started.md
[Hurtig start Windows Store-klient]: app-service-mobile-windows-store-dotnet-get-started.md
[HTML/Javascript Client QuickStart]: app-service-html-get-started.md
[synkronisering af offline data]: app-service-mobile-offline-data-sync.md
[Sådan konfigureres Azure Active Directory-godkendelse]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Sådan konfigureres Facebook-godkendelse]: app-service-mobile-how-to-configure-facebook-authentication.md
[Sådan konfigureres Google-godkendelse]: app-service-mobile-how-to-configure-google-authentication.md
[Sådan konfigureres Microsoft Authentication]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Sådan konfigureres Twitter-godkendelse]: app-service-mobile-how-to-configure-twitter-authentication.md
[Installationsvejledning til Azure App Service]: ../app-service-web/web-sites-deploy.md
[Overvågning af en Azure App-tjeneste]: ../app-service-web/web-sites-monitor.md
[Aktivere logføring af diagnostik i Azure App-tjeneste]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Fejlfinding i forbindelse med en Azure App-tjenesten i Visual Studio]: ../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md
[angive den Node Version]: ../nodejs-specify-node-version-azure-apps.md
[bruge Node moduler]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service-web/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[Azure-portalen]: https://portal.azure.com/
[OData]: http://www.odata.org
[Løftet]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp eksempel på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[opgaveliste eksempel på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Eksempler mappe på GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js værktøjer 1.1 til Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[MSSQL Node.js pakke]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS program]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
