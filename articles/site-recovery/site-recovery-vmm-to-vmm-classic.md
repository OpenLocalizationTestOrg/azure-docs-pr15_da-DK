<properties
    pageTitle="Gentage Hyper-V virtuelle maskiner i VMM skyer til en sekundær VMM websted | Microsoft Azure"
    description="I denne artikel beskrives, hvordan du overfører Hyper-V FOS i VMM skyer til en sekundær VMM websted med Azure gendannelse af websteder."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Gentage Hyper-V virtuelle maskiner i VMM skyer til en sekundær VMM websted

> [AZURE.SELECTOR]
- [Azure-portalen](site-recovery-vmm-to-vmm.md)
- [Klassisk portal](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - ressourcestyring](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Gendannelse af websteder Azure service bidrager til din business løbende og genoprettelse efter genoprettelse (BCDR) strategi ved orchestrating gentagelse, failover og gendannelse af virtuelle maskiner og fysiske servere. Computere kan replikeres til Azure eller til et sekundært lokalt datacenter. En hurtig oversigt over læse [Hvad er Azure gendannelse af websteder?](site-recovery-overview.md)

## <a name="overview"></a>Oversigt

I denne artikel beskrives, hvordan du overfører Hyper-V virtuelle maskiner på Hyper-V hostservere, der administreres i VMM skyer sekundær VMM-websted ved hjælp af Azure gendannelse af websteder.

I artiklen omfatter forudsætninger, hvordan du kan konfigurere en samling af legitimationsoplysninger gendannelse af websteder, installere Azure websted gendannelse udbyder på kilde og målrette VMM servere, registrere serverne i samling af legitimationsoplysninger, konfigurere indstillingerne for dokumentbeskyttelse for VMM skyer og derefter aktivere beskyttelse af Hyper-V FOS. Færdigt ved at teste den sekundære at sikre, at alt fungerer som forventet.

Sende en hvilken som helst kommentarer eller spørgsmål nederst i denne artikel eller på [Azure gendannelse Services-forummet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Arkitektur

Billedet herunder viser de forskellige kommunikationskanaler og porte, der bruges af Azure gendannelse af websteder til organisering og gentagelse

![E2E topologi](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Før du starter

Sørg for, at du har disse forudsætninger sted:

**Forudsætninger** | **Detaljer**
--- | ---
**Azure**| Du har brug for en [Microsoft Azure](https://azure.microsoft.com/) -konto. Du kan starte med en [gratis prøveversion](https://azure.microsoft.com/pricing/free-trial/). Du kan [Få mere at vide](https://azure.microsoft.com/pricing/details/site-recovery/) om gendannelse af websteder priser.
**VMM** | Du skal mindst én VMM server.<br/><br/>VMM serveren skal køre mindst System Center 2012 SP1 med de nyeste akkumulerede opdateringer.<br/><br/>Hvis du vil konfigurere beskyttelse med en enkelt VMM server, skal du mindst to skyer konfigureret på serveren.<br/><br/>Hvis du vil installere beskyttelse med to VMM servere, skal skal hver server have mindst én skyen konfigureret på den primære VMM server, du vil beskytte, og én sky konfigureret på den sekundære VMM-server, du vil bruge til beskyttelse og gendannelse<br/><br/>Alle VMM skyer skal have Hyper-V egenskab profilen.<br/><br/>Skyen kilde, du vil beskytte skal indeholde en eller flere VMM host grupper.<br/><br/>Få flere oplysninger om konfiguration af VMM skyer i [Gennemgang: oprette private skyer med System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) på Keith Mayer blog.
**Hyper-V** | Du har brug for en eller flere Hyper-V værtsservere i de primære og sekundære VMM host grupper, og en eller flere virtuelle maskiner på hver Hyper-V host server.<br/><br/>Host- og destinationswebsteder Hyper-V servere skal køre mindst Windows Server 2012 med Hyper-V-rollen og har installeret de seneste opdateringer.<br/><br/>En hvilken som helst Hyper-V-server, der indeholder FOS, du vil beskytte skal være placeret i en VMM sky.<br/><br/>Hvis du kører Hyper-V i en klynge, skal du være opmærksom på, klynge broker ikke er oprettet automatisk, hvis du har en statisk IP-adresse-baseret klynge. Du skal konfigurere klynge broker manuelt. Du kan [Få mere at vide](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) i Aidan Finn blogindlæg.
**Kort over netværk** | Du kan konfigurere netværk tilknytning til Sørg for, at replikerede virtuelle maskiner optimalt placeres på sekundær Hyper-V værtsservere efter failover, og at de kan oprette forbindelse til relevante VM netværk. Hvis du ikke konfigurerer netværk tilknytning, forbindelse ikke replika FOS til en hvilken som helst netværk efter failover.<br/><br/>Hvis du vil konfigurere netværk tilknytning under installationen, Sørg for, at de virtuelle maskiner på Hyper-V host kildeserveren er tilsluttet et VMM VM netværk. Dette netværk skal være kædet sammen med et logisk netværk, der er knyttet til skyen. < br /<br/>Target skyen på den sekundære VMM-server, du bruger til gendannelse skal have et tilsvarende VM netværk, der er konfigureret, og den tur. bør være knyttet til en tilsvarende logisk netværk, der er knyttet til den destinationsadresse-sky.<br/><br/>Du kan [Få mere at vide](site-recovery-network-mapping.md) om kort over netværk.
**Lagerplads tilknytning** | Når du gentage en virtuel maskine på en kilde Hyper-V host-server til en destination Hyper-V host-server, gemmes replikerede data som standard på standardplaceringen, der er angivet for den destinationsadresse Hyper-V vært i Hyper-V Manager. Få mere kontrol over hvor replikerede data er gemt, kan du konfigurere lagerplads tilknytning<br/><br/> For at konfigurere tilknytning af lagerplads, skal du konfigurere lagerplads klassificeringer på kilde- og målrette VMM servere, inden du starter installation. [Få mere at vide](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Trin 1: Oprette en samling af legitimationsoplysninger gendannelse af websteder

1. Log på [Portalen til administration](https://portal.azure.com) fra serveren VMM du vil registrere.

2. Udvid **Datatjenester** > **Gendannelsestjenester** , og klik på **Websted gendannelse samling**.

3. Klik på **Opret et nyt** > **Hurtig oprettelse**.

4. Angiv et fuldt navn til at identificere samling af legitimationsoplysninger i **navnet**.

5. Vælg det geografiske område for samling af legitimationsoplysninger i **område** . Sådan kontrollerer du se understøttede områder geografiske tilgængelighed i [Azure websted gendannelse priser detaljer](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Klik på **Opret samling**.

    ![Oprette samling](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

Se på statuslinjen, samling af legitimationsoplysninger blev oprettet. Samling af legitimationsoplysninger, vises som **aktiv** på hovedsiden gendannelsestjenester.

## <a name="step-2-generate-a-vault-registration-key"></a>Trin 2: Opret en samling af legitimationsoplysninger registreringsnøgle

Oprette en registreringsnøgle i samling af legitimationsoplysninger. Når du henter Azure websted gendannelse udbyder og installere den på serveren, der VMM, skal du bruge denne tast til at registrere VMM serveren i samling af legitimationsoplysninger.

1. Klik på samling af legitimationsoplysninger for at åbne siden Hurtig Start på siden **Gendannelsestjenester** . Også du kan åbne Hurtig Start når som helst ved hjælp af ikonet.

    ![Ikon for Hurtig Start](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. Vælg **mellem to lokale VMM steder**på rullelisten.
3. Klik på **Generer registrering vigtige filer**i **Forberede VMM servere**. Filen vigtige genereres automatisk og er gyldige for 5 dage, efter den er oprettet. Hvis du ikke har adgang til portalen Azure fra serveren VMM skal du kopiere denne fil på serveren.

    ![Registreringsnøgle](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Trin 3: Installer Azure websted gendannelse udbyder

4. På siden **Hurtig Start** i **forberede VMM servere**, skal du klikke på **Hent Microsoft Azure websted gendannelse Provider til installation på VMM servere** for at hente den nyeste version af installationsfilen udbyder.

2. Køre denne fil på VMM kildeserveren.

    >[AZURE.NOTE] Hvis VMM er implementeret i en klynge, og du installerer provideren for første gang installere det på en aktive node og fuldføre installationen for at registrere VMM serveren i samling af legitimationsoplysninger. Installer udbyderen på de andre noder. Bemærk, at hvis du opgraderer udbyderen du skal opgradere på alle noder, fordi de skal alle køre den samme version som udbyder.

3. Installationsprogrammet understøtter nogle **Kontrol af foreløbig krav** og anmodninger om tilladelse til at stoppe tjenesten VMM for at starte installationen af udbyder. Tjenesten VMM genstartes automatisk, når installationen er færdig. Hvis du installerer på en VMM klynge, bliver du bedt om at stoppe rollen klynge.

4. I **Microsoft Update** kan du vælge i efter opdateringer. Når denne indstilling er aktiveret udbyder opdateringer installeres ifølge din Microsoft Update-politik.

    ![Microsoft-opdateringer](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. Installationsplacering er angivet til ** <SystemDrive>\Programmer\Microsoft System Center 2012 R2\Virtual maskine Manager\bin**. Klik på knappen Installer for at starte installationen provideren.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Klik på **Registrer** for at registrere serveren i samling af legitimationsoplysninger, når udbyderen, der er installeret.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
9. Bekræft navnet på den samling, hvor serveren, der skal registreres i **samling navn**. Klik på *Næste*.

    ![Registrering af Server](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Angiv, hvordan den udbyder, der kører på serveren VMM der oprettes forbindelse til internettet i **Internetforbindelse** . Vælg **Opret forbindelse til eksisterende proxyindstillinger** bruge Internet standardindstillingerne for forbindelsen konfigureret på serveren.

    ![Internet-indstillinger](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Hvis du vil bruge en brugerdefineret proxy skal du konfigurere det, før du installerer udbyderen. Når du konfigurerer brugerdefinerede proxyindstillinger kører en test for at kontrollere proxy forbindelsen.
    - Hvis du ønsker at bruge en brugerdefineret proxy, eller din standardproxy kræver godkendelse, skal du angive proxy oplysninger, herunder proxyadresse og port.
    - Følgende URL-adresser skal være tilgængelige på serveren VMM og Hyper-v-værter
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Tillad, at de IP-adresser, der er beskrevet i [Azure Datacenter IP-områder](https://www.microsoft.com/download/confirmation.aspx?id=41653) og HTTPS (443) protocol. Du skulle hvid liste IP-områder af det Azure område, du vil bruge, og som Vest US.
    - Hvis du bruger en brugerdefineret proxy oprettes VMM RunAs-konto (DRAProxyAccount) automatisk ved hjælp af de angivne proxylegitimationsoplysninger. Konfigurere proxyserveren, så denne konto kan godkende korrekt. Kontoindstillinger VMM RunAs kan redigeres i konsollen VMM. Åbn arbejdsområdet **Indstillinger for** at gøre dette, udvid **sikkerhed**, klik på **Kør som konti**, og derefter ændre adgangskoden til DRAProxyAccount. Du skal genstarte tjenesten VMM, så indstillingen træder i kraft.


8. I **Registreringsnøgle**, skal du vælge nøglen, som du har downloadet fra Azure gendannelse af websteder og kopieret til VMM serveren.


10.  Krypteringsindstillingen bruges kun, når du replikering Hyper-V FOS i VMM skyer til Azure. Hvis du replikeres til en sekundær websted bruges det ikke.

11.  I **servernavn**skal du angive et fuldt navn til at identificere VMM serveren i samling af legitimationsoplysninger. Angiv VMM klynge Rollenavnet i en klynge konfiguration.
12.  **Synkroniser skyen metadata** vælge om du vil synkronisere metadataene for alle skyer på serveren VMM med samling af legitimationsoplysninger. Denne handling skal kun udføres én gang på hver server. Hvis du ikke vil synkronisere alle skyer, kan du lade denne indstilling ikke er markeret og synkronisere hver skyen enkeltvis i skyen-egenskaber i konsollen VMM.

13.  Klik på **Næste** for at fuldføre processen. Efter registreringen hentes metadata fra serveren VMM ved Azure gendannelse af websteder. Serveren, der vises i **VMM servere** > **servere** i samling af legitimationsoplysninger.

    ![Servere](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Kommandolinjen installation

Azure websted gendannelse udbyder kan også installeres fra kommandolinjen. Denne metode kan bruges til at installere udbyderen på en Server CORE til Windows Server 2012 R2.

1. Hente installation fil- og registreringsoplysninger nøglen Provider til en mappe. For eksempel C:\ASR.
2. Stoppe tjenesten System Center Virtual Machine Manager
3. Udtrække udbyder installationsprogrammet ved at køre disse kommandoer fra en kommandoprompt med **administratorrettigheder** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Du kan installere udbyderen ved at køre:

        C:\ASR> setupdr.exe /i

5. Registrere provideren ved at køre:

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Hvor er parametrene:

 - **/Credentials**: obligatorisk parameter, der angiver den placering, hvor den vigtige registreringsdatabasefil er placeret  
 - **/FriendlyName**: obligatorisk parameter for navnet på den server, Hyper-V-vært, der vises i portalen Azure gendannelse af websteder.
 - **/EncryptionEnabled**: valgfri Parameter, som du skal bruge kun i VMM til Azure Scenario, hvis du har brug for kryptering af din virtuelle maskiner på på resten i Azure. Skal du sikre, at navnet på den fil, du angiver har filtypenavnet **.pfx** .
 - **/proxyAddress**: valgfri parameter, der angiver adressen på proxyserveren.
 - **/proxyport**: valgfri parameter, der angiver porten af proxy-server.
 - **/proxyUsername**: valgfri parameter, der angiver brugernavnet Proxy (hvis proxy kræver godkendelse).
 - **/proxyPassword**: valgfri parameter, der angiver adgangskoden til godkendelse med proxy-server (hvis proxy kræver godkendelse).  

## <a name="step-4-configure-cloud-protection-settings"></a>Trin 4: Konfigurere skyen indstillingerne for dokumentbeskyttelse

Når VMM servere er registreret, kan du konfigurere indstillingerne for dokumentbeskyttelse skyen. Hvis du har aktiveret indstillingen **Synkroniser skyen data med samling af legitimationsoplysninger** , da du installerede udbyderen, der vises så alle skyer på serveren VMM under fanen **Beskyttede elementer** i samling af legitimationsoplysninger. Hvis du ikke kan synkronisere en bestemt sky med Azure gendannelse af websteder på siden skyen egenskaber i konsollen VMM under fanen **Generelt** .

![Publicerede skyen](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. Klik på **Konfigurer beskyttelse for VMM skyer**på siden Hurtig Start.
2. Vælg fanen **VMM skyer** i skyen, du vil konfigurere og gå til fanen **konfiguration** .
3. Vælg **VMM**i **mål**.
4. Vælg den på stedet VMM server, der administrerer i skyen, du vil bruge til gendannelse **destinationsplacering**.
4. Vælg den destinationsadresse sky, du vil bruge til failover af virtuelle maskiner i skyen kilde i **Target skyen**. Bemærk, at:

    - Vi anbefaler, at du vælger en destination sky, der opfylder gendannelse krav til de virtuelle maskiner, du vil beskytte.
    - En sky, som kun kan tilhøre et par enkelt skyen – enten som en primær eller en sky, som destination.

5. **Kopiere hyppighed**, Angiv hvor ofte dataene skal synkroniseres mellem 5he kilde- og destinationswebsteder placeringer. Bemærk, at denne indstilling kun er relevant, når værten Hyper-V kører Windows Server 2012 R2. For andre servere bruges en standardindstilling på fem minutter.
6. Angiv om du vil oprette yderligere betroet punkter i **Yderligere betroet punkter**. Standardværdien nul angiver, at kun det seneste gendannelsespunkt for en primær virtuel maskine er gemt på en host replikeringsserver. Bemærk, at aktivere flere gendannelse punkter kræver ekstra lagerplads til snapshots, der er gemt i hvert gendannelsespunkt. Som standard oprettes gendannelse punkter hver time, så hvert gendannelsespunkt indeholder en time værd data. Værdien gendannelse punkt, som du tildeler til den virtuelle maskine i konsollen VMM skal ikke være mindre end værdien, som du tildeler i konsollen Azure gendannelse af websteder.
7. Angiv, hvordan ofte til at oprette programmet ensartet øjebliksbilleder i **hyppigheden for programmet ensartet snapshots**. Hyper-V anvender to typer af snapshots – et standard øjebliksbillede, der indeholder en trinvis øjebliksbillede af hele virtuelt og et program-ensartet øjebliksbillede, der tager et punkt-in-time øjebliksbillede af programmet dataene i den virtuelle maskine. Program-ensartet snapshots Brug skygge tjenesten VSS (Volume Copy) til at sikre, at programmer er i en ensartet tilstand, når snapshot er taget. Bemærk, at hvis du aktiverer programmet ensartet snapshots, vil det påvirke ydeevnen for programmer, der kører på kilde virtuelle maskiner. Sørg for, at den værdi, du angiver er mindre end antallet yderligere betroet punkter, du konfigurerer.

    ![Konfigurere indstillingerne for dokumentbeskyttelse](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. I **dataoverførsel komprimering**, skal du angive om replikerede data, der overføres skal komprimeres.
9. Angiv, hvordan trafik skal godkendes mellem primær og gendannelse Hyper-V host-servere i- **godkendelse**. Vælg HTTPS, medmindre du har en arbejde Kerberos-miljø konfigureret til. Certifikater til HTTPS-godkendelse konfigureres automatisk af Azure gendannelse af websteder. Der kræves ingen manuel konfiguration. Hvis du markerer Kerberos, bruges en Kerberos brugertilladelse til fælles godkendelse af host-serverne. Som standard åbnes port 8083 og 8084 (for certifikater) i Windows Firewall på værtsservere Hyper-V. Bemærk, at denne indstilling er kun relevant for Hyper-V hostservere, der kører på Windows Server 2012 R2.
10. Redigere det portnummer, hvorpå kilde- og destinationswebsteder host computere lytte til gentagelse trafik i **Port**. For eksempel kan du redigere denne indstilling, hvis du vil anvende Quality of Service (QoS) netværksbåndbredde (throttling) for gentagelse trafik. Kontrollér, at port ikke bruges af andre programmer og at den er åben i firewall-indstillinger.
11. Angiv, hvordan den indledende replikering af data fra kilder til destinationen placeringer håndteres, før normalt replikering starter, i **replikering metode**:

    - **Via netværk**– kopiering af data via netværket kan være tidskrævende og ressource-intensivt. Vi anbefaler, at du bruger denne indstilling, hvis skyen indeholder virtuelle maskiner med relativt lille virtuelle harddiske, og det primære websted er forbundet til det websted, sekundær via en forbindelse med bred båndbredde. Du kan angive, at kopien skal starte med det samme, eller Vælg et tidspunkt. Hvis du bruger netværk gentagelse, anbefaler vi, at du planlægger det belastet.
    - **Offline**– denne metode angiver, at den første replikering kan udføres ved hjælp af eksterne medier. Det er praktisk, hvis du vil undgå er forringet i netværksydeevne eller til geografisk eksterne steder. Hvis du vil bruge denne metode kan du angive eksportplaceringen på kilde skyen, og Importér placering på target skyen. Når du aktiverer beskyttelse til en virtuel maskine, kopieres den virtuelle harddisk til den angivne Eksportplacering. Du sende det til destinationswebstedet og kopiere den til placeringen, importere. De importerede oplysninger kopieres automatisk til replika virtuelle computere.

12. Vælg **Slet replika Virtual Machine** til at angive, at den replika virtuelle maskine skal slettes, hvis du holder op med at beskytte den virtuelle maskine ved at vælge indstillingen **Slet beskyttelse til den virtuelle maskine** under fanen virtuelle maskiner af egenskaberne skyen. Med denne indstilling er aktiveret, når du deaktiverer beskyttelse virtual machine er fjernet fra Azure gendannelse af websteder, indstillingerne for gendannelse af websteder for den virtuelle maskine fjernes i konsollen VMM, og replikaen slettes.

    ![Konfigurere indstillingerne for dokumentbeskyttelse](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Når du har gemt indstillingerne for et job oprettes og kan overvåges på fanen **job** . Alle Hyper-V værtsservere i VMM kilde skyen konfigureres til replikering. Skyen indstillinger kan ændres under fanen **Konfigurer** . Hvis du vil ændre destinationen placering eller destination skyen skal du fjerne skyen konfigurationen og derefter omkonfigurere i skyen.

### <a name="prepare-for-offline-initial-replication"></a>Forberede offline indledende gentagelse

Du skal gøre følgende handlinger til at forberede til første replikering offline:

- På kildeserveren, skal du angive stien til placeringen hvorfra dataeksporten skal foregå. Tildele fuld kontrol NTFS og dele tilladelser til tjenesten VMM på den eksporterede sti. På serveren, skal du angive stien til placeringen hvorfra dataimporten skal forekomme. Tildele de samme tilladelser på denne importsti.
- Hvis import eller eksport stien er delt, skal du tildele Administrator, Superbrugere, Udskriv Operator eller Server Operator gruppemedlemskab til VMM service-kontoen på fjerncomputeren, hvor den delte er placeret.
- Hvis du bruger en hvilken som helst Kør som konti til at tilføje hosts, om import og eksport af stier, tildele læse- og skrivetilladelser til Kør som kontiene i VMM.
- Import og eksport af aktier findes ikke på en hvilken som helst computer, der bruges som Hyper-V host server, fordi tilbagekobling konfiguration ikke understøttes af Hyper-V.
- I Active Directory, på hver Hyper-V begrænset host server, der indeholder virtuelle maskiner, du vil beskytte, aktivere og konfigurere delegering at have tillid til de eksterne computere, som import og eksport af stierne er placeret, som følger:
    1. Åbn på domænecontrolleren, **Active Directory-brugere og computere**.
    2. Klik på **domænenavn**i konsoltræet > **computere**.
    3. Højreklik på servernavnet Hyper-V host > **Egenskaber**.
    4. Klik på fanen **delegering** T**rust til delegering til angivne services på denne computer**.
    5. Klik på **Brug en hvilken som helst godkendelse protocol**.
    6. Klik på **Tilføj** > **brugere og computere**.
    7. Skriv navnet på den computer, hvor den eksporterede sti > **OK**. På listen over tilgængelige tjenester, holde CTRL-tasten nede og klikke på **cifs** > **OK**. Gentag for navnet på den computer, der er vært Importér stien. Gentag efter behov for yderligere Hyper-V værtsservere.

## <a name="step-5-configure-network-mapping"></a>Trin 5: Konfigurere netværk tilknytning
1. Klik på **kort netværk**på siden Hurtig Start.
2. Vælg kildeserveren VMM hvorfra du vil tilknytte netværk, og derefter target VMM server, der skal knyttes netværkene. Der vises på listen over datakilder netværk og deres tilknyttede target netværk. En tom værdi vises for netværk, der ikke er i øjeblikket er knyttet.
3. Vælg et netværk i **netværk på kilde** > **kort**. Tjenesten registrerer VM netværk på serveren og viser dem. Klik på oplysningsikonet ud for navnene kilde- og destinationswebsteder netværk til at få vist undernet for hvert netværk.

    ![Konfigurer tilknytning af netværk](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. Vælg en af VM netværkene target VMM server i dialogboksen.

    ![Vælg en destination netværk](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Når du vælger et destinationsprogram netværk, vises den beskyttede sky, der bruger kilde netværket. Tilgængelige mål-netværk, der er knyttet til den sky, der bruges til beskyttelse vises også. Vi anbefaler, at du vælger et destinationsprogram netværk, der er tilgængelige for alle skyer, som du bruger til beskyttelse. Eller du kan også gå til VMM serveren og redigere egenskaberne for skyen for at tilføje det logiske netværk, der svarer til det vm netværk, du vil vælge.
6. Klik på markeringen i afkrydsningsfeltet for at fuldføre tilknytningsprocessen. Et job begynder at spore tidsplanens status tilknytning. Du kan se det på fanen **job** .


## <a name="step-6-configure-storage-mapping"></a>Trin 6: Konfigurer tilknytning af lager
Når du gentage en virtuel maskine på en kilde Hyper-V host-server til en destination Hyper-V host-server, gemmes replikerede data som standard på standardplaceringen, der er angivet for den destinationsadresse Hyper-V vært i Hyper-V Manager. Få mere kontrol over hvor replikerede data er gemt, kan du konfigurere lagerplads tilknytninger på følgende måde:



1. Definere lagerplads klassificeringer på både kilde- og destinationswebsteder VMM-servere. [Få mere at vide](https://technet.microsoft.com/library/gg610685.aspx). Klassificeringer skal være tilgængelige for værtsservere Hyper-V i kilde- og destinationswebsteder skyer. Klassificeringer behøver ikke at have samme type lagerplads. For eksempel kan du knytte en kilde klassifikation, der indeholder små og mellemstore virksomheder aktier til en destination klassifikation, der indeholder CSVs.
2. Du kan oprette tilknytninger, når klassificeringer er på plads. Sådan gør du på siden **Hurtig Start** > **tilknytte lagerplads**.
3. Klik på fanen **lagerplads** > **tilknytte lagerplads klassificeringer**.
4. Vælg klassificeringer på kilde under fanen **tilknytte lagerplads klassificeringer** og målrette VMM servere. Gemme dine indstillinger.

    ![Vælg en destination netværk](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Trin 7: Aktivér virtuelt beskyttelse
Når servere, skyer og netværk er konfigureret korrekt, kan du aktivere beskyttelse af virtuelle maskiner i skyen.

1. Klik på fanen **virtuelle maskiner** i skyen, hvor den virtuelle maskine er placeret **Aktiver beskyttelse** > **Tilføj virtuelle computere**.
2. På listen over virtuelle maskiner i skyen, Vælg det, du vil beskytte.

    ![Aktiver virtuelt beskyttelse](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Registrere status for handlingen Aktivér beskyttelse i fanen **job** , herunder den første replikering. Når du har kørt færdiggøre beskyttelse jobbet er den virtuelle maskine klar til failover. Når beskyttelse er aktiveret og virtuelle maskiner replikeres, vil du kunne se dem i Azure.

    ![Virtuelt beskyttelse job](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] Du kan også aktivere beskyttelse af virtuelle maskiner i konsollen VMM. Klik på **Aktivér beskyttelse** på værktøjslinjen i fanen **Azure gendannelse af websteder** i virtuelt egenskaber.

### <a name="on-board-existing-virtual-machines"></a>Indbyggede eksisterende virtuelle maskiner

Hvis du har eksisterende virtuelle maskiner i VMM, der replikering med Hyper-V replika skal du indbyggede dem til Azure gendannelse af websteder beskyttelse på følgende måde:

1. Kontrollér, at du har primære og sekundære skyer. Kontrollér, at den Hyper-V-server, der er vært for den eksisterende virtuelle maskine er placeret i den primære sky og, den Hyper-V server, der hoster virtuelt replika er placeret i skyen for sekundær. Sørg for, at du har konfigureret indstillingerne for dokumentbeskyttelse for sky. Indstillingerne skal svare til dem i øjeblikket er konfigureret til Hyper-V replika. Ellers fungerer virtuelt gentagelse muligvis ikke som forventet.
2. Derefter aktivere beskyttelse til den primære virtuelle maskine. Azure gendannelse af websteder og VMM sikrer, at den samme replika vært og virtuelt registreres, og Azure gendannelse af websteder skal genbruge og genoprette gentagelse ved hjælp af de indstillinger, der er konfigureret under konfigurationen af skyen.


## <a name="test-your-deployment"></a>Teste din installation

Du kan køre en test sekundær server til en enkelt virtuel maskine, eller Opret en gendannelse plan bestående af flere virtuelle maskiner og køre en test sekundær server til planen for at teste din installation.  Test failover simulerer din failover og gendannelse ordning i en isolerede netværk.

### <a name="create-a-recovery-plan"></a>Oprette en plan for gendannelse

1. Klik på **Opret gendannelse Plan**under fanen **Gendannelse planer** .
2. Angiv et navn for gendannelse plan og kilde- og destinationswebsteder VMM-servere. Kildeserveren skal have virtuelle maskiner, der er aktiveret for failover og gendannelse. Vælg **Hyper-V** for at få vist kun sky, der er konfigureret til Hyper-V gentagelse.

    ![Oprette en plan](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. **Vælg virtuelt**, Vælg gentagelse grupper. Alle virtuelle maskiner, der er knyttet til gruppen gentagelse er markeret og føjet til planen, gendannelse. Disse virtuelle maskiner er føjet til standardgruppen gendannelse plan – gruppe 1. Hvis det er nødvendigt, kan du tilføje flere grupper. Bemærk, at når gentagelse virtuelle maskiner begynder i overensstemmelse med rækkefølgen af grupperne gendannelse plan.

    ![Tilføje virtuelle maskiner](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Når du har en genoprettelse er blevet oprettet plan, det vises på listen under fanen **Gendannelse planer** .

###<a name="run-a-test-failover"></a>Køre en test failover

1. Vælg planen, og klik på **Test Failover**under fanen **Gendannelse planer** .
2. På siden **Bekræft Test Failover** skal du vælge **ingen**. Bemærk, at aktiveret den mislykkedes over replika virtuelle maskiner med denne indstilling forbindelse ikke til en hvilken som helst netværk. Dette vil teste, at den virtuelle maskine skifter som forventet, men ikke teste dit gentagelse netværksmiljø. Se, hvordan man kan [køre en test failover](site-recovery-failover.md#run-a-test-failover) for flere oplysninger om, hvordan du bruger forskellige netværksindstillinger.
3. Virtuelt test oprettes på samme vært som vært, som den replika virtuelle maskine findes. Det er føjet til samme skyen, virtuelt replika er placeret.

### <a name="run-a-recovery-plan"></a>Køre en plan for gendannelse
Virtuelt replika muligvis ikke en IP-adresse, som er den samme som IP-adressen på den primære virtuelle maskine efter gentagelse. Virtuelle maskiner opdaterer DNS-serveren, de bruger, når de starter. Du kan også tilføje et script for at opdatere DNS-serveren for at sikre en tiden opdatering.

#### <a name="script-to-retrieve-the-ip-address"></a>Script til at hente IP-adresse
Køre dette eksempelscript for at hente IP-adressen.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Script til at opdatere DNS
Køre dette eksempelscript for at opdatere DNS, der angiver den IP-adresse, du har hentet ved hjælp af det forrige eksempelscript.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Beskyttelse af personlige oplysninger for gendannelse af websteder

Dette afsnit indeholder yderligere beskyttelse af personlige oplysninger for Microsoft Azure websted gendannelse tjenesten (""). For at få vist erklæringen for Microsoft Azure-tjenester, vises [Microsoft Azure-erklæring](http://go.microsoft.com/fwlink/?LinkId=324899)

**Funktion: registrering**

- **Hvad gør den**: registrerer server med tjenesten, så du kan beskytte virtuelle maskiner
- **Oplysninger, der indsamles**: Når registrering af tjenesten indsamler, behandler og overfører management certifikatoplysninger fra serveren VMM, der er angivet til at levere nedbrud ved hjælp af navnet på tjenesten på serveren, VMM og navnet på virtuelt skyer på VMM-serveren.
- **Brug af oplysninger**:
    - Administration af certifikat – den bruges til at identificere og godkende registrerede VMM serveren for at få adgang til tjenesten. Tjenesten bruger den offentlige vigtige del af certifikatet til at sikre en token, som kun registrerede VMM serveren kan få adgang til. Serveren, der skal bruge dette token til at få adgang til funktionerne.
    - Navnet på serveren, VMM – feltet VMM servernavnet er påkrævet for at identificere og kommunikere med den relevante VMM-server, hvor sky er placeret.
    - Navne fra serveren VMM i skyen – skyen navnet er påkrævet, når du bruger tjenesten skyen parring/unpairing funktion, der er beskrevet nedenfor. Når du beslutter at danne par din skyen fra et primære datacenter med en anden skybaseret i gendannelse datacenter, vises navnene på alle skyer fra gendannelse datacenter.

- **Valg**: disse oplysninger er en vigtig del af registreringsprocessen Service, fordi det hjælper med at du og -tjenesten til at identificere VMM serveren, du vil beskytte Azure gendannelse af websteder, samt som for at identificere den korrekte registrerede VMM server. Hvis du ikke vil sende disse oplysninger til tjenesten, Brug ikke denne tjeneste. Hvis du registrere din server og derefter senere vil unregister det, kan du gøre det ved at slette serveroplysninger VMM fra portalen Service (som er Azure portalen).

**Funktion: Aktivere Azure gendannelse af websteder beskyttelse**

- **Hvad gør den**: feltet Azure websted gendannelse-Provider installeret på serveren VMM er bane vejen til at kommunikere med tjenesten. Udbyderen, der er et DLL-bibliotek (Dynamic) vært VMM processen. Når udbyderen, der er installeret, bliver aktiveret funktionen "Datacenter gendannelse" i administratorkonsol VMM. En ny eller eksisterende virtuelle maskiner i en sky, som kan aktivere en egenskab, der kaldes "Datacenter gendannelse" for at beskytte den virtuelle maskine. Når denne egenskab er angivet, sender provideren navn og -ID på den virtuelle maskine til tjenesten. Virtuel beskyttelse er aktiveret af Windows Server 2012 eller Windows Server 2012 R2 Hyper-V replikering teknologi. Virtuelt dataene replikeres fra én Hyper-V vært til en anden (oftest placeret i et andet "gendannelse" datacenter).

- **Oplysninger, der indsamles**: tjeneste til indsamler, behandler og overfører metadataene for den virtuelle maskine, som indeholder navnet, ID, virtuelt netværk og navnet på skyen som den tilhører.

- **Brug af oplysninger**: tjeneste til bruger ovenstående oplysninger til at udfylde virtuelt oplysningerne på din Service-portal.

- **Valg**: Dette er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil disse oplysninger sendes til tjenesten, ikke aktivere Azure gendannelse af websteder beskyttelse til en hvilken som helst virtuelle maskiner. Bemærk, at alle data, der sendes af provideren til tjenesten sendes over HTTPS.

**Funktion: En plan for**

- **Hvad gør den**: Denne funktion hjælper dig med at opbygge en plan til organisering af "gendannelse" datacenteret. Du kan angive den rækkefølge, hvori virtuelle maskiner eller en gruppe af virtuelle maskiner skal startes på webstedet gendannelse. Du kan også angive en hvilken som helst automatiseret scripts skal køre eller alle manuel handling til at blive ført på tidspunktet for gendannelse for hver virtuelt. Failover (dækket i næste afsnit) udløses typisk på niveauet gendannelse planlægge for koordineret gendannelse.

- **Oplysninger, der indsamles**: tjeneste til indsamler, behandler og overfører metadataene for gendannelse planen, herunder virtuelt metadata, og metadataene i en hvilken som helst automatiseringsscripts og manuel handling noter.

- **Brug af oplysninger**: de metadata, der er beskrevet ovenfor bruges til at oprette den gendannelse plan Service-portalen.

- **Valg**: Dette er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil disse oplysninger sendes til tjenesten, ikke oprette gendannelse planer i denne tjeneste.

**Funktion: Kort over netværk**

- **Hvad gør den**: Denne funktion kan du knytte netværksoplysninger fra den primære datacenter til datacenteret gendannelse. Når de virtuelle maskiner gendannes på webstedet gendannelse, hjælper denne tilknytning til at oprette forbindelse til netværket for dem.

- **Oplysninger, der indsamles**: som en del af funktionen netværk tilknytning tjenesten indsamler, behandler og overfører metadataene i de logiske netværk for hvert websted (primær og datacenter).

- **Brug af oplysninger**: tjeneste til bruger metadata til at udfylde din tjeneste portal sted, hvor du kan overføre netværksoplysningerne.

- **Valg**: Dette er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil disse oplysninger sendes til tjenesten, ikke bruge funktionen netværk tilknytning.

**Funktion: Failover - test med planlagte og ikke-planlagt,**

- **Hvad gør den**: Denne funktion hjælper failover af en virtuel maskine fra én VMM administrerede datacenter til en anden VMM administrerede datacenter. Handlingen failover udløses af brugeren på deres Service-portalen. Mulige årsager til en failover indeholder en ikke-planlagt begivenhed (for eksempel i forbindelse med en naturlig disaster0 hændelsen planlagte (for eksempel datacenter justering af belastning); en test failover (for eksempel en gendannelse plan afprøvning).

Provideren på serveren VMM får besked om begivenheden fra tjenesten og udfører handlingen failover på værten Hyper-V via VMM grænseflader. Faktisk failover på den virtuelle maskine fra én Hyper-V vært til en anden (typisk kører i et andet "gendannelse" datacenter) håndteres af Windows Server 2012 eller Windows Server 2012 R2 Hyper-V gentagelse teknologi. Når sekundære er fuldført, sender den udbyder, der er installeret på serveren VMM i datacenter "gendannelse" succes oplysninger til tjenesten.

- **Oplysninger, der indsamles**: tjeneste til bruger ovenstående oplysninger til at udfylde status for failover handling oplysningerne på din Service-portal.

- **Brug af oplysninger**: tjeneste til benytter for ovenstående oplysninger på følgende måde:

    - Administration af certifikat – den bruges til at identificere og godkende registrerede VMM serveren for at få adgang til tjenesten. Tjenesten bruger den offentlige vigtige del af certifikatet til at sikre en token, som kun registrerede VMM serveren kan få adgang til. Serveren, der skal bruge dette token til at få adgang til funktionerne.
    - Navnet på serveren, VMM – feltet VMM servernavnet er påkrævet for at identificere og kommunikere med den relevante VMM-server, hvor sky er placeret.
    - Navne fra serveren VMM i skyen – skyen navnet er påkrævet, når du bruger tjenesten skyen parring/unpairing funktion, der er beskrevet nedenfor. Når du beslutter at danne par din skyen fra et primære datacenter med en anden skybaseret i gendannelse datacenter, vises navnene på alle skyer fra gendannelse datacenter.

- **Valg**: Dette er en vigtig del af tjenesten og kan ikke slås fra. Hvis du ikke vil disse oplysninger sendes til tjenesten, ikke bruge denne tjeneste.

## <a name="next-steps"></a>Næste trin

Når du har kørt en test failover kontrollere dit miljø fungerer som forventet, [Få mere at vide om](site-recovery-failover.md) forskellige typer af failover.
