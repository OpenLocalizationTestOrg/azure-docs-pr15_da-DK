<properties 
   pageTitle="Azure SQL Database-forespørgsel ydeevne indsigt" 
   description="Forespørgsel overvågning identificerer de fleste CPU-forbrug forespørgsler til en Azure SQL-Database." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Azure SQL Database-forespørgsel ydeevne indsigt


Administrere og justering af ydeevnen for relationsdatabaser er en udfordring opgave, der kræver betydeligt ekspertise og tid investering. Forespørgsel ydeevne indsigt gør det muligt at bruge mindre tid på fejlfinding databasens ydeevne ved at indsende følgende:

- Bedre indsigt i dine databaser ressourceforbrug (DTU). 
- De mest populære forespørgsler ved CPU/varighed/udførelse af Tæl, som kan potentielt optimeret til forbedret ydeevne.
- Muligheden for at analysere ned i detaljerne for en forespørgsel, få vist dets tekst og historik for ressourceforbrug mere effektivt. 
- Justering af anmærkninger, der viser handlinger, der er udført af [SQL Azure Database Advisor](sql-database-advisor.md) ydeevne  



## <a name="prerequisites"></a>Forudsætninger

- Forespørgsel ydeevne indsigt er kun tilgængelig med version 12 Azure SQL-Database.
- Forespørgsel ydeevne indsigt kræver, at [Forespørgslen Store](https://msdn.microsoft.com/library/dn817826.aspx) er aktiv på din database. Hvis der ikke kører en forespørgsel Store, portalen beder dig om at aktivere den.

 
## <a name="permissions"></a>Tilladelser

Følgende [Rollebaseret adgangskontrol](../active-directory/role-based-access-control-configure.md) tilladelser er påkrævet for at bruge forespørgsel ydeevne indsigt: 

- **Læser**, **ejer**, **bidragyder**, **SQL DB bidragyder**eller **SQL Server bidragyder** tilladelser kræves for at få vist de øverste ressource arket forespørgsler og diagrammer. 
- **Ejer**, **bidragyder**, **SQL DB bidragyder**eller **SQL Server bidragyder** tilladelser kræves for at få vist forespørgselstekst.



## <a name="using-query-performance-insight"></a>Brug af forespørgsel ydeevne indsigt

Forespørgsel ydeevne indsigt er nem at bruge:

- Åbn [Azure-portalen](https://portal.azure.com/) , og find database, du vil undersøge. 
  - Vælg "Forespørgsel ydeevne indsigt" i venstre side menuen under support og fejlfinding.
- Gennemgå listen over mest populære forespørgsler ressource-forbrug under den første fane.
- Vælg en enkelt forespørgsel til at få vist detaljerne.
- Åbn [SQL Azure Database Advisor](sql-database-advisor.md) og Kontrollér, om en hvilken som helst anbefalinger er tilgængelige.
- Brug skyderne eller zoome ikoner for at ændre observerede interval.

    ![dashboard for ydeevne](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Et par timer af data skal hentes af forespørgsel Store for SQL-Database til at levere forespørgsel ydeevne indsigt. Hvis databasen har ingen aktivitet eller forespørgsel Store ikke var aktiv i en bestemt tidsperiode, bliver diagrammerne tom, når du ser den angivne periode. Du kan aktivere forespørgsel Store når som helst, hvis det ikke kører.   



## <a name="review-top-cpu-consuming-queries"></a>Gennemse øverste CPU forbrug forespørgsler

Gør følgende i [portalen](http://portal.azure.com) :

1. Gå til en SQL-database, og klik på **Indstillinger for alle** > **Support + fejlfinding** > **forespørgsel ydeevne indsigt**. 

    ![Forespørgsel ydeevne indsigt][1]

    Visningen mest populære forespørgsler åbnes, og de øverste arket CPU-forespørgsler vises.

1. Klik på omkring diagrammet for at få oplysninger.<br>Den øverste linje viser overordnede DTU % for databasen, mens linjerne viser CPU % forbruge ved de valgte forespørgsler under det valgte interval (f.eks, hvis **seneste uge** er markeret hver søjle repræsenterer en dag).

    ![mest populære forespørgsler][2]

    Gitteret nederste repræsenterer aggregeret oplysninger til de synlige forespørgsler.

  - Forespørgsels-ID'ET – entydigt id for forespørgsel i databasen.
  - CPU per forespørgsel under opbevaringstid interval (afhænger sammenlægning funktionen).
  - Varighed per forespørgsel (afhænger sammenlægning funktionen).
  - Samlet antal udførelser for en bestemt forespørgsel.

    Markér eller fjern individuelle forespørgsler for at medtage eller udelade dem fra diagrammet ved hjælp af afkrydsningsfelter.

1. Hvis dine data bliver forældede, skal du klikke på knappen **Opdater** .
1. Du kan bruge skyderne og zoome knapperne til at ændre observationer interval og undersøge spidser:  ![indstillinger](./media/sql-database-query-performance/zoom.png)
1. Du kan også hvis du vil have en anden visning, kan du vælge **brugerdefineret** fane og angive:
  
  - Metrisk (CPU, varighed, udførelse af Tæl)
  - Tidsinterval (seneste 24 timer, sidste uge, sidste måned). 
  - Antal forespørgsler.
  - Funktionen sammenlægning.

    ![indstillinger](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Få vist oplysninger om individuelle forespørgsel

Sådan vises oplysninger om forespørgsel:

1. Klik på en forespørgsel på listen over mest populære forespørgsler.

    ![detaljer](./media/sql-database-query-performance/details.png)

1. Detaljeret visning åbnes og forespørgsler CPU forbrug/varighed/udførelse af antallet er fordelt over en periode.
1. Klik på omkring diagrammet for at få oplysninger.
  - Øverste diagram viser linjen med overordnede database DTU % og søjlerne har CPU % consumed ved den valgte forespørgsel.
  - Det andet diagram viser samlede varighed efter den valgte forespørgsel.
  - Nederste diagram viser det samlede antal udførelser efter den valgte forespørgsel.
    
    ![Forespørgselsoplysninger][3]

1. Du kan også bruge skyderne, zoome knapper eller klik på **Indstillinger for** at tilpasse, hvordan forespørgselsdata vises eller for at vælge en anden tidsperiode.

## <a name="review-top-queries-per-duration"></a>Gennemse mest populære forespørgsler per varighed

I den seneste opdatering af forespørgsel ydeevne indsigt, vi introduceret to nye målepunkter, der kan hjælpe dig med at identificere potentielle flaskehalse: varighed og udførelse af antal.<br>

Længerevarende forespørgsler har det største potentiale for låsning ressourcer længere, blokerer andre brugere og begrænse skalerbarhed. De er også de bedste kandidater til optimering.<br>

Sådan identificeres længerevarende forespørgsler:

1. Åbne **brugerdefineret** fane i forespørgsel ydeevne indsigt for valgte database
1. Ændre målepunkter skal være **varighed**
1. Vælg antallet af forespørgsler og observationer interval
1. Vælg sammenlægning funktion
  - **Sum** adderer alle forespørgsel kørselstid under hele observationer interval.
  - **Maks** finder forespørgsler, hvilke kørselstid blev maksimale til hele observationer interval.
  - **Avg** finder gennemsnitlige udførelse af tid for alle forespørgsel udførelser og viser toppen af disse gennemsnit. 

    ![forespørgsel varighed][4]

## <a name="review-top-queries-per-execution-count"></a>Gennemse mest populære forespørgsler per udførelse af antal

Høj antal udførelser påvirker muligvis ikke selve databasen og brugen af ressourcer, der kan være lav, men overordnede program kan få langsom.

I nogle tilfælde meget høj udførelse af Tæl kan medføre for at øge af netværk Runder. Runder påvirke ydeevne betydeligt. De er underlagt netværksventetid og efterfølgende server ventetid. 

For eksempel mange datadrevne websteder meget få adgang til databasen til hver anmodning. Mens forbindelse gruppering af hjælper med at, øget netværkstrafikken og behandling af belastning på databaseserveren kan påvirke ydeevnen.  Generel rådgivning er at holde trafik til et minimum.

Hvis du vil identificere ofte udføres forespørgsler ("chatty") forespørgsler:

1. Åbne **brugerdefineret** fane i forespørgsel ydeevne indsigt for valgte database
1. Ændre målepunkter skal være **udførelse af antal**
1. Vælg antallet af forespørgsler og observationer interval

    ![forespørgsel udførelse af antal][5]

## <a name="understanding-performance-tuning-annotations"></a>Forstå ydeevne justering anmærkninger 

Du oplever måske ikoner med lodret linje øverst på diagrammet under udforske arbejdsbelastningen i forespørgsel ydeevne indsigt.<br>

Disse ikoner er anmærkninger; de repræsenterer ydeevnen påvirker handlinger, der er udført af [SQL Azure Database Advisor](sql-database-advisor.md). Du kan få grundlæggende oplysninger om handlingen ved denne anmærkning:

![forespørgsel anmærkning][6]

Hvis du vil vide mere, eller Anvend advisor anbefaling, skal du klikke på ikonet. Oplysninger om handling, åbnes. Hvis det er en aktiv anbefaling kan du anvende den med det samme ved hjælp af kommandoen.

![Forespørgselsoplysninger anmærkning][7]

### <a name="multiple-annotations"></a>Flere anmærkninger. ###

Det er muligt, på grund af zoomniveau, anmærkninger, der er tæt på hinanden vil få skjult i én. Dette repræsenteres af særligt ikon, at klikke på den åbne nye blade, hvor liste over grupperet anmærkninger skal vises.
Korrelere forespørgsler og ydeevne justering af handlinger kan hjælpe dig til bedre for at forstå din arbejdsbyrde. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimering af forespørgsel Store konfigurationen for forespørgsel ydeevne indsigt

Under din brug af forespørgsel ydeevne indsigt, kan der ske følgende forespørgsel Store meddelelser:

- "Forespørgsel Store er ikke konfigureret korrekt på denne database. Klik her for at få mere for at vide."
- "Forespørgsel Store er ikke konfigureret korrekt på denne database. Klik her for at ændre indstillingerne." 

Disse meddelelser vises som regel, når forespørgslen Store ikke er i stand til at indsamle nye data. 

Første tilfælde sker der, når forespørgslen Store er i skrivebeskyttet tilstand og parametre angives optimalt. Du kan løse dette problem ved at øge størrelsen af forespørgsel Store eller rydde forespørgslen Store.

![qds knap][8]

Andet tilfælde sker der, når forespørgsel Store er deaktiveret eller ikke er angivet parametre optimalt. <br>Du kan ændre politikken opbevaring og registrering og aktivere forespørgsel Store, ved at udføre kommandoer nedenfor eller direkte fra portalen:

![qds knap][9]

### <a name="recommended-retention-and-capture-policy"></a>Anbefalede opbevaring og capture politik

Der er to typer af opbevaringspolitikker:

- Størrelse baseret – Hvis angivet til automatisk det vil rydde op i data automatisk, når i nærheden af maksimal længde er nået.
- Tid – som standard baseret vi vil angive den til 30 dage, hvilket betyder, hvis forespørgsel Store vil køre tør for plads, slettes forespørgsel oplysninger, der er ældre end 30 dage

Registrere politik kan sættes til:

- **Alle** – registrerer alle forespørgsler.
- **Automatisk** – sjælden og forespørgsler med små kompilering og udførelse af varighed, ignoreres. Tærskler til udførelse af Tæl, kompilering og runtime varighed bestemmes internt. Dette er standardindstillingen.
- **Ingen** – forespørgsel Store holder op med at indsamle nye forespørgsler, men runtime statistik for allerede er hentet forespørgsler er stadig indsamlede.
    
Vi anbefaler, at indstille alle politikker til automatisk og ren politik til 30 dage:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Øge størrelsen af forespørgsel Store. Dette kan udføres ved at oprette forbindelse til en database og udstede følgende forespørgsel:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Anvende disse indstillinger senere, bliver forespørgslen Store ved at indsamle nye forespørgsler, men hvis du ikke vil vente kan du rydde forespørgslen Store. 
> [AZURE.NOTE] Udføre følgende forespørgsel, slettes alle aktuelle oplysninger i Store forespørgsel. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Oversigt

Forespørgsel ydeevne indsigt hjælper dig med at forstå påvirkningen af din forespørgsel arbejdsbyrde, og hvordan den relaterer til databasen Ressourceforbrug. Med denne funktion kan du få mere at vide om øverst forbrug forespørgsler, og nemt identificere dem til at rette, før de bliver et problem.




## <a name="next-steps"></a>Næste trin

Klik på [anbefalinger](sql-database-advisor.md) på bladet **Forespørgsel ydeevne indsigt** for flere anbefalinger om at forbedre ydeevnen for SQL-database.

![Performance Advisor](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

