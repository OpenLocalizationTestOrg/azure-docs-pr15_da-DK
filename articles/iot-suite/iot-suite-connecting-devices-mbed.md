<properties
   pageTitle="Forbinde en enhed med C på mbed | Microsoft Azure"
   description="Beskriver, hvordan du slutter en enhed til Azure IoT pakke forudkonfigureret remote overvågning løsningen ved hjælp af et program, der er skrevet i C, der kører på en mbed enhed."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>Tilslut din enhed til remote overvågning forudkonfigurerede løsningen (mbed)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-c-sample-solution"></a>Oprette og køre C eksempel løsningen

Følgende fremgangsmåde beskriver trinnene til at oprette forbindelse til en [Mbed aktiverede Freescale FRDM-K64F] [ lnk-mbed-home] enhed til remote overvågning løsningen.

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>Slutte mbed enheden til dit netværk og stationære computer

1. Tilslut mbed enhed til dit netværk ved hjælp af Ethernet-kablet. Dette trin er nødvendigt, fordi programmet kræver forbindelse til internettet.

2. Se [Introduktion til mbed] [ lnk-mbed-getstarted] tilsluttes enheden mbed til din PC.

3. Hvis din PC kører Windows, skal du se [PC-konfiguration] [ lnk-mbed-pcconnect] at konfigurere seriel port adgang til din mbed enhed.

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>Oprette et mbed projekt og importere eksempelkoden

1. I din webbrowser skal du gå til det mbed.org [udviklerwebsted](https://developer.mbed.org/). Hvis du ikke har tilmeldt dig, vises der en indstilling for at oprette en konto, (det er gratis). Ellers skal logge på med legitimationsoplysningerne konto. Klik derefter på **compileren** i øverste højre hjørne på siden. Denne handling giver dig til *arbejdsområdet* brugergrænsefladen.

2. Sørg for, at den hardwareplatform, du bruger vises i øverste højre hjørne af vinduet, eller klik på ikonet i højre hjørne for at vælge din hardwareplatform.

3. Klik på **Importér** i hovedmenuen. Klik derefter på **Klik her** for at importere fra URL-adresse til linket ud for mbed globus logoet.

    ![][6]

4. I pop op-vinduet, Angiv linket for eksempel kode https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ og klik derefter på **Importér**.

    ![][7]

5. Du kan se i vinduet mbed compileren, importere dette projekt også importerer forskellige biblioteker. Nogle er angivet og vedligeholdes af Azure IoT teamet ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/) [iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/) [iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), og [azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), mens andre er tredjeparters biblioteker, der er tilgængelige i kataloget mbed biblioteker.

    ![][8]

6. Åbn filen remote_monitoring\remote_monitoring.c, og Find følgende kode i filen:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Erstat [enheds-Id] og [enhed nøgle], med din enhedsdata til at aktivere programmet eksempel at oprette forbindelse til din IoT-hub. Bruge IoT Hub Hostname til at erstatte [IoTHub navn] og [IoTHub suffiks, det vil sige azure-devices.net] pladsholdere. Eksempelvis hvis din IoT Hub Hostname er **contoso.azure devices.net**, er **contoso** **hubName** og alt efter den er **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### <a name="walk-through-the-code"></a>Gennemgå koden

Hvis du er interesseret i, hvordan programmet virker, beskrives i dette afsnit nogle vigtige dele af eksempelkoden. Hvis du blot ønsker at køre koden, kan du gå videre til at [oprette og køre programmet](#buildandrun).

#### <a name="defining-the-model"></a>Definere modellen

Dette eksempel bruger [serialiseringsfunktion] [ lnk-serializer] bibliotek til at definere en model, der angiver de meddelelser, enheden kan sende til IoT Hub og modtage fra IoT Hub. I dette eksempel definerer navneområdet **Contoso** en **indstillet** model, der angiver telemetridata **temperaturen**, **ExternalTemperature**og **fugtighed** sammen med metadata som enheds-id, egenskaber for enheden og de kommandoer, som enheden svarer til:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

Definitioner af kommandoerne **SetTemperature** og **SetHumidity** , enheden svarer til er relateret til modeldefinitionen:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### <a name="connecting-the-model-to-the-library"></a>Oprette forbindelse modellen til biblioteket

Funktioner **sendMessage** og **IoTHubMessage** er dele af et kode for at sende telemetri fra enheden og oprette forbindelse meddelelser fra IoT Hub til kommandoen programmer.

#### <a name="the-remotemonitoringrun-function"></a>Funktionen remote_monitoring_run

**Programmets Hovedformålet** aktiverer funktionen **remote_monitoring_run** , når programmet starter til at udføre enhedens funktionsmåde som en IoT Hub enhed klient. Denne funktion **remote_monitoring_run** består hovedsageligt af indlejrede funktioner:

- **platform\_initialisering** og **platform\_deinit** udføre platform-specifikke initialisering og lukning handlinger.
- **serialiseringsfunktionen\_initialisering** og **serialiseringsfunktion\_deinit** initialisering og deaktivere initialiseret biblioteket serialiseringsfunktion.
- **IoTHubClient\_Opret** og **IoTHubClient\_Destroy** oprettes en klient handle, **iotHubClientHandle**, ved hjælp af enhed legitimationsoplysninger til at oprette forbindelse til din IoT-hub.

I den primære sektion af funktionen **remote_monitoring_run** udfører programmet ved hjælp af **iotHubClientHandle** håndtaget følgende handlinger:

- Opretter en forekomst af Contoso indstillet modellen og konfigurerer meddelelse tilbagekald for de to kommandoer.
- Sender oplysninger om selve enheden, herunder de kommandoer, der understøtter til din IoT-hub, ved hjælp af biblioteket serialiseringsfunktion. Når hubben modtager denne meddelelse, skifter det Enhedsstatus i dashboardet fra **ventende** til at **køre**.
- Starter en **mens** løkke, der sender temperaturen, eksterne temperatur og fugtighed værdier til IoT Hub i sekundet.

Her er en meddelelse om **DeviceInfo** , der er sendt til IoT Hub ved start til reference:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Her er en meddelelse om **Telemetri** , der er sendt til IoT-Hub til reference:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Til reference er her et eksempel, **kommandoen** modtaget fra IoT Hub:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>
### <a name="build-and-run-the-program"></a>Oprette og køre programmet

1. Klik på **samle** for at opbygge programmet. Du kan sikkert Ignorer alle advarsler, men hvis Opret genererer fejl, kan du løse dem, før du fortsætter.

2. Hvis Opret er gået igennem, webstedet mbed compileren genererer en .bin fil med navnet på projektet og henter den til din lokale computer. Kopiere filen .bin til enheden. Gemme filen .bin på enheden bevirker, at kameraet, for at genstarte og køre det program, der er indeholdt i filen .bin. Du kan genstarte programmet manuelt når som helst ved at trykke på nulstillingsknappen på mbed enhed.

3. Oprette forbindelse til den enhed, ved hjælp af en SSH-klientprogrammet, som trykfarver. Du kan bestemme den serielle port enheden bruger ved at markere Windows Enhedshåndtering.

    ![][11]

4. Klik på **seriel** forbindelsestypen i trykfarver. Enheden tilsluttes typisk ved 9600 baud, så Indtast 9600 i feltet **hastighed** . Klik derefter på **Åbn**.

5. Programmet starter udfører. Du kan være nødvendigt at nulstille tavlen (Tryk på CTRL + SKIFT eller på den tavle Nulstil) Hvis programmet ikke startes automatisk, når du opretter forbindelse.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer
