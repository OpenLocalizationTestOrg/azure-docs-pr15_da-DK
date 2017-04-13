<properties 
    pageTitle="Brug af elastiske database klientbibliotek med enhed Framework | Microsoft Azure" 
    description="Brug af elastiske Database klientbibliotek og enhed Framework til kodningssprog databaser" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="elastic-database-client-library-with-entity-framework"></a>Elastiske Database klientbibliotek med enhed Framework 
 
Dette dokument vises ændringer i et objekt Framework-program, der er nødvendige for at integrere med [elastiske Databaseværktøjer](sql-database-elastic-scale-introduction.md). Der er fokus på skriver [shard tilknytte administration](sql-database-elastic-scale-shard-map-management.md) og [data-afhængige routing](sql-database-elastic-scale-data-dependent-routing.md) med den enhed Framework **Kode første** tilgang. Det [Første kode – ny Database](http://msdn.microsoft.com/data/jj193542.aspx) selvstudium til ELEMENTÆRFILEN fungerer som eksemplet, der kører i hele dokumentet. Eksempelkoden følger dette dokument er en del af elastiske Databaseværktøjer sæt eksempler i Visual Studio-kodeeksempler.
  
## <a name="downloading-and-running-the-sample-code"></a>Hente og køre eksempelkoden
Sådan henter du koden for denne artikel:

* Visual Studio 2012 eller senere er påkrævet. 
* Start Visual Studio. 
* I Visual Studio-Vælg Filer > nyt projekt. 
* Gå til **Online eksempler** til **Visual C#** , og skriv "elastiske db" i søgefeltet i øverste højre hjørne i dialogboksen 'Nyt projekt'.
    
    ![Enhed Framework og elastiske databasen eksempel app][1] 

    Markér eksemplet kaldet **Elastiske DB værktøjer til Azure SQL-enhed Framework Integration**. Når du har accepteret licensen, indlæser stikprøvernes. 

Hvis du vil køre eksemplet, skal du oprette tre tomme databaser i Azure SQL-Database:

* Shard kort Manager-database
* Shard 1 database
* Shard 2-database

Når du har oprettet disse databaser, Udfyld sted deltagerne i **Program.cs** med Azure SQL DB servernavn, databasenavnene og dine legitimationsoplysninger til at oprette forbindelse til databaserne. Oprette løsningen i Visual Studio. Visual Studio henter de nødvendige NuGet pakker til biblioteket elastiske database klient enhed Framework og midlertidige fejl håndtering af som en del af processen build. Sørg for, at gendanne NuGet pakker er aktiveret for din løsning. Du kan aktivere denne indstilling ved at højreklikke på løsningsfilen i Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Enhed Framework arbejdsprocesser 

Enhed Framework udviklere skal have en af følgende fire arbejdsprocesser til at opbygge programmer og sikre brugerdata i programmet objekter: 

* **Kode første (ny Database)**: feltet ELEMENTÆRFILEN udvikler opretter modellen i programmet koden og derefter ELEMENTÆRFILEN genererer databasen fra den. 
* **Kode første (eksisterende Database)**: udvikleren kan ELEMENTÆRFILEN generere programkode til modellen fra en eksisterende database.
* **Model første**: udvikleren opretter modellen i ELEMENTÆRFILEN designer og derefter ELEMENTÆRFILEN opretter databasen fra modellen.
* **Første database**: udvikleren bruger ELEMENTÆRFILEN værktøjer for at udlede modellen fra en eksisterende database. 

Alle disse fremgangsmåder, der er afhængige af klassen DbContext til at administrere transparent databaseforbindelser og databaseskema for et program. Databasen starte sig selv og -Skemafiler oprettelse, som vi vil diskutere mere detaljeret senere i dokumentet, forskellige konstruktører på den grundlæggende klasse DbContext Tillad forskellige niveauer af kontrol over oprettelse af forbindelse. Udfordringer opstår primært med, at den database forbindelse administration i ELEMENTÆRFILEN skærer med forbindelse projektoversigter i de data afhængige routing brugergrænseflader, der leveres af biblioteket elastiske database klient. 

## <a name="elastic-database-tools-assumptions"></a>Elastiske database værktøjer antagelser 

Du kan finde ord definitioner, [elastiske databasen værktøjer ordliste](sql-database-elastic-scale-glossary.md).

Med elastiske database klient-bibliotek definerer du partitioner af programdataene kaldet shardlets. Shardlets er identificeret med en sharding nøgle og er knyttet til specifikke databaser. Et program kan have så mange databaser med efter behov og distribuere shardlets for at give tilstrækkelig kapacitet eller ydeevne givet aktuelle virksomhedens behov. Tilknytning af sharding nøgleværdier til databaserne, der er gemt af et shard kort, der er angivet af elastiske database klienten API'er. Vi kalder denne egenskab **Shard kort Management**eller SMM for kort. Shard kortet fungerer også som broker af databaseforbindelser til anmodninger, der indeholder en sharding-nøgle. Vi henviser til denne egenskab som **data-afhængige routing**. 
 
Shard kort manager beskytter brugere fra inkonsekvent visninger til shardlet data, der kan opstå, når der sker samtidige shardlet management handlinger (såsom flytte data fra én shard til en anden). For at gøre det, administreres shard kort af klienten bibliotek broker databaseforbindelser til et program. Dette giver mulighed for shard kort funktioner for at afbryde en databaseforbindelse automatisk, når shard management handlinger påvirke den shardlet, der er oprettet forbindelse til. Denne metode skal integreres med nogle af ELEMENTÆRFILENS funktionalitet, som oprettelse af nye forbindelser fra et eksisterende dokument til at kontrollere, om databasen findes. Vores observationer er generelt, at de almindelige DbContext konstruktører kun arbejds pålideligt for lukket databaseforbindelser, der kan sikkert klones for ELEMENTÆRFILEN fungerer. Design princippet bag elastiske database er i stedet at kun broker åbnet forbindelser. En tror, at lukke en formidlet ved biblioteket klient før overdrager det til ELEMENTÆRFILEN DbContext forbindelse kan løse dette problem. Men ved at lukke forbindelsen og stole på ELEMENTÆRFILEN åbne den igen, en foregoes validering og konsistens Kontroller, der er udført af biblioteket. Funktionen overførsler i ELEMENTÆRFILEN, men bruger disse forbindelser til at administrere det underliggende databaseskema på en måde, der er usynligt til programmet. Ideelt set vil vi bevare og kombinere alle disse funktioner fra elastiske database klientbibliotek og ELEMENTÆRFILEN i det samme program. I følgende afsnit beskrives disse egenskaber og krav mere detaljeret. 


## <a name="requirements"></a>Krav 

Når du arbejder med både elastiske database klientbibliotek og enhed Framework API'er, vil vi bevare følgende egenskaber: 

* **Skala ud**: tilføje eller fjerne databaser fra data niveauet af delt programmet efter behov for kapacitet kravene af programmet. Det betyder kontrol den oprettelsen og sletningen af databaser og bruge elastiske database shard tilknytte manager API'er til at administrere databaser og tilknytninger af shardlets. 

* **Konsistens**: programmet anvender sharding og anvender data afhængige routing funktionerne i biblioteket klient. For at undgå beskadigelse eller forkert forespørgselsresultater, forbindelser er formidlet via shard kort manager. Dette bevarer også validering og konsistens.
 
* **Kode første**: til at bevare nemmere af ELEMENTÆRFILENS kode første paradigme. I kode første er klasser i programmet tilknyttet transparent underliggende databasestrukturer. Programkoden skal arbejde sammen med DbSets, skjule de fleste aspekter involveret i den underliggende databasebehandling.
 
* **Skema**: enhed Framework håndterer indledende database skemaoprettelse og efterfølgende skema udviklingen gennem overførsler. Ved at beholde disse funktioner, er tilpasse din app nemt som data, der udvikles. 

De følgende retningslinjer giver besked om hvordan du opfylder kravene til kode første programmer, der bruger elastiske Databaseværktøjer. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Data, der er afhængige routing ved hjælp af ELEMENTÆRFILEN DbContext 

Databaseforbindelser med enhed Framework administreres typisk gennem underklasser til **DbContext**. Oprette disse underklasser ved afledt af **DbContext**. Dette er, hvor du definerer din **DbSets** , der implementerer samlingerne sikkerhedskopien af databasen CLR objekter for dit program. I forbindelse med afhængige distribution af data, kan vi identificere flere nyttige egenskaber, der ikke nødvendigvis indeholder andre ELEMENTÆRFILEN kode første programmet scenarier: 

* Databasen findes allerede og er registreret i elastiske database shard kortet. 
* Skema af programmet er allerede blevet installeret til databasen (beskrevet nedenfor). 
* Data-afhængige routing forbindelser til databasen er formidlet ved shard kortet. 

Integrere **DbContexts** med data-afhængige routing til skala ud:

1. Oprette fysiske databaseforbindelser via elastiske database klient grænseflader af shard kort manager 
2. Ombryde forbindelse med **DbContext** underklasse
3. Videregive forbindelsen til de grundlæggende klasser **DbContext** at sikre, at alle behandling i ELEMENTÆRFILEN side sker der, samt. 

Følgende kodeeksempel illustrerer denne metode. (Denne kode er også i det medfølgende Visual Studio-projekt)

    public class ElasticScaleContext<T> : DbContext
    {
    public DbSet<Blog> Blogs { get; set; }
    …

        // C'tor for data dependent routing. This call will open a validated connection 
        // routed to the proper shard by the shard map manager. 
        // Note that the base class c'tor call will fail for an open connection
        // if migrations need to be done and SQL credentials are used. This is the reason for the 
        // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
        public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
            : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
            true /* contextOwnsConnection */)
        {
        }

        // Only static methods are allowed in calls into base class c'tors.
        private static DbConnection CreateDDRConnection(
        ShardMap shardMap, 
        T shardingKey, 
        string connectionStr)
        {
            // No initialization
            Database.SetInitializer<ElasticScaleContext<T>>(null);

            // Ask shard map to broker a validated connection for the given key
            SqlConnection conn = shardMap.OpenConnectionForKey<T>
                                (shardingKey, connectionStr, ConnectionOptions.Validate);
            return conn;
        }    

## <a name="main-points"></a>Hovedpunkter.
* En ny parametre erstatter Standardkonstruktøren i DbContext underklassen 
* Den nye parametre tager de argumenter, der kræves til data afhængige routing gennem elastiske database klient dokumentbibliotek:
    * shard kortet for at få adgang til de data-afhængige routing-grænseflader
    * tasten sharding til at identificere shardlet
    * en forbindelsesstreng med legitimationsoplysninger for data-afhængige routing forbindelsen til shard. 
 
* Opkaldet til basisklassekonstruktør tager en detour til en statisk metode, som udfører alle trinnene nødvendige til distribution af data-afhængige. 
   * OpenConnectionForKey kald af elastiske database klient grænseflader bruges på kortet shard til at oprette en åben forbindelse.
   * Shard kortet opretter den åbne forbindelse til den shard, der indeholder shardlet for den angivne sharding-nøgle.
   * Åbn forbindelsen sendes tilbage til basisklassekonstruktør af DbContext indikerer, at denne forbindelse er der skal bruges i ELEMENTÆRFILEN i stedet for at lade ELEMENTÆRFILEN automatisk oprette en ny forbindelse. Denne måde forbindelsen er blevet mærket ved elastiske databasen klienten API, så den kan garantere konsistens under shard kort management handlinger.
 
  
Brug den nye for din DbContext underklasse i stedet for Standardkonstruktøren i din kode. Her er et eksempel: 

    // Create and save a new blog.

    Console.Write("Enter a name for a new blog: "); 
    var name = Console.ReadLine(); 

    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
    { 
        var blog = new Blog { Name = name }; 
        db.Blogs.Add(blog); 
        db.SaveChanges(); 

        // Display all Blogs for tenant 1 
        var query = from b in db.Blogs 
                    orderby b.Name 
                    select b; 
     … 
    }

Den nye parametre åbnes forbindelsen til den shard, der indeholder dataene til shardlet identificeret med værdien af **tenantid1**. Koden i blokken **ved hjælp af** forbliver uændret at få adgang til **DbSet** til blogs bruge ELEMENTÆRFILEN på shard for **tenantid1**. Dette ændrer semantik for koden i den ved hjælp af blokere, så alle databasefunktioner nu er fastsat til den én shard, hvor **tenantid1** der bevares. For eksempel returneres en LINQ over blogge **DbSet** kun blogge, der er gemt på den aktuelle shard, men ikke på dem, der er gemt på andre shards.  

#### <a name="transient-faults-handling"></a>Midlertidige fejl håndtering af
Microsoft Patterns & fremgangsmåder teamet publiceret [Feltet midlertidige fejl håndtering af programmet Bloker](https://msdn.microsoft.com/library/dn440719.aspx). Biblioteket bruges sammen med elastiske skala klientbibliotek i kombination med ELEMENTÆRFILEN. Sørge for, at de midlertidige undtagelser returnerer til et sted, hvor vi kan sikre dig, den nye parametre bruges efter en midlertidig fejl, så alle nye forbindelsesforsøg foretages ved hjælp af de konstruktører, vi har tweaked. Ellers er ingen garanti for en forbindelse til den korrekte shard, og der er ingen sikkerhed i forbindelse med forbindelsen bevares, når der indtræffer ændringer til kortet, shard. 

Følgende kodeeksempel på viser, hvordan du kan bruge en SQL forsøg politik rundt om de nye **DbContext** underklasse konstruktører: 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
    { 
        using (var db = new ElasticScaleContext<int>( 
                                sharding.ShardMap,  
                                tenantId1,  
                                connStrBldr.ConnectionString)) 
            { 
                    var blog = new Blog { Name = name }; 
                    db.Blogs.Add(blog); 
                    db.SaveChanges(); 
            … 
            } 
        }); 

**SqlDatabaseUtils.SqlRetryPolicy** i ovenstående kode er defineret som en **SqlDatabaseTransientErrorDetectionStrategy** med et antal forsøg af 10 og 5 sekunder ventetiden mellem nye forsøg. Denne metode ligner vejledning til ELEMENTÆRFILEN og brugerinitieret transaktioner (se [begrænsninger ved prøver igen udførelse af strategier (og derefter EF6)](http://msdn.microsoft.com/data/dn307226). Begge situationer kræver, at programmet styrer det område, som den midlertidige undtagelse returnerer: enten genåbne posteringen eller Genopret forbindelse fra stort parametre (som vist), der bruger biblioteket elastiske database klient.

Brug af til at styre, hvor midlertidige undtagelser tage os tilbage i omfang udelukker også brugen af den indbyggede **SqlAzureExecutionStrategy** , der følger med ELEMENTÆRFILEN. **SqlAzureExecutionStrategy** ville åbner en forbindelse, men ikke bruger **OpenConnectionForKey** og derfor undgå alle valideringer, der udføres som en del af **OpenConnectionForKey** opkaldet. I stedet bruger kode udsnittet den indbyggede **DefaultExecutionStrategy** , som også følger med ELEMENTÆRFILEN. I modsætning til **SqlAzureExecutionStrategy**virker den korrekt sammen med politikken forsøg igen fra midlertidige håndtering af fejl. Udførelse af politik er indstillet i klassen **ElasticScaleDbConfiguration** . Bemærk, at vi besluttet ikke at bruge **DefaultSqlExecutionStrategy** , da det foreslår for at bruge **SqlAzureExecutionStrategy** , hvis midlertidige undtagelser forekomme – der kan medføre forkert funktionsmåde, som det er gennemgået. Du kan finde flere oplysninger om de forskellige forsøg politikker og ELEMENTÆRFILEN, [Forbindelse fleksibilitet i ELEMENTÆRFILEN](http://msdn.microsoft.com/data/dn456835.aspx).     

#### <a name="constructor-rewrites"></a>De parametre
Kode eksemplerne ovenfor viser de standard parametre igen skriver kræves for dit program for at bruge data, der er afhængige distribution af med den enhed, som. Den følgende tabel generalizes denne metode til andre konstruktører. 


Aktuelle parametre  | Ændret parametre til data | Grundlæggende parametre | Noter
---------- | ----------- | ------------|----------
MyContext() |ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, boolesk) |Forbindelsen skal være en funktion af shard kortet og data-afhængige routing nøgle. Du har brug for at bypass automatisk forbindelse oprettelse af ELEMENTÆRFILEN og i stedet bruge shard kortet til broker forbindelsen. 
MyContext(string)|ElasticScaleContext (ShardMap, TKey) |DbContext (DbConnection, boolesk) |Forbindelsen er en funktion af shard kortet og tasten data-afhængige routing. En fast database navn eller forbindelse streng ikke virker som de bypass validering af shard kortet. 
MyContext(DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection-, DbCompiledModel, boolesk) |Forbindelsen vil få oprettet for den angivne shard kort og sharding nøgle med den model. Kompileret modellen vil blive overført til de grundlæggende c'tor.
MyContext (DbConnection, boolesk) |ElasticScaleContext (ShardMap, TKey, boolesk) |DbContext (DbConnection, boolesk) |Forbindelsen skal være udledes fra shard kortet og tasten. Det kan ikke være angivet som input (medmindre dette input blev allerede bruger shard kortet og tasten). Booleske vil blive overført. 
MyContext (streng, DbCompiledModel) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection-, DbCompiledModel, boolesk) |Forbindelsen skal være udledes fra shard kortet og tasten. Det kan ikke være angivet som input (medmindre dette input blev ved hjælp af shard kortet og tasten). Kompileret modellen vil blive overført. 
MyContext (ObjectContext, boolesk) |ElasticScaleContext (ShardMap TKey, ObjectContext, boolesk) |DbContext (ObjectContext boolesk) |Den nye parametre behov at sikre, at alle forbindelser i ObjectContext overføres som input igen distribueret til en forbindelse, der administreres af elastiske skala. En detaljeret beskrivelse af ObjectContexts ligger uden for omfanget af dette dokument.
MyContext (DbConnection-, DbCompiledModel, boolesk) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, boolesk)| DbContext (DbConnection-, DbCompiledModel, boolesk) |Forbindelsen skal være udledes fra shard kortet og tasten. Forbindelsen kan ikke gives som input (medmindre dette input blev allerede bruger shard kortet og tasten). Model og boolesk sendes til basisklassekonstruktør. 

## <a name="shard-schema-deployment-through-ef-migrations"></a>Shard skema implementering via ELEMENTÆRFILEN overførsler 

En service, der leveres af den enhed, som er automatisk skema administration. I forbindelse med programmer, der bruger elastiske Databaseværktøjer, ønsker vi at bevare denne egenskab til at klargøre skemaet til nyoprettede shards automatisk, når der føjes databaser til programmet delt. Primære use case er at øge kapacitet data lag til delt programmer ved hjælp af ELEMENTÆRFILEN. Database administration indsats reducerer stole på ELEMENTÆRFILENS funktioner for administration af skema med et delt program, der er bygget på ELEMENTÆRFILEN. 

Skema implementering via ELEMENTÆRFILEN overførsler fungerer bedst på **uåbnet forbindelser**. Dette i modsætning til dette scenario til data, der er afhængige rute, der er baseret på den åbne forbindelse, der er angivet af elastiske databasen klienten API. En anden forskel er konsistens kravet: mens du ønske at sikre konsistens for alle data-afhængige routing forbindelser til at beskytte mod samtidige shard kort strengmanipulation, det er ikke er et problem med indledende skema installation til en ny database, der indeholder endnu ikke er registreret i shard kortet, og endnu ikke er tildelt til at indeholde shardlets. Vi kan derfor stole på almindelig databaseforbindelser til denne scenarier, i modsætning til distribution af data-afhængige.  

Dette medfører en metode hvor skema implementering via ELEMENTÆRFILEN overførsler er tæt forbundet til registrering af den nye database som en shard i programmets shard kort. Dette er afhængig af de følgende forudsætninger: 

* Databasen er allerede blevet oprettet. 
* Databasen er tom – den indeholder ingen bruger skemaet og ingen brugerdata.
* Databasen kan endnu ikke åbnes via elastiske database klienten API'er til data-afhængige routing. 

Med disse forudsætninger, der er på plads, kan vi oprette en almindelig ophævelse åbnet **SqlConnection** til at starte ELEMENTÆRFILEN overførsler for skema installation. Følgende kodeeksempel på viser denne metode. 

        // Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
        // and kick off EF intialization of the database to deploy schema 

        public void RegisterNewShard(string server, string database, string connStr, int key) 
        { 

            Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

            SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
            connStrBldr.DataSource = server; 
            connStrBldr.InitialCatalog = database; 

            // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
            // This requires an un-opened connection. 
            using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
            { 
                // Run a query to engage EF migrations 
                (from b in db.Blogs 
                    select b).Count(); 
            } 

            // Register the mapping of the tenant to the shard in the shard map. 
            // After this step, data-dependent routing on the shard map can be used 

            this.ShardMap.CreatePointMapping(key, shard); 
        } 
 

I dette eksempel vises metoden **RegisterNewShard** , der registrerer shard i shard kortet, installerer skemaet gennem ELEMENTÆRFILEN overførsler og gemmer en tilknytning af en sharding nøgle til shard. Det er baseret på en parametre for den **DbContext** klasse (**ElasticScaleContext** i stikprøven), som tager en SQL-forbindelsesstreng som input. Koden for denne parametre er simple, som vist i følgende eksempel: 


        // C'tor to deploy schema and migrations to a new shard 
        protected internal ElasticScaleContext(string connectionString) 
            : base(SetInitializerForConnection(connectionString)) 
        { 
        } 

        // Only static methods are allowed in calls into base class c'tors 
        private static string SetInitializerForConnection(string connnectionString) 
        { 
            // We want existence checks so that the schema can get deployed 
            Database.SetInitializer<ElasticScaleContext<T>>( 
        new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

            return connnectionString; 
        } 
 
En muligvis har brugt versionen af parametre nedarvet fra den grundlæggende klasse. Men koden skal sikre, at standard initialisering for ELEMENTÆRFILEN anvendes, når der oprettes forbindelse. Kort detour derfor i den statiske metode, inden du ringer til basisklassekonstruktør med forbindelsesstrengen. Bemærk, at registrering af shards skal køre i en anden app domæne eller en proces til at sikre, at indstillingerne for initialisering for ELEMENTÆRFILEN ikke er i konflikt. 


## <a name="limitations"></a>Begrænsninger 

De fremgangsmåder, der er beskrevet i dette dokument medfører et par begrænsninger: 

* ELEMENTÆRFILEN programmer, der bruger **LocalDb** først til at overføre til en almindelig SQL Server-database før du bruger elastiske databasen klientbibliotek. Skalering af et program gennem sharding med elastiske skala er ikke muligt med **LocalDb**. Bemærk, at udvikling kan stadig bruge **LocalDb**. 

* Ændringer til programmet, som indikerer databasen skemaændringer skal gennemgå ELEMENTÆRFILEN overførsler på alle shards. Eksempelkoden for dette dokument viser ikke, hvordan du gør dette. Overvej at bruge Update-Database med en ConnectionString parameter til forbedrer over alle shards; eller udtrække T-SQL-script til ventende overførslen ved hjælp af Update-Database med – scriptet alternativ og anvende T-SQL-scriptet til din shards.  

* Hvis du har en anmodning, antages det, at alle dens databasebehandling er indeholdt i en enkelt shard, som identificeret med tasten sharding leveres af anmodningen. Men denne antagelse ikke altid indeholder SAND. For eksempel når det er ikke muligt at frigøre en sharding nøgle. Du kan løse dette ved indeholder biblioteket klient klassen **MultiShardQuery** , der implementerer fremstilling en forbindelse til at forespørge over flere shards. Lære at bruge **MultiShardQuery** sammen med ELEMENTÆRFILEN er ikke medtaget i dette dokument

## <a name="conclusion"></a>Konklusion

ELEMENTÆRFILEN programmer kan bruge elastiske database klient bibliotekets egenskab til data afhængige routing ved opstod konstruktører af **DbContext** underklasserne bruges i programmet ELEMENTÆRFILEN gennem de trin, der er beskrevet i dette dokument. Denne funktion begrænser de ændringer, der kræves til de steder, hvor **DbContext** klasser allerede findes. Desuden ELEMENTÆRFILEN programmer kan fortsætte med at drage fordel af automatiske skema installation ved at kombinere de trin, der aktiverer de nødvendige ELEMENTÆRFILEN overførsler med registreringen af nye shards og tilknytninger i shard kortet. 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png
 