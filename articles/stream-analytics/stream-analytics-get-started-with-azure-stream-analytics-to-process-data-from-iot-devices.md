<properties
    pageTitle="Introduktion til Azure Stream Analytics til proces data fra IoT enheder. | Microsoft Azure"
    description="IoT føler mærker og datastrømme med stream analyser og databehandling i realtid"
    keywords="iot løsning, komme i gang med iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Introduktion til Azure Stream Analytics til proces data fra IoT enheder

I dette selvstudium lærer du, hvordan du opretter stream-behandling logik for at indsamle data fra internettet af ting (IoT) enheder. Vi bruger en reale, Internet af ting (IoT) use case til at vise, hvordan du opretter din løsning hurtigt og økonomisk.

## <a name="prerequisites"></a>Forudsætninger

-   [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/)
-   Forespørgsels- og eksempelfiler kan downloades fra [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scenarie

Contoso, som er en virksomhed i boksen industrielle automatisering, har fuldstændig automatisk dens produktionsproces. Maskinen i denne plante har sensorer, der kan udsende streams af data i realtid. I dette scenarie skal floor en produktionsleder, ønsker realtid indsigt fra føler data til at søge efter mønstre og udføre handlinger på dem. Vi bruger Stream Analytics forespørgsel sprog (SAQL) over føler dataene til at finde interessante mønstre fra indgående strømmen af data.

Her er der genereres data fra en enhed, Texas værktøj føler mærke.

![Texas værktøj føler mærke](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

Data i dataene, der er i JSON-format og ser ud på følgende:


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

I et scenarie med reale, kan du har hundredvis af disse sensorer genererende begivenheder som en stream. Ideelt set skal en gateway-enhed køre kode for at overføre disse hændelser til [Azure begivenhed hubber](https://azure.microsoft.com/services/event-hubs/) eller [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/). Stream Analytics tingene vil indtager disse begivenheder fra begivenhed Hubs og køre realtid analytics forespørgsler i streams. Derefter kan du sende resultaterne til en af de [understøttede output](stream-analytics-define-outputs.md).

Til nemmere at bruge indeholder denne få Introduktion vejledning en eksempel-datafil, som blev hentet fra reelle føler mærke enheder. Du kan køre forespørgsler på eksempeldataene og få vist resultater. I efterfølgende selvstudier lærer hvordan du knytter tingene til input du og skriver og installere dem til Azure-tjenesten.

## <a name="create-a-stream-analytics-job"></a>Oprette et Stream Analytics-job

1. Klik på plustegnet i [Azure-portalen](http://portal.azure.com), og skriv derefter **STREAM ANALYTICS** i tekstvinduet til højre. Vælg derefter **Stream Analytics job** i resultatlisten.

    ![Oprette et nyt Stream Analytics-job](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Angiv et entydigt jobnavn, og Bekræft, at abonnementet er det rigtige arbejdsområde til dit arbejde. Derefter Opret en ny ressourcegruppe, eller Vælg et eksisterende dokument på dit abonnement.

3. Vælg derefter en placering til dit arbejde. Filoverførsel, når du vælger den samme placering som ressourcegruppe og tilsigtede lagerplads konto anbefales til hastigheden for behandling og reduktion af omkostninger i data.

    ![Oprette et nyt Stream Analytics-job detaljer](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] Du skal oprette denne konto lagerplads, der kun én gang hver område. Denne lagerplads deles på tværs af alle Stream Analytics-job, der er oprettet i det pågældende område.

4. Markér afkrydsningsfeltet for at anbringe tingene på dit dashboard, og klik derefter på **Opret**.

    ![oprettelse af job i gang](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Skal du se afsnittet en "fordeling i gang …" viste i øverst til højre i dit browservindue. Snart ændres den til et færdige vindue som vist nedenfor.

    ![oprettelse af job i gang](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Oprette en Azure Stream Analytics-forespørgsel

Når tingene er blevet oprettet er det tid til at åbne den og oprette en forespørgsel. Du kan nemt få adgang til dit arbejde ved at klikke på feltet for den.

![Jobbet felt](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Klik på feltet **forespørgsel** for at gå til Forespørgselseditor i ruden **Job topologi** . **Forespørgselseditor** kan du angive en T-SQL-forespørgsel, som udfører transformationen over indgående begivenhed dataene.

![Forespørgselsfeltet](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Forespørgsel: Arkivere din rækkedata

Den enkleste type forespørgsel er en pass-through-forespørgsel, der arkiveres alle indtastede data til den angivne output. Hente data eksempelfilen fra [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) til en placering på computeren. 

1. Indsæt forespørgslen fra filen PassThrough.txt. 

    ![Test input værdistrøm](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Klik på de tre prikker ud for dit input, og Markér **overføre eksempeldata fra fil** .

    ![Test input værdistrøm](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. En rude åbnes i højre side som et resultat, i det Vælg HelloWorldASA InputStream.json-datafil fra din hentede placering og klikke på **OK** nederst i ruden.

    ![Test input værdistrøm](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. Klik på det **teste** tandhjulet øverst til venstre område i vinduet og behandle dit testforespørgsel mod eksempel datasættet. Der åbnes et vindue med resultater under din forespørgsel, som behandlingen er fuldført.

    ![Testresultater](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Forespørgsel: Filtrere de data, der er baseret på en betingelse

Lad os prøve til at filtrere resultaterne baseret på en betingelse. Vi vil gerne vise resultater for kun de hændelser, der kommer fra "sensorA." Forespørgslen er i filen Filtering.txt.

![Filtrere en datastream](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Bemærk, at forespørgslen store og små bogstaver sammenligner en strengværdi. Klik på **Test** tandhjul igen for at udføre forespørgslen. Forespørgslen skal returnere 389 rækker af 1860 begivenheder.

![Anden outputresultater fra test forespørgsel](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Forespørgsel: Besked til at udløse en arbejdsproces for business

Lad os gør vores query mere detaljeret. For hver type føler vil vi overvåge Gennemsnitstemperaturen per 30 sekunder vindue og vise resultater kun, hvis Gennemsnitstemperaturen er større end 100 grader. Vi vil skrive følgende forespørgsel og derefter klikke på **Test** for at få vist resultaterne. Forespørgslen er i filen ThresholdAlerting.txt.

![30 sekunder filterforespørgsel](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Du bør nu se resultater, der indeholder kun 245 rækker og navnene på sensorer hvor gennemsnittet tempereret er større end 100. Denne forespørgsel grupperer strømmen af begivenheder efter **dspl**, som er navnet på føler over et **Tumbling vinduet** på 30 sekunder. Tidsmæssig forespørgsler skal du angive, hvordan vi vil tid til opgavefremdrift. Ved hjælp af **TIDSSTEMPEL BY** -delsætning, har vi angivet kolonnen **OUTPUTTIME** for at knytte gange til alle tidsmæssig beregninger. Læs artiklerne om MSDN om [Tidsstyring](https://msdn.microsoft.com/library/azure/mt582045.aspx) og [Windowing funktioner](https://msdn.microsoft.com/library/azure/dn835019.aspx), du kan finde detaljerede oplysninger.

### <a name="query-detect-absence-of-events"></a>Forespørgsel: Registrere fravær af hændelser

Hvordan kan vi skriver en forespørgsel for at finde manglende input begivenheder? Lad os finde sidste gang, en føler sendt data og derefter sendte ikke begivenheder til næste minuttet. Forespørgslen er i filen AbsenseOfEvent.txt.

![Registrere fravær af hændelser](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Vi bruger her en **Venstre ydre** joinforbindelse til samme datastrømmen (selv-joinforbindelse). For en **indre** joinforbindelse returneres et resultat, kun, når der findes et match.  Hvis en begivenhed fra venstre side af joinforbindelsen er ikke-relaterede, til en **Venstre ydre** joinforbindelse returneres en række, der indeholder Null-værdier for alle kolonner i højre side. Denne metode er praktisk til at finde fravær af hændelser. Se vores MSDN dokumentation kan finde flere oplysninger om at [deltage i](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Konklusion

Formålet med dette selvstudium er til at vise, hvordan du kan skrive forskellige Stream Analytics forespørgselssprog forespørgsler og få vist resultater i browseren. Men dette er første gang. Du kan gøre meget mere med Stream analyser. Stream Analytics understøtter en lang række input og skriver og kan også bruge funktioner i Azure Machine Learning for at gøre den til et robust værktøj til at analysere datastreams. Du kan begynde at udforske mere om Stream analyser ved hjælp af vores [læ kort](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Du kan finde flere oplysninger om, hvordan du skal skrive forespørgsler, du læse artiklen om [almindelige forespørgsel mønstre](./stream-analytics-stream-analytics-query-patterns.md).
