<properties
   pageTitle="Introduktion til tidsmæssig tabeller i Azure SQL-Database | Microsoft Azure"
   description="Lær, hvordan du kommer i gang med at bruge tidsmæssig tabeller i Azure SQL-Database."
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="08/29/2016"
   ms.author="carlrab"/>

#<a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Introduktion til tidsmæssig tabeller i Azure SQL-Database

Tidsmæssig tabeller er en ny programmering funktion af Azure SQL-Database, der gør det muligt at spore og analysere en komplet oversigt over ændringer i dine data – uden brug af brugerdefinerede kodning. Tidsmæssig tabeller Hold data, der er tæt forbundet med tid kontekst, så gemte oplysninger kan fortolkes som gyldige kun inden for den specifikke periode. Denne egenskab for tidsmæssig tabeller giver mulighed for effektiv tidsbaserede analyse og få indsigt fra udvikling af data.

##<a name="temporal-scenario"></a>Tidsmæssig scenarie

I denne artikel illustreres trin for at udnytte tidsmæssig tabeller i et scenarie med programmet. Antag, at du vil registrere brugeraktivitet på et nyt websted, som er under udvikling fra bunden eller på et eksisterende websted, du vil udvide med bruger aktivitet analyser. I dette forenklet eksempel antager vi, at antallet af besøgte websider i en periode er en indikator, der skal registreres og overvåges i databasen websted, der er placeret på Azure SQL-Database. Formålet med historiske analysen af brugeraktivitet er at få input til omdesigne websted og give bedre oplevelse for besøgende.

Databasemodel for dette scenario er meget simpel – bruger aktivitet metrisk repræsenteres med en enkelt heltalsfelt **PageVisited**og registreres sammen med grundlæggende oplysninger om brugerprofilen. Desuden til klokkeslæt, baseret analyse, vil du holde flere rækker for hver bruger, hvor hver række repræsenterer antallet af sider, der er en bestemt bruger besøgt inden for et bestemt tidsrum.

![Skema](./media/sql-database-temporal-tables/AzureTemporal1.png)

Heldigvis behøver du ikke at sætte en hvilken som helst indsats i din app til at vedligeholde oplysningerne aktivitet. Med tidsmæssig tabeller, er denne proces automatisk - hvorved du får fuld fleksibilitet under design af webstedet og mere tid til at fokusere på selve dataanalyse. Det eneste, du skal gøre er at sikre, at **WebSiteInfo** tabel er konfigureret som [tidsmæssig system-versionsnummer](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). De nøjagtige trin til at udnytte tidsmæssig tabeller i dette scenarie beskrives nedenfor.

##<a name="step-1-configure-tables-as-temporal"></a>Trin 1: Konfigurere tabeller som tidsmæssig

Afhængigt af om du starter udvikling af nye eller opgradering af eksisterende program, kan du oprette tidsmæssig tabeller eller redigere eksisterende ved at tilføje tidsmæssig attributter. Generelt tilfælde scenariet kan være en blanding af disse to muligheder. Udfør disse handling med [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) eller et andet Transact-SQL development-værktøj.


> [AZURE.IMPORTANT] Det anbefales, at du altid bruger den nyeste version af Management Studio skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database. [Opdatere SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


###<a name="create-new-table"></a>Oprette ny tabel

Bruge kontekst menupunkt "Ny System-versionsnummer tabel" i SSMS Object Explorer til at åbne forespørgselseditor med en tidsmæssig tabel skabelon script og derefter bruge "Angiv værdier for Skabelonparametre" (Ctrl + Skift + M) til at udfylde skabelonen:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Valgte "tidsmæssig (System-versionsnummer)" tabelskabelon i SSDT, når du føjer nye elementer til projektets database. Der åbnes tabeldesign og giver dig mulighed at angive nemt tabellayoutet:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Du kan også en tidsmæssig Opret en tabel ved at angive Transact-SQL-sætningerne direkte, som vist i eksemplet nedenfor. Bemærk, at de obligatoriske elementer på hver tidsmæssig tabel definitionen af og delsætningen SYSTEM_VERSIONING med en reference til en anden brugertabel, der skal gemmes historiske række versioner:

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Når du opretter system-versionsnummer tidsmæssig tabel, oprettes der automatisk ledsagende oversigt over tabellen med standardkonfigurationen. Oversigt over standardtabel indeholder et grupperet B-træet indeks i kolonnerne (end, start) med siden komprimering aktiveret. Denne konfiguration er velegnet til størstedelen af scenarier, hvor tidsmæssig tabeller er bruges, især til [overvågning af data](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

I dette tilfælde har vi til formål at udføre tidsbaserede tendensanalyse over en længere data oversigt og med større datasæt, så lagerplads valg til tabellen historik er et grupperet columnstore indeks. En grupperet columnstore indeholder meget god komprimering og ydeevne til analytiske forespørgsler. Tidsmæssig tabeller giver dig mulighed for at konfigurere indeks på de aktuelle og tidsmæssig tabeller helt uafhængigt af hinanden. 

**Bemærk**: Columnstore indeks er kun tilgængelige i webtjenesten premium.

Følgende script viser, hvordan standardindeks på oversigtstabel kan ændres til grupperet columnstore:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Tidsmæssig tabeller er repræsenteret i Object Explorer med ikonet bestemte for nemmere at identificere, mens dens oversigtstabel vises som en underordnet node.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

###<a name="alter-existing-table-to-temporal"></a>Ændre eksisterende tabel til tidsmæssig

Lad os dækker det alternative scenario, hvor tabellen WebsiteUserInfo allerede findes, men ikke er beregnet til at gemme en oversigt over ændringer. I dette tilfælde kan du blot udvide eksisterende tabellen for at blive tidsmæssig, som vist i følgende eksempel:

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

##<a name="step-2-run-your-workload-regularly"></a>Trin 2: Køre arbejdsbelastningen regelmæssigt

Den største fordel af tidsmæssig tabeller er, at du ikke behøver at ændre eller tilpasse dit websted på nogen måde til at udføre registrering af ændringer. Når oprettet, fastholdes tidsmæssig tabeller transparent tidligere versioner af række, hver gang du udfører ændringer af dine data. 

For at kunne udnytte automatisk registrering af ændringer til dette bestemt scenarie, Lad os lige opdatere kolonne **PagesVisited** hver gang når brugeren slutter deres session på webstedet:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Det er vigtigt at Bemærk, at opdateringsforespørgslen ikke brug for at vide det nøjagtige klokkeslæt, når handlingen faktisk blev udført, eller hvordan historiske data bevares for fremtidige analyser. Begge aspekter håndteres automatisk af Azure SQL-Database. I følgende diagram vises, hvordan historik data oprettes på hver opdatering.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

##<a name="step-3-perform-historical-data-analysis"></a>Trin 3: Udføre historiske dataanalyse

Når tidsmæssig system-versionsstyring er aktiveret, er historiske dataanalyse nu kun én forespørgsel væk fra dig. I denne artikel giver vi et par eksempler, der adresse almindelige analyse scenarier - se alle detaljer, skal du undersøge forskellige indstillinger, der blev introduceret i delsætningen [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) .

For at se de øverste 10 brugere, der er sorteret efter antallet besøgte websider og timen siden skal du køre denne forespørgsel:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Du kan nemt ændre denne forespørgsel for at analysere websted besøg og en dag siden, en måned siden eller på en hvilken som helst sted i fortiden, du ønsker.

Brug eksemplet nedenfor til at udføre grundlæggende statistiske analyser for den forrige dag:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Sådan søger du efter en bestemt bruger aktiviteter inden for et bestemt tidsrum, delsætningen indeholdt:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Grafisk visualisering er især praktisk for tidsmæssig forespørgsler, som du kan få vist tendenser og brug i en intuitiv måde meget nemt:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

##<a name="evolving-table-schema"></a>Udvikling af tabelskema

Typisk, skal du ændre tidsmæssig tabelskema, mens du udfører app udvikling. Til det overføres skal du bare køre normalt ALTER TABLE-sætninger og Azure SQL-Database korrekt ændringer i oversigt over tabellen. Følgende script viser, hvordan du kan tilføje ekstra attribut til sporingsformål:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

På samme måde, kan du ændre definition af kolonne, mens arbejdsbyrden er aktiv:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Til sidst, kan du fjerne en kolonne, som du ikke behøver længere.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````
    
Du kan også bruge seneste [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) til at ændre tidsmæssig tabelskema, mens du har forbindelse til databasen (onlinetilstand) eller som en del af database-projekt (offlinetilstand).

##<a name="controlling-retention-of-historical-data"></a>Styre opbevaring af historiske data

Med system-versionsnummer tidsmæssig tabeller, kan oversigtstabel øge databasestørrelse i mere end almindelige tabeller. En stor og stadig stigende oversigtstabel kan være et problem, både på grund af ren lagerplads omkostninger samt stille en ydeevne skat på tidsmæssig forespørgsler. Det vil sige, udvikling af en dataopbevaringspolitik til administration af dataene i tabellen oversigt er et vigtigt aspekt af planlægning og administration af livscyklus for hver tidsmæssig tabel. Med Azure SQL-Database har du de følgende metoder til administration af historiske data i tabellen tidsmæssig:

- [Tabel partitionering](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
- [Oprydning i brugerdefineret Script](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

##<a name="next-steps"></a>Næste trin

Se [dokumentationen til MSDN](https://msdn.microsoft.com/library/dn935015.aspx)kan finde detaljerede oplysninger om tidsmæssig tabeller.
Besøg kanal 9 for at høre en [reelle kundernes tidsmæssig implementering succeshistorier](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) og se en [live tidsmæssig demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
