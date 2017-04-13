<properties
 pageTitle="Udvikler vejledning - ordliste | Microsoft Azure"
 description="En ordliste almindelige udtryk, der vedrører IoT Hub"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="glossary-of-iot-hub-terms"></a>Ordliste til IoT-Hub

I denne artikel beskrives nogle af de mest almindelige ord, der er knyttet til IoT Hub.

## <a name="advanced-message-queueing-protocol-amqp"></a>Avancerede Message sætter i kø Protocol (AMQP)

[AMQP](https://www.amqp.org/) er et af de SMS protokoller, der understøtter IoT Hub til at kommunikere med enheder. Du kan finde flere oplysninger om de SMS protokoller, der understøtter IoT-Hub, skal du se [sende og modtage meddelelser med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="cloud-to-device-c2d"></a>Cloud-til-enhed (C2D)

Normalt bruges til at referere til meddelelser, der sendes fra IoT Hub til en forbundet enhed. Disse meddelelser er ofte, kommandoer, som Fortæl kameraet, for at udføre en handling. Se [sende og modtage meddelelser med IoT Hub](iot-hub-devguide-messaging.md)kan finde flere oplysninger.

## <a name="condition"></a>Betingelse

Refererer til enhed tilstandsoplysninger, som metoden forbindelse i øjeblikket bruges, som en enhed app har rapporteret. Enheder kan også rapportere deres funktioner. Du kan forespørge betingelse og kapacitet ved hjælp af enhed dobbelt.

## <a name="data-point-message"></a>Datapunkt meddelelse

Et datapunkt meddelelse er en meddelelse i skyen-til-enhed, der indeholder telemetridata som vindhastighed eller temperaturen.

## <a name="desired-properties"></a>Ønskede egenskaber

I forbindelse med enhed twins bruges ønskede egenskaber sammen med *rapporteret egenskaber* til at synkronisere enhedskonfiguration eller tilstand. Ønskede egenskaber kan kun angives af programmet tilbage slutningen og overholdes ved appen enhed. 

## <a name="device-to-cloud-d2c"></a>Enhed til skyen (D2C)

Normalt bruges til at referere til meddelelser, der sendes fra en enhed, der er forbundet til IoT-Hub. Disse meddelelser kan være [datapunkt](#data-point-message) eller [interaktive](#interactive-message) meddelelser. Se [sende og modtage meddelelser med IoT Hub](iot-hub-devguide-messaging.md)kan finde flere oplysninger.

## <a name="device-identity-registry"></a>Enhed identitet registreringsdatabasen

[Enhed identitet registreringsdatabasen](iot-hub-devguide-identity-registry.md) er den indbyggede komponent i en IoT-hub, der indeholder oplysninger om de enkelte enheder tilladelse til at oprette forbindelse til en hub.

## <a name="device"></a>Enhed

I forbindelse med IoT er en enhed typisk en mindre, enkeltstående databehandling enhed, der kan indsamle data eller styre andre enheder. En enhed muligvis f.eks, en påvirkning overvågning enhed eller en controller for til vanding og ventilation i en drivhusgasser.

## <a name="device-twin"></a>Enhed dobbelt

En [enhed med dobbelt](iot-hub-devguide-device-twins.md) er en kopi i IoT Hub af indstillingerne betingelse og konfiguration af en fysisk enhed. Du kan bruge en enhed med dobbelt til at administrere konfigurationen af den fysiske enhed.

## <a name="direct-method"></a>Direkte metode

En [direkte metode](iot-hub-devguide-direct-methods.md) er en metode til at udløse en metode til at udføre på en enhed ved aktivering af en API på IoT centrum.

## <a name="event-hub-compatible-endpoint"></a>Begivenhed Hub-kompatible slutpunkt

Hvis du vil læse enhed i skyen meddelelser, som sendes til din IoT-hub, du opretter forbindelse til et slutpunkt på din hub og bruge en hvilken som helst begivenhed Hub-kompatible metode til at læse disse meddelelser. Begivenhed Hub-kompatible metoder omfatter ved hjælp af begivenhed Hubs SDK'er og Azure Stream analyser.

## <a name="field-gateway"></a>Feltet gateway

En gateway felt gør det muligt for forbindelse til enheder, der kan ikke oprette forbindelse direkte til IoT Hub og installeres typisk lokalt med dine enheder. Du kan finde flere oplysninger, se [Hvad er Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="job"></a>Job

Din løsning back-end kan bruge job til at planlægge og spore aktiviteter i et antal enheder, der er registreret med din IoT hub. Aktiviteter omfatter opdatering af egenskaber for enheden dobbelt beskedteksten, opdatere enhed dobbelt mærker og aktivering direkte metoder.

## <a name="protocol-gateway"></a>Protocol gateway

En protokol gateway er typisk er implementeret i skyen og giver protocol oversættelsestjenester til at oprette forbindelse til IoT Hub-enheder. Du kan finde flere oplysninger, se [Hvad er Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="interactive-message"></a>Interaktiv meddelelse

En interaktiv meddelelse er en meddelelse i skyen-til-enhed, der udløser en øjeblikkelig handling i programmet back-end. En enhed kan for eksempel sende en alarm om en fejl, der automatisk skal være logget på et CRM-system.

## <a name="iot-hub"></a>IoT-Hub

IoT Hub er en komplet administreret Azure tjeneste, der gør det muligt for pålidelig og sikker tovejs kommunikationen mellem millioner af IoT enheder og løsning back-end. Du kan finde flere oplysninger, se [Hvad er Azure IoT Hub?](iot-hub-what-is-iot-hub.md).

## <a name="iot-suite"></a>IoT pakke

Azure IoT pakke pakker sammen Azure tjenesterne med forudkonfigurerede løsninger. Disse forudkonfigurerede løsninger gør det muligt at komme hurtigt i gang med almindelige IoT scenarier-installationer til slut. Du kan finde flere oplysninger, se [Hvad er Azure IoT pakke?](../iot-suite/iot-suite-overview.md).

## <a name="job"></a>Job

Et [job](iot-hub-devguide-jobs.md) i IoT Hub gør det muligt at udføre handlinger som en firmware opgradering på tværs af flere enheder tilsluttet din hub.

## <a name="mqtt"></a>MQTT

[MQTT](http://mqtt.org/) er et af de SMS protokoller, der understøtter IoT Hub til at kommunikere med enheder. Du kan finde flere oplysninger om de SMS protokoller, der understøtter IoT-Hub, skal du se [sende og modtage meddelelser med IoT Hub](iot-hub-devguide-messaging.md).

## <a name="reported-properties"></a>Rapporteret egenskaber

I forbindelse med enhed twins bruges rapporteret egenskaber sammen med *ønskede egenskaber* til at synkronisere enhedskonfiguration eller betingelse. Rapporteret egenskaber kan kan kun angives ved appen enhed og læse og forespørges ved programmet back-end.

## <a name="tags"></a>Mærker

I forbindelse med devcie twins er mærker enhed metadata gemt og bliver hentet af programmet back-end i form af et JSON-dokument. Mærker er ikke synlige for apps på en enhed.

## <a name="system-properties"></a>Egenskaber for system

Egenskaber for system er skrivebeskyttet i forbindelse med enhed twins og Medtag oplysninger om brug af enheden som seneste aktivitet tid og forbindelse tilstand.