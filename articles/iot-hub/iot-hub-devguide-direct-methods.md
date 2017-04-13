<properties
 pageTitle="Udvikler guide – direkte metoder | Microsoft Azure"
 description="Azure IoT Hub udvikler guide – brug direkte metoder til at kalde kode på dine enheder"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Aktivere en direkte metode på en enhed (preview)

## <a name="overview"></a>Oversigt

IoT Hub giver dig mulighed for at starte metoder på enheder fra skyen. Metoder repræsenterer en anmodning om autosvar interaktion med en enhed, der ligner en HTTP-opkaldet, fordi de lykkes eller mislykkes umiddelbart (efter en brugerdefineret timeout) til oplyser brugeren om status for opkaldet. Dette er nyttigt for scenarier, hvor kurset for øjeblikkelig handling er forskellige, afhængigt af om enheden kunne svare, som sender en opstart af typen SMS til en enhed, hvis en enhed er offline (SMS der mere omfattende end et metode opkald).

Du kan betragte en metode som remote procedure call direkte på enheden. Kun metoder, der er implementeret på en enhed kan kaldes fra skyen. Hvis skyen forsøger at aktivere en metode på en enhed, som ikke har denne metode, der er defineret, mislykkes Metodeopkaldet.

Hver enhed metode er beregnet til en enkelt enhed. [Job] [ lnk-devguide-jobs] giver en metode til at aktivere metoder på flere enheder, og kø metode aktivering til afbrudt enheder.

Alle med **tjenesten forbinde** tilladelser på IoT Hub kan aktivere en metode på en enhed.

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Enhed metoder ligner [cloud-til-enhed meddelelser] [ lnk-devguide-messages] i den valgte begge aktivere skyen back-end til at overføre oplysninger til en enhed, men de er forskellige i fundamentale måder. Objekter, er metoder synkron og ikke robust, mens cloud-til-enhed meddelelser er asynkron med op til 48 timer efter holdbarhed.

Metoder følge en anmodning om svar mønster og kan ikke robust. Manglende holdbarhed indeholder to øjeblikkelig fordele, når du skaber enheder:

- **Øjeblikkelig feedback på udførelse af metoden** betyder, at det er ikke nødvendigt for dig at administrere korrelation mellem anmodning og svar.
- **Højere overførselshastighed** betyder, at handlingerne kan udføres hurtigere, fordi IoT Hub ikke leverer en hvilken som helst holdbarhed. IoT Hub tillader flere metode opkald per enhed end cloud-til-enhed meddelelser.

Cloud-til-enhed meddelelser er ikke nødvendigvis kommandoer på enheden, men hellere repræsenterer en skybaseret tjeneste, der passerer nogle bit af oplysninger på enheden at vælge på dens fritid, og som enheden kan eller reagerer ikke. Cloud-til-enhed meddelelser har en længere timeout tid (op til 48 timer) under metoder meget hurtigere at udløbe.

Bruge enhed metoder til øjeblikkelig kommandoen aktivering på en enhed og -job for planlagt start af en kommando på en enhed.

## <a name="method-lifecycle"></a>Metode livscyklus

Metoder er implementeret på enheden og kan kræve nul eller flere input i overførslen metode til at oprette korrekt. Du aktiverer en direkte metode via en tjeneste mod URI (`{iot hub}/twins/{device id}/methods/`). En enhed modtager direkte metoder gennem en enhed-specifikke MQTT emne (`$iothub/methods/POST/{method name}/`). Vi understøtter muligvis metoder på yderligere enhed side-netværksprotokoller i fremtiden.

> [AZURE.NOTE] Når du starter en direkte metode på en enhed, navne og værdier kan kun indeholde US-ASCII-udskriftsvenlig alfanumeriske, undtagen en i det følgende sæt: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Metoder er synkron og enten lykkes eller mislykkes efter timeout-perioden (standard: 30 sekunder, som kan indstilles op til 3600 sekunder). Metoder er nyttige i interaktive scenarier, hvor du vil en enhed skal fungere, men kun hvis enheden er online og modtage kommandoer, som Slå lys fra en telefon. I disse scenarier vil du se en øjeblikkelig lykkes eller mislykkes, så skytjenesten kan handle på resultatet så tidligt som muligt. Enheden kan returnere nogle meddelelsesteksten som et resultat af metoden, men det er ikke påkrævet til metoden til at gøre dette. Der er ingen garanti på rækkefølge eller en hvilken som helst på dokumentsammenfald semantik på metode opkald.

Enhed metode opkald er HTTP kun fra skyen side og MQTT kun fra enheden side.

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om brug af direkte metoder.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Aktivere en direkte metode fra en back end-app

### <a name="method-invocation"></a>Metode aktivering

Direkte metode starter på en enhed er HTTP-opkald, som omfatter:

- *URI* specifikke for enheden (`{iot hub}/twins/{device id}/methods/`)
- POST- *metode*
- *Sidehoveder* , der indeholder tilladelsen, anmode om-ID, indholdstype samt kodning af indhold
- Et gennemsigtigt JSON *brødteksten* i følgende format:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Timeout er i sekunder. Hvis timeout ikke er angivet som standard til 30 sekunder.
  
### <a name="response"></a>Svar

Back end-modtager et svar, som omfatter:

- *Http-statuskode*, som bruges til fejl, der kommer fra IoT hubben, herunder en 404-fejl til enheder, der i øjeblikket ikke forbindelse
- *Sidehoveder* , der indeholder etag anmode om-ID, indholdstype samt kodning af indhold
- Et JSON *brødteksten* i følgende format:

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Begge `status` og `body` er angivet af enheden og bruges til at svare med enhedens egen statuskode og/eller beskrivelse.

## <a name="handle-a-direct-method-on-a-devcie"></a>Håndtere en direkte metode på en devcie

### <a name="method-invocation"></a>Metode aktivering

Enheder modtage direkte metodeanmodninger om MQTT emne:`$iothub/methods/POST/{method name}/?$rid={request id}`

Brødteksten som enheden modtager er placeret i følgende format:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Anmodninger om metode er QoS 0.

### <a name="response"></a>Svar

Enheden sender svar til `$iothub/methods/res/{status}/?$rid={request id}`, hvor:

 - Den `status` egenskaben er leveret af enhed status for udførelse af metoden.
 - Den `$rid` egenskaben er anmodnings-ID fra metode aktiveringen modtaget fra IoT Hub.

Brødteksten er angivet af enheden, kan være en hvilken som helst status.

## <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Nu du har lært, hvordan du bruger direkte metoder, kan du måske interesseret i følgende Developer Guide emne:

- [Planlægge opgaver på flere enheder][lnk-devguide-jobs]

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i det følgende IoT Hub Selvstudium:

- [Bruge skyen-til-enhed metoder][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
