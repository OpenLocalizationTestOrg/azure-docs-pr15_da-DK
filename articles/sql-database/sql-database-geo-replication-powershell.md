<properties 
    pageTitle="Konfigurere aktive geografisk-gentagelse til Azure SQL-Database ved hjælp af PowerShell | Microsoft Azure" 
    description="Konfigurere aktive geografisk-gentagelse til Azure SQL-Database ved hjælp af PowerShell" 
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
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Konfigurere geografisk gentagelse til Azure SQL-Database med PowerShell

> [AZURE.SELECTOR]
- [Oversigt](sql-database-geo-replication-overview.md)
- [Azure-portalen](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

I denne artikel beskrives, hvordan du konfigurerer aktive geografisk replikering til SQL-Database med PowerShell.

For at starte failover ved hjælp af PowerShell, skal du se [starte en planlagt eller uventet sekundær server til Azure SQL-Database med PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Aktive geografisk gentagelse (læsbare secondaries) er nu tilgængelig for alle databaser i alle niveauer i tjenesten. I April 2017 typen ikke kan læses sekundær skal udgå og eksisterende ikke kan læses databaser vil automatisk blive opgraderet til læsbare secondaries.



Konfigurere aktive geografisk replikering ved hjælp af PowerShell, du skal bruge følgende:

- Et Azure-abonnement. 
- En Azure SQL database - den primære database, du vil gentage.
- Azure PowerShell 1.0 eller nyere. Du kan hente og installere Azure PowerShell modulerne med følgende [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Konfigurere dine legitimationsoplysninger og vælge dit abonnement

Først skal du oprette adgang til kontoen Azure så starte PowerShell og derefter køre følgende cmdlet. Angiv den samme mail og adgangskode, du bruger til at logge på portalen Azure i logonskærmen.


    Login-AzureRmAccount

Efter du har logget korrekt vises nogle oplysninger på skærmen, der indeholder det Id, du er logget på med og de Azure abonnementer, du har adgang til.


### <a name="select-your-azure-subscription"></a>Vælg abonnementet Azure

For at markere abonnementet skal du dit abonnement Id. Du kan kopiere abonnementets Id fra de oplysninger, der vises fra det forrige trin, eller hvis du har flere abonnementer og har brug for flere oplysninger kan du køre følgende cmdlet **Get-AzureRmSubscription** og kopiere den ønskede abonnementsoplysninger fra resultatsættet. Følgende cmdlet bruger abonnementets Id til at angive det aktuelle abonnement:

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Når du har kørt **Vælg AzureRmSubscription** vender du tilbage til PowerShell-prompt.


## <a name="add-secondary-database"></a>Tilføje sekundær database


Følgende trin opretter en ny sekundær database i et geografisk gentagelse partnerskab.  
  
Du skal være ejer af abonnement eller medejer for at aktivere et sekundært. 

Du kan bruge cmdlet'en **Ny AzureRmSqlDatabaseSecondary** til at tilføje en sekundær database på en partner server til en lokal database på serveren, du er tilsluttet (den primære database). 

Denne cmdlet erstatter **Start AzureSqlDatabaseCopy** med parameteren **– IsContinuous** .  Det vil sende en **AzureRmSqlDatabaseSecondary** -objekt, der kan bruges af andre cmdletter tydeligt, et bestemt gentagelse link. Denne cmdlet returnerer når sekundær databasen er oprettet og fuldt ud. Afhængigt af størrelsen på databasen kan det tage fra minutter til timer.

Den replikerede database på den sekundære server, har samme navn som databasen på den primære server og, som standard har det samme niveau i tjenesten. Sekundær databasen kan være læsbare eller ikke kan læses, og det kan være en enkelt database eller en elastiske database. Få mere at vide under [Ny AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) og [Service niveauer](sql-database-service-tiers.md).
Når sekundært er oprettet og ud, begynder data replikering fra den primære database til den nye sekundære database. Følgende fremgangsmåde beskriver, hvordan du udfører denne opgave ved hjælp af PowerShell til at oprette ikke kan læses og læsevenligt secondaries, enten med en enkelt database eller en elastiske database.

Hvis der findes allerede partner-database (for eksempel - som resultat afslutter en tidligere geografisk gentagelse relation) mislykkes kommandoen.



### <a name="add-a-non-readable-secondary-single-database"></a>Føje en ikke-læsbar sekundær (enkelt database)

Følgende kommando opretter en ikke-læsbar sekundær af database "mydb" af server "srv2" i ressource gruppe "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Tilføje læsbare sekundær (enkelt database)

Følgende kommando opretter et læsevenligt sekundært af database "mydb" af server "srv2" i ressource gruppe "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Føje en ikke-læsbar sekundær (elastiske database)

Følgende kommando opretter en ikke-læsbar sekundær af database "mydb" i puljen elastiske database med navnet "ElasticPool1" af server "srv2" i ressource gruppe "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Tilføje et læsevenligt sekundært (elastiske database)

Følgende kommando opretter et læsevenligt sekundært af database "mydb" i puljen elastiske database med navnet "ElasticPool1" af server "srv2" i ressource gruppe "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Fjerne sekundære database

Brug cmdlet'en **Fjern AzureRmSqlDatabaseSecondary** til at afbryde permanent gentagelse forholdet mellem en sekundær database og dens primær. Når relation bliver den sekundære database en skrivebeskyttet database. Hvis forbindelsen til sekundær database er brudt kommandoen blev fuldført, men sekundært bliver skrivebeskyttet, når forbindelsen er genoprettet. Få mere at vide under [Fjern AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) og [Service niveauer](sql-database-service-tiers.md).

Denne cmdlet erstatter Stop AzureSqlDatabaseCopy for gentagelse. 

Denne fjernelse er svarer til et tvunget opsigelse før, der fjerner linket gentagelse og bevarer den tidligere sekundære som en enkeltstående database, der ikke replikeres fuldt ud før afslutning. Alle Kæd data, der ryddes op på både tidligere primær og sekundær, tidligere Hvis, eller når de er tilgængelige. Denne cmdlet returnerer når linket gentagelse fjernes. 


Hvis du vil fjerne sekundære, har brugere skriveadgang til både primære og sekundære databaser ifølge RBAC. Se rollebaseret adgangskontrol for detaljer.

Følgende fjernes gentagelse sammenkædning af database med navnet "mydb" til server "srv2" af ressource gruppe "rg2". 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Konfiguration af skærme geografisk gentagelse og tilstand

Overvågning opgaver omfatter overvågning af geografisk replikering konfigurationen og overvåger data replikering tilstand.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) kan bruges til at hente oplysninger om videresende gentagelse linkene ses i visningen sys.geo_replication_links katalog.

Følgende kommando henter status for gentagelse sammenkædningen mellem den primære database "mydb" og sekundært på server "srv2" af ressource gruppe "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om aktive geografisk-gentagelse, se - [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md)
- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier

