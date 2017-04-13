<properties 
    pageTitle="Starte en planlagt eller uventet failover til Azure SQL-Database ved hjælp af Azure portal | Microsoft Azure" 
    description="Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af portalen Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-the-azure-portal"></a>Starte en planlagt eller uventet failover til Azure SQL-Database ved hjælp af Azure portal


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


I denne artikel beskrives, hvordan at starte failover til en sekundær SQL-Database ved hjælp af [Azure-portalen](http://portal.azure.com). For at konfigurere geografisk gentagelse skal du se [Konfigurere geografisk gentagelse til Azure SQL-Database](sql-database-geo-replication-portal.md).


## <a name="initiate-a-failover"></a>Starte en failover

Sekundær databasen, kan skiftes for at blive primært.  

1. I [Azure-portalen](http://portal.azure.com) gå til den primære database i tilknytningen af geografisk gentagelse.
2. Vælg **alle indstillinger**under bladet SQL-Database > **Geografisk gentagelse**.
3. Vælg den database, du vil blive den nye primære og klikke på **Failover**på listen **SECONDARIES** .

    ![failover][2]

4. Klik på **Ja** for at starte sekundære.

Kommandoen skifter med det samme den sekundære database til den primære rolle. 

Der er en kort periode, hvor begge databaser er ikke tilgængelig (af rækkefølgen af 0 til 25 sekunder), mens rollerne, der er skiftet. Hvis den primære database har flere sekundære databaser, omkonfigurere kommandoen automatisk de andre secondaries at oprette forbindelse til den nye primære. Handlingen hele fører mindre end et minut at fuldføre under normale forhold. 

>[AZURE.NOTE] Hvis primært er online og bekræftelse transaktioner, når kommandoen er udstedt datatab kan forekomme.


## <a name="next-steps"></a>Næste trin   

- Kontrollér godkendelseskrav til din server og database er konfigureret på den nye primære efter failover. Yderligere oplysninger finder du [SQL-Database sikkerhed efter nedbrud](sql-database-geo-replication-security-config.md).
- For at få mere at vide gendanne efter nedbrud ved hjælp af aktive geografisk replikering, herunder pre og skrive gendannelse trin og udføre en nedbrud gendannelse analysere skal du se [Genoprettelse efter genoprettelse øvelser](sql-database-disaster-recovery.md)
- For et Sasha Nosov blogindlæg om aktive geografisk-gentagelse, se [Spotlys på nye geografisk gentagelse funktioner](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Oplysninger om at designe skyen programmer til at bruge Active geografisk replikering finder du [design skyen programmer til Forretningskontinuitet ved hjælp af geografisk gentagelse](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Du kan finde oplysninger om at bruge Active geografisk replikering med elastiske database grupper [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- En oversigt over business continurity, under [Oversigt over Business løbende](sql-database-business-continuity.md)




<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png
