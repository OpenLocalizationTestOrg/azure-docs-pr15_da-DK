<properties
    pageTitle="Machine Learning API'er: Tekst Analytics | Microsoft Azure"
    description="Microsofts Machine Learning tekst Analytics API'er kan bruges til at analysere ustrukturerede tekst til synspunkt analyse, udtrækning af vigtige udtryk, registrering af sprog og emne registrering."
    services="machine-learning"
    documentationCenter=""
    authors="onewth"
    manager="jhubbard"
    editor="cgronlun"/> 

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="onewth"/>


# <a name="machine-learning-apis-text-analytics-for-sentiment-key-phrase-extraction-language-detection-and-topic-detection"></a>Machine Learning API'er: Tekst analyser af synspunkt, centrale udtrækning af udtryk, registrering af sprog og emne registrering

>[AZURE.NOTE] Denne vejledning er beregnet til version 1 af API. For version 2, [**refererer til dette dokument**](../cognitive-services/cognitive-services-text-analytics-quick-start.md). Version 2 er nu den foretrukne version af denne API.

## <a name="overview"></a>Oversigt

Tekst Analytics API er en pakke med tekst analytics [webtjenester](https://datamarket.azure.com/dataset/amla/text-analytics) oprettet med Azure Machine Learning. API kan bruges til at analysere ustrukturerede tekst til opgaver som synspunkt analyse, udtrækning af vigtige udtryk, registrering af sprog og emne registrering. Ingen kursus data skal bruges til at bruge denne API: kun få dine tekstdata over. Denne API bruger avanceret naturligt sprog behandling teknikker til at levere bedste i klassen prognoser.

Du kan se tekst analytics i aktion på vores [demowebsted](https://text-analytics-demo.azurewebsites.net/), hvor du også kan finde [eksempler](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) om, hvordan du implementerer tekst analytics i C# og Python.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

---

## <a name="sentiment-analysis"></a>Synspunkt analyse

API returnerer et numerisk resultat mellem 0 og 1. Pointtal, der er tæt på 1 angiver positive synspunkt, mens pointtal, der er tæt på 0 angiver negative synspunkt. Synspunkt resultat er oprettet ved hjælp af klassifikation teknikker. Funktionerne input til klassificeringen omfatter n-g, funktioner, der er dannet ud fra af ordklasse mærker og integrerede objekter word. Engelsk er i øjeblikket er det kun understøttede sprog.
 
## <a name="key-phrase-extraction"></a>Udtrækning af vigtige udtryk

API returnerer en liste over strenge, der angiver de vigtige talepunkter i feltet Indtast tekst. Vi anvende teknikker fra Microsoft Office avanceret behandling af naturligt sprog værktøjskassen. Engelsk er i øjeblikket er det kun understøttede sprog.

## <a name="language-detection"></a>Registrering af sprog

API returnerer fundet sprog og et numerisk resultat mellem 0 og 1. Pointtal, der er tæt på 1 angiver 100% sikkerhed, det identificerede sprog er sand. Sammenlagt 120 sprog understøttes.

## <a name="topic-detection"></a>Emne registrering

Dette er et nyligt frigivne API hvilke returnerer øverst fundet emner for en liste over sendt tekstposter. Et emne, der er identificeret med et vigtige udtryk, som kan være en eller flere relaterede ord. Denne API kræver et minimum af 100 tekstposter skal indsendes, men er udviklet til at registrere emner på tværs af hundredvis til tusindvis af poster. Bemærk, at denne API gebyrer 1 transaktion per tekst post, der er sendt. API er udviklet til at fungere godt til kort, human skrevet tekst som gennemsyn og feedback fra brugere.

---

## <a name="api-definition"></a>Definition af API

### <a name="headers"></a>Sidehoveder

Sørg for at medtage de korrekte sidehoveder i mødeindkaldelsen, som skal være på følgende måde:

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Du kan finde din kontonøgle fra din konto på [Azure Data marked](https://datamarket.azure.com/account/keys). Bemærk, at i øjeblikket kun JSON accepteres for input- og klokkeslætsformater. XML understøttes ikke.

---

## <a name="single-response-apis"></a>Enkelt svar API'er

### <a name="getsentiment"></a>GetSentiment

**URL-ADRESSE** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Eksempel anmodning**

I opkaldet nedenfor, vi anmoder om synspunkt analyse for ordet "Hej verden":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Dette vil returnere et svar på følgende måde:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
        "Score":1.0
    }

---

### <a name="getkeyphrases"></a>GetKeyPhrases

**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Eksempel anmodning**

I nedenstående opkaldet, skal vi anmoder om de vigtigste sætninger, der findes i teksten "Det blev en fantastisk hotel til at holde på, med entydige forskønnelse og venlig personale":

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
    Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Dette vil returnere et svar på følgende måde:

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
      "KeyPhrases":[
        "wonderful hotel",
        "unique decor",
        "friendly staff"
      ]
    }
 
---

### <a name="getlanguage"></a>GetLanguage

**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Eksempel anmodning**

I nedenstående få opkaldet, vi anmoder om for synspunkt efter de vigtige udtryk i feltet tekst *Hej verden*

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
    Text=Hello+World

Dette vil returnere et svar på følgende måde:

    {
      "UnknownLanguage": false,
      "DetectedLanguages": [{
        "Name": "English",
        "Iso6391Name": "en",
        "Score": 1.0
      }]
    }

**Valgfrie parametre**

`NumberOfLanguagesToDetect`er en valgfri parameter. Standardværdien er 1.

---

## <a name="batch-apis"></a>Batchen API'er

Tjenesten tekst Analytics kan du gøre synspunkt og nøgle-sætning ekstraktioner i batch-tilstand. Bemærk, at hver af posterne, der karakter tæller som én transaktion. Som et eksempel, hvis du anmoder om synspunkt efter 1000 poster i et enkelt opkald trækkes 1000 transaktioner.

Bemærk, at de id'er, der er angivet i systemet de id'er, der returneres af systemet. Webtjenesten kontrollerer ikke, at disse-id'er er entydige. Det er ansvarlig for opkalds at bekræfte at er entydige. 


### <a name="getsentimentbatch"></a>GetSentimentBatch

**URL-ADRESSE** 

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Eksempel anmodning**

I INDLÆG opkaldet nedenfor, vi anmoder om for sentiments af udtryk "Hej verden", "Hej Foo verden" og "Hej mine verden" i brødteksten i anmodningen:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Anmodningsteksten:

    {"Inputs":
    [
        {"Id":"1","Text":"hello world"},
        {"Id":"2","Text":"hello foo world"},
        {"Id":"3","Text":"hello my world"},
    ]}

Du kan få på listen over resultater, der er knyttet til din tekst id'er i svaret nedenfor:

    {
      "odata.metadata":"<url>", 
      "SentimentBatch":
      [
        {"Score":0.9549767,"Id":"1"},
        {"Score":0.7767222,"Id":"2"},
        {"Score":0.8988889,"Id":"3"}
      ],  
      "Errors":[]
    }


---

### <a name="getkeyphrasesbatch"></a>GetKeyPhrasesBatch

**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Eksempel anmodning**

I dette eksempel skal vi anmoder om en liste over sentiments for de vigtigste sætninger i følgende tekster: 

* "Det blev en fantastisk hotel til at holde på, med entydige forskønnelse og venlig personale"
* "Det blev en imponerende build konference med meget interessant taler"
* "Trafikken blev slemme, jeg har brugt tre timer, gå til airport"

Denne anmodning foretages som et INDLÆG opkald til slutpunktet:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Anmodningsteksten:

    {"Inputs":
    [
        {"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
        {"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
        {"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
    ]}

Du kan få på listen over vigtige udtryk, der er knyttet til din tekst id'er i svaret nedenfor:

    { "odata.metadata":"<url>",
        "KeyPhrasesBatch":
        [
           {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
           {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
           {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
        ],
        "Errors":[]
    }

---

### GetLanguageBatch

In the POST call below, we are requesting language detection for two text inputs:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Request body:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

This returns the following response, where English is detected in the first input and French in the second input:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "engelsk",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "fransk",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## <a name="topic-detection-apis"></a>Emne registrering API'er

Dette er et nyligt frigivne API hvilke returnerer øverst fundet emner for en liste over sendt tekstposter. Et emne, der er identificeret med et vigtige udtryk, som kan være en eller flere relaterede ord. Bemærk, at denne API gebyrer 1 transaktion per tekst post, der er sendt.

Denne API kræver et minimum af 100 tekstposter skal indsendes, men er udviklet til at registrere emner på tværs af hundredvis til tusindvis af poster.


### <a name="topics--submit-job"></a>Emner – Send job

**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Eksempel anmodning**


I nedenstående INDLÆG opkaldet, vi anmoder om emner for et sæt af 100 artikler, hvor de første og sidste input artikler vises, og to StopPhrases er inkluderet.

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Anmodningsteksten:

    {"Inputs":[
        {"Id":"1","Text":"I loved the food at this restaurant"},
        ...,
        {"Id":"100","Text":"I hated the decor"}
    ],
    "StopPhrases":[
        "restaurant", “visitor"
    ]}

I nedenstående svaret får du job-id for det afsendte job:

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

En liste over enkelt ord eller flere word-sætninger, der ikke skal returneres som emner. Kan bruges til at filtrere meget generisk emner. For eksempel i et datasæt om hotel anmeldelser "hotel" og "hostel" kan være fornuftig stop udtryk.  

### <a name="topics--poll-for-job-results"></a>Emner – afstemningen, så tingene resultater

**URL-ADRESSE**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Eksempel anmodning**

Sende den job-id returneres fra trinnet 'Send job' for at hente resultaterne. Vi anbefaler, at du ringer dette slutpunkt hvert minut indtil Status = 'Fuldført' i svaret. Det tager omkring 10 minutter for en sag fuldført eller længere sager med mange tusindvis af poster.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Mens det behandler, bliver svaret på følgende måde:

    {
        "odata.metadata":"<url>",
        "Status":"Running",
        "TopicInfo":[],
        "TopicAssignment":[],
        "Errors":[]
    }


API returnerer output i JSON-format i følgende format:

    {
        "odata.metadata":"<url>",
        "Status":"Finished",
        "TopicInfo":[
        {
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Score":8.0,
            "KeyPhrase":"food"
        },
        ...
        {
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Score":6.0,
            "KeyPhrase":"decor"
            }
        ],
        "TopicAssignment":[
        {
            "Id":"1",
            "TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
            "Distance":0.7809
        },
        ...
        {
            "Id":"100",
            "TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
            "Distance":0.8034
        }
        ],
        "Errors":[]


Egenskaber for hver del af svaret er som følger:

**TopicInfo egenskaber**

| Nøgle | Beskrivelse |
|:-----|:----|
| TopicId | Et entydigt id for hvert emne. |
| Resultat | Antallet af poster, der er tildelt til emne. |
| KeyPhrase | Et omsummering ord eller udtryk efter emnet. 1 eller flere ord kan være. |

**TopicAssignment egenskaber**

| Nøgle | Beskrivelse |
|:-----|:----|
| Id | Id for posten. Passer til det ID, der er inkluderet i input. |
| TopicId | Det emne-ID som posten, der er tildelt til. |
| Afstand | KONFIDENSINTERVAL, som posten tilhører emnet. Afstanden tættere til nul angiver højere konfidensinterval. |
