<properties
 pageTitle="Bruge direkte metoder | Microsoft Azure"
 description="Dette selvstudium viser, hvordan du bruger direkte metoder"
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
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Selvstudium: Bruge direkte metoder

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der gør det muligt for pålidelig og sikker tovejs-kommunikation mellem millioner af IoT enheder og et program igen slutningen. Forrige selvstudier ([komme i gang med IoT Hub] og [sende meddelelser i skyen-til-enhed med IoT Hub]) illustrere grundlæggende enhed i skyen og skyen-til-enhed SMS funktionaliteten i IoT Hub. IoT Hub giver dig også mulighed for at aktivere ikke-varige metoder på enheder fra skyen. Metoder repræsenterer en anmodning om autosvar interaktion med en enhed, der ligner en HTTP-opkaldet, fordi de lykkes eller mislykkes umiddelbart (efter en brugerdefineret timeout) til oplyser brugeren om status for opkaldet. [Aktivere en direkte metode på en enhed] [ lnk-devguide-methods] beskrives metoder mere detaljeret og indeholder vejledning om, hvornår du skal bruge metoder kontra cloud-til-enhed meddelelser.

Dette selvstudium viser, hvordan til:

- Bruge Azure-portalen til at oprette en IoT hub og oprette en enhed identitet i IoT-hubben.
- Oprette en simuleret enhed, der har en direkte metode, der kan kaldes af skyen.
- Oprette et console-program, der kalder en direkte metode på simuleret enheden via IoT centrum.

> [AZURE.NOTE] Direkte metoder er kun tilgængelig fra enheder, der har forbindelse til IoT Hub ved hjælp af MQTT protokollen på nuværende tidspunkt. Se den [MQTT understøtter] [ lnk-devguide-mqtt] artikel for at få vejledning i at konvertere eksisterende enhed app til at bruge MQTT.

I slutningen af dette selvstudium har du to Node.js console-programmer:

* **CallMethodOnDevice.js**, som kalder en metode på simuleret enheden og viser svaret.
* **SimulatedDevice.js**, som opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, og svarer til den metode, der kaldes af skyen.

> [AZURE.NOTE] I artiklen [IoT Hub SDK'er] [ lnk-hub-sdks] indeholder oplysninger om de forskellige SDK'er, som du kan bruge til at opbygge begge programmer til at køre på enheder og din løsning back-end.

For at fuldføre dette selvstudium skal bruge du følgende:

+ Node.js version 0.10.x eller nyere.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal oprette du en Node.js console-app, der svarer til en metode, der kaldes af skyen.

1. Oprette en ny tom mappe med navnet **simulateddevice**. Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **simulateddevice** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **simulateddevice** skal køre følgende kommando for at installere den **azure-iot-enhed** enhed SDK pakke og **azure-iot-enhed – mqtt** pakke:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **SimulatedDevice.js** -fil i mappen **simulateddevice** .

4. Tilføj følgende `require` sætninger i starten af filen **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Tilføj en **connectionString** variabel og bruge det til at oprette en enhed klient. Erstat **{enhed forbindelsesstrengen}** med den forbindelsesstreng, du har oprettet i afsnittet *oprette en enhed identitet* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Tilføj følgende funktion for at implementere metoden på enheden:

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Åbn forbindelsen til dit IoT hub og start initialiseret lytteren metode:

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Gem og Luk filen **SimulatedDevice.js** .

> [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere retry-politikker (såsom forbindelse forsøg) som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Ringe til en metode på en enhed

I dette afsnit, skal oprette du en Node.js console-app, der kalder en metode på simuleret enheden og derefter viser svaret.

1. Oprette en ny tom mappe med navnet **callmethodondevice**. Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **callmethodondevice** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **callmethodondevice** skal køre følgende kommando for at installere pakken **azure-iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Ved hjælp af en teksteditor, oprette en **CallMethodOnDevice.js** fil i mappen **callmethodondevice** .

4. Tilføj følgende `require` sætninger i starten af filen **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Tilføj følgende variabel erklæring og erstatte værdien pladsholder med forbindelsesstrengen for din IoT hub:

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Oprette klienten for at åbne forbindelsen til din IoT hub.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Tilføj følgende funktion for at kalde metoden enhed og udskrive svaret fra enheden til konsollen:

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Gem og Luk filen **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. Kør følgende kommando for at starte lytter efter metode opkald fra din IoT Hub ved en kommandoprompt i mappen **simulateddevice** :

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. På en kommandoprompt i mappen **callmethodondevice** skal du køre følgende kommando til at starte overvågning IoT centrum:

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Der vises på enheden, reagere på metoden, ved at udskrive meddelelsen og det program, der hedder visningen metode svaret fra enheden:

    ![][9]
    
## <a name="next-steps"></a>Næste trin

Konfigureret en ny IoT hub på portalen Azure i dette selvstudium, og derefter oprettet en enhed identitet i den hub identitet registreringsdatabasen. Du har brugt denne enhed identitet til at aktivere appen simuleret enhed til at reagere på metoder startes af skyen. Du kan også oprettet en app, der aktiverer metoder på enheden og viser svar fra enheden. 

Vil fortsætte med at komme i gang med IoT Hub og udforske andre IoT scenarier, skal du se:

- [Komme i gang med IoT-Hub]
- [Planlægge opgaver på flere enheder][lnk-devguide-jobs]

Hvis du vil se, hvordan du udvider din IoT løsning og tidsplan metode opkald på flere enheder, se [tidsplan og broadcast job] [ lnk-tutorial-jobs] selvstudium.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Sende meddelelser i skyen-til-enhed med IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Komme i gang med IoT-Hub]: iot-hub-node-node-getstarted.md
