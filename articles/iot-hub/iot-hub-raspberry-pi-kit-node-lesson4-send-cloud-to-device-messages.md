<properties
 pageTitle="Køre eksempelprogrammet at modtage meddelelser i skyen-til-enhed | Microsoft Azure"
 description="Eksempelprogrammet i lektion 4 kører på din Pi og overvåger indgående meddelelser fra din IoT hub. En ny opgave i gulp sender meddelelser til dine Pi fra din IoT hub at blinke blinke."
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

# <a name="41-run-the-sample-application-to-receive-cloud-to-device-messages"></a>4.1 køre eksempelprogrammet at modtage meddelelser i skyen-til-enhed

I dette afsnit, skal installere du en eksempelprogrammet på din hindbær Pi 3. Eksempelprogrammet overvåger indgående meddelelser fra din IoT hub. Du kan også køre en gulp opgave på din computer for at sende meddelelser til dine Pi fra IoT centrum. Ved modtagelse af meddelelser, blinker eksempelprogrammet LED. Hvis du opfylder problemer, kan du søge efter løsninger i [foretage fejlfinding af siden](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="411-what-you-will-do"></a>4.1.1 hvad du vil gøre

- Oprette forbindelse eksempelprogrammet til IoT-hub.
- Installere og køre eksempelprogrammet.
- Sende meddelelser fra din IoT hub til din Pi at blinke blinke.

## <a name="412-what-you-will-learn"></a>4.1.2 hvad du lærer

- Sådan overvåge indgående meddelelser fra IoT centrum.
- Sådan sende cloud-til-enhed meddelelser fra din IoT hub til din Pi. 

## <a name="413-what-do-you-need"></a>4.1.3 Hvad har du brug for

- En 3 hindbær Pi, der er konfigureret til brug. Hvis du vil se, hvordan du konfigurerer din Pi, se [Lektion 1: Introduktion til enheden hindbær Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md)
- En IoT hub, der er oprettet i abonnementet Azure. Hvis du vil lære at oprette din Azure IoT Hub, skal du se [lektion 2: oprette din Azure IoT Hub](iot-hub-raspberry-pi-kit-node-get-started.md)

## <a name="414-connect-the-sample-application-to-your-iot-hub"></a>4.1.4 forbinde eksempelprogrammet til IoT-hub

1. Sørg for, at du er i mappen repo `iot-hub-node-raspberrypi-getting-started`. Åbn eksempelprogrammet i Visual Studio-kode ved at køre følgende kommandoer:

    ```bash
    cd Lesson4
    code .
    ```

    Meddelelse om den `app.js` filer i den `app` undermappe. Den `app.js` filen er vigtige kildefilen, der indeholder koden for at overvåge indgående meddelelser fra IoT Hub. Den `blinkLED` funktionen blinker LED.

    ![Repo struktur](media/iot-hub-raspberry-pi-lessons/lesson4/repo_structure.png)

2. Initialiseret konfigurationsfil med følgende kommandoer:

    ```bash
    npm install
    gulp init
    ```

    Hvis du har fuldført lektion 3 på denne computer, nedarves alle konfigurationer, så du kan gå videre til trin 4.1.5. Hvis du har fuldført lektion 3 på en anden computer, skal du erstatte pladsholderne i den `config-raspberrypi.json` fil. Den `config-raspberrypi.json` fil er i en undermappe i mappen privat.

    ![Config](media/iot-hub-raspberry-pi-lessons/lesson4/config_raspberrypi.png)

- Erstat **[enhed værtsnavn eller IP-adresse]** med din Pi IP-adresse eller værtsnavn, som du angiver ved at køre kommandoen`devdisco list --eth`
- Erstat **[IoT enhed forbindelsesstreng]** med forbindelsesstrengen enhed, du får ved at køre kommandoen `az iot hub show-connection-string --name {my hub name} --resource-group {resource group name}`.
- Erstat **[IoT hub forbindelsesstreng]** med IoT hub-forbindelsesstrengen, som du angiver ved at køre kommandoen `az iot device show-connection-string --hub {my hub name} --device-id {device id} --resource-group {resource group name}`.

## <a name="415-deploy-and-run-the-sample-application"></a>4.1.5 installere og køre eksempelprogrammet

Installere og køre eksempelprogrammet på din Pi ved at køre følgende kommandoer:
  
```
gulp
```

Kommandoen gulp kører opgaven Installér værktøjer først. Derefter installerer det eksempelprogrammet til din Pi. Til sidst skal kører det programmet på din Pi og en separat opgave på din computer for at sende 20 blink meddelelser til dine Pi fra IoT centrum.

Når eksempelprogrammet kører, men starter lytter meddelelser fra din IoT hub. I mellemtiden sender opgaven gulp flere "blink" meddelelser fra din IoT Hub til din Pi. For hver af de modtagne meddelelser, blink, kalder eksempelprogrammet funktionen blinkLED for at blinke blinke.

Du bør se LED blinker hver to sekunder, som opgaven gulp skal sende 20 meddelelser fra din IoT hub til din Pi. Den sidste værdi, der er en "stop"-meddelelse, der fortæller programmet stoppe.

![Gulp](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_blink.png)

## <a name="416-summary"></a>4.1.6 oversigt

Du har sendt meddelelser fra din IoT hub til din Pi at blinke blinke. Næste afsnit er valgfri sektion, der viser, hvordan du ændrer til og fra funktionsmåde for LED.

## <a name="next-steps"></a>Næste trin

[Valgfri sektion: ændre til og fra funktionsmåde af led-Indikatoren](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)