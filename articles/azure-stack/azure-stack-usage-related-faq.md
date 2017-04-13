<properties
    pageTitle="Ofte stillede spørgsmål til brugen-relaterede | Microsoft Azure"
    description="Liste over Azure stak målere, sammenligning med Azure brugen API, brugstid og rapporteret tid fejlkoder."
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

# <a name="azure-stack-usage-api-faqs"></a>Azure stak brugen API ofte stillede spørgsmål
I denne artikel finder du svar på nogle ofte stillede spørgsmål om Azure stak brugen API.

## <a name="what-meter-ids-can-i-see"></a>Hvilke dokumentmåler id'er kan jeg se?

I øjeblikket rapporteres brugen for netværket, lager og Beregn ressource udbydere.

| **Ressource-udbyder** | **Tæller-ID** |**Meter navn** | **Enhed** | **Yderligere oplysninger** |
| --------------------------- | --------------------------------------- | -------------------------- | ---------------------------- | ----------------------------------------- |
| **Netværk** | f114cb19-ea64-40b5-bcd7-aee474b62853 | Offentlige IP-adresse brugen | IP-adresse |                    
| **Lagerplads**  | B4438D5D-453B-4EE1-B42A-DC72E377F1E4 | TableCapacity | GB\*timer | Samlede kapacitet, der bruges af tabeller |
|              | B5C15376-6C94-4FDD-B655-1A69D138ACA3 | PageBlobCapacity | GB\*timer | Samlede kapacitet consumed ved siden BLOB |
|              | B03C6AE7-B080-4BFA-84A3-22C800F315C6 | QueueCapacity  | GB\*timer  | Samlede kapacitet, der er brugt i kø |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 | BlockBlobCapacity | GB\*timer  | Samlede kapacitet consumed ved at blokere BLOB |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 | TableTransactions  | Anmode om Tæl i 10,000s   | Tabel serviceanmodninger (i 10,000s) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D | TableDataTransIn | Vandindtrængen data i GB | Tabel service data vandindtrængen i GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 | TableDataTransOut | Outgress i GB | Tabel service data udgangspunkt i GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 | BlobTransactions | Anmodninger om tælle i 10,000s | BLOB serviceanmodninger (i 10,000s) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810   | BlobDataTransIn    | Vandindtrængen data i GB          | BLOB service data vandindtrængen i GB 
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8   | BlobDataTransOut   | Outgress i GB              | BLOB service data udgangspunkt i GB 
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA   | QueueTransactions  | Anmodninger om tælle i 10,000s   | Kø serviceanmodninger (i 10,000s) 
| | E518E809-E369-4A45-9274-2017B29FFF25   | QueueDataTransIn          | Vandindtrængen data i GB         | Kø service data vandindtrængen i GB 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2   | QueueDataTransOut         | Outgress i GB  | Kø service data udgangspunkt i GB 
| **Beregne** | 6DAB500F-A4FD-49C4-956D-229BB9C8C793 | VM størrelse timer | Virtuelt størrelse |



## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Hvordan Azure stak brugen API'er i sammenligning med [Azure brugen API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (i øjeblikket i offentlige preview)?

-   Lejer brugen API er i overensstemmelse med API'EN Azure med én undtagelse: flaget *showDetails* aktuelt understøttes ikke i Azure stablen.

-   Udbyder brugen API gælder kun for Azure stablen.

-   [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) , som er tilgængelig i Azure er i øjeblikket ikke tilgængelig i Azure stak.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Hvad er forskellen mellem brugstid og rapporteret tid?

Brugsrapporter data har to primære klokkeslætsværdier:

-   **Rapporteret tid**. Den tid, når hændelsen brugen angivet brugen systemet

-   **Brugstid**. Den tid, når Azure stak ressourcen blev consumed

Du muligvis vist en uoverensstemmelse i værdier for brugstid og rapporteret tid for en bestemt brugen. Forsinkelsen kan være så lang tid, som flere timer i en hvilken som helst miljø.

I øjeblikket, kan du forespørge *kun af rapporteret tid*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Hvad betyder disse brugen API fejlkoder?

| **HTTP-statuskode** | **Fejlkode** | **Beskrivelse** |
| ---------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| 400/forkert anmodning        | *NoApiVersion*     | Der mangler forespørgselsparameter *api-version* .
| 400/forkert anmodning        | *InvalidProperty*  | En egenskab mangler eller har en ugyldig værdi. Meddelelsen i fejlkoden i brødteksten svar identificerer manglende egenskaben.
| 400/forkert anmodning        | *RequestEndTimeIsInFuture*  | Værdien for *ReportedEndTime* er på et senere tidspunkt. Værdier er på et senere tidspunkt ikke tilladt for dette argument.
| 400/forkert anmodning        | *SubscriberIdIsNotDirectTenant*    | Et udbyder API opkald bruges et abonnement-ID, der ikke er en gyldig lejer af kalderen.
| 400/forkert anmodning        | *SubscriptionIdMissingInRequest*   | Kalderen abonnement ID mangler.
| 400/forkert anmodning        | *InvalidAggregationGranularity*   | En ugyldig sammenlægning granularitet blev anmodet om. Gyldige værdier er daglig og hver time.
| 503                    | *ServiceUnavailable*   | Der opstod en fejl, der kan prøves igen, fordi tjenesten er optaget eller opkaldet er der begrænset. |

## <a name="next-steps"></a>Næste trin
[Kunden fakturering og kortejeren Azure stablede](azure-stack-billing-and-chargeback.md)

[Udbyder Ressourceforbrug API](azure-stack-provider-resource-api.md)

[Lejer Ressourceforbrug API](azure-stack-tenant-resource-usage-api.md)
