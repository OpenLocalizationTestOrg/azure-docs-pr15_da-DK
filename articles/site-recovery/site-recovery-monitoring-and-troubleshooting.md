<properties
    pageTitle="Overvåge og fejlfinding i forbindelse med beskyttelse til virtuelle computere og fysiske servere | Microsoft Auzre" 
    description="Azure gendannelse af websteder koordinater gentagelse, failover og gendannelse af virtuelle maskiner, der er placeret på lokale servere til Azure eller en sekundær datacenter. Brug denne artikel til at overvåge og fejlfinding i forbindelse med VMM eller Hyper-V websted beskyttelse." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Overvåge og fejlfinding i forbindelse med beskyttelse til virtuelle computere og fysiske servere

Denne overvågnings- og fejlfinding i forbindelse med vejledning giver dig mulighed at lære om sporing replikering tilstanden og fejlfinding i forbindelse med teknikker til Azure gendannelse af websteder.

## <a name="understanding-the-components"></a>Forstå komponenterne

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>VMware/fysiske websted installation af replikering mellem lokale og Azure.
Du konfigurerer DR mellem lokale VMware/fysisk maskine; Konfiguration af Server, Master mål og proces Server skal konfigureret. Under aktivering beskyttelse til kildeserveren installeres Azure gendannelse af websteder mobilitet tjeneste. Indlæg lokale afbrydelse når kildeserveren opstår over til Azure, kunder skal konfigurere en Process-Server i Azure og en Master Target server i det lokale miljø for at beskytte kildeserveren tilbage til genopbygget lokalt. 

![VMware/fysiske websted installation af replikering mellem lokale og Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>Installation af VMM websted for replikering mellem lokale websted.

Som en del af konfigurationen af DR mellem to lokalt placering. Azure websted gendannelse udbyder skal hentes og installeres på VMM-serveren. Udbyder skal have forbindelse til internettet til at sikre, at alle handlinger, der udløste fra Azure Portal får oversættes til lokale operationer ud til at aktivere beskyttelse lukning primære side virtuelle maskiner som en del af failover osv.

![VMM websted installation af replikering mellem lokale websted](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>Installation af VMM websted for replikering mellem lokale og Azure.

Som en del af konfigurationen af DR mellem lokale og Azure; Azure websted gendannelse udbyder skal hentes og installeres på VMM serveren sammen med Azure betroet Services bruger som skal være installeret på hver Hyper-V-vært. Se [Forstå websted på Azure beskyttelse](./site-recovery-understanding-site-to-azure-protection.md) kan finde flere oplysninger.

![VMM websted installation af replikering mellem lokale og Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Hyper-V websted installation af replikering mellem lokale og Azure

Dette er den samme som VMM installation – kun forskellen blive udbyderen og Agent bliver installeret på værten Hyper-V sig selv. Se [Forstå websted på Azure beskyttelse](./site-recovery-understanding-site-to-azure-protection.md) kan finde flere oplysninger.

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Overvåge konfiguration, beskyttelse og gendannelse handlinger

Hver handling i ASR bliver overvåget og registreres under fanen "Sager". Gå til fanen job og se, om der er fejl i tilfælde af en hvilken som helst konfiguration, beskyttelse eller gendannelse fejl.

![Overvåge konfiguration, beskyttelse og gendannelse handlinger](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Når du finder fejl under visningen job, skal du vælge jobbet og klikke FEJLOPLYSNINGER for det pågældende job.

![Overvåge konfiguration, beskyttelse og gendannelse handlinger](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Oplysninger om fejlen hjælper dig med at identificere mulig årsag og anbefaling til problemet.

![Overvåge konfiguration, beskyttelse og gendannelse handlinger](media/site-recovery-monitoring-and-troubleshooting/image5.png)

I ovenstående tilfælde synes der at være en anden handling, som er i gang på grund af som beskyttelse konfiguration går ned. Sørg for, at du kan løse problemet ud fra anbefaling – der efter skal du klikke på RESART at starte handlingen igen.

![Overvåge konfiguration, beskyttelse og gendannelse handlinger](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Indstilling for at genstart er ikke tilgængelig for alle handlinger – for dem, der ikke har indstillingen Genstart gå tilbage til objektet, og Udfør handlingen igen. Hver sag kan annulleres på en hvilken som helst tidspunkt under fremdrift ved hjælp af knappen Annuller.

![Overvåge konfiguration, beskyttelse og gendannelse handlinger](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Overvåge gentagelse tilstand for virtuel maskine

ASR udbyder central & remote overvågning via Azure administrationsportalen for hver af de beskyttede enheder. Gå til de beskyttede elementer der efter Vælg VMM SKYER eller beskyttelse grupper. VMM SKYER fane er kun for VMM baseret installationer og alle andre scenarier har de beskyttede objekter under fanen beskyttelse grupper.

![Overvåge gentagelse tilstand for virtuel maskine](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Der efter Vælg objektet beskyttet under respektive skyen eller gruppen beskyttelse. Når du har valgt beskyttet objektet alle tilladte er handlinger vist i den nederste rude.

![Overvåge replikering tilstand for virtuel maskine](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Som vist ovenfor i tilfælde den virtuelle maskine tilstand er meget vigtigt – kan du klikke på knappen flere oplysninger om FEJLEN i bunden for at se fejlen. Baseret på de "mulige årsager" og "Anbefaling" nævnt løse problemet.

![Overvåge gentagelse tilstand for virtuel maskine](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Overvåge gentagelse tilstand for virtuel maskine](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Bemærk: Hvis der er en hvilken som helst aktive transaktioner, der er i gang eller mislykkede derefter gå til visningen job som tidligere nævnt til at få vist den JOB specifikke fejl.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Foretage fejlfinding af problemer med lokale Hyper-V

Oprette forbindelse til den lokale Hyper-V manager-konsollen, vælge den virtuelle maskine, og se gentagelse tilstand.

![Foretage fejlfinding af problemer med lokale Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image12.png)

I dette tilfælde der *Gentagelse sundhed* angives som kritisk – *Vis gentagelse sundhed* kan se detaljerne.

![Foretage fejlfinding af problemer med lokale Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Højreklik på Vælg *Gentagelse*i de tilfælde hvor replikering er standset til den virtuelle maskine,->*CV replikering*
![fejlfinding i forbindelse med lokale Hyper-V problemer](media/site-recovery-monitoring-and-troubleshooting/image19.png)

I tilfælde af virtuelt overfører en ny Hyper-V-vært (inden for klyngen eller en enkeltstående maskine), som er blevet konfigureret gennem ASR, vil ikke kunne gentagelse til den virtuelle maskine påvirkes. Sikre, at den nye Hyper-V vært opfylder alle per-produktudgivelsen og er konfigureret ved hjælp af ASR.

### <a name="event-log"></a>Hændelseslog

| Begivenhed kilder                | Detaljer                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Programmer og logge/Microsoft/VirtualMachineManager/Server/Tjenesteadministrator** (VMM Server)   |  Dette giver nyttige logføring til at løse mange forskellige VMM problemer. |
| **Programmer og Service logfiler/MicrosoftAzureRecoveryServices/gentagelse** (Hyper-V Host)   | Dette giver nyttige logføring til at løse mange problemer med Microsoft Azure Services betroet. <br/> ![Begivenhed kilde til Hyper-V host](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Programmer og en tjeneste logge/Microsoft/Azure websted gendannelse/udbyder/praktisk** (Hyper-V Host)   | Dette giver nyttige logføring til at løse mange problemer med Microsoft Azure websted gendannelse tjenesten. <br/> ![Begivenhed kilde til Hyper-V host](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Programmer og logge/Microsoft/Windows/Hyper-V-VMMS/Tjenesteadministrator** (Hyper-V Host) | Dette giver nyttige logføring til at løse mange problemer med Hyper-V virtuelt styring. <br/> ![Begivenhed kilde til Hyper-V host](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Hyper-V replikering logføringsindstillinger

Alle hændelser, der vedrører Hyper-V replika er logget på Hyper-V-VMMS\\administrator logføringsoplysninger **Services logfiler for programmer og\\Microsoft\\Windows**. En analytisk logfil kan desuden være aktiveret for Hyper-V-VMMS. For at aktivere denne log skal først sørge logfilerne Analytisk og fejlfinding kan ses i Logbog. Åbn Logbog, derefter i **menuen Vis**, klik på **Vis analytisk og fejlfinding hændelseslogge**.

![Foretage fejlfinding af problemer med lokale Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image14.png)

En analytisk loggen er synlig under Hyper-V-VMMS

![Foretage fejlfinding af problemer med lokale Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Klik på **Aktivér Log**i ruden **Handlinger** . Når aktiveret, skal den vises i **Performance-skærm** , som en begivenhed sporingssession placeret under **indsamler datasæt.**

![Foretage fejlfinding af problemer med lokale Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image16.png)

For at få vist de oplysninger, der indsamles, først stoppe Sporingssessionen ved at deaktivere loggen, og derefter gemme loggen og åbne den igen i Logbog eller bruge andre værktøjer til at konvertere den efter behov.



## <a name="reaching-out-for-microsoft-support"></a>At kontakte for Microsoft Support

### <a name="log-collection"></a>Log af websteder

Se [ASR Log af websteder ved hjælp af værktøjet Support diagnosticering Platform (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) for at indsamle de nødvendige logfiler til VMM websted beskyttelse.

Hente [værktøj](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) til Hyper-V websted beskyttelse og udføre på værten Hyper-V for at indsamle logge.

Se [Azure gendannelse Log af websteder for beskyttelse af VMware og fysiske websted](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) for at indsamle de nødvendige logfiler for VMware/fysiske scenarier.

Værktøjet indsamler loggene lokalt under et vilkårligt navn undermappe under **%LocalAppData%\ElevatedDiagnostics**

![Eksempler på trin vises fra Hyper-V websted beskyttelse.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Åbne en supportbilletter

Hvis du vil hæve supportbilletter for ASR, Henvend dig til Azure understøtter hjælp URL-adressen på <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>KB-artikel

-   [Sådan bevares bogstavet for beskyttet virtuelle maskiner, der er mislykkedes eller overflyttet til Azure](http://support.microsoft.com/kb/3031135)
-   [Sådan administreres lokalt til Azure beskyttelse netværk båndbredden](https://support.microsoft.com/kb/3056159)
-   [ASR: "Klyngeressourcen blev ikke fundet" fejl, når du forsøger at aktivere beskyttelse til en virtuel maskine](http://support.microsoft.com/kb/3010979)
-   [Forstå og fejlfinding i forbindelse med Hyper-V replika vejledning](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Almindelige ASR fejl og deres løsninger

Nedenfor finder du de mest almindelige fejl, som du kan rammer og deres løsninger. Hver af fejlen er beskrevet i en separat WIKI-side.

### <a name="general"></a>Generelt
-   <span style="color:green;">Ny</span> [Job ned med fejlen "Handlingen er i gang". Fejl 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Ny</span> [Job ned med fejlen "Server ikke er forbindelse til internettet". Fejl 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Konfiguration
-   [VMM serveren kan ikke registreres på grund af en intern fejl. Se visningen job i portalen websted gendannelse kan finde flere oplysninger om fejlen. Kør installationsprogrammet igen for at registrere serveren.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Kan ikke oprettes forbindelse til den Hyper-V gendannelse Manager samling af legitimationsoplysninger. Bekræft proxyindstillingerne, eller prøv igen senere.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Konfiguration
-   [Kan ikke oprette gruppen beskyttelse: Der opstod en fejl under hentning af listen over servere.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Hyper-V host klynge indeholder mindst ét statisk netværkskort, eller ingen forbundne kort er konfigureret til at bruge DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM har ikke tilladelse til at udføre en handling](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Kan ikke vælge lagerplads kontoen i abonnementet, mens du konfigurerer beskyttelse](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Beskyttelse
- <span style="color:green;">Ny</span> [Aktivér beskyttelse fejlbehæftede med fejlen "beskyttelse ikke kunne konfigureres til den virtuelle maskine". Fejl 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Ny</span> [Aktivér beskyttelse fejlbehæftede med fejlen "beskyttelse ikke kunne være aktiveret for den virtuelle maskine." Fejl 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Ny</span> [Direkte overførsel fejl 23848 - den virtuelle maskine, skal flyttes, ved hjælp af typen Live. Dette kan bryde gendannelse beskyttelsesstatus for den virtuelle maskine.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Aktivér beskyttelse mislykkedes, da Agent, der ikke er installeret på værtsmaskinen](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Blev ikke fundet en passende vært for virtuelt replika - på grund af lav beregne ressourcer](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Ikke blev fundet en passende vært for virtuelt replika - på grund af ingen logisk netværk, der er vedhæftet](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Kan ikke oprette forbindelse til værtsmaskinen replika - forbindelsen ikke kunne oprettes](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Gendannelse
- VMM ikke kan gennemføre handlingen host-
    -   [Skifte til det valgte gendannelsespunkt til virtual machine: generel adgang nægtet fejl.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V mislykkedes til at skifte til det valgte gendannelsespunkt til virtual machine: Handlingen blev afbrudt kan du prøve et nyere gendannelsespunkt. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   En forbindelse til serveren blev ikke oprettet (0x00002EFD)
        -   [Hyper-V kunne ikke aktivere modsat gentagelse til virtual machine](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Hyper-V kunne ikke aktivere gentagelse til virtuelt virtual machine](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Kan ikke acceptere sekundær server til virtuelt](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [Gendannelse plan indeholder virtuelle maskiner, ikke der er klar til planlagte failover](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [Den virtuelle maskine ikke er klar til planlagte failover](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Virtuelle maskine kører ikke og ikke er slukket](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Fraværende-band-handling er der sket på et virtuelt og Anvend failover mislykkedes](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Teste Failover
    -   [Failover kunne ikke startes, da test failover er i gang](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Ny</span>  Failover sker timeout med 'PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout' på grund af konfigurationsindstillinger på sikkerhedsgruppen netværk, der er knyttet til den virtuelle maskine eller det undernet, som computeren tilhører. Referere til ['PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) få mere at vide.


### <a name="configuration-server-process-server-master-target"></a>Konfiguration af Server, proces Server Master mål
Konfiguration af Server (CS), proces Server (PS), Master Targer (MT)
-   [Værten ESXi, som er hostet PS/CS som en VM mislykkes med en lilla skærm.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Fjernskrivebord fejlfinding efter failover
-   Mange kunder har gælder problemer med at oprette forbindelse til den mislykkedes over VM i Azure. [Brug fejlfinding dokumentet for at RDP til VM](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Tilføje en offentlige IP-adresse på en ressource manager virtuel maskine
Hvis knappen **Forbind** i portalen er nedtonet, og du ikke har forbindelse til Azure via en Express rute eller websted til websted VPN-forbindelse, skal du oprette og tildele din VM en offentlig IP-adresse, før du kan bruge RDP/SSH. Følg nedenstående trin for at tilføje en offentlige IP-adresse på netværksgrænsefladen for den virtuelle maskine.  

![Tilføje en offentlige IP-adresse på netværksgrænsefladen for mislykkedes over virtuelt](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)