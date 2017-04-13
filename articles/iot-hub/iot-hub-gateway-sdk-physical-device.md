<properties
    pageTitle="Bruge en reelle enhed med IoT Gateway SDK | Microsoft Azure"
    description="Azure IoT Gateway SDK gennemgang ved hjælp af en Texas værktøj SensorTag enhed til at sende data til IoT Hub gennem en gateway, der kører på et Intel Edison beregne modul"
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-real-device-using-linux"></a>Azure IoT Gateway SDK (beta) – sende enhed i skyen meddelelser med et reelt tal ved hjælp af Linux-enhed

Denne gennemgang [Bluetooth lav energi eksempel] [ lnk-ble-samplecode] viser, hvordan du bruger [Azure IoT Gateway SDK] [ lnk-sdk] at videresende enhed i skyen telemetri til IoT Hub fra en fysisk enhed, og hvordan du omdirigere kommandoer fra IoT Hub til en fysisk enhed.

Denne gennemgang omfatter:

* **Arkitektur**: vigtige arkitektonisk oplysninger om Bluetooth lav energi eksempel.

* **Opret og kør**: trinnene til at oprette og køre eksemplet.

## <a name="architecture"></a>Arkitektur

Denne gennemgang viser, hvordan du oprette og køre en IoT Gateway på en Intel Edison beregne modul, der kører Linux. Gatewayen er oprettet ved hjælp af IoT Gateway SDK. Eksemplet bruger en enhed med Texas værktøj SensorTag Bluetooth lav energi (BLE) til at indsamle temperaturdata.

Når du kører gatewayen det:

- Opretter forbindelse til en SensorTag enhed ved hjælp af Bluetooth lav energi (BLE)-protokollen.
- Opretter forbindelse til IoT Hub ved hjælp af HTTP-protokollen.
- Videresender telemetri fra enheden SensorTag til IoT Hub.
- Dirigerer kommandoer fra IoT Hub på SensorTag enheden.

Gatewayen indeholder følgende moduler:

- Et *BLE modul* , der fungerer sammen med en BLE enhed til at modtage temperaturdata fra enheden, og send kommandoer på enheden.
- En *Enhedsmodul BLE sky* , der oversætter JSON meddelelser fra skyen i BLE instruktioner til *BLE modul*.
- Et *logføring modul* , der logfører alle gateway-meddelelser.
- En *tilknytning af brugeridentitet modul* , der oversætter mellem BLE enhed MAC-adresser og Azure IoT Hub enhed identiteter.
- En *IoT Hub modul* , der overfører telemetridata til en IoT-hub og modtager enhed kommandoer fra en IoT hub.
- Et *BLE printer modul* , der fortolker telemetri fra BLE enheden og udskriver formaterede data til console for at aktivere fejlfinding og fejlfinding.

### <a name="how-data-flows-through-the-gateway"></a>Hvordan data bevæger sig gennem gatewayen

Den følgende blokdiagram illustrerer telemetri Overfør data flow pipeline:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

De trin, som et element med telemetri tager rejse fra en enhed, BLE til IoT Hub er:

1. BLE enheden genererer en temperaturen stikprøve og sender den via Bluetooth til modulet BLE i gatewayen.
2. Modulet BLE modtager stikprøvernes og udgiver den til broker sammen med MAC-adressen på enheden.
3. Modulet identitet tilknytning opfanger denne meddelelse og bruger en intern tabel til at oversætte MAC-adressen på enheden til en IoT Hub enhed identitet (enheds-ID og nøglen enhed). Det derefter publicerer en ny meddelelse, der indeholder eksempeldataene temperaturen, MAC-adressen for enheden, enheds-ID og tasten enhed.
4. Modulet IoT Hub modtager denne nye meddelelse (genereres af modulet identitet tilknytning) og publicerer til IoT Hub.
5. Modulet logføring logfører alle meddelelser fra broker til en diskfil.

Den følgende blokdiagram illustrerer enhed kommandoen data flow pipeline:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. Modulet IoT Hub undersøger med jævne mellemrum IoT-hub til nye meddelelser i kommandoen.
2. Når modulet IoT Hub modtager en ny kommandomeddelelse, udgiver den til broker.
3. Modulet identitet tilknytning henter kommandoen meddelelsen og bruger en intern tabel til at oversætte IoT Hub enheds-ID til en enhed MAC-adresse. Det derefter publicerer en ny meddelelse, som indeholder MAC-adressen for destinationen enhed i Egenskaber for kortet i meddelelsen.
4. BLE skyen til enhedsmodul henter denne meddelelse og oversætter det til den korrekte BLE instruktion for modulet BLE. Det derefter publicerer en ny meddelelse.
5. Modulet BLE henter denne meddelelse og afvikler i/o-instruktion ved at kommunikere med BLE enhed.
6. Modulet logføring logfører alle meddelelser fra broker til en diskfil.

## <a name="prepare-your-hardware"></a>Forberede din hardware

Dette selvstudium, antages det, du bruger en [Texas værktøj SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) enhed har forbindelse til en Intel Edison tavle.

### <a name="set-up-the-edison-board"></a>Konfigurere Edison tavlen

Før du går i gang, skal du sikre dig, kan du oprette forbindelse enheden Edison til trådløst netværk. Hvis du vil konfigurere enheden Edison, skal du slutte den til en host computer. Intel indeholder Introduktion vejledninger til følgende operativsystemer:

- [Komme i gang med Intel Edison udvikling tavle på Windows 64-bit][lnk-setup-win64].
- [Komme i gang med Intel Edison udvikling tavle på Windows 32-bit][lnk-setup-win32].
- [Komme i gang med Intel Edison udvikling tavle på Mac OS X][lnk-setup-osx].
- [Introduktion til Intel® Edison tavle på Linux][lnk-setup-linux].

Hvis du vil konfigurere enheden Edison og blive fortrolig med det, du skal fuldføre alle trin i disse "Introduktion" artikler med undtagelse af det sidste trin, "Vælg IDE", som er unødvendige for det aktuelle selvstudium. I slutningen af konfigurationen af Edison har du:

- Blinkede din Edison med den nyeste firmware.
- Oprettet en seriel forbindelse fra din vært til Edison.
- Kør scriptet **configure_edison** for at angive en adgangskode og aktivere WiFi på din Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Aktivere forbindelsen til enheden SensorTag fra din Edison tavle

Før du kører eksemplet, skal du bekræfte, at din Edison tavle kan oprette forbindelse til SensorTag enheden.

Først skal du bekræfte, at din Edison kan oprette forbindelse til SensorTag enheden.

1. Fjerne blokeringen af bluetooth på Edison, og Kontrollér, at versionsnummeret vises **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Udføre kommandoen **bluetoothctl** . Du er nu i en interaktiv bluetooth shell. 

3. Angiv kommandoen **power på** Tilslut bluetooth-enhed. Du burde se output, der svarer til:
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

4. Mens du stadig er i shell interaktive bluetooth Angiv den kommando **scanne på** til at søge efter Bluetooth-enheder. Du burde se output, der svarer til:
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

5. Gøre SensorTag enheden synlig ved at trykke på den lille knap (den grønne LED skal flash). Edison skal finde SensorTag enhed:
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    I dette eksempel kan du se, MAC-adressen for SensorTag enheden er **A0:E6:F8:B5:F6:00**.

6. Slå fra scanning ved at angive kommandoen **scanne fra** .
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

7. Oprette forbindelse til enheden SensorTag ved hjælp af MAC-adressen ved at angive **forbinde \<MAC-adresse >**. Bemærk, at et eksempel på output nedenfor er forkortet:
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Bemærk: Du kan få vist GATT egenskaber for enheden igen ved hjælp af kommandoen **liste attributter** .

8. Du kan nu fjernes fra enheden ved hjælp af kommandoen **afbryde forbindelsen** og afslut derefter fra bluetooth shell ved hjælp af kommandoen **Afslut** :
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Nu er du klar til at køre eksemplet BLE Gateway på enheden Edison.

## <a name="run-the-ble-gateway-sample"></a>Køre BLE Gateway-eksempel

Hvis du vil køre BLE eksempel på din Edison, skal du udføre tre opgaver:

- Konfigurere to eksempel enhederne i din IoT Hub.
- Opbygge IoT Gateway SDK på enheden Edison.
- Konfigurere og køre eksemplet BLE på enheden Edison.

På tidspunktet for skrivning understøtter IoT Gateway SDK kun gateways, der bruger BLE moduler på Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Konfigurere to eksempel enheder i din IoT Hub

- [Oprette en IoT hub] [ lnk-create-hub] i dit Azure-abonnement, skal du navnet på din hub til at udføre denne gennemgang. Hvis du ikke har en konto, kan du oprette en [gratis konto] [ lnk-free-trial] på blot et par minutter.
- Tilføj en enhed, som kaldes **SensorTag_01** til IoT-hub, og notere-id og enhed nøglen. Du kan bruge [enhed Stifinder eller iothub explorer] [ lnk-explorer-tools] værktøjer til at føje denne enhed til IoT hubben du oprettede i forrige trin og til at hente nøglen. Du knytte denne enhed til SensorTag enheden, når du konfigurerer gatewayen.

### <a name="build-the-iot-gateway-sdk-on-your-edison-device"></a>Opbygge IoT Gateway SDK på enheden Edison

Versionen af **ciffer** på Edsion understøtter ikke submodules. For at hente den fulde kilde til IoT Gateway SDK til Edison har du to muligheder:

- Indstillingen #1: Klone [Azure IoT Gateway SDK] [ lnk-sdk] lager på din Edison og derefter manuelt klone lager for hver submodule.
- Indstillingen #2: Klone [Azure IoT Gateway SDK] [ lnk-sdk] lager på en stationær enhed hvor **ciffer** understøtter submodules og derefter kopiere den komplette lager med submodules til din Edison.

Hvis du vælger indstillingen #2, kan du bruge følgende **ciffer** kommandoer til at klone IoT Gateway SDK og alle dens submodules:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Du skal derefter zip hele lokale lager til en enkelt arkivfilen, før du kopierer det til Edison. Du kan bruge et værktøj som **pscp** der følger med **trykfarver** til at kopiere arkivfilen til Edison. Eksempel:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Når du har en fuldstændig kopi af IoT Gateway SDK-lager på din Edison, kan du oprette den med følgende kommando fra den mappe, der indeholder SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Konfigurere og køre eksemplet BLE på enheden Edison

Hvis du vil initialiseres og køre eksemplet, skal du konfigurere hvert modul, der deltager i gatewayen. Denne konfiguration er angivet i filen JSON, og du skal konfigurere alle fem deltagende moduler. Der er et eksempel JSON-fil, der er angivet i lageret kaldet **gateway_sample.json** , som du kan bruge som udgangspunkt for bygge din egen konfigurationsfil. Denne fil er i mappen **eksempler/ble_gateway_hl/src** i lokal kopi af IoT Gateway SDK-lager.

I nedenstående afsnit beskrives, hvordan du redigerer denne konfigurationsfil til BLE eksempel og antager, at IoT Gateway SDK lager er i den **/home/root/azure-iot-gateway-sdk /** mappe på enheden Edison. Hvis lageret er et andet sted, skal du justere stierne i overensstemmelse hermed:

#### <a name="logger-configuration"></a>Konfiguration af logføring

Hvis du allerede har gateway-lager er placeret i mappen **/home/root/azure-iot-gateway-sdk /**, konfigurere modulet logføring på følgende måde:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>BLE modul konfiguration

Eksempel konfigurationen for enheden BLE antages, at en Texas værktøj SensorTag enhed. Standard BLE enheder, der kan fungere som en ydre GATT skal fungere, men du skal opdatere GATT karakteristisk id'er og data (for at skrive instruktioner). Tilføje MAC-adressen for enheden SensorTag: 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>IoT Hub modul

Tilføj navnet på din IoT Hub. Værdien suffiks er typisk **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Identitet tilknytning modul konfiguration

Tilføj MAC-adressen for enheden SensorTag og enheds-Id og tast til den **SensorTag_01** enhed, du har føjet til din IoT Hub:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>BLE Printer modul konfiguration

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Konfigurationen af routing

Følgende konfiguration sikrer, at følgende:
- Modulet **logføring** modtager, og log alle meddelelser.
- Modulet **SensorTag** sender meddelelser til både **tilknytning** og **BLE Printer** moduler.
- Modulet **tilknytning** sender meddelelser til modulet **IoTHub** skal sendes til IoT centrum.
- Modulet **IoTHub** sender meddelelser tilbage til modulet **tilknytning** .
- Modulet **tilknytning** sender meddelelser tilbage til modulet **SensorTag** .

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Hvis du vil køre stikprøvernes køre du **ble_gateway_hl** binære der passerer stien til filen JSON konfiguration. Hvis du har brugt filen **gateway_sample.json** , ser kommandoen til at udføre sådan ud:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Du skal muligvis trykke på den lille knap på SensorTag kameraet, for at gøre den synlig, før du kører stikprøvernes.

Når du kører eksemplet, kan du bruge [enhed Stifinder eller iothub explorer] [ lnk-explorer-tools] værktøj til at overvåge meddelelserne gatewayen videresender fra SensorTag enheden.

## <a name="send-cloud-to-device-messages"></a>Sende meddelelser i skyen-til-enhed

Modulet BLE understøtter også afsendelse instruktioner fra Azure IoT Hub på enheden. Du kan bruge [Azure IoT Hub enhed Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) eller [IoT Hub Explorer](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) til at sende JSON-meddelelser, der modulet BLE gateway overfører til BLE enhed. Eksempelvis hvis du bruger Texas værktøj SensorTag enhed kan derefter du sende følgende JSON meddelelser på enheden fra IoT Hub.

- Nulstille alle indikatorer og brummer (slå dem fra)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Konfigurere I/O som 'remote'

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Slå den røde LED

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Slå den grønne LED

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Slå brummer

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

Standardindstillingen for en enhed med HTTP-protokollen til at oprette forbindelse til IoT Hub er at kontrollere hver 25 minutter til en ny kommando. Hvis du sender flere separate kommandoer skal du derfor 25 minutter til enheden for at modtage hver kommando.

> [AZURE.NOTE] Gatewayen kontrollerer også for nye kommandoer, når det startes, så du kan gennemtvinge det til at behandle en kommando ved at stoppe og starte gatewayen.

## <a name="next-steps"></a>Næste trin

Hvis du vil opnå en mere avanceret forståelse af IoT Gateway SDK og eksperimentere med nogle kodeeksempler, skal du besøge følgende udvikler selvstudier og ressourcer:

- [Azure IoT Gateway SDK][lnk-sdk]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
