<properties
   pageTitle="Forretningskontinuitet i skyen - gendanne en slettet database - SQL-Database | Microsoft Azure"
   description="Få mere at vide om punkt-in-Time gendannelse, der gør det muligt at annullere en Azure SQL-Database til et tidligere punkt i gang (op til 35 dage)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Gendanne en Azure SQL-database ved hjælp af automatiseret databasesikkerhedskopier

SQL-Database har du tre muligheder for brug af [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)af databaser gendannelse. Du kan gendanne en database fra de service-definerede sikkerhedskopier i deres [opbevaringsperiode](sql-database-service-tiers.md) til:

- En ny database på samme logiske serveren gendannes til et bestemt punkt i tidspunkt inden for opbevaringsperioden. 
- En database på samme logiske serveren gendannes til sletningen tid til en slettet database.
- En ny database på en hvilken som helst logiske server i et område, gendannes til de seneste daglige sikkerhedskopier i geografisk replikerede blob-lager (RA-GRS).

Du kan også bruge [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md) til at oprette en [database kopiere](sql-database-copy.md) på nogen logiske server i et område, der er en transaktion overensstemmelse med den aktuelle SQL-Database. Du kan bruge databasekopi og [eksportere til en BACPAC](sql-database-export.md) , at arkivere en en transaktion ensartet kopi af en database til længerevarende opbevaring ud over dit opbevaringsperiode eller til at overføre en kopi af databasen til et lokalt eller Azure VM forekomst af SQL Server.

## <a name="recovery-time"></a>Gendannelse tid

Gendannelse tid til at gendanne en database ved hjælp af automatiseret databasesikkerhedskopier der påvirkes af en række faktorer: 
 - Størrelsen på databasen
 - Databasen præstationsniveau
 - Antallet af transaktionslog involveret
 - Mængden aktivitet, der skal være afspilles igen for at gendanne til gendannelsespunkt
 - Netværksbåndbredden, hvis indstillingen Gendan er til et andet område 
 - Antallet af samtidige Gendan anmodninger behandles i target område. 
 
 Indstillingen Gendan kan tage flere timer for en stor og/eller meget aktive database. Hvis der er længere afbrydelse i et område, kan det skyldes, at der bliver stort antal geografisk-Gendan anmodninger behandles af andre områder. Hvis der er et stort antal anmodninger om dette kan øge gendannelse klokkeslættet for databaser i det pågældende område. Fleste database gendanner fuldført inden for 12 timer.

 Der er ingen indbyggede funktioner for at flere Gendan. Den [Azure SQL-Database: fuldstændig Server gendannelse](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) script er et eksempel på en måde at udføre denne opgave.

> [AZURE.IMPORTANT] Hvis du vil gendanne, ved hjælp af automatiseret sikkerhedskopier, skal du være medlem af rollen bidragyder til SQL Server i abonnementet eller være ejeren af abonnementet. Du kan gendanne ved hjælp af portalen Azure, PowerShell eller REST-API. Du kan ikke bruge Transact-SQL. 

## <a name="point-in-time-restore"></a>Punkt-In-Time Gendan

Punkt-In-Time gendanne gør det muligt at gendanne en eksisterende database som en ny database til et tidligere tidspunkt på den samme logiske server ved hjælp af [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md). Du kan ikke overskrive den eksisterende database. Du kan gendanne til et tidligere tidspunkt ved hjælp af [Azure-portalen](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) eller [REST-API](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Punkt-In-Time gendannelse: Azure portal](sql-database-point-in-time-restore-portal.md)
- [Punkt-In-Time Gendan: PowerShell](sql-database-point-in-time-restore-powershell.md)

Databasen kan gendannes på alle niveauer af ydeevnen eller elastiske puljen. Du har brug at sikre, at du har en tilstrækkelige DTU kvote på logiske server eller i elastiske gruppen. Husk på, at indstillingen Gendan opretter en ny database og at service niveau og ydeevne niveauet for den gendannede database kan være anderledes end den aktuelle tilstand for live-database. Når fuldført, er den gendannede database en normal fuldt tilgængelig online database, der er faktureret til normal rente, der er baseret på dens tjeneste niveau og ydeevne niveau. Du ikke betale gebyrer, indtil database gendannelsen er fuldført.

Du generelt gendanne en database til en earler punkt til gendannelse formål. Når du gør det, kan du behandle den gendannede database som en erstatning for den oprindelige database eller bruge det til at hente data fra og derefter opdatere den oprindelige database. 

- ***Webdatabase erstatning:*** Hvis den gendannede database er beregnet som en erstatning for den oprindelige database, skal du kontrollere niveauet for ydeevne og/eller webtjenesten egner sig og tilpasse databasen, hvis det er nødvendigt. Du kan omdøbe den oprindelige database og giv derefter den gendannede database det oprindelige navn ved hjælp af kommandoen JUSTER DATABASE i T-SQL. 
- ***Genoprettelse af data:*** Hvis du planlægger at hente data fra den gendannede database til at gendanne fra en bruger eller program fejl, skal du separat til at skrive og udføre uanset data gendannelse scripts, som du har brug for til at udtrække data fra den gendannede database til den oprindelige database. Selvom gendannelsen kan tage lang tid at fuldføre, vil gendanne elementer databasen være synlige på listen database i hele. Hvis du sletter databasen under gendannelsen, det vil annullere handlingen, og du skal ikke betale for den database, der ikke blev fuldført gendannelsen. 

Du kan finde detaljerede oplysninger om brug af punkt-in-Time Gendan til at gendanne fra bruger- og programfejl, se [punkt-in-Time Gendan](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="deleted-database-restore"></a>Gendanne slettede database

Gendanne slettede database gør det muligt at gendanne en slettet database til sletningen tid til en slettet database på samme logiske serveren ved hjælp af [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md). 

> [AZURE.IMPORTANT] Hvis du sletter en Azure SQL-Database server-forekomst, slettes også alle dens databaser og kan ikke gendannes. Der er ingen understøttelse for at gendanne en slettet server på nuværende tidspunkt.

Du kan bruge den samme eller et nyt databasenavn for den gendannede database. Du kan bruge [Azure-portalen](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) eller [RESTEN (createMode = Gendan)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [AZURE.SELECTOR]
- [Slettet database Gendan: Azure-portalen](sql-database-restore-deleted-database-portal.md)
- [Slettet database Gendan: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="geo-restore"></a>Geografisk-Gendan

Geografisk-Gendan gør det muligt at gendanne en SQL-database på en hvilken som helst server i en hvilken som helst Azure område fra den seneste geografisk replikerede [automatiseret daglige sikkerhedskopiering](sql-database-automated-backups.md). Geografisk-Gendan bruger en geografisk overflødige sikkerhedskopi som kilden og kan bruges til at gendanne en database, selvom den database eller datacenter er ikke tilgængelig på grund af en afbrydelse. Du kan bruge [Azure-portalen](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md), eller den [RESTEN (createMode = gendannelse)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

> [AZURE.SELECTOR]
- [Geografisk-Gendan: Azure portal](sql-database-geo-restore-portal.md)
- [Geografisk-Gendan: PowerShell](sql-database-geo-restore-powershell.md)

Geografisk-Gendan er standardindstillingen for gendannelse, når databasen er ikke tilgængelig på grund af en hændelse i det område, hvor databasen er hostet. Hvis en stor skala hændelse i et område medfører utilgængelighed for dit databaseprogram, kan du bruge geografisk-Gendan for at gendanne en database fra den seneste sikkerhedskopiering til en server i en hvilken som helst andet område. Alle sikkerhedskopier er geografisk replikerede og kan have en forsinkelse fra, når sikkerhedskopieringen er taget og geografisk replikeres til Azure blob i et andet område. Denne forsinkelse kan være op til en time, så i tilfælde af nedbrud der kan være op til 1 time tab af data, det vil sige frigivne Produktionsordre af op til 1 time. Følgende viser gendannelse af databasen fra den sidste daglige sikkerhedskopiering.

![geografisk-Gendan](./media/sql-database-geo-restore/geo-restore-2.png)

Du kan finde detaljerede oplysninger om brug af geografisk-Gendan til at gendanne fra en afbrydelse, se [gendanne fra en afbrydelse](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Geografisk-Gendan leveres med alle niveauer af tjenesten, men det er den mest grundlæggende de nedbrud gendannelse-løsninger, der er tilgængelige i SQL-Database med længste frigivne Produktionsordre og vurdering gendannelse tid (Indsæt). For grundlæggende databaser med maksimumstørrelsen for 2 GB geografisk-Gendan er et begrundet DR-løsning med en Indsæt 12 timer. For større Standard eller Premium databaser, hvis betydeligt kortere gendannelse tider er beskedteksten, eller hvis du vil reducere risikoen for tab af data skal du overveje at bruge Active geografisk-gentagelse. Aktive geografisk replikering tilbyder en meget lavere frigivne Produktionsordre og Indsæt, som kun kræver, at du starte en failover til en løbende replikerede sekundær. Yderligere oplysninger finder du [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md).

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Udføre automatisk gendannelse ved hjælp af automatiseret sikkerhedskopier

Som beskrevet ovenfor, i addiition til portalen Azure kan databaser gendannelse udføres programmically ved hjælp af Azure PowerShell og REST-API. Nedenstående tabeller viser beskrive sæt af tilgængelige kommandoer.

### <a name="powershell"></a>PowerShell

|Cmdlet|Beskrivelse|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/en-us/library/azure/mt603648.aspx)|Henter en eller flere databaser.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/en-us/library/azure/mt693387.aspx)|Henter en slettet database, som du kan gendanne.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Henter en geografisk overflødige sikkerhedskopi af en database.|
|[Gendanne AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Gendanner en SQL-database.|
||||

### <a name="rest-api"></a>REST-API

|API|Beskrivelse|
|---|-----------|
|[RESTEN (createMode = gendannelse)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Gendanner en database|
|[Få oprette eller opdatere Databasestatus](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Returnerer status under en gendannelse|
||||



## <a name="summary"></a>Oversigt

Automatisk sikkerhedskopiering Beskyt dine databaser bruger og programfejl, utilsigtede database sletning og længerevarende afbrydelser. Denne nul omkostninger nul-administrator løsning leveres med alle SQL-databaser. 

## <a name="next-steps"></a>Næste trin

- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier
- Hvis du vil vide mere om Azure SQL-Database, der automatisk sikkerhedskopiering, se [SQL-Database automatisk sikkerhedskopiering](sql-database-automated-backups.md)
- For at få mere for at vide om indstillinger for hurtigere genoprettelse, se [Aktiv geografisk replikering](sql-database-geo-replication-overview.md)  
- Hvis du vil lære at bruge Automatiseret sikkerhedskopier til at arkivere, se [databasekopi](sql-database-copy.md)
