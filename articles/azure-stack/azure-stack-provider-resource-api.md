<properties
    pageTitle="Udbyder Ressourceforbrug API | Microsoft Azure"
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

# <a name="provider-resource-usage-api"></a>Udbyder Ressourceforbrug API

Provideren udtryk gælder for tjenesteadministratoren og til en hvilken som helst delegeret udbydere. Tjenesteadministratorer og delegeret udbydere kan bruge udbyder brugen API til at få vist brugen af deres direkte lejere. For eksempel P0 kan ringe til API'EN udbyder for at få oplysninger om brug af P1's og P2's direkte brug, og P1 kan ringe til brug af oplysninger om P3 og P4.

![Grundlæggende model i udbyder hierarkiet](media/azure-stack-provider-resource-api/image1.png)


## <a name="api-call-reference"></a>API opkald reference

### <a name="request"></a>Anmode om

Anmodningen får detaljer om forbrug for de ønskede abonnementer og for den ønskede tidsramme. Der er ingen anmodningsteksten.

Denne brugen API er en udbyder API, så kalderen skal tildeles en ejer, bidragyder eller læser rolle i udbyderens abonnement.

| **Metode**  | **Anmode om URI** |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
|  HENT        | https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumenter

| **Argument**              | **Beskrivelse** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| *armendpoint*             | Azure ressourcestyring slutpunkt Azure stak-miljø. Azure stak konferencen er, at navnet på ARM slutpunkt i formatet https://api. {domænenavnet}'. Eksempelvis hvis domænenavnet er azurestack.local, bliver ARM slutpunktet https://api.azurestack.local. |
| *subId*                   | Abonnement-ID på den bruger, er at foretage opkaldet. |
| *reportedStartTime*       | Starttidspunktet for forespørgslen. Værdien for *dato/klokkeslæt* skal være i UTC og i starten af timetallet, for eksempel 13:00. Angiv denne værdi til UTC midnat til daglige sammenlægning. Formatet er *escape-tegn* ISO 8601, for eksempel 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, hvor kolon er escape-tegn til % 3a og plus er escape-tegn til % 2b så det er URI fulde. |
| *reportedEndTime*         | Sluttidspunkt for forespørgslen. Begrænsninger, der gælder for *reportedStartTime* gælder også for dette argument. Værdien for *reportedEndTime* må ikke være i fremtiden. |
| *aggregationGranularity*  | Valgfri parameter, der indeholder to separate mulige værdier: dagligt og hver time. Som værdierne foreslå, en returnerer dataene i daglige granularitet, og den anden er en time opløsning. Indstillingen Daglig er standard. |
| *subscriberId*            | Abonnement-ID. Abonnement-ID for en direkte lejer af provideren er påkrævet for at få filtrerede data. Hvis ingen abonnement-ID-parameter er angivet, returnerer opkaldet Brugsdata for alle udbyderens direkte lejere. |
| *API-version*             | Version af den protokol, der bruges til at foretage denne anmodning. Du skal bruge 2015-06-01-preview. |
| *continuationToken*       | Token hentet fra det sidste opkald til brugen API-udbyder. Det er nødvendigt, når et svar er større end 1000 linjer. Dette er bogmærke i gang. Hvis det er ikke til stede, data er hentet fra begyndelsen af dag eller time, baseret på granularitet overføres i. |



### <a name="response"></a>Svar

Hent /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = dagligt & subscriberId = sub1.1 & api-version = 1.0

{

"værdi":\[

{

"-id": "/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1 ",

"navn": "sub1.1-meterID1"

"type": "Microsoft.Commerce/UsageAggregate"

"Egenskaber": {}

"subscriptionId": "sub1.1"

"usageStartTime": "2015-03-03T00:00:00 + 00:00",

"usageEndTime": "2015-03-04T00:00:00 + 00:00",

"instanceData": "{\\" Microsoft.Resources\\": {\\" resourceUri\\":\\" resourceUri1\\",\\"placering\\

":\\" Alaska\\",\\" mærker\\": null-værdi,\\" additionalInfo\\": null}}",

"antal":2.4000000000

"meterId": "meterID1"

}

},

…

### <a name="response-details"></a>Svar detaljer


| **Argument**       | **Beskrivelse**
| ------------------ | ------------------------------------------------------------------------------------------------------------- |
| *id*               | Entydigt ID for brugen aggregat
| *Navn*             | Navnet på et brugen aggregat
| *type*             | Definition af ressource
| *subscriptionId*   | Abonnement-id for brugeren, Azure stak
| *usageStartTime*   | UTC starttidspunktet for den brugen Malerbøtte, som denne brugen aggregat tilhører
| *usageEndTime*     | UTC sluttidspunktet for den brugen Malerbøtte, som denne brugen aggregat tilhører
| *instanceData*     | Nøgle-værdi-par af forekomst detaljer (i et nyt format):<br> *resourceUri*: fuldt kvalificeret ressource-ID, som indeholder grupperne og navnet på forekomsten <br> *placering*: område, hvor tjenesten blev kørt <br> *mærker*: ressource mærker, der er angivet af brugeren <br> *additionalInfo*: flere detaljer om den ressource, der consumed, for eksempel OS af typen version eller billede |
| *antal*         | Mængde Ressourceforbrug, der er indtruffet i denne tidsramme |
| *meterId*          | Entydigt ID for den ressource, der blev brugt (også kaldet *ResourceID*) |

## <a name="next-steps"></a>Næste trin

[Lejer Ressourceforbrug API reference](azure-stack-tenant-resource-usage-api.md)

[Brugen beslægtede ofte stillede spørgsmål](azure-stack-usage-related-faq.md)
