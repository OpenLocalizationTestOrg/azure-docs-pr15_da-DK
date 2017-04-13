<properties
    pageTitle="Komme i gang ved at køre aktivere databasen til Stræk guiden | Microsoft Azure"
    description="Lær, hvordan du konfigurerer en database til Stræk Database ved at køre aktivere databasen til Stræk guiden."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Komme i gang ved at køre aktivere databasen til Stræk guiden

For at konfigurere en database til Stræk Database skal du køre aktivere databasen for Stræk guiden.  Dette emne beskrives de oplysninger, du skal angive og valgmuligheder, du skal gøre i guiden.

Hvis du vil vide mere om Stræk Database skal du se [Stræk Database](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Senere, hvis du deaktiverer Stræk Database, Husk, at deaktivere Stræk Database til en tabel eller til en database ikke sletter remote objektet. Hvis du vil slette tabellen remote eller fjerndatabasen, har du slippe den ved hjælp af administrationsportalen Azure. Remote objekterne fortsætte med at afholde Azure omkostninger, indtil du slette dem manuelt. 

## <a name="launch-the-wizard"></a>Starte guiden

1.  Vælg den database, som du vil aktivere Stræk i SQL Server Management Studio i Object Explorer.

2.  Højre\-skal du klikke på og vælg **opgaver**, og vælg derefter **Stræk**, og vælg derefter **aktivere** at starte guiden.

## <a name="Intro"></a>Introduktion
Gennemgå formålet med guiden, og forudsætningerne.

De vigtige forudsætninger omfatter følgende:

-   Du skal være administrator for at ændre databaseindstillinger.
-   Du skal have en Microsoft Azure-abonnement.
-   SQL Server har lov til at kommunikere med Azure fjernserveren.

![Siden Introduktion i guiden Stræk][StretchWizardImage1]

## <a name="Tables"></a>Vælg tabeller
Vælg de tabeller, du vil aktivere Stræk.

Tabeller med mange rækker vises øverst på listen sorteret. Før guiden viser listen over tabeller, analyserer dem til datatyper, ikke understøttes i øjeblikket af Stræk Database.

![Vælg tabeller side i guiden Stræk][StretchWizardImage2]

|Kolonne|Beskrivelse|
|----------|---------------|
|(ingen titel)|Markér afkrydsningsfeltet i denne kolonne til at aktivere den markerede tabel for Stræk.|
|**Navn**|Angiver navnet på kolonnen i tabellen.|
|(ingen titel)|Et symbol i denne kolonne kan repræsentere en advarsel, der ikke\'t forhindrer dig i at aktivere den markerede tabel til Stræk. Det kan også repræsentere en blokeringsproblem, der forhindrer dig i at aktivere den markerede tabel til Stræk \- f.eks, fordi tabellen bruger en ikke-understøttet datatype. Hold markøren over symbolet for at få vist flere oplysninger i et værktøjstip. Du kan finde flere oplysninger om [begrænsninger for Stræk Database](sql-server-stretch-database-limitations.md).|
|**Den strækkes**|Angiver, om tabellen er allerede aktiveret for Stræk.|
|**Overføre**|Du kan overføre en hel tabel (**Hele tabellen**), eller du kan angive et filter på en eksisterende kolonne i tabellen. Hvis du vil bruge et andet filter til at markere rækker til at overføre, kan du køre sætningen ALTER TABLE for at angive filterfunktionen, efter du har afsluttet guiden. Flere oplysninger om filterfunktionen, skal du se [markere rækker til at overføre ved hjælp af funktionen et filter](sql-server-stretch-database-predicate-function.md). Flere oplysninger om hvordan du anvender funktionen, skal du se [Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md) eller [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Rækker**|Angiver antallet af rækker i tabellen.|
|**Størrelse (KB)**|Angiver størrelsen på tabellen i KB.|

## <a name="Filter"></a>Du kan også angive et række-filter

Hvis du vil angive funktionen et filter for at markere rækker til at overføre skal gøre følgende på siden **Vælg tabeller** .

1.  Klik på **Hele tabellen** på rækken for tabellen, på listen **Vælg de tabeller, du vil strække** . Dialogboksen **Vælg rækker, der skal strække** åbnes.

    ![Definere en filter-funktion][StretchWizardImage2a]

2.  Vælg **Vælg rækker**i dialogboksen **Vælg rækker, der skal strække** .

3.  Angiv et navn til filterfunktionen i **feltet navn**.

4.  Vælg en kolonne fra tabellen for **Where** -delsætningen, Vælg en operator, og Angiv en værdi.

5. Klik på **Kontrollér** for at teste funktionen. Hvis funktionen returnerer resultater fra tabellen – det vil sige, rapporterer Hvis der er rækker til at overføre, der opfylder betingelsen - testen **succes**.

    >   [AZURE.NOTE] Tekstfeltet, der viser filterforespørgslen er skrivebeskyttet. Du kan ikke redigere forespørgslen i tekstfeltet.

6.  Klik på udført for at vende tilbage til siden **Vælg tabeller** .

Filterfunktionen er oprettet i SQL Server kun, når du har afsluttet guiden. Indtil da, kan du vende tilbage til siden **Vælg tabeller** til at ændre eller omdøbe filterfunktionen.

![Vælg tabeller side efter definerer et filterfunktionen][StretchWizardImage2b]

Hvis du vil bruge en anden type af filterfunktionen til at markere rækker til at overføre, kan du gøre et af følgende ting.  

-   Afslutte guiden, og kør sætningen ALTER TABLE til at aktivere Stræk for tabellen og til at angive funktionen et filter. Du kan finde flere oplysninger om [Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md).  

-   Køre sætningen ALTER TABLE for at angive funktionen et filter, efter du har afsluttet guiden. Se [tilføje en filter funktion efter køre guiden](sql-server-stretch-database-predicate-function.md#addafterwiz)for de påkrævede trin.

## <a name="Configure"></a>Konfigurere Azure-installation

1.  Log på Microsoft Azure med en Microsoft-konto.

    ![Log på Azure - guiden Stræk Database][StretchWizardImage3]

2.  Markere det eksisterende Azure abonnement skal bruges til Stræk Database.

3.  Vælg en Azure område.
    -   Hvis du opretter en ny server, oprettes serveren i dette område.  
    -   Hvis du har eksisterende servere i det valgte område, viser guiden dem, når du vælger **eksisterende server**.

    For at minimere ventetid skal du vælge det Azure område, hvor dine SQL Server er placeret. Du kan finde flere oplysninger om områder [Azure områder](https://azure.microsoft.com/regions/).

4.  Angiv, om du vil bruge en eksisterende server eller oprette en ny Azure server.

    Hvis Active Directory på din SQL Server er knyttet til Azure Active Directory, kan du du kan også bruge en samlet tjenestekonto til SQL Server til at kommunikere med Azure fjernserveren. Se [Ændre angive databaseindstillinger (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)for at få flere oplysninger om kravene til denne indstilling.

    -   **Oprette ny server**

        1.  Oprette et logon og en adgangskode til serveradministratoren.

        2.  Du kan også bruge en samlet tjenestekonto til SQL Server til at kommunikere med Azure fjernserveren.

        ![Oprette ny Azure server - guiden Stræk Database][StretchWizardImage4]

    -   **Eksisterende server**

        1.  Vælg den eksisterende Azure server.

        2.  Vælg godkendelsesmetoden.

            -   Hvis du vælger **SQL Server-godkendelse**, give administrator logon og adgangskode.

            -   Vælg **Active Directory integreret godkendelse** til at bruge en samlet tjenestekonto til SQL Server til at kommunikere med Azure fjernserveren. Hvis den valgte server ikke er integreret med Azure Active Directory, vises denne indstilling ikke.

        ![Vælg eksisterende Azure server - guiden Stræk Database][StretchWizardImage5]

## <a name="Credentials"></a>Secure legitimationsoplysninger
Du skal have en database master nøgle til at sikre de legitimationsoplysninger, Stræk Database bruges til at oprette forbindelse til fjerndatabasen.  

Hvis der allerede findes en database master nøgle, Skriv adgangskoden til den.  

![Secure legitimationsoplysninger side i guiden Stræk Database][StretchWizardImage6b]

Hvis databasen ikke har en eksisterende master tast, skal du angive en stærk adgangskode for at oprette en database master-nøgle.  

![Secure legitimationsoplysninger side i guiden Stræk Database][StretchWizardImage6]

Flere oplysninger om tasten database master i [oprette MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) og [oprette en Database Master-nøgle](https://msdn.microsoft.com/library/aa337551.aspx). Du kan finde flere oplysninger om de legitimationsoplysninger, som guiden opretter, [Oprette databasen fastsat LEGITIMATIONSOPLYSNINGER (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Vælg IP-adresse
Brug undernet IP-adresseområder (anbefales) eller den offentlige IP-adresse for SQL Server, til at oprette en regel på Azure, hvor SQL Server kommunikere med Azure fjernserveren.

IP-adresse eller adresser, du angiver på denne side kan du se Azure serveren for at tillade indgående data, forespørgsler og administration af handlinger, der er startet af SQL Server for at gå gennem Azure firewallen. Guiden ændre ikke noget i indstillingerne for firewallen på SQL Server.

![Vælg IP-adresse side i guiden Stræk][StretchWizardImage7]

## <a name="Summary"></a>Oversigt
Gennemse de værdier, du har angivet og de indstillinger, du har valgt i guiden, og de estimerede omkostninger på Azure. Vælg **Udfør** for at aktivere Stræk.

![Oversigtsside i guiden Stræk][StretchWizardImage8]

## <a name="Results"></a>Resultater
Gennemse resultaterne.

Hvis du vil overvåge statussen for dataoverførsel, se [skærm og foretage fejlfinding af dataoverførsel (Stræk Database)](sql-server-stretch-database-monitor.md).

![Siden med søgeresultater i guiden Stræk][StretchWizardImage9]

## <a name="KnownIssues"></a>Fejlfinding i forbindelse med guiden
**Guiden Stræk mislykkedes.**
Hvis Stræk Database ikke er endnu aktiveret på serverniveau, og du kan køre guiden uden at systemet administratortilladelser for at kunne aktivere den, mislykkes guiden. Spørg systemadministratoren for at aktivere Stræk Database på den lokale server-forekomst, og derefter køre guiden igen. Du finder flere oplysninger i [nødvendige: tilladelse til at aktivere Stræk Database på serveren](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Næste trin
Aktivere flere tabeller til Stræk Database. Overvåge dataoverførsel og administrere Stræk\-aktiveret databaser og tabeller.

-   [Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md) til at aktivere flere tabeller.

-   [Skærm og foretage fejlfinding af dataoverførsel](sql-server-stretch-database-monitor.md) at få vist status for dataoverførsel.

-   [Afbryde og genoptage Stræk Database](sql-server-stretch-database-pause.md)

-   [Administrere og foretage fejlfinding af Stræk Database](sql-server-stretch-database-manage.md)

-   [Sikkerhedskopiering Stræk-kompatible databaser](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Se også

[Aktivere Stræk Database til en database](sql-server-stretch-database-enable-database.md)

[Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png
