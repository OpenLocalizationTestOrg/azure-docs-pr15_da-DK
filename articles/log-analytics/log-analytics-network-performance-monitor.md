<properties
    pageTitle="Netværk Performance Monitor løsning i OMS | Microsoft Azure"
    description="Network Performance Monitor hjælper med at du overvåge ydeevnen for dit netværk på nær real-gang til registrere og finde netværk flaskehalse for performance."
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
    ms.date="07/28/2016"
    ms.author="banders"/>

# <a name="network-performance-monitor-preview-solution-in-oms"></a>Netværk Performance-skærm (Preview)-løsning i OMS

>[AZURE.NOTE] Dette er et [eksempel løsning](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Dette dokument beskrives, hvordan Network Performance Monitor løsningen i OMS, som hjælper med at du overvåge ydeevnen for dit netværk på nær real-gang til registrere og finde til konfiguration og brug-flaskehalse for performance. Du kan overvåge tab og ventetid mellem to netværk, undernet eller servere med løsningen Network Performance Monitor. Network Performance Monitor registrerer netværksproblemer som trafik blackholing, routing fejl og problemer, der traditionelt netværk overvågning metoder ikke er kunne registrere. Network Performance Monitor genererer beskeder og giver besked, når en grænseværdi er brudt for en netværksforbindelse. Disse tærskler kan være lært automatisk af systemet, eller du kan konfigurere dem til at anvende brugerdefinerede regler for påmindelser. Network Performance Monitor sikrer, at rette registrering af problemer med ydeevnen og localizes kilden til problemet til en bestemt netværk segmentet eller enhed.

Du kan registrere netværksproblemer, med dashboardet løsning, som viser opsummerede oplysninger om dit netværk, herunder seneste netværk sundhed begivenheder, beskadiget netværk links og undernetværk links, der modstående høj pakketab og ventetid. Du kan analysere ned i en netværksforbindelse til at få vist den aktuelle tilstand status for undernetværk links samt node-til-node links. Du kan også få vist historiske tendensen for tab og ventetid på netværket, undernetværk og node-til-node niveau. Du kan registrere forbigående netværksproblemer ved at få vist historiske tendens diagrammer for pakketab og ventetid og finde netværk flaskehalse på et topologi kort. Interaktive topologi grafen giver dig mulighed at visualisere hop ved hop netværk omdirigerer og finde frem til kilden til problemet. Du kan bruge Log Søg for at se forskellige analytics krav til at oprette brugerdefinerede rapporter, der er baseret på de data, der indsamles via Network Performance Monitor som alle andre løsninger.

Løsningen bruger korte transaktioner som en primær metode til at registrere netværk fejl. Tilfældet, kan du bruge det uden at tage hensyn til en bestemt netværksenhed leverandør eller model. Det fungerer på tværs af lokale, skyen (IaaS) og hybridmiljøer. Løsningen opdager automatisk netværkstopologien og forskellige omdirigerer i dit netværk.

Typisk netværk overvågning produkter fokuserer på overvåger netværk enhed (routere, parametre osv.)-tilstand, men giver ikke indsigt i faktisk kvaliteten af netværksforbindelsen mellem to punkter, hvilket betyder Network Performance Monitor.

### <a name="using-the-solution-standalone"></a>Brug af løsning enkeltstående

Hvis du vil overvåge kvaliteten af netværksforbindelser mellem deres kritiske arbejdsbelastninger, kan netværk, datacentre eller office websteder, og du bruge løsningen Network Performance Monitor alene til at overvåge connectivity sundhed mellem:

- flere datacentre eller office websteder, der er forbundet med en offentlig eller privat netværk
- kritiske arbejdsbelastninger, som kører line of business-applikationer
- offentlige skytjenester som Microsoft Azure eller Amazon Web Services (AWS) og lokale netværk, hvis du har IaaS (VM) tilgængelige, og du har gateways, der er konfigureret til at tillade kommunikation mellem lokale netværk og skyen netværk
- Azure og lokale netværk, når du bruger Express rute

### <a name="using-the-solution-with-other-networking-tools"></a>Brug af løsningen med andre netværk værktøjer

Hvis du vil overvåge en linje med forretningsprogram, kan du bruge løsningen Network Performance Monitor som en companion løsning på andre netværksværktøjer. Et langsomt netværk kan føre til langsom programmer og Network Performance Monitor kan hjælpe dig med at undersøge problemer med ydeevnen program, der er forårsaget af underliggende problemer. Da løsningen ikke kræver en hvilken som helst adgang til netværksenheder, skal administratoren af programmet ikke skal have et netværk team til at angive oplysninger om, hvordan netværket påvirker programmer.

Også, hvis du allerede investerer i andre netværk, overvågning værktøjer, derefter løsningen kan ændre disse værktøjer fordi mest traditionelle netværk overvågning løsninger ikke leverer indsigt i til slut netværk ydeevne målepunkter som tab og ventetid.  Network Performance Monitor løsningen kan hjælpe dig med at udfylde hul.


## <a name="installing-and-configuring-agents-for-the-solution"></a>Installation og konfiguration af supportmedarbejdere for løsningen

Bruge grundlæggende processer til at installere supportmedarbejdere på [forbinde Windows-computere at Log analyser](log-analytics-windows-agents.md) og [Oprette forbindelse Operations Manager til Log Analytics](log-analytics-om-agents.md).

>[AZURE.NOTE]
Du skal installere mindst 2 supportmedarbejdere og har nok data til at registrere og overvåge dine netværksressourcer. Ellers bliver løsningen i en konfiguration af tilstand, indtil du installerer og konfigurerer yderligere supportmedarbejdere.

### <a name="where-to-install-the-agents"></a>Her kan du installere supportmedarbejdere

Før du installerer supportmedarbejdere, skal du overveje topologien for netværket, og hvilke dele af netværket, du vil overvåge. Vi anbefaler, at du installerer mere end én agent for hvert undernet, du vil overvåge. Vælg to eller flere servere eller FOS med andre ord for hver undernet, du vil overvåge, og Installer agenten på dem.

Hvis du er sikker på, om topologien for dit netværk, kan du installere supportmedarbejdere på servere med kritiske arbejdsbelastninger, hvor du vil overvåge netværks ydeevne. Du kan for eksempel vil holde styr på et netværksforbindelse mellem en webserver og en server, der kører SQL Server. I dette eksempel skal du installere en agent på begge servere.

Supportmedarbejdere overvåge netværksforbindelsen (kæder) mellem hosts – ikke selve hosts. Så hvis du vil overvåge en netværksforbindelse, skal du installere supportmedarbejdere på begge slutpunkter for pågældende kæde.

### <a name="configure-agents"></a>Konfigurere supportmedarbejdere

Når du har installeret supportmedarbejdere, skal du åbne firewall-porte til disse computere at sikre, at supportmedarbejdere kan kommunikere. Du har brug at hente og derefter køre [EnableRules.ps1 PowerShell-script](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) uden parametre i en PowerShell-vinduet med administratorrettigheder

Scriptet opretter registreringsdatabasenøgler, der kræves, før Network Performance Monitor og opretter Windows firewallregler for at give supportmedarbejdere til at oprette TCP-forbindelser med hinanden. De registreringsdatabasenøgler, der er oprettet af scriptet også angive om du vil logge loggene fejlfinding og stien til filen logfiler. Den definerer også agent TCP-port bruges til kommunikation. Værdierne for disse taster angives automatisk ved hjælp af script, så du ikke bør manuelt ændre disse taster.

Den port, åbnes som standard er 8084. Du kan bruge en brugerdefineret port ved at indsende parameteren `portNumber` til scriptet. Dog skal den samme port anvendes på alle computere hvor scriptet køres.

>[AZURE.NOTE] Scriptet EnableRules.ps1 konfigurerer Windows firewallregler kun på den computer, hvor scriptet køres. Hvis du har en firewall på netværket, skal du sikre dig, at den giver mulighed for trafik til den TCP-port, der bruges af Network Performance Monitor.


## <a name="configuring-the-solution"></a>Konfiguration af løsningen

Brug følgende oplysninger til at installere og konfigurere løsningen.

1. Network Performance Monitor løsningen får data fra computere, der kører Windows Server 2008 SP 1 eller nyere eller Windows 7 SP1 eller nyere, som er de samme krav som Microsoft overvågning Agent (MMA).
2. Føje Network Performance Monitor løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).  
  ![Netværk Performance Monitor symbol](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. I portalen OMS, får du vist et nyt felt med titlen **Network Performance Monitor** med meddelelsen *løsning kræver yderligere konfiguration*. Du skal konfigurere løsning for at tilføje netværk baseret på subnetworks og noder, der er registreret af supportmedarbejdere. Klik på **Network Performance Monitor** for at starte konfiguration af standardnetværk.  
  ![løsning kræver yderligere konfiguration](./media/log-analytics-network-performance-monitor/npm-config.png)


### <a name="configure-the-solution-with-a-default-network"></a>Konfigurere løsningen med en standard-netværk

På konfigurationssiden af skal se du ét enkelt netværk med navnet **standard**. Når du ikke har angivet nogen netværk, placeres automatisk opdaget undernet i standardnetværk.

Når du opretter et netværk, du føjer et undernet til den og pågældende undernet er fjernet fra standardnetværk. Hvis du sletter et netværk, returneres alle undernet automatisk til standardnetværk.

Med andre ord er standardnetværk beholderen til alle de undernet, der ikke findes i en hvilken som helst brugerdefinerede netværk. Du kan ikke redigere eller slette standardnetværk. Altid forbliver den i systemet. Du kan dog oprette så mange netværk, som du vil have.

I de fleste tilfælde undernet i din organisation, er arrangeret i mere end ét netværk og skal du oprette en eller flere netværk for at gruppere logisk dit undernet.

### <a name="create-new-networks"></a>Oprette nye netværk

Et netværk i Network Performance Monitor er en beholder for undernet. Du kan oprette et netværk med en hvilken som helst navn, du vil, og Tilføj undernet til netværket. For eksempel kan du oprette et netværk med navnet *Building1* og derefter tilføjer undernet, eller du kan oprette et netværk med navnet *DMZ* og derefter tilføjer alle undernet tilhører perimeternetværk til dette netværk.

#### <a name="to-create-a-new-network"></a>Oprette et nyt netværk

1. Klik på **Tilføj netværk** , og skriv derefter netværksnavn og beskrivelse.
2.  Vælg en eller flere undernet, og klik derefter på **Tilføj**.
3. Klik på **Gem** for at gemme konfigurationen.  
  ![tilføje netværk](./media/log-analytics-network-performance-monitor/npm-add-network.png)



### <a name="wait-for-data-aggregation"></a>Vent datasammenlægning

Når du har gemt konfigurationen for første gang, starter løsningen indsamling af netværk pakke tab og ventetid oplysninger mellem noderne hvor supportmedarbejdere er installeret. Denne proces kan tage et stykke tid, nogle gange over 30 minutter. Under denne tilstand vises feltet Network Performance Monitor på oversigtssiden oversigt en meddelelse om *datasammenlægning i processen*.

![datasammenlægning i gang](./media/log-analytics-network-performance-monitor/npm-aggregation.png)


Når dataene er blevet overført, får du vist Network Performance Monitor felt opdateret viser data.

![Netværk Performance Monitor felt](./media/log-analytics-network-performance-monitor/npm-tile.png)

Klik på feltet for at få vist dashboardet Network Performance Monitor.

![Netværk Performance Monitor dashboard](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Redigere indstillinger for overvågning for undernet

Alle undernet, hvor mindst én agent blev installeret findes på fanen **Subnetworks** på konfigurationssiden.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Sådan aktiveres eller deaktiveres overvågning for bestemt subnetworks

1. Markér eller fjern markeringen i afkrydsningsfeltet ud for **undernetværk-ID** og derefter sikre, **bruges til overvågnings** er markeret eller ikke markeres, efter behov. Du kan markere eller fjerne flere undernet. Når deaktiveret, overvåges subnetworks ikke, når supportmedarbejdere opdateres for at stoppe pinge andre supportmedarbejdere.
2. Vælg de noder, du vil overvåge for et bestemt undernetværk ved at vælge undernetværk på listen og flytte de nødvendige noder mellem de lister, der indeholder ikke overvåges og overvåget noder.
Du kan føje en brugerdefineret **Beskrivelse** til undernetværk, hvis du ønsker.
3. Klik på **Gem** for at gemme konfigurationen.  
  ![redigere undernet](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Vælg noder til at overvåge

Alle de noder, der har en agent, der er installeret på dem, findes under fanen **noder** .

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Sådan aktiveres eller deaktiveres overvågning for noder

1. Markér eller fjern noderne, du vil overvåge eller stoppe med at overvåge.
2. Klik på **Brug til overvågnings**eller fjerne det, efter behov.
3. Klik på **Gem**.  
  ![aktivere node overvågning](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)


### <a name="set-monitoring-rules"></a>Konfigurere overvågning regler

Network Performance Monitor genererer sundhed begivenheder om forbindelsen mellem et par noder eller undernetværk eller netværket links, når en grænseværdi er brudt. Disse tærskler kan være lært automatisk af systemet, eller du kan konfigurere regler for brugerdefinerede påmindelser.

Den *regel som standard* er oprettet af systemet og opretter hændelsen tilstand, når tab eller ventetid mellem et par af netværk eller undernetværk links brud på grænseværdi for systemet har lært. Du kan vælge at deaktivere standardreglen og oprette brugerdefinerede overvågning regler

#### <a name="to-create-custom-monitoring-rules"></a>Du kan oprette brugerdefinerede overvågning regler

1. Klik på **Tilføj regel** under fanen **skærm** , og Angiv den regelnavn og beskrivelse.
2. Vælge sæt af netværks- eller undernetværk links til at overvåge fra listerne.
3. Vælg først det netværk, hvor den første undernetværk/s interesse findes på rullelisten i netværk, og vælg derefter undernetværk/s på tilsvarende undernetværk rullelisten.
Markér **alle subnetworks** , hvis du vil overvåge alle subnetworks i en netværksforbindelse. Vælg de andre undernetværk/s af interesse på samme måde. Og du kan klikke på **Tilføj undtagelse** for at udelade overvågning for bestemt undernetværk links fra det markerede, du har foretaget.
4. Hvis du ikke vil oprette sundhed begivenheder for de elementer, du har valgt, fjern derefter markeringen **aktivere sundhed overvågning på de links, der er dækket af denne regel**.
5. Vælg overvågning betingelser.
Du kan angive brugerdefinerede tærskler for generering af sundhed begivenhed ved at skrive tærskelværdi. Når værdien af betingelsen er over den markerede grænseværdi for den valgte netværk/undernetværk par, der oprettes en tilstand-hændelse.
6. Klik på **Gem** for at gemme konfigurationen.  
  ![oprette brugerdefineret overvågning regel](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)


## <a name="data-collection-details"></a>Detaljer om data af websteder

Network Performance Monitor bruger TCP SYN-SYNACK-ACK handshake pakker til at indsamle tab og ventetid oplysninger og traceroute bruges også til at få topologi oplysninger.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles for Network Performance Monitor.

| platform | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
| Windows |![Ja](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![Ja](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![Nej](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|            ![Nej](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|![Nej](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)| TCP handshakes hver 5 sekunder, data sendes hvert 3. |

Løsningen gør brug af korte transaktioner for at vurdere tilstanden for netværket. OMS supportmedarbejdere, der er installeret på forskellige sted i netværk exchange TCP-pakker til hinanden og i processen, lære returkørslen tid og pakke tabet, hvis en hvilken som helst. Hver agent udfører med jævne mellemrum, en sporingsrute til andre supportmedarbejdere til at finde alle de forskellige omdirigerer i det netværk, der skal kontrolleres. Brug af disse data, kan supportmedarbejdere at udlede netværksventetid og pakke tab figurer. Testene, der gentages hvert 5 og data aggregeres af aktører i en periode på tre minutter før du overfører til OMS.

>[AZURE.NOTE] Selvom supportmedarbejdere kommunikere ofte med hinanden, skal genererer de ikke en masse netværkstrafik under udførelse af testene. Supportmedarbejdere regne kun TCP SYN-SYNACK-ACK handshake pakker til beregning af tab og ventetid – ingen data, der er udvekslet pakker. Under denne proces supportmedarbejdere kommunikere med hinanden, når det er nødvendigt og agent kommunikation topologien er optimeret til at reducere netværkstrafik.

## <a name="using-the-solution"></a>Ved hjælp af løsningen

I dette afsnit forklares alle dashboardet funktioner, og hvordan du bruger dem.

### <a name="solution-overview-tile"></a>Løsning oversigt felt

Når du har aktiveret Network Performance Monitor løsningen, indeholder feltet løsning på siden Oversigt over OMS et hurtigt overblik over netværkstilstand. Det viser et kransediagram, der viser antallet af sund og beskadiget undernetværk links. Når du klikker på feltet, åbnes dashboardet løsning.

![Netværk Performance Monitor felt](./media/log-analytics-network-performance-monitor/npm-tile.png)


### <a name="network-performance-monitor-solution-dashboard"></a>Netværk Performance Monitor løsning dashboard

Bladet **Netværk oversigt** viser en oversigt over netværkene sammen med deres relative størrelse. Dette efterfølges af felter, der viser antallet af netværk links, undernet links og stier i systemet (en kurve består af to værter med supportmedarbejdere og alle hop mellem dem IP-adresser).

Bladet **Toppen netværk sundhed hændelser** indeholder en liste over den seneste tilstand begivenheder og beskeder i systemet og klokkeslættet, siden begivenheden har været aktiv. En tilstand begivenhed eller en besked genereres, når den pakketab eller forsinkelse på et netværk eller undernetværk link overstiger en grænseværdi.

Bladet **Toppen beskadiget netværk Links** viser en liste over beskadiget netværk links. Disse er de links, netværk, der har en eller flere uønskede sundhed begivenhed på dem på nuværende tidspunkt.

**Toppen undernetværk Links med mest tab** og **Undernetværk Links med mest ventetid** blade viser de øverste undernetværk ved pakketab og øverste undernetværk hyperlinks af ventetid henholdsvis. Lang ventetid eller nogle mængden af pakketab kan forventes på links til bestemte netværk. Disse links vises i de øverste ti lister, men det er ikke markeret beskadiget.

Bladet **Almindelige forespørgsler** indeholder et sæt af søgeforespørgsler, hente rå netværk overvågningsdata direkte. Du kan bruge disse forespørgsler som udgangspunkt for at oprette dine egne forespørgsler til tilpasset rapportering.

![Netværk Performance Monitor dashboard](./media/log-analytics-network-performance-monitor/npm-dash01.png)


### <a name="drill-down-for-depth"></a>Analysere ned til dybde

Du kan klikke på forskellige links på løsning dashboardet bruges til at analysere ned dybere i en hvilken som helst interesseområde. Når du ser en besked eller et link af typen beskadiget netværk vises på dashboardet, kan du eksempelvis klikke den for at undersøge yderligere. Du kan blive ført til en side, der viser alle undernetværk links til linket bestemt netværk. Du vil kunne se tab, forsinkelse og sundhed status for hvert undernetværk link og hurtigt finde ud af, hvilke undernetværk links er skyld i problemet. Du kan derefter klikke på **Vis node links** for at få vist alle node links til linket beskadiget undernet. Du kan derefter i individuelle node-til-node links og finde linkene beskadiget node.

Du kan klikke på **Vis topologi** for at få vist hop ved hop topologien for omdirigerer mellem noderne kilde- og destinationstabellerne. Beskadiget omdirigerer eller hop vises med rødt, så du hurtigt kan identificere problemet til en bestemt del af netværket.

![analysere ned data](./media/log-analytics-network-performance-monitor/npm-drill.png)


#### <a name="trend-charts"></a>Tendens diagrammer

På hvert niveau, du analysere ned, kan du se tendensen for tab og ventetid for en netværksforbindelse. Tendens diagrammer er også tilgængelig for undernetværk og Node links. Du kan ændre tidsintervallet for grafen afbilde ved hjælp af kontrolelementet tid øverst i diagrammet.

Tendens søjlediagrammer viser et historisk perspektiv af ydeevnen for en netværksforbindelse. Nogle netværksproblemer er forbigående karakter og vil være svært at fange kun ved at kigge på den aktuelle status for netværket. Dette skyldes, at problemer kan overfladen hurtigt og forsvinder, før alle meddelelser, kun for at igen på et senere tidspunkt. Sådan midlertidige problemer kan også være svært for administratorer, fordi dem, der er problemer ofte grundflade som Uforklarligt øges i programmet svartid, selv når alle programkomponenter ser ud til at køre hurtigere.

Du kan nemt registrere disse typer problemer ved at se på en tendensdiagram, hvor problemet vises som en pludselig samling i netværksventetid eller pakketab.

![tendensdiagram](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Hop ved hop topologi kort

Network Performance Monitor viser hop ved hop topologien for omdirigerer mellem to noder på en interaktiv topologi tilknytning. Du kan få vist topologikortet ved at vælge en node dette link og derefter klikke på **Vis topologi**. Du kan også få vist topologikortet ved at klikke på **stier** felt på dashboardet til. Når du klikker på **stier** på dashboardet til, får du skal vælge noderne kilde- og destinationstabellerne i panelet venstre, og klik derefter på **afbildning** , hvis du vil afbilde omdirigerer mellem de to noder.

Topologi kort viser, hvor mange omdirigerer er mellem de to noder og hvad stier data pakkerne tage. Netværk ydeevne flaskehalse er markeret med rødt på for topologikortet. Du kan finde en defekt netværksforbindelse eller en defekt enhed ved at kigge på røde farvede elementer på for topologikortet.

Når du klikker på en node eller holde markøren over det på topologikortet, får du vist egenskaberne for noden som fulde Domænenavn og IP-adressen. Klik på en hop for at få vist det er IP-adresse. Du kan fremhæve bestemt omdirigerer ved at fjerne og derefter vælge kun de omdirigerer, du vil fremhæve på kortet. Du kan zoome ind eller ud på topologikortet ved hjælp af musehjul.

Bemærk, at topologien vises i kortet er layer 3 topologi og ikke indeholder layer 2 enheder og forbindelser.

![hop ved hop topologi kort](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Fejl lokalisering

Network Performance Monitor er i stand til at finde netværk flaskehalse uden at oprette forbindelse til netværksenheder. Baseret på de data, den samler fra netværket og ved at anvende avancerede algoritmer på netværk graf, gør Network Performance Monitor en probabilistic estimat over de dele af netværk, der er mest sandsynlige kilden til problemet.

Denne metode er praktisk til at finde ud af netværk flaskehalse, når adgang til hop ikke tilgængelig, fordi det ikke kræver data, der skal indsamles fra netværksenheder f.eks parametre. Det er også nyttig, når hop mellem to noder, der ikke er i din administrativ kontrol. Hop kan for eksempel være Internetudbyder routere.

### <a name="log-analytics-search"></a>Log Analytics søgning

Alle data, som er tilgængelig grafisk gennem dashboardet Network Performance Monitor og analysere ned sider findes også indbygget i Log Analytics Søg. Du kan søge i dataene ved hjælp af søgning forespørgselssprog og oprette brugerdefinerede rapporter ved at eksportere dataene til Excel eller PowerBI. Bladet **Almindelige forespørgsler** i dashboardet har nogle nyttige forespørgsler, som du kan bruge som udgangspunkt for at oprette dine egne forespørgsler og rapporter.

![søgeforespørgsler](./media/log-analytics-network-performance-monitor/npm-queries.png)


## <a name="investigate-the-root-cause-of-a-health-alert"></a>Undersøge den egentlige årsag en tilstand-besked

Nu hvor du har læst om Network Performance Monitor, Lad os se på en enkelt undersøgelsen og den egentlige årsag for en tilstand.

1. På siden Oversigt, får du vist en hurtig snapshot af tilstanden for dit netværk på grundlag **Network Performance Monitor** flisen. Bemærk, at af 80 subnetworks kæderne overvåges 43 er beskadiget. Dette bør føre til undersøgelsen. Klik på feltet for at få vist dashboardet løsning.  
  ![Netværk Performance Monitor felt](./media/log-analytics-network-performance-monitor/npm-investigation01.png)

2. I nedenstående eksempel billedet, vil du bemærke, at der er i øjeblikket 4 sundhed begivenheder og 4 netværk links, som er beskadiget. Du beslutter at undersøge problemet og klikke på linket **Sharepoint-Web** netværk til at finde ud af, i roden af problemet.  
  ![Eksempel på beskadiget netværk link](./media/log-analytics-network-performance-monitor/npm-investigation02.png)

3. Analysere ned side vises alle undernetværk hyperlinks på **Sharepoint-Web** netværksforbindelse. Du vil bemærke, at for begge disse undernetværk hyperlinks, har ventetid, krydset i grænseværdi for at foretage netværksforbindelsen beskadiget. Du kan også se ventetid tendenser begge undernetværk kæder. Du kan bruge valget af tidszone kontrolelement i diagrammet til at fokusere på det nødvendige tidsinterval. Du kan se tidspunktet for den dag, når ventetid er nået frem til dens spidsbelastning. Senere kan du søge logfilerne for denne periode for at undersøge problemet. Klik på **Vis node links** til at analysere yderligere ned.  
  ![Eksempel på beskadiget undernet links](./media/log-analytics-network-performance-monitor/npm-investigation03.png)

4.  Svarer til den forrige side, siden analysere ned til linket bestemt undernetværk viser ned enkelte node links. Du kan udføre lignende handlinger her, som du gjorde i det forrige trin. Klik på **Vis topologi** for at få vist topologien mellem noderne 2.  
  ![Eksempel på beskadiget node links](./media/log-analytics-network-performance-monitor/npm-investigation04.png)

5. Alle stier mellem de 2 valgte noder afbildes i for topologikortet. Du kan visualisere hop ved hop topologien for omdirigerer mellem to noder på for topologikortet. Du får et tydeligt billede af, hvor mange omdirigerer findes mellem de to noder og hvad stier data pakkerne tager. Netværk ydeevne flaskehalse er markeret i rød farve. Du kan finde en defekt netværksforbindelse eller en defekt enhed ved at kigge på røde farvede elementer på for topologikortet.  
  ![Eksempel på visning af beskadiget topologi](./media/log-analytics-network-performance-monitor/npm-investigation05.png)

6. Du kan gennemse tab, forsinkelse og antallet hop i hver sti i ruden **Sti detaljer** . I dette eksempel kan du se, at der er 3 beskadiget stier, som nævnt i ruden. Bruge rullepanelet til at få vist oplysninger om disse beskadiget stier.  Brug afkrydsningsfelterne til at vælge en af stierne, så topologi for kun én sti afbildes. Du kan bruge din hjulknappen for at zoome ind eller ud på for topologikortet.

  I den under billedet kan du tydeligt se den egentlige årsag af problemområder på bestemt sektion i netværket ved at kigge på kurver og hop i rød farve. At klikke på en node i topologikortet vises egenskaberne for noden, herunder det fulde Domænenavn og IP-adresse. At klikke på en hop Viser IP-adressen på hop.  
  ![beskadiget topologi - sti detaljer eksempel](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>Næste trin

- [Logfiler over Søg](log-analytics-log-searches.md) til at få vist detaljerede netværk ydeevne dataposter.
