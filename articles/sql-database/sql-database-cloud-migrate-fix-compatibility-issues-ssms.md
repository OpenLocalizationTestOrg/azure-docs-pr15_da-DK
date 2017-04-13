<properties
   pageTitle="Løse SQL Server-database kompatibilitetsproblemer med SQL Server Management Studio før overførsel til SQL-Database | Microsoft Azure"
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

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Løse SQL Server-database kompatibilitetsproblemer ved hjælp af SQL Server Management Studio før overførsel til SQL-Database

> [AZURE.SELECTOR]
- Brug [guiden SQL Azure overflytning](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Brug [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Brug [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Erfarne brugere kan løse SQL Server-database kompatibilitetsproblemer med brug af SQL Server Management Studio før overførsel til Azure SQL-Database.


> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="using-sql-server-management-studio"></a>Brug af SQL Server Management Studio

Brug SQL Server Management Studio til at løse problemer med kompatibilitet ved hjælp af forskellige Transact-SQL-kommandoer, som **JUSTER DATABASE**. Denne metode er primært til erfarne brugere, der er vant til arbejder Transact-SQL på en live-database. Ellers, anbefales det, at du bruger SSDT. 



## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Overføre en kompatible SQL Server-database til SQL-Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
