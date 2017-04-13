<properties
    pageTitle="Objektbeholdere løsning i Log Analytics | Microsoft Azure"
    description="Objektbeholdere løsningen i Log Analytics hjælper dig med at få vist og administrere din Docker objektbeholder hosts på ét sted."
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



# <a name="containers-preview-solution-log-analytics"></a>Beholdere (Preview) løsning Log Analytics

I denne artikel beskrives, hvordan du konfigurerer og bruger beholdere løsningen i Log Analytics, hvilket kan du få vist og administrere din Docker objektbeholder hosts på ét sted. Docker er et software virtualization system, der bruges til at oprette beholdere, automatiserer softwareinstallation til deres IT-infrastruktur.

Med løsningen, kan du se, hvilke objektbeholdere kører på din objektbeholder værter og hvad billeder kører i beholdere. Du kan få vist detaljerede overvågningslog oplysninger, der viser kommandoer, der bruges med beholdere. Og du kan foretage fejlfinding af objektbeholdere ved at få vist og søge centralt logfiler uden at skulle se fra en fjernplacering Docker hosts. Du kan finde beholdere, der kan være støj og arket overskydende ressourcer på en vært. Og du kan få vist centralt CPU, hukommelse, lager og oplysninger om netværk brug og ydeevnen af objektbeholdere.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen

Brug følgende oplysninger til at installere og konfigurere løsningen.

Tilføje beholdere løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).

Der er to måder at installere og bruge Docker med OMS:

- På Linux understøttede operativsystemer, installere og køre Docker og derefter installere og konfigurere OMS Agent til Linux
- På CoreOS, installere og køre Docker og derefter konfigurere OMSAgent til at køre i en objektbeholder

Gennemse de understøttede Docker og Linux-operativsystemversioner til din objektbeholder vært på [GitHub](https://github.com/Microsoft/OMS-docker).

>[AZURE.IMPORTANT] Docker skal køre **før** du installerer [OMS Agent for Linux](log-analytics-linux-agents.md) på din objektbeholder hosts. Hvis du allerede har installeret agenten før du installerer Docker, skal du geninstallere OMS Agent for Linux. Du kan finde flere oplysninger om Docker, [Docker websted](https://www.docker.com).

Du har brug for til følgende indstillinger, der er konfigureret på din objektbeholder hosts, før du kan overvåge beholdere.

## <a name="configure-settings-for-the-linux-container-host"></a>Konfigurere indstillinger for værten Linux objektbeholder

Når du har installeret Docker, kan du bruge følgende indstillinger for værten objektbeholder til at konfigurere agent til brug med Docker. CoreOS understøtter ikke denne konfigurationsmetode.

### <a name="to-configure-settings-for-the-container-host---systemd-suse-opensuse-centos-7x-rhel-7x-and-ubuntu-15x-and-higher"></a>Konfigurere indstillinger for værten objektbeholder - systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x og Ubuntu 15.x eller nyere)

1. Redigere docker.service for at tilføje følgende:

    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```

2. Tilføje $DOCKER\_VÆLGER i &quot;ExecStart = / usr/bin/docker daemon&quot; i docker.service filen. Brug af i følgende eksempel.

    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```

3. Genstart tjenesten Docker. Eksempel:

    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-ubuntu-14x"></a>Konfigurere indstillinger for værten objektbeholder - Upstart (Ubuntu 14.x)

1. Redigere /etc/default/docker og tilføje følgende:

    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Gem filen, og genstart derefter tjenesterne Docker og OMS.

    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Konfigurere indstillinger for værten objektbeholder - Amazon Linux

1. Redigere /etc/sysconfig/docker og tilføje følgende:

    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Gem filen, og genstart derefter tjenesten Docker.

    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Konfigurere indstillinger for CoreOS beholdere

Når du har installeret Docker, kan du bruge følgende indstillinger for CoreOS til at køre Docker og oprette en beholder. Du kan bruge en understøttet version af Linux – herunder CoreOS, med denne konfigurationsmetode. Du skal bruge [OMS arbejdsområde-ID og nøgle](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Bruge OMS for alle beholdere med CoreOS

- Start objektbeholderen OMS, du vil overvåge. Redigere og bruge i følgende eksempel.

  ```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Skifte fra ved hjælp af en installerede agent til en i en objektbeholder

Hvis du tidligere har brugt den direkte-installeret agent og vil i stedet bruge en agent, der kører i en objektbeholder, skal du først fjerne OMSAgent. Se [trinnene for at installere OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="containers-data-collection-details"></a>Objektbeholdere Datadetaljer af websteder

Løsningen beholdere indsamler forskellige ydelsesdata til målepunkter, og log fra objektbeholder værter og beholdere med OMS supportmedarbejdere til Linux, som du har aktiveret og fra OMSAgent kører i beholdere.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles af objektbeholdere.

| platform | OMS Agent for Linux | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Linux|![Ja](./media/log-analytics-containers/oms-bullet-green.png)|![Nej](./media/log-analytics-containers/oms-bullet-red.png)|![Nej](./media/log-analytics-containers/oms-bullet-red.png)|            ![Nej](./media/log-analytics-containers/oms-bullet-red.png)|![Nej](./media/log-analytics-containers/oms-bullet-red.png)| hvert 3.|


Den følgende tabel vises eksempler på datatyper, der indsamles via beholdere løsningen:

| Datatype | Felter |
| --- | --- |
| Ydeevnen for værter og beholdere | Computer, objektnavn, CounterName & #40; % processortid Disk læser MB, Disk skriver MB, hukommelse brugen MB, netværk Modtag byte, netværk sende byte, Processor brugen sec netværk & #41; CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Objektbeholder lager | TimeGenerated, Computer, objektbeholder navn, ContainerHostname, billede, ImageTag, ContinerState, ExitCode, EnvironmentVar, kommandoen, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Objektbeholder billede lager | TimeGenerated, Computer, billede, ImageTag, ImageSize, VirtualSize, kører, midlertidigt, ikke længere, mislykkedes, SourceSystem, ImageID, TotalContainer |
| Objektbeholder log | TimeGenerated Computer billed-ID, og beholder navn, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Loggen for objektbeholder tjenesten | TimeGenerated, Computer, TimeOfCommand, billede, kommandoen, SourceSystem, ContainerID |

## <a name="monitor-containers"></a>Overvåge objektbeholdere

Når du har aktiveret i portalen OMS løsningen, kan du se feltet **beholdere** , der viser oversigtsoplysninger om din objektbeholder værter og beholdere kører i hosts.

![Objektbeholdere felt](./media/log-analytics-containers/containers-title.png)

Feltet viser en oversigt over, hvor mange beholdere, du har i miljøet og om de er mislykkedes, kører eller er stoppet.

### <a name="using-the-containers-dashboard"></a>Ved hjælp af objektbeholdere dashboard

Klik på feltet **beholdere** . Derfra kan du se visninger, der er organiseret efter:

- Objektbeholder begivenheder
- Fejl
- Objektbeholdere Status
- Objektbeholder billede lager
- CPU-og hukommelse

Hver rude i dashboardet er en visuel repræsentation af en søgning, der udføres på indsamlede data.

![Objektbeholdere dashboard](./media/log-analytics-containers/containers-dash01.png)

![Objektbeholdere dashboard](./media/log-analytics-containers/containers-dash02.png)

Bladet **Objektbeholder Status** , klik på på øverst i området, som vist nedenfor.

![Objektbeholdere status](./media/log-analytics-containers/containers-status.png)

Log søgning åbnes og viser oplysninger om hosts og beholdere kører i dem.

![Log søgning af objektbeholdere](./media/log-analytics-containers/containers-log-search.png)

Her kan du redigere søgeforespørgsel for at ændre den for at finde bestemte kriterier, du er interesseret i. Du kan finde flere oplysninger om Log søgninger, [Log søgninger i Log Analytics](log-analytics-log-searches.md).

For eksempel kan du ændre forespørgslen søgning, så den viser alle holdt op med at beholdere i stedet for de, der kørende beholdere ved at ændre **kører** til **showet afbrydes** i søgestrengen.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Fejlfinding i forbindelse med ved at søge efter en mislykket objektbeholder

OMS markerer en objektbeholder som **mislykket** , hvis det er afsluttet med en forskellig fra nul afslutningskode. Du kan se en oversigt over fejl og fejl i miljø i bladet **Mislykkedes beholdere** .

### <a name="to-find-failed-containers"></a>Finde mislykkedes objektbeholdere

1. Klik på bladet **Objektbeholder begivenheder** .  
  ![objektbeholdere begivenheder](./media/log-analytics-containers/containers-events.png)
2. Log søgning åbnes og viser status for beholdere, som følger.  
  ![objektbeholdere tilstand](./media/log-analytics-containers/containers-container-state.png)
3. Klik derefter på værdien mislykkedes for at få vist yderligere oplysninger såsom billedstørrelse og antal holdt op med at og mislykkede billeder. Udvide **Vis flere** for at få vist billede-ID.  
  ![mislykkedes objektbeholdere](./media/log-analytics-containers/containers-state-failed.png)
4. Dernæst skal finde den objektbeholder, der kører dette billede. Skriv følgende i søgestrengen.
  `Type=ContainerInventory <ImageID>`Dette viser loggene. Du kan rulle for at få vist objektbeholderen mislykkedes.  
  ![mislykkedes objektbeholdere](./media/log-analytics-containers/containers-failed04.png)


## <a name="search-logs-for-container-data"></a>Søg logfiler til objektbeholder data

Når du fejlfinding af en bestemt fejl, kan det hjælpe for at se, hvor det bliver til virkelighed i dit miljø. Følgende logtyper hjælper dig med at oprette forespørgsler for at returnere oplysninger, du vil.

- **ContainerInventory** – Brug denne type, når du vil have oplysninger om objektbeholder placering, hvad deres navne er, og hvad billeder de kører.
- **ContainerImageInventory** – Brug denne type, når du forsøger at finde oplysninger organiseret efter billedet og til at få vist billedoplysninger som billede-id'er og størrelser.
- **ContainerLog** – Brug denne type, når du vil søge efter specifikke fejl logoplysninger og poster.
- **ContainerServiceLog** – Brug denne type, når du prøver at finde oplysninger om revisionsspor for Docker daemonen, som start, stop, Slet eller hente kommandoer.

### <a name="to-search-logs-for-container-data"></a>Sådan søger du logfiler til objektbeholder data

- Vælg et billede, du kender mislykkes senest og finde fejllogge for den. Starte med at finde objektbeholdernavnet på en, der kører billedet med en **ContainerInventory** søgning. Eksempelvis Søg efter`Type=ContainerInventory ubuntu Failed`  
    ![Søge efter Ubuntu objektbeholdere](./media/log-analytics-containers/search-ubuntu.png)

  Notér navnet på objektbeholderen ud for **navn**, og Søg efter disse logfiler. I dette eksempel er det `Type=ContainerLog adoring_meitner`.

**Få vist ydeevnen oplysninger**

Når du er begyndt at oprette forespørgsler, kan det hjælpe for at se, hvad der er muligt først. For eksempel for at få vist alle ydelsesdata, kan du prøve en bred forespørgsel ved at skrive følgende søgeforespørgsel.

```
Type=Perf
```

![objektbeholdere ydeevne](./media/log-analytics-containers/containers-perf01.png)



Du kan se dette i en formular, der er mere grafiske, når du klikker på ord **målepunkter** i resultaterne.

![objektbeholdere ydeevne](./media/log-analytics-containers/containers-perf02.png)



Du kan begrænse ydelsesdata du får vist til en bestemt objektbeholder ved at skrive navnet på den til højre for din forespørgsel.

```
Type=Perf <containerName>
```

Der viser listen over ydeevne målepunkter, der er indsamlet for en enkelt beholder.

![objektbeholdere ydeevne](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Eksempel log søgeforespørgsler

Det er ofte nyttigt at oprette forespørgsler starter med et eksempel eller to og derefter redigere dem, så de passer til dit miljø. Som udgangspunkt, kan du eksperimentere med bladet **Væsentlige forespørgsler** til at opbygge mere avancerede forespørgsler.

![Objektbeholdere forespørgsler](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Gemme log søgeforespørgsler

Gemme forespørgsler er en standardfunktion i Log Analytics. Ved at gemme dem, du har dem, du har fundet nyttige praktiske til senere brug.

Når du opretter en forespørgsel, som du kan finde nyttige, kan du gemme det ved at klikke på **Favoritter** øverst på siden Log søgning. Derefter kan du nemt åbne den senere fra siden **Mine Dashboard** .

## <a name="next-steps"></a>Næste trin

- [Logfiler over Søg](log-analytics-log-searches.md) til at få vist detaljerede objektbeholder dataposter.
