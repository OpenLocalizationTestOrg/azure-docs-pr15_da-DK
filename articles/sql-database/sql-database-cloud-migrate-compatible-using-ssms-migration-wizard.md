<properties
   pageTitle="Overføre SQL Server-database til SQL-Database ved hjælp af installere Database til Microsoft Azure-Database guiden | Microsoft Azure"
   description="Microsoft Azure SQL-Database database overførslen guiden til Microsoft Azure-Database"
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

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Overføre SQL Server-database til SQL-Database ved hjælp af installere Database til guiden Microsoft Azure-Database


> [AZURE.SELECTOR]
- [Guiden SSMS overflytning](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Eksportere til BACPAC fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer fra BACPAC fil](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transaktions gentagelse](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Installere databasen til Microsoft Azure-Database guiden i SQL Server Management Studio overfører en [kompatible SQL Server-database](sql-database-cloud-migrate.md) direkte i din Azure SQL-databaseserver.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Brug Implementer databasen til guiden Microsoft Azure-Database

> [AZURE.NOTE] Følgende trin forudsætter, at du har en [klargjort SQL-databaseserver](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Kontrollér, at du har den nyeste version af SQL Server Management Studio. Nye versioner af Management Studio opdateres månedlige skal være synkroniseret med opdateringer til Azure-portalen.

    > [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Åbn Management Studio og oprette forbindelse til SQL Server-databasen overføres i Object Explorer.
3. Højreklik på databasen i Object Explorer, peg på **opgaver**og klikke på **Installere Database til Microsoft Azure SQL-Database...**

    ![Implementere på Azure fra menuen opgaver](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  Klik på **Næste**i installationsguiden, og klik derefter på **Opret forbindelse** for at konfigurere forbindelsen til din SQL-databaseserver.

    ![Implementere på Azure fra menuen opgaver](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Angiv din forbindelsesoplysninger til at oprette forbindelse til din SQL-databaseserver, i sektionen Forbind til dialogboksen Server.

    ![Implementere på Azure fra menuen opgaver](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  Angiv følgende oplysninger om filen [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) , som denne guide opretter under overflytningen:

 - **Nyt databasenavn** 
 - **Udgave af Microsoft Azure SQL Database** ([webtjenesten](sql-database-service-tiers.md))
 - **Maksimumstørrelsen for database**
 - **Tjenesten mål** (ydeevneniveau)
 - **Midlertidigt filnavn**  

    ![Eksportere indstillinger](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  Fuldføre guiden. Afhængigt af størrelsen og kompleksiteten af databasen tage installation fra et par minutter til mange timer. Hvis denne guide registrerer kompatibilitetsproblemer, fejl vises skærmbilledet og fortsætter overførslen ikke. Vejledning i at løse problemer med database, kan du gå til [løse kompatibilitetsproblemer database](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.  Bruger Object Explorer, oprette forbindelse til dine overførte database i Azure SQL-databaseserver.
8.  Ved hjælp af portalen Azure, se din database og dens egenskaber.

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
