<properties
    pageTitle="Guiden Hurtig start: Machine Learning tekst Analytics API'er | Microsoft Azure"
    description="Azure Machine Learning tekst Analytics - guiden Hurtig Start"
    services="cognitive-services"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>

# <a name="getting-started-with-the-text-analytics-apis-to-detect-sentiment-key-phrases-topics-and-language"></a>Introduktion til tekst Analytics API'er til at registrere synspunkt, vigtige sætninger, emner og sprog

<a name="HOLTop"></a>

Dette dokument beskrives, hvordan til indbyggede din tjeneste eller program tilladelse til at bruge [Tekst Analytics API'er](//go.microsoft.com/fwlink/?LinkID=759711).
Du kan bruge disse API'er til at registrere synspunkt, vigtige sætninger, emner og sprog fra din tekst. [Klik her for at få vist en interaktiv demonstration af oplevelsen.](//go.microsoft.com/fwlink/?LinkID=759712)

Se [API definitioner](//go.microsoft.com/fwlink/?LinkID=759346) af teknisk dokumentation til API'erne.

Denne vejledning er beregnet til version 2 af API'er. Få at vide om version 1 af API'erne, der [refererer til dette dokument](../machine-learning/machine-learning-apps-text-analytics.md).

Ved slutningen af selvstudiet, vil du kunne registrere programmeringsmæssigt:

- **Synspunkt** - er tekst, positivt eller negativt?

- **Tasten sætninger** - Hvad er personer omhandler i en enkelt artikel?

- **Emner** - Hvad er personer omhandler på tværs af mange artikler?

- **Sprog** - hvilke sprog er tekst skrevet på?

Bemærk, at denne API gebyrer 1 transaktion per dokument, som sendes. Som et eksempel, hvis du anmoder om synspunkt for 1000 dokumenter i et enkelt kald trækkes 1000 transaktioner.



<a name="Overview"></a>
## <a name="general-overview"></a>Generel oversigt ##

Dette dokument er en trinvis vejledning. Vores mål er at vejlede dig gennem de trin, der er nødvendigt at undervise en model, og peg ressourcer, der gør det muligt at anbringe det i fremstilling. Denne øvelse tager om 30 minutter.

For disse opgaver, skal du have en redaktør og ringe RESTful slutpunkterne dit sprog.

Lad os komme i gang!

## <a name="task-1---signing-up-for-the-text-analytics-apis"></a>Opgave 1 - signerende til tekst Analytics API'er ####

I denne opgave, kan du tilmelde dig til tekst analytics-tjenesten.

1. Gå til **Kognitiv tjenester** i [Azure-portalen](//go.microsoft.com/fwlink/?LinkId=761108) , og Sørg **Tekst Analytics** er valgt som 'API type'.

1. Vælg en plan. Du kan vælge det **gratis niveau for 5.000 transaktioner/måned**. Som det er en gratis plan, skal du ikke betale for ved hjælp af tjenesten. Du skal logge på abonnementet Azure. 

1. Udfyld de øvrige felter, og Opret din konto.

1. Når du tilmelder dig tekst Analytics, kan du finde **API-nøgle**. Kopiér den primære nøgle, som du skal bruge den, når du bruger API-tjenester.


## <a name="task-2---detect-sentiment-key-phrases-and-languages"></a>Opgave 2 – registrerer synspunkt, vigtige sætninger og sprog ####

Det er nemt at registrere synspunkt, sprog og vigtige udtryk i din tekst. Du får programmeringsmæssigt de samme resultater, som den [demo oplevelse](//go.microsoft.com/fwlink/?LinkID=759712) returnerer.

>[AZURE.TIP] Vi anbefaler, at du opdele tekst til sætninger for synspunkt analyse. Generelt fører til en højere præcision i synspunkt prognoser.

Bemærk, at de understøttede sprog er som følger:

| Funktion | Understøttede sprogkoder |
|:-----|:----|
| Synspunkt | `en`(På engelsk), `es` (spansk) `fr` (fransk), `pt` (Brasilien) |
| Vigtige udtryk | `en`(På engelsk), `es` (spansk) `de` (tysk), `ja` (japansk) |


1. Du skal angive overskrifterne følger. Bemærk, at JSON i øjeblikket er det eneste accepterede input format for API'erne. XML understøttes ikke.

        Ocp-Apim-Subscription-Key: <your API key>
        Content-Type: application/json
        Accept: application/json

1. Dernæst skal formatere din Skriv rækker i JSON. For synspunkt, vigtige sætninger og sprog er formatet den samme. Bemærk, at hvert-ID skal være entydige ID returneres af systemet. Den maksimale størrelse på et enkelt dokument, der kan sendes er 10KB, og den samlede maksimale størrelse af sendte input er 1MB. Kan ikke sendes ikke mere end 1000 dokumenter i et opkald. Begrænsning af hastigheden findes med en hastighed på 100 opkald i minuttet - vi anbefaler derfor, at du har indsendt store mængder dokumenter i et enkelt opkald. Sprog er en valgfri parameter, der skal angives hvis analyse af ikke-engelsk tekst. Et eksempel på input er vist nedenfor, hvor den valgfri parameter `language` for synspunkt analyse eller tast udtryk udtrækning af er inkluderet:

        {
            "documents": [
                {
                    "language": "en",
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "language": "en",
                    "id": "100",
                    "text": "Final document"
                }
            ]
        }

1. Foretage et **INDLÆG** opkald til systemet med input til synspunkt, vigtige sætninger og sprog. URL-adresserne ser ud som følger:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages

1. Dette opkald vil returnere en JSON formateret svar med id'erne og fundet egenskaber. Et eksempel på output til synspunkt er vist nedenfor (med flere oplysninger om fejlen udelades). Hvis det er synspunkt returneres en bogstavkarakter mellem 0 og 1 for hvert dokument:

        // Sentiment response
        {
            "documents": [
                {
                    "id": "1",
                    "score": "0.934"
                },
                ...
                {
                    "id": "100",
                    "score": "0.002"
                },
            ]
        }

        // Key phrases response
        {
            "documents": [
                {
                    "id": "1",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
                ...
                {
                    "id": "100",
                    "keyPhrases": ["key phrase 1", ..., "key phrase n"]
                },
            ]
        }

        // Languages response
        {
            "documents": [
                {
                    "id": "1",
                    "detectedLanguages": [
                        {
                            "name": "English",
                            "iso6391Name": "en",
                            "score": "1"
                        }
                    ]
                },
                ...
                {
                    "id": "100",
                    "detectedLanguages": [
                        {
                            "name": "French",
                            "iso6391Name": "fr",
                            "score": "0.985"
                        }
                    ]
                }
            ]
        }


## <a name="task-3---detect-topics-in-a-corpus-of-text"></a>Opgave 3 - registrere emner i en indekserede af tekst ####

Dette er et nyligt frigivne API hvilke returnerer øverst fundet emner for en liste over sendt tekstposter. Et emne, der er identificeret med et vigtige udtryk, som kan være en eller flere relaterede ord. API er udviklet til at fungere godt til kort, human skrevet tekst som gennemsyn og feedback fra brugere.

Denne API kræver **et minimum af 100 tekstposter** skal sendes, men er udviklet til at registrere emner på tværs af hundredvis til tusindvis af poster. Alle ikke-engelsk poster eller poster med mindre end 3 ord, bliver kasseret og kan derfor ikke tildeles til emner. Den maksimale størrelse på et enkelt dokument, der kan sendes er 30KB til emne registrering, og den samlede maksimale størrelse af sendte input er 30MB. Emne registrering er rente, der er begrænset til 5 indsendelser hver 5 minutter.

Der er to ekstra **valgfrit** inputparametre, der kan hjælpe med at forbedre kvaliteten af resultater:

- **Stop ord.**  Disse ord og deres Luk formularer (fx flertalsformer), der skal udelades fra hele emne registrering pipeline. Brug denne til almindelige ord (for eksempel, "problem", "fejl" og "brugere" kan være relevante valgmuligheder for kundeklager over software). De enkelte strenge skal være et enkelt ord.
- **Stoppe sætninger** - disse udtryk, der skal udelades fra listen over returnerede emner. Brug dette til at udelukke generisk emner, du ikke vil have vist i resultaterne. For eksempel ville "Microsoft" og "Azure" være relevante indstillinger for emner til at udelukke. Strenge kan indeholde flere ord.

Følg disse trin for at registrere emner i din tekst.

1. Formatere input i JSON. Denne gang, kan du definere stopord og standse udtryk.

        {
            "documents": [
                {
                    "id": "1",
                    "text": "First document"
                },
                ...
                {
                    "id": "100",
                    "text": "Final document"
                }
            ],
            "stopWords": [
                "issue", "error", "user"
            ],
            "stopPhrases": [
                "Microsoft", "Azure"
            ]
        }

1. Ved hjælp af de samme sidehoveder, som defineret i opgave 2, gøre et **INDLÆG** ringe til emner slutpunktet:

        POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/topics

1. Dette vil returnere en `operation-location` som overskrift i svaret, hvor værdien er URL-adressen til forespørgsel til de deraf følgende emner:

        'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

1. Forespørgsel den returnerede `operation-location` med jævne mellemrum med en anmodning om **få** . Én gang i minuttet anbefales.

        GET https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>

1. Slutpunktet vil returnere et svar, herunder `{"status": "notstarted"}` før behandling, `{"status": "running"}` under behandling og `{"status": "succeeded"}` med output én gang fuldført. Du kan derefter bruge output som i følgende format (note detaljer, som er blevet udeladt fejl format og datoer i dette eksempel):

        {
            "status": "succeeded",
            "operationProcessingResult": {
                "topics": [
                    {
                        "id": "8b89dd7e-de2b-4a48-94c0-8e7844265196"
                        "score": "5"
                        "keyPhrase": "first topic name"
                    },
                    ...
                    {
                        "id": "359ed9cb-f793-4168-9cde-cd63d24e0d6d"
                        "score": "3"
                        "keyPhrase": "final topic name"
                    }
                ],
                "topicAssignments": [
                    {
                        "topicId": "8b89dd7e-de2b-4a48-94c0-8e7844265196",
                        "documentId": "1",
                        "distance": "0.354"
                    },
                    ...
                    {
                        "topicId": "359ed9cb-f793-4168-9cde-cd63d24e0d6d",
                        "documentId": "55",
                        "distance": "0.758"
                    },            
                ]
            }
        }

Bemærk, at vellykket svaret til emner fra den `operations` slutpunkt har følgende skema:

    {
            "topics" : [{
                "id" : "string",
                "score" : "number",
                "keyPhrase" : "string"
            }],
            "topicAssignments" : [{
                "documentId" : "string",
                "topicId" : "string",
                "distance" : "number"
            }],
            "errors" : [{
                "id" : "string",
                "message" : "string"
            }]
        }

Forklaringer for hver del af dette svar er som følger:

**emner**

| Nøgle | Beskrivelse |
|:-----|:----|
| id | Et entydigt id for hvert emne. |
| resultat | Antallet af dokumenter, der er tildelt til emne. |
| keyPhrase | Et omsummering ord eller udtryk efter emnet. |

**topicAssignments**

| Nøgle | Beskrivelse |
|:-----|:----|
| documentId | Id for dokumentet. Passer til det ID, der er inkluderet i input. |
| topicId | Emne-ID, dokumentet er blevet tildelt til. |
| afstand | Dokument-emne tilknytning karakteren mellem 0 og 1. Lavere en afstanden resultat, jo stærkere emne tilknytningen er. |

**fejl**

| Nøgle | Beskrivelse |
|:-----|:----|
| id | Entydigt id for input dokument fejlen refererer til. |
| meddelelse | Fejlmeddelelse. |

## <a name="next-steps"></a>Næste trin ##

Tillykke! Du har nu fuldført ved hjælp af tekst analyser af dine data. Du kan nu ønske at undersøge ved hjælp af et værktøj som [Power BI](//powerbi.microsoft.com) til at visualisere dine data samt automatisere din indsigt for at give dig en realtid visning af dataene tekst.

For at se, hvordan tekst Analytics-funktioner, som synspunkt, kan bruges som en del af en bot skal du se [Følelsesmæssige Bot](http://docs.botframework.com/en-us/bot-intelligence/language/#example-emotional-bot) eksemplet på webstedet Bot Framework.
