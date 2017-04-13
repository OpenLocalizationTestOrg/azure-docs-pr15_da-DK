<properties
 pageTitle="Udvikler vejledning - job | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - planlægning job til at køre på flere enheder tilsluttet din hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Planlægge opgaver på flere enheder (preview)

## <a name="overview"></a>Oversigt

Som beskrevet i forrige artikler, Azure IoT Hub aktiverer et antal dokumentkomponenter ([dobbelt egenskaber for enheden og de mærker,] [ lnk-twin-devguide] og [skyen-til-enhed metoder][lnk-dev-methods]).  IoT back-end-programmer kan typisk enhed administratorer og operatorer til at opdatere og interagere med IoT enheder ad gangen og på et bestemt tidspunkt.  Job omfatter udførelse af enhed dobbelt opdateringer og C2D metoder mod et sæt enheder ad gangen tidsplan.  For eksempel bruger en operator en back-end-program, der skal starte og registrere et job for at genstarte et sæt af enheder i opbygning af 43 og -gulve 3 ad gangen, der ikke er forstyrrende på transaktioner building.

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Overvej at bruge job ved: en løsning tilbage afslutte skal planlægge og spore fremskridt et af følgende på et sæt af enhed:

- Opdatere egenskaber for enheden dobbelt behov
- Opdatere enhed dobbelt mærker
- Aktivere C2D metoder

## <a name="job-lifecycle"></a>Jobbet livscyklus

Job er startet af løsning back-end og vedligeholdes af IoT Hub.  Du kan starte et job via en tjeneste mod URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) og -forespørgsel til status på en udførelsen job via en tjeneste mod URI (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Når et job startes, aktivere forespørgsler for sager back-end program tilladelse til at opdatere status for kører job.

> [AZURE.NOTE] Når du starter et job, navne og værdier kan kun indeholde US-ASCII-udskriftsvenlig alfanumeriske, undtagen en i det følgende sæt: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om brug af sager.

## <a name="jobs-to-execute-direct-methods"></a>Opgaver, der skal udføre direkte metoder

Følgende er HTTP 1.1 anmodning om detaljerne for at udføre en [direkte metode] [ lnk-dev-methods] på et antal enheder, der bruger en sag:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Job opdatere egenskaber for enheden dobbelt

Følgende er HTTP 1.1 anmodning om detaljerne for opdatering af egenskaber for enheden dobbelt ved hjælp af en sag:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Forespørgsler for opgavefremdrift på job

Følgende er HTTP 1.1 anmodning om oplysninger om [forespørgsler for sager][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
ContinuationToken leveres fra svaret.  

## <a name="jobs-properties"></a>Egenskaber for job

Følgende er en liste over egenskaber og tilsvarende beskrivelser, som kan bruges, når forespørgsler for job eller jobbet resultater.

| Egenskaben | Beskrivelse |
| -------------- | -----------------|
| **job-id** | Programmet angivet ID til opgaven. |
| **Starttidspunkt** | Programmet angivet starttidspunktet (ISO-8601) til opgaven. |
| **Sluttidspunkt** | IoT Hub angivet dato (ISO-8601) til, jobbet afsluttet. Gælder kun, når jobbet når tilstanden 'færdige'. | 
| **type** | Typer af job: |
| | **scheduledUpdateTwin**: et job, der bruges til at opdatere et sæt med dobbelt beskedteksten egenskaber eller mærker. |
| | **scheduledDeviceMethod**: et job, der bruges til at aktivere en enhed metode på en række med dobbelt. |
| **status** | Aktuelle status for jobbet. Mulige værdier for status: |
| | **ventende** : planlagt og venter på at hentes af tjenesten job. |
| | **planlagt** : planlagt for en gang i fremtiden. |
| | **kører** : aktuelt aktive job. |
| | **annulleret** : jobbet blev annulleret. |
| | **mislykkedes** : Jobbet mislykkedes. |
| | **fuldført** : jobbet er fuldført. |
| **deviceJobStatistics** | Statistik om udførelse af det job. |

Under Vis udskrift findes objektet deviceJobStatistics, når jobbet er afsluttet.

| Egenskaben | Beskrivelse |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Antallet af enheder i jobbet. |
| **deviceJobStatistics.failedCount** | Antal enheder, hvor jobbet mislykkedes. |
| **deviceJobStatistics.succeededCount** | Antal enheder, hvor jobbet lykkedes. |
| **deviceJobStatistics.runningCount** | Antal enheder, der kører jobbet. |
| **deviceJobStatistics.pendingCount** | Antal enheder, der er udestående at køre jobbet. |


### <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i det følgende IoT Hub Selvstudium:

- [Planlægge og Udsend job][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
