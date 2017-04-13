<properties
    pageTitle="Overvåge databaseydeevnen for i Azure SQL-Database | Microsoft Azure"
    description="Få mere at vide om indstillinger for overvågning af databasen med Azure værktøjer og administration af dynamiske visninger."
    keywords="database overvågning, databasens ydeevne i skyen"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="09/27/2016"
    ms.author="carlrab"/>

# <a name="monitoring-database-performance-in-azure-sql-database"></a>Overvåge databaseydeevnen for i Azure SQL-Database
Overvågning af ydeevnen af en SQL-database i Azure starter med overvågning ressource anvendelsen i forhold til niveauet af database-ydeevne, du vælger. Overvågning hjælper med at du bestemme, om din database er unødvendig kapacitet eller er et problem, fordi ressourcer er maxed ud af, og beslut derefter, om det er tid til at justere ydeevneniveau og [webtjenesten](sql-database-service-tiers.md) i databasen. Du kan overvåge databasen ved hjælp af grafiske værktøjer i [Azure portal](https://portal.azure.com) eller ved hjælp af SQL- [Administration af dynamiske visninger](https://msdn.microsoft.com/library/ms188754.aspx).

## <a name="monitor-databases-using-the-azure-portal"></a>Overvåge databaser ved hjælp af portalen Azure

Du kan overvåge anvendelsen af en enkelt database ved at vælge din database og klikke på **overvågnings** diagrammet i [Azure-portalen](https://portal.azure.com/). Dette viser et **metrisk** vindue, du kan ændre ved at klikke på knappen **Rediger diagram** . Tilføj følgende målepunkter:

- CPU procentdel
- DTU procentdel
- Data EY procentdel
- Database størrelse procentdel

Når du har tilføjet disse målepunkter, kan du fortsætte med at få dem vist i **overvågnings** diagrammet med flere detaljer i vinduet **metrisk** . Alle fire målepunkter viser den gennemsnitlige udnyttelse procentdel i forhold til **DTU** af databasen. Artiklen [service niveauer](sql-database-service-tiers.md) for at få oplysninger om DTUs.

![Nyt niveau overvågning af databaseydeevnen i.](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

Du kan også konfigurere beskeder på ydeevne målepunkter. Klik på knappen **Tilføj påmindelse** i vinduet **metrisk** . Følg guiden for at konfigurere din besked. Du har mulighed for at besked, hvis målene overskrider en bestemt grænse, eller hvis metrikværdien falder under en bestemt grænse.

Eksempelvis hvis du forventer arbejdsbelastningen på databasen kan vokse, kan du vælge til at konfigurere en besked via mail, når databasen når 80% på ethvert af ydeevnen målepunkter. Du kan bruge det som en tidlig varsling til at regne ud, når du muligvis nødt til at skifte til det næste ydeevneniveau.

Ydeevnen målepunkter kan også hjælpe dig med at fastlægge, om du kan få nedgradere til et lavere ydeevneniveau for. Forudsætter, at du bruger en Standard S2 database og alle ydeevne målepunkter viser, at databasen på gennemsnit ikke bruger mere end 10% til enhver tid. Det er muligt, at databasen fungerer godt i Standard S1. Dog være opmærksom på arbejdsbelastninger, som forøgelse eller varierer før du foretager beslutning til at flytte til et lavere ydeevneniveau for.

## <a name="monitor-databases-using-dmvs"></a>Overvåge databaser ved hjælp af DMVs

De samme mål, som vises i portalen er også tilgængelig via systemvisninger: [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) i logiske **master** databasen på din server og [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) i databasen. Brug **sys.resource_stats** , hvis du har brug for til at overvåge kvartalsvise data på tværs af en længere periode. Brug **sys.dm_db_resource_stats** , hvis du har brug for til at overvåge mere detaljerede data i en mindre tidsramme. Du kan finde yderligere oplysninger finder [Azure SQL Database ydeevne vejledning](sql-database-performance-guidance.md#monitoring-resource-use-with-sysresourcestats).

>[AZURE.NOTE] **sys.dm_db_resource_stats** returnerer en tom resultatsæt, når den bruges i Web- og Business edition databaser, som er udgået.

Du kan overvåge individuelle databaser i puljen med de teknikker, der er beskrevet i dette afsnit for elastiske database grupper. Men du kan også overvåge puljen som helhed. Finde oplysninger i [skærm og administrere en elastiske database puljen](sql-database-elastic-pool-manage-portal.md).
