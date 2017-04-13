<properties
    pageTitle="Active Directory gentagelse Status løsning i Log Analytics | Microsoft Azure"
    description="Active Directory gentagelse Status løsning pack regelmæssigt overvåger din Active Directory-miljø til gentagelse fejl og oplyser om resultatet i dashboardet OMS."
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
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="active-directory-replication-status-solution-in-log-analytics"></a>Active Directory gentagelse Status løsning i Log Analytics

Active Directory er en vigtig komponent en virksomheds IT-miljø. For at sikre høj tilgængelighed og høj ydeevne, har hver domænenavn fra domænecontrolleren sin egen kopi af Active Directory-databasen. Domænecontrollere replikeres med hinanden for at overføre ændringer hele virksomheden. Fejl i denne gentagelse proces kan medføre en række problemer med hele virksomheden.

AD gentagelse Status løsning pakken regelmæssigt overvåger din Active Directory-miljø til gentagelse fejl og oplyser om resultatet i dashboardet OMS.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen
Brug følgende oplysninger til at installere og konfigurere løsningen.

- Supportmedarbejdere skal være installeret på domæne enheder, der er medlemmer af domænet skal evalueres eller på medlemsservere, der er konfigureret til at sende AD gentagelse data til OMS. For at forstå, hvordan du knytter Windows-computere til OMS, skal du se [forbinde Windows-computere at Log Analytics](log-analytics-windows-agents.md). Hvis domænecontrolleren allerede er en del af et eksisterende System Center Operations Manager-miljø, du vil oprette forbindelse til OMS, kan du se [Forbinde Operations Manager til Log analyser](log-analytics-om-agents.md).
- Føje Active Directory gentagelse Status løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  Der er ingen yderligere konfiguration, der er påkrævet.


## <a name="ad-replication-status-data-collection-details"></a>Detaljer om AD gentagelse Status data af websteder

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for AD gentagelse Status.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Windows|![Ja](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Ja](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Nej](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Nej](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Ja](./media/log-analytics-ad-replication-status/oms-bullet-green.png)| hver 5 dage|


## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Du kan også aktivere en ikke-domænecontrolleren for at sende AD data til OMS
Hvis du ikke vil tilslutte en af dine domænecontrollere direkte til OMS, kan du bruge en hvilken som helst anden OMS tilsluttet computer på dit domæne til at indsamle data til AD gentagelse Status løsning pack og har den sende dataene.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Aktivere en ikke-domænecontrolleren for at sende AD data til OMS
1.  Kontrollér, at computeren er medlem af det domæne, du vil overvåge ved hjælp af løsningen AD gentagelse Status.
2.  [Opret forbindelse Windows-computer til OMS](log-analytics-windows-agents.md) eller [forbinde den med dine eksisterende Operations Manager-miljø til OMS](log-analytics-om-agents.md), hvis den ikke allerede har forbindelse.
3.  På computeren, kan du angive følgende registreringsdatabasenøgle:
    - Nøgle: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management grupper\<ManagementGroupName > \Solutions\ADReplication**
    - Value: **IsTarge**
    - Værdidata: **Sand**

    >[AZURE.NOTE]Disse ændringer træder ikke i kraft indtil du genstarter tjenesten Microsoft overvågning Agent (HealthService.exe).

## <a name="understanding-replication-errors"></a>Forstå gentagelse fejl
Når du har AD gentagelse statusdata, der sendes til OMS, får du vist et felt, der ligner følgende på dashboardet OMS, der angiver, hvor mange gentagelse fejl, du har i øjeblikket.  
![AD gentagelse Status side om side](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Kritiske gentagelse fejl** er dem, der er på eller over 75% af [gravstedet](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) til Active Directory-område.

Når du klikker på feltet, skal du se flere oplysninger om fejlene.
![AD gentagelse Status dashboard](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)


### <a name="destination-server-status-and-source-server-status"></a>Destination serverstatus og kilde serverens Status
Disse blade viser status for destinationsservere og kilde-servere, der oplever gentagelse fejl. Nummeret efter hvert controller domænenavn angiver antallet af replikeringsfejl på pågældende domænenavn fra domænecontrolleren.

Fejlmeddelelser for både destinationsservere og kilde servere vises, fordi nogle af de problemer, der er nemmere at foretage fejlfinding af fra perspektivet kilde server og andre fra destination server perspektiv.

I dette eksempel kan du se, at mange destinationsservere har stort set det samme antal fejl, men der er en kildeserveren (ADDC35), der indeholder mange flere fejl end alle andre. Det er sandsynligt, der er et problem med ADDC35, der forårsager det ikke lykkes at sende data til dens gentagelse partnere. Løse problemerne på ADDC35 vil sandsynligvis løse mange af de fejl, der vises i bladet destination server.

### <a name="replication-error-types"></a>Typer af replikering fejl
Denne blade giver oplysninger om typerne fejl, der findes i hele virksomheden. De enkelte fejl har en entydig numerisk kode og en meddelelse, der kan hjælpe dig med at bestemme den egentlige årsag fejlen.

Krans øverst giver dig en ide på hvilke fejl vises mere og mindre hyppigt i dit miljø.

Her kan du se når flere domænecontrollere oplever fejlen samme gentagelse. I dette tilfælde skal du muligvis opleve identificere en løsning på et domænenavn fra domænecontrolleren, og derefter gentage den på andre domæne enheder, der påvirkes af den samme fejl.

### <a name="tombstone-lifetime"></a>Gravstedet
Gravstedet bestemmer, hvor lang tid et slettet objekt, kaldes en tombstone, bevares i Active Directory-databasen. Når et slettet objekt passerer gravstedet, fjernes den automatisk fra Active Directory-database i en spildopsamlingsprocessen.

Gravstedet standard er 180 dage for de nyeste versioner af Windows, men den var 60 dage i ældre versioner, og den kan ændres eksplicit af en Active Directory-administrator.

Det er vigtigt at vide, hvis du har gentagelse fejl, som nærmer eller har overskredet gravstedet. Hvis to domænecontrollere oplever fejlen gentagelse, fortsætter tidligere gravstedet, deaktiveres replikering mellem to domænecontrollerne, selvom underliggende gentagelse fejlen er rettet.

Bladet gravstedet hjælper dig med at identificere steder, hvor det er i risiko for sker. De enkelte fejl i den **Over 100% TSL** kategori repræsenterer en partition, der ikke har replikeres mellem dens kilde- og destinationstabellerne server til mindst gravstedet til området.

I så fald er blot løse fejlen replikering ikke nok. Som minimum skal du undersøge manuelt for at identificere og oprydning ventende objekterne, før du kan genstarte gentagelse. Du kan selv vil afvikle et domænenavn fra domænecontrolleren.

Ud over at identificere gentagelse fejl, som har bevaret tidligere gravstedet, får du også vil skal være opmærksom på eventuelle fejl, der falder i **50-75% TSL** eller **75-100% TSL** kategorier.

Dette er fejl, der er klart ventende, ikke forbigående, så de sandsynligt behøver installation i henhold til at løse. Det positive er, at de endnu ikke har nået gravstedet. Hvis du løse disse problemer straks og *før* de når gravstedet, gentagelse kan genstarte med minimale manuel handling.

Som nævnt tidligere viser feltet dashboard for løsningen AD gentagelse Status antallet af *kritiske* gentagelse fejl i dit miljø, der er defineret som fejl, der er over 75% af gravstedet (herunder fejl, der er over 100% af TSL). Bestræbe dig på at beholde dette nummer hos 0.

>[AZURE.NOTE] Alle tombstone levetid procentdel beregningerne er baseret på den faktiske gravstedet for din Active Directory-område, så du kan have tillid til, at disse procenter er nøjagtige, selvom du har en brugerdefineret tombstone levetid værdi, der er angivet.

### <a name="ad-replication-status-details"></a>AD gentagelse status detaljer
Når du klikker på et element på en af listerne, kan du se få mere at vide om det ved hjælp af Log søgning. Resultaterne er filtreret for at få vist kun de fejl, der er relateret til elementet. Eksempelvis hvis du klikker på den første domænenavn fra domænecontrolleren vises under **Destination serverens Status (ADDC02)**, får du vist søgeresultater, der er filtreret til Vis fejl med pågældende domænenavn fra domænecontrolleren er angivet som destinationsserver:

![AD gentagelse status fejl i søgeresultaterne](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Her kan du filtrere yderligere, ændre søgeforespørgslen og så videre. Du kan finde flere oplysninger om brug af Log søgningen, [Log søgninger](log-analytics-log-searches.md).

Feltet **HelpLink** viser URL-adressen for en TechNet side med yderligere oplysninger om denne specifikke fejl. Du kan kopiere og indsætte dette link i dit browservindue for at se oplysninger om fejlfinding og løse fejlen.

Du kan også klikke på **Eksporter** for at eksportere resultaterne til Excel. Dette giver dig mulighed at visualisere gentagelse Fejldata på nogen måde, du vil have.

![eksporterede AD gentagelse status fejl i Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD gentagelse Status ofte stillede spørgsmål
**Sp: hvor ofte er AD gentagelse statusdatoen opdateret?**
SV: oplysninger, der opdateres hver 5 dage.

**Sp: er der en metode til at konfigurere, hvor ofte disse data opdateres?**
SV: ikke på nuværende tidspunkt.

**Sp: har jeg brug at tilføje alle over mine enheder, domæne til mit OMS arbejdsområde for at kunne se gentagelse status?**
SV: Nej, kun en enkelt domænenavn fra domænecontrolleren skal tilføjes. Hvis du har flere domænecontrollere i arbejdsområdet OMS, sendes data fra dem alle til OMS.

**Sp: Jeg vil ikke tilføje et domæne enheder til min OMS arbejdsområde. Kan jeg stadig bruge løsningen AD gentagelse Status?**
SV: Ja. Du kan angive værdien af en registreringsdatabasenøgle for at aktivere denne. Se [aktivere et ikke - domænecontrolleren til at sende AD data til OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**Sp: Hvad er navnet på den proces, der indeholder data indsamles?**
SV: AdvisorAssessment.exe

**Sp: hvor lang tid tager det for data indsamles?**
SV: data samling tid afhænger af størrelsen af Active Directory-miljøet, men det tager normalt mindre end 15 minutter.

**Sp: Hvad type data, der indsamles?**
SV: gentagelse oplysninger der indsamles via LDAP.

**Sp: er der en metode til at konfigurere, når data indsamles?**
SV: ikke på nuværende tidspunkt.

**Sp: hvilke tilladelser har jeg brug for til at indsamle data?**
SV: normal brugertilladelser til Active Directory er normalt tilstrækkelig.

## <a name="troubleshoot-data-collection-problems"></a>Foretage fejlfinding af problemer med indsamling af data
For at indsamle data om kræver AD gentagelse Status løsning pakken mindst én domænenavn fra domænecontrolleren have forbindelse til arbejdsområdet OMS. Før du gør dette, får du vist en meddelelse om, der **indsamles data stadig**.

Hvis du har brug for hjælp, der forbinder en over dine enheder, domæne, kan du se dokumentationen på [forbinde Windows-computere at Log analyser](log-analytics-windows-agents.md). Du kan også hvis domænecontrolleren er allerede forbindelse til et eksisterende System Center Operations Manager-miljø, kan du se dokumentationen på [Forbinde System Center Operations Manager til Log Analytics](log-analytics-om-agents.md).

Hvis du ikke vil oprette forbindelse et af dine domænecontrollere direkte til OMS eller SCOM, kan du se [aktivere et ikke - domænecontrolleren til at sende AD data til OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).


## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger i Log Analytics](log-analytics-log-searches.md) til at få vist detaljerede Active Directory-replikering statusdatoen.
