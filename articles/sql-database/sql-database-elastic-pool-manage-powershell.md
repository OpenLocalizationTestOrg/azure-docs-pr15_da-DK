<properties 
    pageTitle="Administrere en elastiske database puljen (PowerShell) | Microsoft Azure" 
    description="Lær at bruge PowerShell til at administrere en elastiske database puljen."  
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/22/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-powershell"></a>Overvåge og administrere en elastiske database puljen med PowerShell 

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Administrere en [elastiske database puljen](sql-database-elastic-pool.md) ved hjælp af PowerShell-cmdlet'er. 

Almindelige fejlkoder, se [SQL fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer](sql-database-develop-error-messages.md).

Værdier for grupper kan findes i [eDTU og lager begrænsninger](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases). 

## <a name="prerequisites"></a>Forudsætninger

* Azure PowerShell 1.0 eller nyere. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).
* Elastiske database grupper er kun tilgængelige med SQL-Database version 12-servere. Hvis du har en SQL-Database V11 server, [ved hjælp af PowerShell for at opgradere til version 12 og oprette en gruppe](sql-database-upgrade-server-portal.md) i ét trin.


## <a name="move-a-database-into-an-elastic-pool"></a>Flytte en database til en elastiske puljen

Du kan flytte en database, ind eller ud af en gruppe med [Sæt AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). 

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="change-performance-settings-of-a-pool"></a>Ændre indstillinger for ydelse af en samling

Når ydeevne befinder sig, kan du ændre indstillingerne for puljen for at imødekomme forøgelse. Brug cmdlet'en [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) . Indstille parameteren - Dtu til eDTUs per puljen. Se [eDTU og lager begrænsninger](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) for mulige værdier.  

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## <a name="get-the-status-of-pool-operations"></a>Hent status for puljen handlinger

Oprette en gruppe, kan det tage tid. Brug cmdlet'en [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) for at spore status for puljen handlinger, herunder oprettelse og opdateringer.

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## <a name="get-the-status-of-moving-an-elastic-database-into-and-out-of-a-pool"></a>Hent status for at flytte en elastiske database til og fra en gruppe

Flytte en database kan tage tid. Spore statussen Flyt ved hjælp af cmdlet'en [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) til.

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="get-resource-usage-data-for-a-pool"></a>Hente data om brug af ressource til en gruppe

Mål, som kan hentes som en procentdel af den ressource pulje grænse:   


| Metriske navn | Beskrivelse |
| :-- | :-- |
| CPU\_procent | Gennemsnittet beregne udnyttelse i procent af grænsen på puljen. |
| fysisk\_data\_læse\_procent | Middel i/o-anvendelsen i procenten baseret på tilladte puljen. |
| Log\_skrive\_procent | Gennemsnittet skrive ressourceudnyttelse i procent af grænsen på puljen. | 
| DTU\_forbrug\_procent | Gennemsnitlig eDTU udnyttelse i procent af eDTU grænsen for puljen | 
| lagerplads\_procent | Beregne gennemsnittet af lagerplads udnyttelse i procent af lagergrænse på puljen. |  
| medarbejdere\_procent | Maksimale samtidige medarbejdere (anmodninger) i procenten baseret på tilladte puljen. |  
| sessioner\_procent | Maksimale antal samtidige sessioner i procenten baseret på tilladte puljen. | 
| eDTU_limit | Aktuelle Maks elastiske puljen DTU indstilling for denne elastiske puljen inden for dette interval. |
| lagerplads\_grænse | Aktuelle Maks elastiske puljen-lagergrænse indstilling for denne elastiske puljen i megabyte inden for dette interval. |
| eDTU\_bruges | Gennemsnitlig eDTUs bruges af puljen i det pågældende interval. |
| lagerplads\_bruges | Gennemsnitlig lagerplads, der bruges af puljen i dette interval i byte |

**Målepunkter granularitet/opbevaring perioder:**

* Data returneres på 5 minutter granularitet.  
* Dataopbevaring er 35 dage.  

Denne cmdlet og API begrænser antallet af rækker, der kan hentes i et opkald til 1000 rækker (om 3 dage data på 5 minutter granularitet). Men denne kommando kan kaldes flere gange med forskellige start/slut tidsintervaller til at hente flere data 

Til at hente målepunkter:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## <a name="get-resource-usage-data-for-an-elastic-database"></a>Hente data om brug af ressource til en elastiske database

Disse API'er er den samme som den aktuelle (version 12) API'er, der bruges til at overvåge ressource anvendelsen af en enkeltstående database, undtagen følgende semantisk forskellen. 

For denne API målepunkter hentet er udtrykt som en procentdel af den per Maks eDTUs (eller tilsvarende knop for den underliggende metrikværdi som CPU, EY osv) angive for denne gruppe. For eksempel 50% anvendelsen af nogen af disse målepunkter angiver, at den bestemte Ressourceforbrug er til 50% af den per database knop grænsen for ressourcen i den overordnede gruppe. 

Til at hente målepunkter:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## <a name="add-an-alert-to-a-pool-resource"></a>Tilføje en besked til en ressource, puljen

Du kan tilføje regler for påmindelser til ressourcer til at sende besked via mail eller beskeder om strenge til [URL-adressen slutpunkter](https://msdn.microsoft.com/library/mt718036.aspx) , når ressourcen rammer en grænseværdi for anvendelsen, du har angivet. Brug cmdlet'en Tilføj AzureRmMetricAlertRule. 

> [AZURE.IMPORTANT]Ressource anvendelsen overvågning for elastiske grupper indeholder en mellemliggende mindst 20 minutter. Konfigurere beskeder på mindre end 30 minutter for elastiske grupper understøttes ikke i øjeblikket. Få påmindelser elastiske grupper med et punktum (parameter ved navn "-WindowSize" i PowerShell API) på mindre end 30 minutter muligvis ikke blive udløst. Sørg for, at alle beskeder, du definerer for elastiske grupper bruger et punktum (WindowSize) af 30 minutter eller mere.

I dette eksempel tilføjer en besked for at få besked, når en samling eDTU forbrug går over bestemt grænse.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## <a name="add-alerts-to-all-databases-in-a-pool"></a>Føje påmindelser til alle databaser i en gruppe

Du kan tilføje regler for påmindelser til alle database i en elastiske puljen til at sende besked via mail eller beskeder om strenge til [URL-adressen slutpunkter](https://msdn.microsoft.com/library/mt718036.aspx) når en ressource rammer en anvendelsen grænseværdi for oprettet af beskeden. 

> [AZURE.IMPORTANT] Ressource anvendelsen overvågning for elastiske grupper indeholder en mellemliggende mindst 20 minutter. Konfigurere beskeder på mindre end 30 minutter for elastiske grupper understøttes ikke i øjeblikket. Få påmindelser elastiske grupper med et punktum (parameter ved navn "-WindowSize" i PowerShell API) på mindre end 30 minutter muligvis ikke blive udløst. Sørg for, at alle beskeder, du definerer for elastiske grupper bruger et punktum (WindowSize) af 30 minutter eller mere.

I dette eksempel føjer en besked til hver enkelt database i en gruppe for at få besked, når denne database DTU forbrug går over bestemt grænse.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Indsamle og overvåge brugen ressourcedata på tværs af flere grupper i et abonnement

Det er besværligt til at overvåge hver elastiske puljen separat, når du har et stort antal databaser i et abonnement. I stedet kan kombineres SQL database PowerShell-cmdletter og T-SQL-forespørgsler for at indsamle data om brug af ressource fra flere grupper og deres databaser til overvågning og analyse af ressourceforbrug. Et [Eksempel på implementering](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) af sættet af powershell-scripts kan findes i GitHub SQL Server eksempler lager sammen med dokumentation på dens virkemåde og hvornår det skal bruges.

Hvis du vil bruge denne eksempel-implementering, skal du følge disse trin, der er vises nedenfor.


1. Download [scripts og dokumentation](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Rediger scriptene for dit miljø. Angiv en eller flere servere, som er hostet elastiske grupper.
3. Angiv en telemetridatabase, hvor de indsamlede målepunkter er skal gemmes. 
4. Tilpasse scriptet for at angive varigheden af udførelse af de scripts.

På et højt niveau, du scriptene gøre følgende:

*   Optælles alle servere i en given Azure abonnement (eller en bestemt liste over servere).
*   Kører et baggrundsjob for hver server. Jobbet kører i en løkke med jævne mellemrum og indsamler telemetridata til alle grupper i feltet server. Det derefter indlæser de indsamlede data i den angivne telemetridatabase.
*   Optælles en liste over databaser i hver gruppe til at indsamle database ressource brugerdata. Der genindlæses derefter de indsamlede data til telemetri-databasen.

Indsamlede målene i telemetridatabase kan være analysere dem for at overvåge tilstanden af elastiske grupper og databaserne i den. Scriptet installeres funktionen en foruddefineret tabel-værdi (TVF) i telemetridatabase til at aggregat omfanget af en bestemt tidsramme. For eksempel kan resultaterne af TVF bruges til at vise "øverste N elastiske grupper med maksimale eDTU anvendelsen i en bestemt tidsramme." Du kan også bruge analytisk værktøjer som Excel eller Power BI til at forespørge og analysere de indsamlede data.

## <a name="example-retrieve-resource-consumption-metrics-for-a-pool-and-its-databases"></a>Eksempel: hente ressource forbrug måleredskaber for en gruppe og dens databaser

I dette eksempel henter forbrug målene for en given elastiske gruppe og alle dens databaser. Indsamlede data er formateret og skrives til en .csv-fil. Filen kan gennemses med Excel.

    $subscriptionId = '<Azure subscription id>'       # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name
        
    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC
    
    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
    
    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
    
    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }
    
    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }
    
    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
    
    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## <a name="latency-of-elastic-pool-operations"></a>Ventetid på elastiske puljen handlinger

- Ændre min eDTUs per database eller Maks eDTUs per database typisk fuldfører på fem minutter eller mindre.
- Ændre eDTUs per puljen, afhænger af den samlede mængde af meget plads der bruges af alle databaser i puljen. Ændringer i gennemsnit 90 minutter eller mindre per 100 GB. Eksempelvis hvis den samlede mængde plads bruges af alle databaser i puljen er 200 GB, og den forventede ventetid til at ændre puljen eDTU per puljen er 3 timer eller mindre.

## <a name="migrate-from-v11-to-v12-servers"></a>Overføre fra V11 til version 12-servere

PowerShell-cmdlet'er er tilgængelige til at starte, stoppe eller overvåge en opgradering til version 12 Azure SQL-Database fra V11 eller en anden version pre-12-version.

- [Opgradering til version 12 SQL-Database ved hjælp af PowerShell](sql-database-upgrade-server-powershell.md)

Referencedokumentation om disse PowerShell-cmdletter, i:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Stop-cmdlet betyder, at annullere, ikke midlertidigt. Der findes ingen måde at genoptage en opgradering, end starte igen fra begyndelsen. Stop-cmdlet rydder op og Frigør alle relevante ressourcer.

## <a name="next-steps"></a>Næste trin

- [Opret elastiske job](sql-database-elastic-jobs-overview.md) Elastiske job kan du kører T-SQL-scripts i forhold til en hvilken som helst antal databaser i puljen.
- Se [Skalering ud med Azure SQL-Database](sql-database-elastic-scale-introduction.md): bruge elastiske Databaseværktøjer skala ud, skal du flytte data, forespørgsel, eller Opret transaktioner.
