<properties
    pageTitle="Forespørgsel eksempler på almindelige brugsmønstre i Stream Analytics | Microsoft Azure"
    description="Almindelige Azure Stream Analytics forespørgsel mønstre "
    keywords="forespørgsel eksempler"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Forespørgsel eksempler på almindelige Stream Analytics brugsmønstre

## <a name="introduction"></a>Introduktion

Forespørgsler i Azure Stream Analytics udtrykkes i et forespørgselssprog, synes godt om SQL-, som er beskrevet i oversigtsvejledning [Stream Analytics forespørgsel sprog](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  I denne artikel beskrives løsninger på flere almindelige forespørgsel mønstre baseret på virkelighedens scenarier.  Det er en igangværende arbejde og fortsætter med at blive opdateret med nye mønstre jævnligt.

## <a name="query-example-data-type-conversions"></a>Eksempel med en opdateringsforespørgsel: Data for datatypekonvertering
**Beskrivelse**: definere typerne egenskaberne på input-stream.
For eksempel bil vægt kommer på input strømmen som strenge og skal konverteres til INT til at udføre OPSUMMERE data.

**Input**:

| Gøre | Tid | Vægt |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Output**:

| Gøre | Vægt |
| --- | --- |
| Honda | 3000 |

**Løsning**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Forklaring**: bruge en CAST-sætning i feltet vægt til at angive dens (se en liste over understøttede datatyper [her](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Eksempel med en opdateringsforespørgsel: ved hjælp af synes godt om/ikke kan lide at mønster tilsvarende
**Beskrivelse**: Kontrollér, at feltværdien på begivenheden svarer til et bestemt mønster eksempelvis returnerede nummerplader, der starter med A og slutter med 9

**Input**:

| Gøre | LicensePlate | Tid |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Du kan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**Output**:

| Gøre | LicensePlate | Tid |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Du kan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**Løsning**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Forklaring**: bruge sætningen LIKE til at kontrollere, at værdien i feltet LicensePlate starter med A og derefter har en streng med nul eller flere tegn og den slutter med 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Eksempel med en opdateringsforespørgsel: angive logik til forskellige tilfælde/værdier (tilfælde sætninger)
**Beskrivelse**: giver forskellige beregning for et felt, der er baseret på nogle kriterier.
For eksempel indeholder en streng beskrivelse for hvor mange biler overføres af det samme mærke med en speciel sag for 1.

**Input**:

| Gøre | Tid |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output**:

| CarsPassed | Tid |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Løsning**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Forklaring**: feltet sag delsætning tillader os til at levere en anden beregning, der er baseret på nogle kriterier (i dette tilfælde antallet af biler i vinduet samling).

## <a name="query-example-send-data-to-multiple-outputs"></a>Eksempel med en opdateringsforespørgsel: sende data til flere output
**Beskrivelse**: sende data til flere output destinationer fra et enkelt job.
For eksempel at analysere data for en besked i grænseværdi-baserede og arkivere alle begivenheder til blob-lager

**Input**:

| Gøre | Tid |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Gøre | Tid |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Gøre | Tid | Tæl |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Løsning**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Forklaring**: feltet INTO-delsætningen fortæller Stream Analytics som output til at skrive dataene fra denne sætning.
Den første forespørgsel er en pass-through de data, vi har modtaget til output, vi med navnet ArchiveOutput.
Den anden forespørgsel indeholder nogle enkle sammenlægning og filtrering og sender resultaterne til et efterfølgende advarsler system.
*Bemærk*: Du kan også genbruge resultaterne af CTEs (det vil sige med sætninger) i flere output sætninger – dette er en ekstra fordel åbne færre læsere til inputkilden.
For eksempel 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Eksempel med en opdateringsforespørgsel: tælle entydige værdier
**Beskrivelse**: tælle antallet af entydige feltværdier, der vises i strømmen inden for en tidsramme.
For eksempel, hvor mange entydige foretage af biler passerede gennem kun standen i et andet vindue i 2?

**Input**:

| Gøre | Tid |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output:**

| Tæl | Tid |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Løsning:**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Forklaring:** Vi har en indledende sammenlægning for at få entydige gør med deres count over vinduet.
Vi gør derefter en sammenlægning af hvor mange gør vi har – givet alle entydige værdier i et vindue være samme tidsstemplet derefter vinduet anden sammenlægning skal være minimale skal aggregeres ikke 2 windows fra det første trin.

## <a name="query-example-determine-if-a-value-has-changed"></a>Eksempel med en opdateringsforespørgsel: finde ud af, om en værdi er blevet ændret#
**Beskrivelse**: se på et tidligere værdi til at bestemme, hvis det er anderledes end den aktuelle værdi er For eksempel den forrige bil på farten kun den samme foretage som den aktuelle bil?

**Input**:

| Gøre | Tid |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Output**:

| Gøre | Tid |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Løsning**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Forklaring**: Brug mellemliggende til Kig i input streame en begivenhed tilbage og få værdien foretage. Derefter sammenligner med afkrydsningsfeltet Foretag på den aktuelle hændelse, og Medtag begivenheden, hvis de er forskellige.

## <a name="query-example-find-first-event-in-a-window"></a>Eksempel med en opdateringsforespørgsel: Find første begivenhed i et vindue
**Beskrivelse**: Find første bil i hver 10 minutters interval?

**Input**:

| LicensePlate | Gøre | Tid |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Output**:

| LicensePlate | Gøre | Tid |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Løsning**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

Nu Lad os ændre problemet og finde første bil af bestemt i hver 10 minutters interval.

| LicensePlate | Gøre | Tid |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Løsning**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Eksempel med en opdateringsforespørgsel: Find sidste begivenhed i et vindue
**Beskrivelse**: Find sidste bil i hver 10 minutters interval.

**Input**:

| LicensePlate | Gøre | Tid |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Output**:

| LicensePlate | Gøre | Tid |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Løsning**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Forklaring**: der er to trin i forespørgslen – den første del finder seneste tidsstempel i windows 10 minutter. Det andet trin sammenkæder resultatet af den første forespørgsel med oprindelige stream til at finde hændelser, der passer sidste tidsstempler i hvert vindue. 

## <a name="query-example-detect-the-absence-of-events"></a>Eksempel med en opdateringsforespørgsel: registrere fravær af hændelser
**Beskrivelse**: Kontrollér, at en stream har ingen værdi, der svarer til et bestemt kriterium.
Eksempelvis har 2 fortløbende biler fra det samme mærke angivet farten kun inden for 90 sekunder?

**Input**:

| Gøre | LicensePlate | Tid |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEFINITION 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**Output**:

| Gøre | Tid | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC 123 | 2015-01-01T00:00:01.0000000Z |

**Løsning**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Forklaring**: Brug mellemliggende til Kig i input streame en begivenhed tilbage og få værdien foretage. Derefter sammenligner med afkrydsningsfeltet Foretag på den aktuelle hændelse, og Medtag begivenheden, hvis de er den samme og bruge mellemliggende tid til at hente data om den forrige bil.

## <a name="query-example-detect-duration-between-events"></a>Eksempel med en opdateringsforespørgsel: registrere varigheden mellem begivenheder
**Beskrivelse**: finde varigheden af en given hændelse. For eksempel den angivne en web clickstream fastlægge tid på en funktion.

**Input**:  
  
| Bruger | Funktion | Begivenhed | Tid |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Start | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Afslut | 2015-01-01T00:00:08.0000000Z |
  
**Output**:  
  
| Bruger | Funktion | Varighed |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Løsning**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Forklaring**: Brug sidste funktionen til at hente sidste klokkeslæt-værdi, når hændelsestype blev 'Start'. Bemærk, at sidste funktionen bruger PARTITION af [bruger] til at angive, at resultatet skal beregnes per unikt bruger.  Forespørgslen har en 1 time maksimale grænseværdi for tidsforskel mellem 'Start' og 'Stop' begivenheder, men kan konfigureres, som er nødvendig (GRÆNSE DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Eksempel med en opdateringsforespørgsel: registrere varigheden af en betingelse
**Beskrivelse**: Find ud af hvor lang tid der opstod en betingelse for.
Antag f.eks, en fejl, der udløste alle biler har en forkert tykkelse (over 20.000 pund) – vi vil beregne varigheden af fejlen.

**Input**:

| Gøre | Tid | Vægt |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Output**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Løsning**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Forklaring**: Brug mellemliggende tid til at se input strømmen til 24 timer og se efter forekomster, hvor StartFault og StopFault bruges af vægt < 20000.

## <a name="query-example-fill-missing-values"></a>Eksempel med en opdateringsforespørgsel: udfylde manglende værdier
**Beskrivelse**: For strømmen af hændelser, der har manglende værdier, giver en strøm af hændelser med jævne mellemrum.
For eksempel generere begivenhed hver 5 sekunder, som rapporterer senest vist datapunktet.

**Input**:

| t | værdi |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**Output (første 10 rækker)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Løsning**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Forklaring**: denne forespørgsel genererer begivenheder i sekundet 5 og kan udskrives den sidste hændelse, der blev modtaget før. [Hopping vindue] (https://msdn.microsoft.com/library/dn835041.aspx "Hopping vinduet - Azure Stream Analytics") varighed bestemmer, hvor langt tilbage forespørgslen ser for at finde den seneste hændelse (300 sekunder i dette eksempel).


## <a name="get-help"></a>Få hjælp
For at få hjælp, kan du prøve vores [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Næste trin

- [Introduktion til Azure Stream Analytics](stream-analytics-introduction.md)
- [Introduktion til brug af Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalere Azure Stream Analytics-job](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics Management RESTEN API Reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
