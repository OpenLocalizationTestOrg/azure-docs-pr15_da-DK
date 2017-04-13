<properties
    pageTitle="Overvåge XTP hukommelseslager | Microsoft Azure"
    description="Vurdering og overvåge XTP hukommelseslager bruger, kapacitet. løse kapacitet fejl 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Skærm i hukommelsen OLTP lagerplads

Når du bruger [i hukommelsen OLTP](sql-database-in-memory.md), findes dataene i hukommelse optimeret tabeller og tabelvariabler i i hukommelsen OLTP lagerplads. Hver Premium webtjenesten har en maksimale i hukommelsen OLTP lagerplads størrelse, der er beskrevet i [SQL Database Service niveauer artikel](sql-database-service-tiers.md#service-tiers-for-single-databases). Når denne grænse overskrides, indsætte og opdatere handlinger kan starte ned (med fejl 41823). På dette tidspunkt vil for enten slette data for at frigøre hukommelse, eller du opgradere det ydeevne niveau i din database.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Finde ud af, om data kan være i hukommelseslager knop

Bestemme kappen lagerplads: se [SQL Database Service niveauer artikel](sql-database-service-tiers.md#service-tiers-for-single-databases) til lagerplads caps af de forskellige Premium service lag.

Estimering af hukommelseskrav til en hukommelse optimeret tabel fungerer på samme måde for SQL Server som den ser i Azure SQL-Database. Tage et par minutter at gennemgå dette emne på [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Bemærk, at den tabel, og variable tabelrækker, samt indeks, tælle mod datastørrelse Maks bruger. Desuden skal ALTER TABLE plads til at oprette en ny version af hele tabellen og de tilhørende indeks.

## <a name="monitoring-and-alerting"></a>Overvågning og advarer

Du kan overvåge hukommelseslager brug som en procentdel af den [lagerplads afslutter for din ydeevne niveau](sql-database-service-tiers.md#service-tiers-for-single-databases) i Azure- [portalen](https://portal.azure.com/): 

- Find feltet ressource anvendelsen på bladet Database, og klik på Rediger.
- Vælg derefter metrikværdien `In-Memory OLTP Storage percentage`.
- Klik på ressourceudnyttelse for at åbne bladet metriske for at tilføje en påmindelse, og derefter klikke på Tilføj påmindelse.

Eller brug følgende forespørgsel til at vise hukommelseslager anvendelsen:

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Rette med manglende hukommelse situationer - fejl 41823

Kører med manglende hukommelse resultater i Indsæt, opdatere og oprette handlinger ned med fejlmeddelelse 41823.

Fejlmeddelelse 41823 angiver, at de hukommelse optimeret tabeller og tabelvariabler har overskredet den maksimale størrelse.

For at løse fejlen, enten:


- Slette data fra tabellerne hukommelse optimeret, da potentielt overføre data til traditionelle, diskbaserede tabeller. eller,
- Opgradere webtjenesten til én med tilstrækkelig hukommelseslager for de data, du vil bevare i hukommelse optimeret tabeller.

## <a name="next-steps"></a>Næste trin
Yderligere ressourcer om om [overvågning Azure SQL-Database ved hjælp af dynamiske management visninger](sql-database-monitoring-with-dmvs.md)
