<properties
 pageTitle="Valgfrit afsnit – ret til og fra funktionsmåde for LED | Microsoft Azure"
 description="Tilpasse meddelelser for at ændre Indikatoren til og fra funktionsmåde."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="42-optional-section-change-the-on-and-off-behavior-of-the-led"></a>4.2 valgfri sektion: ændre til og fra funktionsmåde af led-Indikatoren

## <a name="421-what-you-will-do"></a>4.2.1 hvad du vil gøre

Tilpasse meddelelser for at ændre Indikatoren til og fra funktionsmåde. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="422-what-you-will-learn"></a>4.2.2 hvad du lærer

Bruge flere Node.js funktioner til at ændre Indikatoren til og fra funktionsmåde.

## <a name="423-what-you-need"></a>4.2.3 hvad du bør

Du skal have gennemført [4.1 Kør en eksempelprogrammet på din hindbær Pi modtage skyen til enhed meddelelser](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md).

## <a name="424-add-nodejs-functions"></a>4.2.4 tilføje Node.js funktioner

1. Åbn eksempelprogrammet i Visual Studio kode ved at køre følgende kommandoer:

    ```bash
    cd Lesson4
    code .
    ```

2. Åbn den `app.js` fil, og derefter tilføje følgende funktioner i slutningen:

    ```javascript
    function turnOnLED() {
      wpi.digitalWrite(CONFIG_PIN, 1);
    }

    function turnOffLED() {
      wpi.digitalWrite(CONFIG_PIN, 0);
    }
    ```

    ![opdatere app.js](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)

3. Tilføj følgende betingelser før standard en i små og store bogstaver Skift blokering af den `receiveMessageCallback` funktionen:

    ```javascript
    case 'on':
      turnOnLED();
      break;
    case 'off':
      turnOffLED();
      break;
    ```

    Du har nu konfigureret eksempel program tilladelse til at besvare flere instruktioner gennem meddelelser. Instruktionerne til "under" slår LED og instruktionerne til "fra" slukker LED.

4. Åbn filen gulpfile.js og derefter tilføje en ny funktion før funktionen `sendMessage`:

    ```javascript
    var buildCustomMessage = function (messageId) {
      if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
        return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
      } else if (messageId < MAX_MESSAGE_COUNT) {
        return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
      } else {
        return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
      }
    }
    ```

    ![opdatere gulpfile](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)

5. I den `sendMessage` fungere, skal du erstatte linjen `var message = buildMessage(sentMessageCount);` med den nye linje, der er vist i følgende kodestykket:

    ```javascript
    var message = buildCustomMessage(sentMessageCount);
    ```

6. Gemme alle ændringer.

### <a name="425-deploy-and-run-the-sample-application"></a>4.2.5 installere og køre eksempelprogrammet

Installere og køre eksempelprogrammet på din Pi ved at køre følgende kommando:

```bash
gulp
```

Du bør se LED slået til i to sekunder, og derefter deaktiveret for en anden to sekunder. Den sidste meddelelse "stop" stopper eksempelprogrammet i at køre.

![til og fra](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

Tillykke! Du tilpasser korrekt de meddelelser, der sendes til Pi fra IoT centrum.

### <a name="427-summary"></a>4.2.7 oversigt over

Valgfri sektion demos Sådan tilpasses meddelelserne, så programmet kan styre til og fra funktionsmåde for LED i en anden måde.

