<properties
    pageTitle="Overvågning af VMware løsning i Log Analytics | Microsoft Azure"
    description="Få mere at vide om, hvordan løsningen VMware overvågning kan hjælpe administrere logfiler og overvåge ESXi hosts."
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
    ms.date="10/28/2016"
    ms.author="banders"/>

# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>VMware overvågning (Preview)-løsning i Log Analytics

Løsningen VMware overvågning i Log Analytics er en løsning, der hjælper dig med at oprette et centralt logføring og overvågning tilgang til den store VMware logfiler. I denne artikel beskrives, hvordan du kan foretage fejlfinding af, registrere og administrere ESXi værter på ved hjælp af løsningen på ét sted. Løsning, kan du se detaljerede data for alle dine ESXi værter på ét sted. Du kan se øverste begivenhed tæller, status og tendenser over VM og ESXi værter via loggene ESXi host. Du kan foretage fejlfinding af ved at få vist og søge centralt ESXi host logfiler. Og du kan oprette beskeder, der er baseret på log søgeforespørgsler.

Løsningen bruger oprindelige syslog funktionaliteten i værten ESXi til opslagsnål data til en destination VM, som har OMS Agent. Men løsningen ikke skrive filer til syslog i destinationen VM. OMS agent åbner port 1514 og lytter til dette. Når den modtager dataene, flytter OMS agent dataene til OMS.

## <a name="installing-and-configuring-the-solution"></a>Installation og konfiguration af løsningen

Brug følgende oplysninger til at installere og konfigurere løsningen.

- Føje VMware overvågning løsningen til arbejdsområdet OMS ved hjælp af processen beskrevet i [tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Understøttede VMware ESXi hosts
vSphere ESXi Host 5.5 og 6.0

#### <a name="prepare-a-linux-server"></a>Forberede en Linux-server
Oprette et Linux-operativsystem VM at modtage alle syslog data fra ESXi hosts. [OMS Linux Agent](log-analytics-linux-agents.md) er af websteder for alle ESXi host syslog data. Du kan bruge flere ESXi værter videresende logfiler til en enkelt Linux-server, som i følgende eksempel.  

   ![Syslog flow](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Konfigurere syslog af websteder

1. Konfigurere videresendelse af syslog for VSphere. Finde detaljerede oplysninger til at konfigurere viderestilling af syslog [konfigurationen syslog på ESXi 5.x og 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Gå til **ESXi Værtskonfigurationen** > **Software** > **Avancerede indstillinger for** > **Syslog**.
  ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  

2. Tilføj din Linux-server og port tal *1514*i feltet *Syslog.global.logHost* . For eksempel `tcp://hostname:1514` eller`tcp://123.456.789.101:1514`

3. Åbn ESXi host firewallen for syslog. **ESXi Værtskonfigurationen** > **Software** > **Sikkerhedsprofil** > **Firewall** , og Åbn **Egenskaber**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  

4. Se vSphere Console for at bekræfte, at syslog er konfigureret korrekt. Bekræfte på værten ESXI pågældende port **1514** er konfigureret.

5. Teste forbindelsen mellem Linux-server og værten ESXi ved hjælp af den `nc` kommando på værten ESXi. Eksempel:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

6. Hent og Installer OMS Agent for Linux på Linux-serveren. Se [dokumentationen til OMS Agent til Linux](https://github.com/Microsoft/OMS-Agent-for-Linux)kan finde flere oplysninger.

7. Når OMS Agent for Linux er installeret, gå til mappen /etc/opt/microsoft/omsagent/sysconf/omsagent.d og Kopier filen vmware_esxi.conf til mappen /etc/opt/microsoft/omsagent/conf/omsagent.d og ændring af ejer/gruppen og tilladelser i filen. Eksempel:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```

8.  Genstarte OMS Agent for Linux ved at køre `sudo /opt/microsoft/omsagent/bin/service_control restart`.

9. På portalen OMS udfører en log søgning efter `Type=VMware_CL`. Når OMS indsamler syslog dataene, bevarer den formatet syslog. I portalen hentes der nogle bestemte felter, som *Hostname* og *ProcessName*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Hvis Vis log søgeresultaterne ligner billedet ovenfor, er du indstillet til brug dashboardet OMS VMware overvågning løsning.  

## <a name="vmware-data-collection-details"></a>VMware Datadetaljer af websteder

Løsningen VMware overvågning indsamler forskellige ydelsesdata til målepunkter, og log fra ESXi hosts med OMS supportmedarbejdere til Linux, som du har aktiveret.

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles.

| platform | OMS Agent for Linux | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Linux|![Ja](./media/log-analytics-vmware/oms-bullet-green.png)|![Nej](./media/log-analytics-vmware/oms-bullet-red.png)|![Nej](./media/log-analytics-vmware/oms-bullet-red.png)|            ![Nej](./media/log-analytics-containers/oms-bullet-red.png)|![Nej](./media/log-analytics-vmware/oms-bullet-red.png)| hvert 3.|


Den følgende tabel vises eksempler på datafelter, der indsamles via løsningen VMware overvågning:

| feltnavn | Beskrivelse |
| --- | --- |
| Device_s| VMware lagerenheder |
| ESXIFailure_s | typer af fejl |
| EventTime_t | tid, når hændelsen indtraf |
| HostName_s | ESXi værtsnavn |
| Operation_s | oprette VM eller slette VM |
| ProcessName_s | navn på denne begivenhed |
| ResourceId_s | navnet på VMware værten |
| ResourceLocation_s | VMware |
| ResourceName_s | VMware |
| ResourceType_s | Hyper-V |
| SCSIStatus_s | VMware SCSI-status |
| SyslogMessage_s | Syslog data |
| UserName_s | bruger, der oprettes eller slettes VM |
| VMName_s | VM navn |
| Computer | Host computer |
| TimeGenerated | dataene blev oprettet kl. |
| DataCenter_s | VMware datacenter |
| StorageLatency_s | lagerplads ventetid (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Overvågning af VMware løsning oversigt

Feltet VMware vises i portalen OMS. Den indeholder en overordnet oversigt over eventuelle fejl. Når du klikker på feltet, går du en dashboardvisning.

![side om side](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigere i visningen dashboard

I visningen **VMware** dashboard er blade organiseret efter:

- Antal fejl Status
- Tæller øverste Host efter begivenhed
- Øverste begivenhed tæller
- Virtuelt aktiviteter
- ESXi Host disken begivenheder


![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Klik på en hvilken som helst blade for at åbne Log Analytics søgeruden, der viser detaljerede oplysninger, der er specifikke for bladet.

Her kan du redigere søgeforespørgsel for at ændre den til noget bestemt. Selvstudium til de grundlæggende regler for OMS søgning, se den [OMS log Søg selvstudium.](log-analytics-log-searches.md)

#### <a name="find-esxi-host-events"></a>Finde ESXi host begivenheder

En enkelt ESXi vært genererer flere logfiler, baseret på deres processer. Løsningen VMware overvågning centraliserer dem og indeholder en oversigt over antallet af begivenhed. Denne centralt visning hjælper dig med at forstå, hvilke ESXi host har en stor mængde hændelser, og hvilke hændelser indtræffer oftest bruger i dit miljø.

![begivenhed](./media/log-analytics-vmware/events.png)

Du kan analysere yderligere ved at klikke på en ESXi vært eller en hændelsestype.

Når du klikker på en ESXi værtsnavn, kan du få vist oplysninger fra ESXi værten. Hvis du vil indsnævre resultaterne med hændelsestypen, skal du tilføje `“ProcessName_s=EVENT TYPE”` i søgestrengen. Du kan vælge **ProcessName** i søgefilteret. Indsnævres oplysninger for dig.

![større detaljeringsgrad](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Finde høj VM aktiviteter

En virtuel maskine kan oprettede og slettede på en hvilken som helst ESXi vært. Det er praktisk for en administrator til at identificere, hvor mange FOS opretter en ESXi vært. I-slå, hjælper med at forstå ydeevne og kapacitetsplanlægning. Holde styr på VM aktivitet hændelser er afgørende, når du administrerer dit miljø.

![større detaljeringsgrad](./media/log-analytics-vmware/vmactivities1.png)

Hvis du vil se yderligere ESXi host VM oprettelse af data, skal du klikke på en ESXi værtsnavn.

![større detaljeringsgrad](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Almindelige søgeforespørgsler

Løsningen omfatter andre nyttige forespørgsler, der kan hjælpe dig med at administrere din ESXi værter, som høj lagerplads, lagerplads ventetid og stien fejl.

![forespørgsler](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Gemme forespørgsler

Gemme søgeforespørgsler er en standardfunktion i OMS og kan hjælpe dig med at holde alle forespørgsler, som du har fundet nyttige. Når du opretter en forespørgsel, som du kan finde nyttige, kan du gemme det ved at klikke på **Favoritter**. En gemt forespørgsel kan du nemt genbruge den senere fra siden [Mine Dashboard](log-analytics-dashboards.md) , hvor du kan oprette dine egne brugerdefinerede dashboards.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Opret beskeder fra forespørgsler

Når du har oprettet dine forespørgsler, vil du muligvis bruge forespørgsler til giver dig besked, når bestemte hændelser indtræffer. Se [beskeder i Log Analytics](log-analytics-alerts.md) oplysninger om, hvordan du opretter påmindelser. Eksempler på advarer forespørgsler og andre forespørgsel eksempler, kan du se [overvåge VMware ved hjælp af OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogindlægget.

## <a name="frequently-asked-questions"></a>Ofte stillede spørgsmål

### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Hvad skal jeg gøre på ESXi hoste indstilling? Hvilken virkning skal der være på mit nuværende miljø?
Løsningen bruger den oprindelige ESXi Host Syslog viderestilling af funktion. Du behøver ikke yderligere Microsoft software på værten ESXi til at registrere loggene. Det skal have en lav betydning for dit eksisterende miljø. Men du har brug for at konfigurere syslog videresendelse af, hvilket er ESXI funktionalitet.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Har jeg brug at genstarte vært for mit ESXi?
Nej. Denne proces kræver ikke en genstart. Nogle gange opdaterer vSphere korrekt ikke syslog. Log på til værten for ESXi i så fald og Genindlæs syslog. Igen skal behøver du ikke at genstarte værten, så denne proces ikke forstyrrende i dit miljø.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-oms"></a>Kan jeg øge eller mindske lydstyrken data sendes til OMS i?
Ja, kan du. Du kan bruge indstillingerne for ESXi Host logføringsniveau i vSphere. Log af websteder er baseret på niveauet for *oplysninger* . Så, hvis du vil overvåge VM oprettelse eller sletning, skal du holde niveauet *oplysninger* på Hostd. Du kan finde flere oplysninger [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-oms-my-log-setting-is-set-to-info"></a>Hvorfor Hostd leverer ikke data til OMS? Min log indstilling er angivet til oplysninger.
Der opstod en ESXi host fejl for syslog tidsstemplet. Du kan finde flere oplysninger [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Når du anvender løsningen, skal Hostd fungere normalt.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan jeg have flere ESXi værter videresendelse syslog data til en enkelt VM med omsagent?
Ja. Du kan have flere ESXi værter videresendelse til en enkelt VM med omsagent.

### <a name="why-dont-i-see-data-flowing-into-oms"></a>Hvorfor kan jeg ikke se data, der flyder i OMS?

Der kan være flere årsager:

- Værten ESXi skubber ikke korrekt data til VM kører omsagent. Hvis du vil teste, skal du udføre følgende trin:
    1. For at bekræfte, at logge på ved hjælp af ssh ESXi værten og køre følgende kommando:`nc -z ipaddressofVM 1514`

        Hvis dette ikke lykkes, vSphere indstillinger i sektionen Avanceret konfiguration har sandsynligvis ikke rette. Se [konfigurere syslog samling](#configure-syslog-collection) oplysninger om, hvordan du konfigurerer ESXi værten for syslog viderestilling.

    2. Hvis syslog port connectivity er gået igennem, men du stadig ikke kan se alle data, derefter Genindlæs syslog på værten ESXi ved hjælp af ssh til at køre følgende kommando:` esxcli system syslog reload`

- VM med OMS Agent er ikke angivet korrekt. Hvis du vil teste ved at udføre følgende trin:
    1. OMS lytter til port 1514 og sender data til OMS. For at bekræfte, at den er åben, skal du køre følgende kommando:`netstat -a | grep 1514`
    2. Skal du se afsnittet port `1514/tcp` åbne. Hvis du ikke gør det, du bekræfte, at omsagent er installeret korrekt. Hvis du ikke kan se portoplysningerne om, og derefter syslog porten ikke er åben på VM.
        1. Kontrollér, at OMS Agent kører ved hjælp af `ps -ef | grep oms`. Hvis det ikke kører, kan du starte processen ved at køre kommandoen` sudo /opt/microsoft/omsagent/bin/service_control start`
        2. Åbn den `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fil.

            Kontrollere, at det stort brugeren og gruppen indstilling er gyldige, svarende til:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

            Hvis filen ikke findes, eller det brugeren og gruppen indstilling er forkert, løs problemet ved at [forberede en Linux-server](#prepare-a-linux-server).

## <a name="next-steps"></a>Næste trin

- Brug [Log søgninger](log-analytics-log-searches.md) i Log Analytics til at få vist detaljerede VMware host data.
- [Oprette dine egne dashboards](log-analytics-dashboards.md) med VMware host data.
- [Opret beskeder](log-analytics-alerts.md) , når bestemte VMware host hændelser indtræffer.
