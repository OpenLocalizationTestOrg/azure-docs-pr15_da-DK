<properties
    pageTitle="Hvad du skal gøre i tilfælde af en Azure service afbrydelser, der påvirker Azure-tasten samling | Microsoft Azure"
    description="Få mere at vide, hvad du skal gøre i tilfælde af en Azure tjenesteforstyrrelse, der påvirker Azure-tasten samling."
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure-tasten samling tilgængelighed og redundans

Azure-tasten samling funktioner redundans at sikre dig, at dine nøgler og hemmeligheder forbliver tilgængelige for dit program selvom enkelte komponenter i tjenesten mislykkes i flere lag.

Indholdet af din nøgle samling replikeres inden for området og til et sekundært område mindst 150 sømil ikke til stede, men inden for den samme Geografi. Dette bevarer stor holdbarhed dine nøgler og hemmeligheder.

Hvis der ikke individuelle komponenter i tjenesten vigtige samling, trin alternative komponenter i området at kunne levere din anmodning om at kontrollere, at der er ingen er forringet af funktionalitet. Du behøver ikke at foretage dig noget udløses dette. Der sker automatisk, og der vil være synlige for dig.

I sjældne begivenheden, en hel Azure område er ikke tilgængelig, er de anmodninger, du foretager af Azure-tasten samling i den pågældende region automatisk distribueret (*mislykkedes*) til et sekundært område. Når det primære område findes igen, anmodninger distribueres tilbage (*mislykkedes tilbage*) til det primære område. Igen, behøver du ikke foretage dig noget fordi dette sker automatisk.

Der er et par advarsler skal være opmærksom på:

* I tilfælde af en region failover, kan det tage et par minutter for tjenesten mislykkes. Anmodninger, der er foretaget i denne periode kan mislykkes, indtil sekundære er fuldført.
* Når en failover er fuldført, er din nøgle samling i skrivebeskyttet tilstand. Anmodninger, der understøttes i denne tilstand er:
 * Liste over vigtige vaults
 * Få egenskaberne for vigtige vaults
 * Listen hemmeligheder
 * Få hemmeligheder
 * Listen taster
 * Hent nøgler (egenskaber for)
 * Kryptere
 * Dekryptere
 * Ombryde
 * Fjerne ombrydning af
 * Bekræfte
 * Log
 * Sikkerhedskopi
* Når en failover er mislykkedes tilbage, findes alle anmodning om de typer (herunder læsning *og* skriveanmodninger).
