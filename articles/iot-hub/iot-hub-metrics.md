<properties
 pageTitle="IoT Hub diagnosticering målepunkter"
 description="En oversigt over Azure IoT Hub statistik, så brugerne kan vurdere den overordnede status for deres ressource"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-diagnostic-metrics"></a>Introduktion til diagnosticering målepunkter

Diagnosticering målepunkter giver dig bedre data om tilstanden for de ressourcer, der Azure i abonnementet Azure. Målepunkter gør det muligt at vurdere den overordnede status for tjenesten og de enheder, der er forbundet til netværket. Bruger mod statistik er vigtigt, fordi de hjælpe dig med at se, hvad der foregår med din IoT hub Hjælp egentlige årsag problemer med og uden at skulle kontakte support til Azure.

Du kan aktivere diagnosticering målepunkter fra Azure-portalen.

## <a name="how-to-enable-diagnostic-metrics"></a>Sådan aktiveres diagnosticering målepunkter

1. Oprette en IoT hub. Du kan finde instruktioner til, hvordan du opretter en IoT hub i [Komme i gang] [ lnk-get-started] vejledning.

2. Åbn bladet af din IoT hub. Klik på **diagnosticering**derfra.

    ![][1]

3. Konfigurere din diagnosticering ved at angive status til **på** og vælge en Azure-lager-konto for at gemme dataene diagnosticering. Markér **målepunkter**, og tryk derefter på **Gem**. Bemærk, at kontoen Azure-lager skal oprettes forvejen og, er du betale separat for lagerplads. Du kan også vælge at sende dataene diagnosticering til begivenhed Hubs ark.

    ![][2]

4. Når du har konfigureret diagnosticering skal du gå tilbage til bladet **Oversigt** IoT hub. Målepunkter oplysninger er udfyldt i sektionen **overvågning** af bladet. Klikke på diagrammet, åbnes ruden målepunkter, hvor du kan se en oversigt over målepunkter oplysninger til din IoT hub og redigere markeringen af målepunkter vises i diagrammet. Du kan også konfigurere påmindelser baseret på metriske værdier.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Målepunkter og hvordan du kan bruge dem.

IoT Hub indeholder flere målepunkter for at give dig et overblik over tilstanden for din hub og det samlede antal enheder, der er forbundet til netværket. Du kan kombinere oplysninger fra flere målepunkter til at farvelægge et større billede af tilstanden for IoT hubben. I følgende tabel beskrives hver IoT hub registrerer målene, og hvordan hver metrisk relaterer til den overordnede status for IoT hubben.

| Metrisk | Metriske beskrivelse | Hvad metrikværdien skal bruges til |
| ---- | ---- | ---- |
| d2c.telemetry.Ingress.allProtocol | Antallet af meddelelser, der sendes på tværs af alle enheder | Oversigt over dataene på meddelelse sendes |
| d2c.telemetry.Ingress.success | Antallet af alle vellykket meddelelser i hubben | Oversigt over vellykket meddelelse vandindtrængen i hubben |
| c2d.commands.Egress.Complete.success | Antallet af alle kommandoen meddelelser fuldført ved modtagelse enheden på tværs af alle enheder | Sammen med målene på abandon og Afvis, giver et overblik over overordnede C2D kommandoen succes rente |
| c2d.commands.Egress.Abandon.success | Antallet af alle meddelelser, der er blevet opgivet ved modtagelse enheden på tværs af alle enheder | Fremhæver potentielle problemer, hvis meddelelser er få opgivet oftere end forventet |
| c2d.commands.Egress.Reject.success | Antallet af alle meddelelser, der er blevet afvist ved modtagelse enheden på tværs af alle enheder | Fremhæver potentielle problemer, hvis meddelelser er få afvist oftere end forventet |
| devices.totalDevices | Middel, min og max af antallet enheder, der er registreret til IoT-hub | Antallet enheder, der er registreret til hubben |
| devices.connectedDevices.allProtocol | Middel, min og max af antallet samtidig tilsluttede enheder | Oversigt over antallet enheder, der er forbundet til hub |

## <a name="next-steps"></a>Næste trin

Nu, hvor du har set en oversigt over diagnosticering målepunkter, skal du følge dette link for at få flere oplysninger om administration af Azure IoT Hub:

- [Handlinger overvågning][lnk-monitor]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
