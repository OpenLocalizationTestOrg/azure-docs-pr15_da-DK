<properties
    pageTitle="Oprette en Azure Search-tjenesten ved hjælp af portalen Azure | Microsoft Azure | Hostet skyen search-tjenesten"
    description="Lær, hvordan du klargør en Azure Search-tjenesten ved hjælp af portalen Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Oprette en Azure Search-tjenesten ved hjælp af portalen Azure

Denne vejledning fører dig gennem processen med at oprette (eller klargøring) en Azure Search-tjenesten ved hjælp af [Azure-portalen](https://portal.azure.com/).

Denne vejledning forudsætter, at du allerede har et abonnement til Azure og kan logge på portalen Azure.

## <a name="find-azure-search-in-the-azure-portal"></a>Finde Azure søgning i portalen Azure
1. Gå til [Azure-portalen](https://portal.azure.com/) , og log på.
1. Klik på plustegnet ("+") i øverste venstre hjørne.
2. Vælg **Data + lagerplads**.
3. Vælg **Azure Søg**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>For at vælge navnet på tjenesten og URL-adressen slutpunkt for din tjeneste
1. Tjenestenavnet på din bliver en del af din Azure Search-tjenesten slutpunkt URL-adresse, der er mod, du vil gøre dine API opkald til at administrere og bruge search-tjenesten.
2. Skriv tjenestenavnet på din i feltet **URL-adresse** . Navnet på tjenesten:
  * må kun indeholde små bogstaver, tal eller stiplet ("-")
  * kan ikke bruge en bindestreg ("-") som det første 2 tegn eller sidste enkelt tegn
  * må ikke indeholde gentagne bindestreger ("-")
  * er begrænset mellem 2 og 60 tegn


## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Vælg et abonnement, hvor du beholder din tjeneste
Hvis du har mere end ét abonnement, kan du vælge, hvilken af skærmene omfatter denne Azure Search-tjenesten.

## <a name="select-a-resource-group-for-your-service"></a>Vælg en ressourcegruppe til din tjeneste
Opret en ny ressourcegruppe eller Vælg et eksisterende dokument. En ressourcegruppe er en samling af Azure tjenester og ressourcer, der bruges sammen. Eksempelvis hvis du bruger Azure Søg indeksere en SQL-database, skal derefter begge af disse tjenester være en del af den samme ressourcegruppe.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Vælg den placering, hvor tjenesten bruges
Som en Azure service findes Azure Søg til at være vært datacentre over hele verden. Vær opmærksom på, [priser kan variere](https://azure.microsoft.com/pricing/details/search/) efter Geografi.

## <a name="select-your-pricing-tier"></a>Vælg din priser niveau
[Azure Søg tilbydes aktuelt i flere priser lag](https://azure.microsoft.com/pricing/details/search/): gratis, grundlæggende eller Standard. Hvert niveau har sin egen [kapacitet og begrænsninger for](search-limits-quotas-capacity.md). Se [vælge en priser niveau eller SKU](search-sku-tier.md) vejledning.

I dette tilfælde har vi valgt kan Standard niveauet for vores tjeneste.

## <a name="select-the-create-button-to-provision-your-service"></a>Vælg knappen "Opret" til at klargøre din tjeneste

![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Tilpasse din tjeneste

Når din tjeneste er klargjort, kan du skalere det at opfylde dine behov. Hvis du har valgt det Standard niveau for dine Azure Search-tjenesten, kan du tilpasse din tjeneste todimensional: replikaer og partitioner. Hvis du har valgt de grundlæggende trin, kan du kun tilføje replikaer.

*__Partitioner__* Tillad, at din tjeneste til at gemme og søge i flere dokumenter.

*__Replikaer__* Tillad, at din tjeneste til at håndtere en højere belastning på søgeforespørgsler - [en tjeneste kræver 2 replikaer for at opnå en skrivebeskyttet SLA og kræver 3 replikaer for at opnå en læse-og skriveadgang SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå til din Azure Search-tjenesten management blade i portalen Azure.
2. Vælg **skala**i bladet **Indstillinger** .
3. Du kan tilpasse din tjeneste ved at tilføje replikaer eller partitioner.
  * Du kan ikke skalere din tjeneste tidligere 36 Søg enheder. Det samlede antal Søg enheder er produktet af din replikaer og partitioner (replikaer * partitioner = samlet søgning enheder).
  * Hvis du har valgt de grundlæggende trin, kan du kun skalere til 3 replikaer. Grundlæggende tjenester er bundet til en enkelt partition.

![](./media/search-create-service-portal/scale-service.png)

## <a name="next"></a>Næste
Efter klargøring af en Azure Search-tjenesten, vil du være klar til at [definere en Azure søgeindekset](search-what-is-an-index.md) så du kan overføre og søge i dine data.

Se [Introduktion til Azure søgning i portalen](search-get-started-portal.md) for at få en hurtig selvstudium.
