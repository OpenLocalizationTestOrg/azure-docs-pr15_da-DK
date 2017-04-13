<properties 
   pageTitle="Syslog meddelelser i Log Analytics | Microsoft Azure"
   description="Syslog er en begivenhed logføring-protokollen, der er fælles for Linux.   I denne artikel beskrives, hvordan du kan konfigurere samling af Syslog meddelelser i Log analyser og oplysninger om de poster, de opretter i OMS-lager."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="syslog-data-sources-in-log-analytics"></a>Syslog datakilder i Log Analytics

Syslog er en begivenhed logføring-protokollen, der er fælles for Linux.  Programmer sender meddelelser, der kan være gemt på den lokale computer eller leveret til en Syslog indsamler.  Når OMS Agent for Linux er installeret, konfigurerer lokale Syslog daemonen for at videresende meddelelser til agenten.  Agenten sender derefter meddelelsen til Log Analytics, hvor der oprettes en tilsvarende post i OMS-lager.  

> [AZURE.NOTE]Log Analytics understøtter samling af meddelelser, der sendes af rsyslog eller syslog viser. Standard syslog daemonen på version 5 af Red Hat Enterprise Linux, CentOS og Oracle Linux version (sysklog) understøttes ikke for indsamling af syslog hændelser. For at indsamle syslog data fra denne version af disse salgsdistributioner, bør [rsyslog daemon](http://rsyslog.com) installeret og konfigureret til at erstatte sysklog.

![Syslog af websteder](media/log-analytics-data-sources-syslog/overview.png)


## <a name="configuring-syslog"></a>Konfigurere Syslog
OMS Agent for Linux indsamler kun begivenheder med faciliteter og severities, der er angivet i konfigurationen.  Du kan konfigurere Syslog via portalen OMS eller ved at administrere konfigurationsfiler på din Linux supportmedarbejdere.


### <a name="configure-syslog-in-the-oms-portal"></a>Konfigurere Syslog i portalen OMS

Konfigurere Syslog i [menuen Data i indstillinger for analyser](log-analytics-data-sources.md#configuring-data-sources).  Denne konfiguration er leveret til konfigurationsfil på hver Linux agent.

Du kan tilføje en ny funktion ved at skrive i dets navn og klikke på **+**.  For hver enkelt facilitet, der kun meddelelser med de valgte severities samles.  Se severities for funktionen bestemt, du vil indsamle.  Du kan ikke angive eventuelle yderligere kriterier til at filtrere meddelelser.

![Konfigurere Syslog](media/log-analytics-data-sources-syslog/configure.png)


Alle ændringer i konfigurationen publiceres som standard automatisk i alle supportmedarbejdere.  Hvis du vil konfigurere Syslog manuelt på hver Linux agent, derefter fjerne markeringen i afkrydsningsfeltet *Anvend under konfiguration til min Linux-computere*.


### <a name="configure-syslog-on-linux-agent"></a>Konfigurere Syslog på Linux agent

Når den [OMS agent er installeret på en Linux-klient](log-analytics-linux-agents.md), den installerer en standard syslog konfigurationsfil, der definerer facilitet og alvorlighed af de meddelelser, der er indsamlet.  Du kan redigere denne fil for at ændre konfigurationen.  Af konfigurationsfilen er forskellige, afhængigt af Syslog daemonen, som kunden har installeret.

> [AZURE.NOTE] Hvis du redigerer syslog konfigurationen, skal du genstarte syslog daemonen for at ændringerne kan træde i kraft.

#### <a name="rsyslog"></a>rsyslog

Konfigurationsfil til rsyslog er placeret i **/etc/rsyslog.d/95-omsagent.conf**.  Standardindholdet er vist nedenfor.  Dette indsamler syslog meddelelser, der sendes fra en agent for lokale for alle faciliteter med et niveau af advarsel eller nyere.

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

Du kan fjerne en funktion ved at fjerne sektionen af konfigurationsfilen.  Du kan begrænse de severities, der er indsamlet for en bestemt funktion ved at ændre den facilitet posten.  Hvis du vil begrænse funktionen bruger til meddelelser med en alvorlighed af fejl eller højere ændre du linjen konfigurationsfil til følgende:

    user.error  @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog viser

Konfigurationsfil til rsyslog er placering på **/etc/syslog-ng/syslog-ng.conf**.  Standardindholdet er vist nedenfor.  Dette indsamler syslog meddelelser, der sendes fra en agent for lokale for alle faciliteter og alle severities.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };
    
    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };
    
    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };
    
    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };
    
    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };
    
    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };
    
    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Du kan fjerne en funktion ved at fjerne sektionen i konfigurationsfilen.  Du kan begrænse de severities, der er indsamlet for en bestemt funktion ved at fjerne dem fra listen.  Hvis du vil begrænse funktionen bruger blot påmindelser og vigtige meddelelser, skal redigere du del af konfigurationsfil til følgende:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Ændre Syslog porten

OMS agent lytter efter Syslog meddelelser på den lokale klient på port 25224.  Du kan ændre denne port ved at føje følgende afsnit til konfigurationsfil OMS agent, findes i **/etc/opt/microsoft/omsagent/conf/omsagent.conf**.  Erstat 25224 i posten **port** med det portnummer, som du ønsker.  Bemærk, at du også ændre konfigurationsfil til Syslog daemonen til at sende meddelelser til denne port.

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>Dataindsamling

OMS agent lytter efter Syslog meddelelser på den lokale klient på port 25224. Konfigurationsfil til Syslog daemonen videresender Syslog meddelelser, der sendes fra programmet til denne port, hvor de er der indsamles via Log analyser.


## <a name="syslog-record-properties"></a>Syslog postegenskaber

Syslog poster har en type af **Syslog** og har egenskaberne i den følgende tabel.

| Egenskaben | Beskrivelse |
|:--|:--|
| Computer | Computer, der er indsamlet begivenheden fra. |
| Facilitet | Definerer del af den computer, der genererede meddelelsen. |
| HostIP | IP-adressen på systemet sender meddelelsen.  |
| Værtsnavn | Navnet på det system, sender meddelelsen. |
| SeverityLevel | Prioritering af begivenheden. |
| SyslogMessage | Tekst i meddelelsen. |
| Proces-id | ID for processen, som genererede meddelelsen. |
| EventTime | Dato og klokkeslæt, hændelsen blev oprettet.



## <a name="log-queries-with-syslog-records"></a>Log forespørgsler med Syslog poster

Den følgende tabel indeholder forskellige eksempler på log forespørgsler, der henter Syslog poster.

| Forespørgsel | Beskrivelse |
|:--|:--|
| Skriv = Syslog | Alle Syslogs. |
| Skriv = Syslog SeverityLevel = fejl | Alle Syslog poster med alvorlighed af fejl. |
| Skriv = Syslog & #124; måle count() ved Computer | Antallet af Syslog poster efter computer. |
| Skriv = Syslog & #124; måle count() efter facilitet | Antallet af Syslog poster efter facilitet. |

## <a name="next-steps"></a>Næste trin

- Få mere at vide om [log søgninger](log-analytics-log-searches.md) til at analysere de data, der indsamles fra datakilder og løsninger. 
- Bruge [Brugerdefinerede felter](log-analytics-custom-fields.md) til at analysere data fra syslog poster i individuelle felter.
- [Konfigurere Linux supportmedarbejdere](log-analytics-linux-agents.md) at indsamle andre typer data. 
