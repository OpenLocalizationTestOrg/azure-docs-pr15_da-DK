<properties
     pageTitle="Brug portalen Azure til at konfigurere filoverførsel | Microsoft Azure"
     description="En oversigt over, hvordan du konfigurerer filoverførsel ved hjælp af portalen Azure"
     services="iot-hub"
     documentationCenter=""
     authors="dominicbetts"
     manager="timlt"
     editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/30/2016"
     ms.author="dobett"/>

# <a name="configure-file-uploads-using-the-azure-portal"></a>Konfigurere filoverførsler ved hjælp af portalen Azure

## <a name="file-upload"></a>Filoverførsel

Bruge [filer overføre funktionalitet i IoT Hub][lnk-upload], skal du først knytte en Azure-lager-konto til din hub. Vælg indstillinger for **Filoverførsel** til at vise en liste over Overfør filegenskaber for IoT hubben, der ændres.

![][13]

**Objektbeholder til lagring**: Brug portalen Azure til at vælge en blob objektbeholder på en Azure-lager konto i dit aktuelle Azure abonnement skal tilknyttes IoT centrum. Hvis det er nødvendigt, kan du oprette en Azure-lager-konto for **lagerplads konti** blade og blob objektbeholderen på bladet **beholdere** . IoT Hub genererer automatisk SAS URI'er med skrivetilladelser til denne blob objektbeholder til enheder skal bruges, når de overføre filer.

![][14]

**Modtag beskeder for overførte filer**: aktivere eller deaktivere meddelelser om overførsel af filer via indstillingen.

**SAS TTL**: denne indstilling er den time-to-live af de SAS URI'er returneres på enheden af IoT Hub. Som standard angivet til en time, men kan tilpasses til andre værdier ved hjælp af skyderen.

**Meddelelse om fil indstillinger default TTL**: meddelelse fra Overførselscenter på time-to-live af en fil, før det er udløbet. Som standard angivet til én dag, men kan tilpasses til andre værdier ved hjælp af skyderen.

**Antal filer meddelelse maksimale levering**: antallet gange IoT hubben forsøger at levere en fil-meddelelse fra Overførselscenter. Som standard angivet til 10, men kan tilpasses til andre værdier ved hjælp af skyderen.

![][15]

## <a name="next-steps"></a>Næste trin

Du kan finde flere oplysninger om funktionerne fil til overførsel af IoT Hub se [overføre filer fra en enhed] [ lnk-upload] i udvikler vejledning.

Følg disse links for at få flere oplysninger om administration af Azure IoT Hub:

- [Flere administrere IoT enheder][lnk-bulk]
- [Brugen målepunkter][lnk-metrics]
- [Handlinger overvågning][lnk-monitor]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]
- [Secure din IoT løsning fra bunden op][lnk-securing]


  [13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
  [14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
  [15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md