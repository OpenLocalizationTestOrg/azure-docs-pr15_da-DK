<properties
    pageTitle="Sende meddelelser i skyen-til-enhed med IoT Hub | Microsoft Azure"
    description="Følge dette selvstudium for at lære at sende cloud-til-enhed meddelelser ved hjælp af Azure IoT Hub med Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Selvstudium: Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub og Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introduktion

Azure IoT Hub er en komplet administreret tjeneste, der hjælper med at aktivere pålidelig og sikker tovejskommunikation mellem millioner af IoT enheder og afslutte et program igen. [Komme i gang med IoT Hub] selvstudiet viser, hvordan du oprette en IoT-hub, klargøre en enhed identitet i den og kode en simuleret enhed, som sender enhed i skyen meddelelser.

Dette selvstudium bygger på [Kom i gang med IoT Hub]. Det kan du se hvordan til:

- Sende cloud-til-enhed meddelelser til en enkelt enhed via IoT Hub fra dit program skyen back-end.
- Modtage cloud-til-enhed meddelelser på en enhed.
- Fra dit program skyen slutningen, skal du anmode om levering bekræftelse (*feedback*) for meddelelser, der sendes til en enhed fra IoT Hub.

Du kan finde flere oplysninger i de meddelelser, cloud-til-enhed i [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

I slutningen af dette selvstudium skal køre du to Node.js console-programmer:

* **SimulatedDevice**, en ændret version af appen, der er oprettet i [komme i gang med IoT Hub], som opretter forbindelse til din IoT hub og modtager meddelelser i skyen-til-enhed.
* **SendCloudToDeviceMessage**, der sender meddelelsen cloud-til-enhed til den simulerede enhed via IoT-Hub, og derefter modtager dens levering bekræftelse.

> [AZURE.NOTE] IoT Hub har SDK understøttelse af mange Enhedsplatforme og sprog (herunder C, Java og Javascript) via Azure IoT enhed SDK'er. Du kan finde en trinvis vejledning i at tilslutte enheden til dette selvstudium kode og generelt til Azure IoT Hub [Azure IoT Developer Center].

For at fuldføre dette selvstudium skal bruge du følgende:

+ Node.js version 0.10.x eller nyere.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

## <a name="receive-messages-on-the-simulated-device"></a>Modtage meddelelser på simuleret enheden

I dette afsnit, skal du ændre programmet simuleret enhed du har oprettet i [komme i gang med IoT Hub] for at modtage cloud-til-enhed meddelelser fra IoT-hubben.

1. Ved hjælp af en teksteditor, Åbn filen SimulatedDevice.js.

2. Rediger funktionen **connectCallback** for at håndtere meddelelser, der sendes fra IoT Hub. I dette eksempel aktiverer enheden altid funktionen **udført** for at fortælle IoT-Hub, at den har behandlet meddelelsen. Den nye version af funktionen **connectCallback** ser sådan ud:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

    > [AZURE.NOTE] Hvis du bruger HTTP i stedet for MQTT eller AMQP som transport, kontrollerer **DeviceClient** forekomst for meddelelser fra IoT Hub sjældent (mindre end hver 25 minutter). Du kan finde flere oplysninger om forskellene mellem MQTT, AMQP og HTTP support og IoT Hub (throttling) se [IoT Hub Developer Guide][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Sende en meddelelse i skyen-til-enhed

I dette afsnit, skal oprette du en Node.js console-app, som sender meddelelser til skyen-til-enhed til appen simuleret enhed. Du skal bruge enheden-id'et for den enhed, du har tilføjet i [komme i gang med IoT Hub] selvstudiet. Du kan også bruge forbindelsesstrengen til din IoT-hub, som du kan finde i [Azure-portalen].

1. Oprette en tom mappe kaldet **sendcloudtodevicemessage**. Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **sendcloudtodevicemessage** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **sendcloudtodevicemessage** skal køre følgende kommando for at installere pakken **azure-iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **SendCloudToDeviceMessage.js** -fil i mappen **sendcloudtodevicemessage** .

4. Tilføj følgende `require` sætninger i starten af filen **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Tilføj følgende kode til **SendCloudToDeviceMessage.js** fil. Erstat den forbindelse strengværdi pladsholder med forbindelsesstrengen for IoT hubben du oprettede i selvstudium i [komme i gang med IoT Hub] . Erstat pladsholderen target enhed med den enhed, du har tilføjet i [komme i gang med IoT Hub] selvstudiet enheds-id:

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Tilføj følgende funktion for at udskrive handlingen resultater til konsollen:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Tilføj følgende funktion for at udskrive levering feedback meddelelser til konsollen:

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Føj følgende kode for at sende en meddelelse til din enhed og håndtere feedback meddelelsen, når enheden accepterer meddelelsen cloud-til-enhed:

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Gem og Luk **SendCloudToDeviceMessage.js** fil.

## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. Kommandoprompten i mappen **simulateddevice** , skal du køre følgende kommando for at sende telemetri til IoT Hub og lytte til skyen-til-enhed meddelelser:

    ```
    node SimulatedDevice.js 
    ```

    ![Køre appen simuleret enhed][img-simulated-device]

2. Kør følgende kommando for at sende en meddelelse i skyen-til-enhed, og vent til bekræftelse feedback på en kommandoprompt i mappen **sendcloudtodevicemessage** :

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Køre appen for at sende kommandoen c2d][img-send-command]

    > [AZURE.NOTE] Dette selvstudium implementerer ikke nogen forsøg politik for enkels risvin. Fremstilling kode, skal du implementere forsøg politikker (som eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl].

## <a name="next-steps"></a>Næste trin

I dette selvstudium, du har lært hvordan du kan sende og modtage meddelelser i skyen-til-enhed. 

For at se eksempler på komplette til slut-løsninger, der bruger IoT-Hub skal du se [Azure IoT pakke].

Hvis du vil vide mere om udvikling af løsninger med IoT Hub skal du se [IoT Hub Developer Guide].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Komme i gang med IoT-Hub]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[IoT Hub Developer Guide]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Håndtering af midlertidige fejl]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-portalen]: https://portal.azure.com
[Azure IoT pakke]: https://azure.microsoft.com/documentation/suites/iot-suite/