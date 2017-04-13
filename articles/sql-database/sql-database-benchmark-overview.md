<properties
    pageTitle="Azure SQL-Database benchmark oversigt"
    description="Dette emne beskrives Azure SQL Database Benchmark bruges i måling af ydeevnen for Azure SQL-Database."
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
    ms.date="06/21/2016"
    ms.author="carlrab" />

# <a name="azure-sql-database-benchmark-overview"></a>Azure SQL-Database benchmark oversigt

## <a name="overview"></a>Oversigt
Microsoft Azure SQL Database indeholder tre [service niveauer](sql-database-service-tiers.md) med flere ydeevneniveauer. Hvert ydeevneniveau indeholder en stigende række ressourcer eller 'potens', der er udviklet til at levere stadig højere overførselshastighed.

Det er vigtigt at kunne sæt tal på hvordan stigende styrken i hvert ydeevneniveau oversætter til øget databasens ydeevne. Hvis du vil gøre denne Microsoft har udviklet Azure SQL Database Benchmark (ASDB). Benchmark øvelser en blanding af grundlæggende handlinger, der findes i alle arbejdsbelastninger, som OLTP. Vi mål overførselshastigheden opnået for databaser, der kører for hvert ydeevneniveau.

Ressourcer og styrken i hver tjeneste niveau og ydeevne niveau udtrykkes med hensyn til [Databasen transaktion enheder (DTUs)](sql-database-technical-overview.md#understand-dtus). DTUs ikke en metode til at beskrive relative kapaciteten af et ydeevneniveau, der er baseret på et blandede mål for CPU, hukommelse, og læse og skrive satser, som hvert ydeevneniveau. Fordoble DTU bedømmelse af en database passer til fordoble database power. Benchmark giver os mulighed at vurdere virkningen af databaseydeevnen af stigende power, som hvert ydeevneniveau ved at benytte faktisk databasehandlinger, mens du skalering databasestørrelse, antallet af brugere og transaktion satser i forhold til de ressourcer, der er angivet til databasen.

Brug af transaktioner i timen, den Standard webtjenesten ved hjælp af transaktioner i minuttet og webtjenesten Premium, ved hjælp af transaktioner per sekunders, det gør det nemmere at hurtigt relatere ydeevnen potentielle af hver tjeneste niveauet kravene i et program ved at udtrykke overførselshastighed af grundlæggende serviceniveauet.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Korrelerende benchmarkresultater til virkelige databasens ydeevne
Det er vigtigt at forstå, ASDB, som alle benchmarks er repræsentant og vejledende kun. De transaktion satser opnået med programmet benchmark vil ikke være den samme som dem, der kan opnås med andre programmer. Benchmark består af en samling af forskellige transaktion typer kører i forhold til et skema, der indeholder en række forskellige tabeller og datatyper. Benchmark gør brug af de samme grundlæggende handlinger, der er fælles for alle arbejdsbelastninger, som OLTP, men det repræsenterer ikke nogen bestemt klasse database eller program. Formålet med benchmark er at tilvejebringe et begrundet vejledning til relative ydeevnen for en database, der kan være forventet, når skalering op eller ned mellem ydeevne. I virkeligheden, databaser er af forskellige størrelser og kompleksitet, støder på forskellige kombinationer af arbejdsmængder og reagerer på forskellige måder. For eksempel et EY-intensivt program kan ramme EY tærskler hurtigere, eller et CPU-intensivt program kan ramme CPU-begrænsninger hurtigere. Der er ingen garanti, en bestemt database skaleres på samme måde som benchmark under øget Indlæs.

Benchmark og dens metoder er beskrevet mere detaljeret nedenfor.

## <a name="benchmark-summary"></a>Oversigt over benchmark
ASDB måler ydeevnen for en blanding af grundlæggende Databasefunktioner som forekommer hyppigst i online transaction processing (OLTP) arbejdsmængder. Selvom benchmark er udviklet med cloud computing i huske, databaseskema, data populationen, og transaktioner er udviklet til at være alment repræsentant af de grundlæggende elementer, der er mest almindeligt brugte i OLTP arbejdsmængder.

## <a name="schema"></a>Skema
Skemaet er designet til at have nok række og -kompleksitet til at understøtte en lang række handlinger. Benchmark kører mod en database, der består af seks tabeller. Tabellerne, der findes tre kategorier: fast størrelse, skalering og voksende. Der er to fast størrelse tabeller. tre skalering tabeller. og én voksende tabel. Fast størrelse tabeller har en konstant antal rækker. Skalering tabeller har en kardinalitet, der er proportional databasens ydeevne, men ikke ændre under benchmark. Tabellen voksende er tilpasset som en skalering tabel på indledende indlæsning, men kardinalitet ændringer i løbet af kører benchmark, som er indsat og slettet rækker.

Skemaet indeholder en blanding af datatyper, herunder heltal, numerisk tegn og dato/klokkeslæt. Skemaet indeholder primære og sekundære nøgler, men ikke de fremmede nøgler – det vil sige, der er ingen begrænsninger referentiel integritet mellem tabeller.

Et data generering af program genererer dataene til den første database. Heltal og numeriske data, der genereres med forskellige strategier. I nogle tilfælde distribueret værdier tilfældigt via et område. I andre tilfælde et sæt af værdier er vilkårligt den ombyttede for at sikre, at en bestemt fordeling vedligeholdes. Tekstfelter er dannet ud fra en vægtet liste over ord, hvilket giver realistisk udseende data.

Databasen er tilpasset baseret på en "skala faktor." Den farveskala faktor (forkortelse som SF) bestemmer kardinalitet af skaleringen og voksende tabeller. Som beskrevet nedenfor i sektionen brugere og Pacing, databasestørrelse, antallet af brugere og optimal ydeevne alle skalere i forhold til hinanden.

## <a name="transactions"></a>Transaktioner
Arbejdsbelastningen består af ni posttyper, som vist i nedenstående tabel. Hver transaktion er udviklet til at fremhæve et bestemt sæt af egenskaber for system i database engine og system hardwaren med høj kontrast fra de andre transaktioner. Denne fremgangsmåde gør det nemmere at vurdere virkningen af forskellige komponenter til overordnede ydeevne. For eksempel giver posteringen "Læst tunge" et stort antal Læs handlinger fra disk.

| Transaktionstype | Beskrivelse |
|---|---|
| Læs Lite | VÆLG; i hukommelsen. skrivebeskyttet |
| Læs mediet | VÆLG; hovedsageligt i hukommelsen; skrivebeskyttet |
| Læs tunge | VÆLG; hovedsageligt ikke i hukommelsen; skrivebeskyttet |
| Opdater Lite | OPDATERE, F.EKS. i hukommelsen. skrivebeskyttet |
| Opdatere tunge | OPDATERE, F.EKS. hovedsageligt ikke i hukommelsen; skrivebeskyttet |
| Indsæt Lite | INDSÆT; i hukommelsen. skrivebeskyttet |
| Indsætte tunge | INDSÆT; hovedsageligt ikke i hukommelsen; skrivebeskyttet |
| Slet | SLET; blanding af i hukommelsen og ikke i hukommelsen; skrivebeskyttet |
| CPU tunge | VÆLG; i hukommelsen. relativt CPU belastet; skrivebeskyttet |

## <a name="workload-mix"></a>Arbejdsbelastningen mix
Transaktioner vælges tilfældigt fra en vægtet fordeling med den følgende overordnede blanding. Den overordnede blanding har en læse-og skriveadgang forholdet mellem cirka 2:1.

| Transaktionstype | % af Mix |
|---|---|
| Læs Lite | 35 |
| Læs mediet | 20 |
| Læs tunge | 5 |
| Opdater Lite | 20 |
| Opdatere tunge | 3 |
| Indsæt Lite | 3 |
| Indsætte tunge | 2 |
| Slet | 2 |
| CPU tunge | 10 |

## <a name="users-and-pacing"></a>Brugere og pacing
Benchmark arbejdsbelastningen er baseret på et værktøj, som sender transaktioner på tværs af et sæt af forbindelser for at simulere funktionaliteten af et antal samtidige brugere. Selvom alle forbindelser og transaktioner maskine, der er oprettet, til enkel refererer vi til disse forbindelser som "brugere". Selvom hver enkelt bruger fungerer uafhængigt af alle andre brugere, udføre alle brugere på samme cyklus trin, der er vist nedenfor:

1. Oprette en databaseforbindelse.
2. Gentag, indtil en angivelse af, at afslutte:
    - Vælg en transaktion tilfældigt (fra en vægtet fordeling).
    - Udføre den valgte transaktion og måle svartid.
    - Vent på en pacing forsinkelse.
3. Luk forbindelsen til databasen.
4. Afslut.

Pacing forsinkelsen (i trin 2c) er markeret tilfældigt, men med en fordeling, der har et gennemsnit af 1,0 sekund. Og som derfor kan hver bruger i gennemsnit generere mere end én transaktion sekundet.

## <a name="scaling-rules"></a>Skalering regler
Antallet af brugere, bestemmes af databasestørrelse (i skala-faktor enheder). Der er en bruger på hver fem skala-faktor-enheder. På grund af pacing forsinkelse, skal kan en bruger oprette mere end én transaktion sekundet, i gennemsnit.

For eksempel en skala-faktor på 500 (SF = 500) database har 100 brugere og kan opnå en maksimal hastighed på 100 Kreative. For at drive en højere Kreative kræver rente flere brugere og en større database.

Tabellen nedenfor viser antallet af brugere, der faktisk sustained for hver tjeneste niveau og ydeevne niveau.

| Webtjenesten (ydeevneniveau) | Brugere | Databasestørrelse |
|---|---|---|
| Grundlæggende | 5 | 720 MB |
| Standard (S0) | 10 | 1 GB |
| Standard (S1) | 20 | 2.1 GB |
| Standard (S2) | 50 | 7.1 GB |
| Premium (P1) | 100 | 14 GB |
| Premium (P2) | 200 | 28 GB |
| Premium (P6/P3) | 800 | 114 GB |

## <a name="measurement-duration"></a>Mål varighed
En gyldig benchmark-kørsel kræver en stationær tilstand mål varighed på mindst en time.

## <a name="metrics"></a>Målepunkter
De vigtigste målepunkter i benchmark er overførselshastighed og svartid.

- Overførselshastigheden er målingen stor ydeevne i benchmark. Overførselshastighed rapporteres i transaktioner per for-tidsenhed, hvor der tælles alle typer.
- Svartid er en måling af ydeevne forudsigelighed. Svar tidsbegrænsningen varierer med klasse-tjenesten, med højere klasser af tjenesten med et mere strenge svar tid krav, som vist nedenfor.

| Klasse-tjenesten  | Overførselshastighed mål | Svar tid krav |
|---|---|---|
| Premium | Transaktioner i sekundet | 95th fraktil på 0,5 sekunder |
| Standard | Transaktioner i minuttet | 90 fraktil på 1,0 sekunder |
| Grundlæggende | Transaktioner i sekundet | 80th fraktil på 2.0 sekunder |

## <a name="conclusion"></a>Konklusion
Azure SQL Database Benchmark måler relative ydeevnen for Azure SQL-Database, der kører på tværs af området af tilgængelige service niveauer og ydeevne. Benchmark øvelser en blanding af grundlæggende Databasefunktioner som forekommer hyppigst i online transaction processing (OLTP) arbejdsmængder. Ved at måle faktisk ydeevne, indeholder benchmark et mere sigende vurdering af effekten på overførsel for at ændre niveauet for ydeevnen, end det er muligt ved lige listen over de ressourcer, der leveres af hvert niveau som CPU-hastighed, hukommelsesstørrelse og IOP'ER. Vi fortsætter fremover, at udvikle benchmark for at udvide dets omfang og udvide de data, der er angivet.

## <a name="resources"></a>Ressourcer
[Introduktion til SQL-Database](sql-database-technical-overview.md)

[Tjenesten niveauer og ydeevne](sql-database-service-tiers.md)

[Ydeevnen vejledning til enkelt databaser](sql-database-performance-guidance.md)
