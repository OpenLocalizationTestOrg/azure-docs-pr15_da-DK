<properties
    pageTitle="Overvåge Surface hubber med Log Analytics | Microsoft Azure"
    description="Du kan bruge løsningen grundflade-Hub til at registrere tilstanden for din grundflade Hubs og forstå, hvordan de anvendes."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="monitor-surface-hubs-with-log-analytics"></a>Skærm Surface hubber med Log Analytics

I denne artikel beskrives, hvordan du kan bruge løsningen grundflade-Hub i Log Analytics til at overvåge Microsoft Surface Hub enheder med Microsoft Operations Management Suite (OMS). Log Analytics hjælper dig med at registrere tilstanden for din grundflade Hubs samt at forstå, hvordan de anvendes.

Hver grundflade-Hub har Microsoft overvågning Agent installeret. Dens gennem agent, kan du sende data fra din grundflade-Hub til OMS. Logfiler læses fra din grundflade Hubs, og som derefter sendes til OMS-tjenesten. Problemer, som er offline, servere den kalender, der ikke kan synkroniseres, eller hvis kontoen enhed ikke kan logge på Skype vises i OMS i dashboardet grundflade-Hub. Du kan identificere enheder, der ikke kører, eller, der er andre problemer, og anvende potentielt rettelser af de fundne problemer ved hjælp af dataene i dashboardet.


## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen

Brug følgende oplysninger til at installere og konfigurere løsningen. For at administrere din grundflade Hubs fra Microsoft handlinger Management pakke (OMS), skal du bruge følgende:

- Et gyldigt abonnement til [OMS](http://www.microsoft.com/oms).
- Niveauet [OMS abonnement](https://azure.microsoft.com/pricing/details/log-analytics/) , der understøtter antallet enheder, du vil overvåge. OMS priser varierer afhængigt af hvor mange enheder er registreret, og hvor meget data det processer. Du skal tage dette i betragtning ved planlægning af implementeringen grundflade-Hub.

Derefter skal vil du enten tilføje et OMS abonnement til dit eksisterende Microsoft Azure-abonnement eller oprette et nyt arbejdsområde direkte via portalen OMS. Detaljeret vejledning til brug af begge metoder er i [komme i gang med Log analyser](log-analytics-get-started.md). Når OMS abonnementet er konfigureret, er der to måder at tilmelde din grundflade-Hub-enheder:

- Automatisk via InTune
- Manuelt ved hjælp af **indstillingerne** på enheden grundflade-Hub.

## <a name="set-up-monitoring"></a>Konfigurere overvågning

Du kan overvåge systemtilstand og aktivitet på din grundflade-Hub, ved hjælp af Log Analytics i OMS. Du kan tilmelde grundflade Hub i OMS ved hjælp af InTune eller lokalt ved hjælp af **Indstillinger** på grundflade-hubben.

## <a name="connect-surface-hubs-to-oms-through-intune"></a>Oprette forbindelse Surface Hubs til OMS gennem InTune

Du skal arbejdsområde-ID og arbejdsområde nøgle til arbejdsområdet OMS, der kommer til at administrere din grundflade Hubs. Du kan få dem fra portalen OMS.

InTune er en Microsoft-produkter, der gør det muligt at administrere centralt de OMS konfigurationsindstillinger, der er anvendt på en eller flere af dine enheder. Følg disse trin for at konfigurere dine enheder gennem InTune:

1. Log på InTune.
2. Gå til **Indstillinger for** > **forbindelse kilder**.
3. Oprette eller redigere en politik, der er baseret på skabelonen grundflade-Hub.
4. Gå til sektionen OMS (Azure funktionsdygtige indsigt) på politikken, og tilføje *Arbejdsområde-ID* og *Arbejdsområde nøgle* til politikken.
5. Gem politikken.
6. Knytte politikken til den relevante gruppe af enheder.

  ![InTune politik](./media/log-analytics-surface-hubs/intune.png)

InTune synkroniserer derefter indstillingerne for OMS med enhederne i gruppen target tilmelding af dem i arbejdsområdet OMS.

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>Oprette forbindelse grundflade Hubs til OMS ved hjælp af appen indstillinger

Du skal arbejdsområde-ID og arbejdsområde nøgle til arbejdsområdet OMS, der kommer til at administrere din grundflade Hubs. Du kan få dem fra portalen OMS.

Hvis du ikke bruger InTune til at administrere dit miljø, kan du tilmelde dig enheder manuelt ved hjælp af **indstillingerne** under hver grundflade-Hub:

1. Fra din grundflade-Hub skal du åbne **Indstillinger**.
2. Angiv Administratoroplysninger enhed, når du bliver bedt om.
3. Klik på **denne enhed**, og klik på **Konfigurer OMS indstillinger**under **overvågnings**.
4. Vælg **Aktiver overvågning**.
6. Skriv **Arbejdsområde-ID** i dialogboksen OMS indstillinger, og skriv **Arbejdsområde nøgle**.  
  ![indstillinger](./media/log-analytics-surface-hubs/settings.png)
7. Klik på **OK** for at fuldføre konfigurationen.

Der vises en bekræftelse fortæller hvorvidt OMS konfigurationen blev anvendt på enheden. Hvis den er, vises en meddelelse om, at agenten er oprettet forbindelse til tjenesten OMS. Enheden starter derefter sender data til OMS, hvor du kan få vist og reagere på den.

## <a name="monitor-surface-hubs"></a>Overvåge Surface Hubs

Overvåge dine grundflade Hubs minder ved hjælp af OMS meget overvågning tilmeldte enheder.

1. Log på portalen OMS.
2. Gå til grundflade-Hub løsning pack dashboard.
3. Enhedens tilstand vises.

  ![Surface Hub dashboard](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

Du kan oprette [beskeder](log-analytics-alerts.md) baseret på eksisterende eller brugerdefineret log søgninger. Ved hjælp af OMS indsamler fra din grundflade Hubs dataene, kan du søge efter problemer og en besked for de betingelser, du definerer for dine enheder.


## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede data, grundflade-Hub.
- Opret [beskeder](log-analytics-alerts.md) for at give dig besked, hvis der opstår problemer med din grundflade Hubs.
