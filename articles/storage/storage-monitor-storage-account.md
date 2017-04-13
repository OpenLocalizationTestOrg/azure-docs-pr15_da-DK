<properties
    pageTitle="Hvordan du kan overvåge en lagerplads konto | Microsoft Azure"
    description="Lær at overvåge en lagerplads konto i Azure ved hjælp af portalen Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Overvåge en lagerplads konto i portalen Azure

## <a name="overview"></a>Oversigt

Du kan overvåge kontoen lagerplads fra [Azure-portalen](https://portal.azure.com). Når du konfigurerer kontoen lagerplads for overvågning via portalen, bruger Azure-lager [Lagerplads Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx) til at registrere målepunkter for din konto og logge anmodning om data.

> [AZURE.NOTE]Yderligere omkostninger er knyttet til at undersøge overvågningsdata [Azure-portalen](https://portal.azure.com). Få mere at vide under <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">lagerplads Analytics og fakturering</a>. <br />

> Azure fillagring i øjeblikket understøtter lagerplads Analytics målepunkter, men endnu understøtter ikke logføring. Du kan aktivere måleredskaber for Azure fillagring via [Azure-portalen](https://portal.azure.com).

> Lagerplads konti med en gentagelse af typen af Zone overflødige lagerplads (ZRS) har ikke målepunkter eller muligheden for logføring aktiveret på nuværende tidspunkt. 

> Du kan finde en dybdegående guide på ved hjælp af lagerplads analyser og andre værktøjer til at identificere, diagnosticere og foretage fejlfinding af Azure-lager-relaterede problemer, [skærm, diagnosticere, og foretage fejlfinding af Microsoft Azure-lager](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Sådan: Konfigurere overvågning for en lagerplads konto

1. Klikke på **lagerplads**på [Azure-portalen](https://portal.azure.com), og klik kontonavnet lagerplads til at åbne dashboard.

2. Klik på **Konfigurer**, og Rul ned til **overvågning af** indstillingerne for de blob, tabel og kø tjenester.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. Angiv niveauet for overvågning og dataopbevaringspolitik for hver tjeneste i **overvågning**:

    -  Hvis du vil angive niveauet for overvågning, Vælg en af følgende:

      **Minimale** - indsamler målepunkter som vandindtrængen/udgangspunkt, tilgængelighed, forsinkelse og succes procenter, som skal aggregeres til blob, tabel og kø tjenester.

      **Detaljeret** - indsamler det samme sæt målepunkter for hver lagerplads handling i Azure Storage Service API ud over den minimale målepunkter. Detaljeret målepunkter aktivere nærmere analyse af problemer, der opstår under programmet handlinger.

      **Fra** - deaktiverer overvågning. Eksisterende overvågningsdata bevares til slutningen af opbevaringsperioden.

- Hvis du vil angive dataopbevaringspolitik, i **opbevaring (i dage)**, skal du skrive antallet af dage af data for at bevare fra 1 til 365 dage. Hvis du ikke vil angive en opbevaringspolitik, du Angiv nul. Hvis der ikke er nogen opbevaringspolitik, er det op til at slette den overvågningsdata. Vi anbefaler, at en opbevaringspolitik, der er baseret på hvor længe du vil bevare lagerplads analytics-data til din konto, så gamle og ubrugt analytics-data kan slettes ved system uden omkostninger.

4. Klik på **Gem**, når du er færdig overvågning konfigurationen.

Du skal starte, se overvågningsdata på dashboardet og siden **skærm** efter omkring en time.

Før du konfigurerer overvågning for en lagerplads konto, ingen overvågningsdata der indsamles, og målepunkter diagrammerne på siden dashboard og **skærm** er tom.

Når du har angivet overvågning niveauer og opbevaringspolitikker, kan du vælge, hvilken af de tilgængelige før mailadvarslen sendes til at overvåge [Azure-portalen](https://portal.azure.com), og hvilke målepunkter afbilde på målepunkter diagrammer. Et standardsæt af målepunkter vises på hver overvågning niveau. Du kan bruge **Tilføje målepunkter** til at tilføje eller fjerne målepunkter fra listen målepunkter.

Målepunkter er gemt i kontoen lagerplads i fire tabeller med navnet $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue og $MetricsCapacityBlob. Du kan finde yderligere oplysninger [Om Analytics Lagringsmål](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Sådan: tilpasse dashboardet for overvågning

Du kan vælge op til seks målepunkter afbilde på målepunkter diagram fra ni tilgængelige målepunkter på dashboardet til. Tilgængelighed, succes procentdel og samlet antal anmodninger målepunkter er tilgængelige for hver tjeneste (blob, tabel og kø). Tilgængelig på dashboardet til målene er det samme for minimale eller detaljeret overvågning.

1. Klikke på **lagerplads**på [Azure-portalen](https://portal.azure.com), og klik derefter på navnet på lagerplads konto til at åbne dashboard.

2. Hvis du vil ændre målene, der er afbildet i diagrammet, kan du her en af følgende fremgangsmåder:

    - Hvis du vil tilføje en ny metrikværdi til diagrammet, skal du klikke på farvede afkrydsningsfeltet ud for overskriften metriske i tabellen nedenfor diagrammet.

    - Hvis du vil skjule en metrikværdi, der er afbildet i diagrammet, skal du fjerne markeringen farvede afkrydsningsfeltet ud for overskriften metriske.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Som standard diagrammet i tendenser, viser den aktuelle værdi af hver metrisk (indstillingen **Relative** øverst i diagrammet). Hvis du vil have vist en Y-aksen, så du kan se absolutte værdier, skal du vælge **absolut**.

4. Hvis du vil ændre tidsintervallet Vælg målepunkter diagram vises 6 timer, 24 timer eller 7 dage øverst i diagrammet.


## <a name="how-to-customize-the-monitor-page"></a>Sådan: tilpasse siden skærm

Du kan se det samlede sæt af målepunkter for kontoen lagerplads på siden **skærm** .

- Hvis kontoen lagerplads har konfigureret minimale overvågning, samles målepunkter som vandindtrængen/udgangspunkt, tilgængelighed, forsinkelse og succes procenter fra blob, tabel og kø tjenester.

- Hvis kontoen lagerplads har konfigureret detaljeret overvågning, findes målene på en bedre opløsning på individuelle lagerplads handlinger ud over aggregeringerne service-niveau.

Brug følgende procedurer til at vælge, hvilke lagringsmål til at få vist i målepunkter diagrammer og tabel, der vises på siden **skærm** . Disse indstillinger påvirker ikke den samling, sammenlægning og opbevaring af overvågningsdata i kontoen lagerplads.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Sådan: føje målepunkter til tabellen målepunkter


1. Klikke på **lagerplads**på [Azure-portalen](https://portal.azure.com), og klik derefter på navnet på lagerplads konto til at åbne dashboard.

2. Klik på **skærmen**.

    Siden **skærm** åbnes. Tabellen målepunkter viser et undersæt af de målinger, som er tilgængelige for overvågning som standard. Illustrationen viser skærm standardvisningen for en lagerplads konto med detaljeret overvågning konfigureret for alle tre tjenester. Bruge **Tilføje målepunkter** til at vælge den metrik, du vil overvåge fra alle tilgængelige målepunkter.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Overvej omkostninger, når du vælger målepunkter. Der er transaktion og udgangspunkt omkostninger, der er knyttet til opdatering af overvågning vises. Få mere at vide under [lagerplads Analytics og fakturering](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Klik på **Tilføj målepunkter**.

    De samlede målepunkter, der er tilgængelige i minimale overvågning er øverst på listen. Hvis afkrydsningsfeltet er markeret, vises metrikværdien i listen målepunkter.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Hold markøren over højre side af dialogboksen for at få vist et rullepanel, som du kan trække for at rulle gennem flere statistikker i visningen.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Klik på pil ned ved en metrikværdi for at udvide en liste over handlinger på metrisk er fastsat dynamisk for at medtage. Markér hver handling, du vil have vist i tabellen målepunkter [Azure-portalen](https://portal.azure.com).

    I nedenstående illustration er godkendelse FEJLPROCENTEN metrisk udvidet.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Når du vælger målepunkter for alle tjenester, skal du klikke på OK (markering) for at opdatere overvågning konfigurationen. De valgte målepunkter føjes til tabellen målepunkter.

7. Hvis du vil slette en metrikværdi fra tabellen, skal du klikke på metrikværdien for at markere den, og klik **Slette metrisk**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Sådan: tilpasse målepunkter diagrammet på siden skærm

1. På siden **skærm** for kontoen lagerplads i tabellen målepunkter skal du vælge op til 6 målepunkter afbilde på målepunkter diagrammet. Hvis du vil markere en metrikværdi, skal du markere feltet på den venstre side. Hvis du vil fjerne en metrikværdi fra diagrammet, skal du fjerne markeringen i afkrydsningsfeltet.

2. For at skifte mellem relative værdier (sidste værdi, der kun vises) og absolutte værdier (Y-aksen vises) diagrammet skal du vælge **relativ** eller **absolut** øverst i diagrammet.

3.  Hvis du vil ændre intervallet klokkeslættet på målepunkter diagram viser, Vælg **6 timer**, **24 timer**eller **7 dage** øverst i diagrammet.



## <a name="how-to-configure-logging"></a>Sådan: Konfigurere logføring

For hver af de tilgængelige med kontoen lagerplads (blob, tabel og kø) lagerplads tjenester, du kan gemme diagnosticering logfiler for anmodninger om læse, skrive anmodninger og/eller slette anmodninger og kan angive opbevaringspolitikken data for hver af tjenesterne.

1. Klikke på **lagerplads**på [Azure-portalen](https://portal.azure.com), og klik derefter på navnet på lagerplads konto til at åbne dashboard.

2. Klik på **Konfigurer**, og brug pil ned på tastaturet til at rulle ned til **logføring**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. For hver service (blob, tabel og kø) skal du konfigurere følgende:

    - Typer af anmodning om at logge: Læs anmodninger, skrive anmodninger og slette anmodninger.

    - Antallet af dage til at bevare de registrerede data. Indtast nul er, hvis du ikke vil angive en opbevaringspolitik. Hvis du ikke angiver en opbevaringspolitik, er det op til at slette logfiler.

4. Klik på **Gem**.

Loggene diagnosticering gemmes i en blob objektbeholder med navnet $logs i kontoen lagerplads. Om adgang til objektbeholderen $logs kan finde oplysninger [Om lagerplads-Analytics logføring](http://msdn.microsoft.com/library/azure/hh343262.aspx).
