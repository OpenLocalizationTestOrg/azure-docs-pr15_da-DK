<properties
   pageTitle="Retningslinjer for og Azure SQL Database generelt begrænsninger"
   description="Denne side beskrives nogle generelle begrænsninger til Azure SQL-Database samt områder af interoperabilitet og support."
   services="sql-database"
   documentationCenter="na"
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/06/2016"
   ms.author="carlrab" />

# <a name="azure-sql-database-general-limitations-and-guidelines"></a>Retningslinjer for og Azure SQL Database generelt begrænsninger

Dette emne indeholder generelle begrænsninger og vejledninger til Azure SQL-Database. For at få overblik over kvoter, ressourcestyring og support, skal du se [Yderligere ressourcer](#additional-guidelines) i slutningen af dette emne.

## <a name="connectivity-and-authentication"></a>Forbindelsen og godkendelse

  - Windows-godkendelse understøttes ikke. Se [Administration af databaser og logon i Azure SQL-Database](sql-database-manage-logins.md). Azure Active Directory-godkendelse understøttes dog med visse begrænsninger. Se [oprette forbindelse til SQL-Database med Azure Active Directory-godkendelse](sql-database-aad-authentication.md).

  - Microsoft Azure SQL Database understøtter tabeldata stream (TDS) protocol klientversion 7.3 eller senere.

  - Kun TCP/IP-forbindelser er tilladt.

  - SQL Server 2008 SQL Server-browseren understøttes ikke, fordi Microsoft Azure SQL Database ikke har dynamiske porte, kun port 1433.

## <a name="sql-server-agentjobs"></a>SQL Server-Agent/job

Microsoft Azure SQL Database understøtter ikke SQL Server Agent, men du kan bruge elastiske job til at køre job på tværs af en-til-mange-databaser. Du kan finde flere oplysninger om elastiske job [elastiske job](sql-database-elastic-jobs-overview.md).

## <a name="sql-server-collation-support"></a>Understøttelse af SQL Server-sortering

Standard databasesorteringen bruges af Microsoft Azure SQL-Database er **SQL_LATIN1_GENERAL_CP1_CI_AS**, hvor **LATIN1_GENERAL** er engelsk (USA), **CP1** er tegntabel 1252, **CI** der skelnes ikke mellem, og **som** er uden accent. Det er ikke muligt at ændre sorteringen for version 12 databaser. Du kan finde flere oplysninger om, hvordan du kan angive sorteringen, [SÆTVIS (Transact-SQL)](https://msdn.microsoft.com/library/ms184391.aspx).

## <a name="naming-requirements"></a>Krav til navngivning

Visse brugernavne er ikke tilladt af sikkerhedsmæssige årsager. Du kan ikke bruge følgende navne:

 - **administrator**
 - **administrator**
 - **Gæst**
 - **rod**
 - **systemadministratorens**

Navne for alle nye objekter skal overholde SQL Server-reglerne for id'er. Du kan finde flere oplysninger under [id'er](https://msdn.microsoft.com/library/ms175874.aspx).

Desuden logon og bruger navne må ikke indeholde den \ tegn (Windows-godkendelse ikke understøttes).

## <a name="additional-guidelines"></a>Yderligere retningslinjer

- Ud over de generelle begrænsninger, der er beskrevet i denne artikel, er SQL-Database bestemte kvoter for serverforbrug og begrænsninger, der er baseret på dine **webtjenesten**. Du kan finde en oversigt over service niveauer, [SQL-Database service niveauer](sql-database-service-tiers.md).

- Du kan finde andre begrænsninger for SQL-Database, [Azure SQL Database ressource begrænsninger](sql-database-resource-limits.md).

- Sikkerhed i relaterede retningslinjer [retningslinjer for Azure SQL Database sikkerhed og begrænsninger](sql-database-security-guidelines.md).

- Et andet relateret område omgiver kompatibilitet med Azure SQL-Database med lokale versioner af SQL Server, såsom SQL Server 2014 og SQL Server 2016. Den seneste version 12-version af Azure SQL-Database, der har skrevet mange forbedringer i dette område. Få mere at vide ved at se [Hvad er nyt i version 12 SQL-Database](sql-database-v12-whats-new.md).

- Du kan finde oplysninger om driver tilgængelighed og support for SQL-Database, [Dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md).
