<properties
    pageTitle="Foretage fejlfinding af almindelige forbindelsesproblemer til Azure SQL-Database"
    description="Trin til at identificere og løse almindelige forbindelsesfejl i til Azure SQL-Database."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Foretage fejlfinding af forbindelsesproblemer til Azure SQL-Database

Når forbindelsen til Azure SQL-Database ikke, modtager du [fejlmeddelelser](sql-database-develop-error-messages.md). I denne artikel er et centralt emne, der hjælper dig med at løse problemer med serverforbindelsen Azure SQL-Database. Den introducerer [medfører, at fælles](#cause) forbindelsesproblemer, anbefaler [et fejlfindingsværktøj](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) , der hjælper med at identitet problemet og indeholder trin til fejlfinding for at løse [midlertidige fejl](#troubleshoot-transient-errors) og [fast eller permanent fejl](#troubleshoot-the-persistent-errors). Til sidst skal viser det [alle de relevante artikler til problemer med serverforbindelsen Azure SQL-Database](#all-topics-for-azure-sql-database-connection-problems).

Hvis du støder på problemer med mailforbindelsen, kan du prøve fejlfinding i forbindelse med trinnene, der er beskrevet i denne artikel.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Årsag

Forbindelsesproblemer kan skyldes et af følgende:

- Fejl ved at anvende bedste fremgangsmåder og retningslinjer for design under designprocessen programmet på computeren.  Se [Oversigt over SQL Database-udvikling](sql-database-develop-overview.md) at komme i gang.
- Konfigureres igen Azure SQL-Database
- Firewall-indstillinger
- Timeout for
- Forkert logonoplysninger
- Højeste antal nået på nogle ressourcer, Azure SQL-Database

Generelt kan forbindelsesproblemer til Azure SQL-Database klassificeres på følgende måde:

- [Midlertidige fejl (forbigående eller forbigående)](#troubleshoot-transient-errors)
- [Fast eller permanent fejl (fejl, der gentages regelmæssigt)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Prøv fejlfindingsværktøjet til problemer med serverforbindelsen Azure SQL-Database

Hvis du støder på et bestemt forbindelsesfejl, kan du prøve [dette værktøj](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), som hjælper dig med hurtigt identitet og løse problemet.

## <a name="troubleshoot-transient-errors"></a>Midlertidige fejlfinding
Hvis dit program er opstår midlertidige fejl, skal du gennemgå følgende artikler for at tip om at foretage fejlfinding af og reducere hyppigheden for disse fejl:

- [Fejlfinding i forbindelse med Database &lt;x&gt; på serveren &lt;y&gt; ikke er tilgængelig (fejl: 40613)](sql-database-troubleshoot-connection.md)
- [Fejlfinding i forbindelse med, diagnosticere og forhindre SQL forbindelsesfejl og midlertidige fejl for SQL-Database](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Fejlfinding i forbindelse med fast fejl (fejl permanent)

Hvis programmet ikke vedvarende kan oprette forbindelse til Azure SQL-Database, angiver det som regel et problem med et af følgende:

- Konfiguration af firewall. Azure SQL-database eller klientsiden firewall blokerer forbindelser til Azure SQL-Database.
- Netværk konfigureres igen på klientsiden: en ny IP-adresse eller en proxy-server.
- Brugerfejl: eksempelvis skrevet forkert parametrene for dataforbindelse, som servernavnet i forbindelsesstrengen.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Trin til at løse problemer med fast serverforbindelsen

1.  Konfigurere [firewallregler](sql-database-configure-firewall-settings.md) til at tillade klienten IP-adresse.
2.  Sørg for, at port 1433 er åbent for udgående forbindelser alle firewalls mellem klienten og på internettet. Se [konfigurere Windows Firewall for at tillade adgang til SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) for ekstra oplysninger.
3.  Bekræft din forbindelsesstreng og andre indstillinger for forbindelsen. I afsnittet forbindelsesstreng i [connectivity problemer emne](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.  Kontrollér tjenestetilstand i dashboardet. Hvis du mener, at der er en internationale afbrydelse, kan du se [gendanne fra en afbrydelse](sql-database-disaster-recovery.md) til trin til at gendanne til et nyt område.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Alle emner til Azure SQL-Database forbindelsesproblemer

I følgende tabel vises hver forbindelse problem emne, der gælder direkte til tjenesten Azure SQL-Database.


| &nbsp; | Titel | Beskrivelse |
| --: | :-- | :-- |
| 1 | [Foretage fejlfinding af forbindelsesproblemer til Azure SQL-Database](sql-database-troubleshoot-common-connection-issues.md) | Dette er landingssiden til fejlfinding for forbindelsesproblemer i Azure SQL-Database. Det beskrives, hvordan du identificerer og løser midlertidige fejl og fast eller permanent fejl i Azure SQL-Database. |
| 2 | [Fejlfinding i forbindelse med, diagnosticere og forhindre SQL forbindelsesfejl og midlertidige fejl for SQL-Database](sql-database-connectivity-issues.md) | Lær at foretage fejlfinding af diagnosticere og forhindre en SQL-forbindelsesfejl eller midlertidige fejl i Azure SQL-Database. |
| 3 | [Generelle retningslinjer for forbigående fejl fejlhåndtering](best-practices-retry-general.md) | Indeholder generelle retningslinjer for håndtering af midlertidige fejl, når du opretter forbindelse til Azure SQL-Database. |
| 4 | [Udføre fejlfinding på forbindelsesproblemer med Microsoft Azure SQL-Database](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Dette værktøj kan identitet løse dit problem forbindelsesfejl. |
| 5 | [Fejlfinding i forbindelse med "Database &lt;x&gt; på serveren &lt;y&gt; er ikke tilgængelig i øjeblikket. Prøv forbindelsen senere"fejl](sql-database-troubleshoot-connection.md) | Beskriver, hvordan du identificerer og løser en 40613 fejl: "Database &lt;x&gt; på serveren &lt;y&gt; er ikke tilgængelig i øjeblikket. Skal du oprette forbindelse igen senere." |
| 6 | [SQL-fejlkoder for SQL-Database-klientprogrammer: Webdatabase forbindelsesfejl og andre problemer](sql-database-develop-error-messages.md) | Indeholder oplysninger om SQL fejlkoder til SQL-Database klientprogrammer som almindelige database forbindelsesfejl, database kopi problemer og generelle fejl. |
| 7 | [Azure SQL-Database ydeevne vejledning til enkelt databaser](sql-database-performance-guidance.md) | Giver vejledning for at hjælpe dig med at afgøre, hvilke webtjenesten er rigtige for dit program. Indeholder også anbefalinger for justering af dit program du får mest muligt ud af dine Azure SQL-Database. |
| 8 | [SQL-Database udvikling oversigt](sql-database-develop-overview.md) | Indeholder links til kodeeksempler for forskellige teknologier, som du kan bruge til at oprette forbindelse til og interagere med Azure SQL-Database. |
| 9 | Opgradere til Azure SQL-Database version 12 side ([Azure-portalen](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Giver vejledning for at opgradere eksisterende Azure SQL Database V11 servere og databaser til version 12 Azure SQL-Database ved hjælp af Azure portal eller PowerShell. |


## <a name="next-steps"></a>Næste trin

- [Foretage fejlfinding af problemer med ydeevnen Azure SQL-Database](sql-database-troubleshoot-performance.md)
- [Fejlfinding i forbindelse med spørgsmål om tilladelser Azure SQL-Database](sql-database-troubleshoot-permissions.md)
- [Se alle emner for tjenesten Azure SQL-Database](sql-database-index-all-articles.md)
- [Søge i dokumentationen til Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Få vist de seneste opdateringer til tjenesten Azure SQL-Database](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>Yderligere ressourcer

- [SQL-Database udvikling oversigt](sql-database-develop-overview.md)
- [Generelle retningslinjer for forbigående fejl fejlhåndtering](../best-practices-retry-general.md)
- [Dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md)
- [Læringssti til brug af Azure SQL-Database](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [Læringssti til brug af elastiske Databasefunktioner og værktøjer](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 
