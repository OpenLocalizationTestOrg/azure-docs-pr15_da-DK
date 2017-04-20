> [AZURE.SELECTOR]
- [C på Windows](../articles/iot-suite/iot-suite-connecting-devices.md)
- [C på Linux](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
- [C på mbed](../articles/iot-suite/iot-suite-connecting-devices-mbed.md)
- [Node.js](../articles/iot-suite/iot-suite-connecting-devices-node.md)

## <a name="scenario-overview"></a>Oversigt over scenario

I dette scenario skal du oprette en enhed, der sender følgende fjernmåling til fjernbetjeningen overvågningsløsning [forudkonfigurerede][lnk-what-are-preconfig-solutions]:

- Udetemperaturen
- Indre temperatur
- Luftfugtighed

Koden på enheden genererer eksempelværdier for nemheds skyld, men vi opfordrer dig til at udvide eksemplet ved at tilslutte reelle sensorer til enheden og sende reelle fjernmåling.

For at afslutte dette selvstudium skal have du en aktiv Azure konto. Hvis du ikke har en konto, kan du oprette en gratis prøveversion konto med blot et par minutter. Yderligere oplysninger finder du [Azure gratis prøveversion][lnk-free-trial].

## <a name="before-you-start"></a>Før du starter

Før du skriver kode til enheden, skal du klargøre din remote forudkonfigurerede overvågningsløsning og derefter klargøre en ny brugerdefineret enhed i denne løsning.

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>Klargør din remote forudkonfigurerede overvågningsløsning

Den enhed, du opretter i dette selvstudium sender data til en forekomst af [fjernovervågning] [ lnk-remote-monitoring] forudkonfigureret løsning. Hvis du allerede har klargjort til fjernovervågning forudkonfigureret løsning i Azure-kontoen, skal du følge nedenstående trin:

1. Klik på på siden <https://www.azureiotsuite.com/> **+** til at oprette en ny løsning.

2. Klik på **Vælg** **fjernovervågning** panelet for at oprette den nye løsning.

3. Angiv et **navn til løsning** af dit valg på siden **Opret Remote overvågningsløsning** , Marker det **område** , du vil installere til, og vælg Azure abonnementet skal bruge. Klik derefter på **Opret løsning**.

4. Vent, indtil processen klargøring er fuldført.

> [AZURE.WARNING] De løsninger, der er forudkonfigureret bruge fakturerbare Azure tjenester. Sørg for at fjerne forudkonfigurerede løsningen fra abonnementet, når du er færdig med at undgå eventuelle unødvendige gebyrer. Du kan fjerne en forudkonfigureret løsning helt fra dit abonnement ved at besøge siden <https://www.azureiotsuite.com/> .

Når klargøringsprocessen for remote overvågningsløsning er færdig, skal du klikke på **Start** for at åbne dashboardet løsning i din browser.

![][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>Klargør din enhed i den eksterne overvågning løsning

> [AZURE.NOTE] Hvis du allerede har klargjort en enhed i din løsning, kan du springe dette trin over. Du skal kende legitimationsoplysningerne for enheden, når du opretter klientprogrammet.

For en enhed til at oprette forbindelse til de forudkonfigurerede løsning, kan den identificere sig selv IoT hub ved hjælp af gyldige legitimationsoplysninger. Du kan hente legitimationsoplysningerne enhed fra dashboardet løsning. Du kan medtage enhed legitimationsoplysningerne i klientprogrammet senere i dette selvstudium. 

Hvis du vil tilføje en ny enhed til din remote overvågning løsning, skal du udføre følgende trin i dashboardet løsning:

1.  Klik på **Tilføj en enhed**i nederste venstre hjørne af dashboardet.

    ![][1]

2.  Klik på **Tilføj ny** **Brugerdefineret enhed** , i panelet.

    ![][2]

3.  Vælg **Lad mig definere mine egne enheds-ID**, Angiv et enheds-ID som **mydevice**, klik på **Kontroller ID** for at kontrollere, at navnet ikke allerede er i brug, og klik derefter på **Opret** for at klargøre enheden.

    ![][3]

5. Noter enhed-legitimationsoplysninger (enheds-ID, IoT Hub værtsnavn og nøglen enhed), dit klientprogram har brug for dem til at oprette forbindelse til den eksterne overvågning løsning. Klik derefter på **udført**.

    ![][4]

6. Kontroller, at enheden vises i sektionen devices. **Enhedens status Afventer, indtil enheden opretter forbindelse til den eksterne overvågningsløsning.**

    ![][5]

[img-dashboard]: ./media/iot-suite-selector-connecting/dashboard.png
[1]: ./media/iot-suite-selector-connecting/suite0.png
[2]: ./media/iot-suite-selector-connecting/suite1.png
[3]: ./media/iot-suite-selector-connecting/suite2.png
[4]: ./media/iot-suite-selector-connecting/suite3.png
[5]: ./media/iot-suite-selector-connecting/suite5.png

[lnk-what-are-preconfig-solutions]: ../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: ../articles/iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/