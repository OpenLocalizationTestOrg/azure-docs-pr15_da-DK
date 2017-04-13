
<properties
   pageTitle="Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SQL Server Management Studio | Microsoft Azure"
   description="Microsoft Azure SQL-Database database overførslen eksportere database, skal du eksportere BACPAC filer, Eksporter Data niveau-guiden"
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
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Eksportere en SQL Server-database til en BACPAC fil ved hjælp af SQL Server Management Studio

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
Denne artikel beskrives, hvordan du kan eksportere en SQL Server-database til en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fil ved hjælp af Data niveau programmet guiden Eksporter i SQL Server Management Studio. 

1. Kontrollér, at du har den nyeste version af SQL Server Management Studio. Nye versioner af Management Studio opdateres månedlige skal være synkroniseret med opdateringer til Azure-portalen.

     > [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Åbn Management Studio og oprette forbindelse til din kildedatabasen i Object Explorer.

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Højreklik på kildedatabasen i Object Explorer, peg på **opgaver**, og klik på **Eksporter Data lag program...**

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Konfigurere Eksporter for at gemme filen BACPAC for enten en lokal diskplacering eller til en Azure blob i eksportguiden. Den eksporterede BACPAC omfatter altid det komplette databaseskema og som standard data fra alle tabeller. Brug fanen Avanceret, hvis du vil udelukke data fra nogle eller alle tabeller. Du kan for eksempel vil eksportere kun dataene for referencetabeller i stedet for fra alle tabeller.

***Vigtige*** Når du eksporterer en BACPAC til Azure blob-lager, kan du bruge standard lagerplads. Importere en BACPAC fra premium lagerplads understøttes ikke.

    ![Export settings](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)


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
