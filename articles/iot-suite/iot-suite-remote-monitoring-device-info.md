<properties
 pageTitle="Enhed oplysninger metadata i remote overvågning løsningen | Microsoft Azure"
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
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Enhed oplysninger metadata i remote overvågning forudkonfigureret løsning

Azure IoT pakke remote overvågning forudkonfigurerede løsning demonstrerer en metode til håndtering af enhedsmetadata. I denne artikel beskrives den fremgangsmåde, der tager denne løsning til gør det muligt at forstå:

- Hvilken enhedsmetadata løsningen gemmer.
- Sådan håndteres løsningen enhedsmetadata.

## <a name="context"></a>Kontekst

Remote overvågning forudkonfigurerede løsningen bruger [Azure IoT Hub] [ lnk-iot-hub] til at aktivere dine enheder til at sende data til skyen. IoT Hub omfatter en [enhed identitet registreringsdatabasen] [ lnk-identity-registry] til at styre adgangen til IoT-Hub. IoT Hub enhed identitet registreringsdatabasen er adskilt fra den eksterne overvågning løsning-specifikke *enhed registreringsdatabasen* , der gemmer enhed oplysninger metadata. Remote overvågning løsningen bruger en [DocumentDB] [ lnk-docdb] database til at implementere dens enhed registreringsdatabasen til lagring af enhed oplysninger metadata. [Microsoft Azure IoT referencearkitektur] [ lnk-ref-arch] beskriver rollen for enhed registreringsdatabasen i en typisk IoT løsning.

> [AZURE.NOTE] Remote overvågning forudkonfigurerede løsningen bevarer enhed identitet registreringsdatabasen synkroniseret med registreringsdatabasen enhed. Begge bruge det samme enheds-id til at entydigt identificerer de enkelte enheder tilsluttet din IoT hub.

[IoT Hub enhed administration preview] [ lnk-dm-preview] tilføjer funktioner til IoT-Hub, der svarer til de enhed oplysninger management-funktioner, der er beskrevet i denne artikel. Remote overvågning løsningen bruger i øjeblikket kun alment tilgængelig (GA) funktioner i IoT Hub.

## <a name="device-information-metadata"></a>Enhed oplysninger metadata

Enhed oplysninger metadata JSON gemt et dokument i enhed DocumentDB registreringsdatabasen har følgende struktur:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: selve enheden skriver disse egenskaber og enheden er nøglecenter til disse data. Egenskaber for enheden andre eksempel omfatter producent, model tal og serienummer. 
- **DeviceID**: entydigt enheds-id. Denne værdi er den samme i IoT Hub enhed identitet registreringsdatabasen.
- **HubEnabledState**: status for enhed i IoT Hub. Denne værdi er indstillet til **null** indtil enheden tilsluttes først. I portalen løsning, der er repræsenteret en **null** -værdi som den enhed, der "registrerede, men ikke til stede."
- **CreatedTime**: tidspunktet enheden blev oprettet.
- **DeviceState**: tilstanden rapporteret af enheden.
- **UpdatedTime**: den tid, enheden sidst blev opdateret via portalen løsning.
- **SystemProperties**: portalen løsning skriver dialogboksen Egenskaber for system og enheden ikke har kendskab til disse egenskaber. Et eksempel systemegenskab er **ICCID** , hvis løsningen godkendes med og forbindelse til en tjeneste, administration af SIM-aktiverede enheder.
- **Kommandoer**: en liste over kommandoer enheden understøtter. Enheden giver dig disse oplysninger til løsningen.
- **CommandHistory**: en liste over de kommandoer, der er sendt af remote overvågning løsningen til enheden og status for kommandoerne.
- **IsSimulatedDevice**: et flag, der identificerer en enhed som en simuleret enhed.
- **id**: DocumentDB entydigt id for denne enhed dokument.

> [AZURE.NOTE] Enhedsoplysninger kan også indeholde metadata for at beskrive telemetri enheden sender til IoT Hub. Remote overvågning løsningen bruger disse telemetri metadata til at tilpasse, hvordan dashboard viser [dynamiske telemetri][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Livscyklus

Når du opretter en enhed i portalen løsning, opretter løsningen en indtastning i dets enhed registreringsdatabasen som vist tidligere. Meget af det er først stubbed og **HubEnabledState** er angivet til **null**. På dette tidspunkt opretter løsningen også en post for enheden i enhed identitet registreringsdatabasen, som udløser tasterne enheden bruger til at godkende med IoT Hub.

Når en enhed først opretter forbindelse til løsningen, sender meddelelsen enhed oplysninger. Denne enhed oplysningsmeddelelse omfatter egenskaber for enheden som producenten af enheden, model tal og serienummeret. En enhed oplysningsmeddelelse indeholder også en oversigt over de kommandoer, enheden understøtter herunder oplysninger om eventuelle kommandoparametre. Når løsningen modtager denne meddelelse, opdaterer enhed oplysninger metadataene i registreringsdatabasen enhed.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Få vist og redigere enhedsoplysninger i portalen løsning

Listen over enheder i portalen løsning viser følgende egenskaber for enheden som kolonner: **Status**, **DeviceId**, **producenten**, **Model tal**, **serienummer**, **Firmware**, **Platform**, **Processor**og **Installeret RAM**. Egenskaber for enheden **længde** - og **længdegrader** drive sted i vinduet fra Bing kort på dashboardet. 

![Liste over enheder][img-device-list]

Hvis du klikker på **Rediger** i ruden **Detaljer for enhed** i portalen løsning, kan du redigere disse egenskaber. Redigere disse egenskaber opdaterer posten for enhed i DocumentDB databasen. Men hvis en enhed sender meddelelsen opdaterede enhed oplysninger, overskriver den eventuelle ændringer i portalen løsning. Du kan ikke redigere **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState**og **UpdatedTime** egenskaber i portalen løsning, da kun enhed har nøglecenter over disse egenskaber.

![Rediger enhed][img-device-edit]

Du kan bruge portalen løsning for at fjerne en enhed fra din løsning. Når du fjerner en enhed, fjerner enhed oplysninger metadata fra løsning enhed registreringsdatabasen løsningen, og fjerner posten enhed i IoT Hub enhed identitet registreringsdatabasen. Før du kan fjerne en enhed, skal du deaktivere den.

![Fjern enhed][img-device-remove]

## <a name="device-information-message-processing"></a>Behandling af enhed oplysninger meddelelse

Enhed oplysningsmeddelelser sendes af en enhed er væsentligt forskellige fra telemetri meddelelser. Enhed oplysningsmeddelelser omfatter oplysninger som egenskaber for enheden, kommandoerne en enhed kan besvare og en hvilken som helst kommandoen oversigt. IoT Hub selve har ikke kendskab til de metadata, der er indeholdt i en enhed oplysningsmeddelelse og behandler meddelelsen på samme måde, det behandler en hvilken som helst enhed i skyen-meddelelse. I remote overvågning løsningen, en [Azure Stream Analytics] [ lnk-stream-analytics] (ASA) job læser meddelelser fra IoT Hub. **DeviceInfo** stream analyser job filtre for meddelelser, der indeholder **"Objekttype": "DeviceInfo"** og sender dem til den **EventProcessorHost** host-forekomst, der kører i et web-job. Logik i forekomsten **EventProcessorHost** bruger enheds-id til at finde posten DocumentDB for den pågældende enhed og opdatere posten. Enhed registreringsdatabasen posten omfatter nu oplysninger som egenskaber for enheden, kommandoer og kommandoen oversigt.

> [AZURE.NOTE] En enhed oplysningsmeddelelse er meddelelsen standard enhed i skyen. Løsningen skelner mellem enhed oplysningsmeddelelser og telemetri meddelelser ved hjælp af ASA forespørgsler.

## <a name="example-device-information-records"></a>Eksempel enhed oplysninger poster

Remote overvågning forudkonfigurerede løsningen bruger to typer enhed oplysninger poster: poster for de simulerede enheder, der er installeret med løsning og -posterne for de brugerdefinerede enheder, du opretter forbindelse til løsningen.

### <a name="simulated-device"></a>Simuleret enhed

I følgende eksempel vises posten JSON enhed oplysninger til en simuleret enhed. Denne post er en værdi, der er angivet for **UpdatedTime**, som angiver enheden har sendt meddelelsen **DeviceInfo** til IoT-Hub. I en post indeholder nogle almindelige egenskaber for enheden, definerer seks kommandoerne simuleret enheder understøtter, og der flaget **IsSimulatedDevice** , der er angivet til **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Brugerdefineret enhed

I følgende eksempel viser JSON enhed oplysninger post til en brugerdefineret enhed og har **IsSimulatedDevice** flag indstillet til **0**. Du kan se, at enheden brugerdefinerede understøtter to kommandoer og, portalen løsning har sendt en **SetTemperature** -kommando til enheden:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

Følgende JSON **DeviceInfo** meddelelse vises på enheden, der er sendt til at opdatere enhed oplysninger metadata:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Næste trin

Nu du er færdig med at lære, hvordan du kan tilpasse de forudkonfigurerede løsninger, kan du udforske nogle af de andre funktioner og egenskaber de IoT pakke forudkonfigureret løsninger:

- [Skønnet vedligeholdelse forudkonfigureret løsning oversigt][lnk-predictive-overview]
- [Ofte stillede spørgsmål om IoT pakke][lnk-faq]
- [IoT sikkerhed fra bunden][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
