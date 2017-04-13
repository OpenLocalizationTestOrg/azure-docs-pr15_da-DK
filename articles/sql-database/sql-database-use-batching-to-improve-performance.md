 <properties
    pageTitle="Sådan bruges samling til at forbedre ydeevne i programmet Azure SQL-Database"
    description="Emnet indeholder dokumentation, der batching Databasefunktioner betydeligt imroves hastigheden og skalerbarhed af programmerne Azure SQL-Database. Selvom disse batching teknikker arbejder til en SQL Server-database, er fokus i denne artikel om Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Sådan bruges samling til at forbedre ydeevnen i SQL-Database-program

Samling handlinger til Azure SQL-Database betydeligt forbedrer ydeevne og skalerbarhed over dine programmer. Den første del af denne artikel omhandler for at forstå fordelene, nogle eksempler på testresultater, som sammenligner sekventielle og batchopdatering anmodninger om til en SQL-Database. Resten af artiklen viser teknikker, scenarier og overvejelser kan hjælpe dig med at bruge samling korrekt i Azure-programmer.

**Forfattere**: Jason Roth, Silvano Coriani, Trent Swanson (fuld skala 180 Inc.)

**Korrekturlæsere**: Conor CUNNINGHAM Formand, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>Hvorfor er samling vigtige for SQL-Database?
Samling opkald til en ekstern tjeneste er en lang række velkendte strategi for at øge ydeevne og skalerbarhed. Der er fast behandling omkostninger til en hvilken som helst interaktion med en ekstern tjeneste, såsom serialisering, netværksoverførsel og deserialisering. Emballagen mange separat transaktioner i et enkelt batch minimerer disse omkostninger.

I denne artikel vil vi undersøge forskellige SQL-Database samling strategier og scenarier. Selvom disse strategier er også vigtige for lokale programmer, der bruger SQL Server, er der flere årsager til fremhævning i brug af samling for SQL-Database:

- Er der potentielt større netværksventetid i at få adgang til SQL-Database, især hvis du får adgang til SQL-Database fra uden for det samme Microsoft Azure-datacenter.
- SQL-Database multiprofiler karakteristika betyder, at effektiviteten i forbindelse med data access lag svarer til den overordnede skalerbarhed af databasen. SQL-Database skal forhindre, at en enkelt lejer/bruger monopoliserer databaseressourcer til skade for andre lejere. Som svar på forbrug over foruddefinerede kvoter SQL-Database reducere overførselshastighed eller svare med (throttling) undtagelser. Effektiviteten, såsom samling, gør det muligt at udføre mere arbejde på SQL-Database før at kontakte disse begrænsninger. 
- Samling er også effektive til arkitekturer, der bruger flere databaser (sharding). Effektiviteten af din interaktion med hver database enhed er stadig en vigtig faktor i din overordnede skalerbarhed. 

En af fordelene ved at bruge SQL-Database er, at du ikke behøver at administrere servere, der hoster databasen. Men denne administrerede infrastruktur også betyder, at du skal overveje databasen optimeringer anderledes. Du kan ikke længere se for at forbedre database hardware- eller netværksproblemer infrastrukturen. Microsoft Azure styrer disse miljøer. Det primære område, du kan styre er, hvordan dit program arbejde sammen med SQL-Database. "Samling" er en af disse optimeringer. 

Den første del af papiret undersøger forskellige batching teknikker til .NET-programmer, der bruger SQL-Database. De sidste to afsnit dækker samling retningslinjer og scenarier.

## <a name="batching-strategies"></a>Samling strategier

### <a name="note-about-timing-results-in-this-topic"></a>Bemærkning om tidsindstilling resultater i dette emne
>[AZURE.NOTE] Resultaterne er ikke benchmarks, men er beregnet til at vise **relative ydeevne**. Tidsindstillinger er baseret på et gennemsnit af mindst 10 test kører. Handlinger er indsættes i en tom tabel. Disse test blev målt pre-version 12, og de nødvendigvis svarer ikke til overførsel, som du kan støde ind i en version 12-database ved hjælp af de nye [service niveauer](sql-database-service-tiers.md). Relative fordelen batching metode bør være tilsvarende.

### <a name="transactions"></a>Transaktioner
Ser underlig til at starte en gennemgang af samling af omhandler transaktioner. Men brugen af klientsiden transaktioner har en lille serversiden batching effekt, der forbedrer ydeevnen. Og transaktioner kan tilføjes med kun nogle få kodelinjer, så de er en hurtig måde at forbedre ydeevnen af sekventielle handlinger.

Overvej følgende C# kode, der indeholder en sekvens af Indsæt, og Opdater handlinger på en enkelt tabel.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Følgende kode i ADO.NET udfører sekventielt disse handlinger.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

Den bedste måde at optimere denne kode er at implementere en form for klientsiden samling af disse opkald. Men der er en nem måde at øge ydeevnen for denne kode med blot ombryde rækkefølgen af opkald i en transaktion. Her er den samme kode, der bruger en transaktion.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Transaktioner bliver faktisk brugt i begge af disse eksempler. I det første eksempel er hvert enkelt opkald en implicit transaktion. I det andet eksempel ombrydes en eksplicit transaktion alle på opkald. Per i dokumentationen til den [Skriv baseret transaktionslog](https://msdn.microsoft.com/library/ms186259.aspx)trækkes logposter til disken, når transaktionen er bekræftet. Så ved at medtage flere opkald i en transaktion, kan skrivning til transaktionsloggen til forsinke indtil posteringen er anvendt. Det betyder, aktiverer du samling for skriver til serverens transaktionslog.

I følgende tabel vises nogle ad hoc-testresultater. Testene, der udføres samme sekventielle indsættes med og uden transaktioner. For flere perspektiv kørte det første sæt af test fra en fjernplacering fra en bærbar computer til databasen i Microsoft Azure. Det andet sæt af test kørte fra en skybaseret tjeneste og database, at begge været bosiddende i det samme Microsoft Azure-datacenter (vest USA). Følgende tabel viser varigheden i millisekunder af sekventielle indsættes med og uden transaktioner.

**Lokalt til Azure**:

| Handlinger | Ingen transaktion (ms) | Transaktions (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure til Azure (samme datacenter)**:

| Handlinger | Ingen transaktion (ms) | Transaktions (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Resultaterne er ikke benchmarks. Se [bemærkning om tidsindstilling resultater i dette emne](#note-about-timing-results-in-this-topic).

Baseret på de forrige testresultater, reduceres ombryde en enkelt handling i en transaktion faktisk ydeevnen. Men som du øger antallet af handlinger i en enkelt transaktion, bedre ydeevne mere bliver markeret. Ydeevnen forskellen er også mere tydelige, når der optræder alle handlinger i Microsoft Azure-datacenter. Øget forsinkelse på ved hjælp af SQL-Database fra uden for Microsoft Azure-datacenteret overskygger ydeevnen ved at bruge transaktioner.

Selvom brugen af transaktioner kan øge ydeevnen, kan du fortsætte med at [overholde bedste fremgangsmåder til transaktioner og forbindelser](https://msdn.microsoft.com/library/ms187484.aspx). Holde posteringen så kort som muligt, og Luk forbindelsen til databasen, når arbejdet, der er fuldført. Den ved hjælp af sætningen i det forrige eksempel sikrer, at forbindelsen afbrydes, når efterfølgende programkode blokering er fuldført.

Det forrige eksempel viser, at du kan føje en lokal transaktion til en hvilken som helst ADO.NET kode med to linjer. Transaktioner tilbyder en hurtig måde at forbedre ydeevnen for kode, der gør sekventielle Indsæt, opdatere og slette handlinger. Overvej dog at ændre koden kortet yderligere, kan du udnytte klientsiden samling som tabelværdier parametre for den hurtigste ydeevne.

Du kan finde flere oplysninger om transaktioner i ADO.NET, [Lokale transaktioner i ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Tabelværdier parametre
Tabelværdier parametre understøtter brugerdefinerede tabeltyper som parametre i Transact-SQL-sætninger, lagrede procedurer og funktioner. Denne klientsiden batching metode kan du sende flere rækker med data i parameteren tabelværdier. For at bruge tabelværdier parametre skal du først definere tabeltypen. Følgende Transact-SQL-sætning opretter en tabeltype med navnet **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

I kode, skal oprette du en **DataTable** med nøjagtige samme navne og typer af tabeltypen. Overføre denne **DataTable** i en parameter i en tekstforespørgsel eller gemt procedure Calls. I følgende eksempel viser denne teknik:

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

I det forrige eksempel, indsætter objektet **SqlCommand** rækker fra en tabelværdier parameter **@TestTvp**. Tidligere oprettet **DataTable** objektet er tildelt til denne parameter med metoden **SqlCommand.Parameters.Add** . Samling indsættes i et opkald betydeligt øger ydeevnen over sekventielle indsættes.

Bruge en lagret procedure for at forbedre det forrige eksempel yderligere, i stedet for en tekstbaserede kommando. Følgende Transact-SQL-kommando opretter en lagret procedure, der tager parameteren **SimpleTestTableType** tabelværdier.

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Ændre angivelsen **SqlCommand** objekt i det forrige eksempel koden følger.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

I de fleste tilfælde har tabelværdier parametre tilsvarende eller bedre ydeevne end andre batching teknikker. Tabelværdier parametre, er ofte bedre, fordi de er mere fleksible end andre indstillinger. For eksempel tillade andre teknikker, såsom SQL flere kopier kun indsættelse af nye rækker. Men med tabelværdier parametre, du kan bruge logik i den lagrede procedure til at bestemme, hvilke rækker der er opdateringer, og hvilke der er indsætter. Tabeltypen kan også redigeres for at indeholde kolonnen "Handling", der angiver, om den angivne række skal indsættes, opdateres eller slettes.

Følgende tabel viser ad hoc-testresultater for brug af tabelværdier parametre i millisekunder.

| Handlinger | Lokalt til Azure (ms)  | Azure samme datacenter (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Resultaterne er ikke benchmarks. Se [bemærkning om tidsindstilling resultater i dette emne](#note-about-timing-results-in-this-topic).

Ydeevnen fra samling er umiddelbart synlige. I den forrige sekventielle test tog 1000 handlinger 129 sekunder uden for datacenteret og 21 sekunder fra inden for datacenteret. Men med tabelværdier parametre 1000 handlinger tage kun 2.6 sekunder uden for datacenteret og 0,4 sekunder i datacenteret.

Du kan finde flere oplysninger om tabelværdier parametre, [Table-Valued parametre](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>SQL flere kopier
SQL flere kopi er en anden måde at indsætte store mængder data i en destinationsdatabasen. .NET-programmer kan bruge klassen **SqlBulkCopy** til at udføre flere indsættelser. **SqlBulkCopy** ligner i funktionen værktøjet, **Bcp.exe**eller Transact-SQL-sætningen, **Flere indsætte**. Følgende kodeeksempel viser, hvordan Sådan masseredigeres Kopiér rækkerne i kilden **DataTable**, tabel for at destinationstabellen i SQL Server, MinTabel.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Der er nogle tilfælde, hvor flere kopi er foretrukne over tabelværdier parametre. Se sammenligningen af tabelværdier parametre kontra indsætte flere handlinger i emnet [Table-Valued parametre](https://msdn.microsoft.com/library/bb510489.aspx).

Følgende ad hoc-testresultaterne viser ydeevnen for samling med **SqlBulkCopy** i millisekunder.

| Handlinger | Lokalt til Azure (ms)  | Azure samme datacenter (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | . 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Resultaterne er ikke benchmarks. Se [bemærkning om tidsindstilling resultater i dette emne](#note-about-timing-results-in-this-topic).

Langt bedre klassen **SqlBulkCopy** i mindre batchen størrelser, bruge tabelværdier parametre. Dog udføres **SqlBulkCopy** 12-31% hurtigere end tabelværdier parametre for testene af 1.000 og 10.000 rækker. **SqlBulkCopy** er et godt valg til batchopdelte indsættes som tabelværdier parametre, især, når der sammenlignes med ydeevnen for ikke-batches handlinger.

Du kan finde flere oplysninger om flere kopi i ADO.NET, [Flere kopier handlinger i SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Flere række parameteriseret indsætte sætninger
Et alternativ til mindre grupper er at oprette en stor parameteriseret INSERT-sætning, der indsættes flere rækker. Følgende kodeeksempel viser denne teknik.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

I dette eksempel er beregnet til at vise de grundlæggende koncepter. Et scenarie med mere realistisk vil gentage gennem de nødvendige enheder til at oprette forespørgselsstrengen og parametrene, kommandoen samtidigt. Du er begrænset til alt 2100 forespørgselsparametre, så dette begrænser det samlede antal rækker, der kan behandles på denne måde.

Følgende ad hoc-testresultaterne viser ydeevnen for denne type insert-sætning i millisekunder.

| Handlinger | Tabelværdier parametre (ms) | Én sætning Indsæt (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Resultaterne er ikke benchmarks. Se [bemærkning om tidsindstilling resultater i dette emne](#note-about-timing-results-in-this-topic).

Denne metode kan være lidt hurtigere til navne, der er mindre end 100 rækker. Selvom forbedring er lille, er denne teknik en anden mulighed, der muligvis fungerer godt i scenariet specifikt program.

### <a name="dataadapter"></a>DataAdapter
Klassen **DataAdapter** giver mulighed at redigere et **datasæt** objekt og derefter sende ændringerne som indsætte, opdatere og slette handlinger. Hvis du bruger **DataAdapter** på denne måde, er det vigtigt at huske, separat opkald foretages for hver entydige handling. For at forbedre ydeevnen, skal du bruge egenskaben **UpdateBatchSize** til antallet handlinger, som skal være batches ad gangen. Du kan finde yderligere oplysninger finder [Udføre batchen handlinger ved hjælp af DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Enhed framework
Enhed Framework understøtter i øjeblikket ikke samling. Forskellige udviklere i communityet har forsøgt at vise løsninger, som Tilsidesæt metoden **SaveChanges** . Men løsningerne, der er typisk komplekse og tilpasset til det program og datamodel. Denne funktion anmodning har enhed Framework codeplex projektet i øjeblikket en diskussion side. Du finder denne diskussion under [Design mødenoter – 2 August 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Fuldstændig føler vi, at det er vigtigt at tale om XML som en strategi for batching. Brug af XML har dog ingen fordele i forhold til andre metoder og visse ulemper. Tilgangen ligner tabelværdier parametre, men en XML-fil eller en streng, der overføres til en lagret procedure i stedet for en brugerdefineret tabel. Den lagrede procedure fortolker kommandoerne i den lagrede procedure.

Der er flere ulemper ved denne fremgangsmåde:

- Arbejde med XML kan være besværlige og fejl.
- Parsing af XML-filen på databasen, kan det være CPU-intensivt.
- Denne metode er langsommere end tabelværdier parametre i de fleste tilfælde.

Brug af XML for batchen forespørgsler anbefales ikke disse årsager.

## <a name="batching-considerations"></a>Overvejelser i forbindelse med "samling"
De følgende afsnit indeholder flere vejledning til brug af "samling" i programmerne på SQL-Database.

### <a name="tradeoffs"></a>Kompromiserne
Afhængigt af din arkitektur kan samling omfatte en fordeling ydeevne og fleksibilitet. For eksempel gennemgå det scenario, hvor din rolle uventet går ned. Hvis du mister én række af data, er mindre end virkningerne af at miste en stor mængde ikke er sendt rækker. Er der en større risiko, når du bufferen rækker inden du sender dem til databasen i en bestemt tidsramme.

På grund af denne fordeling evaluere typer handlinger, du batchen. Batch mere aggressivt (større batches og længere tid windows) med data, der er mindre kritiske.

### <a name="batch-size"></a>Batchstørrelse
I vores test var der typisk nogen fordel at opdele store batches i mindre dele. Faktisk udløste denne underinddeling ofte langsommere end afsendelse af en enkelt stor gruppe. For eksempel bør du overveje et scenarie, hvor du vil indsætte 1000 rækker. Tabellen nedenfor viser, hvor lang tid det tager tabelværdier parametre bruges til at indsætte 1000 rækker, når der er inddelt i mindre batches.

| Batchstørrelse | Gentagelser | Tabelværdier parametre (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Resultaterne er ikke benchmarks. Se [bemærkning om tidsindstilling resultater i dette emne](#note-about-timing-results-in-this-topic).

Du kan se, at den bedste ydeevne for 1000 rækker er at sende dem på én gang. Der opstod en lille ydeevnen at opdele en 10000 række batchen i to batches med 5000 i andre test (ikke vist her). Men tabel skemaet for disse test er relativt simple, så du skal udføre test på bestemte data og batchen størrelser at bekræfte disse resultater.

En anden faktor skal du tænke på er, at hvis samlede batchen bliver for stor, kan SQL-Database begrænses og afslå at acceptere batchen. Test din scenarie for at afgøre, om der er en ideel batchstørrelse for de bedste resultater. Give batchstørrelse kan konfigureres på kørselstidspunktet til at aktivere hurtig justeringer baseret på ydeevnen eller fejl.

Til sidst skal saldo størrelsen på batchen med de risici, som er knyttet til samling. Hvis der er midlertidige fejl eller rollen mislykkes, skal du overveje at konsekvenserne af prøver igen eller for at miste dataene i batchen.

### <a name="parallel-processing"></a>Parallel behandling
Hvad nu, hvis du oprindeligt brugte for fremgangsmåden for at reducere batchstørrelsen, men bruges flere tråde til at udføre arbejdet? Vores test viste igen, flere mindre flertrådet batches typisk udføres dårligere end et enkelt større batch. Følgende test forsøger at indsætte 1000 rækker i en eller flere parallelle batches. Denne test viser, hvordan flere samtidige batches faktisk nedsat ydeevne.

| Batchstørrelse [gentagelser] | To tråde (ms) | Fire tråde (ms) | Seks tråde (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Resultaterne er ikke benchmarks. Se [bemærkning om tidsindstilling resultater i dette emne](#note-about-timing-results-in-this-topic).

Der er flere mulige årsager til forringet ydeevne på grund af parallelitet:

- Der findes flere samtidige netværk kald i stedet for ét.
- Flere handlinger i en enkelt tabel kan medføre konflikt og blokerer.
- Der er udgifter i forbindelse med flere tråde.
- Udgifter til åbning af flere forbindelser opvejer fordelen parallel behandling.

Hvis du tilpasser forskellige tabeller eller databaser, er det muligt at se nogle få med denne strategi ydeevne. Database sharding eller oprette samlinger der ville være et scenario for denne metode. Sharding bruger flere databaser og dirigerer andre data til hver database. Hvis hver lille batch skal til en anden database, kan det være mere effektivt at udføre derefter handlingerne parallelt. Forøgelse i ydeevnen er dog ikke væsentlige nok til at bruge som grundlag for en beslutning til at bruge databasen sharding i din løsning.

I nogle design kan parallel udførelse af mindre batches medføre forbedret gennemløb af forespørgsler i et system belastning. I dette tilfælde, selvom det er hurtigere at behandle en enkelt større batchen, kan behandling af flere batches parallelt være mere effektivt.

Hvis du ønsker at bruge parallel udførelse, kan du overveje at styre det maksimale antal arbejdstråde. Kan medføre et mindre tal mindre konflikt og en hurtigere kørselstid. Overvej også at den ekstra belastning, som dette placerer på destinationsdatabasen både i forbindelser og transaktioner.

### <a name="related-performance-factors"></a>Relaterede ydeevne faktorer
Typisk retningslinjer for databasens ydeevne påvirker også samling. For eksempel indsætte reduceres ydeevnen for tabeller, der har en stor primær nøgle eller mange ikke-grupperede indeks.

Hvis tabelværdier parametre bruger en lagret procedure, kan du bruge kommandoen **SET NOCOUNT ON** i starten af proceduren. Denne sætning undertrykker returnering af antallet af de pågældende rækker i proceduren. Dog i vores test brugen af **SET NOCOUNT ON** enten har ingen effekt eller nedsat ydeevne. Gemt testproceduren var enkelt med en enkelt **indsætte** kommando fra parameteren tabelværdier. Det er muligt, at mere komplekse lagrede procedurer ville få glæde af denne erklæring. Men ikke forudsætter, at føje **SET NOCOUNT ON** til den lagrede procedure automatisk forbedrer ydeevnen. For at forstå effekten, skal du teste den lagrede procedure med og uden sætningen **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Samling scenarier
I nedenstående afsnit beskrives, hvordan du bruger tabelværdier parametre i tre scenarier i programmet. Det første scenarie viser, hvordan bufferen og samling kan fungere sammen. Det andet scenario forbedrer ydeevnen ved at udføre handlinger master-detaljer i et enkelt lagret procedure opkald. Det endelige scenario viser, hvordan du bruger tabelværdier parametre i handlingen "UPSERT".

### <a name="buffering"></a>Bufferen
Selvom der er nogle scenarier, der er indlysende kandidat til samling, er der mange situationer, der kan drage fordel af samling af forsinkede behandling. Forsinkede behandling bærer dog en større risiko, at dataene er tabt i tilfælde af en uventet fejl. Det er vigtigt at forstå denne risiko og overveje konsekvenserne.

For eksempel bør du overveje et webprogram, der registrerer navigationsoversigten over hver enkelt bruger. Programmet kan gøre en database, der ringer for at optage brugerens sidevisning i hver sideanmodning. Men højere ydeevne og skalerbarhed opnås ved bufferen brugernes navigation aktiviteter og derefter sende disse data til databasen i batches. Du kan udløse database opdateringen ved forløbet tid og/eller bufferstørrelse. En regel kan for eksempel angive, at batchen skal behandles efter 20 sekunder, eller når bufferen når 1000 elementer.

Følgende kodeeksempel bruger [Aktiv filtypenavne - modtagelse](https://msdn.microsoft.com/data/gg577609) til at behandle bufferlagrede hændelser anledning en overvågning klasse. Når bufferen udfylder eller et timeout er nået, mængde brugerdata sendes til databasen med en tabelværdier parameter.

Klassen følgende NavHistoryData modeller navigation brugeroplysninger. Den indeholder grundlæggende oplysninger som bruger-id, URL-adressen adgang til og access tid.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

Klassen NavHistoryDataMonitor er ansvarlig for bufferen navigation brugerdata til databasen. Den indeholder en metode, RecordUserNavigationEntry, som reagerer ved at løfte en **OnAdded** begivenhed. Følgende kode viser parametre logik, der bruger modtagelse til at oprette en opbevaringstid af websteder, der er baseret på begivenheden. Det derefter abonnerer på denne opbevaringstid af websteder med metoden bufferen. Overbelastning angiver, at bufferen skal sendes hver 20 sekunder eller 1000 poster.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Handleren konverterer alle bufferlagrede elementer til en tabelværdier type og overfører derefter denne type til en lagret procedure, der behandler batchen. Følgende kode viser fuldført definitionen for både NavHistoryDataEventArgs og NavHistoryDataMonitor klasser.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Hvis du vil bruge denne bufferlagring klasse, opretter programmet et statisk NavHistoryDataMonitor objekt. Hver gang en bruger har adgang til en side, kalder programmet metoden NavHistoryDataMonitor.RecordUserNavigationEntry. Bufferlagring logik skrider frem til tager sig af afsendelse af disse poster til databasen i batches.

### <a name="master-detail"></a>Overordnede detaljerede
Tabelværdier parametre er nyttige til enkle Indsæt scenarier. Det kan dog være større udfordring at batchen indsættes, der involverer mere end én tabel. "Overordnet/detaljeret" scenariet er et godt eksempel. Den overordnede tabel identificerer det primære objekt. En eller flere detaljer tabeller indeholder flere data om objektet. I dette scenarie skal gennemtvinge fremmed nøgle-relationer forholdet mellem oplysninger til en entydig master enhed. Overvej en forenklet version af en PurchaseOrder tabel og dens tilknyttede OrderDetail tabel. Følgende Transact-SQL opretter PurchaseOrder tabel med fire kolonner: ordre-id, OrderDate, kunde-id og Status.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Hver ordre indeholder en eller flere Produktkøb. Disse oplysninger registreres i tabellen PurchaseOrderDetail. Følgende Transact-SQL opretter tabellen PurchaseOrderDetail med fem kolonner: ordre-id, OrderDetailID, ProductID, UnitPrice og OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

Kolonnen ordre-id i tabellen PurchaseOrderDetail skal referere til en ordre fra tabellen PurchaseOrder. Følgende definitionen af en fremmed nøgle gennemtvinger denne begrænsning.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

For at kunne bruge tabelværdier parametre, skal du have en brugerdefineret tabeltype for hver måltabellen.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Definer en lagret procedure, der accepterer tabeller for disse filtyper. Denne fremgangsmåde kan et program til lokalt batchen et sæt af ordrer og ordreoplysninger i et enkelt opkald. Følgende Transact-SQL indeholder angivelsen fuldført lagret procedure for eksemplet køb rækkefølge.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

I dette eksempel lokalt definerede @IdentityLink tabel indeholder de faktiske ordre-id-værdier fra de nyligt indsatte rækker. Disse rækkefølge id'er er forskellig fra de midlertidige ordre-id-værdier i den @orders og @details tabelværdier parametre. Derfor den @IdentityLink tabel opretter derefter forbindelse Ordrenr værdierne fra den @orders -parameter til de reelle ordre-id-værdier for de nye rækker i tabellen PurchaseOrder. Når dette trin skal den @IdentityLink tabel kan lette indsætte ordreoplysninger med den faktiske ordre-id, der opfylder fremmed nøgle-begrænsningen.

Denne lagrede procedure kan bruges fra kode eller fra andre Transact-SQL-opkald. Se afsnittet tabelværdier parametre i dette dokument til et eksempel på en kode. Følgende Transact-SQL viser, hvordan du ringe til sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Denne løsning gør det muligt for hvert batch for at bruge et sæt af ordre-id-værdier, der starter med 1. Disse midlertidige ordre-id-værdier beskrive relationerne i batchen, men de faktiske ordre-id-værdier er fastsat på tidspunktet for handlingen Indsæt. Du kan køre de samme sætninger i det forrige eksempel flere gange og oprette entydige ordrer i databasen. Overvej at tilføje flere kode eller database logik, der forhindrer dublerede ordrer, når du bruger samling metode derfor.

I dette eksempel vises, kan batches endnu mere komplekse databasehandlinger, som overordnet detaljeret handlinger, ved hjælp af tabelværdier parametre.

### <a name="upsert"></a>UPSERT
En anden batching scenario omfatter samtidigt opdatere eksisterende rækker og indsætte nye rækker. Denne handling kaldes nogle gange handlingen "UPSERT" (Opdater + insert). I stedet for at foretage separat opkald til at indsætte og opdatere er sætningen FLET bedst egnet til denne opgave. Sætningen FLETNINGEN, kan udføre begge Indsæt og opdatere handlinger i et enkelt opkald.

Tabelværdier parametre kan bruges med sætningen FLET til at udføre opdateringer og indsættelser. For eksempel kan du overveje en forenklet medarbejdertabellen, der indeholder følgende kolonner: medarbejder-id, Fornavn, efternavn, SocialSecurityNumber:

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
I dette eksempel kan du bruge SocialSecurityNumber, er entydige til at udføre en fletning af flere medarbejdere. Først skal oprette den brugerdefinerede tabeltype:

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Dernæst skal oprette en lagret procedure eller skrive programkode, der bruger sætningen FLET til at udføre opdateringen og indsætte. I følgende eksempel bruges sætningen FLET på en tabelværdier parameter @employees, af typen EmployeeTableType. Indholdet af den @employees tabel ikke vises her.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Se dokumentation og eksempler på sætningen FLET kan finde flere oplysninger. Selvom det samme arbejde kan udføres i en flere trin, der er gemt procedure Calls med Frasorter Indsæt, og Opdater handlinger, sætningen FLET er mere effektivt. Database kode kan også oprette Transact-SQL-kald, som bruger sætningen FLET direkte uden brug af to database opkald til at indsætte og opdatere.

## <a name="recommendation-summary"></a>Anbefaling oversigt

Følgende liste indeholder en oversigt over de batching anbefalinger, der er beskrevet i dette emne:

- Brug bufferen og samling til at øge ydeevne og skalerbarhed over SQL Database-programmer.
- Forstå kompromiserne mellem samling/bufferen og fleksibilitet. Under en rolle fejl, kan risikoen for at miste en ikke-behandlede mængde vigtige data opveje ydeevne fordelen samling.
- Forsøge at holde alle kald til databasen i en enkelt datacenter for at reducere ventetid.
- Hvis du vælger en enkelt batching teknik, tilbyder tabelværdier parametre den bedste ydeevne og fleksibilitet.
- Følg disse generelle retningslinjer for hurtigste Indsæt ydeevnen, men teste scenariet:
    - Brug en enkelt parameteriseret for < 100 rækker, kommandoen Indsæt.
    - Bruge tabelværdier parametre for < 1000 rækker.
    - For > = 1000 rækker, bruge SqlBulkCopy.
- For opdatere og slette handlinger skal du følge tabelværdier parametre med lagrede procedure logik, der bestemmer den korrekte handling på hver række i parameteren tabel.
- Batchen størrelse retningslinjer:
    - Brug de største batchen størrelser, der giver mening for dit program og virksomhedens behov.
    - Saldo ydeevnen af store batches med risiciene for midlertidige eller katastrofal fejl. Hvad er vigtigt af forsøg eller tab af data i batchen? 
    - Teste den største batchstørrelse for at bekræfte, at SQL-Database, ikke afvise den.
    - Oprette konfigurationsindstillinger, der kontrolelement samling, som batchstørrelsen eller bufferlagring tidsramme. Disse indstillinger giver fleksibilitet. Du kan ændre Transaktionsbatchkørsler fremstilling uden geninstallation skytjenesten.
- Undgå parallel udførelse af navne, der kører på en enkelt tabel i en database. Hvis du vælger at opdele en enkelt batch på tværs af flere arbejdstråde, Kør test for at finde det perfekte antal tråde. Efter en uspecificeret grænseværdi flere tråde kan mindske ydeevnen i stedet for øge den.
- Overvej at bufferen på størrelse og tid som en metode til implementering af samling for flere scenarier.

## <a name="next-steps"></a>Næste trin

I denne artikel fokuserer på hvordan databasedesign og kodningssprog teknikker, der er relateret til samling kan forbedre dit program ydeevne og skalerbarhed. Men det er kun én faktor i din strategi for overordnede. Flere måder at forbedre ydeevne og skalerbarhed i [Azure SQL-Database ydeevne vejledning til enkelt databaser](sql-database-performance-guidance.md) og [pris og ydeevne overvejelser i forbindelse med en elastiske database puljen](sql-database-elastic-pool-guidance.md).
