<properties
   pageTitle="Logik apps indhold Skriv håndtering af | Microsoft Azure"
   description="Forstå, hvordan logik Apps omhandler indholdstyper på design og runtime"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="logic-apps-content-type-handling"></a>Logik Apps indholdstype håndtering af

Der er mange forskellige typer indhold, der kan passerer gennem en logik App – herunder JSON, XML, flade filer og binære data.  Men alle indholdstyper, der understøttes, nogle oprindeligt er forstået af logik Apps programmet, og andre kan kræve konvertering eller konverteringer efter behov.  I følgende artikel beskriver, hvordan programmet håndterer forskellige indholdstyper og hvordan de kan korrekt håndteres efter behov.

## <a name="content-type-header"></a>Indholdstype sidehoved

Hvis du vil starte enkel, Lad os se på to `Content-Types` , der ikke kræver nogen konvertering eller konvertering til brug i en logik App - `application/json` og `text/plain`.

### <a name="applicationjson"></a>Programmet/json

Arbejdsproces-program, der er afhængig af den `Content-Type` sidehoved fra HTTP ringer for at bestemme håndteringen relevante.  En anmodning til indholdstypen `application/json` bliver gemt og behandles som en JSON-objekt.  Desuden kan JSON indhold parses som standard uden at bruge en hvilken som helst konvertering.  Så en anmodning med overskriften indholdstype `application/json ` sådan ud:

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

kunne parses i en arbejdsproces med et udtryk som `@body('myAction')['foo'][0]` at få en værdi (i dette tilfælde `bar`).  Ingen yderligere konvertering er nødvendig.  Hvis du arbejder med data, der er JSON, men ikke har et sidehoved, der er angivet, kan du manuelt konverteret den til JSON ved hjælp af den `@json()` funktionen (for eksempel: `@json(triggerBody())['foo']`).

### <a name="textplain"></a>Almindelig tekst

Ligner `application/json`, HTTP-meddelelser med den `Content-Type` overskriften for `text/plain` skal gemmes i det er rå form.  Desuden, hvis en efterfølgende handlinger uden en hvilken som helst konvertering anmodning går med et `Content-Type`: `text/plain` sidehoved.  Eksempelvis hvis arbejder med en flad fil modtager du muligvis følgende HTTP indhold:

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

as `text/plain`.  Hvis i den næste handling du sende dem som brødteksten i en anden anmodning (`@body('flatfile')`), anmodningen ville have en `text/plain` indholdstype sidehoved.  Hvis du arbejder med data, der er almindelig tekst, men ikke har et sidehoved, der er angivet, kan du manuelt konverteret det til tekst ved hjælp af den `@string()` funktionen (for eksempel: `@string(triggerBody())`)

### <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Programmet/XML- og program-/ oktetstreamen og konverteringsprogram funktioner

Logik App program bevarer altid den `Content-Type` , der blev modtaget på http-anmodning eller svar.  Hvad betyder, at hvis modtages indhold med `Content-Type` af `application/octet-stream`, herunder, i en efterfølgende handling med ingen konvertering medfører en udgående anmodning med `Content-Type`: `application/octet-stream`.  På denne måde programmet kan guaruntee data går ikke tabt som teksten bevæger sig i hele arbejdsprocessen.  Dog gemmes handlingstilstand (input og output) i et JSON-objekt, som teksten flyder i hele arbejdsprocessen.  Det betyder, at hvis du vil bevare nogle datatyper, programmet konverterer indholdet til en binær base64-kodet streng med relevante metadata, der bevarer begge `$content` og `$content-type` -, vil automatisk blive konverteret.  Du kan også manuelt konvertere mellem typer indhold ved hjælp af indbygget i konverteringsprogram funktioner:

* `@json()`-Konverterer data til`application/json`
* `@xml()`-Konverterer data til`application/xml`
* `@binary()`-Konverterer data til`application/octet-stream`
* `@string()`-Konverterer data til`text/plain`
* `@base64()`-Konverterer indhold til en base64-streng
* `@base64toString()`-konverterer en base64-kodet streng til`text/plain`
* `@base64toBinary()`-konverterer en base64-kodet streng til`application/octet-stream`
* `@encodeDataUri()`-koder en streng som en dataUri bytematrix
* `@decodeDataUri()`-afkoder en dataUri til en bytematrix

Hvis du har modtaget en HTTP-anmodning med eksempelvis `Content-Type`: `application/xml` af:

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

Jeg kan konverteres og bruge senere med noget i retning af `@xml(triggerBody())`, eller i en funktion som `@xpath(xml(triggerBody()), '/CustomerName')`.

### <a name="other-content-types"></a>Andre indholdstyper

Andre indholdstyper, der understøttes og fungerer sammen med en logik App, men det kræver manuelt hente meddelelsesteksten ved kodning på `$content`.  For eksempel, hvis jeg udløst fra en `application/x-www-url-formencoded` anmodningen, så ud som følgende:

```
CustomerName=Frank&Address=123+Avenue
```

Da denne ikke almindelig tekst eller en JSON gemmes i handlingen som:

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

Hvor `$content` er de data, kodet som en base64 streng til at bevare alle data.  Der findes i øjeblikket ikke en oprindelig funktion til formular-data, kan jeg stadig bruge disse data i en arbejdsproces ved manuelt at få adgang til data med en funktion som `@string(body('formdataAction'))`.  Hvis jeg ville min udgående anmodning også have den `application/x-www-url-formencoded` indholdstype sidehoved, jeg kan kun føje den til selve handling uden en hvilken som helst konvertering som `@body('formdataAction')`.  Men det fungerer kun hvis brødteksten er den eneste parameter i den `body` input.  Hvis du forsøger at gøre `@body('formdataAction')` inden for en `application/json` Anmod om får du en kørselsfejl som den sender kodet brødteksten.
