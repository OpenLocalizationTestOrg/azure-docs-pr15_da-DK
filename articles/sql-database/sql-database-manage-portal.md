<properties
    pageTitle="Administrere Azure SQL-Database ved hjælp af portalen Azure | Microsoft Azure"
    description="Lær at bruge Azure-portalen til at administrere en relationel database i skyen ved hjælp af portalen Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.date="09/19/2016"
    ms.author="sstein"/>


# <a name="managing-azure-sql-databases-using-the-azure-portal"></a>Administrere Azure SQL-databaser ved hjælp af portalen Azure


> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

[Azure portal](https://portal.azure.com/) kan du oprette, overvåge og administrere Azure SQL-databaser og servere. Denne artikel indeholder en hurtig beskrivelse og links til oplysninger om de mest almindelige opgaver.

## <a name="view-your-azure-sql-databases-servers-and-pools"></a>Få vist dine Azure SQL-databaser, servere og grupper

Klik på **flere tjenester**, og skriv **SQL** i søgefeltet for at få vist de tilgængelige SQL-Database-tjenester:

![SQL-Database](./media/sql-database-manage-portal/sql-services.png)


## <a name="how-do-i-create-or-view-azure-sql-databases"></a>Hvordan opretter eller få vist Azure SQL-databaser?

For at åbne bladet **SQL-databaser** skal du klikke på **SQL-databaser**, og klik på den database, du vil arbejde med, eller klik på **+ Tilføj** for at oprette en SQL-database. Få mere at vide under [oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md).


![SQL-databaser](./media/sql-database-manage-portal/sql-databases.png)


## <a name="how-do-i-create-or-view-azure-sql-servers"></a>Hvordan opretter eller få vist Azure SQL-servere?

For at åbne bladet **SQL-servere** skal du klikke på **SQL-servere**, og klik på den server, du vil arbejde med, eller klik på **+ Tilføj** for at oprette en SQL server. Få mere at vide under [oprette en SQL-database i minutter ved hjælp af portalen Azure](sql-database-get-started.md).

![SQL-servere](./media/sql-database-manage-portal/sql-servers.png)


## <a name="how-do-i-create-or-view-sql-elastic-pools"></a>Hvordan opretter eller få vist SQL elastiske grupper?

Klik på **SQL elastiske grupper**for at åbne bladet **SQL elastiske grupper** , og derefter klikke på den gruppe, du vil arbejde med, eller klik på **+ Tilføj** for at oprette en gruppe. Få mere at vide under [oprette en elastiske database puljen ved hjælp af Azure portal](sql-database-elastic-pool-create-portal.md).

![SQL elastiske grupper](./media/sql-database-manage-portal/elastic-pools.png)



## <a name="how-do-i-update-or-view-sql-database-settings"></a>Hvordan opdaterer eller få vist SQL database-indstillinger?

For at få vist eller opdatere indstillingerne for din database skal du klikke på den ønskede indstilling på bladet SQL-database:


![Indstillinger for SQL-database](./media/sql-database-manage-portal/settings.png)


## <a name="how-do-i-find-a-sql-databases-fully-qualified-server-name"></a>Hvordan finder jeg en SQL-databaser fuldstændige servernavn?

Klik på **Oversigt** på bladet **SQL-database** for at få vist servernavn databaser, og Bemærk, at navnet på server:


![Indstillinger for SQL-database](./media/sql-database-manage-portal/server-name.png)


## <a name="how-do-i-manage-firewall-rules-to-control-access-to-my-sql-server-and-database"></a>Hvordan administrerer jeg firewallregler til at styre adgangen til min SQL server og database?

Klik på **Angiv server firewall** på bladet **SQL-database** for at få vist, oprette eller opdatere firewallregler. Yderligere oplysninger finder du se [konfigurere en regel for Azure SQL-Database serverniveau firewall ved hjælp af portalen Azure](sql-database-configure-firewall-settings.md).


![firewallregler](./media/sql-database-manage-portal/sql-database-firewall.png)


## <a name="how-do-i-change-my-sql-database-service-tier-or-performance-level"></a>Hvordan ændrer jeg min SQL database-tjenesten niveau eller ydeevne niveau?


Hvis du vil opdatere service niveau eller ydeevne niveauet for en SQL-database, skal du klikke på **priser niveau (skala DTUs)** på bladet **SQL-database** . Yderligere oplysninger finder du [ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database](sql-database-scale-up.md).


![priser niveauer](./media/sql-database-manage-portal/pricing-tier.png)


## <a name="how-do-i-configure-auditing-and-threat-detection-for-a-sql-database"></a>Hvordan kan jeg konfigurere overvågning og truslen registrering for en SQL-database?

Hvis du vil konfigurere overvågning og truslen registrering for en SQL-database, skal du klikke på **overvågning og truslen registrering** på bladet **SQL-database** . Yderligere oplysninger finder du [kommer i gang med SQL database overvågning](sql-database-auditing-get-started.md)og [komme i gang med SQL Database truslen registrering](sql-database-threat-detection-get-started.md).


## <a name="how-do-i-configure-dynamic-data-masking-for-a-sql-database"></a>Hvordan kan jeg konfigurere dynamiske data masking til en SQL-database?

Hvis du vil konfigurere dynamiske data masking til en SQL-database, skal du klikke på **dynamiske data masking** på bladet **SQL-database** . Yderligere oplysninger finder du [kommer i gang med SQL Database dynamiske Data Masking](sql-database-dynamic-data-masking-get-started.md).


## <a name="how-do-i-configure-transparent-data-encryption-tde-for-a-sql-database"></a>Hvordan kan jeg konfigurere gennemsigtig datakryptering (TDE) til en SQL-database?

For at konfigurere kryptering af gennemsigtig data til en SQL-database, skal du klikke på **gennemsigtig datakryptering** på bladet **SQL-database** . Yderligere oplysninger finder du [Aktivere TDE på en database ved hjælp af portalen](https://msdn.microsoft.com/library/dn948096#Anchor_1).

## <a name="how-do-i-view-or-change-the-max-size-of-a-sql-database"></a>Hvordan få vist eller ændre den maksimale størrelse for en SQL-database?

For at få vist eller ændre størrelsen en SQL-database skal du klikke på **databasestørrelse** på bladet **SQL-database** . Opdater den maksimale størrelse af en database ved at ændre niveauet for tjenesten niveau eller ydeevne. Yderligere oplysninger finder du [ændre service niveau og ydeevne niveauet (priser niveau) for en SQL-database](sql-database-scale-up.md).

## <a name="how-do-i-monitor-and-improve-the-performance-of-a-sql-database"></a>Hvordan overvåge og forbedre ydeevnen for en SQL-database?

Klik på **ydeevne oversigt** på bladet **SQL-database** for at overvåge og forbedre ydeevnen egenskaber ved en SQL-database. Yderligere oplysninger finder du [SQL Database ydeevne indsigt](sql-database-performance.md).


## <a name="how-do-i-configure-geo-replication"></a>Hvordan kan jeg konfigurere geografisk gentagelse?

Hvis du vil konfigurere geografisk gentagelse til en SQL-database, skal du klikke på **Geografisk gentagelse** på bladet **SQL-database** . Yderligere oplysninger finder du [Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Azure portal](sql-database-geo-replication-portal.md).


## <a name="how-do-i-failover-to-a-geo-replicated-sql-database"></a>Hvordan får jeg failover til en geografisk replikerede SQL-database?

Sådan flytter du til en geografisk replikerede sekundær, klikke på **Geografisk gentagelse** i bladet **SQL-database** , og klik derefter på **Failover**. Yderligere oplysninger finder du [starte en planlagt eller uventet sekundær server til Azure SQL-Database ved hjælp af Azure portal](sql-database-geo-replication-failover-portal.md).


## <a name="how-do-i-copy-a-sql-database"></a>Hvordan kan jeg kopiere en SQL-database?

Du kan kopiere en SQL-database ved at klikke på **Kopiér** på bladet **SQL-database** . Yderligere oplysninger finder du [kopiere en Azure SQL-database ved hjælp af portalen Azure](sql-database-copy-portal.md).


![Indstillinger for SQL-database](./media/sql-database-manage-portal/sql-database-copy.png)

## <a name="how-do-i-archive-an-azure-sql-database-to-a-bacpac-file"></a>Hvordan jeg arkivere Azure SQL-database til en BACPAC fil?

Klik på **Eksporter** på bladet **SQL-database** for at oprette en BACPAC af en SQL-database. Yderligere oplysninger finder du [arkiv Azure SQL-database til en BACPAC fil ved hjælp af portalen Azure](sql-database-export.md).


![SQL-database Eksportér](./media/sql-database-manage-portal/sql-database-export.png)



## <a name="how-do-i-restore-a-sql-database-to-a-previous-point-in-time"></a>Hvordan kan jeg gendanne en SQL-database til et tidligere punkt i gang?

Hvis du vil gendanne en SQL-database, skal du klikke på **Gendan** på bladet **SQL-database** . Yderligere oplysninger finder du [gendanner en Azure SQL-Database til et tidligere tidspunkt ved hjælp af Azure portal](sql-database-point-in-time-restore-portal.md).


![Indstillinger for SQL-database](./media/sql-database-manage-portal/sql-database-restore.png)


## <a name="how-do-i-create-an-azure-sql-database-from-a-bacpac-file"></a>Hvordan opretter jeg en Azure SQL-database fra en BACPAC fil?

Hvis du vil oprette en SQL-database fra en BACPAC-fil, skal du klikke på **Indlæs database** på bladet **SQL server** . Få mere at vide i [importere en BACPAC fil for at oprette en Azure SQL-database](sql-database-import.md).


![SQL server](./media/sql-database-manage-portal/server-commands.png)


## <a name="how-do-i-restore-a-deleted-sql-database"></a>Hvordan kan jeg gendanne en slettet SQL-database?

Hvis du vil gendanne en slettet SQL-database, skal du klikke på **slettet databaser** på bladet **SQL server** (den SQL server, der indeholdt den database, der er blevet slettet). Yderligere oplysninger finder du [gendanne en slettet Azure SQL-database ved hjælp af portalen Azure](sql-database-restore-deleted-database-portal.md).

## <a name="how-do-i-delete-a-sql-database"></a>Hvordan sletter jeg en SQL-database?

Hvis du vil slette en SQL-database, skal du klikke på **Slet** i bladet **SQL-database** . 

![Indstillinger for SQL-database](./media/sql-database-manage-portal/sql-database-delete.png)



## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database](sql-database-technical-overview.md)
- [Overvåge og administrere en elastiske database puljen ved hjælp af Azure portal](sql-database-elastic-pool-manage-portal.md)
