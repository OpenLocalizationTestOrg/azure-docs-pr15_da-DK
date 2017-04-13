<properties
   pageTitle="Brug SQL Server Management Studio til at finde ud af SQL-Database kompatibilitet før overførsel til Azure SQL-Database | Microsoft Azure"
   description="Microsoft Azure SQL Database, overflytning af databasen, SQL-Database kompatibilitet, eksportere Data niveau-guiden"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Brug SQL Server Management Studio til at finde ud af SQL-Database kompatibilitet før overførsel til Azure SQL-Database

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Upgrade Advisor](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
I denne artikel, du lære at fastlægge, om en SQL Server-database er kompatibel til at overføre til SQL-Database ved hjælp af Data niveau programmet guiden Eksporter i SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Brug af SQL Server Management Studio

1. Kontrollér, at du har den nyeste version af SQL Server Management Studio. Nye versioner af Management Studio opdateres månedlige skal være synkroniseret med opdateringer til Azure-portalen.

     > [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Åbn Management Studio og oprette forbindelse til din kildedatabasen i Object Explorer.
3. Højreklik på kildedatabasen i Object Explorer, peg på **opgaver**, og klik på **Eksporter Data lag program...**

    ![Eksportere et data lag program fra menuen opgaver](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Klik på **Næste**i eksportguiden, og derefter konfigurere Eksportér for at gemme filen BACPAC til enten en lokal diskplacering eller en Azure blob under fanen **Indstillinger** . En BACPAC fil er gemt, hvis du har ingen database kompatibilitetsproblemer. Hvis der er kompatibilitetsproblemer, er de vises på konsollen.

    ![Eksportere indstillinger](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Klik på **fanen Avanceret** for at springe over eksport af data, og fjern markeringen i afkrydsningsfeltet **Markér alt** . Vores mål er på nuværende tidspunkt kun at teste for kompatibilitet.

    ![Eksportere indstillinger](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Klik på **Næste** , og klik derefter på **Udfør**. Database kompatibilitetsproblemer, vises Hvis der overhovedet skal, når guiden har valideret i skemaet.

    ![Eksportere indstillinger](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Hvis ingen fejl vises, skal din database er kompatibel, og du er klar til at overføre. Hvis du har fejl, skal du løse dem. Klik på **fejl** for **validerer skema**for at få vist fejlene. 
    ![Eksportere indstillinger](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  Hvis den *. BACPAC fil er blevet oprettet, og derefter databasen er kompatibel med SQL-Database, og du er klar til at overføre.

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Løse database overførsel kompatibilitetsproblemer](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Overføre en kompatible SQL Server-database til SQL-Database](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
