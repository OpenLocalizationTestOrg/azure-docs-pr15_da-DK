<properties
   pageTitle="Løse SQL Server-database kompatibilitetsproblemer før overførsel til SQL-Database | Microsoft Azure"
   description="Microsoft Azure SQL Database, overflytning af databasen, kompatibilitet, guiden SQL Azure overflytning"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Bruge SQL Azure overførsel guide til at løse SQL Server-database kompatibilitetsproblemer før overførsel til Azure SQL-Database

> [AZURE.SELECTOR]
- Brug [guiden SQL Azure overflytning](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Brug [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Brug [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

I denne artikel kan lære du at finde og rette SQL Server-database kompatibilitetsproblemer ved hjælp af guiden SQL Azure overførsel før overførsel til Azure SQL-Database.

## <a name="using-sql-azure-migration-wizard"></a>Ved hjælp af guiden SQL Azure overflytning

Brug [guiden SQL Azure overflytning](http://sqlazuremw.codeplex.com/) CodePlex til at generere et T-SQL-script fra en inkompatibel kildedatabasen. Dette script konverteres derefter af guiden for at gøre det kompatibelt med den SQL-Database. Du opretter derefter forbindelse til Azure SQL-Database til udførelse af script. Dette værktøj analyserer også sporingsfiler for at finde ud af kompatibilitetsproblemer. Scriptet kan oprettes med skema kun eller kan indeholde data i BCP format. Yderligere dokumentation, herunder en trinvis vejledning findes på CodePlex på [guiden SQL Azure overflytning](http://sqlazuremw.codeplex.com/).  

 ![SAMW overførsel diagram](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Ikke alle inkompatible skema, der er registreret ved hjælp af guiden kan rettes ved dens indbyggede transformationer. Inkompatible script, der ikke kan løses rapporteres som fejl, med kommentarer, der er tilføjet i den oprettede script. Hvis der findes mange fejl, kan du bruge enten Visual Studio eller SQL Server Management Studio til at gå gennem og løse de fejl, som ikke kunne rettes ved hjælp af guiden Overflytning i SQL Server.

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Overføre en kompatible SQL Server-database til SQL-Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
