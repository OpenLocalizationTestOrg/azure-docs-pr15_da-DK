<properties 
   pageTitle="Windows og Linux ydeevne tællere i Log Analytics | Microsoft Azure"
   description="Tællere i ydeevne indsamles af Log Analytics til at analysere ydeevne på Windows og Linux supportmedarbejdere.  I denne artikel beskrives, hvordan du konfigurerer samling af tællere i ydeevne til både Windows og Linux supportmedarbejdere, oplysninger om de er gemt i OMS lager, og hvordan du analysere dem i portalen OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />

# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Windows og Linux ydeevne datakilder i Log Analytics 

Tællere i ydeevne i Windows og Linux giver indblik ydeevnen for hardwarekomponenter, operativsystemer og programmer.  Log Analytics kan indsamle tællere i ydeevne med hyppige intervaller for nær realtid (NRT) analyse ud over sammenlægning ydelsesdata længere analysen af ord og -rapportering.

![Tællere i ydeevne](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Konfigurere tællere i ydeevne

Konfigurere tællere i ydeevne i [menuen Data i indstillinger for analyser](log-analytics-data-sources.md#configuring-data-sources).

Når du først konfigurere Windows eller Linux ydeevne tællere til et nyt arbejdsområde OMS, får du mulighed for at hurtigt at oprette flere almindelige tællere.  De er angivet med et afkrydsningsfelt ud for hver.  Sikre, at nogen tællere, du vil oprette først er markeret, og klik derefter på **Tilføj de valgte performance tællere**.

![Konfigurere tællere i Windows ydeevne](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Følg denne fremgangsmåde for at tilføje en ny Windows ydeevne tæller at indsamle.

1. Skriv navnet på tælleren i tekstfeltet i Formatér *objekt (forekomst) \counter*.  Når du begynder at skrive, får du vist med en tilsvarende liste over almindelige tællere.  Du kan vælge en tæller fra listen eller skrive på et af dine egne.  Du kan også returnere alle forekomster for en bestemt tæller ved at angive *object\counter*. 
2. Klik på **+** , eller tryk på **Enter** for at føje tælleren til listen.
3. Når du tilføjer en tæller, bruger standard i 10 sekunder for dens **Eksempel Interval**.  Du kan ændre det til en højere værdi på op til 1800 sekunder (30 minutter), hvis du vil reducere lagerplads krav indsamlede ydelsesdata.
4. Klik på knappen **Gem** øverst på skærmen for at gemme konfigurationen, når du er færdig med at tilføje tællere.

![Konfigurere tællere i Linux ydeevne](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Følg denne fremgangsmåde for at tilføje en ny Linux ydeevne tæller at indsamle.

1. Alle ændringer i konfigurationen publiceres som standard automatisk i alle supportmedarbejdere.  For Linux supportmedarbejdere, der sendes en konfigurationsfil til Fluentd dataindsamler.  Hvis du vil ændre denne fil manuelt på hver Linux agent, derefter fjerne markeringen i afkrydsningsfeltet *Anvend under konfiguration til min Linux-computere*.
2. Skriv navnet på tælleren i tekstfeltet i Formatér *objekt (forekomst) \counter*.  Når du begynder at skrive, får du vist med en tilsvarende liste over almindelige tællere.  Du kan vælge en tæller fra listen eller skrive på et af dine egne.  
2. Klik på **+** , eller tryk på **Enter** for at tilføje tælleren til listen over andre tællere til objektet.
3. Du kan bruge det samme **Eksempel Interval**alle tællere til et objekt.  Standardværdien er 10 sekunder.  Du ændre det til en højere værdi på op til 1800 sekunder (30 minutter), hvis du vil reducere lagerplads krav indsamlede ydelsesdata.
4. Klik på knappen **Gem** øverst på skærmen for at gemme konfigurationen, når du er færdig med at føje tællere.

## <a name="data-collection"></a>Dataindsamling

Log Analytics indsamler alle angivne ydeevne tællere til deres angivne eksempel interval på alle supportmedarbejdere, der har, tæller installeret.  Dataene sammenlægges ikke, og den rækkedata er tilgængelig i alle log Søg visninger for den varighed, der er angivet af abonnementet OMS.


## <a name="performance-record-properties"></a>Ydeevnen postegenskaber

Ydeevnen poster har en type af **Performance** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Computer         | Computer, der er indsamlet begivenheden fra. |
| CounterName      | Navnet på tælleren ydeevne |
| CounterPath      | Hele stien til tælleren i formularen \\ \\ \<Computer >\\objekt(forekomst)\\tæller. |
| CounterValue     | Numeriske værdi for tælleren.  |
| Navn på forekomst     | Navnet på begivenhed forekomsten.  Tømme Hvis nogen forekomst. |
| Objektnavn       | Navnet på objektet ydeevne |
| SourceSystem  | Type af dataene er indsamlet fra en agent. <br> Oprette forbindelse OpsManager – Windows agent, enten direkte eller SCOM <br> Linux – alle Linux supportmedarbejdere  <br> AzureStorage – Azure diagnosticering |
| TimeGenerated       | Dato og klokkeslæt, der blev prøver fra dataene. |


## <a name="sizing-estimates"></a>Beregner et skøn over størrelseshåndtag

 Et overslag til samling af en bestemt tæller med 10 sekunders intervaller er cirka 1 MB dagen hver forekomst.  Du kan beregne en bestemt tæller med følgende formel lagerplads krav.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Log søgninger med ydeevnen poster

Den følgende tabel indeholder forskellige eksempler på log søgninger, der henter ydeevne poster.

| Forespørgsel | Beskrivelse |
|:--|:--|
| Skriv = performance | Alle ydelsesdata |
| Skriv = performance Computer = "MyComputer" | Alle ydelsesdata fra en bestemt computer |
| Skriv = performance CounterName = "Diskkølængde" | Alle ydelsesdata for en bestemt tæller |
| Skriv = performance (objektnavn = Processor) CounterName = "% Processor klokkeslætsformat" navn på forekomst = _Total & #124; måle Avg(Average) som AVGCPU af Computer | Gennemsnitlig CPU-forbrug på tværs af alle computere |
| Skriv = performance (CounterName = "% Processor klokkeslætsformat") & #124;  måle max(Max) ved Computer | Maksimale CPU-forbrug på tværs af alle computere |
| Skriv = performance objektnavn = logisk disk CounterName = "Aktuelle Disk længde kø" Computer = "MyComputerName" & #124; måle Avg(Average) ved navn på forekomst | Beregne gennemsnittet af Aktuel længde på Disk kø på tværs af alle forekomster af en given computer |
| Skriv = performance CounterName = "DiskTransfers/sec" & #124; måle percentile95(Average) ved Computer | 95th fraktil af Disk overførsler/Sec på tværs af alle computere |
| Skriv = performance CounterName = navn på forekomst "% Processor klokkeslætsformat" = "_Total" & #124; måle avg(CounterValue) ved Computer Interval 1 time | Hver time gennemsnittet af CPU-brug på tværs af alle computere |
| Skriv = performance Computer = "MyComputer" CounterName = % * navn på forekomst = _Total & #124; måle percentile70(CounterValue) ved CounterName Interval 1 time | Hver time 70 fraktil for hver procentvis tælleren % for en bestemt computer |
| Skriv = performance CounterName = navn på forekomst "% Processor klokkeslætsformat" = "_Total" (Computer = "MyComputer") & #124; måle min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) ved Computer Interval 1 time | Hver time gennemsnit, minimum, maksimale og 75 fraktil CPU-brug for en bestemt computer |

## <a name="viewing-performance-data"></a>Visning af ydelsesdata

Når du kører en log søgning efter ydelsesdata, vises visningen **Log** som standard.  For at få vist dataene i grafisk format, skal du klikke på **målepunkter**.  En detaljeret grafisk visning, klik på den **+** ud for en tæller.  

![Få vist målepunkter skjult](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Hvis det tidsrum, du har valgt er 6 timer eller derunder, er diagrammet opdateres hvert par.  Dynamiske data vises på i højre side af diagrammet i lyseblå.

![Få vist målepunkter udvidet med dynamiske data](media/log-analytics-data-sources-performance-counters/metricsexpanded.png)

Aggregere ydeevne data i en log søgning, skal du se [efter behov metriske sammenlægning og visuelle effekter i OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger.  
- Eksportere indsamlede data til [Power BI](log-analytics-powerbi.md) til flere visualiseringer og analyse.