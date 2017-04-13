<properties
    pageTitle="Azure lagerplads gentagelse | Microsoft Azure"
    description="Data i din Microsoft Azure-lager konto replikeres til holdbarhed og høj tilgængelighed. Replikeringsindstillinger omfatter lokalt overflødige lagerplads (LRS), zone overflødige lagerplads (ZRS), geografisk overflødige lagerplads (GRS) og læseadgang geografisk overflødige lagerplads (RA-GRS)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Azure lagerplads gentagelse

Dataene i din Microsoft Azure-lager konto replikeres altid for at sikre holdbarhed og høj tilgængelighed. Replikering kopierer dine data, i det samme datacenter eller til et andet datacenter, afhængigt af hvilken gentagelse indstilling du vælger. Replikering beskytter dine data og bevarer dit program op og klokkeslæt i tilfælde af mislykkede forsøg på midlertidige hardware. Hvis dine data replikeres til et andet datacenter, beskytter, der også dine data mod opstår nedbrud i den primære placering.

Replikering sikrer, at kontoen lagerplads opfylder [Service-serviceaftale (SLA) for lagerplads](https://azure.microsoft.com/support/legal/sla/storage/) selv trods mislykkede forsøg. Se SLA oplysninger om Azure-lager garanterer for holdbarhed og tilgængelighed. 

Når du opretter en lagerplads-konto, kan du vælge en af følgende indstillinger for gentagelse:  

- [Lokalt overflødige lagerplads (LRS)](#locally-redundant-storage)
- [Zone overflødige lagerplads (ZRS)](#zone-redundant-storage)
- [Geografisk overflødige lagerplads (GRS)](#geo-redundant-storage)
- [Læseadgang geografisk overflødige lagerplads (RA-GRS)](#read-access-geo-redundant-storage)

Læseadgang geografisk overflødige lagerplads (RA-GRS) er standardindstillingen, når du opretter en ny konto lagerplads.

Den følgende tabel indeholder en hurtig oversigt over forskellene mellem LRS, ZRS, GRS og RA-GRS, mens efterfølgende afsnit adresse til hver type af replikering mere detaljeret.

| Strategi for gentagelse                                                               | LRS | ZRS | GRS | RA-GRS |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Data replikeres på tværs af flere datacentre.                                     | Nej  | Ja | Ja | Ja    |
| Data kan læses fra den sekundære placering og fra den primære placering. | Nej  | Nej  | Nej  | Ja    |
| Antallet af kopier af data vedligeholdes på separate knuder.                             | 3   | 3   | 6   | 6      |

Se [Azure lagerplads priser](https://azure.microsoft.com/pricing/details/storage/) til prisoplysninger for de forskellige redundans indstillinger.

>[AZURE.NOTE] Premium lagerplads understøtter kun lokalt overflødige lagring (LRS). Finde oplysninger om Premium lagerplads [Premium lagerplads: High-Performance lagerplads til Azure virtuelt arbejdsbelastninger](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Lokalt overflødige lagerplads

Lokalt overflødige lagerplads (LRS) kopieres dataene tre gange i en enhed for tidsskala lagerplads der er placeret i et datacenter i det område, hvor du har oprettet kontoen lagerplads. En anmodning om Skriv returnerer korrekt kun, når den er skrevet til alle tre kopier. Disse tre replikaer hver er placeret separat fejl domæner og opgradering domæner inden for et lagerenhed for tidsskala. 

En enhed for tidsskala lagerplads er en samling af rack af lagerplads knuder. En fejl domæne (FD) er en gruppe af knuder, der repræsenterer en fysisk måleenhed fejl og kan betragtes som noder, der hører til den samme fysiske rack. En opgradering domæne (UD) er en gruppe af knuder, der er opgraderet sammen under processen med en tjenesteopgraderingen (udrulning af). De tre replikaer spredes på tværs af UDs og FDs inden for en enhed for tidsskala lagerplads til at sikre, at dataene er tilgængelig, selvom hardwarefejl påvirker en enkelt rack, eller når noder opgraderes under en udrulning. 

LRS er indstillingen laveste omkostninger og indeholder mindst holdbarhed sammenlignet med andre indstillinger. Alle tre kopier kan gå tabt eller uoprettelig i tilfælde af datacenter niveau nedbrud (brand, oversvømmelse osv.). Hvis du vil reducere denne risiko anbefales geografisk overflødige lagerplads (GRS) for de fleste programmer.

Lokalt overflødige lagerplads muligvis stadig en god idé i visse scenarier: 

- Giver en højeste maksimale båndbredde Replikeringsindstillinger i Azure-lager.

- Hvis dit program lagrer data, der nemt genskabes, kan du vælge LRS.

- Nogle programmer er begrænset til replikering data kun inden for et land på grund af krav til styring af data. Kan være en parvis område i et andet land skal du se [Azure områder](https://azure.microsoft.com/regions/) for oplysninger om område par.

## <a name="zone-redundant-storage"></a>Zone overflødige lagerplads

Zone overflødige lagerplads (ZRS) kopieres asynkront dine data på tværs af datacentre inden for et eller to områder ud over lagring af tre replikaer ligner LRS, hvilket giver højere holdbarhed end LRS. Data gemt i ZRS er robust, selvom det primære datacenter er ikke tilgængelig eller uoprettelig.
Kunder, der planlægger at bruge ZRS skal være opmærksom på, som: 

- ZRS er kun tilgængelig for Bloker BLOB i generelle formål lagerplads konti, og understøttes kun i lagerplads service versioner 2014-02-14 eller nyere. 

- Da asynkron gentagelse indebærer en forsinkelse, i tilfælde af nedbrud lokale er det muligt, at ændringer, der endnu ikke er blevet replikeres til sekundært, gå tabt, hvis dataene ikke kan gendannes fra primært.

- Replikaen muligvis ikke tilgængelig, indtil Microsoft starter failover til sekundært.

- ZRS konti kan ikke konverteres senere til LRS eller GRS. På samme måde, en eksisterende LRS eller GRS konto ikke kan konverteres til en ZRS-konto.

- ZRS konti har ikke målepunkter eller logføringsegenskab. 

## <a name="geo-redundant-storage"></a>Geografisk overflødige lagerplads

Geografisk overflødige lagerplads (GRS) kopieres dataene til en sekundær område, der er hundredvis af sømil væk fra det primære område. Hvis kontoen lagerplads har GRS aktiveret, derefter er dine data robust selv i tilfælde af en komplet internationale afbrydelse eller nedbrud, ikke er det primære område gendannes.

For en lagerplads konto med GRS aktiveret, er en opdatering først sendt til den primære område, hvor den replikeres tre gange. Derefter replikeres opdateringen asynkront til den sekundære område, hvor det er også replikeres tre gange. 

Med GRS begge de primære og sekundære områder administrere replikaer på tværs af separat fejl domæner og opgradere domæner inden for en lagerplads enhed for tidsskala som beskrevet med LRS.

Overvejelser i forbindelse med:

- Da asynkron gentagelse indebærer en forsinkelse, i tilfælde af nedbrud internationale er det muligt, at ændringer, der endnu ikke er blevet replikeres til den sekundære region, gå tabt, hvis dataene ikke kan gendannes fra det primære område.

- Replikaen er ikke tilgængelig, medmindre Microsoft starter failover til den sekundære område.

- Hvis et program ønsker at læse fra den sekundære område skal brugeren aktivere RA-GRS. 

Når du opretter en lagerplads-konto, kan du vælge det primære område for kontoen. Sekundær område bestemmes baseret på den primære område og kan ikke ændres. Følgende tabel viser den primære og sekundære område-par.

| Primære             | Sekundær           |
|---------------------|---------------------|
| Nord centrale USA    | Syd centrale USA    |
| Syd centrale USA    | Nord centrale USA    |
| Indtastning af østasiatiske USA             | Vest USA             |
| Vest USA             | Indtastning af østasiatiske USA             |
| USA Øst 2           | Centrale USA          |
| Centrale USA          | USA Øst 2           |
| Nord Europe        | Vest Europe         |
| Vest Europe         | Nord Europe        |
| Syd Østasien     | Sydøstasien           |
| Sydøstasien           | Syd Østasien     |
| Indtastning af østasiatiske Kina          | Nord Kina         |
| Nord Kina         | Indtastning af østasiatiske Kina          |
| Japan øst          | Japan vest          |
| Japan vest          | Japan øst          |
| Brasilien syd        | Syd centrale USA    |
| Australien øst      | Australien Sydøst |
| Australien Sydøst | Australien øst      |
| Indien syd         | Indien Central       |
| Indien Central       | Indien syd         |
| Krigsskibe i USA Gov Iowa         | USA Gov Virginia     |
| USA Gov Virginia     | Krigsskibe i USA Gov Iowa         |
| Canada Central      | Canada øst          |
| Canada øst         | Canada Central      |
| Storbritannien vest             | Storbritannien syd            |
| Storbritannien syd            | Storbritannien vest             |
| Tyskland Central     | Tyskland nordøst   |
| Tyskland nordøst   | Tyskland Central     |
| Vest USA 2           | Central Vest USA     |
| Central Vest USA     | Vest USA 2           |

Du kan finde opdaterede oplysninger om områder, der understøttes af Azure [Azure områder](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Læseadgang geografisk overflødige lagerplads

Læseadgang geografisk overflødige lagerplads (RA-GRS) maksimerer tilgængelighed til kontoen lager ved at give skrivebeskyttet adgang til dataene i den sekundære placering, ud over gentagelse på tværs af to områder, der leveres af GRS. 

Når du aktiverer skrivebeskyttet adgang til dine data i det sekundære region, findes dataene på en sekundær slutpunkt, ud over det primære slutpunkt for kontoen lagerplads. Sekundær slutpunktet svarer til det primære slutpunkt, men tilføjer suffikset `–secondary` til kontonavnet. Hvis din primære slutpunkt for tjenesten Blob er eksempelvis `myaccount.blob.core.windows.net`, så er din sekundære slutpunkt `myaccount-secondary.blob.core.windows.net`. Hurtigtasterne til kontoen lagerplads er de samme for begge primære og sekundære slutpunkter.

Overvejelser i forbindelse med:

- Dit program har til at administrere som det arbejde med, når du bruger RA-GRS slutpunkt. 

- RA-GRS er beregnet til høj tilgængelighed formål. Gennemgå [ydeevne tjekliste](storage-performance-checklist.md)skalerbarhed vejledning.

## <a name="next-steps"></a>Næste trin

- [Azure-lager priser](https://azure.microsoft.com/pricing/details/storage/)
- [Om Azure lagerplads konti](storage-create-storage-account.md)
- [Azure-lager skalerbarhed og ydeevne destinationer](storage-scalability-targets.md)
- [Indstillinger for Microsoft Azure lagring redundans og læseadgang geografisk overflødige lagerplads](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [SOSP papir - Azure-lager: En meget tilgængelige Skylagringstjeneste med stærke konsistens](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
