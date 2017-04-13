<properties
    pageTitle="SQL-Database: Hvad er en DTU? | Microsoft Azure"
    description="Forstå, hvad en Azure SQL-Database er transaktion opgaver."
    keywords="databaseindstillinger, databasens ydeevne"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor="CarlRabeler"/>

<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="NA"
    ms.date="09/06/2016"
    ms.author="carlrab"/>

# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Forklarer Database transaktion enheder (DTUs) og elastiske Database transaktion enheder (eDTUs)

Denne artikel forklares Database transaktion enheder (DTUs) og elastiske Database transaktion enheder (eDTUs) samt hvad sker der, når du klikker på den maksimale DTUs eller eDTUs.  

## <a name="what-are-database-transaction-units-dtus"></a>Hvad er Database transaktion enheder (DTUs)

En DTU er en måleenhed for de ressourcer, der skal være tilgængelig for en enkeltstående Azure SQL-database på en særlig ydeevneniveau i en [enkeltstående database webtjenesten](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels). En DTU er et blandet mål for CPU, hukommelse og data i/o- og transaktion log/o-i et forhold, bestemmes af en OLTP benchmark arbejdsbyrde, der er udviklet til at være typisk af reale OLTP arbejdsmængder. Fordoble DTUs ved at øge ydeevneniveau i en database passer til fordoble sæt af ressource, der er tilgængelige til den pågældende database. For eksempel indeholder en Premium P11 database med 1750 DTUs 350 x flere DTU beregne power end en grundlæggende database med 5 DTUs. For at forstå metode bag OLTP benchmark arbejdsbelastningen bruges til at bestemme DTU Bland, skal du se [Oversigt over SQL-Database benchmark](sql-database-benchmark-overview.md).

![Introduktion til SQL-Database: enkelt database DTUs ved niveau og niveau](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

Du kan [ændre tjenesten niveauer](sql-database-scale-up.md) når som helst med minimale nedetid i dit program (normalt gennemsnittet under fire sekunder). For mange virksomheder og apps er at kunne oprette databaser og Ring op enkelt databasens ydeevne op eller ned efter behov nok, især hvis brugsmønstre er relativt forudsigelige. Men hvis du har uventede brugsmønstre, det kan gøre det svært at administrere omkostninger og business modellen. I dette scenarie skal bruge du en elastiske puljen med et bestemt antal eDTUs.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Hvad er elastiske Database transaktion enheder (eDTUs)

En eDTU er en måleenhed over sættet af ressourcer (DTUs), der kan deles mellem et sæt af databaser på en Azure SQL server - kaldet en [elastiske puljen](sql-database-elastic-pool.png). Elastiske grupper indeholder en enkel rentable løsning for at administrere ydeevne mål for flere databaser, der har bredt varierende og uventede brugsmønstre. Se [elastiske grupper og service niveauer](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) kan finde flere oplysninger.

![Introduktion til SQL-Database: eDTUs ved niveau og niveau](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

En samling gives et fastsat antal eDTUs til en fast pris. I gruppen får individuelle databaser fleksibilitet til automatisk skalering inden for angivne parametre. En database kan meget belastet forbruge flere eDTUs for at imødekomme behov. Databaser under light indlæse forbruge mindre, og databaser uden belastning forbruge ingen eDTUs. Klargøring af ressourcer til hele puljen i stedet for enkelt databaser forenkler din administrationsopgaver. Plus du har et mere forudsigelige budget for puljen.

Yderligere eDTUs kan tilføjes til en eksisterende gruppe med ingen database nedetid eller uden at påvirke databaserne i elastiske puljen. På samme måde, hvis ekstra eDTUs ikke længere skal bruges de kan fjernes fra en eksisterende gruppe når som helst tidspunkt. Du kan tilføje eller subtrahere databaser til puljen. Hvis en database forudsigeligt under-ved hjælp af ressourcer, flytte den.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hvordan kan jeg se antallet af DTUs, der bruges til min arbejdsbelastningen?

Hvis du er interesseret i at overføre et eksisterende lokalt eller SQL Server virtuelt arbejdsbelastningen til Azure SQL-Database, du kan bruge [DTU Lommeregner](http://dtucalculator.azurewebsites.net/) til omtrentlig antallet af DTUs det er nødvendigt. Du kan bruge [SQL Database forespørgsel ydeevne indsigt](sql-database-query-performance.md) for at forstå din database ressourceforbrug (DTUs) for at få bedre indsigt i Sådan optimeres arbejdsbelastningen for en eksisterende arbejdsbyrde i Azure SQL-Database. Du kan også bruge [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV for at få forbrug ressourceoplysninger for den sidste en time. Du kan også kan katalog visning [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) også forespørges på for at få de samme data i de sidste 14 dage, selvom på en lavere pålidelighed af fem minutters gennemsnit.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Hvordan ved jeg, hvis jeg kan få glæde af en elastiske samling af ressourcer?

Grupper er egnet til et stort antal databaser med bestemte anvendelsen mønstre. For en given database er dette mønster er betegnet ved lav gennemsnitlige udnyttelse med relativt sjælden anvendelsen spidser. SQL-Database automatisk evaluerer den historiske Ressourceforbrug over databaser i en eksisterende SQL-databaseserver og anbefaler den relevante gruppe konfiguration på portalen Azure. Du kan finde flere oplysninger, se [Hvornår skal en elastiske database puljen bruges?](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Hvad sker der, når jeg rammer min maksimale DTUs

Ydeevne er kalibreret og omfattet for at angive de nødvendige ressourcer for at køre databasens arbejdsbelastning op til maks begrænsningerne tilladt for dine valgte tjeneste niveau og ydeevne niveau. Hvis din arbejdsbyrde er nå begrænsningerne på en af CPU/Data EY/Log EY begrænsninger, du fortsat modtage ressourcerne på den maksimalt tilladte niveau, men du sandsynligvis at se øget latenstider for dine forespørgsler. Disse begrænsninger medfører ikke fejl, men hellere reduceret arbejdsbelastningen, medmindre bliver langsommere bliver så alvorlige, starte forespørgsler tidsindstilling. Hvis du nå begrænsninger af maksimalt tilladte samtidige bruger sessioner/anmodninger (arbejdstråde), kan du se eksplicitte fejl. Se [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md) for oplysninger om grænsen for ressourcer end CPU, hukommelse, data I/O og transaktion log i/o.

## <a name="next-steps"></a>Næste trin

- Du kan finde oplysninger om DTUs og eDTUs tilgængelig til enkeltstående databaser og elastiske grupper i [webtjenesten](sql-database-service-tiers.md) .
- Se [Azure SQL-Database ressource begrænsninger](sql-database-resource-limits.md) for oplysninger om grænsen for ressourcer end CPU, hukommelse, data I/O og transaktion log i/o.
- Se [SQL Database forespørgsel ydeevne indsigt](sql-database-query-performance.md) at forstå din forbrug (DTUs).
- Se [Oversigt over SQL-Database benchmark](sql-database-benchmark-overview.md) at forstå metode bag OLTP benchmark arbejdsbelastningen bruges til at bestemme DTU blanding.