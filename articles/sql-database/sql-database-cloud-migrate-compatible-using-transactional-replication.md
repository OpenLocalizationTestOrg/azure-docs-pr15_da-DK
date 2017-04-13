<properties
   pageTitle="Overføre til SQL-Database med transaktionsreplikering | Microsoft Azure"
   description="Microsoft Azure SQL-Database database overførslen importere databasen, transaktions gentagelse"
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
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Overføre SQL Server-database til Azure SQL-Database ved hjælp af transaktions gentagelse

> [AZURE.SELECTOR]
- [Guiden SSMS overflytning](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Eksportere til BACPAC fil](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importer fra BACPAC fil](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Transaktions gentagelse](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

I denne artikel kan lære du at overføre en kompatible SQL Server-database til Azure SQL-Database med minimale nedetid ved hjælp af SQL Server-transaktions replikering.

## <a name="understanding-the-transactional-replication-architecture"></a>Forstå transaktions gentagelse arkitektur

Når du ikke vil risikere at fjerne SQL Server-databasen fra fremstilling, mens overførslen foregår, kan du bruge SQL Server-transaktions replikering som din migreringsløsning. Hvis du vil bruge denne løsning, kan du konfigurere din Azure SQL-Database som et abonnement til den lokale SQL Server-forekomst, du vil overføre. De lokale transaktions gentagelse forhandler synkroniserer data fra den lokale database skal synkroniseres (udgiver), mens nye transaktioner fortsætte med at opstå. 

Du kan også bruge transaktions gentagelse til at overføre et undersæt af din lokale database. Den publikation, hvor du replikeres til Azure SQL-Database kan være begrænset til et undersæt af tabellerne i databasen replikeres. For hver tabel replikeres, kan du begrænse data til et undersæt af rækkerne og/eller et undersæt af kolonnerne.

Med transaktions gentagelse vises alle ændringer til dine data eller skema i din Azure SQL-Database. Når synkroniseringen er fuldført, og du er klar til at overføre, skal du ændre forbindelsesstrengen over dine programmer til at vise dem til din Azure SQL-Database. Når transaktions gentagelse løber eventuelle ændringer, der er skrevet på din lokale database, og alle dine programmer, peg på Azure DB, kan du fjerne transaktions gentagelse. Azure SQL-Database er nu systemet fremstilling.

 ![SeedCloudTR diagram](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Transaktions gentagelse krav

Transaktions replikering er en teknologi indbyggede og integreret med SQL Server siden SQL Server 6.5. Det er en fuldt udviklet og dokumenterede teknologi, at de fleste af DBAs ved, som de har oplevelse. Med [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)er det nu muligt at konfigurere din Azure SQL-Database som [transaktions gentagelse abonnement](https://msdn.microsoft.com/library/mt589530.aspx) i publikationen i det lokale miljø. Den oplevelse, som du får konfigurerer den fra Management Studio er den samme som om du har konfigureret et transaktions gentagelse abonnement på en lokal server. Understøttelse af dette scenario er understøttet, når udgiveren og forhandleren er mindst én af følgende versioner af SQL Server:

 - SQL Server 2016 og derover 
 - SQL Server 2014 SP1 CU3 og derover
 - SQL Server 2014 RTM CU10 og derover
 - SQL Server 2012 SP2 CU8 og derover
 - SQL Server 2012 SP3 og derover


> [AZURE.IMPORTANT] Brug den seneste version af SQL Server Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. Ældre versioner af SQL Server Management Studio ikke kan konfigurere SQL-Database som et abonnement. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Næste trin

- [Nyeste version af SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Nyeste version af SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Yderligere ressourcer

- [Transaktions gentagelse](https://msdn.microsoft.com/library/mt589530.aspx)
- [SQL-Database version 12](sql-database-v12-whats-new.md)
- [Transact-SQL delvist eller ikke-understøttede funktioner](sql-database-transact-sql-information.md)
- [Overføre SQL Server-databaser med SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)
