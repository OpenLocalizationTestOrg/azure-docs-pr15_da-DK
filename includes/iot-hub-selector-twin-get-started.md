> [AZURE.SELECTOR]
- [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
- [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)

## <a name="introduction"></a>Introduktion

Enhed twins er JSON-dokumenter, der gemmer enhed tilstandsoplysninger (metadata, konfigurationer og betingelser). IoT Hub fortsætter en enhed med dobbelt for hver enhed, du opretter forbindelse til IoT-Hub.

Brug enhed twins til:

* Gemme enhed metadata fra din back-end.
* Rapportere aktuelle statusoplysninger som tilgængelige funktioner og betingelser (for eksempel connectivity metoden bruges) fra din enhed-app.
* Synkronisere tilstanden for længerevarende arbejdsprocesser (såsom firmware og konfiguration af opdateringer) mellem enhed app og back-end.
* Forespørge din enhed metadata, konfiguration eller tilstand.

> [AZURE.NOTE] Enhed twins er designet til synkronisering og til at forespørge enhedskonfigurationer og betingelser. Bruge [enhed i skyen meddelelser] [ lnk-d2c] for serier af tidsstemplet begivenheder (såsom telemetri streams tidsbaserede føler data) og [skyen-til-enhed metoder] [ lnk-methods] for interaktive kontrolelementer enheder, som aktivering af en vifte fra en bruger kontrolleres app.

Enhed twins gemmes i en IoT hub og indeholder:

* *mærker*, enhed metadata tilgængelig kun for back-end;
* *ønskede egenskaber*, JSON objekter kan redigeres ved back-end og opbevaringstid ved appen enhed; og
* *rapporteret egenskaber*, JSON objekter kan ændres af enhed app og kan læses af baggrunden slutte. Mærker og egenskaber kan ikke indeholde matrixer, men objekter kan indlejres.

![][img-twin]

Desuden kan app back-end forespørge enhed twins baseret på alle de ovenstående data.
Se [forstå enhed twins] [ lnk-twins] kan finde flere oplysninger om enhed twins og [IoT Hub forespørgselssprog] [ lnk-query] reference til forespørgsler.

> [AZURE.NOTE] Enhed twins er kun tilgængelig fra enheder, der har forbindelse til IoT Hub ved hjælp af MQTT protokollen på nuværende tidspunkt. Se den [MQTT understøtter] [ lnk-devguide-mqtt] artikel for at få vejledning i at konvertere eksisterende enhed app til at bruge MQTT.

Dette selvstudium viser, hvordan til:

- Oprette en back end-app, der tilføjer *mærker* til en enhed med dobbelt og en simuleret enhed, som oplyser dens connectivity kanal som en *rapporteret egenskab* på enheden dobbelt.
- Forespørge enheder fra din back-end-app ved hjælp af filtre på mærker og egenskaber, der tidligere har oprettet.


<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md