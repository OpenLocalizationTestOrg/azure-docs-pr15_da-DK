<properties
   pageTitle="Overføre en SQL Server-database til Azure SQL-Database | Microsoft Azure"
   description="Microsoft Azure SQL Database database installerer, database overførslen Indlæs database Eksportér database, guiden Overflytning"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importere fra BACPAC til SQL-Database ved hjælp af SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure-portalen](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

I denne artikel viser, hvordan du importere fra en [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -fil til SQL-Database ved hjælp af Data niveau programmet guiden Eksporter i SQL Server Management Studio.

> [AZURE.NOTE] Følgende trin forudsætter, at du har allerede klargjort din logiske Azure SQL-forekomst, og have ved hånden, forbindelsesoplysningerne.

1. Kontrollér, at du har den nyeste version af SQL Server Management Studio. Nye versioner af Management Studio opdateres månedlige skal være synkroniseret med opdateringer til Azure-portalen.

     > [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Oprette forbindelse til din Azure SQL-databaseserver, skal du højreklikke på mappen **databaser** og klikke på **Importér Data lag program...**

    ![Importere data lag programmet menupunkt](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Importere en BACPAC-fil fra din lokale disk for at oprette databasen i Azure SQL-Database, eller Vælg Azure lagerplads konto og beholder, som du har overført filen BACPAC.

    ![Indstillinger for profilimport](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Når du importerer en BACPAC fra Azure blob-lager, kan du bruge standard lagerplads. Importere en BACPAC fra premium lagerplads understøttes ikke.

4.  Angiv **nyt databasenavn** efter databasen på Azure SQL DB, angive **Udgave af Microsoft Azure SQL Database** (webtjenesten), **Maksimum databasestørrelse**og **Service formålet** (ydeevneniveau).

    ![Databaseindstillinger](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Klik på **Næste** , og klik derefter på **Udfør** for at importere BACPAC-filen til en ny database i Azure SQL-databaseserveren.

6. Bruger Object Explorer, oprette forbindelse til dine overførte database i Azure SQL-databaseserver.

6.  Ved hjælp af portalen Azure, se din database og dens egenskaber.

## <a name="next-steps"></a>Næste trin

- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
