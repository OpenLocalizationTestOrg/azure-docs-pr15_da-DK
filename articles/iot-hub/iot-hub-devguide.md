<properties
 pageTitle="Udvikler vejledning emner for IoT Hub | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning, der indeholder IoT Hub slutpunkter, sikkerhed, enhed identitet registreringsdatabasen, administration af enheder og -beskeder"
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

# <a name="azure-iot-hub-developer-guide"></a>Azure IoT Hub udvikler vejledning

Azure IoT Hub er en komplet administreret tjeneste, der hjælper med at aktivere pålidelig og sikker tovejskommunikation mellem millioner af IoT enheder og afslutte et program igen.

Azure IoT Hub giver med:

* Sikker kommunikation ved hjælp af hver enhed sikkerhedslegitimationsoplysninger og adgangskontrol.
* Pålidelige enhed i skyen og skyen-til-enhed hyper-skala Chat.
* Nemt enhed-forbindelse med enhed billedbiblioteker til de mest populære sprog og platforme.

Vejledningen IoT Hub udvikler indeholder følgende artikler:

- [Sende og modtage meddelelser med IoT Hub] [ devguide-messaging] beskriver de SMS funktioner (enhed i skyen og skyen-til-enhed), IoT Hub Fremviser. I artiklen indeholder også oplysninger om emner som meddelelsesformater, og de understøttede kommunikationsprotokoller og de portnumre, de bruger.
- [Overføre filer fra en enhed] [ devguide-upload] beskrives, hvordan du kan overføre filer fra en enhed. I artiklen indeholder også oplysninger om emner som meddelelserne kan sende uplaod processen.
- [Administrer identiteter enhed i IoT Hub] [ devguide-identities] beskrivelse af, hvad oplysninger hver IoT hub enhed identitet registreringsdatabasen gemmer, og hvordan du kan få adgang til og redigere den.
- [Styre adgangen til IoT Hub] [ devguide-security] beskriver den sikkerhedsmodel, der bruges til at give adgang til IoT Hub funktionalitet for begge enheder og komponenter i skyen. Artiklen indeholder oplysninger om brug af tokens og x.509-certifikater og oplysninger om de tilladelser, kan du give.
- [Bruge enhed twins til at synkronisere stat og konfigurationer] [ devguide-device-twins] beskrives *enhed dobbelt* konceptet og funktionerne for den Fremviser som synkronisering af en enhed med dens dobbelt. Artiklen indeholder oplysninger om de data, der er gemt i en enhed med dobbelt.
- [Aktivere en direkte metode på en enhed] [ devguide-directmethods] beskrives livscyklus for en direkte metode, oplysninger om at aktivere metoder på en enhed fra din back end-app og håndtere metoden direkte på din enhed.
- [Planlægge job på flere enheder] [ devguide-jobs] beskrives, hvordan du kan planlægge job på flere enheder. I artiklen beskrives, hvordan til at sende job, udfører opgaver som at udføre en direkte metode, ved at opdatere en devcie ved hjælp af en devcie dobbelt. Det også beskrives det, hvordan at forespørge på status for en sag.
- [Reference - IoT Hub slutpunkter] [ devguide-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger. I artiklen beskrives også, hvordan du kan bruge en gateway felt til at aktivere visse enheder til at oprette forbindelse til IoT Hub slutpunkterne.
- [Reference - forespørgselssprog til twins, metoder og job] [ devguide-query] beskrives pågældende forespørgselssprog, der gør det muligt for at hente oplysninger fra din hub om din enhed twins og -job.
- [Reference - kvotaer og (throttling)] [ devguide-quotas] opsummerer kvoter i tjenesten IoT Hub og variere den benyttede funktionsmåden du kan forvente at se, når du overskrider en kvote.
- [Reference - enheder og tjenester SDK'er] [ devguide-sdks] lister SDK'er kan du bruge udvikle både enhed og service-programmer, der arbejder sammen med din IoT hub. Artiklen indeholder links til API onlinedokumentation.
- [Reference - understøttelse af IoT Hub MQTT] [ devguide-mqtt] indeholder detaljerede oplysninger om, hvordan IoT Hub understøtter MQTT protocol. I artiklen beskrives understøttelse af den indbyggede til SDK'er MQTT protokol og indeholder oplysninger om direkte ved hjælp af MQTT-protokollen.
- [Ordliste] [ devguide-glossary] en liste over almindelige IoT Hub-relaterede betingelser.



[devguide-messaging]: iot-hub-devguide-messaging.md
[devguide-upload]: iot-hub-devguide-file-upload.md
[devguide-identities]: iot-hub-devguide-identity-registry.md
[devguide-security]: iot-hub-devguide-security.md
[devguide-device-twins]: iot-hub-devguide-device-twins.md
[devguide-directmethods]: iot-hub-devguide-direct-methods.md
[devguide-jobs]: iot-hub-devguide-jobs.md
[devguide-endpoints]: iot-hub-devguide-endpoints.md
[devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[devguide-query]: iot-hub-devguide-query-language.md
[devguide-sdks]: iot-hub-devguide-sdks.md
[devguide-mqtt]: iot-hub-mqtt-support.md
[devguide-glossary]: iot-hub-devguide-glossary.md

