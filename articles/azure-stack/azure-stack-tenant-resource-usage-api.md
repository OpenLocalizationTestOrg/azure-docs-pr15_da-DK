<properties
    pageTitle="Lejer Ressourceforbrug API | Microsoft Azure"
    description="Reference til ressourceforbrug API, som hente oplysninger om brugen af Azure stak."
    services="azure-stack"
    documentationCenter=""
    authors="AlfredoPizzirani"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="alfredop"/>

# <a name="tenant-resource-usage-api"></a>Lejer Ressourceforbrug API

En lejer kan bruge lejer API lejerens egen ressource brugen dataene. Denne API stemmer overens med Azure brugen API (i øjeblikket i private preview).

Du kan bruge Windows PowerShell-cmdlet'en **Get-UsageAggregates** til at få Brugsdata som i Azure.

## <a name="api-call"></a>API-opkald

### <a name="request"></a>Anmode om

Anmodningen får detaljer om forbrug for de ønskede abonnementer og for den ønskede tidsramme. Der er ingen anmodningsteksten.

| **Metode**  | **Anmode om URI** |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HENT         | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumenter

| **Argument**             | **Beskrivelse** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *Armendpoint*             | Azure ressourcestyring slutpunkt Azure stak-miljø. |
| *subId*                   | Abonnement-ID på den bruger, er at foretage opkaldet. Du kan bruge denne API kun for forespørgsel til et enkelt abonnement brugen. Udbydere kan bruge forespørgsel brugen af udbyder ressource brugen API til alle lejere. |
| *reportedStartTime*       | Starttidspunktet for forespørgslen. Værdien for *dato/klokkeslæt* skal være i UTC og i starten af timetallet, for eksempel 13:00. Angiv denne værdi til UTC midnat til daglige sammenlægning. Formatet er *escape-tegn* ISO 8601, for eksempel 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, hvor kolon er escape-tegn til % 3a og plus er escape-tegn til % 2b så det er URI fulde. |
| *reportedEndTime*         | Sluttidspunkt for forespørgslen. Begrænsninger, der gælder for *reportedStartTime* gælder også for dette argument. Værdien for *reportedEndTime* må ikke være i fremtiden. |
| *aggregationGranularity*  | Valgfri parameter, der indeholder to separate mulige værdier: dagligt og hver time. Som værdierne foreslå, en returnerer dataene i daglige granularitet, og den anden er en time opløsning. Indstillingen Daglig er standard. |
| *subscriberId*            | Abonnement-ID. Abonnement-ID for en direkte lejer af provideren er påkrævet for at få filtrerede data. Hvis ingen abonnement-ID-parameter er angivet, returnerer opkaldet Brugsdata for alle udbyderens direkte lejere. |
| *API-version*             | Version af den protokol, der bruges til at foretage denne anmodning. Du skal bruge 2015-06-01-preview. |
| *continuationToken*       | Token hentet fra det sidste opkald til brugen API-udbyder. Det er nødvendigt, når et svar er større end 1000 linjer. Dette er bogmærke i gang. Hvis det er ikke til stede, data er hentet fra begyndelsen af dag eller time, baseret på granularitet overføres i. |

### <a name="response"></a>Svar

Hent /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = dagligt & api-version = 1.0

{

"værdi":\[

{

"-id": "/ subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",

"navn": "Abn1-meterID1"

"type": "Microsoft.Commerce/UsageAggregate"

"Egenskaber": {}

"subscriptionId": "Abn1"

"usageStartTime": "2015-03-03T00:00:00 + 00:00",

"usageEndTime": "2015-03-04T00:00:00 + 00:00",

"instanceData": "{\\" Microsoft.Resources\\": {\\" resourceUri\\":\\" resourceUri1\\",\\" placering\\":\\" Alaska\\",\\" mærker\\": null-værdi,\\" additionalInfo\\": null}}",

"antal":2.4000000000

"meterId": "meterID1"

}

},

…

### <a name="response-details"></a>Svar detaljer

| **Argument**      | **Beskrivelse** |
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *id*              | Entydigt ID for brugen aggregat |
| *Navn*            | Navnet på et brugen aggregat |
| *type*            | Definition af ressource |
| *subscriptionId*  | Abonnement-id for den Azure bruger |
| *usageStartTime*  | UTC starttidspunktet for den brugen Malerbøtte, som denne brugen aggregat tilhører |
| *usageEndTime*    | UTC sluttidspunktet for den brugen Malerbøtte, som denne brugen aggregat tilhører |
| *instanceData*    | Nøgle-værdi-par af forekomst detaljer (i et nyt format):<br>  *resourceUri*: fuldt kvalificeret ressource-ID, herunder ressourcegrupper og navnet på forekomsten <br>  *placering*: område, hvor tjenesten blev kørt <br>  *mærker*: ressource mærker, som brugeren angiver <br>  *additionalInfo*: flere detaljer om den ressource, der consumed, for eksempel OS af typen version eller billede |
| *antal*        | Mængde Ressourceforbrug, der er indtruffet i denne tidsramme |
| *meterId*         | Entydigt ID for den ressource, der blev brugt (også kaldet *ResourceID*) |

## <a name="next-steps"></a>Næste trin

[Brugen beslægtede ofte stillede spørgsmål](azure-stack-usage-related-faq.md)
