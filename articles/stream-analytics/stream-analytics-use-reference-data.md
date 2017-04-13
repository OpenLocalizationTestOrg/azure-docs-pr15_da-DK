<properties
    pageTitle="Brug af reference data og opslag tabeller i Stream Analytics | Microsoft Azure"
    description="Brug referencedata i en Stream Analytics-forespørgsel"
    keywords="opslagstabel, referencedata"
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

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Bruge reference data eller opslag tabeller i en Stream Analytics input stream

Referencedata (også kaldet en opslagstabel) er en endelig stokastisk variabel, der er statisk eller nedsætte ændre art, bruges til at udføre et opslag eller koordinere med dine datastream. For at gøre brug af referencedata i Azure Stream Analytics tingene generelt anvender en [Reference Data deltage](https://msdn.microsoft.com/library/azure/dn949258.aspx) i forespørgslen. Stream Analytics bruger Azure Blob-lager som lager lag til Reference Data, og med Azure Data Factory reference data kan være transformeret og/eller kopieret til Azure Blob-lager til brug som Reference Data fra [en hvilken som helst antal skyen baseret og lokale data butikker](../data-factory/data-factory-data-movement-activities.md). Referencedata er formateret som en sekvens af BLOB (som defineret i input konfigurationen) i stigende rækkefølge af datatypen Dato/klokkeslæt, der er angivet i blob-navn. Det understøtter **kun** tilføjelse til slutningen af sekvensen ved hjælp af en dato/klokkeslæt **større** end det, der er angivet efter det sidste blob i rækkefølge.

## <a name="configuring-reference-data"></a>Konfiguration af referencedata

Hvis du vil konfigurere dataene reference, skal du først oprette en input, der er af typen **Reference Data**. Tabellen nedenfor beskrives det, hver egenskab, skal du angive under oprettelse af reference datainput med dens beskrivelse:

<table>
<tbody>
<tr>
<td>Egenskabsnavn</td>
<td>Beskrivelse</td>
</tr>
<tr>
<td>Indtast Alias</td>
<td>Et fuldt navn, der skal bruges i forespørgslen tingene til at referere til denne input.</td>
</tr>
<tr>
<td>Lagerplads konto</td>
<td>Navnet på kontoen lagerplads, hvor filerne blob er placeret. Hvis det er i samme abonnement som Stream Analytics tingene, kan du vælge den på rullelisten ned.</td>
</tr>
<tr>
<td>Lagerplads Kontonøgle</td>
<td>Hemmeligt nøglen der er knyttet til kontoen lagerplads. Dette får udfyldes automatisk, hvis kontoen lagerplads er i samme abonnement som Stream Analytics tingene.</td>
</tr>
<tr>
<td>Objektbeholder til lagring</td>
<td>Beholdere giver en logisk gruppering for blob, der er gemt i tjenesten Microsoft Azure Blob. Når du overfører en blob til Blob-tjenesten, skal du angive en objektbeholder til pågældende blob.</td>
</tr>
<tr>
<td>Sti mønster</td>
<td>Filstien bruges til at finde din BLOB inden for den angivne beholder. I sti, kan du vælge at angive en eller flere forekomster af de følgende 2 variabler:<BR>{date}, {time}<BR>Eksempel 1: products/{date}/{time}/product-list.csv<BR>Eksempel 2: products/{date}/product-list.csv
</tr>
<tr>
<td>Datoformat [Valgfrit]</td>
<td>Hvis du har brugt {date} inden for den sti mønster, du har angivet, kan du vælge datoformatet, hvor dine filer er organiseret fra rullemenuen af understøttede formater. Eksempel: Åååå/MM/DD</td>
</tr>
<tr>
<td>Klokkeslætsformat [Valgfrit]</td>
<td>Hvis du har brugt {time} i mønstret sti, du har angivet, kan du vælge det klokkeslætsformat, hvor dine filer er organiseret fra rullemenuen af understøttede formater. Eksempel: HH</td>
</tr>
<tr>
<td>Begivenhed serialiseringsformatet</td>
<td>For at sikre, at dine forespørgsler fungerer som forventet, Stream Analytics skal vide, hvilke serialiseringsformatet bruger du til indgående datastreams. Til Reference Data er de understøttede formater csv- og JSON.</td>
</tr>
<tr>
<td>Kode</td>
<td>UTF-8 er kun understøttet kodningsformatet på nuværende tidspunkt</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Generering af referencedata i en tidsplan

Hvis dataene reference er et langsomt ændre datasæt, derefter support til opdatering af data er aktiveret ved at angive en sti mønster i input konfigurationen ved hjælp af {date} reference og {tid} erstatning tokens. Stream Analytics vil opfanger de opdaterede reference datadefinitioner, der er baseret på denne sti mønster. For eksempel et mønster med `sample/{date}/{time}/products.csv` med et datoformat **"åååå-MM-DD"** og et klokkeslæt får formatet af **"TT"** Bækkens analyse til at vælge den opdaterede blob `sample/2015-04-16/17:30/products.csv` på 5:30 PM på April 16 2015 UTC tidszonen.

> [AZURE.NOTE] I øjeblikket se Stream Analytics job efter blob opdateringen kun, når computerens klokkeslæt går videre til den tid, der er kodet i blob-navn. For eksempel jobbet søger efter `sample/2015-04-16/17:30/products.csv` som muligt, men ingen tidligere end 5:30 PM på April 16 2015 UTC tidszonen. Det kan *aldrig* udseende til en fil med et kodet tidspunkt tidligere end det sidste, der er fundet.
> 
> F.eks. Når jobbet finder blob `sample/2015-04-16/17:30/products.csv` det ignorerer alle filer med en kodet dato tidligere end 5:30 PM April 16 2015 så hvis en sen ankommer `sample/2015-04-16/17:25/products.csv` blob oprettes i samme beholder jobbet kan ikke bruge den.
> 
> På samme måde hvis `sample/2015-04-16/17:30/products.csv` er kun produceret på 10:03 PM April 16 2015, men ingen blob med en tidligere dato er til stede i beholderen, jobbet vil bruge denne fil fra og med 10:03 PM April 16 2015 og bruge de forrige referencedata indtil da.
> 
> En undtagelse er når jobbet skal igen behandle data tilbage i tiden eller jobbet er første gang. Ved at starte jobbet søger efter den seneste blob produceret før jobbet starttidspunkt angivet. Dette er gjort for at sikre, at der er en **ikke-tomme** reference stokastisk variabel, når jobbet starter. Hvis en ikke blev fundet, jobbet vises følgende diagnostic: `Initializing input without a valid reference data blob for UTC time <start time>`.


[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan bruges til at dirigerer oprettelse af de opdaterede blob, der kræves, før Stream Analytics opdatere reference datadefinitioner. Data Factory er en skybaseret data integration tjeneste, som orchestrates og automatisere flytning og transformation af data. Data Factory understøtter [oprettelse af forbindelse til et stort antal skybaseret og lokale data butikker](../data-factory/data-factory-data-movement-activities.md) og flytte data nemt med jævne mellemrum, du angiver. Se denne [GitHub eksempel](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs)kan finde flere oplysninger og trinvis vejledning i at konfigurere en Data Factory pipeline til at oprette referencedata for Stream analyser, opdaterer en foruddefineret tidsplan.

## <a name="tips-on-refreshing-your-reference-data"></a>Tip til at opdatere dataene reference ##

1. Overskrive reference data BLOB medfører ikke Stream Analytics genindlæse blob og i nogle tilfælde kan det medføre jobbet mislykkes. Den anbefalede måde at ændre referencedata er at tilføje en ny blob ved hjælp af samme beholder og sti mønster defineret i jobbet input og bruge en dato/klokkeslæt **større** end den, der er angivet af den sidste blob i rækkefølge.
2.  Referencedata BLOB ikke er sorteret efter den blob "Senest ændret" klokkeslæt, men kun efter dato og klokkeslæt, der er angivet i blob Navngiv ved hjælp af {date} og {tid} erstatninger.
3.  I nogle tilfælde et job skal gå tilbage i tiden kan skal derfor reference data BLOB ikke ændret eller slettet.

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin
Du har indført til Stream Analytics, en administreret tjeneste til streaming analytics på data fra internettet ting. Hvis du vil vide mere om denne tjeneste skal du se:

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
