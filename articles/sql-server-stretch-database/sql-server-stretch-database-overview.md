<properties
    pageTitle="Strække Database oversigt | Microsoft Azure"
    description="Få mere at vide, hvordan Stræk Database overfører dataene kolde transparent og sikkert til Microsoft Azure skyen."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Strække Database oversigt

Stræk Database overfører dataene kolde transparent og sikkert til Microsoft Azure skyen.

Hvis du blot ønsker at komme i gang med Stræk Database, kan du se [komme i gang ved at køre den aktivere Database, Stræk guiden](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Hvad er fordelene ved Stræk Database?
Stræk Database indeholder følgende fordele:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Indeholder omkostninger\-effektive tilgængelighed til kolde data
Strække varm- og koldtvandssystemer transaktions data dynamisk fra SQL Server til Microsoft Azure med SQL Server-Stræk Database. I modsætning til typisk kolde datalager, dine data er altid online og tilgængelig til at forespørge. Du kan angive længere data opbevaring tidslinjer uden at bryde banken for store tabeller som kunde Ordreoversigt. Få glæde af den lave omkostninger i Azure i stedet for skalering dyr på\-anlæg lagerplads. Du vælger det priser niveau og konfigurere indstillinger i Azure portalen for at bevare kontrollen over omkostninger. Skalere op eller ned efter behov. Gå til [SQL Server Stræk Database priser](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) siden status for oplysninger.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Kræver ikke ændringer i forespørgsler eller -programmer
Få adgang til dine SQL Server-data, der problemfrit uanset om det er\-anlæg eller den strækkes til skyen.  Du indstiller politikken, som bestemmer, hvor data er gemt, og SQL Server håndterer flytning af data i baggrunden. Hele tabellen er altid online og forespørgselsmulig. Og, Stræk Database kræver ikke ændringer af eksisterende forespørgsler eller programmer – placeringen af data er helt gennemsigtig til programmet.

### <a name="streamlines-on-premises-data-maintenance"></a>Strømlines på\-anlæg vedligeholdelse af data
Reducere på\-anlæg vedligeholdelse og lager til dine data. Sikkerhedskopier for din på\-lokale data kører hurtigere og fuldføres inden for vinduet reparation. Sikkerhedskopier til skyen del af dine data kører automatisk. Din på\-lokale lagerbehov betydeligt reduceres. Azure-lager kan være 80% billigere end at tilføje på\-anlæg SSD.

### <a name="keeps-your-data-secure-even-during-migration"></a>Holder dine data fortrolige selv under overførsel
Få glæde ro i huske, som du strække programmerne vigtigste sikkert til skyen. SQL Server altid krypteret giver kryptering til dine data i bevægelse. Række niveau sikkerhed sikkerhed og andre avancerede SQL Server-sikkerhedsfunktioner også arbejde med Stræk Database til at beskytte dine data.

## <a name="what-does-stretch-database-do"></a>Hvad gør Stræk Database?
Når du aktiverer Stræk Database til en SQL Server-forekomst, en database og mindst én tabel, begynder Stræk Database automatisk at overføre dine kolde data til Azure.

-   Hvis du gemmer kolde data i en separat tabel, kan du overføre hele tabellen.

-   Hvis tabellen indeholder både varm- og koldtvandssystemer data, kan du angive funktionen et filter for at markere rækkerne, der skal overføres.

**Du behøver ikke at ændre eksisterende forespørgsler og klienten apps.** Du fortsat har problemfri adgang til både lokale og eksterne data, selv under dataoverførsel. Der er en lille mængde ventetid for forespørgsler i remote, men du kun opstå denne ventetid, når du opretter en forespørgsel kolde dataene.

**Stræk Database sikrer, at ingen data går tabt** , hvis der opstår fejl under overførsel. Der er også forsøg til at håndtere forbindelsesproblemer, der kan opstå under overflytningen. En dynamisk visning indeholder status for overførsel.

**Du kan stoppe dataoverførsel** til at foretage fejlfinding af problemer på den lokale server eller for at maksimere den tilgængelige netværksbåndbredde.

![Strække database oversigt][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>Er Stræk Database for dig?
Hvis du kan foretage følgende udtalelser, kan Stræk Database hjælpe til at opfylde dine behov og løse dine problemer med.

|Hvis du er en beslutning maker|Hvis du er en Databaseadministrator|
|------------------------------|-------------------|
|Jeg anvende for at bevare transaktions data for lang tid.|Størrelsen af min tabeller får af kontrolelementet.|
|Jeg har nogle gange forespørge på kolde data.|Mine brugere at, de vil have adgang til kolde data, men de kun sjældent bruger den.|
|Jeg har apps, herunder ældre apps, som jeg ikke vil opdatere.|Jeg har beholde købe og tilføje mere lagerplads.|
|Jeg vil finde en måde at spare penge på lagerplads.|Jeg kan ikke sikkerhedskopiere eller gendanne sådanne store tabeller i SERVICEAFTALEN.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Hvilken slags databaser og tabeller er kandidater til Stræk Database?
Strække Database destinationer transaktions databaser med store datamængder koldtvandssystemer, typisk er gemt i et lille antal tabeller. Disse tabeller kan indeholde mere end en milliarder rækker.

Hvis du bruger funktionen tidsmæssig tabel i SQL Server 2016, kan du bruge Stræk Database til at overføre hele eller dele af den tilknyttede oversigtstabel til omkostninger\-effektive lageret i Azure. Du kan finde flere oplysninger om [Administrere opbevaring af historiske Data i System-versionsnummer tidsmæssig tabeller](https://msdn.microsoft.com/library/mt637341.aspx).

Bruge Stræk Database Advisor, en funktion i SQL Server 2016 Upgrade Advisor, der identificerer databaser og tabeller for Stræk Database. Se [Identificer databaser og tabeller til Stræk Database](sql-server-stretch-database-identify-databases.md)for at få flere oplysninger. Hvis du vil vide mere om potentielle problemer med blokering, skal du se [begrænsninger for Stræk Database](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Teste drev Stræk Database
**Test drev Stræk Database med eksempeldatabasen AdventureWorks.** Du kan få eksempeldatabasen AdventureWorks, hente mindst databasefilen og eksempler og scripts filen fra [her](https://www.microsoft.com/download/details.aspx?id=49502). Når du gendanner eksempeldatabasen til en forekomst af SQL Server 2016, udpakke filen eksempler og åbne filen Stræk DB eksempler fra mappen Stræk DB. Køre scripts i filen for at kontrollere den meget plads der bruges af dine data, før og efter at du aktiverer Stræk Database, til at registrere status for dataoverførsel, og at bekræfte, at du kan fortsætte med at forespørge eksisterende data og indsætte nye data, både under og efter dataoverførsel.

## <a name="next-step"></a>Næste trin
**Identificere databaser og tabeller, der er kandidater til Stræk Database.** Hent SQL Server 2016 Upgrade Advisor og køre Stræk Database Advisor for at identificere databaser og tabeller, der er kandidater til Stræk Database. Stræk Database Advisor identificerer også blokering problemer. Flere oplysninger i [Identificer databaser og tabeller til Stræk Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
