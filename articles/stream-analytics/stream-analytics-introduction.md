<properties 
    pageTitle="Introduktion til Stream Analytics | Microsoft Azure" 
    description="Få mere at vide om Stream Analytics, en administreret tjeneste, der hjælper dig med at analysere streaming data fra internettet af ting (IoT) i realtid." 
    keywords="Analytics som en tjeneste, administrerede tjenester, stream behandling, streaming analytics, hvad er stream analytics"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>Hvad er Stream Analytics?

Azure Stream Analytics er et fuldt administreret, rentable realtid begivenhed behandlingsprogram, der hjælper med at låse op deep indsigt fra data. Stream Analytics gør det nemt at konfigurere realtid analytisk beregninger på data streaming fra enheder, sensorer, websteder, sociale medier, programmer, infrastruktursystemer og meget mere.

Du kan redigere et Stream Analytics-job angive inputkilden streaming data, output sink til resultaterne af dit arbejde med nogle få klik i portalen Azure, og en datatransformation udtrykt i en SQL-lignende sprog. Du kan overvåge og Juster skala/hastigheden på tingene på portalen Azure at skalere fra et par kilobyte til en gigabyte eller flere af hændelser behandlet sekundet.

Stream Analytics bruger år af Microsoft Research igangværende udvikling af meget indstillet streaming-programmer for behandling af tid og sprog integrationer til intuitiv specifikationer af sådanne.

## <a name="what-can-i-use-stream-analytics-for"></a>Hvad kan jeg bruge Stream analyser?
Store datamængder der flyder i høj overførselshastighed over en netværksforbindelse i dag. Organisationer, der kan behandle og reagere på disse streaming data i realtid kan dramatisk forbedre effektiviteten og skille sig på markedet. Scenarier i realtid streaming analytics kan findes på tværs af alle virksomheder: tilpassede, realtid aktie-handel analyse og beskeder, der tilbydes af finansielle tjenesteydelser firmaer; realtid svindel registrering; data og -identitet beskyttelse tjenesterne; pålidelige indtagelse og analyse af data, der genereres af sensorer og aktuatorer integreret i fysisk objekter (Internet af ting eller IoT); Webanalyse clickstream; og kunde relation management (CRM) programmer udstedelse beskeder, når kundeoplevelsen inden for en tidsramme nedsat. Virksomheder, der søger efter den mest fleksible, pålidelig og økonomiske måde at gøre disse realtid begivenhed-stream dataanalyse kan udføres i meget teknikskabeloner moderne business verden.

## <a name="key-capabilities-and-benefits"></a>Vigtige funktioner og fordele
-   **Brugervenlig:** Stream Analytics understøtter en enkel, deklarative forespørgsel model til at beskrive transformationer. For at optimere til nemmere at bruge Stream Analytics bruger en T-SQL-variant og fjerner behovet for kunder til at håndtere stream behandling systemer tekniske lede. Brug af [strømmen Analytics forespørgsler sprog](https://msdn.microsoft.com/library/azure/dn834998.aspx) i forespørgselseditor i browseren, får du Spring ujævn mening Autofuldførelse så du kan hurtigt og nemt implementere tid serie forespørgsler, herunder tidsmæssig-baserede joinforbindelser, slideshowtype samlinger, tidsmæssig filtre og andre almindelige handlinger som joinforbindelser, samlinger, prognoser og filtre. Desuden muliggør i browseren forespørgsel test mod et eksempel på datafil hurtig og gentaget udvikling.  

-   **Skalerbarhed:** Stream Analytics er kan håndtere høj begivenhed overførsel af op til 1GB/sekund. Integration med [Azure begivenhed Hubs](https://azure.microsoft.com/services/event-hubs/) og [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) Tillad løsningen til indtager millioner af hændelser af hver anden kommer fra forbundne enheder, klikrækkefølger, og logfiler til at navngive nogle. For at opnå, bruger Stream Analytics leverandør mulighed for begivenhed Hubs, som kan generere 1 MB/s per partition. Brugerne kan partition beregning i en lang række logiske trin i definitionen af forespørgslen, hver med muligheden for at være yderligere opdelt for at øge skalerbarhed.  

-   **Pålidelighed, repeterbarhed og hurtig gendannelse:** En administreret tjeneste i skyen, Stream Analytics hjælper med at undgå tab af data og giver Forretningskontinuitet i tilfælde af mislykkede forsøg via indbyggede gendannelse. Tjenesten giver mulighed for at opretholde internt tilstand, gentaget resultater at sikre, at det er muligt at arkivere begivenheder og genanvende behandling af fremover, altid opnå de samme resultater. Dette gør det muligt for kunder til at gå tilbage i tiden og undersøge beregninger, når du udfører Rodårsagsanalyse, what if-analyse osv.  

-   **Lave omkostninger:** Stream Analytics som en skybaseret tjeneste, er optimeret til at give brugerne en meget lav udgift for at komme i gang og vedligeholde realtid analytics-løsninger. Tjenesten er oprettet for dig at betale undervejs baseret på Streaming enhed brugen og mængden behandlet af systemet. Brugen er udledt afhængigt af mængde hændelser behandlet og mængden Beregn power klargjort inden for klyngen til at håndtere de respektive Stream Analytics-job.  

-   **Refererer til data:** Stream Analytics giver brugere mulighed for at angive og bruge referencedata. Det kan være historiske data eller blot ikke-streaming data, der ændrer mindre hyppigt over tid. Systemet forenkler brugen af referencedata skal behandles som alle andre indgående begivenhed stream til at deltage med andre begivenhed streams optagelse i realtid til at udføre transformationer.  

-   **Brugerdefinerede funktioner:** Stream Analytics er integreret med Azure Machine Learning til at definere funktionskald i tjenesten Machine Learning som en del af en Stream Analytics-forespørgsel. Dette udvider funktionerne i Stream Analytics til at udnytte eksisterende Azure Machine Learning løsninger. For yderligere oplysninger om dette, skal du gennemse [Machine Learning integration selvstudium](stream-analytics-machine-learning-integration-tutorial.md).

-   **Connectivity:** Stream Analytics opretter forbindelse direkte til Azure begivenhed Hubs og Azure IoT Hubs for stream indtagelse og Azure Blob-tjenesten til indtager historiske data. Resultater kan skrives fra Stream Analytics til Azure-lager BLOB eller tabeller, Azure SQL DB, Azure Data sø butikker, DocumentDB, begivenhed hubber, Azure Service Bus emner eller køer og Power BI, hvor de kan derefter visualized, yderligere behandlet af arbejdsprocesser, bruges i batchen analytics via [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) eller behandles igen som en serie af begivenheder. Det er muligt at skriver flere Stream Analytics sammen med andre datakilder og behandling programmer uden at miste streaming karakteren af beregningerne, når du bruger begivenhed Hubs.  

## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin
Du har indført til Stream Analytics, en administreret tjeneste til streaming analytics på data fra internettet ting. Hvis du vil vide mere om denne tjeneste skal du se:

- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)

