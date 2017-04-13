<properties 
    pageTitle="Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Azure portal | Microsoft Azure" 
    description="Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af portalen Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Konfigurere geografisk gentagelse til Azure SQL-Database ved hjælp af Azure portal


> [AZURE.SELECTOR]
- [Oversigt](sql-database-geo-replication-overview.md)
- [Azure-portalen](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

I denne artikel viser, hvordan du konfigurerer aktive geografisk-gentagelse til SQL-Database ved hjælp af [Azure-portalen](http://portal.azure.com).

For at starte failover ved hjælp af Azure portal, skal du se [starte en planlagt eller uventet sekundær server til Azure SQL-Database ved hjælp af Azure portal](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Aktive geografisk gentagelse (læsbare secondaries) er nu tilgængelig for alle databaser i alle niveauer i tjenesten. I April 2017 typen ikke kan læses sekundær skal udgå og eksisterende ikke kan læses databaser vil automatisk blive opgraderet til læsbare secondaries.

Hvis du vil konfigurere geografisk replikering ved hjælp af portalen Azure, skal du i følgende ressource:

- En Azure SQL database - den primære database, du vil gentage til et andet geografisk område.

## <a name="add-secondary-database"></a>Tilføje sekundær database

Følgende trin opretter en ny sekundær database i et geografisk gentagelse partnerskab.  

Hvis du vil tilføje en sekundær, skal du være abonnement ejeren eller medejer. 

Sekundær databasen, har samme navn som den primære database og, som standard har det samme niveau i tjenesten. Sekundær databasen kan være en enkelt database eller en elastiske database. Du kan finde yderligere oplysninger finder [Service niveauer](sql-database-service-tiers.md).
Når sekundært er oprettet og ud, begynder data replikering fra den primære database til den nye sekundære database. 

> [AZURE.NOTE] Kommandoen mislykkes, hvis der findes allerede partner-database (for eksempel - som resultat afslutter en tidligere geografisk gentagelse relation).

### <a name="add-secondary"></a>Tilføje sekundær

1. Gå til den database, du vil konfigurere til geografisk gentagelse i [Azure-portalen](http://portal.azure.com).
2. Vælg **Geografisk gentagelse**på siden SQL-database, og vælg derefter området til at oprette den sekundære database. Du kan vælge en hvilken som helst område end det område, der er vært for den primære database, men [parvis område](../best-practices-availability-paired-regions.md) anbefales.

    ![konfigurere geografisk gentagelse](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Vælg eller konfigurere server og priser niveau for den sekundære database.

    ![konfigurere sekundær](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Du kan også kan du tilføje en sekundær database til en gruppe af elastiske database:

 Klik på **elastiske database puljen** for at oprette den sekundære database i en gruppe, og vælg en gruppe på serveren. En samling skal allerede findes på serveren, som arbejdsprocessen ikke oprette en gruppe.

6. Klik på **Opret** for at tilføje sekundært.
 
6. Sekundær databasen er oprettet, og processen forhåndsudfyldningen begynder. 
 
    ![konfigurere sekundær](./media/sql-database-geo-replication-portal/seeding0.png)

7. Når processen forhåndsudfyldningen er fuldført, vises den sekundære database dens status.

    ![forhåndsudfyldning fuldført](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Fjerne sekundære database

Handlingen permanent ophører gentagelse til den sekundære database og ændrer sekundært rolle til en almindelig læse / skrive-database. Hvis forbindelsen til den sekundære database er brudt, kommandoen blev fuldført, men sekundær behandler ikke bliver skrivebeskyttet indtil efter connectivity genoprettes.  

1. Gå til den primære database i tilknytningen af geografisk gentagelse i [Azure-portalen](http://portal.azure.com).
2. Vælg **Geografisk gentagelse**på siden SQL-Database.
3. Vælg den database, du vil fjerne fra tilknytningen af geografisk gentagelse på listen **SECONDARIES** .
4. Klik på **Stop gentagelse**.

    ![fjerne sekundære](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Klikke på **Stop gentagelse** åbnes vises en bekræftelsesmeddelelse så skal du klikke på **Ja** for at fjerne databasen fra tilknytningen af geografisk gentagelse (angive den til en skrivebeskyttet database ikke er en del af en hvilken som helst gentagelse).


## <a name="next-steps"></a>Næste trin

- Hvis du vil vide mere om aktive geografisk-gentagelse, se - [Aktive geografisk-gentagelse](sql-database-geo-replication-overview.md)
- Se [Oversigt over Business løbende](sql-database-business-continuity.md) til en business løbende oversigt og scenarier

