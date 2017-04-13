<properties
    pageTitle="Opgradere til Version 2 af tekst analyser API | Microsoft Azure"
    description="Azure Machine Learning tekst Analytics - opgradering til Version 2"
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

# <a name="upgrading-to-version-2-of-the-text-analytics-api"></a>Opgradere til Version 2 af tekst analyser API #

Denne vejledning, fører dig gennem processen med at opgradere din kode fra ved hjælp af den [første version af API](../machine-learning/machine-learning-apps-text-analytics.md) til at bruge den anden version. 

Hvis du ikke har brugt API og gerne vil have mere at vide, kan du **[vide mere om API her](//go.microsoft.com/fwlink/?LinkID=759711)** , eller **[Følg guiden Hurtig Start](//go.microsoft.com/fwlink/?LinkID=760860)**. For tekniske reference referere til **[API Definition](//go.microsoft.com/fwlink/?LinkID=759346)**.

### <a name="part-1-get-a-new-key"></a>Del 1. Få en ny nøgle ###

Først skal du få en ny API-nøgle fra **Azure-portalen**:

1. Gå til tekst Analytics-tjenesten gennem [Cortana Intelligence galleriet](//gallery.cortanaintelligence.com/MachineLearningAPI/Text-Analytics-2). Her kan du også finde links til de dokumentation og alle de kodeeksempler.

1. Klik på **Tilmeld dig**. Dette link kommer du til Azure management-portalen, hvor du kan tilmelde dig tjenesten.

1. Vælg en plan. Du kan vælge det **gratis niveau for 5.000 transaktioner/måned**. Som det er en gratis plan, skal du ikke betale for ved hjælp af tjenesten. Du skal logge på abonnementet Azure. 

1. Når du tilmelder dig tekst Analytics, får du en **API-nøgle**. Kopiér denne tast, som du skal bruge den, når du bruger API-tjenester.

### <a name="part-2-update-the-headers"></a>Del 2. Opdatere overskrifterne ###

Opdater sendte sidehoved værdierne, som vist nedenfor. Bemærk, at kontonøgle kodes ikke længere.

**Version 1**

    Authorization: Basic base64encode(<your Data Market account key>)
    Accept: application/json

**Version 2**

    Content-Type: application/json
    Accept: application/json
    Ocp-Apim-Subscription-Key: <your Azure Portal account key>


### <a name="part-3-update-the-base-url"></a>Del 3. Opdatere den grundlæggende URL-adresse ###

**Version 1**

    https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/

**Version 2**

    https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/

### <a name="part-4a-update-the-formats-for-sentiment-key-phrases-and-languages"></a>Del 4a. Opdatere formater for synspunkt, vigtige sætninger og sprog ###

#### <a name="endpoints"></a>Slutpunkter ####

FÅ slutpunkter er nu udeladt, så alle input skal sendes som en POST-anmodning. Opdater slutpunkterne til dem, der er vist nedenfor.

| |Version 1 enkelt slutpunkt|Version 1 batchen slutpunkt|Version 2 slutpunkt|
|---|---|---|---|
|Ringe til type|HENT|INDLÆG|INDLÆG|
|Synspunkt|```GetSentiment```|```GetSentimentBatch```|```sentiment```|
|Vigtige udtryk|```GetKeyPhrases```|```GetKeyPhrasesBatch```|```keyPhrases```|
|Sprog|```GetLanguage```|```GetLanguageBatch```|```languages```|

#### <a name="input-formats"></a>Formater til input ####

Bemærk kun INDLÆG formatet besvares nu, så du skal omformatere alt input som tidligere har brugt enkelt dokument slutpunkterne i overensstemmelse hermed. Input er ikke store og små bogstaver.

**Version 1 (batchen)**

    {
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="output-from-sentiment"></a>Output fra synspunkt ####

**Version 1**

    {
      "SentimentBatch":[{
        "Id":"string",
        "Score":"double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "score":"double"
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-key-phrases"></a>Output fra vigtige udtryk ####

**Version 1**

    {
      "KeyPhrasesBatch":[{
        "Id":"string",
        "KeyPhrases":["string"]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "keyPhrases":["string"]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }

#### <a name="output-from-languages"></a>Output fra sprog ####


**Version 1**

    {
      "LanguageBatch":[{
        "id":"string",
        "detectedLanguages": [{
          "Score":"double"
          "Name":"string",
          "Iso6391Name":"string"
        }]
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2**

    {
      "documents":[{
        "id":"string",
        "detectedLanguages": [{
          "score":"double"
          "name":"string",
          "iso6391Name":"string"
        }]
      }],
      "errors" : [{
        "id":"string",
        "message":"string"
      }]
    }


### <a name="part-4b-update-the-formats-for-topics"></a>Del 4b. Opdatere formater for emner ###

#### <a name="endpoints"></a>Slutpunkter ####

| |Version 1 slutpunkt | Version 2 slutpunkt|
|---|---|---|
|Sende til emne registrering (INDLÆG)|```StartTopicDetection```|```topics```|
|Hent emne resultater (Hent)|```GetTopicDetectionResult?JobId=<jobId>```|```operations/<operationId>```|

#### <a name="input-formats"></a>Formater til input ####

**Version 1**

    {
      "StopWords": [
        "string"
      ],
      "StopPhrases": [
        "string"
      ], 
      "Inputs": [
        {
          "Id": "string",
          "Text": "string"
        }
      ]
    }

**Version 2**

    {
      "stopWords": [
        "string"
      ],
      "stopPhrases": [
        "string"
      ],
      "documents": [
        {
          "id": "string",
          "text": "string"
        }
      ]
    }

#### <a name="submission-results"></a>Resultater for afsendelse ####

**Version 1 (INDLÆG)**

Tidligere, når jobbet afsluttet, modtager du følgende JSON output, og hvor job-id vil være føjet til en URL-adresse til at hente output.

    {
        "odata.metadata":"<url>",
        "JobId":"<JobId>"
    }

**Version 2 (INDLÆG)**

Svaret nu indeholder værdien sidehoved, hvor `operation-location` bruges som slutpunktet undersøges, om resultaterne:

    'operation-location': 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/operations/<operationId>'

#### <a name="operation-results"></a>Handlingen resultater ####

**Version 1 (Hent)**

    {
      "TopicInfo" : [{
        "TopicId" : "string"
        "Score" : "double"
        "KeyPhrase" : "string"
      }],
      "TopicAssignment" : [{
        "Id" : "string",
        "TopicId" : "string",
        "Distance" : "double"
      }],
      "Errors" : [{
        "Id":"string",
        "Message":"string"
      }]
    }

**Version 2 (Hent)**

Som tidligere **med jævne mellemrum afstemning output** (den foreslåede periode er hvert minut) indtil output returneres. 

Når emner API er færdig, status læse `succeeded` returneres. Dette vil så indeholde outputresultaterne i det format, der er vist nedenfor:

    {
        "status": "succeeded",
        "createdDateTime": "string",
        "operationType": "topics",
        "processingResult": {
            "topics" : [{
            "id" : "string"
            "score" : "double"
            "keyPhrase" : "string"
          }],
          "topicAssignments" : [{
            "topicId" : "string",
            "documentId" : "string",
            "distance" : "double"
          }],
          "errors" : [{
              "id":"string",
              "message":"string"
          }]
        }
    }

### <a name="part-5-test-it"></a>Del 5. Test den! ###

Du skulle nu være klar! Teste din kode med en lille prøve at sikre, at du korrekt kan behandle dine data.
