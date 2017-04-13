<properties
   pageTitle="Find ud af SQL-Database kompatibelt ved hjælp af SqlPackage.exe | Microsoft Azure"
   description="Microsoft Azure SQL Database, overflytning af databasen, SQL-Database kompatibilitet, SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Find ud af SQL-Database kompatibelt ved hjælp af SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Upgrade Advisor](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

I denne artikel kan lære du at finde ud af, om en SQL Server-database er kompatibel til at overføre til SQL-Database ved hjælp af værktøjet [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) kommandoprompt.

## <a name="using-sqlpackageexe"></a>Brug af SqlPackage.exe

1. Åbn en kommandoprompt og ændre en mappe, der indeholder den nyeste version af sqlpackage.exe. Dette værktøj leveres med de nyeste versioner af [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) og [SQL Server Data Tools til Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), eller du kan hente den nyeste version af [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) direkte fra Microsoft downloadcenter.
2. Udfør følgende kommando for SqlPackage med for dit miljø følgende argumenter:

    ' sqlpackage.exe /Action:Export /ssn: < servernavn > /sdn: < databasenavn > /tf: < target_file > /p:TableData = < schema_name.table_name >>< output_file > 2 > & 1'

  	| Argument  | Beskrivelse  |
  	|---|---|
  	| < servernavn >  | Kildenavn server  |
  	| < databasenavn >  | Kildenavn database  |
  	| < target_file >  | filnavnet og placeringen for BACPAC fil  |
  	| < schema_name.table_name >  | de tabeller, hvor data er output til destinationsfilen  |
  	| < output_file >  | filnavnet og placering for outputfilen med fejl, hvis en hvilken som helst  |

    Årsagen til argumentet /p:TableName er, at vi kun vil teste for database kompatibilitet til eksport til Azure SQL DB version 12 i stedet for at eksportere dataene fra alle tabeller. Argumentet Eksportér for sqlpackage.exe understøtter desværre ikke, udtrække nul tabeller. Du skal angive mindst én tabel, som en enkelt lille tabel. < Output_file > indeholder rapport over eventuelle fejl. Den "> 2 > & 1" streng pipes både standard output og standardafvigelsen som følge af kommandoer til angivne outputfil.

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Åbn outputfilen og gennemse kompatibilitet fejl, hvis en hvilken som helst. 

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Løse database overførsel kompatibilitetsproblemer](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Overføre en kompatible SQL Server-database til SQL-Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
