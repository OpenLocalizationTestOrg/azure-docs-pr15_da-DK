<properties
 pageTitle="Udvikler vejledning - enhed identitet registreringsdatabasen | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - beskrivelse af enhed identitet registreringsdatabasen og hvordan du kan bruge det til at administrere dine enheder"
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

# <a name="manage-device-identities-in-iot-hub"></a>Administrer identiteter enhed i IoT Hub

## <a name="overview"></a>Oversigt

Hver IoT hub har en enhed identitet registreringsdatabasen, der indeholder oplysninger om de enheder, der har tilladelse til at oprette forbindelse til hubben. Før en enhed kan oprette forbindelse til en-hub, skal der være en post for den pågældende enhed i den hub enhed identitet registreringsdatabasen. En enhed skal også godkende med hubben baseret på legitimationsoplysninger, der er gemt i registreringsdatabasen enhed identitet.

Enhed identitet registreringsdatabasen er en REST-kompatible samling af enhed identitet ressourcer på et højt niveau. Når du tilføjer en post i registreringsdatabasen, opretter IoT Hub et sæt per enhed ressourcer i tjenesten som den kø, der indeholder de meddelelser, startet cloud-til-enhed.

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Brug enhed identitet registreringsdatabasen, når du er nødt til at klargøre enheder, der tilsluttes du IoT-hub, og når du har brug at styre og enheder adgangen til enhed mod slutpunkterne i centrum.

> [AZURE.NOTE] Enhed identitet registreringsdatabasen indeholder ikke en hvilken som helst program-specifikke metadata.

## <a name="device-identity-registry-operations"></a>Enhed identitet registreringsdatabasen handlinger

IoT Hub enhed identitet registreringsdatabasen Fremviser følgende handlinger:

* Oprette enhed identitet
* Opdatere enhed identitet
* Hente enhed identitet ved hjælp af ID
* Slet enhed identitet
* Liste over op til 1000 identiteter
* Eksportere alle identiteter til Azure blob-lager
* Importere identiteter fra Azure blob-lager

Alle disse handlinger kan bruge optimistisk på dokumentsammenfald, som angivet i [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] Den eneste måde til at hente alle identiteter i en hub identitet registreringsdatabasen er at bruge [eksportere] [ lnk-export] funktionalitet.

En IoT Hub enhed identitet registreringsdatabasen:

- Indeholder ikke en hvilken som helst programmets metadata.
- Kan åbnes som en ordbog ved hjælp af **deviceId** som nøgle.
- Understøtter ikke udtryksfuld forespørgsler.

En IoT løsning har typisk en separat løsning-specifikke butik, der indeholder program-specifikke metadata. For eksempel vil løsning-specifikke store i en smart dokumentkomponent løsning post det rum, hvor en temperaturføler er installeret.

> [AZURE.IMPORTANT] Brug kun enhed identitet registreringsdatabasen til administration af enheder og klargøring handlinger. Høj overførselshastighed handlinger på kørselstidspunktet skal ikke afhænge udfører handlinger i enhed identitet registreringsdatabasen. For eksempel er kontrollere forbindelsestilstanden for en enhed før afsendelse af en kommando ikke et understøttede mønster. Sørg for at se, [throttling satser] [ lnk-quotas] for enhed identitet registreringsdatabasen og [godkendelse af enhed gyldighed] [ lnk-guidance-heartbeat] mønster.

## <a name="disable-devices"></a>Deaktivere enheder

Du kan deaktivere enheder ved at opdatere egenskaben **status** for en identitet i registreringsdatabasen. Typisk, kan du bruge denne egenskab i to scenarier:

- Under en klargøring organisering proces. Du kan finde flere oplysninger, se [Klargøring af enheden][lnk-guidance-provisioning].
- Hvis en eller anden grund, kan du overveje en enhed er skadet eller er blevet uautoriseret.

## <a name="import-and-export-device-identities"></a>Importere og eksportere enhed identiteter

Du kan eksportere enhed identiteter ad gangen fra en IoT hub identitet registreringsdatabasen, ved hjælp af asynkrone handlinger på [IoT Hub ressource udbyder slutpunkt][lnk-endpoints]. Eksporter er længerevarende job, der bruger en leveret af kunden blob objektbeholder til at gemme enhed identitetsdata, der læses fra registreringsdatabasen identitet.

Du kan importere enhed identiteter ad gangen til en IoT hub identitet registreringsdatabasen, ved hjælp af asynkrone handlinger på [IoT Hub ressource udbyder slutpunkt][lnk-endpoints]. Importerer er længerevarende job, der bruger data i en leveret af kunden blob objektbeholder til at skrive enhed identitetsdata til enhed identitet registreringsdatabasen.

- Du kan finde detaljerede oplysninger om import og eksport API'er, se [IoT Hub ressource udbyder REST API'er][lnk-resource-provider-apis].
- Hvis du vil vide mere om at køre importere og eksportere job, se [flere styring af IoT Hub enhed identiteter][lnk-bulk-identity].

## <a name="device-provisioning"></a>Klargøring af enheden

Datatypen enhed, der gemmer en given IoT løsning afhænger af særlige krav på denne løsning. Men som et minimum, en løsning skal lagre enhed identitet og godkendelsesnøgler. Azure IoT Hub omfatter en identitet registreringsdatabasen, som kan gemme værdier for hver enhed såsom id'er, godkendelsesnøgler og status-koder. En løsning kan bruge andre Azure tjenester som Azure-tabellager, Azure blob-lager eller Azure DocumentDB til at gemme enhedsdata yderligere.

*Klargøring af enheden* er processen med at tilføje den første enhedsdata til butikker i din løsning. Hvis du vil aktivere en ny enhed til at oprette forbindelse til din hub, skal du tilføje et nyt enheds-ID og nøgler til IoT Hub identitet registreringsdatabasen. Som en del af processen klargøring, kan det være nødvendigt at initialiseret enhed-specifikke data i andre løsning butikker.

## <a name="device-heartbeat"></a>Godkendelse af enhed gyldighed

IoT Hub identitet registreringsdatabasen indeholder et felt med navnet **connectionState**. Du skal kun bruge feltet **connectionState** under udvikling og fejlfinding, IoT løsninger, der skal ikke forespørge på feltet på kørselstidspunktet (for eksempel, at kontrollere, om en enhed har forbindelse for at beslutte, om du vil sende en meddelelse i skyen-til-enhed eller en SMS-besked).

Hvis din IoT løsningen skal du ved, om en enhed er tilsluttet (enten på kørselstidspunktet, eller med større præcision, end egenskaben **connectionState** indeholder), din løsning skal implementere *godkendelse gyldighed mønster*.

I mønster godkendelse gyldighed sender enheden enhed i skyen meddelelser mindst én gang hver fast mængde tid (for eksempel, mindst én gang hver time). Det betyder, at selvom en enhed ikke har data, der skal sende, det stadig sender en tom enhed i skyen meddelelse (som regel med en egenskab, der identificerer den som en godkendelse gyldighed). På siden service løsningen fører et kort med den sidste godkendelse gyldighed modtaget for hver enhed, og antager, at der er et problem med en enhed, hvis det ikke modtager en meddelelse til godkendelse af gyldighed inden for det forventede tidsrum.

En mere kompleks implementering kan indeholde oplysninger fra [Handlinger overvågning] [ lnk-devguide-opmon] til at identificere enheder, der forsøger at oprette forbindelse eller kommunikere men ned. Når du implementerer godkendelse gyldighed mønster, skal du kontrollere [IoT Hub kvotaer og Throttles][lnk-quotas].

> [AZURE.NOTE] Hvis en IoT løsningen skal forbindelsestilstanden enhed udelukkende til at afgøre, om du vil sende meddelelser i skyen-til-enhed, og meddelelser er ikke transmittere til store datasæt i enheder, er en meget nemmere mønster du bør overveje at bruge gangen korte udløbet. Dette opnås det samme resultat som vedligeholde en enhed forbindelse tilstand registreringsdatabasen ved hjælp af godkendelse af gyldighed mønsteret, mens der betydeligt mere effektivt. Det er også muligt, ved at anmode om meddelelse godkendelser, at få besked via IoT hubben på hvilke enheder, der kan modtage meddelelser og som ikke er online, eller der ikke er markeret.

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om devcie identitet registreringsdatabasen.

## <a name="device-identity-properties"></a>Egenskaber for enheden identitet

Enhed identiteter repræsenteres som JSON dokumenter med følgende egenskaber.

| Egenskaben | Indstillinger | Beskrivelse |
| -------- | ------- | ----------- |
| deviceId | påkrævet, skrivebeskyttet opdateringer | En store og små bogstaver streng (op til 128 tegn lang) af ASCII-7-bit alfanumeriske tegn + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | påkrævet, skrivebeskyttet | En hub oprettede, store og små bogstaver streng op til 128 tegn. Det bruges til at skelne enheder med samme **deviceId**, når de har slettet og genoprettes. |
| ETag | påkrævet, skrivebeskyttet | En streng, der repræsenterer en svage etag for enhed identitet, ud fra [RFC7232][lnk-rfc7232].|
| auth | valgfri | En sammensat objekt, der indeholder godkendelse oplysninger og sikkerhed materialer. |
| auth.symkey | valgfri | En sammensat objekt, der indeholder en primær og sekundær nøgle, gemmes i base64 format. |
| status | påkrævet | En access-indikator. Kan være **aktiveret** eller **deaktiveret**. Hvis **aktiveret**enheden har tilladelse til at oprette forbindelse. Hvis **deaktiveret**, denne enhed ikke kan få adgang til en hvilken som helst enhed mod slutpunkt. |
| statusReason | valgfri | En 128 tegn lang streng, der gemmer årsagen til, at Enhedsstatus er identitet. Alle UTF-8 tegn er tilladt. |
| statusUpdateTime | skrivebeskyttet | En tidsmæssig indikator, der viser datoen og klokkeslættet for den seneste statusopdatering af. |
| connectionState | skrivebeskyttet | Et felt, der angiver forbindelsesstatus: **tilsluttet** eller **forbindelsen er afbrudt**. Dette felt repræsenterer IoT Hub visningen af forbindelsesstatus enhed. **Vigtigt**: Dette felt skal bruges kun til udvikling/fejlfinding formål. Forbindelsestilstanden opdateres kun for enheder, der bruger MQTT eller AMQP. Desuden er baseret på protocol niveau pinger (MQTT pinger eller AMQP pinger), og det kan have en maksimale forsinkelse af kun fem minutter. Disse årsager, kan der være forkerte forekomster, som enheder rapporteret forbindelse, men, der faktisk er afbrudt. |
| connectionStateUpdatedTime | skrivebeskyttet | En tidsmæssig indikator, der viser den dato og det sidste gang forbindelsestilstanden blev opdateret. |
| lastActivityTime  | skrivebeskyttet | En tidsmæssig indikator, der viser den dato og det sidste gang enheden forbindelse, modtaget eller sendt en meddelelse. |

> [AZURE.NOTE] Forbindelsesmåden kan kun repræsentere IoT Hub visning af status for forbindelsen. Opdateringer til denne tilstand kan udskydes, afhængigt af netværk betingelser og konfigurationer.

## <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Nu du har lært, hvordan du bruger IoT Hub enhed identitet registreringsdatabasen, kan du måske interesseret i Developer Guide følgende emner:

- [Styre adgangen til IoT Hub][lnk-devguide-security]
- [Bruge enhed twins til at synkronisere stat og konfigurationer][lnk-devguide-device-twins]
- [Aktivere en direkte metode på en enhed][lnk-devguide-directmethods]
- [Planlægge opgaver på flere enheder][lnk-devguide-jobs]

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i det følgende IoT Hub Selvstudium:

- [Introduktion til Azure IoT Hub][lnk-getstarted-tutorial]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md