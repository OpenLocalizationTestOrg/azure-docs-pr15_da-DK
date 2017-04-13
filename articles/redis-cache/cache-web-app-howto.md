<properties 
    pageTitle="Sådan oprettes en Web App med Redis Cache | Microsoft Azure" 
    description="Lær at oprette en Web App med Redis Cache" 
    services="redis-cache" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="hero-article" 
    ms.date="10/11/2016" 
    ms.author="sdanie"/>

# <a name="how-to-create-a-web-app-with-redis-cache"></a>Sådan oprettes en Web App med Redis Cache

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Dette selvstudium viser, hvordan du opretter og installerer en ASP.NET web application til en WebApp i Azure App Service ved hjælp af Visual Studio-2015. Eksempelprogrammet viser en liste over team statistik fra en database og viser forskellige måder at bruge Azure Redis Cache til at gemme og hente data fra cachen. Når du udfører selvstudiet har du en igangværende WebApp, der læser og skriver til en database, optimeret med Azure Redis Cache og Azure som vært.

Du kan lære følgende:

-   Sådan oprettes en ASP.NET MVC 5 web application i Visual Studio.
-   Hvordan du kan få adgang til data fra en database ved hjælp af enhed Framework.
-   Sådan forbedre overførselshastigheden og reducere indlæsning af database ved at gemme og hente data ved hjælp af Azure Redis Cache.
-   Sådan bruger du en Redis sorteret sæt til at hente de øverste 5 teams.
-   Hvordan du klargør Azure ressourcer til programmet ved hjælp af en ressourcestyring skabelon.
-   Hvordan du publicerer programmet til Azure ved hjælp af Visual Studio.

## <a name="prerequisites"></a>Forudsætninger

For at fuldføre selvstudiet, skal du have følgende forudsætninger.

-   [Azure-konto](#azure-account)
-   [Visual Studio-2015 med Azure SDK til .NET](#visual-studio-2015-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Azure-konto

Du har brug for en Azure-konto for at fuldføre selvstudiet. Du kan:

* [Åbne en Azure-konto gratis](/pricing/free-trial/?WT.mc_id=redis_cache_hero). Du får kredit, der kan bruges til at teste betalt Azure tjenester. Selvom kreditterne bruges, kan du beholde kontoen og bruge gratis Azure tjenester og funktioner.
* [Aktivere Visual Studio abonnement fordele](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). MSDN-abonnement giver dig kredit hver måned, som du kan bruge til betalt Azure services.

### <a name="visual-studio-2015-with-the-azure-sdk-for-net"></a>Visual Studio-2015 med Azure SDK til .NET

Selvstudiet er skrevet til Visual Studio-2015 med [Azure SDK for.NET](../dotnet-sdk.md) 2.8.2 eller nyere. Kan du [hente den nyeste Azure SDK til Visual Studio 2015 her](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio installeres automatisk med SDK, hvis du ikke allerede har den.

Hvis du har Visual Studio 2013, kan du [hente den nyeste Azure SDK til Visual Studio-2013](http://go.microsoft.com/fwlink/?LinkID=324322). Nogle skærmbilleder kan se anderledes ud end illustrationer vises i dette selvstudium.

>[AZURE.NOTE] Afhængigt af hvor mange af SDK afhængighederne du allerede har på computeren, kan installerer SDK tage lang tid, fra flere minutter at en halv time eller mere.

## <a name="create-the-visual-studio-project"></a>Oprette Visual Studio-projekt

1. Åbne Visual Studio, og klik på **filer**, **Ny** **projekt**.

2. Udvid noden **Visual C#** i listen over **skabeloner** , Vælg **skyen**, og klik på **ASP.NET-webprogrammet**. Sørg for, at **.NET Framework 4.5.2** er markeret.  Skriv **ContosoTeamStats** i **tekstfeltet** , og klik på **OK**.
 
    ![Oprette et projekt][cache-create-project]

3. Vælg **MVC** som projekttype. Fjern markeringen i afkrydsningsfeltet **Host i skyen** . Bruger du [Klargør Azure ressourcer](#provision-the-azure-resources) og [publicere programmet til Azure](#publish-the-application-to-azure) i de efterfølgende trin i selvstudiet. Et eksempel på klargøring af en App Service WebApp fra Visual Studio ved at lade **Host i skyen** , der er markeret, kan du se [komme i gang med Web Apps i Azure App-tjenesten ved hjælp af ASP.NET og Visual Studio](../app-service-web/web-sites-dotnet-get-started.md).

    ![Vælg project-skabelon][cache-select-template]

4. Klik på **OK** for at oprette projektet.

## <a name="create-the-aspnet-mvc-application"></a>Oprette programmet ASP.NET MVC

I dette afsnit af selvstudiet, skal oprette du det grundlæggende program, der læser og viser team statistik fra en database.

-   [Tilføje modellen](#add-the-model)
-   [Tilføje controlleren](#add-the-controller)
-   [Konfigurere visningerne](#configure-the-views)

### <a name="add-the-model"></a>Tilføje modellen

1. Højreklik på **modeller** i **Solution Explorer**, og vælg **Tilføj**, **klasse**. 

    ![Tilføj model][cache-model-add-class]

2. Angiv `Team` i klassen navn og klikke på **Tilføj**.

    ![Tilføje model klasse][cache-model-add-class-dialog]

3. Erstatte den `using` sætninger øverst i den `Team.cs` filen med følgende ved hjælp af sætninger.


        using System;
        using System.Collections.Generic;
        using System.Data.Entity;
        using System.Data.Entity.SqlServer;


4. Erstatte definitionen af den `Team` klasse med følgende kodestykke, der indeholder en opdateret `Team` klasse definition samt nogle andre enhed Framework hjælper-klasser. Du kan finde flere oplysninger om kode første metode til at enhed Framework, der bruges i dette selvstudium, [kode først til en ny database](https://msdn.microsoft.com/data/jj193542).


        public class Team
        {
            public int ID { get; set; }
            public string Name { get; set; }
            public int Wins { get; set; }
            public int Losses { get; set; }
            public int Ties { get; set; }
        
            static public void PlayGames(IEnumerable<Team> teams)
            {
                // Simple random generation of statistics.
                Random r = new Random();
        
                foreach (var t in teams)
                {
                    t.Wins = r.Next(33);
                    t.Losses = r.Next(33);
                    t.Ties = r.Next(0, 5);
                }
            }
        }
        
        public class TeamContext : DbContext
        {
            public TeamContext()
                : base("TeamContext")
            {
            }
        
            public DbSet<Team> Teams { get; set; }
        }
        
        public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
        {
            protected override void Seed(TeamContext context)
            {
                var teams = new List<Team>
                {
                    new Team{Name="Adventure Works Cycles"},
                    new Team{Name="Alpine Ski House"},
                    new Team{Name="Blue Yonder Airlines"},
                    new Team{Name="Coho Vineyard"},
                    new Team{Name="Contoso, Ltd."},
                    new Team{Name="Fabrikam, Inc."},
                    new Team{Name="Lucerne Publishing"},
                    new Team{Name="Northwind Traders"},
                    new Team{Name="Consolidated Messenger"},
                    new Team{Name="Fourth Coffee"},
                    new Team{Name="Graphic Design Institute"},
                    new Team{Name="Nod Publishers"}
                };
        
                Team.PlayGames(teams);
        
                teams.ForEach(t => context.Teams.Add(t));
                context.SaveChanges();
            }
        }
        
        public class TeamConfiguration : DbConfiguration
        {
            public TeamConfiguration()
            {
                SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            }
        }


2. I **Solution Explorer**, skal du dobbeltklikke på **web.config** for at åbne den.

    ![Web.config][cache-web-config]

3.  Tilføj følgende forbindelsesstrengen til den `connectionStrings` sektion. Navnet på forbindelsesstrengen skal svare til navnet på den klasse, enhed Framework database kontekst som er `TeamContext`.

        <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />


    Når du har tilføjet, på `connectionStrings` sektion skal ligne eksemplet nedenfor.


        <connectionStrings>
            <add name="DefaultConnection" connectionString="Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-ContosoTeamStats-20160216120918.mdf;Initial Catalog=aspnet-ContosoTeamStats-20160216120918;Integrated Security=True"
                providerName="System.Data.SqlClient" />
            <add name="TeamContext" connectionString="Data Source=(LocalDB)\v11.0;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"  providerName="System.Data.SqlClient" />
        </connectionStrings>

### <a name="add-the-controller"></a>Tilføje controlleren

1. Tryk på **F6** for at oprette projektet. 
2. I **Solution Explorer**skal du højreklikke på mappen **enheder** og vælg **Tilføj**, **Controller**.

    ![Tilføje controller][cache-add-controller]

3. Vælg **MVC 5 Controller med visninger, ved hjælp af enhed Framework**, og klik på **Tilføj**. Hvis du får en fejl, når du klikker på **Tilføj**, kan du sikre dig, at du har oprettet projektet først.

    ![Tilføje controller klasse][cache-add-controller-class]

5. Vælg **Team (ContosoTeamStats.Models)** fra rullelisten **Model klasse** . Vælg **TeamContext (ContosoTeamStats.Models)** fra rullelisten **Data kontekst klasse** . Skriv `TeamsController` i tekstfeltet **Controller** navn (hvis det ikke automatisk udfyldt). Klik på **Tilføj** for at oprette klassen controller og tilføje standardvisningerne.

    ![Konfigurere controller][cache-configure-controller]

4. Udvid **Global.asax** i **Solution Explorer**, og dobbeltklik på **Global.asax.cs** for at åbne den.

    ![Global.asax.CS][cache-global-asax]

5. Tilføj de følgende to ved hjælp af sætninger øverst i filen under det andet ved hjælp af sætninger.


        using System.Data.Entity;
        using ContosoTeamStats.Models;


6. Tilføj følgende linje af kode i slutningen af den `Application_Start` metode.


        Database.SetInitializer<TeamContext>(new TeamInitializer());


7. I **Solution Explorer**skal du udvide `App_Start` , og dobbeltklik på `RouteConfig.cs`.

    ![RouteConfig.cs][cache-RouteConfig-cs]

8. Erstatte `controller = "Home"` i den følgende kode i den `RegisterRoutes` metode med `controller = "Teams"` som vist i følgende eksempel.


        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
        );


### <a name="configure-the-views"></a>Konfigurere visningerne

1. Udvid mappen **visninger** og klik derefter på den **delte** mappe i **Solution Explorer**, og dobbeltklik på **_Layout.cshtml**. 

    ![_Layout.cshtml][cache-layout-cshtml]

2. Ændre indholdet af den `title` element og Erstat `My ASP.NET Application` med `Contoso Team Stats` som vist i følgende eksempel.


        <title>@ViewBag.Title - Contoso Team Stats</title>


3. I den `body` skal du opdatere først `Html.ActionLink` -sætning og Erstat `Application name` med `Contoso Team Stats` og erstatte `Home` med `Teams`.
    -   Før:`@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
    -   Efter:`@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`

    ![Kodeændringer][cache-layout-cshtml-code]

4. Tryk på **Ctrl + F5** for at oprette og køre programmet. Denne version af programmet læser resultaterne direkte fra databasen. Bemærk de **Opretter nye**, **redigere**, **Detaljer**og **slette** handlinger, der automatisk blev føjet til programmet ved **MVC 5 Controller med visninger, ved hjælp af enhed Framework** scaffold. Du skal tilføje Redis Cache for at optimere dataadgang og angive yderligere funktioner til programmet i næste afsnit af selvstudiet.

![Starter programmet][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Konfigurere programmet til at bruge Redis Cache

I dette afsnit af selvstudiet, skal du konfigurere eksempel program tilladelse til at lagre og hente Contoso team statistik fra en Azure Redis Cache forekomst ved hjælp af [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) cache klient.

-   [Konfigurere programmet til at bruge StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
-   [Opdatere klassen TeamsController for at returnere resultater fra cachen eller databasen](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
-   [Opdatere Opret, Rediger og Slet metoder til at arbejde med cachen](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
-   [Opdatere visningen Teams indeks til at arbejde med cachen](#update-the-teams-index-view-to-work-with-the-cache)


### <a name="configure-the-application-to-use-stackexchangeredis"></a>Konfigurere programmet til at bruge StackExchange.Redis

1. Konfigurere en klientprogrammet i Visual Studio ved hjælp af pakken StackExchange.Redis NuGet, højreklik på projektet i **Solution Explorer** , og vælg **Administrer NuGet pakker**. 

    ![Administrere NuGet-pakker][redis-cache-manage-nuget-menu]

2. Skriv **StackExchange.Redis** i søgefeltet, Vælg den ønskede version fra resultaterne, og klik på **Installer**.

    ![StackExchange.Redis NuGet pakke][redis-cache-stack-exchange-nuget]

    Pakken NuGet downloader og tilføjer de nødvendige samling referencer til klientprogrammet og adgang til Azure Redis Cache med StackExchange.Redis cache-klienten. Hvis du foretrækker at bruge en stærk med navnet version af biblioteket **StackExchange.Redis** klient, vælge **StackExchange.Redis.StrongName**; Ellers skal du vælge **StackExchange.Redis**.

3. Udvid mappen **enheder** i **Solution Explorer**, og dobbeltklik på **TeamsController.cs** for at åbne den.

    ![Teams controller][cache-teamscontroller]

4. Tilføj de følgende to ved hjælp af sætninger til **TeamsController.cs**.

        using System.Configuration;
        using StackExchange.Redis;

5. Tilføj følgende to egenskaber til den `TeamsController` klasse.

        // Redis Connection string info
        private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
        {
            string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
            return ConnectionMultiplexer.Connect(cacheConnection);
        });
    
        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }
  
1. Oprette en fil på computeren med navnet `WebAppPlusCacheAppSecrets.config` og placere det i en placering, der ikke tjekkes ind kildekode i dit eksempelprogram, hvis du beslutter at tjekke den ind et sted. I dette eksempel på `AppSettingsSecrets.config` fil er placeret på `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.

    Redigere den `WebAppPlusCacheAppSecrets.config` filen og tilføje følgende indholdet. Hvis du kører programmet lokalt bruges disse oplysninger til at oprette forbindelse til din Azure Redis Cache forekomst. Senere i selvstudiet du klargør en Azure Redis Cache forekomst og opdatere cachen brugernavn og adgangskode. Hvis du ikke vil køre eksempelprogrammet lokalt kan du springe oprettelse af denne fil og de efterfølgende trin, som henviser til fil, fordi når du installerer til Azure programmet henter forbindelsesoplysningerne cachen fra app indstillingen for Web App og ikke fra denne fil. Da den `WebAppPlusCacheAppSecrets.config` ikke er installeret til Azure med dit program, du ikke har brug for det medmindre du vil køre programmet lokalt.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


2. I **Solution Explorer**, skal du dobbeltklikke på **web.config** for at åbne den.

    ![Web.config][cache-web-config]

3. Tilføj følgende `file` attribut til den `appSettings` element. Hvis du har brugt et andet navn eller placering, kan du erstatte disse værdier for de vises i eksemplet.
    -   Før:`<appSettings>`
    -   Efter:` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`

    ASP.NET-runtime fletter indholdet af den eksterne fil med markering i den `<appSettings>` element. Kørsel af ignorerer attributten fil, hvis den angivne fil ikke blev fundet. Din hemmeligheder (forbindelsesstrengen til din cache) er ikke inkluderet som en del af koden for programmet. Når du installerer din online til Azure, den `WebAppPlusCacheAppSecrests.config` fil ikke installeres (som er det, du vil). Der er flere måder til at angive disse hemmeligheder i Azure, og i dette selvstudium de konfigureres automatisk for dig, når du [Klargør Azure ressourcer](#provision-the-azure-resources) i en efterfølgende selvstudium trin. Se [bedste fremgangsmåder til implementering af adgangskoder og andre følsomme data til ASP.NET og Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)kan finde flere oplysninger om at arbejde med hemmeligheder i Azure.


### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Opdatere klassen TeamsController for at returnere resultater fra cachen eller databasen

I dette eksempel kan team statistik hentes fra databasen eller fra cachen. Team statistik gemmes i cachen som en fortløbende `List<Team>`, og også som et sorteret sæt Redis datatyper. Når hente elementer fra et sorteret sæt, kan du hente nogle, alle eller forespørgsel for visse elementer. I dette eksempel kan du forespørge sorteret sættet for de øverste 5 teams sorteret efter antal wins.

>[AZURE.NOTE] Det er ikke påkrævet for at gemme team stikprøvefunktioner i flere formater i cachen for at kunne bruge Azure Redis Cache. Dette selvstudium bruger flere formater til at vise nogle af de forskellige måder og forskellige datatyper, som du kan bruge til data fra cache.



1. Tilføj følgende brug af en sætning til den `TeamsController.cs` fil øverst med hinanden ved hjælp af sætninger.

        using System.Diagnostics;
        using Newtonsoft.Json;

2. Erstat aktuelt `public ActionResult Index()` metode med følgende implementering.


        // GET: Teams
        public ActionResult Index(string actionType, string resultType)
        {
            List<Team> teams = null;
        
            switch(actionType)
            {
                case "playGames": // Play a new season of games.
                    PlayGames();
                    break;
        
                case "clearCache": // Clear the results from the cache.
                    ClearCachedTeams();
                    break;
        
                case "rebuildDB": // Rebuild the database with sample data.
                    RebuildDB();
                    break;
            }
        
            // Measure the time it takes to retrieve the results.
            Stopwatch sw = Stopwatch.StartNew();
        
            switch(resultType)
            {
                case "teamsSortedSet": // Retrieve teams from sorted set.
                    teams = GetFromSortedSet();
                    break;
        
                case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                    teams = GetFromSortedSetTop5();
                    break;
        
                case "teamsList": // Retrieve teams from the cached List<Team>.
                    teams = GetFromList();
                    break;
        
                case "fromDB": // Retrieve results from the database.
                default:
                    teams = GetFromDB();
                    break;
            }
        
            sw.Stop();
            double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

            // Add the elapsed time of the operation to the ViewBag.msg.
            ViewBag.msg += " MS: " + ms.ToString();
        
            return View(teams);
        }


3. Tilføj følgende tre metoder til den `TeamsController` klasse at implementere den `playGames`, `clearCache`, og `rebuildDB` handlingstyper fra den switch-sætning, der er tilføjet i det forrige kodestykke.

    Den `PlayGames` metode opdaterer team statistik ved at simulere en ferie spil, gemmer resultatet til databasen og rydder nu forældede data fra cachen.


        void PlayGames()
        {
            ViewBag.msg += "Updating team statistics. ";
            // Play a "season" of games.
            var teams = from t in db.Teams
                        select t;
    
            Team.PlayGames(teams);
    
            db.SaveChanges();
    
            // Clear any cached results
            ClearCachedTeams();
        }


    Den `RebuildDB` metode initialiseres igen databasen med standardsættet af teams, genererer statistik for dem og rydder nu forældede data fra cachen.
    
        void RebuildDB()
        {
            ViewBag.msg += "Rebuilding DB. ";
            // Delete and re-initialize the database with sample data.
            db.Database.Delete();
            db.Database.Initialize(true);

            // Clear any cached results
            ClearCachedTeams();
        }


    Den `ClearCachedTeams` metode fjerner alle cachelagrede team statistik fra cachen.

    
        void ClearCachedTeams()
        {
            IDatabase cache = Connection.GetDatabase();
            cache.KeyDelete("teamsList");
            cache.KeyDelete("teamsSortedSet");
            ViewBag.msg += "Team data removed from cache. ";
        } 


4. Tilføj følgende fire metoder til den `TeamsController` klasse at implementere de forskellige metoder til at hente team statistik fra cachen og databasen. Hver af disse metoder returnerer en `List<Team>` som derefter vises i visningen.

    Den `GetFromDB` metode læser team statistik fra databasen.

        List<Team> GetFromDB()
        {
            ViewBag.msg += "Results read from DB. ";
            var results = from t in db.Teams
                orderby t.Wins descending
                select t; 
    
            return results.ToList<Team>();
        }


    Den `GetFromList` metode læser team statistik fra cachen som en fortløbende `List<Team>`. Hvis der er en fundne forekomster skal læses fra databasen team statistik og derefter gemmes i cachen til næste gang. I dette eksempel bruger vi JSON.NET serialisering ordne sekventielt .NET-objekter til og fra cachen. Du kan finde flere oplysninger, se, [hvordan du arbejder med .NET objekter i Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

        List<Team> GetFromList()
        {
            List<Team> teams = null;

            IDatabase cache = Connection.GetDatabase();
            string serializedTeams = cache.StringGet("teamsList");
            if (!String.IsNullOrEmpty(serializedTeams))
            {
                teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

                ViewBag.msg += "List read from cache. ";
            }
            else
            {
                ViewBag.msg += "Teams list cache miss. ";
                // Get from database and store in cache
                teams = GetFromDB();

                ViewBag.msg += "Storing results to cache. ";
                cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
            }
            return teams;
        }


    Den `GetFromSortedSet` metode læser team statistik fra et cachelagrede sorteret sæt. Hvis der er en fundne forekomster, er team statistik læses fra databasen og gemmes i cachen som et sorteret sæt.


        List<Team> GetFromSortedSet()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();
            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
            if (teamsSortedSet.Count() > 0)
            {
                ViewBag.msg += "Reading sorted set from cache. ";
                teams = new List<Team>();
                foreach (var t in teamsSortedSet)
                {
                    Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                    teams.Add(tt);
                }
            }
            else
            {
                ViewBag.msg += "Teams sorted set cache miss. ";
    
                // Read from DB
                teams = GetFromDB();
    
                ViewBag.msg += "Storing results to cache. ";
                foreach (var t in teams)
                {
                    Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                    cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
                }
            }
            return teams;
        }


    Den `GetFromSortedSetTop5` metode læser top 5 teams fra sættet cachelagrede sorteret. Det starter ved at markere cachen for eksistensen af de `teamsSortedSet` nøgle. Hvis denne nøgle ikke er til stede, den `GetFromSortedSet` metode kaldes for at læse team statistik og gemme dem i cachen. Næste er cachelagrede sorteret sættet forespørges for de øverste 5 teams, der returneres som.


        List<Team> GetFromSortedSetTop5()
        {
            List<Team> teams = null;
            IDatabase cache = Connection.GetDatabase();

            // If the key teamsSortedSet is not present, this method returns a 0 length collection.
            var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            if(teamsSortedSet.Count() == 0)
            {
                // Load the entire sorted set into the cache.
                GetFromSortedSet();

                // Retrieve the top 5 teams.
                teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
            }

            ViewBag.msg += "Retrieving top 5 teams from cache. ";
            // Get the top 5 teams from the sorted set
            teams = new List<Team>();
            foreach (var team in teamsSortedSet)
            {
                teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
            }
            return teams;
        }


### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Opdatere Opret, Rediger og Slet metoder til at arbejde med cachen

Stilladser koden, der er oprettet som et led i dette eksempel indeholder metoder til at tilføje, redigere og slette grupper. Når en gruppe er tilføjet, redigeres eller fjernes, bliver dataene i cachen forældede. I dette afsnit skal du redigere disse tre metoder til at rydde de cachelagrede teams, så cachen ikke synkroniseret med databasen.

1. Gå til den `Create(Team team)` metode i den `TeamsController` klasse. Tilføje et opkald til den `ClearCachedTeams` metode, som vist i følgende eksempel.


        // POST: Teams/Create
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Teams.Add(team);
                db.SaveChanges();
                // When a team is added, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
    
            return View(team);
        }


2. Gå til den `Edit(Team team)` metode i den `TeamsController` klasse. Tilføje et opkald til den `ClearCachedTeams` metode, som vist i følgende eksempel.


        // POST: Teams/Edit/5
        // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
        // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
        [HttpPost]
        [ValidateAntiForgeryToken]
        public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
        {
            if (ModelState.IsValid)
            {
                db.Entry(team).State = EntityState.Modified;
                db.SaveChanges();
                // When a team is edited, the cache is out of date.
                // Clear the cached teams.
                ClearCachedTeams();
                return RedirectToAction("Index");
            }
            return View(team);
        }


3. Gå til den `DeleteConfirmed(int id)` metode i den `TeamsController` klasse. Tilføje et opkald til den `ClearCachedTeams` metode, som vist i følgende eksempel.


        // POST: Teams/Delete/5
        [HttpPost, ActionName("Delete")]
        [ValidateAntiForgeryToken]
        public ActionResult DeleteConfirmed(int id)
        {
            Team team = db.Teams.Find(id);
            db.Teams.Remove(team);
            db.SaveChanges();
            // When a team is deleted, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Opdatere visningen Teams indeks til at arbejde med cachen

1. Udvid mappen **visninger** , derefter **Teams** mappen, i **Solution Explorer**, og dobbeltklik på **Index.cshtml**.

    ![Index.cshtml][cache-views-teams-index-cshtml]

2. Se efter følgende afsnit element øverst i filen.

    ![Handling tabel][cache-teams-index-table]

    Dette er linket til at oprette en ny gruppe. Erstatte elementet afsnit med den følgende tabel. I denne tabel indeholder Handlingslinks til at oprette en ny gruppe, afspilles en ny ferie spil, rydde cachen, hente teams fra cachen i flere formater, henter teams fra databasen og genopbygge databasen med frisk eksempeldata.


        <table class="table">
            <tr>
                <td>
                    @Html.ActionLink("Create New", "Create")
                </td>
                <td>
                    @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
                </td>
                <td>
                    @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
                </td>
                <td>
                    @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
                </td>
                <td>
                    @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
                </td>
                <td>
                    @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
                </td>
                <td>
                    @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
                </td>
                <td>
                    @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
                </td>
            </tr>    
        </table>


3. Rul ned til bunden af filen **Index.cshtml** og Tilføj følgende `tr` element, så de den sidste række i den sidste tabel i filen.

        <tr><td colspan="5">@ViewBag.Msg</td></tr>

    I denne række vises værdien af `ViewBag.Msg` som indeholder en statusrapport over den aktuelle handling, der er angivet, når du klikker på et af linkene handling fra det forrige trin.   

    ![Statusmeddelelse][cache-status-message]

4. Tryk på **F6** for at oprette projektet.

## <a name="provision-the-azure-resources"></a>Klargøre Azure ressourcer

Hvis du vil placere dit program i Azure, skal du først klargøre de Azure tjenester, der kræver, at dit program. Eksempelprogrammet i dette selvstudium bruger følgende Azure tjenester.

-   Azure Redis Cache
-   App Service WebApp
-   SQL-Database

Du skal installere disse tjenester til en ny eller eksisterende ressourcegruppe efter eget valg, skal du klikke på knappen følgende **Implementer til Azure** .

[! [Installeres til Azure] [deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Denne **Implementer til Azure** -knap bruger skabelonen [Opret en WebApp plus Redis Cache plus SQL-Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Azure Hurtig start](https://github.com/Azure/azure-quickstart-templates) til at klargøre disse tjenester og angive forbindelsesstrengen for den SQL-Database og indstillingen programmet for forbindelsesstrengen Azure Redis Cache.

>[AZURE.NOTE] Hvis du ikke har en Azure-konto, kan du [oprette en gratis Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) på blot et par minutter.

Klik på knappen **Installer til Azure** tager dig til portalen Azure og starter processen med at oprette de ressourcer, der er beskrevet af skabelonen.

![Installere på Azure][cache-deploy-to-azure-step-1]

1. Vælg det Azure abonnement bruger, og vælg en eksisterende ressourcegruppe eller oprette en ny på bladet **brugerdefineret installation** , og Angiv ressource gruppe placering.
2. Angiv navn på en administratorkonto på bladet **parametre** (**ADMINISTRATORLOGIN** - Brug ikke **administrator**), administrator-logonadgangskode (**ADMINISTRATORLOGINPASSWORD**), og databasenavnet (**DATABASENAVN**). De andre parametre er konfigureret til en gratis App-tjeneste-plan, samt lavere omkostninger indstillinger for SQL-Database og Azure Redis Cache, som ikke leveres med en gratis niveau.
3. Ændre nogen af de andre indstillinger, hvis du ønsker, eller Behold standardindstillingerne, og klik på **OK**.


![Installere på Azure][cache-deploy-to-azure-step-2]

1. Klik på **Gennemse juridiske betingelser**.
2. Læs vilkårene på bladet **Køb** , og klik på **Køb**.
3. For at starte klargøring af ressourcerne, der skal du klikke på **Opret** på bladet **brugerdefineret installation** .

Klik på ikonet for at få vist status for din installation, og klikke på **installation i gang**.

![Installation i gang][cache-deployment-started]

Du kan få vist status for din installation på bladet **Microsoft.Template** .

![Installere på Azure][cache-deploy-to-azure-step-3]

Når klargøring er fuldført, kan du publicere dit program til Azure fra Visual Studio.

>[AZURE.NOTE] Eventuelle fejl, der kan forekomme under processen klargøring vises på bladet **Microsoft.Template** . Almindelige fejl er for mange SQL-servere eller for mange gratis App Service vært planer per abonnement. Løs eventuelle fejl, og genstart processen ved at klikke på **Geninstaller** i bladet **Microsoft.Template** eller knappen **Implementer til Azure** i dette selvstudium.

## <a name="publish-the-application-to-azure"></a>Publicere programmet til Azure

I dette trin i selvstudiet, skal du publicere programmet til Azure og køre den i skyen.

1. Højreklik på **ContosoTeamStats** projektet i Visual Studio, og vælg **Publicer**.

    ![Publicere][cache-publish-app]

2. Klik på **Microsoft Azure App tjeneste**.

    ![Publicere][cache-publish-to-app-service]

3. Vælg det abonnement, der bruges ved oprettelse af de ressourcer, der Azure, udvid ressourcegruppen, der indeholder ressourcerne, Vælg den ønskede Web App, og klik på **OK**. Hvis du har brugt knappen **Implementer til Azure** starter kaldt Web App med **websted** efterfulgt af nogle flere tegn.

    ![Vælg WebApp][cache-select-web-app]

4. Klik på **Valider forbindelse** for at bekræfte dine indstillinger, og klik derefter på **Udgiv**.

    ![Publicere][cache-publish]

    Efter et øjeblik publiceringsprocessen fuldfører og en browser startes med kørslen eksempel på programmet på computeren. Hvis du får en DNS-fejl under validering eller publicere, og klargøring processen for de Azure ressourcer for programmet netop er afsluttet, skal du vente et øjeblik og prøve igen.

    ![Cache tilføjet][cache-added-to-application]

I følgende tabel beskrives hver handlingslink fra eksempelprogrammet.

| Handling                  | Beskrivelse                                                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Opret et nyt              | Oprette en ny gruppe.                                                                                                                                               |
| Afspil Season             | Afspille en ferie spil, opdatere team statistik, og fjern markeringen forældede team data fra cachen.                                                                          |
| Ryd Cache             | Fjern team statistik fra cachen.                                                                                                                             |
| Liste fra cachen         | Hent team statistik fra cachen. Hvis der er en fundne forekomster, indlæse statistikkerne fra databasen, og Gem i cachen til næste gang.                                        |
| Sorteret sæt fra cachen   | Hent team statistik fra cachen til brug af et sorteret sæt. Hvis der er en fundne forekomster, indlæse statistikkerne fra databasen, og Gem i cachen med et sorteret sæt.  |
| Top 5 Teams fra cachen  | Hent de øverste 5 teams fra cachen til brug af et sorteret sæt. Hvis der er en fundne forekomster, indlæse statistikkerne fra databasen, og Gem i cachen med et sorteret sæt. |
| Indlæse fra DB            | Hente team statistik fra databasen.                                                                                                                       |
| Genopbygge DB              | Genopbygge databasen og genindlæses med eksempeldata team.                                                                                                        |
| Redigere / detaljer / Slet | Redigere et team, få vist detaljer for et team, slette en gruppe.                                                                                                             |


Klik på nogle af handlingerne og eksperimentere med henter data fra forskellige kilder. Ikke forskelle i den tid, det tager for at fuldføre de forskellige metoder til at hente data fra databasen og cachen.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Slette ressourcerne, når du er færdig med programmet

Når du er færdig med eksempel selvstudium programmet, kan du slette de Azure ressourcer, der bruges for at spare omkostninger og ressourcer. Hvis du bruger knappen **Implementer til Azure** i afsnittet [klargøre Azure ressourcer](#provision-the-azure-resources) og alle ressourcerne er indeholdt i den samme ressourcegruppe, kan du slette dem sammen på én gang ved at slette ressourcegruppen.

1. Log på [Azure-portalen](https://portal.azure.com) , og klik på **ressourcegrupper**.
2. Skriv navnet på din ressourcegruppe i tekstfeltet **filtrere elementer …** .
3. Klik på **...** til højre for din ressourcegruppe.
4. Klik på **Slet**.

    ![Slet][cache-delete-resource-group]

5. Skriv navnet på din ressourcegruppe, og klik på **Slet**.

    ![Bekræft sletning][cache-delete-confirm]

Efter et øjeblik ressourcegruppen og alle dens indeholdte ressourcer, der slettes.

>[AZURE.IMPORTANT] Bemærk, at slette en ressourcegruppe er ikke kan fortrydes og, ressourcegruppen og alle ressourcerne i den slettet permanent. Sørg for, at du ikke ved et uheld sletter den forkert ressourcegruppe eller ressourcer. Hvis du oprettede ressourcer til at være vært i dette eksempel i en eksisterende ressourcegruppe, kan du slette hver ressource enkeltvis fra deres respektive blade.

## <a name="run-the-sample-application-on-your-local-machine"></a>Køre eksempelprogrammet på din lokale computer

Hvis du vil køre programmet lokalt på din computer, skal du en Azure Redis Cache-forekomst, til at cachelagre dine data. 

-   Hvis du har udgivet dit program til Azure, som beskrevet i forrige afsnit, kan du bruge den Azure Redis Cache-forekomst, der blev klargjort i dette trin.
-   Hvis du har en anden eksisterende Azure Redis Cache-forekomst, kan du bruge, til at køre dette eksempel lokalt.
-   Hvis du har brug at oprette en Azure Redis Cache-forekomst, kan du følge trinnene i [oprette en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Når du har valgt eller oprettet cachen bruge, kan du gå til cachen i portalen Azure og hente de [værtsnavn](cache-configure.md#properties) og [access-taster](cache-configure.md#access-keys) til din cache. Yderligere oplysninger finder du [konfigurere Redis cacheindstillinger](cache-configure.md#configure-redis-cache-settings).

1. Åbn den `WebAppPlusCacheAppSecrets.config` fil, du oprettede under trinnet [Konfigurer programmet til at bruge Redis cachen](#configure-the-application-to-use-redis-cache) i dette selvstudium, ved hjælp af editor efter eget valg.

2. Redigere den `value` attribut og erstatte `MyCache.redis.cache.windows.net` med [værtsnavn](cache-configure.md#properties) på din cache og angive enten den [primære eller sekundære nøgle](cache-configure.md#access-keys) i din cache som adgangskoden.


        <appSettings>
          <add key="CacheConnection" value="MyCache.redis.cache.windows.net,abortConnect=false,ssl=true,password=..."/>
        </appSettings>


3. Tryk på **Ctrl + F5** for at køre programmet.

>[AZURE.NOTE] Bemærk, fordi det program, herunder databasen, der kører lokalt og cachen Redis ligger i Azure, cachen kan blive vist til under udføre databasen. Bedste ydeevne, skal være klientprogrammet og Azure Redis Cache forekomst på samme placering. 

## <a name="next-steps"></a>Næste trin

-   Lær mere om [Introduktion til ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) på [ASP.NET](http://asp.net/) -webstedet.
-   Se flere eksempler for at oprette en ASP.NET Web App i App Service, under [oprette og anvende en ASP.NET WebApp i Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) fra [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 forbinde [demo](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
    -   Du kan finde flere Hurtig start-guider fra HealthClinic.biz videoen, [Azure udvikler værktøjer Hurtig start-guider](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).
-   Få mere at vide om den [kode først til en ny database](https://msdn.microsoft.com/data/jj193542) metode til at enhed Framework, der bruges i dette selvstudium.
-   Lær mere om [webapps i Azure App Service](../app-service-web/app-service-web-overview.md).
-   Lær, hvordan du kan [overvåge](cache-how-to-monitor.md) din cache på portalen Azure.

-   Udforske Azure Redis Cache premium-funktioner
    -   [Sådan konfigureres brugerdata i en Premium Azure Redis Cache](cache-how-to-premium-persistence.md)
    -   [Sådan konfigureres databaseserver for en Premium Azure Redis Cache](cache-how-to-premium-clustering.md)
    -   [Sådan konfigureres virtuelt netværk understøttelse af en Premium Azure Redis Cache](cache-how-to-premium-vnet.md)
    -   Se [Azure Redis Cache ofte stillede spørgsmål](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) få mere at vide om størrelse, overførselshastighed og båndbredde med premium cache.



<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

