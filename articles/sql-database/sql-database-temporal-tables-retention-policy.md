<properties
   pageTitle="Administrere historiske data i tidsmæssig tabeller med opbevaringspolitik | Microsoft Azure"
   description="Lær at bruge tidsmæssig opbevaringspolitik til at holde historiske data under din kontrol."
   services="sql-database"
   documentationCenter=""
   authors="bonova"
   manager="drasumic"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sql-database"
   ms.date="10/12/2016"
   ms.author="bonova"/>

#<a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Administrere historiske data i tidsmæssig tabeller med opbevaringspolitik

Tidsmæssig tabeller kan øge databasestørrelse mere end almindelige tabeller, især hvis du bevarer historiske data for en længere periode. Det vil sige, opbevaringspolitik for historiske data er et vigtigt aspekt af planlægning og administration af livscyklus for hver tidsmæssig tabel. Tidsmæssig tabeller i Azure SQL-Database, der følger med til brugervenlige opbevaring ordning, der hjælper dig med at udføre denne opgave.

Tidsmæssig historik opbevaring kan være konfigureret på niveauet for individuelle tabel, som giver brugerne mulighed at oprette fleksible aldersfordelt politikker. Det er nemt at anvende tidsmæssig opbevaring: det kræver kun én parameter skal indstilles under tabel oprettelse eller skema ændring.

Når du definerer opbevaringspolitik, starter Azure SQL-Database regelmæssigt kontrollere, om der er historiske rækker, der er berettiget til automatisk Dataoprydning. Identifikation af matchende rækker og deres fjernelse fra tabellen historik forekomme transparent, i opgaven baggrund, der er planlagt og køre af systemet. Alder betingelse for tabelrækkerne historik tjekkes baseret på den kolonne, der repræsenterer SYSTEM_TIME udløb. Hvis opbevaringsperiode, for eksempel er indstillet til seks måneder, opfylder tabelrækker, der er berettiget til oprydning betingelsen, følgende:

````
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
````

I det foregående eksempel antages det, **ValidTo** kolonne svarer til slutningen af SYSTEM_TIME periode.

##<a name="how-to-configure-retention-policy"></a>Sådan konfigureres opbevaringspolitik?

Før du konfigurerer opbevaringspolitik for en tidsmæssig tabel, Kontrollér først om tidsmæssig historiske opbevaring er aktiveret *på niveauet for databasen*.

````
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
````

Database flaget **is_temporal_history_retention_enabled** er indstillet til til som standard, men brugerne kan ændre den med JUSTER DATABASE-sætning. Det er også automatisk angivet til fra efter [punkt i tiden gendannelsen](sql-database-point-in-time-restore-portal.md) handling. Hvis du vil aktivere tidsmæssig historik opbevaring oprydning for databasen, skal du udføre følgende sætning:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

> [AZURE.IMPORTANT] Selvom **is_temporal_history_retention_enabled** er slået fra, men udløses automatisk oprydning for udestående rækker ikke er tilfældet, kan du konfigurere opbevaring for tidsmæssig tabeller.


Opbevaringspolitik konfigureres under oprettelse af tabellen ved at angive værdien for parameteren HISTORY_RETENTION_PERIOD:

````
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
````

Azure SQL-Database kan du angive opbevaringsperiode ved hjælp af forskellige tidsenheder: dage, uger, måneder og år. Hvis HISTORY_RETENTION_PERIOD udelades, antages UENDELIG opbevaring. Du kan også bruge UENDELIG nøgleord eksplicit.

I nogle tilfælde kan du overveje at konfigurere opbevaring efter oprettelse af tabel eller for at ændre tidligere konfigureret værdi. Brug ALTER TABLE-sætningen i så fald:

````
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
````

> [AZURE.IMPORTANT]  Angive SYSTEM_VERSIONING til fra *bevarer ikke* opbevaring periode værdi. Angive SYSTEM_VERSIONING til ON uden HISTORY_RETENTION_PERIOD angivet eksplicit resultater i UENDELIG opbevaringsperioden.

For at gennemse aktuelle tilstand for opbevaringspolitikken, skal du bruge følgende forespørgsel, der forbinder tidsmæssig opbevaring aktivering af flag på niveauet for databasen med opbevaring perioder for enkelte tabeller:

````
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2   
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
````


##<a name="how-sql-database-deletes-aged-rows"></a>Hvordan sletter SQL-Database aldersfordelt rækker?

Oprydningsprocessen, afhænger af layoutet indeks i tabellen oversigt. Det er vigtigt at lægge mærke, der *kun oversigt over tabeller med et grupperet indeks (B-træet eller columnstore) kan have endelig opbevaringspolitik konfigureret*. Der oprettes en baggrund opgave for at udføre Dataoprydning i udestående for alle tidsmæssig tabeller med endelig opbevaringsperiode.
Oprydning logik for rowstore (B-træet) grupperet indekset sletter udestående række i mindre dele (op til 10 K) minimering trykket på databaselog og i/o-undersystem. Selvom oprydning logik anvender kræves der for B-træet indeks rækkefølgen af sletninger for de rækker, der er ældre end opbevaringsperiode kan sikres solidt. Det vil sige, *træder ikke i en hvilken som helst afhængighed af oprydning rækkefølge i dine programmer*.

Oprydning opgaven til grupperet columnstore fjerner hele [Rækkegrupper](https://msdn.microsoft.com/library/gg492088.aspx) på én gang (typisk indeholder 1 million af rækker, hver), som er meget effektiv, især hvis historiske data er oprettet i en høj tempo.

![Grupperet columnstore opbevaring](./media/sql-database-temporal-tables-retention-policy/cciretention.png)


Fremragende datakomprimering og effektiv opbevaring oprydning gør grupperet columnstore indeks perfekte valg for scenarier når arbejdsbelastningen genererer hurtigt høj mængde historiske data. Dette mønster er typisk for intensivt [transaktions behandling arbejdsbelastninger, der bruger tidsmæssig tabeller](https://msdn.microsoft.com/library/mt631669.aspx) for registrering af ændringer og overvågning, tendensanalyser eller IoT data indtagelse.

##<a name="index-considerations"></a>Overvejelser i forbindelse med indeks

Oprydning opgaven for tabeller med rowstore grupperet indeks kræver indeks, skal starte med den kolonne, der svarer SYSTEM_TIME udløb. Hvis der ikke findes sådanne indeks, kan du ikke konfigurere endelig opbevaringsperiode:

*Meddelelse 13765, Level 16, State 1 <br> </br> konfiguration endelig opbevaringsperiode på system-versionsnummer tidsmæssig tabel 'temporalstagetestdb.dbo.WebsiteUserInfo' mislykkedes, fordi oversigtstabel 'temporalstagetestdb.dbo.WebsiteUserInfoHistory' ikke indeholder påkrævet grupperet indeks. Overvej at oprette en grupperet columnstore eller B-træet indeks begyndende med den kolonne, der svarer til slutningen af SYSTEM_TIME periode i tabellen oversigt.*

Det er vigtigt at Bemærk, at den historiktabel, der er oprettet af Azure SQL-Database allerede har grupperet indeks, som er kompatibel for opbevaringspolitik. Hvis du forsøger at fjerne indekset i en tabel med endelig opbevaringsperiode, mislykkes handlingen med følgende fejl:

*Meddelelse 13766, Level 16, State 1 <br> </br> kan ikke slippe grupperet indekset 'WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory', fordi den bruges til automatisk oprydning af gamle data. Overvej at angive HISTORY_RETENTION_PERIOD til UENDELIG på den tilsvarende system-versionsnummer tidsmæssige tabel, hvis du vil slippe dette indeks.*

Oprydning på grupperet columnstore indekset fungerer optimalt, hvis historiske rækker er indsat i stigende rækkefølge (sorteret efter slutningen af kolonnen periode), hvilket er altid sag, når tabellen historik udfyldes udelukkende af SYSTEM_VERSIONIOING ordning. Hvis rækker i tabellen historik ikke er sorteret ved slutningen af perioden kolonne (hvilket kan være tilfældet, hvis du har overført eksisterende historiske data), skal du oprette grupperet columnstore indeks oven på B-træet rowstore indeks, der er sorteret korrekt, at opnå optimal ydeevne igen.

Undgå at skulle genopbygge grupperet columnstore indeks i oversigt over tabellen med endelig opbevaringsperioden, da det kan blive ændret rækkefølge i grupperne række naturligt pålagt af handlingen system-versionsstyring. Hvis du har brug at genopbygge grupperet columnstore indeks på tabellen historik, kan du gøre det ved at oprette det igen oven på kompatibel B-træet indeks bevarelse rækkefølge i den rowgroups, der er nødvendige for almindelige Dataoprydning. Den samme fremgangsmåde skal udføres, hvis du opretter tidsmæssig tabel med eksisterende oversigtstabel, der er grupperet kolonneindeks uden garanterede datarækkefølge:

````
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
````

Når endelig opbevaringsperiode er konfigureret til oversigtstabel med grupperet columnstore indekset, kan du ikke oprette flere ikke-grupperede B-træet indeks på den pågældende tabel:

````
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
````

Et forsøg på at udføre over sætningen mislykkes med følgende fejl:

*Meddelelse 13772, Level 16, State 1 <br> </br> kan ikke oprette ikke-grupperede indeks på en tidsmæssig oversigtstabel 'WebsiteUserInfoHistory' da den har begrænset opbevaringsperiode og grupperet columnstore indeks, der er defineret.*

##<a name="querying-tables-with-retention-policy"></a>Forespørgsler tabeller med opbevaringspolitik

Alle forespørgsler på tabellen tidsmæssig filtrere automatisk historiske rækker, der svarer endelig opbevaringspolitik, for at undgå uventede og modstridende resultater, da udestående rækker kan slettes ved oprydning opgaven, *når som helst tidspunkt og i vilkårlig rækkefølge*.

Følgende billede viser forespørgselsplanen for en simpel forespørgsel:

````
SELECT * FROM dbo.WebsiteUserInfo FROM SYSTEM_TIME ALL;
````

Forespørgselsplanen omfatter ekstra filter anvendt til slutningen af kolonnen periode (ValidTo) i operatoren "Grupperet indeks scanne" på tabellen oversigt (fremhævet). I dette eksempel antages det, 1 måned opbevaringsperiode blev angivet på WebsiteUserInfo tabel.

![Opbevaring forespørgselsfilter](./media/sql-database-temporal-tables-retention-policy/queryexecplanwithretention.png)

Hvis du forespørger oversigtstabel direkte, kan du se rækker, der er ældre end angivne opbevaring periode, men uden en hvilken som helst garanti for gentaget forespørgselsresultater. Følgende billede viser plan for udførelse af forespørgsel for forespørgslen på tabellen historik uden ekstra filtre anvendt:

![Forespørgsler oversigt uden opbevaring filter](./media/sql-database-temporal-tables-retention-policy/queryexecplanhistorytable.png)

Din forretningslogik ikke stole på læse oversigtstabel ud over opbevaringsperiode, som du kan få modstridende eller uventede resultater. Det anbefales at bruge tidsmæssig forespørgsler med-delsætning FOR SYSTEM_TIME til at analysere data i tidsmæssig tabeller.

##<a name="point-in-time-restore-considerations"></a>Peg i overvejelser i forbindelse med tiden gendannelsen

Når du opretter en ny database ved at [gendanne eksisterende database til et bestemt tidspunkt](sql-database-point-in-time-restore-portal.md), er der tidsmæssig opbevaring deaktiveret på niveauet for databasen. (**is_temporal_history_retention_enabled** flag indstillet til fra). Denne funktion giver dig mulighed at undersøge alle historiske rækker på Gendan, uden at bekymre dig, at udestående rækker er fjernet, før du kommer til at forespørge dem. Du kan bruge det til at *undersøge historiske data ud over konfigurerede opbevaringsperiode*.

Sige, at en tidsmæssig tabel har en måned opbevaring angivet periode. Hvis databasen er oprettet i Premium webtjenesten, vil du kunne oprette databasekopi med databasetilstanden op til 35 dage tilbage i fortiden. Effektivt giver dig tilladelse til at analysere historiske rækker, der er op til 65 dage ved at forespørge på oversigtstabel direkte.

Hvis du vil aktivere tidsmæssig opbevaring oprydning, skal du køre følgende Transact-SQL-sætning efter punkt i tid Gendan:

````
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
````

##<a name="next-steps"></a>Næste trin

Hvis du vil lære at bruge se tidsmæssig tabeller i dine programmer [Introduktion til tidsmæssig tabeller i Azure SQL-Database](sql-database-temporal-tables.md).

Besøg kanal 9 for at høre en [kunders tidsmæssig implementering succeshistorier](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) og se en [live tidsmæssig demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

Gennemse [dokumentationen til MSDN](https://msdn.microsoft.com/library/dn935015.aspx)kan finde detaljerede oplysninger om tidsmæssig tabeller.
