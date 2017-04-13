<properties
    pageTitle="Løse en SQL-forbindelsesfejl, midlertidig fejl | Microsoft Azure"
    description="Lær at foretage fejlfinding af diagnosticere og forhindre en SQL-forbindelsesfejl eller midlertidige fejl i Azure SQL-Database. "
    keywords="SQL-forbindelse, forbindelsesstreng, forbindelsesproblemer, midlertidig fejl, forbindelsesfejl"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="daleche"/>


# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Fejlfinding i forbindelse med, diagnosticere og forhindre SQL forbindelsesfejl og midlertidige fejl for SQL-Database

I denne artikel beskrives, hvordan du kan forhindre, fejlfinding i forbindelse med, diagnosticere og reducere forbindelsesfejl og midlertidige fejl, som klientprogrammet opstår, når det skal arbejde sammen med Azure SQL-Database. Lær, hvordan du konfigurerer forsøg, opbygge forbindelsesstrengen og justere andre indstillinger for forbindelsen.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Midlertidige fejl midlertidige

En midlertidig fejl – også midlertidige fejl – har underliggende årsag, der snart løser sig selv. En lejlighedsvise årsag til midlertidige fejl er når Azure systemet hurtigt forskydes hardwareressourcer til bedre belastning forskellige arbejdsbelastninger. De fleste af disse ofte fuldført i mindre end 60 sekunder konfigureres igen begivenheder. Under denne konfigureres igen tidsperioden, skal du muligvis forbindelsesproblemer til Azure SQL-Database. Programmer, der opretter forbindelse til Azure SQL-Database skal være indbygget forventer disse midlertidige fejl, håndterer dem ved at implementere forsøg i deres kode i stedet for fremhævelse dem til brugere, som programfejl.

Hvis klientprogrammet bruger ADO.NET, fortalt dit program om midlertidige fejlen ved at kaste af en **SqlException**. Egenskaben **tal** kan sammenlignes med listen over midlertidige fejl i øverst del af emnet: [SQL fejlkoder for SQL-Database klientprogrammer](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Forbindelse kontra kommandoen

Du kan forsøge at oprette SQL-forbindelse eller oprette den igen, afhængigt af følgende:

* **En midlertidig fejl opstår under en forbindelse til at prøve**: forbindelsen skal forsøges igen efter forsinke i flere sekunder.

* **En midlertidig fejl opstår under en SQL-kommando**: kommandoen ikke bør forsøges med det samme. I stedet efter en forsinkelse, skal være frisk oprettet forbindelse. Vælg derefter kommandoen kan blive forsøgt igen.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

### <a name="retry-logic-for-transient-errors"></a>Retry-logik for forbigående fejl


Klientprogrammer, der forekomme en midlertidig fejl er mere robust, når de indeholder forsøg.


Når dit program kommunikerer med Azure SQL-Database via en 3 part-program, Forespørg med leverandøren, uanset om programmer indeholder forsøg for forbigående fejl.

<a id="principles-for-retry" name="principles-for-retry"></a>

#### <a name="principles-for-retry"></a>Principper for forsøg


- Et forsøg på at åbne en forbindelse skal blive forsøgt igen, hvis fejlen er midlertidige.


- En SQL SELECT-sætning, der opstår fejl med en midlertidig fejl bør ikke gentages direkte.
 - Oprette en ny forbindelse i stedet, og prøv Vælg.


- Når en SQL UPDATE-sætning ikke med en midlertidig fejl, oprettes et nyt forbindelse skal før OPDATERINGEN skal gentages.
 - Retry-logik skal sikre dig, at enten hele databasen posteringen fuldført, eller som den hele transaktion er annulleret.


#### <a name="other-considerations-for-retry"></a>Andre overvejelser ved forsøg


- En batchfil, der startes automatisk efter arbejdstid, og som fuldfører før morgen, kan vil risikere at meget tålmodig med længe tidsintervaller mellem forsøg dens prøv igen.


- En bruger interface program skal højde for menneskelig tendens til at give efter for lang ventetid.
 - Dog må løsningen ikke være til at gentage alle par sekunder, fordi denne politik kan mod oversvømmelse systemet med anmodninger.


#### <a name="interval-increase-between-retries"></a>Forøg intervallet mellem nye forsøg



Vi anbefaler, at du forsinkelse i 5 sekunder før din første forsøg. Prøver igen efter en forsinkelse, der er mindre end 5 sekunder risici sikkerhedsmæssige skytjenesten. For hver efterfølgende forsøg igen forsinkelsen kan vokse eksponentielt, op til maksimalt 60 sekunder.

En diskussion til *blokerer periode* for klienter, der bruger ADO.NET er tilgængelig i [SQL Server forbindelse gruppering af (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Du kan også angive det maksimale antal forsøg, før programmet afsluttes selv.


#### <a name="code-samples-with-retry-logic"></a>Eksempler på kode med forsøg


Kodeeksempler med forsøg i en række programmeringssprog, findes på:

- [Dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

#### <a name="test-your-retry-logic"></a>Teste din forsøg


Hvis du vil afprøve dine forsøg, skal du simulere eller medfører en fejl, end der kan rettes, mens programmet stadig er aktiv.


##### <a name="test-by-disconnecting-from-the-network"></a>Test ved at afbryde forbindelsen til netværket


En måde, du kan teste din forsøg er at afbryde klientcomputeren forbindelsen til netværket, mens der kører automatisk. Fejlen kan:

- **SqlException.Number** = 11001
- Meddelelse: "ingen værten kendes"


Som en del af den første forsøg på at dit program rette den rettelse af stavning, og derefter forsøge at oprette forbindelse.


Hvis du vil gøre dette praktiske, du tager stikket til din computer fra netværket før du starter programmet. Derefter genkende programmet en kørselstidspunktet parameter, der medfører, at programmet til:

1. Føje midlertidigt 11001 til sin liste over fejl skal du tænke på som forbigående.
2. Forsøge første forbindelsen på sædvanlig vis.
3. Når fejlen er taget, kan du fjerne 11001 på listen.
4. Få vist en meddelelse om brugeren til at slutte computeren til netværket.
 - Pause yderligere udførelse ved hjælp af metoden **Console.ReadLine** eller en dialogboks med knappen OK. Brugeren trykker på ENTER efter den computer, der er forbundet til netværket.
5. Forsøg igen at oprette forbindelse, du forventer at modtage succes.


##### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Test ved stavefejl databasenavnet, når der oprettes forbindelse


Programmet kan bevidst staver forkert brugernavn før første forbindelsesforsøg. Fejlen kan:

- **SqlException.Number** = 18456
- Meddelelse: "Login mislykkedes for bruger 'WRONG_MyUserName'."


Som en del af den første forsøg på at dit program rette den rettelse af stavning, og derefter forsøge at oprette forbindelse.


For at gøre dette praktiske, kunne dit program genkende en kørselstidspunktet parameter, der medfører, at programmet til:

1. Føje midlertidigt 18456 til sin liste over fejl skal du tænke på som forbigående.
2. Føje bevidst 'WRONG_' til brugernavnet.
3. Når fejlen er taget, kan du fjerne 18456 på listen.
4. Fjern 'WRONG_' fra brugernavnet.
5. Forsøg igen at oprette forbindelse, du forventer at modtage succes.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

### <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection parametre for forbindelse forsøg


Hvis klientprogrammet opretter forbindelse til klasse **System.Data.SqlClient.SqlConnection**til Azure SQL-Database ved hjælp af .NET Framework, skal du bruge .NET 4.6.1 eller nyere, så du kan udnytte dens forbindelse retry-funktion. Finde detaljerede oplysninger om funktionen [her](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Når du opretter [forbindelsesstreng](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) for objektet **SqlConnection** , bør du koordinere værdier blandt følgende parametre:

- ConnectRetryCount &nbsp; &nbsp; *(standard er 1. Området er 0 til 255.)*
- ConnectRetryInterval &nbsp; &nbsp; *(standard er 1 sekund. Område er 1 til 60.)*
- Forbindelsen afbrydes efter &nbsp; &nbsp; *(standard er 15 sekunder. Området er 0 gennem 2147483647)*


Dine valgte værdier skal specifikt, foretage de følgende lighedstype: SAND:

- Forbindelsen afbrydes efter = ConnectRetryCount * ConnectionRetryInterval

Eksempelvis hvis antallet = 3 og interval = 10 sekunder, timeout for en af kun 29 sekunder ikke vil helt giver systemet have tid til dens 3 og afsluttende forsøg på at oprette forbindelse: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-versus-command"></a>Forbindelse kontra kommandoen


Parametrene **ConnectRetryCount** og **ConnectRetryInterval** lade **SqlConnection** objektet Prøv at oprette forbindelse igen uden fortæller eller Generer dit program, som kontrollen returneres til dit program. Gentagelserne kan forekomme i følgende situationer:

- mySqlConnection.Open metode opkald
- mySqlConnection.Execute metode opkald

Der findes en subtlety. Hvis der opstår en midlertidig fejl, mens din *forespørgsel* udføres, **SqlConnection** objektet Prøv ikke at oprette forbindelse igen, og det selvfølgelig forsøger ikke igen på din forespørgsel. Dog kontrollerer **SqlConnection** meget hurtigt forbindelse før afsendelse af din forespørgsel til udførelse af. Hvis Hurtig kontrol registreres et problem med netværksforbindelsen, forsøg **SqlConnection** handlingen Opret forbindelse. Hvis vil afsendelsen lykkes, har du forespørger er sendt til udførelse af.


#### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Skal ConnectRetryCount kombineres med programmet forsøg?

Antag, at dit program har robust brugerdefineret forsøg. Det kan gentagne handlingen Opret forbindelse 4 gange. Hvis du tilføjer **ConnectRetryInterval** og **ConnectRetryCount** = 3 til din forbindelsesstreng, du vil øge antal forsøg til 4 * 3 = 12 forsøg. Du kan ikke til hensigt en høj antallet af forsøg.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-azure-sql-database"></a>Forbindelser til Azure SQL-Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Forbindelse: Forbindelsesstreng


Det er nødvendigt til at oprette forbindelse til Azure SQL-Database forbindelsesstrengen er en smule anderledes end strengen til at oprette forbindelse til Microsoft SQL Server. Du kan kopiere forbindelsesstrengen for databasen fra [Azure-portalen](https://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Forbindelse: IP-adresse


Du skal konfigurere SQL-databaseserver for at acceptere kommunikation fra IP-adressen på den computer, der er vært klientprogrammet. Du kan gøre dette ved at redigere indstillingerne for firewallen via [Azure-portalen](https://portal.azure.com/).


Hvis du glemmer at konfigurere IP-adressen, mislykkes dit program med en praktiske fejlmeddelelse om, at det er nødvendigt IP-adressen.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Finde flere oplysninger: [Sådan: konfigurere firewall-indstillinger på SQL-Database](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Forbindelse: porte


Du skal typisk kun sikre, at port 1433 er åbne for udgående kommunikation på den computer, der er vært du-klientprogrammet.


For eksempel når klientprogrammet hostes på en Windows-computer, Windows Firewall på værten giver dig mulighed at åbne port 1433:


1. Åbn Kontrolpanel
2. &gt;Alle elementer i Kontrolpanel
3. &gt;Windows Firewall
4. &gt;Avancerede indstillinger
5. &gt;Udgående regler
6. &gt;Handlinger
7. &gt;Ny regel


Hvis klientprogrammet er placeret på en Azure VM (virtual machine), skal du læse:<br/>[Ud over 1433 for ADO.NET 4.5-porte og -version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md).


Finde baggrundsoplysninger om cofiguration af porte og IP-adresse: [Azure SQL-Database firewall](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Forbindelse: ADO.NET 4.6.1


Hvis dit program bruger ADO.NET klasser som **System.Data.SqlClient.SqlConnection** til at oprette forbindelse til Azure SQL-Database, anbefaler vi, at du bruger .NET Framework version 4.6.1 eller nyere.


ADO.NET 4.6.1:

- Til Azure SQL-Database er der øget pålidelighed, når du åbner en forbindelse ved hjælp af metoden **SqlConnection.Open** . Metoden **Åbn** omfatter nu bedste indsats forsøg mekanismer i svar til midlertidige fejl, fejl i forbindelsestimeout-perioden.
- Understøtter gruppering af forbindelse. Dette omfatter en effektiv kontrol, der fungerer objektet connection får dit program.



Når du bruger et connection-objekt fra en forbindelse gruppe, anbefales det, dit program midlertidigt afbryde forbindelsen, når du bruger den ikke straks. Åbne en forbindelse igen er ikke dyr er den måde, oprette en ny forbindelse.


Hvis du bruger ADO.NET 4.0 eller tidligere, anbefaler vi, at opgradere du til den seneste ADO.NET.

- Du kan [hente ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx)og November 2015.


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnosticering

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnosticering: Teste om funktioner kan oprette forbindelse


Hvis programmet går ned til at oprette forbindelse til Azure SQL-Database, er en diagnosticering mulighed at prøve at oprette forbindelse til en hjælpeprogram. Ideelt værktøjet vil oprette forbindelse ved hjælp af det samme bibliotek, der bruger dit program.


På en hvilken som helst Windows-computer, kan du prøve disse værktøjer:

- SQL Server Management Studio (ssms.exe), som opretter forbindelse ved hjælp af ADO.NET.
- sqlcmd.exe, som opretter forbindelse ved hjælp af [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Når forbindelse, du kontrollere, om en kort SQL udvælgelsesforespørgsel fungerer.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnosticering: Kontrollere de åbne porte


Antag, at du har mistanke om, at forbindelsen forsøg mislykkes på grund af problemer med port. Du kan køre et program, der-rapporter baseret på port konfigurationerne på din computer.


Følgende funktioner kan være nyttige på Linux:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`
 - (Ændre værdien eksempel for at være din IP-adresse).


I Windows [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) kan utility være nyttige. Her er en eksempel udførelse, forespørges port situation på en Azure SQL-databaseserver, og som blev Kør på en bærbar computer:


```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnosticering: Logge din fejl


Nogle gange bedst diagnosticeres et forbigående problem ved hjælp af genkendelse af et generelt mønster over dage eller uger.


Kunden kan hjælpe dig i en diagnose ved at logge alle fejl. Du kan muligvis koordinere logposter med Fejldata Azure SQL-Database logger selve internt.


Enterprise bibliotek 6 (EntLib60) indeholder .NET administrerede klasser til at hjælpe med at logføring:

- [5 – lige så nemt som falder fra en logfil: Brug af logføring programmet blokering](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnosticering: Undersøge systemets logfiler for fejl


Her er nogle Transact-SQL SELECT-sætninger, der forespørgselslogge af fejl og andre oplysninger.


| Forespørgsel med logfiler | Beskrivelse |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | Visningen [sys.event_log](http://msdn.microsoft.com/library/dn270018.aspx) indeholder oplysninger om enkelte begivenheder, herunder nogle, der kan medføre midlertidige fejl eller connectivity mislykkede.<br/><br/>Ideelt set kan du justere **start_time** eller **end_time** værdierne med oplysninger om når klientprogrammet stødte på problemer.<br/><br/>**TIP:** Du skal oprette forbindelse til **master** databasen til at køre dette. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | Visningen [sys.database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) indeholder aggregeret optællinger for de typer af begivenhed til yderligere diagnosticering.<br/><br/>**TIP:** Du skal oprette forbindelse til **master** databasen til at køre dette. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnosticering: Søge efter problemet hændelser i logfilen for SQL-Database


Du kan søge efter poster om problemet begivenheder Log af Azure SQL-Database. Prøv følgende Transact-SQL SELECT-sætning i den **overordnede** database:


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Et par returnerede rækker fra sys.fn_xe_telemetry_blob_target_read_file


Næste er, hvad en returnerede række kan se ud. De null-værdier vises er ofte ikke null-værdier i andre rækker.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6


Enterprise bibliotek 6 (EntLib60) er en struktur af .NET klasser, der hjælper dig med at implementere robust klienter af skytjenester, hvoraf den ene er tjenesten Azure SQL-Database. Du kan finde emner dedikeret til hvert område, hvor der kan hjælpe EntLib60 ved at besøge:

- [Enterprise Library 6 – April 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Forsøg til håndtering af midlertidige fejl er et område, hvor EntLib60 kan hjælpe:

- [4 – perseverance, hemmeligt af alle Triumphs: Brug af midlertidige fejl håndtering af programmet blokering](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


> [AZURE.NOTE] Koden til EntLib60 er tilgængelig for offentlige [hente](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft har ingen planer om at foretage yderligere funktionsopdateringer eller vedligeholdelse opdateringer til EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60 klasser for forbigående fejl, og prøv igen


Følgende EntLib60 klasser er særligt nyttige til forsøg. Alle dette er eller er yderligere under navneområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

*I navneområdet* *Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling* *:*

- **RetryPolicy** klasse
 - **ExecuteAction** metode


- **ExponentialBackoff** klasse


- **SqlDatabaseTransientErrorDetectionStrategy** klasse


- **ReliableSqlConnection** klasse
 - **ExecuteCommand** metode


I navneområdet **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** klasse

- **NeverTransientErrorDetectionStrategy** klasse


Her er links til oplysninger om EntLib60:

- Gratis [Reserver Download: Developer's Guide til Microsoft Enterprise bibliotek, 2. Edition](http://www.microsoft.com/download/details.aspx?id=41145)

- Bedste fremgangsmåder: [Prøv igen generelle retningslinjer](../best-practices-retry-general.md) har en fremragende uddybende oplysninger om forsøg.

- NuGet download af [Enterprise Library - midlertidige håndtering af fejl programmet Bloker 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: Logføring blokering


- Logføring blokering er en meget fleksibel og konfigurerbar løsning, der gør det muligt at:
 - Oprette og gemme logmeddelelser i en lang række placeringer.
 - Kategorisere og filtrere meddelelser.
 - Indsamle kontekstafhængige oplysninger, der er nyttigt til fejlfinding og sporing samt for overvågning og generelle logføring krav.


- Logføring blokering abstracts logføringsfunktionen fra destinationen, log, så programkoden er ensartet, uanset placeringen af og type i destination logføring store.


Yderligere oplysninger finder du: [5 – som nemt som falder fra et Log: Brug af logføring programmet blokering](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>EntLib60 IsTransient metode-kildekode


Dernæst skal er fra klassen **SqlDatabaseTransientErrorDetectionStrategy** C#-kildekode for metoden **IsTransient** . Koden oplyser, hvilke der blev betragtet som skal være midlertidige og værd at forsøg og April 2013.

Flere **//comment** linjer er blevet fjernet fra denne kopi til at fremhæve læsbarhed.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## <a name="next-steps"></a>Næste trin

- Fejlfinding i forbindelse med andre almindelige problemer med mailforbindelsen Azure SQL-Database, gå til [fejlfinding af forbindelsesproblemer til Azure SQL-Database](sql-database-troubleshoot-common-connection-issues.md).

- [SQL Server-forbindelse (ADO.NET)-gruppering](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying* er en Apache 2.0 licens generelle forsøger bibliotek, der er skrevet i **Python**til at forenkle opgaven med at tilføje flere forsøg funktionsmåde til stort set alt.](https://pypi.python.org/pypi/retrying)
