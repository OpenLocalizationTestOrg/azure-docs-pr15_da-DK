<properties
    pageTitle="Azure SQL Database ressource begrænsninger"
    description="Denne side beskrives nogle almindelige ressource grænser for Azure SQL-Database."
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
    ms.date="10/13/2016"
    ms.author="carlrab" />


# <a name="azure-sql-database-resource-limits"></a>Azure SQL-Database ressource begrænsninger

## <a name="overview"></a>Oversigt

Azure SQL-Database, administrerer de ressourcer, der er tilgængelige for en database ved hjælp af to forskellige mekanismer: **Styring af ressourcer** og **Aktivering af begrænsninger**. Dette emne forklares disse to hovedområder af Ressourcestyring.

## <a name="resource-governance"></a>Ressource styring
Et mål for design af Basic, som Standard og Premium service niveauer er til Azure SQL-Database til at fungere som om databasen, der kører på sin egen computer, helt isolerede fra andre databaser. Ressource styring emulerer dette problem. Hvis aggregeret ressource anvendelsen når den maksimale tilgængelige CPU, hukommelse, Log i/o- og Data i/o-ressourcer, der er tildelt til databasen, vil ressource styring kø forespørgsler i udførelse af og tildele ressourcer til forespørgslerne i kø, som de frigøre.

Som på en dedikeret maskine medfører ved hjælp af alle tilgængelige ressourcer en længere udførelse af forespørgsler, hvilket kan medføre kommandoen timeout på klienten udføres i øjeblikket. Programmer med tilstanden forsøg og programmer, udfører forespørgsler i databasen med en høj frekvens kan opstå fejlmeddelelser, når du forsøger at udføre nye forespørgsler, når tilladte samtidige anmodninger er nået.

### <a name="recommendations"></a>Anbefalinger:
Overvåge ressource anvendelsen samt gennemsnitlige svar tidspunkter for forespørgsler, når nærmer sig maksimale anvendelsen af en database. Når du støder på højere forespørgsel latenstider normalt har du tre muligheder:

1.  Reducere mængden af indgående anmodninger til databasen for at forhindre timeout og bunke op af anmodninger.

2.  Tildele et højere ydeevneniveau til databasen.

3.  Optimere forespørgsler for at reducere ressource anvendelsen af hver forespørgsel. Du kan finde flere oplysninger i afsnittet forespørgsel justerings/Hinting i artiklen Azure SQL Database ydeevne vejledning.

## <a name="enforcement-of-limits"></a>Aktivering af grænser
Ressourcer end CPU, hukommelse, Log i/o- og Data i/o-håndhæves ved at nægte nye anmodninger, når grænsen nås. Klienter, modtager en [fejlmeddelelse om](sql-database-develop-error-messages.md) afhængigt af den grænse, som er nået.

For eksempel er antallet af forbindelser til en SQL-database og antallet af samtidige anmodninger, der kan behandles begrænset. SQL-Database kan antallet af forbindelser til databasen, der skal være større end antallet af samtidige anmodninger til at understøtte gruppering af forbindelse. Mens du kan nemt styre mængden af forbindelser, der er tilgængelige ved programmet, er mængden parallelle anmodninger ofte gange sværere at anslå og få kontrol. Især under spidsbelastning indlæse når programmet enten sender for mange anmodninger eller databasen når dens ressource begrænser og starter en lang række arbejdstråde på grund af længere tid, der kører forespørgsler, kan fejl opstå.

## <a name="service-tiers-and-performance-levels"></a>Tjenesten niveauer og ydeevne

Der findes service niveauer og ydeevneniveauer for begge enkeltstående database og elastiske grupper.

### <a name="standalone-databases"></a>Enkeltstående databaser

Begrænsninger af en database er defineret af database service niveau og ydeevne niveau til en enkeltstående database. Den følgende tabel beskriver egenskaber for grundlæggende, Standard og Premium databaser på forskellige ydeevneniveauer.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>Elastiske grupper

[Elastiske grupper](sql-database-elastic-pool.md) del ressourcer på tværs af databaser i puljen. Den følgende tabel beskriver egenskaber for Basic, som Standard og Premium elastiske database grupper.

[AZURE.INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Se beskrivelserne i [tjenesten niveau funktioner og begrænsninger for](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)en udvidet definition af hver ressource, der er angivet i de tidligere tabeller. Se en oversigt over service niveauer, [Azure SQL Database Service niveauer og ydeevne](sql-database-service-tiers.md).

## <a name="other-sql-database-limits"></a>Andre begrænsninger for SQL-Database

| Område | Grænse | Beskrivelse |
|---|---|---|
| Databaser ved hjælp af automatiseret eksportere per abonnement | 10 | Automatiseret Eksportér giver dig mulighed at oprette en brugerdefineret tidsplan til at sikkerhedskopiere dine SQL-databaser. Du kan finde flere oplysninger, se [SQL-databaser: understøttelse af automatiserede SQL Database Eksporter](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|
| Database per server | Op til 5000 | Op til 5000 databaser er tilladt per server på version 12-servere. |  
| DTUs per server | 45000 | 45000 DTUs er tilgængelige per server på version 12-servere til klargøring databaser, elastiske grupper og lagre data. |



## <a name="resources"></a>Ressourcer

[Azure-abonnement og begrænsninger for tjenesten, kvoter og begrænsninger](../azure-subscription-service-limits.md)

[Azure SQL Database-tjenesten niveauer og ydeevne](sql-database-service-tiers.md)

[Fejlmeddelelser for SQL-Database klientprogrammer](sql-database-develop-error-messages.md)
