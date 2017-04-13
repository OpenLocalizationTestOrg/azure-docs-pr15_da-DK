<properties
   pageTitle="Forbinde en enhed med C på Windows | Microsoft Azure"
   description="I denne artikel beskrives, hvordan du knytter en enhed til Azure IoT pakke forudkonfigureret remote overvågning løsningen ved hjælp af et program, der er skrevet i C, der kører på Windows."
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


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>Tilslut din enhed til remote overvågning forudkonfigurerede løsningen (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>Oprette en C eksempel løsning i Windows

Følgende trin viser, hvordan du bruger Visual Studio til at oprette en klientprogrammet, der er skrevet i C, der kommunikerer med løsningen Remote overvågning forudkonfigureret.

Oprette et starter projekt i Visual Studio-2015 og tilføje IoT Hub enhed klient NuGet pakker:

1. Oprette et C console-program ved hjælp af skabelonen Visual C++ **Win32-konsollen program** i Visual Studio-2015. Navngive projektet **RMDevice**.

2. Sikre, at **Console program** er markeret, og fjern markeringen i **Precompiled sidehoved** og **sikkerhed udvikling livscyklus (SDL) kontrollerer**på siden **Indstillinger for programmer** i **Win32-program guiden**.

3. I **Solution Explorer**skal du slette filer stdafx.h, targetver.h og stdafx.cpp.

4. Omdøb filen RMDevice.cpp til RMDevice.c i **Solution Explorer**.

5. I **Solution Explorer**, skal du højreklikke på **RMDevice** projektet, og klik derefter på **Administrer NuGet pakker**. Klik på **Gennemse**, og derefter søge efter og installere de følgende NuGet-pakker til projektet:

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

6. Højreklik på **RMDevice** projektet i **Solution Explorer**, og klik derefter på **Egenskaber** for at åbne dialogboksen til projektets **Egenskabssider** . Yderligere oplysninger finder du [Indstillingen Visual C++ Projektegenskaber][lnk-c-project-properties]. 

7. Klik på mappen **linkfaciliteten** , og klik derefter på siden **Input** egenskab.

8. Føje **crypt32.lib** til egenskaben **Yderligere afhængigheder** . Du kan klikke på **OK** og derefter på **OK** igen for at gemme projektet egenskabsværdier.

## <a name="specify-the-behavior-of-the-iot-hub-device"></a>Angive funktionsmåden for IoT Hub-enhed

IoT Hub klientbiblioteker Brug en model til at angive formatet for de meddelelser, der sender enheden til IoT Hub og de kommandoer, den modtager fra IoT Hub.

1. Åbn filen RMDevice.c i Visual Studio. Erstatte den nuværende `#include` sætninger med følgende kode:

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. Tilføj følgende variabelerklæringer efter den `#include` sætninger. Erstatte pladsholder værdier [enheds-Id] og [enhed nøgle] med værdier til din enhed fra dashboardet remote overvågning løsning. Bruge IoT Hub Hostname dashboard til at erstatte [IoTHub navn]. Eksempelvis hvis din IoT Hub Hostname er **contoso.azure devices.net**, Erstat [navn på IoTHub] med **contoso**:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. Tilføj følgende kode for at definere den model, der gør det muligt for kameraet, for at kommunikere med IoT Hub. Denne model angiver, at enheden sender temperaturen, eksterne temperatur, fugtighed og et enheds-id, som telemetri. Enheden sender også metadata om enheden til IoT hubben, herunder en liste over kommandoer, der understøtter enheden. Denne enhed svarer til kommandoerne **SetTemperature** og **SetHumidity**:

    ```
    // Define the Model
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

## <a name="implement-the-behavior-of-the-device"></a>Implementere funktionsmåden for enheden

Tilføj nu kode, der implementerer den funktionsmåde, der er defineret i modellen.

1. Tilføj følgende funktioner, der udføres, når enheden modtager kommandoerne **SetTemperature** og **SetHumidity** fra IoT Hub:

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

2. Tilføj følgende funktion, der sender en meddelelse til IoT Hub:

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. Tilføj følgende funktion, der henter serialisering biblioteket i SDK:

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. Tilføj følgende funktion for at oprette forbindelse til IoT-Hub, sende og modtage meddelelser og Afbryd hubben. Bemærk, hvordan enheden sender metadata om sig selv, herunder de kommandoer, der understøtter til IoT-Hub, når der oprettes forbindelse. Disse metadata gør det muligt for løsningen til at opdatere status for enheden til **kører** på dashboardet til:

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
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

5. Erstat **Hovedformålet** med følgende kode til at aktivere funktionen **remote_monitoring_run** :

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. Klik på **Opret** og derefter **Opbygge løsning** for at opbygge programmet enhed.

7. I **Solution Explorer**, højreklik på **RMDevice** projektet, skal du klikke på **fejlfinding**, og klik derefter på **Start ny forekomst** for at køre eksemplet. Konsollen viser meddelelser, som programmet sender eksempel telemetri til IoT Hub og modtager kommandoer.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx