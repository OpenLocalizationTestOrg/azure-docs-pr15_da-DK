<properties
    pageTitle="SQL-Database udvikle oversigt | Microsoft Azure"
    description="Få mere at vide om tilgængelige connectivity biblioteker og bedste fremgangsmåder for at oprette forbindelse til SQL Database-programmer."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>SQL-Database udvikling oversigt
I denne artikel indeholder en gennemgang af grundlæggende overvejelser, som en udvikler skal være opmærksom på, når du skriver kode til at oprette forbindelse til Azure SQL-Database.

## <a name="language-and-platform"></a>Sprog og platform
Kodeeksempler, der er tilgængelige for forskellige programming sprog og platforme. Du kan finde links til kodeeksempler på: 

* Du kan finde flere oplysninger: [dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Ressource begrænsninger
Azure SQL-Database, administrerer de ressourcer, der er tilgængelige for en database ved hjælp af to forskellige mekanismer: styring af ressourcer og aktivering af begrænsninger.

* Du kan finde flere oplysninger: [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md)

## <a name="security"></a>Sikkerhed
Azure SQL-Database indeholder ressourcer til at begrænse adgangen, beskyttelse af data og overvåge aktiviteter på en SQL-Database.

* Du kan finde flere oplysninger: [sikring af SQL-Database](sql-database-security.md)

## <a name="authentication"></a>Godkendelse
* Azure SQL-Database understøtter både SQL Server-godkendelse brugere og -logon, samt [Azure Active Directory authentication](sql-database-aad-authentication.md) brugere og logon.
* Du skal angive en bestemt database i stedet for indsættelse af standardværdier til *master* -databasen.
* Du kan ikke bruge sætningen Transact-SQL **Brug myDatabaseName;** på SQL-Database til at skifte til en anden database.
* Få mere at vide: [SQL-Database sikkerhed: administrere database access og login sikkerhed](sql-database-manage-logins.md)

## <a name="resiliency"></a>Fleksibilitet
Når der opstår en midlertidig fejl under oprettelse af forbindelse til SQL-Database, skal din kode gentagne opkaldet.  Vi anbefaler, retry-logik Brug backoff logik, så det ikke overvælde den SQL-Database med flere klienter prøver igen samtidigt.

* Kode eksempler: For kodeeksempler, der illustrerer retry-logik, se eksempler for sproget i dit valg på: [dataforbindelsesbiblioteker for SQL-Database og SQL Server](sql-database-libraries.md)
* Få mere at vide: [fejlmeddelelser for SQL-Database klientprogrammer](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Håndtering af forbindelser
* I din klient forbindelse logik skal tilsidesætte Standardtimeout for at være 30 sekunder.  Standard til 15 sekunder er for kort til forbindelser, der er afhænger af internettet.
* Hvis du bruger en [forbindelse puljen](http://msdn.microsoft.com/library/8xx3tyca.aspx), skal du sørge for at lukke forbindelsen chatsamtale dit program ikke aktivt bruger den, og ikke forbereder genbruge den.

## <a name="network-considerations"></a>Overvejelser i forbindelse med netværk
* På den computer, der er vært klientprogrammet, skal du sikre firewallen tillader udgående TCP-kommunikation på port 1433.  Få mere at vide: [konfigurere en firewall til Azure SQL-Database](sql-database-configure-firewall-settings.md)
* Hvis klientprogrammet opretter forbindelse til SQL-Database version 12, mens din klient kører på en Azure VM (virtual machine), skal du åbne bestemte portområder på VM. Få mere at vide: [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md)
* Klientforbindelser til Azure SQL-Database version 12 nogle gange tilsidesætte proxyen og interagere direkte med databasen. Porte end 1433 bliver vigtige. Få mere at vide: [porte ud over 1433 til ADO.NET 4.5 og version 12 SQL-Database](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Data Sharding med elastiske skala
Elastiske skala forenkler skalering ud (og i). 

* [Designs til flere lejer SaaS programmer med Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Afhængige distribution af data](sql-database-elastic-scale-data-dependent-routing.md)
* [Introduktion til Azure SQL Database elastiske skala Preview](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Næste trin

Udforsk alle [funktionerne i SQL-Database](https://azure.microsoft.com/services/sql-database/).
