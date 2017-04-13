<properties
    pageTitle="Registrering af ændringer løsning i Log Analytics | Microsoft Azure"
    description="Du kan bruge løsningen registrering af konfiguration i Log Analytics så du nemt identificere software og Windows Services ændringer, der forekommer i dit miljø – identificere disse ændringer i konfigurationen kan hjælpe dig med at finde funktionsdygtige problemer."
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="change-tracking-solution-in-log-analytics"></a>Ændre registrering løsning i Log Analytics


Du kan bruge løsningen registrering af konfiguration i Log Analytics så du nemt identificere software og Windows Services ændringer og Linux daemon ændringer, der forekommer i dit miljø – identificere disse ændringer i konfigurationen kan hjælpe dig med at finde funktionsdygtige problemer.

Du har installeret løsning for at opdatere typen agent, som du har installeret. Ændringer i installeret software og Windows-tjenester på overvåget serverne læses og derefter data, der sendes til tjenesten Log Analytics i skyen for behandling. Logik anvendes på modtager data og skytjenesten registrerer dataene. Når ændringer der bliver fundet, vises servere med ændringer i dashboardet for registrering af ændringer. Ved hjælp af oplysningerne på dashboardet til registrering af ændringer, kan du nemt se de ændringer, der er foretaget i serverinfrastrukturen.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
Brug følgende oplysninger til at installere og konfigurere løsningen.

- Operations Manager er påkrævet for løsningen registrering af ændringer.
- Du skal have en agent for Windows eller Operations Manager på alle computere, hvor du vil overvåge ændringer.
- Føje løsningen registrering af ændringer til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.


## <a name="change-tracking-data-collection-details"></a>Ændre registrering Datadetaljer af websteder

Registrering af ændringer indsamler softwaren lager og Windows-tjenesten metadata ved hjælp af supportmedarbejdere, som du har aktiveret.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for registrering af ændringer.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Ja](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Ja](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Nej](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![Nej](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Ja](./media/log-analytics-change-tracking/oms-bullet-green.png)| hver time|

## <a name="use-change-tracking"></a>Brug registrering af ændringer

Når løsningen er installeret, kan du se en oversigt over ændringer til dine overvåget servere ved hjælp af feltet **Registrering af ændringer** på siden **Oversigt** i OMS.

![Billede af ændringsregistrering felt](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Du kan få vist ændringer i din infrastruktur og derefter gå til oplysninger om følgende kategorier:

- Skriv ændringer af konfiguration for software og Windows-tjenester
- Software ændres til programmer og opdateringer til individuelle servere
- Samlet antal softwareændringer for hvert program
- Windows-tjenesteændringer til individuelle servere

![Billede af ændringsregistrering dashboard](./media/log-analytics-change-tracking/oms-changetracking01.png)

![Billede af ændringsregistrering dashboard](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>For at få vist Skift ændringer på grund type

1. Klik på feltet **Registrering af ændringer** på siden **Oversigt** .
2. Gennemgå oversigtsoplysningerne i en af Skift type blade på dashboardet **Ændre sporing** , og klik derefter på én for at få vist detaljerede oplysninger om det på siden **log søgning** .
3. På en af siderne log søgning, du kan få vist resultater ved klokkeslæt, detaljerede resultater og en oversigt over dine log søgning. Du kan også filtrere efter facetter og indsnævre resultaterne.

## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede data for registrering.
