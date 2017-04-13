<properties
    pageTitle="Replikeres lokalt VMware virtuelle maskiner eller fysiske servere til en sekundær websted | Microsoft Azure"
    description="Brug denne artikel kan replikeres VMware FOS eller Windows/Linux fysiske servere til en sekundær websted med Azure gendannelse af websteder."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Gentage lokale VMware virtuelle maskiner eller fysiske servere til en sekundær websted


## <a name="overview"></a>Oversigt

InMage spejder i Azure gendannelse af websteder indeholder realtid replikering mellem lokale VMware websteder. InMage spejder er inkluderet i gendannelse af websteder Azure serviceabonnementer.


## <a name="prerequisites"></a>Forudsætninger

**Azure-konto**: Du skal bruge en [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.


## <a name="step-1-create-a-vault"></a>Trin 1: Oprette en samling af legitimationsoplysninger
1. Log på [Azure-portalen](https://portal.azure.com).
2. Klik på ny > Administration > sikkerhedskopiering og gendannelse af websteder (OMS). Du kan også klikke på Gennemse > gendannelse Services samling > Tilføj.
3. Angiv et navn til at identificere samling af legitimationsoplysninger i **navn** . Hvis du har mere end ét abonnement, skal du vælge en af dem.
4. Oprette en ny ressourcegruppe i **ressourcegruppe** , eller Vælg et eksisterende dokument. Angiv et Azure område for at fuldføre obligatoriske felter. 
5.  Vælg det geografiske område for samling af legitimationsoplysninger i **placering**. For at kontrollere understøttede områder, skal du se [Azure websted gendannelse priser](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Hvis du vil få hurtig adgang til samling fra dashboardet klikke på Fastgør til dashboard, og klik derefter på Opret.
6. Den nye samling af legitimationsoplysninger vises på dashboardet > alle de ressourcer, og på de primære gendannelsestjenester vaults blade.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Trin 2: Konfigurere samling af legitimationsoplysninger og hente InMage spejder komponenter
7. Vælg din samling bladet gendannelse Services vaults og klik på indstillinger.
8. I **Indstillinger for** > **Kom godt i gang** skal du klikke på **Gendannelse af websteder** > trin 1: **Forberede infrastruktur** > **beskyttelse mål**.
9. Vælg til gendannelse websted i **målet for beskyttelse** , og Vælg Ja, med VMware vSphere Hypervisor. Klik derefter på OK.
10. Klik på Hent du kan hente InMage spejder 8.0.1-filer GA software og registrering nøgle i **spejder konfiguration**. Installationsfiler til alle de påkrævede komponenter er hentet .zip-filen.


## <a name="step-3-install-component-updates"></a>Trin 3: Installere komponentopdateringer

Læs om de seneste [opdateringer](#updates). Du skal installere opdatering-filer på servere i følgende rækkefølge:

1. MODTAGELSE server, hvis der er en
2. Konfiguration af servere
3. Processen servere
3. Master target servere
4. vContinuum servere
5. Kildeserveren (både Windows og Linux Server)

Installere opdateringerne på følgende måde:

1. Hente [opdatere](https://aka.ms/asr-scout-update4) .zip-filen. Denne .zip-filen indeholder følgende filer:

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.GZ
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - UA update4 bit for RHEL5, OL5, OL6, SUSE 10, 11 SUSE: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Udtrække .zip-filer.<br>
3. **For MODTAGELSE server**: Kopiér **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** til MODTAGELSE serveren og udtrække den. **Køre/installere**i mappen udpakkede.<br>
4. **Til konfiguration af server/proces server**: Kopiér **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** til af konfigurationsserveren og process-server. Dobbeltklik for at køre den.<br>
5. **Til Windows mastersider target server**: for at opdatere samlet agent, skal du kopiere **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** til serveren, master mål. Dobbeltklik på den for at køre den. Bemærk, at den samlet agent også gældende for kildeserveren. Du skal installere det på kildeserveren så godt som nævnt senere i denne liste.<br>
7. **For vContinuum serveren**: kopiere **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** til vContinuum-serveren.  Sørg for, at du har lukket guiden vContinuum. Dobbeltklik på filen for at køre den.<br>
8. **For Linux master target server**: Hvis du vil opdatere samlet agent, kopiere **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** til master destinationsadresse-serveren og udtrække den. **Køre/installere**i mappen udpakkede.<br>
9. **Datakilde til Windows server**: Hvis du vil opdatere samlet agent, skal du kopiere **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** til kildeserveren. Dobbeltklik på den for at køre den.<br>
10. **For Linux kildeserveren**: for at opdatere samlet agent, skal du kopiere tilsvarende version af UA fil til Linux-serveren og udtrække den. **Køre/installere**i mappen udpakkede.  Eksempel: For RHEL 6,7 64 bit server skal du kopiere **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** til serveren og udtrække den. **Køre/installere**i mappen udpakkede.

## <a name="step-4-set-up-replication"></a>Trin 4: Konfigurere gentagelse
1. Konfigurere replikering mellem kilden og målrette VMware websteder.
2. Bruge den InMage spejder dokumentation, der hentes vejledning med produktet. Du kan også åbne dokumentationen på følgende måde:

    - [Produktbemærkninger](https://aka.ms/asr-scout-release-notes)
    - [Kompatibilitet matrix](https://aka.ms/asr-scout-cm)
    - [Brugervejledningen](https://aka.ms/asr-scout-user-guide)
    - [Brugervejledning til MODTAGELSE](https://aka.ms/asr-scout-rx-user-guide)
    - [Vejledning til hurtig installation](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Opdateringer

### <a name="azure-site-recovery-scout-801-update-4"></a>Azure websted gendannelse spejder 8.0.1-filer opdatering 4
Spejder opdatering 4 er en kumulativ opdatering. Den har alle rettelserne af update1 indtil update3 og følgende nye fejlrettelser og forbedringer.

**Nye platform support** 

- Har tilføjet understøttelse af vCenter/vSphere 6.0, 6.1 og 6.2
- Understøttelse af er blevet føjet til følgende Linux-operativsystemer
    - Red Hat Enterprise Linux (RHEL) 7.0, 7.1 og 7.2 
    - CentOS 7.0, 7.1 og 7.2
    - Red Hat Enterprise Linux (RHEL) 6.8
    - CentOS 6.8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 64-bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** er pakket med grundlæggende spejder GA pakke **InMage_Scout_Standard_8.0.1 GA.zip**. Som nævnt i [trin 1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault), skal du hente spejder GA pakke fra portalen.

**Fejlrettelser og forbedringer** 

- Forbedret lukning håndtering af til følgende Linux OSes og kopier for at undgå uønskede Synkroniser igen problemer.
    - Red Hat Enterprise Linux (RHEL) 6
    - Oracle Linux (OL) 6
- For Linux, udføre adgang til destinationsmappe tilladelser i samlet agent installation directory er nu begrænset kun til den lokale bruger.
- I Windows indlæst timeout problem mens udstedelse almindelige fordelt konsistens bogmærke på stærkt distribuerede programmer som SQL og del punkt klynger.
- Tilføjede log relaterede rettelse i CX base installer.
- VMware vCLI 6.0 Hent link føjes til Windows Master Target base installer.
- Tilføjet mere kontrol og logfiler til netværk konfigurationer ændringer under failover og DR drills.
- Endda opbevaring oplysninger, der ikke rapporteres til CX.  
- Lydstyrken ændre størrelsen handlingen vContinuum guiden går ned, når kilde lydstyrken Formindsk er der sket for fysisk klynge.
- Klynge beskyttelse mislykkedes med fejlen "Kunne ikke finde disksignaturen" Når klyngedisken er PRDM disk.
- cxps transport server går ned på grund af ud af området undtagelse. 
- Servernavn og IP-kolonner kan nu ændres opslagsnål installationssiden vContinuum guiden.
- Forbedringer af MODTAGELSE API
    - Indeholder fem nyeste tilgængelige almindelige konsistens punkter (kun garantere mærker).
    - Indeholder kapacitet og oplysninger om ledig plads til alle de enheder, der er beskyttet.
    - Indeholder spejder driver tilstand på kildeserveren. 
    
>[AZURE.NOTE] 
>
>- **InMage_Scout_Standard_8.0.1_GA.zip** base pakke er nu opdateret CX base installer **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** og Windows Master destinationswebsteder base installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Brug ny CX- og Windows Master destinationswebsteder GA bit for alle nye installation.
>- Opdater 4 direkte kan anvendes på 8.0.1-filer GA.
>- Server configuration og MODTAGELSE opdateringer kan ikke annulleres, tilbage, når de er anvendt på systemet.

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure websted gendannelse spejder 8.0.1-filer Update 3
Opdater 3 indeholder følgende fejlrettelser og forbedringer:

- Server configuration og MODTAGELSE blev ikke registreret til samling af legitimationsoplysninger gendannelse af websteder, når de er bag proxyen.
- Antallet timer, som gendannelse punkt formålet (frigivne Produktionsordre) ikke er overholdt få opdateres ikke i rapporten tilstand.
- Af konfigurationsserveren ikke synkroniseret med MODTAGELSE, når ESX hardware oplysninger eller oplysninger om netværk indeholder UTF-8 tegn.
- Windows Server 2008 R2 domæne enheder mislykkes til at starte efter genoprettelsen.
- Offlinesynkronisering fungerer ikke som forventet.
- Efter VM (virtual machine) failover, gentagelse par sletningen sidder fast i CX UI for lang tid, og brugerne ikke kan gennemføre failback eller genaktiveres.
- Overordnede afbryder øjebliksbillede handlinger, som er udført af konsistens jobbet er optimeret for at reducere program som SQL-klienter.
- Ydeevnen for værktøjet konsistens (VACP.exe) er blevet forbedret ved at reducere hukommelsesforbruget, der kræves til oprettelse af snapshots i Windows.
- Tryk installere service går ned, når adgangskoden er større end 16 tegn.
- vContinuum er ikke markere og bede om nye vCenter legitimationsoplysninger, når legitimationsoplysningerne, der er ændret.
- På Linux som, overordnet mål cache-styring (cachemgr) ikke hente filer fra processerveren, hvilket resulterer i replikering par (throttling).
- Når den fysiske failover klynge (MSCS) disk rækkefølge ikke er den samme på alle noderne, er replikering ikke angivet for nogle af de klynge enheder.
<br/>Bemærk, at klyngen skal være reprotected for at kunne udnytte denne rettelse.  
- SMTP-funktioner fungerer ikke som forventet, når MODTAGELSE opgraderes fra spejder 7.1 til spejder 8.0.1-filer.
- Er der tilføjet flere statistik log for handlingen rollback registrere den tid, det har truffet for at fuldføre den.
- Har tilføjet understøttelse af Linux-operativsystemer på kildeserveren:
    - Rød Hat Enterprise Linux (RHEL) 6 update 7
    - CentOS 6 opdatere 7
- CX og MODTAGELSE Grænsefladeelementer kan nu vise på beskeden for at parre, der fører dig til bitmaptilstand.
- De følgende sikkerhedsrettelser, der er tilføjet i MODTAGELSE:

**Beskrivelse af problemet**|**Implementering procedurer**
---|---
Godkendelse igennem via parameter uautoriseret adgang|Begrænset adgang til ikke-relevante brugere.
Anmodning om på tværs af websteder forfalskning|Implementeret på siden token koncepter, så der oprettes tilfældigt for hver side. <br/>Med dette, får du vist følgende: <li> Der er kun en enkelt logon forekomst for samme bruger.</li><li>Siden Opdater virker ikke – blive omdirigeret til dashboard.</li>
Skadelig filoverførsel|Begrænsede filer til bestemte udvidelser. Tilladte filtypenavne er: 7z, aiff, afrikansk, avi, bmp, csv, dokument, docx, fla, flv, gif, gz, gzip, jpeg, jpg, loggen, midt, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, PDF-, png, ppt, ppt, pxd, qt, ram, rar, Ressourcestyring, filformatet, rmvb, RTF-, sdc, sitd, swf, sxc, sxw, tjære, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, og zip.
Fast scripting på tværs af websteder | Tilføjede input valideringer.


>[AZURE.NOTE]
>
>-  Alle opdateringerne til gendannelse af websteder er akkumuleret. Opdatering 3 har alle rettelserne af opdatering 1 og 2 for opdatering. Opdater 3 kan anvendes direkte på 8.0.1-filer GA.
>-  Server configuration og MODTAGELSE opdateringer kan ikke annulleres, tilbage, når de er anvendt på systemet.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure websted gendannelse spejder 8.0.1-filer opdatering 2 (Opdater 03 Dec 15)

Løsninger i opdatering 2 omfatter:

- **Konfiguration af server**: løse et problem, der forhindret funktionen 31-dages gratis vha i arbejde, som forventet, når konfigurationsserveren er registreret i gendannelse af websteder.
- **Samlede agent**: løse for et problem opdatering 1, der udløste opdateringen ikke er installeret på master target serveren, når den er blevet opgraderet fra version 8.0 til 8.0.1-filer.


### <a name="azure-site-recovery-scout-801-update-1"></a>Azure websted gendannelse spejder 8.0.1-filer opdatering 1

Opdatering 1 omfatter følgende fejlrettelser og nye funktioner:

- 31 dage af gratis beskyttelse per server-forekomst. Dette gør det muligt at teste funktionerne eller konfigurere en test af koncept.
    - Alle handlinger på serveren, herunder failover og failback, er gratis for de første 31 dage begyndende fra det tidspunkt, hvor en server, der først er beskyttet med websted gendannelse spejder.
    - Fra 32nd dag og derefter skal du betale hver beskyttet server på et standard forekomst DISKONTO Azure gendannelse af websteder beskyttelse til en kunde-ejes websted.
    - Antallet af beskyttet servere, der betaler i øjeblikket findes når som helst, på siden Dashboard i samling af legitimationsoplysninger Azure gendannelse af websteder.
- Support, der er tilføjet for vSphere kommandolinjen (vCLI) 5,5 opdatering 2.
- Understøttelse af er føjet til Linux-operativsystemer på kildeserveren:
    - RHEL 6 opdatere 6
    - RHEL 5 opdatere 11
    - CentOS 6 opdatering 6
    - CentOS 5 opdatering 11
- Fejldatabase løser højde for følgende:
    - Samling af legitimationsoplysninger registrering mislykkes af server configuration eller MODTAGELSE server.
    - Klynge enheder vises ikke som forventet, når grupperet virtuelle maskiner er reprotected, når de CV.
    - Failback mislykkes, når master target serveren er placeret på en anden ESXi server fra lokale fremstilling virtuelle computere.
    - Konfiguration af filtilladelser ændres, når du har opgraderet til 8.0.1-filer, som påvirker beskyttelsen og handlinger.
    - Grænseværdi for ny synkronisering, ikke er tvungne som forventet, som fører til inkonsekvent gentagelse funktionsmåde.
    - Indstillingerne for frigivne Produktionsordre vises ikke korrekt i grænsefladen konfiguration server. Værdien for dekomprimerede data viser forkert komprimeret værdien.
    -  Handlingen Fjern slettes ikke som forventet i guiden vContinuum, og gentagelse slettet ikke fra grænsefladen konfiguration server.
    -  Disken er i guiden vContinuum ikke er valgt automatisk, når du klikker på **Detaljer** i visningen disk under beskyttelse af MSCS virtuelle maskiner.
    - Under scenariet fysiske til virtuelle (P2V) er ikke påkrævet HP-tjenester, som CIMnotify og CqMgHost, flyttes til manuel i virtuelt gendannelse. Dette giver yderligere starttidspunktet.
    - Linux virtuelt beskyttelse mislykkes, når der er mere end 26 diske på master destinationsadresse-serveren.

## <a name="next-steps"></a>Næste trin

Stil et spørgsmål, du har på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
