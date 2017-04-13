<properties
 pageTitle="Udvikler vejledning - kvotaer og (throttling) | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - beskrivelse af kvoter, der gælder for IoT Hub og forventede variere den benyttede funktionsmåde"
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

# <a name="reference---quotas-and-throttling"></a>Reference - kvotaer og (throttling)

## <a name="quotas-and-throttling"></a>Kvotaer og (throttling)

Hvert Azure abonnement kan have højst 10 IoT hubs.

Hver IoT hub er klargjort med et bestemt antal enheder i en bestemt SKU (du kan finde flere oplysninger, se [Azure IoT Hub priser][lnk-pricing]). Den SKU og antal enheder, bestemmer for meddelelser, kan du sende den maksimale daglige kvote.

SKU bestemmer også variere den benyttede begrænsningerne, der IoT Hub gennemtvinger på alle handlinger.

## <a name="operation-throttles"></a>Handlingen throttles

Handlingen throttles er rente begrænsninger, der er anvendt i feltet minut områder, og er beregnet til at undgå misbrug. IoT Hub forsøger at undgå at returnere fejl, når det er muligt, men det starter og returnerer undtagelser, hvis begrænsning ignoreres for længe.

Følgende er på listen over tvungen throttles. Værdierne refererer til en enkelt hub.

| Begrænsning | Hub værdi |
| -------- | ------------- |
| Identitet registreringsdatabasen handlinger (oprette, hente, liste, opdatere og slette) | 5000/min/enhed (for S3) <br/> 100/min/enhed (for S1 og S2). |
| Forbindelser til enheder | 6000/sec/enhed (for S3) 120/sec/enhed (for S2), 12/sec/enhed (for S1). <br/>Minimum af 100/sec. <br/> For eksempel to S1 enheder er 2\*12 = 24/sec, men du har mindst 100/sec på tværs af dine enheder. Med ni S1 enheder, du har 108/sec (9\*12) på tværs af dine enheder. |
| Sender enhed i skyen | 6000/sec/enhed (for S3) 120/sec/enhed (for S2), 12/sec/enhed (for S1). <br/>Minimum af 100/sec. <br/> For eksempel to S1 enheder er 2\*12 = 24/sec, men du har mindst 100/sec på tværs af dine enheder. Med ni S1 enheder, du har 108/sec (9\*12) på tværs af dine enheder. |
| Cloud-til-enhed sender | 5000/min/enhed (for S3), 100/min/enhed (for S1 og S2). |
| Cloud-til-enhed modtager | 50000/min/enhed (for S3) 1000/min/enhed (for S1 og S2). |
| Overfør filhandlinger | 5000 fil overføre meddelelser/min/enhed (for S3), 100 filer Overfør meddelelser/min/enhed (for S1 og S2). <br/> 10000 SAS URI'er kan være ud for en Azure-lager-konto på én gang.<br/> 10 SAS URI'er/enhed kan være ud ad gangen. | 

Det er vigtigt at gøre det klart, at *enhedsforbindelser* begrænsning styrer den rente, hvor der kan oprettes nye enhedsforbindelser med en IoT-hub, og ikke det maksimale antal samtidigt tilsluttede enheder. Begrænsning, der afhænger af antallet enheder, der er klargjort for hubben.

Eksempelvis hvis du køber en enkelt S1 enhed, får du en begrænsning af 100 forbindelser sekundet. Det betyder, at du opretter forbindelse mellem 100.000 enheder, det tager mindst 1000 sekunder (cirka 16 minutter). Du kan dog have så mange samtidigt forbundne enheder, du har enheder, der er registreret i din enhed identitet registreringsdatabase.

En detaljeret beskrivelse af IoT Hub (throttling) funktionsmåde, kan du se blogindlægget [IoT Hub (throttling), og du][lnk-throttle-blog].

>[AZURE.NOTE] Det er muligt at øge kvoter eller begrænsning begrænsninger ved at øge antallet af klargjort enheder i en IoT hub på et givet tidspunkt.

>[AZURE.IMPORTANT] Identitet registreringsdatabasen handlinger er beregnet til Runtime-brug i administration af enheder og klargøring scenarier. Læse eller opdatere et stort antal enhed identiteter understøttes gennem [importere og eksportere job][lnk-importexport].

## <a name="next-steps"></a>Næste trin

Andre referenceemner i i denne IoT Hub udvikler vejledning omfatter:

- [IoT Hub slutpunkter][lnk-devguide-endpoints]
- [Forespørgselssprog til twins, metoder og job][lnk-devguide-query]
- [IoT Hub MQTT support][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md