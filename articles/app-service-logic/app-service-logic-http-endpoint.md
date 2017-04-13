<properties
   pageTitle="Logik apps som kaldes slutpunkter"
   description="Hvordan du kan oprette og konfigurere udløse slutpunkter og bruge dem i en logik app i Azure App Service"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>


# <a name="logic-apps-as-callable-endpoints"></a>Logik apps som kaldes slutpunkter

Logik Apps kan oprindeligt fremvise et synkron HTTP slutpunkt som en udløser.  Du kan også bruge anvendelse af kan kaldes slutpunkter til at aktivere logik Apps som en indlejret arbejdsproces gennem "arbejdsproceshandlingen" i en logik App.

Der er 3 typer udløsere, der kan modtage anmodninger om:

* Anmode om
* ApiConnectionWebhook
* HttpWebhook

Vi bruger **anmodning** som eksemplet for resten af artiklen, men alle principperne gælder identisk for de andre 2 typer udløsere.

## <a name="adding-a-trigger-to-your-definition"></a>Tilføje en udløser til definitionen for din
Det første trin er at føje en udløser til definitionen for din logik app, der kan modtage indgående anmodninger.  Du kan søge i designvisningen for "HTTP anmodning" at føje udløser kortet. Du kan definere en anmodningsteksten JSON skemaet og designer genererer tokens for at hjælpe dig med at analysere og overføre data fra den manuelt udløse gennem arbejdsprocessen.  Jeg anbefaler at bruge et værktøj som [jsonschema.net](http://jsonschema.net) til at oprette et JSON-skema fra et eksempel brødtekst data.

![Anmode om udløser kort][2]

Når du gemmer din App logik definition, genereres en tilbagekald URL-adressen ligner denne:
 
``` text
https://prod-03.eastus.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?*signature*...
```

Denne URL-adresse indeholder en SAS nøgle i parametrene til forespørgslen, som bruges til godkendelse.

Du kan også finde dette slutpunkt i portalen Azure:

![][1]

Eller ved at ringe til:

``` text
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

### <a name="security-for-the-trigger-url"></a>Sikkerhed i forbindelse med udløser URL-adressen

Logik App tilbagekald URL-adresser er genereret sikkert ved hjælp af en delt Access-signatur.  Signaturen er passeret som en parameter i forespørgslen, og skal godkendes, før logik app der tager et billede.  Det er oprettet via en entydig kombination af en hemmeligt nøgle per logik app, udløsernavn og den handling, der udføres.  Medmindre en bruger har adgang til tasten hemmeligt logik app, vil de ikke kunne oprette en gyldig signatur.

## <a name="calling-the-logic-app-triggers-endpoint"></a>Ringe til logik app udløser slutpunkt

Når du har oprettet slutpunktet for din udløser, du kan udløse det via en `POST` til den fulde URL-adresse. Du kan medtage flere sidehoveder og alt indhold i meddelelsens brødtekst.

Hvis indholdstypen er `application/json` derefter du vil kunne reference egenskaber fra i indkaldelsen. Det, ellers skal behandles som en enkelt binære enhed, der kan sendes til andre API'er, men kan ikke refereres til i arbejdsprocessen uden at konvertere indholdet.  Hvis du overfører eksempelvis `application/xml` indhold du kunne bruge `@xpath()` til at gøre en xpath udtrækning af eller `@json()` konvertere fra XML til JSON.  Du kan finde flere oplysninger om at arbejde med indhold filtyper [kan ikke findes her](app-service-logic-content-type.md)

Desuden kan du angive et JSON-skema i definitionen. Derved designer til at generere tokens, som du kan derefter overføres til trin.  For eksempel følgende får en `title` og `name` token, der er tilgængelige i designer:

```
{
    "properties":{
        "title": {
            "type": "string"
        },
        "name": {
            "type": "string"
        }
    },
    "required": [
        "title",
        "name"
    ],
    "type": "object"
}
```

## <a name="referencing-the-content-of-the-incoming-request"></a>Henvisninger til indholdet af den indgående anmodning

Den `@triggerOutputs()` funktionen output vil blive sendt indholdet af den indgående anmodning. For eksempel vil det se ud:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Du kan bruge den `@triggerBody()` genvej til at få adgang til den `body` egenskaben specifikt. 

## <a name="responding-to-the-request"></a>Besvare anmodningen

For nogle anmodninger, der starter en logik-app, kan du vil besvare med noget af indholdet kalderen. Der er en ny handlingstype kaldet **svar** , der kan bruges til at oprette statuskode, brødtekst og overskrifter til dit svar. Bemærk, at hvis der findes ingen **svar** figur, logik app slutpunktet kan *med det samme* svare med **202 accepteret**.

![HTTP-svar handling][3]

``` json
"Response": {
            "conditions": [],
            "inputs": {
                "body": {
                    "name": "@{triggerBody()['name']}",
                    "title": "@{triggerBody()['title']}"
                },
                "headers": {
                    "content-type": "application/json"
                },
                "statusCode": 200
            },
            "type": "Response"
        }
```

Svar har følgende:

| Egenskaben | Beskrivelse |
| -------- | ----------- |
| Statuskode | HTTP-statuskode til at besvare indgående anmodningen. Det kan være en hvilken som helst gyldigt statuskode, der starter med 2xx, 4xx eller 5xx. 3xx status-koder er ikke tilladt. | 
| brødteksten | Et objekt i brødteksten, der kan være en tekststreng, et JSON objekt eller endda binære indhold, der refereres til fra et tidligere trin. | 
| sidehoveder | Du kan definere et vilkårligt antal sidehoveder skal medtages i svaret | 

Alle trin i appen logik, der kræves til svaret skal fuldføres inden for *60 sekunder* , før den oprindelige forespørgsel til at modtage svar **medmindre arbejdsprocessen hedder som en indlejret logik App**. Hvis intet svar er nået i 60 sekunder og derefter den indgående anmodning afbrydes og modtage svar **408 klient timeout** HTTP.  Indlejrede logik Apps, skal den overordnede logik App fortsætter med at vente på et svar, indtil færdig, uanset hvor lang tid tager det.

## <a name="advanced-endpoint-configuration"></a>Konfiguration af avanceret slutpunkt

Logik apps har indbygget understøttelse af slutpunktet direkte adgang og Brug altid den `POST` metode til at starte en kørsel af appen logik. **Http-lytteren** API app tidligere understøtter også ændre URL-adressen segmenter og metoden HTTP. Du kan også konfigurere ekstra sikkerhed eller et brugerdefineret domæne ved at føje den til værten for API app (den Web app, hostet API-app). 

Denne funktionalitet er tilgængelige via **API administration**:
* [Ændre metoden for din anmodning](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod)
* [Ændre URL-adressen segmenter for din anmodning](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL)
* Konfigurere din API administration af domæner under fanen **Konfigurer** i portalen klassisk Azure
* Konfigurere politik for at kontrollere, om grundlæggende godkendelse (**link det er nødvendigt**)

## <a name="summary-of-migration-from-2014-12-01-preview"></a>Oversigt over overførsel fra 2014-12-01-eksempel

|  2014-12-01-eksempel | 2016-06-01 |
|---------------------|--------------------|
| Klik på **Http-lytteren** API app | Klik på **Manuel udløser** (ingen API app påkrævet) |
| HTTP-lytteren at "*sender svar automatisk*" | Enten medtage en **svar** handling eller ikke i Arbejdsprocesdefinitionen |
| Konfigurere basic eller OAuth-godkendelse | via API administration |
| Konfigurere HTTP metode | via API administration |
| Konfigurere relativ sti | via API administration |
| Henvise til indgående brødteksten via`@triggerOutputs().body.Content` | Reference via`@triggerOutputs().body` |
| **Sende HTTP-svar** handling på http-lytteren | Klik på **svar på http-anmodning** (ingen API app påkrævet)


[1]: ./media/app-service-logic-http-endpoint/manualtriggerurl.png
[2]: ./media/app-service-logic-http-endpoint/manualtrigger.png
[3]: ./media/app-service-logic-http-endpoint/response.png
