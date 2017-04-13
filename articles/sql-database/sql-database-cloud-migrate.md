<properties
   pageTitle="Overførsel af SQL Server-database til SQL-Database | Microsoft Azure"
   description="Lær, hvordan om lokal SQL Server-database overførsel til Azure SQL-Database i skyen. Brug Overførselsværktøj fra databasen til at teste kompatibilitet før overflytning af databasen."
   keywords="Webdatabase migrering, overførsel af sql server-database, overførsel Databaseværktøjer, overføre databasen, overføre sql-database"
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
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Overførsel af SQL Server-database til SQL-Database i skyen

I denne artikel kan lære du at Sådan overføres en lokal SQL Server 2005 database eller nyere til Azure SQL-Database. I denne database overførselsprocessen overføre du dit skema og dine data fra SQL Server-databasen i dit aktuelle miljø i SQL-Database. Hvis du vil lykkes, skal den eksisterende database først overfører en kompatibilitet test. Med [Version 12 SQL-Database](sql-database-v12-whats-new.md)er der nogle resterende kompatibilitetsproblemer end problem, der er relateret til handlingerne serverniveau og kryds-database. Databaser og programmer, der er afhængige af [delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md) har du brug for nogle igen engineering for at løse denne inkompatibilitet, før SQL Server-databasen kan overføres.

Hvis du vil overføre, er følgende trin du drage:

- **Test for kompatibilitet**: validere database kompatibilitet med [Version 12 SQL-Database](sql-database-v12-whats-new.md). 
- **Løse kompatibilitetsproblemer, hvis mindst ét**: Hvis godkendelsen mislykkes, skal du løse valideringsfejlene.  
- **Udfør overførslen** Når databasen er kompatibel, kan du bruge en eller flere metoder til at foretage overførslen. 

SQL Server indeholder flere metoder til at udføre hver af disse opgaver. I denne artikel indeholder en oversigt over de tilgængelige metoder for hver opgave. I følgende diagram vises trinnene og metoderne.

  ![VSSSDT overførsel diagram](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)
  
 > [AZURE.NOTE] For at overføre en SQL Server-database, herunder Microsoft Access, Sybase, MySQL Oracle og DB2 til Azure SQL-Database, se [SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/).

## <a name="database-migration-tools-test-sql-server-database-compatibility-with-sql-database"></a>Overførsel af Databaseværktøjer teste SQL Server-database kompatibilitet med SQL-Database

For at teste for SQL-Database kompatibilitetsproblemer, før du starter overførselsprocessen database, kan du bruge en af følgende fremgangsmåder:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Upgrade Advisor](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- [SQL Server Data Tools til Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): SSDT bruger de seneste kompatibilitet regler til at registrere SQL-Database version 12 inkompatibilitet. Hvis der registreres inkompatibilitet, kan du løse problemer med fundet direkte i dette værktøj. Denne metode er den anbefalede måde at teste og løse problemer med SQL-Database version 12. 
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md): SqlPackage er et kommandolinjen værktøj, der tester for kompatibilitet problemer og danner en rapport, der indeholder fundet kompatibilitetsproblemer. Hvis du bruger dette værktøj, Sørg for, at du bruger den nyeste version for at bruge de seneste kompatibilitet regler. Hvis der er fundet fejl, anbefales du skal bruge et andet værktøj til at løse alle fundet kompatibilitetsproblemer - SSDT.  
- [Feltet Eksportér Data niveau programmet guiden i SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md): Denne guide registrerer og rapporterer fejl skærmbilledet. Hvis det ikke er fundet fejl, kan du fortsætte og gennemføre overflytningen til SQL-Database. Hvis der er fundet fejl, anbefales du skal bruge et andet værktøj til at løse alle fundet kompatibilitetsproblemer - SSDT.
- [Feltet Microsoft SQL Server 2016 opgradere Advisor Preview](http://www.microsoft.com/download/details.aspx?id=48119): Dette enkeltstående værktøj, der er i Vis udskrift, registrerer og genererer en rapport med SQL-Database version 12 inkompatibilitet. Dette værktøj endnu har ikke de seneste kompatibilitet regler. Hvis der findes ingen fejl, kan du fortsætte og gennemføre overflytningen til SQL-Database. Hvis der er fundet fejl, anbefales du skal bruge et andet værktøj til at løse alle fundet kompatibilitetsproblemer - SSDT. 
- [SQL Azure overførsel guiden ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): SAMW er et codeplex-værktøj, der bruger Azure SQL Database V11 kompatibilitet regler til at registrere Azure SQL-Database version 12 inkompatibilitet. Hvis der registreres inkompatibilitet, kan være fast nogle problemer direkte i dette værktøj. Dette værktøj kan være kompatibilitetsproblemer, der ikke skal rettes. Det er første Azure SQL-Database Hjælp overførselsværktøjet tilgængelige og understøttes aktivt af SQL Server-community. Dette værktøj kan også udføre overførslen fra i selve værktøjet. 

## <a name="fix-database-migration-compatibility-issues"></a>Løse database overførsel kompatibilitetsproblemer

Hvis der registreres kompatibilitetsproblemer, skal du løse dem, før du fortsætter med SQL Server-database overførslen. Der er en lang række kompatibilitetsproblemer, der kan opstå, afhængigt af begge på versionen af SQL Server i kildedatabasen og kompleksiteten af den database, du overfører. Ældre versioner af SQL Server har flere kompatibilitetsproblemer. Brug følgende ressourcer, ud over en bestemt internetsøgning ved hjælp af din søgeprogram med valgmuligheder:

- [SQL Server-Databasefunktioner, der ikke understøttes i Azure SQL-Database](sql-database-transact-sql-information.md)
- [Udgåede Database Engine-funktionen i SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Udgåede Database Engine-funktionen i SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Udgåede Database Engine-funktionen i SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Udgåede Database Engine-funktionen i SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Udgåede Database Engine-funktionen i SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Ud over at søge på internettet og bruge Brug disse ressourcer [MSDN SQL Server communityforummer](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) eller [StackOverflow](http://stackoverflow.com/).

Benyt en af følgende database overførselsværktøjer til at løse problemer, der er fundet:

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

- Brug [SQL Server Data Tools til Visual Studio ("SSDT")](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md): Hvis du vil bruge SSDT, du importerer dine databaseskema til SQL Server Data Tools til Visual Studio "SSDT") og opbygge projektet til en SQL-Database version 12-installation. Derefter kan du løse alle fundet kompatibilitetsproblemer i SSDT. Når du er færdig, synkronisere du ændringerne tilbage til kildedatabasen (eller en kopi i kildedatabasen. SSDT er i øjeblikket den anbefalede metode til at teste og løse problemer med SQL-Database version 12. Følg linket for et [Gennemgå ved hjælp af SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md).
- Brug af [SQL Server Management Studio ("SSMS")](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md): Hvis du vil bruge SSMS, skal du udføre Transact-SQL-kommandoer til at rette en fejl, der er registreret bruger et andet værktøj. Denne metode er primært til erfarne brugere at redigere databaseskemaet direkte i kildedatabasen. 
- Du kan bruge [SQL Azure overførsel guide ("SAMW")](sql-database-cloud-migrate-fix-compatibility-issues.md): Hvis du vil bruge SAMW, du opretter et Transact-SQL-script fra kildedatabasen. Guiden transformerer scriptet, når det er muligt at gøre skemaet kompatibel med SQL-Database version 12. Når du er færdig SAMW kan oprette forbindelse til SQL-Database version 12 til udførelse af script. Dette værktøj analyserer også sporingsfiler for at finde ud af kompatibilitetsproblemer. Scriptet kan oprettes med skema kun eller kan indeholde data i BCP format.

## <a name="migrate-a-compatible-sql-server-database-to-sql-database"></a>Overføre en kompatible SQL Server-database til SQL-Database

Hvis du vil overføre en kompatible SQL Server-database, indeholder Microsoft flere metoder til overførsel for forskellige scenarier. Din tolerance afhænger af den metode, du vælger for nedetid, størrelsen og kompleksiteten af SQL Server-databasen, og din forbindelse til Microsoft Azure skyen.  

> [AZURE.SELECTOR]
- [Guiden SSMS overflytning](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Eksportere til BACPAC fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer fra BACPAC fil](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transaktions gentagelse](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

For at vælge din overførselsmetode, er det første spørgsmål til at spørge, hvis du har råd til at tage database af fremstilling under overførslen. Overføre en database, mens aktive transaktioner virkelighed kan resultere i databasen uoverensstemmelser og mulige databasefejl. Der findes mange metoder til at suspendere en database, i at deaktivere klientforbindelse til at oprette et [øjebliksbillede af databasen](https://msdn.microsoft.com/library/ms175876.aspx).

Hvis du vil overføre med minimale nedetid, du brug [SQL Server transaktion replikering](sql-database-cloud-migrate-compatible-using-transactional-replication.md) Hvis databasen opfylder kravene til transaktions gentagelse. Hvis du har råd nogle nedetid, eller du foretager en testoverførsel af en fremstilling database senere migrering, kan du overveje at en af følgende tre metoder:

- [Guiden SSMS overflytning](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md): For små til mellemstore databaser, overføre en kompatible SQL Server 2005 database eller nyere er så enkelt som kører [Installere Database til guiden til Microsoft Azure-Database](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) i SQL Server Management Studio.
- [Eksportere til BACPAC fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) , og klik derefter på [Importer fra fil BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md): Hvis du har forbindelse udfordringer (ingen forbindelse, lav båndbredde eller timeout problemer) og til mellemstore og store databaser, du bruger en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil. Med denne metode kan eksportere du SQL Server-skemaet og data til en BACPAC fil. Du kan derefter importere filen BACPAC til SQL-Database ved hjælp af Data niveau programmet guiden Eksporter i SQL Server Management Studio eller værktøjet [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandoprompt.
- Brug af BACPAC og BCP sammen: Brug en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil og [BCP](https://msdn.microsoft.com/library/ms162802.aspx) for meget større databaser til at opnå større parallelization for øge ydeevnen, selvom med større kompleksitet. Overføre skemaet og dataene separat med denne metode.
 - [Eksportere skemaet kun til en BACPAC fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md).
 - [Importér skemaet kun fra filen BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) i SQL-Database.
 - Brug [BCP](https://msdn.microsoft.com/library/ms162802.aspx) til at uddrage data til flade filer og derefter [parallelle belastning](https://technet.microsoft.com/library/dd425070.aspx) filerne til Azure SQL-Database.

     ![SQL Server database overførsel – overføre SQL-database til skyen.](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

## <a name="next-steps"></a>Næste trin

- [Microsoft SQL Server 2016 Upgrade Advisor Preview](http://www.microsoft.com/download/details.aspx?id=48119)
- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

##<a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
[Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
