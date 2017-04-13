<properties
   pageTitle="Konfiguration af programmet afhængighed skærm (ADM) i handlinger Management-pakken (OMS) | Microsoft Azure"
   description="Programmet afhængighed skærm (ADM) er en handlinger Management pakke (OMS)-løsning, der automatisk opdager programkomponenter på Windows og Linux-systemer og kort kommunikationen mellem tjenester.  Denne artikel indeholder oplysninger om installation af ADM i dit miljø og bruge det i en række forskellige scenarier."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Konfiguration af programmet afhængighed skærm løsning i handlinger Management pakke (OMS)
![Administration af advarselsikon](media/operations-management-suite-application-dependency-monitor/icon.png) Programmet afhængighed skærm (ADM) opdager programkomponenter på Windows og Linux-systemer og kort kommunikationen mellem tjenester automatisk. Det kan du få vist dine servere, mens du betragte – som forbundne systemer, der leverer kritiske tjenester.  Programmet afhængighed skærm viser forbindelser mellem servere, processer, og porte på tværs af forskellige TCP-forbindelse arkitekturer med ingen konfiguration kræves dog installation af en agent.

I denne artikel beskrives oplysninger om konfiguration af programmet afhængighed skærm og onboarding supportmedarbejdere.  Se oplysninger om brug af ADM, [ved hjælp af programmet afhængighed skærm løsning i handlinger Management pakke (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Programmet afhængighed skærm er i øjeblikket i private preview.  Du kan anmode om adgang til ADM privat eksemplet på [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Under privat eksempelvisning alle OMS-konti har ubegrænset adgang til ADM.  ADM noder er gratis, men Log Analytics-data for typerne AdmComputer_CL og AdmProcess_CL vil forbrugsafregnede som enhver anden løsning.
>
>Når ADM indtaster offentlige preview, bliver den kun tilgængelig for gratis og betalte kunder af indsigt og Analytics i kørslen OMS priser Plan.  Vil være begrænset til 5 ADM noder gratis niveau konti.  Hvis du deltager i private preview og er ikke registreret i kørslen OMS priser Plan, når ADM placeres i prøveversionen, deaktiveres ADM på det pågældende tidspunkt. 



## <a name="connected-sources"></a>Tilknyttede kilder
I følgende tabel beskrives de tilknyttede kilder, der understøttes af løsningen ADM.

| Forbundne kilde | Understøttes | Beskrivelse |
|:--|:--|:--|
| [Windows supportmedarbejdere](../log-analytics/log-analytics-windows-agents.md) | Ja | ADM analyserer og indsamler data fra Windows agentcomputere.  <br><br>Bemærk, ud over agent OMS Windows supportmedarbejdere kræver Microsoft afhængighed Agent.  Se [understøttede operativsystemer](#supported-operating-systems) til en komplet liste over operativsystemversioner. |
| [Linux supportmedarbejdere](../log-analytics/log-analytics-linux-agents.md) | Ja | ADM analyserer og indsamler data fra Linux agentcomputere.  <br><br>Bemærk, ud over agent OMS Linux supportmedarbejdere kræver Microsoft afhængighed Agent.  Se [understøttede operativsystemer](#supported-operating-systems) til en komplet liste over operativsystemversioner. |
| [SCOM administration af gruppe](../log-analytics/log-analytics-om-agents.md) | Ja | ADM analyserer og indsamler data fra Windows og Linux supportmedarbejdere i en forbundne SCOM management gruppe. <br><br>En direkte forbindelse fra SCOM agent computeren til OMS er påkrævet. Data sendes direkte fra videresendt fra gruppen management til OMS-lager.|
| [Azure-lager-konto](../log-analytics/log-analytics-azure-storage.md) | Nej | ADM indsamler data fra en for agentcomputere, så der er ingen data fra det at indsamle fra Azure-lager. |

Bemærk, at ADM kun understøtter 64-bit-platforme.

I Windows, bruges Microsoft overvågning Agent (MMA) af både SCOM og OMS til at samle og sende overvågningsdata.  (Denne agent hedder den SCOM Agent, OMS Agent, MMA eller direkte Agent, afhængigt af konteksten.)  SCOM og OMS giver forskellige af feltet versioner af MMA, men disse versioner kan hver rapport til SCOM til OMS eller til begge.  På Linux, OMS Agent for Linux indsamler og sender overvågningsdata til OMS.  Du kan bruge ADM på servere med OMS direkte supportmedarbejdere eller servere, der er knyttet til OMS via SCOM Management grupper.  Til denne dokumentation, vi refererer til alle disse supportmedarbejdere – uanset om Linux eller Windows, om forbindelse til en SCOM MG eller direkte til OMS – som "OMS Agent", medmindre navnet bestemt installation af agent er behov for kontekst.

ADM agent overføres ikke selve dataene, og det kræver ikke ændringer af firewalls eller porte.  ADM'S data er altid overføres af OMS Agent til OMS, enten direkte eller via OMS gatewayen.

![ADM supportmedarbejdere](media/operations-management-suite-application-dependency-monitor/agents.png)

Hvis du er tilsluttet en SCOM kunde med en Management Group OMS:

- Hvis din SCOM supportmedarbejdere kan få adgang til internettet for at oprette forbindelse til OMS, kræves ingen ekstra konfiguration.  
- Hvis din SCOM supportmedarbejdere ikke kan få adgang til OMS via internettet, skal du konfigurere gatewayen OMS til at arbejde med SCOM.
  
Hvis du bruger den OMS direkte Agent, skal du konfigurere det OMS Agent sig selv at oprette forbindelse til OMS eller din OMS Gateway.  Gatewayen OMS kan hentes fra [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Undgå duplikerede data

Hvis du er en SCOM-kunde, skal du ikke konfigurere din SCOM supportmedarbejdere til at kommunikere direkte til OMS eller data rapporteres to gange.  I ADM, vil det resultere i computere, der vises to gange på listen computer.

Konfiguration af OMS der skal ske i kun én af følgende steder:

- Panelet SCOM Console handlinger Management-pakken til administrerede computere
- Konfiguration af Azure funktionsdygtige indsigt i egenskaberne MMA

Brug af begge konfigurationer med det samme arbejdsområde i hver medfører dubletter af data. Brug af begge med forskellige arbejdsområder kan medføre problemer med uoverensstemmende konfiguration (en med ADM løsning aktiveret, og den anden uden), der kan forhindre data der flyder til ADM helt.  

Bemærk, at selvom computeren selve ikke er angivet i konsollen SCOM OMS konfiguration, hvis en forekomst gruppe som "Windows Server-forekomster gruppe" er aktiv, det stadig resultere i maskine modtagelse OMS konfigurationen via SCOM.



## <a name="management-packs"></a>Management packs
Når ADM er aktiveret i et OMS arbejdsområde, en 300KB Management Pack sendes til alle de Microsoft overvågning supportmedarbejdere i dette arbejdsområde.  Hvis du bruger SCOM supportmedarbejdere i en [forbindelse management gruppe](../log-analytics/log-analytics-om-agents.md), som skal installeres ADM Management Pack fra SCOM.  Hvis supportmedarbejdere er direkte forbundet, vil blive leveret Styringspanelet ved OMS.

Administrationspanelet hedder Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Teksten er skrevet til *%Programfiles%\Microsoft overvågning Agent\Agent\Health-State\Management servicepakker\*.  Den datakilde, der bruges af management pack er *% Program files%\Microsoft overvågnings Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Konfiguration
Ud over Windows og Linux computere har en agent, installeret og tilsluttet OMS, installationsprogram afhængighed Agent skal hentes fra ADM løsningen og derefter installeret som rod eller administrator på hver enkelt administrerede server.  Når ADM-agent er installeret på en server, rapportering til OMS, vises ADM afhængighed kort i 10 minutter.  Hvis du har problemer, skal du sende [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Skifte fra BlueStripe FactFinder
Programmet afhængighed skærm levere BlueStripe teknologi til OMS faser. FactFinder understøttes stadig for eksisterende kunder, men ikke længere kan købes individuelle.  Denne prøveversion af afhængighed Agent kan kun kommunikere med OMS.  Hvis du er aktuelle FactFinder kunde, skal du identificere et sæt test servere for ADM, ikke som styres af FactFinder. 

### <a name="download-the-dependency-agent"></a>Hente afhængighed Agent
Ud over Microsoft Management Agent (MMA) og OMS Linux Agent som understøtter forbindelsen mellem computer og OMS, have alle computere, analysere ved programmet afhængighed skærm afhængighed Agent, der er installeret.  På Linux, skal være installeret OMS Agent for Linux før afhængighed Agent. 

![Afhængighed skærm-programmet](media/operations-management-suite-application-dependency-monitor/tile.png)

Klik på **Konfigurer løsning** i feltet **Programmet afhængighed skærm** for at åbne bladet **Afhængighed Agent** for at hente afhængighed Agent.  Bladet afhængighed Agent indeholder links til Windows og Linux supportmedarbejdere. Klik på det relevante link for at hente hver agent. Se følgende afsnit for at få oplysninger om installation af agenten på forskellige systemer.

### <a name="install-the-dependency-agent"></a>Installere afhængighed Agent

#### <a name="microsoft-windows"></a>Microsoft Windows
Der kræves administratorrettigheder for at installere eller fjerne agenten.

Afhængighed-Agent er installeret på Windows-computere med ADM-Agent-Windows.exe. Hvis du kører denne eksekverbare fil uden eventuelle indstillinger, derefter starter det en guide, som du kan følge for at udføre installationen interaktivt.  

Brug følgende trin til at installere afhængighed Agent på hver Windows-computer.

1.  Sørg for, at OMS agent er installeret ved hjælp af vejledningen ved forbinde computere direkte til OMS.
2.  Hent Windows agent og kør den med følgende kommando.<br>*ADM-Agent-Windows.exe*
3.  Følg guiden for at installere agenten.
4.  Hvis afhængighed Agent kan ikke startes, kan du se i logfilerne detaljerede fejloplysninger. På Windows-supportmedarbejdere er logmappen *C:\Program Files\BlueStripe\Collector\logs*. 

Afhængighed Agent til Windows kan fjernes af en Administrator via Kontrolpanel.


#### <a name="linux"></a>Linux
Adgang til roden kræves for at installere eller konfigurere agenten.

Afhængighed-Agent er installeret på Linux-computere med ADM-Agent-Linux64.bin, et shell script med en præsentation og binær. Du kan køre filen med vi eller tilføje tilladelser til selve filen til at køre.
 
Brug følgende trin til at installere afhængighed Agent på alle Linux-computere.

1.  Sørg for, at OMS agent er installeret ved hjælp af vejledningen i [Indsaml og administrere data fra Linux-computere.  Dette skal være installeret før Linux afhængighed Agent](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installere Linux afhængighed agent som rod ved hjælp af følgende kommando.<br>*vi ADM-Agent-Linux64.bin*.
3.  Hvis afhængighed Agent kan ikke startes, kan du se i logfilerne detaljerede fejloplysninger. På Linux supportmedarbejdere er logmappen */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Fjerne afhængighed Agent på Linux
For helt at fjerne afhængighed Agent fra Linux, skal du fjerne agenten selve og proxyen som installeres automatisk med agenten.  Du kan fjerne begge med følgende kommando.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>Installere fra en kommandolinje
Den forrige sektion giver vejledning i installation af afhængighed Monitor agent, ved hjælp af standardindstillingerne.  Afsnittene herunder giver vejledning til installation på agent fra en kommandolinje med brugerdefinerede indstillinger.

#### <a name="windows"></a>Windows
Bruge indstillinger fra tabellen nedenfor til at udføre installationen fra en kommandolinje. Se en liste over installationen flag Kør installationsprogrammet med den /? flag på følgende måde.

    ADM-Agent-Windows.exe /?

| Flag | Beskrivelse |
|:--|:--|
| /S | Udføre en uovervåget installation uden brugerinput. |

Filer til Windows afhængighed-Agent er placeret i *C:\Programmer\Microsoft Files\BlueStripe\Collector* som standard.


#### <a name="linux"></a>Linux
Bruge indstillinger fra tabellen nedenfor til at udføre installationen. Vil se en liste over installationen flag køre installationen programmer med - hjælp flag på følgende måde.

    ADM-Agent-Linux64.bin -help

| Beskrivelse af flag
|:--|:--|
| -s | Udføre en uovervåget installation uden brugerinput. |
| – Kontrollér | Kontrollerer tilladelser og operativsystem, men kan ikke installeres på agent. |

Filer til afhængighed-Agent er placeret i følgende mapper.

| Filer | Placering |
|:--|:--|
| Core-filer | /usr/lib/bluestripe-Collector |
| Logfiler | /var/opt/Microsoft/Dependency-Agent/Log |
| Config-filer | /etc/opt/Microsoft/Dependency-Agent/config |
| Tjenesten eksekverbare filer | /sbin/bluestripe-Collector<br>/sbin/bluestripe-Collector-Manager |
| Binær lagerfiler | /var/opt/Microsoft/Dependency-Agent/storage |



## <a name="troubleshooting"></a>Fejlfinding i forbindelse med
Hvis du støder på problemer med programmet afhængighed skærm, kan du indsamle oplysninger om fejlfinding fra flere komponenter ved hjælp af følgende oplysninger.

### <a name="windows-agents"></a>Windows supportmedarbejdere

#### <a name="microsoft-dependency-agent"></a>Afhængighed af Microsoft Agent
Åbn en kommandoprompt som administrator for at generere fejlfinding i forbindelse med data fra en Agent for afhængighed, og Kør scriptet CollectBluestripeData.vbs ved hjælp af følgende kommando.  Du kan tilføje – Hjælp flaget for at få vist yderligere indstillinger.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

Pakken understøttelse af Data er gemt i mappen % BRUGERPROFIL % for den aktuelle bruger.  Du kan bruge – filen <filename> mulighed for at gemme den på en anden placering.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Afhængighed af Microsoft Agent Management Pack til MMA
Afhængighed Agent Management Pack kører i Microsoft Management Agent.  Den modtager data fra en Agent for afhængighed og sender den til ADM skybaseret tjeneste.
  
Kontrollér, at management pack hentes ved at benytte følgende fremgangsmåde.

1.  Se efter en fil kaldet Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp i C:\Program Files\Microsoft overvågning Agent\Agent\Health State\Management servicepakker.  
2.  Hvis filen er ikke findes, og agenten er forbundet til en SCOM administration af gruppe, skal du kontrollere, at den er blevet importeret i SCOM ved at markere Management Packs i arbejdsområdet Administration af konsollen handlinger.

ADM Styringspanelet skriver hændelser til Operations Manager Windows-hændelsesloggen.  Loggen kan være [søger i OMS](../log-analytics/log-analytics-log-searches.md) via system log løsningen, hvor du kan konfigurere hvilke logfiler til at overføre.  Hvis fejlfinding hændelser er aktiveret, skrives programmets hændelseslog, med kilden til hændelsen *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Microsoft overvågning Agent
Åbn en kommandoprompt som administrator for at indsamle diagnosticering sporinger, og Kør følgende kommandoer: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Sporinger skrives til c:\Windows\Logs\OpsMgrTrace.  Du kan stoppe sporing med StopTracing.cmd.


### <a name="linux-agents"></a>Linux supportmedarbejdere

#### <a name="microsoft-dependency-agent"></a>Afhængighed af Microsoft Agent
Til at generere fejlfinding i forbindelse med data fra afhængighed en Agent, logon med en konto, der har rettigheder, sudo eller rod og køre følgende kommando.  Du kan tilføje – Hjælp flaget for at få vist yderligere indstillinger.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

Pakken understøttelse af Data er gemt på /var/opt/microsoft/dependency-agent/log (hvis rod) under den Agent installation directory eller til den private mappe på den bruger, der kører scriptet (hvis det er ikke-rod).  Du kan bruge – filen <filename> mulighed for at gemme den på en anden placering.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Afhængighed af Microsoft Agent Fluentd Plug-in til Linux
Afhængighed Agent Fluentd-plug-in'en kører i OMS Linux Agent.  Den modtager data fra en Agent for afhængighed og sender den til ADM skybaseret tjeneste.  

Logfiler skrives til følgende to filer.

- /var/opt/Microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>OMS Agent for Linux
En fejlfinding ressource til at oprette forbindelse Linux-servere til OMS kan findes her: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Logfiler til OMS Agent til Linux er placeret i */Varians/vælge/microsoft/omsagent/log/*.  

Logfilerne for omsconfig (konfiguration af agent) er placeret i */Varians/vælge/microsoft/omsconfig/log/*.
 
Logfilen for de OMI og SCX komponenter, som understøtter målepunkter ydelsesdata er placeret i */Varians/vælge/omi/log/* og */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Dataindsamling
Du kan forvente hver agent til at overføre omkring 25MB til hver dag, afhængigt af hvor kompleks er dit system afhængigheder.  ADM afhængighed data sendes af hver agent hver 15 sekunder.  

ADM Agent forbruger typisk 0,1% af hukommelse og 0,1% CPU-systemet.


## <a name="supported-operating-systems"></a>Understøttede operativsystemer
I følgende afsnit vises de understøttede operativsystemer til afhængighed Agent.   32-bit arkitekturer understøttes ikke af operativsystemer.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows-skrivebord
- Bemærk: Windows 10 er endnu ikke understøttet
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux og Oracle Linux (med RHEL kerne)
- Kun standard og SMP Linux kernen versioner understøttes.
- Ikke-standard kernen versioner, såsom PAE og Xen, der ikke understøttes for en hvilken som helst Linux-fordeling. For eksempel understøttes et system med release tekststrengen "2.6.16.21-0.8-xen" ikke.
- Brugerdefineret kerner, herunder rekompileringer af standard kerner, der ikke understøttes
- Centos Plus kernen understøttes ikke.
- Oracle Unbreakable kernen (UEK) er behandlet i en anden sektion nedenfor.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| OS-Version | Kerneversion |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| OS-Version | Kerneversion |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6,7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| OS-Version | Kerneversion |
|:--|:--|
| 5,8 | 2.6.18-308 |
| 5,9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux m / Unbreakable kernen (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| OS-Version | Kerneversion
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| OS-Version | Kerneversion
|:--|:--|
| 5,8 | Oracle 2.6.32-300 (UEK R1) |
| 5,9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| OS-Version | Kerneversion
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| OS-Version | Kerneversion
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Diagnosticerings- og brugen af data
Microsoft indsamler automatisk brugen og ydeevne data via din brug af tjenesten programmet afhængighed skærm. Microsoft bruger disse Data til at give og forbedre kvaliteten, sikkerhed og integriteten af tjenesten programmet afhængighed skærm. Data indeholder oplysninger om konfiguration af din software som operativsystem og version og indeholder også IP-adresse, DNS-navn og arbejdsstation navn for at give præcise og effektive fejlfinding funktioner. Vi indsamler ikke navne, adresser eller andre kontaktoplysninger.

Flere oplysninger om dataindsamling og -format, skal du se [Microsoft Online Services-erklæring](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Næste trin
- Lær, hvordan du [bruge programmet afhængighed Monitor](operations-management-suite-application-dependency-monitor.md) én gang den er installeret og konfigureret.
