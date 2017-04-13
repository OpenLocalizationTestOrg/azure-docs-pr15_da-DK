<properties
    pageTitle="Vælg en anden SKU eller priser niveau til Azure søgning | Microsoft Azure"
    description="Azure søgning kan klargøres på disse lagerenheder: gratis, grundlæggende og standardversionen, hvor Standard er tilgængelige i forskellige ressource konfigurationer og kapacitet niveauer."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Vælg en anden SKU eller priser niveau til Azure søgning

I Azure søgning, en [tjeneste er klargjort](search-create-service-portal.md) på en bestemt priser niveau eller SKU. Indstillingerne for inkluderer **ledig**, **grundlæggende**eller **Standard**, hvor **Standard** er tilgængelig i flere konfigurationer og kapacitet. 

Formålet med denne artikel er at vælge et trin. Hvis et trin kapacitet viser sig for lav, skal du klargør en ny tjeneste højere lag og derefter Genindlæs dit indeks. Der er ingen lokal opgradering af den samme tjeneste fra én SKU til en anden. 

> [AZURE.NOTE] Når du vælger et niveau og [klargøring en søgetjeneste](search-create-service-portal.md), du kan øge replika og partition tæller i tjenesten. Du kan finde vejledning, [skala ressource niveauer for forespørgsel og indeksering arbejdsmængder](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>Sådan tilgang priser niveau beslutning

I Azure Søg bestemmer niveauet kapacitet, ikke tilgængelighed af funktioner. Generelt er funktioner tilgængelige på alle niveauer, herunder funktionerne preview. Den eneste undtagelse er ingen understøttelse af indeks i S3 HD.

> [AZURE.TIP] Vi anbefaler, at du altid klargøre en **gratis** tjeneste (én per abonnement med intet udløb) så dens tilgængelige for de projekter, nedtonet. Bruge tjenesten **ledig** for at teste og evaluering oprette en anden fakturerbar tjeneste **grundlæggende** eller **Standard** lag til fremstilling eller større test arbejdsmængder.

Kapacitet og omkostninger for kører tjenesten gå hånd i hånden. Oplysninger i denne artikel kan hjælpe dig med at beslutte, hvilke SKU leverer den rette balance, men på grund af det er nyttigt, du skal mindst grov beregner på følgende:

- Antallet af og størrelsen af indeks, du planlægger at oprette
- Antallet af og størrelsen af dokumenter til at overføre
- Nogle ide forespørgsel beholdning, med hensyn til forespørgsler hver anden (QPS)

Antal og størrelse er vigtige, fordi maksimumbegrænsninger er nået via en grænse på antallet af indeks eller dokumenter i en tjeneste eller på ressourcer (lagerplads eller replikaer), der bruges af tjenesten. Den faktiske grænse for tjenesten er afhængigt af hvad der bliver brugt først: ressourcer eller objekter.

Følgende trin skal forenkle processen med estimater i hånden:

- **Trin 1** Gennemse SKU beskrivelserne nedenfor for at få mere at vide om forskellige indstillinger.
- **Trin 2** Besvar spørgsmålene nedenfor for at ankomme til en foreløbig beslutning.
- **Trin 3** Færdiggøre din beslutning ved at gennemse hårde begrænsninger på lager og priser.

## <a name="sku-descriptions"></a>SKU beskrivelser

Den følgende tabel indeholder beskrivelser af hvert niveau. 

Niveau|Primære scenarier
----|-----------------
**Gratis**|En delt tjeneste gratis, bruges til evaluering, undersøgelsen eller small arbejdsmængder. Da den er delt med andre abonnenter, varierer forespørgsel overførselshastighed og indeksering afhængigt af, hvem der ellers ved hjælp af tjenesten. Kapacitet er lille (50 MB eller 3 indeks med op 10.000 dokumenter hver).
**Grundlæggende**|Lille fremstilling arbejdsbelastningen på dedikeret hardware. Meget tilgængeligt. Kapacitet er op til 3 replikaer og 1 partition (2 GB).
**S1**|Standard 1 understøtter fleksible kombinationer af partitioner (12) og replikaer (12), bruges til mellemstore fremstilling arbejdsbelastningen på dedikeret hardware. Du kan tildele partitioner og replikaer i kombinationer, der understøttes af et maksimalt antal 36 fakturerbar Søg enheder. På dette niveau partitioner er 25 GB og QPS er cirka 15 forespørgsler sekundet.
**S2**|Standard 2 kører større fremstilling arbejdsbelastninger, som bruger samme 36 Søg enheder, som S1 men med større størrelse partitioner og replikaer. På dette niveau partitioner er 100 GB og QPS er om 60 forespørgsler sekundet.
**S3**|Standard 3 kører proportionalt større fremstilling arbejdsbelastninger på højere end-systemer i konfigurationer af op til 12 partitioner eller 12 replikaer under 36 Søg enheder. På dette niveau partitioner er 200 GB og QPS er mere end 60 forespørgsler sekundet. 
**S3 HD**|Standard 3 høj tæthed er udviklet til et stort antal mindre indeks. Du kan have op til 3 partitioner på 200 GB. QPS er mere end 60 forespørgsler sekundet. 

> [AZURE.NOTE] Replika og partition maksimumværdier faktureret ud som Søg enheder (36 enhed i maksimale per service), der er sat, en effektiv nedre grænse end hvad det maksimale antal indebærer på nominel værdi. For eksempel for at bruge maksimalt antal 12 replikaer, du kan have højst 3 partitioner (12 * 3 = 36 enheder). På samme måde for at bruge maksimale partitioner skal du reducere replikaer til 3. Se [skala ressource niveauer for forespørgsel og indeksering arbejdsmængder i Azure søgning](search-capacity-planning.md) til et diagram på tilladte kombinationer.

## <a name="review-limits-per-tier"></a>Gennemse begrænsninger per niveau

Det følgende diagram er et undersæt af begrænsningerne fra [Tjenesten begrænsninger i Azure Søg](search-limits-quotas-capacity.md). Det viser sandsynligvis vil påvirke en SKU beslutning faktorer. Du kan referere til dette diagram, når du gennemser spørgsmålene nedenfor.

Ressource|Gratis|Grundlæggende|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Serviceniveauaftale (SLA)|Ingen <sup>1</sup> |Ja |Ja  |Ja |Ja  |Ja 
Begrænsninger for indeks|3|5|50|200|200|1000 <sup>2</sup>
Begrænsninger for dokument|samlet 10.000|1 million hver tjeneste|15 millioner per partition |60 millioner per partition|120 millioner per partition |1 million per indeks
Maksimale partitioner|I/T. |1 |12  |12 |12|3 <sup>2</sup>
Partitionsstørrelse|50 MB total|2 GB per service|25 GB per partition |100 GB per partition (op til maksimalt 1,2 TB hver tjeneste)|200 GB per partition (op til maksimalt 2,4 TB hver tjeneste)|200 GB (op til maksimalt 600 GB per service)
Maksimale replikaer|I/T. |3 |12 |12 |12|12
Forespørgsler sekundet|I/T.|~ 3 / replika|~ 15 per replika|~ 60 per replika|> 60 per replika|> 60 per replika

<sup>1</sup> gratis og Preview lagerenheder ikke kommer med SLA. SLA gennemtvinges, når en anden SKU bliver alment tilgængelig.

<sup>2</sup> S3 og S3 HD understøttes af identiske høj kapacitet infrastruktur, men hver enkelt når dens maksimumgrænsen på forskellige måder. S3 er beregnet til et mindre antal meget store indeks. Som sådan dens maksimumgrænsen er bundet til ressource (2,4 TB for hver tjeneste). S3 HD rettet mod et stort antal meget lille indeks. På 1.000 indeks grænsen S3 HD i form af indeks begrænsninger. Hvis du er en S3 HD-kunde, der kræver mere end 1000 indeks, kan du kontakte Microsoft Support for at få oplysninger om, hvordan du fortsætter.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Fjerne lagerenheder, der ikke opfylder kravene 

Følgende spørgsmål kan hjælpe dig med at komme frem til det rigtige SKU beslutning for din arbejdsbyrde.

1. Har du krav til **Serviceaftale (SLA)** ? Begræns SKU beslutning til grundlæggende eller ikke-preview Standard.
2. **Hvor mange indeks** gør du kræver? En af de største variabler factoring til en SKU beslutning er antallet af indeks, der understøttes af hver SKU. Indeks-understøttelse er på væsentligt forskellige niveauer i de nederste priser niveauer. Krav til antallet af indeks kan være en primær Determinanten i en SKU beslutning.
3. **Hvor mange dokumenter** indlæses i hvert indeks? Antallet af og størrelsen af dokumenter bestemmer eventuel størrelsen på indekset. Hvis du kan beregne planlagte størrelsen af indekset, kan du sammenligne dette nummer mod partitionsstørrelsen per SKU, forlænget med antallet af partitioner, der er påkrævet for at gemme et indeks til størrelsen. 
4. **Hvad er den forventede forespørgsel indlæses**? Når krav til lagerplads er forstået, bør du overveje arbejdsbelastninger, som forespørgslen. S2 og begge S3 lagerenheder tilbyder i nærheden af tilsvarende overførselshastighed, men SLA krav vil udelukke en hvilken som helst preview lagerenheder. 
5. Hvis du overvejer S2 eller S3 niveau, kan du bestemme, om du har brug for [indeks](search-indexer-overview.md). Indeks er ikke tilgængelige for S3 HD niveauet. Alternativ metode er at bruge en opslagsnål model for indeks opdateringer, hvor du kan skrive programkode til at overføre et datasæt til et indeks.

De fleste kunder kan reglen en bestemt SKU ind eller ud, baseret på deres svar på disse spørgsmål. Hvis du stadig ikke er sikker på, at hvilke SKU til, kan du stille spørgsmål til MSDN- eller StackOverflow fora eller kontakt Azure Support for at få yderligere vejledning.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Beslutning validering: SKU tilbyder tilstrækkelige lager og QPS?

Som et sidste trin, du besøger [priser side](https://azure.microsoft.com/pricing/details/search/) og [-tjenester og -index sektioner i tjenesten begrænsninger](search-limits-quotas-capacity.md) for at dobbelttjekke dine overslag mod abonnement og tjenesten begrænsninger. 

Hvis den pris eller opbevaring krav er overskredet, kan du få refactor arbejdsmængder mellem flere mindre tjenester (for eksempel). På mere detaljeret niveau, kan du omdesigne indeks for at være mindre, eller du kan bruge filtre til at gøre forespørgsler mere effektiv.

> [AZURE.NOTE] Krav til lagerplads kan være uberettiget oppustede, hvis dokumenter indeholder overflødige data. Ideelt set indeholder dokumenter kun kan søges efter data eller metadata. Binære data er ikke kan søges og skal være gemt separat (måske på en Azure tabel eller blob storage) med et felt i indekset for at holde en URL-referencen til de eksterne data. Den maksimale størrelse på et enkelt dokument er 16 MB (eller mindre Hvis du er flere overførsel af flere dokumenter i en anmodning om). Du kan få flere oplysninger i [tjenesten begrænsninger i Azure Søg](search-limits-quotas-capacity.md) .

## <a name="next-step"></a>Næste trin

Når du kender som SKU der passer bedst, skal du fortsætte med disse trin:

- [Oprette en søgetjeneste i portalen](search-create-service-portal.md)
- [Ændre fordelingen af partitioner og replikaer skalere din tjeneste](search-capacity-planning.md)

