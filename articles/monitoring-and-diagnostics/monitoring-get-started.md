<properties
    pageTitle="Introduktion til Azure skærm | Microsoft Azure"
    description="Introduktion til brug af Azure skærm til at få indsigt i driften af dine ressourcer og handle ud fra data."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="johnkem"/>

# <a name="get-started-with-azure-monitor"></a>Introduktion til Azure skærm

Azure skærm er tjenesten platform, som indeholder en enkelt kilde til at overvåge Azure ressourcer. Med Azure skærm, kan du visualisere, forespørgsel, distribuere, arkivere og handle på målepunkter og logfiler, der kommer fra ressourcer i Azure. Du kan arbejde med disse data ved hjælp af skærm portalen bladet, [Overvåge PowerShell-cmdletter](./insights-powershell-samples.md), [På tværs af platforme CLI](insights-cli-samples.md)eller [Azure skærm REST API'er](https://msdn.microsoft.com/library/dn931943.aspx). I denne artikel vil gennemgå vi nogle af de vigtigste komponenter i Azure skærm, ved hjælp af portalen til demonstration.

1. Gå til **flere tjenester** i portalen, og find indstillingen **skærm** . Klikke på stjerneikonet for at føje denne indstilling til listen Favoritter, så den altid er nem adgang fra den venstre navigationslinje.

    ![Overvåge, i listen over tjenester](./media/monitoring-get-started/monitor-more-services.png)

2. Klik på indstillingen **skærm** til at åbne bladet **skærm** . Denne blade samler alle dine overvågning indstillinger og data i en samlet visning. Det åbner til sektionen **aktivitetslog** .

    ![Overvåge blade navigation](./media/monitoring-get-started/monitor-blade-nav.png)

    > [AZURE.WARNING] Indstillingerne **Service beskeder** og **meddelelse grupper** vises over vises kun for dem, som har tilsluttet sig den Private eksempelvisning af disse funktioner.

    Azure skærm har tre grundlæggende kategorier af overvågningsdata: **aktivitetslog**, **målepunkter**og **diagnosticeringslogfiler**.

3. Klik på **log af aktivitet** for at sikre dig, vises sektionen aktivitet log.

    ![Aktivitet Log blade](./media/monitoring-get-started/monitor-act-log-blade.png)

    [**Aktivitetslog**](./monitoring-overview-activity-logs.md) beskrives alle handlinger, der udføres på ressourcer i dit abonnement. Bruger aktivitet loggen, kan du bestemme den ' hvad, som, og hvornår ' for en hvilken som helst oprette, opdatere eller slette handlinger på ressourcer i dit abonnement. For eksempel aktivitet loggen kan du se, hvornår en WebApp stoppede og der ikke længere den. Aktivitet loghændelser er gemt i platformen og til at forespørge om 90 dage.
   
    Du kan oprette og gemme forespørgsler til generelle filtre og derefter fastgøre de vigtigste forespørgsler til en portalen dashboard, så du altid ved, om der er foretaget hændelser, der opfylder dine kriterier.

4. Filtrere visningen til en bestemt ressourcegruppe over den sidste uge, og derefter klikke på knappen **Gem** .

    ![Gemme aktivitet log forespørgsel](./media/monitoring-get-started/monitor-act-log-save.png)

5. Nu skal du klikke på knappen **Fastgør** .

    ![Klikke på Fastgør til aktivitetslog](./media/monitoring-get-started/monitor-act-log-pin.png)

    De fleste af visninger i denne gennemgang kan være fastgjort til et dashboard. Dette kan du oprette en enkelt kilde af oplysninger til funktionsdygtige data på dine tjenester. 

6. Gå tilbage til dit dashboard. Du kan nu se, at forespørgslen (og antal resultater) vises i dit dashboard. Dette er nyttigt, hvis du vil have vist eventuelle høj profil handlinger, der er opstået for nylig i dit abonnement, f.eks. en ny rolle har fået tildelt, eller en VM er blevet slettet.

    ![Aktivitet logfilen fastgjort til dashboard](./media/monitoring-get-started/monitor-act-log-db.png)

7. Gå tilbage til feltet **skærm** og klikke på sektionen **målepunkter** . Først skal du vælge en ressource ved filtrering og vælge Hjælp på rullelisten indstillinger på øverst del af bladet.

    ![Filtrere ressourcen for målepunkter](./media/monitoring-get-started/monitor-met-filter.png)

    Alle Azure ressourcer udsende [**målepunkter**](./monitoring-overview-metrics.md). Denne visning samler alle målepunkter i en enkelt rude af glas så du nemt kan forstå, hvordan dine ressourcer udfører.

8. Når du har valgt en ressource, vises alle tilgængelige målepunkter på venstre side af bladet. Du kan diagram flere målepunkter på én gang ved at vælge målepunkter og ændre graph type- og klokkeslætsintervallet. Du kan også få vist alle metriske beskeder, der er angivet på denne ressource.

    ![Metriske blade](./media/monitoring-get-started/monitor-metric-blade.png)

    > [AZURE.NOTE] Nogle målepunkter er kun tilgængelige ved at aktivere [Programmet indsigt](../application-insights/app-insights-overview.md) og/eller Windows eller Linux Azure diagnosticering af din ressource.

9. Når du er tilfreds med dit diagram, kan du bruge knappen **Fastgør** til at fastgøre den til dit dashboard.

10. Gå tilbage til bladet **skærm** , og klik på **diagnosticeringslogfiler**.

    ![Diagnosticeringslogfiler blade](./media/monitoring-get-started/monitor-diaglogs-blade.png)

    [**Diagnosticeringslogfiler**](monitoring-overview-of-diagnostic-logs.md) er logfiler, der udledes *ved* en ressource, der leverer data om driften af den bestemte ressource. Netværk sikkerhed gruppe regel tællere og logik App arbejdsproces hændelseslogge er begge typer diagnosticeringslogfiler. Disse logfiler kan være gemt i en lagerplads-konto, streames til en begivenhed Hub og/eller sendes til [Log analyser](../log-analytics/log-analytics-overview.md). Log Analytics er Microsofts funktionsdygtige intelligence produkt til avanceret søgning og advarer.
   
    Du kan få vist og filtrere en liste over alle de ressourcer i dit abonnement til at identificere, hvis de har aktiveret logge til diagnosticering på portalen.

11. Klik på en ressource i bladet diagnosticeringslogfiler. Hvis diagnosticeringslogfiler gemmes i en lagerplads-konto, vises en liste over hver time logfiler, som du kan hente direkte.

    ![Diagnosticeringslogfiler for en ressource](./media/monitoring-get-started/monitor-diaglogs-detail.png)

    Du kan også klikke på **Indstillinger for diagnosticering**, som gør det muligt at angive eller ændre dine indstillinger for arkivering til en konto med lagerplads, streaming til begivenhed hubber eller sende til en Log Analytics-arbejdsområde.

    ![Aktivere diagnosticeringslogfiler](./media/monitoring-get-started/monitor-diaglogs-enable.png)

    Hvis du har konfigureret diagnosticeringslogfiler til Log Analytics, kan du søge dem i sektionen **søgning Log** på portalen.

12. Gå til afsnittet **beskeder** i bladet skærm.

    ![beskeder blade for offentlige](./media/monitoring-get-started/monitor-alerts-nopp.png)

    Her kan du administrere alle [**beskeder**](./monitoring-overview-alerts.md) på dine Azure ressourcer. Dette omfatter beskeder på målepunkter, aktivitet log begivenheder (i private preview), programmet indsigt web test (placeringer) og programmet indsigt proaktiv diagnosticering. Påmindelser kan udløse en mail skal sendes eller en HTTP-POST til en webhook URL-adresse.
   
13. Klik på **Tilføj metriske besked** for at oprette en besked.

    ![tilføje metriske besked](./media/monitoring-get-started/monitor-alerts-add.png)

    Derefter kan du fastgøre en besked til dit dashboard for at se tilstanden, når som helst.

14. Sektionen overvåge indeholder også links til [Programmet indsigt](../application-insights/app-insights-overview.md) programmer og [Log Analytics](../log-analytics/log-analytics-overview.md) management-løsninger. Disse andre Microsoft-produkter har tæt integration med Azure skærm.

15. Hvis du ikke bruger programmet indsigt eller Log Analytics, sandsynligvis, Azure skærm har et partnerskab med din aktuelle overvågning, logføring og advarsler produkter. Se vores [partnere side](./monitoring-partners.md) til en komplet liste og anvisninger til, hvordan du integrerer.

Ved at følge disse trin, og Fastgør alle relevante felter til et dashboard, kan du oprette omfattende visninger af programmet og infrastruktur som denne:

![Azure skærm dashboard](./media/monitoring-get-started/monitor-final-dash.png)

## <a name="next-steps"></a>Næste trin
- Læs [Oversigt over Azure skærm](./monitoring-overview.md)
