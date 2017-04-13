<properties
 pageTitle="Udvikler vejledning - messaging | Microsoft Azure"
 description="Azure IoT Hub udvikler vejledning - enhed i skyen og messaging cloud-til-enhed"
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

# <a name="send-and-receive-messages-with-iot-hub"></a>Sende og modtage meddelelser med IoT Hub

## <a name="overview"></a>Oversigt

IoT Hub indeholder følgende SMS primitiver til at kommunikere med en enhed:

- [Enhed i skyen] [ lnk-d2c] fra en enhed til et program igen slutningen.
- [Cloud-til-enhed] [ lnk-c2d] tilbage slutningen (*tjenesten* eller *skyen*) fra et program.

Core egenskaber for IoT Hub SMS funktionalitet er pålidelighed og holdbarhed af meddelelser. Disse egenskaber aktivere spændstighed til vekslende forbindelse på siden enhed, og at indlæse spidser i begivenhed behandling i skyen-side. IoT Hub implementerer *mindst én gang* levering garantier til både enhed i skyen og skyen-til-enhed messaging.

IoT Hub understøtter flere [enhed mod protokoller] [ lnk-protocols] (såsom MQTT, AMQP og HTTP). For at understøtte problemfri kompatibilitet på tværs af protokoller, IoT Hub definerer et [almindelige meddelelsesformat] [ lnk-message-format] , der understøtter alle enhed mod protokoller.

IoT Hub Fremviser [begivenhed Hub-kompatible slutpunkt] [ lnk-compatible-endpoint] til at give back end-programmer til at læse de enhed i skyen-meddelelser, der modtages af hubben.

### <a name="when-to-use"></a>Hvornår skal jeg bruge

Messaging er en central funktion af IoT Hub. Bruge den, når du har brug at sende meddelelser fra enheden til din back-end- eller sende meddelelser fra din back-end til en enhed.

En sammenligning af tjenesterne IoT Hub og begivenhed Hubs se [sammenligning af IoT Hub og begivenhed Hubs][lnk-compare].

## <a name="device-to-cloud-messages"></a>Enhed i skyen meddelelser

Du sender enhed i skyen meddelelser via et enhed mod slutpunkt (**/devices/ {deviceId} / meddelelser/hændelser**). Din back end-tjenesten modtager enhed i skyen meddelelser via en med adgang til tjenesten slutpunkt (**/messages/events**), der er kompatibelt med [Begivenhed Hubs][lnk-event-hubs]. Derfor kan du bruge standard [begivenhed Hubs integration og SDK'er] [ lnk-compatible-endpoint] til at modtage meddelelser enhed i skyen.

IoT Hub implementerer enhed i skyen messaging på en måde, som minder om [Begivenhed Hubs][lnk-event-hubs]. IoT Hub enhed i skyen meddelelser er mere ligner begivenhed Hubs *begivenheder* end [Service Bus] [ lnk-servicebus] *meddelelser*.

Denne implementering har følgende konsekvenserne:

* På samme måde begivenhed Hubs begivenheder enhed i skyen meddelelser er pålidelig og opbevares i en IoT hub i op til syv dage (se [enhed i skyen konfigurationsindstillinger][lnk-d2c-configuration]).
* Er opdelt enhed i skyen meddelelser på tværs af et fast antal partitioner, der er angivet til klokkeslættet for oprettelsen af (se [enhed i skyen konfigurationsindstillinger][lnk-d2c-configuration]).
* Analogously til begivenhed hubber skal klienter læser enhed i skyen meddelelser håndtere partitioner og checkpointing. Se [Begivenhed Hubs - forbrug begivenheder][lnk-event-hubs-consuming-events].
* Som begivenhed Hubs begivenheder enhed i skyen meddelelser kan være højst 256 KB og kan grupperes i batches til at optimere sender. Navne må maksimalt være 256 KB, og højst 500 meddelelser.

Der er dog nogle vigtige forskelle mellem IoT Hub enhed i skyen messaging og begivenhed Hubs:

* Som beskrevet i [kontrollere adgang til IoT Hub] [ lnk-devguide-security] i afsnittet, IoT Hub kan per enhed godkendelse og adgangskontrol.
* IoT Hub gør det muligt for millioner af samtidigt forbundne enheder (se [kvoter og (throttling)][lnk-quotas]), mens begivenhed Hubs er begrænset til 5000 AMQP forbindelser per navneområde.
* IoT Hub tillader ikke vilkårlig partitionering ved hjælp af en **PartitionKey**. Enhed i skyen meddelelser er opdelt baseret på deres oprindelige **deviceId**.
* Skalering IoT Hub er en smule anderledes end skalering begivenhed Hubs. Du kan finde flere oplysninger, se [Skalering IoT Hub][lnk-guidance-scale].

> [AZURE.NOTE] Du kan erstatte IoT Hub for begivenhed Hubs alle scenarier. Det kan for eksempel være nødvendigt at opdele begivenheder i forhold til en anden egenskab eller et felt før analyse af datastreams i nogle begivenhed behandling beregninger. I dette scenarie skal kunne du bruge en begivenhed Hub til decouple to dele af strømmen behandling pipeline. Du kan finde flere oplysninger, se *partitioner* i [Azure begivenhed Hubs oversigt][lnk-eventhub-partitions].

Yderligere oplysninger om, hvordan du bruger enhed i skyen messaging, finder du [IoT Hub API'er og SDK'er][lnk-sdks].

> [AZURE.NOTE] Når du bruger HTTP til at sende meddelelser enhed i skyen, navne og værdier kan kun indeholde ASCII-alfanumeriske tegn, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Ikke-telemetri trafik

Ofte, ud over telemetri datapunkter send enheder også meddelelser og anmodninger, der kræver udførelse af og håndtering af fra programmet forretningslogik. For eksempel vigtige beskeder, der skal udløse en bestemt handling i back-end eller enhed svar på kommandoer, der sendes fra back-end.

Finde flere oplysninger om den bedste måde at behandle denne type meddelelse på [Selvstudium: hvordan til at behandle IoT Hub enhed i skyen meddelelser] [ lnk-d2c-tutorial] selvstudium.

### <a name="device-to-cloud-configuration-options"></a>Enhed i skyen konfigurationsindstillinger

En IoT hub Fremviser følgende egenskaber for at gør det muligt at styre enhed i skyen messaging.

* **Antal partition**. Angive denne egenskab ved oprettelse af til at definere antallet af partitioner for enhed i skyen begivenhed indtagelse.
* **Opbevaring tid**. Denne egenskab angiver, hvor længe enhed i skyen meddelelser. Standard er én dag, men den kan øges til syv dage.

Desuden analogously til begivenhed hubber IoT Hub gør det muligt at administrere consumer grupper på enhed i skyen-modtager slutpunkt.

Du kan redigere alle disse egenskaber, enten fra et program gennem [IoT Hub ressource udbyder REST API'er][lnk-resource-provider-apis], eller ved at bruge [Azure portal][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Teknologi til forhindring mailspoofing egenskaber

For at undgå enhed mailspoofing i enhed i skyen meddelelser, IoT Hub stempler alle meddelelser med følgende egenskaber:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

De første to indeholder **deviceId** og **generationId** af kommer fra enheden, ud fra [Egenskaber for enheden identitet][lnk-device-properties].

Egenskaben **ConnectionAuthMethod** indeholder et JSON serialiseret objekt med følgende egenskaber:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Cloud-til-enhed meddelelser

Du kan sende cloud-til-enhed meddelelser via et med adgang til tjenesten slutpunkt (**/messages/devicebound**). En enhed modtager dem via en enhed-specifikke slutpunkt (**/devices/ {deviceId} / meddelelser/devicebound**).

Alle de meddelelser, cloud-til-enhed henvender sig til en enkelt enhed ved at angive egenskaben **til** til **/devices/ {deviceId} / meddelelser/devicebound**.

>[AZURE.IMPORTANT] Hver enhed køen indeholder højst 50 cloud-til-enhed meddelelser. Forsøger at sende flere meddelelser til de samme enhed medfører en fejl.

> [AZURE.NOTE] Når du sender meddelelser til skyen-til-enhed, navne og værdier kan kun indeholde ASCII-alfanumeriske tegn, plus ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Meddelelse livscyklus

For at garantere mindst én gang levering af meddelelser, fortsætter IoT Hub cloud-til-enhed meddelelser i hver kommunikationsenhedskøer. Enheder skal eksplicit bekræftelse *fuldførelse* for IoT Hub at fjerne dem fra køen. Dette garanterer fleksibilitet mod forbindelsen og mislykkede forsøg på enheden.

I det følgende diagram viser grafen tilstand livscyklus for en meddelelse i skyen-til-enhed.

![Cloud-til-enhed meddelelse livscyklus][img-lifecycle]

Når tjenesten sender en meddelelse, betragtes det som *sat i kø*. Når en enhed ønsker at *modtage* en meddelelse, IoT Hub *låse* meddelelsen (indstiller tilstanden til **usynlige**) tillade andre tråde på den samme enhed begynde at modtage andre meddelelser. Når en enhed tråd afsluttet behandlingen af en meddelelse, om IoT Hub ved at *gennemførelse af* meddelelsen.

En enhed kan også:

- *Afvise* meddelelsen, som medfører, at IoT Hub til at angive den til **Deadlettered** tilstanden. Bemærk: enheder oprette forbindelse med MQTT kan ikke afvise C2D meddelelser.
- *Annullere* den meddelelse, som medfører, at IoT Hub placere meddelelsen tilbage i køen, med tilstanden, der er angivet til **sat i kø**.

En tråd kan ikke behandle en meddelelse uden at give besked IoT Hub. I dette tilfælde overgang meddelelser automatisk fra tilstanden **usynlige** tilbage til tilstanden **sat i kø** efter *timeout for synlighed (eller låse)*. Standardværdien for denne timeout er ét minut.

Meddelelsen kan overgang mellem **sat i kø** og **usynlige** tilstandene for højst antallet gange, der er angivet i egenskaben **Maks levering count** på IoT Hub. Efter dette antal overgange indstiller IoT Hub tilstanden af meddelelsen til **Deadlettered**. På samme måde, IoT Hub indstiller tilstanden for en meddelelse til **Deadlettered** efter dens udløbsdatoen (se [tid til live][lnk-ttl]).

Selvstudium til skyen-til-enhed meddelelser, se [Selvstudium: hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub][lnk-c2d-tutorial]. Du kan finde referenceemner om, hvordan forskellige API'er og SDK'er afsløre funktionen cloud-til-enhed, under [IoT Hub API'er og SDK'er][lnk-sdks].

> [AZURE.NOTE] Typisk, udfører cloud-til-enhed meddelelser, når tab af meddelelsen ikke kan påvirke programmet logik. For eksempel meddelelsesindhold er blevet bevaret i lokale lager, eller handlingen er udført korrekt. Meddelelsen kan også udførelsen midlertidige oplysninger, hvis tab ikke vil påvirke funktionaliteten i programmet. Nogle gange for længerevarende opgaver, kan du udfylde meddelelsen cloud-til-enhed efter vedvarer opgavebeskrivelse i lokale lager. Derefter kan du give besked programmet back-end med en eller flere enhed i skyen meddelelser på forskellige stadier af statussen for opgaven.

### <a name="message-expiration-time-to-live"></a>Udløb af meddelelser (tid til live)

Alle meddelelser, cloud-til-enhed har et udløbstidspunkt. Nuværende tidspunkt er angivet af tjenesten (i egenskaben **ExpiryTimeUtc** ) eller ved IoT Hub ved hjælp af standard *tid til live* specificeret som en IoT Hub egenskab. Se [Cloud-til-enhed konfigurationsindstillinger][lnk-c2d-configuration].

> [AZURE.NOTE] En almindelig måde at drage fordel af udløb af meddelelser og undgå at er sende meddelelser til afbrudt enheder, at Angiv kort tid til live værdier. Denne metode opnås det samme resultat som vedligeholde forbindelsestilstanden enhed, mens der mere effektivt. Når du anmoder om meddelelse godkendelser, IoT Hub om hvilke enheder er at modtage meddelelser, og hvilke enheder, der ikke er online eller er mislykket.

### <a name="message-feedback"></a>Besked feedback

Når du sender en meddelelse i skyen-til-enhed, kan tjenesten anmode om leveringen af hver meddelelse tilbagemeldinger med henblik på sluttilstand af denne meddelelse.

- Hvis du angiver egenskaben **Ack** til **positive**, genererer IoT Hub meddelelsen feedback, hvis, og kun hvis, meddelelsen cloud-til-enhed har nået tilstanden **fuldført** .
- Hvis du angiver egenskaben **Ack** til **negative**, genererer IoT Hub meddelelsen feedback, men kun hvis, cloud-til-enhed meddelelsen når tilstanden **Deadlettered** .
- Hvis du indstiller egenskaben **Ack** til **fuld**, genererer IoT Hub meddelelsen feedback i begge tilfælde.

> [AZURE.NOTE] Hvis **Ack** er **fuld**, og du ikke modtager meddelelsen feedback, betyder det, at meddelelsen feedback er udløbet. Tjenesten kan ikke ved, Hvad skete der med den oprindelige meddelelse. I praksis skal en tjeneste sikre, at det kan behandle feedbacken, før det udløber. Den maksimale udløbstid to dage, som giver mulighed for masser af tid til at få tjenesten kører igen Hvis der opstår fejl.

Som beskrevet i [slutpunkter][lnk-endpoints], IoT Hub leverer feedback gennem et med adgang til tjenesten slutpunkt (**/messages/servicebound/feedback**) som meddelelser. Semantik for at modtage feedback er den samme som for cloud-til-enhed meddelelser, og har den samme [meddelelse livscyklus][lnk-lifecycle]. Når det er muligt, batches besked feedback i en enkelt meddelelse med følgende format:

| Egenskaben | Beskrivelse |
| -------- | ----------- |
| EnqueuedTime | Tidsstempel, der angiver, hvor meddelelsen blev oprettet. |
| Bruger-id | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Teksten er en JSON-serialiseret matrix af poster, hver med følgende egenskaber:

| Egenskaben | Beskrivelse |
| -------- | ----------- |
| EnqueuedTimeUtc | Tidsstempel, der angiver når med resultatet af meddelelsen er der sket. Den enhed, der er fuldført eller meddelelsen udløbet. |
| OriginalMessageId | **MessageId** i skyen-til-enhed meddelelsen som denne feedback information gælder. |
| Statuskode | Påkrævet heltal. Bruges i feedback meddelelser, der genereres af IoT Hub. <br/> 0 = succes <br/> 1 = meddelelsen er udløbet <br/> 2 = levering af maksimalt antal hop <br/> 3 = meddelelse afvist |
| Beskrivelse | Strengværdier **statuskode**. |
| DeviceId | **DeviceId** target enhed i skyen-til-enhed meddelelsen som denne del af en feedback gælder. |
| DeviceGenerationId | **DeviceGenerationId** mål enhed i skyen-til-enhed meddelelsen som denne del af en feedback gælder. |


>[AZURE.IMPORTANT] Tjenesten skal angive et **MessageId** til meddelelsen cloud-til-enhed skal kunne koordinere dens feedback med den oprindelige meddelelse.

I følgende eksempel viser brødteksten i meddelelsen feedback.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Cloud-til-enhed konfigurationsindstillinger

Hver IoT hub Fremviser følgende konfigurationsmuligheder til skyen-til-enhed messaging.

| Egenskaben | Beskrivelse | Område- og standard |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Standard-TTL til skyen-til-enhed meddelelser. | ISO_8601 interval op til 2D (minimum 1 minut). Standard: 1 time. |
| maxDeliveryCount | Levering af maksimalt antal for cloud-til-enhed per kommunikationsenhedskøer. | 1 til 100. Som standard: 10. |
| feedback.ttlAsIso8601 | Opbevaring for tjenesten bundet feedback meddelelser. | ISO_8601 interval op til 2D (minimum 1 minut). Standard: 1 time. |
| feedback.maxDeliveryCount | Levering af maksimalt antal for feedback kø. | 1 til 100. Som standard: 100. |

Kan finde flere oplysninger i [oprette IoT hubs][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Læse meddelelser enhed i skyen

IoT Hub Fremviser et slutpunkt for dine back end-tjenester til at læse de enhed i skyen-meddelelser, der modtages af centrum. Slutpunktet er begivenhed Hub-kompatible, som gør det muligt at bruge en af funktioner til tjenesten begivenhed Hubs understøtter til at læse meddelelser.

Når du bruger [Azure Service Bus SDK til .NET] [ lnk-servicebus-sdk] eller [Begivenhed Hubs - begivenhed Processor Host][lnk-eventprocessorhost], kan du bruge en hvilken som helst IoT Hub forbindelsesstrenge med de rette tilladelser. Brug derefter **meddelelser/begivenheder** som navnet på begivenheden Hub.

Når du bruger SDK'er (eller produkt integrationer), der ikke bekendt med IoT-Hub, skal du hente en begivenhed Hub-kompatible slutpunkt og begivenhed Hub-kompatible navn fra IoT Hub indstillingerne i [Azure portal][lnk-management-portal]:

1. Klik på **meddelelser**i bladet IoT hub.
2. I sektionen **enhed i skyen settings** skal du finde følgende værdier: **begivenhed Hub-kompatible slutpunkt**, **begivenhed Hub-kompatible navn**og **partitioner**.

    ![Indstillinger for enhed i skyen][img-eventhubcompatible]

> [AZURE.NOTE] Hvis SDK kræver værdien **værtsnavn** eller **Namespace** , skal du fjerne skemaet fra **begivenhed Hub-kompatible slutpunkt**. Hvis din begivenhed Hub-kompatible slutpunkt er **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, **Hostname** ville være **iothub-ns-myiothub-1234.servicebus.windows.net**og **Namespace** ville være **iothub-ns-myiothub-1234**f.eks.

Du kan derefter bruge en hvilken som helst delt adgang sikkerhedspolitik, der indeholder **ServiceConnect** tilladelserne til at oprette forbindelse til den angivne begivenhed Hub.

Hvis du vil oprette en begivenhed Hub forbindelsesstreng ved hjælp af de tidligere oplysninger, skal du bruge følgende mønster:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Følgende er en liste over SDK'er og integrationer, som du kan bruge med begivenhed Hub-kompatible slutpunkter, IoT Hub Fremviser:

* [Java begivenhed Hubs klient](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Du kan få vist den [spout kilde](https://github.com/apache/storm/tree/master/external/storm-eventhubs) på GitHub.
* [Apache knallertmotor integration](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Referenceemner i:

De følgende referenceemner giver dig flere oplysninger om udveksling af meddelelser med IoT Hub.

## <a name="message-format"></a>Meddelelsesformat

IoT Hub meddelelser omfatter:

* Et sæt af *Egenskaber for system*. Egenskaber, der IoT Hub fortolker eller indstiller. Dette sæt er foruddefinerede.
* Et sæt af *Egenskaber for tjenesteprogram*. En ordbog med strengegenskaber, som programmet kan definere og access, uden at skulle deserialisere meddelelsens brødtekst. IoT Hub ændrer aldrig disse egenskaber.
* En uigennemsigtig binære brødtekst.

Du kan finde flere oplysninger om, hvordan meddelelsen kodes i forskellige protokoller, se [IoT Hub API'er og SDK'er][lnk-sdks].

I følgende tabel vises sæt af egenskaber for system i IoT Hub meddelelser.

| Egenskaben | Beskrivelse |
| -------- | ----------- |
| MessageId | Et bruger-angives id for den meddelelse, bruges til anmodning om autosvar mønstre. Format: En store og små bogstaver streng (op til 128 tegn lang) af ASCII-7-bit alfanumeriske tegn + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Fortløbende nummer | Et tal (entydige for hver enhed kø) tildelt ved IoT Hub til hver meddelelse i skyen-til-enhed. |
| Til | En destination, der er angivet i [Skyen-til-enhed] [ lnk-c2d] meddelelser. |
| ExpiryTimeUtc | Dato og klokkeslæt for udløb af meddelelser. |
| EnqueuedTime | Dato og klokkeslæt meddelelsen blev modtaget af IoT Hub. |
| CorrelationId | En strengegenskab i en svarmeddelelse, der typisk indeholder MessageId for din anmodning i anmodning autosvar mønstre. |
| Bruger-id | Et ID, der bruges til at angive udgangspunktet for meddelelser. Når meddelelser er genereret IoT Hub, den er indstillet til `{iot hub name}`. |
| ACK | En feedback meddelelse generator. Denne egenskab bruges i skyen-til-enhed meddelelser til at anmode om IoT Hub til at generere feedback meddelelser som et resultat af forbrug meddelelsens af enheden. Mulige værdier: **ingen** (standard): ingen feedback-meddelelse er oprettet, **positive**: modtager en meddelelse om feedback, hvis meddelelsen blev afsluttet, **negative**: modtager en meddelelse om feedback, hvis meddelelsen er udløbet (eller levering af maksimalt antal er nået) uden at blive fuldført af den enhed, eller **fuld**: både positive og negative. Kan finde flere oplysninger under [besked feedback][lnk-feedback]. |
| ConnectionDeviceId | Et ID, der er angivet af IoT Hub i de meddelelser, enhed i skyen. Den indeholder **deviceId** af den enhed, der har sendt meddelelsen. |
| ConnectionDeviceGenerationId | Et ID, der er angivet af IoT Hub i de meddelelser, enhed i skyen. Den indeholder **generationId** (ud fra [Egenskaber for enheden identitet][lnk-device-properties]) af den enhed, der har sendt meddelelsen. |
| ConnectionAuthMethod | En godkendelsesmetode, der er angivet af IoT Hub i de meddelelser, enhed i skyen. Denne egenskab indeholder oplysninger om den anvendte godkendelsesmetode, som bruges til at godkende den enhed, der sender meddelelsen. Du kan finde flere oplysninger, se [enhed til skyen forfalskning teknologi til forhindring af][lnk-antispoofing].|

## <a name="communication-protocols"></a>Kommunikationsprotokoller

IoT Hub kan bruge [MQTT]-enheder[lnk-mqtt], MQTT over WebSockets, [AMQP][lnk-amqp], AMQP over WebSockets og HTTP-protokoller for enhed side-kommunikation. Den følgende tabel indeholder overordnet anbefalinger til dit valg af protokol:

| Protokol | Når du skal vælge denne protokol |
| -------- | ------------------------------------ |
| MQTT <br> MQTT over WebSocket     | Brug på alle enheder, der ikke har brug for at oprette forbindelse flere enheder (hver med sin egen per enhed legitimationsoplysninger) via den samme TLS-forbindelse. |
| AMQP <br> AMQP over WebSocket    | Brug på feltet og skyen gateways udnytte forbindelse multiplexing på tværs af enheder. |
| HTTP    | Bruges til enheder, der ikke understøtter andre protokoller. |

Overvej følgende punkter, når du vælger din protokol for enhed side-kommunikation:

* **Cloud-til-enhed mønster**. HTTP har ikke en effektiv måde at implementere server opslagsnål. Når du bruger HTTP, afstemning enheder som f.eks IoT Hub til skyen-til-enhed meddelelser. Denne metode er ineffektiv til både feltet enhed og IoT Hub. Hver enhed skal under aktuelle HTTP retningslinjerne afstemning for meddelelser, hver 25 minutter eller mere. På den anden side MQTT og AMQP understøtter server opslagsnål ved modtagelse af meddelelser i skyen-til-enhed. De gør det muligt for øjeblikkelig skubber af meddelelser fra IoT Hub på enheden. Hvis levering ventetid er et problem, er MQTT eller AMQP de bedste protokoller til brug. HTTP fungerer også til sjældent forbundne enheder.
* **Feltet gateways**. Når du bruger MQTT og HTTP, du kan ikke oprette forbindelse til flere enheder (hver med sin egen per enhed legitimationsoplysninger) med samme TLS-forbindelsen. Derfor for [felt gateway scenarier][lnk-azure-gateway-guidance], disse protokoller er ikke-optimal, fordi de kræver en TLS-forbindelse mellem felt gateway og IoT Hub for hver enhed har forbindelse til gatewayen felt.
* **Lav ressource enheder**. MQTT og HTTP biblioteker har en mindre miljøet end AMQP biblioteker. Hvis enheden har begrænset ressourcer (for eksempel, mindre end 1 MB RAM), kan disse protokoller som f.eks være kun protocol implementeringen tilgængelige.
* **Netværk gennemgangen**. Standard AMQP protokollen bruger port 5671, mens MQTT lytter på port 8883, hvilket kan medføre problemer i netværk, der er lukket til ikke-HTTP-protokoller. MQTT over WebSockets AMQP WebSockets og HTTP er kan bruges i dette scenarie.
* **Datastørrelse**. MQTT og AMQP er binære protokoller, som giver mere kompakt overførsler end HTTP.

> [AZURE.NOTE] Når du bruger HTTP, skal hver enhed afstemning til skyen-til-enhed meddelelser, hver 25 minutter eller mere. Under udvikling er det dog kan acceptere afstemning oftere end hver 25.

## <a name="port-numbers"></a>Portnumre

Enheder kan kommunikere med IoT Hub i Azure ved hjælp af forskellige protokoller. Valg af protokollen styres typisk af løsningen specifikke krav. I følgende tabel vises de udgående porte, der skal være åben for en enhed for at kunne bruge en bestemt protokol:

| Protokol | Porte |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT over WebSockets | 443    |
| AMQP     | 5671    |
| AMQP over WebSockets | 443    |
| HTTP     | 443     |
| LWM2M (Device management) | 5684 |

Når du har oprettet en IoT hub på et Azure område, bevarer den samme IP-adresse i pågældende hub levetid hubben. Men hvis du vil bevare kvalitetsniveau, hvis Microsoft flytter IoT hubben til en anden skala enhed derefter tildeles en ny IP-adresse.

## <a name="notes-on-mqtt-support"></a>Noter om understøttelse af MQTT

IoT Hub implementerer MQTT v3.1.1 protocol med følgende begrænsninger og bestemte funktionsmåde:

  * **QoS 2 understøttes ikke**. Når en enhed klient publicerer en meddelelse med **QoS 2**, lukkes IoT Hub netværksforbindelsen. Når en enhed klient abonnerer på et emne med **QoS 2**, tildeler IoT Hub maksimale QoS niveau 1 i **SUBACK** pakken.
  * **Behold meddelelser bevares ikke**. Hvis publicerer en enhed klient en meddelelse med flaget BEHOLD angivet til 1, tilføjer IoT Hub på **x-vælge-bevare** programmet egenskab til meddelelsen. I dette tilfælde IoT Hub Behold meddelelsen, bevares ikke, men i stedet overfører dem til back end-program.

Du kan finde flere oplysninger, se [IoT Hub MQTT understøtter][lnk-devguide-mqtt].

Som endelige vederlag, skal du gennemse [Azure IoT protocol gateway] [ lnk-azure-protocol-gateway] , der gør det muligt at installere en høj ydeevne brugerdefineret protokol gateway, der grænseflader direkte med IoT Hub. Gatewayen Azure IoT protocol gør det muligt at tilpasse enhed protokollen til brownfield MQTT installationer eller andre brugerdefinerede protokoller. Denne fremgangsmåde kræver dog, at du har kørt og betjene en brugerdefineret protokol gateway.

## <a name="additional-reference-material"></a>Yderligere referencemateriale

Andre referenceemner i i udvikler vejledning omfatter:

- [IoT Hub slutpunkter] [ lnk-endpoints] beskrives de forskellige slutpunkter, hver IoT hub fremviser for runtime og styring handlinger.
- [(Throttling) og kvoter for] [ lnk-quotas] beskrives de kvoter, der gælder for tjenesten IoT Hub og variere den benyttede funktionsmåden kan forvente, når du bruger tjenesten.
- [IoT Hub enheder og tjenester SDK'er] [ lnk-sdks] viser en liste over de forskellige sprog SDK'er du en bruges, når du udvikler både enhed og service-programmer, der arbejder sammen med IoT Hub.
- [Forespørge sprog efter twins, metoder og job] [ lnk-query] beskrives forespørgselssprog, du kan bruge til at hente oplysninger fra IoT Hub om din enhed twins, metoder og -job.
- [Understøttelse af IoT Hub MQTT] [ lnk-devguide-mqtt] indeholder flere oplysninger om understøttelse af IoT Hub for MQTT-protokollen.

## <a name="next-steps"></a>Næste trin

Nu du har lært at sende og modtage meddelelser via IoT-Hub, kan du måske interesseret i Developer Guide følgende emner:

- [Overføre filer fra en enhed][lnk-devguide-upload]
- [Administrer identiteter enhed i IoT Hub][lnk-devguide-identities]
- [Styre adgangen til IoT Hub][lnk-devguide-security]
- [Bruge enhed twins til at synkronisere stat og konfigurationer][lnk-devguide-device-twins]
- [Aktivere en direkte metode på en enhed][lnk-devguide-directmethods]
- [Planlægge opgaver på flere enheder][lnk-devguide-jobs]

Hvis du gerne vil prøve nogle af de begreber, der er beskrevet i denne artikel, kan du måske interesseret i følgende IoT Hub selvstudier:

- [Introduktion til Azure IoT Hub][lnk-getstarted-tutorial]
- [Hvordan du kan sende meddelelser i skyen-til-enhed med IoT Hub][lnk-c2d-tutorial]
- [Sådan behandles IoT Hub enhed i skyen meddelelser][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
