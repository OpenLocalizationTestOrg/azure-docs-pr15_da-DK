<properties
 pageTitle="Udvikler vejledning - IoT Hub slutpunkter | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - referenceoplysninger om IoT Hub slutpunkter"
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

# <a name="reference---iot-hub-endpoints"></a>Reference - IoT Hub slutpunkter

## <a name="list-of-iot-hub-endpoints"></a>Liste over IoT Hub slutpunkter

Azure IoT Hub er en med flere lejer-tjeneste, som viser dens funktionalitet i forskellige aktører. I det følgende diagram viser de forskellige slutpunkter, IoT Hub Fremviser.

![IoT Hub slutpunkter][img-endpoints]

Følgende er en beskrivelse af slutpunkterne:

* **Ressource-udbyder**. Provideren IoT Hub ressource Fremviser en [Azure ressourcestyring] [ lnk-arm] grænseflade, der gør det muligt for abonnementsejere Azure-, du vil oprette og slette IoT hubs og opdatere IoT hub egenskaber. Egenskaber for IoT Hub styrer [hub niveau sikkerhedspolitikker][lnk-accesscontrol], i modsætning til niveau for enheden adgangskontrol og funktionelle indstillinger til skyen-til-enhed og enhed i skyen messaging. Provideren IoT Hub ressource kan du også eksportere [enhed identiteter][lnk-importexport].
* **Administration af enheder identitet**. Hver IoT hub viser et sæt af HTTP RESTEN slutpunkter til at administrere enhed identiteter (oprette, hente, opdatere og slette). [Enhed identiteter] [ lnk-device-identities] der bruges til enhed godkendelse og adgangskontrol.
* **Administration af enheder med dobbelt**. Hver IoT hub viser et sæt med adgang til tjenesten HTTP RESTEN slutpunkt til at forespørge og opdatere [enhed twins] [ lnk-twins] (Opdater mærker og egenskaber).
* **Administrationen af sager**. Hver IoT hub viser et sæt med adgang til tjenesten HTTP RESTEN slutpunkt til at forespørge og administrere [job][lnk-jobs].
* **Slutpunkter for enhed**. For hver enhed, der er klargjort i enhed identitet registreringsdatabasen, Fremviser IoT Hub et sæt af slutpunkter, en enhed kan bruge til at sende og modtage meddelelser:
    - *Sende enhed i skyen meddelelser*. Brug dette slutpunkt til at [sende enhed i skyen meddelelser][lnk-d2c].
    - *Modtag cloud-til-enhed meddelelser*. En enhed bruger dette slutpunkt til at modtage målrettede [cloud-til-enhed meddelelser][lnk-c2d].
    - *Påbegynd filoverførsler*. En enhed bruger dette slutpunkt til at modtage en Azure-lager SAS URI fra IoT Hub at [overføre en fil][lnk-upload].
    - *Hent og Opdater dobbelt egenskaber*. En enhed bruger denne slutpunkter for at få adgang til dens [enhed dobbelt][lnk-twins]'s egenskaber.
    - *Modtag direkte metoder anmodninger*. En enhed bruger denne slutpunkter lytte til [direkte metoder][lnk-methods]'s anmodninger.

    Disse slutpunkter vises ved hjælp af [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 og [AMQP 1.0] [ lnk-amqp] protokoller. Bemærk, at AMQP er også tilgængelig via [WebSockets] [ lnk-websockets] på port 443.
    
    Twins' og metoder endpoins findes kun ved hjælp af [MQTT v3.1.1][lnk-mqtt].

* **Tjenesten slutpunkter**. Hver IoT hub Fremviser et sæt af slutpunkter for dit program back-end kan bruge til at kommunikere med dine enheder. Disse slutpunkter er i øjeblikket kun tilgængelig ved hjælp af [AMQP] [ lnk-amqp] protokol, med undtagelse af den metode aktivering slutpunkt, der vises via HTTP 1.1.
    - *Modtag enhed i skyen meddelelser*. Dette slutpunkt er kompatibelt med [Azure begivenhed Hubs][lnk-event-hubs]. En back end-tjenesten kan bruge det til at læse alle [meddelelser enhed i skyen] [ lnk-d2c] sendes af dine enheder.
    - *Sende meddelelser i skyen-til-enhed og modtage leveringen bekræftelse*. Disse slutpunkter aktivere dit program back-end til at sende pålidelig [cloud-til-enhed meddelelser][lnk-c2d], og at modtage tilsvarende levering eller udløb bekræftelse.
    - *Modtag fil beskeder*. Dette SMS slutpunkt gør det muligt at modtage beskeder om, når dine enheder korrekt overføre en fil. 
    - *Direkte metode aktivering*. Dette slutpunkt gør det muligt for en back end-tjeneste til at starte en [direkte metode] [ lnk-methods] på en enhed.

[IoT Hub API'er og SDK'er] [ lnk-sdks] artikel beskrives de forskellige måder at få adgang til disse slutpunkter.

Til sidst skal det er vigtigt at være opmærksom på, at alle IoT Hub slutpunkter bruger [TLS] [ lnk-tls] protocol og ingen slutpunkt nogensinde vises på ikke-krypteret/usikker kanaler.

## <a name="field-gateways"></a>Feltet gateways

I en IoT løsning kan et *felt gateway* , der er placeret mellem dine enheder og IoT Hub slutpunkterne. Det er normalt placeret tæt på dine enheder. Dine enheder kommunikere direkte med gatewayen felt ved hjælp af en protokol, der understøttes af enhederne. Gatewayen felt opretter forbindelse til en IoT Hub slutpunktet, ved hjælp af en protokol, der understøttes af IoT Hub. En felt gateway kan være meget specialiserede hardware eller lav power kører software, der udfører scenariet til slut, som er beregnet gatewayen.

Du kan bruge [Azure IoT Gateway SDK] [ lnk-gateway-sdk] at implementere en felt gateway. Dette SDK indeholder bestemte funktioner såsom muligheden for at multiplex kommunikation fra flere enheder på den samme forbindelse til IoT-Hub.

## <a name="next-steps"></a>Næste trin

Andre referenceemner i i denne IoT Hub udvikler vejledning omfatter:

- [Forespørgselssprog til twins, metoder og job][lnk-devguide-query]
- [Kvotaer og (throttling)][lnk-devguide-quotas]
- [IoT Hub MQTT support][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md