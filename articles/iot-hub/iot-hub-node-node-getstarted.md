<properties
    pageTitle="Azure IoT-Hub til Node.js Introduktion | Microsoft Azure"
    description="Azure IoT hubben med Node.js komme i gang selvstudium. Brug Azure IoT Hub og Node.js med Microsoft Azure IoT SDK'er for at implementere en Internet ting løsning."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Introduktion til Azure IoT Hub til Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

I slutningen af dette selvstudium har du tre Node.js console-programmer:

* **CreateDeviceIdentity.js**, som opretter en enhed identitet og tilknyttede sikkerhed for at oprette forbindelse mellem enheden simuleret.
* **ReadDeviceToCloudMessages.js**, som viser telemetri sendt af enheden simuleret.
* **SimulatedDevice.js**, som opretter forbindelse til din IoT hub med enhed identitet oprettede tidligere, og sender en meddelelse til telemetri hver anden ved hjælp af AMQP protokollen.

> [AZURE.NOTE] I artiklen [IoT Hub SDK'er] [ lnk-hub-sdks] indeholder oplysninger om de forskellige SDK'er, som du kan bruge til at opbygge begge programmer til at køre på enheder og din løsning back-end.

For at fuldføre dette selvstudium skal bruge du følgende:

+ Node.js version 0.10.x eller nyere.

+ En active Azure-konto. (Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Du har nu oprettet IoT centrum. Du har IoT Hub hostname og forbindelsesstrengen IoT-Hub, som du har brug for at fuldføre resten af dette selvstudium.

## <a name="create-a-device-identity"></a>Oprette en enhed identitet

I dette afsnit, skal oprette du en Node.js console-app, der opretter en enhed identitet i identitet registreringsdatabasen i IoT-hub. En enhed kan ikke oprette forbindelse til IoT-hub, medmindre den har en post i registreringsdatabasen enhed identitet. Se afsnittet **Enhed identitet registreringsdatabasen** af [IoT Hub Developer Guide] [ lnk-devguide-identity] kan finde flere oplysninger. Når du kører denne console-program, oprettes der et entydigt enheds-ID og -tasten, enheden kan bruge til at identificere sig, når der udsendes enhed i skyen meddelelser til IoT-Hub.

1. Oprette en ny tom mappe med navnet **createdeviceidentity**. Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **createdeviceidentity** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **createdeviceidentity** , du køre følgende kommando for at installere pakken **azure-iothub** Service SDK:

    ```
    npm install azure-iothub --save
    ```

3. Ved hjælp af en teksteditor, oprette en **CreateDeviceIdentity.js** fil i mappen **createdeviceidentity** .

4. Tilføj følgende `require` sætning i starten af filen **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Tilføje følgende kode i filen **CreateDeviceIdentity.js** , og Erstat værdien pladsholder med forbindelsesstrengen for IoT hubben du oprettede i forrige afsnit: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Tilføj følgende kode for at oprette en definition af enhed i registreringsdatabasen identitet enhed i IoT-hubben. Denne kode opretter en enhed, hvis enheds-id ikke findes i registreringsdatabasen, ellers returneres nøglen i den eksisterende enhed:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Gem og Luk **CreateDeviceIdentity.js** fil.

8. Hvis du vil køre programmet **createdeviceidentity** , skal du udføre følgende kommando kommandoprompten i mappen createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Skal du notere af **enheds-id** og **nøglen enhed**. Du bruge disse værdier senere, når du opretter et program, der opretter forbindelse til IoT Hub som en enhed.

> [AZURE.NOTE] IoT Hub identitet registreringsdatabasen gemmer kun enhed identiteter for at aktivere sikker adgang til den pågældende hub. Det gemmer enheds-id'er og taster, der bruges som sikkerhedslegitimationsoplysninger og en aktiveret/deaktiveret flag, du kan bruge til at deaktivere adgang til en enkelt enhed. Hvis programmet skal gemme andre enhed-specifikke metadata, skal der bruges et program-specifikke store. Du [IoT Hub Developer Guide] [ lnk-devguide-identity] kan finde flere oplysninger.

## <a name="receive-device-to-cloud-messages"></a>Modtage enhed i skyen meddelelser

I dette afsnit, skal oprette du en Node.js console-app, som læser enhed i skyen meddelelser fra IoT Hub. En IoT hub Fremviser en [Begivenhed Hubs][lnk-event-hubs-overview]-kompatible slutpunkt til gør det muligt at læse enhed i skyen meddelelser. Hvis du vil bevare ting enkel, opretter dette selvstudium en grundlæggende læser, ikke der passer til en høj overførselshastighed installation. [Behandle enhed i skyen meddelelser] [ lnk-process-d2c-tutorial] selvstudium viser, hvordan du behandle enhed i skyen meddelelser skaleres til brug. [Introduktion til begivenhed Hubs] [ lnk-eventhubs-tutorial] selvstudium indeholder yderligere oplysninger om, hvordan at behandle meddelelser fra begivenhed Hubs og er gældende for IoT Hub begivenhed Hub-kompatible slutpunkterne.

> [AZURE.NOTE] Begivenhed Hub-kompatible slutpunkt for at læse enhed i skyen meddelelser altid bruger AMQP-protokollen.

1. Oprette en ny tom mappe med navnet **readdevicetocloudmessages**. Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **readdevicetocloudmessages** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **readdevicetocloudmessages** skal køre følgende kommando for at installere **azure-begivenhed-hubs** pakken:

    ```
    npm install azure-event-hubs --save
    ```

3. Ved hjælp af en teksteditor, oprette en **ReadDeviceToCloudMessages.js** fil i mappen **readdevicetocloudmessages** .

4. Tilføj følgende `require` sætninger i starten af filen **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Tilføj følgende variabel erklæring og erstatte værdien pladsholder med forbindelsesstrengen for din IoT hub:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Tilføj følgende to funktioner, der udskrives output til konsollen:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Tilføj følgende kode for at oprette **EventHubClient**, Åbn forbindelsen til dit IoT-Hub, og Opret en modtager for hver partition. Dette program anvender et filter, når der oprettes en modtager, så modtageren læser kun meddelelser, der sendes til IoT Hub, når modtageren begynder at køre. Dette filter er nyttig i et testmiljø, så du kan se det aktuelle sæt af meddelelser. I et produktionsmiljø din kode bør sørge for, at det behandler alle meddelelserne - se, [hvordan du behandle IoT Hub enhed i skyen meddelelser] [ lnk-process-d2c-tutorial] selvstudium kan finde flere oplysninger:

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Gem og Luk filen **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Oprette en simuleret enhed app

I dette afsnit, skal oprette du en Node.js console-app, der simulerer en enhed, som sender enhed i skyen meddelelser til en IoT-hub.

1. Oprette en ny tom mappe med navnet **simulateddevice**. Oprette en package.json fil ved hjælp af følgende kommando i din kommandoprompt i mappen **simulateddevice** . Accepter alle standardværdier:

    ```
    npm init
    ```

2. På din kommandoprompt i mappen **simulateddevice** skal køre følgende kommando for at installere den **azure-iot-enhed** enhed SDK pakke og **azure-iot-enhed – amqp** pakke:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Ved hjælp af en teksteditor, oprette en ny **SimulatedDevice.js** -fil i mappen **simulateddevice** .

4. Tilføj følgende `require` sætninger i starten af filen **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Tilføj en **connectionString** variabel og bruge det til at oprette en enhed klient. Erstatte **{youriothostname}** med navnet på IoT hub du har oprettet sektionen *Opret en IoT Hub* . Erstat **{yourdevicekey}** med den enhed nøgleværdi, du har oprettet i afsnittet *oprette en enhed identitet* :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Tilføj følgende funktion for at få vist output fra programmet på computeren:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Oprette et tilbagekald og bruge funktionen **setInterval** til at sende en ny meddelelse til din IoT hub hver andet:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

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

8. Åbn forbindelsen til dit IoT-Hub, og begynd at sende meddelelser:

    ```
    client.open(connectCallback);
    ```

9. Gem og Luk filen **SimulatedDevice.js** .

> [AZURE.NOTE] Dette selvstudium implementerer for at holde ting enkel, ikke en politik for prøv igen. Fremstilling kode, skal du implementere forsøg politikker (som en eksponentiel backoff), som foreslåede i MSDN-artikel [Midlertidige håndtering af fejl][lnk-transient-faults].


## <a name="run-the-applications"></a>Køre programmer

Du er nu klar til at køre programmerne.

1. På en kommandoprompt i mappen **readdevicetocloudmessages** skal du køre følgende kommando til at starte overvågning IoT centrum:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Node.js IoT Hub service-klientprogrammet til at overvåge enhed i skyen meddelelser][7]

2. På en kommandoprompt i mappen **simulateddevice** skal køre følgende kommando for at begynde at sende telemetridata til IoT-hub:

    ```
    node SimulatedDevice.js
    ```

    ![Node.js IoT Hub enhed klientprogrammet og sende meddelelser enhed i skyen][8]

3. Feltet **brugen** i [Azure portal] [ lnk-portal] viser antallet af meddelelser, der sendes til hubben:

    ![Azure portalen brugen felt viser antallet af meddelelser, der sendes til IoT-Hub][43]

## <a name="next-steps"></a>Næste trin

Konfigureret en ny IoT hub på portalen Azure i dette selvstudium, og derefter oprettet en enhed identitet i den hub identitet registreringsdatabasen. Du har brugt denne enhed identitet til at aktivere appen simuleret enhed til at sende enhed i skyen meddelelser til den pågældende hub. Du kan også oprettet en app, der viser de meddelelser, der modtages af hubben. 

Vil fortsætte med at komme i gang med IoT Hub og udforske andre IoT scenarier, skal du se:

- [Oprette forbindelse til din enhed][lnk-connect-device]
- [Kom godt i gang med administration af enheder][lnk-device-management]
- [Introduktion til IoT Gateway SDK][lnk-gateway-SDK]

Få vist [proces enhed i skyen meddelelser] for at lære at udvide din IoT løsning og behandle enhed i skyen meddelelser skaleres,[ lnk-process-d2c-tutorial] selvstudium.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
