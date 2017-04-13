<properties
    pageTitle="Oprette og administrere skalerede ud Azure SQL-databaser ved hjælp af elastiske job | Micosoft Azure"
    description="Gennemgå oprettelse og administration af et job elastiske database."
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/27/2016"
    ms.author="ddove"/>

# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Oprette og administrere skalerede ud Azure SQL-databaser ved hjælp af elastiske jobs (preview)

> [AZURE.SELECTOR]
- [Azure-portalen](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


**Elastiske Database job** forenkle administration af grupper af databaser ved at udføre administrative handlinger som skemaændringer, administration af legitimationsoplysninger, reference dataopdateringer, ydeevne dataindsamling eller lejer (kunde) telemetri af websteder. Elastiske Database job er tilgængelige via Azure portalen og PowerShell-cmdlet'er. Dog reduceret Azure portalen overflader funktionalitet, der er begrænset til udførelse af på tværs af alle databaser i en [elastiske Database puljen (preview)](sql-database-elastic-pool.md). Se [oprette og administrere jobs ved hjælp af PowerShell](sql-database-elastic-jobs-powershell.md)for at få adgang til yderligere funktioner og udførelse af scripts på tværs af en gruppe af databaser, herunder en brugerdefineret defineret af websteder eller et shard sæt (oprettet ved hjælp af [elastiske Database klientbibliotek](sql-database-elastic-scale-introduction.md)). Se [Oversigt over elastiske Database](sql-database-elastic-jobs-overview.md)kan finde flere oplysninger om job. 

## <a name="prerequisites"></a>Forudsætninger

* Et Azure-abonnement. Du kan finde en gratis prøveversion [gratis prøveversion af en måned](https://azure.microsoft.com/pricing/free-trial/).
* En elastiske database puljen. Se [om elastiske database grupper](sql-database-elastic-pool.md)
* Installation af elastiske job service databasekomponenter. Se [installere tjenesten elastiske database job](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Oprette sager

1. Ved hjælp af [Azure-portalen](https://portal.azure.com), fra en eksisterende elastiske database job puljen, skal du klikke på **Opret jobbet**.
2. Skrive i det brugernavn og adgangskoden til den databaseadministrator (oprettet på installation af job) til job kontrolelementet database (metadata storage for sager).

    ![Navngive jobbet skal du skrive eller indsætte kode og klikke på Kør][1]
2. Skriv et navn til opgaven i bladet **Oprette Job** .
3. Skriv det brugernavn og adgangskode for at oprette forbindelse til de destinationsadresse-databaser med de nødvendige tilladelser til udførelse af script kan udføres.
4. Indsæt eller skriv i scriptet T-SQL.
5. Klik på **Gem** , og klik derefter på **Kør**.

    ![Oprette sager og køre][5]

## <a name="run-idempotent-jobs"></a>Køre idempotent job

Når du kører et script i forhold til et sæt af databaser, skal du være sikker på, at script er idempotent. Det vil sige, skal scriptet kunne køre flere gange, selvom den mislykkes før i ufuldstændig tilstand. For eksempel, når et script mislykkes, jobbet automatisk forsøges indtil det lykkes (inden for angivne grænser, som vil afsendelsen logik vil til sidst holde op med den prøver igen). Den måde at gøre dette er at bruge den delsætningen "Hvis findes", og Slet eventuelle fundet forekomst før du opretter et nyt objekt. Et eksempel er vist her:

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Du kan også bruge delsætningen "Hvis ikke findes" før du opretter en ny forekomst:

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

Dette script opdaterer derefter tabellen tidligere har oprettet.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Kontrollere jobstatus

Når et job er startet, kan du se om dens forløb.

1. Klik på **Administrer sager**fra siden elastiske database puljen.

    ![Klik på "Administrer sager"][2]

2. Klik på navnet (a) på en sag. **STATUS** kan være "Afsluttet" eller "Mislykkedes". Oplysninger om den job vises (b) med dens datoen og klokkeslættet for oprettelsen og kører. Listen (c) under den, der viser status for scriptet mod hver database i puljen, giver oplysninger om dato og klokkeslæt.

    ![Markere et færdig job][3]


## <a name="checking-failed-jobs"></a>Kontrollere mislykkedes job

Hvis et job mislykkes, kan en log af kørslen blev fundet. Klik på navnet på det mislykkede job til at se detaljerne.

![Kontrollere en mislykkede job][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 
