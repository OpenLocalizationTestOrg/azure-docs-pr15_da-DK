<properties
    pageTitle="Fejlfinding i forbindelse med sikkerhedskopiering og gendannelse med Azure SQL-Database"
    description="Lær, hvordan du gendanner en database i skyen fra fejl og afbrydelser ved hjælp af sikkerhedskopier og replikaer i Azure SQL-Database."
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

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Gendanne en database til et tidligere tidspunkt, gendanne en slettet database eller gendanne fra en data center afbrydelse

SQL-Database bevarer kopier af databasen, så du kan gendanne fra afbrydelser og brugerfejl. Tilgængelige indstillinger afhænger af den database webtjenesten og indstillinger, du vælger. Se [Oversigt over Business løbende](sql-database-business-continuity.md) til detaljer og Designovervejelser.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Til at gendanne en database til et tidligere tidspunkt
1.  [Azure-portalen](https://azure.microsoft.com/), klik på **SQL-databaser**.
2.  Vælg databasen på listen, og klik derefter på **Gendan**.
3.  Skriv et nyt navn til databasen, vælge datoen og tid til at gendanne fra, og klik derefter på **Opret.**
4.  Foretage justeringer af app som det er nødvendigt at referere til den nye database. Se [gendanne en database til et tidspunkt](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Gendanne en slettet ved et uheld database
1.  [Azure-portalen](https://azure.microsoft.com/), klik på **SQL-servere**.
2.  Vælg den server, hostet databasen på listen.
3.  Rul ned, og klik på **slettede databaser**blade Server.
4.  Vælg databasen, du kan gendanne, og klik derefter på **Opret**.
5.  Foretage justeringer af app som det er nødvendigt at referere til den nye database. Se [gendanne en slettet database](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Til at gendanne fra en regionale datacenter afbrydelse
Med Standard- og Premium databaser, hvis du har konfigureret geografisk replikerede secondaries, kan du gendanne ved hjælp af disse secondaries. Det giver dig mulighed for at gendanne en database med en mindre potentiel for tab af data. Se [gendanne Azure SQL-database ved hjælp af automatiseret databasesikkerhedskopier](sql-database-disaster-recovery.md) få mere at vide.

Azure leverer også sikkerhedskopier af hver database i et andet område (en geografisk overflødige sikkerhedskopi). Du kan oprette en ny database fra disse sikkerhedskopier, som kaldes geografisk-Gendan, men det er sandsynligt, vil du opleve tab af data, hvis du skal have denne metode alene.

**Gendanne en database ved hjælp af geografisk-Gendan:**

- [Azure-portalen](https://azure.microsoft.com/), klik på **Ny**, skal du klikke på **Data og lagerplads**, skal du klikke på **SQL-Database**og derefter vælge **sikkerhedskopi** som databasekilden. Se [gendanne en Azure SQL-database fra en afbrydelse](sql-database-disaster-recovery.md) få mere at vide.