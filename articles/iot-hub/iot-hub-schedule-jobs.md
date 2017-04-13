<properties
 pageTitle="Sådan planlægges job | Microsoft Azure"
 description="Dette selvstudium viser, hvordan du kan planlægge job"
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

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Selvstudium: Planlægge og transmittere jobs (preview)

## <a name="introduction"></a>Introduktion
Azure IoT Hub er en komplet administreret tjeneste, der gør det muligt for en programmet back-end at oprette og spore job, planlægge og opdatere millioner af enheder.  Job kan bruges til følgende handlinger:

- Opdatere egenskaber for enheden dobbelt behov
- Opdatere enhed dobbelt mærker
- Aktivere cloud-til-enhed metoder

Objekter et job ombrydes en af disse handlinger og holder styr på status for udførelse af mod et sæt enheder, der er defineret af en dobbelt forespørgsel.  For eksempel kan ved hjælp af et job en programmet back-end aktivere en genstart metode på 10.000 enheder, der er specificeret af en dobbelt forespørgsel og planlagt på et senere tidspunkt.  Dette program kan derefter registrere status for opgaver, som hver af enhederne, modtage og udfører metoden genstart.

Yderligere oplysninger om hver af disse funktioner i følgende artikler:

- Enhed dobbelt og egenskaber: [Kom i gang med to] [ lnk-get-started-twin] og [Selvstudium: Sådan bruger du dobbelt egenskaber][lnk-twin-props]
- Cloud-til-enhed metoder: [Developer guide - direkte metoder] [ lnk-dev-methods] og [Selvstudium: C2D metoder][lnk-c2d-methods]

Dette selvstudium viser, hvordan til:

- Oprette en simuleret enhed, der har en direkte metode, der gør det muligt for lockDoor, som kan kaldes af programmet tilbage slutningen.
- Oprette et console-program, der kalder metoden lockDoor direkte på simuleret enheden ved hjælp af et job og opdaterer egenskaberne dobbelt beskedteksten ved hjælp af en enhed job.

I slutningen af dette selvstudium har du to Node.js console-programmer:

**simDevice.js**, som opretter forbindelse til din IoT hub med enhed identitet og modtager en lockDoor direkte metode.

**scheduleJobService.js**, som kalder en direkte metode på simuleret enheden og opdatere de to disired egenskaber ved hjælp af en sag.

For at fuldføre dette selvstudium skal bruge du følgende:

Node.js version 0.12.x eller nyere <br/>  [Forberede din udviklingsmiljø] [ lnk-dev-setup] beskriver, hvordan du installerer Node.js til dette selvstudium på Windows eller Linux.

En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal du oprette en Node.js console-app, der svarer til en direkte metode, der kaldes af i skyen, der udløser en simuleret enheden genstart og bruger enhed dobbelt rapporteret egenskaber for at aktivere enhed dobbelt forespørgsler til at identificere enheder, og når de sidste genstartet.

1. Oprette en ny tom mappe med navnet **simDevice**.  Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **simDevice** .  Accepter alle standardværdier:

    ```
    npm init
    ```
    
2. På din kommandoprompt i mappen **simDevice** skal køre følgende kommando for at installere den **azure-iot-enhed** enhed SDK pakke og **azure-iot-enhed – mqtt** pakke:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **simDevice.js** -fil i mappen **simDevice** .

4. Tilføj følgende 'kræver' sætninger i starten af filen **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Tilføj en **connectionString** variabel og bruge det til at oprette en enhed klient.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Tilføj følgende funktion for at håndtere metoden lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Tilføj følgende kode for at registrere handler for metoden lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Gem og Luk filen **simDevice.js** .

> [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere retry-politikker (som en eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Planlægge job til at ringe til en direkte metode og opdatere en dobbelt egenskaber

I dette afsnit, skal du oprette en Node.js console-app, der starter en remote lockDoor på en enhed med en direkte metode og opdatere de to egenskaber.

1. Oprette en ny tom mappe med navnet **scheduleJobService**.  Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **scheduleJobService** .  Accepter alle standardværdier:

    ```
    npm init
    ```
    
2. På din kommandoprompt i mappen **scheduleJobService** skal køre følgende kommando for at installere den **azure-iothub** enhed SDK pakke og **azure-iot-enhed – mqtt** pakke:

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. Ved hjælp af en teksteditor, oprette en ny **scheduleJobService.js** -fil i mappen **scheduleJobService** .

4. Tilføj følgende 'kræver' sætninger i starten af filen **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Tilføj følgende variabelerklæringer og erstatte pladsholder værdier:

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Tilføj følgende funktion, der skal bruges til at overvåge udførelse af jobbet:

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Føj følgende kode for at planlægge jobbet, der kalder metoden enhed:

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Føj følgende kode for at planlægge job til opdatering af to:

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Gem og Luk filen **scheduleJobService.js** .

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. Kommandoprompten i mappen **simDevice** skal køre følgende kommando for at begynde at lytte til metoden genstart direkte.

    ```
    node simDevice.js
    ```

2. Kommandoprompten i mappen **scheduleJobService** , skal du køre følgende kommando for at udløse remote genstarte og -forespørgsel til enhed med dobbelt til at finde sidst genstart tid.

    ```
    node scheduleJobService.js
    ```

3. Du får vist output fra både enheden og back-end-programmer.


## <a name="next-steps"></a>Næste trin

I dette selvstudium brugte du et job til at planlægge en direkte metode til en enhed og opdatering af den enhed dobbelt egenskaber.

Hvis du vil fortsætte med at komme i gang med IoT Hub og enhed management mønstre som remote over air firmware-opdatering, i:

[Selvstudium: Sådan foretager du en firmware-opdatering][lnk-fwupdate]

Hvis du vil fortsætte med at komme i gang med IoT-Hub, se [Kom i gang med IoT Gateway SDK][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx