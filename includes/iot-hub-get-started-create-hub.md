## <a name="create-an-iot-hub"></a>Oprette en IoT-hub

Oprette en IoT hub til din simuleret enhed for at oprette forbindelse til. Følgende trin viser, hvordan du udfører denne opgave ved hjælp af portalen Azure.

1. Log på [Azure portal][lnk-portal].

2. Klik på **Ny**i Jumpbar, > **Internet ting** > **Azure IoT Hub**.

    ![Azure portal Jumpbar][1]

3. Vælg konfiguration for IoT-hub i bladet **IoT hub** .

    ![IoT hub blade][2]

    * Angiv et navn til din IoT hub i feltet **navn** . Hvis **navn** er gyldige og tilgængelige, vises en grøn markering i feltet **navn** .
    * Markér en [priser og skala niveau][lnk-pricing]. Dette selvstudium kræver ikke en bestemt niveau. Dette selvstudium skal du bruge det gratis F1 niveau.
    * Opret en ny ressourcegruppe i **ressourcegruppe**, eller Vælg en eksisterende. Kan finde flere oplysninger under [Brug af grupper til at administrere dine Azure ressourcer][lnk-resource-groups].
    * Vælg en placering til at hoste din IoT hub på **placering**. Dette selvstudium skal du vælge din nærmeste placering.

4. Når du har valgt dine IoT hub konfigurationsindstillinger, kan du klikke på **Opret**.  Det kan tage et par minutter til Azure til at oprette IoT centrum. Hvis du vil kontrollere status, kan du overvåge forløbet på Startboard eller i panelet beskeder.

    ![Ny IoT hub status][3]

5. Klik på nyt felt til din IoT hub i Azure portalen for at åbne bladet for den nye IoT-hub, når IoT hubben er blevet oprettet. Skal du notere **Hostname**, og klik derefter på **delt access politikker**.

    ![Ny IoT hub blade][4]

6. Klik på **iothubowner** politikken i bladet **delt access politikker** og derefter kopiere, og noter forbindelsesstrengen i bladet **iothubowner** . Du kan finde flere oplysninger under [adgangskontrol] [ lnk-access-control] i "Azure IoT Hub udvikler guide."

    ![Delt adgang politikker blade][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide-security.md
