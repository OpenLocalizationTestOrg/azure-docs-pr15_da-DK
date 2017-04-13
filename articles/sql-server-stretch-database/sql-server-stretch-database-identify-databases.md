<properties
    pageTitle="Identificere databaser og tabeller for Stræk Database ved at køre Stræk Database Advisor | Microsoft Azure"
    description="Lær at identificere databaser og tabeller, der er kandidater til Stræk Database."
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
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificere databaser og tabeller for Stræk Database ved at køre Stræk Database Advisor

For at identificere databaser og tabeller, der er kandidater til Stræk Database skal du hente SQL Server 2016 Upgrade Advisor og køre Stræk Database Advisor. Stræk Database Advisor identificerer også blokering problemer.

## <a name="download-and-install-upgrade-advisor"></a>Downloade og installere Upgrade Advisor
Hent og Installer Upgrade Advisor fra [her](http://go.microsoft.com/fwlink/?LinkID=613421). Dette værktøj er ikke inkluderet på installationsmedierne SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Køre Advisor Stræk Database

1.  Køre Upgrade Advisor.

2.  Vælg **scenarier**, og vælg derefter **Kør STRÆK DATABASE ADVISOR**.

3.  Klik på **Vælg DATABASER til at ANALYSERE**bladet **Køre Stræk Database Advisor** .

4.  Angiv eller Vælg navnet på den server, og godkendelse ringet bladet **Vælg databaser** . Klik på **Opret forbindelse**.

5.  Der vises en liste over databaser på den valgte server. Vælg de databaser, du vil analysere. Klik på **Vælg**.

6.  Klik på **Kør**på bladet **Køre Stræk Database Advisor** .  Analysen kører.

## <a name="review-the-results"></a>Få vist resultaterne

1.  Når analysen på bladet **Analyzed databaser** er færdig, skal du vælge en af de databaser, som du analysere dem for at få vist bladet **analyseresultaterne igen** .

    Bladet **analyseresultaterne** viser en liste over anbefalede tabeller i den valgte database, der opfylder kriteriet standard anbefaling.

2.  Vælg en af de anbefalede tabeller til at vise bladet **tabellen resultater** på listen over tabeller på bladet **analyseresultaterne igen** .

    Hvis der problemer med blokering via, viser bladet **tabellen resultater** blokering problemer for den markerede tabel. Du kan finde oplysninger om blokering problemer, der er registreret af Stræk Database Advisor [begrænsninger for Stræk Database](sql-server-stretch-database-limitations.md).

3.  Vælg en af problemerne, at få vist flere oplysninger om det valgte problem i på listen over blokering problemer på bladet **tabellen resultater** , og foreslår afhjælpning trin. Implementere foreslåede afhjælpning trin, hvis du vil konfigurere den markerede tabel til Stræk Database.

## <a name="next-step"></a>Næste trin
Aktivere Stræk Database.

-   For at aktivere Stræk Database på en **database**skal du se [Aktivere Stræk Database til en database](sql-server-stretch-database-enable-database.md).

-   For at aktivere Stræk Database på en anden **tabel**, når Stræk er allerede aktiveret på databasen, kan du se [Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Se også

[Begrænsninger for Stræk Database](sql-server-stretch-database-limitations.md)

[Aktivere Stræk Database til en database](sql-server-stretch-database-enable-database.md)

[Aktivere Stræk Database til en tabel](sql-server-stretch-database-enable-table.md)

[Alle emner til Azure SQL Server-Stræk Database-tjenesten](sql-server-stretch-database-index-all-articles.md)
