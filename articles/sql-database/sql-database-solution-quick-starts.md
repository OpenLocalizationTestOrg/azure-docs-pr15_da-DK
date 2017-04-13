<properties
   pageTitle="Azure SQL Database løsning Hurtig start | Microsoft Azure"
   description="Få mere at vide om Azure SQL Database-løsninger"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-quickstart"
   ms.date="09/06/2016"
   ms.author="carlrab"/>

# <a name="explore-azure-sql-database-solution-quick-starts"></a>Udforske Azure SQL Database løsning Hurtig start

I denne artikel indeholder en oversigt over de Azure SQL Database løsning hurtig starter. Disse hurtig starter er placeret i GitHub SQL Server eksempler lager og viser brugen af SQL-Database i en komplet løsning, der er baseret på virkelige scenarier. Du kan finde enkel selvstudier med trinvis vejledning, der viser anvendelsen af en bestemt funktion i SQL-Database, [udforske Azure SQL-Database selvstudier](sql-database-explore-tutorials.md).

## <a name="try-the-wingtiptickets-demo-and-hands-on-lab"></a>Prøv WingTipTickets demo og praktiske øvelser

[Azure SQL Database WingTipTickets](https://github.com/microsoft/wingtiptickets) demo og praktiske demonstrerer en Azure SQL-Database og Azure søgebaserede eksempelprogram, der bruges til at sælge koncertbilletter.


## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools"></a>Indsamle og overvåge brugen ressourcedata på tværs af flere grupper

[Løsning Hurtig Start: elastiske puljen telemetri ved hjælp af PowerShell](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) indeholder en løsning til indsamling og overvågning SQL-Database ressourceforbrug på tværs af flere grupper i et abonnement. Det er besværligt til at overvåge hver elastiske puljen separat, når du har et stort antal databaser i et abonnement.

Du kan løse dette problem, kan du kombinere SQL Database PowerShell-cmdletter og T-SQL-forespørgsler til at indsamle data om brug af ressource fra flere grupper og deres databaser. Dette hjælper dig med at overvåge og analysere ressourceforbrug mere effektivt.

Hurtig Start indeholder et sæt af PowerShell-scripts og T-SQL-forespørgsler sammen med dokumentation på Hvad betyder det løsningen, og hvordan du implementerer den.

## <a name="get-started-with-elastic-database-in-an-saas-scenario"></a>Komme i gang med elastiske Database i et scenarie med SaaS

 [Løsning Hurtig Start: elastiske puljen brugerdefineret dashboard til SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) indeholder en løsning til en Software-som-en-løsning (SaaS) scenarie, der bruger funktionen elastiske Database i SQL-Database til at levere en økonomisk og SVG database back-end for et SaaS program.

I denne løsning, kan du gennemgå implementeringen af en web-app. Denne WebApp kan du visualisere den belastning, som er oprettet i en elastiske database af en Indlæs generator, der bruger et brugerdefineret dashboard, der supplerer Azure portalen.

Hurtig Start indeholder en Indlæs generator og overvågning WebApp sammen med dokumentation om appen gør og hvornår det skal bruges.

## <a name="create-an-azure-sql-database-by-using-code-first-development-and-the-entity-framework"></a>Oprette en Azure SQL-database ved hjælp af kode første udvikling og den enhed, som

Video og eksempel i [Kode første til en ny Database](https://msdn.microsoft.com/data/jj193542.aspx) indeholder en introduktion til kode første udvikling, der er beregnet til en ny database. Dette scenario er beregnet til en database, der ikke findes, men der oprettes ved første kode. Du kan også opretter dette scenario en tom database, som kode første tilføjer nye tabeller.

Kode først giver dig mulighed at angive din model ved hjælp af C# eller Visual Basic .NET klasser. Du kan udføre valgfri yderligere konfiguration ved hjælp af attributter for dine klasser og egenskaber, eller ved hjælp af en fluent API.

## <a name="integrate-elastic-database-tools-into-an-entity-framework-application"></a>Integrere elastiske Databaseværktøjer i et objekt Framework-program

[Elastiske Database klientbibliotek med enhed Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) eksempel viser de ændringer, du skal træffe til enhed Framework programmet integreres med [elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md). Der er fokus på skriver [shard tilknytte administration](sql-database-elastic-scale-shard-map-management.md) og [data-afhængige routing](sql-database-elastic-scale-data-dependent-routing.md) med den enhed Framework kode første tilgang.

Den [Første kode til en ny database stikprøve for ELEMENTÆRFILEN](http://msdn.microsoft.com/data/jj193542.aspx) fungerer som eksemplet kører overalt i dette eksempel. Eksempelkoden, der følger med dette dokument er en del af eksemplerne i Visual Studio-kodeeksempler elastiske Database værktøjer sæt.

## <a name="integrate-elastic-database-tools-with-row-level-security"></a>Integrere elastiske Databaseværktøjer med række-sikkerhed på brugerniveau

[Multiprofiler programmer med elastiske Databaseværktøjer og sikkerhed på brugerniveau række](sql-database-elastic-tools-multi-tenant-row-level-security.md) viser ændringerne, du vil foretage en enhed Framework program tilladelse til at integrere [elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md) med [sikkerhed på brugerniveau række](https://msdn.microsoft.com/library/dn765131). Dette eksempel viser, hvordan du bruger disse teknologier sammen til at opbygge et program med et meget SVG data trin, der understøtter multiprofiler shards.

Du kan gøre dette ved hjælp af ADO.NET SqlClient eller enhed Framework. Dette eksempel udvider [elastiske Database klientbibliotek med enhed Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) ved at tilføje understøttelse af multiprofiler shard databaser.
Det opretter en simpel console program til oprettelse af blogge og indlæg, med fire lejere og to multiprofiler shard databaser.

## <a name="create-online-surveys-with-the-tailspin-surveys-application"></a>Oprette online undersøgelser med programmet Hold undersøgelser

Denne [Hold undersøgelser eksempel program](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) er et multiprofiler webprogram, kaldet undersøgelser, der gør det muligt for brugerne at oprette online undersøgelser. Eksemplet adresser nogle vigtige tvivl om, hvordan du administrerer bruger-id'er i en multiprofiler applikation, inklusive tilmelding, godkendelse, autorisation og app roller.

## <a name="learn-about-the-latest-security-features-of-sql-database-with-the-contoso-clinic-demo-application"></a>Få mere at vide om de seneste sikkerhedsfunktioner i SQL-Database med programmet Contoso øvelse Demo

Dette [program Contoso øvelse Demo](https://github.com/Microsoft/azure-sql-security-sample) viser de seneste sikkerhedsfunktioner i SQL-Database.

## <a name="next-steps"></a>Næste trin

[Udforske Azure SQL-Database selvstudier](sql-database-explore-tutorials.md)
