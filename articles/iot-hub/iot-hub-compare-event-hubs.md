<properties
 pageTitle="Sammenligne Azure IoT Hub til Azure begivenhed hubber | Microsoft Azure"
 description="En sammenligning af tjenesterne Azure IoT Hub og Azure begivenhed Hubs fremhævning funktionelle forskelle og use cases."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Sammenligning af Azure IoT Hub og Azure begivenhed Hubs

En af de vigtigste use cases til IoT Hub er at indsamle telemetri fra enheder. Derfor sammenlignes ofte IoT Hub til [Azure begivenhed hubber][]. Som IoT Hub er begivenhed Hubs en hændelse processing-tjeneste, som gør det muligt for begivenheden og telemetri vandindtrængen i skyen massive skaleres med lav ventetid og høj pålidelighed.

Tjenesterne har, mange forskelle, som er beskrevet i følgende tabel.

| Område | IoT-Hub | Begivenhed Hubs |
| ---- | ------- | ---------- |
| Kommunikationsmønstre | Gør det muligt for enhed i skyen og skyen-til-enhed Chat. | Du kan kun bruges begivenhed vandindtrængen (som regel anses for enhed i skyen scenarier). |
| Understøttelse af enheder protokol | Understøtter MQTT, AMQP, AMQP WebSockets og HTTP. Desuden IoT Hub fungerer med [Azure IoT protocol gateway][lnk-azure-protocol-gateway], en brugerdefinerbare protocol gateway implementering til at understøtte brugerdefinerede protokoller. | Understøtter AMQP, AMQP WebSockets og HTTP. |
| Sikkerhed | Indeholder per enhed identitet og simpel adgangskontrol. Se [sektionen sikkerhed for af IoT Hub developer guide]. | Indeholder begivenhed Hubs hele [delt adgang politikker][Event Hubs - security], med begrænset tilbagekaldte certifikater understøtter gennem [Publishers politikker][Event Hubs publisher policies]. IoT løsninger, ofte påkrævet at implementere en brugerdefineret løsning for at understøtte per enhed legitimationsoplysninger og forfalskning teknologi til forhindring af mål. |
| Handlinger overvågning | Aktiverer IoT løsninger abonnere på et bredt udvalg af enhed identitet administration og connectivity begivenheder som individuelle enhed godkendelsesfejl, throttling og forkert format undtagelser. Disse hændelser gør det muligt at hurtigt at identificere forbindelsesproblemer på niveauet for individuelle enhed. | Viser kun aggregat målepunkter. |
| Skala | Er udviklet til support millioner af samtidigt tilsluttede enheder. | Et mere begrænset antal samtidige forbindelser – op til 5.000 AMQP forbindelser, ud fra [Azure Service Bus kvoter][]kan understøtte. På den anden side begivenhed Hubs gør det muligt at angive partitionen for hver meddelelse sendes. |
| Enhed SDK'er | Indeholder [enhed SDK'er] [ Azure IoT Hub SDKs] for et stort udvalg af platforme og sprog. | Understøttes på .NET og C. Desuden AMQP og HTTP send grænseflader. |
| Filoverførsel | Aktiverer IoT løsninger til at overføre filer fra enheder til skyen. Indeholder en fil meddelelse slutpunkt til integration af arbejdsprocesser og en drift overvågning kategori til fejlfinding support. | Bruger et mønster til kontrol af krav til at anmode om filer fra enheder og give enheder med en lagerplads nøgle for posteringen manuelt. |

Selvom det kun use case er enhed i skyen telemetri vandindtrængen indeholder IoT Hub i oversigt, en tjeneste, som er udviklet specielt til IoT enhed forbindelse på. Det kan fortsætte med at udvide tilbud for disse scenarier med IoT-specifikke funktioner. Begivenhed Hubs henvender sig til begivenhed indtrængen på en massiv skala, både i forbindelse med blandt datacenter og handel datacenter scenarier.

Det er ikke usædvanlige bruge både IoT Hub og begivenhed Hubs i den samme løsning – hvor IoT Hub håndterer kommunikationen enhed i skyen, og begivenhed Hubs håndterer senere begivenhed vandindtrængen i realtid behandling programmer.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om planlægning af implementeringen IoT-Hub, skal du se [skalering, HA og DR][lnk-scaling].

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

[Azure begivenhed Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Sektionen sikkerhed for af IoT Hub developer guide]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Azure Service Bus kvoter]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
