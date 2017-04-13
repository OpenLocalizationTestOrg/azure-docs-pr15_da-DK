<properties
    pageTitle="Aktivere Stræk Database til en database | Microsoft Azure"
    description="Lær, hvordan du konfigurerer en database til Stræk Database."
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
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="enable-stretch-database-for-a-database"></a>Aktivere Stræk Database til en database

For at konfigurere en eksisterende database til Stræk Database skal du markere **opgaver | Stræk | Aktivere** til en database i SQL Server Management Studio til at åbne guiden **Stræk aktivere Database** . Du kan også bruge Transact\-SQL til at aktivere Stræk Database til en database.

Hvis du vælger **opgaver | Stræk | Aktivere** til en enkelt tabel, og du ikke har endnu aktiveret databasen for Stræk Database, guiden konfigurerer databasen til Stræk Database og lader dig vælge tabeller som en del af processen. Følg trinnene i dette emne i stedet for trinnene i [Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-database.md).

Aktivering af Stræk Database på en database eller en tabel kræver db\_tilladelser som ejer. Aktivering af Stræk Database på en database kræver også KONTROLELEMENTET DATABASE tilladelser.

 >   [AZURE.NOTE] Senere, hvis du deaktiverer Stræk Database, Husk, at deaktivere Stræk Database til en tabel eller til en database ikke sletter remote objektet. Hvis du vil slette tabellen remote eller fjerndatabasen, har du slippe den ved hjælp af administrationsportalen Azure. Remote objekterne fortsætte med at afholde Azure omkostninger, indtil du slette dem manuelt.

## <a name="before-you-get-started"></a>Før du går i gang

-   Før du konfigurerer en database til Stræk, anbefaler vi, at du kører Stræk Database Advisor for at identificere databaser og tabeller, der er berettiget til Stræk. Stræk Database Advisor identificerer også blokering problemer. Se [Identificer databaser og tabeller til Stræk Database](sql-server-stretch-database-identify-databases.md)for at få flere oplysninger.

-   Gennemse [begrænsninger for strække Database](sql-server-stretch-database-limitations.md).

-   Stræk Database overfører data til Azure. Derfor skal du have en Azure-konto og et abonnement til fakturering. For at få en Azure-konto, [skal du klikke her](http://azure.microsoft.com/pricing/free-trial/).

-   Har den forbindelse, og log oplysninger, du har brug for at oprette en ny Azure server eller for at vælge en eksisterende Azure server.

## <a name="EnableTSQLServer"></a>Forudsætninger: Aktivere Stræk Database på serveren
Før du kan aktivere Stræk Database på en database eller en tabel, er det nødvendigt at aktivere den på den lokale server. Denne handling kræver systemadministrator eller serveradmin tilladelser.

-   Hvis du har de nødvendige administrative tilladelser, konfigurerer guiden **Aktivere Database til Stræk** serveren til Stræk.

-   Hvis du ikke har de nødvendige tilladelser, en administrator skal aktivere indstillingen manuelt ved at køre **sp\_konfigurere** før du kører guiden eller en administrator har til at køre guiden.

Kør for at aktivere manuelt Stræk Database på serveren, **sp\_konfigurere** og aktivere indstillingen **fjerndata arkiv** . I følgende eksempel aktiveres indstillingen **fjerndata arkiv** ved at angive dens værdi til 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO

RECONFIGURE;
GO
```
Flere oplysninger i [konfigurere dataene arkivere Server Configuration Option](https://msdn.microsoft.com/library/mt143175.aspx) og [sp_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Brug guiden til at aktivere Stræk Database på en database
For at få oplysninger om aktivere databasen til Stræk guiden, herunder de oplysninger, du skal angive og valgmuligheder, du skal gøre, skal du se [komme i gang ved at køre den aktivere Database, Stræk guiden](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Bruge Transact\-SQL Sådan aktiveres Stræk Database på en database
Før du kan aktivere Stræk Database på enkelte tabeller, er det nødvendigt at aktivere den i databasen.

Aktivering af Stræk Database på en database eller en tabel kræver db\_tilladelser som ejer. Aktivering af Stræk Database på en database kræver også KONTROLELEMENTET DATABASE tilladelser.

1.  Før du begynder, vælge en eksisterende Azure server for de data, der overfører Stræk Database eller oprette en ny Azure server.

2.  Oprette en regel på Azure-serveren, med IP-adresseområder for SQL Server, hvor SQL Server kommunikere med den eksterne server.

    Du kan nemt finde de værdier, du skal bruge og oprette firewallreglen ved forsøg på at oprette forbindelse til Azure serveren fra Object Explorer i SQL Server Management Studio (SSMS). SSMS hjælper dig med at oprette reglen ved at åbne dialogboksen følgende som allerede indeholder de påkrævede IP-adresse værdier.

    ![Oprette en firewallregel i SSMS][FirewallRule]

3.  For at konfigurere en SQL Server-database til Stræk Database, skal databasen har en database master-nøgle. Tasten database master sikrer de legitimationsoplysninger, Stræk Database bruges til at oprette forbindelse til fjerndatabasen. Her er et eksempel, der opretter en ny database master nøgle.

    ```tsql
    USE <database>;
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>';
    GO
    ```

    Flere oplysninger om tasten database master i [oprette MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) og [oprette en Database Master-nøgle](https://msdn.microsoft.com/library/aa337551.aspx).

4.  Når du konfigurerer en database til Stræk Database, har du giver en legitimationsoplysningerne for Stræk databasen til brug af kommunikation mellem de til lokal SQL Server og Azure fjernserveren. Har du to muligheder.

    -   Du kan angive legitimationsoplysninger for en administrator.

        -   Hvis du aktiverer Stræk Database ved at køre guiden, kan du oprette legitimationsoplysninger på det pågældende tidspunkt.

        -   Hvis du vil aktivere Stræk Database ved at køre **JUSTER DATABASE**, har du oprette legitimationsoplysninger manuelt, inden du kører **JUSTER DATABASE** for at aktivere Stræk Database.

        Her er et eksempel, der opretter en ny legitimationsoplysninger.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>';
        GO
        ```

        Du kan finde flere oplysninger om legitimationsoplysninger, [Oprette databasen fastsat LEGITIMATIONSOPLYSNINGER (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Oprette legitimationsoplysninger, der kræver, at ændre alle LEGITIMATIONSOPLYSNINGER tilladelser.

    -   Du kan bruge en samlet tjenestekonto for SQL Server til at kommunikere med Azure fjernserveren, når følgende betingelser er opfyldt alle.

        -   Den tjenestekonto, som forekomsten af SQL Server kører under er en domænekonto.

        -   Domænekontoen hører til et domæne, hvis Active Directory er knyttet til Azure Active Directory.

        -   Azure fjernserveren er konfigureret til at understøtte Azure Active Directory-godkendelse.

        -   Den tjenestekonto, som forekomsten af SQL Server kører under skal konfigureres som en dbmanager eller systemadministrator konto på Azure fjernserveren.

5.  For at konfigurere en database til Stræk Database skal du køre kommandoen JUSTER DATABASE.

    1.  Angive navnet på en eksisterende Azure server for argumentet SERVER, herunder den `.database.windows.net` del af navnet \- for eksempel `MyStretchDatabaseServer.database.windows.net`.

    2.  Angive legitimationsoplysninger for en eksisterende administrator med argumentet LEGITIMATIONSOPLYSNINGER, eller angive FEDERATED\_SERVICE\_konto = ON. I følgende eksempel indeholder en eksisterende legitimationsoplysninger.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO
    ```

## <a name="next-steps"></a>Næste trin
-   [Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md) til at aktivere flere tabeller.

-   [Overvåge Stræk Database](sql-server-stretch-database-monitor.md) til at se status for dataoverførsel.

-   [Afbryde og genoptage Stræk Database](sql-server-stretch-database-pause.md)

-   [Administrere og foretage fejlfinding af Stræk Database](sql-server-stretch-database-manage.md)

-   [Sikkerhedskopiering Stræk-kompatible databaser](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Se også

[Identificere databaser og tabeller for Stræk Database](sql-server-stretch-database-identify-databases.md)

[ALTER sæt databaseindstillinger (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[FirewallRule]: ./media/sql-server-stretch-database-enable-database/firewall.png
