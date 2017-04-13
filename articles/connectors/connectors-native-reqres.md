<properties
    pageTitle="Bruge og svar handlinger | Microsoft Azure"
    description="Oversigt over de og svar udløser og handling i en Azure logik-app"
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-request-and-response-components"></a>Introduktion til komponenterne til og svar

Med og svar komponenterne i et logik app, kan du besvare i realtid begivenheder.

Det kan du f.eks.:

- Besvare en HTTP-anmodning med data fra en lokal database via en logik app.
- Udløse en logik app fra en ekstern webhook begivenhed.
- Ringe til en logik app med handlingen og svar fra i en anden logik app.

For at komme i gang ved hjælp af handlingerne og svar i en logik app skal du se [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-request-trigger"></a>Brug HTTP-anmodning udløser

En udløser er en begivenhed, der kan bruges til at starte arbejdsprocessen, der er defineret i en logik app. [Få mere at vide om udløsere](connectors-overview.md).

Her er et eksempel sekvens af, hvordan du konfigurerer en HTTP-anmodning i logik App Designer.

1. Tilføj udløseren, **der modtages anmodning - når en HTTP-anmodning** i din logik app. Du kan eventuelt angive et JSON-skema (ved hjælp af et værktøj som [JSONSchema.net](http://jsonschema.net)) til anmodningsteksten. Dette giver mulighed for designer til at generere tokens for egenskaber i HTTP-anmodningen.
2. Tilføje endnu en handling, så du kan gemme appen logik.
3. Når du har gemt logik app, kan du få HTTP URL-anmodningen fra kortet anmodning.
4. En HTTP-POST (du kan bruge et værktøj som [Postman](https://www.getpostman.com/)) til URL-adressen udløser logik app.

>[AZURE.NOTE] Hvis du ikke angiver en indsats, en `202 ACCEPTED` svar returneres straks til kalderen. Du kan bruge handlingen svar til at tilpasse et svar.

![Svar udløser](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Bruge handlingen HTTP-svar

Handlingen HTTP-svar gælder kun, når du bruger den i en arbejdsproces, der udløses af en HTTP-anmodning. Hvis du ikke angiver en indsats, en `202 ACCEPTED` svar returneres straks til kalderen.  Du kan tilføje handlingen svar på en hvilken som helst trin i arbejdsprocessen. Appen logik kun holder den indgående anmodning åben i et minut for et svar.  Når du har et minut, hvis intet svar blev sendt fra arbejdsprocessen (og et svar handling findes i definitionen), en `504 GATEWAY TIMEOUT` returneres til kalderen.

Her er, hvordan du tilføjer en HTTP-svar handling:

1. Vælg knappen **Nyt trin** .
2. Vælg **Tilføj en handling**.
3. Skriv **svar** for at få vist handlingen svar i søgefeltet handling.

    ![Vælg handlingen, svar](./media/connectors-native-reqres/using-action-1.png)

4. Tilføj eventuelle parametre, der kræves til svarmeddelelse HTTP.

    ![Udføre handlingen svar](./media/connectors-native-reqres/using-action-2.png)

5. Klik på det øverste venstre hjørne af værktøjslinjen for at gemme, og din logik app kan både gemme og publicere (aktivere).

## <a name="request-trigger"></a>Anmode om udløser

Her er nogle oplysninger for den udløser, der understøtter denne forbindelse. Der findes en enkelt anmodning udløser.

|Udløser|Beskrivelse|
|---|---|
|Anmode om|Sker, når der modtages en HTTP-anmodning|

## <a name="response-action"></a>Svar handling

Her er nogle oplysninger til den handling, der understøtter denne forbindelse. Der findes en enkelt svar handling, der kan kun bruges, når den leveres med en anmodning om udløser.

|Handling|Beskrivelse|
|---|---|
|Svar|Returnerer et svar til den forbundne HTTP-anmodning|

### <a name="trigger-and-action-details"></a>Oplysninger om udløser og handling

I følgende tabel beskrives de input felter for udløser og handling, og den tilhørende Medtag detaljer.

#### <a name="request-trigger"></a>Anmode om udløser
Følgende er en input felt til udløser fra en indgående HTTP-anmodning.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|JSON skema|skema|HTTP-anmodningsteksten JSON skema|
<br>

**Output detaljer**

Følgende er output oplysninger om anmodningen.

|Egenskabsnavn|Datatype|Beskrivelse|
|---|---|---|
|Sidehoveder|objekt|Anmode om sidehoveder|
|Brødteksten|objekt|Anmode om objekt|

#### <a name="response-action"></a>Svar handling

Følgende er input felter for handlingen HTTP-svar. A * betyder, at det er et obligatorisk felt.

|Vist navn|Egenskabsnavn|Beskrivelse|
|---|---|---|
|Status kode *|Statuskode|HTTP-statuskode|
|Sidehoveder|sidehoveder|Et JSON-objekt af enhver svar sidehoveder medtage|
|Brødteksten|brødteksten|Svar brødteksten|

## <a name="next-steps"></a>Næste trin

Prøv nu den platform og [oprette en logik app](../app-service-logic/app-service-logic-create-a-logic-app.md). Du kan udforske andre tilgængelige forbindelserne i logik apps ved at kigge på vores [API'er liste](apis-list.md).
