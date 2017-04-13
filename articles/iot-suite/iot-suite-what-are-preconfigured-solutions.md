<properties
 pageTitle="Azure IoT forudkonfigureret løsninger | Microsoft Azure"
 description="En beskrivelse af Azure IoT forudkonfigureret løsninger og deres arkitektur med links til yderligere ressourcer."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Hvad er Azure IoT pakke forudkonfigureret løsninger?

Azure IoT pakke forudkonfigureret løsninger er almindelige IoT løsning mønstre, som du kan installere til Azure med dit abonnement-installationer. Du kan bruge de forudkonfigurerede løsninger:

- Som et udgangspunkt til dine egne IoT løsninger.
- Få at vide om almindelige mønstre i IoT løsningsdesign og udvikling.

Hver forudkonfigurerede løsning er en komplet og til slut implementering, der bruger simuleret enheder til at generere telemetri.

Ud over at implementere og kører løsningerne i Azure, kan du hente den komplette kildekode og derefter tilpasse og udvide løsningen til bestemte IoT behov.

> [AZURE.NOTE] For at installere en af de forudkonfigurerede løsninger, skal du besøge [Microsoft Azure IoT Suite][lnk-azureiotsuite]. I artiklen [Introduktion til IoT forudkonfigureret løsningerne] [ lnk-getstarted-preconfigured] indeholder flere oplysninger om, hvordan du installere og køre en af løsningerne.

Tabellen nedenfor viser, hvordan løsningerne, der knyttes til bestemte IoT funktioner:

| Løsning | Data indtagelse | Enhed identitet | Kommandoen og kontrol | Regler og handlinger | Skønnet Analytics |
|------------------------|-----|-----|-----|-----|-----|
| [Remote overvågning][lnk-getstarted-preconfigured] | Ja | Ja | Ja | Ja | -   |
| [Skønnet vedligeholdelse][lnk-predictive-maintenance] | Ja | Ja | Ja | Ja | Ja |

- *Data indtagelse*: indtrængen af data på Skaler til skyen.
- *Enhed identitet*: administrere entydige identiteter af alle forbundne enheder.
- *Styrings- og*: sende meddelelser til en enhed fra skyen årsagen kameraet, for at udføre en handling til.
- *Regler og handlinger*: løsning back-end bruger regler til at handle på bestemte enhed i skyen data.
- *Skønnet analytics*: løsning back-end gælder analyserer enhed i skyen data for at forudsige, når bestemte handlinger skal træde i kraft. For eksempel at analysere fly program telemetri for at afgøre, hvornår program vedligeholdelse forfalder.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Remote overvågnings forudkonfigureret løsning oversigt

Vi har valgt at diskutere remote overvågning forudkonfigurerede løsningen i denne artikel, fordi den illustrerer mange almindelige designelementer, som deler de andre løsninger.

I følgende diagram vises de vigtigste elementer i remote overvågning løsningen. I nedenstående afsnit indeholder flere oplysninger om disse elementer.

![Remote overvågnings forudkonfigureret løsningsarkitektur][img-remote-monitoring-arch]

## <a name="devices"></a>Enheder

Når du installerer remote overvågning forudkonfigurerede løsningen, er fire simuleret enheder allerede klargjort i den løsning, simulere køling. Disse simuleret enheder har en indbygget temperatur og luftfugtighed model, udsender telemetri. Disse simuleret enheder er inkluderet, til at illustrere strømmen af data via løsningen til slut, og at give praktisk kilde til telemetri og et mål for kommandoer, hvis du er en back end-udvikler ved hjælp af løsningen som udgangspunkt for en brugerdefineret implementering.

Når en enhed først tilsluttes IoT Hub i remote overvågning forudkonfigurerede løsningen, optælles meddelelsen med enhed sendes til IoT hubben på listen over kommandoer, som enheden kan svare på. Kommandoerne, der er i remote overvågning forudkonfigurerede løsningen: 

- *Ping enhed*: enheden svarer til denne kommando med en bekræftelse. Dette er nyttigt for at kontrollere, at enheden er stadig aktiv, og lytte.
- *Starte Telemetri*: giver besked om kameraet, for at begynde at sende telemetri.
- *Stoppe Telemetri*: giver besked om kameraet, for at ophøre med at sende telemetri.
- *Ændre Indstil punkt temperaturen*: styrer simuleret temperaturen telemetri værdierne sender enheden. Dette er nyttigt til test back end-logik.
- *Diagnosticering Telemetri*: objekter, hvis enheden skal sendes eksterne temperaturen som telemetri.
- *Ændre Enhedsstatus*.: angiver egenskaben enhed state metadata, at enheden rapporter. Dette er nyttigt til test back end-logik.

Du kan tilføje mere simuleret enheder på den løsning, udsender den samme telemetri og svare på de samme kommandoer. 

## <a name="iot-hub"></a>IoT-Hub

I denne forudkonfigurerede løsning forekomsten IoT Hub svarer til *Skyen Gateway* i en typisk [IoT løsningsarkitektur][lnk-what-is-azure-iot].

En IoT hub modtager telemetri fra enhederne på et enkelt slutpunkt. En IoT hub bevarer også enhed bestemte slutpunkter for hvor hvert enheder kan hente de kommandoer, der sendes til den.

IoT hubben gør den modtagne telemetri gennem den tjeneste side telemetri læse slutpunkt.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Forudkonfigurerede løsningen anvender tre [Azure Stream Analytics] [ lnk-asa] (ASA) job til at filtrere telemetri strømmen fra enhederne:


- *DeviceInfo job* - udskriver data til en begivenhed-hub, som omdirigerer enhed registrering bestemte meddelelser sendes, når en enhed først opretter forbindelse, eller som svar på en **Skift status for enheden** -kommando til løsning enhed registreringsdatabasen (en DocumentDB database). 
- *Telemetri job* - sender alle rå telemetri til Azure blob-lager til lagring af kolde og beregner telemetri sammenlægninger, vises i dashboardet løsning.
- *Regler job* – filtrerer telemetri strømmen for værdier, der overskrider en hvilken som helst regel tærskler og skriver dataene til en begivenhed hub. Når en regel udløses, visningen løsning portalen dashboard viser begivenheden som en ny række i tabellen alarm historik og udløser en handling, der er baseret på de indstillinger, der er defineret i visningerne regler og handlinger i portalen løsning.

I denne forudkonfigurerede løsning ASA job udgør en del af den **IoT løsning tilbage slutningen** i en typisk [IoT løsningsarkitektur][lnk-what-is-azure-iot].

## <a name="event-processor"></a>Begivenhed processor

I denne forudkonfigurerede løsning begivenhed processor danner en del af den **IoT løsning tilbage slutningen** i en typisk [IoT løsningsarkitektur][lnk-what-is-azure-iot].

**DeviceInfo** og **regler** ASA job sender deres output til begivenhed hubs til levering til andre back-end-tjenester. Løsningen bruger en [EventPocessorHost] [ lnk-event-processor] forekomst, kører i et [WebJob][lnk-web-job], at læse meddelelser fra disse begivenhed hubs. **EventProcessorHost** bruger datatypen **DeviceInfo** til at opdatere dataene i databasen DocumentDB enhed og bruger datatypen **regler** til at kalde logik app og opdatering af beskeder vises i portalen løsning.

## <a name="device-identity-registry-and-documentdb"></a>Enhed identitet registreringsdatabasen og DocumentDB

Hver IoT hub omfatter en [enhed identitet registreringsdatabasen] [ lnk-identity-registry] , hvor enhed taster. IoT Hub bruger disse oplysninger godkende enheder – en enhed skal være registreret og har en gyldig nøgle, før den kan oprette forbindelse til den pågældende hub.

Denne løsning gemmer yderligere oplysninger om enheder, som deres tilstand, de understøtter kommandoerne og andre metadata. Løsningen bruger en DocumentDB database til at gemme denne løsning-specifikke enhedsdata og portalen løsning henter data fra denne DocumentDB database til visning og redigering.

Løsningen skal også holde dine oplysninger i enhed identitet registreringsdatabasen synkroniseret med indholdet af DocumentDB databasen. **EventProcessorHost** bruger dataene fra **DeviceInfo** stream analytics tingene til at administrere synkroniseringen.

## <a name="solution-portal"></a>Løsning portal

![Løsning dashboard][img-dashboard]

Portalen løsning er en webbaseret brugergrænseflade, der er implementeret i skyen som en del af den forudkonfigurerede løsning. Det gør muligt at:

- Få vist telemetri og alarm oversigt i et dashboard.
- Klargøring af nye enheder.
- Administrere og overvåge enheder.
- Sende kommandoer til bestemte enheder.
- Administrer regler og handlinger.

I denne forudkonfigurerede løsning løsning portalen udgør en del af den **IoT løsning tilbage slutningen** og en del af **behandling og business connectivity** i typisk [IoT løsningsarkitektur][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Næste trin

Finde flere oplysninger om IoT løsning arkitekturer [Microsoft Azure IoT services: referencearkitektur][lnk-refarch].

Nu du ved, hvad en forudkonfigureret løsning er, du kan komme i gang ved at installere løsningen *remote overvågning* forudkonfigureret: [Introduktion til de forudkonfigurerede løsninger][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md