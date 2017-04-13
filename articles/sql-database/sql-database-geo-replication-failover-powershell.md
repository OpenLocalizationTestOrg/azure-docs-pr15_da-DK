<properties 
    pageTitle="Starte en planlagt eller uventet failover for Azure SQL-Database med PowerShell | Microsoft Azure" 
    description="Starte en planlagt eller uventet failover for Azure SQL-Database ved hjælp af PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Starte en planlagt eller uventet failover for Azure SQL-Database med PowerShell



> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


I denne artikel beskrives, hvordan at starte en planlagt eller uventet failover for SQL-Database med PowerShell. For at konfigurere geografisk gentagelse skal du se [Konfigurere geografisk gentagelse til Azure SQL-Database](sql-database-geo-replication-powershell.md).



## <a name="initiate-a-planned-failover"></a>Starte en planlagt failover

Brug cmdlet'en **Set-AzureRmSqlDatabaseSecondary** med parameteren **- Failover** rykke en sekundær database for at blive den nye primære database, degradere den eksisterende primære for at blive et sekundært. Denne funktionalitet er udviklet til en planlagt failover, f.eks, under nedbrud gendannelse øvelser, og kræver, at den primære database er tilgængelig.

Kommandoen udfører arbejdsprocessen for følgende:

1. Skifte midlertidigt gentagelse til synkron tilstand. Dette medfører alle udestående transaktioner skal trækkes til sekundært.

2. Skifte roller af de to databaser i tilknytningen af geografisk gentagelse.  

Denne fremgangsmåde garanterer, at de to databaser synkroniseres, før rollerne, der skifter og derfor undgå tab af data skal forekomme. Der er en kort periode, hvor begge databaser er ikke tilgængelig (af rækkefølgen af 0 til 25 sekunder), mens rollerne, der er skiftet. Handlingen hele fører mindre end et minut at fuldføre under normale forhold. Du kan finde yderligere oplysninger finder [Sæt AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Denne cmdlet returnerer når processen med at skifte den sekundære database til primære er fuldført.

Følgende kommando skifter rollerne af databasen med navnet "mydb" på server "srv2" under den ressource gruppe "rg2" til primære. Det oprindelige primære som "db2" tidligere tilsluttet skifter til sekundær efter de to databaser er synkroniseret fuldt ud.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] Det er muligt, at handlingen ikke kan gennemføre og svarer muligvis ikke i sjældne tilfælde. I dette tilfælde kan brugeren kalde kommandoen kraft failover (ikke-planlagt failover) og acceptere tab af data.


## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Starte et ikke-planlagt failover fra den primære database til den sekundære database


Du kan bruge cmdlet'en **Set-AzureRmSqlDatabaseSecondary** til med **– Failover** og **-AllowDataLoss** parametre rykke en sekundær database for at blive den nye primære database på en ikke-planlagt måde at tvinge sænkningen af den eksisterende primære at blive et sekundært ad gangen, når den primære database er ikke længere er tilgængelig.

Denne funktionalitet er udviklet til nedbrud, når gendannelse tilgængeligheden af databasen er kritiske og kan acceptere nogle datatab. Når tvunget failover startes, bliver den primære database den angivne sekundære database med det samme, og begynder at acceptere skrivetransaktioner. Så snart den oprindelige primære database er i stand til at oprette forbindelse igen med den nye primære database efter handlingen tvunget failover, en trinvis sikkerhedskopiering er udført på den oprindelige primære database og den gamle primære database er blevet lavet til en sekundær database til den nye primære database efterfølgende, er det blot en kopi af den nye primære.

Men fordi punkt i tid gendanne ikke understøttes på sekundær databaser, hvis du vil gendannelsesdata, der er anvendt til den gamle primære database, som ikke var det er blevet replikeres til den nye primære database, skal du deltage CSS til at gendanne en database til kendte log sikkerhedskopien.

> [AZURE.NOTE] Hvis kommandoen udstedes, når feltet både primære og sekundære er online den gamle primære bliver den nye sekundære med det samme uden synkronisering af data. Udfører transaktioner når kommandoen er udstedt nogle tab af data kan opstå, hvis primært er.


Hvis den primære database har flere secondaries lykkes kommandoen delvist. Den sekundære, hvor kommandoen blev udført bliver primære. Den gamle primære dog forbliver primære, det vil sige to primærfarverne der ender i ustabil tilstand og forbundet via et afbrudt gentagelse link. Brugeren har manuelt reparere denne konfiguration ved hjælp af en "Fjern sekundær" API på en af disse primære databaser.


Følgende kommando skifter rollerne af databasen med navnet "mydb" til primær, når primært er tilgængeligt. Den oprindelige primære som "mydb" tidligere tilsluttet skiftes der til sekundær, når den er igen er online. På dette tidspunkt kan synkroniseringen medføre tab af data.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## <a name="next-steps"></a>Næste trin   

- Kontrollér godkendelseskrav til din server og database er konfigureret på den nye primære efter failover. Yderligere oplysninger finder du [SQL-Database sikkerhed efter nedbrud](sql-database-geo-replication-security-config.md).
- For at få mere at vide gendanne efter nedbrud ved hjælp af aktive geografisk replikering, herunder pre og skrive gendannelse trin og udføre en nedbrud gendannelse analysere skal du se [Nedbrud gendannelse Drills](sql-database-disaster-recovery.md)
- For et Sasha Nosov blogindlæg om aktive geografisk-gentagelse, se [Spotlys på nye geografisk replikering funktioner](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- Oplysninger om at designe skyen programmer til at bruge Active geografisk replikering finder du [design skyen programmer til Forretningskontinuitet ved hjælp af geografisk gentagelse](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Du kan finde oplysninger om at bruge Active geografisk replikering med elastiske database grupper [elastiske puljen nedbrud gendannelse strategier](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- En oversigt over business continurity, under [Oversigt over Business løbende](sql-database-business-continuity.md)
