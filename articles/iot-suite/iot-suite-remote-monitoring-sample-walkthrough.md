<properties
 pageTitle="Remote overvågnings forudkonfigureret løsning gennemgang | Microsoft Azure"
 description="En beskrivelse af Azure IoT forudkonfigureret løsning remote overvågning og dens arkitektur."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Remote overvågning forudkonfigureret løsning gennemgang

## <a name="introduction"></a>Introduktion

Den IoT pakke remote overvågning [forudkonfigureret løsning] [ lnk-preconfigured-solutions] overvåger en implementering af en til en komplet løsning til flere maskiner, der kører på remote placeringer. Løsningen kombinerer vigtige Azure services for at angive en generisk implementering af scenariet business, og du kan bruge den som udgangspunkt til din egen implementering. Du kan [tilpasse] [ lnk-customize] løsningen til bestemte virksomhedens behov.

I denne artikel vejledes du gennem nogle af de vigtigste elementer i remote overvågning løsningen til gør det muligt at forstå, hvordan det fungerer. Denne viden hjælper dig med at:

- Foretage fejlfinding af problemer i løsningen.
- Planlægge, hvordan du tilpasser til løsningen til at opfylde dine egne specifikke krav. 
- Designe din egen IoT løsning, der bruger Azure tjenester.

## <a name="logical-architecture"></a>Logiske arkitektur

I det følgende diagram viser de logiske komponenter forudkonfigurerede løsningens:

![Logiske arkitektur](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>Simuleret enheder

I den forudkonfigurerede løsning repræsenterer simuleret enheden køling (som en dokumentkomponent air conditioner eller facilitet air håndtering af enhed). Når du installerer den forudkonfigurerede løsning, du også automatisk klargøre fire simuleret enheder, der kører på en [Azure WebJob][lnk-webjobs]. Simuleret enheder gør det nemt for dig at udforske funktionsmåden for løsningen uden at skulle installere en hvilken som helst fysiske enheder. Du skal installere en reelle fysisk enhed, se [forbinde enheden til eksterne overvågning forudkonfigurerede løsning] [ lnk-connect-rm] selvstudium.

Hver simuleret enhed kan sende følgende meddelelsestyper til IoT Hub:

| Meddelelse  | Beskrivelse |
|----------|-------------|
| Start  | Når enheden starter, sender programmet en **enhed-info** -meddelelse, der indeholder oplysninger om sig selv til back-end. Disse data omfatter enheds-id, enhedsmetadata, en liste over kommandoerne, der understøtter enheden, og den aktuelle konfiguration på enheden. |
| Tilstedeværelse | En enhed sender med jævne mellemrum meddelelsen **tilstedeværelse** til at rapportere om enheden kan registrerer tilstedeværelsen af en føler. |
| Telemetri | En enhed sender med jævne mellemrum en meddelelse om **telemetri** , simuleret værdier for temperatur og fugtighed, der indsamles fra enhedens simuleret sensorer-rapporter. |


Simuleret enheder sende følgende egenskaber for enheden i en **enhed-info** -meddelelse:

| Egenskaben               |  Formål |
|------------------------|--------- |
| Enheds-ID              | Id, der er enten findes eller tildelt, da en enhed er oprettet i en løsning. |
| Producenten           | Producenten af enheden |
| Model tal           | Model antallet af enheden |
| Serienummer          | Serienummeret for enheden |
| Firmware               | Aktuelle version af firmware på enheden |
| Platform               | Platformarkitektur på enheden |
| Processor              | Processor, der kører på enheden |
| Installeret RAM          | Mængden af RAM installeret på enheden |
| Hub aktiveret tilstand      | IoT Hub tilstand egenskab for enheden |
| Tidspunkt for oprettelse           | Enheden er oprettet i løsningen tid |
| Opdaterede tid           | Sidste gang egenskaber er blevet opdateret for enheden |
| Længde               | Længde placeringen af enheden |
| Længdegrader              | Breddegrader placeringen af enheden |

Simulator forhåndsudfylder disse egenskaber i simuleret enheder med eksempelværdier.  Hver gang simulator starter en simuleret enhed indlæg enheden de foruddefinerede metadata til IoT-Hub. Bemærk, hvordan dette overskriver eventuelle metadata opdateringer, der er foretaget i portalen enhed.


Simuleret enheder kan håndtere de følgende kommandoer, der sendes fra dashboardet løsning gennem IoT hubben:

| Kommandoen                | Beskrivelse                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Sender en _ping_ til kameraet, for at kontrollere, at den er aktiv   |
| StartTelemetry         | Starter den enhed, der sender telemetri                 |
| StopTelemetry          | Stopper enhed fra at sende telemetri             |
| ChangeSetPointTemp     | Ændrer værdien Indstil punkt omkring som genereres tilfældige data |
| DiagnosticTelemetry    | Udløser enhed simulator for at sende en yderligere telemetri værdi (externalTemp) |
| ChangeDeviceState      | Ændringer af en udvidet tilstand egenskab for enheden og sender meddelelsen enhed oplysninger fra enheden |

Kommandoen enhed bekræftelse til løsning back-end leveres gennem IoT hubben.

## <a name="iot-hub"></a>IoT-Hub

[IoT hub] [ lnk-iothub] ingests data, der sendes fra enhederne i skyen, og gør det tilgængeligt til Azure Stream Analytics (ASA) sager. IoT hub sender også kommandoer til dine enheder på vegne af portalen enhed. Hvert stream ASA job bruger en separat IoT Hub consumer gruppe til at læse strømmen af meddelelser fra dine enheder.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

I remote overvågning løsningen [Azure Stream Analytics] [ lnk-asa] (ASA) afsender enhed meddelelser modtages af IoT-hub til andre back end-komponenter til behandling eller opbevaring. Forskellige ASA job udføre bestemte funktioner, der er baseret på indholdet af meddelelser.

**Job 1: din mobiltelefon** filtrerer enhed oplysningsmeddelelser fra indgående meddelelse strømmen og sender dem til en begivenhed Hub slutpunkt. En enhed sender enhed oplysningsmeddelelser ved start og svar på en **SendDeviceInfo** -kommando. Dette job bruger følgende forespørgselsdefinitionen til at identificere **enhed info** -meddelelser:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Dette job sender dens output til en begivenhed Hub til videre behandling.

**Job 2: regler** evaluerer indgående temperaturen luftfugtighed telemetri værdierne og mod per enhed tærskler. Tærskelværdi er angivet i editoren regler, der er tilgængelige i dashboardet løsning. Hvert enhed/værdi-par er gemt af tidsstempel i en blob som Stream Analytics læser i som **Reference Data**. Jobbet sammenligner alle ikke-tomme værdier mod sæt grænsen for enheden. Hvis den overstiger den ' >' betingelse jobbet skriver **en alarm, der angiver, at grænseværdi overskrides og leverer den enhed, værdi og tidsstempel værdier** . Dette job bruger følgende forespørgselsdefinitionen til at identificere telemetri meddelelser, der skal udløse en alarm:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

Jobbet sender dens output til en begivenhed Hub til videre behandling og gemmer oplysninger om hver enkelt besked til blob-lager fra hvor dashboardet løsning kan læse oplysningerne om besked.

**Job 3: Telemetri** fungerer på indgående enhed telemetri strømmen på to måder. Først sender alle telemetri meddelelser fra enhederne til fast blob-lager til længerevarende opbevaring. Andet beregner gennemsnit, minimum og maksimale luftfugtighed værdier over en fem minutters skyderen vindue og sender disse data til blob-lager. Løsning dashboardet læser telemetridata fra blob-lager til at udfylde diagrammerne. Dette job bruger følgende forespørgselsdefinitionen:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Begivenhed Hubs

På **din mobiltelefon** og **regler** ASA job output deres data til begivenhed hubber pålideligt videresende til **Begivenhed Processor** kører i WebJob.

## <a name="azure-storage"></a>Azure-lager

Løsningen bruger Azure blob-lager til at bevare alle rå og opsummerede telemetri dataene fra enheder i løsningen. Dashboard læser telemetridata fra blob-lager til at udfylde diagrammerne. For at få vist beskeder, læser dashboardet data fra blob-lager, der registrerer når telemetri værdier overskredet den konfigurerede tærskelværdi. Løsningen bruges også blob-lager til at registrere den tærskelværdi, der konfigureres i dashboardet.

## <a name="webjobs"></a>WebJobs

Ud over vært enhed simulatorer, hoste WebJobs i løsningen også **Begivenhed Processor** kører i en Azure WebJob pågældende håndtag enhed oplysningsmeddelelser og kommandoen svar. Den anvender:

- Enhed oplysningsmeddelelser til at opdatere enhed registreringsdatabasen (gemt i databasen, DocumentDB) med oplysningerne om den aktuelle enhed.
- Kommandoen svarmeddelelser opdatere enhed kommandooversigten (gemt i databasen, DocumentDB).

## <a name="documentdb"></a>DocumentDB

Løsningen bruger en DocumentDB database til at gemme oplysninger om de enheder, der er forbundet med løsningen. Disse oplysninger omfatter enhedsmetadata og oversigten over kommandoer, der sendes til enheder fra dashboardet.

## <a name="web-apps"></a>Webapps

### <a name="remote-monitoring-dashboard"></a>Remote overvågning dashboard
Denne side i webprogrammet bruger PowerBI javascript-kontrolelementer (se [PowerBI-visuelle elementer repo](https://www.github.com/Microsoft/PowerBI-visuals)) til at visualisere telemetridata fra enhederne. Løsningen bruger kørslen ASA telemetri til at skrive telemetridata for at blob storage.


### <a name="device-administration-portal"></a>Enhed administrationsportalen

Denne WebApp kan du:

- Klargøre en ny enhed. Denne handling angiver entydigt enheds-id og genererer tasten godkendelse. Det skriver oplysninger om enheden til både IoT Hub identitet registreringsdatabasen og løsning-specifikke DocumentDB databasen.
- Administrere egenskaber for enheden. Denne handling indeholder eksisterende egenskaber for visning og opdatering af med nye egenskaber.
- Sende kommandoer til en enhed.
- Få vist kommandoen oversigten til en enhed.
- Aktivere og deaktivere enheder.

## <a name="next-steps"></a>Næste trin

Følgende blogindlæg til TechNet indeholder flere oplysninger om remote overvågning forudkonfigurerede løsningen:

- [IoT pakke - Under struktur - Remote overvågning](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [IoT pakke - Remote overvågning - tilføje direkte og simuleret enheder](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Du kan fortsætte med at komme i gang med IoT pakke ved at læse følgende artikler:

- [Tilslut din enhed til remote overvågning forudkonfigurerede løsningen][lnk-connect-rm]
- [Tilladelser på webstedet azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md