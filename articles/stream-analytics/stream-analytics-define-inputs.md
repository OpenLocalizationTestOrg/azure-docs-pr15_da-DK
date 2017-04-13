<properties
    pageTitle="Dataforbindelse: Data streame input fra en hændelse stream | Microsoft Azure"
    description="Få mere at vide om konfiguration af en dataforbindelse til Stream Analytics kaldet 'input'. Input omfatter en datastream fra begivenheder og også refererer til data."
    keywords="datastream, dataforbindelse, begivenhed værdistrøm"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Dataforbindelse: Få mere at vide om data stream input fra begivenheder til Stream Analytics

Dataforbindelsen til Stream Analytics er en datastrøm af begivenheder fra en datakilde. Dette kaldes en "input". Stream Analytics har førsteklasses integration med Azure stream kilder begivenhed-Hub, IoT Hub og Blob datalager, der kan være fra det samme eller forskellige Azure abonnement som analytics tingene.

## <a name="data-input-types-data-stream-and-reference-data"></a>Data indtastet typer: Data stream og reference data
Som data overføres til en datakilde, er det consumed ved kørslen Stream analyser og behandles i realtid. Input er opdelt i to forskellige typer: data streame input og referere til data input.

### <a name="data-stream-inputs"></a>Data streame input
En datastream er ubundet rækkefølge af hændelser, der kommer over tid. Stream Analytics job skal indeholde mindst én stream datainput skal consumed og transformeret ved jobbet. BLOB-lager, begivenhed Hubs og IoT Hubs understøttes som data stream inputkilder. Begivenhed Hubs bruges til at indsamle begivenhed streams fra flere enheder og -tjenester, som sociale medier aktivitet feeds, aktiekurser handelsoplysninger eller data fra sensorer. IoT Hubs er optimeret til at indsamle data fra forbundne enheder i Internet af ting (IoT) scenarier.  BLOB-lager kan bruges som en inputkilde til ingesting flere data som en stream.  

### <a name="reference-data"></a>Referencedata
Stream Analytics understøtter en anden type input kendt som referencedata. Dette er ekstra data, som er enten statiske eller langsomt ændre over tid og bruges typisk for at udføre korrelation og look-ups. Azure Blob-lager er i øjeblikket kun understøttede inputkilden for referencedata. Reference datakilde BLOB er begrænset til 100MB i størrelse.
Hvis du vil lære at oprette reference data input, skal du se [Brug Reference Data](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Oprette en stream datainput med en begivenhed Hub

[Azure begivenhed hubber](https://azure.microsoft.com/services/event-hubs/) er meget SVG begivenhed ingestor udgivelse-abonnement. Det kan indsamle millioner af begivenheder sekundet, så du kan behandle og analysere store mængder data, der er oprettet med din forbundne enheder og programmer. Det er en af de mest almindeligt anvendte input for Stream analyser. Begivenhed hubber og Stream Analytics giver sammen kunderne en til en komplet løsning for realtid analyser. Begivenhed Hubs vores kunder kan feed begivenheder i Azure i realtid, og Stream Analytics job kan behandle dem i realtid. Kunder kan for eksempel sende web Klik, føler målinger, online log begivenheder til begivenhed hubber, og oprette Stream Analytics jobs for at bruge begivenhed Hubs som inputdataene streams i realtid filtrering, sammenlægning og korrelations.

Det er vigtigt at være opmærksom på, standard tidsstemplet af hændelser, der kommer fra begivenhed Hubs i Stream Analytics er tidsstemplet, begivenheden, der er modtaget i begivenhed-Hub, som er EventEnqueuedUtcTime. For at behandle dataene som en stream ved hjælp af et tidsstempel i begivenhed overførslen, skal nøgleordet [TIDSSTEMPEL ved](https://msdn.microsoft.com/library/azure/dn834998.aspx) bruges.

### <a name="consumer-groups"></a>Forbrugergrupper

Hver Stream Analytics begivenhed Hub input skal være konfigureret til at have sin egen consumer gruppe. Når et job indeholder en selv-joinforbindelse eller flere input, kan nogle input læses af mere end én læser strømmen, som påvirker antallet af læsere i en enkelt consumer gruppe. Hvis du vil undgå at overskride begivenhed Hub grænsen på 5 læsere per consumer gruppe per partition, er det en god fremgangsmåde at angive en forbruger gruppe for hvert Stream Analytics-job. Bemærk, at der er også en begrænsning på 20 consumer grupper hver begivenhed Hub. Yderligere oplysninger finder du [Begivenhed Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Konfigurere begivenhed Hub som en inputdataene stream

Tabellen nedenfor beskrives hver egenskab, i tilfælde af, at Hub input fane med dens beskrivelse:

| EGENSKABSNAVN | BESKRIVELSE |
|------|------|
| Indtast Alias | Et fuldt navn, der skal bruges i forespørgslen tingene til at referere til denne input |
| Tjenesten Bus Namespace | Et Service Bus navneområde er en beholder for et sæt messaging enheder. Når du har oprettet en ny begivenhed-Hub, kan du også oprettet et Service Bus navneområde. |
| Begivenhed Hub | Navnet på din begivenhed-Hub, der er indtastet. |
| Navn på begivenhed Hub politik | Politikken delt adgang, som kan oprettes under fanen begivenhed Hub konfigurere. Hver delt access-politik skal have et navn, tilladelser, du har angivet, og hurtigtasterne. |
| Nøgle til begivenhed Hub politik | Delt hurtigtast bruges til at godkende adgang til navneområdet Service Bus. |
| Begivenhed Hub Consumer gruppe (valgfrit) | Gruppen Consumer til indtager data fra den begivenhed Hub. Hvis ikke er angivet, bruge Stream Analytics job gruppen standard Consumer til indtager data fra den begivenhed Hub. Det anbefales at bruge en entydige forbruger gruppe for hvert Stream Analytics-job. |
| Begivenhed serialiseringsformatet | Du bruger for at sikre, at dine forespørgsler fungerer som forventet, Stream Analytics skal vide, hvilke serialiseringsformatet (JSON, csv- eller Avro) til indgående datastreams. |
| Kode | UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt. |

Når dine data kommer fra en hændelse Hub kilde, kan du få adgang til par metadatafelter i forespørgslen Stream analyser. I nedenstående tabel vises felterne og deres beskrivelse.

| EGENSKABEN | BESKRIVELSE |
|------|------|
| EventProcessedUtcTime | Dato og klokkeslæt, hændelsen blev behandlet af Stream analyser. |
| EventEnqueuedUtcTime | Dato og klokkeslæt, hændelsen blev modtaget af begivenhed Hubs. |
| PartitionId | Baseret på nul partition-ID'ET for det input kort. |

For eksempel kan du skrive en forespørgsel som følger:

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Oprette en IoT Hub datainput værdistrøm

Azure Iot Hub er en meget SVG udgivelse abonnement begivenhed ingestor optimeret til IoT scenarier.
Det er vigtigt at være opmærksom på, standard tidsstemplet af hændelser, der kommer fra IoT Hubs i Stream Analytics er tidsstemplet, begivenheden, der er modtaget i IoT-Hub, som er EventEnqueuedUtcTime. For at behandle dataene som en stream ved hjælp af et tidsstempel i begivenhed overførslen, skal nøgleordet [TIDSSTEMPEL ved](https://msdn.microsoft.com/library/azure/dn834998.aspx) bruges.

> [AZURE.NOTE] Kun meddelelser, som sendes til en egenskab for DeviceClient kan behandles.

### <a name="consumer-groups"></a>Forbrugergrupper

Hver Stream Analytics IoT Hub input skal være konfigureret til at have sin egen consumer gruppe. Når et job indeholder en selv-joinforbindelse eller flere input, kan nogle input læses af mere end én læser strømmen, som påvirker antallet af læsere i en enkelt consumer gruppe. Hvis du vil undgå at overskride IoT Hub grænsen på 5 læsere per consumer gruppe per partition, er det en god fremgangsmåde at angive en forbruger gruppe for hvert Stream Analytics-job.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Konfigurere IoT Hub som en datastream input

Tabellen nedenfor beskrives, hver egenskab under fanen IoT Hub input med dens beskrivelse:

| EGENSKABSNAVN | BESKRIVELSE |
|------|------|
| Indtast Alias | Et fuldt navn, der skal bruges i forespørgslen tingene til at referere til denne input. |
| IoT-Hub | En IoT Hub er en beholder for et sæt af SMS enheder. |
| Første eller sidste ark | Navnet på din IoT Hub slutpunkt. |
| Delt adgang politikkens navn | Politikken delt adgang til at give adgang til IoT hubben. Hver delt access-politik skal have et navn, tilladelser, du har angivet, og hurtigtasterne. |
| Delte hurtigtast for politik | Delt hurtigtast bruges til at godkende adgang til IoT hubben. |
| Forbruger gruppe (valgfrit) | Gruppen Consumer til indtager data fra IoT-hubben. Hvis ikke er angivet, bruge Stream Analytics job gruppen standard Consumer til indtager data fra IoT-hubben. Det anbefales at bruge en entydige forbruger gruppe for hvert Stream Analytics-job. |
| Begivenhed serialiseringsformatet | Du bruger for at sikre, at dine forespørgsler fungerer som forventet, Stream Analytics skal vide, hvilke serialiseringsformatet (JSON, csv- eller Avro) til indgående datastreams. |
| Kode | UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt. |

Når dine data kommer fra en IoT Hub kilde, kan du få adgang til par metadatafelter i forespørgslen Stream analyser. I nedenstående tabel vises felterne og deres beskrivelse.

| EGENSKABEN | BESKRIVELSE |
|------|------|
| EventProcessedUtcTime | Dato og klokkeslæt, hændelsen blev behandlet. |
| EventEnqueuedUtcTime | Dato og klokkeslæt, hændelsen blev modtaget af IoT hubben. |
| PartitionId | Baseret på nul partition-ID'ET for det input kort. |
| IoTHub.MessageId | Bruges til at koordinere tovejskommunikation i IoT Hub. |
| IoTHub.CorrelationId | Bruges i meddelelsen svar og feedback i IoT Hub. |
| IoTHub.ConnectionDeviceId | Godkendte id bruges til at sende meddelelsen, tidsstemplet på servicebound meddelelser med IoT Hub. |
| IoTHub.ConnectionDeviceGenerationId | GenerationId af den godkendte enhed, der bruges til at sende meddelelsen, tidsstemplet på servicebound meddelelser med IoT Hub. |
| IoTHub.EnqueuedTime | Tidspunkt, hvor meddelelsen blev modtaget af IoT Hub. |
| IoTHub.StreamId | Brugerdefineret hændelsesegenskab tilføjet af afsender enheden. |

## <a name="create-a-blob-storage-data-stream-input"></a>Oprette en Blob storage datainput værdistrøm

Blob-lager tilbyder en økonomisk og SVG løsning for scenarier med store mængder ustrukturerede data til at gemme i skyen. Data i [Blob-lager](https://azure.microsoft.com/services/storage/blobs/) betragtes generelt data "resten", men den kan behandles som en datastream af Stream analyser. Et almindelige scenarie for Blob storage input med Stream Analytics er behandling af logfiler, hvor telemetri er hentet fra et system og skal fortolkes og behandles for at udtrække data, der giver mening.

Det er vigtigt at være opmærksom på, standard tidsstemplet af Blob storage hændelser i Stream Analytics er tidsstemplet, blob sidst blev ændret hvilke *isBlobLastModifiedUtcTime*. For at behandle dataene som en stream ved hjælp af et tidsstempel i begivenhed overførslen, skal nøgleordet [TIDSSTEMPEL ved](https://msdn.microsoft.com/library/azure/dn834998.aspx) bruges.

Bemærk også, at CSV formateret input **kræver** en overskriftsrække til at definere felter for datasæt. Yderligere felter i brevhoveder i række skal alle være **entydig**.

> [AZURE.NOTE] Stream Analytics understøtter ikke at føje indhold til en eksisterende blob. Stream Analytics kan kun få vist en blob én gang og de ændringer, der er udført, når denne læst ikke bliver behandlet. Den bedste fremgangsmåde er at overføre alle dataene én gang og ikke tilføje eventuelle yderligere hændelser til blob-lager.

Tabellen nedenfor beskrives, hver egenskab under fanen Blob storage input med dens beskrivelse:

<table>
<tbody>
<tr>
<td>EGENSKABSNAVN</td>
<td>BESKRIVELSE</td>
</tr>
<tr>
<td>Indtast Alias</td>
<td>Et fuldt navn, der skal bruges i forespørgslen tingene til at referere til denne input.</td>
</tr>
<tr>
<td>Lagerplads konto</td>
<td>Navnet på kontoen lagerplads, hvor filerne blob er placeret.</td>
</tr>
<tr>
<td>Lagerplads Kontonøgle</td>
<td>Den hemmelige nøgle, der er knyttet til kontoen lagerplads.</td>
</tr>
<tr>
<td>Objektbeholder til lagring
</td>
<td>Beholdere giver en logisk gruppering for blob, der er gemt i tjenesten Microsoft Azure Blob. Når du overfører en blob til Blob-tjenesten, skal du angive en objektbeholder til pågældende blob.</td>
</tr>
<tr>
<td>Sti præfiks mønster [Valgfrit]</td>
<td>Filstien bruges til at finde din BLOB inden for den angivne beholder.
I sti, kan du vælge at angive en eller flere forekomster af de følgende 3 variabler:<BR>{date}, {tid}<BR>{partition}<BR>Eksempel 1: cluster1/logfiler / {dato} / {tid} / {partition}<BR>Eksempel 2: cluster1/logfiler / {dato}<P>Bemærk, at "*" er ikke en tilladt værdi for pathprefix. Kun gyldige <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure blob tegn</a> er tilladt.</td>
</tr>
<tr>
<td>Datoformat [Valgfrit]</td>
<td>Hvis dato tokenet bruges i præfiks stien, kan du vælge datoformatet, hvor dine filer er organiseret. Eksempel: Åååå/MM/DD</td>
</tr>
<tr>
<td>Klokkeslætsformat [Valgfrit]</td>
<td>Hvis tokenet tid der bruges i præfiks stien, kan du angive det klokkeslætsformat, hvor dine filer er organiseret. Den eneste understøttede værdi er i øjeblikket HH.</td>
</tr>
<tr>
<td>Begivenhed serialiseringsformatet</td>
<td>Du bruger for at sikre, at dine forespørgsler fungerer som forventet, Stream Analytics skal vide, hvilke serialiseringsformatet (JSON, csv- eller Avro) til indgående datastreams.</td>
</tr>
<tr>
<td>Kode</td>
<td>For csv- og JSON er UTF-8 kun understøttede kodningsformatet på nuværende tidspunkt.</td>
</tr>
<tr>
<td>Afgrænser</td>
<td>Stream Analytics understøtter et antal almindelige afgrænsere serialisering data i en CSV-fil. Understøttede værdier er komma, semikolon, mellemrum, tabulatorstop og lodret streg.</td>
</tr>
</tbody>
</table>

Når dine data kommer fra en kilde, Blob-lager, kan du få adgang til et par metadatafelter i forespørgslen Stream Analytics. I nedenstående tabel vises felterne og deres beskrivelse.

| EGENSKABEN | BESKRIVELSE |
|------|------|
| BlobName | Navnet på den input blob, der hændelsen stammer fra. |
| EventProcessedUtcTime | Dato og klokkeslæt, hændelsen blev behandlet af Stream analyser. |
| BlobLastModifiedUtcTime | Dato og klokkeslæt, blob sidst blev ændret. |
| PartitionId | Baseret på nul partition-ID'ET for det input kort. |

For eksempel kan du skrive en forespørgsel som følger:

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin
Du har lært dataforbindelsesindstillinger til forbindelse i Azure for din Stream Analytics-job. Hvis du vil vide mere om Stream Analytics, i:

- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
