<properties
   pageTitle="Operativsystem forespørgsel Store i Azure SQL-Database"
   description="Få mere at vide om at bruge den forespørgsel, der er gemt i Azure SQL-Database"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Operativsystem den forespørgsel, der er gemt i Azure SQL-Database 

Forespørgsel Store i Azure er en fuldt administreret database-funktion, der løbende indsamler og præsenterer detaljerede historiske oplysninger om alle forespørgsler. Du kan overveje forespørgsel Store som svarende til en fly flight dataregistrering, der betydeligt forenkler forespørgslens ydeevne, fejlfinding i forbindelse med begge dele til skyen og lokale kunder. Denne artikel forklares bestemte aspekter af operativsystem forespørgsel Store i Azure. Ved hjælp af denne allerede indsamlede forespørgselsdata, kan du hurtigt diagnosticere og løse problemer med ydeevnen og dermed bruge mere tid, der fokuserer på deres virksomhed. 

Forespørgsel Store har været [globalt tilgængelig](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) i Azure SQL-Database siden November 2015. Forespørgsel Store er grundlaget for analyse af ydeevnen og justering af funktioner som [SQL Database Advisor og ydeevne Dashboard](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). På tidspunktet for publicering af denne artikel, kører forespørgslen Store i mere end 200.000 brugerdatabaser i Azure, ved at indsamle oplysninger i forbindelse med forespørgsel for flere måneder, uden afbrydelser.

> [AZURE.IMPORTANT] Microsoft er ved at aktivere forespørgsel Store til alle Azure SQL-databaser (eksisterende og nye). 

## <a name="optimal-query-store-configuration"></a>Optimal forespørgsel Store konfiguration

I dette afsnit beskrives standardindstillinger for optimal konfigurationen, der er designet til at sikre pålidelig drift af forespørgsel Store og afhængige funktioner, såsom [SQL Database Advisor og ydeevne Dashboard](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Standardkonfiguration er udviklet til fortløbende dataindsamling, der er minimale tid i OFF/READ_ONLY tilstand.

| Konfiguration | Beskrivelse | Standard | Kommentar |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Angiver grænsen for det område af data, der forespørgsel Store kan udføre i z kundedatabase | 100 | Tvungen for nye databaser |
| INTERVAL_LENGTH_MINUTES | Definerer størrelsen af tidsramme, hvor indsamlede runtime statistik for forespørgselsplaner sammenlagt og bevaret. Alle aktive forespørgselsplan indeholder mindst en række til et stykke tid, der er defineret med denne konfiguration | 60   | Tvungen for nye databaser |
| STALE_QUERY_THRESHOLD_DAYS | Tidsbaserede oprydning politik, der bestemmer opbevaringsperiode af permanente runtime statistik og inaktiv forespørgsler | 30 | Tvungen for nye databaser og databaser med forrige standard (367) |
| SIZE_BASED_CLEANUP_MODE | Angiver, om automatisk Dataoprydning skal udføres, når forespørgslen Store datastørrelse nærmer sig grænsen | AUTOMATISK | Gennemtvinges for alle databaser |
| QUERY_CAPTURE_MODE | Angiver, om alle forespørgsler eller kun et undersæt af forespørgsler, registreres | AUTOMATISK | Gennemtvinges for alle databaser |
| FLUSH_INTERVAL_SECONDS | Angiver den maksimale periode, hvor hentes runtime statistik holdes i hukommelsen, før Træk til disk | 900 | Tvungen for nye databaser |
||||||

> [AZURE.IMPORTANT] Disse standardindstillinger for anvendes automatisk på den sidste trin i forespørgsel Store aktivering i alle Azure SQL-databaser (se før vigtig bemærkning). Efter denne lys op, Azure SQL-Database ikke ændre konfigurationsværdier, der er angivet af kunder, medmindre de have negativ indflydelse på primær arbejdsbelastningen eller pålidelige handlinger af forespørgsel Store.

Hvis du vil beholde dine brugerdefinerede indstillinger, skal du bruge [JUSTER DATABASE med forespørgsel Store indstillinger](https://msdn.microsoft.com/library/bb522682.aspx) til at gendanne konfiguration til den forrige tilstand. Se [Bedste fremgangsmåder til den Store-forespørgsel](https://msdn.microsoft.com/library/mt604821.aspx) for at få mere for at vide, hvordan toppen vælger optimal konfiguration af parametre.

## <a name="next-steps"></a>Næste trin

[SQL-Database ydeevne indsigt](sql-database-performance.md)

## <a name="additional-resources"></a>Yderligere ressourcer

For flere oplysninger om Tjek ud i følgende artikler:

- [En flight dataregistrering for databasen](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Overvåge ydeevne ved hjælp af forespørgsel Store](https://msdn.microsoft.com/library/dn817826.aspx)

- [Forespørgsel Store brugsscenarier](https://msdn.microsoft.com/library/mt614796.aspx)

- [Overvåge ydeevne ved hjælp af forespørgsel Store](https://msdn.microsoft.com/library/dn817826.aspx) 
