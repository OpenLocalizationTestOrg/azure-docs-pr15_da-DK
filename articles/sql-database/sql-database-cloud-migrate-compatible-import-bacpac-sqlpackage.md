<properties
   pageTitle="Importere til SQL-Database fra en BACPAC fil ved hjælp af SqlPackage"
   description="Microsoft Azure SQL-Database database overførslen importeres database, skal du importere BACPAC-fil, sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importere til SQL-Database fra en BACPAC fil ved hjælp af SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure-portalen](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Denne artikel beskrives, hvordan du importerer til SQL-database fra en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil ved hjælp af værktøjet [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandolinjen. Dette værktøj leveres med de nyeste versioner af [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) og [SQL Server Data Tools til Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller du kan hente den nyeste version af [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direkte fra Microsoft downloadcenter.


> [AZURE.NOTE] Følgende trin forudsætter, at du har allerede klargjort en SQL-databaseserver, have ved hånden, forbindelsesoplysningerne og har kontrolleret, kildedatabasen er kompatibel.

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importere fra en BACPAC til Azure SQL-Database ved hjælp af SqlPackage

Brug følgende trin til at bruge værktøjet [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) kommandolinjen til at importere en kompatible SQL Server-database (eller Azure SQL-database) fra en BACPAC fil.

> [AZURE.NOTE] Følgende trin forudsætter, at du har allerede klargjort en Azure SQL-databaseserver og have ved hånden, forbindelsesoplysningerne.

1. Åbn en kommandoprompt og ændre en mappe, der indeholder værktøjet sqlpackage.exe kommandolinjen – dette værktøj leveres med både Visual Studio og SQL Server.
2. Udfør følgende kommando for sqlpackage.exe med for dit miljø følgende argumenter:

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| Argument  | Beskrivelse  |
  	|---|---|
  	| < servernavn >  | Target servernavn  |
  	| < databasenavn >  | Target databasenavn  |
  	| < brugernavn >  | brugernavn i den destinationsadresse-server |
  	| < adgangskode >  | brugerens adgangskode  |
  	| < source_file >  | filnavnet og placering for filen BACPAC, der skal importeres  |

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
