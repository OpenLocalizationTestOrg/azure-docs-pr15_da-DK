## <a name="view-device-telemetry-in-the-dashboard"></a>Vis enhed fjernmåling i dashboardet

Dashboardet i den eksterne overvågning løsning kan du få vist fjernmåling, der sender dine enheder til IoT Hub.

1. Tilbage til remote overvågning løsning dashboardet i browseren, skal du klikke på **enheder** i det venstre panel til at navigere på **listen enheder**.

2. På **listen enheder**, skal du se **, at status for enheden kører nu**.

    ![][18]

3. Klik på **Dashboard** til at vende tilbage til dashboardet, skal du vælge enheden i **enheden for at få vist** rullemenuen til at få vist dens fjernmåling. Fjernmåling fra eksempelprogrammet er 50 enheder for indre temperatur, 55 enheder for udetemperaturen og 50 enheder for fugtighed. Bemærk, at dashboardet vises der som standard kun værdier for temperatur og fugtighed.

    ![][img-telemetry]

## <a name="send-a-command-to-your-device"></a>Sende en kommando til enheden

Dashboardet i den eksterne overvågning løsning kan du sende kommandoer til dine enheder via IoT Hub. I den eksterne overvågning løsning kan du sende en kommando til at angive den indre temperatur i en enhed.

1. Klik på **enheder** i det venstre panel til at navigere på **listen enheder**i den eksterne overvågning løsning-dashboard.

2. Klik på **Enheds-ID** til din enhed på **listen enheder**.

3. Klik på **kommandoer**i detaljeruden for **enheden** .

    ![][13]

4. Vælg **SetTemperature**i rullelisten **kommando** , og skriv en ny værdi for temperatur i **temperatur** . Klik på **Send kommando** for at sende kommandoen til enheden.

    ![][14]

    > [AZURE.NOTE] Kommandoregistreringsbufferen viser fra starten kommandostatus **Igangsat**. Når enheden accepterer kommandoen, ændres status til **succes**.

5. Dashboardet, Kontroller, at enheden sender 75 nu som den nye værdi for temperatur.

## <a name="next-steps"></a>Næste trin

Artiklen [tilpasse forudkonfigureret løsninger] [ lnk-customize] i denne artikel beskrives nogle metoder, du kan udvide dette eksempel. Mulige udvidelser omfatter ved hjælp af reelle sensorer og gennemføre yderligere kommandoer.

Yderligere oplysninger om [tilladelser på webstedet azureiotsuite.com][lnk-permissions].

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-permissions]: ../articles/iot-suite/iot-suite-permissions.md
