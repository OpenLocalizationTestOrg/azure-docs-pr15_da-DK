<properties
   pageTitle="Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SqlPackage | Microsoft Azure"
   description="Microsoft Azure SQL-Database database overførslen eksportere database, skal du eksportere BACPAC fil, sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

I denne artikel viser, hvordan du eksportere en SQL Server-database til en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil ved hjælp af værktøjet [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandolinjen. Dette værktøj leveres med de nyeste versioner af [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) og [SQL Server Data Tools til Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller du kan hente den nyeste version af [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direkte fra Microsoft downloadcenter.

1. Åbn en kommandoprompt og ændre en mappe, der indeholder værktøjet sqlpackage.exe kommandolinjen – dette værktøj leveres med både Visual Studio og SQL Server. Bruge funktionen Søg på din computer til at finde stien i dit miljø.
2. Udfør følgende kommando for sqlpackage.exe med for dit miljø følgende argumenter:

    ' sqlpackage.exe /Action:Export /ssn: < servernavn > /sdn: < databasenavn > /tf: < target_file >

  	| Argument  | Beskrivelse  |
  	|---|---|
  	| < servernavn >  | Kildenavn server  |
  	| < databasenavn >  | Kildenavn database  |
  	| < target_file >  | filnavnet og placeringen for BACPAC fil  |

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importere en BACPAC til Azure SQL-Database ved hjælp af SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importere en BACPAC til Azure SQL Database SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importere en BACPAC Azure SQL Database Azure-portalen](sql-database-import.md)
- [Importere en BACPAC til Azure SQL Database PowerShell](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
