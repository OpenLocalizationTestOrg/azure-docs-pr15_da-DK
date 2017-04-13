<properties 
   pageTitle="Brug af knudepunkt udførelse af visningen i Data sø Tools til Visual Studio | Microsoft Azure" 
   description="Lær at bruge visningen knudepunkt udførelse af til eksamen Data sø Analytics sager." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/13/2016"
   ms.author="jgao"/>

# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Brug af knudepunkt udførelse af visningen i Data sø Tools til Visual Studio

Lær at bruge visningen knudepunkt udførelse af til eksamen Data sø Analytics sager.

## <a name="prerequisites"></a>Forudsætninger

- Et grundlæggende kendskab til brug af Data sø Tools til Visual Studio til at udvikle U-SQL-script.  Se [Selvstudium: udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

## <a name="open-the-vertex-execution-view"></a>Åbne visningen knudepunkt udførelse af

For en bestemt opgave, kan du klikke på linket "Knudepunkt udførelse af visning" i det nederste venstre hjørne. Du kan blive bedt om at indlæse profiler først, og det kan tage lidt tid afhængigt af din netværksforbindelse.

![Data sø Analytics værktøjer knudepunkt udførelse af visning](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Forstå knudepunkt udførelse af visning

Når du har angivet knudepunkt udførelse af visningen, er der tre dele:

![Data sø Analytics værktøjer knudepunkt udførelse af visning](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

- Knudepunkt Datavælger: er vælgeren knudepunkt i venstre side.  Du kan vælge knudepunkterne ved funktioner (f.eks de 10 vigtigste data læse, eller Vælg ved fase).

    En af de mest almindelige anvendte filtre er knudepunkter på kritiske vej. Kritisk vej er længste stien til et U-SQL-job. Det er praktisk til at optimere dine sager ved at kontrollere, hvilke knudepunkt bruger længst tid.

- Den øverste midterste rude:

    ![Data sø Analytics værktøjer knudepunkt udførelse af visning](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

    Denne visning viser også kører status for alle knudepunkter. Den konverterer tid i overensstemmelse hermed til din lokale computer, og viser forskellige status i forskellige farver.

- Den nederste midterste rude:

    ![Data sø Analytics værktøjer knudepunkt udførelse af visning](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

    - Processen navn: Navnet på forekomsten knudepunkt. Det består af forskellige dele i StageName | VertexName | VertexRunInstance. For eksempel SV7_Split [62] .v1 knudepunktet står for den anden, der kørende forekomst (.v1, indeks, startende fra 0) af knudepunkt tal 62 i fase SV7_Split.
    - Samlede Data læse-og skriveadgang: Dataene er læst/skrevet af denne knudepunkt.
    - Stat/Afslut Status: Den endelige status, når knudepunktet er afsluttet.
    - Afslut kode/manglende Type: Fejlen, når knudepunktet mislykkedes.
    - Oprettelse af årsag: Hvorfor knudepunktet blev oprettet.
    - Ressource ventetid/proces ventetid/VN kø ventetid: den tid, det tager for knudepunkt at vente ressourcer til at behandle data, og til at holde i køen.
    - Processen/Creator GUID: GUID for det aktuelle kørende knudepunkt eller forfatteren.
    - Version: den n'te forekomst af igangværende knudepunktet (systemet kan planlægge, at nye forekomster af et knudepunkt til mange årsager, for eksempel failover, beregne redundans osv.)
    - Version oprettes gang.
    - Behandle Opret Start tid/proces i kø tid/proces Start tid/proces færdigt tid: Når knudepunkt processen begynder oprettelsestidspunkt. Når processen knudepunkt starter til køen; Når visse knudepunkt processen starter; Når visse knudepunktet er fuldført.

## <a name="next-steps"></a>Næste trin

- For at få et overblik over Data sø Analytics skal du se [Oversigt over Azure Data sø analyser](data-lake-analytics-overview.md).
- For at komme i gang U-SQL udviklingsprogrammer skal du se [udvikle U-SQL-scripts ved hjælp af Data sø Tools til Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Få U-SQL under [Introduktion til Azure Data sø Analytics U-SQL-sprog](data-lake-analytics-u-sql-get-started.md).
- Se [administrere Azure Data sø Analytics Azure-portalen](data-lake-analytics-manage-use-portal.md)til af administrationsopgaver.
- Hvis du vil logge diagnosticeringsoplysninger, se [adgang til diagnosticering logfiler for Azure Data sø analyser](data-lake-analytics-diagnostic-logs.md)
- For at se en mere kompleks forespørgsel skal du se [analysér websted logfiler, der bruger Azure Data sø analyser](data-lake-analytics-analyze-weblogs.md).
- For at få vist oplysninger om job, se [Brug Job Browser og Job visning for Azure Data sø Analytics-job](data-lake-analytics-data-lake-tools-view-jobs.md)