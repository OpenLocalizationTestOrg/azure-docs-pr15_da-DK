<properties
    pageTitle="Skalere Stream Analytics tingene med Azure Machine Learning funktioner | Microsoft Azure"
    description="Lær, hvordan du korrekt skalere Stream Analytics jobs (partitionering, sø antal og mere) når du bruger Azure Machine Learning funktioner."
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>

# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Skalere Stream Analytics tingene med Azure Machine Learning funktioner

Det er ofte meget lette at konfigurere et Stream Analytics-job og køre nogle eksempeldata via den. Hvad skal vi gøre, når vi har brug at køre samme jobbet med højere data lydstyrke? Det kræver, at vi at forstå, hvordan du konfigurerer Stream Analytics jobbet, så det skaleres. I dette dokument, vil vi fokusere på de særlige aspekter af skalering Stream Analytics sager med Machine Learning funktioner. Du kan finde oplysninger om, hvordan skalere Stream Analytics job Generelt i artiklen [Skalering job](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Hvad er en Azure Machine Learning funktion i Stream Analytics?

En maskine Learning funktion i Stream Analytics kan bruges som et normalt funktionskald i forespørgselssprog Stream analyser. Bag scenen er funktionskald dog faktisk Azure Machine Learning Web serviceanmodninger. Machine Learning web services til at understøtte "samling" flere rækker, der kaldes Mini batchen i den samme web service API opkald, for at forbedre overordnede overførselshastighed. Se følgende artikler for at få mere at vide, [Azure Machine Learning-funktioner i Stream analyser](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) og [Azure Machine Learning Web Services](machine-learning/machine-learning-consume-web-services.md#request-response-service-rrs).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurere et Stream Analytics-job med Machine Learning funktioner

Når du konfigurerer en Machine Learning funktion for Stream Analytics sag, er der to parametre skal du tænke på, batchstørrelsen på Machine Learning funktionskald og Streaming enhederne (SUs) klargjort til Stream Analytics jobbet. For at bestemme de korrekte værdier for disse skal først foretages en beslutning mellem ventetid og overførselshastighed, det vil sige, forsinkelse af Stream Analytics job og gennemløb af hver sø. SUs kan altid føjes til et job for at øge kapaciteten af et godt partitioneret Stream Analytics-forespørgsel, selvom flere SUs forøger omkostningerne til kørsel af jobbet.

Det er derfor vigtigt at finde ud af *tolerance* ventetid i køre et Stream Analytics-job. Yderligere ventetid i at køre Azure Machine Learning serviceanmodninger, naturligt øger med batchstørrelse, hvilket vil sammensat forsinkelse på Stream Analytics jobbet. På den anden side batchen større tillader Stream Analytics jobbet til at behandle *flere begivenheder med den *samme tal * af Machine Learning web serviceanmodninger. Forøgelse af Machine Learning web service ventetid er ofte underordnede lineær forøgelse af batchstørrelse, så det er vigtigt at overveje batchstørrelsen på mest omkostninger effektive til en Machine Learning webtjeneste i en given situation. Batchen standardstørrelsen for serviceanmodninger web er 1000 og kan redigeres ved hjælp af [Stream Analytics REST-API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST-API") eller [PowerShell-klienten til Stream Analytics]-(stream-analytics-monitor-and-manage-jobs-use-powershell.md "PowerShell-klienten til Stream Analytics").

Når en batchstørrelse er blevet bestemt, mængden Streaming enheder (SUs) kan bestemmes, baseret på antallet af hændelser, der skal funktionen procesfarver sekundet. Se artiklen [Stream Analytics skala job](stream-analytics-scale-jobs.md#configuring-streaming-units)for yderligere oplysninger om Streaming enheder.

Generelt, er 20 samtidige forbindelser til webtjenesten Machine Learning til hver 6 SUs, bortset fra at 1 sø job og 3 sø job får 20 samtidige forbindelser også.  Eksempelvis hvis inputdataene rente er 200.000 begivenheder sekundet og batchstørrelse er tilbage til standard for 1000 er den resulterende web service forsinkelse med 1000 begivenheder Mini batchen 200 millisekunder. Det betyder, at hver forbindelse kan foretage 5 anmodninger til webtjenesten Machine Learning i en anden. Med 20 forbindelser, kan kørslen Stream Analytics behandle 20.000 begivenheder i 200 millisekunder og derfor 100.000 begivenheder i en anden. Så for at behandle 200.000 begivenheder sekundet skal Stream Analytics jobbet 40 samtidige forbindelser, der vises til 12 SUs. Diagrammet nedenfor illustrerer anmodninger fra Stream Analytics jobbet til Machine Learning web service slutpunkt – hver 6 SUs har 20 samtidige forbindelser til Machine Learning webtjeneste på maks.

![Skala Stream Analytics med Machine Learning funktioner 2 job eksempel] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skala Stream Analytics med Machine Learning funktioner 2 job eksempel")

Generelt ***B*** til batchstørrelse, ***L*** til web service ventetid batchstørrelse B i millisekunder, overførselshastigheden for en Stream Analytics-job med ***N*** SUs er:

![Skalere Stream Analytics med Machine Learning funktioner formel] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skalere Stream Analytics med Machine Learning funktioner formel")

En ekstra overvejelser kan være på 'Maks samtidige opkald' Machine Learning web service side, det anbefales for at angive det til den største værdi (aktuelt 200).

Yderligere oplysninger om denne indstilling skal du gennemse [Skalering artikel til Machine Learning webtjenester](../machine-learning/machine-learning-scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Eksempel – synspunkt analyse

I følgende eksempel indeholder en Stream Analytics sag med synspunkt analysen Machine Learning funktion, som beskrevet i [Stream Analytics Machine Learning integration selvstudium](stream-analytics-machine-learning-integration-tutorial.md).

Forespørgslen er en simpel fuldt partitioneret forespørgsel efterfulgt af funktionen **synspunkt** , som vist nedenfor:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Det følgende scenario med en overførsel af 10.000 tweets sekundet skal der oprettes et Stream Analytics-job for at udføre synspunkt analyse af tweets (hændelser). Brug af 1 sø, kan dette Stream Analytics-job kunne håndtere trafikken? Med batchen standardstørrelsen 1000 skal jobbet kunne holde med input. Yderligere skal funktionen tilføjede Machine Learning generere ikke mere end et sekund af ventetid, som er generelle standard forsinkelse på synspunkt analysen Machine Learning-webtjeneste (med en standard batchstørrelse på 1000). Kørslen Stream Analytics **overordnede** eller til slut ventetid vil typisk være et par sekunder. Se nærmere mere detaljeret i dette Stream Analytics-job, *især* Machine Learning-funktionskald. Har batchstørrelsen som 1000, tager en overførsel af 10.000 hændelser om 10 anmodninger til webtjeneste. Selv med 1 sø er der tilstrækkelig samtidige forbindelser til denne input trafik.

Men hvad nu, hvis input begivenhed rente øger med 100 x og nu Stream Analytics jobbet skal behandle 1.000.000 tweets sekundet? Der er to muligheder:

1.  Øge batchstørrelsen eller
2.  Partition input strømmen til at behandle hændelser parallelt

Med den første indstilling job **Ventetid** , øger.

Med den anden mulighed skal flere SUs klargøres og genererer derfor flere samtidige Machine Learning web serviceanmodninger. Det betyder job **omkostninger** øges.


Antag, at forsinkelse på synspunkt analysen Machine Learning webtjeneste er 200 millisekunder for 1000 begivenhed batches eller under, 250ms til 5.000 begivenhed batches, 300ms til 10.000 begivenhed batches eller 500ms for 25.000 begivenhed batches.

1. Ved hjælp af den første indstilling, (**ikke** klargøring flere SUs), kan batchstørrelsen øges til **25.000**. Dette giver også tilladelse jobbet til at behandle 1.000.000 hændelser med 20 samtidige forbindelser til webtjenesten Machine Learning (med en forsinkelse på 500ms per opkald). Yderligere forsinkelse på Stream Analytics jobbet på grund af funktionen synspunkt anmodninger på Machine Learning web serviceanmodninger ville så øges fra **200 millisekunder** til **500ms**. Bemærk, at batch størrelse **kan** dog øget uendelig som Machine Learning web services kræver datastørrelse på en anmodning om være 4 MB eller mindre webtjeneste anmoder om timeout efter 100 sekunder af handlingen.
2. Ved hjælp af den anden mulighed, batchstørrelse er tilbage på 1000, med 200 millisekunder web service ventetid, hver 20 samtidige forbindelser til webtjenesten ville have til at behandle 1000 *20* 5 hændelser = 100.000 sekundet. Så for at behandle 1.000.000 hændelser sekundet, skal jobbet 60 SUs. Sammenlignet med den første indstilling, ville Stream Analytics job få yderligere web batchen serviceanmodninger, tur. genererer en øgede omkostninger.

Nedenfor vises en tabel til overførsel af Stream Analytics jobbet til forskellige SUs og batchen størrelser (i antallet af hændelser sekundet).

| batchstørrelse (ventetid på ML)  | 500 (200 millisekunder) | 1.000 (200 millisekunder) | 5.000 (250ms) | 10.000 (300ms) | 25.000 (500ms) |
|--------|-------------------------|---------------|---------------|----------------|----------------|
| **1 SØ** | 2.500 | 5.000 | 20.000 | 30.000 | 50000 |
| **3 SUs** | 2.500 | 5.000 | 20.000 | 30.000 | 50000 |
| **6 SUs** | 2.500 | 5.000 | 20.000 | 30.000 | 50000 |
| **12 SUs** | 5.000 | 10.000 | 40.000 | 60.000 | 100.000 |
| **18 SUs** | 7.500 | 15.000 | 60.000 | 90,000 | 150.000 |
| **24 SUs** | 10.000 | 20.000 | 80.000 | 120,000 | 200.000 |
| **…** | … | … | … | … | … |
| **60 SUs** | 25.000 | 50000 | 200.000 | 300.000 | 500.000 |

På nuværende tidspunkt, skal du allerede har en god forståelse af, hvordan Machine Learning funktioner i Stream Analytics fungerer. Du sandsynligvis forstå også, Stream Analytics job "hente" data fra datakilder og hver "hente" returnerer en mængde hændelser for Stream Analytics jobbet til at behandle. Hvordan web denne hente model virkning maskine læring serviceanmodninger?

Normalt batchstørrelsen vi Machine Learning funktioner nøjagtigt ikke kan divideres med antallet af hændelser, der returneres af hvert Stream Analytics-job "hente". Når dette sker Machine Learning-webtjeneste bliver kaldt med "delvis" batches. Dette er gjort for at ikke betale yderligere job ventetid omkostninger i samler begivenheder fra hente til at hente.

## <a name="new-function-related-monitoring-metrics"></a>Ny funktion beslægtede overvågning målepunkter

Tre flere funktionen-relaterede statistikker er tilføjet i området skærm i et Stream Analytics-job. Det er funktionen anmodninger, funktionen BEGIVENHEDER og mislykkedes funktionen anmodninger, som vist i nedenstående grafik.

![Skalere Stream Analytics med Machine Learning funktioner målepunkter] (./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skalere Stream Analytics med Machine Learning funktioner målepunkter")

Er defineret som følger:

**Funktionen anmodninger**: antallet af anmodninger om funktionen.

**FUNKTIONSHÆNDELSER**: tal begivenheder i funktionen anmodninger.

**Mislykkedes funktionen anmodninger**: antallet af mislykkede funktionen anmodninger.

## <a name="key-takeaways"></a>Vigtige Takeaways  

Hvis du vil opsummere hovedpunkterne, hvis du vil skalere et Stream Analytics-job med Machine Learning funktioner, betragtes følgende elementer:

1.  Indtast begivenhed rente
2.  Tolererede ventetid for igangværende Stream Analytics jobbet (og dermed batchstørrelsen på Machine Learning web serviceanmodninger)
3.  Klargjort Stream Analytics SUs og antallet af Machine Learning web serviceanmodninger (de ekstra funktionen omkostninger)

En fuldt partitioneret Stream Analytics-forespørgsel blev brugt som et eksempel. Hvis der er brug for en mere kompleks forespørgsel [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) er en god ressource til at få mere hjælp fra Stream Analytics-teamet.

## <a name="next-steps"></a>Næste trin

Hvis du vil vide mere om Stream Analytics, i:

- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
