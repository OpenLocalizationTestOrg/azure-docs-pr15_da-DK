<properties
    pageTitle="Forbinde Linux-computere til Log Analytics | Microsoft Azure"
    description="Ved hjælp af Log Analytics, kan du indsamle og reagere på data, der er dannet ud fra Linux-computere."
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

# <a name="connect-linux-computers-to-log-analytics"></a>Forbinde Linux-computere til Log Analytics

Ved hjælp af Log Analytics, kan du indsamle og reagere på data, der er dannet ud fra Linux-computere. Tilføje data, der indsamles fra Linux OMS tillader dig at administrere Linux systemer og objektbeholder løsninger som Docker uanset hvor computerne er placeret – stort set hvor som helst. Så kan de pågældende datakilder findes i dit lokale datacenter som fysiske servere, virtuelle computere i en skyen hostet tjeneste som Amazon Web Services (AWS) eller Microsoft Azure eller endda bærbare på dit skrivebord. Desuden indsamler OMS også data fra Windows-computere på samme måde, så den understøtter en virkelig hybrid IT-miljø.

Du kan få vist og administrere data fra alle disse kilder med Log Analytics i OMS med en enkelt management portal. Dette ikke er nødvendigt at overvåge den ved hjælp af mange forskellige systemer, gør det nemt at bruge, og du kan eksportere alle data, du er tilfreds til uanset analytics virksomhedsløsning eller system, du allerede har.

I denne artikel er en Startvejledning, som kan hjælpe dig med at indsamle og administrere data til din Linux-computere, der bruger OMS Agent for Linux. For flere tekniske detaljer som proxy serverkonfiguration, oplysninger om CollectD målepunkter og brugerdefinerede JSON-datakilder, finder du disse oplysninger på [OMS Agent for Linux oversigt](https://github.com/Microsoft/OMS-Agent-for-Linux) og [OMS Agent for Linux komplet dokumentation](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) på Github.


I øjeblikket, kan du indsamle følgende typer data fra Linux-computere:

- Ydeevnen målepunkter
- Syslog begivenheder
- Beskeder fra Nagios og Zabbix
- Docker objektbeholder ydeevne målepunkter, lager og logfiler

## <a name="supported-linux-versions"></a>Understøttede Linux-versioner

Både x86 og x64 versioner understøttes officielt på en række forskellige distribuerede Linux-versioner. OMS Agent for Linux kan også køre på andre salgsdistributioner, ikke vises på listen.

- Amazon Linux 2012.09 gennem 2015.09
- CentOS Linux 5, 6 og 7
- Oracle Linux 5, 6 og 7
- Red Hat Enterprise Linux Server 5, 6 og 7
- Debian GNU/Linux, 6, 7 og 8
- Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10
- SUSE Linux Enterprise Server 11 og 12

## <a name="oms-agent-for-linux"></a>OMS Agent for Linux
Handlinger Management pakke Agent for Linux består af flere pakker. Release-filen indeholder følgende pakker, tilgængelige ved at køre shell pakke med `--extract`.

**Pakke** | **Version** | **Beskrivelse**
----------- | ----------- | --------------
omsagent | 1.1.0 | Handlinger Management Suite Agent for Linux
omsconfig | 1.1.1 | Konfiguration agent for OMS Agent
OMI | 1.0.8.3 | Åbn administrationsinfrastruktur (OMI) – en lette CIM-Server
scx | 1.6.2 | OMI CIM-udbydere på operativsystem ydeevne målepunkter
Apache cimprov | 1.0.0 | Apache HTTP Server ydeevne overvågning-provider til OMI. Kun installeret, hvis der registreres Apache HTTP-Server.
MySQL-cimprov | 1.0.0 | MySQL serverens ydeevne overvågning-provider til OMI. Kun installeret, hvis der registreres MySQL/MariaDB server.
docker cimprov | 0.1.0 | Docker-provider til OMI. Kun installeret, hvis der registreres Docker.

### <a name="additional-installation-artifacts"></a>Yderligere installation elementer
Når du har installeret OMS agent for Linux-pakker, anvendes ændringerne i følgende yderligere systemindstillinger konfigurationen. Disse elementer fjernes, når pakken omsagent er fjernet.
- En uden rettigheder bruger ved navn: `omsagent` er blevet oprettet. Dette er den konto, omsagent daemonen kører som
- Der oprettes en sudoers "omfatter" fil på /etc/sudoers.d/omsagent dette godkender omsagent genstarte syslog og omsagent daemons. Hvis sudo "omfatter" direktiver ikke understøttes i den installerede version af sudo, skrives disse poster til /etc/sudoers.
- Syslog konfigurationen er ændret for at videresende et undersæt af hændelser til agenten. Du kan finde flere oplysninger i afsnittet **Konfiguration af dataindsamling** nedenfor

### <a name="linux-data-collection-details"></a>Linux Datadetaljer af websteder

Den følgende tabel viser data samling metoder og andre oplysninger om, hvordan data indsamles.

| kilde | Direkte Agent | SCOM agent | Azure-lager | SCOM påkrævet? | SCOM agent data, der sendes via administration af gruppe | Frekvens af websteder |
|---|---|---|---|---|---|---|
|Zabbix|![Ja](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|1 minut|
|Nagios|![Ja](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|under modtagelse af|
|Syslog|![Ja](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|fra Azure-lager: ti minutter. fra en agent: på modtagelse|
|Linux tællere i ydeevne|![Ja](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|Som planlagt, mindst 10 sekunder|
|registrering af ændringer|![Ja](./media/log-analytics-linux-agents/oms-bullet-green.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|            ![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|![Nej](./media/log-analytics-linux-agents/oms-bullet-red.png)|hver time|



### <a name="package-requirements"></a>Krav til pakke
| **Påkrævet pakke**  | **Beskrivelse**   | **Minimumversion**|
|--------------------- | --------------------- | -------------------|
|Glibc |    GNU C bibliotek   | 2,5-12|
|Openssl    | OpenSSL biblioteker | 0.9.8E eller 1.0|
|Krøllet | Krøllet web client | 7.15.5
|Python ctypes |funktionsbiblioteker | i/t.|
|PAM | Tilslutningsbar godkendelse moduler  |i/t. |

>[AZURE.NOTE] Rsyslog eller syslog viser er påkrævet til at indsamle syslog meddelelser. Standard syslog daemonen på version 5 af Red Hat Enterprise Linux, CentOS og Oracle Linux version (sysklog) understøttes ikke for indsamling af syslog hændelser. For at indsamle syslog data fra denne version af disse salgsdistributioner, bør rsyslog daemonen installeret og konfigureret til at erstatte sysklog.

## <a name="quick-install"></a>Hurtig installation

Kør følgende kommandoer for at hente omsagent, validere kontrolsummen og derefter installere og indbyggede agenten. Kommandoer er for 64-bit. Arbejdsområde-ID og primærnøgle findes i portalen OMS under **Indstillinger** under fanen **Forbindelse kilder** .

![oplysninger om arbejdsområde](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

Der findes en række andre metoder til at installere agenten og opgradere den. Du kan læse mere om dem på [trin for at installere OMS Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux).

Du kan også få vist [Azure video gennemgang](https://www.youtube.com/watch?v=mF1wtHPEzT0).

## <a name="choose-your-linux-data-collection-method"></a>Vælg din Linux metode til indsamling af data

Hvordan du vælger de datatyper, du vil indsamle afhænger af om du vil bruge OMS-portalen, eller hvis du vil redigere forskellige konfigurationsfiler direkte i din Linux-klienter. Hvis du vælger at bruge portalen, sendes konfigurationen automatisk til alle dine Linux-kunder. Hvis du skal bruge forskellige konfigurationer til forskellige Linux-klienter, skal du redigere klientfiler individuelt – eller bruge en alternativ som PowerShell DTK, kok eller Puppet.

Du kan angive syslog begivenheder og tællere i ydeevne, du vil indsamle ved hjælp af konfigurationsfiler på Linux-computere. *Hvis du vælger at konfigurere dataindsamling ved at redigere agent konfigurationsfiler, skal du deaktivere centralt konfigurationen.*  Du kan konfigurere indsamling af data i den agent konfigurationsfiler samt at deaktivere central konfiguration for alle OMS supportmedarbejdere for Linux eller individuelle computere der findes anvisninger nedenfor.

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>Deaktivere OMS administration for en enkelt Linux-computer

Indsamling af centralt data til af konfigurationsdata er deaktiveret for en enkelt Linux computer med scriptet OMS_MetaConfigHelper.py. Det kan være nyttigt, hvis et undersæt af computere skal have en særlig konfiguration.

Sådan deaktiveres centralt konfiguration:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

Sådan genaktiveres centralt konfiguration:

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Linux tællere i ydeevne

Linux ydeevne tællere ligner tællere i Windows ydeevne – begge fungerer på samme måde. Du kan bruge følgende procedurer til at tilføje og konfigurere dem. Når de er føjet til OMS, indsamles data til dem alle 30 sekunder.

### <a name="to-add-a-linux-performance-counter-in-oms"></a>Sådan tilføjer du en Linux ydeevne tæller i OMS

1. For at konfigurere OMS supportmedarbejdere til Linux ved hjælp af portalen OMS, kan du føje tællere i Linux ydeevne på siden Indstillinger, klik på **Data**.  
2. Klik på **Linux ydeevne tællere** og vælg eller Angiv navnet på den tæller, du vil tilføje, på siden **Indstillinger** under **Data** .  
    ![data](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. Hvis du ikke kender det fulde navn på tælleren, du kan begynde at skrive en del af et navn og en liste over tilgængelige tællere vises. Når du finder den tæller, du vil tilføje, klik på navnet på listen og derefter klikke på ikonet plus for at tilføje tælleren.
4. Når du tilføjer tælleren, vises det på listen over tællere fremhævet med en farvet søjle.
5. Indstillingen **Anvend under konfiguration til mine computere** er som standard markeret. Hvis du vil deaktivere afsendelse konfigurationsdata, skal du fjerne markeringen.
6. Når du er færdig ændring tællere i ydeevne, nederst på siden skal du klikke på **Gem** for at færdiggøre dine ændringer. De, du har foretaget ændringer i konfigurationen sendes derefter til alle OMS supportmedarbejdere for Linux, der er registreret med OMS, typisk inden for 5 minutter.

### <a name="configure-linux-performance-counters-in-oms"></a>Konfigurere Linux tællere i ydeevne i OMS

For Windows tællere i ydeevne, kan du vælge en bestemt forekomst for hver ydeevne tæller. Dog for Linux tællere i ydeevne gælder uanset forekomst af en tæller, som du vælger for alle underordnede tællere for tælleren overordnede. I følgende tabel vises de almindelige forekomster, der er tilgængelige for både Linux og Windows tællere i ydeevne.

| **Navnet på forekomsten** | **Betydning** |
| --- | --- |
| \_Total | Summen af alle forekomster |
| \* | Alle forekomster |
| (/ & #124; / varians) | Stemmer overens med navnet forekomster: / eller /var |


På samme måde gælder det eksempel interval, hvor du vælger til en overordnet tæller for alle dens underordnede tællere. Alle de underordnede tæller eksempelintervaller og forekomster bundet med andre ord sammen.

### <a name="add-and-configure-performance-metrics-with-linux"></a>Tilføje og konfigurere ydeevne målepunkter med Linux

Ydeevnen målepunkter at indsamle styres af konfiguration i /etc/opt/microsoft/omsagent/conf/omsagent.conf. Se [tilgængelige ydeevne målepunkter](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) for tilgængelige klasser og målepunkter til OMS Agent til Linux.

Hvert objekt eller kategori af ydeevnen målepunkter at indsamle skal være defineret i konfigurationsfil som en enkelt `<source>` element. Syntaksen følger mønstret nedenfor.

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

Konfigurerbar parametrene for dette element er:

- **Objekt\_navnet**: objektnavn til samlingen.
- **Forekomst\_regex**: en *regulære udtryk* , der definerer hvilke forekomster til at indsamle. Værdien: `.*` angiver alle forekomster. At indsamle processor måleredskaber for kun på \_samlede forekomst, du kan angive `_Total`. Hvis du vil indsamle proces måleredskaber for kun crond eller sshd forekomster, kan du angive: `(crond|sshd)`.
- **Tæller\_navn\_regex**: en *regulære udtryk* , der definerer som tællere (for objektet) til at indsamle. Hvis du vil samle alle tællere til objektet, skal du angive: `.*`. Hvis du vil indsamle kun Udskift mellemrum tællere for objektet hukommelse, kan du angive:`.+Swap.+`
- **Interval:**: den hyppighed, hvormed indsamling af objektets tællere.

Standardkonfiguration på ydeevne målepunkter er:

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>Aktivere tællere i MySQL ydeevne ved hjælp af Linux kommandoer

Hvis MySQL Server eller MariaDB Server allerede findes på computeren, når omsagent samlet er installeret, installeres automatisk en ydeevne, overvågning-provider til MySQL-Server. Denne provider opretter forbindelse til den lokale MySQL/MariaDB server til at fremvise præstationstal. Du skal konfigurere MySQL brugerlegitimationsoplysninger, så udbyderen, der kan få adgang til MySQL-serveren.

Brug eksemplet nedenfor kommandoen til at angive en standard-brugerkonto til MySQL-serveren på localhost.

>[AZURE.NOTE] Filen legitimationsoplysninger skal læses af kontoen omsagent. Det anbefales at køre kommandoen mycimprovauth som omsgent.


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


Du kan også angive de nødvendige MySQL-legitimationsoplysninger i en fil ved at oprette filen: /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth. Du kan finde flere oplysninger om administration af MySQL legitimationsoplysninger for overvågning via filen mysql-TS [administrere MySQL overvågning legitimationsoplysninger i filen godkendelse](#manage-mysql-monitoring-credentials-in-the-authentication-file).

Se [databasetilladelser, der kræves for MySQL ydeevne tællere](#database-permissions-required-for-mysql-performance-counters) til oplysninger om objekttilladelser, der kræves, før MySQL brugeren MySQL Server indsamle data om ydeevne.

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>Aktivere tællere i Apache HTTP Server ydeevne ved hjælp af Linux kommandoer

Hvis der registreres Apache HTTP Server på computeren, når omsagent samlet er installeret, installeres automatisk en ydeevne, overvågning-provider til Apache HTTP-Server. Denne provider er baseret på en Apache "modul", der skal indlæses i Apache HTTP-serveren for at få adgang til ydelsesdata.

Du kan indlæse modulet med følgende kommando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Hvis du vil fjerne modulet Apache overvågning, skal du køre følgende kommando:

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>Sådan får du vist ydelsesdata med Log Analytics

1. Klik på feltet Log søgning i portalen handlinger Management-pakken.
2. Skriv i søgepanelet `* (Type=Perf)` til at få vist alle tællere i ydeevne.


Fordi OMS indsamler også Windows ydelsesdata tæller, du bør omfang ned søgningen til Linux-specifikke data. Så, i følgende eksempel vil vise ydelsesdata specifikke for en eksempel Linux-server, der kaldes Chorizo21.

```
Type=Perf Computer=chorizo*
```

![eksempel-server, der vises i søgeresultaterne](./media/log-analytics-linux-agents/oms-perfsearch01.png)

Du kan klikke på **målepunkter** for at få vist de tællere, der er indsamlet data for i resultaterne. Realtidsdata vises som grafer for hver tæller.

![målepunkter](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## <a name="syslog"></a>Syslog

Syslog er en begivenhed logføring protokol svarer til Windows-hændelseslogge – begge fungerer på samme måde, når vises i OMS.

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>Tilføje en ny Linux syslog facilitet i OMS

1. Klik på **Syslog** og derefter skrive navnet på funktionen syslog, du vil føje til venstre for på plusikonet, på siden **Indstillinger** under **Data** .
    ![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.  Hvis du ikke kender det fulde navn på funktionen, du kan begynde at skrive en del af et navn og en liste over tilgængelige syslog faciliteter vises. Når du finder funktionen syslog, som du vil tilføje, klik på navnet på listen og derefter klikke på ikonet plus for at tilføje funktionen syslog.
3.  Når du har tilføjet funktionen, vises det på listen over fremhævet med en farvet søjle. Derefter skal du vælge severities (kategorier af syslog facilitet oplysninger), du vil indsamle.
4.  Klik på **Gem** for at færdiggøre dine ændringer i bunden af siden. De, du har foretaget ændringer i konfigurationen sendes derefter til alle OMS supportmedarbejdere for Linux, der er registreret med OMS, typisk inden for 5 minutter.


### <a name="configure-linux-syslog-facilities-in-linux"></a>Konfigurere Linux syslog faciliteter i Linux

Syslog hændelser, der sendes fra syslog daemonen, for eksempel rsyslog eller syslog-angives, til en lokal port, som agenten lytter på. Som standard port 25224. Når agenten er installeret, anvendes en standard syslog konfiguration. Dette er fundet på:


Rsyslog: /etc/rsyslog.d/rsyslog-oms.conf

Syslog viser: /etc/syslog-ng/syslog-ng.conf


Standard OMS agent syslog konfigurationen uploader syslog begivenheder fra alle faciliteter med en prioritering af advarsel eller nyere.

>[AZURE.NOTE] Hvis du redigerer syslog konfigurationen, skal du genstarte syslog daemonen for at ændringerne kan træde i kraft.

Standard syslog konfigurationen for OMS Agent for Linux for OMS er:

#### <a name="rsyslog"></a>Rsyslog

```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>Syslog viser

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>Sådan får du vist alle Syslog begivenheder med Log Analytics

1. Klik på feltet **Log søgning** i portalen handlinger Management-pakken.
2. Vælg en foruddefineret syslog søgning i gruppering **Log Management** og derefter vælge en at køre den.

I dette eksempel vises alle Syslog begivenheder.

![Syslog hændelser, der er vist i Log søgninger](./media/log-analytics-linux-agents/oms-linux-syslog.png)

Du kan nu analysere i søgeresultaterne.

## <a name="linux-alerts"></a>Linux beskeder

Hvis du bruger Nagios eller Zabbix til at administrere din Linux-computere, kan OMS modtage beskeder, der er dannet ud fra disse værktøjer. Der er dog i øjeblikket ingen metode til at konfigurere indgående beskeder om data ved hjælp af portalen OMS. I stedet skal du redigere en konfigurationsfil for at begynde at sende beskeder til OMS.



### <a name="collect-alerts-from-nagios"></a>Indsaml beskeder fra Nagios

Hvis du vil indsamle beskeder fra en Nagios server, skal du foretage følgende ændringer i konfigurationen.

1. Giv brugeren **omsagent** skrivebeskyttet adgang til logfilen Nagios (det vil sige /var/log/nagios/nagios.log/var/log/nagios/nagios.log). Hvis filen nagios.log ejes af gruppen **nagios** , kan du føje bruger **omsagent** til gruppen **nagios** .

    ```
    sudo usermod –a -G nagios omsagent
    ```

2. Redigere filen omsagent.confconfiguration (/ etc/opt/microsoft/omsagent/conf/omsagent.conf). Sikre, at følgende poster er til stede og ikke kommenterede:

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```

3. Genstart omsagent daemonen:

    ```
    sudo service omsagent restart
    ```

### <a name="collect-alerts-from-zabbix"></a>Indsaml beskeder fra Zabbix

For at indsamle beskeder fra en Zabbix server, skal du udføre lignende trin til rettighederne for Nagios ovenfor, bortset fra skal du angive et brugernavn og en adgangskode i *klar tekst*. Dette er ikke ideel, men ændres sandsynligvis snart. Du kan løse dette problem ved anbefaler vi, at du opretter brugeren og give den tilladelse til at overvåge kun.

Et eksempel på afsnit i konfigurationsfil omsagent.conf (/ etc/opt/microsoft/omsagent/conf/omsagent.conf) for Zabbix skal ligne følgende:

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>Få vist påmindelser i Log Analytics Søg

Når du har konfigureret din Linux-computere for at sende beskeder til OMS, kan du bruge et par enkle log søgeforespørgsler til at få vist de vigtige beskeder. I følgende eksempel på forespørgsel returnerer alle de registrerede beskeder, der er blevet oprettet. Eksempelvis hvis nogle slags problem opstår i din IT-infrastruktur, kan derefter resultater for det følgende eksempel på forespørgsel angive, kan problemet stammer fra. Og du kan nemt gå til de vigtige beskeder af kildesystem for at afgrænse din undersøgelse. Fordelen er, at du ikke behøver at gå til forskellige systemer fra starten – forudsat, at dine beskeder sendes til OMS, du kan starte der.

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>Have vist alle Nagios beskeder med Log Analytics
1. Klik på feltet **Log søgning** i portalen handlinger Management-pakken.
2. Skriv følgende søgeforespørgsel i værktøjslinjen forespørgsel

    ```
    Type=Alert SourceSystem=Nagios
    ```
![Nagios beskeder vises i Log Søg](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

Efter at du kan se søgeresultaterne, kan du gå til yderligere oplysninger som *AlertState*.

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>Have vist alle Zabbix beskeder med Log Analytics
1. Klik på feltet **Log søgning** i portalen handlinger Management-pakken.
2. Skriv følgende søgeforespørgsel i værktøjslinjen forespørgsel

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![Zabbix beskeder vises i Log Søg](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

Efter at du kan se søgeresultaterne, kan du gå til yderligere oplysninger som *AlertName*.


## <a name="compatibility-with-system-center-operations-manager"></a>Kompatibilitet med System Center Operations Manager

OMS Agent for Linux deler agent binære filer med System Center Operations Manager agent. Installere OMS Agent for Linux på et system, der aktuelt administreres af Operations Manager opgraderer OMI og SCX pakkerne på computeren til en nyere version. OMS Agent for Linux og System Center 2012 R2 er kompatible. Dog **System Center 2012 SP1 og tidligere versioner er i øjeblikket er ikke kompatibel eller understøttede med OMS Agent for Linux.**

>[AZURE.NOTE] Hvis OMS Agent for Linux er installeret på en computer, der ikke er i øjeblikket administreres af Operations Manager, og du senere vil administrere computeren med Operations Manager, skal du ændre OMI konfigurationen, før du registrere computeren. **Dette trin er ikke er nødvendigt, hvis Operations Manager-agent er installeret før OMS Agent for Linux.**

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>Aktivere OMS Agent for Linux til at kommunikere med Operations Manager

1. Redigere filen /etc/opt/omi/conf/omiserver.conf
2. Sørg for, at den linje, der begynder med **httpsport =** definerer port 1270. F.eks.`httpsport=1270`
3. Genstart OMI server:

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## <a name="database-permissions-required-for-mysql-performance-counters"></a>Databasetilladelser, der kræves for MySQL ydeevne tællere

Hvis du vil give tilladelser til en MySQL overvågning bruger, skal tildele brugeren have 'Giv option' rettighed samt de rettigheder, der gav.

For at MySQL-brugeren til at returnere ydelsesdata brugeren skal have adgang til følgende forespørgsler:

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

Ud over disse forespørgsler på MySQL kræver bruger Vælg adgang til følgende standard tabeller:

- INFORMATION_SCHEMA
- MySQL

Disse rettigheder kan tildeles ved at køre følgende give kommandoer.

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>Administrere MySQL overvågning legitimationsoplysninger i filen godkendelse

De følgende afsnit kan du administrere MySQL legitimationsoplysninger.

### <a name="configure-the-mysql-omi-provider"></a>Konfigurere provideren MySQL OMI

Provideren MySQL OMI kræver en forudkonfigureret MySQL-bruger og installeret MySQL-klientbiblioteker for at forespørge om ydeevne/sundhed oplysningerne fra MySQL-forekomsten.

### <a name="mysql-omi-authentication-file"></a>MySQL OMI godkendelsesfil

MySQL OMI udbyder bruger en godkendelsesfil til at finde ud af, hvilken binde-adresse og port MySQL forekomst lytter på, og hvad legitimationsoplysninger, der skal bruge til at indsamle målepunkter. Under installationen MySQL OMI udbyder scanner MySQL my.cnf konfigurationsfiler (standardplaceringer) for bind-adresse og port og delvist angive den MySQL OMI godkendelsesfil.

For at fuldføre overvågning af en MySQL server-forekomst, du Tilføj en allerede oprettet MySQL OMI godkendelsesfil i den rigtige mappe.

### <a name="authentication-file-format"></a>Godkendelse-filformat

MySQL OMI godkendelse filen er en tekstfil, der indeholder oplysninger om:

- Port
- Bind-adresse
- MySQL brugernavn
- Base64-kodet adgangskode

Filen MySQL OMI godkendelse giver kun rettigheder til at læse-og skriveadgang til Linux brugeren, som genererede den.

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

Standard MySQL OMI godkendelsesfil indeholder en standardforekomst og et portnummer afhængigt af hvilke oplysninger er tilgængelige og fortolket fra den fundne MySQL-konfigurationsfil.

Standardforekomsten er en måde at administrere flere MySQL-forekomster på én Linux vært nemmere og er markeret med forekomsten med port 0. Alle tilføjede forekomster arver egenskaber er angivet fra standardforekomsten. Eksempelvis hvis MySQL forekomst lytte på port '3308' er tilføjet, bruges på standardforekomst bind-adresse, brugernavn og adgangskode Base64-kodet til at prøve og overvåge forekomsten lytter på 3308. Hvis forekomsten på 3308 er binded til en anden adresse og bruger det samme MySQL brugernavn og din adgangskode par kun respecification binde-adresse er nødvendig og de andre egenskaber nedarves.

Eksempler på filen godkendelse minde om følgende.

Standardforekomst og forekomst med port 3308:

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

Standardforekomst og forekomst med port 3308 + forskellige Base 64-kodet adgangskode:

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **Egenskaben** | **Beskrivelse** |
| --- | --- |
| Port | Port repræsenterer den aktuelle port forekomsten MySQL lytter på.  Port 0 betyder, at de egenskaber, følge bruges standardforekomst. |
| Bind-adresse | den adresse, binde er den aktuelle MySQL bind-adresse |
| brugernavn | Denne brugernavnet for den MySQL-bruger, du vil bruge til at overvåge MySQL server-forekomsten. |
| Base64 kodet adgangskode | Dette er adgangskoden til den MySQL overvågning bruger kodet i Base64. |
| Automatiske opdateringer | Når provideren MySQL OMI opgraderes, udbyderen Scan til ændringer i filen my.cnf og overskrive filen MySQL OMI godkendelse. Angiv dette flag til true eller false, afhængigt af nødvendige opdateringer til filen MySQL OMI godkendelse. |

#### <a name="authentication-file-location"></a>Godkendelse filplacering

MySQL OMI godkendelse filen skal være placeret på følgende placeringer og med navnet "mysql-TS":

/var/opt/Microsoft/MySQL-cimprov/auth/omsagent/MySQL-auth

Fil (og auth/omsagent directory), skal ejes af omsagent brugeren.

## <a name="agent-logs"></a>Agent logfiler

Logfiler til OMS Agent til Linux er på:

/ varians/vælge/microsoft/omsagent/log /

Logfiler til OMS Agent til Linux til omsconfig (konfiguration af agent) program er på:

/ varians/vælge/microsoft/omsconfig/log /

Logfiler til OMI og SCX komponenter (som understøtter målepunkter ydelsesdata) er på:

/ varians/vælge/omi/log/og /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>Fejlfinding i forbindelse med OMS Agent for Linux

Brug følgende oplysninger til at diagnosticere og foretage fejlfinding af almindelige problemer.

Hvis ingen af de oplysninger om fejlfinding i dette afsnit hjælper dig med at, kan du også bruge følgende ressourcer for at løse problemet.

- Kunder med Premier support kan logge en supportsag via [Premier](https://premier.microsoft.com/)
- Kunder med Azure supportaftaler kan logge understøttelse af sager i [Azure-portalen](https://manage.windowsazure.com/?getsupport=true)
- Arkivere et [GitHub problem](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)
- Feedback-forum for at få ideer og for at oprette en fejl rapportere [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>Vigtige logplaceringer

Fil | Sti
---- | -----
OMS Agent for Linux logfil | `/var/opt/microsoft/omsagent/log/omsagent.log `
OMS Agent Log konfigurationsfil | `/var/opt/microsoft/omsconfig/omsconfig.log`

### <a name="important-configuration-files"></a>Af vigtige konfigurationsfiler

Catergory | Filplacering
----- | -----
Syslog | `/etc/syslog-ng/syslog-ng.conf`eller `/etc/rsyslog.conf` eller`/etc/rsyslog.d/95-omsagent.conf`
Ydeevne, Nagios, Zabbix, OMS output og generelle agent | `/etc/opt/microsoft/omsagent/conf/omsagent.conf`
Yderligere konfigurationer | `/etc/opt/microsoft/omsagent/conf.d/*.conf`

>[AZURE.NOTE] Redigering konfigurationsfiler til tællere i ydeevne og syslog overskrives, hvis OMS Portal-konfigurationen er aktiveret. Du kan deaktivere konfiguration på portalen OMS (for alle noder) eller for enkelt noder ved at køre følgende:

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>Aktivere logføring af fejlfinding

For at aktivere logføring af fejlfinding, kan du bruge OMS output plug-in og detaljeret output.

#### <a name="oms-output-plugin"></a>OMS output-plug-in

FluentD kan plug-in'et angive logføring til anden logføringsniveauer for input og output. For at angive en anden logføringsniveau for OMS output skal du redigere konfigurationen af generelle agent i den `/etc/opt/microsoft/omsagent/conf/omsagent.conf` fil.

I bunden af konfigurationsfilen ændre den `log_level` egenskab fra `info` til `debug`.

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

Logføring af fejlfinding kan du se batchopdelte overførsler til tjenesten OMS adskilt efter type, antal dataelementer og tid, det tager at sende.

*Eksempel logfil til fejlfinding, der er aktiveret:*
```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>Detaljeret output
I stedet for bruger OMS output plug-in'en, kan du også får dataelementer direkte til `stdout`, som er synlige i OMS Agent for Linux logfil.

I filen OMS generelle agent konfiguration på `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, kommentar fra OMS output plug-in'en ved at tilføje en `#` foran hver linje.

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Under output plug-in'en, fjerner kommentaren i følgende afsnit ved at fjerne den `#` symbol i begyndelsen af hver linje.

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>Videresendte Syslog meddelelser vises ikke i logfilen

#### <a name="probable-causes"></a>Årsag

- Anvendt på serveren, der Linux konfigurationen tillader ikke samling af sendte faciliteter og/eller logføringsniveauer
- Syslog, er ikke korrekt bliver videresendt til Linux-serveren
- Antallet af beskeder bliver videresendt sekundet er for stor til den grundlæggende konfiguration af OMS Agent for Linux til at håndtere

#### <a name="resolutions"></a>Løsninger

- Bekræfte, at konfigurationen i portalen OMS for Syslog indeholder alle faciliteterne og de korrekte logføringsniveauer
  - **OMS Portal > Indstillinger > Data > Syslog**
-  Bekræfte den oprindelige syslog messaging daemons (`rsyslog`, `syslog-ng`) kan modtage videresendte meddelelser
- Kontrollere firewall-indstillinger på Syslog serveren for at sikre, at meddelelser ikke blive blokeret
-  Simulere en Syslog meddelelse til OMS ved hjælp af den `logger` kommando - f.eks.:
  - `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>Problemer med at oprette forbindelse til OMS, når du bruger en proxy

#### <a name="probable-causes"></a>Årsag

- Proxyen angivet når installation og konfiguration af agenten er forkerte
- Slutpunkterne OMS-tjenesten er ikke whitelistested i dit datacenter

#### <a name="resolutions"></a>Løsninger

- Geninstallere OMS Agent for Linux ved hjælp af følgende kommando med indstillingen `-v` aktiveret. Dette giver mulighed for detaljeret output af agent forbindelse via proxy til OMS-tjenesten.
  - `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  - Gennemgå dokumentationen for OMS proxy på [konfigurerer agent til brug sammen med en HTTP-proxy-server](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server)
- Kontrollér, at følgende OMS-tjenesten slutpunkterne er whitelisted

Agent ressource | Porte
---- | ----
& #42. ods.opinsights.Azure.com | Port 443
& #42. OMS.opinsights.Azure.com | Port 443
ods.systemcenteradvisor.com | Port 443
& #42;.blob.core.windows.net/ | Port 443

### <a name="a-403-error-is-displayed-when-onboarding"></a>Fejlen 403 vises når onboarding

#### <a name="probable-causes"></a>Årsag

- Dato og klokkeslæt er forkerte på Linux-Server
- Arbejdsområde-ID og arbejdsområde krypteringsnøglen er forkerte

#### <a name="resolution"></a>Opløsning

- Kontrollér klokkeslæt på Linux-serveren med den `date` kommandoen. Hvis dataene er større end eller mindre end 15 minutter fra det aktuelle klokkeslæt, mislykkes onboarding. Opdater dato og/eller tidszone for Linux server for at løse problemet.
- Den seneste version af OMS Agent for Linux giver dig besked, hvis en tidsforskel forårsager onboarding fejl
- Nyt-indbyggede med den korrekte arbejdsområde-ID og arbejdsområde nøgle. Yderligere oplysninger finder du i [Onboarding ved hjælp af kommandolinjen](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) .

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>Fejlen 500 eller 404-fejl vises i logfilen efter onboarding

Dette er et kendt problem, der opstår under første overførslen af Linux data i et OMS arbejdsområde. Dette påvirker ikke data, der sendes eller andre problemer. Du kan ignorere fejlene, når først onboarding.

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Nagios data vises ikke i portalen OMS

#### <a name="probable-causes"></a>Årsag
- Omsagent brugeren har ikke tilladelse til at læse fra logfilen Nagios
- Nagios kilde- og filter sektioner er stadig kommenterede i filen omsagent.conf

#### <a name="resolutions"></a>Løsninger

- Føje omsagent brugeren for at læse fra filen Nagios. Du kan få flere oplysninger i [Nagios beskeder](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts) .
- I OMS Agent for Linux generelle konfigurationsfil på `/etc/opt/microsoft/omsagent/conf/omsagent.conf`, sikre dig, der **både** Nagios kilden og filter sektioner har kommentarer fjernede, svarende til følgende eksempel.

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Linux data vises ikke i portalen OMS

#### <a name="probable-causes"></a>Sandsynlige årsager

- Onboarding til tjenesten OMS mislykkedes
- Forbindelsen til tjenesten OMS er blokeret
- OMS Agent for Linux data er sikkerhedskopierede

#### <a name="resolutions"></a>Løsninger

- Bekræfte onboarding til tjenesten OMS lykkedes ved at kontrollere, at den `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` findes.
- Nyt-indbyggede ved hjælp af omsadmin.sh kommandolinjen. Yderligere oplysninger finder du i [Onboarding ved hjælp af kommandolinjen](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) .
- Hvis du bruger en proxy, bruge proxy fejlfindingstrin ovenfor
- I nogle tilfælde, når OMS Agent for Linux ikke kan kommunikere med tjenesten OMS data på agenten er sikkerhedskopierede fuld bufferen størrelsen på 50 MB. Genstarte OMS Agent for Linux ved at køre den enten på `service omsagent restart` eller `systemctl restart omsagent` kommandoer.
  >[AZURE.NOTE] Problemet er løst i Agent version 1.1.0-28 eller nyere versioner.

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>Syslog Linux ydeevne tæller konfiguration anvendes ikke på portalen OMS

#### <a name="probable-causes"></a>Årsag

- Konfiguration agent i OMS Agent for Linux har ikke hentet seneste konfigurationen fra portalen OMS.
- Revideret indstillingerne i portalen blev ikke anvendt

#### <a name="resolutions"></a>Løsninger

`omsconfig`er konfiguration agent i OMS Agent for Linux, der henter OMS konfiguration af portal ændringer hver 5 minutter. Denne konfiguration derefter anvendes på OMS Agent for Linux konfigurationsfiler placeret på `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

- I nogle tilfælde OMS Agent for Linux konfiguration agent muligvis ikke kunne kommunikere med tjenesten konfiguration af portal, hvilket resulterer i seneste konfiguration ikke anvendes.
- Kontrollér, at den `omsconfig` agent er installeret med følgende:
  - `dpkg --list omsconfig`eller`rpm -qi omsconfig`
  - Hvis du ikke er installeret, kan du installere den nyeste version af OMS Agent for Linux

- Kontrollér, at den `omsconfig` agent kan kommunikere med tjenesten OMS
  - Køre den `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` kommandoen
    - Kommandoen ovenfor returnerer konfigurationen pågældende agent henter fra portalen, herunder indstillinger for Syslog, Linux tællere i ydeevne og brugerdefinerede logge
    - Hvis kommandoen ovenfor mislykkes, skal du køre den `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` kommandoen. Denne kommando gennemtvinger omsconfig agent til at kommunikere med tjenesten OMS til at hente den nyeste konfiguration.


### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>Brugerdefinerede Linux logdata vises ikke i portalen OMS

#### <a name="probable-causes"></a>Årsag

- Onboarding til OMS tjenesten mislykkedes
- Indstillingen **Anvend følgende konfiguration til min Linux-serverne** er ikke valgt
- omsconfig har ikke fået seneste brugerdefinerede loggen fra portalen
- Den `omsagent` brug kan ikke få adgang til den brugerdefinerede log på grund af problemer med tilladelser eller `omsagent` blev ikke fundet. I dette tilfælde får du vist følgende output:
  - `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  - `[DATETIME] [error]: file not accessible by omsagent.`
- Dette er et kendt problem med betingelsen Kør, der er blevet løst i OMS Agent for Linux version 1.1.0-217

#### <a name="resolutions"></a>Løsninger
- Kontrollér, at du korrekt har en onboarded, ved at bestemme, om den `/etc/opt/microsoft/omsagent/conf/omsadmin.conf` filen findes.
  - Hvis nødvendige, indbyggede igen ved hjælp af omsadmin.sh kommandolinjen. Yderligere oplysninger finder du i [Onboarding ved hjælp af kommandolinjen](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) .
- Sikre dig, at indstillingen **Anvend følgende konfiguration til min Linux-servere** er markeret i portalen OMS under **Indstillinger** under fanen **Data**  
  ![anvende konfiguration](./media/log-analytics-linux-agents/customloglinuxenabled.png)

- Kontrollér, at den `omsconfig` agent kan kommunikere med tjenesten OMS
  - Køre den `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` kommandoen
  - Kommandoen ovenfor returnerer konfigurationen pågældende agent henter fra portalen, herunder indstillinger for Syslog, Linux tællere i ydeevne og brugerdefinerede logge
  - Hvis kommandoen ovenfor mislykkes, skal du køre den `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` kommandoen. Denne kommando gennemtvinger omsconfig agent til at kommunikere med OMS-tjenesten og hente den nyeste konfiguration.


I stedet for OMS Agent for Linux bruger, der kører som en bruger med rettigheder `root`, OMS Agent for Linux kører som den `omsagent` bruger. I de fleste tilfælde skal have tildelt udtrykkelig tilladelse til brugeren for at læse bestemte filer.

Give tilladelse til at `omsagent` bruger, du køre følgende kommandoer:

1. Tilføje den `omsagent` bruger til en bestemt gruppe med`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Give universal læseadgang til den ønskede fil med`sudo chmod -R ugo+rw <FILE DIRECTORY>`

Der er et kendt problem med betingelsen Kør, der er blevet løst i OMS Agent for Linux version 1.1.0-217. Når du har opdateret til den nyeste version agent, skal du køre følgende kommando for at få den seneste version af plug-in'en til output:

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/conf/omsagent.conf
```

## <a name="known-limitations"></a>Kendte begrænsninger
Gennemse de følgende afsnit for at få mere at vide om aktuelle begrænsninger af OMS Agent for Linux.

### <a name="azure-diagnostics"></a>Azure diagnosticering

For Linux virtuelle maskiner, der kører i Azure, muligvis yderligere trin til at tillade dataindsamling ved Azure diagnosticering og handlinger Management-pakken. Diagnosticerings-udvidelse til Linux **version 2.2** er påkrævet for kompatibilitet med OMS Agent for Linux.

Se [Brug af kommandoen Azure CLI for at aktivere Linux diagnosticering lokalnummer](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension)kan finde flere oplysninger om installation og konfiguration af filtypenavnet diagnosticering for Linux.

**Opgradering filtypenavnet Linux Diagnostics fra 2.0 til 2,2 Azure CLI ASM:**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

Disse eksempler på kommandoer henviser til en fil med navnet PrivateConfig.json. Formatet af den pågældende fil skal ligne følgende eksempel.

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>Sysklog understøttes ikke

Rsyslog eller syslog viser er påkrævet til at indsamle syslog meddelelser. Standard syslog daemonen på version 5 af Red Hat Enterprise Linux, CentOS og Oracle Linux version (sysklog) understøttes ikke for indsamling af syslog hændelser. For at indsamle syslog data fra denne version af disse salgsdistributioner, bør rsyslog daemonen installeret og konfigureret til at erstatte sysklog. Se [installere den nyligt indbyggede rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)kan finde flere oplysninger om erstatte sysklog med rsyslog.

## <a name="next-steps"></a>Næste trin

- [Tilføje Log Analytics løsninger fra løsningsgalleriet](log-analytics-add-solutions.md) til at tilføje funktionalitet og indsamle data.
- Bliv fortrolig med [log søgninger](log-analytics-log-searches.md) til at få vist detaljerede oplysninger, der indsamles af løsninger.
- Bruge [dashboards](log-analytics-dashboards.md) til at gemme og få vist dine egne brugerdefinerede søgninger.
