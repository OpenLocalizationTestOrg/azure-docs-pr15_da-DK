<properties
   pageTitle="Brug af SQL-forbindelsen i logik Apps | Microsoft Azure App Service"
   description="Hvordan du kan oprette og konfigurere app'en SQL-forbindelse eller API og bruge det i en logik app i Azure App Service"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Introduktion til Microsoft SQL forbindelsen og tildele den til din logik App
>[AZURE.NOTE] Denne version i denne artikel gælder for logik apps 2014-12-01-skema prøveversionen. Klik på [SQL Azure API](../connectors/connectors-create-api-sqlazure.md)til Azure SQL-2015-08-01-skema prøveversionen.

Oprette forbindelse til en lokal SQL Server eller en Azure SQL-Database for at oprette og ændre dine oplysninger eller data. Forbindelser kan bruges i logik Apps for at hente processen, eller push data som en del af en "arbejdsproces". Når du bruger SQL-forbindelsen i arbejdsprocessen, kan du opnå en række forskellige scenarier. Det kan du f.eks.:

- Få vist en sektion af de data, der findes i dine SQL-database ved hjælp af et websted eller mobile-program.
- Indsætte data i en SQL-databasetabeller for lagerplads. Du kan for eksempel angive medarbejderposter, Opdater salgsordrer og så videre.
- Hente data fra SQL og bruge det i en forretningsproces. For eksempel kan du få kundeposter og anbringe disse kundeposter i SalesForce.

Du kan føje SQL-forbindelse til virksomhedens arbejdsgange og processer data som en del af denne arbejdsproces i en logik App. 

## <a name="triggers-and-actions"></a>Udløsere og handlinger
*Udløsere* er begivenheder, der udføres. For eksempel, er når en ordre opdateres, eller når en ny kunde tilføjet. En *handling* er resultatet af udløseren. For eksempel, hvor en ordre er opdateret, kan du sende en besked til sælgeren. Eller, når der føjes en ny kunde, kan du sende en mail med Velkommen til den nye kunde.

SQL-forbindelse kan bruges som en udløser eller en handling i et logik app og understøtter data i JSON og XML-formater. For hver tabel, der er medtaget i pakken indstillinger (mere om, senere i dette emne), der er et sæt JSON handlinger og et sæt af XML-handlinger.

SQL-forbindelsen har følgende udløsere og handlinger, der er tilgængelige:

Udløsere | Handlinger
--- | ---
Afstemning Data | <ul><li>Indsætte i tabel</li><li>Opdateringstabel</li><li>Vælg tabel</li><li>Slette fra tabel</li><li>Ringe til lagret Procedure</li></ul>

## <a name="create-the-sql-connector"></a>Oprette SQL-forbindelse

En forbindelse kan oprettes i en logik app eller oprettes direkte fra Azure Marketplace. Sådan oprettes en forbindelse fra Marketplace:  

1. Azure startboard, Vælg **Marketplace**.
2. Søge efter "SQL Connector", markere den, og vælg **Opret**.
3. Skriv navnet, App Service planlægge og andre egenskaber.
4. Angiv følgende Pakkeindstillinger for:

    Navn | Påkrævet |  Beskrivelse
--- | --- | ---
Servernavn | Ja | Angiv navnet på SQL Server. For eksempel Angiv *SQL Server/sqlexpress* eller *SQLserver.mydomain.com*.
Port | Nej | Standard er 1433.
Brugernavn | Ja | Angive et brugernavn, der kan logge ind i SQL Server. Hvis forbindelse til en lokal SQL Server, skal du angive SQL-legitimationsoplysninger.
Adgangskode | Ja | Angiv brugernavnet og adgangskoden.
Databasenavn | Ja | Angiv den database, du opretter forbindelse. For eksempel kan du angive *kunder* eller *dbo/ordrer*.
Lokalt | Ja | Standardindstillingen er falsk. Angiv False, hvis forbindelse til en Azure SQL-database. Skriv True, hvis forbindelse til en lokal SQL Server.
Tjenesten Bus forbindelsesstreng | Nej | Hvis du opretter forbindelse til en lokal installation, skal du angive Service Bus relay forbindelsesstrengen.<br/><br/>[Ved hjælp af Forbindelsesstyring Hybrid](app-service-logic-hybrid-connection-manager.md)<br/>[Service Bus priser](https://azure.microsoft.com/pricing/details/service-bus/)
Partner servernavn | Nej | Hvis den primære server er tilgængelig, kan du angive en partnerserver som en alternativ eller sikkerhedskopiering server.
Tabeller | Nej | Liste over tabellerne i databasen, der kan blive opdateret via forbindelsen. For eksempel angive *OrdersTable* eller *EmployeeTable*. Hvis ingen tabeller er angivet, kan alle tabeller bruges. Gyldige tabeller og/eller lagrede procedurer er påkrævet for at bruge denne forbindelse som en handling.
Lagrede procedurer | Nej | Angiv en eksisterende lagret procedure, der kan ringes via forbindelsen. For eksempel angive *sp_IsEmployeeEligible* eller *sp_CalculateOrderDiscount*. Gyldige tabeller og/eller lagrede procedurer er påkrævet for at bruge denne forbindelse som en handling.
Tilgængelige dataforespørgsel | For at udløse support | SQL-sætning til at afgøre, om data er tilgængelige for forespørgsel blandt en SQL Server-databasetabel. Dette skal returnere en numerisk værdi, der repræsenterer antallet af rækker med data, der er tilgængelige. Eksempel: Vælg COUNT(*) tabelnavn.
Afstemning dataforespørgsel | For at udløse support | SQL-sætningen til afstemning tabellen SQL Server-database. Du kan angive et vilkårligt antal SQL-sætninger, der er adskilt med semikolon. Denne sætning er udført en transaktion og kun anvendt når dataene lagres sikkert i din app, logik. Eksempel: SELECT *fra tabelnavn; SLETTE fra tabelnavn. <br/>**Note**<br/>Du skal angive en afstemning-sætning, der undgår en uendelig løkke ved at slette, flytte eller opdatere markerede data for at sikre, at samme data ikke spurgt igen.

5. Når du er færdig, ligner den Pakkeindstillinger følgende:  
![][1]  

6. Vælg **Opret**. 


## <a name="use-the-connector-as-a-trigger"></a>Bruge forbindelsen som udløser
Lad os se på en enkelt logik app, der undersøger data fra en SQL-tabel, tilføjer dataene i en anden tabel og opdaterer dataene.

For at bruge SQL forbindelsen som en udløser, skal du angive de **Tilgængelige dataforespørgsel** og **Afstemning dataforespørgsel** værdier. **Tilgængelige dataforespørgsel** køres tidsplanen, du angiver og angiver, om data er tilgængelige. Da denne forespørgsel returnerer kun et skalær tal, kan det være indstillet og optimeret til hyppige udførelse af.

**Afstemning dataforespørgsel** udføres kun, når forespørgslen Data tilgængelige betyder, at dataene er tilgængelige. Denne sætning udfører inden for en transaktion og er kun anvendt, når de udpakkede data gemmes solidt i arbejdsprocessen. Det er vigtigt at undgå at uddrage uendelig de samme data igen. Denne udførelse af transaktions art kan bruges til at slette eller opdatere data for at sikre, at det ikke er indsamlet næste gang data forespørges.

> [AZURE.NOTE] I skemaet, der returneres af denne erklæring identificerer de tilgængelige egenskaber i forbindelsen. Alle kolonner skal navngives.

#### <a name="data-available-query-example"></a>Eksempel på data tilgængelige forespørgsel

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Eksempel på afstemning Data forespørgsel

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Tilføje på udløser
1. Når du opretter eller redigerer en logik app, Markér den SQL-forbindelse, du har oprettet som udløser. Her vises de tilgængelige udløsere: **Afstemning Data (JSON)** og **Afstemning Data (XML)**:  
![][5]

2. Vælg **Afstemning Data (JSON)** udløser, Angiv frekvensen, og klik på ✓:  
![][6]

3. Udløseren vises nu som konfigureret i appen logik. Output(s) af udløseren vises og kan bruges som input i alle efterfølgende handlinger:  
![][7]

## <a name="use-the-connector-as-an-action"></a>Bruge forbindelsen som en handling
Brug af vores enkle logik app scenarie, som undersøger data fra en SQL-tabel, tilføjer dataene i en anden tabel, og opdaterer dataene.

For at bruge SQL forbindelsen som en handling, skal du skrive navnet på tabeller og/eller lagrede procedurer, som du har angivet, da du oprettede SQL forbindelsen:

1. Efter din udløser (eller vælg 'Kør denne logik manuelt'), tilføje den SQL-forbindelse, du har oprettet i galleriet. Vælg en af Indsæt handlinger, som *Indsætte i TempEmployeeDetails (JSON)*:  
![][8]

2. Angiv inputværdierne i posten, der skal indsættes, og klik på ✓:  
![][9]

3. Vælg den samme SQL-forbindelse, du har oprettet i galleriet. Som en handling, skal du vælge opdateringshandlingen i den samme tabel, som *Opdatering EmployeeDetails*:  
![][11]

4. Angiv inputværdierne for opdateringen, og klik på ✓:  
![][12]

Du kan teste logik app ved at tilføje en ny post i den tabel, der er forespørges.

## <a name="what-you-can-and-cannot-do"></a>Hvad du kan og ikke

SQL-forespørgsel | Understøttes | Ikke understøttet
--- | --- | ---
Hvor delsætning | <ul><li>Operatorer: Og, eller, =, <>, <, <, >, > = og ud</li><li>Flere sub betingelser kan kombineres ved '(' og')'</li><li>Streng konstanter, Datetime (omgivet af enkelte anførselstegn), tal (skal kun indeholde numeriske tegn)</li><li>Absolut skal vises i et udtryk, der binært format, vil ((operand operator operand) og/eller (operand operator operand)) *</li></ul> | <ul><li>Operatorer: Mellem, i</li><li>Alle indbyggede funktioner som ADD(), MAX() nu(), POWER() og osv.</li><li>Matematiske operatorer som *, -, +, osv.</li><li>Sammensætning af strenge er ved hjælp af +.</li><li>Alle joinforbindelser</li><li>ER NULL og er ikke Null</li><li>Et tal med ikke-numerisk tegn, som hexadecimale tal</li></ul>
Felter (i Select-forespørgsel) | <ul><li>Gyldige kolonnenavne adskilt af kommaer. Ingen tabel navn præfikser tilladt (forbindelse virker på én tabel ad gangen).</li><li>Navne kan escape-tegn med ' [' og ']'</li></ul> | <ul><li>Nøgleord som toppen, DISTINCT og osv.</li><li>Udjævning, som Gade + by + Zip AS adresse</li><li>Alle indbyggede funktioner, som ADD(), MAX() nu(), POWER() og osv.</li><li>Matematiske operatorer, som *, -, +, osv.</li><li>Sammensætning af strenge er ved hjælp af +</li></ul>

#### <a name="tips"></a>Tip

- Vi anbefaler, at du oprettelse af en lagret procedure og execute med brug af fremgangsmåden execute gemt API til avancerede forespørgsler.
- Når du bruger indre forespørgsler, kan du bruge dem i lagrede procedurer.
- For deltagelse i flere betingelser, du kan bruge 'Og' og 'Eller' operatorer.

## <a name="hybrid-configuration-optional"></a>Hybridkonfiguration (valgfrit)

> [AZURE.NOTE] Dette trin er påkrævet, kun, hvis du bruger SQL Server lokalt bag firewallen.

App Service bruges Hybrid Configuration Manager til at oprette sikker forbindelse til dit lokale system. Hvis du er forbindelse bruger en lokal SQL Server, er Forbindelsesstyring Hybrid påkrævet.

> [AZURE.NOTE] Hvis du vil Introduktion til Azure logik Apps før tilmelding til en Azure-konto, skal du gå til [Prøve logik App](https://tryappservice.azure.com/?appservice=logic), hvor du straks kan oprette en forbigående starter logik app i App-tjeneste. Ingen kreditkort, der kræves. ingen forpligtelser.

Under [Brug af Forbindelsesstyring Hybrid](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Få mere ud af forbindelsen
Nu hvor forbindelsen er oprettet, kan du tilføje den til en arbejdsprocesser for virksomheder ved hjælp af en logik App. Se [Hvad er logik Apps?](app-service-logic-what-are-logic-apps.md).

Få vist Swagger REST-API reference på [forbindelser og API Apps Reference](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Du kan også gennemse ydeevne statistik og kontrollere sikkerhed til forbindelsen. Se [administrere og overvåge dine indbyggede API Apps og forbindelser](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
