<properties
    pageTitle="Skalere Stream Analytics job at øge overførselshastighed | Microsoft Azure"
    description="Lær at skalere Stream Analytics job ved konfiguration af input partitioner, justering af definitionen af forespørgslen og indstille job streaming enheder."
    keywords="data streaming, finjustere streaming databehandling analytics"
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

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Skalere Azure Stream Analytics jobs for at øge stream databehandling overførselshastighed

Lær at finjustere analytics job og beregne *streaming enheder* for Stream analyser, hvordan du kan skalere Stream Analytics job ved konfiguration af input partitioner, justering af definitionen af analytics-forespørgslen og indstille job streaming enheder. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Hvad er delene af et Stream Analytics-job?
En definition af Stream Analytics job omfatter input, en forespørgsel og output. Input er fra hvor jobbet læser datastrømmen, forespørgslen bruges til at transformere data input strømmen og output er hvor jobbet sender resultatet af job til.  

Et job kræver mindst én inputkilde til data streaming. Datakilden stream input kan gemmes i en Azure Service Bus begivenhed Hub eller i Azure Blob-lager. Se [Introduktion til Azure Stream analyser](stream-analytics-introduction.md) og [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)kan finde flere oplysninger.

## <a name="configuring-streaming-units"></a>Konfiguration af streaming enheder
Streaming enheder (SUs) repræsenterer ressourcer og databehandling power, der kræves for at udføre en Azure Stream Analytics-job. SUs ikke en metode til at beskrive hændelsen relative behandling kapacitet, der er baseret på et blandede mål for CPU, hukommelse, og læse og skrive satser. Hver streaming enhed svarer til omkring 1MB/sekund af overførsel. 

Vælge hvor mange SUs der kræves til et bestemt job afhænger af partition konfigurationen for materialer, og den forespørgsel, der er defineret for jobbet. Du kan vælge op til din kvote i streaming enheder for en sag ved hjælp af portalen Azure klassisk. Hver Azure abonnement som standard har en kvote af op til 50 streaming enheder for alle analytics-job i et bestemt område. Hvis du vil øge streaming enheder for dine abonnementer, skal du kontakte [Microsoft Support](http://support.microsoft.com).

Antallet af streaming enheder, der kan udnytte et job, afhænger af partition konfigurationen for materialer, og den forespørgsel, der er defineret for jobbet. Bemærk også, at en gyldig værdi for stream-enhederne skal bruges. Gyldige værdier begynde på 1, 3, 6 og derefter opad i trin 6, som vist nedenfor.

![Azure Stream Analytics streame enheder skala][img.stream.analytics.streaming.units.scale]

I denne artikel viser dig, hvordan til at beregne og finjustere forespørgsel for at øge kapaciteten for analytics sager.

## <a name="embarrassingly-parallel-job"></a>Embarrassingly parallelle job
Embarrassingly parallelle jobbet er den mest SVG scenarie, har vi i Azure Stream Analytics. Den forbinder én partition af input til én forekomst af forespørgslen til en partition af output. At opnå denne parallelitet kræver et par ting:

1.  Hvis din forespørgsel logik er afhængig af den samme nøgle behandles af den samme forespørgsel-forekomst, skal derefter du kontrollere, at hændelserne, gå til den samme partition på dit input. Tale om begivenhed Hubs betyder det, at dataene begivenhed, skal have **PartitionKey** angive, eller du kan bruge partitioneret afsendere. For Blob betyder det, som hændelserne, der sendes til den samme partition mappe. Hvis din forespørgsel logik ikke kræver den samme nøgle behandles af den samme forespørgsel-forekomst, kan du ignorere dette krav. Et eksempel på dette ville være en simpel forespørgsel i project-Vælg/filter.  
2.  Når dataene er placeret på, som det skal være på en side, har vi brug at sikre, at din forespørgsel er opdelt. Dette kræver, at du kan bruge **Partition ved** i alle trin. Flere trin er tilladt, men de alle skal være opdelt ved den samme nøgle. Bemærk er, at i øjeblikket, den leverandør nøgle skal være indstillet til **PartitionId** skal have et fuldt parallelle job.  
3.  Kun begivenhed hubber og Blob understøtter partitioneret output. Du skal konfigurere feltet **PartitionKey** for at være **PartitionId**begivenhed Hubs output til. For Blob behøver du ikke at gøre noget.  
4.  En anden, hvis du vil Bemærk, at antallet input partitioner skal være lig med antallet af output partitioner. BLOB output understøtter ikke partitioner, men dette er i orden, fordi den arver partitionsskema i forespørgslen mod strømmen. Eksempler på partition værdier, der giver et fuldt parallelle job:  
    1.  8 begivenhed Hubs input partitioner og 8 begivenhed Hubs output partitioner
    2.  8 begivenhed Hubs input partitioner og Blob Output  
    3.  8 blob input partitioner og Blob Output  
    4.  8 blob input partitioner og 8 begivenhed Hubs outputformater partitioner  

Her er nogle scenarier, der er embarrassingly parallelle.

### <a name="simple-query"></a>Simpel forespørgsel
Input-begivenhed Hubs med 8 partitioner Output – begivenhed Hub med 8 partitioner

**Forespørgsel:**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Denne forespørgsel er et enkelt filter, og som sådan vi behøver ikke at bekymre dig om partitionering input vi sender til begivenhed hubber. Du vil bemærke, at forespørgslen indeholder **Partition ved** af **PartitionId**, så vi opfylde behov #2 fra ovenfor. Output skal vi konfigurere begivenhed Hubs output i jobbet have feltet **PartitionKey** , der er angivet til **PartitionId**. En sidste check, input partitioner == output partitioner. Denne topologi er embarrassingly parallel.

### <a name="query-with-grouping-key"></a>Forespørgsel med gruppering nøgle
Input-begivenhed Hubs med 8 partitioner Output – Blob

**Forespørgsel:**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Denne forespørgsel har en gruppering nøgle, og som sådan den samme nøgle skal behandles af den samme forespørgsel forekomst. Det betyder, at vi brug at sende vores begivenheder til begivenheder Hubs i en partitioneret måde. Hvilken nøgle vi betyder noget for dig? **PartitionId** er et job logik konceptet, er vi beskytter reelle nøglen **TollBoothId**. Det betyder, at vi skal angive **PartitionKey** begivenhed data giver vi sender til begivenhed hubber til et skal være **TollBoothId** af begivenheden. Forespørgslen indeholder **Partition ved** af **PartitionId**, så vi er god der. For output, da det er Blob, behøver vi ikke at bekymre dig om at konfigurere **PartitionKey**. For kravet #4 igen, er dette Blob, så vi ikke behøver at bekymre dig om den. Denne topologi er embarrassingly parallel.

### <a name="multi-step-query-with-grouping-key"></a>Flere trin forespørgsel med gruppering nøgle ###
Input-begivenhed Hub med 8 partitioner Output – begivenhed Hub med 8 partitioner

**Forespørgsel:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Denne forespørgsel har en gruppering nøgle, og som sådan den samme nøgle skal behandles af den samme forespørgsel forekomst. Vi kan bruge den samme strategi som den forrige forespørgsel. Forespørgslen har flere trin. Har hvert trin **Partition ved** af **PartitionId**? Ja, så vi er god. Vi brug at angive **PartitionKey** til **PartitionId** som beskrevet ovenfor til output, og vi kan også få vist den har det samme antal partitioner som input. Denne topologi er embarrassingly parallel.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Scenarier, der ikke er embarrassingly parallelle

### <a name="mismatched-partition-count"></a>Uoverensstemmende Partition Tæl ###
Input-begivenhed Hubs med 8 partitioner Output – begivenhed Hub med 32 partitioner

Det er ligegyldigt, hvad forespørgslen skyldes, at i dette tilfælde input partition antal! = output partition antal.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>Ikke bruger begivenhed hubber eller BLOB som output
Input-begivenhed Hubs med 8 partitioner Output – PowerBI

PowerBI output understøtter ikke aktuelt partitionering.

### <a name="multi-step-query-with-different-partition-by-values"></a>Flere trin forespørgsel med forskellige Partition ved værdier
Input-begivenhed Hub med 8 partitioner Output – begivenhed Hub med 8 partitioner

**Forespørgsel:**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Som du kan se, bruger det andet trin **TollBoothId** som tasten leverandør. Dette er ikke den samme som det første trin og derfor kræver os til at gøre en Bland. 

Dette er nogle eksempler og counterexamples over værdistrøm Analytics-job, der vil kunne opnå en embarrassingly parallelle topologi og med den risikoen for maksimale skala. Om sager, der ikke være en af disse profiler, bliver fremtidige opdateringer, der beskriver Sådan maximally skalere nogle af de andre vedtagne Stream Analytics-scenarier.

Nu foretage for brug af de generelle retningslinjer nedenfor:

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Beregne det maksimale antal streaming enheder af et job
Det samlede antal streaming enheder, der kan bruges af en Stream Analytics-job, afhænger af antallet af trinnene i den forespørgsel, der er defineret for jobbet og antallet af partitioner for hvert enkelt trin.

### <a name="steps-in-a-query"></a>Trinnene i en forespørgsel
En forespørgsel kan have et eller flere trin. Hvert trin er en underordnet forespørgsel, der er defineret ved hjælp af nøgleordet **med** . Den eneste forespørgsel, der er uden for nøgleordet **med** tælles også som et trin, for eksempel **SELECT** -sætningen i følgende forespørgsel:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Den forrige forespørgsel har to trin.

> [AZURE.NOTE] Dette eksempelforespørgsel kan forklares senere i denne artikel.

### <a name="partition-a-step"></a>Partition et trin

Partitionering et trin kræver, at følgende betingelser:

- Inputkilden skal være opdelt. Du kan finde flere oplysninger [Begivenhed Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md).
- **SELECT** -sætningen i forespørgslen skal læse fra en partitioneret inputkilde.
- Forespørgslen i trinnet skal have nøgleordet **Partition ved**

Når en forespørgsel er opdelt, bliver de input hændelser behandlede og aggregeret i separat partition grupper, og output hændelser oprettes for hver af grupperne. Hvis en kombinerede aggregat er hensigtsmæssigt, skal du oprette en anden ikke-opdelt trin til aggregat.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Beregne Maks streaming enheder for en sag

Alle ikke-opdelt trin kan sammen skalere op til seks streaming enhederne for et Stream Analytics-job. Hvis du vil tilføje yderligere streaming enheder, en trin skal være opdelt. Hver partition kan have seks streaming enheder.

<table border="1">
<tr><th>Forespørgsel med en sag</th><th>Maks streaming enheder til opgaven</th></td>

<tr><td>
<ul>
<li>Forespørgslen indeholder ét trin.</li>
<li>Trinnet ikke er opdelt.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Strømmen indtastede data er opdelt med 3.</li>
<li>Forespørgslen indeholder ét trin.</li>
<li>Trinnet er opdelt.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>Forespørgslen indeholder to trin.</li>
<li>Ingen af trinnene er opdelt.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>Stream datainput er opdelt med 3.</li>
<li>Forespørgslen indeholder to trin. Trinnet input er opdelt og er ikke det andet trin.</li>
<li>SELECT-sætningen læser fra partitioneret input.</li>
</ul>
</td>
<td>24 (18 partitioneret vejledning) + 6 for ikke-opdelt trin</td></tr>
</table>

### <a name="examples-of-scale"></a>Eksempler på skala
Følgende forespørgsel beregner antallet af biler ved at følge en kun kraftværk med tre tollbooths i en tre-minutters-vinduet. Denne forespørgsel kan skaleres op til seks streaming enheder.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Hvis du vil bruge flere streaming enheder til forespørgslen, skal både dataene streame input, og forespørgslen skal være opdelt. Da der data stream partition er indstillet til 3, kan følgende ændret forespørgsel skaleres op til 18 streaming enheder:

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Når en forespørgsel er opdelt, behandles de input begivenheder og sammenlagt i separat partition grupper. Output begivenheder, oprettes der også for hver af grupperne. Partitionering kan give nogle uventede resultater, når feltet **Group by** ikke er nøglen Partition i stream datainput. Feltet **TollBoothId** i den forrige eksempelforespørgsel er for eksempel ikke Partition nøgle af Input1. Dataene fra afgiftsbås #1 kan spredes i flere partitioner.

Hver af Input1 partitionerne behandles separat af Stream Analytics, og der oprettes flere poster af det antal bil gang på gennem for den samme afgiftsbås i det samme tumbling-vindue. Hvis tasten input partition ikke kan ændres, kan dette problem løses ved at tilføje et ekstra trin ikke partition, for eksempel:

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Denne forespørgsel kan skaleres til 24 streaming enheder.

>[AZURE.NOTE] Hvis du vil deltage i to streams, kan du sikre dig, streams er opdelt med Partitionsnøglen for den kolonne, du har joinforbindelserne, og du har det samme antal partitioner i begge streams.


## <a name="configure-stream-analytics-job-partition"></a>Konfigurere Stream Analytics job partition

**Justere en streaming enhed for en sag**

1. Log på [portalen til administration](https://manage.windowsazure.com).
2. Klik på **Stream Analytics** i venstre rude.
3. Klikke på den Stream Analytics-sag, du vil skalere.
4. Klik på **SKALA** øverst på siden.

![Azure Stream Analytics streame enheder skala][img.stream.analytics.streaming.units.scale]

I portalen Azure, kan indstillinger for skalering åbnes under indstillinger:

![Azure-portalen Stream Analytics jobkonfiguration][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Overvåg job ydeevne

Ved hjælp af administrationsportalen, kan du spore gennemløb af en sag i begivenheder/andet:

![Azure Stream Analytics overvåge job][img.stream.analytics.monitor.job]

Beregne den forventede overførsel af arbejdsbelastningen i begivenheder/andet. Hvis overførselshastigheden er mindre end forventet, finjustere den input partition, finjustere forespørgslen og tilføje yderligere streaming enheder for dit arbejde.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Stream Analytics overførselshastighed skaleres - hindbær Pi scenarie


For at forstå, hvordan stream analytics job skalere i et typisk scenarie med hensyn til behandling overførselshastighed på tværs af flere Streaming enheder, er her et forsøg, der sender føler data (klienter) til begivenhed Hub, behandler den og sender besked eller statistik som output til en anden begivenhed Hub.

Kunden sender elektronisk føler data til begivenhed hubber i JSON-format til Stream Analytics og data output er også i JSON-format.  Her er, hvordan ser eksempeldataene synes godt om-  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Forespørgsel: "sende en besked, når lys er slået fra"  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Måle overførselshastighed: Overførselshastighed i denne kontekst er mængden inputdataene behandlet af Stream Analytics i et fast tidsrum (10 minutter). For at opnå bedste behandling overførsel for inputdataene begge dataene streame input, og forespørgslen skal være opdelt. Også er **COUNT()**medtaget i forespørgslen til at måle, hvor mange input begivenheder blev behandlet. For at sikre jobbet ikke blot venter på input begivenheder på vej, blev hver partition Input begivenhed hubben forudinstalleret med tilstrækkelige inputdataene (om 300MB).  

Finder nedenfor du resultaterne med øge antallet af Streaming enheder og tilsvarende Partition tæller i begivenhed Hubs.  

<table border="1">
<tr><th>Indtast partitioner</th><th>Output partitioner</th><th>Streaming enheder</th><th>Vedvarende overførselshastighed
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/s</td>
</tr>
</table>

![IMG.stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forummet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 
