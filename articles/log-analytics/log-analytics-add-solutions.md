<properties
    pageTitle="Tilføje Log Analytics løsninger fra løsningsgalleriet | Microsoft Azure"
    description="Log Analytics løsninger er en samling af logik, visualisering og dataindsamling regler, der giver målepunkter drejes rundt om et bestemt problemområde."
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

# <a name="add-log-analytics-solutions-from-the-solutions-gallery"></a>Tilføje Log Analytics løsninger fra løsningsgalleriet

Log Analytics løsninger er en samling af **logik**, **visualisering** og **data acquisition regler** , der leverer målepunkter drejes rundt om et bestemt problemområde. I denne artikel lister løsninger understøttes Log analyser og fortæller dig, hvordan du kan tilføje og fjerne dem ved hjælp af løsningsgalleriet.

Løsninger giver mulighed for større indsigt til:

- hjælpe med at undersøge og løse problemer med funktionsdygtige hurtigere
- indsamling og koordinere forskellige typer maskine data
- hjælper dig med at være proaktiv med aktiviteter som kapacitetsplanlægning, programrettelse rapportering og sikkerhedsovervågning.


>[AZURE.NOTE] Log Analytics omfatter Log søgefunktionalitet, så du ikke behøver at installere en løsning for at aktivere den. Dog kan du få datavisualiseringer, foreslåede søgninger og viden ved at tilføje løsninger fra løsningsgalleriet.

Når du har tilføjet en løsning, der indsamles fra servere i infrastrukturen dataene og sendes til OMS-tjenesten. Behandling af OMS tager tjenesten typisk et par minutter til en time. Når tjenesten behandler data, kan du få den vist i OMS.

Du kan nemt fjerne en løsning, når det er ikke længere er nødvendigt. Når du fjerner en løsning, sendes ikke dataene til OMS, hvilket kan reducere mængden af data, der bruges af dine daglige kvote, hvis du har en.


## <a name="solutions-supported-by-the-microsoft-monitoring-agent"></a>Løsninger, der understøttes af Microsoft overvågning Agent

På nuværende tidspunkt kan servere, der er forbundet til brug af Microsoft overvågning Agent OMS bruge de fleste af de løsninger, der er tilgængelige, herunder:

- Active Directory vurdering
- Beskeder om Management (uden SCOM beskeder)
- Antimalwareprogram
- Registrering af ændringer
- Sikkerhed
- SQL-vurdering
- Systemopdateringer

Følgende løsninger er dog *ikke* understøttes med Microsoft overvågning Agent og kræver en agent for System Center Operations Manager (SCOM).

- Beskeder om administration af (herunder SCOM beskeder)
- Kapacitet administration
- Konfiguration af vurdering

Se i [Forbindelse Operations Manager til Log Analytics](log-analytics-om-agents.md) for oplysninger om tilslutning SCOM agent til Log Analytics.

### <a name="to-add-a-solution-using-the-solutions-gallery"></a>Tilføje en løsning med løsningsgalleriet

1. Klik på feltet **Løsningsgalleriet** på siden Oversigt i OMS.    
    ![løsningsgalleriet](./media/log-analytics-add-solutions/sol-gallery.png)
2. Lær om hver tilgængelige løsning på siden OMS løsningsgalleriet. Klik på navnet på den løsning, du vil føje til OMS.
3. På siden for den løsning, du vælger, vises detaljerede oplysninger om løsningen. Klik på **Tilføj**.
4. Et nyt felt til den løsning, du har tilføjet vises på Oversigt i side i OMS, og du kan begynde at bruge den, når tjenesten OMS behandler dine data.

## <a name="to-configure-solutions"></a>Konfigurere løsninger
1. Du skal konfigurere nogle løsninger. For eksempel skal du konfigurere automatisering, Azure gendannelse af websteder og sikkerhedskopiering, før du kan bruge dem.
2. På grund af disse løsninger, skal du klikke på dens felt på siden Oversigt.  
    ![konfigurere løsning](./media/log-analytics-add-solutions/configure-additional.png)
3. Derefter konfigurere løsningen med de nødvendige oplysninger, og klik derefter på **Gem**.  
    ![konfigurere løsning](./media/log-analytics-add-solutions/configure.png)

### <a name="to-remove-a-solution-using-the-solutions-gallery"></a>Fjerne en løsning med løsningsgalleriet

1. Klik på feltet **Indstillinger** på siden Oversigt i OMS.
2. På siden Indstillinger under fanen løsninger, klik på **Fjern** for den løsning, du vil fjerne.
3. Klik på **Ja** for at fjerne løsningen i dialogboksen bekræftelse.

## <a name="data-collection-details-for-oms-features-and-solutions"></a>Detaljer om samling af data for OMS funktioner og løsninger

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for OMS funktioner og løsninger. Direkte supportmedarbejdere og SCOM supportmedarbejdere er grundlæggende det samme, men den direkte agent indeholder flere funktioner for at lade det til at oprette forbindelse til arbejdsområdet OMS og føres gennem en proxyserver. Hvis du bruger en agent for SCOM, skal den målrettes som OMS agent til at kommunikere med OMS. SCOM supportmedarbejdere i denne tabel er OMS supportmedarbejdere, der er forbundet til SCOM. Du kan finde oplysninger om at oprette forbindelse til din eksisterende SCOM-miljø til OMS i [Forbinde Operations Manager til Log Analytics](log-analytics-om-agents.md) .

>[AZURE.NOTE] Typen agent, som du bruger bestemmer, hvordan data sendes til OMS, med følgende betingelser:

- Du kan enten bruge direkte agent eller en vedhæftet SCOM OMS agent.
- Når SCOM er påkrævet, sendes SCOM agent data for løsningen altid til OMS ved hjælp af gruppen SCOM management. Når SCOM er påkrævet, bruges kun SCOM agent desuden af løsningen.
- Når SCOM er ikke nødvendigt, og tabellen viser, at SCOM agent data der sendes til OMS ved hjælp af gruppen management, derefter sendes SCOM agent data altid til OMS ved hjælp af administration af grupper. Direkte supportmedarbejdere springer gruppen administration og sende deres data direkte til OMS.
- Når SCOM agent data ikke sendes ved hjælp af en gruppe i administration, derefter data, der sendes direkte til OMS – springer gruppen administration.


|datatype| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|---|
|AD vurdering|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|  7 dage|
|AD gentagelse Status|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 dage|
|Beskeder (Nagios)|Linux|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|under modtagelse af|
|Beskeder (Zabbix)|Linux|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|1 minut|
|Beskeder (Operations Manager)|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|3 minutter|
|Antimalwareprogram|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| hver time|
|Kapacitet administration|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| hver time|
|Registrering af ændringer|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| hver time|
|Registrering af ændringer|Linux|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|hver time|
|Konfiguration af vurdering (ældre Advisor)|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| to gange om dagen|
|ETW|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutter|
|IIS-logfilerne|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutter|
|Vigtige Vaults|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutter|
|Netværk programmet Gateways|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutter|
|Netværk sikkerhedsgrupper|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|10 minutter|
|Office 365|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|meddelelse om|
|Tællere i ydeevne|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|som planlagt, mindst 10 sekunder|
|Tællere i ydeevne|Linux|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|som planlagt, mindst 10 sekunder|
|Tjenesten struktur|Windows|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|5 minutter|
|SQL-vurdering|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| 7 dage|
|SurfaceHub|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|under modtagelse af|
|Syslog|Linux|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|fra Azure-lager: ti minutter. fra en agent: på modtagelse|
|Systemopdateringer|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| mindst 2 gange i dag og 15 minutter, når du har installeret en opdatering|
|Windows sikkerhed hændelseslogfiler|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)| til Azure-lager: 10 min. til agenten: på modtagelse|
|Windows firewall logfiler|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)| under modtagelse af|
|Windows-hændelseslogge|Windows|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)| til Azure-lager: 1 min. til agenten: på modtagelse|
|Tråd Data|Windows (2012 R2 / 8.1 eller nyere)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Ja](./media/log-analytics-add-solutions/oms-bullet-green.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)|![Nej](./media/log-analytics-add-solutions/oms-bullet-red.png)| hver 1 minut|

## <a name="log-analytics-preview-solutions-and-features"></a>Log Analytics Preview løsninger og funktioner

Ved at køre en tjeneste og følge devops fremgangsmåder er vi kunne partner med kunder at udvikle funktioner og løsninger.

Under privat eksempelvisning får en lille gruppe kunder adgang til en tidlig implementering af funktion eller løsning for at få feedback og forbedre. Denne tidlig implementering har minimale funktioner og drift egenskaber.

Vores mål er at prøve ting hurtigt, så vi kan finde det, der fungerer, og hvad virker ikke. Vi navigere gennem denne proces, indtil feedback fra privat preview kunderne fortæller os, er vi klar til en offentlig preview.

Under Vis udskrift offentlige yder vi funktion eller løsning tilgængelig for alle brugere til at få flere feedback og validere vores skalering og effektivitet. I denne fase:

- Funktionerne Preview vises under fanen Indstillinger og kan aktiveres af en bruger
- Eksempel-løsninger, der kan tilføjes via galleriet eller ved hjælp af en publiceret script

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Hvad skal jeg vide om funktionerne Preview og løsninger?

Vi glæder om de nye funktioner og løsninger, og vi elsker arbejde med dig om at udvikle dem.

Preview funktioner og -løsninger, der ikke passer til alle dog så før bede om at deltage i en privat udskrift eller aktivere et offentlige eksempel Sørg for, at du arbejder med noget, som er under udvikling OK.

Når du aktiverer en preview funktion passerer gennem portalen, du kan se en advarsel, der minder dig om, at funktionen er i Vis udskrift.

#### <a name="for-both-private-and-public-preview"></a>Til både *private* og *offentlige* preview

Følgende gælder for både offentlige og private eksempler:

- Ting, du kan altid fungerer ikke korrekt.
  - Problemer med område i at blive en mindre irriterende gennem til noget overhovedet ikke på arbejde
- Der er risiko for eksemplet for at have en negativ indflydelse på dine systemer / miljø
  - Vi kan du prøve at undgå negative ting, sker der med systemerne, du bruger med OMS, men nogle gange uventede sker
- Tab af data / beskadigelse kan forekomme
- Kan derfor skal du til at indsamle diagnosticeringslogfiler eller andre data til hjælp til fejlfinding af problemer
- Funktionen eller løsning kan redigeres, fjernes (enten midlertidigt eller permanent)
  - Baseret på vores learnings under Vis udskrift vi beslutte at slippe ikke funktion eller løsning
- Eksempelvisning af fungerer muligvis ikke eller ikke har testet med alle konfigurationer, og vi kan begrænse:
  - De operativsystemer, der kan bruges (fx en funktion kan kun gælder for Linux under preview)
  - Typen agent (MMA, SCOM), der kan bruges (fx en funktion fungerer muligvis ikke med SCOM under preview)  
- Preview løsninger og funktioner er ikke omfattet af serviceaftale
- Brugen af funktionerne preview vil betale Brugsgebyrer
- Funktioner eller funktioner, du skal bruge til funktionen / løsning at være nyttige mangle eller være ufuldstændig
- Funktioner / løsninger, der er muligvis ikke tilgængelig i alle områder
- Funktioner / løsninger, der er muligvis ikke lokaliseret
- Funktioner / løsninger har muligvis en grænse på antallet kunder eller enheder, der kan bruge den
- Du skal muligvis bruge scripts til at udføre konfigurationen og til at aktivere løsning/funktion
- Brugergrænsefladen (UI) kan ikke fuldføres og kan ændres fra dag til dag
- Eksempelvisning af offentlige er muligvis ikke passer til dine fremstilling / kritiske systemer

#### <a name="for-private-preview"></a>Til *privat* preview

Ud over elementerne ovenfor er følgende specifikke for privat eksempelvisninger:

- Vi forventer, at du kan give os feedback om din oplevelse, så vi kan forbedre funktion/løsningen og
- Vi kan kontakte dig til feedback ved hjælp af undersøgelser, telefonopkald eller e-mail
- Ting, du fungerer ikke altid korrekt
- Vi kan kræve en ikke-tidligt aftalen om hemmeligholdelse for deres deltagelse eller kan omfatte fortroligt indhold
  - Før du blogge, tweeting eller på anden måde kommunikerer med tredjeparter skal du kontrollere med Program Manager ansvarlig for eksempel at forstå en hvilken som helst begrænsninger for tidligt
- Kører ikke på fremstilling / kritiske systemer


### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Hvordan får jeg adgang til private preview funktioner og løsninger?

Vi inviterer kunder til privat eksempelvisninger gennem flere forskellige måder, afhængigt af eksemplet.

- Deltage i månedlige kunde undersøgelsen og giver os tilladelse til at følge op på du øger dine chancer for at der inviteres til en privat preview.
- Din Microsoft account Manager kan udpege du.
- Du kan tilmelde dig baseret på oplysninger, der skrives på twitter [msopsmgmt](https://twitter.com/msopsmgmt)
- Du kan tilmelde dig på baggrund af oplysninger om delte community hændelser – Se for os møde ups, konferencer og i onlinegrupper.


## <a name="next-steps"></a>Næste trin

- [Logfiler over Søg](log-analytics-log-searches.md) til at få vist detaljerede oplysninger, der indsamles af løsninger.
