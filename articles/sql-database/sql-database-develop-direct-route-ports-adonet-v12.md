<properties 
    pageTitle="Porte ud over 1433 for SQL-Database | Microsoft Azure"
    description="Klientforbindelser fra ADO.NET til Azure SQL-Database version 12 nogle gange tilsidesætte proxyen og interagere direkte med databasen. Porte end 1433 bliver vigtige."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="" />


<tags 
    ms.service="sql-database" 
    ms.workload="drivers"
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016"
    ms.author="annemill"/>


# <a name="ports-beyond-1433-for-adonet-45-and-sql-database-v12"></a>Porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database


Dette emne beskrives de ændringer, der bringer Azure SQL-Database version 12 forbindelse funktionsmåden for klienter, der bruger ADO.NET 4.5 eller en nyere version.


## <a name="v11-of-sql-database-port-1433"></a>V11 af SQL-Database: Port 1433


Når klientprogrammet bruger ADO.NET 4.5 til at oprette forbindelse og forespørgsler med SQL-Database V11, er den interne rækkefølge på følgende måde:


1. ADO.NET forsøger at oprette forbindelse til SQL-Database.

2. ADO.NET bruger port 1433 til at ringe et program modul, og program, der opretter forbindelse til SQL-Database.

3. SQL-Database sender dens svar tilbage til det program, der videresender ADO.NET svaret til port 1433.


**Terminologi:** Vi beskriver den foregående rækkefølge ved at sige, at ADO.NET arbejde sammen med SQL-Database ved hjælp af den *proxy omdirigere*. Hvis ingen program er involveret, kan vi sagde blev brugt *direkte distribution* .


## <a name="v12-of-sql-database-outside-vs-inside"></a>Version 12 af SQL-Database: uden for vs i


Forbindelser til version 12, skal vi spørge om klientprogrammet kører *uden for* eller *inde* i rammen Azure skyen. Underafsnit diskutere to almindelige scenarier.


#### <a name="outside-client-runs-on-your-desktop-computer"></a>*Udvendige:* Klienten kører på computeren


Port 1433 er den eneste port, der skal være åben på computeren, der er vært klientprogrammet SQL-Database.


#### <a name="inside-client-runs-on-azure"></a>*I:* Klienten kører på Azure


Når kunden kører inden for kanten Azure skyen, bruger den hvad vi kan ringe til en *direkte vej* for at interagere med SQL-databaseserver. Når der er oprettet en forbindelse, yderligere involverer interaktioner mellem klienten og databasen ingen program-proxy.


Sekvensen er som følger:


1. ADO.NET 4,5 (eller nyere) starter en kort interaktion med Azure skyen og modtager et dynamisk identificerede portnummer.
 - Dynamisk identificerede portnummeret er i området af 11000 11999 eller 14000 14999.

2. ADO.NET opretter derefter forbindelse til SQL-databaseserver direkte, med ingen program ind imellem.

3. Forespørgsler sendes direkte til databasen, og resultaterne returneres direkte til kunden.


Sørg for, at port områder af 11000 11999 og 14000-14999 på klientcomputeren Azure er venstre tilgængelig for ADO.NET 4.5 klient interaktion med SQL-Database version 12.

- Især skal porte i området være fri for udgående blokering.

- På din Azure VM styrer **Windows Firewall med Avanceret sikkerhed** portindstillinger.
 - Du kan bruge den [firewall brugergrænsefladen](http://msdn.microsoft.com/library/cc646023.aspx) til at tilføje en regel, der angives af **TCP-** protokollen sammen med et portområde med syntaksen som **11000 11999**.


## <a name="version-clarifications"></a>Version beskrivelser af en lang


Dette afsnit oplyser monikere, der refererer til produktversioner. Det viser også nogle par af versioner mellem produkter.


#### <a name="adonet"></a>ADO.NET


- ADO.NET 4.0 understøtter TDS 7.3 protokollen, men ikke 7.4.
- ADO.NET 4,5 og nyere understøtter TDS 7.4-protokollen.


#### <a name="sql-database-v11-and-v12"></a>SQL-Database V11 og version 12


Klient forbindelse forskellene mellem SQL Database V11 og version 12 er fremhævet i dette emne.


*Note:* Transact-SQL-sætningen `SELECT @@version;` returnerer en værdi, der starter med et tal som '11'. eller '12'. og disse til vores version navnene på V11 og version 12 for SQL-Database.


## <a name="related-links"></a>Relaterede links


- ADO.NET 4.6 blev udgivet juli 20 2015. En blog-meddelelse fra .NET-teamet findes [her](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


- ADO.NET 4.5 blev udgivet 15 August 2012. En blog-meddelelse fra .NET-teamet findes [her](http://blogs.msdn.com/b/dotnet/archive/2012/08/15/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code.aspx).
 - Et blogindlæg om ADO.NET 4.5.1 findes [her](http://blogs.msdn.com/b/dotnet/archive/2013/06/26/announcing-the-net-framework-4-5-1-preview.aspx).


- [TDS protocol Versionsliste](http://www.freetds.org/userguide/tdshistory.htm)


- [SQL-Database udvikling oversigt](sql-database-develop-overview.md)


- [Azure SQL-Database firewall](sql-database-firewall-configure.md)


- [Sådan: konfigurere firewall-indstillinger på SQL-Database](sql-database-configure-firewall-settings.md)

