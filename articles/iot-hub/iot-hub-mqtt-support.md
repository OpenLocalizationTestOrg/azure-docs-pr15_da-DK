<properties
 pageTitle="Understøttelse af IoT Hub MQTT | Microsoft Azure"
 description="Beskrivelse af MQTT support i IoT hub-niveau"
 services="iot-hub"
 documentationCenter=".net"
 authors="kdotchkoff"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/24/2016"
 ms.author="kdotchko"/>

# <a name="iot-hub-mqtt-support"></a>IoT Hub MQTT support

IoT Hub gør det muligt for enheder til at kommunikere med IoT Hub enhed slutpunkter ved hjælp af [MQTT v3.1.1] [ lnk-mqtt-org] protocol på port 8883 eller MQTT v3.1.1 over WebSocket protokollen om port 443. IoT Hub kræver, at alle enhedskommunikation skal være sikker ved hjælp af TLS/SSL (det vil sige, IoT Hub ikke understøtter ikke sikre forbindelser over port 1883).

## <a name="connecting-to-iot-hub"></a>Oprette forbindelse til IoT-Hub

En enhed kan bruge MQTT protokollen til at oprette forbindelse til en IoT hub ved hjælp af biblioteker på [Microsoft Azure IoT SDK'er] [ lnk-device-sdks] eller ved hjælp af MQTT protokol direkte.

## <a name="using-the-device-client-sdks"></a>Ved hjælp af enhed klienten SDK'er

[Enhed klient SDK'er] [ lnk-device-sdks] , der understøtter MQTT protokollen er tilgængelige for Java, Node.js, C, C# og Python. Enhed klienten SDK'er Brug standard IoT Hub forbindelsesstrengen til at oprette forbindelse til en IoT hub. Hvis du vil bruge MQTT protokollen, skal parameteren klient protocol angives til **MQTT**. Som standard enhed klienten SDK'er oprette forbindelse til en IoT Hub med **CleanSession** flag er indstillet til **0** og bruge **QoS 1** til udveksling af meddelelser med IoT-hub.

Når en enhed er forbundet til en IoT-hub, giver enhed klienten SDK'er metoder, der aktiverer kameraet, for at sende meddelelser til og modtage meddelelser fra en IoT hub.

Den følgende tabel indeholder links til kodeeksempler for hvert understøttede sprog og angiver parameteren skal bruges til at oprette forbindelse til IoT Hub ved hjælp af MQTT-protokollen.

| Sprog                   | Protocol parameter        |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | Azure-iot-enhed – mqtt     |
| [Java][lnk-sample-java]    | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c]          | MQTT_Protocol             |
| [C#][lnk-sample-csharp]    | TransportType.Mqtt        |
| [Python][lnk-sample-python] | IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Overføre en enhed app fra AMQP til MQTT
Hvis du bruger [enhed klient SDK'er][lnk-device-sdks], Skift fra ved hjælp af AMQP til MQTT kræver, at ændre parameteren protocol i klienten initialiseringen som anført ovenfor.

Når du gør det, skal du kontrollere følgende elementer:

* AMQP returnerer fejl for mange betingelser, mens MQTT afbryder forbindelsen. Som et resultat kræver din undtagelse, der håndterer logik muligvis nogle ændringer.
* MQTT understøtter ikke de *afvise* handlinger, når du modtager [C2D meddelelser][lnk-messaging]. Hvis din back-end skal modtage et svar fra app enhed, skal du overveje at bruge [direkte metoder][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Ved hjælp af MQTT protokollen direkte

Hvis en enhed ikke kan bruge enhed klienten SDK'er, kan det stadig oprette forbindelse til offentlige enhed slutpunkterne ved hjælp af MQTT-protokollen. I **FORBINDE** pakken skal enheden bruge følgende værdier:

- Brug **deviceId**for feltet **ClientId** . 
- Feltet **brugernavn** skal bruge `{iothubhostname}/{device_id}`, hvor {iothubhostname} er det fulde CName af IoT-hub.

    Eksempelvis hvis navnet på din IoT hub er **contoso.azure devices.net** og navnet på din enhed er **MyDevice01**, feltet fulde **brugernavn** skal indeholde `contoso.azure-devices.net/MyDevice01`.

- Brug et SAS token for feltet **adgangskode** . Formatet af SAS Tokenet er den samme som for både HTTP- og AMQP protokoller:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Du kan finde flere oplysninger om, hvordan du kan generere SAS tokens, i afsnittet enhed i [ved hjælp af IoT Hub sikkerhedstokens][lnk-sas-tokens].
    
    Når du tester, kan du også bruge [Enhed Explorer] [ lnk-device-explorer] værktøj til hurtigt at oprette et SAS token, som du kan kopiere og indsætte i din egen programkode:
    
    1. Gå til **fanen i enhed Explorer** .
    2. Klik på **SAS Token** (øverst til højre).
    3. På **SASTokenForm**, Vælg din enhed i **DeviceID** rullemenu. Angiv din **TTL**.
    4. Klik på **Generer** for at oprette et token.
    
    SAS tokenet, der genereres har denne struktur:   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

    Del af dette token til at bruge som feltet **adgangskode** til at oprette forbindelse ved hjælp af MQTT er:   `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

MQTT oprette forbindelse og Afbryd pakker, IoT Hub problemer en begivenhed på kanalen **Handlinger overvågning** med yderligere oplysninger, der kan hjælpe dig med at foretage fejlfinding af problemer med serverforbindelsen.

### <a name="sending-messages-to-iot-hub"></a>Sende meddelelser til IoT-Hub

Når du har foretaget en vellykket forbindelse, en enhed kan sende meddelelser til IoT Hub ved hjælp af `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` som et **Emnenavn**. Den `{property_bag}` element gør det muligt for kameraet, for at sende meddelelser med yderligere egenskaber i en url-kodet format. Eksempel:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [AZURE.NOTE] Dette `{property_bag}` element bruger den samme kodning som for forespørgselsstrenge i HTTP-protokollen.

Klientprogrammet enhed kan også bruge `devices/{device_id}/messages/events/{property_bag}` som den **bliver emnenavn** til at definere *vil meddelelser* videresendes som en telemetri meddelelse.

IoT Hub understøtter ikke QoS 2 meddelelser. Hvis en enhed klient publicerer en meddelelse med **QoS 2**, lukkes IoT Hub netværksforbindelsen.
IoT-Hub, bevares ikke Behold meddelelser. Hvis en enhed sender en meddelelse med flaget **BEHOLD** angivet til 1, tilføjer IoT Hub på **x-vælge-bevare** programmet egenskab til meddelelsen. I dette tilfælde i stedet for vedvarer Behold meddelelsen, overfører IoT Hub det til back end-programmet.

### <a name="receiving-messages"></a>Modtage meddelelser

Hvis du vil modtage meddelelser fra IoT-Hub, skal en enhed abonnere ved hjælp af `devices/{device_id}/messages/devicebound/#` som **Emne Filter**. Flere niveauer jokertegnet **#** i filteret emne bruges kun til at tillade enheden at modtage flere egenskaber i emnenavnet på. IoT Hub ikke tillader brugen af den **#** eller **?** jokertegn for filtrering af underordnede emner. Da IoT Hub ikke er en generelle formål pub sub SMS mægler, understøtter det kun dokumenterede emnenavne og emne filtre.

Skal du note, enheden ikke vil modtage meddelelser fra IoT-Hub, før det har oprettet abonnement til dokumentets enhed bestemte slutpunkt repræsenteret af den `devices/{device_id}/messages/devicebound/#` emne filter. Efter vellykket abonnement, der er oprettet, starter enheden modtager kun cloud-til-enhed meddelelser, der er sendt til den efter tidspunktet for abonnementet. Hvis enheden tilsluttes med **CleanSession** flag er indstillet til **0**, bevares abonnementet på tværs af forskellige sessioner. I dette tilfælde, næste gang den opretter forbindelse til **CleanSession 0** enheden modtager udestående meddelelser, der er sendt til den, mens det blev afbrudt. Hvis enheden bruger **CleanSession** flag, der er angivet til **1** , selvom den ikke vil modtage meddelelser fra IoT-Hub, indtil den abonnerer på dens enhed-slutpunkt.

IoT Hub leverer meddelelser med **Emnenavn** `devices/{device_id}/messages/devicebound/`, eller `devices/{device_id}/messages/devicebound/{property_bag}` Hvis der er en hvilken som helst meddelelsesegenskaber. `{property_bag}`indeholder url-kodet nøgle/værdi-par af meddelelsesegenskaber. Egenskaber for tjenesteprogram og egenskaber for bruger angives system (såsom **messageId** eller **correlationId**) medtages i egenskaben pose. System egenskabsnavne har præfikset **$**, egenskaber for tjenesteprogram bruge det oprindelige navn med Intet præfiks.

Når en enhed klient abonnerer på et emne med **QoS 2**, tildeler IoT Hub maksimale QoS niveau 1 i **SUBACK** pakken. Herefter leverer IoT Hub meddelelser til den enhed, ved hjælp af QoS-1.

### <a name="additional-considerations"></a>Yderligere overvejelser

Som endelige vederlag, hvis du vil tilpasse MQTT protocol funktionsmåden i skyen-side, skal du gennemse [Azure IoT protocol gateway] [ lnk-azure-protocol-gateway] , der gør det muligt at installere en høj ydeevne brugerdefineret protokol gateway, der grænseflader direkte med IoT Hub. Gatewayen Azure IoT protocol gør det muligt at tilpasse enhed protokollen til brownfield MQTT installationer eller andre brugerdefinerede protokoller. Denne fremgangsmåde kræver dog, at du har kørt og betjene en brugerdefineret protokol gateway.

## <a name="next-steps"></a>Næste trin

Kan finde flere oplysninger i [noterne MQTT understøtter] [ lnk-mqtt-devguide] i Azure IoT Hub udvikler vejledning.

Hvis du vil vide mere om MQTT protokollen, se [MQTT dokumentation][lnk-mqtt-docs].

Hvis du vil vide mere om planlægning af implementeringen IoT-Hub, skal du se:

- [Azure godkendt for IoT enhed katalog][lnk-devices]
- [Understøttelse af flere protokoller][lnk-protocols]
- [Sammenligne med begivenhed Hubs][lnk-compare]
- [Skalering, HA og DR][lnk-scaling]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#using-sas-tokens-as-a-device
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md
