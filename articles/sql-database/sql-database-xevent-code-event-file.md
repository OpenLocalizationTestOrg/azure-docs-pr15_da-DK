<properties 
    pageTitle="XEvent begivenhedsfil kode for SQL-Database | Microsoft Azure" 
    description="Indeholder PowerShell og Transact-SQL til en bestående af to faser kodeeksempel, der viser mål begivenhedsfil i en udvidet begivenhed på Azure SQL-Database. Azure-lager er en påkrævet del af dette scenario." 
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jhubbard" 
    editor="" 
    tags=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="genemi"/>


# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Begivenhed fil target kode for udvidede begivenheder i SQL-Database

[AZURE.INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Du vil et eksempel på fuldført kode for en solid måde at oplysninger om registrering og rapport for en udvidet begivenhed.


I Microsoft SQL Server bruges [begivenhedsfil mål](http://msdn.microsoft.com/library/ff878115.aspx) til at gemme begivenhed output til en lokal harddisk-fil. Men disse filer er ikke tilgængelige til Azure SQL-Database. I stedet bruge vi tjenesten Azure-lager til at understøtte begivenhedsfil destinationen.


I dette emne vises et eksempel på to faser kode:


- PowerShell til at oprette en objektbeholder til lagring af Azure i skyen.

- Transact-SQL:
 - Tildele objektbeholderen Azure-lager til en begivenhedsfil destination.
 - At oprette og starte sessionen begivenhed, og så videre.


## <a name="prerequisites"></a>Forudsætninger


- En Azure-konto og abonnement. Du kan tilmelde dig en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/).


- En database, kan du oprette en tabel i.
 - Du kan også kan du [oprette en **AdventureWorksLT** demonstrationsdatabase](sql-database-get-started.md) i minutter.


- SQL Server Management Studio (ssms.exe), ideelt den seneste månedlige opdatering version. Du kan hente de seneste ssms.exe fra:
 - Emne med titlen [Hente SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
 - [Et direkte link til overførslen.](http://go.microsoft.com/fwlink/?linkid=616025)


- Du skal have [Azure PowerShell moduler](http://go.microsoft.com/?linkid=9811175) installeret.
 - Modulerne, der indeholder kommandoer, som - **Ny AzureStorageAccount**.


## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fase 1: PowerShell kode til Azure-lager objektbeholder


Denne PowerShell er fase 1 i kodeeksemplet bestående af to faser.

Scriptet starter med kommandoer til at rydde op, når en tidligere muligt Kør, og er rerunnable.



1. Indsæt PowerShell-script i en teksteditor som Notepad.exe, og Gem scriptet som en fil med filtypenavnet **.ps1**.

2. Starte PowerShell ISE som Administrator.

3. Når du bliver spurgt, Skriv<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>og tryk derefter på Enter.

4. Åbn filen **.ps1** i PowerShell ISE. Køre scriptet.

5. Scriptet første gang starter et nyt vindue, hvor du logger på Azure.
 - Hvis du skal køre scriptet uden at forstyrre din session, har du praktisk mulighed for at kommentere kommandoen **Tilføj AzureAccount** .


![PowerShell ISE med Azure modul installeret, er du klar til at køre scriptet.][30_powershell_ise]


&nbsp;


```
## TODO: Before running, find all 'TODO' and make each edit!!

#--------------- 1 -----------------------


# You can comment out or skip this Add-AzureAccount
# command after the first run.
# Current PowerShell environment retains the successful outcome.

'Expect a pop-up window in which you log in to Azure.'


Add-AzureAccount

#-------------- 2 ------------------------


'
TODO: Edit the values assigned to these variables, especially the first few!
'

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME'
$policySasExpiryTime = '2016-01-28T23:44:56Z'
$policySasStartTime  = '2015-08-01'


$storageAccountName     = 'gmstorageaccountxevent'
$storageAccountLocation = 'West US'
$contextName            = 'gmcontext'
$containerName          = 'gmcontainerxevent'
$policySasToken         = 'gmpolicysastoken'


# Leave this value alone, as 'rwl'.
$policySasPermission = 'rwl'

#--------------- 3 -----------------------


# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.'


Select-AzureSubscription -SubscriptionName $subscriptionName


#-------------- 4 ------------------------


'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.'


If ($storageAccountName)
{
    Remove-AzureStorageAccount -StorageAccountName $storageAccountName
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString()

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...'

New-AzureStorageAccount `
    -StorageAccountName $storageAccountName `
    -Location           $storageAccountLocation

[System.DateTime]::Now.ToString()

[System.Media.SystemSounds]::Beep.Play()


'
Get the primary access key for your storage account.
'


$primaryAccessKey_ForStorageAccount = `
    (Get-AzureStorageKey `
        -StorageAccountName $storageAccountName).Primary

"`$primaryAccessKey_ForStorageAccount = $primaryAccessKey_ForStorageAccount"

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
'

#--------------- 6 -----------------------


# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
'

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $primaryAccessKey_ForStorageAccount


'Create a container within the storage account.
'


$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context


'Create a security policy to be applied to the SAS token.
'

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime 

'
Generate a SAS token for the container.
'
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken
}
Catch 
{
    $Error[0].Exception.ToString()
}

#-------------- 7 ------------------------


'Display the values that YOU must edit into the Transact-SQL script next!:
'

"storageAccountName: $storageAccountName"
"containerName:      $containerName"
"sasTokenWithPolicy: $sasTokenWithPolicy"

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
'

'
(Later, return here to delete your Azure Storage account. See the preceding - Remove-AzureStorageAccount -StorageAccountName $storageAccountName)'

'
Now shift to the Transact-SQL portion of the two-part code sample!'

# EOFile
```


&nbsp;


Lægge mærke til de par navngivne værdier, der udskrives PowerShell-script, når den slutter. Ind i Transact-SQL-script, der følger efter som fase 2 skal du redigere disse værdier.


## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fase 2: Transact-SQL-kode, der bruger Azure-lager objektbeholder


- I fase 1 i dette eksempel-kode kørte du en PowerShell-script for at oprette en beholder Azure-lager.
- Næste i fase 2, følgende Transact-SQL-script skal du bruge objektbeholderen.


Scriptet starter med kommandoer til at rydde op, når en tidligere muligt Kør, og er rerunnable.


PowerShell-script udskrives et par navngivne værdier, når den er afsluttet. Du skal redigere Transact-SQL-script for at bruge disse værdier. Finde **Opgaveliste** i Transact-SQL-script til at finde redigeringspunkter.


1. Åbn SQL Server Management Studio (ssms.exe).

2. Oprette forbindelse til databasen Azure SQL-Database.

3. Klik for at åbne en ny forespørgselsrude.

4. Indsæt følgende Transact-SQL-script i forespørgselsruden.

5. Find hver **Opgaveliste** i scriptet, og foretag de ønskede ændringer.

6. Gem, og derefter køre scriptet.


&nbsp;


> [AZURE.WARNING] SAS nøgleværdi genereres af den foregående PowerShell-script kan begynde med en '?' (spørgsmålstegn). Når du bruger tasten SAS i følgende T-SQL-script, skal du *fjerne linjeafstanden '?'*. Ellers kan din indsats blokeres af sikkerhed.


&nbsp;


```
---- TODO: First, run the PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.


SET NOCOUNT ON;
GO


----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO


CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO


------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).


IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO


IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO


CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO


------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO


------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO


ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO


-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO


-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


&nbsp;


Hvis destinationen ikke vedhæfte, når du kører, skal du stop og genstart sessionen begivenhed:


```
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


&nbsp;


## <a name="output"></a>Output


Når Transact-SQL-script er fuldført, skal du klikke på en celle under kolonneoverskriften **event_data_XML** . En **<event>** element vises som viser en UPDATE-sætning.

Her er et **<event>** element, der blev oprettet under testen:


&nbsp;


```
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

&nbsp;


Det foregående Transact-SQL-script bruges Systemfunktionen følgende til at læse event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)


En gennemgang af avancerede indstillinger for visning af data fra udvidede begivenheder kan hentes her:

- [Avanceret visning af Target Data fra udvidede begivenheder](http://msdn.microsoft.com/library/mt752502.aspx)

&nbsp;


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konvertering af kodeeksemplet til at køre på SQL Server


Antag, at du vil køre den foregående Transact-SQL-eksempel på Microsoft SQL Server.


- For enkel, vil du vil erstatte helt brug af objektbeholderen Azure-lager med en enkelt fil som **C:\myeventdata.xel**. Filen skal skrives til den lokale harddisk på den computer, vært for SQL Server.


- Du skal ikke alle slags Transact-SQL-sætningerne for **oprette MASTER nøgle** og **Oprette LEGITIMATIONSOPLYSNINGER**.


- I sætningen **Oprette BEGIVENHED SESSION** i dets **Tilføje mål** -delsætning, du vil erstatte på Http-værdi, der er tildelt har foretaget **filnavn =** med en fulde sti som **C:\myfile.xel**.
 - Ingen Azure-lager konto skal være involveret.


## <a name="more-information"></a>Få mere at vide


Du kan finde flere oplysninger om konti og beholdere i tjenesten Azure-lager, i:

- [Sådan bruger du Blob-lager fra .NET](../storage/storage-dotnet-how-to-use-blobs.md)
- [Navngive og henvisninger til beholdere, BLOB og Metadata](http://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Arbejde med rodobjektbeholderen](http://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lektion 1: Oprette en politik for lagrede access og en delt adgang signatur på en Azure objektbeholder](http://msdn.microsoft.com/library/dn466430.aspx)
    - [Lektion 2: Oprette en SQL Server-legitimationsoplysninger ved hjælp af en delt adgang signatur](http://msdn.microsoft.com/library/dn466435.aspx)




<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

