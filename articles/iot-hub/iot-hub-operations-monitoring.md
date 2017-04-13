<properties
 pageTitle="IoT Hub handlinger overvågning"
 description="En oversigt over Azure IoT Hub handlinger overvågning, så du kan overvåge statussen for handlinger på din IoT hub i realtid"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introduktion til handlinger overvågning

IoT Hub handlinger overvågning gør det muligt at overvåge statussen for handlinger på din IoT hub i realtid. IoT Hub registrerer begivenheder på tværs af flere kategorier af handlinger, og du kan vælge til afsendelse af hændelser fra en eller flere kategorier til slutpunkt IoT centrum for behandling. Du kan overvåge data for fejl eller oprette mere komplekse behandling baseret på dataene mønstre.

IoT Hub overvåger fem kategorier af hændelser:

- Enhed identitet handlinger
- Enhed telemetri
- Kommandoer i skyen-til-enhed
- Forbindelser
- Fil overførsler

## <a name="how-to-enable-operations-monitoring"></a>Sådan aktiveres handlinger overvågning

1. Oprette en IoT hub. Du kan finde instruktioner til, hvordan du opretter en IoT hub i [Komme i gang] [ lnk-get-started] vejledning.

2. Åbn bladet af din IoT hub. Klik på **Handlinger overvågning**derfra.

    ![][1]

3. Vælg de overvågning kategorier, du vil du overvåge, og klik derefter på **Gem**. Hændelserne, der er tilgængelige for læsning fra begivenhed Hub-kompatible slutpunktet er angivet i **overvågnings indstillinger**. IoT Hub slutpunktet hedder `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Begivenhed kategorier og hvordan du bruger dem.

Hver handlinger overvågning kategori spor forskellige typer interaktion med IoT Hub og hver overvågning kategori har et skema, der definerer, hvordan hændelser i den pågældende kategori er struktureret.

### <a name="device-identity-operations"></a>Enhed identitet handlinger

Kategorien enhed identitet handlinger registrerer fejl, der opstår, når du forsøger at oprette, opdatere eller slette en post på din IoT hub identitet registreringsdatabasen. Sporing af denne kategori er nyttig til klargøring af scenarier.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Enhed telemetri

Kategorien enhed telemetri registrerer fejl, der optræder med IoT hub og er relateret til telemetri rørledning. I denne kategori indeholder fejl, der opstår, når du sender telemetri begivenheder (såsom throttling) og modtage telemetri hændelser (såsom uautoriseret læser). Bemærk, at denne kategori ikke kan fange fejl på grund af kode, der kører på selve enheden.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Kommandoer i skyen-til-enhed

Kategorien cloud-til-enhed kommandoer registrerer fejl, der optræder med IoT hub og er relateret til enhed kommandoen rørledning. I denne kategori indeholder fejl, der opstår ved at sende kommandoer (såsom uautoriseret afsenderen), modtager kommandoer (såsom levering antal hop) og modtage kommandoen feedback (f.eks feedback udløbet). I denne kategori fanger ikke fejl fra en enhed, der håndterer en kommando forkert, hvis kommandoen blev leveret.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Forbindelser

Kategorien forbindelser registrerer fejl, der opstår, når enheder tilknytte eller fjerne tilknytning fra en IoT hub. Sporing af denne kategori er nyttig til at identificere uautoriseret forbindelse forsøg og til at spore, når en forbindelsen afbrydes til enheder i områder af dårlig forbindelse.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Fil overførsler

Kategorien fil Overfør registrerer fejl, der optræder med IoT hubben og er relateret til funktionaliteten for overførsel af filer. I denne kategori indeholder fejl, der opstår med den SAS URI (såsom når det udløber før en enhed giver besked om hubben på en færdige Overfør), mislykkedes overførsler af enheden, og når en fil findes ikke i lagerplads under oprettelse af IoT Hub meddelelse meddelelse. Bemærk, at denne kategori ikke kan fange fejl, der opstår direkte mens enheden overføres en fil til lager.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Næste trin

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md