<properties 
    pageTitle="Brug af elastiske database klientbibliotek med Dapper | Microsoft Azure" 
    description="Brug af elastiske database klientbibliotek med Dapper." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng"/>

# <a name="using-elastic-database-client-library-with-dapper"></a>Brug af elastiske database klientbibliotek med Dapper 

Dette dokument er for udviklere, der er afhængige af Dapper til at oprette programmer, men også vil få dobbelt [elastiske database værktøjer](sql-database-elastic-scale-introduction.md) for at oprette programmer, der implementerer sharding for at skala fra deres data niveau.  Dette dokument illustrerer ændringerne i Dapper-baserede programmer, der er nødvendige for at integrere med elastiske Databaseværktøjer. Vores fokus routing på skriver elastiske database shard administration og data, der er afhængige med Dapper. 

**Eksempel på kode**: [elastiske Databaseværktøjer til Azure SQL Database - Dapper integration](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-e19fc77f).
 
Det er nemt at integrere **Dapper** og **DapperExtensions** med biblioteket elastiske database-klienten til Azure SQL-Database. Dine programmer kan bruge data, der er afhængige routing ved at ændre oprettelse og åbning af nye [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekter til at bruge [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) opkaldet fra [klientbibliotek](http://msdn.microsoft.com/library/azure/dn765902.aspx). Denne funktion begrænser ændringer i dit program til kun, hvor nye forbindelser oprettes og åbnes. 

## <a name="dapper-overview"></a>Dapper oversigt
**Dapper** er en OR mapper. Det er tilknyttet .NET objekter fra dit program til en relationel database (og omvendt). Den første del af eksempelkoden viser, hvordan du kan integrere biblioteket elastiske database klient med Dapper-baserede programmer. Den anden del af eksempelkoden viser, hvordan du integrerer, når du bruger både Dapper og DapperExtensions.  

Funktionen mapper i Dapper indeholder metoder til udvidelse database-forbindelse, og som forenkler indsendelse T-SQL-sætningerne for udførelse af eller forespørge på databasen. Eksempelvis gør Dapper det nemt at tilknytte mellem din .NET-objekter og parametrene for SQL-sætningerne for **Udfør** opkald eller for at bruge resultatet af dine SQL-forespørgsler i .NET objekter ved hjælp af **forespørgsel** opkald fra Dapper. 

Når du bruger DapperExtensions, skal du ikke længere giver SQL-sætningerne. Filtypenavne metoder som **GetList** eller **indsætte** over databaseforbindelsen oprette SQL-sætningerne i baggrunden.
 
En anden fordel ved Dapper samt DapperExtensions er, at programmet styrer oprettelse af forbindelsen til databasen. Dette hjælper med at interagere med biblioteket elastiske database-klienten som mæglere Webdatabase forbindelser, der er baseret på tilknytning af shardlets databaser.

For at få de Dapper assemblies skal du se [Dapper prik net](http://www.nuget.org/packages/Dapper/). Du kan finde de Dapper udvidelser [DapperExtensions](http://www.nuget.org/packages/DapperExtensions).

## <a name="a-quick-look-at-the-elastic-database-client-library"></a>Et hurtigt blik på biblioteket elastiske database klient

Med elastiske database klient-bibliotek, du definerer partitioner af programdataene kaldet *shardlets* , knytte dem til databaser og identifikation af *sharding taster*. Du kan have så mange databaser, når du har brug for, og distribuere din shardlets på tværs af disse databaser. Tilknytning af sharding nøgleværdier til databaserne, der er gemt af et shard kort, der leveres af bibliotekets API'er. Denne egenskab kaldes **shard tilknytte administration**. Shard kortet fungerer også som broker af databaseforbindelser til anmodninger, der indeholder en sharding nøgle. Denne egenskab kaldes **afhængige distribution af data**.

![Shard kort og afhængige distribution af data][1]

Shard kort manager beskytter brugere fra inkonsekvent visninger til shardlet data, der kan opstå, når der sker samtidige shardlet management handlinger på databaserne. Hvis du vil gøre det, broker shard kort databaseforbindelser til et program, der er oprettet med biblioteket. Når shard management handlinger påvirke shardlet, kan dette shard kort funktioner for at afbryde automatisk en databaseforbindelse. 

I stedet for ved hjælp af den traditionelle måde at oprette forbindelser til Dapper, skal vi bruge [OpenConnectionForKey metode](http://msdn.microsoft.com/library/azure/dn824099.aspx). Dette sikrer, at alle validering finder sted og forbindelser administreres korrekt, når data flytter mellem shards.

### <a name="requirements-for-dapper-integration"></a>Krav til integration af Dapper

Når du arbejder med både biblioteket elastiske database klienten og de Dapper API'er, vil vi bevare følgende egenskaber:

* **Scaleout**: Vi vil tilføje eller fjerne databaser fra data niveauet af delt programmet efter behov for kapacitet kravene af programmet. 

-    **Konsistens**: da vores program skaleres ved hjælp af sharding, vi skal udføre afhængige distribution af data. Vi vil bruge Data afhængige routing funktionerne i biblioteket til at gøre dette. Især vi vil bevare valideringen og konsistens garanterer leveres af forbindelser, der er formidlet via shard kort manager for at undgå beskadigelse eller forkert forespørgselsresultaterne. Dette sikrer, at forbindelser til en given shardlet er afvist eller ikke længere Hvis (for eksempel) på shardlet aktuelt flyttes til en anden shard ved hjælp af delt/Flet API'er.

-    **Objekt tilknytning**: Vi vil bevare nemmere i tilknytningen leveres af Dapper til at oversætte mellem klasser i programmet på computeren og underliggende databasestrukturer. 

Følgende afsnit indeholder en vejledning til disse krav til programmer, der er baseret på **Dapper** og **DapperExtensions**.

## <a name="technical-guidance"></a>Teknisk vejledning
### <a name="data-dependent-routing-with-dapper"></a>Data, der er afhængige rute med Dapper 

Med Dapper er programmet typisk ansvarlig for at oprette og åbne forbindelser til den underliggende database. Givet en type T af programmet, returnerer Dapper forespørgselsresultater som .NET samlinger af typen T. Dapper udfører tilknytningen fra T-SQL resultatrækker til objekter af typen T. På samme måde, knytter Dapper .NET objekter i SQL værdier eller parametre for sætninger data strengmanipulation language (DML). Dapper tilbyder denne funktion via filtypenavn metoder på almindelig [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objektet fra ADO .NET SQL Client biblioteker. SQL-forbindelsen, der returneres af de elastiske skala API'er til DDR er også almindelig [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekter. Dette giver os mulighed at bruge direkte Dapper filtypenavne over den type, der returneres af biblioteket klient DDR API, som det er også en simpel SQL Client forbindelse.

Bemærkningerne gør det nemt at bruge forbindelser formidlet ved biblioteket elastiske database klient til Dapper.

Denne kodeeksempel (fra eksemplet ledsagende) viser den fremgangsmåde, hvor tasten sharding leveres af programmet til biblioteket til broker forbindelsen til den rigtige shard.   

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                     key: tenantId1, 
                     connectionString: connStrBldr.ConnectionString, 
                     options: ConnectionOptions.Validate))
    {
        var blog = new Blog { Name = name };
        sqlconn.Execute(@"
                      INSERT INTO
                            Blog (Name)
                            VALUES (@name)", new { name = blog.Name }
                        );
    }

Opkaldet til [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) API erstatter standard oprettelse og åbning af en SQL Client forbindelse. [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) opkaldet tager de argumenter, der kræves til afhængige distribution af data: 

-    Shard kortet for at få adgang til data afhængige routing grænseflader
-    Tasten sharding til at identificere shardlet
-    Legitimationsoplysninger (brugernavn og adgangskode) for at oprette forbindelse til shard

Objektet shard kort opretter forbindelse til den shard, der indeholder shardlet for den angivne sharding nøgle. Elastiske database klienten API'er også mærke forbindelsen til implementere dens konsistens garantier. Da opkaldet til [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) returnerer et almindelige SQL Client connection-objekt, følger den efterfølgende opkald til metoden **Execute** lokalnummer fra Dapper standard Dapper øvelsen.

Forespørgsler fungerer stort set på samme måde – du først åbne forbindelsen ved hjælp af [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) fra klienten API. Derefter skal du bruge de almindelige Dapper lokalnummer metoder for at tilknytte resultaterne af dine SQL-forespørgsel i .NET objekter:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId1, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate ))
    {    
           // Display all Blogs for tenant 1
           IEnumerable<Blog> result = sqlconn.Query<Blog>(@"
                                SELECT * 
                                FROM Blog
                                ORDER BY Name");

           Console.WriteLine("All blogs for tenant id {0}:", tenantId1);
           foreach (var item in result)
           {
                Console.WriteLine(item.Name);
            }
    }

Bemærk, at den **ved hjælp af** blok med DDR forbindelsen områder alle databasefunktioner i blokken til den én shard, hvor tenantId1 der bevares. Forespørgslen returnerer kun blogge, der er gemt på den aktuelle shard, men ikke på dem, der er gemt på andre shards. 

## <a name="data-dependent-routing-with-dapper-and-dapperextensions"></a>Data, der er afhængige routing med Dapper og DapperExtensions

Dapper leveres med et netværk af flere filtyper, der kan giver yderligere nemmere og fremstilling fra databasen, når databasen udviklingsprogrammer. DapperExtensions er et eksempel. 

Brug af DapperExtensions i dit program ændres ikke, hvordan databaseforbindelser oprettes og administreres. Det er stadig ansvaret for at åbne forbindelser, og der forventes almindelige SQL Client forbindelsesobjekter ved hjælp af lokalnummer metoder. Vi kan stole på [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) , som beskrevet ovenfor. Som i følgende kodeeksempler vises, er den eneste ændring, som vi ikke længere har til at skrive T-SQL-sætningerne:

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           var blog = new Blog { Name = name2 };
           sqlconn.Insert(blog);
    }

Og her er en kode prøve til forespørgslen: 

    using (SqlConnection sqlconn = shardingLayer.ShardMap.OpenConnectionForKey(
                    key: tenantId2, 
                    connectionString: connStrBldr.ConnectionString, 
                    options: ConnectionOptions.Validate))
    {
           // Display all Blogs for tenant 2
           IEnumerable<Blog> result = sqlconn.GetList<Blog>();
           Console.WriteLine("All blogs for tenant id {0}:", tenantId2);
           foreach (var item in result)
           {
               Console.WriteLine(item.Name);
           }
    }

### <a name="handling-transient-faults"></a>Håndtering af midlertidige fejl

Microsoft Patterns & fremgangsmåder teamet publiceret [Midlertidige fejl håndtering af programmet Bloker](http://msdn.microsoft.com/library/hh680934.aspx) for at hjælpe udviklere afhjælpe almindelige midlertidige fejl betingelser, der er stødt på, når der benyttes i skyen. Kan finde flere oplysninger under [Perseverance, hemmeligt af alle Triumphs: Brug af midlertidige fejl håndtering af programmet blokering](http://msdn.microsoft.com/library/dn440719.aspx).

Kodeeksemplet er afhængig af biblioteket midlertidige fejl for at beskytte mod midlertidige fejl. 

    SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
    {
       using (SqlConnection sqlconn = 
          shardingLayer.ShardMap.OpenConnectionForKey(tenantId2, connStrBldr.ConnectionString, ConnectionOptions.Validate))
          {
              var blog = new Blog { Name = name2 };
              sqlconn.Insert(blog);
          }
    });

**SqlDatabaseUtils.SqlRetryPolicy** i ovenstående kode er defineret som en **SqlDatabaseTransientErrorDetectionStrategy** med et antal forsøg af 10 og 5 sekunder ventetiden mellem nye forsøg. Hvis du bruger transaktioner, skal du kontrollere, at din forsøg omfang går tilbage til starten af posteringen tale om en midlertidig fejl.

## <a name="limitations"></a>Begrænsninger

De fremgangsmåder, der er beskrevet i dette dokument medfører et par begrænsninger:

* Eksempelkoden for dette dokument viser ikke, hvordan du administrerer skema på tværs af shards.
* Hvis du har en anmodning, antager vi, at alle dens databasebehandling er indeholdt i en enkelt shard, som identificeret med tasten sharding leveres af anmodningen. Dog holder antager dette ikke altid, for eksempel, når det ikke er muligt at gøre en sharding nøgle tilgængelig. Du kan løse dette ved indeholder biblioteket elastiske database klient [MultiShardQuery klasse](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardexception.aspx). Klassen implementerer fremstilling en forbindelse til at forespørge over flere shards. Brug af MultiShardQuery sammen med Dapper er ikke medtaget i dette dokument.

## <a name="conclusion"></a>Konklusion

Programmer, der bruger Dapper og DapperExtensions kan nemt drage fordel af elastiske Databaseværktøjer til Azure SQL-Database. Gennem de trin, der er beskrevet i dette dokument, kan disse programmer Brug værktøjets egenskab til data, der er afhængige routing ved at ændre oprettelse og åbning af nye [SqlConnection](http://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) objekter for at bruge [OpenConnectionForKey](http://msdn.microsoft.com/library/azure/dn807226.aspx) kald af biblioteket elastiske database klient. Denne funktion begrænser de programmet ændringer, der kræves til de steder, hvor nye forbindelser oprettes og åbnes. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-working-with-dapper/dapperimage1.png
 