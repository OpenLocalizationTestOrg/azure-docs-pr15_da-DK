<properties
    pageTitle="Introduktion til elastiske database job"
    description="Sådan bruger du elastiske database job"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="ddove"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove" />

# <a name="getting-started-with-elastic-database-jobs"></a>Introduktion til elastiske Database job

Elastiske Database jobs (preview) til Azure SQL-Database kan du pålidelighed udføre T-SQL-scripts, der strækker sig over flere databaser, mens du prøver igen og give eventuel fuldførelse garantier automatisk. Du kan finde flere oplysninger om funktionen elastiske Database job, skal du se [funktionen oversigtssiden](sql-database-elastic-jobs-overview.md).

Dette emne udvider stikprøvernes findes i [Introduktion til elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md). Når fuldført, kan du: Lær at oprette og administrere job, administrere en gruppe af relaterede databaser. Det er ikke påkrævet for at bruge værktøjerne i elastiske skala for at udnytte fordelene ved elastiske job.

## <a name="prerequisites"></a>Forudsætninger

Hente og køre den [Kom i gang med elastiske Database værktøjer eksempel](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Oprette en shard kort manager ved hjælp af appen eksempel

Her kan du oprette et shard kort manager sammen med flere shards, efterfulgt af indsættelse af data i shards. Hvis du allerede har shards, der er konfigureret med delt data i dem, kan du springe følgende trin og flytte til næste afsnit.

1. Oprette og køre programmet **Kom i gang med elastiske Databaseværktøjer** eksempel. Følg trinnene, indtil trin 7 i sektionen [downloade og køre appen eksempel](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). I slutningen af trin 7, får du vist følgende kommandoprompt:

    ![kommandoprompt][1]

2.  Skriv "1" i kommandoprompten, og tryk på **Enter**. Dette opretter shard kort manager, og lægger to shards til serveren. Skriv "3" og trykke på **Enter**. Gentag denne handling fire gange. Dette indsætter eksempel datarækker i din shards.

3.  [Azure-portalen](https://portal.azure.com) skal vise tre nye databaser i din version 12-server:

    ![Visual Studio bekræftelse][2]

    På dette tidspunkt skal du oprette en brugerdefineret database af websteder, der afspejler alle databaser i shard kortet. Dette vil give os til at oprette og udføre et job, Tilføj en ny tabel på tværs af shards.

Her vil vi normalt oprette et shard kort mål ved hjælp af **Ny AzureSqlJobTarget** cmdlet. Shard kort manager-database skal være angivet som en database destination og derefter bestemte shard kortet er angivet som destination. I stedet går vi at Optæl alle databaser i serveren og føje databaserne til den nye brugerdefinerede websteder med undtagelse af master databasen.

##<a name="creates-a-custom-collection-and-adds-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Opretter en brugerdefineret samling og tilføjer alle databaser i feltet server til brugerdefineret samling destination med undtagelse af master.


    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    $dbsinserver | %{
    $currentdb = $_.DatabaseName 
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason
                
        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target." 
        }

        else
        {
            throw $_
        }
    
    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch 
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
}
    
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Oprette et T-SQL-Script til udførelse af på tværs af databaser

    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

##<a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Oprette job for at udføre et script på tværs af den brugerdefinerede gruppe af databaser

    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job


##<a name="execute-the-job"></a>Udføre jobbet 

Følgende PowerShell-script kan bruges til at udføre en eksisterende sag:

Opdater følgende variabel afspejler det ønskede jobnavn, der er udført:

    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="retrieve-the-state-of-a-single-job-execution"></a>Hente tilstanden for en enkelt job udførelse af

Brug samme **Get-AzureSqlJobExecution** cmdlet'en med parameteren **IncludeChildren** for at få vist tilstanden for underordnede job udførelser, nemlig bestemte tilstanden til udførelse af hver job mod hver database, der er rettet af jobbet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="view-the-state-across-multiple-job-executions"></a>Få vist tilstanden på tværs af flere jobstatus udførelser

Cmdletten **Get-AzureSqlJobExecution** har flere valgfrie parametre, der kan bruges til at få vist flere job udførelser, filtreres gennem de medfølgende parametre. Følgende viser nogle af de mulige måder at bruge Get-AzureSqlJobExecution:

Hente alle udførelser aktivt job på øverste niveau:

    Get-AzureSqlJobExecution

Hente alle udførelser på øverste niveau job, herunder inaktiv job udførelser:

    Get-AzureSqlJobExecution -IncludeInactive

Hente alle underordnede jobstatus udførelser af en medfølgende jobstatus udførelse af ID, herunder inaktiv job udførelser:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId –IncludeInactive -IncludeChildren

Hente alle job udførelser, der er oprettet ved hjælp af en tidsplan / job kombination, herunder inaktive job:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Hente alle job målretning af et angivet shard kort, herunder inaktive job:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive

Hente alle job målretning af en bestemt brugerdefineret samling, herunder inaktive job:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId –IncludeInactive
 
Hente listen over job opgave udførelser inden for et bestemt job udførelse af:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Hente oplysninger om job opgave udførelse af:

Følgende PowerShell-script kan bruges til at få vist detaljerne for en sag opgave udførelse af, hvilket er særligt nyttigt, når du foretager fejlfinding fejl under udførelse af.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="retrieve-failures-within-job-task-executions"></a>Hente fejl i jobbet opgave udførelser

Objektet JobTaskExecution indeholder en egenskab til opgaven sammen med en meddelelsesegenskab livscyklus. Hvis et job opgave udførelse mislykkedes, egenskaben livscyklus indstilles til *mislykket* og egenskaben meddelelse indstilles til resulterende Undtagelsesmeddelelsen og dens stak. Hvis et job mislykkedes, er det vigtigt at få vist detaljerne for opgaver, som mislykkedes for en given sag.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="waiting-for-a-job-execution-to-complete"></a>Venter til udførelse af et job til at fuldføre

Følgende PowerShell-script kan bruges til at vente på en jobopgave til at udføre:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Oprette en brugerdefineret udførelse af politik

Elastiske Database job understøtter oprettelse af brugerdefinerede udførelse af politikker, der kan anvendes, når du starter job.
  
I øjeblikket tillader udførelse af politikker til at definere:

* Navn: Id for udførelse af politikken.
* Job Timeout: Samlet tid før et job annulleres af elastiske Database job.
* Indledende Interval for gentagelse: Interval skal gå, før første gentagelse.
* Maksimale Interval for gentagelse: Knop af intervaller, prøv at bruge.
* Forsøg Interval Backoff koefficient: Koefficient bruges til at beregne det næste interval mellem nye forsøg.  Følgende formel bruges: (indledende forsøg Interval) * Math.pow ((Interval Backoff koefficient) (antal nye forsøg) - 2). 
* Maksimale antal forsøg: Det maksimale antal forsøger at udføre inden for et job.

Udførelse af standardpolitikken bruger følgende værdier:

* Navn: Udførelse af standardpolitik for
* Job Timeout: 1 uge
* Indledende Interval for gentagelse: 100 millisekunder
* Maksimale Interval for gentagelse: 30 minutter
* Prøv igen Interval koefficient: 2
* Maksimale antal forsøg: 2.147.483.647

Oprette den ønskede udførelse af politik:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Opdatere en brugerdefineret udførelse af politik

Opdatere den ønskede udførelse af politik til at opdatere:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
 
## <a name="cancel-a-job"></a>Annullere et job

Elastiske Database job understøtter anmodninger om annullering af sager.  Hvis elastiske Database job registrerer en anmodning om aflysning for en sag, der aktuelt kører, vil det forsøge at stoppe jobbet.

Der er to forskellige måder, elastiske Database job kan udføre en annullering:

1. Annullere udføres i øjeblikket opgaver: Hvis der registreres en annullering, mens en opgave er i gang, en annullering forsøges i det i øjeblikket aspekt af opgaven.  For eksempel: Hvis der er en længerevarende forespørgsel, der aktuelt udføres, når en annullering forsøges, vil der være et forsøg på at annullere forespørgslen.
2. Annullering opgave nye forsøg: Hvis der registreres en annullering af kontrolelementet tråd, før en opgave er startet til udførelse af, kontrol tråd vil undgå at åbne opgaven og erklære anmodningen, som annulleret.

Hvis du ønsker en sag aflysning for en overordnet sag, vil annulleringen anmodningen anvendes for det overordnede job og alle dens underordnede job.
 
Hvis du vil sende en anmodning om annulleringen, bruge Cmdletten **Stop AzureSqlJobExecution** og angive parameteren **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Slette en sag ved navn og den jobs

Elastiske Database job understøtter asynkron sletningen af sager. Et job kan markeres til sletning og systemet sletter jobbet og alle dens jobs når alle jobstatus udførelser har fuldført til opgaven. Systemet vil ikke automatisk annullere aktivt job udførelser.  

Stop AzureSqlJobExecution skal åbnes i stedet for at annullere aktivt job udførelser.

Brug cmdlet'en **Fjern AzureSqlJob** for at udløse job sletningen, og Angiv parameteren **Jobnavn** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="create-a-custom-database-target"></a>Oprette et brugerdefineret database mål
Brugerdefineret database destinationer kan defineres i elastiske Database job som kan bruges til udførelse af direkte eller skal indgå i en brugerdefineret database gruppe. Da **elastiske Database grupper** ikke understøttes endnu direkte via PowerShell APIs, oprette du blot en brugerdefineret database mål og brugerdefinerede database samling target hvilket omfatter alle databaser i puljen.

Angiv følgende variabler til at afspejle den ønskede databaseoplysninger:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="create-a-custom-database-collection-target"></a>Oprette et brugerdefineret database samling mål
En brugerdefineret database samling destination kan defineres for at aktivere udførelse af på tværs af flere definerede database destinationer. Når en database gruppe er oprettet, kan databaser knyttes til brugerdefineret samling destination.

Angiv følgende variabler til at afspejle den ønskede brugerdefinerede samling target konfiguration:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="add-databases-to-a-custom-database-collection-target"></a>Føje databaser til en brugerdefineret database samling destination

Database destinationer kan knyttes til brugerdefineret database samling destinationer til at oprette en gruppe af databaser. Når der oprettes et job, der er beregnet til en brugerdefineret database samling destination, er det udvidet for at målrette for databaser, der er knyttet til gruppen på kørselstidspunktet.

Tilføje den ønskede database til en bestemt brugerdefineret samling:

    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Gennemse databaser i en brugerdefineret database samling destination

Brug cmdlet'en **Get-AzureSqlJobTarget** til at hente de underordnede databaser i en brugerdefineret database samling destination. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Oprette et job for at udføre et script på tværs af en brugerdefineret database samling destination

Brug cmdlet'en **Ny AzureSqlJob** til at oprette en sag mod en gruppe af databaser, der er defineret af et brugerdefineret database samling mål. Elastiske Database job kan udvide jobbet til flere underordnede job hver svarer til en database, der er knyttet til brugerdefineret database samling destinationen og sikre, at scriptet køres i forhold til hver database. Igen, det er vigtigt, at scripts, som er idempotent skal være tolerant for nye forsøg.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Indsamling af data på tværs af databaser

**Elastiske Database job** understøtter udførelse af en forespørgsel på tværs af en gruppe af databaser og sender resultaterne til en bestemt databasetabel. Tabellen kan forespørges efter fakultet at få vist forespørgslens resultater fra hver database. Dette giver asynkron mulighed for at udføre en forespørgsel på tværs af mange databaser. Fejl i visse tilfælde som en af de databaser, der ikke tilgængelig i øjeblikket håndteres automatisk via forsøg.

Den angivne destinationstabellen oprettes automatisk, hvis den ikke findes, svarer til skemaet for det returnerede resultatsæt. Hvis et script udførelse af returnerer flere resultatsæt, sende elastiske Database job kun den første opgave til medfølgende destinationstabellen.

Følgende PowerShell-script kan bruges til at udføre et script, indsamling af resultaterne i en bestemt tabel. Dette script antager, at et T-SQL-script er blevet oprettet, skriver et enkelt resultatsæt og et brugerdefineret database samling mål er blevet oprettet.

Angiv følgende for at afspejle den ønskede script, legitimationsoplysninger og udførelse af destination:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Oprette og starte et job for data samling scenarier
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Oprette en tidsplan til udførelse af job ved hjælp af en sag udløser

Følgende PowerShell-script kan bruges til at oprette en tidsplan for reoccurring. Dette script bruger et et minut interval, men ny AzureSqlJobSchedule understøtter også - DayInterval, - HourInterval, - MonthInterval, og -WeekInterval parametre. Planer, der udføres kun én gang kan oprettes ved der passerer - enkelt.

Oprette en ny tidsplan:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime 
    Write-Output $schedule

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Oprette en sag udløser for at få et job, der udføres på en tidsplan

En jobstatus udløser kan defineres for at få et job udføres efter en tidsplan. Følgende PowerShell-script kan bruges til at oprette en sag udløser.

Angiv følgende variabler skal svare til den ønskede sag og tidsplan:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName –JobName $jobName
    Write-Output $jobTrigger

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Fjerne en planlagt tilknytning stoppe jobbet fra udførelse af tidsplanen

Hvis du vil afbryde opstår igen jobstatus udførelse af gennem en jobstatus udløser kan jobstatus udløser fjernes. Fjerne en sag udløser for at stoppe en sag i at blive udført ifølge en tidsplan for ved hjælp af **Fjern AzureSqlJobTrigger** cmdlet.

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName





## <a name="import-elastic-database-query-results-to-excel"></a>Importere elastiske resultater af databaseforespørgsler til Excel

 Du kan importere resultaterne af en forespørgsel til en Excel-fil.

1. Start Excel 2013.
2.  Gå til fanen **Data** .
3.  Klik på **Fra andre kilder** , og klik på **Fra SQL Server**.

    ![Excel Importér fra andre kilder][5]
4.  Skriv navn, og log serveroplysninger i **Guiden Dataforbindelse** . Klik derefter på **Næste**.
5.  Vælg databasen, **ElasticDBQuery** i dialogboksen **Vælg den database, der indeholder de ønskede data**.
6.  Vælg tabellen **kunder** i listevisningen, og klik på **Næste**. Klik derefter på **Udfør**.
7.  Vælg **tabel** formen **Importdata** , under **Vælg, hvordan du vil have vist disse data i projektmappen**, og klik på **OK**.

Alle rækker fra tabellen **kunder** , gemt i forskellige shards udfylde Excel-regnearket.

## <a name="next-steps"></a>Næste trin
Du kan nu bruge Excels data funktioner. Bruge forbindelsesstrengen med dit servernavn, databasenavnet og legitimationsoplysninger til at forbinde dine BI og data integration værktøjer til den elastiske forespørgsel database. Sørg for, at SQL Server understøttes som en datakilde til din værktøj. Se elastiske forespørgsel database og eksterne tabeller ligesom andre SQL Server-database og SQL Server-tabeller, du vil oprette forbindelse til med din værktøjet.

### <a name="cost"></a>Omkostninger
Der er ingen ekstra gebyr til brug af funktionen elastiske Database forespørgsel. Dog på nuværende tidspunkt denne funktion er kun tilgængelig på premium databaser som et slutpunkt, men shards kan være en hvilken som helst webtjenesten.

Se [SQL Database priser oplysninger](https://azure.microsoft.com/pricing/details/sql-database/)til prisoplysninger.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
