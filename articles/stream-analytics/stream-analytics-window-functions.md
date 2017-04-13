<properties
    pageTitle="Introduktion til Stream Analytics vinduet funktioner | Microsoft Azure"
    description="Få mere at vide om de tre vindue funktioner i Stream Analytics (tumbling, hopping, hvis du flytter)."
    keywords="tumbling vindue, hvis du flytter vinduet hopping vindue"
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


# <a name="introduction-to-stream-analytics-window-functions"></a>Introduktion til Stream Analytics vinduet funktioner

I mange realtid streaming scenarier, er det nødvendigt at udføre handlinger kun på de data, der er indeholdt i tidsmæssig windows. Indbygget understøttelse af windowing funktioner er en vigtige funktion af Azure Stream Analytics, der flytter nål på udvikler produktiviteten i redigering komplekse stream behandler job. Stream Analytics kan udviklere bruge [**Tumbling**](https://msdn.microsoft.com/library/dn835055.aspx), [**Hopping**](https://msdn.microsoft.com/library/dn835041.aspx) og [**glidende**](https://msdn.microsoft.com/library/dn835051.aspx) windows til at udføre tidsmæssig handlinger på streaming data. Det er værd at bemærke, at alle handlinger i [vinduet](https://msdn.microsoft.com/library/dn835019.aspx) output resultaterne i **slutningen** af vinduet. Output fra vinduet bliver enkelt hændelse, der er baseret på den aggregatfunktion, der bruges. Begivenheden, har tidsstempel i slutningen af vinduet, og funktionerne for vinduet defineres med en fast længde. Endelig er det vigtigt at være opmærksom på, at alle funktioner i vinduet skal bruges i en [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) -delsætning.

![Stream Analytics vindue fungerer begreber](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Tumbling vindue

Tumbling vinduet bruges funktionerne til segment skal tildeles en datastream i særskilte tid segmenter og udføre en funktion imod dem, som eksemplet nedenfor. De vigtigste differentiators af et Tumbling vindue er, at de Gentag, der ikke overlapper og en begivenhed kan ikke tilhører mere end ét tumbling vindue.

![Stream Analytics vinduet funktioner tumbling Introduktion](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Hopping vindue

Hopping vinduet funktioner hop fremad i tid med et bestemt tidsrum. Det kan være let at tænke på dem som Tumbling vinduer, som kan overlapper hinanden, så hændelser kan tilhøre mere end ét resultatsæt i Hopping-vinduet. Hvis du vil gøre en Hopping vindue den samme som en Tumbling ville vinduet ét skal blot angive hop størrelse for at være den samme som vinduesstørrelse. 

![Stream Analytics vindue fungerer hopping Introduktion](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Hvis du flytter vinduet

Skyderen vinduet funktioner, i modsætning til Tumbling eller Hopping windows, giver en output **kun** , når en hændelse indtræffer. Hvert vindue, har mindst én begivenhed, og vinduet flytter løbende frem ved en € (epsilon). Sådan Hopping Windows tilhører begivenheder mere end ét skubbe vindue.

![Stream Analytics vindue fungerer skyderen Introduktion](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Få hjælp til vinduet funktioner

For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
