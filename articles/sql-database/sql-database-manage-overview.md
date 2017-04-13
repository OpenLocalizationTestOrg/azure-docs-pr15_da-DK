<properties
    pageTitle="Oversigt: værktøjer til administration for SQL-Database | Microsoft Azure"
    description="Sammenligner funktioner og indstillinger for administration af Azure SQL-Database"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>Oversigt: værktøjer til administration for SQL-Database

Dette emne udforsker og sammenligner funktioner og indstillinger for administration af Azure SQL-databaser, så du kan vælge det rigtige værktøj til jobbet, din virksomhed, og du. Vælge værktøjet højre afhænger af, hvor mange databaser administrerer du, skal opgaven, og hvor ofte en opgave er udført.

## <a name="azure-portal"></a>Azure-portalen

[Azure-portalen](https://portal.azure.com) er en webbaseret program, hvor du kan oprette, opdatere, og Slet databaser og logiske servere og overvåge databaseaktivitet i. Dette værktøj er velegnet, hvis du er lige gået i gang med Azure, administration af nogle databaser, eller har brug at gøre noget hurtigt.

Se [administrere SQL-databaser ved hjælp af portalen Azure](sql-database-manage-portal.md)kan finde flere oplysninger om ved hjælp af portalen.

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio og SQL Server Data Tools i Visual Studio

SQL Server Management Studio (SSMS) og SQL Server Data Tools (SSDT) er klientværktøjer, der kører på computeren til at administrere og udvikling af databasen i skyen. Hvis du bruger et programudvikler kendskab til Visual Studio eller andre integreret udviklingsmiljøer (IDEs), [du prøve at bruge SSDT i Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Mange databaseadministratorer kender SSMS, som kan bruges med Azure SQL-databaser. [Hent den nyeste version af SSMS](https://msdn.microsoft.com/library/mt238290) og altid bruge den nyeste version, når du arbejder med Azure SQL-Database. Du kan finde flere oplysninger om administration af din Azure SQL-databaser med SSMS, kan du se [administrere SQL-databaser ved hjælp af SSMS](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Brug altid den seneste version af [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) og [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) skal være synkroniseret med opdateringer til Microsoft Azure og SQL-Database.


## <a name="powershell"></a>PowerShell

Du kan bruge PowerShell til at administrere databaser og elastiske database grupper, og til at automatisere Azure ressource installationer. Microsoft anbefaler at dette værktøj til at administrere et stort antal databaser og automatisere installation og ressourceændringer i et produktionsmiljø.

Kan finde flere oplysninger under [Administrere SQL-Database med PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Elastiske Databaseværktøjer
Bruge værktøjerne i elastiske database til at udføre handlinger som f.eks. 

* Udførelse af en T-SQL-script mod et sæt databaser ved hjælp af et [elastiske job](sql-database-elastic-jobs-overview.md)
* Flytte flere lejer model databaser til en enkelt lejer model med [opdelte Flet værktøj](sql-database-elastic-scale-overview-split-and-merge.md)
* Administration af databaser i en enkelt lejer model eller en med flere lejer model ved hjælp af [elastiske skala klientbibliotek](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Yderligere ressourcer

- [Azure ressourcestyring](https://azure.microsoft.com/features/resource-manager/)
- [Azure-automatisering](https://azure.microsoft.com/documentation/services/automation/)
- [Azure Scheduler](https://azure.microsoft.com/documentation/services/scheduler/)