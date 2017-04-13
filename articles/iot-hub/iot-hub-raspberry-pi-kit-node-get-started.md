<properties
 pageTitle="Komme i gang med hindbærfyld Pi 3 | Microsoft Azure"
 description="Komme i gang med hindbær Pi 3, oprette Azure IoT centrum og tilslutte din Pi til IoT-hub"
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

# <a name="get-started-with-raspberry-pi-3"></a>Komme i gang med hindbærfyld Pi 3

I dette selvstudium starte du med at lære grundlæggende om arbejde med hindbær Pi 3, der kører Raspbian. Derefter lærer du at problemfrit forbinde dine enheder til skyen med [Azure IoT Hub](iot-hub-what-is-iot-hub.md). Windows 10 IoT Core eksempler, skal du besøge [windowsondevices.com](http://www.windowsondevices.com/).

## <a name="lesson-1-configure-your-device"></a>Lektion 1: Konfigurere din enhed

![Lektion 1 E2E Diagram](media/iot-hub-raspberry-pi-lessons/e2e-lesson1.png)

I denne lektion skal du konfigurere enheden hindbær Pi 3 med et operativsystem, konfigurere din udviklingsmiljø og installere et program til Pi.

### <a name="configure-your-device"></a>Konfigurere din enhed

Konfigurere din hindbær Pi 3 for førstegangsbrug og installere Raspbian OS, et gratis operativsystem, der er optimeret til hindbær Pi hardware.

*Anslået tid at gennemføre: 30 minutter* 

[Gå til 'Konfigurer din enhed'](iot-hub-raspberry-pi-kit-node-lesson1-configure-your-device.md)

### <a name="get-the-tools"></a>Få værktøjerne
Hent de værktøjer og software til at opbygge og anvende dit første program for hindbær Pi 3.

*Anslået tid at gennemføre: 20 minutter* 

[Gå til 'Get værktøjerne'](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)

### <a name="create-and-deploy-the-blink-application"></a>Oprette og installere programmet blink

Klone programmet eksempel Node.js fra Github og gulp for at installere dette program til din hindbær Pi 3 tavle. Dette eksempelprogram blinker LED tilsluttet kortet hvert 2.

*Anslået tid at gennemføre: 5 minutter* 

[Gå til ' Opret og installere programmet blink'](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)

## <a name="lesson-2-create-your-iot-hub"></a>Lektion 2: Oprette din IoT-hub

![Lektion 2 E2E Diagram](media/iot-hub-raspberry-pi-lessons/e2e-lesson2.png)

I denne lektion, du du opretter kontoen gratis Azure, klargøre Azure IoT centrum og oprette din første enhed i Azure IoT hub.

Udfylde Lektion 1, før du starter denne lektion.

### <a name="get-the-azure-tools"></a>Få de Azure værktøjer

Installer Azure kommandolinjen (Azure CLI).

*Anslået tid at gennemføre: 10 minutter* 

[Gå til komme Azure-værktøjer](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)

### <a name="create-your-iot-hub-and-register-your-raspberry-pi-3"></a>Oprette din IoT hub og registrere din hindbær Pi 3

Oprette din ressourcegruppe, klargøre første Azure IoT centrum og tilføje din første enhed til Azure IoT Hub ved hjælp af Azure CLI. 

*Anslået tid at gennemføre: 10 minutter* 

[Gå til "Oprette IoT-hub og registrere din hindbær Pi 3"](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


## <a name="lesson-3-send-device-to-cloud-messages"></a>Lektion 3: Send enhed i skyen meddelelser

![Lektion 3 E2E Diagram](media/iot-hub-raspberry-pi-lessons/e2e-lesson3.png)

I denne lektion sender du meddelelser fra din Pi til IoT-hub. Du kan også oprette en Azure funktionen app, der henter indgående meddelelser fra din IoT hub og skriver dem til Azure-tabellager.

Udføre lektioner 1 og 2, før du starter denne lektion.

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Oprette en Azure funktionen og Azure-lager-konto

Bruge en skabelon til Azure ressourcestyring til at oprette en Azure funktionen app og en Azure-lager-konto.

*Anslået tid at gennemføre: 10 minutter* 

[Gå til 'Opret en Azure funktionen og Azure-lager konto'](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md)

### <a name="run-sample-application-to-send-device-to-cloud-messages"></a>Køre Northwind til at sende enhed i skyen meddelelser

Installere og køre en Northwind til enheden hindbær Pi 3, som sender meddelelser til IoT-hub.

*Anslået tid at gennemføre: 10 minutter* 

[Gå til "Run Northwind til at sende enhed i skyen meddelelser"](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

### <a name="read-messages-persisted-in-azure-storage"></a>Læste meddelelser bevaret i Azure-lager
Overvåge enhed i skyen-meddelelser, som de er skrevet til Azure-lager.

*Anslået tid at gennemføre: 5 minutter* 

[Gå til 'læste meddelelser bevaret i Azure-lager'](iot-hub-raspberry-pi-kit-node-lesson3-read-table-storage.md)


## <a name="lesson-4-send-cloud-to-device-messages"></a>Øvelse 4: Send cloud-til-enhed meddelelser

![Lektion 4 E2E Diagram](media/iot-hub-raspberry-pi-lessons/e2e-lesson4.png)

I denne lektion demos hvordan du kan sende meddelelser fra Azure IoT-hub til din hindbær Pi 3. Meddelelserne styre til og fra funktionsmåden for den Indikator, der er tilsluttet din Pi. Et eksempelprogram er forberedt for dig at opnå denne opgave.

Udfylde lektioner 1, 2 og 3, før du starter denne lektion.

### <a name="run-the-sample-application-to-receive-cloud-to-device-messages"></a>Køre eksempelprogrammet at modtage meddelelser i skyen-til-enhed

Eksempelprogrammet i lektion 4 kører på din Pi og overvåger indgående meddelelser fra din IoT hub. En ny opgave i gulp sender meddelelser til dine Pi fra din IoT hub at blinke blinke.

*Anslået tid at gennemføre: 10 minutter* 

[Gå til "Run eksempel program tilladelse til at modtage meddelelser i skyen-til-enhed"](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)

### <a name="optional-section-change-the-on-and-off-behavior-of-the-led"></a>Valgfri sektion: ændre til og fra funktionsmåde af led-Indikatoren

Tilpasse meddelelser for at ændre Indikatoren til og fra funktionsmåde.

*Anslået tid at gennemføre: 10 minutter* 

[Gå til ' valgfri sektion: ændre til og fra funktionsmåde for LED'](iot-hub-raspberry-pi-kit-node-lesson4-change-led-behavior.md)


## <a name="troubleshooting"></a>Fejlfinding i forbindelse med

Hvis du opfylder alle troubles under lektionerne, kan du søge efter løsninger på denne side.

[Gå til 'Fejlfinding'](iot-hub-raspberry-pi-kit-node-troubleshooting.md)
