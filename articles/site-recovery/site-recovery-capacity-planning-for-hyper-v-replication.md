<properties
    pageTitle="Køre værktøjet Hyper-V kapacitet planner til gendannelse af websteder | Microsoft Azure"
    description="I denne artikel indeholder en vejledning til brug af værktøjet Hyper-V kapacitet teamplanlægning til Azure gendannelse af websteder"
    services="site-recovery"
    documentationCenter="na"
    authors="rayne-wiselman"
    manager="jwhit"
    editor="" />
<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/12/2016"
    ms.author="raynew" />

# <a name="run-the-hyper-v-capacity-planner-tool-for-site-recovery"></a>Køre værktøjet Hyper-V kapacitet planner til gendannelse af websteder

Som en del af installationen af Azure gendannelse af websteder skal du finde ud af dine krav til båndbredde og gentagelse. Værktøjet Hyper-V kapacitet teamplanlægning for gendannelse af websteder hjælper dig med at finde ud af dine krav til gentagelse og båndbredde til Hyper-V virtuelt gentagelse.


I denne artikel beskrives, hvordan til at køre værktøjet Hyper-V kapacitet teamplanlægning. Dette værktøj, der skal bruges sammen med de andre kapacitet planlægning værktøjer og oplysninger, der er beskrevet i [kapacitet planlægning af gendannelse af websteder](site-recovery-capacity-planner.md).


## <a name="before-you-start"></a>Før du starter

Du kan køre værktøjet på en Hyper-V server eller en klynge node i din primære websted. Sådan køres værktøjet værtsservere Hyper-V skal bruge:

- Operativsystem: Windows Server® 2012 eller Windows Server® 2012 R2
- Hukommelse: 20 MB (minimum)
- CPU: 5 procent omkostninger (minimum)
- Diskplads: 5 MB (minimum)

Før du kører værktøjet skal du forberede det primære websted. Hvis du replikering mellem to lokale websteder og du vil kontrollere båndbredde, skal du forberede en replikeringsserver.


## <a name="step-1-prepare-the-primary-site"></a>Trin 1: Forbered det primære websted
1. Oprette en liste over alle de Hyper-V virtuelle maskiner, du vil gentage og de Hyper-V hosts/klynger som de er placeret på det primære websted. Værktøjet kan køres, hver gang for flere enkeltstående værter eller en enkelt klynge, men ikke begge dele sammen. Det skal også køre separat for hver operativsystem, så du skal samle og Bemærk serverne Hyper-V på følgende måde:

  - Windows Server® 2012 enkeltstående servere
  - Windows Server® 2012 klynger
  - Windows Server® 2012 R2 enkeltstående servere
  - Windows Server® 2012 R2 klynger

3. Aktivere fjernadgang til WMI på alle Hyper-V værter og klynger. Kør denne kommando på hver server klynge at sikre dig, at firewallregler og brugertilladelser angives:

        netsh firewall set service RemoteAdmin enable

5. Aktivere overvågning på servere og klynger, på følgende måde:

  - Åbn Windows Firewall med **Avanceret sikkerhed** snap-in, og derefter aktivere følgende indgående regler: **COM + netværksadgang (DCOM-IN)** og alle regler i **hændelseslog fjernadministration gruppe**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Trin 2: Forberede en replikeringsserver (lokalt på lokale replikering)

Du behøver ikke at gøre dette, hvis du replikeres til Azure.

Vi anbefaler, at du konfigurerer en enkelt Hyper-V vært som en gendannelse server så en dummy VM kan replikeres til den for at kontrollere båndbredde.  Du kan springe dette, men du kunne ikke måle båndbredde, medmindre du gør det.

1. Hvis du ønsker at bruge en klyngenode, som replikaen konfigurere Hyper-V replika broker:

    - Åbn **Failoverklyngestyring**i **Server Manager**.
    - Oprette forbindelse til klyngen, fremhæve klyngenavnet og klikke på **Handlinger** > **Konfigurere rolle** at åbne guiden høj tilgængelighed.
    - Klik på **Hyper-V replika Broker**i **Vælg rolle** . Angive et **NetBIOS-navn** og **IP-adresse** , der skal bruges som forbindelsespunktet til den klynge (kaldet en klient adgangspunkt) i guiden. **Hyper-V replika Broker** vil blive konfigureret, hvilket resulterer i et navn til client access punkt, som du skal være opmærksom på.
    - Kontrollér, rollen Hyper-V replika Broker korrekt er online og kan mislykkes mellem alle noder af klyngen. Gør dette, højreklik på rollen, peg på **flytte**, og klik derefter på **Vælg Node**. Vælg en node > **OK**.
    - Hvis du bruger certifikat-baseret godkendelse, skal du kontrollere hver klyngenode og klienten adgangspunkt alle har det certifikat, der er installeret.
2.  Aktivere en replikeringsserver:

    - Åbn manglende klynge Manager for en klynge, oprette forbindelse til klyngen og klikke på **roller** > Vælg rolle > Indstillinger for **Gentagelse**> **aktivere denne klynge som en replikeringsserver**. Bemærk, at hvis du bruger en klynge som replikaen skal du have rollen Hyper-V replika Broker Præsenter på klynge i det primære websted.
    - Åbn Hyper-V Manager for en enkeltstående server. Klik på **Hyper-V indstillinger** for den server, du vil aktivere i ruden **Handlinger** , og klik på **Gentagelse konfiguration** **Aktiver computeren som en replikeringsserver**.
3. Konfigurere godkendelse:

    - Vælg, hvordan til at godkende den primære server og godkendelse porte i **godkendelse og porte** . Hvis du bruger certifikat skal du klikke på **Vælg certifikat** for at vælge en. Brug Kerberos, hvis de primære og gendannelsesfiler Hyper-V hosts er i det samme domæne, eller der er tillid til domæner. Bruge certifikater til forskellige domæner eller en arbejdsgruppe installation.
    - Tillade **alle** godkendte (primær) server til at sende gentagelse data til denne replikeringsserver i sektionen **godkendelse og lagerplads** . Klik på **OK** eller **Anvend**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

    - Køre **netsh http Vis servicestate** for at kontrollere, at lytteren kører for den protokol/port, du har angivet:  
4. Konfigurere firewalls. Under installationen af Hyper-V oprettes firewallregler for at tillade trafik på standard porte (HTTPS på 443, Kerberos på 80). Aktivere disse regler på følgende måde:

        - Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
        - Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
        - Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
        - Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## <a name="step-3-run-the-capacity-planner-tool"></a>Trin 3: Køre værktøjet kapacitet teamplanlægning

Når du har forberedt webstedet primære og konfigurere en gendannelse server kan du køre værktøjet.

1. [Hente](https://www.microsoft.com/download/details.aspx?id=39057) værktøjet fra Microsoft Download Center.
2. Køre værktøjet fra en af de primære servere (eller en af noderne fra den primære klynge). Højreklik på .exe-filen, og vælg derefter **Kør som administrator**.
3. Angiv hvor længe du vil indsamle data ind, **før du går i gang** . Vi anbefaler, at du køre værktøjet under fremstilling timer at sikre, at dataene er repræsentant. Hvis du kun forsøg på at kontrollere netværksforbindelsen, kan du indsamle til et minut kun.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Angiv servernavnet eller fulde Domænenavn for en enkeltstående vært i **Primary site detaljer** , eller til en klynge angive det fuldstændige Domænenavn for klienten acceptere punkt, klyngenavn eller en hvilken som helst node i klyngen, og klik derefter på **Næste**. Værktøjet registrerer automatisk navnet på den kører på serveren. Værktøjet opfanger VM'er, der kan overvåges for de angivne servere.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. **Replika websted** oplysninger Hvis du replikeres til Azure, eller hvis du er replikering til en sekundær datacenter, og du ikke har konfigureret en replikeringsserver, Vælg **springe test, der involverer replika websted**. Hvis du replikering til en sekundær datacenter, og du har konfigureret en replika type i det fulde Domænenavn for enkeltstående server eller klient adgangspunkt for klynge i **servernavn (eller) Hyper-V replika Broker KNOP**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Aktivere **springe de test, der involverer udvidede replika websted**i **Udvidede replika detaljer** i. De understøttes ikke af gendannelse af websteder.
7. I **Vælg FOS til replikeres** værktøjerne opretter forbindelse til serveren eller klynge og viser FOS og disk, der kører på den primære server, i overensstemmelse med indstillingerne du har angivet på siden **Oplysninger om primære websted** . Bemærk, at VM'er, der er allerede aktiveret for gentagelse, eller som ikke kører vises ikke. Vælg de FOS, hvor du vil indsamle målepunkter. Vælge de virtuelle harddiske automatisk indsamler data til FOS for.
9. Hvis du har konfigureret en replikeringsserver eller en klynge, i **Network information** angive tilnærmet WAN båndbredden du tror, der skal bruges mellem primær og replika websteder og vælge certifikater, hvis du har konfigureret certifikatgodkendelse.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. Kontrollér indstillingerne i **Oversigt** , og klik på **Næste** for at begynde at indsamle målepunkter. Værktøjet status og status vises på siden **Beregne kapacitet** . Når de kører værktøj er afsluttet klikker på **Vis rapport** for at gennemgå output. Som standard gemmes rapporter og logfiler i **%systemdrive%\Users\Public\Documents\Capacity teamplanlægning**.

    ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## <a name="step-4-interpret-the-results"></a>Trin 4: Fortolke resultaterne
Her er nogle vigtige målepunkter. Du kan ignorere målepunkter, som ikke er angivet her. De er ikke relevante for gendannelse af websteder.

### <a name="on-premises-to-on-premises-replication"></a>Lokalt på lokale replikering
  - Virkningen af replikering på værten primære Beregn, hukommelse
  - Virkningen af replikering på primært, gendannelse hosts disk lagerplads, IOP'ER
  - Samlede båndbredde, der kræves for delta gentagelse (Mbps)
  - Observerede netværksbåndbredde mellem den primære vært og værten gendannelse (Mbps)
  - Forslag til ideel antallet af aktive parallelle pengeoverførsler mellem de to værter/klynger

### <a name="on-premises-to-azure-replication"></a>Lokalt på Azure replikering
  - Virkningen af replikering på værten primære Beregn, hukommelse
  - Virkningen af replikering på værten primære lagerplads diskplads, IOP'ER
  - Samlede båndbredde, der kræves for delta gentagelse (Mbps)

## <a name="more-resources"></a>Flere ressourcer

- Du kan finde detaljerede oplysninger om værktøjet læse dokumentet, der følger med værktøjet overførslen.
- Se en gennemgang af værktøjet på Keith Mayer [TechNet blog](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
- [Få vist resultaterne](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) af vores performance-test for lokalt på lokale Hyper-V replikering



## <a name="next-steps"></a>Næste trin

Når du er færdig med kapacitetsplanlægning kan du begynde at implementere gendannelse af websteder:

- [Gentage Hyper-V FOS i VMM skyer til Azure](site-recovery-vmm-to-azure.md)
- [Gentage Hyper-V FOS (uden VMM) til Azure](site-recovery-hyper-v-site-to-azure.md)
- [Gentage Hyper-V FOS mellem VMM websteder](site-recovery-vmm-to-vmm.md)
- [Gentage Hyper-V FOS mellem VMM websteder med SAN](site-recovery-vmm-san.md)
- [Gentage hyper-V FOS på enkelt VMM server](site-recovery-single-vmm.md)