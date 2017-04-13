<properties
   pageTitle="Azure IoT protocol gateway | Microsoft Azure"
   description="Beskriver, hvordan du bruger Azure IoT protocol gateway til at udvide de funktioner og understøttelse af Azure IoT Hub."
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/23/2016"
   ms.author="kdotchko"/>

# <a name="supporting-additional-protocols-for-iot-hub"></a>Understøtter flere protokoller for IoT Hub

Azure IoT Hub understøtter indbygget kommunikation over MQTT, AMQP og HTTP-protokollerne. I nogle tilfælde enheder eller felt gateways muligvis ikke bruge en af disse almindelige protokoller og kræver protocol tilpasning. Du kan bruge en brugerdefineret gateway i så fald. En brugerdefineret gateway kan aktivere protocol tilpasning for IoT Hub slutpunkter ved broer trafik til og fra IoT Hub. Du kan bruge [Azure IoT protocol gateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) som en brugerdefineret gateway til at aktivere protocol tilpasning for IoT Hub.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT protocol gateway

Azure IoT protocol gateway er en ramme for protocol tilpasning, der er udviklet til Høj skalerbarhed, tovejs enhedskommunikation med IoT Hub. Gatewayen protocol er en pass-through-komponent, der accepterer enhedsforbindelser over en bestemt protokol. Den bygger trafik til IoT Hub over AMQP 1.0. Azure IoT protocol gatewayen er tilgængelige som et open source-software projekt til at levere fleksibilitet for at tilføje understøttelse af en række forskellige protokoller og protocol versioner.

Du kan installere gatewayen protocol i Azure på en meget SVG måde ved hjælp af Azure Cloud Services arbejder roller. Gatewayen protocol kan desuden være installeret i lokale miljøer, som feltet gateways.

Gatewayen Azure IoT protocol omfatter en MQTT protokol adapter, der gør det muligt at tilpasse MQTT protocol funktionsmåden, hvis det er nødvendigt. Da IoT Hub leverer indbygget understøttelse af MQTT v3.1.1 protokol, skal du kun overveje at bruge MQTT protocol adapter, hvis du har behov for protocol tilpasninger eller specifikke krav for flere funktioner.

MQTT kortet demonstrerer også programming modellen med opbygning af protocol kort til andre protokoller. Desuden kan Azure IoT protocol gateway programming modellen du Tilslut brugerdefinerede komponenter til specialiserede behandling som brugerdefineret godkendelse, meddelelse transformationer, komprimering/kompression eller kryptering, der er relateret trafik mellem enheder og IoT Hub.

For større fleksibilitet leveres protocol gateway og MQTT implementering i et open source-software-projekt. Dette kan du tilpasse implementeringen efter behov.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Azure IoT protocol gatewayen og hvordan du bruger og installere den som en del af din IoT løsning, i:

* [Azure IoT protocol gateway lager på GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Azure IoT protocol gateway udvikler vejledning](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Hvis du vil vide mere om planlægning af implementeringen IoT-Hub, skal du se:

- [Sammenligne med begivenhed Hubs][lnk-compare]
- [Skalering, HA og DR][lnk-scaling]

Hvis du vil udforske yderligere funktionerne i IoT-Hub, i:

- [Udvikler vejledning][lnk-devguide]
- [Simulere en enhed med IoT Gateway SDK][lnk-gateway]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
