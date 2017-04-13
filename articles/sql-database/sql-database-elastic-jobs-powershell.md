<properties 
    pageTitle="Oprette og administrere elastiske Database job ved hjælp af PowerShell" 
    description="PowerShell til at administrere grupper Azure SQL-Database" 
    services="sql-database" documentationCenter=""  
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="create-and-manage-a-sql-database-elastic-database-jobs-using-powershell-preview"></a>Oprette og administrere en SQL-Database elastiske database-job ved hjælp af PowerShell (preview)

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)



De PowerShell APIs for **elastiske databasen jobs** (i eksemplet), gør det muligt definere en gruppe af databaser som scripts udføres. Denne artikel beskrives, hvordan du kan oprette og administrere **elastiske Database job** ved hjælp af PowerShell-cmdlet'er. Se [Oversigt over elastiske sager](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Forudsætninger
* Et Azure-abonnement. Du kan finde en gratis prøveversion [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).
* Et sæt af databaser, der er oprettet med de elastiske Databaseværktøjer. Se [Introduktion til elastiske Databaseværktøjer](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Du kan finde detaljerede oplysninger, se, [hvordan du installerer og konfigurerer Azure PowerShell](../powershell-install-configure.md).
* **Elastiske Database job** PowerShell-pakke: se [installere elastiske Database job](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Vælg din Azure abonnement

For at markere det abonnement, du har brug for dit abonnement Id (**-SubscriptionId**) eller et andet abonnement, skal du navngive (**-SubscriptionName**). Hvis du har flere abonnementer, du kan du køre følgende cmdlet **Get-AzureRmSubscription** og kopiere den ønskede abonnementsoplysninger fra resultatsættet. Når du har dine abonnementsoplysninger, kan du køre den følgende cmdlet accepterer for at angive dette abonnement som standard, nemlig destinationen for oprettelse og administration af sager:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

[PowerShell ISE](https://technet.microsoft.com/library/dd315244.aspx) anbefales for at få hjælp til at udvikle og udføre PowerShell-scripts mod elastiske databasen job.

## <a name="elastic-database-jobs-objects"></a>Elastiske job databaseobjekter

I følgende tabel vises af alle objekttyperne **elastiske Database job** sammen med den relevante PowerShell APIs og beskrivelse.

<table style="width:100%">
  <tr>
    <th>Objekttype</th>
    <th>Beskrivelse</th>
    <th>Relaterede PowerShell-API'er</th>
  </tr>
  <tr>
    <td>Legitimationsoplysninger</td>
    <td>Brugernavn og din adgangskode skal bruges, når du opretter forbindelse til databaser til udførelse af scripts eller anvendelsen af DACPACs. <p>Adgangskoden er krypteret før afsendelse af til og lagring i elastiske Database job databasen.  Adgangskoden er dekrypteres af tjenesten elastiske Database job via de legitimationsoplysninger, der er oprettet, og der er overført fra installationsscriptet.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Ny AzureSqlJobCredential</p><p>Angiv AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Transact-SQL-script, der skal bruges til udførelse af på tværs af databaser.  Scriptet skal være skrevet for at være idempotent, da tjenesten vil forsøge udførelse af script på mislykkede forsøg.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Ny AzureSqlJobContent</p>
    <p>Angiv AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Data lag program </a> pakke, der skal anvendes på tværs af databaser.

    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Ny AzureSqlJobContent</p>
    <p>Angiv AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Database mål</td>
    <td>Database og servernavn, der peger på en Azure SQL-Database.

    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Ny AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Shard kort mål</td>
    <td>Kombination af en database mål og en legitimationsoplysninger, der skal bruges til at bestemme oplysninger, der er gemt i en elastiske Database shard tilknytning.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Ny AzureSqlJobTarget</p>
    <p>Angiv AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Brugerdefineret samling mål</td>
    <td>Gruppen definerede over databaser, bruge til udførelse af.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Ny AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Brugerdefineret samling underordnede mål</td>
    <td>Database destinationen, der refereres til i en brugerdefineret samling.</td>
    <td>
    <p>Tilføje AzureSqlJobChildTarget</p>
    <p>Fjern AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Job</td>
    <td>
    <p>Definition af parametre for et job, der kan bruges til at udløse udførelse af eller til at opfylde en tidsplan.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Ny AzureSqlJob</p>
    <p>Angiv AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Udførelse af job</td>
    <td>
    <p>Objektbeholder af opgaver, der er nødvendige for at opfylde enten udførelse af et script eller anvende en DACPAC til en destination, ved hjælp af legitimationsoplysninger for databaseforbindelser med fejl håndteret i henhold til en politik til udførelse af.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop AzureSqlJobExecution</p>
    <p>Vent-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Udførelse af jobbet opgave</td>
    <td>
    <p>Samlet enhed for arbejde at opfylde et job.</p>
    <p>Hvis en jobopgave ikke kunne korrekt udføre, resulterende Undtagelsesmeddelelsen vil blive logført og en ny tilsvarende jobopgave oprettes og udført i henhold til den angivne udførelse af politik.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Start-AzureSqlJobExecution</p>
    <p>Stop AzureSqlJobExecution</p>
    <p>Vent-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Jobbet udførelse af politik</td>
    <td>
    <p>Kontrolelementer job udførelse af timeout, prøv igen begrænsninger og intervallerne mellem nye forsøg.</p>
    <p>Elastiske Database job omfatter en politik i standard job som medføre grundlæggende uendelig nye forsøg antal mislykkede forsøg job opgave med eksponentiel backoff af intervallerne mellem hvert forsøg igen.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Ny AzureSqlJobExecutionPolicy</p>
    <p>Angiv AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Tidsplan</td>
    <td>
    <p>Tid baseret specifikation for udførelse af til at finde sted på et reoccurring interval eller ad gangen.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Ny AzureSqlJobSchedule</p>
    <p>Angiv AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Jobbet udløsere</td>
    <td>
    <p>En tilknytning mellem et job og en tidsplan for at udløse udførelse af jobbet efter tidsplanen.</p>
    </td>
    <td>
    <p>Ny AzureSqlJobTrigger</p>
    <p>Fjern AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Understøttede elastiske Database job gruppere typer
Jobbet udfører Transact-SQL (T-SQL) scripts eller anvendelse af DACPACs på tværs af en gruppe af databaser. Når et job sendes der skal udføres på tværs af en gruppe af databaser, jobbet "udvider" det i underordnede job, hvor hver udfører den nødvendige udførelse af mod en enkelt database i gruppen. 
 
Der er to typer af grupper, du kan oprette: 

* [Shard kort](sql-database-elastic-scale-shard-map-management.md) gruppe: Når et job er sendt til at målrette et shard kort, jobbet forespørger shard afbildning for at finde ud af det aktuelle sæt shards, og derefter opretter underordnede opgaver for hver shard i shard kortet.
* Brugerdefineret gruppe af websteder: en brugerdefineret defineret sæt af databaser. Når et job er beregnet til en brugerdefineret samling, oprettes der underordnede job for hver database i øjeblikket i brugerdefineret samling.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Hvis du vil angive elastiske databasen job forbindelse

En forbindelse, skal være indstillet til job *kontrolelementet database* ved hjælp af job API'er. Kører denne cmdlet udløser et legitimationsoplysninger vindue til pop op-anmoder om det brugernavn og adgangskode, når du installerer elastiske databasen job har oprettet. Alle eksempler, der er angivet i dette emne forudsætter, at det første trin allerede er udført.

Åbn en forbindelse til de elastiske Database job:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Krypterede legitimationsoplysninger i elastiske Database-job

Databaselegitimationsoplysninger skal indsættes i job *kontrolelement databasen* med den krypterede adgangskode. Det er nødvendigt at lagre legitimationsoplysninger for at aktivere job skal udføres på et senere tidspunkt (ved hjælp af jobbet tidsplaner).
 
Kryptering fungerer gennem et certifikat, der er oprettet som en del af installationsscriptet. Installationsscriptet opretter og overfører certifikatet i Azure skybaseret tjeneste til dekryptering af de lagrede krypterede adgangskoder. Azure skybaseret tjeneste gemmer senere offentlig nøgle i job *kontrolelementet database* som gør det muligt grænsefladen PowerShell API eller Azure klassisk Portal til at kryptere en medfølgende adgangskode uden at certifikatet, der skal installeres lokalt.
 
Legitimationsoplysninger adgangskoder er krypteret og sikker fra brugere med skrivebeskyttet adgang til elastiske job databaseobjekter. Men det er muligt for en hacker læse / skrive-adgang til elastiske databasen job objekter udtrække en adgangskode. Legitimationsoplysninger er designet til at genbruges på tværs af job udførelser. Legitimationsoplysninger er overført til target databaser ved oprettelse af forbindelser. Er der i øjeblikket ingen begrænsninger på de destinationsadresse-databaser, der bruges for hver legitimationsoplysninger, ondsindet bruger tilføjer en database destination til en database under den ondsindede bruger kontrolelement. Brugeren kan efterfølgende starte et job målretning af denne database for at få de legitimationsoplysninger adgangskode.

Bedste fremgangsmåder for sikkerhed for elastiske Database sager omfatter:

* Begrænse brugen af API'er til personer, der er tillid til.
* Legitimationsoplysninger bør have de minimale privilegier, der er nødvendigt at udføre opgaven job.  Du kan finde flere oplysninger kan ses i SQL Server MSDN-artiklen [godkendelse og tilladelser](https://msdn.microsoft.com/library/bb669084.aspx) .

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Sådan oprettes en krypteret legitimationsoplysninger til udførelse af job via databaser

Hvis du vil oprette en ny krypterede legitimationsoplysninger, beder [**Get-legitimationsoplysninger cmdlet**](https://technet.microsoft.com/library/hh849815.aspx) om et brugernavn og adgangskode, der kan overføres til [**Ny AzureSqlJobCredential cmdlet**](https://msdn.microsoft.com/library/mt346063.aspx).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Opdatere legitimationsoplysninger

Når adgangskoder ændrer, bruge [**cmdlet'en Set-AzureSqlJobCredential**](https://msdn.microsoft.com/library/mt346062.aspx) og angive parameteren **CredentialName** .

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Til at definere et elastiske Database shard kort mål

Brug et shard kort for at udføre en sag mod alle databaser i et shard sæt (oprettet ved hjælp af [elastiske Database klientbibliotek](sql-database-elastic-database-client-library.md)), som mål-database. I dette eksempel kræver et delt program, der er oprettet ved hjælp af biblioteket elastiske Database klient. Se [Kom i gang med elastiske Database værktøjer eksempel](sql-database-elastic-scale-get-started.md).

Shard kort manager-database skal være angivet som en database destination og derefter bestemte shard kortet skal være angivet som destination.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Oprette et T-SQL-Script til udførelse af på tværs af databaser

Når du opretter T-SQL-scripts til udførelse af, er det meget anbefalede til at oprette dem for at være [idempotent](https://en.wikipedia.org/wiki/Idempotence) og tolerant mod fejl. Elastiske Database job gentagne udførelse af et script, når udførelse af støder på en fejl, uanset klassificeringen af fejlen.

Brug [**Ny AzureSqlJobContent cmdlet**](https://msdn.microsoft.com/library/mt346085.aspx) til at oprette og gemme et script til udførelse af og angive parametrene **- ContentName** og **-CommandText** .

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Oprette et nyt script fra en fil
Hvis T-SQL-script er defineret i en fil, kan du bruge dette til at importere scriptet:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Opdatere et T-SQL-script til udførelse af på tværs af databaser  

Denne PowerShell-script opdaterer T-SQL kommandotekst til et eksisterende script.

Angiv følgende variabler til at afspejle den ønskede script definition skal angives:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Opdatere definitionen til et eksisterende script

    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Oprette et job for at udføre et script på tværs af et shard kort

Denne PowerShell-script starter et job til udførelse af et script på tværs af hver shard i et elastiske skala shard kort.

Angiv følgende variabler til at afspejle den ønskede script- og destinationswebsteder:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>At udføre et job 

Denne PowerShell-script udfører et eksisterende job:

Opdater følgende variabel afspejler det ønskede jobnavn, der er udført:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution
 
## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Til at hente tilstanden for en enkelt job udførelse af

Brug [**cmdlet'en Get-AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346058.aspx) og angive parameteren **JobExecutionId** til at få vist status for udførelse af jobbet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Brug samme **Get-AzureSqlJobExecution** cmdlet'en med parameteren **IncludeChildren** for at få vist tilstanden for underordnede job udførelser, nemlig bestemte tilstanden til udførelse af hver job mod hver database, der er rettet af jobbet.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Sådan får du vist tilstanden på tværs af flere job udførelser

[**Get-AzureSqlJobExecution cmdlet**](https://msdn.microsoft.com/library/mt346058.aspx) har flere valgfrie parametre, der kan bruges til at få vist flere job udførelser filtreres gennem de medfølgende parametre. Følgende viser nogle af de mulige måder at bruge Get-AzureSqlJobExecution:

Hente alle udførelser aktivt job på øverste niveau:

    Get-AzureSqlJobExecution

Hente alle udførelser på øverste niveau job, herunder inaktiv job udførelser:

    Get-AzureSqlJobExecution -IncludeInactive

Hente alle underordnede job udførelser af en medfølgende job udførelse af ID, herunder inaktiv job udførelser:

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

## <a name="to-retrieve-failures-within-job-task-executions"></a>Til at hente fejl i jobbet opgave udførelser

**JobTaskExecution objekt** indeholder en egenskab for livscyklus for opgaven sammen med en meddelelsesegenskab. Hvis et job opgave udførelse mislykkedes, egenskaben livscyklus indstilles til *mislykket* og meddelelsesegenskaben indstilles til resulterende Undtagelsesmeddelelsen og dens stak. Hvis et job mislykkedes, er det vigtigt at få vist detaljerne for opgaver, som mislykkedes for en given sag.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>Gå til udførelse af et job til at fuldføre

Følgende PowerShell-script kan bruges til at vente på en jobopgave til at udføre:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Oprette en brugerdefineret udførelse af politik

Elastiske Database job understøtter oprettelse af brugerdefinerede udførelse af politikker, der kan anvendes, når du starter job.
  
I øjeblikket tillader udførelse af politikker til at definere:

* Navn: Id for udførelse af politikken.
* Job Timeout: Samlet tid før et job annulleres af elastiske Database job.
* Indledende Interval for gentagelse: Interval skal gå, før første gentagelse.
* Maksimale Interval for gentagelse: Knop af intervaller, forsøg at bruge.
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
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
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

1. Annuller udføres i øjeblikket opgaver: Hvis der registreres en annullering, mens en opgave er i gang, en annullering forsøges i det i øjeblikket aspekt af opgaven.  For eksempel: Hvis der er en længerevarende forespørgsel, der aktuelt udføres, når en annullering forsøges, vil der være et forsøg på at annullere forespørgslen.
2. Annullere opgave forsøg: Hvis der registreres en annullering af kontrolelementet tråd, før en opgave er startet til udførelse af, kontrol tråd skal undgå at åbne opgaven og erklære anmodningen, som annulleret.

Hvis du ønsker en sag aflysning for en overordnet sag, vil annulleringen anmodningen anvendes for det overordnede job og alle dens underordnede job.
 
Brug [**Stop AzureSqlJobExecution cmdlet**](https://msdn.microsoft.com/library/mt346053.aspx) for at sende en anmodning om annulleringen, og Angiv parameteren **JobExecutionId** .

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Slette et job og job historik asynkront

Elastiske Database job understøtter asynkron sletningen af sager. Et job kan markeres til sletning og systemet sletter jobbet og alle dens jobs når alle job udførelser har fuldført til opgaven. Systemet vil ikke automatisk annullere aktivt job udførelser.  

Kalde [**Stop AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346053.aspx) for at annullere aktivt job udførelser.

Brug [**Fjern AzureSqlJob cmdlet**](https://msdn.microsoft.com/library/mt346083.aspx) for at udløse job sletningen, og Angiv parameteren **Jobnavn** .

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
 
## <a name="to-create-a-custom-database-target"></a>At oprette et brugerdefineret database mål

Du kan definere brugerdefinerede database mål for direkte udførelse af eller skal indgå i en brugerdefineret database gruppe. Da **elastiske Database grupper** ikke understøttes endnu direkte ved hjælp af PowerShell APIs, kan du f.eks, oprette en brugerdefineret database mål og brugerdefinerede database samling target hvilket omfatter alle databaser i puljen.

Angiv følgende variabler til at afspejle den ønskede databaseoplysninger:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>At oprette et brugerdefineret database samling mål

Brug [**Ny AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) cmdlet til at definere et brugerdefineret database samling mål for at aktivere udførelse af på tværs af flere definerede database mål. Når du har oprettet en database gruppe, kan databaser være knyttet til brugerdefineret samling mål.

Angiv følgende variabler til at afspejle den ønskede brugerdefinerede samling mål konfiguration:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Føje databaser til en brugerdefineret database samling destination

Hvis du vil tilføje en database til en bestemt brugerdefineret samling Brug [**Tilføj AzureSqlJobChildTarget**](https://msdn.microsoft.comlibrary/mt346064.aspx) cmdlet'en.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Gennemse databaser i en brugerdefineret database samling destination

Brug cmdlet'en [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) til at hente de underordnede databaser i en brugerdefineret database samling destination. 
 
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Oprette et job for at udføre et script på tværs af en brugerdefineret database samling destination

Brug cmdlet'en [**Ny AzureSqlJob**](https://msdn.microsoft.com/library/mt346078.aspx) til at oprette en sag mod en gruppe af databaser, der er defineret af et brugerdefineret database samling mål. Elastiske databasen job skal udvide jobbet til flere underordnede job hver svarer til en database, der er knyttet til brugerdefineret databasen samling mål og sikre, at scriptet køres i forhold til hver database. Igen, det er vigtigt, at scripts, som er idempotent skal være tolerant for nye forsøg.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Indsamling af data på tværs af databaser

Du kan bruge et job til at udføre en forespørgsel på tværs af en gruppe af databaser og sende resultaterne til en bestemt tabel. Tabellen kan forespørges efter fakultet at få vist forespørgslens resultater fra hver database. Dette giver en asynkron metode til at udføre en forespørgsel på tværs af mange databaser. Mislykkede forsøg håndteres automatisk via forsøg.

Den angivne destinationstabellen oprettes automatisk, hvis den ikke findes. Den nye tabel svarer til det returnerede resultatsæt skema. Hvis et script returnerer flere resultatsæt, sende elastiske Database job kun først til destinationstabellen.

Følgende PowerShell-script udfører et script og samler resultaterne i en bestemt tabel. Dette scriptet antages, der er oprettet et T-SQL-script, skriver et enkelt resultatsæt og, et brugerdefineret database samling mål er blevet oprettet.

Dette script bruger Cmdletten [**Get-AzureSqlJobTarget**](https://msdn.microsoft.com/library/mt346077.aspx) . Angiv parametrene for script, legitimationsoplysninger og udførelse af destination:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Oprette og starte et job for data samling scenarier

Dette script bruger Cmdletten [**Start AzureSqlJobExecution**](https://msdn.microsoft.com/library/mt346055.aspx) .
 
    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Planlægge en sag udførelse af udløser

Følgende PowerShell-script kan bruges til at oprette en tilbagevendende tidsplan. Dette script bruger et minut interval, men [**Ny AzureSqlJobSchedule**](https://msdn.microsoft.com/library/mt346068.aspx) understøtter også - DayInterval, - HourInterval, - MonthInterval, og -WeekInterval parametre. Planer, der udføres kun én gang kan oprettes ved der passerer - enkelt.

Oprette en ny tidsplan:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Udløse en sag, der udføres på en tidsplan

En sag udløser kan defineres for at få et job udføres efter en tidsplan. Følgende PowerShell-script kan bruges til at oprette en sag udløser.

Bruge [Ny AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346069.aspx) og angive følgende variabler skal svare til den ønskede sag og tidsplan:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    –JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Fjerne en planlagt tilknytning stoppe jobbet fra udførelse af tidsplanen

Hvis du vil afbryde opstår igen job udførelse af gennem en sag udløser kan job udløser fjernes. Fjerne en sag udløser for at stoppe en sag i at blive udført ifølge en tidsplan for ved hjælp af [**Fjern AzureSqlJobTrigger cmdlet**](https://msdn.microsoft.com/library/mt346070.aspx).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Hente job udløsere, der er bundet til en tidsplan

Følgende PowerShell-script kan bruges til at hente og få vist de job udløsere, der er registreret til en bestemt tidsplan.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Hvis du vil hente job udløsere bundet til et job 

Brug [Get-AzureSqlJobTrigger](https://msdn.microsoft.com/library/mt346067.aspx) til at hente og få vist tidsplaner, der indeholder et registreret job.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Sådan oprettes et data lag program (DACPAC) til udførelse af på tværs af databaser

For at oprette en DACPAC skal du se [Data lag programmer](https://msdn.microsoft.com/library/ee210546.aspx). Brug [Ny AzureSqlJobContent cmdlet](https://msdn.microsoft.com/library/mt346085.aspx)for at installere en DACPAC. DACPAC skal være adgang til tjenesten. Det anbefales at overføre et oprettet DACPAC til Azure-lager og oprette en [Delt Access signatur](../storage/storage-dotnet-shared-access-signature-part-1.md) til DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Opdatere data lag programmet (DACPAC) til udførelse af på tværs af databaser

Eksisterende DACPACs, der er registreret i elastiske Database job kan blive opdateret, hvis du vil pege på ny URI'er. Brug cmdlet'en [**Set-AzureSqlJobContentDefinition-cmdlet**](https://msdn.microsoft.com/library/mt346074.aspx) til at opdatere DACPAC URI på en eksisterende registrerede DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Oprette et job for at anvende et data lag program (DACPAC) på tværs af databaser

Når en DACPAC er blevet oprettet i elastiske Database job, kan et job oprettes for at anvende DACPAC på tværs af en gruppe af databaser. Følgende PowerShell-script kan bruges til at oprette et DACPAC job på tværs af en brugerdefineret samling af databaser:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
