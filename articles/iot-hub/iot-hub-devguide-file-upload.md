<properties
 pageTitle="Udvikler vejledning - filoverførsel | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - overførsel af filer fra en enhed til IoT-Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="upload-files-from-a-device"></a>Overføre filer fra en enhed

## <a name="overview"></a>Oversigt

Som beskrevet i [IoT Hub slutpunkter] [ lnk-endpoints] artikel enheder kan starte filoverførsler ved at sende en besked via et enhed mod slutpunkt (**/devices/ {deviceId} / filer**).  Når en enhed giver besked om IoT hubben på en færdige Overfør, genererer IoT Hub fil overførsler, som du kan modtage gennem et med adgang til tjenesten slutpunkt (**/messages/servicebound/filenotifications**) som meddelelser.

I stedet for brokering meddelelser via IoT Hub selve fungerer IoT Hub i stedet som en afsender til en tilknyttet konto Azure-lager. En enhed anmoder om et lager token fra IoT-Hub, der er specifikke for enheden ønsker at overføre filen. Enheden bruger SAS URI for at overføre filen til lager, og når overførslen er færdig enheden sender en meddelelse om fuldførelse til IoT Hub. IoT Hub kontrollerer, at filen er blevet overført, og derefter tilføjer en meddelelse om overførsel af filer til det nye med adgang til tjeneste fil meddelelse SMS slutpunkt.

Før du kan overføre en fil til IoT Hub fra en enhed, skal du konfigurere din hub ved at [knytte en Azure-lager] [ lnk-associate-storage] konto til den.

Enheden kan derefter [initialiseret en overførsel] [ lnk-initialize] og derefter [besked IoT hub] [ lnk-notify] når overførslen er fuldført. Du kan også når en enhed giver besked om IoT-Hub, at overførslen er færdig, tjenesten kan oprette en [besked om][lnk-service-notification].

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Brug denne funktion IoT-Hub, når du skal overføre en fil fra en enhed til back end-tjenesten i stedet for at sende en meddelelse via IoT Hub.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Knytte en Azure-lager konto med IoT Hub

Hvis du vil bruge funktionen fil til overførsel, skal du først sammenkæde et firma Azure-lager til IoT-Hub. Du kan gøre dette enten via [Azure portal][lnk-management-portal], eller fra et program gennem [IoT Hub ressource udbyder REST API'er][lnk-resource-provider-apis]. Når du har knyttet en Azure-lager-konto til din IoT-Hub, returnerer tjenesten en SAS URI til en enhed, når enheden starter en anmodning om overførsel.

> [AZURE.NOTE] [Azure IoT Hub SDK'er] [ lnk-sdks] automatisk at håndtere hentning af SAS URI, overføre filen og besked om IoT Hub af en færdige Overfør.

## <a name="initialize-a-file-upload"></a>Initialiseret en filoverførsel

IoT Hub har et slutpunkt specifikt for enheder til at anmode om en SAS URI for lagerplads til at overføre en fil. Enheden starter overførslen fil ved at sende et INDLÆG til IoT hubben på `{iot hub}.azure-devices.net/devices/{deviceId}/files` med følgende JSON brødteksten:

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub returnerer følgende oplysninger, der bruger enheden til at overføre filen:

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Frarådede: initialiseret en filoverførsel med få

> [AZURE.NOTE] I dette afsnit beskrives frarådede funktioner til at modtage en SAS URI fra IoT Hub. Brug metoden POST, der er beskrevet ovenfor.

IoT Hub har to RESTEN slutpunkter til at understøtte filoverførsel, en-til-få SAS URI for lager og den anden til at informere IoT hubben på en færdige Overfør. Enheden starter overførslen fil ved at sende få til IoT hubben på `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. Hubben returnerer en SAS URI bestemt til den fil, der overføres, og en korrelations-ID'ET, der skal bruges, når overførslen er fuldført.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Besked IoT hubben på en færdige filoverførsel

Enheden er ansvarlig for at overføre filen til lager, med Azure lagerplads SDK'er. Når overførslen er fuldført, enheden sender et INDLÆG til IoT hubben på `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` med følgende JSON brødteksten:

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Værdien af `isSuccess` er en boolesk repræsenterer, uanset om filen blev overført. Statuskode for `statusCode` er status for at overføre filen til lagring, og den `statusDescription` svarer til den `statusCode`.

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om overførsel af filer fra en enhed.

## <a name="file-upload-notifications"></a>Meddelelser om overførsel af filer

Når en enhed overfører en fil og giver besked om IoT hubben på Overfør fuldførelse, genererer tjenesten eventuelt en meddelelse, der indeholder navn og lager placeringen af filen.

Som beskrevet i [slutpunkter][lnk-endpoints], IoT Hub leverer fil overførsler via et med adgang til tjenesten slutpunkt (**/messages/servicebound/fileuploadnotifications**) som meddelelser. Modtag semantik for meddelelser om overførsel af filer er den samme som for cloud-til-enhed meddelelser og har den samme [meddelelse livscyklus][lnk-lifecycle]. Alle de meddelelser, der er hentet fra fil Overfør meddelelse slutpunkt er en JSON post med følgende egenskaber:

| Egenskaben | Beskrivelse |
| -------- | ----------- |
| EnqueuedTimeUtc | Tidsstempel, der angiver, hvor meddelelsen blev oprettet. |
| DeviceId | **DeviceId** af den enhed, der er overført filen. |
| BlobUri | URI for den reviderede fil. |
| BlobName | Navnet på den reviderede fil. |
| LastUpdatedTime | Tidsstempel, der angiver, hvor filen sidst blev opdateret. |
| BlobSizeInBytes | Størrelsen af den reviderede fil. |

**Eksempel**. Dette er et eksempel på brødteksten i meddelelsen om en fil Overfør.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Indstillinger for fil Overfør besked konfiguration

Hver IoT hub Fremviser følgende af konfigurationsindstillinger for meddelelser om overførsel af filer:

| Egenskaben | Beskrivelse | Område- og standard |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Kontrollerer, om meddelelser om overførsel af filer er skrevet til filen beskeder slutpunkt. | Boolesk. Som standard: SAND. |
| **fileNotifications.ttlAsIso8601** | Standard TTL for fil-overførsler. | ISO_8601 interval op til 48 H (minimum 1 minut). Standard: 1 time. |
| **fileNotifications.lockDuration** | Låse varighed for fil Overfør beskeder kø. | 5 til 300 sekunder (minimum 5 sekunder). Standard: 60 sekunder. |
| **fileNotifications.maxDeliveryCount** | Levering af maksimalt antal for filen overføre meddelelse kø. | 1 til 100. Som standard: 100. |

## <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Nu du har lært Sådan overfører du filer fra enheder, der bruger IoT Hub, kan du måske interesseret i Developer Guide følgende emner:

- [Administrer identiteter enhed i IoT Hub][lnk-devguide-identities]
- [Styre adgangen til IoT Hub][lnk-devguide-security]
- [Bruge enhed twins til at synkronisere konfigurationer og tilstand][lnk-devguide-device-twins]
- [Aktivere en direkte metode på en enhed][lnk-devguide-directmethods]
- [Planlægge opgaver på flere enheder][lnk-devguide-jobs]

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i det følgende IoT Hub Selvstudium:

- [Sådan overfører du filer fra enheder til skyen med IoT Hub][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
