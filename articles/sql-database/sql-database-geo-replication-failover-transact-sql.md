<properties 
    pageTitle="Starte en planlagt eller uventet failover for Azure SQL-Database med Transact-SQL | Microsoft Azure" 
    description="Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af Transact-SQL" 
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
    ms.date="08/29/2016"
    ms.author="carlrab"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Starte en planlagt eller uventet failover for Azure SQL-Database med Transact-SQL


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


I denne artikel beskrives, hvordan at starte failover til en sekundær SQL-Database ved hjælp af Transact-SQL. For at konfigurere geografisk replikering skal du se [Konfigurere geografisk gentagelse til Azure SQL-Database](sql-database-geo-replication-transact-sql.md).



Hvis du vil starte failover, skal bruge du følgende:

- Et logon, der er DBManager på primært har db_ownership af den lokale database, du vil geografisk-replikeres, og være DBManager på den partner servere, kan du konfigurere geografisk gentagelse.
- SQL Server Management Studio (SSMS)


> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).




## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Starte en planlagt failover fremme en sekundær database for at blive den nye primære

Du kan bruge sætningen **JUSTER DATABASE** rykke en sekundær database for at blive den nye primære database på en planlagt måde degradere den eksisterende primære for at blive et sekundært. Denne sætning er udført på master databasen på Azure SQL-Database logiske serveren, som den geografisk replikerede sekundær database, der bliver forfremmet er placeret. Denne funktionalitet er udviklet til planlagte failover, f.eks, under drills DR og kræver, at den primære database er tilgængelig.

Kommandoen udfører arbejdsprocessen for følgende:

1. Midlertidigt skifter gentagelse til synkron tilstand, giver alle udestående transaktioner skal trækkes til sekundært og blokering af alle nye transaktioner;

2. Skifter rollerne af de to databaser i tilknytningen af geografisk gentagelse.  

Denne fremgangsmåde garanterer, at de to databaser synkroniseres, før rollerne, der skifter og derfor undgå tab af data skal forekomme. Der er en kort periode, hvor begge databaser er ikke tilgængelig (af rækkefølgen af 0 til 25 sekunder), mens rollerne, der er skiftet. Hvis den primære database har flere sekundære databaser, omkonfigurere kommandoen automatisk de andre secondaries at oprette forbindelse til den nye primære.  Handlingen hele fører mindre end et minut at fuldføre under normale forhold. Få mere at vide under [JUSTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) og [Service niveauer](sql-database-service-tiers.md).


Følge nedenstående trin for at starte en planlagt failover.

1. Oprette forbindelse til Azure SQL Database logiske server, som en sekundær geografisk replikerede database er placeret i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at skifte den sekundære database til den primære rolle.

        ALTER DATABASE <MyDB> FAILOVER;

4. Klik på **Udfør** for at køre forespørgslen.

>[AZURE.NOTE] Det er muligt, at handlingen ikke kan gennemføre og kan blive vist utilfreds i sjældne tilfælde. I dette tilfælde kan brugeren udføre kommandoen kraft failover og acceptere tab af data.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Starte et ikke-planlagt failover fra den primære database til den sekundære database

Du kan bruge sætningen **JUSTER DATABASE** rykke en sekundær database for at blive den nye primære database på en ikke-planlagt måde at tvinge sænkningen af den eksisterende primære at blive et sekundært ad gangen, når den primære databse er ikke længere er tilgængelig. Denne sætning er udført på master databasen på Azure SQL-Database logiske serveren, som den geografisk replikerede sekundær database, der bliver forfremmet er placeret.

Denne funktionalitet er udviklet til nedbrud, når gendannelse tilgængeligheden af databasen er kritiske og kan acceptere nogle datatab. Når tvunget failover startes, bliver den primære database den angivne sekundære database med det samme, og begynder at acceptere skrivetransaktioner. Så snart den oprindelige primære database er i stand til at oprette forbindelse igen med den nye primære database, en trinvis sikkerhedskopiering er udført på den oprindelige primære database og den gamle primære database er blevet lavet til en sekundær database til den nye primære database efterfølgende, er det kun en synkronisering kopi af den nye primære.

Da punkt i tid gendanne ikke understøttes i de sekundære databaser, hvis brugeren ønsker at gendanne data, der er anvendt til den gamle primære database, der ikke havde blevet replikeres til den nye primære database, før den tvungne sekundære opstod, skal brugeren til at deltage support for at gendanne dette mistet data.

Hvis den primære database har flere sekundære databaser, omkonfigurere kommandoen automatisk de andre secondaries at oprette forbindelse til den nye primære.

Følge nedenstående trin for at starte et ikke-planlagt failover.

1. Oprette forbindelse til Azure SQL Database logiske server, som en sekundær geografisk replikerede database er placeret i Management Studio.

2. Åbn mappen databaser, Udvid mappen **Systemdatabaser** , skal du højreklikke på **master**og derefter klikke på **Ny forespørgsel**.

3. Bruge sætningen følgende **JUSTER DATABASE** til at skifte den sekundære database til den primære rolle.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Klik på **Udfør** for at køre forespørgslen.

>[AZURE.NOTE] Hvis kommandoen udstedes, når feltet både primære og sekundære er online den gamle primære bliver den nye sekundære med det samme uden synkronisering af data. Udfører transaktioner når kommandoen er udstedt nogle tab af data kan opstå, hvis primært er.



## <a name="next-steps"></a>Næste trin   

- Kontrollér godkendelseskrav til din server og database er konfigureret på den nye primære efter failover. Yderligere oplysninger finder du [SQL-Database sikkerhed efter nedbrud](sql-database-geo-replication-security-config.md).
- For at få mere at vide gendanne efter nedbrud ved hjælp af aktive geografisk replikering, herunder pre og skrive gendannelse trin og udføre en nedbrud gendannelse analysere skal du se [Nedbrud](sql-database-disaster-recovery.md)
- For et Sasha Nosov blogindlæg om aktive geografisk-gentagelse, se [Spotlys på nye geografisk replikering funktioner](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Oplysninger om at designe skyen programmer til at bruge Active geografisk replikering finder du [design skyen programmer til Forretningskontinuitet ved hjælp af geografisk gentagelse](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Du kan finde oplysninger om at bruge Active geografisk replikering med elastiske database grupper [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- En oversigt over business continurity, under [Oversigt over Business løbende](sql-database-business-continuity.md)
