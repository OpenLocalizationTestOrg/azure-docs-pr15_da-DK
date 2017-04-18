## <a name="create-a-device-management-enabled-iot-hub"></a>Oprette en enhed management aktiveret IoT Hub

Da IoT Hub Enhedshåndtering er i Vis udskrift, skal du oprette en enhed administration af IoT hub. Når IoT Hub enhedsstyring når generelt tilgængelig, opdateres dette selvstudium. Følgende trin viser, hvordan du udfører denne opgave ved hjælp af portalen Azure.

1.  Log på [Azure-portalen].
2.  Klik på **Ny**i Jumpbar, og derefter klikke på **Internettet af ting**, og klik **Azure IoT Hub**.

    ![][img-new-hub]

3.  Vælg konfiguration for IoT-Hub i bladet **IoT Hub** .

    ![][img-configure-hub]

  -   Angiv et navn til din IoT Hub i feltet **navn** . Hvis **navn** er gyldige og tilgængelige, vises en grøn markering i feltet **navn** .
  -   Vælg en **priser og skalering niveau**. Dette selvstudium kræver ikke en bestemt niveau.
  -   Opret en ny ressourcegruppe i **ressourcegruppe**, eller Vælg en eksisterende. Se [Brug af grupper til at administrere dine Azure ressourcer]kan finde flere oplysninger.
  -   Markér afkrydsningsfeltet for at **Aktivere Device Management - PREVIEW**.
  -   Vælg en placering til at hoste din IoT Hub på **placering**. IoT Hub Enhedshåndtering er kun tilgængelig i af USA, nord Europe og Østasien under offentlige preview.

    > [AZURE.NOTE]Hvis du ikke markerer afkrydsningsfeltet for at **Aktivere Enhedshåndtering**, fungerer eksemplerne ikke.<br/>Ved at markere **Aktiver styring af enhed**, opretter du et eksempel IoT Hub understøttes kun i indtastning af østasiatiske USA, nord Europe og Østasien og ikke beregnet til fremstilling scenarier. Du kan ikke overføre enheder til og fra enhed administration af hubs.

4.  Når du har valgt dine IoT Hub konfigurationsindstillinger, kan du klikke på **Opret**. Det kan tage et par minutter til Azure til at oprette IoT centrum. Hvis du vil kontrollere status, kan du overvåge forløbet på **Startboard** eller i panelet **beskeder** .

    ![][img-monitor]

5.  Når IoT hubben er blevet oprettet, åbnes automatisk bladet til centrum. Skal du notere **Hostname**, og klik derefter på **delt access politikker**.

    ![][img-keys]

6.  Klik på politikken **iothubowner** , og derefter kopiere og noter forbindelsesstrengen i bladet **iothubowner** . Kopiere den til en placering, du kan åbne senere, fordi du har brug for at fuldføre dette selvstudium.

    > [AZURE.NOTE] Sørg for at undlade at bruge **iothubowner** legitimationsoplysninger i fremstilling scenarier.

    ![][img-connection]

Du har nu oprettet en enhed management aktiveret IoT Hub. Du skal bruge forbindelsesstrengen til at udføre dette selvstudium.

## <a name="create-a-device-identity"></a>Oprette en enhed-identitet

I dette afsnit, skal du bruge en Node værktøj, der hedder [IoT Hub Explorer] [ iot-hub-explorer] til at oprette en enhed identitet til dette selvstudium.

1. Kør følgende i dit miljø, kommandolinjen:

    npm Installer -giothub-explorer@latest

2. Derefter skal du køre følgende kommando til at logge på din-hubben skal huske at erstatte `{service connection string}` med forbindelsesstrengen IoT-Hub, du tidligere har kopieret:

    iothub explorer login "{service forbindelsesstrengen}"

3. Til sidst skal oprette en ny enhed identitet kaldet `myDeviceId` med kommandoen:

    iothub explorer oprette myDeviceId – forbindelsesstreng

Skal du notere enhed forbindelsesstrengen fra resultatet. Denne forbindelsesstreng bruges af appen enhed til at oprette forbindelse til din IoT Hub som en enhed.

![][img-identity]

Se [Kom i gang med IoT Hub] [ lnk-getstarted] for en metode til at oprette enhed identiteter fra et program.

<!-- images and links -->
[img-new-hub]: media/iot-hub-get-started-create-hub-pp/image1.png
[img-configure-hub]: media/iot-hub-get-started-create-hub-pp/image2.png
[img-monitor]: media/iot-hub-get-started-create-hub-pp/image3.png
[img-keys]: media/iot-hub-get-started-create-hub-pp/image4.png
[img-connection]: media/iot-hub-get-started-create-hub-pp/image5.png
[img-identity]: media/iot-hub-get-started-create-hub-pp/devidentity.png

[Azure-portalen]: https://portal.azure.com/
[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
[Bruge grupper til at administrere dine Azure ressourcer]: ../articles/azure-portal/resource-group-portal.md
