<properties
    pageTitle="SQL-Database ydeevne og indstillinger: Service niveauer | Microsoft Azure"
    description="Sammenlign funktioner af SQL-Database ydeevne og business løbende service trin over for saldo omkostninger og kapacitet, som du skalere."
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
    ms.workload="data-management"
    ms.date="08/10/2016"
    ms.author="carlrab"/>

# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>Indstillinger for SQL-Database og ydeevne: forstå, hvad der er tilgængeligt i hver tjeneste lag

[Azure SQL-Database](sql-database-technical-overview.md) indeholder tre service niveauer med flere ydeevneniveauer til at håndtere forskellige arbejdsbelastninger. Hvert ydeevneniveau indeholder en stigende række ressourcer, der er udviklet til at levere stadig højere overførselshastighed. Du kan administrere hver database i sin egen [webtjenesten](sql-database-service-tiers.md#standalone-database-service-tiers-and-performance-levels) med sin egen ydeevneniveau. Du kan også administrere flere databaser i en [elastiske puljen](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus) med et delt sæt ressourcer. De ressourcer, der er tilgængelige for enkeltstående databaser udtrykkes med hensyn til databasen transaktion enheder (DTUs) og elastiske grupper med hensyn til elastiske DTUs eller eDTUs. Få mere at vide om DTUs og eDTUs, kan du se [Hvad er en DTU](sql-database-what-is-a-dtu.md). 

I begge tilfælde omfatter service niveauer **grundlæggende**, **Standard**og **Premium**. Databaseindstillinger i disse lag er de samme for enkeltstående databaser og elastiske grupper, men der er flere overvejelser i forbindelse med elastiske grupper. Denne artikel indeholder oplysninger om tjenesten niveauer for enkeltstående databaser og elastiske grupper.

## <a name="service-tiers-and-database-options"></a>Tjenesten niveauer og databaseindstillinger
Grundlæggende, Standard, og Premium service niveauer alle skal have en oppetid SLA 99,99% og tilbyder mere forudsigelige ydeevne, fleksibel løbende indstillinger, funktioner for sikkerhed og hver time fakturering. Den følgende tabel indeholder eksempler på de lag, der er bedst egnet til forskellige arbejdsbelastninger.

| Webtjenesten | Target arbejdsmængder |
|---|---|
| **Grundlæggende** | Bedst egnet til en lille database understøttende typisk én enkelt aktiv handling på et bestemt tidspunkt. Som eksempler kan nævnes databaser, der bruges til udvikling eller test, eller mindre anvendte sjældent programmer. |
| **Standard** | Indstillingen Gå til for de fleste programmer i skyen, understøtte flere samtidige forespørgsler. Som eksempler kan nævnes arbejdsgruppe eller web-programmer. |
| **Premium** | Udviklet til høj transaktions beholdning, understøttende mange samtidige brugere og kræve, at det højeste niveau af business løbende funktioner. Eksempler er databaser understøttende vigtigste programmer. |

>[AZURE.NOTE] Web og Business udgaver fjernes. Læs [Ofte stillede spørgsmål om Sunset](https://azure.microsoft.com/pricing/details/sql-database/web-business/) , hvis du vil fortsætte med at bruge internettet og Business udgaver.

## <a name="standalone-database-service-tiers-and-performance-levels"></a>Enkeltstående database service niveauer og ydeevne
Enkeltstående databaser er der flere ydeevneniveauer i hver webtjenesten. Har du mulighed for at vælge det niveau, der bedst opfylder dine arbejdsbelastningen. Hvis du vil skalere op eller ned, kan du nemt ændre niveauer af databasen. Se [ændre Database Service niveauer og ydeevne](sql-database-scale-up.md) til detaljer.

Ydeevnen egenskaber, der er angivet her gælder for databaser, der er oprettet ved hjælp af [Version 12 SQL-Database](sql-database-v12-whats-new.md). Databasen får et garanterede sæt ressourcer uanset antallet af databaser, der er hostet, og de forventede ydeevne egenskaber for din database påvirkes ikke.

[AZURE.INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

>[AZURE.NOTE] I [tjenesten niveau funktioner og begrænsninger for](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)en detaljeret forklaring af alle andre rækker i denne tjeneste niveauer tabel.

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>Elastiske puljen service niveauer og ydeevne i eDTUs
Ud over at oprette og skalering en enkeltstående database, kan du også har mulighed for at administrere flere databaser i en [elastiske puljen](sql-database-elastic-pool.md). Alle databaser i en elastiske puljen deler en fælles gruppe af ressourcer. Ydeevnen egenskaber måles ved *Elastiske Database transaktion enheder* (eDTUs). Når med enkeltstående databaser, kommer grupper i tre service niveauer: **grundlæggende**, **Standard**og **Premium**. For grupper definere disse tre service lag stadig begrænsningerne overordnede ydeevne og flere funktioner.

Grupper Tillad databaser til at dele og forbruge DTU ressourcer uden at skulle tildele en særlig ydeevne for hver database i puljen. For eksempel kan en enkeltstående database i en Standard pulje gå fra ved hjælp af 0 eDTUs til den maksimale database eDTU du konfigurere, når du konfigurerer puljen. Grupper Tillad flere databaser med forskellige arbejdsbelastninger effektivt bruge eDTU ressourcer tilgængelige til hele puljen. Du kan finde oplysninger i [pris og ydeevne overvejelser i forbindelse med en elastiske puljen](sql-database-elastic-pool-guidance.md) .

Den følgende tabel beskriver egenskaber for puljen service niveauer.

[AZURE.INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Hver database i en gruppe også overholder de enkeltstående database karakteristika for det pågældende niveau. For eksempel grundlæggende puljen har en grænse for det maksimale antal sessioner i puljen af 4800-28800, men en enkelt database i en grundlæggende pulje har en database begrænsning på 300 sessioner.

## <a name="choosing-a-service-tier"></a>Vælge et webtjenesten

For at afgøre, på et webtjenesten, du starte med at afgøre, om databasen skal være en enkeltstående database eller være en del af en elastiske samling. 

### <a name="choosing-a-service-tier-for-a-standalone-database"></a>Vælge et webtjenesten til en enkeltstående database

For at afgøre, på et webtjenesten til en enkeltstående database, du start ved at bestemme de Databasefunktioner, skal du vælge din SQL-Database udgave:

- Databasestørrelse (2 GB maksimale til grundlæggende, 250 GB maksimum for Standard og 500 GB til 1 TB maksimale til Premium - afhængigt af niveauet for ydeevne)
- Database sikkerhedskopiering opbevaringsperiode (7 dage for Basic 35 dage til Standard, og 35 dage for Premium)

Når du har valgt SQL-Database udgaven, er du klar til at bestemme ydeevnen for databasen (antallet af DTUs). Du kan gætte og derefter [skalere op eller ned dynamisk](sql-database-scale-up.md) baseret på faktisk oplevelse. Du kan også bruge [DTU Lommeregner](http://dtucalculator.azurewebsites.net/) til omtrentlig antallet af DTUs det er nødvendigt. 

### <a name="choosing-a-service-tier-for-an-elastic-database-pool"></a>Hvis du vælger et webtjenesten for en elastiske database puljen.

Start for at fastlægge webtjenesten for en elastiske database puljen, ved at bestemme de Databasefunktioner, skal du vælge webtjenesten for din gruppe.

- Databasestørrelse (2 GB for Basic, 250 GB til Standard og 500 GB for Premium)
- Database sikkerhedskopiering opbevaringsperiode (7 dage for Basic 35 dage til Standard, og 35 dage for Premium)
- Antallet af databaser i puljen (400 til Basic, 400 til Standard og 50 for Premium)
- Maksimal storage per puljen (117 GB for Basic 1200 til Standard, og 750 for Premium)

Når du har valgt webtjenesten for din gruppe, er du klar til at bestemme ydeevnen for puljen (eDTUs). Du kan gætte og derefter [skalere eller indskrænke dynamisk](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) baseret på faktisk oplevelse. Du kan bruge [DTU Lommeregner](http://dtucalculator.azurewebsites.net/) til omtrentlig antallet af DTUs, der er behov for hver database i en gruppe til at bestemme den øvre grænse for puljen.

## <a name="next-steps"></a>Næste trin
- Få mere at vide om priser for disse lag på [SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/).
- Se oplysninger om [elastiske grupper](sql-database-elastic-pool-guidance.md) og [pris og ydeevne overvejelser i forbindelse med elastiske grupper](sql-database-elastic-pool-guidance.md).
- Lær, hvordan du [skærm, Administrer, og tilpasse størrelsen på elastiske grupper](sql-database-elastic-pool-manage-portal.md) og [overvåge ydeevnen for enkeltstående databaser](sql-database-single-database-monitor.md).
- Nu hvor du ved, om SQL-Database lag, afprøve dem med en [gratis konto](https://azure.microsoft.com/pricing/free-trial/) , og lær [at oprette din første SQL-database](sql-database-get-started.md).

## <a name="additional-resources"></a>Yderligere ressourcer

- [Designs til flere lejer SaaS programmer, der bruger Azure SQL-Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
- [Microsoft Virtual Academy kurset på elastiske Databasefunktioner i Azure SQL-Database](https://mva.microsoft.com/en-US/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
