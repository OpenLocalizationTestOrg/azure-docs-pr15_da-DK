<properties
    pageTitle="Opgradering til version 12 Azure SQL-Database ved hjælp af PowerShell | Microsoft Azure"
    description="Beskriver, hvordan du opgradere til Azure SQL-Database version 12 herunder hvordan du kan opgradere Web og Business databaser, og hvordan du opgradere en V11 server overføre dens databaser direkte i en elastiske database puljen, ved hjælp af PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="sstein"/>

# <a name="upgrade-to-azure-sql-database-v12-using-powershell"></a>Opgradering til version 12 Azure SQL-Database ved hjælp af PowerShell


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


SQL-Database version 12 er den nyeste version, så opgradering til SQL-Database version 12 anbefales.
SQL-Database version 12 har mange [fordele i forhold til den tidligere version](sql-database-v12-whats-new.md) , herunder:

- Øget kompatibilitet med SQL Server.
- Forbedret premium ydeevne og nye ydeevneniveauer.
- [Elastiske database grupper](sql-database-elastic-pool.md).

Denne artikel indeholder anvisninger til opgradering af eksisterende SQL-Database V11 servere og databaser til version 12 SQL-Database.

Under processen med at opgradere til version 12, opgradere du nogen Web og Business databaser til en ny webtjenesten så kørselsvejledning til opgradering af websted og Business databaser er inkluderet.

Derudover kan kan overføre til en [elastiske database puljen](sql-database-elastic-pool.md) være mere rentable end opgradering til individuel performanceniveauer (priser niveauer) for enkelt databaser. Grupper forenkle også database management, fordi du skal kun bruge til at administrere indstillingerne for puljen i stedet for at administrere separat ydeevneniveauer af individuelle databaser ydeevne. Hvis du har databaser på flere servere, kan du overveje at flytte dem til den samme server og udnytte at placere dem i en gruppe.

Du kan følge trinnene i denne artikel kan du nemt overføre databaser fra V11 servere direkte i elastiske database grupper.

Bemærk, at dine databaser skal forblive online og fortsætte med at arbejde i hele handlingen opgradering. Kan forekomme i en meget lille varighed, der typisk omkring 90 sekunder, men kan være op til 5 minutter på tidspunktet for den faktiske overgang til det nye ydeevneniveau midlertidige slippe af forbindelser til databasen. Hvis dit program har [midlertidige fejl håndtering af for forbindelse afslutninger](sql-database-connectivity-issues.md) er det tilstrækkelig til at beskytte mod mistede forbindelser i slutningen af opgraderingen.

Opgradere til SQL-Database version 12 kan ikke fortrydes. Serveren gendannes ikke til V11 efter en opgradering.

Efter opgradering til version 12, kan [service niveau anbefalinger](sql-database-service-tier-advisor.md) og [elastiske puljen anbefalinger](sql-database-elastic-pool-create-portal.md) umiddelbart ikke tilgængelige indtil tjenesten har tid til at evaluere din arbejdsbelastninger på den nye server. Oversigt over V11 server anbefaling gælder ikke for version 12-servere, så det ikke bevares.  

## <a name="prepare-to-upgrade"></a>Forberede dig på at opgradere

- **Opgradere databaser for alle Web og Business**: Brug portalen, eller brug [PowerShell til at opgradere databaser og server](sql-database-upgrade-server-powershell.md).
- **Gennemse og annullere geografisk gentagelse**: Hvis din Azure SQL-database er konfigureret til geografisk gentagelse du bør dokumentere dens aktuelle konfiguration og [stop geografisk gentagelse](sql-database-geo-replication-portal.md#remove-secondary-database). Omkonfigurere databasen til geografisk replikering, når opgraderingen er fuldført.
- **Åbn følgende porte, hvis du har klienter på en Azure VM**: Hvis klientprogrammet opretter forbindelse til SQL-Database version 12, mens din klient kører på en Azure VM (virtual machine), skal du åbne portområder 11000 11999 og 14000-14999 på VM. Yderligere oplysninger finder du [porte til version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md).


## <a name="prerequisites"></a>Forudsætninger

Hvis du vil opgradere en server til version 12 med PowerShell, skal du har den seneste Azure PowerShell installeret og kører. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Konfigurere dine legitimationsoplysninger og vælge dit abonnement

Du skal oprette adgang til din Azure-konto, hvis du vil køre PowerShell-cmdlet'er mod abonnementet Azure. Kør følgende, og du vil få vist en log på skærmen for at angive dine legitimationsoplysninger. Brug af samme mail og adgangskode, du bruger til at logge på portalen Azure.

    Add-AzureRmAccount

Efter du har logget korrekt skal du se nogle oplysninger på skærmen, der indeholder det Id, du er logget på med og de Azure abonnementer, du har adgang til.

For at markere det abonnement, du vil arbejde med du har brug for dit abonnement Id (**-SubscriptionId**) eller et andet abonnement navn (**-SubscriptionName**). Du kan kopiere det fra det forrige trin, eller hvis du har flere abonnementer kan du køre Cmdletten **Get-AzureRmSubscription** og kopiere den ønskede abonnementsoplysninger fra resultatsættet.

Med dit abonnementsoplysninger til at angive dit aktuelle abonnement, skal du køre følgende cmdlet:

    Set-AzureRmContext -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Følgende kommandoer køres mod abonnementet du lige har valgt ovenfor.

## <a name="get-recommendations"></a>Få anbefalinger

Hvis du vil have anbefaling til serveren til opgraderingen, skal du køre følgende cmdlet:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1”

Se [oprette en elastiske database puljen](sql-database-elastic-pool-create-portal.md) og [Azure SQL-Database priser niveau anbefalinger](sql-database-service-tier-advisor.md)kan finde flere oplysninger.



## <a name="start-the-upgrade"></a>Starte opgraderingen

For at starte opgraderingen af serveren køre følgende cmdlet:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Når du kører begynder denne kommando opgraderingsprocessen. Du kan tilpasse output fra anbefalingen og giver den redigerede anbefaling til denne cmdlet.


## <a name="upgrade-a-server"></a>Opgradere en server


    # Adding the account
    #
    Add-AzureRmAccount

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION'
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP'
    $ServerName = 'YOUR_SERVER'

    # Selecting the right subscription
    #
    Set-AzureRmContext -SubscriptionName $SubscriptionName

    # Getting the upgrade recommendations
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName

    # Starting the upgrade process
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## <a name="custom-upgrade-mapping"></a>Brugerdefineret opgradere tilknytning

Hvis anbefalingerne ikke passer til din server og forretningsmulighed, kan derefter du vælge hvordan dine databaser opgraderes og knytte dem til enkelte eller elastiske databaser.

ElasticPoolCollection og DatabaseCollection parametre er valgfri:

    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100
    $elasticPool.DatabaseDtuMin = 0
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”)
    $elasticPool.Name = "elasticpool_1"
    $elasticPool.StorageMb = 800

    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap1.Name = "DBMain1"
    $databaseMap1.TargetEdition = "Standard"
    $databaseMap1.TargetServiceLevelObjective = "S0"

    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties
    $databaseMap2.Name = "DBMain2"
    $databaseMap2.TargetEdition = "Standard"
    $databaseMap2.TargetServiceLevelObjective = "S2"

    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -ServerVersion 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool)



## <a name="monitor-databases-after-upgrading-to-sql-database-v12"></a>Overvåge databaser efter opgradering til SQL-Database version 12


Når du har opgraderet, anbefales det at overvåge databasen aktivt for at sikre programmer kører på den ønskede ydeevne og optimere brugen efter behov.

Derudover til overvågning individuelle databaser kan du overvåge elastiske database grupper [ved hjælp af portalen](sql-database-elastic-pool-manage-portal.md) eller med [PowerShell](sql-database-elastic-pool-manage-powershell.md)


**Forbrug ressourcedata:** For Basic, som Standard og Premium databaser er forbrug ressourcedata tilgængelig via den [sys.dm_ db_ resource_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) DMV i databasen. Denne DMV indeholder nær realtid forbrug ressourceoplysninger på 15 anden granularitet i den forrige time handling. DTU procentdel forbrug for et interval beregnes som den maksimale procentdel forbrug af CPU, EY og log målangivelserne. Her er en forespørgsel til at beregne det gennemsnitlige DTU procentdel forbrug over den seneste time:

    SELECT end_time
         , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
           ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Yderligere overvågning oplysninger:

- [Azure SQL-Database ydeevne vejledning til enkelt databaser](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Pris og ydeevne overvejelser i forbindelse med en elastiske database puljen](sql-database-elastic-pool-guidance.md).
- [Overvågning Azure SQL-Database ved hjælp af dynamiske management visninger](sql-database-monitoring-with-dmvs.md)



**Beskeder:** Konfigurere 'Beskeder' i portalen Azure til at give dig besked, når DTU forbruget for en opgraderet database nærmer sig visse højt niveau. Database påmindelser kan konfigureres i portalen Azure på forskellige ydeevne målepunkter som DTU, CPU, EY og Log. Gå til din database, og vælg **regler for påmindelser** i bladet **Indstillinger** .

For eksempel kan du konfigurere en besked via mail på "DTU procent" Hvis procentdel gennemsnitsværdien DTU overstiger 75% over de sidste fem minutter. Referere til at [modtage beskeder om meddelelser](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) for at lære mere om at konfigurere påmindelser.



## <a name="next-steps"></a>Næste trin

- [Opret en elastiske database puljen](sql-database-elastic-pool-create-portal.md) og tilføje nogle eller alle databaserne i puljen.
- [Ændre niveauet for tjenesten niveau og ydeevnen af databasen](sql-database-scale-up.md).



## <a name="related-information"></a>Relaterede oplysninger

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)
