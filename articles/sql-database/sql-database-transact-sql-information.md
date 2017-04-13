<properties
   pageTitle="Ikke understøttes i Azure SQL Database T-SQL | Microsoft Azure"
   description="Transact-SQL-sætninger, der understøttes fuldt mindre end i Azure SQL-Database"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/30/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="azure-sql-database-transact-sql-differences"></a>Azure SQL Database Transact-SQL-forskelle


De fleste Transact-SQL-funktioner, der afhænger af programmer understøttes i både Microsoft SQL Server og Azure SQL-Database. En delvis liste over understøttede funktioner til programmer følger:

- Datatyper.
- Operatorer.
- Streng, aritmetiske, logiske, og markøren funktioner.

Azure SQL-Database er dog designet til at isolere funktioner fra en hvilken som helst afhængighed af **master** databasen. Mange serverniveau aktiviteter er upassende for SQL-Database, og der ikke understøttes. Funktioner, som er udeladt i SQL Server understøttes ikke Generelt i SQL-Database.

> [AZURE.NOTE]
> I dette emne beskrives de funktioner, der er tilgængelige med SQL-Database, når du har opgraderet til den aktuelle version SQL-Database version 12. Finde flere oplysninger om version 12 [SQL-Database version 12 hvad ny](sql-database-v12-whats-new.md).

I følgende afsnit opstilles funktioner, der er delvist understøttet, og de funktioner, der er helt ikke-understøttede.


## <a name="features-partially-supported-in-sql-database-v12"></a>Funktioner, der er delvist understøttet i SQL-Database version 12

SQL-Database version 12 understøtter nogle, men ikke alle de argumenter, der findes i de tilsvarende SQL Server 2016 Transact-SQL-sætninger. Sætningen CREATE PROCEDURE er for eksempel tilgængelig, men alle indstillingerne i CREATE PROCEDURE ikke er tilgængelige. Se sammenkædede syntaksen emner Få mere at vide om de understøttede områder af hver enkelt sætning.

- Databaser: [Opret](https://msdn.microsoft.com/library/dn268335.aspx )/[ændre](https://msdn.microsoft.com/library/ms174269.aspx)
- DMVs er alment tilgængelig for funktioner, der er tilgængelige.
- Funktioner: [Opret](https://msdn.microsoft.com/library/ms186755.aspx)/[ændre funktionen](https://msdn.microsoft.com/library/ms186967.aspx)
- [AFBRYDE](https://msdn.microsoft.com/library/ms173730.aspx) 
- Logon: [Opret](https://msdn.microsoft.com/library/ms189751.aspx)/[ændre logon](https://msdn.microsoft.com/library/ms189828.aspx)
- Lagrede procedurer: [Opret](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabeller: [Opret](https://msdn.microsoft.com/library/dn305849.aspx)/[ændre](https://msdn.microsoft.com/library/ms190273.aspx)
- Typer (brugerdefineret): [Typen Opret](https://msdn.microsoft.com/library/ms175007.aspx)
- Brugere: [Opret](https://msdn.microsoft.com/library/ms173463.aspx)/[ændre bruger](https://msdn.microsoft.com/library/ms176060.aspx)
- Visninger: [Opret](https://msdn.microsoft.com/library/ms187956.aspx)/[ændre visning](https://msdn.microsoft.com/library/ms173846.aspx)

## <a name="features-not-supported-in-sql-database"></a>Funktioner, der ikke understøttes i SQL-Database

- Sortering af systemobjekter
- Forbindelse relateret: slutpunkt sætninger, ORIGINAL_DB_NAME. SQL-Database understøtter ikke Windows-godkendelse, men understøtter lignende Azure Active Directory-godkendelse. Nogle godkendelsestyper kræver den seneste version af SSMS. Yderligere oplysninger finder du i [tilslutning til SQL-Database eller SQL Data Warehouse ved brug af Azure Active Directory-godkendelse](sql-database-aad-authentication.md).
- Cross databaseforespørgsler ved hjælp af tre eller fire del navne. (Skrivebeskyttede forespørgsler i tværs af databaser understøttes ved hjælp af [elastiske databaseforespørgsel](sql-database-elastic-query-overview.md)).
- Cross database ejerskab sammenkædning, TROVÆRDIG indstilling
- Dataindsamler
- Databasediagrammer
- Database Mail
- DATABASEPROPERTY (Brug DATABASEPROPERTYEX i stedet)
- EXECUTE AS-logon
- : Extensible håndtering af en krypteringsnøgle
- Eventing: begivenheder, begivenhed beskeder, forespørgsel beskeder
- Funktioner, der er relateret til databasen fil placering, størrelse og databasefiler, der automatisk administreres af Microsoft Azure.
- Funktioner, der er relateret til høj tilgængelighed, som administreres via din Microsoft Azure-konto: sikkerhedskopiere, gendanne, AlwaysOn, databasespejling, log levering, gendannelse funktionsmåder. Flere oplysninger, se Azure SQL Database-sikkerhedskopiering og gendannelse.
- Funktioner, der er afhængige af log læseren kører på SQL-Database: Push gentagelse, Skift dataregistrering.
- Funktioner, der er afhængige af SQL Server Agent eller forbindelse databasen: job, beskeder, operatorer, baseret på politik Management, database mail, central administration-servere.
- FILESTREAM
- Funktion: fn_get_sql, fn_virtualfilestats, fn_virtualservernodes
- Global midlertidige tabeller
- Hardware-relaterede serverindstillinger: hukommelse, arbejdstråde, CPU forbindelse, spor flag osv. Brug tjenesten niveauer i stedet.
- HAS_DBACCESS
- AFBRYDE STATISTIK JOB
- Sammenkædede servere, OPENQUERY, OPENROWSET, OPENDATASOURCE, flere indsætte og firedelt navne
- Master/target servere
- .NET framework [CLR-integration med SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Ressource regulatoren
- Semantisk søgning
- Server-legitimationsoplysninger. Brug database fastsat legitimationsoplysninger i stedet.
- Bryde niveau elementer: Server roller, IS_SRVROLEMEMBER, sys.login_token. Servertilladelser på brugerniveau er ikke tilgængelige, selvom nogle erstattes med tilladelser på elementniveau database. Nogle serverniveau DMVs er ikke tilgængelige, selvom nogle erstattes af database-niveau DMVs.
- Ikke-serverbaseret express: localdb, Brugerforekomster
- Tjenesten broker
- ANGIV REMOTE_PROC_TRANSACTIONS
- LUKNING
- sp_addmessage
- indstillinger for sp_configure og OMKONFIGURERE. Nogle indstillinger er tilgængelige med [Ændre DATABASE fastsat konfiguration](https://msdn.microsoft.com/library/mt629158.aspx).
- sp_helpuser
- sp_migrate_user_to_contained
- Overvågning af SQL Server. Bruge SQL-Database overvågning i stedet.
- SQL Server Profiler
- SQL Server sporing
- Spore flag. Nogle sporing flag elementer er blevet flyttet til kompatibilitet funktionsmåder.
- Transact-SQL fejlfinding
- Udløsere: Server-fastsat eller logon udløsere
- Brug sætningen: Hvis du vil ændre databasekonteksten til en anden database, skal du oprette en ny forbindelse til den nye database.


## <a name="full-transact-sql-reference"></a>Fuld Transact-SQL-reference

Du kan finde flere oplysninger om Transact-SQL-grammatik, brugen og eksempler, [Transact-SQL-Reference (databaseprogram)](https://msdn.microsoft.com/library/bb510741.aspx) i SQL Server bøger Online. 

### <a name="about-the-applies-to-tags"></a>Om "Gælder for"-mærker

Transact-SQL-reference medtager emner, der relaterer til SQL Server-versioner 2008 for Præsenter. Under titlen på emnet der er et ikon liggende søjle-, liste de fire SQL Server-platforme og angiver anvendelse. For eksempel blev tilgængelighed grupper introduceret i SQL Server 2012. [Opret AVAILABILTY gruppe](https://msdn.microsoft.com/library/ff878399.aspx) emne angiver, at sætningen gælder for ** SQL Server (begyndende med 2012). Sætningen gælder ikke for SQL Server 2008, SQL Server 2008 R2, Azure SQL-Database, Azure SQL Data Warehouse eller Parallel Data Warehouse.

Generelle emnet for et emne kan bruges i et produkt i nogle tilfælde, men der er mindre forskelle mellem produkter. Forskellen er anført på midtpunkter i emnet efter behov.

